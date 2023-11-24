---
tags:
  - kernel
---
### Work-Deferring Mechanisms
Deferring is a method by which you schedule a piece of work to be executed in the future. The kernel provide three facilities for deferring work:
- Softirqs: Executed in an atomic context.
- Tasklets (deprecated): Executed in an atomic context.
- Workqueues: Executed in a process context.
#### [[Softirqs]]
#### [[Workqueues]]