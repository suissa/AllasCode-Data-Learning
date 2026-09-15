# Learning Plane Architecture

## Purpose

The Learning Plane converts verified runtime experience into candidate model improvements while preserving AllasCode authority boundaries.

It is asynchronous with respect to production execution. Production behavior must not depend on a training step completing successfully.

## Pipeline

```text
Runtime Evidence
  -> Evidence Intake
  -> Episode Builder
  -> Experience Store
  -> Reward Engine
  -> Dataset Builder
  -> Training Manager
  -> Training Backend
  -> Candidate Model
  -> Model Evaluator
  -> Governor Gate
  -> Model Registry
```

## Components

### Evidence Intake
Accepts references to immutable execution evidence. It must not rewrite source evidence.

Required properties:
- source identity;
- source hash;
- capture time;
- producer identity;
- execution correlation id;
- evidence type;
- integrity metadata.

### Episode Builder
Builds a normalized learning episode from one or more immutable evidence references.

It may normalize representation, but must preserve lineage back to every source artifact.

### Experience Store
Stores immutable episodes. Corrections produce new episode versions; existing episodes are not silently rewritten.

### Reward Engine
Derives reward dimensions from deterministic evidence, independent verifier outputs, and only then bounded model judgments when deterministic verification is impossible.

### Dataset Builder
Selects episodes according to a declared policy and produces a reproducible dataset snapshot.

Every training dataset must be addressable by content hash and selection-policy version.

### Training Manager
Creates and tracks training runs. It owns orchestration, not model acceptance.

### Training Backend
Backend-neutral interface implemented by ART, TRL, Unsloth, or future systems.

### Model Evaluator
Evaluates candidate models against frozen tests, holdout episodes, adversarial cases, invariants, and regression suites.

### Governor Gate
Makes the acceptance decision. Training success is not equivalent to production approval.

### Model Registry
Stores approved and rejected candidate artifacts, including provenance and evaluation evidence.

## Invariants

1. Learning never owns runtime execution authority.
2. A trained actor cannot be the sole verifier of its own result.
3. Reward derivation must be reproducible from referenced evidence and policy version.
4. Source evidence is immutable.
5. Episode lineage is mandatory.
6. Dataset membership must be reproducible.
7. Candidate model promotion requires independent evaluation.
8. A backend-specific object cannot become a canonical AllasCode domain primitive.
9. Production rollback must remain possible without retraining.
10. A failed learning pipeline must not convert a valid runtime result into `Error`.
