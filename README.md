# URL Shortener — Spring Boot Backend

A REST API backend for a URL shortening service built with Spring Boot 4.x, Spring Security, JWT authentication, and MySQL.

---

## Tech Stack

- **Java 21**
- **Spring Boot 4.1.0**
- **Spring Security 7** — authentication & authorization
- **JWT (jjwt)** — stateless token-based auth
- **Spring Data JPA + Hibernate** — ORM
- **MySQL 8** — database
- **Lombok** — boilerplate reduction
- **Maven** — build tool

---

## Project Structure

```
src/main/java/com/url/shortener/
│
├── models/
│   ├── User.java               # User entity
│   ├── UrlMapping.java         # Shortened URL entity
│   └── ClickEvent.java         # Click tracking entity
│
├── repository/
│   ├── UserRepository.java
│   ├── UrlMappingRepository.java
│   └── ClickEventRepository.java
│
├── service/
│   ├── UserService.java        # Registration logic
│   ├── UrlMappingService.java  # URL shortening + analytics logic
│   ├── UserDetailsImpl.java    # Spring Security user wrapper
│   └── UserDetailsServiceImpl.java
│
├── controller/
│   ├── AuthController.java     # /api/auth endpoints
│   ├── UrlMappingController.java
│   ├── UserController.java
│   └── RedirectController.java # Public redirect endpoint
│
├── dtos/
│   ├── ClickEventDTO.java
│   ├── LoginRequest.java
│   ├── RegisterRequest.java
│   └── UrlMappingDTO.java
│
├── security/
│   └── WebSecurityConfig.java  # Spring Security config
│
└── security/jwt/
    ├── JwtUtils.java
    ├── JwtAuthenticationFilter.java
    └── JwtAuthenticationResponse.java
```

---

## Database Schema

```
User (1) ──── (many) UrlMapping (1) ──── (many) ClickEvent
```

- One user can have many shortened URLs
- Each shortened URL records a `ClickEvent` every time it is accessed

---

## API Endpoints

### Auth — `/api/auth`

| Method | Endpoint | Access | Description |
|--------|----------|--------|-------------|
| POST | `/api/auth/public/register` | Public | Register a new user |
| POST | `/api/auth/public/login` | Public | Login and receive JWT token |

### URL Management — `/api/urls`

| Method | Endpoint            | Access | Description |
|--------|---------------------|--------|-------------|
| POST | `/api/urls/shorten` | Protected | Shorten a new URL |
| GET | `/api/urls/myurls`        | Protected | Get all URLs for logged-in user |

### Analytics — `/api/urls`

| Method | Endpoint | Access | Description |
|--------|----------|--------|-------------|
| GET | `/api/urls/analytics/{shortUrl}` | Protected | Click breakdown for a specific URL |
| GET | `/api/urls/totalClicks` | Protected | Total clicks across all user URLs |

### Redirect

| Method | Endpoint | Access | Description |
|--------|----------|--------|-------------|
| GET | `/{shortUrl}` | Public | Redirects to original URL (302) |

---

## How JWT Auth Works

```
POST /login
      ↓
Credentials verified against DB
      ↓
JWT token generated and returned
      ↓
Client sends token in every request:
Authorization: Bearer <token>
      ↓
JwtAuthenticationFilter validates token
      ↓
Request reaches controller with user identity known
```

---

## Local Setup

### Prerequisites
- Java 21
- MySQL 8
- Maven

### Steps

**1. Clone the repo**
```bash
git clone https://github.com/chandan-howale/url-shortener-sb.git
cd url-shortener-sb/shortener-initial-setup
```

**2. Create the database**
```sql
CREATE DATABASE urlshortenerdb;
```

**3. Configure environment**

Copy the example properties file:
```bash
cp src/main/resources/application.properties.example src/main/resources/application.properties
```

Fill in your values:
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/urlshortenerdb
spring.datasource.username=root
spring.datasource.password=your_password

jwt.secret=your_base64_encoded_secret
jwt.expiration=172800000
```

> ⚠️ `jwt.secret` must be a valid Base64 encoded string.

**4. Run the app**
```bash
mvn spring-boot:run
```

Hibernate will auto-create all tables on first run (`ddl-auto=update`).

---

## Author

**Chandan Howale**
| MCA Graduate | Java Backend Developer |
[GitHub](https://github.com/chandan-howale) | [LinkedIn](https://www.linkedin.com/in/chandanhowale)