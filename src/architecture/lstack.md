# lstack

The **lstack** is a stack with architecture-dependent depth which allows the initialization of loops with zero-overhead. This means that no instruction is wasted checking for loop bounds and no hardware is needed to do branch prediction for loop bounds. Additionally, for the stack processor it means that the loop index is easily obtainable in one instruction for the top four loops. Only the very top loop is checked for its bounds, so the end of a loop that is not on top of the stack should never be encountered.

This stack also preserves `dc0` on each iteration so that immediate values can be consumed every loop instead of needing to load everything onto the stack. The other DCs are not preserved so that they can be used for vector operations. In this situation `dc0` is treated specially.
