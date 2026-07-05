# Claim Connect — Insurance Claims Microservices Backend

![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.2-brightgreen)
![Java](https://img.shields.io/badge/Java-17-orange)
![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-2023.0.1-blue)
![MySQL](https://img.shields.io/badge/MySQL-8.x-blue)
![JWT](https://img.shields.io/badge/JWT-Security-red)
![License](https://img.shields.io/badge/License-MIT-yellow)

A production-ready **microservices-based insurance claim management system** built with Spring Boot and Spring Cloud. The system allows hospitals to raise claims on behalf of patients, patients to approve/reject claims, and insurance companies to make final decisions.

---

## 📌 Table of Contents
- [Architecture Overview](#architecture-overview)
- [Tech Stack](#tech-stack)
- [Services](#services)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [API Endpoints](#api-endpoints)
- [Claim Lifecycle](#claim-lifecycle)
- [Security](#security)
- [Database Schema](#database-schema)

---

## 🏗️ Architecture Overview

```
                        ┌─────────────────┐
                        │   React Frontend │
                        │  localhost:5173  │
                        └────────┬────────┘
                                 │
                        ┌────────▼────────┐
                        │   API Gateway   │
                        │  localhost:3434  │
                        │  (JWT Validation)│
                        └────────┬────────┘
                                 │
              ┌──────────────────┼──────────────────┐
              │                  │                  │
    ┌─────────▼──────┐  ┌───────▼────────┐  ┌──────▼───────────────┐
    │ PatientService │  │ HospitalService │  │InsuranceCompanyService│
    │  port: 2002    │  │  port: 2000    │  │      port: 2003       │
    └────────────────┘  └────────────────┘  └──────────────────────┘
                                 │
                    ┌────────────▼───────────┐
                    │   ClaimRequestService  │
                    │       port: 2001       │
                    └────────────────────────┘
                                 │
              ┌──────────────────┼──────────────────┐
              │                  │                  │
    ┌─────────▼──────┐  ┌───────▼────────┐  ┌──────▼──────┐
    │  Eureka Server │  │  Config Server │  │    MySQL    │
    │  port: 8761    │  │  port: 1234    │  │  port: 3307 │
    └────────────────┘  └────────────────┘  └─────────────┘
```

---

## 🛠️ Tech Stack

| Category | Technology |
|---|---|
| Language | Java 17 |
| Framework | Spring Boot 3.4.2 |
| Microservices | Spring Cloud (Eureka, API Gateway, Config Server) |
| Inter-service Communication | Feign Clients |
| Security | Spring Security, JWT, BCrypt |
| Database | MySQL 8.x |
| ORM | Spring Data JPA, Hibernate |
| API Documentation | Swagger UI |
| Build Tool | Maven |
| IDE | Spring Tool Suite (STS) |

---

## 📦 Services

| Service | Port | Description |
|---|---|---|
| **ConfigServer** | 1234 | Centralized configuration from GitHub |
| **EurekaServer** | 8761 | Service registry and discovery |
| **ApiGateway** | 3434 | Single entry point, JWT validation, routing |
| **PatientService** | 2002 | Patient registration, login, profile management |
| **HospitalService** | 2000 | Hospital registration, login, profile management |
| **InsuranceCompanyService** | 2003 | Insurance company registration and login |
| **ClaimRequestService** | 2001 | Core service — claim creation and lifecycle |

---

## ✅ Prerequisites

Make sure you have the following installed:

- Java 17+
- Maven 3.6+
- MySQL 8.x (running on port 3307)
- Spring Tool Suite (STS) or IntelliJ IDEA
- Git

---

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/vamshi-982/claim-connect-backend.git
cd claim-connect-backend
```

### 2. Set up MySQL Database

Open MySQL Workbench and create the database:

```sql
CREATE DATABASE claim_connect;
```

### 3. Configure application.properties

For each business service (PatientService, HospitalService, InsuranceCompanyService, ClaimRequestService), update:

```properties
spring.datasource.url=jdbc:mysql://localhost:3307/claim_connect?createDatabaseIfNotExist=true
spring.datasource.username=root
spring.datasource.password=YOUR_PASSWORD
```

### 4. Start services in this exact order

> ⚠️ Order matters! Each service depends on the previous one.

```
1. ConfigServer       → port 1234
2. EurekaServer       → port 8761
3. PatientService     → port 2002
4. HospitalService    → port 2000
5. InsuranceCompanyService → port 2003
6. ClaimRequestService → port 2001
7. ApiGateway         → port 3434  (start LAST)
```

In STS: Right click each project → **Run As → Spring Boot App**

### 5. Verify all services are running

Open browser → **http://localhost:8761**

You should see all 4 business services registered with status **UP**.

---

## 📡 API Endpoints

All requests go through the **API Gateway** at `http://localhost:3434`

### 🔓 Public Endpoints (No token required)

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/patient/register` | Register new patient |
| POST | `/api/patient/authenticate` | Patient login → returns JWT |
| POST | `/api/hospital/register` | Register new hospital |
| POST | `/api/hospital/authenticate` | Hospital login → returns JWT |
| POST | `/api/insuranceComp/register` | Register insurance company |
| POST | `/api/insuranceComp/authenticate` | Insurance login → returns JWT |

### 🔐 Protected Endpoints (JWT required)

#### Patient APIs
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/patient/profile` | Get patient profile |
| PUT | `/api/patient/update` | Update patient details |
| GET | `/api/claimrequest/patient/claims` | Get all claims for patient |
| PUT | `/api/claimrequest/accept/{claimId}` | Accept a claim |
| PUT | `/api/claimrequest/revert/{claimId}` | Revert a claim |
| PUT | `/api/claimrequest/reject/{claimId}` | Reject a claim |

#### Hospital APIs
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/hospital/profile` | Get hospital profile |
| GET | `/api/claimrequest/search/{email}` | Search patient by email |
| POST | `/api/claimrequest/create` | Create new claim request |
| GET | `/api/claimrequest/hospital/claims` | Get all claims by hospital |
| PUT | `/api/claimrequest/edit/{claimId}` | Edit a reverted claim |

#### Insurance Company APIs
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/insuranceComp/profile` | Get insurance company profile |
| GET | `/api/claimrequest/insurance/claims` | Get all claims for insurance |
| PUT | `/api/claimrequest/approve/{claimId}` | Approve a claim |
| PUT | `/api/claimrequest/insurancerevert/{claimId}` | Revert a claim |
| PUT | `/api/claimrequest/insurancereject/{claimId}` | Reject a claim |

---

## 🔄 Claim Lifecycle

```
Hospital creates claim
        │
        ▼
   [PENDING] ◄──────────────────────────┐
        │                               │
   Patient Action                       │
   ┌────┴────┐                          │
   │         │                          │
[ACCEPT]  [REVERT] ──► Hospital edits ──┘
   │      [REJECT] ──► Terminal ❌
   │
   ▼
[ACCEPTED]
        │
   Insurance Action
   ┌────┴────┐
   │         │
[APPROVE] [REVERT] ──► Hospital edits ──┐
   │      [REJECT] ──► Terminal ❌       │
   │                                    │
[APPROVED] ✅                           └──► [PENDING]
```

---

## 🔐 Security

- **JWT Authentication** at API Gateway level — tokens validated before routing
- **BCrypt** password hashing for all roles
- **Role-based access control** — PATIENT, HOSPITAL, INSURANCECOMPANY
- **@Valid** input validation on all registration endpoints
- Public endpoints: `/register` and `/authenticate` for all roles
- All other endpoints require valid JWT in `Authorization: Bearer <token>` header

---

## 🗄️ Database Schema

### Tables in `claim_connect` database:

**patient** — patientId, patientName, patientEmail, patientPassword, insuranceCompanyId

**hospital** — hospitalId, hospitalName, hospitalEmail, hospitalPwd

**insurance_company** — insuranceCompId, insuranceCompName, insuranceCompEmail, insuranceCompPwd

**claim_request** — claimId, patientId, hospitalId, insuranceCompanyId, synopsis, price, status, lastUpdatedBy, createdAt, updatedAt

---

## 👨‍💻 Author

**Siddapuram Vamshi**
- GitHub: [@vamshi-982](https://github.com/vamshi-982)
- LinkedIn: [siddapuramvamshi](https://linkedin.com/in/siddapuramvamshi)

---

## 📄 License

This project is licensed under the MIT License.
