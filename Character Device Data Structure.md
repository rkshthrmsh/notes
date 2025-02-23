---
tags:
  - kernel
  - deviceDrivers
---
### Character Device Data Structure
Character devices are represented in the kernel as instances of struct `cdev`, declared in `include/linux/cdev.h`.
```c
struct cdev {
	struct kobject kobj;
	struct module *owner;
	const struct file_operations *ops;
	dev_t dev;
[...]
}
```
- `kobj`: This is the underlying kernel object for this character device object, used to enforce the [[Linux device model]].
- `owner`: This should be set to the `THIS_MODULE` macro.
- `ops`: This is the set of file operations associated with this character device.
- `dev`: This is the character device identifier.
#### Device File Operations
The `cdev->ops` element points to the file operations supported by a given device. Each of these operations is the target of a particular system call, in a manner that, when the system call is invoked by a program in user space on the character device, this system call is redirected in the kernel to its file operation counterpart in `cdev->ops`. `struct file_operations` is the data structure that holds these operations.
```c
struct file_operations {
	struct module *owner;
	/* This is a mandatory field that should point to the module owning
	this structure. It is used for proper reference counting. Most of
	the time, it is set to THIS_MODULE, a macro defined in
	<linux/module.h>. */

	loff_t (*llseek) (struct file *, loff_t, int);
	/* This method is used to move the current cursor position in the
	file given as the first parameter. On a successful move, the
	function must return the new position, or else a negative value
	must be returned. If this method is not implemented, then every
	seek performed on this file will succeed by modifying the position
	counter in the file structure (file->f_pos), except the seek
	relative to end-of-file, which will fail. */

	ssize_t (*read) (struct file *, char __user *, size_t, loff_t *);
	/* The role of this function is to retrieve data from the device.
	this function must return either the number (positive) of bytes
	successfully read, or else return an appropriate negative code on
	error. If this function is not implemented, then any read() system
	call on the device file will fail, returning with -EINVAL (an
	"invalid argument"). */

	ssize_t (*write) (struct file *, const char __user *, size_t, loff_t *);
	/* The role of this function is to send data to the device. Like 
	the read() function, it must return a positive number, which, in 
	this case, represents the number of bytes that have been written 
	successfully, or else return an appropriately negative code on 
	error. In the same way, if it is not implemented in the driver, 
	then the write() system call attempt will fail with -EINVAL. */

	unsigned int (*poll) (struct file *, struct poll_table_struct *);
	/* This file operation must return a bitmask describing the status 
	of the device. It is the kernel backend for both poll() and 
	select() system calls, both used to query whether the device is 
	writable, readable, or in some special state. Any caller of this 
	method will block until the device enters the requested state. If 
	this file operation is not implemented, then the device is always 
	assumed to be readable, writable, and in no special state. */

	int (*mmap) (struct file *, struct vm_area_struct *);
	/* This is used to request part or all of the device memory to be 
	mapped to a process address space. If this file operation is not 
	implemented, then any attempt to invoke the mmap() system call on 
	the device file will fail, returning -ENODEV. */

	int (*open) (struct inode *, struct file *);
	/* This file operation is the backend of the open() system call, 
	which, if not implemented (if NULL), will result in the success of 
	any attempt to open the device and the driver won't be notified of 
	the operation. */
	
	int (*flush) (struct file *, fl_owner_t);
	/* This operation is invoked when the file structure is being 
	released. Like open, release can be NULL. */

	long (*unlocked_ioctl) (struct file *, unsigned int, unsigned long);
	/* This is the backend of the ioctl system call, whose purpose is 
	to extend the commands that can be sent to the device (such as 
	formatting a track of a floppy disk, which is neither reading nor 
	writing). The commands defined by this function will extend a set 
	of predefined commands that are already recognized by the kernel 
	without referring to this file operation. Thus, for any command 
	that is not defined (either because this function is not 
	implemented or because it does not support the specified command), 
	the system call will return -ENOTTY, to say "No such ioctl for 
	device". Any non-negative value returned by this function is passed 
	back to the calling program to indicate successful completion. */

	int (*release) (struct inode *, struct file *);
	/* This is invoked when the file is being released, in response to 
	the close() system call. Like open, release is not mandatory and 
	can be NULL. */

	int (*fsync) (struct file *, loff_t, loff_t, int datasync);
	/* This operation is the backend of the fsync() system call, whose 
	purpose is to flush any pending data. If it is not implemented, any 
	call to fsync() on the device file will fail, returning -EINVAL. */

	int (*flock) (struct file *, int, struct file_lock *);
	[...]
};
```
#### [[File Representation in the Kernel]]