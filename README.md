# AllasCode Data Learning

AllasCode Data Learning is the **Learning Plane** of the AllasCode architecture.

Its purpose is to transform verified runtime experience into reusable learning signals without giving the learning system authority over execution, acceptance, or truth.

The central separation is:

```text
Knowledge      -> Skills
Experience     -> Episodes
Learning       -> Training backends
Execution      -> Runtime
Evidence       -> Evidence Plane
Verification   -> Independent Verifiers
Memory         -> Memory Plane
```

The Learning Plane consumes immutable evidence produced by the runtime, builds training episodes, derives rewards from independent verification, trains candidate model adaptations, evaluates them, and publishes only approved model artifacts.

## Core principle

```text
Observe -> Hypothesize -> Act -> Verify
             ^                    |
             |------ Learn -------|
```

Learning is **out of the runtime critical path**. The runtime remains deterministic and authoritative for execution. A model may propose a hypothesis or action, but it cannot self-authorize its execution, self-verify its result, or define its own reward.

## Authority model

```text
Actor != Verifier != Reward Authority != Runtime Authority
```

- **Runtime** owns execution and public `Ok/Error` emission.
- **Agent** may interpret evidence, form hypotheses, choose strategies, and propose actions.
- **Verifier** independently evaluates the outcome.
- **Reward Calculator** derives learning signals from immutable evidence and verifier outputs.
- **Learning Plane** trains candidate model adaptations.
- **Governor / Acceptance layer** decides whether a learned artifact may be promoted.

## Architecture

```text
AllasCode Runtime
      |
      | events / traces / logs / metrics / proofs
      v
Evidence Plane
      |
      | immutable verified execution evidence
      v
Episode Builder
      |
      v
Experience Store
      |
      +------------------------------+
      |                              |
      v                              v
Reward Engine                  Dataset Builder
      |                              |
      +---------------+--------------+
                      |
                      v
               Training Manager
                      |
             TrainingBackend interface
             /        |         \
           ART        TRL      Unsloth
          GRPO       SFT/RL      ...
                      |
                      v
                Candidate Model
                      |
                      v
                Model Evaluator
                      |
                      v
                 Governor Gate
                      |
                      v
                 Model Registry
```

## Why this exists

Traditional fine-tuning teaches a model from examples. AllasCode Data Learning is designed to learn from **verified experience**.

A healing episode can contain:

```text
initial state
+ immutable source evidence
+ event history
+ traces
+ logs
+ metrics
+ hypothesis
+ selected actions
+ mutations
+ verifier result
+ proof
+ final outcome
```

This allows agents such as `CodeManager`, `CodeHealer`, `SystemHealer`, and other cognitive agents to learn strategies such as:

- which hypothesis should be investigated first;
- which evidence is causally relevant;
- when not to mutate anything;
- how to minimize the mutation surface;
- when escalation to Human-in-the-Healing-Loop is appropriate;
- which recovery strategy succeeds under a given context.

The objective is **not** to make a model memorize AllasCode semantics. Declarative platform knowledge belongs in Skills. Learning is reserved primarily for heuristics, prioritization, strategy, adaptation, and decision policies.

## ART integration

[OpenPipe ART](https://github.com/OpenPipe/ART) may be used as an initial reinforcement-learning backend, particularly for GRPO-based multi-step agent training.

ART is an implementation detail behind the `TrainingBackend` contract. The Learning Plane must remain backend-independent.

```text
TrainingBackend
  |- ART / GRPO
  |- TRL
  |- Unsloth
  |- future RL/SFT systems
```

No ART-specific object, lifecycle, or storage model is allowed to become a normative AllasCode domain contract.

## Reward safety

Reward must never be controlled by the same actor being trained.

Bad examples:

```text
reward = "error disappeared"
reward = "tests pass"
reward = LLM_self_score
```

These permit reward hacking such as suppressing errors, modifying tests, weakening invariants, or hiding failures.

AllasCode reward derivation follows the authority order:

```text
deterministic evidence > independent verifier > bounded judge model
```

Example reward dimensions:

```yaml
positive:
  intent_satisfied: 0.25
  invariants_preserved: 0.20
  proof_accepted: 0.20
  tests_passed: 0.15
  causal_hypothesis_quality: 0.10
  minimal_mutation: 0.05
  resource_efficiency: 0.05

penalties:
  unnecessary_retry: -0.02
  unnecessary_mutation: -0.05
  invariant_violation: -0.50
  unauthorized_side_effect: -1.00
  fabricated_evidence: -1.00
```

Weights are policy inputs and are not part of the immutable episode itself.

## Repository structure

```text
architecture/
  learning-plane.md
  authority-boundaries.md
  episode-model.md
  reward-model.md
  training-backend.md

schemas/
  episode.schema.json
  reward.schema.json
  training-run.schema.json

policies/
  reward-authority.md
  promotion-policy.md

examples/
  code-healing-episode.json
```

## Non-goals

This repository does not define:

- runtime execution semantics;
- `Ok/Error` emission authority;
- application business rules;
- model-provider APIs as canonical architecture;
- self-verification by the trained actor;
- direct online weight mutation in the production runtime.

## Status

Initial architectural specification. Interfaces and schemas are intentionally backend-neutral so ART can be used immediately without coupling the AllasCode learning model to ART itself.
