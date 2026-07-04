# RFC-0008_REVENUE_INTELLIGENCE.md

---
id: RFC-0008
title: Revenue Intelligence System
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: Revenue Architecture
type: Request For Comments
classification: Critical
related:
  - RFC-0001
  - RFC-0002
  - RFC-0003
  - RFC-0006
  - ADR-004
---

# 1. Purpose

This RFC defines the Revenue Intelligence System of ATLAS.

Unlike traditional dynamic pricing engines, Revenue Intelligence continuously reasons about demand, occupancy, profitability and market conditions to maximize long-term business performance rather than simply increasing prices.

Revenue optimization is treated as a continuous decision-making process.

---

# 2. Objectives

The Revenue Intelligence System must:

- maximize long-term revenue
- maximize occupancy profitability
- recommend optimal pricing
- anticipate demand changes
- explain every recommendation
- continuously learn from outcomes
- support owner decision making

---

# 3. Architectural Position

```
Business Events

↓

Growth Brain

↓

Revenue Brain (future)

↓

Revenue Agent

↓

Pricing Tool

↓

Pricing Service

↓

Updated Availability
```

The Revenue Agent recommends.

The Pricing Service executes.

---

# 4. Core Principles

Revenue Intelligence must be:

- explainable
- data-driven
- event-driven
- probabilistic
- conservative under uncertainty
- continuously improving
- owner-centric

Recommendations must never become automatic price changes without policy approval.

---

# 5. Inputs

Revenue decisions may consider:

## Internal Signals

- occupancy
- reservations
- cancellations
- lead time
- booking pace
- historical ADR
- RevPAR
- seasonality
- property performance

---

## External Signals

Future integrations may include:

- weather
- holidays
- local events
- competitor pricing
- airline demand
- search trends
- tourism indicators

External signals remain optional.

---

# 6. Revenue Decision Pipeline

```
Business Events

↓

Context Retrieval

↓

Demand Estimation

↓

Opportunity Detection

↓

Pricing Simulation

↓

Recommendation

↓

Owner Approval (if required)

↓

Price Update
```

---

# 7. Demand Estimation

Demand is estimated using:

- booking velocity
- historical occupancy
- future reservations
- seasonal patterns
- market signals

Demand estimates are probabilistic.

---

# 8. Opportunity Detection

Examples:

High demand detected.

↓

Increase price.

---

Weak occupancy.

↓

Launch promotion.

---

Long vacancy window.

↓

Reduce minimum stay.

---

Weekend approaching.

↓

Increase urgency messaging.

Every opportunity includes supporting evidence.

---

# 9. Recommendation Types

Examples:

- raise price
- reduce price
- hold price
- increase minimum stay
- decrease minimum stay
- create promotion
- close availability
- open availability

Recommendations remain independent from execution.

---

# 10. Explainability

Every recommendation includes:

- explanation
- supporting data
- confidence
- estimated impact
- assumptions

Owners must understand why recommendations exist.

---

# 11. Simulation Engine

Before execution, simulations estimate:

- expected occupancy
- projected revenue
- conversion impact
- downside risk

Multiple scenarios may be compared.

---

# 12. Learning Loop

```
Recommendation

↓

Owner Decision

↓

Market Outcome

↓

Reservation Data

↓

Growth Brain

↓

Future Recommendations
```

The system continuously improves.

---

# 13. AI Collaboration

Revenue Agent collaborates with:

Operations Agent

↓

Maintenance constraints.

---

Conversion Agent

↓

Reservation likelihood.

---

Content Agent

↓

Promotional campaigns.

The Orchestrator resolves conflicting priorities.

---

# 14. Risk Management

Revenue Intelligence avoids:

- extreme pricing
- unstable fluctuations
- owner confusion
- unexplained decisions
- destructive automation

Stability is prioritized over aggressive optimization.

---

# 15. KPIs

Primary metrics:

- ADR
- RevPAR
- occupancy
- gross revenue
- net revenue
- booking pace
- average lead time
- direct booking ratio
- revenue per guest

Future metrics may be added without redesign.

---

# 16. Event Model

Example events:

PriceRecommendationGenerated

PriceUpdated

PromotionCreated

DemandForecastUpdated

RevenueOpportunityDetected

RecommendationAccepted

RecommendationRejected

Events continuously improve future intelligence.

---

# 17. Security

Revenue recommendations require:

- tenant isolation
- auditability
- permission validation
- historical traceability

Critical pricing changes may require explicit owner approval.

---

# 18. Observability

Every recommendation records:

- recommendation_id
- context version
- supporting metrics
- confidence
- estimated impact
- execution status
- actual outcome

Prediction quality is continuously evaluated.

---

# 19. Anti-Patterns

Forbidden:

- black-box pricing
- unexplained recommendations
- automatic extreme price changes
- duplicated pricing rules
- AI modifying prices without policy validation
- ignoring historical outcomes

---

# 20. Future Evolution

Future capabilities include:

- competitor intelligence
- portfolio optimization
- reinforcement learning
- dynamic pricing policies
- autonomous experimentation
- multi-property optimization
- market prediction models
- Revenue Brain

---

# 21. Success Criteria

The Revenue Intelligence System is considered successful when:

- pricing recommendations become increasingly accurate;
- recommendations remain explainable;
- long-term revenue improves;
- owner trust increases;
- learning occurs continuously through business events.

---

# 22. Summary

The Revenue Intelligence System transforms pricing from a static configuration into an intelligent, explainable and continuously learning capability.

By combining AI reasoning, business events and institutional memory, ATLAS enables owners to optimize profitability while maintaining transparency, stability and full control over commercial decisions.