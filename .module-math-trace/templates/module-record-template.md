# Module Analysis Record: <module name>

## Record metadata

- **Module ID:** `<stable-slug>`
- **Module/symbol:** `<class/function/component>`
- **Source paths:** `<repository-relative paths>`
- **Call chain:** `<upstream → module → downstream>`
- **Status:** `in progress | statically analyzed | runtime verified | needs revalidation`
- **Created / last reviewed:** `<date if available>`
- **Code revision / fingerprint:** `<commit, relevant diff, or unknown>`
- **Scope:** `<what is covered>`
- **Exclusions:** `<what is not covered>`

## 1. Role in the system

Explain the module's responsibility and position in the dataflow.

## 2. Input/output contract

| Name | Source | Shape | Axis meanings | dtype/device/range | Notes |
|---|---|---|---|---|---|
| | | | | | |

## 3. Complete computation ledger

| Step | Source location / expression | Formula | Input shape | Output shape | Parameters | Meaning / information effect | Evidence status |
|---|---|---|---|---|---|---|---|
| | | | | | | | |

## 4. Main equations and symbol definitions

Write the exact implementation equation. Define every symbol and index. Separate code behavior from paper/document intention.

## 5. Worked numerical example

Label toy values as illustrative. Record every intermediate result and shape. If values depend on learned weights, state the assumed weights.

## 6. Training and gradients

- Trainable parameters:
- Shared parameters:
- Detach/stop-gradient:
- Loss path:
- Important gradient or scale effects:
- Not checked:

## 7. Verification evidence

- **Static code inspection:**
- **Runtime command/test/probe:**
- **Observed shapes/results:**
- **Paper/docs comparison:**
- **Not verified and why:**

Never write “passed” unless the test actually ran.

## 8. Findings and limitations

### Verified code facts
- 

### Mathematical derivations
- 

### Paper/document claims
- 

### Inferences (not established facts)
- 

### Unknowns / unresolved questions
- 

## 9. Current conclusion

Summarize what the module computes and what its output means. Distinguish intermediate representation from final prediction.

## 10. Next steps

Ordered list of exact operations, questions, tests, or derivations to perform.

## 11. Revision history

| Revision/date | Change | Evidence/reason | Supersedes |
|---|---|---|---|
| r0 / <date> | Initial record | | |

## 12. Continuity marker

- **Completed:** 
- **Current finding:** 
- **Next:** 
