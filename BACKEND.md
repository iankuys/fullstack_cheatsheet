# ⚡ FastAPI Backend Cheat Sheet

## 📦 Quick Setup

```python
from fastapi import FastAPI, HTTPException, Depends, status
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel, EmailStr
import jwt, bcrypt, psycopg2
from datetime import datetime, timedelta

app = FastAPI()
app.add_middleware(CORSMiddleware, allow_origins=["*"], allow_methods=["*"], allow_headers=["*"])
```

## 🔐 JWT Auth (Essential Pattern)

```python
SECRET_KEY = "your-secret"
ALGORITHM = "HS256"

def create_token(data: dict):
    expire = datetime.utcnow() + timedelta(minutes=30)
    return jwt.encode({**data, "exp": expire}, SECRET_KEY, ALGORITHM)

def verify_token(token: str):
    try:
        return jwt.decode(token, SECRET_KEY, [ALGORITHM])
    except jwt.JWTError:
        raise HTTPException(401, "Invalid token")
```

## 📝 Core Models

```python
class UserCreate(BaseModel):
    email: EmailStr
    password: str
    name: str

class TokenResponse(BaseModel):
    access_token: str
    token_type: str = "bearer"
```

## 🗄️ Database (PostgreSQL)

```python
@contextmanager
def get_db():
    conn = psycopg2.connect(
        host="localhost", database="myapp", 
        user="postgres", password="password"
    )
    try:
        yield conn
    finally:
        conn.close()

# Quick Operations
def create_user(email, password_hash, name):
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute(
            "INSERT INTO users (email, password_hash, name) VALUES (%s, %s, %s) RETURNING id",
            (email, password_hash, name)
        )
        conn.commit()
        return cursor.fetchone()[0]
```

## 🚪 Auth Endpoints

```python
@app.post("/register")
def register(user: UserCreate):
    # Hash password
    password_hash = bcrypt.hashpw(user.password.encode(), bcrypt.gensalt())
    
    # Create user
    user_id = create_user(user.email, password_hash.decode(), user.name)
    
    # Return token
    token = create_token({"user_id": user_id, "email": user.email})
    return {"access_token": token, "token_type": "bearer"}

@app.post("/login")
def login(credentials: UserLogin):
    user = get_user_by_email(credentials.email)
    if not user or not bcrypt.checkpw(credentials.password.encode(), user['password_hash'].encode()):
        raise HTTPException(401, "Invalid credentials")
    
    token = create_token({"user_id": user['id'], "email": user['email']})
    return {"access_token": token, "token_type": "bearer"}
```

## 📄 PDF Processing

```python
import pdfplumber
from fastapi import File, UploadFile

def extract_pdf_text(file_content: bytes):
    with pdfplumber.open(io.BytesIO(file_content)) as pdf:
        return {
            "text": "\n".join(page.extract_text() or "" for page in pdf.pages),
            "page_count": len(pdf.pages),
            "tables": [page.extract_tables() for page in pdf.pages]
        }

@app.post("/upload-pdf")
async def upload_pdf(file: UploadFile = File(...)):
    if not file.filename.endswith('.pdf'):
        raise HTTPException(400, "Only PDF files allowed")
    
    content = await file.read()
    data = extract_pdf_text(content)
    
    return {
        "filename": file.filename,
        "page_count": data["page_count"],
        "text_preview": data["text"][:500] + "..."
    }
```



## 🌐 WebSocket Chat

```python
from fastapi import WebSocket, WebSocketDisconnect
import json

class ConnectionManager:
    def __init__(self):
        self.connections: Dict[int, WebSocket] = {}

    async def connect(self, websocket: WebSocket, user_id: int):
        await websocket.accept()
        self.connections[user_id] = websocket

    async def send_to_user(self, message: str, user_id: int):
        if user_id in self.connections:
            await self.connections[user_id].send_text(message)

manager = ConnectionManager()

@app.websocket("/ws/{user_id}")
async def websocket_endpoint(websocket: WebSocket, user_id: int):
    await manager.connect(websocket, user_id)
    try:
        while True:
            data = await websocket.receive_text()
            msg = json.loads(data)
            await manager.send_to_user(data, msg['recipient_id'])
    except WebSocketDisconnect:
        del manager.connections[user_id]
```

## 📨 Common Endpoints

```python
@app.get("/users")
def get_users():
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute("SELECT id, email, name FROM users")
        return [dict(row) for row in cursor.fetchall()]

@app.post("/messages")
def send_message(message: dict):
    with get_db() as conn:
        cursor = conn.cursor()
        cursor.execute(
            "INSERT INTO messages (sender_id, recipient_id, content) VALUES (%s, %s, %s)",
            (message['sender_id'], message['recipient_id'], message['content'])
        )
        conn.commit()
    return {"status": "sent"}
```

## ⚡ Best Practices

```python
# Pagination
@app.get("/messages")
def get_messages(page: int = 1, limit: int = 20):
    offset = (page - 1) * limit
    # Return paginated results
    return {"page": page, "limit": limit, "data": []}

# Background tasks
from fastapi import BackgroundTasks

@app.post("/send-email")
def queue_email(background_tasks: BackgroundTasks, email: str):
    background_tasks.add_task(send_email, email)
    return {"status": "queued"}

# Error handling
@app.exception_handler(404)
async def not_found(request, exc):
    return {"error": "Not found"}
```

## 🚨 Status Codes

- **200** - OK
- **201** - Created  
- **400** - Bad Request
- **401** - Unauthorized
- **403** - Forbidden
- **404** - Not Found
- **422** - Validation Error
- **500** - Server Error

```python
raise HTTPException(status_code=401, detail="Invalid token")
raise HTTPException(status_code=404, detail="User not found")
```

## 🔧 Environment

```python
import os

# .env file
SECRET_KEY = os.getenv("SECRET_KEY", "dev-key")
DB_URL = os.getenv("DATABASE_URL", "postgresql://user:pass@localhost/db")
CORS_ORIGINS = os.getenv("CORS_ORIGINS", "*").split(",")
```

## 🚀 Quick Start

```bash
# Install
pip install fastapi uvicorn psycopg2-binary python-jose[cryptography] passlib[bcrypt]

# Run
uvicorn main:app --reload --port 8000

# Production
gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker
```

## 🐛 Common Issues

**422 Error:** Request doesn't match Pydantic model  
**CORS Error:** Add `allow_origins=["*"]` to CORS middleware  
**JWT Error:** Use `Bearer {token}` format in Authorization header  
**DB Error:** Always use `conn.commit()` and context managers

## ⚠️ FastAPI Gotchas

**Request Body:** Use Pydantic models, not function params  
**Dependencies:** Use `Depends(get_db)` not `get_db()`  
**File Uploads:** Use `UploadFile = File(...)` not Pydantic  
**Async/Sync:** Don't mix sync DB calls in async functions  
**CORS:** Add middleware for frontend requests
