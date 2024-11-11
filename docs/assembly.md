# System V AMD64 ABI

## Stack Frame

A calling convention specifies how functions receive parameters, return values, and how the call stack and registers are managed.

The stack pointer `%rsp` points to the top element of the stack.

Data can be stored on and retrieved from the stack using the `push` and `pop` instructions. Space for data with no specified initial value can be allocated on the stack by simply
decrementing the stack pointer by an appropriate amount. Similarly, space can be deallocated by incrementing the stack pointer.

## Caller-saved vs Callee-saved Registers

The calling convention provides callers and callees with specific guarantees and responsibilities regarding register values across function calls. Certain registers must have their values preserved across function calls. A callee may use these registers, but if it wants to modify them, it must save their original values and restore them before returning. These are known as **callee-saved registers**. All other registers are known as **caller-saved registers**, meaning that **if** a caller needs to preserve their values across a function call, it must explicitly save them before the call and restore them after the callee returns.

> Registers `%rbp`, `%rbx` and `%r12` through `%r15` “belong” to the calling function and the called function is required to preserve their values. In other words, a called function must preserve these registers’ values for its caller. Remaining registers “belong” to the called function. If a calling function wants to preserve such a register value across a function call, it must save the value in its local stack frame. [^1]

As an example, consider the C code below, where the callee writes to `%rbx` which is a callee-saved register.

```c title="callee_saved_example.c" linenums="1"
void __attribute__((noinline)) foo() {
  __asm__ volatile ("movq $0xDEADBEEFDEADBEEF, %%rbx\n\t":::"rbx");
}

int main() {
  foo();
  return 0;
}
```

Below is the generated assembly for the callee function. 

```asm title="callee_saved_example.s" linenums="1"
foo:
  pushq	%rbp
  movq	%rsp, %rbp
  pushq	%rbx
  movq $0xDEADBEEFDEADBEEF, %rbx
  movq	-8(%rbp), %rbx
```



[^1]: System V Application Binary Interface. AMD64 Architecture Processor Supplement, Draft v0.99.6
