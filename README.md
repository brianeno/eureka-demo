# Eureka Service Discovery Sample Code

Code demonstrating service discovery with Netflix Eureka, Spring Boot 3.5.6, and Spring Cloud 2025.0.0.

This is from my medium article **Microservice Service Discovery with Spring Cloud**

This article and others from me can be found at https://medium.com/@brianenochson

## Key Features Demonstrated

- **Service Registration**: Services automatically register with Eureka on startup
- **Service Discovery**: Product service discovers inventory service by name, not URL
- **Load Balancing**: Automatic client-side load balancing with @LoadBalanced RestTemplate
- **Fault Tolerance**: Product service handles inventory service failures gracefully
- **Dynamic Scaling**: Add/remove service instances without configuration changes

## Quick Start

### Using Docker Compose (Recommended)

```bash
# Build all services
./build-all.sh

# Start everything
docker-compose up

# Access Eureka Dashboard
open http://localhost:8761
```

### Using Maven Directly

```bash
# Terminal 1 - Eureka Server
cd eureka-server
mvn spring-boot:run

# Terminal 2 - Product Service  
cd product-service
mvn spring-boot:run

# Terminal 3 - Inventory Service
cd inventory-service
mvn spring-boot:run
```
## Key Features

- **Dynamic Service Discovery** - Services register/deregister automatically
- **Client-Side Load Balancing** - Requests distributed across instances
- **Random Ports** - Services use `server.port=0` for dynamic allocation

## Testing

```bash
# Get product with inventory (discovers inventory service dynamically)
curl http://localhost:8081/products/1

# Check service health
curl http://localhost:8081/products/health
curl http://localhost:8082/inventory/health
```

## Docker Commands

```bash
# Build images
docker-compose build

# Start with logs
docker-compose up

# Start in background
docker-compose up -d

# Stop services
docker-compose down

# Clean up everything, including the images
docker-compose down -v --rmi all
```

## Configuration Notes

- Services use random ports (`server.port=0`) for flexibility
- Eureka credentials configured via environment variables
- Instance IDs include random values to ensure uniqueness

## Running Locally

### Option 1: Using Maven (Recommended for Development)

1. **Start the Eureka Server:**
```bash
cd eureka-server
mvn spring-boot:run
```

2. **Start the Inventory Service (Instance 1):**
```bash
cd inventory-service
mvn spring-boot:run
```

3. **Start the Inventory Service (Instance 2 - Optional):**
```bash
cd inventory-service
mvn spring-boot:run
```

4. **Start the Product Service:**
```bash
cd product-service
mvn spring-boot:run
```

### Option 2: Using Docker Compose

1. **Build all services:**
```bash
# Build each service
cd eureka-server && mvn clean package && cd ..
cd product-service && mvn clean package && cd ..
cd inventory-service && mvn clean package && cd ..
```

2. **Start all services:**
```bash
docker-compose up
```

This will start:
- 1 Eureka Server
- 1 Product Service
- 2 Inventory Service instances (for load balancing demonstration)

## Verifying the Setup

1. **Eureka Dashboard:**
   - Open http://localhost:8761
   - You should see PRODUCT-SERVICE and INVENTORY-SERVICE registered

Let's find the port our inventory and product services are running on.

http://localhost:8761/eureka/apps

Note the terminals.

2. **Test Product Service:**
```bash
# Get product with inventory check
curl http://localhost:8081/products/1

# Response:
{
  "id": 1,
  "name": "Laptop",
  "price": 999.99,
  "availableQuantity": 100
}
```

3. **Test Inventory Service Directly:**

```bash
# Check inventory
curl http://localhost:8082/inventory/1

# Response:
{
  "productId": 1,
  "quantity": 100
}
```

4. **Health Checks:**
```bash
# Product Service
curl http://localhost:8081/products/health

# Inventory Service
curl http://localhost:8082/inventory/health
```

## Load Balancing Demonstration

When running multiple inventory service instances, the product service automatically load balances requests between them.

To see this in action:
1. Start both inventory service instances (ports 8082 and 8083)
2. Watch the logs of both instances
3. Make multiple requests to the product service
4. Observe requests being distributed between inventory instances

```bash
# Make multiple requests to see load balancing
for i in {1..10}; do curl http://localhost:8081/products/1; echo; done
```
