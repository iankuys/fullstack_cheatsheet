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