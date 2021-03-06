# dstack

The **dstack** is the primary stack on which all operations take place. This stack is the only stack that can be rotated and copied in one cycle. If the **dstack** is rotated, all elements below the selected element will not shift down, but all elements above it will shift down and the selected element will be placed on the top. If the **dstack** is copied, all elements move down and the copied element is moved to the top.

All elements on the **dstack** are capable of being pushed once, doing nothing, popping once, or popping twice. When the stack is rotated or copied, the `[4:0]` bits are used to encode the location to perform the operation on, which makes the critical path for determining how the **dstack** is affected by the instruction as short as possible.

The **dstack** is where almost all parameters should be passed into functions and where almost all parameters should be returned from functions. Should a function need to access a loop index explicitly as a constant parameter, it can safely use loop index instructions. Information may also be passed into functions via the status bits. A compiler should focus on passing information purely on the stack.

Inline expansion of functions is easily possible by the compiler since the inlined function consumes the parameters in the same already-optimized fashion. On a register-machine, the compiler could perform optimization passes to avoid moving registers before passing them to the function, but since the stack machine already passes parameters in the correct order, functions can be inlined efficiently after compilation without any drawbacks.
