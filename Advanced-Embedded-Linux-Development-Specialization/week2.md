1. ## File I/O Linux
   * Files are opened file descriptors in the kernel (fd) integer
   * Every process is going to have its own custom list of files based on which file it's opened
   * That list of files is going to be indexed by the file descriptor
   * Every process in Linux is going to have at least three file descriptors open when it is created:
      + stdin (fd 0 or STDIN_FILENO) --> terminal input dev
        If you're using the terminal to interact with your program, this is where you're going to be typing input in from the keyboard.
      + stdout (fd 1 or STDOUT_FILENO) --> terminal display
        If you're looking at print f output, this is where your print f output is going to be 
      + stderr (fd 2 or STDERR_FILENO) --> terminal display
        where the error messages are typically sent.
    

  * In Linux system programming, we're using the ones that don't have the f prefix
    relate to buffered I/O and not system calls, F calls are one step above the system call level interface and interaction with the kernel.
  * File buffering just means that rather than reading and writing a full block, you're going to be reading and writing copies and memory
  *  `read`: The return value from read is going to be the number of bytes read or minus one on error `errno `.
  * If there is a return value less than count, that means that there are no more bytes available now.
  *  `Umask` is really just a per-process way that you can restrict the permission set on files.

### Interaction between kernel space and userspace by Strace
 It's like a protocol analyzer for the system call traces that are happening from userspace, showing exactly what's getting called and what's getting returned from the kernel. 
 ![image](https://github.com/mariamahmed2/Magic-Ant/assets/64314238/d504520a-3054-43d2-9367-c5055a1cfb02)
 
The actual system call for open is occurring and it's returning three. Three is going to be our file descriptor and then you can see the call that's going to check the file for content, there's a read on the same three file descriptor that we opened here in the previous step, and you can see that it's returning zero bytes.
### Non-Blocking Reads
![image](https://github.com/mariamahmed2/Magic-Ant/assets/64314238/490f7684-9fd1-4842-b73f-d0f657086ba3)


  
