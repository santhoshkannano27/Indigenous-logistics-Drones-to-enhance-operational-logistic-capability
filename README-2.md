# Indigenous Logistics Drone (ILD) System

A Python simulation, 3D mesh, and reporting toolkit for an indigenously
designed logistics/cargo drone, built to enhance operational and logistic
capability — forward-post resupply, medical logistics, and disaster-relief
delivery.

The project models drone specifications, payload limits, endurance/range
estimation, mission planning across multiple delivery waypoints, and a
battery-drain flight simulation. It can also generate a simplified 3D
airframe mesh (STL/OBJ) and export mission results to CSV/JSON.

## Features

- **Drone specs** — configurable airframe weight, payload limit, battery
  capacity, cruise speed, and power draw.
- **Payload validation** — rejects missions that exceed rated payload.
- **Endurance & range estimation** — simplified energy model that accounts
  for extra power draw as payload increases, with a safety reserve margin.
- **Mission planning** — define an origin and a sequence of delivery
  waypoints, each with its own distance and payload drop.
- **Feasibility check** — flags missions that exceed max payload or max
  range before flight.
- **Flight simulation** — steps through each waypoint, tracking elapsed
  time, battery percentage remaining, and payload delivered.
- **3D airframe mesh builder** — procedurally generates a low-poly drone
  airframe (fuselage, arms, motor mounts, landing legs) and exports it to
  `.stl` / `.obj` for viewing in Blender, MeshLab, Windows 3D Viewer, etc.
- **Report exporter** — exports the flight log to CSV and the full mission
  report (specs + mission + feasibility + flight log) to JSON.

## Requirements

- Python 3.8+
- No external dependencies — standard library only (see `requirements.txt`)

## Project Structure

```
.
├── code.py            # Drone specs, payload/endurance models, mission planner, simulator
├── mesh_builder.py     # Procedural 3D airframe mesh + STL/OBJ export
├── exporter.py         # Mission report export to CSV/JSON
├── requirements.txt    # Dependency notes (none required; optional extras listed)
└── README.md           # This file
```

## Usage

### 1. Run the mission simulation

```bash
python code.py
```

```
============================================================
 MISSION REPORT — ILD-01 (Indigenous Logistics Drone)
============================================================
Origin: Base Camp Alpha
Total distance: 27.5 km
Total payload: 11.0 kg (44% of rated capacity)
Feasible: YES
------------------------------------------------------------
Waypoint          Dist (km)   T+ (min)    Batt %    Payload left (kg)
Outpost Bravo     8.5         7.3         78.1      5.0
Outpost Charlie   6.0         12.4        64.5      0.0
Return to Base    13.0        23.6        38.3      0.0
============================================================

Max range at current payload: 31.1 km
Max endurance at current payload: 26.7 min
```

### 2. Generate the 3D airframe mesh

```bash
python mesh_builder.py
```

Produces `ild_airframe.stl` and `ild_airframe.obj` — a low-poly model of
the fuselage, four arms, motor mounts, and landing legs. Open either file
in Blender, MeshLab, or Windows 3D Viewer to inspect it.

### 3. Export the mission report

```bash
python exporter.py
```

Produces:
- `mission_flight_log.csv` — one row per waypoint (distance, elapsed time,
  battery %, payload delivered/remaining)
- `mission_report.json` — full report: drone specs, mission summary,
  feasibility check, and flight log

## Customizing a mission

Edit the `main()` function in `code.py`, or import the classes into your
own script:

```python
from code import DroneSpecs, Mission, Waypoint, MissionPlanner, FlightSimulator
from exporter import export_flight_log_csv, export_mission_report_json
from mesh_builder import build_drone_airframe, export_stl, export_obj

specs = DroneSpecs(max_payload_kg=20.0, battery_capacity_wh=900.0)
planner = MissionPlanner(specs)
simulator = FlightSimulator(specs)

mission = Mission(
    origin="Forward Base",
    waypoints=[
        Waypoint(label="Checkpoint 1", distance_from_previous_km=10, payload_drop_kg=8),
        Waypoint(label="Return", distance_from_previous_km=10, payload_drop_kg=0),
    ],
)

feasible, issues = planner.feasibility_check(mission)
log = simulator.simulate(mission)

export_flight_log_csv(log, "my_flight_log.csv")
export_mission_report_json(specs, mission, feasible, issues, log, "my_report.json")

mesh = build_drone_airframe(specs)
export_stl(mesh, "my_drone.stl")
```

## Notes

- The power/energy model is a simplified linear approximation intended for
  academic/demonstration purposes, not flight-certified engineering
  analysis.
- A 20% battery reserve margin is built into range/endurance calculations
  by default (configurable via `PerformanceEstimator.RESERVE_MARGIN_PCT`).
- The airframe mesh is a geometric approximation for visualization, not a
  certified CAD model — dimensions are illustrative, not derived from
  structural analysis.

## License

Academic project — for educational use.

##prototype video :
https://youtu.be/4kJgMOXadxM?si=HypMTsGXbxsIU2Ne
