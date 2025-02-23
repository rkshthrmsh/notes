---
tags:
  - deviceDrivers
---
#### The `ioctl` Method
A typical Linux system contains around 350 system calls (syscalls), but only a few of them are linked to file operations. Sometimes, devices may need to implement specific commands that are not provided by system calls, and especially the ones associated with files. In this case, the solution is to use input/output control (**ioctl**), which is a method by which you extend a list of commands associated with a device. You can use it to send special commands to devices (reset, shutdown, configure, and so on). If the driver does not define this method, the kernel will return an `-ENOTTY` error to any `ioctl()` system call.
```c
	long ioctl(struct file *f, unsigned int cmd, unsigned long arg);
```

In the preceding prototype, `f` is the pointer to the file descriptor representing an opened instance of the device, `cmd` is the ioctl command, and `arg` is a user parameter, which can be the address of any user memory on which the driver can call `copy_to_user()` or `copy_from_user()`.

To be concise, and for obvious reasons, an IOCTL command needs to be identified by a number, which should be unique to the system. The unicity of IOCTL numbers across the system will prevent command mismatches. Linux provides four helper macros to create an IOCTL identifier, depending on whether there is data transfer or not and the direction of the transfer. Their respective prototypes are as follows:
```c
_IO(MAGIC, SEQ_NO)
_IOR(MAGIC, SEQ_NO, TYPE)
_IOW(MAGIC, SEQ_NO, TYPE)
_IORW(MAGIC, SEQ_NO, TYPE)
```
- `_IO`: The IOCTL command does not need data transfer. 
- `_IOR`: This means that we're creating an IOCTL command number for passing information from the kernel to user space (which is reading data). The driver will be allowed to return the `sizeof(TYPE)` bytes to the user without this return value being considered as an error.
- `_IOW`: This is the same as `_IOR`, but the user sends data to the driver this time.
- `_IOWR`: The IOCTL command needs both write and read parameters.

What their parameters mean (in the order they are passed) is described here:
- A number coded on 8 bits (0 to 255), called the **magic number**. 
- A sequence number or command ID, also on 8 bits. 
- A data type, if any, that will inform the kernel about the size to be copied. This could be the name of a structure or a data type.

It is recommended to generate IOCTL numbers in a dedicated header file, since the header should be available in user space as well. An example header file:
```c
#ifndef PACKT_IOCTL_H
#define PACKT_IOCTL_H
/* We need to choose a magic number for our driver, * and sequential numbers for each command: */
#define EEP_MAGIC 'E'
#define ERASE_SEQ_NO 0x01
#define RENAME_SEQ_NO 0x02
#define GET_FOO 0x03
#define GET_SIZE 0x04

/* Partition name must be 32 byte max */
#define MAX_PART_NAME 32

/* Now let's define our ioctl numbers: */
#define EEP_ERASE _IO(EEP_MAGIC, ERASE_SEQ_NO)
#define EEP_RENAME_PART _IOW(EEP_MAGIC, RENAME_SEQ_NO, unsigned long) 
#define EEP_GET_FOO _IOR(EEP_MAGIC, GET_FOO, struct my_struct *) 
#define EEP_GET_SIZE _IOR(EEP_MAGIC, GET_SIZE, int *)
#endif
```

After the commands have been defined, the header needs to be included in the final code. Moreover, because they are all unique and limited, it is a good practice to use a switch ... case statement to handle each command and return a -ENOTTY error code when an undefined ioctl command is called.
```c
#include "eep_ioctl.h"
static long eep_ioctl(struct file *f, unsigned int cmd,
					  unsigned long arg)
{
	int part;
	char *buf = NULL;
	int size = 2048;
	switch(cmd){
		case EEP_ERASE:
			erase_eepreom();
			break;
		case EEP_RENAME_PART:
			buf = kmalloc(MAX_PART_NAME, GFP_KERNEL);
			copy_from_user(buf, (char *)arg, MAX_PART_NAME);
			rename_part(buf);
			break;
		case EEP_GET_SIZE:
			if (copy_to_user((int*)arg, &size, sizeof(int)))
				return -EFAULT;
			break;
		default:
			return –ENOTTY;
	}
	return 0;
}
```

If the IOCTL command requires more than one argument, the arguments can be gathered in a structure and a pointer to that structure can be passed to `ioctl`.

From the user space, the same header should be used as shown in the following example:
```c
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <unistd.h>
#include "eep_ioctl.h" /* our ioctl header file */
int main()
{
	int size = 0;
	int fd;
	char *new_name = "lorem_ipsum";
	fd = open("/dev/eep-mem1", O_RDWR);
	if (fd < 0){
		printf("Error while opening the eeprom\n");
	return 1;
	}
	/* ioctl to erase partition */
	ioctl(fd, EEP_ERASE);
	/* call to get partition size */
	ioctl(fd, EEP_GET_SIZE, &size);
	/* rename partition */
	ioctl(fd, EEP_RENAME_PART, new_name);
	close(fd);
	return 0;
}
```