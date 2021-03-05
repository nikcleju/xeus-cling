# Information Theory Lab 03: Random Data Generator

## Usage

This file is designed to be viewed online in a browser.

This file is a Jupyter Notebook file, usign `xeus-cling`, a Jupyter kernel for C++ based on the `cling` C++ interpreter and the `xeus` native implementation of the Jupyter protocol, xeus.

- GitHub repository: https://github.com/jupyter-xeus/xeus-cling/
- Online documentation: https://xeus-cling.readthedocs.io/

<img src="images/xeus-cling.png" alt="xeus-cling logo" style="width: 200px;"/>

<!--[![xeus-cling](images/xeus-cling.png)](https://github.com/jupyter-xeus/xeus-cling/)-->



## Usage

<div style="background: #efffed;
            border: 1px solid grey;
            margin: 8px 0 8px 0;
            text-align: center;
            padding: 8px; ">
    <i class="fa-play fa" 
       style="font-size: 40px;
              line-height: 40px;
              margin: 8px;
              color: #444;">
    </i>
    <div>
    To run the selected code cell, hit <pre style="background: #efffed">Shift + Enter</pre>
    </div>
</div>

<br>

## 1. Objective

Understand the concepts of entropy and discrete memoryless source. Generate a data file from a memoryless source and attempt to compress it.

# 2. Practical considerations

## 2.1 Generating random numbers in C/C++

In C/C++, you can generate random integer numbers by calling `rand()`, which returns a random integer uniformly selected from the range 0 ... RAND_MAX (a predefined constant).

To work properly, the random number generator must first be initialized with some unique number, bu calling `srand()`. A typical scenario is to initialize it with the current time obtained by `time(NULL)`.

Run the following example multiple times:


```c++
srand(time(NULL));   // initialize random number generator
int x = rand();      // generate random x
x                    // display it
```

Let's display the maximum value RAND_MAX:


```c++
RAND_MAX
```

## 2.2 Generating random letters with predefined probabilities

Suppose we want to randomly generate either `a` or `b`, with probabities $p_1 = 0.7$ (for `a`) and $p_2 = 0.3$ (for `b`).

We can do this by randomly selecting a number $x$ with `rand()`, and then checking whether $x$ falles within the interval $[0, \;\; 0.7] * RAND\_MAX$ (happens with probability 0.7)
or within the interval $[0.7, 1] * RAND\_MAX$ (happens with probability 0.3), as illustrated below:

![Randomly selecting from two intervals](fig/L03_twointervals.png)

Run the following example multiple times and see the different outputs:


```c++
char letter;

int x = rand();
if (x < 0.7*RAND_MAX)
    letter = 'a';
else
    letter = 'b';

letter                      // display it
```

### Exercise 1: generate one of four letters

**Exercise**:  Randomly generate one of the four letters `a`, `b`, `c` and `d` with probabilities 0.4, 0.3, 0.2, 0.1


```c++
// Write solution here:

```

### Exercise 2: generate multiple letters

**Exercise**: Suppose we want to generate one any letter of the alphabet (26 letters), according to 26 probabilities available in a vector `prob[26]`.

We want to avoid writing 26 `if ()` instructions. 

Can we find a smart algorithm to do this?


```c++
double prob[26] = {0.036, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04};

char letter;

int x = rand();

// Write solution here:



letter                  // keep this on last line to display the letter
```

## 2.3 Reading probabilities from a vector of strings

In our program, we receive the probabilities as a vector of strings, for example like 
```
argv:  {"0.4","0.3","0.2","0.1"}
```

We need to convert the numbers from a string to an actual number variable. This can be done with the function `sscanf()` ("*scanf() for strings*"), which operates just like `scanf()`, but reads data from a string.

**Example:**

First let's define a `double` variable to store the result:


```c++
double p;                        // we will store the value into this variable
p                                // display the initial value
```

... and now let's convert the string `"0.4"` to the number `0.4`:


```c++
sscanf("0.4", "%lf", &p);        // first argument = the source string,   second = what we read   , third = where to store it
p                                // the value was put here
```

# 3. Exercises


1. Write a C program to generate random data and save it into a file, according to a specified set of probabilities.

    * The program shall receive the **name** of the file, the data **size** and the **probabilities** as command-line arguments:
    
        `entropy.exe data.txt 10000 0.5 0.1 0.1 0.3`
    
        The arguments are:
    
         * the name of the output file (`data.txt`);
         * the number of bytes to generate (10000);
         * the distribution (0.5 0.1 0.1, 0.3, four different messages). Note: the last probability can be deduced automatically, equal to (1 - sum of all the others).
         
    * The program should follow the following steps:
    
        * Convert the numerical data from command-line to actual number variables, with `sscanf()`, and display the probabilities. The probabilities must be stored as a vector.
        * Understand how many different probabilities are given (i.e. how many different letters we consider), and dynamically allocate a vector to old the probabilities.
        * Allocate an array of `unsigned char` of necessary size;
        * Generate characters randomly according to the probabilities (starting from letter 'a') and put them into array above
        * Write the final array to file (in binary format), using `fwrite()`


2. Generate a 10000-bytes long file with only two messages, with equal probability.

    a. Compute its entropy using the program from the previous lab;
    
    b. Compress the file using zip or 7zip. What is the compression ratio achieved? How is it related to the entropy?
  
3. Repeat the previous exercise with a distribution of four messages and eight, with equal probability.

