# ML Matching System Documentation

## 🎯 Overview

The ML Matching System is responsible for two core intelligent features:

1. **Route Matching**: Finding users with opposite commute routes for mutual stay sharing
2. **Room Recommendations**: Suggesting optimal rooms based on user location and preferences

This system is integrated directly into the backend (no separate service) for hackathon simplicity.

## 📁 Structure
backend/app/services/
├── ml_service.py # Core ML algorithms
└── room_service.py # Room recommendation logic


## 🧮 Distance Calculation Algorithm

### Haversine Formula

The system uses the **Haversine formula** to calculate the great-circle distance between two points on Earth's surface.

**Formula Components:**
- Earth's radius: 6,371 kilometers
- Latitude and longitude in radians
- Difference calculations for lat/lng deltas

**Accuracy:** Accurate to within 0.5% for most distances

**Use Cases:**
- Calculating distance from user to each room
- Determining proximity between route start/end points
- Filtering rooms within search radius

### Distance Thresholds

| Use Case | Threshold | Purpose |
|----------|-----------|---------|
| Room Search | 5 km (default) | Find nearby rooms |
| Route Matching | 2 km | Consider locations as "matching" |
| Recommendation | 10 km | Maximum distance for suggestions |

---

## 🤝 Route Matching Algorithm

### Core Concept

The algorithm identifies users traveling in opposite directions who could benefit from sharing stays.

**Example:**
- **User A**: Lives in Brooklyn, works in Manhattan
- **User B**: Lives in Manhattan, works in Brooklyn

These users have opposite commutes and could potentially share rooms near their respective destinations.

### Matching Logic Flow

1. **Input**: User provides start location (home) and end location (work/school)

2. **Data Retrieval**: System fetches all active travel routes from database except user's own

3. **Distance Calculations**:
   - Distance between User A's start and User B's end
   - Distance between User A's end and User B's start

4. **Compatibility Scoring**:
   - Perfect match: Both distances < 2 km → Score 1.0
   - Partial match: One distance < 2 km → Score 0.5
   - No match: Both distances > 5 km → Score 0.0

5. **Ranking**: Sort matches by compatibility score descending

6. **Output**: Return top 5 matches with scores and route information

### Compatibility Score Formula

The compatibility score is calculated as:

**Score = (Start-End Match Score + End-Start Match Score) / 2**

Where each match score is:
- 1.0 if distance ≤ 2 km
- Linear decrease from 1.0 to 0.0 for distances 2-5 km
- 0.0 if distance > 5 km

### Matching Thresholds

| Threshold | Value | Description |
|-----------|-------|-------------|
| Perfect Match | ≤ 2 km | Locations considered identical |
| Partial Match | 2-5 km | Some overlap, lower score |
| No Match | > 5 km | Too far apart |

---

## 🏠 Room Recommendation Algorithm

### Core Concept

Recommends rooms based on proximity to user's location and preference alignment.

### Scoring Factors

| Factor | Weight | Description |
|--------|--------|-------------|
| Distance | 60% | Proximity to user's current location |
| Price | 40% | Alignment with user's budget |

### Distance Scoring

The distance score is calculated as:

**Distance Score = max(0, 1 - (distance / max_distance))**

- Maximum distance: 10 km
- Closer rooms get higher scores
- Rooms beyond 10 km are excluded

### Price Scoring

The price score is calculated as:

**If price ≤ max_price:**
- Score = 1.0

**If price > max_price:**
- Score = max(0, 1 - ((price - max_price) / 50))

This allows some flexibility above the budget while heavily penalizing significantly more expensive rooms.

### Combined Score

**Final Score = (Distance Score × 0.6) + (Price Score × 0.4)**

### Recommendation Flow

1. **Input**: User location and optional preferences (max price, amenities)

2. **Filtering**:
   - Exclude rooms > 10 km away
   - Exclude inactive rooms

3. **Scoring**: Calculate individual and combined scores for each room

4. **Threshold**: Keep only rooms with score > 0.4

5. **Sorting**: Order by combined score descending

6. **Output**: Return top 10 recommendations with scores and reasons

---

## 📊 Data Structures

### Route Input Format

| Field | Type | Description |
|-------|------|-------------|
| user_id | String | Unique user identifier |
| start_lat | Float | Start location latitude |
| start_lng | Float | Start location longitude |
| end_lat | Float | End location latitude |
| end_lng | Float | End location longitude |

### Match Output Format

| Field | Type | Description |
|-------|------|-------------|
| user_id | String | Matched user's ID |
| user_name | String | Matched user's name |
| compatibility | Float | Match score (0.0 - 1.0) |
| route_name | String | Matched user's route name |
| start_address | String | Matched user's start location |
| end_address | String | Matched user's end location |

### Recommendation Output Format

| Field | Type | Description |
|-------|------|-------------|
| room_id | String | Room identifier |
| title | String | Room title |
| distance | Float | Distance in kilometers |
| price | Float | Price per hour |
| score | Float | Recommendation score (0.0 - 1.0) |
| reasons | Array | List of matching reasons |

---

## 🔄 Integration Points

### Backend Integration

The ML algorithms are called directly from API route handlers:

- **Route Matching**: Called from `api/matches.py` → `find_matches()` endpoint
- **Room Recommendations**: Called from `api/rooms.py` → `list_rooms()` endpoint

### No External Dependencies

All calculations are performed using Python's built-in `math` module. No external ML libraries required for hackathon simplicity.

### Caching Strategy (Future)

For production scale:
- Cache user routes (update daily)
- Pre-calculate pairwise distances for active routes
- Use Redis for match result caching

---

## 🧪 Testing Strategy

### Unit Tests

| Test Case | Input | Expected Output |
|-----------|-------|-----------------|
| Same location | Distance = 0 | Score = 1.0 |
| Opposite routes | Perfect opposite | Score > 0.9 |
| Unrelated routes | Far apart | Score = 0.0 |
| Boundary distance | Exactly 2 km | Score = 1.0 |
| Over budget room | Price > max_price | Lower score |

### Test Data

Use fixed coordinates for predictable testing:
- New York City coordinates
- London coordinates
- Known distance pairs (e.g., NYC to Boston ≈ 306 km)

### Performance Testing

| Metric | Target | Measurement |
|--------|--------|-------------|
| Distance calc | < 1 ms | Single calculation |
| 1000 rooms search | < 100 ms | Full search |
| 100 route matches | < 50 ms | Full matching |

---

## 📈 Accuracy Metrics

### Distance Accuracy

| Distance | Haversine Error | Acceptable? |
|----------|-----------------|-------------|
| < 10 km | < 0.1% | ✅ Yes |
| 10-100 km | < 0.5% | ✅ Yes |
| > 100 km | < 1% | ✅ Yes |

### Matching Precision

| Scenario | Expected Precision |
|----------|-------------------|
| Same neighborhood | > 90% match rate |
| Same city | > 70% match rate |
| Different cities | < 10% match rate |

---

## 🚀 Future Enhancements

### Phase 1 (Post-Hackathon)

- Add time-based matching (similar departure times)
- Include amenity preferences in recommendations
- Add user rating factor to scores

### Phase 2

- Machine learning model for personalized recommendations
- Reinforcement learning from user bookings
- Real-time traffic data integration

### Phase 3

- Predictive demand modeling
- Dynamic pricing suggestions
- Route clustering for group matches

---

## 🐛 Common Issues & Solutions

### Issue: No Matches Found

**Possible Causes:**
- User hasn't saved a route
- No other users with opposite routes in database
- Threshold too strict

**Solutions:**
- Prompt user to save route first
- Increase search radius threshold
- Seed database with sample routes

### Issue: Slow Performance with Many Rooms

**Solutions:**
- Use bounding box pre-filtering
- Limit initial query to 100 rooms
- Cache distance calculations

### Issue: Inaccurate Distances

**Note:** Haversine assumes spherical Earth, which is accurate enough for city-scale distances.

---

## 📚 References

### Algorithms
- Haversine Formula: Standard navigation formula for great-circle distances
- Weighted Scoring: Multi-criteria decision making

### Coordinate System
- WGS84 (World Geodetic System 1984)
- Standard for GPS coordinates
- Used by Google Maps, OpenStreetMap

### Units
- All distances in kilometers
- All coordinates in decimal degrees
- Time in 24-hour format

---

**Last Updated:** [Current Date]
**Version:** 1.0.0
**Status:** ✅ Hackathon Ready
