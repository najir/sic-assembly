# sic-assembly
    Isaac Perks
    06/02/2023

## Description
I've recently gotten into a small development based game on Steam just called "SIC-1" that provides an IDE and various tools for creating/reading SIC-1 Assembly. This game contains a variety of problems to solve and a leaderboard of effeciency based on the provided code. I wanted to create a repo to serve as a way to save a larger collection of all the code and problems solved inside this assembly language for education purposes. 

A good portion of early tasks are too simple to even save a file, so it's description will contain enough info to completely rewrite it.
### Task: Subleq Instruction and Output
Negate the input and write it out
- Simply use subleq to take an input and provide the negation as an output
- SIC-1 contains 0-255 bits where 253 is the reading of an input and 254 reads as 0 and writes a result
- Running subleq 254, 253 provides the solution
    - 254 reads as 0
    - 253 reads in INPUT
    - subleq 254, 253 == 0 - INPUT == -INPUT

Code:

    subleq @OUT, @IN

### Task: Data Directive and Looping
Use .data and Labels to loop
- subleq can be used to jump to another piece of code
    - set A,B to 0 and C to our jumping point label(example: '@loop:')
- .data can be used to set up labeled constants, in this case '@zero: .data 0' to create a constant of 0 for looping

Code:

    @loop:
    subleq @OUT, @IN
    subleq @zero, @zero, @loop

    @zero: .data 0

### Task: Output given Inputs
Use subleq to output any given input
- Using subleq, store a labeled value of -INPUT
- subleq OUTPUT(0), -INPUT
    - subleq 0, -INPUT == 0 --INPUT == INPUT

Code:

    @loop:
    subleq @tmp, @IN
    subleq @OUT, @tmp
    subleq @tmp, @tmp, @loop  ; Reset @tmp to zero, and jump to @loop

    @tmp: .data 0

### Task: Sum two inputs
Using subleq, sum two inputs and provide as output
- Utilize the same code from the last task
- Add in another subleq of labeled value for INPUT
    - resulting code will set variable to -INPUT - INPUT
    - variable is then output as 0 -- variable == 0 - (-INPUT - INPUT)

Code:

    @loop:
    subleq @tmp, @IN
    subleq @tmp, @IN
    subleq @OUT, @tmp
    subleq @tmp, @tmp, @loop  ; Reset @tmp to zero, and jump to @loop

    @tmp: .data 0

### Task: Read, then output 2 variables
- Read A, then B. Then subtract A from B
- Save a loop address
- Save 2 variables to represent 2 inputs and give us access to a variable we can set to 0 again for negating our values
- Take input, save it to temp variable
    - negate this input to make it positive
    - reset on of our used variables back to 0
- Subtract first input by new input
    - use on of our reset variables to negate this value back to original
- send result to output 

Code: 

    @loop:
    ; Saves input to tmp, flips value to positive
    subleq @tmp, @IN
    subleq @zero, @tmp

    ; Subtracts B from tmp and flips again
    subleq @zero, @IN
    ; one of the variables needs reset to 0 to flip negative
    subleq @tmp, @tmp
    subleq @tmp, @zero

    subleq @OUT, @tmp

    ; Resets values to 0 and loops
    subleq @tmp, @tmp
    subleq @zero, @zero, @loop

    ; Saved variables
    @tmp: .data 0
    @zero: .data 0

### Task: Sign Function
- Read input, if < 0 output -1, if == 0 output 0, if > 0 output 1
- If our x > 0, our tmp value is -x
- if x == 0, tmp == 0
- if x < 0, our temp is +x
- Using subleq we can choose to continue to next address or skip to our Eq/Gt labels based on the subraction of certain numbers
    - Our first subleq checks if our value is negative, as subleq would continue to the next address if the result is > 0
    - Our second subleq checks if our value == 0, as adding a 1 to 0 would have our subleq continue to the next address
    - Our last subleq must be greater then so just output

Code: 

    @loop:
    ; If A < 0, subleq goes to next address
    ; If A >= 0, subleq goes to @outputEq
    subleq @tmp, @IN, @outputEq
    subleq @OUT, @one
    subleq @tmp, @tmp, @loop


    @outputEq:
    ; If A -(-1) > 0, subleq goes to next address
    ; If A -(-1) <= 0, subleq goes to @outputGt
    subleq @tmp, @negone, @outputGt
    subleq @OUT, @zero
    subleq @tmp, @tmp, @loop


    @outputGt:
    subleq @OUT, @negone
    subleq @tmp, @tmp, @loop

    @tmp: .data 0
    @zero: .data 0
    @negone: .data -1
    @one: .data 1

## Build
    Windows 11
    SIC-1 on Steam: https://store.steampowered.com/app/2124440/SIC1/
