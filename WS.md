# ⚡ WebSocket Chat Cheat Sheet

## 🔐 JWT Auth Setup

```python
def create_token(data, expire_minutes=60):
    expire = datetime.utcnow() + timedelta(minutes=expire_minutes)
    return jwt.encode({**data, "exp": expire}, SECRET, ALGORITHM)

def validate_token(token):
    try:
        return jwt.decode(token, SECRET, [ALGORITHM])
    except JWTError:
        raise ValueError("Invalid token")
```

## 🌐 Connection Manager

```python
class ConnectionManager:
    def __init__(self):
        self.connections = defaultdict(list)  # {room_id: [(ws, client_id)]}
    
    def connect(self, websocket, room_id, client_id):
        self.connections[room_id].append((websocket, client_id))
    
    def disconnect(self, client_id, room_id):
        self.connections[room_id] = [
            (ws, cid) for ws, cid in self.connections[room_id] 
            if cid != client_id
        ]
    
    async def broadcast_to_room(self, sender_id, message, room_id):
        for websocket, client_id in self.connections[room_id]:
            if client_id != sender_id:
                await websocket.send_text(message)

manager = ConnectionManager()
```

## 💬 WebSocket Endpoint

```python
@app.websocket("/ws/chat")
async def chat_endpoint(websocket: WebSocket):
    await websocket.accept()
    
    # Authenticate
    token = websocket.query_params.get("token")
    if not token:
        await websocket.close(code=1008, reason="Token required")
        return
        
    try:
        data = validate_token(token)
        client_id, room_id, name = data["client_id"], data["room_id"], data["name"]
        manager.connect(websocket, room_id, client_id)
        
        while True:
            message = await websocket.receive_text()
            broadcast_msg = {
                "name": name,
                "message": message,
                "timestamp": datetime.utcnow().isoformat()
            }
            await manager.broadcast_to_room(
                client_id, json.dumps(broadcast_msg), room_id
            )
                
    except WebSocketDisconnect:
        manager.disconnect(client_id, room_id)
    except Exception as e:
        await websocket.close(code=1011, reason=str(e))
```

## 🔄 Room Management

```python
# Create room endpoint
@app.get("/create-room")
def create_room(name: str):
    room_id = generate_room_code()
    client_id = uuid4().hex
    token = create_token({
        "client_id": client_id, 
        "room_id": room_id, 
        "name": name
    })
    return {
        "status": "success", 
        "token": token, 
        "room_id": room_id
    }

# Join room endpoint
@app.get("/join-room")
def join_room(name: str, room_id: str):
    if not manager.room_exists(room_id):
        raise HTTPException(404, "Room not found")
    
    client_id = uuid4().hex
    token = create_token({
        "client_id": client_id,
        "room_id": room_id,
        "name": name
    })
    return {"status": "success", "token": token}
```

## ⚛️ React WebSocket Hook

```javascript
function useWebSocket(url, token) {
  const [messages, setMessages] = useState([]);
  const [connectionStatus, setConnectionStatus] = useState('Connecting');
  const wsRef = useRef(null);

  useEffect(() => {
    const wsUrl = `${url}?token=${token}`;
    wsRef.current = new WebSocket(wsUrl);

    wsRef.current.onopen = () => setConnectionStatus('Connected');
    wsRef.current.onclose = () => setConnectionStatus('Disconnected');
    wsRef.current.onerror = () => setConnectionStatus('Error');
    
    wsRef.current.onmessage = (event) => {
      const message = JSON.parse(event.data);
      setMessages(prev => [...prev, message]);
    };

    return () => {
      wsRef.current?.close();
    };
  }, [url, token]);

  const sendMessage = (message) => {
    if (wsRef.current?.readyState === WebSocket.OPEN) {
      wsRef.current.send(JSON.stringify(message));
    }
  };

  return { messages, sendMessage, connectionStatus };
}
```

## 💬 Chat Component

```javascript
function ChatRoom({ token }) {
  const { messages, sendMessage, connectionStatus } = useWebSocket(
    'ws://localhost:8000/ws/chat', 
    token
  );
  const [input, setInput] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    if (input.trim()) {
      sendMessage(input);
      setInput('');
    }
  };

  return (
    <div>
      <div>Status: {connectionStatus}</div>
      <div>
        {messages.map((msg, i) => (
          <div key={i}>
            <strong>{msg.name}:</strong> {msg.message}
          </div>
        ))}
      </div>
      <form onSubmit={handleSubmit}>
        <input 
          value={input} 
          onChange={(e) => setInput(e.target.value)}
          placeholder="Type message..."
        />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

## ⚠️ Common Issues & Solutions

**Duplicate Messages**
- ❌ Multiple event listeners
- ✅ Proper cleanup in useEffect

**Connection Drops**
- ❌ Network timeouts
- ✅ Ping/pong heartbeat

**Authentication Errors**
- ❌ Invalid/expired tokens
- ✅ Server-side token validation

**Mixed Content (HTTP/HTTPS)**
- ❌ Using `ws://` on HTTPS site
- ✅ Use `wss://` for secure connections

## 🎯 Best Practices

✅ **Always authenticate** WebSocket connections  
✅ **Clean up** event listeners and connections  
✅ **Validate messages** on server side  
✅ **Handle reconnection** gracefully  
✅ **Use secure connections** (wss://)  
✅ **Implement rate limiting** for message spam  
✅ **Log all events** for debugging
