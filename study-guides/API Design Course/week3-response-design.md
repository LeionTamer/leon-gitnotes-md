# Week 3: Response Design and Error Handling

## HTTP Status Codes

### Success Codes (2xx)
- 200 OK: Standard success response
- 201 Created: Resource creation success
- 204 No Content: Success with no response body

### Client Error Codes (4xx)
- 400 Bad Request: Invalid syntax
- 401 Unauthorized: Authentication required
- 403 Forbidden: Authenticated but not authorized
- 404 Not Found: Resource doesn't exist
- 409 Conflict: Request conflicts with server state

### Server Error Codes (5xx)
- 500 Internal Server Error: Generic server error
- 502 Bad Gateway: Invalid upstream response
- 503 Service Unavailable: Server temporarily unavailable

## Error Handling

### Standard Error Response Format
```json
{
    "error": {
        "code": "INVALID_INPUT",
        "message": "The request parameters are invalid",
        "details": [
            {
                "field": "email",
                "message": "Must be a valid email address"
            }
        ],
        "timestamp": "2025-03-03T10:00:00Z",
        "requestId": "req-123-abc"
    }
}
```

### Global Exception Handling Example
```python
class CustomException(HTTPException):
    def __init__(
        self,
        status_code: int,
        error_code: str,
        message: str,
        details: Optional[list] = None
    ):
        self.status_code = status_code
        self.error_code = error_code
        self.message = message
        self.details = details

@app.exception_handler(CustomException)
async def custom_exception_handler(request: Request, exc: CustomException):
    return JSONResponse(
        status_code=exc.status_code,
        content={
            "error": {
                "code": exc.error_code,
                "message": exc.message,
                "details": exc.details,
                "timestamp": datetime.utcnow().isoformat(),
                "requestId": request.state.request_id
            }
        }
    )

@app.get("/items/{item_id}")
async def read_item(item_id: int):
    if item_id == 404:
        raise CustomException(
            status_code=404,
            error_code="RESOURCE_NOT_FOUND",
            message=f"Item {item_id} not found",
            details=[{"field": "item_id", "message": "Item does not exist"}]
        )
    return {"item_id": item_id}
```

## Data Serialization

### JSON Format
```json
{
    "id": 1,
    "name": "Product Name",
    "price": 29.99,
    "categories": ["electronics", "accessories"],
    "metadata": {
        "created": "2025-03-03T10:00:00Z",
        "lastModified": "2025-03-03T10:00:00Z"
    }
}
```

### XML Format
```xml
<?xml version="1.0" encoding="UTF-8"?>
<product>
    <id>1</id>
    <name>Product Name</name>
    <price>29.99</price>
    <categories>
        <category>electronics</category>
        <category>accessories</category>
    </categories>
    <metadata>
        <created>2025-03-03T10:00:00Z</created>
        <lastModified>2025-03-03T10:00:00Z</lastModified>
    </metadata>
</product>
```