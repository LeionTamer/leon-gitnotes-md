# Week 1: Foundations

## REST Basics

### Architectural Style
- Representational State Transfer (REST) principles
- Client-server architecture
- Uniform interface
- Layered system

### Resource Identification
- URI structure
  - Base URL: `https://api.example.com`
  - Resource path: `/resources/{resource-id}`
  - Collection paths: `/resources`
  - Nested resources: `/resources/{resource-id}/sub-resources`
  - Query parameters: `/resources?filter=value`

### Statelessness
- Session management
- Request independence
- Scalability benefits

#### Key Principles of Statelessness
1. **Complete Requests**
   - Each request contains all information needed
   - No server-side session storage
   - Authentication tokens sent with each request

2. **Benefits**
   - Improved scalability (any server can handle any request)
   - Better reliability
   - Simplified server-side architecture
   - Easier load balancing
   - Enhanced visibility for monitoring

3. **Implementation Approaches**
   - JWT (JSON Web Tokens) for authentication
   - Request/Response headers for context
   - Query parameters for state information
   - Client-side state management

#### Example Stateless vs Stateful Requests
```http
# Stateful (Bad)
GET /api/user/preferences
Cookie: sessionId=abc123

# Stateless (Good)
GET /api/user/preferences
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

```ts
  async login(credentials: UserCredentials): Promise<void> {
    const response = await fetch(`${this.baseUrl}/login`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(credentials)
    });

    if (response.ok) {
      const { token } = await response.json();
      // Store JWT in sessionStorage
      sessionStorage.setItem('authToken', token);
    }
  }
```

```python
from fastapi import FastAPI, Depends

@app.post("/login")
async def login(username: str, password: str):
    # In practice, verify credentials against database
    if username == "test" and password == "test":
        token = create_jwt_token({"sub": username})
        return {"token": token}
    raise HTTPException(status_code=401)

@app.get("/api/user/preferences")
async def get_preferences(current_user: str = Depends(get_current_user)):
    return {
        "theme": "dark",
        "notifications": True,
        "username": current_user
    }
```

#### Common Challenges
- Authentication management
- Caching strategies
- Performance considerations
- Handling complex workflows

### Caching Strategies

#### Client-Side Caching
- Browser cache using HTTP headers
  - `Cache-Control`
  - `ETag`
  - `Last-Modified`
  - `Expires`

#### Server-Side Caching
1. **In-Memory Caching**
   - Redis
   - Memcached
   - Application memory
   - Fastest retrieval times
   - Limited by available memory

2. **Database Caching**
   - Query cache
   - Result cache
   - Database-specific solutions
   - Useful for complex queries

3. **CDN Caching**
   - Geographic distribution
   - Edge locations
   - Best for static content
   - Reduced latency

#### Cache Control Headers
```http
# Example Cache Headers
Cache-Control: public, max-age=3600
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
Last-Modified: Wed, 21 Oct 2015 07:28:00 GMT
```

#### Implementation Patterns
1. **Cache-Aside (Lazy Loading)**
   ```python
   def get_data(key):
       value = cache.get(key)
       if value is None:
           value = database.query(key)
           cache.set(key, value)
       return value
   ```

2. **Write-Through**
   ```python
   def save_data(key, value):
       database.save(key, value)
       cache.set(key, value)
   ```

#### Best Practices
- Set appropriate TTL (Time-To-Live)
- Implement cache invalidation
- Use cache versioning
- Monitor cache hit ratios
- Consider cache coherency
- Handle cache stampede

#### Common Challenges
- Cache invalidation
- Data consistency
- Memory management
- Cold start problems
- Cache penetration

## HTTP Methods

### Core Methods
- GET: Retrieve resources
- POST: Create new resources
- PUT: Update existing resources
- DELETE: Remove resources
- PATCH: Partial updates

### Best Practices
- Idempotency
- Safe methods
- Request/response patterns

## HATEOAS

### Concept
- Hypermedia as the Engine of Application State
- Dynamic navigation through APIs
- Self-documenting APIs

### Implementation
- Link relations
- Resource discovery
- State transitions

## Additional Architectural Styles

### SOAP
- XML-based protocol
- Strong typing and contracts (WSDL)
- Enterprise features (WS-Security, WS-ReliableMessaging)
- Platform and language independent

### GraphQL
- Query-based architecture
- Client-driven data fetching
- Strong typing system
- Real-time capabilities with subscriptions

### gRPC
- Binary protocol (Protocol Buffers)
- HTTP/2 based
- Bi-directional streaming
- Code generation tools