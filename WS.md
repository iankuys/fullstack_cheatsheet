# WebSocket Chat Application: Interview Cheatsheet

This cheatsheet provides concise notes, examples, and best practices based on your codebase for building a WebSocket-based chat application using FastAPI and React.

---

## **1. Backend: FastAPI Implementation**

### **Key Components**
1. **FastAPI**: Framework for building APIs and WebSocket endpoints.
2. **JWT Authentication**: Used for secure client identification.
3. **ConnectionManager**: Manages WebSocket connections and broadcasts messages.

---

### **1.1 FastAPI Setup**
- Add `CORSMiddleware` to handle cross-origin requests:
  ```python
  app.add_middleware(
      CORSMiddleware,
      allow_origins=["*"],  # Allow all origins for development
      allow_credentials=True,
      allow_methods=["*"],
      allow_headers=["*"],
  )
  ```

---

### **1.2 JWT Authentication**
- **Token Creation**:
  ```python
  def createToken(data, expire_minutes=60):
      expire = datetime.utcnow() + timedelta(minutes=expire_minutes)
      data.update({"exp": expire})
      return jwt.encode(data, secret, algorithm=ALGO)
  ```

- **Token Validation**:
  ```python
  def validateJWTNonHeader(token):
      try:
          return jwt.decode(token, secret, algorithms=ALGO)
      except JWTError:
          raise ValueError("Invalid JWT token")
  ```

---

### **1.3 Connection Management**
- **Connect a Client**:
  ```python
  def connect(self, websocket, room_id, client_id):
      self.connections[room_id].append((websocket, client_id))
      print(f"Added connection to room {room_id}. Total: {len(self.connections[room_id])}")
  ```

- **Disconnect a Client**:
  ```python
  def disconnect(self, client_id, room_id):
      self.connections[room_id] = [
          (ws, cid) for ws, cid in self.connections[room_id] if cid != client_id
      ]
      print(f"Removed connection from room {room_id}. Remaining: {len(self.connections[room_id])}")
  ```

- **Broadcast to Room**:
  ```python
  async def broadcast_to_room(self, sender_client_id, message, room_id):
      for websocket, client_id in self.connections[room_id]:
          if client_id != sender_client_id:
              await websocket.send_text(message)
  ```

- **Ping/Pong Mechanism**:
  ```python
  async def ping_pong(websocket: WebSocket):
      while True:
          try:
              await websocket.send_text("ping")
              await asyncio.sleep(30)  # Send ping every 30 seconds
          except Exception:
              break
  ```

- **Rate Limiting**:
  ```python
  from collections import defaultdict
  from time import time

  message_timestamps = defaultdict(list)

  async def rate_limit(client_id, max_messages=5, time_window=10):
      now = time()
      timestamps = message_timestamps[client_id]
      timestamps = [t for t in timestamps if now - t < time_window]
      message_timestamps[client_id] = timestamps
      if len(timestamps) >= max_messages:
          raise ValueError("Rate limit exceeded")
  ```

- **Validate Incoming Messages**:
  ```python
  def validate_message(message):
      if not isinstance(message, dict):
          raise ValueError("Message must be a dictionary")
      required_keys = {"name", "client_id", "message", "room_id", "timestamp"}
      if not required_keys.issubset(message.keys()):
          raise ValueError("Missing required keys in message")
  ```

---

### **1.4 WebSocket Endpoint**
- **Authenticate and Connect**:
  ```python
  @app.websocket("/ws/chat")
  async def chat(websocket: WebSocket):
      await websocket.accept()
      try:
          token = websocket.query_params.get("token")
          if not token:
              raise ValueError("Token is required")
          data = validateJWTNonHeader(token)
          client_id, room_id, name = data["client_id"], data["room_id"], data["name"]
          connection_manager.connect(websocket, room_id, client_id)
      except Exception as e:
          await websocket.close(code=1008, reason=str(e))
          return
  ```

- **Receive and Broadcast Messages**:
  ```python
  while True:
      try:
          message = await websocket.receive_text()
          validate_message(json.loads(message))
          broadcast_message = {
              "name": name,
              "client_id": client_id,
              "message": message,
              "room_id": room_id,
              "timestamp": datetime.utcnow().isoformat(),
          }
          await rate_limit(client_id)
          await connection_manager.broadcast_to_room(client_id, json.dumps(broadcast_message), room_id)
      except Exception as e:
          print(f"Error: {e}")
          break
  ```

---

### **1.5 Room Management**
- **Create a Room**:
  ```python
  @app.get("/create-room")
  def create(name):
      room_id = generateRoomCode()
      client_id = uuid4().hex
      token = createToken({"client_id": client_id, "room_id": room_id, "name": name})
      return {"status": "success", "token": token, "room_id": room_id, "client_id": client_id}
  ```

- **Join a Room**:
  ```python
  @app.get("/join-chat-room")
  def join_room(name, roomId: str):
      if not connection_manager.room_exists(roomId):
          return {"status": "error", "message": f"Room {roomId} does not exist."}
      client_id = uuid4().hex
      token = createToken({"client_id": client_id, "room_id": roomId, "name": name})
      return {"status": "success", "token": token, "room_id": roomId, "client_id": client_id}
  ```

---

## **2. Frontend: React Implementation**

### **2.1 WebSocket Connection**
- **Initialize WebSocket**:
  ```javascript
  useEffect(() => {
      wsRef.current = new WebSocket(wsUrl);

      const handleMessage = (event) => {
          const receivedMessage = JSON.parse(event.data);
          setMessages((prevMessages) => [...prevMessages, receivedMessage]);
      };

      wsRef.current.addEventListener("message", handleMessage);

      return () => {
          wsRef.current.removeEventListener("message", handleMessage);
          wsRef.current.close();
      };
  }, [wsUrl]);
  ```

---

### **2.2 Sending Messages**
- **Send a Message**:
  ```javascript
  const handleSendMessage = (message) => {
      if (wsRef.current.readyState === WebSocket.OPEN) {
          const messageObj = { name: userData.name, message, timestamp: new Date().toISOString() };
          wsRef.current.send(JSON.stringify(messageObj));
          setMessages((prevMessages) => [...prevMessages, messageObj]);
      }
  };
  ```

---

### **2.3 Handling Edge Cases**
- **Duplicate Messages**:
  - Ensure proper cleanup of event listeners in the `useEffect` cleanup function.
- **Abnormal Closure (Code 1006)**:
  - Add a ping/pong mechanism to keep the connection alive.
- **Mixed Content Issues**:
  - Use `wss://` for secure WebSocket connections.

---

## **3. Common Challenges and Solutions**

### **1. Duplicate Messages**
- **Cause**: Multiple event listeners or improper cleanup.
- **Solution**: Use `removeEventListener` in the cleanup function.

### **2. Abnormal Closure**
- **Cause**: Network issues or server-side errors.
- **Solution**: Add error handling and reconnection logic.

### **3. Token Validation**
- **Cause**: Invalid or expired JWT tokens.
- **Solution**: Validate tokens on the server and close the connection if invalid.

---

## **4. Best Practices**

1. **WebSocket Lifecycle Management**:
   - Properly initialize, handle, and clean up WebSocket connections.

2. **Error Handling**:
   - Handle errors gracefully on both the client and server.

3. **Authentication**:
   - Use JWT tokens to authenticate WebSocket connections.

4. **Scalability**:
   - Use a connection manager to handle multiple rooms and clients efficiently.

5. **Security**:
   - Use `wss://` for secure connections.
   - Validate all incoming messages and tokens.

---

## **5. Example Scenarios**

### **Scenario 1: User Joins a Room**
1. User sends a request to `/join-chat-room`.
2. Server validates the room and generates a JWT token.
3. User connects to the WebSocket endpoint with the token.

### **Scenario 2: User Sends a Message**
1. User sends a message via WebSocket.
2. Server broadcasts the message to all clients in the room, excluding the sender.

### **Scenario 3: User Disconnects**
1. WebSocket connection is closed.
2. Server removes the user from the room and cleans up resources.

---

## **6. Debugging Tips**

1. **Log Everything**:
   - Add logs for WebSocket lifecycle events (`open`, `message`, `close`, `error`).

2. **Test with Multiple Browsers**:
   - Test in Chrome, Firefox, and other browsers to identify browser-specific issues.

3. **Simulate Errors**:
   - Disconnect the server or introduce invalid tokens to test error handling.

4. **Monitor WebSocket State**:
   - Use browser developer tools to inspect WebSocket connections.

---

## **7. Key Takeaways**

- WebSocket is ideal for real-time communication but requires careful management of connections and state.
- JWT tokens provide a secure way to authenticate WebSocket connections.
- Proper error handling and cleanup are essential to avoid issues like duplicate messages or connection leaks.
- Testing with multiple clients and browsers is crucial to ensure robustness.
