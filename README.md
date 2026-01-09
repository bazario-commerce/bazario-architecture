# bazario-architecture
## 🛒 Bazario Microservices Platform

### High-Level Architecture

![Image](https://user-images.githubusercontent.com/16538471/139402051-c5552c9e-086c-456d-9517-b45d33e13163.png)

![Image](https://javatechonline.com/wp-content/uploads/2025/06/E-commerce_system_architecture-1.jpg)

![Image](https://devcenter2.assets.heroku.com/article-images/1544603322-kafkak.png)

**Overview**
This project demonstrates a **scalable, event-driven e-commerce platform** using microservices, Kafka, Redis, and Elasticsearch.

---

### Core Principles Used

* Microservices with clear bounded contexts
* Event-driven communication using Kafka
* CQRS-like read/write separation (Search vs MySQL)
* Stateless authentication
* Horizontal scalability

---

# 2️⃣ Sequence Diagrams (MOST IMPORTANT)

---

## 🧍 User Registration Flow

![Image](https://images.doclify.net/gleek-web/d/7a7748a6-db81-449b-a3ad-f7872d8be508.png)

![Image](https://microservices.io/i/Microservice_Architecture.png)

**Flow**

1. Client → API Gateway
2. User Service → MySQL
3. User Service → Kafka (`USER_REGISTERED`)
4. Notification Service → Email

🧠 **Pattern**
✔ Event-driven
✔ Async side effects
✔ No tight coupling

---

## 🔍 Product Search Flow (Elasticsearch)

![Image](https://images.doclify.net/gleek-web/d/7a7748a6-db81-449b-a3ad-f7872d8be508.png)

![Image](https://agilie.com/_next/image?q=75\&url=%2Fapi%2Fimageproxy%3Furl%3Dhttps%3A%2F%2Fd12zh9bqbty5wp.cloudfront.net%2Fckeditor_assets%2Fpictures%2F3411%2Fcontent_content_elastic_2_1.jpg\&w=1920)

**Why this matters**

* MySQL is **not used** for search
* Elasticsearch handles:

  * Ranking
  * Typos
  * Full-text search

🧠 **Pattern**
✔ CQRS (Read optimized path)

---

## 💳 Checkout → Payment → Order (Critical)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2AZ6yjDuAK_88DlfWemNukzw.png)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2AwizQ2gFzsQiSJESKsV1FSQ.jpeg)

**This is a Saga (Choreography)**

1. Order Service creates `PENDING` order
2. Emits `ORDER_CREATED`
3. Payment Service processes payment
4. Emits `PAYMENT_SUCCESS`
5. Order Service marks order `CONFIRMED`
6. Notification Service sends email

🧠 **Pattern**
✔ Saga – Choreography
✔ Eventually consistent
✔ No distributed transactions

---

# 3️⃣ Failure Scenarios

---

## ❌ Payment Fails

![Image](https://developer.ibm.com/developer/default/articles/use-saga-to-solve-distributed-transaction-management-problems-in-a-microservices-architecture/images/saga-choreo-failure.jpg)

![Image](https://www.kai-waehner.de/wp-content/uploads/2022/05/Screenshot-2022-05-26-at-12.10.33-1024x617.png)

**What happens**

* Payment Service emits `PAYMENT_FAILED`
* Order Service marks order `FAILED`
* Inventory not reserved
* User notified

🎯 **Why this is good**

* No rollback transactions
* Each service owns its state

---

## ❌ Kafka Consumer Down

![Image](https://www.kai-waehner.de/wp-content/uploads/2022/05/Screenshot-2022-05-23-at-08.31.06.png)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2Az3V2HJRyf4P3wjL-pU-13A.jpeg)

**Handling**

* Retry with backoff
* Dead Letter Topic (DLT)
* Manual replay

🧠 **Pattern**
✔ At-least-once delivery
✔ Idempotent consumers

---

# 4️⃣ Explicit Design Patterns Used

| Area            | Pattern             |
| --------------- | ------------------- |
| Checkout        | Saga (Choreography) |
| Auth            | Stateless JWT       |
| Cart            | Cache-Aside         |
| Search          | CQRS                |
| Messaging       | Event-driven        |
| Fault tolerance | Retry + DLQ         |
| Scalability     | Horizontal scaling  |

> “This system favors eventual consistency over distributed transactions.”

---

# 5️⃣ How Real Companies Do This

| Company  | Similarity                     |
| -------- | ------------------------------ |
| Amazon   | Kafka + async order processing |
| Flipkart | Elasticsearch for catalog      |
| Uber     | Event-driven microservices     |
| Stripe   | Payment as isolated service    |

📌 **Key point**

> “This architecture mirrors real-world large-scale commerce platforms.”

---

# 6️⃣ Final GitHub Repo Layout

### Root

```
bazario-architecture/
 ├── README.md
 ├── diagrams/
 │   ├── architecture.png
 │   ├── checkout-saga.png
 │   ├── search-flow.png
 ├── adr/
 │   ├── 001-why-kafka.md
 │   ├── 002-why-redis.md
 └── tech-stack.md
```

### Each Service

```
order-service/
 ├── README.md
 ├── src/
 ├── Dockerfile
 ├── openapi.yaml
 └── k8s/
```

