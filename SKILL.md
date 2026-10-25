---
name: module-math-trace
description: Deeply analyze a project module from actual source code through equations, tensor shapes, intermediate values, gradients, and dataflow. Use for detailed module explanation or verification, and when the user says “迭代分析”, “迭代本模块”, or “优化本次分析”.
---

# Module Math Trace

## Mission

Explain a selected project module down to each meaningful computation step. Connect actual source code, equations, tensor shapes and axis meanings, numerical behavior, gradient paths, and the module's role in the system.

Repository code is the primary evidence for what executes. Papers, comments, and documentation may explain intended design, but must not be silently substituted for implementation behavior.

Do not modify application code unless explicitly asked. Keep analysis and probes read-only with respect to tracked source files.

## When to use

Use for deep explanations, mathematical audits, tensor-shape tracing, code/paper comparisons, and diagnosis of intermediate values, gradients, or shape mismatches. Do not use for a shallow overview or an unrelated general tutorial.

## Required rules

1. Inspect actual source code before asserting implementation behavior.
2. Trace operations in execution order. Never skip meaningful intermediate steps.
3. Every meaningful operation must include input shape, output shape, axis meanings, and mathematical effect.
4. Define every axis and symbol. Do not write only [B,T,D] without explaining B, T, and D.
5. Do not invent dimensions, parameter values, runtime results, or tests.
6. Label evidence: [Code] directly visible in source; [Math] derived mathematically; [Paper/Docs] stated in identified documentation; [Inference] plausible but not established; [Unverified] requires unavailable runtime/config/data.
7. Label toy values as illustrative, not actual project output.
8. Check broadcasting, padding, indexing, dimension order, residuals, masks, and reductions explicitly.
9. Do not equate elementwise multiplication with similarity unless aggregation/normalization actually makes it a similarity measure.
10. Never claim a test ran or passed unless it actually ran.
11. Do not ask the user to repeat information already available in the conversation or records.
12. If work spans responses, finish at a logical boundary and record exactly what is complete and what comes next.

## Step 1 — Resolve the target

- Identify repository root and read applicable AGENTS.md instructions.
- Resolve the target to exact file paths, symbols, call sites, configuration, tests, and downstream consumers.
- Identify the execution path: forward method, function, training step, inference pipeline, or equivalent.
- Define the boundary: input producer, operations inside the target, output consumers, and whether the target is reused or called at multiple scales.
- Never infer implementation solely from a class/function name.

## Step 2 — Load persistent module memory

Before analysis, read .module-math-trace/INDEX.md and the matching file under .module-math-trace/records/. Use .module-math-trace/templates/module-record-template.md when creating a record.

If code/config changed since the record was written, mark affected claims as needing revalidation. Reuse verified facts, but re-check source locations. Do not treat chat history alone as durable memory.

## Step 3 — Establish input/output contracts

For each tensor/array, record its variable name and source; shape with named axes; layout (for example B×T×C versus B×C×T); dtype/device/units/range when relevant; whether dimensions are fixed, configured, symbolic, or runtime-dependent; masks/padding/valid lengths/timestamps; preprocessing; and whether the tensor is learned, fixed, cached, or derived.

If an exact dimension cannot be established statically, state the symbolic shape and what evidence is missing. Never silently assume batch size, sequence length, channel count, or layout.

## Step 4 — Map dataflow

Show input/source → preprocessing/upstream module → target → downstream consumer/output. For neural networks, include the enclosing block and repeated structures such as feature pyramids, residual blocks, attention blocks, or detection heads.

Explain whether the output is an intermediate representation, probability, class score, offset, mask, state, loss, or decoded prediction.

## Step 5 — Trace every meaningful operation

Inspect the actual path in execution order. Cover relevant indexing/slicing, reshape/view/flatten/transpose/permute, FC/linear layers, embeddings, convolution/depthwise/grouped convolution, pooling, normalization, activation, softmax/sigmoid, addition/concatenation/multiplication/division, gating/residuals, attention, interpolation/resampling, temporal downsampling, positional encoding, loss, masks, reductions, and decoding.

For every operation answer:
1. What exact expression does the code execute?
2. What are the input shapes and axis meanings?
3. What formula represents it?
4. What are the output shape and axis meanings?
5. What learned parameter shapes are involved?
6. What information is mixed, preserved, compressed, broadcast, or discarded?
7. Why may it be useful here?
8. What assumptions, limitations, and failure modes apply?

Split operations into separate steps whenever merging them would hide a mathematical operation or shape change.

## Step 6 — Derive shapes explicitly

Use actual framework semantics and parameters:
- Linear: [..., Din] → [..., Dout]; PyTorch weight is normally [Dout, Din].
- Conv1d input [B,Cin,L]: Lout = floor((L + 2p - d(k - 1) - 1)/s + 1), where k is kernel size, s stride, p padding, d dilation.
- Conv2d/Conv3d: calculate each spatial/temporal axis separately.
- Depthwise convolution: verify groups and channel multiplier in code.
- Pooling/downsampling: calculate output lengths and boundary behavior.
- Broadcasting: show original shapes, logical expanded shape, and broadcast axes.
- Addition/residual: prove shape compatibility or identify projection/broadcast.
- Concatenation: name the axis and calculate resulting size.
- Reshape/permutation: show old and new axis meanings.
- Normalization: state which axes contribute to statistics.
- Attention: show Q/K/V, score, mask, probability, output shapes, and softmax axis.
- Interpolation: state old/new lengths and coordinate alignment.
- Loss: show shapes before reduction, mask, reduction method, and denominator.

These are checks, not substitutes for inspecting code.

## Step 7 — Derive the mathematics

For each nontrivial expression:
1. Start from the exact code expression.
2. Define every symbol, index, axis, parameter, and operator.
3. Rewrite it as a formula.
4. Expand compound operations into smaller steps.
5. Expand vectors/matrices elementwise when useful.
6. Explain mathematical effect, assumptions, and limits.
7. Separate algebraic consequences from stated design intent.
8. When relevant, derive local gradients and identify gradient flow or detach/stop-gradient paths.

Example: for z = a * (a + b), show z = a ⊙ (a + b) and z_d = a_d² + a_d b_d. Explain that this is per-dimension multiplicative interaction, not itself a scalar similarity score. Do not claim a design is optimal without evidence.

## Step 8 — Numerical walkthrough

For difficult operations, use a small illustrative tensor (for example T=3, D=2). Show the input, each intermediate tensor in order, arithmetic, shape after every step, and final output. Clearly label toy values. If output depends on learned weights, use explicitly hypothetical weights or keep the result symbolic.

## Step 9 — Training and gradients

Where relevant, identify trainable parameters, parameter sharing, detach operations, loss-to-parameter paths, gradient gating, masks/reductions, saturation, scale sensitivity, and gradient imbalance. Inspect custom backward functions if present; otherwise state what remains unknown.

## Step 10 — Verify

Prefer low-cost reliable checks: inspect tests/configurations; run a focused existing test if safe; use a small input or temporary non-destructive probe to assert/print shapes; compare static derivations with runtime; compare implementation with papers/docs when intent matters.

Do not install packages, download large checkpoints, launch expensive training, or overwrite files without need and authorization. If execution is unavailable, label the result “static analysis only” and list checks that remain. If code and paper disagree, report both, the exact discrepancy, and its impact.

## Step 11 — Update durable records

After analysis, update:
- .module-math-trace/records/<module-id>.md — full module analysis.
- .module-math-trace/INDEX.md — registry, status, and next step.
- .module-math-trace/iterations/CHANGELOG.md — only for meaningful changes to the Skill workflow or record schema, not ordinary module findings.

Record source paths, call chain, input/output contracts, computation ledger, equations, parameters/configuration, numeric examples, verification evidence, unresolved questions, conclusions, next steps, and revision history. Never delete contradictory historical conclusions; mark old claims superseded and explain why.

If project write access is unavailable, provide the proposed update as an artifact or state explicitly that persistence could not be completed. Never claim a record was saved if it was not.

## Explicit iteration workflow

Triggers: “迭代分析”, “迭代本模块”, “优化本次分析”, “iterate this module”, “iterate the analysis”.

1. Resolve the target from the current conversation. If unclear, ask which module.
2. Read the index and matching record before further analysis.
3. Summarize in 1–3 lines what is established and unresolved.
4. **Before doing further analysis, ask which direction the user wants.** Adapt options to the record. Typical directions:
   - A. 数学深化 — equations, term-by-term derivations, assumptions, edge cases.
   - B. 张量与数值追踪 — intermediate shapes, broadcasting, hand-checkable examples.
   - C. 代码核验 — call sites, configuration, runtime behavior, paper/code discrepancies.
   - D. 梯度与训练 — gradients, losses, detach, parameter sharing, optimization.
   - E. 架构作用 — design rationale, alternatives, failure modes, downstream effects.
   - F. 测试与诊断 — assertions, probes, ablations, focused tests.
   - G. 记录审查 — completeness and correctness of the existing record.
5. Allow multiple choices or a custom direction.
6. After the user chooses, work on the selected direction(s), correcting earlier errors that materially affect them.
7. Update the record with evidence, revisions, unresolved items, and next steps.
8. A simple “继续” means resume from the continuity marker; do not ask for an iteration direction.

If no record exists, say so briefly, create one after analysis, and continue. If iteration target is ambiguous, ask for the target first.

## Required response structure

Adapt depth to the task but retain applicable sections:
1. Scope/evidence: target symbol/path, scope, sources, limitations.
2. Position in system: upstream → target → downstream.
3. Input/output contract: names, shapes, axis meanings, dtype/device as relevant.
4. Mathematical structure: exact implemented equations and definitions.
5. Step-by-step computation ledger:

| Step | Code expression/location | Formula | Input shape | Output shape | Meaning/information effect |
|---|---|---|---|---|---|

6. Worked numerical example with all intermediate results and shapes when useful.
7. Why each operation exists, what it does not solve, and its failure modes.
8. Training/gradients when relevant.
9. Verification evidence and unresolved checks.
10. Conclusion and precise next step.

## Shape ledger conventions

- Use one shape notation consistently and define every symbol.
- Distinguish dimension size from semantic meaning.
- If code uses [B,C,T] but prose uses [B,T,C], show the transpose explicitly.
- Mark dynamic dimensions and identify their source.
- For branches, record each output before fusion, then the fused result.
- For variable-length sequences, account for valid lengths, padding, and masks.
- Same shape does not imply same semantics.

## Continuity protocol

If the explanation cannot fit in one response:
1. Split at a natural section boundary.
2. Finish the current logical unit.
3. End with a compact marker: Completed, Current finding, Next.
4. On “继续”, resume from Next without repeating completed material.
5. Do not claim full completion while meaningful operations remain.
6. Never end with a dangling sentence, incomplete formula, unfinished table, or promise to provide the actual answer later.

## Pitfalls

- Paper-only explanation → inspect implementation.
- Input/output only → enumerate intermediate operations.
- Assumed dimensions → trace configuration/call sites or state unknowns.
- Multiplication called similarity → show reduction/normalization explicitly.
- Block confused with layer → define code boundary and included operations.
- Probability claimed without normalization → inspect activation and axis.
- Branch contribution inferred from final sum → inspect pre-fusion activations, norms, ablations, or gradients.
- Design intention treated as fact → label inference unless sourced.
- Test claimed without execution → state static analysis only.
- Long answer truncated → complete one logical unit and use continuity marker.

## Completion checklist

Before responding, check:
- [ ] Exact implementation and module boundary identified.
- [ ] Upstream/downstream dataflow established.
- [ ] Every input/output has shape and axis meanings.
- [ ] Every meaningful operation is covered in execution order.
- [ ] Broadcasting, padding, reductions, and shape changes are derived.
- [ ] Equation symbols are defined and branches shown before fusion.
- [ ] Numerical example included when useful.
- [ ] Facts, derivations, inferences, and unknowns are distinguished.
- [ ] Verification claims are truthful.
- [ ] Existing records consulted and updated when relevant.
- [ ] Explicit iteration trigger led to a direction question before further analysis.
- [ ] If work remains, the next step is precise.
