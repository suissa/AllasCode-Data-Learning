# Model Promotion Policy

## Principle

A successful training run produces a **candidate artifact**, never an automatically trusted production model.

Promotion is a separate governance decision based on independent evaluation.

## Required promotion evidence

A candidate must provide:

- immutable model artifact hash;
- base-model identity;
- training backend and backend version;
- training dataset hash;
- dataset-selection policy version;
- reward policy version;
- training configuration hash;
- holdout evaluation results;
- regression results against the currently approved model;
- invariant/safety evaluation;
- adversarial or reward-hacking evaluation when applicable;
- provenance linking the artifact back to training episodes.

## Promotion states

```text
trained
  -> evaluating
  -> accepted | rejected | inconclusive | compromised | blocked
```

`trained` is never equivalent to `accepted`.

## Minimum acceptance rules

A candidate may be accepted only when:

1. mandatory invariants pass;
2. no integrity or provenance check is compromised;
3. it does not regress a protected evaluation dimension beyond policy tolerance;
4. the Governor receives sufficient independent evaluation evidence;
5. rollback metadata is available;
6. artifact identity and lineage are complete.

## Status semantics

- **accepted**: candidate may enter the approved registry according to deployment policy.
- **rejected**: evidence is sufficient to conclude the candidate fails acceptance criteria.
- **inconclusive**: evidence is insufficient to safely decide.
- **compromised**: integrity, provenance, evaluation, or evidence trust has been violated.
- **blocked**: evaluation or promotion cannot proceed because a prerequisite or policy gate prevents it.

These states must not be collapsed into a generic failure because their recovery semantics differ.

## Rollback

Every promoted artifact must retain a reference to the previously approved artifact and the evidence that justified promotion.

Rollback must not require retraining.

## Production learning

Direct online mutation of production model weights is prohibited by default.

Production experience may be collected continuously, but model adaptation follows:

```text
collect -> build episodes -> reward -> train candidate -> evaluate -> govern -> promote
```

Any future online-learning mode requires a separate explicit policy and cannot weaken the authority boundaries defined by this repository.
