# Authority Boundaries

## Separation of powers

```text
Runtime Authority     -> executes Behaviors and owns public Ok/Error emission
Actor                  -> proposes hypotheses, plans, and actions
Verifier               -> independently assesses outcomes
Reward Authority       -> converts evidence into learning signals
Training Manager       -> orchestrates optimization jobs
Governor               -> accepts or rejects candidate artifacts
Registry               -> records approved/rejected artifacts and provenance
```

No role may silently inherit the authority of another role.

## Mandatory constraints

### Actor
May:
- read authorized context;
- retrieve Skills;
- formulate hypotheses;
- request Actions;
- propose mutations.

Must not:
- declare its own result verified;
- rewrite source evidence;
- directly assign its own reward;
- promote its own model artifact.

### Verifier
Must evaluate from evidence independent of the actor's self-report whenever deterministic evidence is available.

Verifier independence means at minimum that the verification decision cannot be mutated by the actor being evaluated.

### Reward Authority
Reward is a derived projection, never source truth.

The priority order is:

```text
deterministic measurement
  > independent formal/verifier result
  > bounded judge model
```

Judge-model output must include model identity, prompt/policy version, confidence, and source evidence references.

### Training Manager
A completed training job means only that a candidate artifact was produced. It does not imply semantic acceptance.

### Governor
Promotion requires evaluation evidence. Governor policy must be versioned and auditable.

## Forbidden authority collapse

The following configurations are invalid:

```text
Actor == sole Verifier
Actor == Reward Authority
TrainingBackend == Governor
Reward == actor self-score
training completion == production promotion
```

## Runtime boundary

Learning occurs after or beside execution, not inside the authority path that determines the canonical runtime result.

A learning system failure may emit its own operational event, but cannot retroactively change a valid runtime `Ok` into `Error`, or vice versa.
