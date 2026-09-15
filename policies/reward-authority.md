# Reward Authority Policy

## Rule

The trained actor must never be the sole authority that determines its own reward.

Reward is a projection over immutable evidence. It is not source truth and it is not an execution result.

## Evidence precedence

```text
1. deterministic measurement
2. independent verifier result
3. bounded judge-model assessment
```

A lower-priority source must not override a contradictory higher-priority source without an explicit policy rule.

## Deterministic reward sources

Prefer deterministic sources whenever available, including:

- invariant checks;
- test outcomes;
- proof verification;
- runtime result classification;
- exact resource measurements;
- mutation counts;
- retry counts;
- idempotency checks;
- unauthorized side-effect detection;
- source-integrity checks.

## Independent verifier sources

Verifier-derived reward may be used when the result depends on semantic assessment that is outside the trained actor's authority.

The verifier result must include evidence references and verifier identity.

## Judge models

A judge model is a fallback, not the primary authority.

Every judge-derived component must record:

- judge model identity;
- prompt/policy version;
- evidence provided to the judge;
- raw judge output or its immutable hash;
- normalized score;
- confidence when available.

Judge output must not silently override deterministic failures.

## Reward hacking protections

The following outcomes must not produce positive reward merely because the immediate target symptom disappeared:

- deleted or weakened tests;
- disabled invariants;
- swallowed errors;
- fabricated evidence;
- unauthorized source mutation;
- hidden side effects;
- bypassed verifier;
- changed acceptance criteria;
- changed reward policy by the trained actor.

## Reproducibility

A reward must be reproducible from:

```text
episode hash
+ evidence hashes
+ reward policy version
+ verifier outputs
+ judge metadata, if any
```

Changing weights creates a new reward projection; it does not mutate the original episode.
