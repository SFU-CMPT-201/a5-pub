# Assignment 6: Debugging tools

Debugging is fundamental in software development. You probably know this already from your
experience in previous courses because you might have run into errors in your code and had to find
what the problems were and fixed them. In fact, debugging is a very deep and broad topic. There are
countless books published on this (and a good book to check out is [Why Programs
Fail](https://www.whyprogramsfail.com/) by [Andreas Zeller](https://andreas-zeller.info/)). It is
also an active research area where numerous researchers try to improve the status quo. Though the
science of debugging is not within the scope of this course, we still want to discuss it, especially
regarding the tools you can use for debugging.

## Task 0: Logging

The most basic form of debugging is *tracing* where you track your program's code *flow*, i.e., the
line-by-line execution of your code. There are many tools you can use for code flow tracing, and
logging is the most basic tool where you insert print statements in various places in your code.
Practically all software uses some form of logging. For example, if you look at `/var/log`, you will
see the many log files that Linux generates.

You might have used logging in your own debugging, e.g., using `printf()`. However, most developers
use a *logging library* since it provides many useful features. For example, it allows you to turn
on or off logging messages based on message severity, e.g., fatal, warning, info, etc. It also
allows you to include useful information without requiring you to manually do it, such as source
file name, line number, etc. `printf()` is perhaps sufficient for toy programs, but once you pass
that stage, it is much better to use a logging library.

In this task, you will use a simple C logging library [log.c](https://github.com/rxi/log.c) to debug
a program. Though this library does not have many features, it suffices our purpose of understanding
what a logging library is.

* First, start recording with `record`.
* Create a directory named `logging` under this assignment's directory, and do everything in that
  directory.
* Create a file named `logging.c` and write the following code.
  ```bash
  #include <stdio.h>

  int main() {
    int size = 5;
    int numbers[size];
    int sum = 0;

    for (int i = 0; i <= size; ++i) {
      numbers[i] = i;
      sum += numbers[i];
    }

    printf("The sum is: %d\n", sum);

    return 0;
  }
  ```
* Write a Makefile that compiles `logging.c` and generates an executable named `logging`.
* Go to [log.c](https://github.com/rxi/log.c) and clone the repo outside of `units/`. For example,
  you can clone the repo in your home root directory (`$HOME`).
* Carefully read `README.md` in the cloned repo to understand how to use the logging functions.
* From the cloned repo, copy `log.c` and `log.h` to `logging/`.
* Insert some logging functions in `logging.c` to trace the program execution. Make sure you modify
  your Makefile to compile correctly.
* Debug the problem and fix it. You need to submit the fixed version. Do *not* change the `printf()`
  format since it will be used for grading.
* Entering `make` should produce a working executable named `logging`. This will be used for
  grading.

## Task 1: Assertions

## Task 2: Static checkers

## Task 3: Fuzzers

## Task 4: Debugger

```c
#include <stdio.h>

int main() {
    int *ptr = NULL;
    *ptr = 42;  // This will cause a segmentation fault (access violation)
    return 0;
}
```
