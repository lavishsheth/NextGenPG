[pg_management_system_readme.md](https://github.com/user-attachments/files/22758271/pg_management_system_readme.md)
# PG Management System

A full-stack application to manage Paying Guest (PG) accommodations. The system provides features for browsing PG locations, booking rooms, paying monthly rent, raising maintenance/issues, viewing available rooms, and managing PG services. This project uses **React** for the frontend and **Spring Boot** for the backend. You can choose **MySQL** or **MongoDB** as the database. The final app will be deployable to cloud or PaaS platforms.

---

## Table of Contents
1. [Project Overview](#project-overview)
2. [Features](#features)
3. [Tech Stack](#tech-stack)
4. [Architecture](#architecture)
5. [Getting Started (Development)](#getting-started-development)
   - [Prerequisites](#prerequisites)
   - [Backend Setup (Spring Boot)](#backend-setup-spring-boot)
   - [Frontend Setup (React)](#frontend-setup-react)
   - [Using Docker (optional)](#using-docker-optional)
6. [Database Schema / Collections](#database-schema--collections)
7. [API Endpoints (examples)](#api-endpoints-examples)
8. [Authentication & Authorization](#authentication--authorization)
9. [Testing](#testing)
10. [Deployment](#deployment)
11. [Future Enhancements](#future-enhancements)
12. [Contributing](#contributing)
13. [License](#license)
14. [Contact](#contact)

---

## Project Overview

This PG Management System is built to simplify PG operations for owners and tenants. Owners can list PGs, manage rooms & services, and track bookings and payments. Tenants can search PGs by location, book rooms, make rent payments, raise issues, and view provided services.

## Features
- User roles: Admin / Owner / Tenant
- Browse PG locations and search by city/area/filters
- Room listing and availability
- Booking flow with status (pending, confirmed, cancelled)
- Rent payments (mock/payments integration)
- Issue/maintenance request form and tracker
- Services list (wifi, laundry, food, cleaning, etc.)
- Notifications (email / in-app) for bookings & payments
- Admin dashboard for managing PGs, users, payments

## Tech Stack
- Frontend: React (Vite or Create React App), React Router, Axios, Tailwind CSS or Bootstrap
- Backend: Java 17+, Spring Boot, Spring Data JPA (for MySQL) or Spring Data MongoDB
- DB: MySQL or MongoDB (choose one)
- Authentication: JWT (stateless) or Spring Security (session-based)
- Optional: Docker, Docker Compose

## Architecture
- Frontend SPA (React) consumes REST APIs provided by Spring Boot
- Backend exposes REST endpoints, handles business logic, and persists to DB
- Authentication via JWT (preferred) — tokens issued on login, checked by backend
- Optional file storage for images (local or cloud like AWS S3)

## Getting Started (Development)

### Prerequisites
- JDK 17+
- Node.js (16+) and npm or yarn
- MySQL (8+) or MongoDB
- Maven or Gradle
- (Optional) Docker & Docker Compose

### Backend Setup (Spring Boot)
1. Clone the repo:
```bash
git clone <repo-url>
cd pg-management-system/backend
```

2. Configure database in `application.yml` or `application.properties`.

**MySQL example (`application.yml`):**
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/pg_db?useSSL=false&serverTimezone=UTC
    username: root
    password: password
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
```

**MongoDB example (`application.yml`):**
```yaml
spring:
  data:
    mongodb:
      uri: mongodb://localhost:27017/pg_db
```

3. Environment variables (example `.env` for spring boot):
```
SPRING_DATASOURCE_URL=jdbc:mysql://localhost:3306/pg_db
SPRING_DATASOURCE_USERNAME=root
SPRING_DATASOURCE_PASSWORD=password
JWT_SECRET=your_jwt_secret_key
```

4. Build and run:
```bash
# with Maven
mvn clean install
mvn spring-boot:run

# or run the generated jar
java -jar target/pg-backend-0.0.1-SNAPSHOT.jar
```

### Frontend Setup (React)
1. Move to frontend folder and install dependencies:
```bash
cd ../frontend
npm install
# or
# yarn
```

2. Configure `.env` (example):
```
REACT_APP_API_URL=http://localhost:8080/api
```

3. Run in development:
```bash
npm start
# or
# yarn start
```

4. Build for production:
```bash
npm run build
```

### Using Docker (optional)
You can create a `docker-compose.yml` to run backend, frontend (optional), and database.

Example snippet (MySQL + backend):
```yaml
version: '3.8'
services:
  mysql:
    image: mysql:8
    environment:
      MYSQL_DATABASE: pg_db
      MYSQL_ROOT_PASSWORD: password
    ports:
      - 3306:3306
    volumes:
      - db_data:/var/lib/mysql

  backend:
    build: ./backend
    ports:
      - 8080:8080
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/pg_db
      SPRING_DATASOURCE_USERNAME: root
      SPRING_DATASOURCE_PASSWORD: password
    depends_on:
      - mysql

volumes:
  db_data:
```

## Database Schema / Collections
(High-level — adapt to your needs)

**Users**: `id, name, email, passwordHash, role, phone, createdAt`

**PG**: `id, ownerId, title, description, address, city, state, pricePerMonth, services[], images[], createdAt`

**Room**: `id, pgId, roomNumber, capacity, rent, isAvailable, features[]`

**Booking**: `id, roomId, userId, startDate, endDate, status, createdAt`

**Payment**: `id, bookingId, userId, amount, status, method, transactionId, paidAt`

**Issue**: `id, userId, roomId, pgId, title, description, status, createdAt, resolvedAt`

## API Endpoints (examples)

**Auth**
- `POST /api/auth/register` — register user
- `POST /api/auth/login` — login, returns JWT

**PG / Rooms**
- `GET /api/pgs` — list PGs (filters: city, price, services)
- `GET /api/pgs/{id}` — details
- `POST /api/pgs` — create PG (owner)
- `POST /api/pgs/{id}/rooms` — add room (owner)

**Booking**
- `POST /api/bookings` — create booking
- `GET /api/bookings/user/{userId}` — user bookings
- `PUT /api/bookings/{id}/cancel` — cancel booking

**Payments**
- `POST /api/payments` — create payment (integrate with gateway or mock)
- `GET /api/payments/user/{userId}`

**Issues**
- `POST /api/issues` — raise issue
- `GET /api/issues/pg/{pgId}` — owner view

> Secure endpoints with JWT; use role-based access control for owner/admin-only APIs.

## Authentication & Authorization
- Use **JWT** for stateless authentication:
  - `/auth/login` returns access token
  - Protect endpoints with a JWT filter in Spring Security
- Roles: `ROLE_ADMIN`, `ROLE_OWNER`, `ROLE_TENANT`
- For file uploads (images), use multipart endpoints secured with JWT

## Testing
- Backend: JUnit + Mockito for unit tests; Spring Boot Test for integration tests
- Frontend: Jest + React Testing Library
- Manual API testing: Postman / Insomnia

## Deployment
- Build backend jar and frontend static bundle.
- Deploy backend to Heroku / AWS Elastic Beanstalk / Azure / DigitalOcean (or Docker container on any VPS).
- Serve frontend via Netlify / Vercel / S3 + CloudFront or inside the backend (static resources).
- Use environment variables for production DB credentials and JWT secrets.
- Configure HTTPS and CORS for security.

## Future Enhancements
- Integrate real payment gateway (Stripe / Razorpay)
- Add calendar integrations for bookings
- Multi-tenant support for multiple PG owners with dashboards
- Role-based dashboards with rich analytics
- Push notifications (FCM) and email notifications

## Contributing
1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m "feat: add ..."`
4. Push and create a PR

Please follow the coding standards and write tests for new features.

## License
MIT License (or choose your license)

## Contact
Rohan Pawar — rohan@example.com
