#done 

[Defination](https://www.quora.com/What-is-a-handle-in-C-programming): 
- The simplest definition of a handle in C programming is that it is just a pointer to a pointer. It can have many uses, but one of the easiest to grasp is one where the operating system might need to move blocks of memory around on the heap.
- For example, assume you need a 1M block of memory. If you malloc that block, it might start at, for example, memory location 0x10000. But then, that location is fixed and can’t be moved for as long as the program needs to access it. And this can be a problem if memory is becoming fragmented.
- So instead, you can create a handle. In this case, your program manages the pointer p, but allow the operating system to manage the pointer \*p. By doing this, the block pointed to by \*p can be moved, and \*p pointed to a new block. Your program can then access the memory with \*\*p, \*p[x], or similar suitable pointer arithmetic. And the beauty of it is that though the OS may have moved \*p in the meantime, the double dereferencing will find the new memory location no matter where it might have been moved to.

