# PAPO–Wildfire Smoke Exposure

**Conceptual Model Design Documentation for Simulation (CMDDS)** for extending the Policy-Aware Personalized
Opportunity (PAPO) framework to wildfire smoke (PM2.5) exposure response.

This repository holds the CMDDS specification only — it defines *what* to simulate and *why*, while
deliberately leaving *how* to implement (code, parameters, platforms) open to forks and downstream projects.

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23485910.svg)](https://doi.org/10.5281/zenodo.23485910)

- **Current version:** 1.2 — see [`CMDDS_PAPO_Wildfire_Smoke.md`](./CMDDS_PAPO_Wildfire_Smoke.md) for the full specification, including the Changelog at the end of the document. Each release (v1.0, v1.1, v1.2) is tagged in this repo's history and archived as its own Zenodo DOI.
- **Sibling framework:** [PAPO-Heatwave-AI](https://github.com/Min-PH/PAPO-Heatwave-AI) — the parent architecture this CMDDS extends to a second acute environmental hazard.
- **Author:** Min Wu, PhD — Zilber College of Public Health, University of Wisconsin-Milwaukee

## What this is

PAPO addresses the gap between population-level wildfire smoke policy (AQI-triggered advisories, clean-air
shelter openings, filtration distribution) and an individual's actual, structurally constrained ability to act
on that policy. This CMDDS specifies the entities, inputs, translation logic, required governance gates, and
simulation protocol needed to build and stress-test PAPO-Smoke implementations — it is a simulation-first
artifact, not an operational system.

## Status

This is a Stage 1 knowledge-graph specification and Simulation-First Protocol artifact under the MOKG-PH
governance framework. Implementations targeting real-world deployment must satisfy the Pre-Deployment
Checklist in the CMDDS document before any real population is exposed to PAPO-Smoke–driven recommendations.

## Contributing / forking

Contributors are encouraged to fork this repository and adapt PAPO-Smoke to their own city or region. All
extensions should clearly document assumptions and deviations from this CMDDS.

## Citation

See [`CITATION.cff`](./CITATION.cff), or cite:

> Min Wu. CMDDS for PAPO–Wildfire Smoke Exposure v1.2 – Conceptual Simulation Framework. [DOI to be added once archived on Zenodo]
