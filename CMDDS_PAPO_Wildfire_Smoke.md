# CMDDS — PAPO Wildfire Smoke Exposure Framework
## Conceptual Model Design Documentation for Simulation (CMDDS)
**Version:** 1.2
**Date:** 2026-08-19
**Author:** Min Wu, PhD
**Affiliation:** Zilber College of Public Health, University of Wisconsin-Milwaukee
**Contact / GitHub:** https://github.com/Min-PH/PAPO-Wildfire-Smoke-AI

**Cite:** Min Wu. CMDDS for PAPO–Wildfire Smoke Exposure v1.2 – Conceptual Simulation Framework. Zenodo. DOI: 10.5281/zenodo.23485910

> **Note on lineage.** This CMDDS extends the Policy-Aware Personalized Opportunity (PAPO) framework from the validated CMDDS_PAPO_Heatwave v1.2 architecture to a second acute environmental hazard: wildfire smoke (PM2.5) exposure. Rather than re-deriving governance requirements from scratch, the v1.0 release inherited the MOKG-PH–informed structural requirements already validated for the heatwave case (bias injection, null-output-as-required-architecture, pre-selected equity metrics, governance-signed human-handover boundaries, explicit underserved-population nodes, the Simulation-First Protocol, and the Pre-Deployment Checklist) from initial release, rather than adding them in a later revision cycle. Domain-specific additions unique to smoke exposure are noted inline.
>
> **v1.1 update.** This revision incorporates the findings of the MOKG-PH-A v2.1 Case Analysis (proposed Case #005-C, August 2026), which audited PAPO–Smoke v1.0 against the same Hybrid-mode governance toolkit that shaped the heatwave case. The audit confirmed the inherited architecture holds without modification — Dignity Audit 5/5, Opportunity-vs-Risk Framing Audit 5/5 — and surfaced two structural gaps genuinely new to this domain (a live conflict between two Tier 1 hard-constraint values under compound heat-smoke conditions, and unspecified resource coordination for plume footprints that cross governance boundaries), plus reaffirmed two items carried forward unresolved from the heatwave case (strategic-loop policy-revision authority; refusal-vs-null-output conflation). All four are addressed below and are marked **★ v1.1** at each point of change; see the Changelog for the complete list.
>
> **v1.2 update.** This revision incorporates a MOKG-PH-A Step 5 Mechanism-Evidence module — a dedicated empirical audit of this CMDDS's central domain-specific mechanism claim (Assumption #8: "stay indoors" is not uniformly protective, and Personalized Opportunity, not awareness, is the binding constraint). Roughly a dozen independent sources across the US and Australia, 2007–2025, were checked against a pre-declared prediction, a cross-vantage robustness test, and a rival-model adjudication; the mechanism clears the module's threshold on all three counts. This is evidentiary grounding, not a governance-gap audit — it does not add new required entities, authorities, or checklist items — so the changes are lighter-touch than v1.1's and are concentrated in Step 4's Assumptions and a new Evidence Base subsection, marked **★ v1.2**. One honest limitation carried through from the module is stated plainly rather than smoothed over: see Step 4, Assumption #8.

## Overview
This document provides a developer-facing CMDDS for the Policy-Aware Personalized Opportunity (PAPO)
framework applied to wildfire smoke exposure response. It is written for AI developers, data scientists, and
simulation engineers who want to prototype, simulate, or extend PAPO for wildfire smoke events in real cities
and regions.

The CMDDS defines what to simulate and why, while deliberately leaving how to implement (code, parameters,
platforms) open. This makes it suitable for agent-based models, system dynamics, discrete-event simulation,
digital twins, and VR-supported decision environments — consistent with the heatwave CMDDS.

**Why smoke, and why smoke rather than fire.** Modeling wildfires directly requires simulating physical
ignition mechanics, wind-driven flame vectors, and emergency evacuation traffic — variables governed by
geography and combustion physics, not personal health opportunity. Anchoring the simulation in the **smoke
plume** instead of the **fire perimeter** keeps the model in PAPO's actual domain: PM2.5 dispersion follows
continuous, spatially-continuous plume dynamics that overlay cleanly onto the same spatial-grid and agent-based
architecture used for heat, letting compute stay on individual-level exposure and opportunity states rather than
volatile fire mechanics. This is a simulation-first, not deployment-first, artifact, matching the heatwave CMDDS's
posture.

This CMDDS is also, like its heatwave counterpart, a Stage 1 knowledge-graph specification and Simulation-First
Protocol artifact under the MOKG-PH governance framework. Implementations targeting operational deployment must
satisfy the Pre-Deployment Checklist (see end of document) before any real population is exposed to
PAPO-Smoke–driven recommendations.

-------------------------
## CMDDS Step 1 — System Purpose, Simulation Goals & Policy Boundary

### System Purpose
Wildfire smoke events differ from heatwaves in one structurally important way: while heat risk is bounded by
local ambient temperature and dictated fairly uniformly across a neighborhood, wildfire PM2.5 travels hundreds
of miles, producing a single wide, shifting plume footprint under which millions of individuals experience
sharply different **micro-exposures**. Ambient AQI is close to uniform across a metro area; **indoor** exposure
is not. This means Personalized Opportunity (PO) — not policy clarity, not individual awareness — is
generally the dominant bottleneck standing between a public health advisory and a protected individual.

PAPO addresses this gap by translating wildfire smoke emergency policy (AQI-triggered advisories, clean-air
shelter openings, filtration distribution programs) into personalized, feasible, and actionable opportunities
in near real time, explicitly accounting for the fact that policy guidance such as "stay indoors and run
filtration" has wildly different feasibility depending on an individual's housing envelope, financial resources,
occupation, and transit dependency. **★ v1.2:** this claim is not merely asserted — see Step 4 Evidence Base for
the empirical audit that grounds it.

The purpose of this CMDDS is to enable simulations that test whether, when, and for whom policy-aware
opportunity delivery improves wildfire smoke response.

### Core Simulation Questions (Answerable by Design)
Simulations built from this CMDDS should be able to answer specific, concrete questions, such as:

#### 1. Timing & Triggers
- How early must an AQI-based policy trigger activate for personalized opportunities to meaningfully reduce missed interventions?
- What happens if activation is delayed by 6, 12, or 24 hours relative to AQI threshold crossing?
- How quickly does indoor PM2.5 catch up to ambient levels for different housing-envelope classes, and does the opportunity engine outrun that decay curve?

#### 2. Feasibility vs. Alerts
- How do opportunity-based interventions (mobile filtration delivery, door-to-door transit) compare to standard "stay indoors" / "visit a shelter" alerts in terms of uptake?
- Which constraints — housing envelope tightness, filtration ownership, financial capacity, transit dependency, occupational outdoor exposure — dominate failure modes?

#### 3. Equity & Distribution
- Which population subgroups (renters, transit-dependent residents, outdoor/essential workers, low-income households) benefit most or least from PAPO-Smoke–style interventions?
- How do equity gaps change as filtration-kit and clean-air-shelter capacity scales up or down?
- How do underserved populations (rural / geographically isolated, language-barrier, no-digital-access, and — specific to this domain — structurally outdoor-exposed workers) fare relative to digitally reachable, indoor-employed urban populations?

#### 4. System Stress & Capacity
- Where do bottlenecks emerge under extreme, multi-day smoke events?
- Which resources saturate first — clean-air shelter slots, filtration-kit inventory, or mobile delivery/transit capacity?

#### 5. Bias & Fairness (Required for Complex cases)
- When historical training data is biased (e.g., overrepresentation of homeowner or car-owning recipients in past filtration-kit allocation), what is the disparity delta in opportunity rates across affected groups?
- Does applying a fairness constraint (e.g., demographic parity) close the gap to a governance-approved threshold?

#### 6. Compound Hazard Interactions (domain-specific addition)
- When heat and smoke events co-occur, how do individuals without air conditioning resolve the conflict between sealing the home against PM2.5 and opening windows for thermal relief?
- Does the opportunity engine detect and resolve policy conflicts between concurrent heat and smoke guidance, or does it issue contradictory recommendations?
- ★ v1.1: Does the named Compound-Hazard Protocol Authority's pre-authored guidance (Step 3 §1, §4) actually activate when both hazard thresholds are crossed concurrently, and how much simulated time elapses before it does?

These questions are intentionally simulation-first and do not require clinical outcome modeling.

#### 7. Cross-Jurisdictional Coordination (domain-specific addition ★ v1.1)
- When a plume footprint crosses county, state, or national boundaries faster than mutual-aid agreements activate, how long is the resulting coordination lag, and which resource types are most exposed to it?
- Does modeling resource pools as merely "location-aware" (rather than explicitly jurisdiction-aware) understate cross-boundary bottlenecks?
- How do outcomes differ between a region with pre-established mutual-aid agreements and an otherwise-identical region without them?

These questions are intentionally simulation-first and do not require clinical outcome modeling.

### Candidate Cities/Regions for Simulation (Data-Ready, Smoke-Exposed)
To lower the barrier for contributors, simulations may focus on data-ready, wildfire-smoke-prone regions, such as:

- **United States:** Los Angeles, Sacramento, Portland (OR), Seattle, Denver, Phoenix
- **International:** Sydney, Vancouver, Jakarta (regional haze events)

These regions offer combinations of:

- Recurrent seasonal wildfire smoke incursion
- Open air-quality monitoring data (e.g., regulatory AQI networks, low-cost sensor networks)
- Publicly documented smoke advisory and clean-air-shelter policies

Contributors are encouraged to fork this repo and adapt PAPO-Smoke to their own city or region.

**Equity sentinel for region selection.** Open-data-ready regions may overrepresent well-resourced urban
contexts with dense regulatory monitoring networks. Forks should flag — and where possible offset — the
systematic exclusion of rural or less-resourced jurisdictions, which are often both smoke-exposed and
monitoring-sparse, from validation. Cross-context comparison (e.g., a major metro paired with a
less-resourced or rural, monitoring-sparse region) is encouraged.

### Explicit Non-Goals (Important for Developers)
This CMDDS does not aim to:

- Predict individual clinical or respiratory outcomes
- Model fire ignition mechanics, wind-driven flame spread, or evacuation traffic routing
- Forecast weather, fire behavior, or air quality
- Optimize or automatically generate policy

Simulations are exploratory, comparative, and stress-testing oriented, not predictive or prescriptive — this
mirrors the heatwave CMDDS's non-goals exactly, with fire-behavior modeling added as an explicit exclusion
specific to this domain.

-----------------------
## CMDDS Step 2 — Conceptual Entities, Attributes, and Inputs

This step defines the entities, attributes, and data inputs required to simulate PAPO–Wildfire Smoke. The
CMDDS supports two temporal phases using the same core entities and logic: Pre-Smoke-Event (optional) and
During-Smoke-Event (minimum required). Simulation implementations may model either or both phases.

### Temporal Phases
#### Pre-Smoke-Event (Preparation / Priming)
Establishes policy constraints, resource capacity, and baseline vulnerability — including housing-stock
quality, which is largely static and can be pre-loaded before any specific fire event.
Optional but recommended for simulations examining timing, readiness, and equity.

#### During-Smoke-Event (Response / Execution)
Activates AQI-based policy triggers and delivers personalized opportunities under real-world constraints as
the plume moves through the region.
Required for all PAPO–Wildfire Smoke simulations.

### Core Conceptual Entities
#### 1. Individuals / Households
Residents exposed to smoke risk and potential recipients of personalized opportunities.

#### 2. Policy and Institutional Systems
Public health agencies, air quality management districts, emergency management, and service providers that
define AQI thresholds, priorities, and constraints.

**★ v1.1 — Compound-Hazard Protocol Authority.** This entity must include a named role (e.g., a public-health
medical officer or environmental-health lead) distinct from general AQI-policy-rule authority, with
pre-authorization to issue compound-hazard protocols when concurrent heat and smoke Tier 1 safety guidance
conflict (see Step 3 §1, §4; Step 4 Assumption #9). Population safety and ethical compliance are both Tier 1,
hard-constraint values; when sealing a home against smoke and venting it against heat point in opposite
directions for the same household, this is a conflict *within* the institutional side of the Hybrid model, not
between institution and individual, and is not resolved by the general human-handover boundary alone.

#### 3. Opportunity Resources
Clean-air shelters, mobile filtration-kit / N95 mask distribution, mobile or door-to-door transit, workplace
PPE distribution for outdoor workers, community volunteers, and related assets.

#### 4. Environmental Context
PM2.5 concentration / AQI severity and duration represented as scenario inputs (not weather or plume
forecasting), including co-occurring ambient temperature where compound heat-smoke scenarios are modeled.

#### 5. Underserved-Population Equity Pathways (Required Nodes)
Explicit modeling of populations who are systematically under-reached by digital, English-language, and
urban-resource-dense delivery channels, carried forward directly from the heatwave CMDDS:

- Rural / geographically isolated individuals (cross-zone resource access required)
- Limited-English-proficiency populations (translation and culturally competent outreach pathways)
- No-digital-access populations (non-app, non-text delivery channels: voice, in-person, trusted community intermediary)

**Domain-specific addition — Structurally outdoor-exposed occupational populations.** Essential outdoor
workers, agricultural staff, delivery drivers, and transit users face severe structural limits on their
opportunity to avoid ambient smoke *regardless of awareness or motivation*, because their exposure is tied to
their job, not their housing. This population is far more salient for smoke than for heat and is treated as a
required fourth equity-pathway node for this domain.

**★ v1.1 — Dignity gate for occupational-exposure data.** This pathway is currently the least evidence-mature
of the four (occupational outdoor-exposure data is thin and rarely disaggregated by employer size or job
class). Operationalizing it beyond a coarse indoor/outdoor flag requires occupational-exposure data
partnerships with employers (hours of required outdoor work, PPE access). Any such partnership must include an
explicit dignity gate: employer-supplied data may inform aggregate resource allocation but must never be used
to identify, rank, or surface individual workers to their employer, and workers retain the same refusal rights
at the point of delivery as any other pathway.

These nodes are required for the strategic feedback loop to operate correctly after the first deployed season.
Without them, the strategic loop cannot detect or correct systematic exclusion of underserved groups.

#### 6. Cross-Jurisdictional Coordination Systems (Required Node ★ v1.1)
Mutual-aid agreements, shared resource registries, and a named cross-jurisdictional coordinator role that
activate when a plume footprint spans multiple counties, states, or national governance boundaries. Wildfire
PM2.5 plumes routinely travel hundreds of miles and cross administrative boundaries faster than
inter-jurisdictional mutual-aid or resource-sharing agreements typically activate; without this entity,
Resource Pools (below) are location-aware but not jurisdiction-aware, and cross-boundary coordination lag is
invisible to the simulation. This entity is required for any deployment region where candidate-city selection
(Step 1) or fork-level parameterization identifies meaningful cross-boundary plume risk.

### Entity Attributes and Inputs
#### Phase A — Pre-Smoke-Event Inputs (Optional)
Policy / Institutional Inputs (Static)
Used to initialize the Policy Input Layer:

- Strategic intent and equity priorities
- AQI eligibility thresholds and escalation rules (e.g., "Unhealthy for Sensitive Groups" vs. "Hazardous" tiers)
- Standard operating procedures and agency roles
- Resource inventories (clean-air shelter facilities, filtration-kit / N95 stockpiles, mobile delivery vehicles, staff, volunteers)
- Spatial distribution of services (GIS)
- Baseline neighborhood vulnerability indicators, **including housing-stock quality / building-envelope tightness distributions**
- **★ v1.1: Policy-rule versioning and provenance metadata** — AQI eligibility thresholds and escalation rules represented as versioned, provenance-tagged nodes (rule ID, effective date, superseding rule, re-encode latency), not static values, so mid-season revisions are traceable and the time from "revision decided" to "revision live" can be measured
- **★ v1.1: Compound-Hazard Protocol Authority designation** and the pre-authored, condition-triggered guidance that authority issues for concurrent heat-smoke conditions (e.g., prioritizing relocation to a shelter that is both clean-air and cooled over either sealing or venting in place)
- **★ v1.1: Cross-jurisdictional mutual-aid agreements and shared resource-registry access**, for deployment regions where plume risk crosses county, state, or national boundaries

Individual / Household Inputs (Static or Self-Reported)
Used to initialize the Personalized Opportunity Engine:

- Home location and housing characteristics — **building envelope infiltration rate (I/O ratio), HVAC type and filter rating (e.g., MERV 13+ vs. none), standalone HEPA or DIY (Corsi-Rosenthal) filtration ownership**
- Demographics and baseline vulnerability factors (age, asthma, COPD, cardiovascular disease, pregnancy)
- Chronic respiratory / cardiovascular risk indicators
- **Occupational exposure category** (indoor / outdoor-exposed flag; hours of required outdoor work)
- Mobility, disability, or caregiver needs
- **Vehicle ownership / transit dependency**
- Financial capacity to purchase filtration equipment
- Preferred communication channels
- Digital access constraints
- Language preference and English-proficiency indicator
- Geographic isolation indicator (distance to nearest clean-air shelter, transit availability)

Pre-event simulations may model registration, pre-authorization of vulnerable households, or reduced response
latency, but these are not required.

#### Phase B — During-Smoke-Event Inputs (Required)
Policy / System Inputs (Dynamic)

- AQI alerts and emergency triggers
- Active policy rules and timelines (e.g., shelter hours, mask-distribution windows)
- Real-time service availability and capacity (shelter occupancy, filtration-kit stock)
- Transportation status and routing constraints
- Community outreach signals and unmet-need reports
- **★ v1.1: Cross-jurisdictional resource-sharing requests and mutual-aid activation status** (dormant / requested / active)
- **★ v1.1: Active compound-hazard protocol status** — whether concurrent heat-smoke conditions have triggered the named authority's pre-authored guidance

Individual / Context Inputs (Dynamic)

- Current or inferred location
- **Estimated indoor PM2.5 concentration**, derived from ambient AQI and the individual's infiltration/decay parameters (see Step 3)
- Recent activity or movement indicators
- Connectivity and reachability status
- Check-in events or non-response
- Self-reported respiratory symptoms or distress
- Service utilization events (filtration-kit pickup, shelter check-in)
- Caregiver or neighbor alerts
- **★ v1.1: Consent/opt-out status for continuous monitoring inputs** (location, connectivity/reachability, check-in events) — refreshable by the individual at any time, with no penalty to opportunity eligibility for opting out. This closes a documented Dignity Audit caveat: the dynamic inputs above are otherwise listed without a specified consent mechanism.

### Attribute Categories and State Updates
Attributes are categorized as:

- **Static:** baseline vulnerability, housing envelope quality, occupational category
- **Contextual:** location, routines, communication preferences
- **Dynamic:** ambient and indoor exposure, opportunity availability, uptake, resource capacity

**★ v1.1 — Delivery outcome (Dynamic, required).** Every delivered-or-attempted opportunity must record a
delivery outcome as one of: **accepted**; **refused** (dignity-protected, individual-initiated decline — never
overridden, never penalized, never treated as a failure mode); or **null**, sub-typed by the five required
trigger types in Step 3 §3. Refusal and null are structurally distinct states and must not be conflated in
state updates or strategic-loop aggregation — a household that declines an offered filtration kit and a
household for whom no kit exists must remain distinguishable in every downstream metric (see Step 3 §3, §5;
Step 3.5 component 7).

Simulations are assumed to operate in discrete time steps (e.g., hourly), with state updates driven by policy
triggers, opportunity delivery, and feedback. Hourly resolution is recommended over daily given how quickly
indoor PM2.5 can rise following an ambient AQI spike (see Illustrative Scenario below).

This step defines what data exists and when it becomes active, not how it is modeled. Parameterization, data
sources, and AI methods are intentionally left to downstream implementations.

### Resource Pools (Required for Stress Testing)
Simulations must model opportunity resources as exhaustible, location-aware pools with dynamic availability:

- Clean-air shelter slots (capacity per facility, real-time occupancy)
- **Filtration-kit / N95 mask inventory** (stock, replenishment rate, distribution-point locations)
- Mobile delivery / transit vehicles (fleet size, range, round-trip time, driver shifts)
- Volunteer network (response probability, travel time, availability windows)
- **★ v1.1: Cross-jurisdictional (mutual-aid) resource pools** — inventory shared across governance boundaries, tracked separately from home-jurisdiction stock, with an activation status (dormant / requested / active) and a measured coordination lag between request and availability

Resource state is updated every time step: allocation reduces availability; completion of a delivery, trip, or
visit replenishes it. Without this, simulations cannot detect bottlenecks or test feasibility-driven failure
modes.

### Bias Injection (Required for Complex Cases)
To test equity robustness, implementers **must** inject historical bias into opportunity ranking or eligibility
models for any deployment-bound implementation. For example:

- Train a simple ML ranker on data where past filtration-kit recipients were disproportionately homeowners or car owners.
- Measure the disparity in opportunity rates between homeowner/car-owning and renter/transit-dependent groups (the **disparity delta**).
- Compare against a fairness-constrained version (e.g., demographic parity).
- Confirm the disparity delta meets a governance-approved threshold before deployment authorization.

**Rationale.** As with the heatwave case, a passive bias audit is insufficient for Complex cases involving
AI-driven opportunity ranking under scarcity — active injection testing is required to surface
equity/prediction conflicts before any real population is exposed to the system. Forks that omit this step
have not met the Complex case pre-deployment gate.

This step defines what data exists and when it becomes active, not how it is modeled. Parameterization, data
sources, and AI methods are intentionally left to downstream implementations.

-------------------------------
## CMDDS Step 3 — Core Translation & State Transition Logic

The defining function of PAPO–Wildfire Smoke is the translation of institutional smoke emergency policy into
personalized, feasible micro-opportunities under real-world constraints. This logic operates continuously
across simulation time steps and applies to both pre-event and during-event phases. Different simulation
paradigms (agent-based, system dynamics, discrete-event, hybrid) may operationalize this logic differently, but
the translation sequence must be preserved.

### Translation and Transition Stages
#### 1. Policy Activation and Rule Evaluation

- Environmental and institutional signals (e.g., AQI crossing a threshold, a hazard advisory) activate policy rules.
- Eligibility thresholds (e.g., "sensitive groups" vs. general population tiers), protection priorities, and operational constraints are evaluated.
- Policy state transitions from inactive to active based on predefined AQI or advisory triggers.
- **★ v1.1 — Compound-hazard deferral.** When ambient temperature and AQI both cross their respective policy thresholds concurrently, activation must check for an active Compound-Hazard Protocol before generating single-hazard opportunities. If sealing-vs-venting guidance would conflict (Step 4 Assumption #9), activation defers to the named Compound-Hazard Protocol Authority's pre-authored guidance rather than issuing independent, potentially contradictory heat and smoke recommendations (see Step 3 §4).
- **★ v1.1 — Versioned rule evaluation.** Because policy rules are represented as versioned, provenance-tagged nodes (Step 2), rule evaluation must reference the rule version active at evaluation time, not merely "the current rule set," so re-encode latency between a governance revision decision and its live effect can be measured.

Output: active policy rules and authorized intervention space.

#### 2. Risk and Feasibility Assessment

- Individual baseline vulnerability (respiratory/cardiovascular risk, age) is combined with **estimated indoor PM2.5** and contextual constraints.
- **Indoor air quality (IAQ) decay function:** indoor PM2.5 is modeled as a function of ambient AQI, the household's infiltration rate (I/O ratio), and active filtration status. A poorly sealed unit with no active filtration can approach unsafe indoor thresholds within hours of an ambient spike, even while the resident is nominally "sheltering in place."
- Feasibility checks evaluate access, mobility, financial capacity, occupational constraints, and timing — e.g., can this individual actually reach a shelter without prolonged outdoor exposure; can they afford or obtain filtration.
- **★ v1.1: Cross-jurisdictional feasibility.** For resources located across a governance boundary, feasibility must incorporate mutual-aid activation status (Step 2), not just physical proximity — a shelter slot in an adjacent county may be feasible in principle but infeasible until mutual aid is triggered.
- Behavioral non-uptake and friction are treated as expected constraints, not errors.

Output: prioritized individuals and feasible intervention sets.

#### 3. Personalized Opportunity Generation

- Policy-constrained options are translated into concrete, actionable micro-opportunities (e.g., mobile filtration-kit delivery, door-to-door transit to the nearest clean-air shelter, workplace PPE distribution for outdoor-exposed workers).
- Opportunities are specific, time-bound, and resource-aware.
- Generic guidance ("stay indoors and run filtration," "visit a shelter") is not, by itself, a personalized opportunity — it is the policy input the engine must translate.
- No opportunity is generated if feasibility criteria are not met.
- If no feasible opportunity exists (resource exhaustion, geographic inaccessibility, conflicting rules), the engine must output a null or empty set. This is not an error state — it is a required failure mode that triggers escalation.
- **★ v1.1 — Refusal is not null.** Refusal and null output are architecturally distinct. If a feasible opportunity is generated and offered, and the individual declines it, this is logged as **refused**, not null — refusal is dignity-protected, is never treated as a failure mode, and never triggers escalation. Null output applies only when no feasible opportunity could be generated in the first place (the five trigger types below). Conflating the two corrupts strategic-loop equity calibration by making resource shortfalls indistinguishable from informed individual choice — a gap first identified in the heatwave case (Case #002-C, PAPO-A2) and closed here at the shared PAPO architecture level.

Output: feasible, individualized opportunity candidates (or null or refused).

#### 4. Opportunity Delivery and Escalation

- Opportunities are delivered via appropriate channels (e.g., text, voice, app, human outreach).
- **Human-system handover (governance sign-off required):** Define a clear boundary. Low-risk, reversible actions (e.g., sending an informational text) may be fully automated; actions involving resource commitment or physical safety (e.g., dispatching a mobile filtration delivery, arranging door-to-door transit through hazardous air) require explicit human authorization. Each deployment fork must specify this boundary in writing and obtain governance sign-off before Stage 2 deployment authorization. Simulate delay, rejection, or manual override.
- **★ v1.1 — Compound-hazard sign-off.** Opportunities generated under an active Compound-Hazard Protocol (Step 3 §1) require sign-off from the named Compound-Hazard Protocol Authority in addition to the general human-system handover boundary above. This is a Tier 1-vs-Tier 1 conflict and is not resolved by the standard handover boundary alone.
- **★ v1.1 — Cross-jurisdictional dispatch confirmation.** Deliveries that draw on a mutual-aid resource pool across a governance boundary (mobile filtration dispatch, transit, shelter routing) require the receiving jurisdiction's resource-release confirmation before dispatch.
- Delivery failures or non-response may trigger escalation pathways (e.g., in-person or caregiver checks).
- Human supervision and accountability are assumed for escalated actions.

Output: delivered opportunities and delivery status updates.

#### 5. Feedback and State Update

- Uptake, non-uptake, delays, and resource utilization are recorded.
- System states are updated for individuals, resources, and policy context.
- Two feedback loops must be distinguished:
  - **Tactical loop** (minutes to hours): real-time reallocation of existing resources (e.g., reroute an idle mobile filtration unit, open an overflow clean-air shelter).
  - **Strategic loop** (months to years / across fire seasons): post-season aggregation to update eligibility criteria, filtration-kit stockpile budgeting, and vulnerability definitions.
  - Conflating these loops risks instability or policy inertia; simulations should implement them separately.
- The strategic loop must include explicit pathways for revising eligibility criteria when post-season data reveals systematic under-reach of underserved populations — including, for this domain, outdoor-exposed occupational groups who may show high advisory *awareness* but low advisory *uptake* because the guidance was never structurally feasible for them.
- **★ v1.1 — Named strategic-loop decision authority.** The strategic loop's post-season aggregation must have a named decision authority who reviews eligibility-criteria and stockpile-budgeting recommendations and either enacts or formally declines them within a stated review-and-act cycle length. Specifying the cadence (post-season) without naming who acts on it leaves the loop advisory rather than operative — this closes a gap carried forward unresolved from the heatwave case (Case #002-C, PAPO-A1).
- **★ v1.1 — Refusal/null reporting.** Strategic-loop aggregation must report refusal rate and null-output rate as separate metrics, broken down by the five null-output trigger types and by equity-pathway node, so bias-injection and disparity-delta calculations (Step 2 Bias Injection) are not distorted by unlogged refusals.
- Aggregated, privacy-preserving feedback informs subsequent cycles.

Output: updated system state for next simulation step.

### State Transition Scope
State transitions may include:

- Individual risk and indoor/outdoor exposure status
- Opportunity availability and exhaustion
- Resource capacity and bottlenecks
- Policy escalation or de-escalation

Transitions are event-driven and time-stepped, depending on simulation design.

### Interpretation Constraints

- This logic supports exploratory and comparative simulation, not prediction.
- Outputs reflect system responsiveness, feasibility, and equity signals.
- Clinical outcomes and physiological effects are explicitly out of scope.

This step defines the non-negotiable logic core of PAPO–Wildfire Smoke. All downstream simulations must
preserve this translation structure, even when implementation details differ.

---------------------------
## CMDDS Step 3.5 — Simulation-First Protocol

PAPO–Wildfire Smoke is treated as a Complex case under the MOKG-PH governance framework, on the same basis as
PAPO–Heatwave: it involves AI-driven opportunity ranking under scarcity affecting a large, unevenly vulnerable
population. Complex cases require a Simulation-First Protocol — a structured pre-deployment gate that must be
cleared before any real population is exposed to PAPO-Smoke–driven recommendations. The CMDDS itself provides
the structural specification; per-region forks must implement and run the protocol.

The protocol comprises eight required components (★ v1.1: component 8 added). All must be addressed before
deployment authorization.

| # | Component | What the simulation must do | Source in this CMDDS |
|---|---|---|---|
| 1 | Synthetic population scenarios | Generate realistic populations across both Pre- and During-Smoke-Event phases for the target region, including housing-envelope and occupational-exposure diversity | Step 2 entity attributes |
| 2 | Prioritization rule comparison (≥2 rules) | Run at least two rules from the five-rule table and compare outcomes; governance must select the deployment rule | Step 4 Prioritization Rules |
| 3 | Resource scarcity stress test | Vary filtration-kit and shelter-capacity pools to identify bottlenecks and the threshold at which null outputs become common | Step 2 Resource Pools |
| 4 | Equity metric pre-selection | Select the equity metric **before** the simulation is run; document selection rationale; do not default to "no metric" (equivalent to efficiency-only optimization) | Step 4 Equity Metrics |
| 5 | Timing sensitivity test | Test activation delays of 6, 12, and 24 hours relative to AQI threshold crossing; document the critical threshold at which intervention effectiveness degrades | Step 1 Core Simulation Question 1 |
| 6 | Bias injection test | Train a ranker on biased historical allocation data; measure disparity delta; apply fairness constraint; confirm delta meets governance threshold | Step 2 Bias Injection (Required) |
| 7 | Failure mode / null output coverage | Verify that null outputs trigger correct escalation pathways for all five trigger types (resource exhaustion, geographic inaccessibility, policy conflict — including compound heat-smoke guidance conflicts, non-response, cascading depletion), **and that refusal is logged and reported as a structurally distinct outcome, never merged with null-output counts (★ v1.1)** | Step 3 §3 + Step 4 |
| 8 | Compound-hazard and cross-jurisdictional dry run (★ v1.1) | Simulate at least one concurrent heat-smoke scenario and confirm the named Compound-Hazard Protocol Authority's pre-authored guidance activates instead of independent, possibly contradictory single-hazard recommendations; for regions with cross-boundary plume risk, simulate at least one mutual-aid activation and measure the coordination lag | Step 2 (Cross-Jurisdictional Coordination Systems) + Step 3 §1, §4 |

A fork that completes all eight components and documents the results in a public log entry has met the Complex
case pre-deployment gate. A fork that omits any component has not.

---------------------------
## CMDDS Step 4 — Simulation Outputs, Interpretation, and Limits

### Scope and Assumptions – Read Before Simulating
This CMDDS defines a logical pathway, not an operational system. It rests on several simplifying assumptions
that fail under real-world conditions. Simulations are intended to stress test these assumptions, not to
validate the framework as a turnkey solution.

Explicit assumptions embedded in this CMDDS:

1. **Policy is machine-readable** – We assume AQI eligibility thresholds and rules can be unambiguously encoded. In practice, "sensitive groups" definitions and advisory language often contain discretion or underspecified terms.
2. **A feasible opportunity always exists** – The PAPO translation sequence as drawn assumes the engine can always generate an intervention. **Null output is a required architectural element of the framework (Step 3.3), not an optional fork-level extension.** Implementations that omit it have not implemented PAPO.
3. **Resources are sufficient** – Many illustrations assume infinite filtration-kit and shelter capacity. Real, multi-day smoke events — especially compound heat-smoke events — require prioritization under scarcity.
4. **Human-system handover is resolvable** – We do not prescribe who authorizes what; this must be specified per deployment **with explicit governance sign-off** (Step 3 §4). Deployments without a documented, signed-off handover boundary are not authorized to proceed past Stage 2.
5. **Feedback operates on a single timescale** – The CMDDS separates tactical and strategic loops; simulations must respect this separation.
6. **Data are unbiased** – Historical allocation data used to train opportunity rankers will systematically underrepresent marginalized groups (renters, transit-dependent, outdoor workers) unless fairness constraints are added. Bias injection testing (Step 2) is required for Complex cases.
7. **Underserved populations are reachable through standard channels** – In practice, rural, language-barrier, no-digital-access, *and structurally outdoor-exposed occupational* populations are systematically under-reached. Forks must include explicit equity pathways for these groups (Step 2 §5).
8. **"Stay indoors" is a uniformly protective action** *(domain-specific)* – Policy guidance that treats sheltering-in-place as sufficient assumes adequate structural protection. In practice, without HVAC recirculation, MERV-13+ filtration, or a sufficiently tight building envelope, indoor PM2.5 can approach outdoor hazardous levels within hours (see Illustrative Scenario below). Policy that treats "stay indoors" as uniformly protective will systematically fail lower-income and rental populations. This is the central mechanism by which PO, not policy or awareness, becomes the binding constraint in this domain.

    **★ v1.2 — Evidence base.** This is not a hypothesis original to this CMDDS. A MOKG-PH-A Step 5 mechanism-evidence audit found the pattern independently replicated across roughly a dozen sources spanning two countries, three US states, and multiple Australian jurisdictions, 2007–2025: ED-visit and hospitalization disparities by AC/filtration access, housing tenure, and occupational exposure in California and San Diego; a lag-structured smoke-event signature in the 2016–2019 California series; income- and tenure-disparity findings from the 2008 California wildfires and a San Francisco Fed housing-tenure brief; direct confirmation of the outdoor-worker equity pathway in the January 2025 Los Angeles firestorms; and cross-national replication in the Black Saturday (2009) and Black Summer (2019–2020) Australian bushfires, including an independent Australian building-science echo of this same assumption. The mechanism also beat the rival Awareness/Risk-Communication account on a win criterion declared before scoring: a 2026 nationwide analysis found protective-behavior gaps persisted across all socioeconomic strata regardless of message quality, and a 2024 scoping review found the risk-communication literature has not itself evaluated its effectiveness for vulnerable populations. See Step 4 Evidence Base for detail and Related References for full citations.

    **★ v1.2 — Limitation, stated plainly.** This evidence base establishes that PO is the binding constraint; it does not yet establish that a PAPO-style opportunity-delivery system closes the gap faster or further than existing untargeted filtration and shelter programs. Unlike the heatwave case, which has a direct before/after comparison (Chicago's 1995→1999 response) showing a targeted-outreach intervention outperforming generic advisories, no equivalent intervention-effectiveness study yet exists for wildfire smoke. Multiple sources in the mechanism-evidence audit — most pointedly the 2024 risk-communication scoping review — state this as a known, acknowledged field-wide gap, not a subtle one. This CMDDS's simulation-first posture cannot resolve it; only field-deployment evidence can.
9. **Two Tier 1 constraints cannot conflict for the same individual** *(domain-specific, ★ v1.1)* – Assumptions #2 and #4 above treat "a feasible opportunity always exists" and "human-system handover is resolvable" as separately addressable. Under compound heat-smoke conditions this breaks down: sealing a home protects against PM2.5 but traps heat absent air conditioning, while venting relieves heat but admits smoke — two population-safety (Tier 1) actions in direct opposition for the same household. This is not resolved by a ranking rule; it requires a named Compound-Hazard Protocol Authority who pre-authors condition-triggered guidance (e.g., prioritizing relocation to a shelter that is both clean-air and cooled) before the conflict is encountered live (Step 3 §1, §4; Pre-Deployment Checklist item 12).
10. **Resource pools respect administrative boundaries by default** *(domain-specific, ★ v1.1)* – Modeling resources as location-aware pools (Step 2) does not by itself guarantee cross-boundary availability when a plume spans multiple counties, states, or countries faster than mutual-aid agreements typically activate. Forks deploying in regions with meaningful cross-jurisdictional plume risk must model mutual-aid activation lag explicitly, not assume seamless resource pooling (Step 2 Cross-Jurisdictional Coordination Systems; Pre-Deployment Checklist item 13).

Failure to explicitly model these assumptions will produce optimistic, non-generalizable results. We publish
this CMDDS as a shared grammar, not a finished architecture. Local forks must extend it with triage rules,
bias audits, equity metrics, and explicit allocation logic.

### Evidence Base for the Domain Mechanism (MOKG-PH-A Step 5 Module) ★ v1.2

A dedicated Step 5 mechanism-evidence audit tested Assumption #8 against the historical record — checking
whether the CMDDS's central domain claim is empirically grounded rather than merely plausible — before that
assumption was taken as given elsewhere in this document. Full citations are listed under Related References.

**Instances.** Roughly a dozen independent, triangulable sources across two countries, three US states, and
multiple Australian jurisdictions, spanning 2007–2025: statewide California ED-visit studies linking wildfire
PM2.5 to elevated respiratory-visit risk under limited AC access; a San Diego housed/unhoused case-crossover
study; the Camp Fire/2016–2019 California smoke-event series, showing a lag-structured (not merely
associational) ED-visit signature; income- and housing-tenure disparity studies from the 2008 California
wildfires and a San Francisco Fed community-development brief; the January 2025 Los Angeles firestorm data
brief, which independently confirms both the exposure disparity and the outdoor-worker equity pathway this
CMDDS names as its domain-specific fourth node; and two Australian bushfire events (Black Saturday 2009, Black
Summer 2019–2020), providing genuine cross-national, cross-regulatory-regime replication using a different
data apparatus (household-panel natural-experiment design) than the US claims-data studies.

**Prediction, robustness, and rival adjudication.** The CMDDS's own mechanism predicts a resource-access gap in
smoke-attributable outcomes that does not close once awareness or advisory receipt is accounted for. This
prediction, stated before the literature review, held. The signature survived a genuine change of apparatus
(US insurance-claims data to Australian household-panel and epidemiological data) and a change of building-code
regime. Against the rival Awareness/Risk-Communication model — the dominant existing paradigm, which treats
non-protective behavior mainly as a communication failure — the win criterion (a protection gap persisting
independent of awareness or message quality) was met: a 2026 nationwide analysis found protective-behavior
gaps were reactive rather than anticipatory across all socioeconomic strata, and a 2024 scoping review found
the risk-communication literature has not itself evaluated effectiveness for vulnerable populations.

**Verdict.** The domain-specific mechanism claim clears the module's declared threshold — present in all three
endpoint families (ED-visit/hospitalization disparity by resource access; excess-mortality/hospitalization
disparity by socioeconomic status; direct filtration-effectiveness data, including DIY Corsi–Rosenthal units
performing comparably to commercial HEPA units at a fraction of the cost) — and wins the rival adjudication.

**Honest limitation (carried into Assumption #8 above).** The evidence base establishes that PO is the binding
constraint; it does not yet establish that a PAPO-style delivery system closes the gap faster or further than
existing untargeted filtration and shelter programs. No wildfire-smoke equivalent yet exists to the heatwave
case's Chicago 1995→1999 before/after comparison of a targeted-outreach intervention against generic
advisories. This is a field-wide open question, acknowledged in the field's own literature, not a defect
specific to this CMDDS — and is recorded here as a target for future field-evaluation evidence rather than
resolved by simulation alone.

### Outputs of Interest
Simulations derived from this CMDDS may examine:

- Opportunity uptake, timing, and non-uptake
- Missed, delayed, or infeasible interventions
- Resource utilization, saturation, and bottlenecks
- Differential impacts across vulnerability groups, housing-envelope classes, or locations
- Differential reach across underserved populations (rural / language barrier / no digital access / outdoor-exposed occupations)
- **"Indoor Air Deserts"** — neighborhoods or geographic units where high ambient smoke combines with low residential sealing and limited vehicle/transit access, identified for pre-staging filtration distribution
- Sensitivity to timing, capacity, access, and policy triggers
- Comparative performance of different prioritization rules (see below)
- Disparity delta from bias injection tests, before and after fairness constraints
- Behavior under compound heat-smoke conditions, including detected policy conflicts
- **★ v1.1: Refusal rate**, reported separately from null-output rate and broken down by the five null-output trigger types (see Step 3 §3, §5)
- **★ v1.1: Cross-jurisdictional coordination lag**, where mutual-aid activation is modeled (Step 2, Step 3.5 component 8)

Outputs may be generated at individual, neighborhood, or system levels.

### Prioritization Rules (Required under Scarcity)
When demand exceeds supply, a prioritization rule must be superimposed on the PAPO logic. This rule is not
derivable from clinical risk or policy eligibility alone — it is a normative choice. Simulate and compare at
least two of the following:

| Rule               | Definition                                                                 |
|--------------------|---------------------------------------------------------------------------|
| Clinical risk only | Priority based on age + respiratory/cardiovascular comorbidities + estimated indoor exposure (no barrier weighting). |
| Equity weighted    | Clinical risk × barrier index (e.g., no filtration, poor building envelope, transit dependency, outdoor occupation). |
| Random lottery     | Equal probability among eligible individuals.                             |
| Easiest first      | Minimize resource consumption per intervention (e.g., nearest filtration-kit drop point, shortest delivery route). |
| Worst off first    | Maximize urgency (e.g., highest estimated indoor PM2.5, longest cumulative exposure). |

Measure: final health-proxy status distribution (e.g., cumulative indoor PM2.5 exposure avoided, time to
opportunity), resource utilization, and equity metrics.

**Governance decision required.** Selection among these rules is a normative governance decision, not a
technical optimization. Each deployment fork must (a) compare at least two rules in simulation, (b) document
the rationale for the deployed rule, and (c) obtain governance sign-off before deployment.

> **★ v1.1 Governance note (advisory, not required).** The MOKG-PH-A Case #005-C review recommends
> Equity-weighted as the primary candidate and Worst-off-first as the secondary comparator for the required
> ≥2-rule comparison, since Equity-weighted is the only rule that directly operationalizes all four required
> equity-pathway nodes, including the outdoor-worker pathway that clinical-risk-only scoring would miss
> entirely. This is a recommendation, not a requirement — governance retains full discretion over rule
> selection.

### Equity Metrics (Define Before You Run)

Choose one or more — each encodes a different ethical commitment:

| Metric              | Definition |
|---------------------|------------|
| Equal allocation    | Same proportion served across groups (e.g., by income, race, disability, occupation). |
| Equal outcomes      | Post-event health-proxy status equalized (e.g., mean cumulative indoor PM2.5 exposure). |
| Prioritizing        | Weighted benefit allocation — greater weight assigned to worse-off individuals. |
| Sufficient          | Ensure everyone reaches a minimum safety threshold (e.g., access to filtered air, or a clean-air shelter, within 1 hour). |

> ⚠️ **Important:**
> Defaulting to "no metric" implicitly optimizes for efficiency alone — which systematically disadvantages the
> populations PAPO-Smoke is designed to serve (renters, transit-dependent residents, outdoor workers).
> The equity metric must be selected, documented, and approved by the deployment's governance body **before**
> any simulation run. Always state the selected equity metric explicitly in simulation results.

### Interpretation Guidance
Simulation outputs should be interpreted as:

- Indicators of system responsiveness and feasibility
- Comparative signals across scenarios, regions, or policies
- Evidence of equity gaps in opportunity distribution, including "Indoor Air Desert" mapping
- Stress-test results under constrained resources

Outputs do not represent individual-level prediction or causal inference.

### Ethical and Scope Limits

- No clinical or respiratory outcomes or health predictions are implied
- Fire behavior, plume forecasting, and physiological modeling are out of scope
- Data aggregation is assumed to be privacy-preserving
- Human oversight is integral to intervention delivery
- Simulations are exploratory, not operational decision engines

### Use in Downstream Implementations
Downstream forks may:

- Add region-specific metrics or visualizations
- Integrate real-time AQI monitor feeds (regulatory networks or low-cost sensor networks) or digital twins
- Compare PAPO-Smoke against advisory-only baselines
- Add a compound heat-smoke module that jointly simulates PAPO–Heatwave and PAPO–Smoke logic over the same synthetic population
- Add new failure modes (e.g., sensor/monitor noise, policy conflict, resource shock, cascading multi-day depletion)

All extensions should clearly document assumptions and deviations from this CMDDS.

This step ensures that simulation results are interpretable, comparable, and ethically bounded, while remaining
flexible for diverse implementation approaches.

---------------------------
## Pre-Deployment Checklist

Before any real population is exposed to PAPO-Smoke–driven recommendations, the deployment fork must satisfy
every item below. This checklist consolidates the requirements distributed across Steps 1–4 and the
Simulation-First Protocol.

| # | Requirement | Location in this CMDDS | Status |
|---|---|---|---|
| 1 | Per-region parameterization of resource pools, housing-envelope distribution, and policy constraints | Step 2 | ☐ |
| 2 | All eight Simulation-First Protocol components run and documented (★ v1.1: component 8 added) | Step 3.5 | ☐ |
| 3 | Bias injection performed; disparity delta measured against governance-approved threshold | Step 2 (Bias Injection) | ☐ |
| 4 | Equity metric selected, documented, and approved before simulation run | Step 4 (Equity Metrics) | ☐ |
| 5 | Prioritization rule compared (≥2 rules) and deployment rule approved by governance | Step 4 (Prioritization Rules) | ☐ |
| 6 | Human-system handover boundary specified in writing with governance sign-off | Step 3 §4 | ☐ |
| 7 | Underserved-population equity pathways (rural, language barrier, no digital access, outdoor-exposed occupations) modeled as explicit nodes | Step 2 §5 | ☐ |
| 8 | Null output escalation pathways implemented and verified functional for all five trigger types | Step 3 §3 + Step 4 | ☐ |
| 9 | Jurisdictional compliance check completed for the deployment region | Step 1 (Candidate Cities) | ☐ |
| 10 | Rollback trigger and human-in-the-loop gate configured for live operation | Step 3 §4 + governance | ☐ |
| 11 | Indoor-air infiltration/decay model validated against at least one real or synthetic housing-stock dataset for the deployment region | Step 2 (Entity Attributes) + Step 3 §2 | ☐ |
| 12 | Compound-Hazard Protocol Authority named, with pre-authored sealing-vs-venting (or equivalent) guidance for concurrent heat-smoke Tier 1 conflicts, governance-signed (★ v1.1) | Step 3 §1, §4; Step 4 Assumption #9 | ☐ |
| 13 | Inter-jurisdictional resource-coordination logic (mutual-aid triggers, data-sharing agreements) specified and dry-run tested for deployment regions with cross-boundary plume risk (★ v1.1) | Step 2 (Cross-Jurisdictional Coordination Systems); Step 3.5 component 8 | ☐ |
| 14 | Refusal and null output implemented as structurally distinct, separately-reported data states throughout delivery and strategic-loop aggregation (★ v1.1) | Step 3 §3, §5 | ☐ |
| 15 | Strategic-loop policy-revision decision authority named, with a stated review-and-act cycle length (★ v1.1) | Step 3 §5 | ☐ |

Item 11 is a domain-specific addition: because the entire PO bottleneck in this domain hinges on indoor PM2.5
decay, an unvalidated decay model undermines every downstream feasibility and prioritization calculation.

Items 12–15 were added following the MOKG-PH-A Case #005-C review. Items 12–13 are domain-specific: compound-
hazard governance and cross-jurisdictional coordination did not exist as gates in the heatwave CMDDS, since
neither structural condition arises for a single, spatially-bounded hazard. Items 14–15 close two gaps carried
forward unresolved from the heatwave case (Case #002-C, PAPO-A2 and PAPO-A1 respectively); this analysis
recommends they be back-ported to the heatwave CMDDS's own checklist as well, though that update is outside
this document's scope.

---------------------------
## Illustrative Wildfire Smoke Scenario (Non-Normative)

A regional wildfire drives the local AQI to an unhealthy level (AQI 185, PM2.5 ≈ 120 μg/m³), and a municipal
health alert instructs residents to stay indoors, run HVAC on recirculation with MERV-13+ filtration, or visit
a clean-air shelter. A 62-year-old part-time remote worker with mild asthma receives the alert, understands her
risk, and has no barrier to comprehension — her Awareness & Capability (AC) is high. But she rents a 1970s
walk-up with no central HVAC, only a window A/C unit with a low-efficiency filter; her building's infiltration
rate is high enough that her indoor PM2.5 reaches roughly 78 μg/m³ within three hours of the ambient spike. She
cannot afford a standalone HEPA unit on short notice, and reaching the clean-air shelter 2.5 miles away means a
10-minute walk to a bus stop and an outdoor wait — itself a meaningful acute exposure. In a traditional model
she would be logged as "non-compliant." Under PAPO, her Personalized Opportunity (PO) score falls below the
actionable threshold, and the system escalates past the generic advisory to a targeted micro-intervention:
mobile delivery of a DIY filtration kit and door-to-door transit to the shelter.

This scenario assumes such a targeted intervention is available. In a full simulation, the same individual
might receive a null output if filtration kits are exhausted and transit is unavailable — that failure outcome
is as informative as success, and is exactly the condition the Resource Pools and Null Output requirements in
this CMDDS are designed to surface. At a population scale, running many agents like her identifies "Indoor Air
Deserts" — areas combining high ambient smoke, poor residential sealing, and limited transit access — which
emergency managers can use to pre-stage filtration distribution rather than relying on broadcast messaging
alone.

## Illustrative Compound-Hazard Scenario (Non-Normative) ★ v1.1

A late-summer heat advisory (ambient temperature policy threshold crossed) and a wildfire smoke advisory (AQI
policy threshold crossed) activate on the same afternoon in the same metro area. A household with no air
conditioning and a moderately tight building envelope now faces two simultaneously active Tier 1 safety
guidance sets: the heat protocol recommends opening windows for cross-ventilation, while the smoke protocol
recommends sealing the home and running filtration. Followed independently, these instructions cancel each
other out — venting admits PM2.5, sealing traps heat — and a naively combined system could issue both
recommendations to the same household within minutes of each other.

Under the compound-hazard deferral in Step 3 §1, the policy activation layer detects the concurrent threshold
crossing before generating either single-hazard opportunity, and defers to the named Compound-Hazard Protocol
Authority's pre-authored guidance. In this illustration, that guidance prioritizes relocation to a shelter that
is both clean-air and cooled over either sealing or venting in place; if no such combined-capability shelter
slot is feasible (Step 3 §2), the engine falls back to time-boxed venting during the coolest, lowest-AQI hours
of the day, sourced from local ambient and indoor-decay estimates, with explicit escalation if the household's
indoor PM2.5 or indoor temperature crosses a secondary safety threshold. This is a design pattern, not a
mandated rule; forks must specify their own compound-hazard protocol content, and the pattern here illustrates
what the CMDDS requires structurally (a named authority, pre-authored, condition-triggered) rather than what
that authority must decide.

________________________________________
This repository provides the CMDDS artifact for PAPO wildfire smoke exposure applications. Simulation
implementations are intentionally left open and are expected to reside in forks or downstream projects.

## Related References
- **CMDDS_PAPO_Heatwave v1.2** — the parent architecture this document adapts; see that document's Related References for the underlying PAPO framework publications and the MOKG-PH Case #002-C analysis that shaped the required-elements structure inherited here.
- **MOKG-PH-A Case #005-C (proposed)** — PAPO–Wildfire Smoke Exposure CMDDS v1.0 Case Analysis Report, applying the MOKG-PH-A v2.1 toolkit (Tasks A1–A4), August 2026. Findings incorporated into the v1.1 revision; see Changelog. Case ID is proposed pending confirmation against the internal case ledger.
- **MOKG-PH-A Step 5 Mechanism-Evidence Module (★ v1.2)** — empirical audit of Assumption #8, August 2026. Findings incorporated into the v1.2 revision (Step 4 Evidence Base; see Changelog). Underlying sources:
  - Alahmad et al., *Environmental Research: Health*, 2025 — California statewide ED-visit study, wildfire PM2.5 and AC availability.
  - Casey et al., *Lancet Planetary Health*, 2024 — San Diego housed/unhoused case-crossover study, 2012–2020.
  - Aguilera et al., *Environment International*, 2023 — California statewide lag-structured smoke-event ED-visit analysis, 2016–2019.
  - Reid et al., 2008 California wildfires, as reported in Savich et al., 2026 review — income-disparity in respiratory ED visits and COPD/pneumonia hospitalizations.
  - San Francisco Fed community-development brief — HUD/ACS tract-level housing-tenure and smoke-exposure cross-reference, California, 2014–2018.
  - UCLA Latino Policy and Politics Institute data brief — Los Angeles (Palisades/Eaton/Hurst) firestorms, January 2025.
  - Black Saturday bushfires natural-experiment study (HILDA household panel, triple-differences design), Australia, February 2009.
  - AIHW short-term health-impact assessment and associated epidemiological work; independent Australian commercial-building IAQ analysis — Black Summer bushfires, Australia, 2019–2020.
  - Sohn et al., presented at ATS/AJRCCM, 2026 — nationwide analysis of reactive vs. anticipatory protective behavior across socioeconomic strata.
  - 2024 scoping review of wildfire-smoke risk-communication research (vulnerable-population effectiveness evaluation gap).
  - Corsi–Rosenthal DIY-filtration field and chamber studies — real-world and controlled indoor PM2.5 reduction data.
- Domain rationale for this document was developed from internal design notes: *why extend PAPO to wildfire/smoke exposure*, *why smoke elevates Personalized Opportunity specifically*, *why anchor CMDDS simulation in smoke rather than fire perimeter*, and an illustrative case scenario. The core claim in the second of these is now evidence-grounded via the Step 5 Mechanism-Evidence Module above; the remainder still await your own citations once finalized.
- [Add: regional AQI data source, e.g., AirNow / PurpleAir, once a deployment region is selected]

## Changelog
**v1.0 (2026-08-19)** — Initial release. Adapts the CMDDS_PAPO_Heatwave v1.2 architecture to the wildfire
smoke exposure domain, inheriting all MOKG-PH-informed required elements (bias injection, null output as
required architecture, pre-selected equity metrics, governance-signed human-handover boundary, explicit
underserved-population nodes, Simulation-First Protocol, Pre-Deployment Checklist) from initial release.
Domain-specific additions:

1. Anchored the simulation in smoke plume dynamics rather than fire perimeter (Overview).
2. Added Compound Hazard Interactions as a Core Simulation Question category (Step 1 §6).
3. Added structurally outdoor-exposed occupational populations as a fourth required equity-pathway node (Step 2 §5).
4. Added indoor air quality (IAQ) infiltration/decay modeling as a first-class attribute and translation-logic input (Step 2 Entity Attributes; Step 3 §2).
5. Added assumption #8 ("stay indoors" is not uniformly protective) as the domain-specific mechanism underlying the PO bottleneck (Step 4).
6. Added "Indoor Air Deserts" as an explicit output/interpretation category (Step 4 Outputs of Interest).
7. Added indoor-air decay model validation as Pre-Deployment Checklist item 11.

**v1.1 (2026-08-19)** — Revision following the MOKG-PH-A v2.1 Case Analysis (proposed Case #005-C). The
inherited Hybrid architecture was confirmed to hold without modification (Dignity Audit 5/5; Opportunity-vs-Risk
Framing Audit 5/5). This revision addresses the case's design requirements:

1. Added the **Compound-Hazard Protocol Authority** as a required institutional role and pre-deployment gate,
   resolving the first identified Tier 1-vs-Tier 1 conflict in the PAPO lineage — heat-vs-smoke sealing/venting
   guidance (Step 2 §2; Step 3 §1, §4; Step 4 Assumption #9; Checklist item 12; new Illustrative Compound-Hazard
   Scenario).
2. Added **Cross-Jurisdictional Coordination Systems** as a new required conceptual entity, with mutual-aid
   inputs, resource pools, and a dry-run protocol component, closing a gap in the CMDDS's own data-category
   inventory for plume footprints spanning governance boundaries (Step 1 §7; Step 2 §6; Step 3 §1, §2, §4; Step
   3.5 component 8; Step 4 Assumption #10; Checklist item 13).
3. Architected an explicit **refusal-vs-null-output distinction** throughout the delivery and feedback logic,
   closing a gap first identified in the heatwave case (Case #002-C, PAPO-A2) and now resolved at the shared
   PAPO architecture level (Step 2 Attribute Categories; Step 3 §3, §5; Step 3.5 component 7; Step 4 Outputs;
   Checklist item 14).
4. Named the **strategic-loop policy-revision decision authority** as a required specification, closing a
   second gap carried forward from the heatwave case (Case #002-C, PAPO-A1) (Step 3 §5; Checklist item 15).
5. Added an explicit **dignity gate for occupational-exposure data partnerships** with employers, the least
   evidence-mature of the four required equity pathways (Step 2 §5).
6. Added a **consent/opt-out mechanism** for continuous dynamic individual inputs (location, connectivity,
   check-ins), closing the Dignity Audit's documented caveat (Step 2 Phase B).
7. Reframed policy rules as **versioned, provenance-tagged nodes** with an explicit re-encode-latency field
   (Policy-Aware Adaptation Layer), rather than leaving this translation implicit (Step 2 Phase A; Step 3 §1).
8. Added a non-binding **governance note** recommending Equity-weighted as the primary and Worst-off-first as
   the secondary prioritization-rule comparator (Step 4 Prioritization Rules).

Two items from the Case #005-C review were assessed as informative rather than requiring a CMDDS change and are
recorded here for traceability rather than as document edits: (a) the recommended prioritization-rule comparison
order, captured above as advisory guidance rather than a new requirement; and (b) a proposed MOKG-PH-A toolkit
sub-category for Tier 1-vs-Tier 1 conflicts, which is a change to the analytical toolkit itself, not to this
CMDDS.

**v1.2 (2026-08-19)** — Revision incorporating a MOKG-PH-A Step 5 Mechanism-Evidence module: an empirical audit
of Assumption #8 (roughly a dozen independent sources, US and Australia, 2007–2025) that tested the CMDDS's
central domain claim against a pre-declared prediction, a cross-vantage robustness check, and a rival-model
adjudication. Unlike v1.1, this revision adds no new required entities, authorities, or checklist items — it is
evidentiary grounding for an assumption already in the document, not a new governance requirement. Changes:

1. Added an **Evidence Base** subsection (Step 4) summarizing the module's instances, prediction, robustness,
   rival adjudication, and verdict (mechanism claim clears threshold: 3/3 endpoint families, wins rival
   adjudication against the Awareness/Risk-Communication model).
2. Expanded **Assumption #8** with the evidence-base summary and a stated limitation: the audit establishes
   that Personalized Opportunity is the binding constraint, but not yet that a PAPO-style delivery system closes
   the gap faster than existing untargeted programs — no wildfire-smoke equivalent yet exists to the heatwave
   case's Chicago 1995→1999 before/after intervention comparison. This is recorded as an open field-wide
   question, not resolved by this revision.
3. Added a pointer from Step 1 System Purpose to the new Evidence Base, so the CMDDS's central claim is
   traceable to its grounding at first mention.
4. Added the module's full source list to Related References.

Future revisions should follow the same versioning and ★-marking convention used by the heatwave CMDDS.
