# Week 6: API Gateways and Monitoring

## API Gateways

### Key Features
- Request routing
- Authentication/Authorization
- Rate limiting
- Caching
- Request/Response transformation
- Load balancing
- Monitoring and analytics

### Kong Gateway Example
```yaml
# Kong declarative configuration
_format_version: "2.1"
_transform: true

services:
  - name: users-service
    url: http://users-service:8080
    routes:
      - name: users-route
        paths:
          - /api/users
    plugins:
      - name: rate-limiting
        config:
          minute: 100
      - name: jwt
        config:
          secret_is_base64: false
```

### API Gateway Implementation
```typescript
// Simple API Gateway implementation
import express from 'express';
import httpProxy from 'http-proxy';

const app = express();
const proxy = httpProxy.createProxyServer();

// Service registry
const services = {
    users: 'http://localhost:3001',
    products: 'http://localhost:3002',
    orders: 'http://localhost:3003'
};

// Route handler
app.use('/api/:service', (req, res) => {
    const service = req.params.service;
    const target = services[service];
    
    if (!target) {
        return res.status(404).send('Service not found');
    }
    
    proxy.web(req, res, { target });
});
```

## API Monitoring

### Prometheus Metrics
```typescript
import express from 'express';
import prometheus from 'prom-client';

const httpRequestDuration = new prometheus.Histogram({
    name: 'http_request_duration_seconds',
    help: 'Duration of HTTP requests in seconds',
    labelNames: ['method', 'route', 'status_code']
});

app.use((req, res, next) => {
    const start = Date.now();
    res.on('finish', () => {
        const duration = Date.now() - start;
        httpRequestDuration
            .labels(req.method, req.route.path, res.statusCode)
            .observe(duration / 1000);
    });
    next();
});
```

### API Logging Example
```typescript
import winston from 'winston';

const logger = winston.createLogger({
    level: 'info',
    format: winston.format.json(),
    transports: [
        new winston.transports.File({ filename: 'error.log', level: 'error' }),
        new winston.transports.File({ filename: 'combined.log' })
    ]
});

// Middleware for request logging
app.use((req, res, next) => {
    const startTime = Date.now();
    
    res.on('finish', () => {
        logger.info({
            method: req.method,
            url: req.url,
            status: res.statusCode,
            duration: Date.now() - startTime,
            userAgent: req.get('user-agent')
        });
    });
    
    next();
});
```

### Tracing with OpenTelemetry
```typescript
import { trace } from '@opentelemetry/api';
import { NodeTracerProvider } from '@opentelemetry/node';
import { SimpleSpanProcessor } from '@opentelemetry/tracing';
import { JaegerExporter } from '@opentelemetry/exporter-jaeger';

const provider = new NodeTracerProvider();
const exporter = new JaegerExporter();
provider.addSpanProcessor(new SimpleSpanProcessor(exporter));
provider.register();

const tracer = trace.getTracer('example-service');

app.get('/api/users', async (req, res) => {
    const span = tracer.startSpan('get-users');
    try {
        const users = await getUsersFromDB();
        span.end();
        res.json(users);
    } catch (error) {
        span.recordException(error);
        span.end();
        res.status(500).json({ error: 'Internal server error' });
    }
});
```