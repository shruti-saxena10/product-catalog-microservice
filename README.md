# Product Catalog Microservice
A microservice that manages product information, supports adding, updating, and fetching products, and publishes events (on creation/update) to RabbitMQ.

## Tech Stack:
Java 21
Spring Boot
PostgreSQL
RabbitMQ (for event-driven design)
Maven (build tool)
Docker (containerization)

## Features:
- Create, update, and retrieve products via REST APIs
- Persist product data in PostgreSQL using JPA
- Publish events to GCP Pub/Sub when products are created or updated(Done using RabbitMQ)
- Event-driven architecture with RabbitMQ for product creation and update events.
- Optionally support RabbitMQ for event publishing
- Ready for cloud deployment (Docker + Cloud Run compatible)

# Prerequisites
Before starting, make sure to have the following tools installed on the machine:
- Docker (for running the app and dependencies)
- Docker Compose (for multi-container orchestration)
- Maven (for building the application)

# Setup Instructions
1.Setup instructions (local DB, how to run)
Prerequisites
- Docker
- Docker Compose

Step i) create docker-compose.yml

```
version: '3.8'

  postgres:
    image: postgres:14
    container_name: postgres_container
    restart: always
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: root
      POSTGRES_DB: productdb
    ports:
      - "5436:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - app-network

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin_container
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: admin
    ports:
      - "5050:80"
    depends_on:
      - postgres
    networks:
      - app-network

```

Step ii) Start the containers

```
docker-compose up -d
```

- PostgreSQL will be accessible at localhost:5432
- pgAdmin will be accessible at http://localhost:5050/

Step iii) Login to pgAdmin
Open browser: http://localhost:5050
Use the following credentials:
- Email: admin@admin.com
- Password: admin123

Click Add New Server:
- Name: postgres
- Host: postgres
- Port: 5432
- Username: postgres
- Password: root

Step iv) Stop and remove containers
```
docker-compose down
```

```
docker-compose down -v
```
2.RabbitMQ Setup
Stepi) Update docker-compose.yml under services

```
rabbitmq:
    image: rabbitmq:management
    container_name: rabbitmq
    ports:
      - "5672:5672"    # AMQP port
      - "15672:15672"  # RabbitMQ Management UI
    environment:
      - RABBITMQ_DEFAULT_USER=guest
      - RABBITMQ_DEFAULT_PASS=guest
    restart: unless-stopped
    networks:
      - app-network
```

Step ii) Start the containers   

```
docker-compose up -d
```

- RabbitMQ will be accessible at  ```http://localhost:15672/```

Step iii) Login to rabbitmq
Open browser: http://localhost:5050
Use the following credentials:
- Email: guest
- Password: guest

# Running the application

# API Usage examples

## POST /products/added – Add a new product
RequestBody
```
{
  "name": "Galaxy S24 Ultra",
  "description": "For Power users, mobile photographers, and productivity enthusiasts",
  "category": "Electronics",
  "price": 1300,
  "availableStock": 100
}
```

<img width="1277" alt="image" src="https://github.com/user-attachments/assets/ebf47cce-8eb9-4888-968d-f1264b8fdcd3" />


<img width="1496" alt="image" src="https://github.com/user-attachments/assets/7df792f8-791c-4165-8fe9-32ee5de85b68" />

RabbitMQ
<img width="1504" alt="image" src="https://github.com/user-attachments/assets/2c76e403-4aa5-40dc-8ad9-4a4b167dc29e" />

Application console
<img width="1491" alt="image" src="https://github.com/user-attachments/assets/c88493ff-ff39-428f-8c44-0aa92002f153" />

Published Event
Published event to RabbitMQ: EventPayload(eventType=PRODUCT_CREATED, timestamp=2025-04-29T06:33:54.168870091Z, product=Product(productId=283e0083-736d-41bd-a4ca-e1a764e4bd8f, name=Galaxy S24 Ultra, description=For Power users, mobile photographers, and productivity enthusiasts, category=Electronics, price=1300.0, availableStock=100, lastUpdated=2025-04-29T06:33:54.164032383Z))





