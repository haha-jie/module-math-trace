# Module Math Trace

A Codex Skill for deep, implementation-grounded analysis of project modules, including mathematical derivations, tensor-shape tracing, numerical walkthroughs, gradients, verification, iteration, and persistent module records.

## Install

This repository is structured as a Skill package. Copy or merge these paths into the root of the target code repository:

- `.agents/skills/module-math-trace/SKILL.md`
- `.module-math-trace/INDEX.md`
- `.module-math-trace/records/`
- `.module-math-trace/templates/module-record-template.md`
- `.module-math-trace/iterations/CHANGELOG.md`

Do not overwrite existing module records when merging into a project.

## Use

Invoke explicitly with:

```text
$module-math-trace path/to/module.py::ModuleName
```

Or ask naturally for an in-depth analysis of a specific module.

Iteration triggers:
- `迭代分析`
- `迭代本模块`
- `优化本次分析`

On an explicit iteration trigger, the Skill reads the existing module record and asks which direction to pursue before doing further analysis. A simple `继续` resumes the current explanation rather than opening the iteration-direction prompt.

## Persistent records

The `.module-math-trace/` directory is project-file-based memory. It persists across conversations when the same workspace or repository remains available. Commit or carry the records forward when moving to another workspace.

The index records module status and next steps. Each module gets a separate record with its code path, equations, tensor shapes, verification evidence, unresolved questions, and revision history.

## Evidence discipline

The Skill distinguishes code facts, mathematical derivations, paper/document claims, inferences, and unverified items. It must not claim tests ran or records were saved unless those actions actually occurred.
