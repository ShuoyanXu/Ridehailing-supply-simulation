# Ride-Hailing Supply–Demand Simulation Testbed

## Overview  
This simulation analyzes one full day of vehicle-for-hire operations by modeling supply and demand dynamics across spatial zones. Vehicles are tracked by anonymized VINs (`anon_vin`), and their movements (servicing trips or idling) are governed by historical supply/demand levels and GTA06 interzonal travel‐time data.

---

## Input  

- **Supply & Demand Levels** — from historical trip logs  
- **Interzonal Travel Times** — GTA06 dataset, for realistic travel‐time modeling  

---

## Data Structures  

### Trip Requests  
| Field                          | Description                                   |
|--------------------------------|-----------------------------------------------|
| `request_ID`                   | Unique passenger request ID                   |
| `anon_vin`                     | Anonymized vehicle identifier                 |
| `start_zone`                   | Trip origin zone                              |
| `end_zone`                     | Trip destination zone                         |
| `driver_zone`                  | Zone where driver accepted the request        |
| `Deadhead Distance`            | Distance from acceptance to pickup            |
| `trip_distance`                | Total trip distance (km)                      |
| `fare`                         | Fare charged (CAD)                            |
| `event_type`                   | e.g. `complete`, `cancellation`               |
| `request_datetime`             | When request was made                         |
| `driver_acceptance_datetime`   | When driver accepted                          |
| `passenger_pickup_datetime`    | When passenger was picked up                  |
| `passenger_drop_datetime`      | When passenger was dropped off                |

### P1 (Idle) Events  
| Field                   | Description                                     |
|-------------------------|-------------------------------------------------|
| `anon_vin`              | Anonymized vehicle ID                           |
| `start_zone`            | Zone where idle period began                    |
| `end_zone`              | Zone where idle period ended                    |
| `distance`              | N/A for P1 (idle) events                        |
| `event_type`            | e.g. `P1 Event`                                 |
| `idle_start_datetime`   | Timestamp when vehicle became idle              |
| `idle_end_datetime`     | Timestamp when idle period ended                |

---

## Simulation Process  

1. **Initialization**  
   - Load driver trajectories, zone list, and interzonal travel‐time matrix.  
   - Seed initial supply by zone.  

2. **Time-Step Loop** (e.g. every 5 minutes)  
   - **Demand Ingestion**: pull the number of trip requests per zone.  
   - **Matching & Dispatch**: match available vehicles to requests.  
   - **Event Generation**:  
     - **Trip events** (start, complete)  
     - **P1 idle events** (when no match is found)  
   - **Vehicle Movement**: advance vehicles along links using GTA06 travel times.  

3. **Data Logging**  
   - Record each event (trip or P1) with its timestamps, zones, and distances.

---

## Output  

### P1 Idle Events  
| anon_vin | start_zone | end_zone | distance | event_type | idle_start_datetime | idle_end_datetime |
|----------|------------|----------|----------|------------|---------------------|-------------------|
| 9853     | 533        | 544      | N/A      | P1 Event   | 2020-01-02 00:00    | 2020-01-02 00:05  |
| 36403    | 376        | 373      | N/A      | P1 Event   | 2020-01-02 00:05    | 2020-01-02 00:12  |
| …        | …          | …        | …        | …          | …                   | …                 |

### Trip Completion Events  
| request_ID                                | anon_vin | start_zone | end_zone | event_type     | request_datetime    | driver_acceptance_datetime | passenger_pickup_datetime | passenger_drop_datetime |
|-------------------------------------------|----------|------------|----------|----------------|---------------------|----------------------------|---------------------------|-------------------------|
| 453c4be7a8a4…                             | 17462    | 546        | 547      | Complete trip  | 2020-01-02 00:00    | 2020-01-02 00:00           | 2020-01-02 00:01          | 2020-01-02 00:03        |
| 8c7b13d366d5…                             | 11823    | 400        | 400      | Complete trip  | 2020-01-02 00:00    | 2020-01-02 00:00           | 2020-01-02 00:02          | 2020-01-02 00:05        |
| …                                         | …        | …          | …        | …              | …                   | …                          | …                         | …                       |

---

## Key Metrics  

- **Utilization Rate Distribution**  
- **Average Return Time** (time from drop-off to next dispatch)  
- **Mean Utilization Rate** (per vehicle)  
- **Average Orders per Vehicle**  
- **Average Idling Time**  
- **Average Profit per Vehicle** (per trip/day)  
- **Average Service Time** (request → drop-off)  

---

## Getting Started  

1. **Clone the repo**  
   ```bash
   git clone https://github.com/ShuoyanXu/Ridehailing-supply-simulation.git
   cd Ridehailing-supply-simulation
