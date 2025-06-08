# CTV Temporal Analytics Engine

An open-source tool for streaming Connected TV (CTV) data analysis, insights, and automated optimizations using temporal knowledge graphs. Built with integration support for Prebid Server Video and Google Ad Manager 360.

## 🧠 Inspired by Zep's Temporal Architecture

This project applies the temporal knowledge graph concepts from the paper *"Zep: A Temporal Knowledge Graph Architecture for Agent Memory"* to CTV advertising optimization:

- **Temporal Knowledge Graph**: Uses Neo4j with Graphiti-inspired architecture to capture CTV events over time
- **Hierarchical Memory Structure**: Implements episode, semantic entity, and community subgraphs for CTV data
- **Bi-Temporal Modeling**: Tracks both event occurrence time and system recording time
- **Efficient Retrieval**: Combines semantic similarity, keyword search, and graph traversal for insights

## ✨ Features

### Core Analytics
- **Real-time Event Processing**: Stream processing of CTV bid requests, impressions, viewability, and completion events
- **Temporal Relationship Mapping**: Track relationships between devices, campaigns, creatives, and content over time
- **Community Detection**: Automatically discover clusters of related entities for audience insights
- **Performance Trend Analysis**: Historical performance analysis with temporal context

### Automated Optimizations
- **Rule-based Optimization**: Configurable rules for bid adjustments, creative pausing, and frequency capping
- **Machine Learning Insights**: Predictive models for viewability and completion rate optimization
- **Real-time Alerts**: Automated alerts for performance anomalies and optimization opportunities

### Integrations
- **Prebid Server Video**: Direct integration for bid price adjustments and placement optimization
- **Google Ad Manager 360**: Campaign management and reporting integration
- **Streaming Infrastructure**: Kafka-based event streaming with Redis caching

## 🚀 Quick Start

### Prerequisites
- Docker and Docker Compose
- 8GB+ RAM recommended
- Google Ad Manager 360 credentials (optional)
- Prebid Server access (optional)

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/your-org/ctv-temporal-analytics.git
cd ctv-temporal-analytics
```

2. **Setup environment**
```bash
cp .env.example .env
# Edit .env with your configuration
```

3. **Start the infrastructure**
```bash
make setup-dev
```

4. **Run the application**
```bash
make run
```

### Accessing Services
- **Analytics Dashboard**: http://localhost:3000 (Grafana)
- **Kafka UI**: http://localhost:8080
- **Neo4j Browser**: http://localhost:7474
- **API Documentation**: http://localhost:8000/docs

## 📊 Data Flow Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   CTV Events    │───▶│   Kafka Topics   │───▶│  Analytics      │
│                 │    │                  │    │  Engine         │
│ • Bid Requests  │    │ • ctv_impressions│    │                 │
│ • Impressions   │    │ • ctv_viewability│    │ • Event         │
│ • Viewability   │    │ • ctv_completions│    │   Processing    │
│ • Completions   │    │ • ctv_clicks     │    │ • Entity        │
│ • Clicks        │    │                  │    │   Extraction    │
└─────────────────┘    └──────────────────┘    │ • Optimization  │
                                               └─────────────────┘
                              │
                              ▼
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Optimization  │◀───│ Temporal Knowledge│───▶│   Dashboards    │
│   Actions       │    │     Graph         │    │   & Reports     │
│                 │    │                   │    │                 │
│ • Bid Adjusts   │    │ Episode Subgraph  │    │ • Performance   │
│ • Creative Pause│    │ Entity Subgraph   │    │   Analytics     │
│ • Frequency Cap │    │ Community Graph   │    │ • Trend Analysis│
└─────────────────┘    └──────────────────┘    │ • Optimization  │
                                               │   Insights      │
                                               └─────────────────┘
```

## 🔧 Configuration

### Basic Configuration (config.yaml)

```yaml
database:
  neo4j:
    uri: bolt://localhost:7687
    user: neo4j
    password: your_password

streaming:
  kafka:
    servers: localhost:9092
    consumer_group: ctv-analytics-group

integrations:
  gam:
    credentials_path: /app/credentials/gam-credentials.json
    network_code: your_network_code
  
  prebid:
    server_url: http://localhost:8080

optimization:
  rules:
    enabled: true
    evaluation_interval: 60
```

### Optimization Rules

Add custom optimization rules:

```python
from src.optimization import OptimizationRule
from datetime import timedelta

# Low viewability optimization
rule = OptimizationRule(
    rule_id="low_viewability_optimization",
    condition="avg_viewability < 0.6",
    action="adjust_bid_price",
    confidence_threshold=0.6,
    temporal_window=timedelta(hours=4)
)

engine.optimization_engine.add_optimization_rule(rule)
```

## 📈 Usage Examples

### Basic Analytics Query

```python
from datetime import timedelta
import asyncio

# Get campaign insights
insights = await engine.get_campaign_insights(
    campaign_id="campaign_123",
    time_window=timedelta(days=7)
)

print(f"Campaign CTR: {insights['metrics']['ctr']:.3f}")
print(f"Avg Viewability: {insights['metrics']['avg_viewability']:.2f}")
```

### Temporal Queries

```python
# Query device behavior over time
temporal_data = await engine.graph_engine.temporal_query(
    entity_id="device_456",
    time_window=timedelta(hours=24)
)

# Analyze viewing patterns
viewing_events = [
    event for event in temporal_data 
    if event['event_type'] == 'impression'
]
```

### Custom Data Streaming

```python
# Send custom events to the analytics engine
event = CTVEvent(
    event_id="custom_001",
    event_type=EventType.IMPRESSION,
    timestamp=datetime.now(),
    device_id="device_789",
    campaign_id="campaign_456",
    creative_id="creative_123",
    placement_id="placement_789",
    viewability_score=0.85
)

await engine.data_streamer.process_event(event)
```

## 🔍 Temporal Knowledge Graph Schema

### Nodes
- **Episode**: Raw event data with temporal context
- **Device**: CTV devices with viewing behavior
- **Campaign**: Advertising campaigns with performance metrics
- **Creative**: Ad creatives with engagement data
- **Content**: Video content with context metadata
- **Placement**: Ad placements with performance history

### Relationships
- **INVOLVES**: Links episodes to entities with temporal context
- **TARGETS**: Campaign targeting relationships
- **DISPLAYS**: Creative display relationships
- **VIEWED_ON**: Content viewing relationships

### Temporal Properties
- **timestamp**: Event occurrence time
- **recorded_at**: System recording time (bi-temporal)
- **first_seen**: Entity first appearance
- **last_updated**: Entity last modification

## 🤖 Machine Learning Models

### Viewability Prediction
Predicts video ad viewability based on:
- Device characteristics
- Content context
- Historical performance
- Temporal patterns

### Completion Rate Optimization
Optimizes for video completion using:
- Creative performance history
- Audience behavior patterns
- Content genre correlations
- Time-of-day effects

### Bid Price Optimization
Dynamic bid pricing based on:
- Real-time performance metrics
- Historical conversion data
- Competitive landscape
- Temporal performance patterns

## 🚀 Deployment

### Development
```bash
make run-dev
```

### Production (Kubernetes)
```bash
kubectl apply -f k8s/
```

### Scaling Configuration
```yaml
# Scale analytics pods
kubectl scale deployment ctv-analytics-engine --replicas=5

# Scale Kafka partitions
kafka-topics --alter --topic ctv_impressions --partitions 12
```

## 📊 Monitoring & Observability

### Metrics (Grafana Dashboards)
- Event processing rates
- Optimization success rates
- System performance metrics
- Business KPIs (CTR, viewability, completion rates)

### Alerts
- High error rates
- Processing delays
- Optimization anomalies
- System resource issues

### Health Checks
```bash
# Application health
curl http://localhost:8000/health

# Database connectivity
make neo4j-shell

# Streaming status
make kafka-topics
```

## 🧪 Testing

### Unit Tests
```bash
make test-local
```

### Integration Tests
```bash
make test
```

### Load Testing
```bash
# Generate test data at scale
docker-compose up data-generator

# Monitor performance
make logs
```

## 🛠 Development

### Project Structure
```
ctv-temporal-analytics/
├── src/
│   ├── main.py                 # Application entry point
│   ├── temporal_graph.py       # Temporal knowledge graph engine
│   ├── streaming.py            # Event streaming components
│   ├── optimization.py         # Optimization engine
│   ├── integrations/           # External service integrations
│   └── analytics/              # Analytics and reporting
├── config/                     # Configuration files
├── scripts/                    # Setup and utility scripts
├── tests/                      # Test suite
├── k8s/                        # Kubernetes manifests
└── docker-compose.yml          # Development environment
```

### Adding New Optimizations

1. **Define optimization rule**:
```python
rule = OptimizationRule(
    rule_id="custom_optimization",
    condition="custom_metric < threshold",
    action="custom_action",
    confidence_threshold=0.8,
    temporal_window=timedelta(hours=2)
)
```

2. **Implement action handler**:
```python
async def handle_custom_action(self, event: CTVEvent):
    # Custom optimization logic
    pass
```

3. **Register with engine**:
```python
engine.optimization_engine.add_optimization_rule(rule)
```

## 🔗 API Reference

### REST API Endpoints

#### Analytics
- `GET /api/campaigns/{id}/insights` - Get campaign insights
- `GET /api/devices/{id}/behavior` - Get device behavior analysis
- `GET /api/performance/trends` - Get performance trends

#### Optimization
- `POST /api/optimization/rules` - Add optimization rule
- `GET /api/optimization/history` - Get optimization history
- `PUT /api/optimization/rules/{id}` - Update optimization rule

#### Real-time
- `WebSocket /ws/events` - Real-time event stream
- `WebSocket /ws/optimizations` - Real-time optimization updates

## 🤝 Contributing

1. Fork the