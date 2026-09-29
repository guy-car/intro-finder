# Hello Interview Principles

This project uses a lightweight adaptation of Hello Interview's system design
[Delivery Framework](https://www.hellointerview.com/learn/system-design/in-a-hurry/delivery).
The framework is a thinking aid, not a rigid process or a reason to design for
hypothetical scale.

## Suggested sequence

### 1. Requirements

- Identify a small set of prioritized user capabilities.
- Define only the non-functional requirements that could affect the design.
- State what is out of scope.
- Estimate scale only when a number would change a decision.

### 2. Core entities

- Name the actors and resources needed to support the requirements.
- Keep this to a short conceptual list rather than designing a complete schema.

### 3. System interface

- Define how each user capability is invoked and what it returns.
- The interface may be an IDE workflow, CLI command, or function boundary; it
  does not need to be an HTTP API.

### 4. Data flow, when useful

- Describe the major steps and transformations for a multi-step workflow.
- Skip this when the flow is already obvious.

### 5. High-level design

- Start with the simplest complete design that satisfies the current requirements.
- Walk through each core workflow end to end.
- Add storage, infrastructure, and schema details only as the design requires them.

### 6. Focused deep dives

- Investigate the uncertainties, failures, or tradeoffs most likely to affect the
  current increment.
- Prefer a small feasibility experiment over speculative complexity.
- Revise earlier assumptions when implementation provides new evidence.

## How to apply this here

- Use the framework for product scoping and consequential architecture decisions.
- Preserve owner understanding and decision-making.
- Optimize first for a useful, local, single-user tool.
- Do not introduce interview-scale patterns such as microservices, queues, caches,
  or sharding without a demonstrated need.
- Do not force the framework onto routine implementation work.

## Sources

- [Delivery Framework](https://www.hellointerview.com/learn/system-design/in-a-hurry/delivery)
- [Requirements Gathering](https://www.hellointerview.com/blog/system-design-requirements)
- [Data Modeling](https://www.hellointerview.com/learn/system-design/core-concepts/data-modeling)
- [API Design](https://www.hellointerview.com/learn/system-design/core-concepts/api-design)
