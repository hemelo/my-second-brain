
All the fundamental concepts from [[RABBITMQ|RabbitMq]] comes from [[AMQP Protocol]].
### 📚 Fundamental Concepts

#### 1️⃣ Producer

- **Role:** Sends messages.
- **Example:** An e-commerce system emits an `OrderPlaced` event.
#### 2️⃣ Consumer

- **Role:** Receives and processes messages.
- **Example:** A shipping service listens for `OrderPlaced` events.
#### 3️⃣ Message

- **Role:** The data being transferred (JSON, XML, Binary, etc).
    
---

### 📦 Core Components

#### ✅ Queue

- **Purpose:** Temporary message storage until consumed.
    
- **Options:**
    
    - `Durable`: Survives broker restarts.
    - `Exclusive`: Used by a single connection.
    - `Auto-delete`: Deletes when no consumers remain.
        
#### ✅ Exchange

- **Purpose:** Routes messages to queues based on routing rules.
    
- **Types:**
    - `Direct`: Routes by exact routing key.
    - `Topic`: Routes using wildcard patterns (`*`, `#`).
    - `Fanout`: Broadcasts to all bound queues.
    - `Headers`: Routes by message header attributes.
        
#### ✅ Binding

- **Purpose:** Links an exchange to a queue with a routing key.
    

---

### 📌 Advanced Concepts

#### 📖 Virtual Hosts (vHosts)

- Isolate environments within the same RabbitMQ instance.
#### 📖 Connection & Channel

- **Connection:** TCP link between the app and broker.
- **Channel:** Lightweight virtual connection over a single TCP connection.
    
#### 📖 Acknowledgments (ACKs)

- Ensure messages are processed reliably.
    - `Manual ACK`: Consumer explicitly confirms.
    - `Auto ACK`: Broker considers message handled immediately.
        
#### 📖 Dead Letter Exchange (DLX)

- Captures unprocessed or expired messages.
    
#### 📖 Prefetch Count

- Controls how many unacknowledged messages a consumer can handle simultaneously.
---
### 🔐 Reliability Patterns

|Pattern|Purpose|
|---|---|
|Message Durability|Persist messages after restarts.|
|Publisher Confirms|Producers get delivery confirmations.|
|Consumer ACKs|Ensure message processing before removal.|
|Dead-Letter Queues|Capture failed or rejected messages.|

---
### 📈 Message Flow

```
Producer → Exchange → [Binding Rules] → Queue → Consumer
```

---
### 🧩 Common Use Cases

- Background Jobs / Task Queues
- Event Broadcasting (Notifications)
- Microservices Communication
- Logging Pipelines

---
### 📖 Example: Direct Exchange Flow

1. Producer sends message with `routing key = "order.created"`.
2. `Direct Exchange` routes to `OrderServiceQueue` bound to `order.created`.
3. Consumer from `OrderServiceQueue` processes the order.


---
Tags;
#messaging #protocol #amqp #rabbitmq #integration #distributed-systems