# Resource Booking System

A secure RESTful Resource Booking System built with Java 17, Spring Boot, Spring Security, JWT, Spring Data JPA and MySQL.

## Features

- JWT based login authentication
- BCrypt password hashing
- ADMIN and USER role based authorization
- Resource CRUD for ADMIN
- Resource read access for USER
- Reservation creation using the authenticated JWT identity
- USER can access only their own reservations
- ADMIN can access all reservations
- Reservation statuses: PENDING, CONFIRMED, CANCELLED
- BigDecimal reservation pricing
- Filtering by status, minimum price and maximum price
- Pagination with page and size
- Optional sorting
- Validation and global exception handling
- Booking conflict detection
- Swagger/OpenAPI documentation
- Seed ADMIN and USER accounts
- Unit/integration test starter

## Technologies

- Java 17
- Spring Boot 3.5
- Spring Web
- Spring Data JPA / Hibernate
- Spring Security
- JWT (JJWT)
- MySQL
- Maven
- Springdoc OpenAPI
- JUnit 5

## Database setup

Create MySQL database:

```sql
CREATE DATABASE resource_booking;
```

Default configuration expects:

```text
DB_URL=jdbc:mysql://localhost:3306/resource_booking?createDatabaseIfNotExist=true&useSSL=false&serverTimezone=UTC
DB_USERNAME=root
DB_PASSWORD=root
```

Change `DB_PASSWORD` to your local MySQL password.

## Environment variables

Recommended:

```text
DB_URL=jdbc:mysql://localhost:3306/resource_booking?createDatabaseIfNotExist=true&useSSL=false&serverTimezone=UTC
DB_USERNAME=root
DB_PASSWORD=your_mysql_password
JWT_SECRET=replace-with-a-long-random-secret
JWT_EXPIRATION=86400000
SERVER_PORT=8080
```

The project also contains safe local defaults so it can be started quickly for development.

## Run the project

Make sure Java 17+ and Maven are installed.

```bash
mvn clean install
mvn spring-boot:run
```

Or run `ResourceBookingApplication` from IntelliJ IDEA / Eclipse / VS Code.

Application:

```text
http://localhost:8080
```

Swagger:

```text
http://localhost:8080/swagger-ui.html
```

OpenAPI JSON:

```text
http://localhost:8080/v3/api-docs
```

## Seed users

The application creates these users on first startup:

| Username | Password | Role |
|---|---|---|
| admin | admin123 | ADMIN |
| user | user123 | USER |

Passwords are stored using BCrypt.

## Login

### POST /auth/login

```json
{
  "username": "user",
  "password": "user123"
}
```

Response:

```json
{
  "token": "JWT_TOKEN",
  "username": "user",
  "role": "USER"
}
```

For protected endpoints add:

```text
Authorization: Bearer JWT_TOKEN
```

## Resource APIs

| Method | Endpoint | Access |
|---|---|---|
| GET | /resources | USER, ADMIN |
| GET | /resources/{id} | USER, ADMIN |
| POST | /resources | ADMIN |
| PUT | /resources/{id} | ADMIN |
| DELETE | /resources/{id} | ADMIN |

Example resource:

```json
{
  "name": "Conference Room B",
  "description": "Room for team meetings",
  "type": "ROOM",
  "available": true,
  "price": 750.00
}
```

## Reservation APIs

| Method | Endpoint | Access |
|---|---|---|
| POST | /reservations | USER, ADMIN |
| POST | /reservations/admin | ADMIN |
| GET | /reservations | USER, ADMIN |
| GET | /reservations/{id} | USER, ADMIN |
| PUT | /reservations/{id} | ADMIN |
| DELETE | /reservations/{id} | USER (own), ADMIN |

A normal USER reservation request does not contain `userId`. The logged-in identity is taken from the JWT.

Example:

```json
{
  "resourceId": 1,
  "startTime": "2026-09-10T10:00:00",
  "endTime": "2026-09-10T12:00:00"
}
```

The reservation price is copied from the selected resource and stored as a decimal `BigDecimal`.

## Filtering, pagination and sorting

Filter by status:

```text
GET /reservations?status=CONFIRMED
```

Filter by price:

```text
GET /reservations?minPrice=500&maxPrice=2000
```

Pagination:

```text
GET /reservations?page=0&size=10
```

Sorting:

```text
GET /reservations?sort=price,desc
```

Combined:

```text
GET /reservations?status=CONFIRMED&minPrice=500&maxPrice=2000&page=0&size=10&sort=price,desc
```

Allowed sort fields are `price`, `startTime`, `endTime`, `createdAt`, and `status`.

## Reservation statuses

```text
PENDING
CONFIRMED
CANCELLED
```

## Validation and errors

The API validates required fields, non-negative prices and start/end time ordering.

Common HTTP responses:

- `200 OK`
- `201 Created`
- `204 No Content`
- `400 Bad Request`
- `401 Unauthorized`
- `403 Forbidden`
- `404 Not Found`

## Project structure

```text
src/main/java/com/rutuja/booking
├── config
├── controller
├── dto
├── entity
├── exception
├── repository
├── security
└── service
```

## Testing

Run:

```bash
mvn test
```

The test suite includes a Spring application context test. Additional security and service tests can be added as the project is extended.

## GitHub submission checklist

Before submitting:

- Do not commit real database passwords.
- Do not commit production JWT secrets.
- Test all APIs using Swagger or Postman.
- Verify ADMIN and USER permissions.
- Verify USER ownership restrictions.
- Verify pagination, filtering and sorting.
- Run `mvn clean test`.
- Include screenshots of Swagger/Postman if requested.

## Author

Rutuja Salunkhe
