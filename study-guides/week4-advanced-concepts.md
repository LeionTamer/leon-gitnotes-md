# Week 4: Advanced Concepts

## Pagination

### Offset-Based Pagination
```http
GET /api/products?limit=10&offset=20
```

```json
{
    "data": [...],
    "pagination": {
        "total": 100,
        "limit": 10,
        "offset": 20,
        "next": "/api/products?limit=10&offset=30",
        "previous": "/api/products?limit=10&offset=10"
    }
}
```

### Cursor-Based Pagination
```http
GET /api/products?limit=10&cursor=eyJpZCI6MTAwfQ==
```

```json
{
    "data": [...],
    "pagination": {
        "limit": 10,
        "next_cursor": "eyJpZCI6MjAwfQ==",
        "has_more": true
    }
}
```

## Filtering and Sorting

### Basic Filtering
```http
GET /api/products?category=electronics&price_min=100&price_max=500
GET /api/users?status=active&role=admin
```

### Advanced Filtering
```http
GET /api/products?filter=category:electronics AND price>=100 AND price<=500
GET /api/users?filter=status:active OR role:admin
```

### Sorting
```http
GET /api/products?sort=price:desc,name:asc
GET /api/users?sort=-created_at,+name
```

## Caching

### Client-Side Caching
```http
# Response Headers
Cache-Control: public, max-age=3600
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
Last-Modified: Wed, 03 Mar 2025 10:00:00 GMT
```

### Conditional Requests
```http
# Request Headers
If-None-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"
If-Modified-Since: Wed, 03 Mar 2025 10:00:00 GMT
```

### Server-Side Caching Example
```python
from functools import lru_cache

@lru_cache(maxsize=1000)
def get_product(product_id: int):
    return database.query(f"SELECT * FROM products WHERE id = {product_id}")
```

### Redis Caching Example
```python
import redis

redis_client = redis.Redis(host='localhost', port=6379)

def get_product(product_id: int):
    # Try to get from cache first
    cached = redis_client.get(f"product:{product_id}")
    if cached:
        return json.loads(cached)
    
    # If not in cache, get from database and cache it
    product = database.query(f"SELECT * FROM products WHERE id = {product_id}")
    redis_client.setex(f"product:{product_id}", 3600, json.dumps(product))
    return product
```