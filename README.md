# CrediYa Microservices Platform

## Overview
CrediYa is a microservices-based credit evaluation platform built with Spring WebFlux and following Hexagonal Architecture (Clean Architecture) principles. The system enables user registration, authentication, loan request management, and manual approval/rejection flows with AWS event-driven integrations. This project was developed as part of the PowerUp Bootcamp challenge by Pragma, showcasing advanced cloud-native architecture, reactive programming, and event-driven design.

The main goals of the platform are:
- Provide a **reactive, functional** backend solution using Spring WebFlux.  
- Follow a **clean architecture approach**, with clear separation between domain and infrastructure layers.  
- Integrate with **AWS services** (SQS, SNS, Lambda) to enable event-driven workflows (notifications, reporting, validations).  
- Ensure robust error handling, logging, and traceability across microservices.  

---

## Implemented Features (up to HU 6)
✅ **HU 1 – User Registration**  
- Admin/Advisor can register new users with personal data.  
- Validation for required fields, email format, and salary range.  
- Transactions ensure atomic persistence.  

✅ **HU 2 – Loan Request Registration**  
- Clients can submit loan requests (amount, term, type).  
- Requests are automatically stored with status `PENDING_REVIEW`.  
- Reactive transaction management with WebFlux.  

✅ **HU 3 – Authentication**  
- Login endpoint using email and password.  
- JWT token generation for session handling.  
- Role-based validation for access control (Admin/Advisor vs Client).  

✅ **HU 4 – Loan Request Listing (Manual Review)**  
- Advisors can list loan requests in specific states (`PENDING_REVIEW`, `REJECTED`, `MANUAL_REVIEW`).  
- Paginated and filterable results with loan and client details.  

✅ **HU 5 – Containerization**  
- Each microservice has its own Dockerfile.  
- A **docker-compose.yml** orchestrates all services (Authentication, Loan Requests, Database, LocalStack).  
- Services communicate via internal Docker network.  

✅ **HU 6 – Loan Request Approval/Rejection**  
- Advisors can manually approve or reject requests.  
- On status change, an event is published to an **SQS queue**.  
- A **Lambda Notification Service** consumes messages and sends emails via **SNS**.  
- Consistent and reliable state change + notification process.  

---

## Project Structure
- **Authentication Service** (`autenticacion`): Manages users, login, and roles.  
- **Loan Requests Service** (`solicitudes`): Handles loan submissions, status updates, and approval flows.  
- **(Planned) Reporting Service** (`reportes`): Will consume events and update business metrics.  

Each service follows:  
- **Domain layer** (use cases, models, pure logic).  
- **Infrastructure layer** (adapters, persistence, AWS integration).  
- **Application layer** (handlers, routers, DTOs).  

---

## Getting Started

### Prerequisites
- [Docker](https://www.docker.com/get-started)  
- [Docker Compose](https://docs.docker.com/compose/)  

### Setup
Clone the repositories for each microservice:
```bash
git clone https://github.com/powerup-credit-api/Autenticacion
git clone https://github.com/powerup-credit-api/Solicitudes
git clone https://github.com/powerup-credit-api/Bases_de_datos
git clone https://github.com/powerup-credit-api/ApiGateway

# (reportes-service will be added later)
```

### Run with Docker Compose
From the root folder (where `docker-compose.yml` have to be located):
```bash
docker-compose up --build
```

This will start:
- **Authentication Service** (`http://localhost:8081`)  
- **Loan Requests Service** (`http://localhost:8082`)  
- **Database** (MySQL)  


---

## AWS Local Simulation
For local development, **LocalStack** simulates AWS services:
- **SQS** (Loan status events).  
- **SNS** (Email notifications).  

You can manage resources with the AWS CLI pointing to LocalStack:
```bash
aws --endpoint-url=http://localhost:4566 sqs create-queue --queue-name loan-events
aws --endpoint-url=http://localhost:4566 sns create-topic --name loan-notifications
```

---

## Next Steps
- **HU 7**: Add automated credit capacity validation using AWS Lambda.  
- **HU 8–9**: Add reporting microservice (DynamoDB + SQS consumers).  
- **HU 10**: Deploy with ECS Fargate + API Gateway + RDS.  
- **HU 11**: Scheduled reports sent via email.  
