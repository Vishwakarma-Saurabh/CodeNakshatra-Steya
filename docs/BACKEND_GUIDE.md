# Backend Development Guide

## 📁 Project Structure
backend/
├── app/
│ ├── init.py
│ ├── main.py
│ ├── config.py
│ ├── database.py
│ │
│ ├── models/
│ │ ├── init.py
│ │ ├── user.py
│ │ ├── room.py
│ │ ├── booking.py
│ │ └── route.py
│ │
│ ├── schemas/
│ │ ├── init.py
│ │ ├── user.py
│ │ ├── room.py
│ │ └── booking.py
│ │
│ ├── api/
│ │ ├── init.py
│ │ ├── auth.py
│ │ ├── rooms.py
│ │ ├── bookings.py
│ │ └── matches.py
│ │
│ ├── services/
│ │ ├── init.py
│ │ ├── auth_service.py
│ │ ├── room_service.py
│ │ └── ml_service.py
│ │
│ └── utils/
│ ├── init.py
│ └── helpers.py
│
├── requirements.txt
└── .env


## 🚀 Setup Instructions

### Step 1: Create Virtual Environment
Navigate to the backend directory and create a Python virtual environment.

### Step 2: Activate Virtual Environment
- On macOS/Linux: `source venv/bin/activate`
- On Windows: `venv\Scripts\activate`

### Step 3: Install Dependencies
Install all required packages from requirements.txt using pip.

### Step 4: Set Up PostgreSQL Database
- Install PostgreSQL if not already installed
- Create a new database named `staya`
- Update DATABASE_URL in `.env` file if needed

### Step 5: Environment Variables
Create a `.env` file with:
- DATABASE_URL: PostgreSQL connection string
- SECRET_KEY: JWT signing secret

### Step 6: Run the Server
Start the FastAPI development server with auto-reload enabled. The API will be available at `http://localhost:8000`.

## 🏗️ Layer Architecture

### Models Layer (`app/models/`)
**Purpose:** Database table definitions using SQLAlchemy ORM.

**Files:**
- `user.py`: User model with authentication fields
- `room.py`: Room listing model with location coordinates
- `booking.py`: Booking model for reservations
- `route.py`: Travel route model for matching

**Responsibilities:**
- Define table schemas
- Set column types and constraints
- Define relationships between tables

### Schemas Layer (`app/schemas/`)
**Purpose:** Pydantic models for request/response validation.

**Files:**
- `user.py`: Register, login, response schemas
- `room.py`: Create, update, response schemas
- `booking.py`: Create, response schemas

**Responsibilities:**
- Validate incoming request data
- Serialize outgoing response data
- Define data types and constraints

### API Layer (`app/api/`)
**Purpose:** Route handlers (controllers) for each resource.

**Files:**
- `auth.py`: Registration and login endpoints
- `rooms.py`: CRUD operations for rooms
- `bookings.py`: Booking creation and management
- `matches.py`: Route matching endpoints

**Responsibilities:**
- Handle HTTP requests
- Call appropriate services
- Return HTTP responses

### Services Layer (`app/services/`)
**Purpose:** Business logic and external integrations.

**Files:**
- `auth_service.py`: Password hashing and JWT token management
- `room_service.py`: Room search and filtering logic
- `ml_service.py`: Distance calculation and route matching algorithms

**Responsibilities:**
- Implement business rules
- Handle complex operations
- Keep API layer thin and clean

### Utils Layer (`app/utils/`)
**Purpose:** Helper functions and shared utilities.

**Files:**
- `helpers.py`: Authentication dependency, common functions

**Responsibilities:**
- Provide reusable utility functions
- Handle cross-cutting concerns

## 🔐 Authentication

### JWT Token Flow
1. User registers or logs in
2. Server validates credentials
3. Server generates JWT token with user ID
4. Client stores token and sends in Authorization header
5. Server validates token on protected routes

### Password Security
- Passwords hashed using bcrypt algorithm
- Never store plain text passwords
- Salt rounds: 12 (default)

### Protected Routes
Routes that require authentication use the `get_current_user` dependency which:
- Extracts token from Authorization header
- Validates token signature and expiration
- Returns the authenticated user ID

## 📡 API Endpoints

### Authentication Endpoints
| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/auth/register` | Create new user account |
| POST | `/api/auth/login` | Authenticate and get token |

### Room Endpoints
| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/rooms` | List nearby rooms |
| GET | `/api/rooms/{id}` | Get room details |
| POST | `/api/rooms` | Create new listing |
| DELETE | `/api/rooms/{id}` | Delete listing |

### Booking Endpoints
| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/bookings` | Get user's bookings |
| POST | `/api/bookings` | Create new booking |
| POST | `/api/bookings/{id}/cancel` | Cancel booking |

### Matching Endpoints
| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/matches/routes` | Find opposite route matches |
| POST | `/api/matches/routes/create` | Save travel route |
| GET | `/api/matches/routes/my` | Get user's routes |

## 🗄️ Database

### Connection Management
- SQLAlchemy engine configured with connection pooling
- Session per request pattern using dependency injection
- Sessions automatically closed after request

### ID Generation
- Custom ID generator creates prefixed IDs (e.g., `usr_abc123`)
- Prefixes: `usr_` (users), `rm_` (rooms), `bk_` (bookings), `rt_` (routes)

### Query Examples
- Room search uses bounding box for performance
- Distance calculation performed in Python (not PostGIS)
- Joins used for related data fetching

## 🧮 ML Algorithms

### Distance Calculation
- Uses Haversine formula for accurate geographic distance
- Returns distance in kilometers
- Threshold-based filtering for performance

### Route Matching Logic
1. Calculate distance between User A's start and User B's end
2. Calculate distance between User A's end and User B's start
3. If both distances are below threshold, consider it a match
4. Calculate compatibility score based on distance overlap
5. Return top 5 matches sorted by compatibility

## 🧪 Testing

### Running Tests
Use pytest to run the test suite from the backend directory.

### Test Categories
- Unit tests for services
- Integration tests for API endpoints
- Database transaction tests

### Test Database
Use a separate test database to avoid affecting development data.

## 📦 Deployment

### Production Considerations
- Use production database (PostgreSQL on RDS or similar)
- Set strong SECRET_KEY
- Enable HTTPS
- Use proper CORS origins (not wildcard)
- Consider using Gunicorn with Uvicorn workers

### Environment Variables for Production
- DATABASE_URL: Production database connection
- SECRET_KEY: Strong random string
- CORS_ORIGINS: Frontend production URL

## 🐛 Common Issues & Solutions

### Database Connection Error
- Verify PostgreSQL is running
- Check DATABASE_URL format
- Ensure database exists

### Import Errors
- Verify virtual environment is activated
- Check all dependencies are installed
- Ensure PYTHONPATH is correct

### JWT Token Expired
- Token expires after 7 days by default
- User must re-authenticate

### CORS Errors
- Add frontend URL to CORS_ORIGINS in config
- Check request includes proper Origin header

## 📚 API Documentation

### Interactive Docs
FastAPI provides automatic interactive documentation:
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

### OpenAPI Schema
The OpenAPI schema is available at `http://localhost:8000/openapi.json`.
