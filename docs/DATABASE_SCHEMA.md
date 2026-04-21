# Database Schema Documentation

## 📊 Entity Relationship Diagram
┌─────────────┐ ┌─────────────┐ ┌─────────────┐
│ users │────<│ rooms │────<│ bookings │
└─────────────┘ └─────────────┘ └─────────────┘
│ │ │
│ │ │
▼ ▼ ▼
┌─────────────┐ ┌─────────────┐ ┌─────────────┐
│ routes │ │ reviews │────>│ reviews │
└─────────────┘ └─────────────┘ └─────────────┘


## 📋 Tables

### Users Table
**Table Name:** `users`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | VARCHAR(50) | PRIMARY KEY | User ID prefixed with 'usr_' |
| email | VARCHAR(255) | UNIQUE, NOT NULL | User's email address |
| password_hash | VARCHAR(255) | NOT NULL | Bcrypt hashed password |
| name | VARCHAR(100) | NOT NULL | User's full name |
| phone | VARCHAR(20) | NULL | Contact phone number |
| user_type | VARCHAR(20) | DEFAULT 'traveler' | traveler, host, or both |
| rating | DECIMAL(3,2) | DEFAULT 0.00 | Average rating from reviews |
| created_at | TIMESTAMP | DEFAULT NOW() | Account creation timestamp |

**Indexes:**
- `idx_users_email` on `email` for fast login lookups

---

### Rooms Table
**Table Name:** `rooms`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | VARCHAR(50) | PRIMARY KEY | Room ID prefixed with 'rm_' |
| host_id | VARCHAR(50) | NOT NULL | Foreign key to users.id |
| title | VARCHAR(200) | NOT NULL | Room listing title |
| description | TEXT | NULL | Detailed room description |
| price | DECIMAL(10,2) | NOT NULL | Price per hour/day |
| price_type | VARCHAR(20) | DEFAULT 'hourly' | hourly or daily |
| max_hours | INTEGER | DEFAULT 8 | Maximum booking hours |
| address | TEXT | NOT NULL | Full street address |
| latitude | DECIMAL(10,8) | NOT NULL | Geographic latitude |
| longitude | DECIMAL(11,8) | NOT NULL | Geographic longitude |
| amenities | TEXT[] | DEFAULT '{}' | Array of amenity names |
| images | TEXT[] | DEFAULT '{}' | Array of image URLs |
| is_active | BOOLEAN | DEFAULT true | Listing visibility |
| rating | DECIMAL(3,2) | DEFAULT 0.00 | Average room rating |
| created_at | TIMESTAMP | DEFAULT NOW() | Listing creation timestamp |

**Indexes:**
- `idx_rooms_host` on `host_id`
- `idx_rooms_active` on `is_active`
- `idx_rooms_location` on `(latitude, longitude)` for bounding box queries

---

### Bookings Table
**Table Name:** `bookings`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | VARCHAR(50) | PRIMARY KEY | Booking ID prefixed with 'bk_' |
| user_id | VARCHAR(50) | NOT NULL | Foreign key to users.id (guest) |
| room_id | VARCHAR(50) | NOT NULL | Foreign key to rooms.id |
| booking_date | DATE | NOT NULL | Date of stay |
| start_time | TIME | NOT NULL | Check-in time |
| end_time | TIME | NOT NULL | Check-out time |
| total_price | DECIMAL(10,2) | NOT NULL | Total booking cost |
| status | VARCHAR(20) | DEFAULT 'confirmed' | pending, confirmed, cancelled |
| special_requests | TEXT | NULL | Guest's special requests |
| created_at | TIMESTAMP | DEFAULT NOW() | Booking creation timestamp |
| cancelled_at | TIMESTAMP | NULL | Cancellation timestamp |

**Indexes:**
- `idx_bookings_user` on `user_id`
- `idx_bookings_room` on `room_id`
- `idx_bookings_date` on `booking_date`
- `idx_bookings_status` on `status`

---

### Travel Routes Table
**Table Name:** `travel_routes`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | VARCHAR(50) | PRIMARY KEY | Route ID prefixed with 'rt_' |
| user_id | VARCHAR(50) | NOT NULL | Foreign key to users.id |
| route_name | VARCHAR(100) | NULL | Custom route name |
| start_address | TEXT | NULL | Starting location address |
| start_latitude | DECIMAL(10,8) | NOT NULL | Start point latitude |
| start_longitude | DECIMAL(11,8) | NOT NULL | Start point longitude |
| end_address | TEXT | NULL | Destination address |
| end_latitude | DECIMAL(10,8) | NOT NULL | End point latitude |
| end_longitude | DECIMAL(11,8) | NOT NULL | End point longitude |
| typical_time | TIME | NULL | Usual departure time |
| is_active | BOOLEAN | DEFAULT true | Route active status |
| created_at | TIMESTAMP | DEFAULT NOW() | Route creation timestamp |

**Indexes:**
- `idx_routes_user` on `user_id`
- `idx_routes_active` on `is_active`

---

### Reviews Table (Future)
**Table Name:** `reviews`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | VARCHAR(50) | PRIMARY KEY | Review ID prefixed with 'rev_' |
| booking_id | VARCHAR(50) | UNIQUE, NOT NULL | Foreign key to bookings.id |
| reviewer_id | VARCHAR(50) | NOT NULL | User writing review |
| reviewee_id | VARCHAR(50) | NOT NULL | User being reviewed |
| room_id | VARCHAR(50) | NOT NULL | Room being reviewed |
| rating | INTEGER | NOT NULL, 1-5 | Rating score |
| comment | TEXT | NULL | Review text |
| created_at | TIMESTAMP | DEFAULT NOW() | Review creation timestamp |

**Constraints:**
- `check_rating_range`: rating BETWEEN 1 AND 5

---

## 🔗 Relationships

### One-to-Many Relationships
- **User → Rooms:** One user (host) can have many rooms
- **User → Bookings:** One user (guest) can have many bookings
- **Room → Bookings:** One room can have many bookings
- **User → Routes:** One user can have many travel routes

### One-to-One Relationships
- **Booking → Review:** One booking can have one review

---

## 📥 Sample Data

### Sample User
| id | email | name | user_type |
|----|-------|------|-----------|
| usr_demo123 | john@example.com | John Doe | traveler |

### Sample Room
| id | host_id | title | price | latitude | longitude |
|----|---------|-------|-------|----------|-----------|
| rm_demo456 | usr_host1 | Cozy downtown room | 25.00 | 40.7128 | -74.0060 |

### Sample Booking
| id | user_id | room_id | booking_date | start_time | end_time |
|----|---------|---------|--------------|------------|----------|
| bk_demo789 | usr_guest1 | rm_demo456 | 2024-01-20 | 14:00 | 18:00 |

### Sample Route
| id | user_id | start_latitude | start_longitude | end_latitude | end_longitude |
|----|---------|----------------|-----------------|--------------|---------------|
| rt_demo321 | usr_demo123 | 40.6782 | -73.9442 | 40.7831 | -73.9712 |

---

## 🔍 Common Queries

### Find Nearby Rooms
Query all active rooms and calculate distance in application layer. Filter by approximate bounding box for performance.

### Get User's Upcoming Bookings
Join bookings with rooms table, filter by user_id and date >= today, order by date ascending.

### Find Potential Route Matches
Query all active travel routes except user's own. Calculate distances between start/end points in application layer.

### Calculate Host Rating
Average all review ratings for rooms owned by the host.

---

## 🗄️ Database Setup Commands

### Create Database
CREATE DATABASE steya;

Connect to Database
\c steya

Create Tables
Run the table creation scripts in order: users, rooms, bookings, travel_routes.

Verify Tables
\dt

📊 Performance Considerations:-
Indexing Strategy
Index foreign key columns for fast joins
Index frequently filtered columns (status, is_active)
Composite index on latitude/longitude for location queries

Query Optimization:-
Use bounding box for initial location filtering
Limit results with pagination
Select only needed columns
Scaling Considerations
Consider read replicas for high traffic
Archive old bookings (older than 1 year)
Use connection pooling for database connections