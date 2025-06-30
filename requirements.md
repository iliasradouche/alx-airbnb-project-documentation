# Airbnb Clone Backend — Requirement Specifications

This document details the technical and functional requirements for three core backend features: User Authentication, Property Management, and Booking System. Each section includes API endpoints, input/output specifications, validation rules, and performance criteria.

---

## 1. User Authentication

### Functional Requirements
- Allow users to register as guests or hosts.
- Support login via email/password and OAuth (Google, Facebook).
- Issue JSON Web Tokens (JWT) on successful authentication.
- Allow users to update their profile information and password.

### API Endpoints

#### Register User
- **POST** `/api/v1/auth/register`
- **Input:**  
  ```json
  {
    "email": "user@example.com",
    "password": "string",
    "role": "guest|host",
    "name": "string"
  }
  ```
- **Output:**
  - `201 Created`:  
    ```json
    {
      "id": "uuid",
      "email": "user@example.com",
      "role": "guest",
      "token": "jwt-token"
    }
    ```
  - `400 Bad Request` (validation error): Error message

#### Login
- **POST** `/api/v1/auth/login`
- **Input:**  
  ```json
  {
    "email": "user@example.com",
    "password": "string"
  }
  ```
- **Output:**
  - `200 OK`:
    ```json
    {
      "token": "jwt-token",
      "user": { "id": "uuid", "email": "user@example.com", "role": "guest" }
    }
    ```
  - `401 Unauthorized`: Error message

#### OAuth Callback
- **GET** `/api/v1/auth/oauth/callback?provider=google|facebook&code=oauth-code`
- **Output:** JWT token and user object or error.

#### Update Profile
- **PUT** `/api/v1/users/{user_id}`
- **Authentication:** Required (JWT)
- **Input:**  
  ```json
  {
    "name": "string",
    "profile_photo_url": "string",
    "contact_info": "string"
  }
  ```
- **Output:** Updated user object

### Validation Rules
- Email must be unique, valid.
- Password must be at least 8 characters, include letters and numbers.
- Role must be ‘guest’ or ‘host’.

### Performance Criteria
- Registration and login responses within 500ms under normal load.
- Token generation and validation must not exceed 100ms per request.
- System must support at least 100 concurrent authentication requests.

---

## 2. Property Management

### Functional Requirements
- Hosts can create, update, and delete property listings.
- Properties contain title, description, location, price, images, amenities, and availability calendar.
- Only the property owner (host) or an admin can modify or delete a property.

### API Endpoints

#### Create Property
- **POST** `/api/v1/properties`
- **Authentication:** Host JWT required
- **Input:**  
  ```json
  {
    "title": "Cozy Apartment",
    "description": "2 bed, city center",
    "location": "City, Country",
    "price_per_night": 100,
    "amenities": ["wifi", "pool"],
    "images": ["url1", "url2"],
    "availability": [
      {"start_date": "2025-07-01", "end_date": "2025-07-05"}
    ]
  }
  ```
- **Output:**  
  - `201 Created`: Property object
  - `400 Bad Request`: Validation error

#### Update Property
- **PUT** `/api/v1/properties/{property_id}`
- **Authentication:** Host JWT required
- **Input:** (any updatable fields)
- **Output:** Updated property object

#### Delete Property
- **DELETE** `/api/v1/properties/{property_id}`
- **Authentication:** Host JWT required
- **Output:**  
  - `204 No Content`
  - `403 Forbidden` if not owner

#### List/Search Properties
- **GET** `/api/v1/properties?location=Paris&guests=2&min_price=80&max_price=150&amenities=wifi,pool`
- **Output:** List of properties matching filters

### Validation Rules
- Only authenticated hosts can create/edit/delete.
- Title, description, location, and price are required.
- Availability dates must not overlap for the same property.
- Price must be a positive number.

### Performance Criteria
- Property search must return results within 1 second for up to 10,000 listings.
- Create/update/delete must complete within 800ms under normal load.

---

## 3. Booking System

### Functional Requirements
- Guests can book properties for available dates.
- Prevent double booking for overlapping dates.
- Allow guests/hosts to cancel bookings according to policy.
- Track booking statuses: pending, confirmed, cancelled, completed.

### API Endpoints

#### Create Booking
- **POST** `/api/v1/bookings`
- **Authentication:** Guest JWT required
- **Input:**  
  ```json
  {
    "property_id": "uuid",
    "check_in": "2025-07-01",
    "check_out": "2025-07-05",
    "guests": 2
  }
  ```
- **Output:**  
  - `201 Created`: Booking object
  - `400 Bad Request`: Validation error (e.g., dates not available)

#### Cancel Booking
- **DELETE** `/api/v1/bookings/{booking_id}`
- **Authentication:** Guest or Host JWT required
- **Output:**  
  - `200 OK`: Booking cancellation confirmation
  - `403 Forbidden`: Not permitted

#### List User Bookings
- **GET** `/api/v1/bookings?user_id={user_id}`
- **Authentication:** JWT required
- **Output:** List of bookings

### Validation Rules
- Check-in/check-out must be valid dates and not overlap existing bookings.
- Guests cannot book their own properties.
- Booking duration must be at least 1 night.

### Performance Criteria
- Booking creation and availability checks must complete within 1 second.
- System must handle at least 50 concurrent booking requests without race conditions or double bookings.

---

## General Notes

- All endpoints must follow RESTful conventions and return standard HTTP status codes.
- Input validation errors must be descriptive.
- All APIs require secure authentication via JWT unless stated otherwise.
- All personal and payment data must be encrypted in transit (HTTPS) and at rest.

---