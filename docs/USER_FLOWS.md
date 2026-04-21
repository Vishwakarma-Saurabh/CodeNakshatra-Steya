---

# Steya User Flows

## 1️⃣ User Registration & Login
User visits site
↓
Clicks "Sign Up"
↓
Fills form (email, password, name, phone)
↓
Receives JWT token
↓
Redirected to Dashboard


## 2️⃣ Finding a Room
User opens app
↓
Allows location access
↓
Sees nearby rooms on map/list
↓
Filters by price/distance
↓
Clicks on room card
↓
Views room details (photos, amenities, host info)


## 3️⃣ Booking a Room 
User selects room
↓
Chooses date
↓
Selects time slot (4-8 hours)
↓
Sees total price
↓
Clicks "Book Now"
↓
Receives confirmation
↓
Gets host contact/location details


## 4️⃣ Listing a Room (Host)
User logs in
↓
Clicks "List Your Space"
↓
Fills room details:

Title, description

Price per hour

Address (auto-detected on map)

Amenities (wifi, ac, workspace)

Photos (upload)
↓
Submits listing
↓
Room appears in search


## 5️⃣ Smart Route Matching
User sets commute route:

Home location

Work/Study location

Typical travel time
↓
System finds users with opposite routes
↓
Shows potential matches
↓
User can connect with matches
↓
Coordinate shared stays


## 6️⃣ Booking Management
User opens Dashboard
↓
Views "My Bookings" tab
↓
Sees upcoming/completed bookings
↓
Can:

View booking details

Contact host/guest

Cancel booking (if allowed)

Leave review after stay


## 📱 Mobile Considerations

- Location permission handling
- Touch-friendly UI (large buttons)
- Simplified booking flow
- Push notifications for booking updates

## 🔄 Data Sync Flow
Frontend Action → API Call → Backend → Database
↓
Response → UI Update


## 🧪 Testing User Flows

1. Register new user
2. List a test room
3. Search for room from different account
4. Book the room
5. Verify booking appears in both dashboards
6. Test route matching with sample data
