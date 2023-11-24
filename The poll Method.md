---
tags:
  - deviceDrivers
---
#### The `poll` Method
The `poll` method is the backend of both the `poll()` and `select()` system calls. These system calls are used to passively (by sleeping, without wasting CPU cycles) sense the readability/writability in a file.

The kernel function at the heart of this method implementation is `poll_wait()`, defined in `<linux/poll.h>`, which is the header you must include in the driver code.
```c
void poll_wait(struct file *filp, wait_queue_head_t *wait_address,
			  poll_table *p)
```
`poll_wait()` adds the device associated with a `struct file` structure (given as the first parameter) to a list of those that can wake up processes (put to sleep in the `struct wait_queue_head_t` structure given as the second parameter), according to events registered in the `struct poll_table` structure given as the third parameter. A user process can call the `poll()`, `select()`, or `epoll()` system calls to add a set of files to a list on which it needs to wait, in order to be aware of the associated (if any) device's readiness. The kernel will then call the `poll` entry of the driver associated with each device file. The poll method of each driver should then call `poll_wait()` in order to register events for which the process needs to be notified with the kernel, put that process to sleep until one of these events occurs, and register the driver as one of those that can wake the process up. The usual way is to use a wait queue per event type (one for readability, another one for writability, and eventually one for an exception if needed), according to events supported by the `select()` (or `poll()`) system call.

The return value of the (`*poll`) file operation must have `POLLIN | POLLRDNORM` set if there is data to read, `POLLOUT | POLLWRNORM` if the device is writable, and `0` if there is no new data and the device is not yet writable. In the following example, we assume the device supports both blocking read and write. Of course, you can implement only one of these. If the driver does not define this method, the device will be considered as always readable and writable, and `poll()` or `select()` system calls return immediately.

Implementing the poll operation may require adapting the read or write file operations in a way that, on write, readers are notified of the readability, and on read, writers are notified of the writability.
```c
#include <linux/poll.h>
/* declare a wait queue for each event type (read, write ...) */
static DECLARE_WAIT_QUEUE_HEAD(my_wq);
static DECLARE_WAIT_QUEUE_HEAD(my_rq);
static unsigned int eep_poll(struct file *file, poll_table *wait)
{
	unsigned int reval_mask = 0;
	poll_wait(file, &my_wq, wait);
	poll_wait(file, &my_rq, wait);

	if (new_data_is_ready)
		reval_mask |= (POLLIN | POLLRDNORM);
	if (ready_to_be_written)
		reval_mask |= (POLLOUT | POLLWRNORM);
	return reval_mask;
}
```

In addition, a notification mechanism is require when any of these states change.
```c
wake_up_interruptible(&my_rq); /* Ready to read */
/* set flag accordingly in case poll is called */
new_data_is_ready = true;

wake_up_interruptible(&my_wq); /* Ready to be written to */
ready_to_be_written = true;
```

The following is an excerpt of code that uses select() on a given character device in order to sense data availability:
```c
#include <unistd.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/select.h>

#define NUMBER_OF_BYTE 100
#define CHAR_DEVICE "/dev/packt_char"
char data[NUMBER_OF_BYTE];

int main(int argc, char **argv)
{
	int fd, retval;
	ssize_t read_count;
	fd_set readfds;
	fd = open(CHAR_DEVICE, O_RDONLY);
	if(fd < 0)
		/* Print a message and exit*/
		[...]
	
	while(1){
		FD_ZERO(&readfds);
		FD_SET(fd, &readfds);
		ret = select(fd + 1, &readfds, NULL, NULL, NULL);
		/* From here, the process is already notified */
		if (ret == -1) {
			fprintf(stderr, "select: an error ocurred");
			break;
	}
	
	/* we are interested in one file only */
	if (FD_ISSET(fd, &readfds)) {
		read_count = read(fd, data, NUMBER_OF_BYTE);
		if (read_count < 0)
			/* An error occurred. Handle this */
			[...]
		if (read_count != NUMBER_OF_BYTE)
			/* We have read less than needed bytes */
			[...] /* handle this */
		else
		/* Now we can process the data we have read */
		[...]
	}
	close(fd);
	return EXIT_SUCCESS;
}
```