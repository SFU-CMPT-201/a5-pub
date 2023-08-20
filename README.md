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

* First, start recording with `record`. Make sure you are in the same directory that this
  `README.md` file is in.
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
* Insert some logging functions to `logging.c` to trace the program execution. Make sure you modify
  your Makefile to compile correctly.
* Debug the problem and fix it. You need to submit the version that prints out logging messages
  using `log.c` and fixes the problem. Do *not* change the `printf()` format since it will be used
  for grading.
* Entering `make` should produce a working executable named `logging`. This will be used for
  grading.

## Task 1: Assertions

An assertion is a function that checks if a condition is true and, if not, throws an error. You can
use assertions for anything but the most basic use cases are checking conditions on input arguments
and return values. In C, `assert()` defined in `<assert.h>` provides the functionality. In this
task, you will first learn the background and do an exercise.

### Checking input argument conditions

When you write a function, you make certain assumptions about your input arguments. For example,
let's say you're writing a function that performs integer division as follows.

```c
int unsafe_divide(int dividend, int divisor) {
  return dividend / divisor;
}
```

When writing this function, you're making an implicit assumption that `divisor` is not 0. This is
known as *division by zero*, and in C, this is *undefined behavior*, meaning it can do whatever. In
addition, you are also making an assumption that you are *not* dividing the minimum value possible
for an integer by -1, i.e., it is not that `dividend` is `INT_MIN` and `divisor` is -1 at the same
time. The reason is because `INT_MIN` is -2^31 (assuming a 32-bit integer) and if you were to divide
it by -1, you would have to get +2^31, which is 1 more than the maximum value possible for a 32-bit
integer (2^31 - 1). This is called *integer overflow* and it is also undefined behavior.

In cases like this, you might want to check if these assumptions that you have actually hold. This
way, you don't run into undefined behavior, which will do something unexpected. You can use
assertion to perform such a check.

```c
#include <assert.h>
#include <limits.h>

int divide(int dividend, int divisor) {
  assert(divisor != 0 && (!((dividend == INT_MIN) && (divisor == -1))));
  return dividend / divisor;
}
```

If you call the above function with `INT_MIN` as `dividend` and -1 as `divisor` or just -1 as
`divisor`, the program will abort. This is useful for testing since you can catch the use of your
function that violates your assumptions. Generally, assertions do not have to abort but `assert()`
does abort and is used when termination is the right thing to do.

More broadly, a condition that must be met before executing a piece of code is called a
*precondition*. It is a general concept and can be applied to not just a function but any section of
code in a program. It is frequently used as a safety check before executing a piece of code, similar
to the way we used `assert()` in the example above.

To learn more about undefined behavior in C, we highly encourage you to read [A Guide to Undefined
Behavior in C and C++](https://blog.regehr.org/archives/213) by John Regehr. The above example of
division by zero comes from that article. Also, if you want to see the list of undefined behavior,
you can look at the end of [this article](https://blog.regehr.org/archives/1520) by the same author.
Undefined behavior is a very common source of bugs and vulnerabilities in a program. [This
article](http://blog.llvm.org/2011/05/what-every-c-programmer-should-know.html) by Chris Lattner,
one of the creators of Clang, is also a good read on this topic.

### Checking return conditions

Another common use case for `assert()` is right before returning from a function to check if the
conditions for the return value are met. For example, let's say you are writing a function that
returns the absolute value of an integer as follows.

```c
int absolute_value(int num) {
  int ret = 0;

  if (num < 0) {
    ret = -num;
  } else {
    ret = num;
  }

  return ret;
}
```

Since you want to return an absolute value, it is a good idea to add a check that verifies that the
return value is greater than or equal to 0.

```c
int absolute_value(int num) {
  int ret = 0;

  if (num < 0) {
    ret = -num;
  } else {
    ret = num;
  }

  assert(ret >= 0);
  return ret;
}
```

Similar to the term *precondition* mentioned above, this is broadly called a *postcondition*. A
postcondition is a condition that must be met after executing a piece of code. This is also a
general concept that goes beyond function returns, and the use of `assertion()` in the above example
is just one example.

### Assertion exercise

* If you're not already recording, start recording with `record`. Make sure that you start recording
  in the same directory where this `README.md` is located.
* Create a directory named `assertions` and do everything in that directory.
* Create a file named `abs.c` with the `absolute_value()` function above.
* Add a *precondition* that catches the value of `int num` that can cause undefined behavior. The
  program should abort if the value is passed as the argument. (Hint: read the discussion above on
  undefined behavior.)
* Create a file named `main.c` with a `main()` function that calls your `absolute_value()` function
  (that contains the precondition you added). When calling `absolute_value()`, pass the value that
  causes undefined behavior, thereby causing your precondition to fail.
* Write a Makefile that produces the executable named `assertions`.

## Task 2: Static checkers and sanitizers

We mentioned linters and sanitizers before, which are useful tools for debugging. Linters perform
*static* checks as opposed to *dynamic* checks in the sense that their checks occur when you write
your code rather than when you run your code. You might recall that we discussed the differences
between the terms "static" and "dynamic" when we discussed static and dynamic (shared) libraries. At
that time, we said that static referred to something that occurs at compile time and dynamic
referred to something that occur at run time. However, static actually refers to something
broader---it refers to something that occurs by analyzing code rather than running it. For this
reason, linters belong to a category of tools called *static analysis* tools.

In contrast, sanitizers are dynamic tools in the sense that they perform their checks at run time.
As mentioned before, Clang provides many sanitizers such as `AddressSanitizer`, `ThreadSanitizer`,
`MemorySanitizer`, `UndefinedBehaviorSanitizer`, etc. You can enable these sanitizers at compile
time by using Clang's compiler options (`-fsanitize=`). In this task, you will use one of the
sanitizers and see which error you get.

* Again, if you're not already recording, start recording with `record`. Make sure that you start
  recording in the same directory where this `README.md` is located.
* Create a directory named `sanitizers` and do everything in that directory.
* Create a file named `abs.c` with the `absolute_value()` function above.
* Remove all `assert()` calls from your `absolute_value()` function.
* Create a file named `main.c` with a `main()` function that calls your `absolute_value()` function.
  When calling `absolute_value()`, pass the value that causes undefined behavior.
* Write a Makefile that produces the executable named `assertions`.
* Run the executable and see what happens.
* Modify your Makefile so you can enable `UndefinedBehaviorSanitizer`. The option you need to
  include is `-fsanitize=undefined`.
* Compile and run the executable. The executable should terminate with an error message about the
  undefined behavior. As you can see, a sanitizer automatically checks what you can check with an
  assertion.

## Task 3: Debugger

The last debugging tool we want to discuss is debuggers. A debugger gives you a powerful set of
features suited for all debugging tasks, e.g., step-wise execution, variable inspection, reverse
execution, code and memory manipulation, etc. In this task, you will work through some tutorials and
do an exercise.

* Once again, if you are not recording, start recording with `record`. You should do this in the
  directory that this `README.md` file is in.
*

```c
#include <stdio.h>

int main() {
    int *ptr = NULL;
    *ptr = 42;
    return 0;
}
```
