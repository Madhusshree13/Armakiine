# Design Document: Campus Water Digital Twin System

## Overview

The Campus Water Digital Twin System is a sophisticated real-time monitoring and control platform that creates a virtual representation of campus water infrastructure. The system employs a 4-layer architecture with a multi-agent intelligence layer capable of autonomous danger detection and mitigation.

### Design Philosophy

- **Layered Architecture**: Clear separation of concerns across 4 distinct layers
- **Agent Autonomy**: Intelligent agents operate independently while coordinating through a central orchestrator
- **Real-Time Performance**: Sub-second data ingestion with rapid response to critical events
- **Fault Tolerance**: Redundancy and graceful degradation ensure continuous operation
- **Security First**: Defense-in-depth approach with encryption, access control, and network segmentation

### Key Design Decisions

1. **Event-Driven Architecture**: Use message queues for asynchronous communication between layers
2. **Time-Series Database**: Optimize for high-frequency sensor writes and analytical queries
3. **Agent Framework**: Implement agents as independent microservices with message-based coordination
4. **Digital Twin State**: Maintain in-memory state synchronized with persistent storage
5. **Simulation Engine**: Integrate hydraulic modeling library for real-time predictions

## Architecture

### 4-Layer System Architecture


```
┌─────────────────────────────────────────────────────────────┐
│                    INTERFACE LAYER                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ Web Dashboard│  │ Mobile App   │  │ REST API     │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
└─────────────────────────────────────────────────────────────┘
                            ↕
┌─────────────────────────────────────────────────────────────┐
│                  INTELLIGENCE LAYER                         │
│  ┌────────────────────────────────────────────────────┐    │
│  │           Coordination Agent (Orchestrator)        │    │
│  └────────────────────────────────────────────────────┘    │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐     │
│  │ Water    │ │ Leak     │ │ Pressure │ │ Predict  │     │
│  │ Quality  │ │ Detection│ │ Mgmt     │ │ Maint    │     │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘     │
│  ┌──────────────────────────────────┐                      │
│  │   Emergency Response Agent       │                      │
│  └──────────────────────────────────┘                      │
└─────────────────────────────────────────────────────────────┘
                            ↕
┌─────────────────────────────────────────────────────────────┐
│                     MODEL LAYER                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ Digital Twin │  │ Hydraulic    │  │ Predictive   │     │
│  │ State Engine │  │ Simulator    │  │ Models       │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
└─────────────────────────────────────────────────────────────┘
                            ↕
┌─────────────────────────────────────────────────────────────┐
│                      DATA LAYER                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ Sensor       │  │ Time-Series  │  │ Data Quality │     │
│  │ Ingestion    │  │ Database     │  │ Manager      │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
└─────────────────────────────────────────────────────────────┘
                            ↕
                    Physical Sensors
```

### Layer Responsibilities

**Data Layer**:
- Ingest sensor data from IoT devices via MQTT
- Validate and quality-check incoming data
- Store time-series data in optimized database
- Provide data access APIs to upper layers

**Model Layer**:
- Maintain digital twin state representation
- Execute hydraulic simulations for predictions
- Run machine learning models for predictive maintenance
- Calibrate models using historical data

**Intelligence Layer**:
- Deploy specialized agents for different monitoring domains
- Coordinate agent actions through central orchestrator
- Detect dangers and generate alerts
- Execute or recommend mitigation actions

**Interface Layer**:
- Provide web-based visualization dashboard
- Expose REST API for external integrations
- Handle user authentication and authorization
- Deliver mobile notifications for critical alerts


## Technology Stack

### Perception Layer (Hardware)

**Microcontroller**: ESP32-S3
- Dual-core processor for sensor management and communication
- Built-in WiFi for network connectivity
- Low power consumption for distributed deployment
- Rich GPIO for sensor interfacing

**Sensors**: Ultrasonic Sensors
- Non-contact water level measurement
- Reliable for tank and reservoir monitoring
- Cost-effective for campus-wide deployment
- Weather-resistant for outdoor installations

**Actuators**: Solenoid Valves
- Electrically controlled valve operation
- Fast response time for emergency shutoff
- Low power consumption
- Direct control from ESP32 GPIO

### Transmission Layer (Spine)

**Message Broker**: MQTT (Mosquitto)
- Lightweight pub/sub protocol ideal for IoT
- Much faster to set up than Kafka for prototyping
- Low bandwidth requirements
- QoS levels for reliable delivery
- Native support in ESP32 libraries

**MQTT Topics Structure**:
```
campus-water/sensors/{location}/level
campus-water/sensors/{location}/flow
campus-water/sensors/{location}/quality
campus-water/actuators/{location}/valve/command
campus-water/actuators/{location}/valve/status
campus-water/alerts/{type}
campus-water/system/status
```

### Intelligence Layer (Brain)

**Agent Framework**: Python with asyncio
- Asynchronous agents for concurrent operation
- MQTT client integration (paho-mqtt)
- Lightweight and fast to develop

**AI/ML Engine**: Google Gemini 2.0 Flash
- Advanced reasoning for complex water system scenarios
- Natural language understanding for operator queries
- Pattern recognition for anomaly detection
- Fast inference for real-time decision making
- Multimodal capabilities for analyzing sensor data and system state

**Time-Series Database**: InfluxDB
- Purpose-built for IoT time-series data
- Fast writes for high-frequency sensor data
- Efficient storage with automatic downsampling
- Built-in retention policies
- Flux query language for analytics

**State Management**: In-memory Python dictionaries with InfluxDB persistence
- Fast access for real-time agent decisions
- Periodic persistence to InfluxDB
- Simple architecture for rapid prototyping

### Visualization Layer (Mirror)

**3D Visualization**: Three.js
- WebGL-based 3D rendering
- Real-time campus water network visualization
- Interactive component selection
- Smooth animations for state changes

**Build Tool**: Vite
- Fast development server with HMR
- Optimized production builds
- Modern ES modules support
- Plugin ecosystem for React

**Frontend Framework**: React
- Component-based UI architecture
- Efficient re-rendering with virtual DOM
- Rich ecosystem of libraries
- TypeScript support for type safety

**Real-time Communication**: WebSocket
- Bidirectional communication with backend
- Real-time sensor data streaming
- Instant alert notifications
- Low latency for critical updates

### Supporting Infrastructure

**Backend API**: FastAPI (Python)
- RESTful API with automatic OpenAPI docs
- WebSocket support for real-time updates
- Async request handling
- Easy integration with Gemini API

**Development Environment**: Docker Compose
- Containerized services for consistency
- Easy local development setup
- Mosquitto, InfluxDB, and API in containers
- Volume mounts for data persistence

**Deployment**: Single server or edge device
- Lightweight enough for Raspberry Pi or small server
- No complex orchestration needed for prototype
- Can scale to Kubernetes later if needed


## Components and Interfaces

### Perception Layer Components

#### ESP32 Sensor Node

**Responsibilities**:
- Read ultrasonic sensor data at 1Hz frequency
- Control solenoid valve based on commands
- Publish sensor readings to MQTT broker
- Subscribe to valve control commands
- Handle WiFi connectivity and reconnection

**Firmware Interface** (Arduino/ESP-IDF):
```cpp
class SensorNode {
    void setup();
    void loop();
    void readUltrasonicSensor();
    void publishSensorReading(float level_cm);
    void onValveCommand(String command);
    void controlValve(bool open);
    void handleWiFiReconnect();
};
```

**MQTT Topics** (per node):
- Publish: `campus-water/sensors/{location}/level` - Level readings
- Subscribe: `campus-water/actuators/{location}/valve/command` - Valve commands
- Publish: `campus-water/actuators/{location}/valve/status` - Valve status

### Transmission Layer Components

#### MQTT Broker (Mosquitto)

**Configuration**:
- Port: 1883 (MQTT), 8883 (MQTT over TLS)
- Authentication: Username/password per sensor node
- QoS: Level 1 (at least once delivery) for sensor data
- Retained messages: Latest valve status
- Persistence: Enabled for message durability

**Topic Structure**:
```
campus-water/
├── sensors/
│   ├── {building-id}/
│   │   ├── level
│   │   ├── flow
│   │   └── quality
├── actuators/
│   ├── {building-id}/
│   │   └── valve/
│   │       ├── command
│   │       └── status
├── alerts/
│   ├── contamination
│   ├── leak
│   └── pressure
└── system/
    └── status
```

#### MQTT Bridge Service

**Responsibilities**:
- Subscribe to all sensor topics
- Parse and validate sensor messages
- Write validated data to InfluxDB
- Forward critical alerts to agents
- Handle sensor node registration

**Interface**:
```python
class MQTTBridgeService:
    def on_connect(self, client, userdata, flags, rc) -> None:
        """Handle MQTT connection"""
        
    def on_message(self, client, userdata, msg) -> None:
        """Handle incoming MQTT message"""
        
    def write_to_influxdb(self, sensor_data: SensorReading) -> None:
        """Write sensor data to InfluxDB"""
        
    def forward_to_agents(self, sensor_data: SensorReading) -> None:
        """Forward data to agent system"""
```

#### Data Quality Manager

**Responsibilities**:
- Validate sensor readings against expected ranges
- Detect and flag anomalous data points
- Interpolate missing values using neighboring sensors
- Calculate data quality scores
- Use Gemini 2.0 Flash for advanced anomaly detection

**Interface**:
```python
class DataQualityManager:
    def validate_reading(self, reading: SensorReading) -> ValidationResult:
        """Validate a sensor reading"""
        
    def detect_anomaly_with_gemini(self, reading: SensorReading, history: List[SensorReading]) -> AnomalyAnalysis:
        """Use Gemini to detect complex anomalies"""
        
    def interpolate_missing(self, sensor_id: str, timestamp: datetime) -> Optional[float]:
        """Interpolate missing value"""
        
    def calculate_quality_score(self, sensor_id: str, time_window: timedelta) -> float:
        """Calculate data quality score for sensor"""
```

#### InfluxDB Storage Service

**Responsibilities**:
- Store sensor data in InfluxDB
- Provide query interface for historical data
- Manage data retention and downsampling
- Execute Flux queries for analytics

**Interface**:
```python
class InfluxDBStorage:
    def write_reading(self, reading: SensorReading) -> None:
        """Write sensor reading to InfluxDB"""
        
    def query_range(self, sensor_id: str, start: datetime, end: datetime) -> List[SensorReading]:
        """Query readings in time range"""
        
    def query_latest(self, sensor_ids: List[str]) -> Dict[str, SensorReading]:
        """Get latest reading for each sensor"""
        
    def get_aggregates(self, sensor_id: str, window: str, start: datetime, end: datetime) -> List[Aggregate]:
        """Get aggregated data using Flux"""
```

**InfluxDB Schema**:
```
Measurement: sensor_readings
Tags:
  - sensor_id
  - location
  - sensor_type (level, flow, quality)
Fields:
  - value (float)
  - quality_score (float)
  - flags (string)
Timestamp: nanosecond precision
```


### Model Layer Components

#### Digital Twin State Engine

**Responsibilities**:
- Maintain current state of all infrastructure components
- Update state based on sensor data and simulations
- Provide fast state queries for agents
- Publish state change events

**Interface**:
```python
class DigitalTwinStateEngine:
    def update_component_state(self, component_id: str, state: ComponentState) -> None:
        """Update state of infrastructure component"""
        
    def get_component_state(self, component_id: str) -> ComponentState:
        """Get current state of component"""
        
    def get_network_state(self) -> NetworkState:
        """Get complete network state"""
        
    def subscribe_to_changes(self, component_id: str, callback: Callable) -> None:
        """Subscribe to state changes"""
        
    def get_affected_zones(self, component_id: str) -> List[str]:
        """Get zones affected by component"""
```

**State Storage**: Redis with the following structure:
```
component:{id}:state -> JSON state object
component:{id}:sensors -> Set of sensor IDs
network:topology -> Graph structure
network:zones -> Zone definitions
```

#### Hydraulic Simulator

**Responsibilities**:
- Execute EPANET simulations for flow and pressure
- Predict impact of valve operations
- Simulate contamination spread
- Calibrate model parameters

**Interface**:
```python
class HydraulicSimulator:
    def simulate_current_state(self) -> SimulationResult:
        """Simulate current network conditions"""
        
    def simulate_valve_closure(self, valve_ids: List[str]) -> SimulationResult:
        """Simulate impact of closing valves"""
        
    def simulate_contamination_spread(self, source_node: str, start_time: datetime) -> ContaminationSpread:
        """Simulate contamination propagation"""
        
    def calibrate_model(self, sensor_data: Dict[str, List[SensorReading]]) -> CalibrationResult:
        """Calibrate model parameters"""
        
    def predict_pressure_at_node(self, node_id: str, time_horizon: timedelta) -> List[float]:
        """Predict future pressure at node"""
```

#### Predictive Models Service

**Responsibilities**:
- Run ML models for equipment failure prediction
- Forecast demand patterns
- Detect anomalies in operational data
- Provide confidence intervals for predictions

**Interface**:
```python
class PredictiveModelsService:
    def predict_equipment_failure(self, equipment_id: str, horizon_days: int) -> FailurePrediction:
        """Predict probability of equipment failure"""
        
    def forecast_demand(self, zone_id: str, horizon_hours: int) -> List[float]:
        """Forecast water demand"""
        
    def detect_operational_anomaly(self, equipment_id: str, metrics: Dict[str, float]) -> AnomalyScore:
        """Detect anomalous operation"""
        
    def update_model(self, model_name: str, training_data: DataFrame) -> None:
        """Update model with new training data"""
```


### Intelligence Layer Components

#### Gemini Integration Service

**Responsibilities**:
- Interface with Google Gemini 2.0 Flash API
- Provide context about water system state
- Get AI-powered analysis and recommendations
- Handle rate limiting and error recovery

**Interface**:
```python
class GeminiIntegrationService:
    def analyze_sensor_pattern(self, sensor_data: List[SensorReading], context: str) -> AnalysisResult:
        """Use Gemini to analyze sensor patterns"""
        
    def recommend_mitigation(self, alert: Alert, system_state: NetworkState) -> List[MitigationAction]:
        """Get AI-powered mitigation recommendations"""
        
    def explain_anomaly(self, anomaly: Anomaly, history: List[SensorReading]) -> str:
        """Get natural language explanation of anomaly"""
        
    def answer_operator_query(self, query: str, system_state: NetworkState) -> str:
        """Answer operator questions about system"""
```

**Gemini Prompt Templates**:
```python
CONTAMINATION_ANALYSIS_PROMPT = """
You are analyzing a campus water system. Current sensor readings show:
{sensor_data}

System topology: {topology}

Determine if this indicates contamination and recommend actions.
"""

LEAK_DETECTION_PROMPT = """
Analyze these flow patterns for potential leaks:
{flow_data}

Historical baseline: {baseline}

Identify if there's a leak and estimate location.
"""
```

#### Agent Base Architecture

All agents inherit from a common base class providing:
- MQTT communication for sensor data
- Access to InfluxDB for historical data
- Gemini integration for AI-powered analysis
- Logging and telemetry

```python
class BaseAgent:
    def __init__(self, agent_id: str, mqtt_client: mqtt.Client, gemini_service: GeminiIntegrationService):
        """Initialize agent"""
        
    async def start(self) -> None:
        """Start agent processing loop"""
        
    async def stop(self) -> None:
        """Gracefully stop agent"""
        
    async def process_sensor_data(self, data: SensorReading) -> None:
        """Process incoming sensor data"""
        
    async def publish_alert(self, alert: Alert) -> None:
        """Publish alert via MQTT"""
        
    async def publish_action_recommendation(self, action: MitigationAction) -> None:
        """Recommend mitigation action"""
        
    def get_world_state(self) -> NetworkState:
        """Get current world model state"""
        
    async def query_gemini(self, prompt: str, context: Dict) -> str:
        """Query Gemini for analysis"""
```

#### Water Quality Monitor Agent

**Responsibilities**:
- Monitor water quality sensor data continuously
- Detect contamination events using Gemini analysis
- Classify contamination severity
- Identify affected zones

**Specific Interface**:
```python
class WaterQualityMonitorAgent(BaseAgent):
    async def analyze_quality_reading(self, reading: WaterQualitySensorReading) -> Optional[ContaminationAlert]:
        """Analyze quality reading for contamination using Gemini"""
        
    def classify_contamination_severity(self, parameters: Dict[str, float]) -> ContaminationSeverity:
        """Classify severity of contamination"""
        
    def identify_affected_zones(self, contamination_node: str) -> List[str]:
        """Identify zones affected by contamination"""
        
    async def get_gemini_contamination_analysis(self, reading: WaterQualitySensorReading, history: List[WaterQualitySensorReading]) -> ContaminationAnalysis:
        """Use Gemini to analyze if reading indicates contamination"""
```

**Detection Logic with Gemini**:
- Basic thresholds: pH 6.5-8.5, Turbidity <1 NTU, Chlorine 0.2-4.0 mg/L
- Gemini analyzes patterns across multiple sensors
- Gemini distinguishes sensor faults from actual contamination
- Gemini provides natural language explanation of findings

#### Leak Detection Agent

**Responsibilities**:
- Analyze flow patterns for leak signatures using Gemini
- Localize leak using ultrasonic level sensor correlation
- Classify leak severity
- Distinguish leaks from legitimate demand changes

**Specific Interface**:
```python
class LeakDetectionAgent(BaseAgent):
    async def analyze_flow_pattern(self, level_data: Dict[str, List[LevelReading]]) -> Optional[LeakAlert]:
        """Analyze level patterns for leaks using Gemini"""
        
    def localize_leak(self, level_anomaly: LevelAnomaly) -> LeakLocation:
        """Estimate leak location from level sensors"""
        
    def classify_leak_severity(self, estimated_flow_loss: float) -> LeakSeverity:
        """Classify leak as minor/moderate/critical"""
        
    async def get_gemini_leak_analysis(self, level_pattern: List[float], historical_patterns: List[List[float]]) -> LeakAnalysis:
        """Use Gemini to distinguish leak from demand change"""
```

**Detection Algorithm with Gemini**:
1. Monitor tank level drop rates
2. Gemini analyzes if drop rate is consistent with normal usage
3. Correlate level drops across multiple tanks
4. Gemini estimates leak location based on which tanks are affected
5. Gemini provides confidence score and explanation


#### Pressure Management Agent

**Responsibilities**:
- Monitor pressure levels across network
- Detect low/high pressure events
- Identify root causes of pressure anomalies
- Recommend corrective actions

**Specific Interface**:
```python
class PressureManagementAgent(BaseAgent):
    async def analyze_pressure_reading(self, reading: PressureSensorReading) -> Optional[PressureAlert]:
        """Analyze pressure reading"""
        
    def identify_root_cause(self, pressure_anomaly: PressureAnomaly) -> RootCause:
        """Identify cause of pressure issue"""
        
    def recommend_corrective_action(self, root_cause: RootCause) -> MitigationAction:
        """Recommend action to correct pressure"""
        
    def predict_pressure_impact(self, proposed_action: MitigationAction) -> PressureImpact:
        """Predict impact of proposed action"""
```

**Pressure Thresholds**:
- Minimum: 20 PSI (alert if below)
- Maximum: 80 PSI (alert if above)
- Target range: 40-60 PSI

#### Predictive Maintenance Agent

**Responsibilities**:
- Monitor equipment health metrics
- Predict equipment failures
- Prioritize maintenance recommendations
- Learn from historical failure data

**Specific Interface**:
```python
class PredictiveMaintenanceAgent(BaseAgent):
    async def analyze_equipment_health(self, equipment_id: str, metrics: EquipmentMetrics) -> Optional[MaintenanceAlert]:
        """Analyze equipment health"""
        
    def predict_failure_probability(self, equipment_id: str, horizon_days: int) -> FailureProbability:
        """Predict failure probability"""
        
    def prioritize_maintenance(self, alerts: List[MaintenanceAlert]) -> List[MaintenanceAlert]:
        """Prioritize maintenance by criticality"""
        
    def learn_from_failure(self, equipment_id: str, failure_event: FailureEvent) -> None:
        """Update models with failure data"""
```

**Monitored Metrics**:
- Pump: vibration, temperature, power consumption, flow rate
- Valve: actuation time, position accuracy, cycle count
- Tank: level sensor health, fill/drain rates

#### Emergency Response Agent

**Responsibilities**:
- Execute pre-approved mitigation actions
- Control solenoid valves via MQTT
- Validate action feasibility via Gemini
- Log all actions with reasoning
- Implement safety interlocks

**Specific Interface**:
```python
class EmergencyResponseAgent(BaseAgent):
    async def execute_mitigation_action(self, action: MitigationAction) -> ExecutionResult:
        """Execute mitigation action"""
        
    async def control_valve(self, valve_id: str, command: str) -> bool:
        """Send valve control command via MQTT"""
        
    async def validate_action_with_gemini(self, action: MitigationAction) -> ValidationResult:
        """Validate action using Gemini reasoning"""
        
    def check_safety_interlocks(self, action: MitigationAction) -> bool:
        """Check if action violates safety rules"""
        
    def log_action(self, action: MitigationAction, result: ExecutionResult) -> None:
        """Log action with reasoning"""
        
    async def notify_operators(self, action: MitigationAction) -> None:
        """Notify human operators via WebSocket"""
```

**Pre-Approved Actions**:
- Close isolation valves for contaminated zones
- Open emergency drain valves
- Activate backup water sources
- Isolate leaking sections

**MQTT Valve Control**:
```python
# Publish to: campus-water/actuators/{location}/valve/command
{
    "command": "close",  # or "open"
    "reason": "contamination_isolation",
    "timestamp": "2024-01-15T10:30:00Z",
    "authorized_by": "emergency_response_agent"
}
```


#### Coordination Agent

**Responsibilities**:
- Orchestrate multi-agent responses using Gemini reasoning
- Resolve conflicting mitigation actions
- Maintain shared world model
- Redistribute responsibilities on agent failure

**Specific Interface**:
```python
class CoordinationAgent(BaseAgent):
    async def coordinate_response(self, alerts: List[Alert]) -> ResponsePlan:
        """Coordinate response to multiple alerts using Gemini"""
        
    async def resolve_conflicts_with_gemini(self, actions: List[MitigationAction]) -> List[MitigationAction]:
        """Use Gemini to resolve conflicting actions"""
        
    def update_shared_world_model(self, state_update: StateUpdate) -> None:
        """Update shared world model in memory"""
        
    def handle_agent_failure(self, failed_agent_id: str) -> None:
        """Redistribute responsibilities"""
        
    def log_conflict_resolution(self, conflict: ActionConflict, resolution: Resolution) -> None:
        """Log conflict resolution reasoning from Gemini"""
```

**Priority Hierarchy**:
1. Public health (contamination)
2. Safety (major leaks, system failures)
3. Asset protection (equipment damage prevention)
4. Efficiency (optimization, minor issues)

**Gemini-Powered Conflict Resolution**:
- Gemini analyzes conflicting actions with full system context
- Considers cascading effects and dependencies
- Provides natural language explanation of resolution
- Suggests alternative approaches if conflicts are complex

### Agent Communication Protocol

**MQTT-Based Communication**:
```python
# Alert publication
Topic: campus-water/alerts/{alert_type}
Payload: {
    "alert_id": "uuid",
    "timestamp": "ISO8601",
    "severity": "critical|high|medium|low",
    "source_agent": "agent_id",
    "description": "...",
    "affected_components": ["..."],
    "recommended_actions": ["..."]
}

# Action recommendation
Topic: campus-water/actions/recommendations
Payload: {
    "action_id": "uuid",
    "action_type": "close_valve|open_valve|...",
    "target_components": ["..."],
    "priority": "critical|high|medium|low",
    "reasoning": "Gemini explanation"
}
```


### Visualization Layer Components

#### Three.js 3D Visualization

**Responsibilities**:
- Render 3D campus water network
- Display real-time sensor data on components
- Animate valve operations and water flow
- Provide interactive component selection

**Key Features**:
- WebGL-based 3D rendering with Three.js
- Color-coded status indicators (green=normal, yellow=warning, red=critical)
- Real-time updates via WebSocket
- Camera controls for navigation
- Component highlighting on hover
- Click to view detailed information

**Scene Structure**:
```javascript
class WaterNetworkScene {
    constructor(container) {
        this.scene = new THREE.Scene();
        this.camera = new THREE.PerspectiveCamera();
        this.renderer = new THREE.WebGLRenderer();
        this.controls = new OrbitControls(this.camera, this.renderer.domElement);
    }
    
    addTank(tank) {
        // Add 3D tank model with level indicator
    }
    
    addPipe(pipe) {
        // Add pipe with flow animation
    }
    
    addValve(valve) {
        // Add valve with open/close state
    }
    
    updateComponentStatus(componentId, status) {
        // Update color based on status
    }
    
    animateWaterFlow() {
        // Animate particles along pipes
    }
}
```

#### React Dashboard

**Responsibilities**:
- Provide UI framework for visualization
- Display alert feed and system status
- Handle user interactions
- Manage WebSocket connections

**Component Structure**:
```typescript
// Main dashboard component
const Dashboard: React.FC = () => {
    const [networkState, setNetworkState] = useState<NetworkState>();
    const [alerts, setAlerts] = useState<Alert[]>([]);
    const wsRef = useRef<WebSocket>();
    
    useEffect(() => {
        // Connect to WebSocket
        wsRef.current = new WebSocket('ws://localhost:8000/ws');
        wsRef.current.onmessage = handleWebSocketMessage;
    }, []);
    
    return (
        <div className="dashboard">
            <ThreeJSVisualization networkState={networkState} />
            <AlertPanel alerts={alerts} />
            <ComponentDetails />
            <ActionControls />
        </div>
    );
};

// Alert panel component
const AlertPanel: React.FC<{alerts: Alert[]}> = ({alerts}) => {
    return (
        <div className="alert-panel">
            {alerts.map(alert => (
                <AlertCard key={alert.alert_id} alert={alert} />
            ))}
        </div>
    );
};
```

#### Vite Build Configuration

**Development Server**:
- Hot Module Replacement (HMR) for fast iteration
- Proxy API requests to backend
- WebSocket proxy for real-time updates

**Production Build**:
- Code splitting for optimal loading
- Asset optimization and minification
- Tree shaking for smaller bundle size

```javascript
// vite.config.js
export default {
    server: {
        proxy: {
            '/api': 'http://localhost:8000',
            '/ws': {
                target: 'ws://localhost:8000',
                ws: true
            }
        }
    },
    build: {
        rollupOptions: {
            output: {
                manualChunks: {
                    'three': ['three'],
                    'react-vendor': ['react', 'react-dom']
                }
            }
        }
    }
};
```

#### Backend API Service

**Responsibilities**:
- Expose REST API for system data
- Handle WebSocket connections for real-time updates
- Authenticate and authorize requests
- Query InfluxDB for historical data
- Publish commands to MQTT

**FastAPI Endpoints**:
```python
from fastapi import FastAPI, WebSocket
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

@app.get("/api/v1/network/status")
async def get_network_status() -> NetworkState:
    """Get overall network status"""
    
@app.get("/api/v1/sensors/{sensor_id}/readings")
async def get_sensor_readings(sensor_id: str, start: datetime, end: datetime) -> List[SensorReading]:
    """Get sensor readings from InfluxDB"""
    
@app.get("/api/v1/alerts")
async def get_active_alerts() -> List[Alert]:
    """Get active alerts"""
    
@app.post("/api/v1/actions/{action_id}/approve")
async def approve_action(action_id: str) -> ExecutionResult:
    """Approve and execute mitigation action"""
    
@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    """WebSocket for real-time updates"""
    await websocket.accept()
    
    # Subscribe to MQTT alerts and forward to WebSocket
    mqtt_client.subscribe("campus-water/alerts/#")
    
    while True:
        # Forward MQTT messages to WebSocket
        message = await mqtt_queue.get()
        await websocket.send_json(message)
```

**WebSocket Message Format**:
```json
{
    "type": "sensor_update|alert|action|state_change",
    "timestamp": "2024-01-15T10:30:00Z",
    "data": {
        // Type-specific data
    }
}
```


## Data Models

### Sensor Data Models

```python
class SensorReading:
    sensor_id: str
    timestamp: datetime
    value: float
    unit: str
    quality_score: float  # 0.0-1.0
    flags: List[str]  # e.g., ["interpolated", "anomaly"]

class FlowSensorReading(SensorReading):
    flow_rate: float  # liters/second
    cumulative_volume: float  # liters
    direction: str  # "forward" or "reverse"

class PressureSensorReading(SensorReading):
    pressure: float  # PSI
    
class WaterQualitySensorReading(SensorReading):
    ph: Optional[float]
    turbidity: Optional[float]  # NTU
    chlorine_residual: Optional[float]  # mg/L
    conductivity: Optional[float]  # μS/cm
    temperature: Optional[float]  # Celsius

class LevelSensorReading(SensorReading):
    level: float  # meters
    volume: float  # cubic meters
    percentage: float  # 0-100

class ValvePositionReading(SensorReading):
    position: float  # 0-100 (percentage open)
    status: str  # "open", "closed", "partial", "moving", "fault"
```

### Infrastructure Models

```python
class Component:
    component_id: str
    component_type: str  # "pipe", "valve", "pump", "tank", "node"
    location: GeoLocation
    properties: Dict[str, Any]
    status: ComponentStatus
    last_updated: datetime

class Pipe(Component):
    start_node: str
    end_node: str
    length: float  # meters
    diameter: float  # millimeters
    material: str
    roughness: float  # Hazen-Williams C factor
    install_date: date

class Valve(Component):
    node_id: str
    valve_type: str  # "isolation", "pressure_reducing", "check"
    current_position: float  # 0-100
    controllable: bool
    actuation_time: float  # seconds to fully open/close

class Pump(Component):
    node_id: str
    pump_curve: List[Tuple[float, float]]  # (flow, head) points
    power_rating: float  # kW
    efficiency: float  # percentage
    speed_controllable: bool
    current_speed: float  # percentage of max

class Tank(Component):
    node_id: str
    min_level: float  # meters
    max_level: float  # meters
    diameter: float  # meters
    current_level: float  # meters
    current_volume: float  # cubic meters

class Node(Component):
    elevation: float  # meters
    base_demand: float  # liters/second
    demand_pattern: str  # reference to demand pattern
    zone_id: str
```


### Network State Models

```python
class NetworkState:
    timestamp: datetime
    components: Dict[str, Component]
    zones: Dict[str, Zone]
    active_alerts: List[Alert]
    system_metrics: SystemMetrics

class Zone:
    zone_id: str
    name: str
    nodes: List[str]
    isolation_valves: List[str]
    population_served: int
    status: str  # "normal", "isolated", "contaminated", "low_pressure"

class SystemMetrics:
    total_flow: float  # liters/second
    average_pressure: float  # PSI
    water_loss_percentage: float
    active_pumps: int
    active_alerts: int
    system_health_score: float  # 0-100
```

### Alert and Action Models

```python
class Alert:
    alert_id: str
    timestamp: datetime
    alert_type: str  # "contamination", "leak", "pressure", "maintenance"
    severity: str  # "critical", "high", "medium", "low"
    source_agent: str
    affected_components: List[str]
    affected_zones: List[str]
    description: str
    recommended_actions: List[str]
    status: str  # "active", "acknowledged", "resolved"

class MitigationAction:
    action_id: str
    action_type: str  # "close_valve", "adjust_pump", "isolate_zone"
    target_components: List[str]
    parameters: Dict[str, Any]
    priority: str
    requires_approval: bool
    estimated_impact: str
    safety_validated: bool
    execution_status: str  # "pending", "approved", "executing", "completed", "failed"

class ContaminationAlert(Alert):
    contamination_source: str
    contamination_type: str
    affected_population: int
    spread_prediction: ContaminationSpread

class LeakAlert(Alert):
    estimated_location: GeoLocation
    location_radius: float  # meters
    estimated_flow_loss: float  # liters/second
    leak_severity: str  # "minor", "moderate", "critical"

class PressureAlert(Alert):
    pressure_type: str  # "low", "high"
    affected_nodes: List[str]
    root_cause: str
    pressure_values: Dict[str, float]

class MaintenanceAlert(Alert):
    equipment_id: str
    failure_probability: float
    time_to_failure_days: int
    confidence_interval: Tuple[int, int]
    maintenance_priority: int
```


### Simulation Models

```python
class SimulationResult:
    simulation_id: str
    timestamp: datetime
    scenario: str
    duration: timedelta
    node_pressures: Dict[str, List[float]]  # node_id -> pressure time series
    pipe_flows: Dict[str, List[float]]  # pipe_id -> flow time series
    tank_levels: Dict[str, List[float]]  # tank_id -> level time series
    warnings: List[str]
    convergence_achieved: bool

class ContaminationSpread:
    source_node: str
    start_time: datetime
    affected_nodes_timeline: Dict[datetime, List[str]]
    concentration_map: Dict[str, Dict[datetime, float]]
    population_at_risk: int

class FailurePrediction:
    equipment_id: str
    failure_probability: float
    time_to_failure_days: int
    confidence_lower: int
    confidence_upper: int
    contributing_factors: List[str]
    recommended_actions: List[str]
```

### Database Schemas

**InfluxDB Schema**:

```
# Bucket: campus_water
# Retention: 90 days for raw data, infinite for downsampled

# Measurement: sensor_readings
# Tags:
#   - sensor_id: unique sensor identifier
#   - location: building or zone identifier
#   - sensor_type: level, flow, quality, valve
# Fields:
#   - value: float (sensor reading)
#   - quality_score: float (0.0-1.0)
#   - flags: string (comma-separated flags)
# Timestamp: nanosecond precision

# Example Flux query for recent readings:
from(bucket: "campus_water")
  |> range(start: -1h)
  |> filter(fn: (r) => r._measurement == "sensor_readings")
  |> filter(fn: (r) => r.sensor_type == "level")
  |> aggregateWindow(every: 1m, fn: mean)

# Measurement: alerts
# Tags:
#   - alert_type: contamination, leak, pressure, maintenance
#   - severity: critical, high, medium, low
#   - source_agent: agent identifier
# Fields:
#   - description: string
#   - affected_components: string (JSON array)
#   - status: string (active, acknowledged, resolved)
# Timestamp: alert creation time

# Measurement: actions
# Tags:
#   - action_type: close_valve, open_valve, etc.
#   - execution_status: pending, executing, completed, failed
# Fields:
#   - target_components: string (JSON array)
#   - reasoning: string (Gemini explanation)
#   - executed_by: string (agent or user)
# Timestamp: action execution time

# Downsampling tasks (automated):
# - 1-minute averages retained for 1 year
# - 1-hour averages retained for 5 years
# - Daily summaries retained indefinitely
```

**In-Memory State** (Python dictionaries):

```python
# Network state (in-memory, periodically persisted to InfluxDB)
network_state = {
    "components": {
        "tank-001": {
            "type": "tank",
            "location": {"lat": 37.7749, "lon": -122.4194},
            "current_level": 2.5,  # meters
            "capacity": 10000,  # liters
            "status": "normal",
            "last_updated": datetime.now()
        },
        "valve-001": {
            "type": "valve",
            "location": {"lat": 37.7750, "lon": -122.4195},
            "position": "open",
            "controllable": True,
            "status": "normal",
            "last_updated": datetime.now()
        }
    },
    "zones": {
        "building-a": {
            "nodes": ["tank-001", "valve-001"],
            "status": "normal",
            "population_served": 500
        }
    },
    "active_alerts": [],
    "system_metrics": {
        "total_flow": 150.0,  # L/s
        "active_alerts": 0,
        "system_health_score": 95.0
    }
}
```


## Correctness Properties

A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.

### Property Reflection

After analyzing all acceptance criteria, I identified the following redundancies:
- Requirements 11.5 and 14.2 both address agent failure and responsibility redistribution - these will be combined into a single property
- Several timing requirements (2.5, 3.1, 10.7, 12.4, 15.7) are performance metrics better tested through performance testing rather than property-based testing
- Multiple alert generation properties (5.1, 6.1, 7.1, 7.2, 8.2) follow the same pattern and can be generalized

The following properties represent unique, testable behaviors that provide comprehensive validation coverage.

### Data Layer Properties

**Property 1: Invalid sensor data flagging**
*For any* sensor reading that is missing or has invalid values, the Data Layer should flag it with a data quality issue and provide either an interpolated value or the last-known-good value.
**Validates: Requirements 3.3**

**Property 2: Sensor range validation**
*For any* sensor reading outside its expected range, the Data Layer should flag it as anomalous.
**Validates: Requirements 3.5**

### Agent Communication Properties

**Property 3: Message delivery guarantee**
*For any* message sent from one agent to another through the message-passing architecture, the message should be delivered exactly once.
**Validates: Requirements 4.8**

**Property 4: Conflict resolution priority**
*For any* set of conflicting mitigation actions from multiple agents, the Coordination Agent's resolution should follow the priority hierarchy: public health > safety > asset protection > efficiency.
**Validates: Requirements 4.7, 11.2**

### Water Quality Monitoring Properties

**Property 5: Contamination alert generation**
*For any* water quality reading where parameters exceed safe thresholds (pH outside 6.5-8.5, turbidity >5 NTU, chlorine outside 0.2-4.0 mg/L, conductivity beyond baseline ±20%, temperature outside 4-25°C), a contamination alert should be generated.
**Validates: Requirements 5.1**

**Property 6: Contamination zone identification**
*For any* contamination event at a source node, the affected zones identified should include all nodes reachable through hydraulic flow paths from the contamination source.
**Validates: Requirements 5.3**

**Property 7: Contamination isolation recommendations**
*For any* contamination event, the recommended valve closures should isolate all affected zones from unaffected zones.
**Validates: Requirements 5.4**

### Leak Detection Properties

**Property 8: Leak alert generation**
*For any* flow pattern that indicates a leak (sustained flow above minimum night flow baseline with correlated pressure drops), a leak alert should be generated.
**Validates: Requirements 6.1**

**Property 9: Leak severity classification**
*For any* detected leak, it should be classified as minor (<50 L/min), moderate (50-200 L/min), or critical (>200 L/min) based on estimated flow loss.
**Validates: Requirements 6.3**

**Property 10: Critical leak mitigation**
*For any* leak classified as critical, valve closure recommendations should be generated to minimize water loss.
**Validates: Requirements 6.4**

**Property 11: Demand change distinction**
*For any* legitimate demand change pattern (gradual increase/decrease correlated with time-of-day patterns), it should not be classified as a leak event.
**Validates: Requirements 6.6**

### Pressure Management Properties

**Property 12: Low pressure alert generation**
*For any* pressure reading below 20 PSI at any node, a low-pressure alert should be generated.
**Validates: Requirements 7.1**

**Property 13: High pressure alert generation**
*For any* pressure reading above 80 PSI at any node, a high-pressure alert should be generated.
**Validates: Requirements 7.2**

**Property 14: Pressure anomaly root cause identification**
*For any* pressure anomaly, a root cause should be identified from the set of possible causes (pump failure, valve closure, demand surge, pipe break, tank level issue).
**Validates: Requirements 7.3**

**Property 15: Pressure anomaly corrective actions**
*For any* pressure anomaly with identified root cause, corrective action recommendations should be generated (pump adjustments, valve operations, or system reconfigurations).
**Validates: Requirements 7.4**

**Property 16: Valve operation pressure prediction**
*For any* proposed valve operation, pressure impact predictions should be generated for all affected nodes using hydraulic simulation.
**Validates: Requirements 7.6**

### Predictive Maintenance Properties

**Property 17: High failure probability alerts**
*For any* equipment with failure probability exceeding 70% within 30 days, a maintenance alert should be generated.
**Validates: Requirements 8.2**

**Property 18: Failure prediction completeness**
*For any* failure prediction, it should include estimated time-to-failure and confidence intervals (lower and upper bounds).
**Validates: Requirements 8.3**

**Property 19: Maintenance prioritization**
*For any* set of maintenance alerts, they should be ordered by priority based on criticality score (combining failure probability, time-to-failure, and equipment criticality).
**Validates: Requirements 8.4**

### Emergency Response Properties

**Property 20: Critical danger mitigation execution**
*For any* critical danger event (contamination, critical leak, severe pressure failure), pre-approved mitigation actions should be executed.
**Validates: Requirements 9.1**

**Property 21: Mitigation action simulation validation**
*For any* mitigation action before execution, it should be validated using Digital Twin simulation to verify feasibility and predict outcomes.
**Validates: Requirements 9.3**

**Property 22: Action logging completeness**
*For any* executed mitigation action, an audit log entry should exist containing timestamp, action type, target components, reasoning, and expected outcomes.
**Validates: Requirements 9.4**

**Property 23: Operator notification on action execution**
*For any* executed mitigation action, operator notifications should be sent through all configured channels.
**Validates: Requirements 9.5**

**Property 24: Safety interlock enforcement**
*For any* proposed mitigation action that would worsen the current situation (based on simulation predictions), the action should be blocked by safety interlocks.
**Validates: Requirements 9.6**

### Interface Layer Properties

**Property 25: Component detail completeness**
*For any* infrastructure component selected by an operator, the displayed information should include current status, latest sensor readings, and historical trend data.
**Validates: Requirements 10.3**

**Property 26: Visualization update responsiveness**
*For any* system state change, the interface visualization should reflect the change within the update cycle.
**Validates: Requirements 10.7**

### Coordination Properties

**Property 27: Conflict resolution timeliness**
*For any* set of conflicting mitigation actions from multiple agents, the Coordination Agent should produce a resolution.
**Validates: Requirements 11.1**

**Property 28: Multi-agent collaboration orchestration**
*For any* complex scenario requiring multiple agents, the Coordination Agent should generate a multi-step response plan that coordinates agent actions.
**Validates: Requirements 11.3**

**Property 29: Shared world model consistency**
*For any* two agents querying the shared world model at the same time, they should receive consistent state information.
**Validates: Requirements 11.4**

**Property 30: Agent failure responsibility redistribution**
*For any* agent that fails or becomes unresponsive, its monitoring and response responsibilities should be redistributed to remaining operational agents.
**Validates: Requirements 11.5, 14.2**

**Property 31: Conflict resolution logging**
*For any* conflict resolution performed by the Coordination Agent, an audit log entry should exist containing the conflicting actions, resolution decision, and reasoning.
**Validates: Requirements 11.6**

### Security Properties

**Property 32: Role-based access control**
*For any* user with an assigned role (operator, engineer, administrator, read-only), their access permissions should match the defined permissions for that role.
**Validates: Requirements 13.1**

**Property 33: Unauthorized access logging and alerting**
*For any* unauthorized access attempt, both an audit log entry and a security alert should be created.
**Validates: Requirements 13.4**

**Property 34: Multi-factor authentication for infrastructure modifications**
*For any* action that modifies physical infrastructure (valve operations, pump adjustments), multi-factor authentication should be required.
**Validates: Requirements 13.5**

**Property 35: Audit log immutability**
*For any* user action or system decision, an audit log entry should be created that cannot be modified or deleted.
**Validates: Requirements 13.6**

### Resilience Properties

**Property 36: Component failover**
*For any* Data Layer component failure, the system should failover to a redundant component and continue operation.
**Validates: Requirements 14.1**

**Property 37: Operation with sensor loss**
*For any* scenario with up to 30% of sensors offline, the system should continue monitoring using model-based state estimation for missing sensor data.
**Validates: Requirements 14.3**

**Property 38: Network partition resilience**
*For any* network connectivity loss, the system should continue local monitoring and queue mitigation actions for synchronization when connectivity is restored.
**Validates: Requirements 14.5**

### Integration Properties

**Property 39: Event bus publication**
*For any* alert or system event, it should be published to the campus-wide event bus in the configured protocol format (MQTT or AMQP).
**Validates: Requirements 15.2**

**Property 40: API response performance**
*For any* external system data request, the system should respond within the performance threshold for the majority of requests.
**Validates: Requirements 15.7**

### Reporting Properties

**Property 41: Data export format validity**
*For any* data export operation, the exported file should be valid according to the requested format specification (CSV, JSON, or PDF).
**Validates: Requirements 16.4**

**Property 42: KPI calculation correctness**
*For any* time period, calculated KPIs (water loss percentage, response times, system availability) should be mathematically correct based on the underlying data.
**Validates: Requirements 16.5**


## Error Handling

### Data Layer Error Handling

**Sensor Communication Errors**:
- Connection timeout: Retry with exponential backoff (1s, 2s, 4s, 8s), mark sensor offline after 5 failures
- Malformed messages: Log error, increment error counter, discard message
- Authentication failures: Alert security team, block sensor until manual review

**Data Quality Errors**:
- Out-of-range values: Flag as anomalous, use interpolation or last-known-good value
- Missing data: Interpolate from neighboring sensors if available, otherwise use model prediction
- Timestamp errors: Reject messages with timestamps >5 minutes in future or >1 hour in past

**Database Errors**:
- Write failures: Queue data in memory buffer, retry writes, alert if buffer exceeds threshold
- Query timeouts: Return cached data with staleness indicator, log slow query for optimization
- Connection loss: Failover to replica database, alert operations team

### Model Layer Error Handling

**Simulation Errors**:
- Non-convergence: Relax convergence criteria, use last successful simulation result, alert engineer
- Invalid network topology: Validate topology on load, reject invalid configurations
- Numerical instability: Switch to more stable solver, reduce time step, log warning

**Prediction Model Errors**:
- Model inference failures: Fall back to rule-based heuristics, alert ML team
- Confidence below threshold: Flag prediction as uncertain, require human review
- Model version mismatch: Use compatible model version, schedule model update

**State Synchronization Errors**:
- Redis connection loss: Use local cache, queue state updates, reconnect with exponential backoff
- State inconsistency detected: Trigger state reconciliation, log discrepancy
- Update conflicts: Use timestamp-based conflict resolution (last-write-wins)

### Intelligence Layer Error Handling

**Agent Errors**:
- Agent crash: Coordination Agent detects via heartbeat timeout, redistributes responsibilities
- Message processing failure: Log error, send message to dead-letter queue for analysis
- Infinite loop detection: Timeout agent operations after 30 seconds, restart agent

**Coordination Errors**:
- Conflict resolution timeout: Escalate to human operator with all conflicting actions
- Circular dependencies: Detect cycles, break by priority, alert system architect
- Resource exhaustion: Throttle agent message rates, prioritize critical alerts

**Action Execution Errors**:
- Valve operation failure: Retry once, mark valve as faulty, recommend manual intervention
- Simulation validation failure: Block action, alert operator, log reason
- Safety interlock violation: Block action immediately, log violation, alert safety officer

### Interface Layer Error Handling

**API Errors**:
- Invalid requests: Return 400 Bad Request with detailed error message
- Authentication failures: Return 401 Unauthorized, log attempt
- Rate limit exceeded: Return 429 Too Many Requests with retry-after header
- Internal errors: Return 500 Internal Server Error, log full stack trace

**WebSocket Errors**:
- Connection loss: Client auto-reconnects with exponential backoff
- Message delivery failure: Queue messages, deliver on reconnection
- Protocol errors: Close connection, log error, require client reconnection

**Visualization Errors**:
- Rendering failures: Show error placeholder, log error, continue rendering other components
- Data loading timeout: Show loading indicator, retry with exponential backoff
- Invalid data format: Show error message, log issue, skip invalid data

### Error Recovery Strategies

**Graceful Degradation**:
- Continue operation with reduced functionality when components fail
- Use cached data when real-time data unavailable
- Fall back to manual control when automation fails

**Circuit Breaker Pattern**:
- Detect repeated failures to external services
- Open circuit after threshold (5 failures in 1 minute)
- Half-open after cooldown period (30 seconds)
- Close circuit after successful operations

**Retry Policies**:
- Transient errors: Exponential backoff with jitter
- Network errors: Retry up to 3 times
- Database errors: Retry up to 5 times with increasing delays
- Never retry: Authentication failures, validation errors

**Error Notification**:
- Critical errors: Immediate notification to on-call engineer via SMS/phone
- High priority: Email and dashboard alert within 1 minute
- Medium priority: Dashboard alert, daily summary email
- Low priority: Log only, weekly summary report


## Testing Strategy

### Dual Testing Approach

The system requires both unit testing and property-based testing for comprehensive validation:

**Unit Tests**: Verify specific examples, edge cases, and error conditions
- Specific sensor reading scenarios
- Known contamination events
- Edge cases (empty data, boundary values)
- Integration points between components
- Error handling paths

**Property Tests**: Verify universal properties across all inputs
- Universal correctness properties from the design
- Comprehensive input coverage through randomization
- Validation of system invariants
- Testing across wide range of scenarios

Both approaches are complementary and necessary. Unit tests catch concrete bugs in specific scenarios, while property tests verify general correctness across all possible inputs.

### Property-Based Testing Configuration

**Testing Library**: Hypothesis (Python)
- Industry-standard property-based testing framework
- Excellent support for complex data generation
- Shrinking capability to find minimal failing examples
- Integration with pytest

**Test Configuration**:
- Minimum 100 iterations per property test (due to randomization)
- Deadline: 30 seconds per test
- Mock MQTT broker for testing
- Mock Gemini API responses for deterministic tests
- In-memory InfluxDB for fast test execution

**Property Test Structure**:
```python
from hypothesis import given, strategies as st
import pytest
from unittest.mock import Mock, patch

@given(
    sensor_reading=st.builds(
        SensorReading,
        sensor_id=st.text(min_size=1),
        value=st.floats(allow_nan=False, allow_infinity=False),
        timestamp=st.datetimes()
    )
)
@pytest.mark.property_test
def test_property_1_invalid_sensor_data_flagging(sensor_reading, mock_influxdb):
    """
    Feature: campus-water-digital-twin
    Property 1: For any sensor reading that is missing or has invalid values,
    the Data Layer should flag it with a data quality issue and provide either
    an interpolated value or the last-known-good value.
    Validates: Requirements 3.3
    """
    # Test implementation with mocked dependencies
    pass

@given(
    sensor_data=st.lists(st.builds(SensorReading), min_size=10, max_size=100),
    context=st.text()
)
@patch('gemini_service.GeminiIntegrationService.analyze_sensor_pattern')
def test_property_5_contamination_alert_generation(mock_gemini, sensor_data, context):
    """
    Feature: campus-water-digital-twin
    Property 5: For any water quality reading where parameters exceed safe thresholds,
    a contamination alert should be generated.
    Validates: Requirements 5.1
    """
    # Mock Gemini response
    mock_gemini.return_value = AnalysisResult(contamination_detected=True)
    # Test implementation
    pass
```

**Tag Format**: Each property test must include a docstring with:
- Feature name: `campus-water-digital-twin`
- Property number and description
- Requirements validation reference

### Test Data Generation Strategies

**Sensor Data Generation**:
```python
# Valid ultrasonic level readings
valid_level_reading = st.builds(
    LevelSensorReading,
    sensor_id=st.text(min_size=1, max_size=50),
    level=st.floats(min_value=0, max_value=10),  # meters
    timestamp=st.datetimes(min_value=datetime(2020, 1, 1))
)

# Invalid sensor readings (for testing error handling)
invalid_sensor_reading = st.builds(
    SensorReading,
    value=st.one_of(st.none(), st.floats(allow_nan=True, allow_infinity=True))
)

# Water quality readings with contamination
contaminated_reading = st.builds(
    WaterQualitySensorReading,
    ph=st.floats(min_value=0, max_value=14).filter(lambda x: x < 6.5 or x > 8.5),
    turbidity=st.floats(min_value=5, max_value=100)
)
```

**MQTT Message Generation**:
```python
# Generate MQTT messages
mqtt_message = st.builds(
    MQTTMessage,
    topic=st.sampled_from([
        "campus-water/sensors/building-a/level",
        "campus-water/sensors/building-b/level",
        "campus-water/alerts/contamination"
    ]),
    payload=st.binary()
)
```

**Gemini Response Generation** (for mocking):
```python
# Mock Gemini API responses
gemini_analysis = st.builds(
    AnalysisResult,
    contamination_detected=st.booleans(),
    confidence=st.floats(min_value=0.0, max_value=1.0),
    explanation=st.text(min_size=10, max_size=500),
    recommended_actions=st.lists(st.text(), max_size=5)
)
```

### Unit Testing Strategy

**Test Organization**:
```
tests/
├── unit/
│   ├── perception/
│   │   └── test_esp32_firmware.cpp  # Arduino unit tests
│   ├── transmission/
│   │   ├── test_mqtt_bridge.py
│   │   └── test_data_quality.py
│   ├── intelligence/
│   │   ├── test_gemini_integration.py
│   │   ├── test_water_quality_agent.py
│   │   ├── test_leak_detection_agent.py
│   │   ├── test_pressure_agent.py
│   │   ├── test_maintenance_agent.py
│   │   ├── test_emergency_response_agent.py
│   │   └── test_coordination_agent.py
│   └── visualization/
│       ├── test_api.py
│       └── test_three_scene.test.ts
├── integration/
│   ├── test_end_to_end_contamination.py
│   ├── test_end_to_end_leak.py
│   ├── test_mqtt_to_influxdb.py
│   └── test_agent_coordination.py
└── property/
    ├── test_data_layer_properties.py
    ├── test_agent_properties.py
    ├── test_coordination_properties.py
    ├── test_security_properties.py
    └── test_resilience_properties.py
```

**Unit Test Examples**:

```python
# Example: Test Gemini integration
@patch('google.generativeai.GenerativeModel')
def test_gemini_contamination_analysis(mock_gemini):
    """Test Gemini API integration for contamination analysis"""
    mock_response = Mock()
    mock_response.text = "Contamination detected: pH level critically low"
    mock_gemini.return_value.generate_content.return_value = mock_response
    
    service = GeminiIntegrationService(api_key="test-key")
    reading = WaterQualitySensorReading(
        sensor_id="WQ-001",
        ph=5.0,
        timestamp=datetime.now()
    )
    
    result = service.analyze_sensor_pattern([reading], "Check for contamination")
    
    assert result.contamination_detected
    assert "pH" in result.explanation

# Example: Test MQTT message handling
def test_mqtt_bridge_sensor_message(mock_influxdb):
    """Test MQTT bridge processes sensor messages correctly"""
    bridge = MQTTBridgeService(influxdb_client=mock_influxdb)
    
    message = Mock()
    message.topic = "campus-water/sensors/building-a/level"
    message.payload = json.dumps({"value": 2.5, "timestamp": time.time()})
    
    bridge.on_message(None, None, message)
    
    # Verify data written to InfluxDB
    assert mock_influxdb.write.called
    written_data = mock_influxdb.write.call_args[0][0]
    assert written_data["measurement"] == "sensor_readings"
    assert written_data["fields"]["value"] == 2.5

# Example: Test ESP32 firmware (Arduino)
// test_esp32_firmware.cpp
#include <unity.h>
#include "sensor_node.h"

void test_ultrasonic_reading() {
    SensorNode node;
    float level = node.readUltrasonicSensor();
    TEST_ASSERT_GREATER_THAN(0, level);
    TEST_ASSERT_LESS_THAN(10, level);
}

void test_valve_control() {
    SensorNode node;
    node.controlValve(true);  // Open
    TEST_ASSERT_EQUAL(HIGH, digitalRead(VALVE_PIN));
    
    node.controlValve(false);  // Close
    TEST_ASSERT_EQUAL(LOW, digitalRead(VALVE_PIN));
}
```

**Frontend Unit Tests** (React + Three.js):
```typescript
// test_three_scene.test.ts
import { render } from '@testing-library/react';
import { WaterNetworkScene } from './WaterNetworkScene';

describe('WaterNetworkScene', () => {
    it('renders tank with correct level', () => {
        const tank = {
            id: 'tank-001',
            type: 'tank',
            current_level: 2.5,
            capacity: 10000
        };
        
        const scene = new WaterNetworkScene(document.createElement('div'));
        scene.addTank(tank);
        
        const tankMesh = scene.scene.getObjectByName('tank-001');
        expect(tankMesh).toBeDefined();
        expect(tankMesh.userData.level).toBe(2.5);
    });
    
    it('updates component status color', () => {
        const scene = new WaterNetworkScene(document.createElement('div'));
        scene.addTank({ id: 'tank-001', status: 'normal' });
        
        scene.updateComponentStatus('tank-001', 'critical');
        
        const tankMesh = scene.scene.getObjectByName('tank-001');
        expect(tankMesh.material.color.getHex()).toBe(0xff0000); // Red
    });
});
```

### Integration Testing

**End-to-End Scenarios**:
1. Contamination detection and valve isolation
2. Leak detection from level sensors
3. Multi-agent coordination during complex event
4. System recovery after MQTT broker restart
5. Gemini API failure handling

**Integration Test Example**:
```python
@pytest.mark.integration
async def test_contamination_end_to_end():
    """Test complete contamination detection and response flow"""
    # Setup: Start MQTT broker, InfluxDB, and agents
    system = await setup_test_system()
    
    # Inject contamination event via MQTT
    mqtt_client = mqtt.Client()
    mqtt_client.connect("localhost", 1883)
    
    contaminated_reading = {
        "sensor_id": "WQ-BUILDING-A",
        "ph": 5.0,  # Below safe threshold
        "timestamp": time.time()
    }
    mqtt_client.publish(
        "campus-water/sensors/building-a/quality",
        json.dumps(contaminated_reading)
    )
    
    # Wait for agent processing
    await asyncio.sleep(2)
    
    # Verify alert generated and stored in InfluxDB
    alerts = await system.query_influxdb_alerts()
    assert any(a["alert_type"] == "contamination" for a in alerts)
    
    # Verify valve closure command published
    valve_commands = system.get_mqtt_messages("campus-water/actuators/+/valve/command")
    assert len(valve_commands) > 0
    assert valve_commands[0]["command"] == "close"
    
    # Verify WebSocket notification sent
    ws_messages = system.get_websocket_messages()
    assert any(m["type"] == "alert" for m in ws_messages)

@pytest.mark.integration
def test_mqtt_to_influxdb_pipeline():
    """Test data flows from MQTT to InfluxDB correctly"""
    # Publish sensor data via MQTT
    mqtt_client = mqtt.Client()
    mqtt_client.connect("localhost", 1883)
    
    for i in range(10):
        mqtt_client.publish(
            "campus-water/sensors/test/level",
            json.dumps({"value": 2.0 + i * 0.1, "timestamp": time.time()})
        )
        time.sleep(0.1)
    
    # Wait for bridge to process
    time.sleep(2)
    
    # Query InfluxDB
    influx_client = InfluxDBClient(url="http://localhost:8086", token="test-token")
    query = '''
        from(bucket: "campus_water")
        |> range(start: -1m)
        |> filter(fn: (r) => r.sensor_id == "test")
    '''
    result = influx_client.query_api().query(query)
    
    # Verify all data points stored
    assert len(result) == 10
```

### Performance Testing

**Load Testing**:
- Simulate 100 ESP32 nodes publishing at 1Hz
- Verify MQTT broker handles load
- Verify InfluxDB write performance
- Verify agent processing latency

**Stress Testing**:
- Test with multiple simultaneous alerts
- Test Gemini API rate limiting
- Test WebSocket connection limits
- Test system behavior under network partition

**Performance Test Example**:
```python
@pytest.mark.performance
def test_mqtt_throughput():
    """Test MQTT broker can handle 100 sensors at 1Hz"""
    import threading
    
    def publish_sensor_data(sensor_id):
        client = mqtt.Client()
        client.connect("localhost", 1883)
        for _ in range(60):  # 1 minute
            client.publish(
                f"campus-water/sensors/{sensor_id}/level",
                json.dumps({"value": random.uniform(1.0, 3.0), "timestamp": time.time()})
            )
            time.sleep(1)
    
    # Start 100 sensor threads
    threads = []
    for i in range(100):
        t = threading.Thread(target=publish_sensor_data, args=(f"sensor-{i}",))
        t.start()
        threads.append(t)
    
    # Wait for completion
    for t in threads:
        t.join()
    
    # Verify all messages processed
    # Check InfluxDB for 6000 data points (100 sensors * 60 seconds)
    # Verify no message loss
```

### Continuous Integration

**CI Pipeline**:
1. Lint and format check (black, flake8, mypy)
2. Unit tests (fast, run on every commit)
3. Property tests (run on every commit, 100 iterations)
4. Integration tests (run on every PR)
5. Performance tests (run nightly)
6. Security scans (run on every PR)

**Test Coverage Requirements**:
- Minimum 80% code coverage for unit tests
- All 42 correctness properties must have property tests
- All critical paths must have integration tests
- All error handling paths must be tested

**Test Execution Time**:
- Unit tests: <2 minutes
- Property tests: <10 minutes
- Integration tests: <15 minutes
- Full suite: <30 minutes


## Deployment Architecture

### Docker Compose Deployment

For rapid prototyping, the system uses Docker Compose for simple deployment:

```yaml
version: '3.8'

services:
  mosquitto:
    image: eclipse-mosquitto:2
    ports:
      - "1883:1883"
      - "9001:9001"
    volumes:
      - ./mosquitto/config:/mosquitto/config
      - ./mosquitto/data:/mosquitto/data
      - ./mosquitto/log:/mosquitto/log
    restart: unless-stopped

  influxdb:
    image: influxdb:2.7
    ports:
      - "8086:8086"
    environment:
      - DOCKER_INFLUXDB_INIT_MODE=setup
      - DOCKER_INFLUXDB_INIT_USERNAME=admin
      - DOCKER_INFLUXDB_INIT_PASSWORD=adminpassword
      - DOCKER_INFLUXDB_INIT_ORG=campus-water
      - DOCKER_INFLUXDB_INIT_BUCKET=campus_water
    volumes:
      - influxdb-data:/var/lib/influxdb2
    restart: unless-stopped

  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      - MQTT_BROKER=mosquitto
      - INFLUXDB_URL=http://influxdb:8086
      - GEMINI_API_KEY=${GEMINI_API_KEY}
    depends_on:
      - mosquitto
      - influxdb
    restart: unless-stopped

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    environment:
      - VITE_API_URL=http://localhost:8000
      - VITE_WS_URL=ws://localhost:8000
    depends_on:
      - backend
    restart: unless-stopped

volumes:
  influxdb-data:
```

### ESP32 Deployment

**Firmware Upload**:
```bash
# Using Arduino IDE or PlatformIO
pio run --target upload --upload-port /dev/ttyUSB0

# Or using esptool
esptool.py --port /dev/ttyUSB0 write_flash 0x1000 firmware.bin
```

**Configuration** (stored in SPIFFS):
```json
{
    "wifi_ssid": "CampusWiFi",
    "wifi_password": "...",
    "mqtt_broker": "mqtt.campus.edu",
    "mqtt_port": 1883,
    "mqtt_username": "sensor-node-001",
    "mqtt_password": "...",
    "location": "building-a-tank",
    "sensor_type": "ultrasonic",
    "publish_interval_ms": 1000
}
```

### High Availability Configuration

**MQTT Broker**:
- Run multiple Mosquitto instances with bridge configuration
- Use DNS round-robin for load distribution
- Persistent sessions for reliable delivery

**InfluxDB**:
- Single instance sufficient for prototype
- Regular backups to S3-compatible storage
- Can upgrade to InfluxDB Enterprise for clustering

**Backend Services**:
- Run multiple backend instances behind nginx
- Sticky sessions for WebSocket connections
- Health checks and automatic restart

### Monitoring and Observability

**Metrics Collection**:
```python
# Prometheus metrics in backend
from prometheus_client import Counter, Histogram, Gauge

sensor_messages_received = Counter('sensor_messages_total', 'Total sensor messages received')
alert_generation_time = Histogram('alert_generation_seconds', 'Time to generate alert')
active_alerts = Gauge('active_alerts', 'Number of active alerts')
gemini_api_calls = Counter('gemini_api_calls_total', 'Total Gemini API calls')
```

**Logging**:
```python
import logging
import structlog

# Structured logging
logger = structlog.get_logger()
logger.info("sensor_reading_received", 
            sensor_id="tank-001", 
            value=2.5, 
            quality_score=0.95)
```

**Dashboards**:
- Grafana connected to InfluxDB for system metrics
- Custom dashboard for sensor health
- Alert history and response time tracking

### Security Configuration

**MQTT Security**:
```conf
# mosquitto.conf
listener 1883
allow_anonymous false
password_file /mosquitto/config/passwd

# TLS configuration
listener 8883
cafile /mosquitto/certs/ca.crt
certfile /mosquitto/certs/server.crt
keyfile /mosquitto/certs/server.key
```

**API Security**:
```python
from fastapi.security import HTTPBearer
from jose import jwt

security = HTTPBearer()

@app.get("/api/v1/protected")
async def protected_route(credentials: HTTPAuthorizationCredentials = Depends(security)):
    token = credentials.credentials
    payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
    return {"user": payload["sub"]}
```

**ESP32 Security**:
- Store credentials in encrypted SPIFFS partition
- Use TLS for MQTT connections in production
- Implement certificate-based authentication

### Backup and Recovery

**Backup Strategy**:
```bash
# InfluxDB backup (daily cron job)
docker exec influxdb influx backup /backup/$(date +%Y%m%d)

# Mosquitto persistence backup
tar -czf mosquitto-backup-$(date +%Y%m%d).tar.gz ./mosquitto/data

# Configuration backup
git commit -am "Daily config backup" && git push
```

**Recovery Procedures**:
1. Stop services: `docker-compose down`
2. Restore InfluxDB data from backup
3. Restore Mosquitto persistence
4. Restart services: `docker-compose up -d`
5. Verify sensor connectivity
6. Check agent operation

### Scaling Strategy

**Horizontal Scaling**:
- Add more backend instances behind load balancer
- Partition MQTT topics across multiple brokers
- Use InfluxDB Enterprise for distributed storage

**Vertical Scaling**:
- Increase InfluxDB memory for larger datasets
- Add CPU cores for Gemini API processing
- Upgrade network bandwidth for more sensors

**Edge Deployment**:
- Deploy on Raspberry Pi 4 or similar edge device
- Suitable for small to medium campus deployments
- Can handle 100-500 sensors per instance

## Implementation Considerations

### Technology Choices Rationale

**Why MQTT over Kafka**:
- Lightweight protocol designed for IoT devices
- Much faster to set up for prototyping
- Native support in ESP32 libraries
- Lower resource requirements
- QoS levels for reliable delivery
- Perfect for sensor data streaming

**Why InfluxDB over TimescaleDB**:
- Purpose-built for time-series IoT data
- Simpler setup and configuration
- Better performance for high-frequency writes
- Built-in downsampling and retention policies
- Flux query language optimized for time-series
- No need for PostgreSQL complexity

**Why Gemini 2.0 Flash over custom ML**:
- Advanced reasoning without training data
- Natural language explanations for operators
- Multimodal analysis capabilities
- Fast inference for real-time decisions
- No need to collect training datasets
- Rapid prototyping and iteration

**Why Three.js over Cesium**:
- Lighter weight for campus-scale visualization
- More flexible for custom rendering
- Better performance for real-time updates
- Simpler learning curve
- Rich ecosystem of examples

**Why Vite over Webpack**:
- Extremely fast development server
- Near-instant HMR
- Optimized production builds
- Modern ES modules support
- Better developer experience

### Performance Optimization

**ESP32 Optimizations**:
- Deep sleep between readings to save power
- Batch multiple readings before publishing
- Use QoS 1 for balance of reliability and performance
- Implement exponential backoff for reconnection

**MQTT Optimizations**:
- Use retained messages for latest valve status
- Implement topic hierarchy for efficient filtering
- Enable persistent sessions for reliability
- Configure appropriate keep-alive intervals

**InfluxDB Optimizations**:
- Batch writes from MQTT bridge (100 points per batch)
- Use appropriate retention policies (90 days raw, 5 years downsampled)
- Create continuous queries for common aggregations
- Index tags for fast queries

**Gemini API Optimizations**:
- Cache similar queries to reduce API calls
- Batch multiple analyses when possible
- Use streaming for long responses
- Implement rate limiting and retry logic
- Monitor token usage and costs

**Frontend Optimizations**:
- Use React.memo for expensive components
- Implement virtual scrolling for long alert lists
- Throttle Three.js render loop to 30 FPS
- Use WebWorkers for heavy computations
- Lazy load components with React.lazy

### Development Workflow

**Local Development**:
```bash
# Start infrastructure
docker-compose up -d mosquitto influxdb

# Start backend with hot reload
cd backend
uvicorn main:app --reload

# Start frontend with Vite
cd frontend
npm run dev

# Flash ESP32 firmware
cd firmware
pio run --target upload
```

**Mock Data Generation**:
```python
# Generate mock sensor data for testing
import random
import time
from paho.mqtt import client as mqtt_client

def generate_mock_data():
    client = mqtt_client.Client()
    client.connect("localhost", 1883)
    
    while True:
        level = random.uniform(1.0, 3.0)
        client.publish(
            "campus-water/sensors/building-a/level",
            json.dumps({"value": level, "timestamp": time.time()})
        )
        time.sleep(1)
```

**Code Quality**:
- Pre-commit hooks for Python (black, flake8, mypy)
- ESLint and Prettier for TypeScript/React
- Arduino linter for ESP32 firmware
- Automated testing in CI/CD

### Migration and Rollout Strategy

**Phase 1: Hardware Setup (Week 1)**
- Install ESP32 nodes and sensors
- Configure WiFi and MQTT connectivity
- Verify sensor readings

**Phase 2: Data Pipeline (Week 2)**
- Deploy Mosquitto and InfluxDB
- Implement MQTT bridge service
- Validate data storage

**Phase 3: Intelligence Layer (Weeks 3-4)**
- Integrate Gemini API
- Deploy monitoring agents
- Test alert generation

**Phase 4: Visualization (Week 5)**
- Deploy Three.js visualization
- Implement real-time updates
- Train operators

**Phase 5: Automation (Week 6)**
- Enable automated valve control
- Test emergency response
- Run end-to-end scenarios

**Phase 6: Production (Week 7+)**
- Monitor system performance
- Collect operator feedback
- Iterate and improve

