---
tags:
  - embeddedLinux
  - kernelModules
---
# Linux Kernel Development Basics
## [[Understanding Linux Kernel Module Basic Concepts]]
## Kernel Core Helpers
The Linux kernel is a standalone piece of software that does not depend on any external library. It implements any, and all, functionalities it needs to use.
### Locking Mechanisms and Shared Resources
A resource is said to be shared when it is accessible by several contenders, whether exclusively or not. When it is exclusive, access must be synchronized so that only the allowed contender(s) may own the resource. Such resources might be memory locations or peripheral devices, and the contenders might be processors, processes, or threads. There are two main synchronization mechanisms in the kernel:
- **Locks**: Used for mutual exclusion. When one contender holds the lock, no other can hold it (others are excluded). The most common locks in the kernel are spinlocks and mutexes.
- **Conditional Variables**: For waiting for a change. The kernel does not implement conditional variables directly. Instead it provides the following mechanisms:
	- **Wait queues**: To wait for a change—designed to work with locks.
	- **Completion queues**: To wait for the completion of a given computation.
#### [[Spinlock]]
#### [[Mutex]]