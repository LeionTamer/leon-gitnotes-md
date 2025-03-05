# Week 4: Advanced Concepts

## Pagination Strategies

### Offset-Based Pagination
- Uses `limit` and `offset` parameters
- Simple to implement
- Common in SQL databases

```http
GET /api/articles?limit=10&offset=20
```

#### Example Implementation
```python
@app.get("/articles")
async def get_articles(limit: int = 10, offset: int = 0):
    articles = db.query(Article).offset(offset).limit(limit).all()
    return {
        "data": articles,
        "metadata": {
            "limit": limit,
            "offset": offset,
            "total": db.query(Article).count()
        }
    }
```

#### Pros
- Simple to implement
- Works well with known total counts
- Easy to jump to specific pages

#### Cons
- Performance issues with large offsets
- Inconsistent results with frequent updates
- Skipped or duplicate items possible

### Cursor-Based Pagination
- Uses a pointer to the last item
- Based on unique, sequential values
- Better for real-time data

```http
GET /api/articles?limit=10&cursor=eyJpZCI6MTAwfQ==
```

#### Example Implementation
```python
@app.get("/articles")
async def get_articles(limit: int = 10, cursor: str = None):
    query = db.query(Article).order_by(Article.id)
    
    if cursor:
        last_id = decode_cursor(cursor)
        query = query.filter(Article.id > last_id)
    
    articles = query.limit(limit).all()
    next_cursor = encode_cursor(articles[-1].id) if articles else None
    
    return {
        "data": articles,
        "metadata": {
            "next_cursor": next_cursor,
            "limit": limit
        }
    }
```

#### Pros
- Consistent results
- Better performance
- Works well with real-time data
- No skipped or duplicate items

#### Cons
- Can't jump to specific pages
- More complex to implement
- Requires stable sorting key

### Best Practices
- Use cursor-based for:
  - Real-time data
  - Large datasets
  - Mobile apps
  - Infinite scroll

- Use offset-based for:
  - Admin interfaces
  - Small datasets
  - When page jumping is needed
  - Simple CRUD applications

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