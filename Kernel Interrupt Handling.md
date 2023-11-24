---
tags:
  - kernel
---
### Kernel Interrupt Handling
An interrupt is a signal sent to the processor by an external hardware device requesting immediate attention. Prior to an interrupt being visible to the CPU, this interrupt should be enabled by the interrupt controller, which is a device on its own whose main job consists of routing interrupts to CPUs. The Linux kernel allows the provision of handlers for interrupts we are interested in so that when those interrupts are triggered, our handlers are executed. To be notified when an interrupt occurs, you need to register with that IRQ, providing a function called an interrupt handler that will be called every time that interrupt is raised.

When an interrupt handler is executed, it runs with interrupts disabled on the local CPU. This involves respecting certain constraints while designing an interrupt service routine (ISR):
- **Execution time**: As IRQ handlers run with interrupts disabled on the local CPU, the code must be as short and small as possible and fast enough to assure a fast re-enabling of the previously disabled CPU-local interrupts in order not to miss any further occurring IRQs. Time-consuming IRQ handlers may considerably alter the real-time properties of the system and slow it down.
- **Execution context**: Since interrupt handlers are executed in an atomic context, sleeping is forbidden. Any part of the code requiring or involving sleeping must be deferred into another, safer context.


An IRQ handler needs to be given two arguments: the interrupt line to install the handler for, and a **unique device ID** (**UDI**) of the peripheral.
```c
typedef irqreturn_t (*irq_handler_t) (int, void *);
```
Possible return values using the `irqreturn_t` type are:
- `IRQ_NONE`: On a shared interrupt line, once the interrupt occurs, the kernel IRQ core successively walks through handlers registered for this line and executes them in the order they have been registered. The driver then has the responsibility to check whether it is its device that issued the interrupt. If the interrupt does not come from its device, it must return `IRQ_NONE` to instruct the kernel to call the next registered interrupt handler
- `IRQ_HANDLED`: This value should be returned if the interrupt has been handled successfully. On a threaded IRQ, this value does acknowledge the interrupt (at a controller level) without waking the thread handler up.
- `IRQ_WAKE_THREAD`: On a thread IRQ handler, this value must be returned by the hard-IRQ handler to wake the handler thread. In this case, `IRQ_HANDLED` must only be returned by that threaded handler, previously registered with `devm_request_threaded_irq()`.


The device driver wishing to register an interrupt handler for a given IRQ should call `devm_request_irq()`, defined in `<linux/interrupt.h>`:
```c
devm_request_irq(struct device *dev, unsigned int irq,
				 irq_handler_t handler, unsigned long irqflags
				 const char *devname, void *dev_id)
```
Here, `dev` is the device responsible for the IRQ line, `irq` represents the interrupt line (that is, the interrupt number of the issuing device) to register `handler` for. `handler` is the function pointer to the interrupt handler, and `name` is an ASCII string that should namely describe the interrupt. `flags` represent the interrupt flags. Some important flags are:
- `IRQF_NOBALANCING` excludes the interrupt from IRQ balancing, which is a mechanism that consists of distributing/relocating interrupts across CPUs, with a goal of increasing performance.
- `IRQF_IRQPOLL`: This flag allows the implementation of an irqpoll mechanism, intended to fix interrupt problems, meaning this handler should be added to the list of known interrupt handlers that can be looked for when a given interrupt is not handled.
- `IRQF_ONESHOT`: Normally, the actual interrupt line being serviced is re-enabled after its hardirq handler completes, whether it awakes a [[threaded handler]] or not. This flag keeps the interrupt line disabled after the hardirq handler finishes. It must be set on threaded interrupts for which the interrupt line must remain disabled until the threaded handler has completed, after which it will be re-enabled.
- `IRQF_NO_SUSPEND` does not disable the IRQ during system hibernation/ suspension. It does mean the interrupt is able to wake up the system from a suspended state.
- `IRQF_FORCE_RESUME` enables the IRQ in the system resume path even if `IRQF_NO_SUSPEND` is set.
- `IRQF_NO_THREAD` prevents the interrupt handler from being threaded. This flag overrides the kernel threadirqs command-line option that forces every interrupt to be threaded.
- `IRQF_TIMER` marks this handler as being specific to system timer interrupts. It is just an alias for `IRQF_NO_SUSPEND | IRQF_NO_THREAD`.
- `IRQF_EARLY_RESUME` resumes IRQ early at resume time of system core (syscore) operations instead of at device resume time.
- `IRQF_SHARED` allows for the sharing of the interrupt line among several devices. However, each device driver that needs to share the given interrupt line must set with this flag; otherwise, the handler registration will fail.

`devm_request_irq()` is the managed version of `request_irq()`.
```c
int request_irq(unsigned int irq, irq_handler_t handler,
				unsigned long flags, const char *name, void *dev)
```
If the driver used the managed version, the IRQ core will take care of releasing the resources.


In other cases, such as at the unloading path or when the device leaves, the driver will have to release the IRQ resources by unregistering the interrupt handler using `free_irq()`.
```c
void free_irq(unsigned int irq, void *dev_id)
```

#### Important Points about Interrupts
- On Linux systems, when the handler of an IRQ is being executed by a CPU, all interrupts are disabled on that CPU and the interrupt being serviced is masked on all the other cores. This means interrupt handlers need not be reentrant because the same interrupt will never be received until the current handler has completed. However, all other interrupts but the serviced one remain enabled (or, should we say, unchanged) on other cores, so other interrupts keep being serviced, though the current line is always disabled, as well as further interrupts on the local CPU. As a result, the same interrupt handler is never invoked concurrently to service a nested interrupt.
- Critical regions that need to run with interrupts disabled should be limited as much as possible.
- The interrupt context has its own (fixed and quite low) stack size. It thus totally makes sense to disable IRQs while running an ISR as reentrancy could cause stack overflow if too many preemptions happen.
- Interrupt handlers cannot block; they do not run in a process context. Thus, you may not perform the following operations from within an interrupt handler:
	- You cannot transfer data to/from user space since this may block.
	- You cannot sleep or rely on code that may lead to sleep.
	- You cannot trigger or call `schedule()`.
#### Top and Bottom Halves
The idea with top and bottom halves is to split the interrupt handler into two parts. The first part will run in a so-called hard-IRQ context with interrupts disabled, and will perform the minimum required work (such as doing some quick sanity checks—essentially, time-sensitive tasks, read/write hardware registers, and fast processing of this data and acknowledging interrupts to the device that raised it). This first part is the so-called **top half**. The top-half would then schedule a thread handler, which would run a so-called **bottom-half** function, with interrupts re-enabled, and which is the second part of the interrupt. The bottom half could then perform time-consuming operations (such as buffer processing) and tasks that may sleep, as it runs in a thread.
#### Threaded IRQ Handlers
Threaded interrupt handlers were introduced to reduce the time spent in interrupt handlers and defer the rest of the work out into kernel threads. A threaded interrupt handler runs in its own thread, either in the thread of their parent (if they have one) or in a separate kernel thread. The general rule behind threaded interrupts is simple: keep the hard-IRQ handler as minimal as possible and defer as much work to the kernel thread as possible (preferably, all work).

A threaded interrupt handler can be requested using `devm_request_threaded_irq()`
```c
devm_request_threaded_irq(struct device *dev, unsigned int irq,
						  irq_handler_t handler, irq_handler_t thread_fn,
						  unsigned long irqflags, const char *devname,
						  void *dev_id);
```
- `handler` runs immediately when the interrupt occurs, in an interrupt context, and acts as a hard-IRQ handler. Its job usually consists of reading the interrupt source to determine whether or how to handle the interrupt. If the interrupt does not come from the device, this function should return IRQ_NONE. 

  On the other hand, if the device is the source of the interrupt, and if this hard-IRQ handler can finish interrupt processing fast enough, it should return `IRQ_HANDLED`.
  
  Interrupt processing that does not fall in this time lapse should be deferred in the thread IRQ handlers. In this case, the hard-IRQ handler should return `IRQ_WAKE_THREAD` to awake the threaded handler. Returning `IRQ_WAKE_THREAD` makes sense only when the `thread_fn` handler is also provided.
  
- `thread_fn` is the threaded handler added to the scheduler runqueue when the hard-IRQ handler function returns `IRQ_WAKE_THREAD`.
  
  A default hard-IRQ handler will be installed by the kernel if handler is `NULL` and `thread_fn != NULL`. This is the default primary handler. It does nothing but return `IRQ_WAKE_THREAD` to wake up the associated kernel thread that will execute the `thread_fn` handler.
  
  It is possible to request an IRQ that is exclusively threaded by omitting the hard-interrupt handler. In this case it is mandatory to set the `IRQF_ONESHOT` flag.

The concept of threaded interrupt handlers comes from the real-time kernel tree, and, as such, fulfills many requirements of a real-time system, such as allowing a fine-grained priority model and reducing interrupt latency in the kernel. `/proc/irq/<IRQ>/smp_affinity` can be used to get or set the corresponding `<IRQ>` affinity. This way, the affinity of the hard-interrupt handler can be set to one CPU, while the affinity of the threaded handler can be set to another CPU.
#### Context-Agnostic IRQ
Sometimes, a driver requesting an IRQ does not know about the nature of the interrupt controller that provides this IRQ line, especially when the interrupt controller is a discrete chip (typically, a general-purpose I/O (GPIO) expander connected over SPI or I2C buses). In such cases, the drivers can use the `request_any_context_irq()` or the `devm_request_any_context_irq()` which will know whether the handler will run in a thread context or not.
```c
int request_anycontext_irq(unsigned int irq, irq_handler_t handler,
						  unsigned long flags, const char *name,
						  void *dev_id)
```

`devm_request_any_context_irq` has the same interface as `devm_request_irq()`. It returns a negative error value on failure, while on success, it returns either `IRQC_IS_HARDIRQ` or `IRQC_IS_NESTED`.