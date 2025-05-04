# Event Management System - Backend Documentation

This document provides a detailed overview of the backend functionality and API endpoints for the Event Management System.

---

## Implemented Functionality

### Authentication and Authorization
- User registration and login with JWT-based authentication.
- Role-based access control (RBAC) for different user roles:
  - `attendee`
  - `organizer`
  - `venue_admin`

### User Management
- View user profile.
- Delete user account.
- Switch active role for a user.
- Add new roles to a user.

### Event Management
- Create events by organizers.
- View events created by the logged-in organizer.
- Prevent conflicting event bookings for the same venue and date.

### Venue Management
- Add venues by venue administrators.
- View venues managed by the logged-in venue administrator.

### Registration Management
- Register for events by attendees.
- Cancel event registrations.
- View events registered by the logged-in attendee.

---

## API Endpoints

### Authentication

#### POST /api/auth/register
- **Body**: `{ username, email, password, role }`
- **Access**: Public
- **Description**: Registers a new user and assigns the specified role (default: `attendee`).
- **Returns**: JWT token.

#### POST /api/auth/login
- **Body**: `{ email, password }`
- **Access**: Public
- **Description**: Logs in a user and returns a JWT token.
- **Returns**: JWT token.

---

### User Management

#### GET /api/users/me
- **Access**: Authenticated
- **Description**: Retrieves the profile of the logged-in user.
- **Returns**: User details.

#### DELETE /api/users/me
- **Access**: Authenticated
- **Description**: Deletes the account of the logged-in user.
- **Returns**: Success message.

#### POST /api/users/switch-role
- **Body**: `{ role }`
- **Access**: Authenticated
- **Description**: Switches the active role of the logged-in user.
- **Returns**: Updated JWT token.

#### POST /api/users/add-role
- **Body**: `{ role }`
- **Access**: Authenticated
- **Description**: Adds a new role to the logged-in user.
- **Returns**: Success message.

---

### Event Management

#### POST /api/events/create-event
- **Body**: `{ title, description, date, venueId }`
- **Access**: Authenticated (Role: `organizer`)
- **Description**: Creates a new event for the specified venue and date.
- **Returns**: Event ID and success message.

#### GET /api/events/my
- **Access**: Authenticated (Role: `organizer`)
- **Description**: Retrieves events created by the logged-in organizer.
- **Returns**: List of events.

---

### Venue Management

#### POST /api/venues/add-venue
- **Body**: `{ name, location, capacity }`
- **Access**: Authenticated (Role: `venue_admin`)
- **Description**: Adds a new venue managed by the logged-in venue administrator.
- **Returns**: Venue ID and success message.

#### GET /api/venues/my
- **Access**: Authenticated (Role: `venue_admin`)
- **Description**: Retrieves venues managed by the logged-in venue administrator.
- **Returns**: List of venues.

---

### Registration Management

#### POST /api/registerations/:eventId
- **Access**: Authenticated (Role: `attendee`)
- **Description**: Registers the logged-in attendee for the specified event.
- **Returns**: Success message.

#### DELETE /api/registerations/:eventId
- **Access**: Authenticated (Role: `attendee`)
- **Description**: Cancels the registration of the logged-in attendee for the specified event.
- **Returns**: Success message.

#### GET /api/registerations/my-events
- **Access**: Authenticated (Role: `attendee`)
- **Description**: Retrieves events registered by the logged-in attendee.
- **Returns**: List of events.

---

## Models

### User
- **Fields**: `id`, `username`, `email`, `password`, `active_role`
- **Methods**:
  - `create({ username, email, hashedPassword, activeRole })`
  - `getByEmail(email)`
  - `getByUsername(username)`
  - `getById(id)`
  - `updateActiveRole(userId, role)`
  - `delete(id)`

### UserRole
- **Fields**: `user_id`, `role`
- **Methods**:
  - `create(role, userId)`
  - `hasRole(userId, role)`

### Event
- **Fields**: `id`, `title`, `description`, `date`, `organizer_id`, `venue_id`
- **Methods**:
  - `create({ title, description, date, organizerId, venueId })`
  - `getConflicting(venueId, date)`
  - `getByOrganizer(organizerId)`

### Venue
- **Fields**: `id`, `name`, `location`, `capacity`, `admin_id`
- **Methods**:
  - `create({ name, location, capacity, adminId })`
  - `getByAdmin(adminId)`
  - `getById(id)`

### Registration
- **Fields**: `attendee_id`, `event_id`
- **Methods**:
  - `register(attendeeId, eventId)`
  - `cancel(attendeeId, eventId)`
  - `isRegistered(attendeeId, eventId)`
  - `getByAttendee(attendeeId)`

---

## Middleware

### Authentication Middleware
- **`authenticateUser`**: Verifies the JWT token and attaches the user details to the request object.
- **`authorizeRole(...allowedRoles)`**: Ensures the logged-in user has one of the specified roles.

---

## Configuration

### Environment Variables
- **`.env` File**:
  - `DB_HOST`: Database host.
  - `DB_USER`: Database username.
  - `DB_PASSWORD`: Database password.
  - `DB_NAME`: Database name.
  - `JWT_SECRET`: Secret key for JWT.
