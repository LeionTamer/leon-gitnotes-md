# Week 2: URL Design and API Versioning

## Resource Naming

### Best Practices
- Use nouns instead of verbs
- Prefer plural nouns for collections
- Keep URLs simple and readable
- Use lowercase letters
- Use hyphens instead of underscores

### Examples
```http
# Good Examples
GET /api/users
GET /api/user-profiles
GET /api/orders/123/items

# Bad Examples
GET /api/getUsers
GET /api/user_profiles
GET /api/Orders
```

### Hierarchical Relationships
- Parent-child relationships
- Resource nesting
- Depth limitations

```http
/api/departments/{departmentId}/employees
/api/orders/{orderId}/items/{itemId}
```

## Parameter Types

### Path Parameters
- Used for identifying specific resources
- Required parameters
- Part of resource hierarchy

```http
/api/users/{userId}
/api/posts/{postId}/comments/{commentId}
```

### Query Parameters
- Used for filtering, sorting, and pagination
- Optional parameters
- Non-hierarchical data

```http
/api/users?role=admin&status=active
/api/products?category=electronics&sort=price&order=desc
```

## API Versioning

### URI Versioning
- Version in the URL path
- Simple to implement
- Clear and explicit

```http
/api/v1/users
/api/v2/users
```

### Header Versioning
- Custom headers for version specification
- Clean URLs
- More flexible

```http
Accept: application/vnd.company.api+json;version=1
Accept: application/vnd.company.api+json;version=2
```

### Content Negotiation
- Using Accept headers
- Content-Type versioning
- Media type versioning

```http
Accept: application/vnd.company.api-v1+json
Accept: application/vnd.company.api-v2+json
```

### Version Strategy Comparison
| Strategy | Pros | Cons |
|----------|------|------|
| URI | Simple, explicit | URL pollution |
| Header | Clean URLs | More complex |
| Media Type | Standard-based | Complex negotiation |