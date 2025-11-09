# MERN Stack Microservices with Kubernetes

This project demonstrates a microservices architecture using the MERN (MongoDB, Express.js, React, Node.js) stack, containerized with Docker and orchestrated using Kubernetes. The application consists of a frontend service and two backend microservices (Hello Service and Profile Service).

## Architecture Overview

The application is structured into the following components:

- Frontend: React-based web interface
- Hello Service: Basic greeting microservice
- Profile Service: User profile management microservice

![Architecture Diagram]
[Screenshots/ArchitectureDiagram.png]

## Prerequisites

- Node.js (v14 or higher)
- Docker
- Kubernetes cluster (local or cloud)
- Helm
- AWS Account (for ECR)

## Project Structure

```
├── frontend/                 # React frontend application
├── backend/
│   ├── helloService/        # Hello microservice
│   └── profileService/      # Profile microservice
├── helm/                    # Kubernetes Helm charts
└── docker-compose.yml       # Local development setup
```

## Local Development

### Environment Setup

1. For `helloService`, create `.env` file:
```bash
PORT=3001
```

2. For `profileService`, create `.env` file:
```bash
PORT=3002
MONGO_URL="specifyYourMongoURLHereWithDatabaseNameInTheEnd"
```

3. Install backend dependencies:
```bash
# Install packages for both services
cd backend/helloService && npm install
cd ../profileService && npm install
```

4. Setup frontend:
```bash
cd frontend
npm install
npm start
```

## Building and Pushing Docker Images

1. Build the images:
```bash
# Frontend
docker build -t aviral/samplemicroservice-frontend ./frontend

# Hello Service
docker build -t aviral/samplemicroservice-helloservice ./backend/helloService

# Profile Service
docker build -t aviral/samplemicroservice-profileservice ./backend/profileService
```

2. Push to ECR:
```bash
docker push 975050024946.dkr.ecr.ca-central-1.amazonaws.com/aviral/frontend:latest
docker push 975050024946.dkr.ecr.ca-central-1.amazonaws.com/aviral/helloservice:latest
docker push 975050024946.dkr.ecr.ca-central-1.amazonaws.com/aviral/profileservice:latest
```

![Frontend][Screenshots/FrontendECR.png]
![Hello Service][Screenshots/helloServiceECR.png]
![Profile Service][Screenshots/profileServiceECR.png]


## Kubernetes Deployment

The application is deployed to Kubernetes using Helm charts:

1. Deploy using Helm:
```bash
helm upgrade --install samplemern ./helm
```

2. Verify the deployment:
```bash
kubectl get pods
kubectl get services
```

![Services][Screenshots/services.png]

### Accessing the Application

After successful deployment:

1. Frontend Service: Access through the LoadBalancer URL
2. Hello Service: Available through the frontend
3. Profile Service: Available through the frontend

[Screenshots/frontend.png]
[Screenshots/helloservice.png]

## Service Details

### Frontend Service
- Port: 3000
- Technologies: React
- Features:
  - User interface for interacting with microservices
  - Integration with backend services

### Hello Service
- Port: 3001
- Technologies: Node.js, Express
- Endpoints:
  - GET /: Returns a greeting message

### Profile Service
- Port: 3002
- Technologies: Node.js, Express
- Endpoints:
  - GET /profile: Returns user profile information

## Helm Chart Structure

```
helm/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── deploy-frontend.yaml
    ├── deploy-helloservice-be.yaml
    └── deploy-profiles-be.yaml
```

## Configuration

The application can be configured through the following files:

1. `helm/values.yaml`: Kubernetes deployment configuration
2. `docker-compose.yml`: Local development configuration
3. Environment variables in each service

## Troubleshooting

Common issues and their solutions:

1. Pod Startup Issues:
   ```bash
   kubectl describe pod [pod-name]
   kubectl logs [pod-name]
   ```

2. Service Connection Issues:
   - Verify service names and ports
   - Check network policies
   - Ensure LoadBalancer services have external IPs

## Contributing

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Create a new Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details
