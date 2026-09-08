# Disaster Operations & Use Cases

MeshBeacon Ops is designed for environments where public telecommunication networks, power grids, and internet access are unreliable, disrupted, or entirely absent.

---

## Historical Origin: The 2014 Kelantan Floods (*Bah Kuning*)

In December 2014, the state of Kelantan on the east coast of Peninsular Malaysia experienced catastrophic flooding, remembered locally as **Bah Kuning** (The Yellow Flood) due to the immense volume of silt and mud swept down from the interior river basins.

### What Happened During *Bah Kuning*
1. **Unprecedented Water Levels**: Relentless torrential rain from the Northeast Monsoon caused the Galas, Lebir, and Kelantan rivers to swell simultaneously. Floodwaters rose above 10 meters in places like Manek Urai and Kuala Krai, submerging whole two-story buildings, hospitals, and primary evacuation shelters.
2. **Infrastructure Collapse**: Electrical substations flooded and shut down. Cellular base transceiver stations (BTS) lost power or were submerged under water. Fiber backhaul lines snapped, and cellular signals vanished completely across entire districts.
3. **Information Blackout**: Over 200,000 displaced people were trapped on rooftops and isolated high ground across the state without real-time situational awareness or centralized telemetry.

### The Architectural Lesson
Natural disasters destroy commercial infrastructure precisely when reliable communication is most critical. MeshBeacon was built to address this reality:
- **Zero Internet Requirement**: Ingestion, triage, incident management, and mapping work 100% offline.
- **Low-Power LoRa Mesh**: Devices operate for days on small batteries or solar panels without relying on cellular towers.
- **Local Spatial Awareness**: Raster MBTiles provide complete topographic and street-level maps directly from local storage.
- **Resilient Upstream Sync**: Telemetry is buffered locally and forwarded upstream when connections resume.

---

## Operational Scenarios

### 1. Monsoon & Flash Flood Operations

During seasonal monsoons or sudden flash floods:
- **Deployment**: PapaDuck gateways are placed at mobile command posts or elevated evacuation centers. MamaDuck relay nodes are deployed along elevated rooftops or ridge lines.
- **Field Telemetry**: Rescue boats and field teams carry Duck beacons with GPS sensors and emergency SOS buttons.
- **Operations Console**: MeshBeacon logs all coordinates, tracks boat locations on offline maps, and raises high-priority alerts for incoming SOS packets. Operators dispatch acknowledgments (`ALERT_ACK`) back across the mesh to confirm rescue is en route.

### 2. Search & Rescue (SAR) in Remote Terrain

In dense tropical rainforests, mountainous valleys, or maritime zones:
- **Challenge**: SAR teams operate far beyond cellular coverage. Line-of-sight VHF voice radio is often blocked by terrain.
- **Solution**: Multi-hop LoRa mesh packets hop across ridgeline repeaters back to the base camp.
- **Tracking & Interoperability**: MeshBeacon decodes GPS coordinates, speed, heading, and satellite health. It broadcasts Cursor-on-Target (CoT) XML over UDP multicast to field tablets running ATAK/WinTAK, allowing tactical operators to visualize team members in real time.

### 3. Emergency Operations Center (PKOB / EOC) Management

In municipal or district disaster coordination headquarters:
- **Kiosk Mode (`/kiosk`)**: Fullscreen, auto-refreshing wallboards display live active incidents, responder queues, and device health status on large display panels without requiring manual page reloads.
- **Role-Based Incident Triage**: Radio operators log incoming citizen reports, assign registered responders, record operational triage notes, and mark incidents resolved.
- **Post-Incident Audit & Reporting**: Export timestamped CSV dossiers and print-ready after-action review (AAR) reports for government agencies and disaster relief oversight.

### 4. Remote River Basin & Village Early Warning

In isolated upstream river catchments:
- **Sensors**: Solar-powered Duck nodes equipped with water level sensors, rain gauges, and environmental sensors monitor river rise rates.
- **Automated Rules Engine**: MeshBeacon evaluates incoming telemetry thresholds in real time. When water level or rainfall thresholds are crossed, automated rules dispatch emergency broadcasts across the mesh and notify Telegram responder channels.

### 5. Hybrid Store-and-Forward Emergency Networks

For forward operating bases with intermittent satellite or cellular connectivity:
- **Local Outbox Durability**: Every incoming packet is stored in the local SQLite database.
- **Automatic Upstream Ingestion**: When connectivity becomes available (via Starlink, mobile satellite terminal, or intermittent 4G), the background `SyncRecordToCloud` worker sends idempotent HTTP POST requests to the central state/national server (`/api/ingest`).
- **Read-Only Central Console**: The central server operates in read-only mode (`DASHBOARD_READONLY=true`), monitoring statewide status without interfering with field-level dispatch decisions.
