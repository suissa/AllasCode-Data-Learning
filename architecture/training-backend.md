# Training Backend Contract

## Goal

Training systems are adapters. They do not define the AllasCode learning domain.

The canonical contract is backend-neutral so ART, TRL, Unsloth, or future systems can be replaced without changing episode, reward, evaluation, or governance semantics.

## Input

A backend receives a frozen training request containing:

```text
training_run_id
base_model
candidate_name
dataset_snapshot
training_method
training_parameters
random_seed
resource_policy
artifact_destination
```

The dataset snapshot must be immutable and content-addressed.

## Output

A backend returns a candidate artifact descriptor:

```text
artifact_uri
artifact_hash
base_model
training_method
backend
backend_version
started_at
finished_at
training_metrics
training_config_hash
dataset_hash
```

This result means only that training produced an artifact.

It does not mean the artifact is safe, correct, or promoted.

## ART adapter

ART may implement this interface for multi-step agent reinforcement learning, including GRPO and trajectory-based training.

The adapter is responsible for translating:

```text
AllasCode Episode + Reward
        -> ART trajectory / rollout representation
        -> training
        -> candidate artifact descriptor
```

The translation must preserve:

- episode identity;
- source lineage;
- reward-policy version;
- trajectory grouping semantics;
- model/base-model identity;
- reproducible training parameters.

ART-specific fields must remain inside the adapter boundary.

## Required adapter properties

Every backend adapter must be:

- deterministic where the backend permits, with seeds recorded;
- idempotent at the orchestration layer for the same run id;
- resumable or explicitly non-resumable;
- observable;
- cancellable when supported;
- provenance-preserving;
- unable to promote its own output.

## Capability declaration

A backend should declare capabilities rather than forcing the orchestrator to infer them.

Example:

```yaml
backend: art
capabilities:
  methods:
    - grpo
  parameter_efficient_adapters:
    - lora
  distributed_training: true
  resume: true
  online_inference_during_collection: true
```

Capability discovery is descriptive only and does not transfer authority to the backend.
