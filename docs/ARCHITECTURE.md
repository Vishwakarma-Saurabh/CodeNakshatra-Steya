---

```markdown
# Steya Architecture

## 🏗️ System Overview
┌─────────────────────────────────────────────────────────────┐
│ Frontend (React) │
│ - Room Search with Map │
│ - Room Listing Form │
│ - Booking Interface │
│ - User Dashboard │
└─────────────────────┬───────────────────────────────────────┘
│ REST API
┌─────────────────────▼───────────────────────────────────────┐
│ Backend (FastAPI) │
│ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ │
│ │ Auth │ │ Rooms │ │ Bookings │ │ Users │ │
│ └──────────┘ └──────────┘ └──────────┘ └──────────┘ │
└─────────────────────┬───────────────────────────┬───────────┘
│ │
┌─────────────────────▼───────────┐ ┌─────────────▼───────────┐
│ PostgreSQL │ │ ML Service │
│ - Users, Rooms, Bookings │ │ - Route Matching │
│ - PostGIS for location │ │ - Room Recommendations │
└─────────────────────────────────┘ └─────────────────────────┘


## 📡 Data Flow

### Room Search Flow
User enters location in search bar

Frontend calls GET /rooms?lat=...&lng=...

Backend queries PostgreSQL with PostGIS

Returns nearby rooms sorted by distance

Frontend displays on map and cards


### Booking Flow
User selects room and time slot (flexible)

Frontend calls POST /bookings

Backend checks availability

Creates booking record

Returns confirmation to user


### Smart Matching Flow
User sets travel route (home → work)

Backend sends route to ML service

ML finds users with opposite routes

Calculates compatibility score

Returns potential matches


## 🔐 Authentication

- JWT tokens for authentication
- Token stored in localStorage
- Protected routes require valid token

## 📊 Tech Decisions

| Decision | Choice | Reason |
|----------|--------|--------|
| Backend | FastAPI | Fast, auto-docs, Python for ML integration |
| Database | PostgreSQL | PostGIS for location queries |
| ORM | SQLAlchemy | Python standard, flexible |
| Frontend | React | Component reusability |
| Maps | MapLibre GL | Free, OpenStreetMap support |
| ML | Python | Same ecosystem as backend |

---

**Version**: 1.0.0