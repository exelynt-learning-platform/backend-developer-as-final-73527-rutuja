# Resource Booking System

A secure RESTful Resource Booking System built using **Java 17, Spring Boot, Spring Security, JWT, Spring Data JPA, Hibernate, and MySQL**.

The application allows users to view available resources and create/manage their own reservations. Administrators have full access to manage resources and reservations.

---

## 📌 Project Overview

The Resource Booking System provides REST APIs for managing bookable resources such as:

* Conference rooms
* Vehicles
* Equipment
* Meeting rooms

The application implements JWT-based authentication and role-based authorization with two roles:

* **ADMIN**
* **USER**

Users can view resources and create reservations, while administrators have complete management access.

---

## 🚀 Features

### Authentication & Security

* JWT-based authentication
* Secure login using `/auth/login`
* BCrypt password hashing
* Stateless authentication
* JWT token validation
* Role-Based Access Control (RBAC)
* Protected REST endpoints
* Unauthorized data access prevention

### User Features

* Login using username and password
* View available resources
* Create reservations
* View only their own reservations
* View individual reservations
* Delete their own reservations

### Admin Features

* Full CRUD operations on resources
* View all reservations
* Create reservations for users
* Update reservations
* Delete reservations
* Manage reservation status

### Reservation Features

* Reservation statuses:

  * `PENDING`
  * `CONFIRMED`
  * `CANCELLED`
* Reservation price stored using `BigDecimal`
* Resource booking conflict detection
* Start/end time validation
* Reservation ownership validation

### Search & Pagination

Reservations can be filtered using:

* Status
* Minimum price
* Maximum price

The API also supports:

* Pagination using `page` and `size`
* Optional sorting

---

## 🛠️ Technologies Used

| Technology      | Purpose                        |
| --------------- | ------------------------------ |
| Java 17         | Programming Language           |
| Spring Boot     | Backend Framework              |
| Spring Web      | REST APIs                      |
| Spring Security | Authentication & Authorization |
| JWT             | Token-based Authentication     |
| BCrypt          | Password Encryption            |
| Spring Data JPA | Database Access                |
| Hibernate       | ORM                            |
| MySQL           | Database                       |
| Maven           | Build Tool                     |
| Swagger/OpenAPI | API Documentation              |
| JUnit 5         | Testing                        |

---

## 📂 Project Structure

```text
src
└── main
    ├── java
    │   └── com.rutuja.booking
    │       ├── config
    │       │   └── DataInitializer.java
    │       │
    │       ├── controller
    │       │   ├── AuthController.java
    │       │   ├── ResourceController.java
    │       │   └── ReservationController.java
    │       │
    │       ├── dto
    │       │   ├── LoginRequest.java
    │       │   ├── LoginResponse.java
    │       │   ├── ResourceRequest.java
    │       │   ├── ResourceResponse.java
    │       │   ├── ReservationRequest.java
    │       │   ├── ReservationAdminRequest.java
    │       │   └── ReservationResponse.java
    │       │
    │       ├── entity
    │       │   ├── User.java
    │       │   ├── Resource.java
    │       │   ├── Reservation.java
    │       │   ├── Role.java
    │       │   └── ReservationStatus.java
    │       │
    │       ├── exception
    │       │   ├── BadRequestException.java
    │       │   ├── ResourceNotFoundException.java
    │       │   ├── UnauthorizedException.java
    │       │   └── GlobalExceptionHandler.java
    │       │
    │       ├── repository
    │       │   ├── UserRepository.java
    │       │   ├── ResourceRepository.java
    │       │   └── ReservationRepository.java
    │       │
    │       ├── security
    │       │   ├── JwtService.java
    │       │   ├── JwtAuthenticationFilter.java
    │       │   ├── CustomUserDetailsService.java
    │       │   └── SecurityConfig.java
    │       │
    │       └── service
    │           ├── AuthService.java
    │           ├── ResourceService.java
    │           └── ReservationService.java
    │
    └── resources
        └── application.properties

src
└── test
    └── java
        └── com.rutuja.booking
            └── ResourceBookingApplicationTests.java

pom.xml
README.md
database.sql
.gitignore
```

---

# 🗄️ Database Design

The application uses three main entities.

### User

```text
User
----------------
id
username
email
password
role
```

### Resource

```text
Resource
----------------
id
name
description
type
available
price
```

### Reservation

```text
Reservation
----------------
id
resource_id
user_id
start_time
end_time
price
status
created_at
```

### Entity Relationships

```text
User
  |
  | 1
  |
  | *
Reservation
  |
  | *
  |
  | 1
Resource
```

One user can have multiple reservations, and one resource can have multiple reservations.

---

# 🔐 Authentication

Authentication is implemented using **JWT (JSON Web Token)**.

## Login

### Endpoint

```http
POST /auth/login
```

### Request

```json
{
  "username": "user",
  "password": "user123"
}
```

### Response

```json
{
  "token": "JWT_TOKEN",
  "username": "user",
  "role": "USER"
}
```

After login, use the returned JWT token for protected endpoints.

```text
Authorization: Bearer JWT_TOKEN
```

---

# 👤 Seed Users

The application automatically creates test users when it starts.

| Username | Password | Role  |
| -------- | -------- | ----- |
| admin    | admin123 | ADMIN |
| user     | user123  | USER  |

Passwords are stored in the database using **BCrypt hashing**.

> These credentials are intended for local testing only. Change them for any real deployment.

---

# 🔑 Role-Based Authorization

## USER permissions

| Operation                       | Permission |
| ------------------------------- | ---------- |
| Login                           | ✅          |
| View resources                  | ✅          |
| Create resource                 | ❌          |
| Update resource                 | ❌          |
| Delete resource                 | ❌          |
| Create reservation              | ✅          |
| View own reservations           | ✅          |
| View another user's reservation | ❌          |
| Update reservation              | ❌          |
| Delete own reservation          | ✅          |

## ADMIN permissions

| Operation             | Permission |
| --------------------- | ---------- |
| Login                 | ✅          |
| View resources        | ✅          |
| Create resource       | ✅          |
| Update resource       | ✅          |
| Delete resource       | ✅          |
| Create reservation    | ✅          |
| View all reservations | ✅          |
| Update reservation    | ✅          |
| Delete reservation    | ✅          |

---

# 📡 REST API Endpoints

## Authentication

### Login

```http
POST /auth/login
```

---

## Resources

### Get all resources

```http
GET /resources
```

Access:

```text
USER, ADMIN
```

### Get resource by ID

```http
GET /resources/{id}
```

Access:

```text
USER, ADMIN
```

### Create resource

```http
POST /resources
```

Access:

```text
ADMIN
```

Example:

```json
{
  "name": "Conference Room B",
  "description": "Meeting room for team discussions",
  "type": "ROOM",
  "available": true,
  "price": 750.00
}
```

### Update resource

```http
PUT /resources/{id}
```

Access:

```text
ADMIN
```

### Delete resource

```http
DELETE /resources/{id}
```

Access:

```text
ADMIN
```

---

# 📅 Reservation APIs

### Create reservation

```http
POST /reservations
```

Access:

```text
USER, ADMIN
```

Example:

```json
{
  "resourceId": 1,
  "startTime": "2026-09-10T10:00:00",
  "endTime": "2026-09-10T12:00:00"
}
```

### Important Security Feature

The request does **not** contain `userId`.

The logged-in user's identity is obtained from the JWT authentication context.

This prevents a user from creating a reservation on behalf of another user by modifying the request.

---

### Get reservations

```http
GET /reservations
```

For a USER, only their own reservations are returned.

For an ADMIN, all reservations are returned.

---

### Get reservation by ID

```http
GET /reservations/{id}
```

A USER can access only their own reservation.

An ADMIN can access any reservation.

---

### Update reservation

```http
PUT /reservations/{id}
```

Access:

```text
ADMIN
```

Example:

```json
{
  "resourceId": 1,
  "userId": 2,
  "startTime": "2026-09-10T10:00:00",
  "endTime": "2026-09-10T12:00:00",
  "status": "CONFIRMED"
}
```

---

### Delete reservation

```http
DELETE /reservations/{id}
```

A USER can delete only their own reservation.

An ADMIN can delete any reservation.

---

# 🔎 Reservation Filtering

Reservations support filtering by status and price.

## Filter by status

```http
GET /reservations?status=CONFIRMED
```

Available statuses:

```text
PENDING
CONFIRMED
CANCELLED
```

## Filter by minimum price

```http
GET /reservations?minPrice=500
```

## Filter by maximum price

```http
GET /reservations?maxPrice=2000
```

## Filter by price range

```http
GET /reservations?minPrice=500&maxPrice=2000
```

---

# 📄 Pagination

Pagination is supported using `page` and `size`.

Example:

```http
GET /reservations?page=0&size=10
```

Where:

```text
page = page number
size = number of records per page
```

---

# ↕️ Sorting

Reservations can optionally be sorted.

Example:

```http
GET /reservations?sort=price,desc
```

Ascending:

```http
GET /reservations?sort=price,asc
```

Other supported fields include:

```text
price
startTime
endTime
createdAt
status
```

---

# 🔎 Combined Search

Filtering, pagination and sorting can be used together.

Example:

```http
GET /reservations?status=CONFIRMED&minPrice=500&maxPrice=2000&page=0&size=10&sort=price,desc
```

---

# ✅ Validation

The application validates incoming requests.

Examples:

* Required fields cannot be empty.
* Resource price cannot be negative.
* Reservation start time must be before end time.
* Resource ID must exist.
* User ID must exist for administrator reservation management.
* Invalid reservation status is rejected.
* Invalid pagination values are rejected.
* Invalid sorting fields are rejected.

---

# ⚠️ Error Handling

The application provides centralized exception handling using `GlobalExceptionHandler`.

Common HTTP status codes:

| Status | Meaning                        |
| ------ | ------------------------------ |
| 200    | Request successful             |
| 201    | Resource created               |
| 204    | Resource deleted               |
| 400    | Bad request / validation error |
| 401    | Authentication required        |
| 403    | Access denied                  |
| 404    | Resource not found             |
| 409    | Booking conflict               |

Example error:

```json
{
  "timestamp": "2026-09-01T10:30:00",
  "status": 400,
  "message": "Start time must be before end time"
}
```

---

# 🗓️ Booking Conflict Detection

The system checks whether a resource is already reserved for the requested time.

For example, if:

```text
Conference Room A
10:00 AM - 12:00 PM
```

is already booked, another reservation overlapping that period is rejected.

Example:

```text
11:00 AM - 1:00 PM
```

will be rejected because it overlaps with the existing reservation.

---

# 💰 Reservation Price

Reservation prices are stored using Java's:

```java
BigDecimal
```

The database column uses decimal precision:

```text
DECIMAL(10,2)
```

This is preferable to using floating-point types for monetary values.

---

# 🗃️ Database Configuration

Create the MySQL database:

```sql
CREATE DATABASE resource_booking;
```

The application uses environment variables for database configuration.

```text
DB_URL
DB_USERNAME
DB_PASSWORD
JWT_SECRET
JWT_EXPIRATION
SERVER_PORT
```

Example:

```text
DB_URL=jdbc:mysql://localhost:3306/resource_booking
DB_USERNAME=root
DB_PASSWORD=your_password
JWT_SECRET=your-long-random-secret
JWT_EXPIRATION=86400000
SERVER_PORT=8080
```

Do not commit real passwords or JWT secrets to GitHub.

---

# ▶️ How to Run

## Prerequisites

Install:

* Java 17 or higher
* Maven
* MySQL
* Git

Check Java:

```bash
java -version
```

Check Maven:

```bash
mvn -version
```

---

## Step 1: Clone the repository

```bash
git clone <your-github-repository-url>
```

Move into the project:

```bash
cd resource-booking-system
```

---

## Step 2: Create database

Open MySQL and execute:

```sql
CREATE DATABASE resource_booking;
```

---

## Step 3: Configure database

Set your environment variables or update the local database configuration.

Example:

```text
DB_USERNAME=root
DB_PASSWORD=your_mysql_password
```

---

## Step 4: Build the project

```bash
mvn clean install
```

---

## Step 5: Run the application

```bash
mvn spring-boot:run
```

The application will start on:

```text
http://localhost:8080
```

---

# 📖 Swagger / OpenAPI

Swagger UI is available at:

```text
http://localhost:8080/swagger-ui.html
```

OpenAPI documentation:

```text
http://localhost:8080/v3/api-docs
```

Swagger can be used to:

1. Login
2. Copy the JWT token
3. Click **Authorize**
4. Enter:

```text
Bearer YOUR_JWT_TOKEN
```

5. Test protected APIs.

---

# 📬 Postman

A Postman collection is included in:

```text
postman/Resource-Booking-System.postman_collection.json
```

Import the collection into Postman and configure:

```text
baseUrl = http://localhost:8080
```

After login, use the JWT token for protected APIs.

---

# 🧪 Testing

Run the tests using:

```bash
mvn test
```

The project includes Spring Boot test configuration and can be extended with additional tests for:

* Authentication
* JWT validation
* ADMIN authorization
* USER authorization
* Reservation ownership
* Resource CRUD
* Validation
* Filtering
* Pagination
* Sorting

---

# 🔒 Security Considerations

The application follows these security practices:

* Passwords are hashed using BCrypt.
* Authentication uses JWT.
* Sessions are stateless.
* Protected endpoints require authentication.
* ADMIN endpoints require the ADMIN role.
* USER reservation ownership is checked on the server.
* User identity is obtained from the authenticated JWT instead of request data.
* Database credentials are configurable using environment variables.
* JWT secrets should not be committed to source control.

---

# 📋 Assignment Requirements Covered

| Requirement              | Implementation |
| ------------------------ | -------------- |
| JWT Login                | ✅              |
| JWT Token Validation     | ✅              |
| BCrypt Password Handling | ✅              |
| ADMIN Role               | ✅              |
| USER Role                | ✅              |
| RBAC                     | ✅              |
| Resource CRUD            | ✅              |
| Reservation CRUD         | ✅              |
| USER Own Reservations    | ✅              |
| ADMIN All Reservations   | ✅              |
| JWT-based User Identity  | ✅              |
| Reservation Status       | ✅              |
| Decimal Price            | ✅              |
| Status Filtering         | ✅              |
| Minimum Price Filtering  | ✅              |
| Maximum Price Filtering  | ✅              |
| Pagination               | ✅              |
| Sorting                  | ✅              |
| Validation               | ✅              |
| Error Handling           | ✅              |
| MySQL + JPA/Hibernate    | ✅              |
| Swagger/OpenAPI          | ✅              |
| Seed Users               | ✅              |
| README Documentation     | ✅              |
| Testing                  | ✅              |

---

# 🔮 Future Improvements

Possible future enhancements include:

* Email notifications for reservations
* Refresh tokens
* User registration
* Reservation cancellation endpoint
* Advanced resource availability calendar
* Docker support
* Redis caching
* More extensive integration tests
* Deployment using AWS/Azure
* CI/CD pipeline using GitHub Actions

---

# 👩‍💻 Author

**Rutuja Salunkhe**

Resource Booking System — Backend Developer Assignment

Built using Java, Spring Boot, Spring Security, JWT, JPA/Hibernate and MySQL.
