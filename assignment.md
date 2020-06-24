# Coding Challenge
## Improving Stress-NG Kernel Coverage
---
Ans 1. option **b** is preferable, because signed int will use the only allocated bit as MSB which inturn will be used only for sign determination, leaving no bits to store the numerical value.

**Detailed Explanation**

Bitfields are used to use memory efficiently when we know that the value of a field will always remain in a small range, by specifying the number of bits to be used by the field.

The values a unsigned integer can take having n bits ranges from 0 to 2^n - 1.

However in case of signed integer the first bit is used for sign determination. Hence the range of values for a signed integer having n bits will be - (2 ^ (n-1)) to 2 ^ (n-1) - 1.

In the given question only one bitfield was assigned. This can be used by the unsigned integer to store values 0 and 1, unlike in the case of signed integer where this bit will be used for sign determination only.

---
Ans 2
The given function performs very well for positive arguments less than 31. However the issue arises when this value increases beyond 30.

1) 1 << 32 will be **computed in int**, which will be leading to **overflow**.
 
The solution is to typecast 1 into (unsigned long long). The ways can be:
i) **(unsigned long long) 1 << 32**
ii) **1ULL << 32**

2) The negative argument produces undefined result. This can easily countered using an if condition.

The Modified Code will look like:

	unsigned long long do_shift(const int shift)
	{
		if (shift < 0){
			return 0;
		}

		return 1ULL << shift;
	} 

---

Ans 3
This is an infinite loop leading to **non termination of the program**. This is because the varible used is signed integer and hence once it becomes zero, decrementing it further will lead to overflow. This will assign it the highest value it can hold. This in turn will keep on happening and never stop.

The easiest solution to fix the bug is to **change the variable to signed integer instead.**

The Modified Code Snippit will look like:
	
	int i = 10;

	while (--i >= 0) {
		printf("%d\n", i);
	}

---

Ans 4
i) Stack Memory is very fast to access. We do not have to free the memory assigned. However the memory limit is quite small in case of stack. Also the scope of variables is local.

ii) malloc() gives uninitialised buffer allocated in heap. It gives control over the lifetime of variables. The space allocated can also be large compared with stack memory. However, the con is that we have to free the allocated memory using free() once we are finished with them. 

iii) calloc() gives us a zero-initialized buffer, i.e. if we want to access the content of the allocated memory, it is returned as 0, even if we havn't initialised it before. This counters the Segmentation fault that might be produced in case of malloc(). The con is that calloc() is slower than malloc().

iv) Mmap is advantageous over malloc because memory used up by mmap is immediately returned to the OS. Also, Mmap can be used to speed up the response given by applications.
Mmap is not recommended for allocating memory as it splits available memory and cannot make system calls. Mmap is Wasteful, for example is we require 16 bytes it gives 4096 bytes. It is slow as every time we need to interact with kernel and all 4096 bytes have to be zeroed. It is also complicated as we have to remember size of unmap.

v) Alloca is very fast and wastes very little space. Alloca also doesn't cause memory fragmentation, i.e. does not have separate pools for different sizes of blocks, space used for any size block can be reused for any other size. The space allocated with alloca is automatically freed when it exit through the function that called alloca. 
The first con is that it is less portable, because some non-GNU does not support it. Also, If we try to allocate more memory than that the machine could provide, we do not get a clear error message. Instead we get something similar to Segmentation fault.

Sir the mmap() and alloca() were the first time encountered functions for me. I have tried to learn about them and put the resources in Reference Section. 

---

Ans 5
Using **gdb**

**Detailed Explanation**
Segmentation error runtime error caused because of the memory access violation. A few examples could be accessing an address that is free, Dereferencing uninitialized pointer, passing variable instead of address in scanf, accessing out of array index bounds, etc.

Using gdb is quite effective and simple to debug segmentation faults. Just compile your program and then run the compiled file using gbd:
	gdb ./a.out

then press r and get the faulty functions.


We could also use another tool called **"valgrind"**.

___

Ans 6
The best feature I find in C is the ability of it to work with a diverse range of softwares. It supports the use of kernels and drivers in low-level programming and also supports system software applications in the high-level programming language.

The worst feature I find is the explicit type declaration (which has benefits of it's own, but not suitable for quick programming contests). It leads to overflow of integer in case not declared as long long, leading to wrong answers. Even the limit of long long is quite small as compared to the length of integers we could store in Python, which implements numbers using string.

___

##References
* https://my.eng.utah.edu/~cs4400/malloc.pdf
* http://www.differencebetween.net/technology/hardware-technology/difference-between-mmap-and-malloc/
* https://www.gnu.org/software/libc/manual/html_node/Advantages-of-Alloca.html
* https://www.gnu.org/software/libc/manual/html_node/Disadvantages-of-Alloca.html#:~:text=3.2.,7.3%20Disadvantages%20of%20alloca&text=If%20you%20try%20to%20allocate,(see%20Program%20Error%20Signals).
