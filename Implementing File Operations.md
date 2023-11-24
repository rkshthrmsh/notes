---
tags:
  - deviceDrivers
---
### Implementing File Operations
[[Character Device Data Structure#Device File Operations|File operations]] are system calls that interact with files. Here are implementations of commonly used file operations for character device drivers.
#### `open` File Operation
One usually uses this method to perform device and data structure initializations, after which it should return `0` on success, or a negative error code if something went wrong.
```c
int (*open) (struct inode *inode, struct file *filp);
```
If it is not implemented, device opening will always succeed, but the driver won't be aware, which is not necessarily a problem if the device needs no special initialization.

As we have seen in [[Character Device Data Structure#Device File Operations|file operation]] prototypes, there is almost always a struct file argument. struct file has an element free of use, that is `private_data`. `file->private_data`, if set, will be available to other system calls invoked on the same file descriptor. You can use this field during the lifetime of the file descriptor. It is good practice to set this field in the `open` method as it is always the first system call on any file.

Example:
```c
struct pcf2127 {
	struct cdev cdev;
	unsigned char *sram_data;
	struct i2c_client *client;
	int sram_size;
	[...]
};
```

```c
static unsigned int sram_major = 0;
static struct class *sram_class = NULL;
static int sram_open(struct inode *inode, struct file *filp)
{
	unsigned int maj = imajor(inode);
	unsigned int min = iminor(inode);
	struct pcf2127 *pcf = NULL;
	pcf = container_of(inode->i_cdev, struct pcf2127, cdev);
	pcf->sram_size = SRAM_SIZE;
	if (maj != sram_major || min < 0 ){
		pr_err ("device not found\n");
		return -ENODEV; /* No such device */
	}
	/* prepare the buffer if the device is * opened for the first time */
	if (pcf->sram_data == NULL){
		pcf->sram_data = kzalloc(pcf->sram_size, GFP_KERNEL);
		if (pcf->sram_data == NULL) {
			pr_err("memory allocation failed\n"); return -ENOMEM;
		}
	}
	filp->private_data = pcf;
	return 0;
}
```
#### `release` File Operation
The release method is called when the device gets closed, the reverse of the open method. You must then undo everything you have done in the [[#`open` File Operation]]. If the device node is backed by a hardware device, this operation should also put the device in the appropriate state.

Example:
```c
static int sram_release(struct inode *inode, struct file *filp)
{
	struct pcf2127 *pcf = NULL;
	pcf = container_of(inode->i_cdev, struct pcf2127, cdev);
	mutex_lock(&device_list_lock);
	filp->private_data = NULL;

	/* last close? */ pcf2127->users--;
	if (!pcf2127->users) {
		kfree(tx_buffer);
		kfree(rx_buffer);
		tx_buffer = NULL;
		rx_buffer = NULL;
		[...]
		
		if (any_other_dynamic_struct)
			kfree(any_other_dynamic_struct);
		}
	mutex_unlock(&device_list_lock);
	return 0;
}
```
#### `write` File Operation
This file operation must return the number of bytes (size) written, and the following are the definitions of its arguments: 
- `*buf` represents the data buffer coming from the user space.
- `count` is the size of the requested transfer.
- `*pos` indicates the start position from which data should be written in the file (or in the corresponding memory region if the character device file is memory-backed).

Example:
```c
ssize_t eeprom_write(struct file *filp, const char __user *buf,
					 size_t count, loff_t *f_pos)
{
	struct eeprom_dev *eep = filp->private_data;
	int part_origin = PART_SIZE * eep->part_index;
	int register_address;
	ssize_t retval = 0;
	
	/* step (1) */
	/* Can't write beyond the end of a partition. */
	if (*f_pos >= eep->part_size)
		return -EINVAL;
		
	/* step (2) */
	/* filesize corresponds to the size of device memory */
	if (*pos + count > eep->part_size)
		count = eep->part_size - *pos;
		
	/* step (3) */
	register_address = part_origin + *pos;
	
	/* step(4) */
	/* Copy data from user space to kernel space */
	if (copy_from_user(eep->data, buf, count) != 0)
		return -EFAULT;
		
	/* step (5) */
	/* perform the write to the device */
	if (write_to_device(register_address, buff, count) < 0){ 
		pr_err("i2c_transfer failed\n");
		return –EFAULT;
	}
	
	/* step (6) */
	/* Increase the current position of the cursor in the file,
	according to the number of bytes written and finally,
	return the number of bytes copied */
	*f_pos += count;
	return count;
}
```
#### `read` File Operation
This operation is the backend of the read() system call. Its arguments are described as follows:
- `*buf` is the buffer we receive from user space.
- `count` is the size of the requested transfer (the size of the user buffer).
- `*pos` indicates the start position from which data should be read in the file. It must return the size of the data that has been successfully read.

This size can be less than count though (for example, when reaching the end of the file before reaching the count requested by the user).

Example:
```c
ssize_t eep_read(struct file *filp, char __user *buf, size_t count,
				 loff_t *f_pos)
{
	struct eeprom_dev *eep = filp->private_data;
	if (*f_pos >= EEP_SIZE) /* EOF */
		return 0;
	
	if (*f_pos + count > EEP_SIZE)
		count = EEP_SIZE - *f_pos;

	/* Find location of next data bytes */
	int part_origin = PART_SIZE * eep->part_index;
	int eep_reg_addr_start = part_origin + *pos;
	
	/* perform the read from the device */
	if (read_from_device(eep_reg_addr_start, buff, count) < 0){
		pr_err("i2c_transfer failed\n");
		return –EFAULT;
	}
	
	/* copy from kernel to user space */
	if(copy_to_user(buf, dev->data, count) != 0)
		return -EIO;
		
	*f_pos += count;
	return count;
}
```
#### `llseek` File Operation
The llseek file operation is the kernel backend for the lseek() system call, used to move the cursor position within a file. This callback must return the new position in the file. The following are the definitions of its parameters: 
- `loff_t` is an offset, relative to the current file position, which defines how much of it will be changed.
- `whence` defines where to seek from. The possible values are as follows:
	- `SEEK_SET`: To put the cursor to a position relative from the beginning of the file 
	- `SEEK_CUR`: To put the cursor to a position relative to the current file position
	- `SEEK_END`: To adjust the cursor to a position relative to the end of the file

When implementing this operation, it is a good practice to use the switch statement to check every possible whence case, since they are limited, and adjust the new position accordingly.
```c
switch( whence ){
	case SEEK_SET:/* relative from the beginning of file */
		newpos = offset; /* offset become the new position */
		break;
	case SEEK_CUR: /* relative to current file position */
		/* just add offset to the current position */
		newpos = file->f_pos + offset;
		break;
	case SEEK_END: /* relative to end of file */
		newpos = filesize + offset;
		break;
	default:
		return -EINVAL;
}
/* Check whether newpos is valid */
if ( newpos < 0 )
	return –EINVAL;
/* Update f_pos with the new position */
filp->f_pos = newpos;
/* Return the new file-pointer position */
return newpos;
```
#### [[The poll Method]]
#### [[The ioctl Method]]