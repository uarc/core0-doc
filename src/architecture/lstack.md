# lstack

The **lstack** is a stack with architecture-dependent depth which allows the initialization of loops with zero-overhead. This means that no instruction is wasted checking for loop bounds and no hardware is needed to do branch prediction for loop bounds. Additionally, for the stack processor it means that the loop index is obtainable in one instruction for the top four loops. Only the top loop is checked for its bounds, so the end of a loop that is not on top of the stack should never be encountered.
