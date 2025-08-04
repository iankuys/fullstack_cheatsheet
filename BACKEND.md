# 🚀 FastAPI Backend Cheat Sheet

## 🛠️ Quick Setup & Imports

```python
# Essential imports
from fastapi import FastAPI, HTTPException, Depends, status, WebSocket, WebSocketDisconnect, File, UploadFile, BackgroundTasks
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from fastapi.middleware.cors import CORSMiddleware
from fastapi.encoders import jsonable_encoder
from pydantic import BaseModel, EmailStr
from typing import Optional, List, Dict
import jwt, bcrypt, psycopg2, asyncio, json, os
from psycopg2.extras import RealDictCursor
from contextlib import contextmanager
from datetime import datetime, timedelta
import pdfplumber, io
from pathlib import Path

# App initialization
app = FastAPI(title="My API", description="Backend API", version="1.0.0")

# CORS Middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],  # React app URL
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Security
security = HTTPBearer()

@app.get("/")
def read_root():
    return {"message": "Welcome to the API"}
```

## 📝 Pydantic Models

```python
# User models
class UserCreate(BaseModel):
    email: EmailStr
    password: str
    name: str

class UserLogin(BaseModel):
    email: EmailStr
    password: str

class User(BaseModel):
    id: int
    email: str
    name: str
    created_at: datetime
    class Config:
        from_attributes = True  # For SQLAlchemy models

# Response models
class TokenResponse(BaseModel):
    access_token: str
    token_type: str
    expires_in: int

# Message models
class MessageCreate(BaseModel):
    content: str
    recipient_id: int
```

## 🔐 JWT Authentication

```python
# JWT Configuration
SECRET_KEY = "your-secret-key-here"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

def create_jwt_token(data: dict, expires_delta: Optional[timedelta] = None):
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

def verify_jwt_token(credentials: HTTPAuthorizationCredentials = Depends(security)):
    try:
        payload = jwt.decode(credentials.credentials, SECRET_KEY, algorithms=[ALGORITHM])
        user_id: int = payload.get("user_id")
        if user_id is None:
            raise HTTPException(status_code=401, detail="Invalid token")
        return user_id
    except jwt.ExpiredSignatureError:
        raise HTTPException(status_code=401, detail="Token expired")
    except jwt.JWTError:
        raise HTTPException(status_code=401, detail="Invalid token")

# Usage in endpoints
@app.get("/protected")
def protected_route(current_user_id: int = Depends(verify_jwt_token)):
    return {"message": f"Hello user {current_user_id}"}
```

## 🗄️ Database Setup & Operations

```python
# Database configuration
DATABASE_CONFIG = {
    "host": os.getenv("DB_HOST", "localhost"),
    "database": os.getenv("DB_NAME", "myapp"),
    "user": os.getenv("DB_USER", "postgres"),
    "password": os.getenv("DB_PASSWORD", "password"),
    "port": os.getenv("DB_PORT", "5432")
}

@contextmanager
def get_db():
    """Database connection context manager"""
    conn = psycopg2.connect(**DATABASE_CONFIG, cursor_factory=RealDictCursor)
    try:
        yield conn
    finally:
        conn.close()

# Database schema initialization
def init_db():
    """Initialize database with tables"""
    conn = psycopg2.connect(**DATABASE_CONFIG)
    cursor = conn.cursor()
    
    # Users table
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS users (
            id SERIAL PRIMARY KEY,
            email VARCHAR(255) UNIQUE NOT NULL,
            password_hash VARCHAR(255) NOT NULL,
            name VARCHAR(255) NOT NULL,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )
    ''')
    
    # Messages table
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS messages (
            id SERIAL PRIMARY KEY,
            sender_id INTEGER NOT NULL,
            recipient_id INTEGER NOT NULL,
            content TEXT NOT NULL,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
            FOREIGN KEY (sender_id) REFERENCES users (id),
            FOREIGN KEY (recipient_id) REFERENCES users (id)
        )
    ''')
    
    # Documents table (for PDF processing)
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS documents (
            id SERIAL PRIMARY KEY,
            user_id INTEGER NOT NULL,
            filename VARCHAR(255) NOT NULL,
            file_path VARCHAR(500) NOT NULL,
            extracted_text TEXT,
            page_count INTEGER,
            file_size INTEGER,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
            FOREIGN KEY (user_id) REFERENCES users (id)
        )
    ''')
    
    conn.commit()
    conn.close()

# Common database operations
def create_user(email: str, password_hash: str, name: str):
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute(
            "INSERT INTO users (email, password_hash, name) VALUES (%s, %s, %s) RETURNING id",
            (email, password_hash, name)
        )
        conn.commit()
        return cursor.fetchone()['id']

def get_user_by_email(email: str):
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM users WHERE email = %s", (email,))
        return cursor.fetchone()

def get_user_by_id(user_id: int):
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
        return cursor.fetchone()
```

## 🚪 Authentication Endpoints

```python
@app.post("/register", response_model=TokenResponse)
def register(user_data: UserCreate):
    # Check if user exists
    existing_user = get_user_by_email(user_data.email)
    if existing_user:
        raise HTTPException(status_code=400, detail="Email already registered")
    
    # Hash password
    password_hash = bcrypt.hashpw(user_data.password.encode('utf-8'), bcrypt.gensalt())
    
    # Create user
    user_id = create_user(user_data.email, password_hash.decode('utf-8'), user_data.name)
    
    # Create token
    access_token = create_jwt_token({"user_id": user_id, "email": user_data.email})
    
    return {
        "access_token": access_token,
        "token_type": "bearer",
        "expires_in": ACCESS_TOKEN_EXPIRE_MINUTES * 60
    }

@app.post("/login", response_model=TokenResponse)
def login(login_data: UserLogin):
    # Get user
    user = get_user_by_email(login_data.email)
    if not user:
        raise HTTPException(status_code=401, detail="Invalid credentials")
    
    # Verify password
    if not bcrypt.checkpw(login_data.password.encode('utf-8'), user['password_hash'].encode('utf-8')):
        raise HTTPException(status_code=401, detail="Invalid credentials")
    
    # Create token
    access_token = create_jwt_token({"user_id": user['id'], "email": user['email']})
    
    return {
        "access_token": access_token,
        "token_type": "bearer",
        "expires_in": ACCESS_TOKEN_EXPIRE_MINUTES * 60
    }

@app.get("/me", response_model=User)
def get_current_user(current_user_id: int = Depends(verify_jwt_token)):
    user = get_user_by_id(current_user_id)
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return dict(user)
```

## 📄 PDF Processing

```python
# Core PDF processing functions
def extract_text_from_pdf(file_content: bytes) -> dict:
    """Extract comprehensive PDF data"""
    result = {"text": "", "pages": [], "page_count": 0, "tables": [], "metadata": {}}
    
    try:
        with pdfplumber.open(io.BytesIO(file_content)) as pdf:
            result["page_count"] = len(pdf.pages)
            result["metadata"] = pdf.metadata or {}
            
            for i, page in enumerate(pdf.pages):
                page_text = page.extract_text() or ""
                result["pages"].append({
                    "page_number": i + 1,
                    "text": page_text,
                    "char_count": len(page_text)
                })
                result["text"] += page_text + "\n\n"
                
                # Extract tables
                tables = page.extract_tables()
                for j, table in enumerate(tables or []):
                    result["tables"].append({
                        "page": i + 1, "table_index": j, "data": table
                    })
        return result
    except Exception as e:
        raise HTTPException(status_code=400, detail=f"Error processing PDF: {str(e)}")

def get_pdf_info(file_content: bytes) -> dict:
    """Get basic PDF information"""
    try:
        with pdfplumber.open(io.BytesIO(file_content)) as pdf:
            return {
                "page_count": len(pdf.pages),
                "metadata": pdf.metadata or {},
                "file_size": len(file_content),
                "has_text": any(page.extract_text() for page in pdf.pages),
                "has_tables": any(page.extract_tables() for page in pdf.pages)
            }
    except Exception as e:
        raise HTTPException(status_code=400, detail=f"Error reading PDF info: {str(e)}")

# Document database operations
def save_document(user_id: int, filename: str, file_path: str, extracted_text: str, page_count: int, file_size: int):
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute(
            """INSERT INTO documents (user_id, filename, file_path, extracted_text, page_count, file_size) 
               VALUES (%s, %s, %s, %s, %s, %s) RETURNING id""",
            (user_id, filename, file_path, extracted_text, page_count, file_size)
        )
        conn.commit()
        return cursor.fetchone()['id']

def get_user_documents(user_id: int):
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute(
            "SELECT id, filename, page_count, file_size, created_at FROM documents WHERE user_id = %s ORDER BY created_at DESC",
            (user_id,)
        )
        return cursor.fetchall()
```

## 📄 PDF Processing Endpoints

```python
# File upload setup
UPLOAD_DIR = Path("uploads")
UPLOAD_DIR.mkdir(exist_ok=True)

@app.post("/upload-pdf")
async def upload_pdf(file: UploadFile = File(...), current_user_id: int = Depends(verify_jwt_token)):
    if not file.filename.lower().endswith('.pdf'):
        raise HTTPException(status_code=400, detail="Only PDF files are allowed")
    
    # Generate unique filename and save
    import uuid, shutil
    file_id = str(uuid.uuid4())
    unique_filename = f"{file_id}.pdf"
    file_path = UPLOAD_DIR / unique_filename
    
    try:
        with open(file_path, "wb") as buffer:
            shutil.copyfileobj(file.file, buffer)
        
        with open(file_path, "rb") as f:
            file_content = f.read()
        
        pdf_data = extract_text_from_pdf(file_content)
        
        document_id = save_document(
            user_id=current_user_id,
            filename=file.filename,
            file_path=str(file_path),
            extracted_text=pdf_data["text"],
            page_count=pdf_data["page_count"],
            file_size=len(file_content)
        )
        
        return {
            "document_id": document_id,
            "filename": file.filename,
            "page_count": pdf_data["page_count"],
            "file_size": len(file_content),
            "text_preview": pdf_data["text"][:500] + "..." if len(pdf_data["text"]) > 500 else pdf_data["text"],
            "tables_found": len(pdf_data["tables"])
        }
    except Exception as e:
        if file_path.exists():
            file_path.unlink()
        raise HTTPException(status_code=500, detail=f"Error processing file: {str(e)}")

@app.get("/documents")
def get_documents(current_user_id: int = Depends(verify_jwt_token)):
    documents = get_user_documents(current_user_id)
    return {"documents": [dict(doc) for doc in documents]}

@app.post("/pdf-info")
async def get_pdf_info_endpoint(file: UploadFile = File(...), current_user_id: int = Depends(verify_jwt_token)):
    if not file.filename.lower().endswith('.pdf'):
        raise HTTPException(status_code=400, detail="Only PDF files are allowed")
    
    file_content = await file.read()
    info = get_pdf_info(file_content)
    return {"filename": file.filename, **info}
```

## 🔍 Advanced PDF Features

```python
# Search text in PDF
def search_text_in_pdf(file_content: bytes, search_term: str) -> dict:
    results = {"search_term": search_term, "total_matches": 0, "matches_by_page": []}
    
    try:
        with pdfplumber.open(io.BytesIO(file_content)) as pdf:
            for i, page in enumerate(pdf.pages):
                page_text = page.extract_text() or ""
                matches = page_text.lower().count(search_term.lower())
                
                if matches > 0:
                    results["matches_by_page"].append({
                        "page": i + 1, "matches": matches,
                        "context": get_text_context(page_text, search_term)
                    })
                    results["total_matches"] += matches
        return results
    except Exception as e:
        raise HTTPException(status_code=400, detail=f"Error searching PDF: {str(e)}")

def get_text_context(text: str, search_term: str, context_chars: int = 100) -> list:
    contexts = []
    text_lower, search_lower = text.lower(), search_term.lower()
    start = 0
    
    while True:
        pos = text_lower.find(search_lower, start)
        if pos == -1: break
        
        context_start = max(0, pos - context_chars)
        context_end = min(len(text), pos + len(search_term) + context_chars)
        
        contexts.append({
            "position": pos,
            "context": text[context_start:context_end],
            "before": text[context_start:pos],
            "match": text[pos:pos + len(search_term)],
            "after": text[pos + len(search_term):context_end]
        })
        start = pos + 1
    
    return contexts

@app.post("/search-pdf")
async def search_pdf_endpoint(file: UploadFile = File(...), search_term: str = "", current_user_id: int = Depends(verify_jwt_token)):
    if not file.filename.lower().endswith('.pdf'):
        raise HTTPException(status_code=400, detail="Only PDF files are allowed")
    if not search_term:
        raise HTTPException(status_code=400, detail="Search term is required")
    
    file_content = await file.read()
    results = search_text_in_pdf(file_content, search_term)
    return {"filename": file.filename, **results}
```

## 🌐 WebSocket Chat Implementation

```python
class ConnectionManager:
    def __init__(self):
        self.active_connections: Dict[int, WebSocket] = {}

    async def connect(self, websocket: WebSocket, user_id: int):
        await websocket.accept()
        self.active_connections[user_id] = websocket

    def disconnect(self, user_id: int):
        if user_id in self.active_connections:
            del self.active_connections[user_id]

    async def send_personal_message(self, message: str, user_id: int):
        if user_id in self.active_connections:
            await self.active_connections[user_id].send_text(message)

    async def broadcast(self, message: str):
        for connection in self.active_connections.values():
            await connection.send_text(message)

manager = ConnectionManager()

@app.websocket("/ws/{user_id}")
async def websocket_endpoint(websocket: WebSocket, user_id: int):
    await manager.connect(websocket, user_id)
    try:
        while True:
            data = await websocket.receive_text()
            message_data = json.loads(data)
            
            # Save message to database
            with get_db() as conn:
                cursor = conn.cursor()
                cursor.execute(
                    "INSERT INTO messages (sender_id, recipient_id, content) VALUES (%s, %s, %s)",
                    (user_id, message_data['recipient_id'], message_data['content'])
                )
                conn.commit()
            
            # Send to recipient
            await manager.send_personal_message(
                json.dumps({
                    "sender_id": user_id,
                    "content": message_data['content'],
                    "timestamp": datetime.now().isoformat()
                }),
                message_data['recipient_id']
            )
    except WebSocketDisconnect:
        manager.disconnect(user_id)
```

## 📨 REST API Endpoints & Patterns

```python
# User management
@app.get("/users", response_model=List[User])
def get_users(current_user_id: int = Depends(verify_jwt_token)):
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute("SELECT id, email, name, created_at FROM users WHERE id != %s", (current_user_id,))
        users = cursor.fetchall()
        return [dict(user) for user in users]

# Message endpoints
@app.post("/messages")
def send_message(message: MessageCreate, current_user_id: int = Depends(verify_jwt_token)):
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute(
            "INSERT INTO messages (sender_id, recipient_id, content) VALUES (%s, %s, %s) RETURNING id",
            (current_user_id, message.recipient_id, message.content)
        )
        conn.commit()
        message_id = cursor.fetchone()['id']
    return {"id": message_id, "message": "Message sent successfully"}

@app.get("/messages/{user_id}")
def get_messages(user_id: int, current_user_id: int = Depends(verify_jwt_token)):
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute('''
            SELECT m.*, u.name as sender_name 
            FROM messages m JOIN users u ON m.sender_id = u.id
            WHERE (m.sender_id = %s AND m.recipient_id = %s) 
               OR (m.sender_id = %s AND m.recipient_id = %s)
            ORDER BY m.created_at ASC
        ''', (current_user_id, user_id, user_id, current_user_id))
        messages = cursor.fetchall()
        return [dict(message) for message in messages]

# Pagination pattern
@app.get("/messages")
def get_paginated_messages(page: int = 1, limit: int = 20, current_user_id: int = Depends(verify_jwt_token)):
    offset = (page - 1) * limit
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute(
            "SELECT * FROM messages WHERE sender_id = %s OR recipient_id = %s LIMIT %s OFFSET %s",
            (current_user_id, current_user_id, limit, offset)
        )
        messages = cursor.fetchall()
        return {"messages": [dict(msg) for msg in messages], "page": page, "limit": limit}

# Background tasks
def send_email_notification(email: str, message: str):
    print(f"Sending email to {email}: {message}")

@app.post("/send-notification")
def send_notification(background_tasks: BackgroundTasks, email: str, message: str):
    background_tasks.add_task(send_email_notification, email, message)
    return {"message": "Notification queued"}

# Error handling
@app.exception_handler(404)
async def not_found_handler(request, exc):
    return {"error": "Resource not found"}

# Dependency injection for user
def get_current_user_from_db(current_user_id: int = Depends(verify_jwt_token)):
    user = get_user_by_id(current_user_id)
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return dict(user)
```

## 🚨 Error Handling & Configuration

```python
# Custom exceptions
class UserNotFoundError(HTTPException):
    def __init__(self):
        super().__init__(status_code=404, detail="User not found")

class InvalidCredentialsError(HTTPException):
    def __init__(self):
        super().__init__(status_code=401, detail="Invalid credentials")

# Common status codes patterns
@app.post("/example", status_code=status.HTTP_201_CREATED)
def create_example():
    return {"message": "Created successfully"}

def handle_db_error():
    raise HTTPException(status_code=status.HTTP_500_INTERNAL_SERVER_ERROR, detail="Database error occurred")

# Environment configuration
from functools import lru_cache

class Settings:
    SECRET_KEY: str = os.getenv("SECRET_KEY", "dev-secret-key")
    DATABASE_URL: str = os.getenv("DATABASE_URL", "sqlite:///./app.db")
    ACCESS_TOKEN_EXPIRE_MINUTES: int = int(os.getenv("ACCESS_TOKEN_EXPIRE_MINUTES", "30"))
    CORS_ORIGINS: List[str] = os.getenv("CORS_ORIGINS", "http://localhost:3000").split(",")

@lru_cache()
def get_settings():
    return Settings()

settings = get_settings()
```

---

## 🚀 Running the App

```python
if __name__ == "__main__":
    import uvicorn
    init_db()  # Initialize database
    uvicorn.run("main:app", host="0.0.0.0", port=8000, reload=True)
```

**Command line:**
```bash
# Install dependencies
pip install fastapi uvicorn python-jose[cryptography] passlib[bcrypt] python-multipart psycopg2-binary pdfplumber

# PostgreSQL setup (using Docker)
docker run --name postgres-db -e POSTGRES_PASSWORD=password -e POSTGRES_DB=myapp -p 5432:5432 -d postgres:13

# Run development server
uvicorn main:app --reload --host 0.0.0.0 --port 8000

# Run with gunicorn (production)
gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker
```

**Environment Variables:**
```bash
# .env file
DB_HOST=localhost
DB_NAME=myapp
DB_USER=postgres
DB_PASSWORD=password
DB_PORT=5432
SECRET_KEY=your-secret-key-here
```

---

## 🐛 Common FastAPI Issues & Solutions

**422 Unprocessable Entity:**
- Your request body doesn't match the Pydantic model
- Check field names and types
- Use `@app.post("/login")` with `def login(data: UserLogin):`

**CORS Issues:**
- Add CORS middleware with correct origins
- Check if preflight OPTIONS requests are handled

**JWT Token Issues:**
- Make sure to use `Bearer {token}` format
- Check token expiration
- Verify secret key matches

**Database Connection:**
- Use context managers for database connections
- Don't forget to commit transactions
- Handle connection cleanup properly

---

## 📊 Performance Tips

```python
# Use async/await for I/O operations
@app.get("/async-example")
async def async_endpoint():
    # Use async libraries for database, HTTP requests, etc.
    pass

# Cache expensive operations
from functools import lru_cache

@lru_cache(maxsize=100)
def expensive_function(param: str):
    # Expensive computation
    return result

# Use database connection pooling for production
# Use Redis for caching
# Implement rate limiting
```

---

## ⚠️ FastAPI Gotchas & Tricky Things

### 1. **Request Body Parsing - The Big One!**

**❌ Wrong - This only works for query params:**
```python
@app.post("/login")
def login(email, password):  # This expects ?email=...&password=...
    pass
```

**✅ Correct - For JSON body:**
```python
from pydantic import BaseModel

class LoginRequest(BaseModel):
    email: str
    password: str

@app.post("/login")
def login(data: LoginRequest):
    email = data.email  # Use dot notation, not data["email"]
    password = data.password
```

**✅ For Form Data:**
```python
from fastapi import Form

@app.post("/login")
def login(email: str = Form(...), password: str = Form(...)):
    pass
```

---

### 2. **Pydantic Models vs Dictionaries**

**❌ Wrong:**
```python
@app.post("/users")
def create_user(user: UserCreate):
    name = user["name"]  # AttributeError!
```

**✅ Correct:**
```python
@app.post("/users")
def create_user(user: UserCreate):
    name = user.name  # Use dot notation
    # Or convert to dict: user_dict = user.dict()
```

---

### 3. **CORS Preflight Requests**

**The Problem:**
- Browser sends OPTIONS request before POST/PUT
- Your endpoint only handles POST, not OPTIONS
- Request fails with CORS error

**✅ Solution:**
```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],  # Your frontend URL
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

---

### 4. **Path vs Query vs Body Parameters**

```python
# Path parameter
@app.get("/users/{user_id}")
def get_user(user_id: int):  # From URL path
    pass

# Query parameter  
@app.get("/users")
def get_users(active: bool = False):  # From ?active=true

# Body parameter (JSON)
@app.post("/users")
def create_user(user: UserCreate):  # From request body

# Multiple types
@app.put("/users/{user_id}")
def update_user(
    user_id: int,  # Path
    user: UserUpdate,  # Body
    force: bool = False  # Query
):
    pass
```

---

### 5. **Dependency Injection Gotchas**

**❌ Wrong - Calling the function:**
```python
@app.get("/users")
def get_users(db=get_db()):  # Wrong! This calls get_db() once
    pass
```

**✅ Correct - Using Depends:**
```python
@app.get("/users")
def get_users(db=Depends(get_db)):  # Correct! FastAPI calls it per request
    pass
```

---

### 6. **Response Models & Data Filtering**

```python
class UserOut(BaseModel):
    id: int
    email: str
    # password intentionally omitted

@app.get("/me", response_model=UserOut)
def get_current_user():
    return {
        "id": 1,
        "email": "user@example.com",
        "password": "secret123"  # This will be filtered out automatically
    }
```

---

### 7. **File Upload Confusion**

**❌ Wrong - Using Pydantic:**
```python
class FileUpload(BaseModel):
    file: bytes  # This won't work for file uploads
```

**✅ Correct - Using File/UploadFile:**
```python
from fastapi import File, UploadFile

@app.post("/upload")
def upload_file(file: UploadFile = File(...)):
    content = file.file.read()
    return {"filename": file.filename, "size": len(content)}
```

---

### 8. **Async/Sync Mixing Issues**

**❌ Problematic:**
```python
@app.get("/users")
async def get_users():
    # Using sync database call in async function
    users = sync_db_call()  # This blocks the event loop!
    return users
```

**✅ Better:**
```python
# Use async def only when you have actual async operations
@app.get("/users")
def get_users():  # Regular sync function for sync operations
    users = sync_db_call()
    return users

@app.get("/async-users")
async def get_async_users():
    users = await async_db_call()  # Actual async operation
    return users
```

---

### 9. **Status Code Gotchas**

```python
from fastapi import status

# Specify status codes for clarity
@app.post("/users", status_code=status.HTTP_201_CREATED)
def create_user(user: UserCreate):
    return created_user

# Custom status codes
@app.delete("/users/{user_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_user(user_id: int):
    # For 204, don't return anything
    pass
```

---

### 10. **Common HTTP Error Patterns**

```python
from fastapi import HTTPException, status

# 422 Unprocessable Entity - Usually validation errors
# FastAPI handles this automatically for Pydantic validation

# 401 Unauthorized - Missing or invalid auth
if not token:
    raise HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Authentication required"
    )

# 403 Forbidden - Valid auth but insufficient permissions
if not user.is_admin:
    raise HTTPException(
        status_code=status.HTTP_403_FORBIDDEN,
        detail="Admin access required"
    )

# 404 Not Found - Resource doesn't exist
if not user:
    raise HTTPException(
        status_code=status.HTTP_404_NOT_FOUND,
        detail="User not found"
    )
```

---

### 11. **Background Tasks Timing**

```python
from fastapi import BackgroundTasks

@app.post("/send-email")
def send_email(background_tasks: BackgroundTasks):
    # This runs AFTER the response is sent
    background_tasks.add_task(send_email_task, "user@example.com")
    
    # Response is sent immediately, email sends in background
    return {"message": "Email queued"}
```

---

### 12. **Environment Variable Loading**

```python
import os
from functools import lru_cache

class Settings:
    SECRET_KEY: str = os.getenv("SECRET_KEY", "dev-secret-key")
    DATABASE_URL: str = os.getenv("DATABASE_URL", "sqlite:///./app.db")

@lru_cache()  # Cache settings so they're only loaded once
def get_settings():
    return Settings()

# Use in dependencies
def get_current_user(settings: Settings = Depends(get_settings)):
    # Use settings.SECRET_KEY etc.
    pass
```

---

### 13. **JSON Serialization Issues**

```python
from datetime import datetime
from fastapi.encoders import jsonable_encoder

# Pydantic handles most serialization, but for complex types:
@app.get("/data")
def get_data():
    data = {
        "timestamp": datetime.now(),  # FastAPI handles this
        "custom_object": MyCustomClass()  # Might need custom serializer
    }
    return jsonable_encoder(data)  # Ensures everything is JSON serializable
```

---

### 14. **Testing Gotchas**

```python
from fastapi.testclient import TestClient

client = TestClient(app)

# Test JSON endpoints
response = client.post("/login", json={"email": "test@test.com", "password": "pass"})

# Test form endpoints  
response = client.post("/login", data={"email": "test@test.com", "password": "pass"})

# Test file uploads
response = client.post("/upload", files={"file": ("test.txt", "content", "text/plain")})
```

---

## ⚡ Performance & Production Tips

```python
# Use async for I/O operations
@app.get("/async-example")
async def async_endpoint():
    # Use async libraries for database, HTTP requests, etc.
    pass

# Cache expensive operations
@lru_cache(maxsize=100)
def expensive_function(param: str):
    return result  # Expensive computation

# Production optimizations:
# - Database connection pooling
# - Redis for caching  
# - Rate limiting middleware
# - Background task queues (Celery)
# - Use gunicorn with uvicorn workers
# - Environment-based configuration
```

**🎯 Key Takeaways:**
- Use Pydantic models for JSON request bodies
- Use `Depends()` for dependency injection
- Configure CORS for frontend requests
- Use appropriate status codes
- Be careful with async/sync mixing
- Always validate and handle errors properly
