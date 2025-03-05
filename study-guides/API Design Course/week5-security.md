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

```python
from fastapi import FastAPI, Depends, HTTPException, status
from typing import List

app = FastAPI()

# Dummy user model and retrieval
class User:
    def __init__(self, username: str, roles: List[str]):
        self.username = username
        self.roles = roles

def get_current_user():
    # Simulated user; replace with real authentication logic
    return User("johndoe", ["ADMIN"])

def role_required(required_roles: List[str]):
    def dependency(user: User = Depends(get_current_user)):
        if not any(role in user.roles for role in required_roles):
            raise HTTPException(
                status_code=status.HTTP_403_FORBIDDEN,
                detail="Operation not permitted"
            )
        return user
    return dependency

@app.get("/users")
def get_all_users(user: User = Depends(role_required(["ADMIN"]))):
    # ...existing logic to fetch users...
    return {"users": "list of users"}

@app.get("/reports")
def get_reports(user: User = Depends(role_required(["ADMIN", "MANAGER"]))):
    # ...existing logic to fetch reports...
    return {"reports": "list of reports"}
```

### Attribute-Based Access Control (ABAC)

```python
from fastapi import Depends, HTTPException, status

# Using the same User model and get_current_user from above

def check_access(action: str):
    def dependency(user: User = Depends(get_current_user)):
        # Dummy resource attributes; in practice, fetch the resource or its details
        resource = {"department": "sales"}
        policy = {
            "read": lambda u, r: hasattr(u, "department") and u.department == r["department"],
            "write": lambda u, r: "ADMIN" in u.roles and hasattr(u, "department") and u.department == r["department"]
        }
        if not policy[action](user, resource):
            raise HTTPException(
                status_code=status.HTTP_403_FORBIDDEN,
                detail="Not authorized"
            )
        return user
    return dependency

@app.get("/secure-resource")
def secure_resource(user: User = Depends(check_access("read"))):
    # ...existing resource logic...
    return {"resource": "secured data"}
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
```python
from fastapi import FastAPI, HTTPException
from sqlmodel import SQLModel, Field, create_engine, Session, select

app = FastAPI()
DATABASE_URL = "postgresql://user:password@localhost/dbname"
engine = create_engine(DATABASE_URL)

class User(SQLModel, table=True):
    id: int = Field(default=None, primary_key=True)
    name: str
    email: str

@app.on_event("startup")
def on_startup():
    SQLModel.metadata.create_all(engine)

@app.get("/users/{user_id}")
def read_user(user_id: int):
    with Session(engine) as session:
        statement = select(User).where(User.id == user_id)
        user = session.exec(statement).first()
        if user is None:
            raise HTTPException(status_code=404, detail="User not found")
        return user
```