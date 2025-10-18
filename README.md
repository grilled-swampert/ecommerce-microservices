![Mermaid Chart - Create complex, visual diagrams with text -2025-10-18-163401](https://github.com/user-attachments/assets/9d2bfe29-3499-4ce2-81cb-857d12d7efc7)
<svg id="export-svg" width="100%" xmlns="http://www.w3.org/2000/svg" class="flowchart" style="max-width: 909.588px; background: rgb(255, 255, 255);" viewBox="4 4 909.5875244140625 980.5" role="graphics-document document" aria-roledescription="flowchart-v2">

# E-commerce Microservices Platform

A distributed microservices architecture for e-commerce operations with Kubernetes orchestration, API gateway management, and chaos engineering capabilities.

## Architecture

```
Client → Kong API Gateway → Microservices → PostgreSQL
                              ↓
                          Prometheus (Monitoring)
```

### Core Services

- **Order Service**: Order management and processing
- **Payment Service**: Payment transaction handling
- **User Service**: User authentication and management

Each service is independently deployable, scalable, and monitored.

## Tech Stack

- **Runtime**: Node.js
- **Database**: PostgreSQL
- **Orchestration**: Kubernetes
- **API Gateway**: Kong
- **Monitoring**: Prometheus
- **Containerization**: Docker
- **Load Testing**: k6
- **Chaos Engineering**: Custom experiments

## Project Structure

```
.
├── k8s/                          # Kubernetes configurations
│   ├── kong-rate-limiter.yaml
│   ├── postgres-deployment.yaml
│   ├── order-deployment.yaml
│   ├── payment-deployment.yaml
│   └── user-deployment.yaml
├── services/                     # K8s service definitions & monitors
├── order-service/                # Order microservice
│   ├── index.js
│   ├── Dockerfile
│   ├── logger.js
│   └── metrics.js
├── payment-service/              # Payment microservice
├── user-service/                 # User microservice
├── microservices-load-tests/    # Load testing suite
│   ├── config.json
│   ├── run-load-tests.sh
│   └── *.js
├── experiments/                  # Chaos engineering
│   ├── cpu-stress.yaml
│   ├── io-chaos.yaml
│   ├── network-delay.yaml
│   └── pod-kill.yaml
├── migration.sql                 # Database schema
├── docker-compose.yml            # Local development
└── deploy-all.sh                 # Deployment script
```

## Getting Started

### Prerequisites

- Docker & Docker Compose
- Kubernetes cluster (minikube/kind for local)
- kubectl
- Node.js 16+

### Local Development

```bash
# Start all services locally
docker-compose up -d

# Deploy to local Kubernetes
./deploy-all.sh

# Run database migrations
kubectl exec -it postgres-pod -- psql -U postgres -d ecommerce -f /migration.sql
```

### Configuration

Kong API Gateway is configured with rate limiting and routes:
- `/orders` → Order Service
- `/payments` → Payment Service  
- `/users` → User Service

See `ingress-setup.yaml` for full configuration.

## Testing

### Load Testing

```bash
cd microservices-load-tests
./run-load-tests.sh
```

Test scenarios:
- Individual service load tests
- Comprehensive multi-service tests
- Chaos testing under load

Results are stored in `load-test-results/`.

### Chaos Engineering

Apply chaos experiments to test resilience:

```bash
# CPU stress
kubectl apply -f experiments/cpu-stress.yaml

# Network delay
kubectl apply -f experiments/network-delay.yaml

# Pod failures
kubectl apply -f experiments/pod-kill.yaml

# I/O chaos
kubectl apply -f experiments/io-chaos.yaml
```

## Monitoring

Each service exposes Prometheus metrics at `/metrics`:

- HTTP request duration
- Request rate
- Error rate
- Custom business metrics

ServiceMonitors are configured for automatic Prometheus scraping.

## API Endpoints

### Order Service
- `POST /orders` - Create order
- `GET /orders/:id` - Get order
- `GET /orders` - List orders

### Payment Service
- `POST /payments` - Process payment
- `GET /payments/:id` - Get payment status

### User Service
- `POST /users` - Create user
- `GET /users/:id` - Get user
- `PUT /users/:id` - Update user

## Deployment

### Kubernetes

```bash
# Deploy all services
kubectl apply -f k8s/

# Deploy Kong ingress
kubectl apply -f ingress-setup.yaml

# Verify deployments
kubectl get pods
kubectl get services
```

### Scaling

```bash
# Scale a service
kubectl scale deployment order-service --replicas=3

# Autoscaling
kubectl autoscale deployment order-service --min=2 --max=10 --cpu-percent=80
```

## Development

Each microservice follows the same structure:

```javascript
// index.js - Main application
// logger.js - Winston logging
// metrics.js - Prometheus metrics
// metricsMiddleware.js - Metrics collection
```

To add a new service:

1. Create service directory with required files
2. Add Dockerfile
3. Create K8s deployment and service manifests
4. Add routes to Kong configuration
5. Create ServiceMonitor for Prometheus

## Troubleshooting

Check logs:
```bash
kubectl logs -f deployment/order-service
kubectl logs -f deployment/payment-service
kubectl logs -f deployment/user-service
```

Check service health:
```bash
kubectl get pods
kubectl describe pod <pod-name>
```

Database connection issues:
```bash
kubectl exec -it postgres-pod -- psql -U postgres
```
