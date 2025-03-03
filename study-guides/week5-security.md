# Week 5: Security

## Authentication

### OAuth 2.0
```javascript
// Example OAuth 2.0 configuration
const oauth2Config = {
    authorizationURL: 'https://auth.example.com/authorize',
    tokenURL: 'https://auth.example.com/token',
    clientID: 'YOUR_CLIENT_ID',
    clientSecret: 'YOUR_CLIENT_SECRET',
    callbackURL: 'https://api.example.com/callback'
};
```

### JWT Implementation
```python
# JWT token generation and validation
from jwt import encode, decode

def generate_token(user_data):
    return encode(
        payload={
            'user_id': user_data['id'],
            'exp': datetime.utcnow() + timedelta(hours=24)
        },
        key='your-secret-key',
        algorithm='HS256'
    )

def validate_token(token):
    try:
        return decode(token, 'your-secret-key', algorithms=['HS256'])
    except Exception:
        return None
```

## Authorization

### Role-Based Access Control (RBAC)
```java
@PreAuthorize("hasRole('ADMIN')")
@GetMapping("/users")
public List<User> getAllUsers() {
    return userService.findAll();
}

@PreAuthorize("hasAnyRole('ADMIN', 'MANAGER')")
@GetMapping("/reports")
public List<Report> getReports() {
    return reportService.findAll();
}
```

### Attribute-Based Access Control (ABAC)
```python
def check_access(user, resource, action):
    policy = {
        'read': lambda u, r: u.department == r.department,
        'write': lambda u, r: u.role == 'ADMIN' and u.department == r.department
    }
    return policy[action](user, resource)
```

## Rate Limiting
```python
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse
from slowapi import Limiter
from slowapi.util import get_remote_address

limiter = Limiter(key_func=get_remote_address)
app = FastAPI()

@app.get("/api/resource")
@limiter.limit("5/minute")
async def get_resource(request: Request):
    return {"data": "resource"}
```

## Input Validation
```typescript
interface UserInput {
    email: string;
    password: string;
    age: number;
}

function validateUser(input: UserInput): ValidationResult {
    const errors = [];
    
    if (!isValidEmail(input.email)) {
        errors.push('Invalid email format');
    }
    
    if (input.password.length < 8) {
        errors.push('Password must be at least 8 characters');
    }
    
    if (input.age < 18) {
        errors.push('Must be 18 or older');
    }
    
    return {
        isValid: errors.length === 0,
        errors
    };
}
```

## CORS Configuration
```typescript
// Express.js CORS setup
import cors from 'cors';

app.use(cors({
    origin: ['https://trusted-domain.com'],
    methods: ['GET', 'POST', 'PUT', 'DELETE'],
    allowedHeaders: ['Content-Type', 'Authorization'],
    credentials: true,
    maxAge: 86400
}));
```

## SQL Injection Prevention
```typescript
// Using parameterized queries
import { Pool } from 'pg';

const pool = new Pool();

async function getUserById(id: number) {
    // Safe way
    const result = await pool.query(
        'SELECT * FROM users WHERE id = $1',
        [id]
    );
    
    // Unsafe way - DON'T DO THIS
    // const result = await pool.query(
    //     `SELECT * FROM users WHERE id = ${id}`
    // );
    
    return result.rows[0];
}
```