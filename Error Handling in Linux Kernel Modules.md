---
tags:
  - embeddedLinux
  - kernelModules
---
### Error Handling in Linux Kernel Modules
Returning the wrong error code for a given error can result in either the kernel or user space application misinterpreting and taking the wrong decision, producing unneeded behavior. To keep things clear, there are predefined errors in the kernel tree that cover almost every case. Some of the errors (with their meaning) are defined in `include/uapi/asm-generic/errno-base.h`, and the rest of the list can be found in `include/uapi/asm-generic/errno.h`.

Most of the time, the standard way to return an error is to do so in the form of return ERROR, especially when it comes to answering system calls (`open`, `read`, `ioctl`, or `mmap`). An example for an I/O error:
```c
dev = init(&ptr);
if(!dev)
	return -EIO
```

Errors sometimes cross the kernel space and propagate themselves to the user space. If the returned error is an answer to a system call, the value will be automatically assigned to the user space `errno` global variable, on which you can use `strerror(errno)` to translate the error into a readable string:
```c
# include <errno.h>
# include <string.h>
[...]
if(write(fd, buf, 1) < 0) {
	printf("Write gone wrong: %s\n", strerror(errno));
}
```

When you face an error, you must undo everything that has been set until the error occurred. The usual way to do that is to use the goto statement The reason for using `goto` is that it makes the code more readable, less error-prone and confusing, and a straighter flow control.
```c
ret = 0;;
ptr = kmalloc(sizeof(device_t));
if(!ptr) {
	ret = -ENOMEM
	goto err_alloc
}
err_alloc:
	return ret;
```
**NOTE**: Use `goto` to move forward in a function, not backward, not to implement loops.
### Handling `NULL` Pointer Errors
When it comes to returning an error from functions that are supposed to return a pointer, functions often return the `NULL` pointer. It is functional but it is a quite meaningless approach, since we do not exactly know why this `NULL` pointer is returned. For that purpose, the kernel provides three functions, `ERR_PTR`, `IS_ERR`, and `PTR_ERR`, defined as follows:
```c
void *ERR_PTR(long error);
long IS_ERR(const void *ptr);
long PTR_ERR(const void *ptr);
```
- `ERR_PTR`: Returns the value as a pointer. It can be seen as an *error value to pointer* macro.
- `IS_ERR`: Macro for checking whether the returned value is a pointer error using `if(IS_Err(foo))`
- `PTR_ERR`: Returns the actual error code. It can be seen as a `pointer to error value` macro.
```c
static struct iio_dev *indiodev_setup() {
[...]
struct iio_dev *indio_dev;
indio_dev = devm_iio_device_alloc(&data->client->dev, sizeof(data));

if (!indio_dev)
	return ERR_PTR(-ENOMEM);
[...]
return indio_dev;
}

static int foo_prove([...]){
	[...]
	struct iio_dev *my_indio_dev = indiodev_setup();
	if (IS_ERR(my_indio_dev))
	return PTR_ERR(data->acc_indio_dev);
}
```

As per the kernel coding style, if a function's name is an action or an imperative command, the function should return an integer error code. If, however, the function's name is a predicate, this function should return a Boolean to indicate the succeeded status of the operation.