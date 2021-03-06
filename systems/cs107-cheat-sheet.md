# Systems
[Stanford CS107](http://cs107.stanford.edu/) cheat sheet

### Questions
- [ ] Arithmetic vs. logical shift
- [ ] Operations on pointers and longs (64-bit types) will use the full r-named registers, and operations on ints will use the e-named subregisters.
- [ ] Signed vs. unsigned integer division (lab 6)
- [ ] Registers and register flags, what happens when multiple things running at the same time on the processor?
- [ ] How is the generated memory addresses in assembly are always the same? virtual memory?
- [ ] `cmovg` usually used for ternary expressions?
- [ ] reading the output of `display/8gx $rsp`
- [ ] 16 byte aligned
- [ ] addresses in stack which way would an int go?
- 
- [ ] ...


### Questions
- [ ] signed vs unsigned differences in comparison, right shift, assignment, ...
- [ ] "signed/unsigned char important when promoting to larger type???"
- [ ] `int my_isxdigit(int ch) return my_isdigit(ch) || ((unsigned)ch | 32) - ‘a’ < 6`
- [ ] example of subverting CT check
- [ ] ok to oversize stack???
- [ ] why is freeing twice results in undefined behavior?
- [ ] from lab 1: You might notice that the expression above bears an uncanny resemblance to the half-adder Michael showed in lecture. This is not just coincidence, as both perform a kind of addition using bitwise ops. How neat! (Bonus exploration for bit warriors: The half-adder used bitwise | to join the two subexpressions; the code above joins with +. Why must we use + here? Try substituting | and work out how the resulting expression now operates. The result is nearly equivalent, but not quite -- what has changed?)
- [ ] relative accuracy/error vs precision
- [ ] From lab 5 Note the lack of symmetry in the definition names: INT_MAX/FLT_MAX are largest magnitude positive int/float, but INT_MIN is largest magnitude negative int whereas FLT_MIN is smallest magnitude positive float (normalized). The largest magnitude negative float is -FLT_MAX
- [ ] 

### Bit manipulation
- byte is the smallest addressable unit
- `char` is 1 byte
- `unsigned char a`
- `x >> 1` divide by 2
- `x << 1` multiply by 2
- 1 byte = 8 bits = 2 hex digits, e.g. 0xff = 1111 1111 = 255
- char: 1 byte, short: 2 bytes 0 to 65k, int: 4 bytes 0 to 4B, long 8 bytes 0 to big number
-
	```
	ASCII
	48    0x30    ‘0’
	...
	65    0x41    ‘A'
	...
	97    0x61    ‘a’
	```
- half adder, full adder requires handling of carries
	- `a + b = ((a & b) << 1) | (a ^ b)` only when a and b are 1-bit
	- LSB looks like XOR, next bit looks like AND
- overflow `255 + 0 = 0`, `0x100 % 256 = 0`
- `0xff` is -1
- 2’s complement, works on all `int` types  `-n = 0 - n = 0x100 - n = (0xff + 1) - n = (0xff - n) + 1 = ~n + 1`
- `x &= (x - 1)` clears lowest "on" bit in x
- `(x ^ y) < 0` true if x and y have opposite signs
- `(x & (x - 1)) == 0` is power of 2
- `x >> 31` -1 if `x` is negative, 0 otherwise
- It is signed if it fits in a signed integer. To make it unsigned, append a u suffix, e.g. `1234u`
- if `x < 0` then `x ^ -1 + 1 == -x`, e.g. `0xffffffff (-1) ^ 0xfffffff8 (-8) == 0x7`
- parity calculation for `unsigned int x` `int parity=0; for (int i=0; i < 32; i++) if (x & (1 << i)) parity ^= 1;`
- `(val + mult-1) & ~(mult-1)` rounds `val` up to the closest multiple of `mult` if `mult` is a power of 2
- for `int x, y` if `x, y >=0` midpoint `(x + y) / 2 == x + ((y - x) / 2) == ((unsigned int)x + (unsigned int)y) >> 1`
- `INT_MIN - INT_MAX == 1`
- `(x & y) + ((x ^ y) >> 1)`
- `-1 >> 1 == -1`

![Number circle](https://ilyasbek.files.wordpress.com/2011/06/num_clk-21.gif?w=438&zoom=2)

### C-Strings
- C-strings are arrays of `char`
- segmentation fault is access to INVALID memory location
- address is a memory location, represented as `unsigned long`
- pointer is a variable that holds an address
- `&` is the address of, `&var` is the location in memory where `var` is being stored
- addresses can be compared and added/subtracted
- pointer arithmetic on `int *` scales by `sizeof(int)` and `char *` scales by `sizeof(char)` == 1
- types are compile-time (no runtime checking), if CT is subverted no RT errors are raised
- `int arr[2]` and then `arr[10]` has no array out of bounds or other safety, just grabs the memory
- `int *ptr = arr` "decays" to address of first element (same as `&arr[0]`)
- array arithmetic is syntactic sugar for ptr arithmetic
- 
	```
	ptr + i 	<=> &ptr[i]
	*(ptr + i) 	<=> ptr[i]
	```
- `arr[5]; sizeof(arr) == 20` and `char *p = "hello"; sizeof(p) == 8`
- Can't reassign arrays, `arr = NULL` doesn't compile
- if `arr1[] = {1,2,3,4,5}` then `&arr1[0] == arr1 == &arr1` all point to the address of base

![Memory address space](https://i.stack.imgur.com/CvITh.png)

### Stack vs. heap
- 8MB of stack
- size fixed at declaration, no option to resize
- size can be constant or runtime expression but once sized cannot resize
- stack arrays cannot be re-assigned, no pointer to array start
- `void *` can be assigned to and passed to/from functions but:
	```
	cannot dereference
	cannot do ptr arithmetic
	cannot use array indexing
	```
- operator precedence, `(*s)++` is not the same as `*s++`
- undefined behaviors if
	```
	write outside bounds
	use after free
	free twice
	realloc non-heap address
	```
- prefer stack to heap, unless:
	```
	very large allocation that could blow out the stack
	dynamic construction not known at compile time / need to resize memory
	need of persistent memory outside of the function call/stack frame
	```

### gdb
- `p/t` print binary
- ...

### floats
- `(sign) mantissa x 10 ^ exponent`
- IEEE 754
- a *transcendental function* "transcends" algebra in that it cannot be expressed in terms of a finite sequence of the algebraic operations of addition, multiplication, and root extraction. Examples of transcendental functions include the exponential function, the logarithm, and the trigonometric functions.
- once upon a time there was a floating point co-processor (before that it was done in SW!)
- we hava both + and - 0 !
	- they compare as equal ([0] * 32 == [1] + [0] * 31 or 0 == (1 << 32)) in float
- quarter of 32
- exponents go from -126 to 127
- 253, NaN and np.inf are reserved
- `double` is the more native type, the work to truncate it down to 32 makes it actually smaller times! don't make any such assumptions, depends on the system
- binary fractions
- 1/3 is non terminating so can't be exactly represented
- 0.1 is terminating but it still can't be exactly represented, 1/16+1/32+1/256+1/512+1/1024+...
- `(-1) ^ S * 2 ^(E - bias) * 1.M` (mantessa)
	- bias is `01...1` so if exponent has 5 bits we have `01111`
	- Amount of bits in `M` defines the precision
	- denorm allows us to go slightly lower
- `inf - inf = nan`
- `nan == nan? false`
- `nan > nan? false`
- `nan < nan? false`

### Assembly
- In many programming environments for C and C-derived languages on 64-bit machines, "int" variables are still 32 bits wide, but long integers and pointers are 64 bits wide. These are described as having an LP64 data model.
- addition, subtraction, and multiplication operate equivalently on signed and unsigned integers, it's not quite so with division.
- 
```
int max(int x, int y)
    return x > y ? x : y
```
and the following assembly
```
cmp 	%edi, %esi
mov		%edi, %eax
cmovg	%esi, %eax
```
the `cmovg` conditional move is reading the flag from 2 instructions before
- The top of the stack is a lower address
- Application Binary Interface (ABI)
- Leaf function: no call to other functions
- In assembly return of value and control are two totally different things
	- Return of value done through registers
	- `retq` or `rep retq` are just returning back control
- Stack is growing down
	- Pushing to stack => decrement `%rsp` to make space
	- Pop copies the topmost value from stack into register and incremenets `%rsp`
- `(gdb) x/4gx $rsp   // 4 quadwords, in hex, read from top of stack`
- 


