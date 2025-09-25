# IMIC 2026 Contest: Intelligent Spare Parts Network Optimization (ISPNO)

## Industrial Context

The problem emerges from collaboration between academic researchers and manufacturing industry partners facing critical spare parts management challenges. Modern manufacturing facilities operate complex equipment requiring 500+ unique spare parts, with unplanned downtime costs ranging from $50,000 to $500,000 per hour. The challenge addresses the fundamental trade-off between inventory holding costs ($2-5M annually) and equipment availability (target 95%+ uptime). 

```
figure1_industrial_challenge
```

This contest provides the first standardized benchmark for the intelligent manufacturing systems community, enabling fair comparison of optimization approaches, machine learning methods, and hybrid strategies for spare parts network management.

## Introduction

This benchmark bridges theoretical optimization and practical manufacturing requirements, positioned at the intersection of operations research, predictive maintenance, and intelligent manufacturing control. The study addresses both deterministic optimization (with known demand patterns) and stochastic control (with uncertain equipment failures), using real NASA bearing failure data to ensure industrial authenticity. 

The contribution is threefold: (i) establishing standardized benchmarks for the IMIC community, (ii) enabling cross-methodology comparison between OR and ML approaches, and (iii) providing scalable problem instances from small educational cases to industry-scale challenges. The expected outcomes include validated algorithms for spare parts optimization and a permanent benchmark repository for manufacturing system research.

## Glossary

| **Key Terms** | **Synonyms** | **Definition** |
|:-------------:|:------------:|:--------------:|
| Network Node | Location, Facility | Manufacturing site or warehouse requiring spare parts (N) |
| Part Type | SKU, Component | Unique spare part with specific characteristics (P) |
| Holding Cost | Inventory cost | Daily cost per unit stored in inventory (h) |
| Lead Time | Delivery time | Days from order placement to receipt (L) |
| Service Level | Fill rate | Percentage of demand satisfied immediately (SL) |
| Reorder Point | ROP, s | Inventory level triggering new order (s) |
| Order Quantity | EOQ, Q | Number of units ordered at once (Q) |
| Stockout | Shortage | Demand occurrence when inventory is zero |
| Failure Pattern | Demand pattern | Equipment failure probability distribution |
| Planning Horizon | Time horizon | Simulation duration in days (T) |

## Considered Topology

The spare parts network consists of multiple manufacturing facilities connected to suppliers and optional distribution centers. Each facility operates equipment subject to failures (modeled using NASA bearing degradation data), creating stochastic spare parts demand. 

```
                    SUPPLY NETWORK TOPOLOGY
    
    [Suppliers]              [Warehouses]            [Facilities]
         S1 ─────────────────────► W1 ──────────────────► F1
         │                          │                       │
         │      Lead: 3-7 days     │    Lead: 1-2 days    │
         │                          │                       ↓
         S2 ─────────────────────► W2 ──────────────────► F2
         │                          │                       │
         │                          │                       ↓
         └──────────────────────────┴───────────────────► F3
              Direct shipping                              │
              Lead: 2-5 days                               ↓
                                                    [NASA Failure
                                                     Patterns]
    
    Demand Generation: Equipment failures based on NASA bearing data
    Decision Points: Inventory levels, Order quantities, Supplier selection
```

Unlike complex digital twin systems, this model uses discrete-event simulation with daily time steps, making it computationally tractable while maintaining industrial relevance.

## Goals

The solution shall enable to:
- Minimize total operational costs (holding + ordering + stockout)
- Maintain service levels above 95% for critical parts
- Reduce average inventory investment by 20-30%
- Provide robust policies for uncertain demand patterns
- Scale efficiently from 20 to 1000 parts
- Generate interpretable policies for industry adoption
- Support both optimization and learning-based approaches

## Metrics

| **Name** | **Definition** | **Unit** |
|:--------:|:-------------:|:--------:|
| Total Cost | Sum of holding, ordering, and stockout costs over horizon | $/period |
| Service Level | Percentage of demand fulfilled from stock | % |
| Inventory Turns | Annual demand divided by average inventory | turns/year |
| Average Inventory | Mean inventory level across all parts and locations | units |
| Stockout Events | Number of times demand exceeds available inventory | events |
| Order Frequency | Average number of orders placed per period | orders/day |
| Computation Time | Time to generate solution policy | seconds |
| Policy Complexity | Number of parameters in solution | count |
| Robustness Score | Performance variance across random seeds | coefficient |

## Categories and Problems

### Contest Structure Overview

```
┌──────────────────────────────────────────────────────────────┐
│                    ISPNO CONTEST CATEGORIES                   │
├────────────────────────┬─────────────────────────────────────┤
│   DETERMINISTIC        │        STOCHASTIC                   │
├────────────────────────┼─────────────────────────────────────┤
│ • Known demand forecast│ • Uncertain demand                  │
│ • Single simulation run│ • Multiple random seeds             │
│ • MILP/DP approaches   │ • RL/Robust optimization           │
│ • Optimal solutions    │ • Adaptive policies                 │
└────────────────────────┴─────────────────────────────────────┘
                              ↓
                    Problem Instance Tiers
          ┌─────────────┬─────────────┬──────────────┐
          │   SMALL     │   MEDIUM    │    LARGE     │
          │  20-50 parts│ 100-300 parts│ 500-1000 parts│
          │  3-5 nodes  │  8-12 nodes │  15-25 nodes │
          │  30 days    │   90 days   │   180 days   │
          └─────────────┴─────────────┴──────────────┘
```

### Deterministic Category
Participants receive complete demand forecasts for the planning horizon. This category suits traditional optimization approaches (MILP, dynamic programming) and serves as baseline for comparing methods. Evaluation uses single simulation run with known demands.

### Stochastic Category  
Participants develop policies for uncertain demand revealed daily. Demand follows NASA-derived failure distributions. This category attracts reinforcement learning, approximate dynamic programming, and robust optimization approaches. Evaluation averages performance across 10 random seeds.

### Problem Instances
- **Small (Educational)**: 20-50 parts, 3-5 locations, 30-day horizon
- **Medium (Research)**: 100-300 parts, 8-12 locations, 90-day horizon
- **Large (Industrial)**: 500-1000 parts, 15-25 locations, 180-day horizon

## Work Overview

### Contest Workflow

```
                    PARTICIPANT WORKFLOW
    
    ① Download          ② Develop           ③ Submit
    Instance Files      Solution            Policy
         │                  │                   │
         ▼                  ▼                   ▼
    ┌──────────┐      ┌──────────┐      ┌──────────┐
    │ network  │      │ Optimize │      │ policy   │
    │ parts    │─────►│    or    │─────►│   JSON   │
    │ demand   │      │  Learn   │      └──────────┘
    │ config   │      └──────────┘            │
    └──────────┘                               │
                                               ▼
                        ④ Automated Evaluation
                    ┌───────────────────────────┐
                    │   SimPy Simulator         │
                    │   • Parse policy          │
                    │   • Run simulation        │
                    │   • Calculate metrics     │
                    │   • Generate ranking      │
                    └───────────────────────────┘
                                │
                                ▼
                        ⑤ Leaderboard
                    ┌───────────────────────────┐
                    │ Rank │ Team │ Score       │
                    │  1   │ MIT  │ $45,230     │
                    │  2   │ ETH  │ $47,891     │
                    │  3   │ NUS  │ $48,102     │
                    └───────────────────────────┘
```

### Input Files Structure

```json
{
  "network.json": "Topology, distances, capacities",
  "parts.json": "Costs, suppliers, lead times",
  "demand.json": "Historical or distribution parameters",
  "constraints.json": "Service levels, budget limits"
}
```

### Output Format

```json
{
  "policy.json": {
    "inventory_policy": {
      "reorder_points": {"part_A": 5, "part_B": 10},
      "order_quantities": {"part_A": 20, "part_B": 30}
    },
    "routing_policy": {
      "supplier_preferences": {"part_A": ["S1", "S2"]}
    }
  }
}
```

### Evaluation Process

```
                EVALUATION PIPELINE
    
    Policy JSON ──► Validation ──► Simulation ──► Metrics
         │              │               │            │
         │              ▼               ▼            ▼
         │         Format Check    30-180 days   Cost: $45,230
         │         Constraints     Discrete      Service: 96.2%
         │         Feasibility     Events        Stockouts: 3
         │                             │            │
         └─────────────────────────────┴────────────┘
                            │
                            ▼
                    Results Database
                    Leaderboard Update
```

## Solution Development Flow

```
            SOLUTION DEVELOPMENT PROCESS
    
    ┌─────────────────────────────────────────┐
    │  1. Understand Problem Instance         │
    │     • Network topology                   │
    │     • Part characteristics               │
    │     • Demand patterns (NASA data)        │
    └────────────────┬────────────────────────┘
                     ▼
    ┌─────────────────────────────────────────┐
    │  2. Choose Approach                      │
    │                                          │
    │  OR Methods        ML Methods           │
    │  • MILP            • Neural Networks    │
    │  • Dynamic Prog.   • Reinforcement      │
    │  • Heuristics      • Time Series        │
    └────────────────┬────────────────────────┘
                     ▼
    ┌─────────────────────────────────────────┐
    │  3. Generate Policy Parameters          │
    │     • Reorder points (s)                │
    │     • Order quantities (Q)              │
    │     • Supplier selection rules          │
    └────────────────┬────────────────────────┘
                     ▼
    ┌─────────────────────────────────────────┐
    │  4. Test Locally                        │
    │     • Run simulator                     │
    │     • Check metrics                     │
    │     • Iterate and improve               │
    └────────────────┬────────────────────────┘
                     ▼
    ┌─────────────────────────────────────────┐
    │  5. Submit Final Policy                 │
    └─────────────────────────────────────────┘
```

## Baseline Solutions Provided

- **Simple (s,S) Policy**: Fixed reorder point and order-up-to levels
- **EOQ-based Policy**: Economic order quantity with safety stock
- **Greedy Heuristic**: Order when inventory below average demand
- **Random Policy**: For baseline comparison

## Timeline

```
    2025 CONTEST TIMELINE
    
    March       April-May        June         July        September
      │            │              │            │             │
    Launch      Development    Deadline    Results      Conference
      │            │              │            │             │
    ──┴────────────┴──────────────┴────────────┴─────────────┴──
      ↓            ↓              ↓            ↓             ↓
    Release     Support &      Submit      Winners      Present
    Instances   Tutorials      Solutions   Announced    @ IMIC
```

## Resources

- **GitHub Repository**: Simulator, instances, and documentation
- **Python Starter Kit**: Example solutions with visualization
- **Discord Community**: Real-time support and discussions
- **Video Tutorials**: Step-by-step guidance
- **Baseline Implementations**: Reference algorithms

## Getting Started

```
    QUICK START (30 MINUTES)
    
    1. Install     →  pip install simpy numpy pandas
    2. Download    →  git clone ispno-contest
    3. Test        →  python simulator.py --example
    4. Develop     →  Edit policy.json
    5. Submit      →  Upload to contest portal
```

---

*For questions and support, visit our Discord channel or email ispno@imic2026.org*
