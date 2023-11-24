---
tags:
  - kernel
  - deviceDrivers
---
#### File Representation in the Kernel
Looking at the [[Character Device Data Structure|file operation table]], at least one of the parameters of each operation is either the `struct inode` or struct file type. `struct inode` refers to a file on the disk. However, to refer an open file (associated with a file descriptor within a process), the `struct file` structure is used.
```c
struct inode {
	[...]
	union {
		struct pipe_inode_info *i_pipe;
		struct cdev *i_cdev;
		char *i_link;
		unsigned i_dir_seq;
	};
	[...]
}
```
The most important field in the structure is the union, especially the `i_cdev` element, which is set when the underlying file is a character device. This makes it possible to switch back and forth between `struct inode` and `struct cdev`.

On the other hand, `struct file` is a filesystem data structure holding information about a file (its type, character, block, pipe, and so on), most of which is only relevant to the OS.
```c
struct file {
	[...]
	struct path f_path;
	struct inode *f_inode;
	const struct file_operations *f_op;
	loff_t f_pos;
	void *private_data;
	[...]
}
```
In the preceding data structure, `f_path` represents the actual path of the file in the filesystem, and `f_inode` is the underlying inode that points to this opened file. This makes it possible to switch back and forth between struct file and the underlying `cdev`, through the `f_inode` element. `f_op` represents the file operation table. Because struct file represents an open file descriptor, it tracks the current read/write position within its opened instance. This is done through the `f_pos` element.