# stanley-penguin-neural-network

A neural network implemented in the [Stanley/Penguin](https://github.com/yaznat/stanley-penguin-assembler) assembly language.

## Dataset

- We train the network on a random subset of **20,992** images from the [MNIST dataset](https://en.wikipedia.org/wiki/MNIST_database).
- We evaluate the network on a separate set of **2048** images.

## Architecture

```
Linear(64)
    ↓
  ReLU()
    ↓
Linear(10)
    ↓
 Argmax()
```

Param count: **50,890**.

**Note:** currently, we do not apply bias in linear layers. Bias implementation coming soon.

## Results

After training for **15** epochs at a batch size of **256**, the network reaches **TBD** test accuracy.

## Implementation Challenges

Given the simplicity of Stanley/Penguin assembly, this implementation addresses the following challenges:

### No Floating-Point Operations

We establish a fixed-point representation. We select 12 fractional bits:

```
S = 2^12 = 4096
```

Weight initialization, matrix multiplication, and one-hot encoding are properly scaled by S.

### No Function Calls

We design a simple call stack that maintains arguments, parameters, and destination and return addresses:

```
; --- load x and y into global values managed by the stack ---
LOAD   x
STORE  arg_0
LOAD   y
STORE  arg_1

; --- specify destination and return ---
LOAD   FUNC_ADDR
STORE  destination
LOAD   IP_ADDR
STORE  return

JMP    push                   ; jumps to stack push logic -> goes to function
```

Function implementation:

```
; --- func ---
; param_0:  x
; param_1:  y
;
; Prints the sum x + y to the terminal.

FUNC_ADDR: func

     func: LOAD param_0
     ...
     JMP   pop                ; returns to next address after "JMP push"
```

The call stack copies the proper `arg` values into `param` values before jumping to a function.
