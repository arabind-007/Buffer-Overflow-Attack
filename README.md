# Buffer-Overflow-Attack
In network security and programming, a buffer overflow, or buffer overrun, is an anomaly where a program, while writing data to a buffer, overruns the buffer's boundary and overwrites adjacent memory locations.

simplified:

Memory is contiguous, a "buffer" does not have boundaries per se. In memory unsafe languages, like for example C, it is the responsibility of the programmer to make sure that whatever is written to the buffer stays within the buffer's boundaries. If the programmer doesn't do that or makes a mistake while doing it there can be a buffer overflow vulnerability. Consider the following program taken from https://www.tutorialspoint.com/c_standard_library/c_function_gets:

    #include <stdio.h>
    int main () {
           char str[50];
       printf("Enter a string : ");
              gets(str);
       printf("You entered: %s", str);
       return(0);
           }

This code has a buffer overflow vulnerability, which unfortunately tutorialspoint fails to point out. `gets(str);` reads a string from user input (stdin) and writes it to the buffer `str`. The buffer `str` has been defined with a size of 50 bytes, however `gets` doesn't know that and isn't in any way limited to only writing 50 bytes. If a user, or an attacker, a enters more than 50 characters the buffer will "overflow". Which just means `gets`will keep writing into memory that does not belong to that buffer anymore. Note that, as you cannot tell `gets` what the size of the target buffer is, the use of the `gets` function **always** results in a  buffer overflow vulnerability and therefore should never be used.

Now your next question probably is *what* can we overwrite by writing beyond the buffer? What is there? For simplicity's sake I'm going to assume the program has been compiled with no "stack protections".

First we must know where in memory our buffer is. In this case, the buffer is on the stack. Secondly, we must know that the stack grows "upwards" (figuratively. In reality it grows from high memory addresses to low memory addresses), meaning every time we put something on the stack, like a variable or a buffer, we put it on top of whatever we put on the stack there. So when we overflow our `str` buffer we overwrite whatever has been put on the stack before the `str` buffer was put on the stack. The next question is what has been put there before? Right before the `str` buffer was put on the stack a "stack frame pointer" has been put on the stack, but we don't care too much about that one. The juicy part is what has been placed on the stack before this stack frame pointer, `main`'s *return address*. Normally when `main` finishes, the program execution *returns* to where it was before the `main` function has been called. Where that is is stored in the *return address*.

What an attacker can do now, is to fill the buffer, write over the stack frame pointer and change the return address to whatever the attacker wants. Now the attacker can control what the program should execute next. For example (still assuming it has been compiled without protections), the attacker could write malicious code on the stack (into the buffer or after the return address) and then change the return address to the address of the maliciously injected code on the stack. Now the attacker can make the program do whatever they want.

If you want to learn more about this, I recommend actually doing it. For example the [narnia](https://overthewire.org/wargames/narnia/) wargame from [OverTheWire](https://overthewire.org/) is a great introduction to binary exploitation.

Thank you🖤.
