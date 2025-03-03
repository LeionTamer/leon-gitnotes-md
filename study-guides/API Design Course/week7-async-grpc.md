# Week 7: Asynchronous Processing and gRPC/WebSockets

## Background Jobs with Message Queues

### RabbitMQ Example
```typescript
// filepath: src/messaging/rabbitmq.ts
import amqp from 'amqplib';

async function setupQueue() {
    const connection = await amqp.connect('amqp://localhost');
    const channel = await connection.createChannel();
    
    const queue = 'task_queue';
    await channel.assertQueue(queue, { durable: true });
    
    return { connection, channel, queue };
}

// Producer
async function sendTask(task: any) {
    const { channel, queue } = await setupQueue();
    channel.sendToQueue(queue, Buffer.from(JSON.stringify(task)), {
        persistent: true
    });
}

// Consumer
async function processTask() {
    const { channel, queue } = await setupQueue();
    
    channel.consume(queue, (msg) => {
        const task = JSON.parse(msg!.content.toString());
        // Process task here
        channel.ack(msg!);
    });
}
```

### Apache Kafka Example
```java
// filepath: src/main/java/com/example/kafka/KafkaProducer.java
@Service
public class KafkaProducer {
    private final KafkaTemplate<String, String> kafkaTemplate;
    
    public void sendMessage(String topic, String message) {
        kafkaTemplate.send(topic, message)
            .addCallback(
                result -> log.info("Message sent successfully"),
                ex -> log.error("Failed to send message", ex)
            );
    }
}
```

## gRPC Implementation

### Protocol Buffer Definition
```protobuf
// filepath: src/proto/user.proto
syntax = "proto3";

package user;

service UserService {
    rpc GetUser (UserRequest) returns (UserResponse) {}
    rpc CreateUser (CreateUserRequest) returns (UserResponse) {}
}

message UserRequest {
    string user_id = 1;
}

message CreateUserRequest {
    string name = 1;
    string email = 2;
}

message UserResponse {
    string user_id = 1;
    string name = 2;
    string email = 3;
    timestamp created_at = 4;
}
```

### gRPC Server Implementation
```typescript
// filepath: src/grpc/server.ts
import * as grpc from '@grpc/grpc-js';
import * as protoLoader from '@grpc/proto-loader';

const userProto = protoLoader.loadSync('src/proto/user.proto');
const userDefinition = grpc.loadPackageDefinition(userProto);

const server = new grpc.Server();

server.addService(userDefinition.UserService.service, {
    getUser: async (call, callback) => {
        try {
            const user = await getUserFromDB(call.request.user_id);
            callback(null, user);
        } catch (error) {
            callback(error);
        }
    },
    createUser: async (call, callback) => {
        try {
            const user = await createUserInDB(call.request);
            callback(null, user);
        } catch (error) {
            callback(error);
        }
    }
});
```

## WebSocket Implementation

### WebSocket Server
```typescript
// filepath: src/websocket/server.ts
import { WebSocketServer } from 'ws';

const wss = new WebSocketServer({ port: 8080 });

wss.on('connection', (ws) => {
    ws.on('message', (message) => {
        // Handle incoming messages
        const data = JSON.parse(message.toString());
        
        // Broadcast to all connected clients
        wss.clients.forEach((client) => {
            if (client.readyState === WebSocket.OPEN) {
                client.send(JSON.stringify({
                    type: 'message',
                    data: data
                }));
            }
        });
    });
});
```

### WebSocket Client
```javascript
// filepath: src/websocket/client.js
const socket = new WebSocket('ws://localhost:8080');

socket.onopen = () => {
    console.log('Connected to WebSocket server');
    socket.send(JSON.stringify({
        type: 'hello',
        data: 'Client connected'
    }));
};

socket.onmessage = (event) => {
    const message = JSON.parse(event.data);
    console.log('Received:', message);
};

socket.onerror = (error) => {
    console.error('WebSocket error:', error);
};
```