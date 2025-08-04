# 🎨 System Design Cheat Sheet
*Modern Full-Stack Architecture Patterns*

## 🎯 Core Concepts

**Scalability**: Handle increased load  
**Reliability**: System stays operational  
**Availability**: System accessible when needed  
**Consistency**: Data accuracy across nodes  
**Partition Tolerance**: Works despite network failures  

**CAP Theorem**: Can only guarantee 2 of 3 (Consistency, Availability, Partition tolerance)

## 🏠 System Architecture Layers

```
┌─────────────────────────────────────────────┐
│              Load Balancer                  │
├─────────────────────────────────────────────┤
│    API Gateway    │    Web Servers       │
├─────────────────────────────────────────────┤
│ Service A │ Service B │ Service C    │
├─────────────────────────────────────────────┤
│ Cache (Redis) │ Message Queue      │
├─────────────────────────────────────────────┤
│         Database Cluster                  │
└─────────────────────────────────────────────┘
```

## 📋 Design Patterns

**Microservices**: Small, independent services  
**Event-Driven**: Services communicate via events  
**CQRS**: Separate read/write operations  
**Circuit Breaker**: Prevent cascading failures  
**Bulkhead**: Isolate resources to prevent failure spread

## 📊 Scaling Strategies

**Horizontal Scaling**: Add more servers  
**Vertical Scaling**: Upgrade existing hardware  
**Database Sharding**: Split data across multiple DBs  
**Read Replicas**: Separate read/write operations  
**CDN**: Cache static content globally  
**Load Balancing**: Distribute traffic across servers

## 🎡 Design Examples

### Chat Application Design
```
Users → Load Balancer → API Gateway → Auth Service
                     │
                     ↓
        WebSocket Servers ↔ Message Queue ↔ Database
                     │
                     ↓
             Notification Service
```

**Key Decisions:**
- **WebSocket**: Real-time messaging
- **Message Queue**: Ensure delivery
- **Database**: Store chat history
- **Load Balancer**: Handle multiple connections

### E-commerce Platform
```
Client → CDN → Load Balancer → API Gateway
                              │
         ┌────────────────────┼───────────┐
         │                     │               │
    User Service    Product Service    Order Service
         │                     │               │
    Users DB        Products DB       Orders DB
```

**Challenges:**
- **Consistency**: Order must deduct inventory
- **Scalability**: Handle traffic spikes
- **Performance**: Fast product search

## 🗄️ Database Design

**SQL vs NoSQL**
- **SQL**: ACID, relationships, complex queries
- **NoSQL**: Scale, flexibility, eventual consistency

**Database Patterns**
- **Master-Slave**: Write to master, read from slaves
- **Sharding**: Horizontal partitioning
- **Federation**: Split DBs by function
- **Denormalization**: Trade storage for speed

```sql
-- Chat Message Schema
CREATE TABLE messages (
    id UUID PRIMARY KEY,
    room_id UUID NOT NULL,
    user_id UUID NOT NULL,
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    INDEX(room_id, created_at)
);
```

## 💡 Caching Strategies

**Cache-Aside**: App manages cache  
**Write-Through**: Write to cache + DB  
**Write-Behind**: Write to cache first  
**Refresh-Ahead**: Proactively refresh  

**Cache Levels**
- **Browser**: Static assets
- **CDN**: Global content delivery
- **Application**: In-memory cache
- **Database**: Query result cache

## 🚀 Performance Optimization

**Frontend**
- **Code Splitting**: Load only needed code
- **Lazy Loading**: Load images/components on demand
- **Minification**: Reduce bundle size
- **CDN**: Serve static assets globally

**Backend**
- **Connection Pooling**: Reuse DB connections
- **Async Processing**: Non-blocking operations
- **Database Indexing**: Fast query lookup
- **Compression**: Reduce response size

**Monitoring**
- **Metrics**: Response time, throughput, errors
- **Logs**: Structured logging for debugging
- **Alerts**: Notify on threshold breaches

## 🔒 Security Considerations

**Authentication & Authorization**
- **JWT**: Stateless token-based auth
- **OAuth**: Third-party authentication
- **RBAC**: Role-based access control

**Data Protection**
- **HTTPS**: Encrypt data in transit
- **Input Validation**: Prevent injection attacks
- **Rate Limiting**: Prevent abuse
- **CORS**: Control cross-origin requests

**Common Vulnerabilities**
- **SQL Injection**: Use parameterized queries
- **XSS**: Sanitize user input
- **CSRF**: Use tokens for state changes

## 🎯 System Design Interview Process

**1. Clarify Requirements** (5 min)
- Who are the users? How many?
- What features are needed?
- What's the scale? (reads/writes per second)
- What are the constraints?

**2. High-Level Design** (10 min)
- Draw major components
- Show data flow
- Identify key services

**3. Deep Dive** (15 min)
- Database schema
- API design
- Algorithms for core features
- Handle bottlenecks

**4. Scale & Optimize** (10 min)
- Identify bottlenecks
- Add caching, load balancing
- Discuss trade-offs

## 💡 Interview Tips

✅ **Start simple**, then add complexity  
✅ **Think out loud** - show your process  
✅ **Ask clarifying questions**  
✅ **Consider trade-offs** (consistency vs availability)  
✅ **Back of envelope calculations** for scale  
✅ **Draw diagrams** to visualize architecture  
✅ **Discuss alternatives** and why you chose one

## 📋 Behavioral Questions to Prepare

### Tell Me About Yourself
- **Structure**: Present → Past → Future
- **Example**: "I'm currently a full-stack developer working with React and FastAPI. Previously, I built [specific project] which taught me [specific skill]. I'm looking to grow in [specific direction]."

### Common Questions & STAR Method (Situation, Task, Action, Result)

1. **"Tell me about a challenging bug you fixed"**
   - Situation: What was the problem?
   - Task: What needed to be done?
   - Action: How did you approach it?
   - Result: What was the outcome?

2. **"How do you handle tight deadlines?"**
   - Prioritization strategies
   - Communication with stakeholders
   - Technical debt decisions

3. **"Describe a time you disagreed with a team member"**
   - Focus on collaboration and resolution
   - Show respect for different perspectives

## 🏗️ Advanced System Design Scenarios

### Scenario 1: Designing a Multi-Robot Warehouse System

**Requirements:**
- 100+ robots operating simultaneously
- Real-time inventory tracking
- Collision avoidance
- Task optimization
- Fault tolerance

**System Architecture:**
```
┌─────────────────────────────────────────────────────────┐
│                 Central Orchestrator                    │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │
│  │   Task      │  │  Inventory  │  │  Route      │     │
│  │ Scheduler   │  │   Manager   │  │  Planner    │     │
│  └─────────────┘  └─────────────┘  └─────────────┘     │
└─────────────────────────────────────────────────────────┘
                             │
┌─────────────────────────────────────────────────────────┐
│                  FABRIC Network                         │
│     Robot A ←→ Robot B ←→ Robot C ←→ ... ←→ Robot N     │
└─────────────────────────────────────────────────────────┘
```

**Key Design Decisions:**
- **Data Consistency**: Use distributed consensus for critical decisions
- **Scalability**: Partition robots by warehouse zones
- **Fault Tolerance**: Leader election for robot coordinators
- **Real-time**: WebSocket for instant coordination

### Scenario 2: LLM-Powered Humanoid Robot

**Challenges:**
- Latency: 100ms response time for natural interaction
- Context: Maintain conversation state across sessions
- Safety: Critical decisions must be deterministic
- Learning: Continuous improvement from interactions

**Architecture:**
```python
class HumanoidRobotSystem:
    def __init__(self):
        self.perception = MultimodalPerception()  # Vision + Audio + Touch
        self.cognition = LLMEngine()             # Planning and reasoning
        self.memory = ConversationMemory()       # Context preservation
        self.safety = SafetyController()         # Override system
        self.actuation = MotorController()       # Physical actions
    
    async def process_interaction(self, human_input):
        # Step 1: Perceive environment
        context = await self.perception.analyze_environment()
        
        # Step 2: Retrieve relevant memory
        conversation_context = self.memory.get_context(human_input)
        
        # Step 3: Generate response (with timeout)
        response = await asyncio.wait_for(
            self.cognition.generate_response(human_input, context, conversation_context),
            timeout=0.1  # 100ms limit
        )
        
        # Step 4: Safety check before action
        if self.safety.is_safe_action(response.planned_action):
            await self.actuation.execute(response.planned_action)
        
        # Step 5: Update memory
        self.memory.store_interaction(human_input, response)
        
        return response.verbal_response
```

### Scenario 3: Decentralized Robot Learning Network

**Problem**: How do you enable 1M+ robots to learn from each other?

**Solution - Federated Learning + Blockchain**:
```python
class FederatedRobotLearning:
    def __init__(self, robot_id):
        self.robot_id = robot_id
        self.local_model = RobotBehaviorModel()
        self.fabric_client = FabricClient()
    
    async def contribute_learning(self, experience_data):
        # 1. Train locally on new experience
        model_update = self.local_model.train_on_experience(experience_data)
        
        # 2. Compute gradient/update
        gradient = self.compute_gradient(model_update)
        
        # 3. Submit to FABRIC network with proof of work
        contribution_hash = await self.fabric_client.submit_update({
            "robot_id": self.robot_id,
            "gradient": gradient,
            "experience_quality": self.assess_quality(experience_data),
            "timestamp": time.time()
        })
        
        # 4. Receive aggregated updates from network
        global_update = await self.fabric_client.get_global_update()
        self.local_model.apply_update(global_update)
        
        return contribution_hash
```

## 🎯 OpenMind-Specific Interview Topics

### Technical Deep Dives

**1. "How would you design real-time coordination between 1000 robots?"**
- **P2P vs Centralized**: Discuss trade-offs (FABRIC uses P2P)
- **Consensus Algorithms**: Raft, PBFT for critical decisions
- **Network Partitioning**: How robots handle disconnection
- **Latency Requirements**: Different requirements for different actions

**2. "How do you ensure robot identity and prevent malicious actors?"**
- **Cryptographic Identity**: Public/private key pairs
- **Proof of Location**: GPS + sensor verification
- **Reputation Systems**: Track robot behavior over time
- **Hardware Attestation**: Secure boot and trusted execution

**3. "How would you integrate multiple LLMs with a robot's perception system?"**
- **Model Selection**: Different models for different tasks
- **Context Management**: Maintaining state across modalities
- **Latency Optimization**: Caching, streaming, early stopping
- **Fallback Systems**: When AI fails, what happens?

### Architecture Questions

**"Design OpenMind's OM1 Runtime"**

```python
class OM1Runtime:
    def __init__(self):
        # Core AI capabilities
        self.llm_engine = LLMEngine()
        self.vision_system = VisionSystem()
        self.planning_engine = PlanningEngine()
        
        # Hardware abstraction
        self.hardware_adapter = HardwareAdapter()
        
        # Networking
        self.fabric_client = FabricClient()
        
        # Safety systems
        self.safety_monitor = SafetyMonitor()
        
    async def main_loop(self):
        while True:
            # 1. Perceive environment
            sensor_data = await self.hardware_adapter.get_sensor_data()
            vision_data = await self.vision_system.process(sensor_data.camera)
            
            # 2. Update world model
            world_state = self.update_world_model(sensor_data, vision_data)
            
            # 3. Plan next action
            planned_action = await self.planning_engine.plan(world_state)
            
            # 4. Safety check
            if not self.safety_monitor.is_safe(planned_action, world_state):
                planned_action = self.safety_monitor.get_safe_action()
            
            # 5. Execute action
            await self.hardware_adapter.execute_action(planned_action)
            
            # 6. Share with network (if beneficial)
            if self.should_share_experience(world_state, planned_action):
                await self.fabric_client.share_experience({
                    "state": world_state,
                    "action": planned_action,
                    "outcome": self.evaluate_outcome()
                })
            
            await asyncio.sleep(0.01)  # 100Hz control loop
```

## 💭 Problem-Solving Approach

### When Given a Coding Challenge:

1. **Clarify Requirements**
   - "What should happen if the input is empty?"
   - "Are there any constraints on time/space complexity?"
   - "Should I handle edge cases like null values?"

2. **Think Out Loud**
   - Explain your approach before coding
   - Discuss trade-offs between solutions
   - Mention alternative approaches

3. **Start Simple**
   - Get a working solution first
   - Then optimize if needed
   - Test with examples

4. **Code Organization**
   - Use meaningful variable names
   - Add comments for complex logic
   - Consider error handling

## 🔧 Technical Deep Dives

### React Performance Questions

**"How would you optimize a slow React component?"**
- Check for unnecessary re-renders (React DevTools)
- Use `React.memo()` for expensive components
- Optimize state structure (avoid deeply nested objects)
- Use `useMemo`/`useCallback` for expensive calculations
- Consider component splitting

**"Explain React's reconciliation process"**
- Virtual DOM diffing algorithm
- Key prop importance in lists
- Fiber architecture (React 16+)

### JavaScript Fundamentals

**"Explain closures"**
```js
function outer(x) {
  return function inner(y) {
    return x + y; // inner has access to x
  };
}
const add5 = outer(5);
console.log(add5(10)); // 15
```

**"What's the event loop?"**
- Call stack → Web APIs → Callback queue → Event loop
- Microtasks vs Macrotasks
- Promise resolution priority

### API Design Questions

**"How would you design a REST API for a chat system?"**

```
GET    /api/chats          - List user's chats
POST   /api/chats          - Create new chat
GET    /api/chats/:id      - Get specific chat history
POST   /api/chats/:id/messages - Send message
DELETE /api/chats/:id      - Delete chat
```

**Status Codes:**
- 200: Success with data
- 201: Created successfully
- 204: Success, no content
- 400: Bad request (validation error)
- 401: Unauthorized (need auth)
- 403: Forbidden (auth but no permission)
- 404: Not found
- 429: Rate limited
- 500: Server error

## 🎨 CSS & Design Questions

**"How would you make a responsive navbar?"**
```css
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

@media (max-width: 768px) {
  .navbar {
    flex-direction: column;
  }
  
  .nav-menu {
    display: none;
  }
  
  .nav-menu.active {
    display: flex;
    flex-direction: column;
  }
}
```

**"Explain CSS specificity"**
- Inline styles (1000)
- IDs (100)
- Classes, attributes, pseudo-classes (10)  
- Elements and pseudo-elements (1)

## 🚀 Questions to Ask Them

### About the Role
- "What does a typical day look like?"
- "What are the biggest technical challenges the team faces?"
- "How do you measure success in this position?"

### About the Team
- "How does the team collaborate on projects?"
- "What's the code review process like?"
- "How do you handle technical debt?"

### About Growth
- "What opportunities are there for learning and development?"
- "Where do you see this role in 6 months?"

## 📝 Final Tips

### During the Interview
- **Ask questions throughout** - shows engagement
- **Admit when you don't know something** - then explain how you'd find out
- **Show your thought process** - they want to see how you think
- **Be specific with examples** - use your actual projects
- **Practice whiteboarding** - get comfortable coding without autocomplete

### Red Flags to Avoid
- Badmouthing previous employers
- Not asking any questions
- Being unprepared about the company
- Not being able to explain your own code
- Getting defensive about feedback

### Your Chatbot Project Talking Points
- **Authentication flow** you implemented
- **Real-time chat** functionality decisions
- **State management** patterns you used
- **API design** choices you made
- **Problem-solving** examples from debugging

---

**Remember**: Interviews are conversations, not interrogations. Show curiosity, enthusiasm, and genuine interest in solving problems!