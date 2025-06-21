# 🛫 Optimization of Airmail Delivery Routes Using Swarm Intelligence

[![Website](https://img.shields.io/badge/Website-LIVE-green?logo=github)](https://justkesha.github.io/postal-route-optimization)
[![Languages: RU](https://img.shields.io/badge/Languages-RU-red.svg)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

<img alt="Banner" width="100%" src="preview.png" />

This project is a simple [web application](https://justkesha.github.io/postal-route-optimization/) for building optimal airmail delivery routes using swarm intelligence algorithms.

## Contents
1. [Mathematical Model](#mathematical-model)
2. [Optimization Algorithms](#optimization-algorithms)
3. [Distance Calculation](#distance-calculation)
4. [Interface](#interface)
5. [Project Launch](#project-launch)
6. [Adding New Cities](#adding-new-cities)

## Mathematical Model

### Formal Problem Statement
The problem is a modified version of the Capacitated Vehicle Routing Problem (CVRP) with additional constraints:

1. **Input Parameters**:
   - `G = (V, E)` - graph of cities (vertices) and air routes (edges)
   - `cᵢⱼ` - cost of flight from city `i` to city `j` (distance in km)
   - `dᵢ` - weight of shipments for city `i` (kg)
   - `Q` - payload capacity of one aircraft (kg)
   - `v₀` - starting city (hub)

2. **Objective Function**:
   Minimize the total delivery cost:

   ```min Σ c(Rₖ) for all flights k```

    where `c(Rₖ)` is the cost of flight `k`, calculated as the sum of distances between cities in route `Rₖ`

3. **Constraints**:
- `Σ dᵢ ≤ Q` for each flight (total weight in one flight does not exceed payload capacity)
- Each destination city is visited exactly once
- All flights start and end at the starting city

## Optimization Algorithms

### 1. Particle Swarm Optimization (PSO)

#### Algorithm Parameters:
- Swarm size: 50 particles
- Number of iterations: 100
- Inertia (`ω`): 0.7
- Cognitive parameter (`c₁`): 1.5
- Social parameter (`c₂`): 1.5

#### Update Formulas:
1. Velocity update:

    ```vᵢᵗ⁺¹ = ω·vᵢᵗ + c₁·r₁·(pbestᵢ - xᵢᵗ) + c₂·r₂·(gbest - xᵢᵗ)```

2. Position update:

    ```xᵢᵗ⁺¹ = xᵢᵗ + vᵢᵗ⁺¹```


#### Implementation Features:
- Each particle represents a possible route
- The position update operator is implemented as a route mutation operator
- The fitness function considers:
- Total route distance
- Number of required flights (with a penalty coefficient of 500 km for each additional flight)

### 2. Greedy Algorithm (for comparison)
1. Sort cities by distance from the starting city
2. Sequentially add the nearest cities to the route, considering payload capacity

## Distance Calculation

Distances between cities are calculated using the haversine formula for a spherical Earth:

    a = sin²(Δφ/2) + cos φ₁ · cos φ₂ · sin²(Δλ/2)
    c = 2 · atan2(√a, √(1−a))
    d = R · c


where:
- `φ` - latitude in radians
- `λ` - longitude in radians
- `R` - Earth's radius (6371 km)

Example calculation for Moscow → Saint Petersburg:

    φ₁ = 55.7558° = 0.9731 rad
    φ₂ = 59.9343° = 1.0459 rad
    Δφ = 4.1785° = 0.0729 rad
    Δλ = 7.2822° = 0.1271 rad

    a = sin²(0.03645) + cos(0.9731)·cos(1.0459)·sin²(0.06355) ≈ 0.00306
    c = 2·atan2(√0.00306, √0.99694) ≈ 0.1108
    d = 6371 · 0.1108 ≈ 706 km


## Interface

### Application Structure:
1. **Data Input Panel**:
   - Select starting city
   - Add destination points with specified weight
   - Configure algorithm parameters

2. **Visualization**:
   - Interactive map with cities
   - Route animation
   - Graphical display of aircraft

3. **Results Panel**:
   - General route statistics
   - Details for each flight
   - Calculation of time and cost

## Project Launch

The project does not require a server or additional dependencies. To launch:

1. Save the code to the `index.html` file
2. Open the file in any modern browser

## Adding New Cities

To add new cities to the database, modify the `cities` array in the JavaScript code:

```javascript
const cities = [
    // existing cities
    {
        name: "New City",
        lat: XX.XXXX, // latitude in degrees
        lng: YY.YYYY, // longitude in degrees
        airport: "Airport Name"
    },
    // ...
];
```

The distance matrix will be automatically recalculated when the page loads.

## Additional Parameters
You can configure:

- Average aircraft speed (default 800 km/h)
- Airport handling time (default 1 hour)
- Transportation cost per km (default 100 RUB/km)
