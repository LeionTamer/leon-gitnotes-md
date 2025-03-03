# Week 8: Testing and Documentation

## Testing Approaches

### Unit Testing
```typescript
// filepath: src/tests/user.service.test.ts
import { UserService } from '../services/user.service';

describe('UserService', () => {
    let userService: UserService;

    beforeEach(() => {
        userService = new UserService();
    });

    test('should create a new user', async () => {
        const userData = {
            name: 'John Doe',
            email: 'john@example.com'
        };

        const user = await userService.createUser(userData);
        expect(user).toHaveProperty('id');
        expect(user.name).toBe(userData.name);
    });
});
```

### Integration Testing
```javascript
// filepath: src/tests/api.integration.test.js
const request = require('supertest');
const app = require('../app');

describe('API Integration Tests', () => {
    test('GET /api/users should return list of users', async () => {
        const response = await request(app)
            .get('/api/users')
            .set('Authorization', `Bearer ${testToken}`);

        expect(response.status).toBe(200);
        expect(Array.isArray(response.body)).toBeTruthy();
    });
});
```

### End-to-End Testing
```typescript
// filepath: e2e/user-flow.test.ts
import { test, expect } from '@playwright/test';

test('complete user registration flow', async ({ page }) => {
    await page.goto('/register');
    await page.fill('#email', 'test@example.com');
    await page.fill('#password', 'secure123');
    await page.click('button[type="submit"]');

    await expect(page).toHaveURL('/dashboard');
});
```

## API Documentation

### OpenAPI/Swagger Specification
```yaml
# filepath: src/openapi/spec.yaml
openapi: 3.0.0
info:
  title: User Management API
  version: 1.0.0
paths:
  /users:
    get:
      summary: Get all users
      responses:
        '200':
          description: List of users
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'
    post:
      summary: Create a new user
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserRequest'
components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: string
        name:
          type: string
        email:
          type: string
```

### Swagger UI Integration
```typescript
// filepath: src/app.ts
import express from 'express';
import swaggerUi from 'swagger-ui-express';
import swaggerDocument from './openapi/spec.yaml';

const app = express();

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument));
```