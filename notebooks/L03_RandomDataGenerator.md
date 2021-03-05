# Information Theory Lab 03: Random Data Generator

## About

This file is designed to be viewed and run online in a browser.

This file is a Jupyter Notebook file usign `xeus-cling`, a Jupyter kernel for C++ based on the `cling` C++ interpreter and the `xeus` native implementation of the Jupyter protocol, xeus.

- GitHub repository: https://github.com/jupyter-xeus/xeus-cling/
- Online documentation: https://xeus-cling.readthedocs.io/ 

<img src="images/xeus-cling.png" alt="xeus-cling logo" style="width: 100px;"/>

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

# 1. Objective

Understand the concepts of entropy and discrete memoryless source. Generate a data file from a memoryless source and attempt to compress it.

# 2. Practical considerations

## 2.1 Generating random numbers in C/C++

In C/C++, you can generate random integer numbers by calling `rand()`, which returns a random integer uniformly selected from the range 0 ... RAND_MAX (a predefined constant).

To work properly, the random number generator must first be initialized with some unique number, by calling `srand()`. A typical scenario is to initialize it with the current time obtained by `time(NULL)`.

Run the following example multiple times:


```c++
srand(time(NULL));   // initialize random number generator, only once, at the beginning of the program
```


```c++
int x = rand();      // generate random x
x                    // display it
```

Let's display the maximum value RAND_MAX:


```c++
RAND_MAX
```

## 2.2 Generating random letters with predefined probabilities

Suppose we want to randomly generate either letter `a` or letter `b`, with probabilities $p_a = 0.7$ and $p_b = 0.3$.

We can do this in the following way: get a random number $x$ with `rand()`, and then check whether $x$ falls in the interval  $[0, \; 0.7]$ * RAND_MAX 
or in the interval  $[0.7, \; 1]$ * RAND_MAX.

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

**Exercise**:  Randomly generate one of the four letters `a`, `b`, `c` and `d` with probabilities 0.4, 0.3, 0.2, 0.1. Start by copying the the example above and then update it.


```c++
// Write solution here:

```

### Exercise 2: generate a vector with random letters

**Exercise**:  Randomly generate 10 letters using one of the methods above, and write them inside a vector `letters[]`


```c++
char letters[10];           // define the vector

// Write solution here:
// ...

// Display the letters at the end
letters 
```

### Exercise 3: generate multiple letters

**Exercise**: Suppose we want to generate any letter of the alphabet (26 letters), according to 26 probabilities available in a vector `prob[26]`.
We want to avoid writing 26 `if ()` instructions, like in the previous examples. 
Can we find a smart algorithm to do this without requiring 26 of `if()` ?


```c++
double prob[26] = {0.036, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04, 0.04};

char letter;

int x = rand();

// Write solution here:
// ...


letter                  // keep this on last line to display the letter
```

## 2.3 Reading probabilities from a vector of strings

In our program, we receive the probabilities as a vector of strings called `argv`, for example:
```
argv:  {"entropy.exe", "", "output.txt", "100", "0.4","0.3","0.2","0.1"}
```

We need to convert these from strings to actual numbers variable. This can be done with the function `sscanf()` ("*scanf for strings*"), which operates just like `scanf()`, but reads data from a string.

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

### Exercise 4: convert command line arguments to string

Convert the following string arguments to numbers:


```c++
const char* argv[] = {"entropy.exe", "", "output.txt", "100", "0.4","0.3","0.2","0.1"};

int size;                 // integer to hold the value 100
double p1, p2, p3, p4;    // 4 floats to hold the probabilities

// Write solution here:
// ...

// Display all of them
printf("size = %d, p1 = %g, p2 = %g, p3=%g, p4=%g\n", size, p1, p2, p3, p4);
```

You should obtain the following line:

```size = 100, p1 = 0.4, p2 = 0.3, p3=0.2, p4=0.1```

## 2.4 File operations in binary mode

### Opening and closing
To open a file we call `fopen()`, specifying the name of the file and what we're opening it for.
The function returns NULL if the file could not be opened, so we can check if something went wrong.


```c++
FILE* f = fopen("file.txt", "rb");   // Open file for reading in binary mode

if (f == NULL)
    printf("OMG the file could not be opened!");   // most likelt the file does not exist
```

When we have finished working the file, we should close it with `fclose()`


```c++
fclose(f); // ***** Don't run this here, it crashes ******
```

### Reading binary data

To read data we call `fread(addr, size, count, file)`, with four arguments:
- **addr** = address where to store the read data (a pointer)
- **size** = size of one element, in bytes
- **count** = number of elements (**Note:** the function is designed to read vectors with `count` elements, each of size `size`)
- **file** = the variable representing the opened file

The function returns the **total number of bytes read**.

Example:

```
fread(vector, sizeof(int), 100, f);  // Reads from file `f` a vector with 100 elements, each with `sizeof(int)` bytes, and stores them in the vector `vector`
```

### Writing binary data

To write data from a vector or a variable into a file, we call `fwrite(addr, size, count, file)`, with four arguments:
- **addr** = address of the data (a pointer)
- **size** = size of one element, in bytes
- **count** = number of elements to wrote (**Note:** the function is designed to write vectors with `count` elements, each of size `size`)
- **file** = the variable representing the opened file

The function returns the **total number of bytes written**.

Example:

```
int vector[] = {1,2,3,4,5,6,7,8};  // a vector with 8 integer values

fwrite(vector, sizeof(int), 5, f);  // Writes the first 5 elements from `vector` into the file `f`
```

# 3. Exercises


1. Write a C program *randgen.c* to generate a file filled with random letters chosen from `a`, `b` or `c`, according to a specified set of probabilities.

    * The program shall receive the **name** of the file, the data **size** and the **probabilities** as command-line arguments:
    
        `randgen.exe data.txt 10000 0.5 0.2 0.3`
    
        The arguments are:
    
         * the name of the output file (e.g. `data.txt`);
         
         * the number of letters to generate (10000);
         * the probabilities (e.g. 0.5, 0.2, 0.3).
           
    * The program should follow the following steps:
    
        * Convert the numerical data from command-line to actual number variables, with `sscanf()`, and display the probabilities.
        
        * Dynamically allocate an array of `unsigned char` of necessary size. The letters are first written here, and then will be written to the file only at the end.
        * Generate characters randomly according to the probabilities (starting from letter 'a') and put them in the array
        * Write the array to the file (in binary format), using `fwrite()`


2. Generate a 10000-bytes long file with all three letters having equal probability.

    a. Compute its entropy using the program from the previous lab (`entropy.exe`).
    
    b. Compress the file using zip or 7zip. What is the compression ratio achieved? How is it related to the entropy?


3. Update the program to do the following:

    - Instead of always considering 3 letters, the program should **deduce the number of letters** itself, from the number of given probabilities. For example, if we provide 5 probabilities $0.3, 0.2, 0.1, 0.2, 0.2$, the program should understand that we want five letters `a`, `b`, `c`, `d`, `e`. The probabilities should be stored as a vector, dynamically allocated, since we don't know beforehand how many they are.
      
      Hint: The program can figure out the number of probabilities from `argc`.
  

4. Generate a file with filled with random letters selected from `a` to `j` (10 letters), with equal probability, and compute its entropy.


# 4. Final questions

1. Can you make a file which cannot be compressed at all? How?
