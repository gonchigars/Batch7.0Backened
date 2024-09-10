### Design Document for Event Scheduling System (Backend)

#### 1. **Overview**
The Event Scheduling System is designed to allow users (both admins and regular users) to manage availability, schedule sessions, and receive notifications about upcoming events. This is a Spring Boot-based backend service that provides REST APIs for managing users, availability, sessions, and notifications.

#### 2. **Modules & Responsibilities**
- **User Management**: Handles user registration, authentication, and role management.
- **Availability Management**: Manages users' available time slots for scheduling.
- **Session Scheduling**: Allows admins to schedule sessions based on user availability.
- **Notification Service**: Notifies users about upcoming sessions.
- **Exception Handling**: Centralized management of custom exceptions and general errors.

#### 3. **Architecture**
- **Layered Structure**:
  - **Controller Layer**: Defines RESTful endpoints for user interaction.
  - **Service Layer**: Implements business logic.
  - **Repository Layer**: Handles database operations using JPA.

#### 4. **Components**

1. **MovieApiApplication.java**:
   - Main entry point of the Spring Boot application.

2. **WebConfig.java**:
   - Configures CORS to allow frontend access to the backend (specifically from `http://localhost:3000`).

3. **Controller Layer**:
   - **AvailabilityController**: 
     - Manages CRUD operations for user availability.
     - Endpoints: 
       - `POST /add`: Adds availability for a user.
       - `PUT /update/{id}`: Updates availability by ID.
       - `DELETE /delete/{id}`: Deletes availability by ID.
       - `GET /user/{userId}`: Retrieves availability for a specific user.

   - **NotificationController**: 
     - Provides API to retrieve sessions scheduled by admins and associated with availability.
     - Endpoints:
       - `GET /sessions/admin/{adminId}`: Retrieves sessions scheduled by an admin.
       - `GET /sessions/availability/{availabilityId}`: Retrieves sessions by availability.

   - **SessionController**: 
     - Manages session scheduling and rescheduling.
     - Endpoints:
       - `POST /schedule`: Schedules a session.
       - `PUT /reschedule/{id}`: Reschedules a session.
       - `DELETE /cancel/{id}`: Cancels a session.

   - **UserController**: 
     - Manages user registration and login.
     - Endpoints:
       - `POST /register`: Registers a new user.
       - `POST /login`: Authenticates a user.

4. **Service Layer**:
   - **AvailabilityService**: 
     - Business logic for managing availability.
   - **NotificationService**: 
     - Retrieves sessions related to specific admins or availability.
   - **SessionService**: 
     - Handles session scheduling and conflict checks.
   - **UserService**: 
     - User authentication and registration.

5. **Exception Handling**:
   - **GlobalExceptionHandler**: 
     - Centralized error handling for user-related and runtime exceptions.
     - Custom handling for `UserAlreadyExistsException`.

#### 5. **Data Models**

1. **User**:
   - Attributes: 
     - `id`, `email`, `password`, `role`
   - Relationships: 
     - One-to-many with `Availability`, `Session`, and `Notification`.

2. **Availability**:
   - Attributes: 
     - `id`, `user`, `dayOfWeek`, `startTime`, `endTime`, `intervalDuration`
   - Relationships: 
     - Many-to-one with `User`.
     - One-to-many with `Session`.

3. **Session**:
   - Attributes: 
     - `id`, `admin`, `availability`, `startTime`, `endTime`, `sessionType`
   - Relationships: 
     - Many-to-one with `Admin`, `Availability`.

4. **Notification**:
   - Attributes: 
     - `id`, `user`, `session`, `notificationType`, `sentAt`
   - Relationships: 
     - Many-to-one with `User` and `Session`.

#### 6. **Database Schema (JPA Models)**

- **User Table**:
  - `id`: Primary key.
  - `email`: Unique.
  - `password`: Stored in plain text (needs improvement).
  - `role`: User roles (e.g., `USER`, `ADMIN`).

- **Availability Table**:
  - `id`: Primary key.
  - `dayOfWeek`: Represents the day of the week.
  - `startTime`, `endTime`: Represents the available time range.
  - `intervalDuration`: Duration in minutes.

- **Session Table**:
  - `id`: Primary key.
  - `startTime`, `endTime`: Scheduled session times.
  - `sessionType`: One-on-one or group session.
  - `admin_id`: Foreign key linking to `User`.

- **Notification Table**:
  - `id`: Primary key.
  - `notificationType`: Type of notification (e.g., `email`, `sms`).
  - `sentAt`: Timestamp of when the notification was sent.

#### 7. **APIs Design**

| API Endpoint | Method | Description | Request Body/Params | Response |
|--------------|--------|-------------|---------------------|----------|
| `/api/users/register` | POST | Registers a new user | `User` | `User` |
| `/api/users/login` | POST | Authenticates a user | `LoginRequest` | `String` |
| `/api/availability/add` | POST | Adds availability | `Availability` | `Availability` |
| `/api/availability/user/{userId}` | GET | Gets user availability | `userId` param | `List<AvailabilityDTO>` |
| `/api/sessions/schedule` | POST | Schedules a session | `SessionRequest` | `Session` |
| `/notifications/sessions/admin/{adminId}` | GET | Gets sessions by admin | `adminId` param | `List<Session>` |

#### 8. **Security**
- **User Roles**: 
  - `USER`: Can manage their availability and view their sessions.
  - `ADMIN`: Can schedule sessions for other users and manage sessions.

- **Authentication**: 
  - The current implementation lacks secure password storage and should use hashing mechanisms (e.g., BCrypt).

#### 9. **Improvements & Future Enhancements**
- **Password Hashing**: Store hashed passwords instead of plain text.
- **Session Conflict Handling**: Add more granular checks for session conflicts.
- **Notification Enhancements**: Add more notification types (e.g., push notifications).
- **Role-based Access Control (RBAC)**: Implement fine-grained role control and restrict specific actions to admin users.

#### 10. **Technology Stack**
- **Backend Framework**: Spring Boot
- **Database**: MySQL (JPA)
- **Build Tool**: Maven
- **Logging**: SLF4J with Logback
- **Frontend**: (Assumed) React for client-side.

#### 11. **Deployment & Scaling**
- The backend is designed to scale with horizontal replication. The use of RESTful APIs enables easy integration with various frontend clients, such as web, mobile, and desktop applications.

This design document outlines the system's architecture, components, and future improvements for a comprehensive scheduling solution.
