# Employee Management REST API

A RESTful API application built with **Java 17**, **Spring Boot 3**, **Spring Data JPA**, and **MySQL**.

---

## 📌 Features

1. **Employee Entity with JPA**:
   - `id`: Auto-generated unique ID (`GenerationType.IDENTITY`).
   - `firstName`: Mandatory string.
   - `lastName`: Mandatory string.
   - `email`: Mandatory, unique, formatted email address.
   - `department`: Department name.
   - `salary`: Non-negative decimal number.
   - `phoneNumber`: Phone contact string.
2. **Spring Data JPA Repository**:
   - Extends `JpaRepository<Employee, Long>` with custom lookup (`findByEmail`, `existsByEmail`).
3. **Service Layer**:
   - Clean interface and implementation with transactional management.
   - Complete CRUD operations: Create, Read (all & by ID), Update, Delete.
   - Custom `ResourceNotFoundException` thrown on missing records.
4. **REST Controller (`/api/v1/employees`)**:
   - Standard HTTP response codes: `201 CREATED`, `200 OK`, `404 NOT FOUND`, `400 BAD REQUEST`.
   - Global exception handling via `@RestControllerAdvice` with structured error responses.
5. **Flexible Configuration**:
   - Configurable server port and MySQL datasource via environment variables.

---

## 🏗️ Architecture & Package Layout

```
src/main/java/com/example/employeemanagement/
├── EmployeeManagementApplication.java
├── controller/
│   └── EmployeeController.java
├── dto/
│   ├── EmployeeDto.java
│   └── ErrorResponse.java
├── entity/
│   └── Employee.java
├── exception/
│   ├── GlobalExceptionHandler.java
│   └── ResourceNotFoundException.java
├── repository/
│   └── EmployeeRepository.java
└── service/
    ├── EmployeeService.java
    └── impl/
        └── EmployeeServiceImpl.java
```

---

## ⚙️ Configuration & Environment Variables

All settings can be customized in `src/main/resources/application.properties` or overridden via environment variables:

| Environment Variable | Description | Default Value |
|----------------------|-------------|---------------|
| `SERVER_PORT` | Port on which the Spring Boot application runs | `8080` |
| `DB_URL` | JDBC URL for MySQL database | `jdbc:mysql://localhost:3306/employee_db?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true` |
| `DB_USERNAME` | MySQL database username | Required |
| `DB_PASSWORD` | MySQL database password | Required |

Set `DB_USERNAME` and `DB_PASSWORD` in the environment before starting the application. Do not commit database credentials to source control.

### Setting Up MySQL Database

Before running the application, create the database in MySQL:

```sql
CREATE DATABASE IF NOT EXISTS employee_db;
```

---

## 🚀 Building & Running

### Using Maven:
```bash
# Build the project
mvn clean install

# Run the Spring Boot application
mvn spring-boot:run
```

Or pass custom environment variables at runtime:
```bash
SERVER_PORT=8081 DB_URL=jdbc:mysql://localhost:3306/employee_db DB_USERNAME=root DB_PASSWORD=secret mvn spring-boot:run
```

---

## 📡 API Endpoints

Base URL: `http://localhost:8080/api/v1/employees`

### 1. Create Employee
* **Method:** `POST`
* **URL:** `/api/v1/employees`
* **Status:** `201 CREATED`
* **Request Body:**
```json
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "department": "Engineering",
  "salary": 85000.0,
  "phoneNumber": "+1-555-0199"
}
```
* **Sample cURL:**
```bash
curl -X POST http://localhost:8080/api/v1/employees \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "John",
    "lastName": "Doe",
    "email": "john.doe@example.com",
    "department": "Engineering",
    "salary": 85000.0,
    "phoneNumber": "+1-555-0199"
  }'
```

---

### 2. Get All Employees
* **Method:** `GET`
* **URL:** `/api/v1/employees`
* **Status:** `200 OK`
* **Sample cURL:**
```bash
curl -X GET http://localhost:8080/api/v1/employees
```

---

### 3. Get Employee By ID
* **Method:** `GET`
* **URL:** `/api/v1/employees/{id}`
* **Status:** `200 OK` (or `404 NOT FOUND` if ID does not exist)
* **Sample cURL:**
```bash
curl -X GET http://localhost:8080/api/v1/employees/1
```

---

### 4. Update Employee
* **Method:** `PUT`
* **URL:** `/api/v1/employees/{id}`
* **Status:** `200 OK` (or `404 NOT FOUND` if ID does not exist)
* **Request Body:**
```json
{
  "firstName": "John",
  "lastName": "Smith",
  "email": "john.smith@example.com",
  "department": "Senior Engineering",
  "salary": 95000.0,
  "phoneNumber": "+1-555-0200"
}
```
* **Sample cURL:**
```bash
curl -X PUT http://localhost:8080/api/v1/employees/1 \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "John",
    "lastName": "Smith",
    "email": "john.smith@example.com",
    "department": "Senior Engineering",
    "salary": 95000.0,
    "phoneNumber": "+1-555-0200"
  }'
```

---

### 5. Delete Employee
* **Method:** `DELETE`
* **URL:** `/api/v1/employees/{id}`
* **Status:** `200 OK` (or `404 NOT FOUND` if ID does not exist)
* **Sample Response:**
```json
{
  "message": "Employee deleted successfully with id: 1",
  "status": 200
}
```
* **Sample cURL:**
```bash
curl -X DELETE http://localhost:8080/api/v1/employees/1
```

---

## 🛡️ Error Handling

The application provides standardized error payloads.

### Resource Not Found (404) Example:
```json
{
  "timestamp": "2026-11-20T21:05:00",
  "status": 404,
  "error": "Not Found",
  "message": "Employee not found with id : '99'",
  "path": "/api/v1/employees/99"
}
```

### Validation Error (400) Example:
```json
{
  "timestamp": "2026-11-20T21:05:00",
  "status": 400,
  "error": "Bad Request",
  "message": "Validation failed for one or more fields",
  "path": "/api/v1/employees",
  "validationErrors": {
    "email": "Email must be a valid email address",
    "firstName": "First name is mandatory"
  }
}
```
