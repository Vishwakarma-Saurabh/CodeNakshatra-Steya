# Steya API Reference

## 🌐 Base URL
http://localhost:8000/api


## 🔐 Authentication

### Register

POST /auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123",
  "name": "John Doe",
  "phone": "+1234567890"
}

Response 201:
{
  "id": "usr_123",
  "email": "user@example.com",
  "name": "John Doe",
  "token": "eyJhbGciOiJIUzI1NiIs..."
}

Login
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}

Response 200:
{
  "id": "usr_123",
  "email": "user@example.com",
  "name": "John Doe",
  "token": "eyJhbGciOiJIUzI1NiIs..."
}

🏠 Room Endpoints
List Rooms (with location)

GET /rooms?lat=40.7128&lng=-74.0060&radius=5&duration=short

Query Parameters:
- lat, lng: Required - Search location
- radius: Search radius in km (default: 5)
- duration: "short" (4-8h) or "daily"
- maxPrice: Maximum price

Response 200:
{
  "rooms": [
    {
      "id": "rm_456",
      "title": "Cozy room near downtown",
      "price": 25.00,
      "priceType": "hourly",
      "location": {
        "lat": 40.7128,
        "lng": -74.0060,
        "address": "123 Main St"
      },
      "distance": 0.5,
      "hostName": "Jane Smith",
      "rating": 4.5,
      "images": ["url1", "url2"],
      "amenities": ["wifi", "ac"]
    }
  ]
}

Get Room Details
GET /rooms/{room_id}

Response 200:
{
  "id": "rm_456",
  "title": "Cozy room near downtown",
  "description": "Perfect for short rest",
  "price": 25.00,
  "priceType": "hourly",
  "maxHours": 8,
  "location": {
    "lat": 40.7128,
    "lng": -74.0060,
    "address": "123 Main St"
  },
  "host": {
    "id": "usr_789",
    "name": "Jane Smith",
    "rating": 4.5
  },
  "amenities": ["wifi", "ac", "workspace"],
  "images": ["url1", "url2"],
  "availability": [
    {
      "date": "2024-01-20",
      "slots": [
        {"start": "09:00", "end": "13:00", "available": true},
        {"start": "13:00", "end": "17:00", "available": true}
      ]
    }
  ]
}

Create Room Listing
POST /rooms
Authorization: Bearer <token>
Content-Type: application/json

{
  "title": "Spare bedroom in apartment",
  "description": "Quiet room for short stays",
  "price": 20.00,
  "priceType": "hourly",
  "maxHours": 8,
  "address": "456 Oak Ave, City",
  "latitude": 40.7128,
  "longitude": -74.0060,
  "amenities": ["wifi", "ac"],
  "images": ["url1", "url2"]
}

Response 201:
{
  "id": "rm_457",
  "message": "Room listed successfully"
}

📅 Booking Endpoints
Create Booking
POST /bookings
Authorization: Bearer <token>
Content-Type: application/json

{
  "roomId": "rm_456",
  "date": "2024-01-20",
  "startTime": "14:00",
  "endTime": "18:00",
  "totalPrice": 100.00
}

Response 201:
{
  "id": "bk_123",
  "roomId": "rm_456",
  "status": "confirmed",
  "date": "2024-01-20",
  "startTime": "14:00",
  "endTime": "18:00",
  "totalPrice": 100.00
}

Get User Bookings
GET /bookings
Authorization: Bearer <token>

Response 200:
{
  "bookings": [
    {
      "id": "bk_123",
      "room": {
        "id": "rm_456",
        "title": "Cozy room",
        "address": "123 Main St"
      },
      "date": "2024-01-20",
      "startTime": "14:00",
      "endTime": "18:00",
      "status": "confirmed"
    }
  ]
}

🤝 Matching Endpoints
Get Route Matches
POST /matches/routes
Authorization: Bearer <token>
Content-Type: application/json

{
  "startLocation": {
    "lat": 40.7128,
    "lng": -74.0060
  },
  "endLocation": {
    "lat": 40.7580,
    "lng": -73.9855
  },
  "travelTime": "09:00"
}

Response 200:
{
  "matches": [
    {
      "userId": "usr_456",
      "name": "Alice Johnson",
      "compatibility": 0.85,
      "route": {
        "start": "Manhattan",
        "end": "Brooklyn"
      },
      "message": "Opposite commute route match"
    }
  ]
}

Get Room Recommendations
GET /recommendations/rooms?lat=40.7128&lng=-74.0060
Authorization: Bearer <token>

Response 200:
{
  "recommendations": [
    {
      "roomId": "rm_456",
      "title": "Cozy room",
      "score": 0.92,
      "reason": "Near your frequent route"
    }
  ]
}

📝 Error Responses
{
  "error": "ERROR_CODE",
  "message": "Human readable message"
}

Common Status Codes:
400: Bad Request
401: Unauthorized
404: Not Found
500: Server Error
