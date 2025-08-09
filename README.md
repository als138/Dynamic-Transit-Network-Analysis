# Dynamic Transit Network Analysis

This project analyzes a **complex dynamic network** derived from public transportation data, focusing on the **Capital District Transportation Authority (CDTA)** bus system in New York State. It builds both **static** and **dynamic** network models from GTFS (General Transit Feed Specification) data and performs structural and temporal analyses.

## Table of Contents
- [Introduction](#introduction)
- [Dataset](#dataset)
- [Static Network Construction](#static-network-construction)
- [Dynamic Network Construction](#dynamic-network-construction)
- [Aggregated Network](#aggregated-network)
- [Complexity Analysis](#complexity-analysis)
- [Dynamics Analysis](#dynamics-analysis)
- [Output Files](#output-files)
- [Network Metrics](#network-metrics)
- [Requirements](#requirements)
- [How to Run](#how-to-run)
- [License](#license)

---

## Introduction
The **Dynamic Transit Network Analysis** project investigates the structural and temporal characteristics of a public transportation network.  
The analysis uses **GTFS** data to:
- Build a **static network** representing physical connections between bus stops.
- Create **dynamic snapshots** of the network by day and hour.
- Aggregate these snapshots into a single weighted graph.
- Compute complexity and dynamics metrics.

---

## Dataset
The dataset is from **[OpenMobilityData.org](https://openmobilitydata.org/p/cdta)** and contains:
- **Stops** (`stops.txt`) – IDs, names, coordinates, and types of all stops.
- **Trips** (`trips.txt`) – Information about each trip (route, service type, direction).
- **Stop Times** (`stop_times.txt`) – Arrival and departure times at each stop.
- **Calendar** (`calendar.txt`) – Service schedules by weekday/weekend and active dates.

**GTFS Data Includes**:
- Bus routes and stop locations.
- Service schedules.
- Optional: fare info, temporary changes, etc.

---

## Static Network Construction
- **Nodes**: Bus stops.
- **Edges**: Direct connections between consecutive stops within the same trip.
- **Processing**:
  - Merge `trips.txt` and `stop_times.txt` by `trip_id`.
  - Sort stops by `stop_sequence`.
  - Remove duplicate stops in a trip.
  - Remove duplicate edges across trips.
- **Graph Type**: Undirected, unweighted (physical connectivity only).
- Built using **NetworkX**.

---

## Dynamic Network Construction
- For each combination of **day type** (`weekday`, `saturday`, `sunday`) and **hour** (0–23):
  - Build a **snapshot graph** containing only active trips.
  - Edges have weights:
    - `trip_count`: Number of trips in that hour between two stops.
    - `time_sum`: Total travel time between stops.
    - `avg_travel_time`: Average travel time for that hour.
- Each snapshot is saved in `.graphml` format, viewable in **Cytoscape** or **Gephi**.

---

## Aggregated Network
- All hourly snapshots are **merged** into one graph (`agg_graph`).
- Weights are summed:
  - `trip_count` → total trips over all periods.
  - `time_sum` → total travel time over all periods.
  - `avg_travel_time` recalculated.
- Output: `gtfs_aggregated.graphml`

---

## Complexity Analysis
The aggregated network exhibits:
- **Scale-Free**: Degree distribution follows a power law.
- **Small-World**: Short average path length despite large size.
- **Hubs**: Some stations have extremely high connectivity.
- **Low Clustering**: Limited formation of local triangles.
- **Assortative Mixing**: Highly connected nodes tend to connect to each other.

---

## Dynamics Analysis
- **Snapshots** preserve temporal activity patterns.
- Aggregation removes specific time-of-day detail but retains overall frequency patterns (`trip_count`).
- Enables detection of high-traffic corridors over multiple days.

---

## Output Files
Example files generated:
- `gtfs_snapshot_weekday_hour_07.graphml`
- `gtfs_snapshot_saturday_hour_10.graphml`
- `gtfs_snapshot_sunday_hour_15.graphml`
- `gtfs_aggregated.graphml`

---

## Network Metrics (Aggregated Graph)
| Metric | Value |
|--------|-------|
| Nodes | 2892 |
| Edges | 3475 |
| Density | 0.00083 |
| Components | 2 |
| Largest CC Size | 2671 nodes |
| Avg Shortest Path | 24.53 |
| Diameter | 94 |
| Avg Clustering | 0.0374 |
| Transitivity | 0.0645 |
| Degree Assortativity | +0.296 |

---

## Requirements
- Python 3.x
- NetworkX
- Pandas
- NumPy
- Matplotlib
- (Optional) Cytoscape or Gephi for visualization

---

## How to Run
1. **Prepare the dataset**:
   - Download GTFS data and place it in the `gtfs` folder.
2. **Run preprocessing**:
   ```bash
   python build_static_network.py
   python build_dynamic_network.py
   python aggregate_network.py
   ```
3. **Analyze and visualize**:
   - Open `.graphml` files in Gephi/Cytoscape.
   - Use `analysis.py` to compute network metrics.

---

## License
This project is released under the MIT License.
