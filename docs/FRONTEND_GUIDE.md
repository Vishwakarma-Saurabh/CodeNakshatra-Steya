# Frontend Development Guide

## 📁 Project Structure
frontend/
├── public/
│ └── index.html
├── src/
│ ├── components/
│ │ ├── Layout/
│ │ │ ├── Header.jsx
│ │ │ └── Footer.jsx
│ │ ├── Map/
│ │ │ └── SimpleMap.jsx
│ │ ├── Rooms/
│ │ │ ├── RoomCard.jsx
│ │ │ └── RoomList.jsx
│ │ └── Auth/
│ │ ├── LoginForm.jsx
│ │ └── RegisterForm.jsx
│ ├── pages/
│ │ ├── Home.jsx
│ │ ├── Search.jsx
│ │ ├── RoomDetail.jsx
│ │ ├── ListRoom.jsx
│ │ ├── Dashboard.jsx
│ │ └── Matches.jsx
│ ├── services/
│ │ └── api.js
│ ├── context/
│ │ └── AuthContext.jsx
│ ├── App.jsx
│ ├── index.js
│ └── index.css
├── package.json
├── tailwind.config.js
└── .env.local


## 🚀 Setup Instructions

### Step 1: Create React App
Navigate to project root and create the React application using Create React App or Vite.

### Step 2: Install Dependencies
Install the required npm packages:
- react-router-dom (for routing)
- axios (for API calls)
- leaflet and react-leaflet (for maps)
- tailwindcss (for styling)

### Step 3: Configure Tailwind CSS
Set up Tailwind by creating the configuration file and adding the directives to the main CSS file.

### Step 4: Environment Variables
Create a `.env.local` file with the backend API URL.

### Step 5: Run Development Server
Start the frontend development server on port 3000.

## 🧩 Core Components

### SimpleMap Component
**Purpose:** Displays an interactive map with room markers using Leaflet and OpenStreetMap.

**Location:** `src/components/Map/SimpleMap.jsx`

**Features:**
- Centers on user's current location
- Shows room markers with popups
- Updates view when location changes
- Handles marker click events

**Props:**
- `rooms`: Array of room objects with location data
- `userLocation`: Current user coordinates
- `onMarkerClick`: Callback function when marker is clicked
- `height`: Map container height (default: 500px)

### RoomCard Component
**Purpose:** Displays individual room information in a card format.

**Location:** `src/components/Rooms/RoomCard.jsx`

**Features:**
- Room image, title, and rating
- Price per hour display
- Distance from user
- Amenities list (first 3 shown)
- Book Now button

**Props:**
- `room`: Room object with all details
- `onClick`: Callback when card is clicked

### RoomList Component
**Purpose:** Renders a list of RoomCard components.

**Location:** `src/components/Rooms/RoomList.jsx`

**Features:**
- Displays multiple room cards in a grid/stack
- Shows loading state
- Handles empty state

## 📄 Pages

### Home Page
**Route:** `/`

**Purpose:** Landing page with hero section and feature highlights.

**Sections:**
- Hero with call-to-action buttons
- Feature cards (Find Rooms, List Space, Smart Matching)
- How It Works steps

### Search Page
**Route:** `/search`

**Purpose:** Main page for discovering nearby rooms.

**Layout:** Split screen - map on right, room list on left.

**Features:**
- Geolocation detection
- Price and radius filters
- Real-time room updates
- Click room card or marker to view details

### Room Detail Page
**Route:** `/rooms/:id`

**Purpose:** Detailed view of a single room.

**Information Displayed:**
- All room images
- Full description
- Amenities list
- Host information
- Availability calendar
- Booking form

### List Room Page
**Route:** `/list-room` (Protected)

**Purpose:** Form for hosts to create new room listings.

**Form Fields:**
- Title and description
- Price per hour
- Maximum hours allowed
- Address with map picker
- Amenities selection
- Image upload

### Dashboard Page
**Route:** `/dashboard` (Protected)

**Purpose:** User's personal dashboard.

**Tabs/Sections:**
- My Bookings (upcoming and past)
- My Listings (for hosts)
- Profile settings
- Earnings summary (for hosts)

### Matches Page
**Route:** `/matches` (Protected)

**Purpose:** View and manage route matches.

**Features:**
- Set travel route
- View compatible opposite commuters
- Match compatibility score
- Contact matches

## 🔌 API Integration

### API Service Structure
**Location:** `src/services/api.js`

**Exported Modules:**
- `authAPI`: Register, login
- `roomsAPI`: List, get one, create, delete
- `bookingsAPI`: Create, get all, cancel
- `matchesAPI`: Find matches, create route, get routes

### Authentication Flow
- Token stored in localStorage after login/register
- Token automatically added to all authenticated requests via axios interceptor
- Protected routes check for valid token
- Redirect to login if unauthenticated

## 🎨 Styling Guidelines

### Tailwind CSS Classes
- Use utility classes for all styling
- Custom colors defined in `tailwind.config.js`
- Responsive design using breakpoint prefixes (`md:`, `lg:`)

### Custom CSS Classes
- `.room-card`: Card hover effects and transitions
- `.btn-primary`: Primary button styling
- `.btn-secondary`: Secondary button styling

### Color Scheme
- Primary: Blue (`#4A90E2`) - Trust and reliability
- Secondary: Orange (`#FF6B35`) - Energy and action
- Accent: Green (`#50E3C2`) - Success and sustainability

## 🔐 Authentication Context

**Location:** `src/context/AuthContext.jsx`

**Provides:**
- `user`: Current user object
- `login()`: Login function
- `register()`: Register function
- `logout()`: Logout function
- `loading`: Auth loading state

**Usage:** Wrap the app with `AuthProvider` and use `useAuth()` hook in components.

## 📱 Responsive Design

### Breakpoints
- Mobile: < 640px (stacked layout)
- Tablet: 640px - 1024px
- Desktop: > 1024px (side-by-side layout)

### Mobile Considerations
- Map and list stack vertically
- Simplified filters
- Larger touch targets
- Bottom navigation bar

## 🧪 Testing

### Running Tests
Use the test script defined in package.json to run Jest tests.

### Test Coverage
- Component rendering tests
- API service mock tests
- Authentication flow tests
- Form validation tests

## 📦 Build for Production

### Build Command
Run the build script to create an optimized production build in the `build/` folder.

### Deployment Options
- Vercel (recommended for React apps)
- Netlify
- AWS S3 + CloudFront

### Environment Variables for Production
Set the production API URL in the deployment platform's environment variables.

## 🐛 Common Issues & Solutions

### Map Not Displaying
- Check Leaflet CSS import
- Verify container has explicit height
- Ensure MapContainer is imported correctly

### CORS Errors
- Backend CORS origins must include frontend URL
- Check API base URL in environment variables

### Authentication Issues
- Token may be expired - clear localStorage
- Check token format in Authorization header

### Images Not Loading
- Use placeholder images during development
- Ensure image URLs are accessible

## 📚 Additional Resources

- React Documentation: https://react.dev
- Leaflet Documentation: https://leafletjs.com
- Tailwind CSS Documentation: https://tailwindcss.com
- React Router Documentation: https://reactrouter.com
