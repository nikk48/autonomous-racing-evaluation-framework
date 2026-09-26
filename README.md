# Autonomous Racing: Experimentation & Evaluation Framework

Python framework for systematic experimentation, hyperparameter optimisation (Optuna) and reproducible, auditable evaluation of autonomous-racing agents in [TORCS](http://torcs.sourceforge.net/).

Built as Part C of an MSc dissertation at the University of Manchester (Alliance Manchester Business School), based on an industry-proposed brief from IBM: *"Systematic Experimentation and Hyperparameter Optimisation for Reproducible Evaluation of Autonomous Racing Agents."*

---

## What this is

A working evaluation pipeline that:

- Runs **systematic hyperparameter optimisation** (grid search, random search, Optuna) on a hand-coded racing controller
- **Verifies reproducibility** independently — using SHA-256 file hashes and telemetry timestamps, rather than trusting run logs alone
- **Detects data-quality problems**: the verification process caught a real logging defect during this project, where two run-log rows pointed to the same telemetry file
- **Fairly labels and compares evidence from different sources**, including externally generated artefacts, without blending them into one misleading ranking
- Reports **safety and completion outcomes**, and computational-efficiency indicators (runtime, decision latency, CPU, memory), alongside lap time

## What this isn't

This is **not** a claim to have built the fastest autonomous racing agent. The contribution is methodological: a trustworthy way to test, verify, and compare autonomous-racing evidence — not a performance benchmark.

The framework's own hand-coded, Optuna-tuned configuration produced a **modest, verified 1.2-second improvement** over baseline (see Results below). A much faster lap time from an externally developed PPO-based policy also appears in the results table below; **that policy was built by a teammate as part of the wider group dissertation project, not by this framework or by me** — it's included only to test the framework's ability to score a fundamentally different type of evidence on equal footing.

---

## Methodology

Evaluation followed a staged approach:

1. **Dummy-mode validation** — a simplified simulator validated the logging and analysis pipeline before any live testing (81 grid-search configurations, 5 random-search configurations, 10 Optuna trials).
2. **Hyperparameter optimisation** — grid search, random search and Optuna were applied to a hand-coded controller's parameters.
3. **Live evaluation** — selected configurations were tested live in TORCS on the Corkscrew track, with 3 valid runs each.
4. **Reproducibility verification** — repeated live runs were checked against their underlying raw telemetry files (file path, SHA-256 content hash, and internal timestamps), not accepted on the basis of run-log labels alone.

## Results

### My framework's own result (rule-based controller + HPO)

All configurations below were designed, tuned and evaluated within this project, verified across 3 live runs each (100% completion, 0 crashes, 0 off-track events):

| Configuration      | Source              | Mean lap time (s) | vs. baseline |
|---------------------|----------------------|--------------------:|-------------:|
| Untuned baseline     | rule-based           | 196.726              | —            |
| GRID_030             | grid search          | 199.806              | −3.080s      |
| GRID_040             | grid search          | 198.906              | −2.180s      |
| **OPTUNA_LIVE_001**  | **Optuna (this project)** | **195.526**    | **+1.200s**  |

The Optuna-derived configuration produced a verified **1.2-second improvement over baseline (0.61%)**, confirmed across 3 live runs via SHA-256 file-hash and telemetry-timestamp verification. This process also correctly flagged one earlier logging defect during verification.

The two grid-search configurations favoured by dummy-mode (offline) validation actually **underperformed** once tested live — a result reported rather than discarded, since it demonstrates why offline and live evidence must be evaluated separately rather than assumed to transfer directly.

### Comparator artefact (not developed in this project)

| Evidence source      | Controller             | Mean lap time (s) | Developed by |
|------------------------|--------------------------|--------------------:|--------------|
| Externally generated   | PPO-based policy          | 107.298              | A teammate, as part of the wider group dissertation project |

This PPO-based result is **not this project's agent and not a contribution of this project**. It was imported only to test whether the framework could ingest, label, and fairly score an external artefact of a substantially different type alongside its own rule-based configurations. Its lap time demonstrates that capability — it should not be read as this project's own performance result.

### Safety, completion and efficiency

All four framework-evaluated configurations completed 100% of valid runs with zero crashes and zero off-track events. Runtime and decision latency were similar across configurations (~12.3–12.5s runtime, ~0.006–0.007ms latency); CPU and memory readings varied more and are treated cautiously, since they may reflect other host-machine processes rather than the controller itself.

---

## Limitations

- Live testing covered a single track (Corkscrew) with a small sample size (3 runs per configuration) — reproducibility under these fixed conditions should not be read as a broader robustness claim across tracks, seeds, or disturbed driving conditions.
- CPU and memory measurements may include noise from other host-machine processes.

---

## Repository structure
