# backend-developer-as-final-73527-rutuja
Final Project Assignment - This repository contains the complete final project code and documentation.
Resource Booking System

A secure RESTful Resource Booking System developed using Java 17, Spring Boot, Spring Security, JWT, Spring Data JPA, Hibernate, and MySQL.

The application allows authenticated users to view available resources and create and manage their reservations. Administrators have complete access to manage resources and reservations.

Features
JWT-based user authentication
Secure BCrypt password hashing
Role-Based Access Control (RBAC)
ADMIN and USER roles
Resource management
Reservation management
Reservation ownership protection
Reservation status management
Price-based filtering
Status-based filtering
Pagination
Sorting
Input validation
Global exception handling
Booking conflict detection
MySQL database integration
Swagger/OpenAPI documentation
Seed users for testing
Technology Stack
Java: 17
Framework: Spring Boot
Security: Spring Security + JWT
Database: MySQL
ORM: Spring Data JPA / Hibernate
Build Tool: Maven
API Documentation: Swagger/OpenAPI
Testing: JUnit 5 / Spring Boot Test
Project Structure
backend-developer-as-final-73527-rutuja
│
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com.rutuja.booking
│   │   │       ├── config
│   │   │       ├── controller
│   │   │       ├── dto
│   │   │       ├── entity
│   │   │       ├── exception
│   │   │       ├── repository
│   │   │       ├── security
│   │   │       └── service
│   │   │
│   │   └── resources
│   │       └── application.properties
│   │
│   └── test
│       └── java
│
├── postman
│   └── Resource-Booking-System.postman_collection.json
│
├── database.sql
├── pom.xml
├── README.md
└── .gitignore
Database Design

The application uses three main entities.

User

Stores application users and their roles.

User
----------------
id
username
email
password
role
Resource

Represents a bookable item.

Resource
----------------
id
name
description
type
available
price
Reservation

Stores booking information.

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
Relationships
User 1 -------- * Reservation

Resource 1 ---- * Reservation
Authentication

The application uses JWT-based authentication.

Login Endpoint
POST /auth/login
Request
{
  "username": "user",
  "password": "user123"
}
Response
{
  "token": "JWT_TOKEN",
  "username": "user",
  "role": "USER"
}

The JWT token must be sent with protected API requests:

Authorization: Bearer JWT_TOKEN
Seed Users

The application automatically creates two users during startup.

Username	Password	Role
admin	admin123	ADMIN
user	user123	USER

Passwords are stored using BCrypt hashing.

These credentials are intended for local testing.

Authorization
USER

A USER can:

Login
View resources
View individual resources
Create reservations
View their own reservations
Delete their own reservations

A USER cannot:

Create resources
Update resources
Delete resources
Access another user's reservations
Update reservations
ADMIN

An ADMIN can:

View resources
Create resources
Update resources
Delete resources
View all reservations
Create reservations
Update reservations
Delete reservations
Resource APIs
Method	Endpoint	Access
POST	/auth/login	Public
GET	/resources	USER, ADMIN
GET	/resources/{id}	USER, ADMIN
POST	/resources	ADMIN
PUT	/resources/{id}	ADMIN
DELETE	/resources/{id}	ADMIN
Create Resource
POST /resources

Example request:

{
  "name": "Conference Room B",
  "description": "Room for team meetings",
  "type": "ROOM",
  "available": true,
  "price": 750.00
}
Reservation APIs
Method	Endpoint	Access
POST	/reservations	USER, ADMIN
GET	/reservations	USER, ADMIN
GET	/reservations/{id}	USER, ADMIN
PUT	/reservations/{id}	ADMIN
DELETE	/reservations/{id}	USER (own), ADMIN
Create Reservation
POST /reservations

Example:

{
  "resourceId": 1,
  "startTime": "2026-09-10T10:00:00",
  "endTime": "2026-09-10T12:00:00"
}
Reservation Ownership

The reservation request does not contain a userId.

The authenticated user's identity is obtained from the JWT/Spring Security authentication context.

This prevents a USER from creating or accessing reservations belonging to another user by simply changing a userId in the request.

Reservation Status

Reservations support the following statuses:

PENDING
CONFIRMED
CANCELLED
Filtering

Reservations can be filtered by status and price.

Status
GET /reservations?status=CONFIRMED
Minimum Price
GET /reservations?minPrice=500
Maximum Price
GET /reservations?maxPrice=2000
Price Range
GET /reservations?minPrice=500&maxPrice=2000
Pagination

Pagination is supported using page and size.

Example:

GET /reservations?page=0&size=10
Sorting

Reservation results can optionally be sorted.

Example:

GET /reservations?sort=price,desc

Ascending:

GET /reservations?sort=price,asc

Supported sorting fields:

price
startTime
endTime
createdAt
status
Combined Filtering, Pagination and Sorting

Example:

GET /reservations?status=CONFIRMED&minPrice=500&maxPrice=2000&page=0&size=10&sort=price,desc
Validation

The application validates:

Required request fields
Resource IDs
User IDs
Non-negative prices
Reservation start and end times
Reservation status
Pagination values
Sorting fields

The reservation start time must be before the end time.

Booking Conflict Detection

The system checks whether a resource is already booked during the requested time period.

For example, if a resource is booked from:

10:00 - 12:00

another reservation overlapping this period will be rejected.

Error Handling

Global exception handling is implemented using GlobalExceptionHandler.

Common HTTP status codes:

Status	Description
200	Successful request
201	Resource created
204	Successfully deleted
400	Invalid request
401	Authentication required
403	Access denied
404	Resource not found
409	Booking conflict

Example error response:

{
  "timestamp": "2026-09-01T10:30:00",
  "status": 400,
  "message": "Start time must be before end time"
}
Database Configuration

Create the MySQL database:

CREATE DATABASE resource_booking;

The application uses environment variables for database configuration.

DB_URL
DB_USERNAME
DB_PASSWORD
JWT_SECRET
JWT_EXPIRATION
SERVER_PORT

Example:

DB_URL=jdbc:mysql://localhost:3306/resource_booking
DB_USERNAME=root
DB_PASSWORD=your_mysql_password
JWT_SECRET=your-long-random-secret
JWT_EXPIRATION=86400000
SERVER_PORT=8080

Do not commit real database passwords or JWT secrets to GitHub.

How to Run the Project
Prerequisites

Install the following:

Java 17 or higher
Maven
MySQL
Git

Check Java:

java -version

Check Maven:

mvn -version
1. Clone the Repository
git clone <your-github-repository-url>

Navigate to the project:

cd backend-developer-as-final-73527-rutuja
2. Create the Database

Open MySQL and run:

CREATE DATABASE resource_booking;
3. Configure Database Credentials

Set the required environment variables:

DB_USERNAME=root
DB_PASSWORD=your_mysql_password
4. Build the Project
mvn clean install
5. Start the Application
mvn spring-boot:run

The application runs on:

http://localhost:8080
Swagger / OpenAPI Documentation

Swagger UI:

http://localhost:8080/swagger-ui.html

OpenAPI documentation:

http://localhost:8080/v3/api-docs

Swagger can be used to test the REST APIs.

To test protected APIs:

Login using /auth/login
Copy the JWT token
Click Authorize in Swagger
Enter:
Bearer YOUR_JWT_TOKEN
Execute the protected API requests.
Postman

A Postman collection is included in the project:

postman/Resource-Booking-System.postman_collection.json

Import this file into Postman to test the APIs.

Testing

Run the test suite using:

mvn test

The project contains Spring Boot test configuration.

Important scenarios to test include:

Successful login
Invalid login
JWT authentication
ADMIN authorization
USER authorization
Resource CRUD
Reservation creation
Reservation ownership
Unauthorized reservation access
Validation errors
Reservation filtering
Pagination
Sorting
Booking conflicts
Security

The following security practices are implemented:

JWT-based stateless authentication
BCrypt password hashing
Role-based authorization
Protected REST endpoints
Server-side reservation ownership validation
JWT-based user identity
Environment-based database configuration
No real credentials committed to source control
Assignment Requirements

The project covers the required assignment functionality:

JWT login authentication

JWT token validation

BCrypt password handling

ADMIN and USER roles

Role-Based Access Control

ADMIN resource CRUD

USER resource read access

Reservation creation

Reservation ownership

ADMIN access to all reservations

USER access to own reservations

Reservation statuses

Decimal reservation price

Status filtering

Minimum price filtering

Maximum price filtering

Pagination

Optional sorting

Request validation

Error handling

MySQL integration

JPA/Hibernate

Swagger/OpenAPI

Seed users

Testing

Future Improvements

The application can be extended with:

User registration
Refresh tokens
Email notifications
Reservation cancellation workflow
Resource availability calendar
Docker support
CI/CD pipeline
Cloud deployment
More comprehensive integration tests

