# cstack

|State|Result on Pop|
|:---:|:--:|
|PC|Moves PC back to location in caller after the jump/call|
|interrupt|Set the `i` flag to allow checks for interrupt handling|

The **cstack** is a call stack that contains state information required to return from a subroutine call. This includes the PC and if the last call was to an interrupt. The status bits are not preserved in a call. If an interrupt is issued and accepted or either of the call instructions are executed, the **cstack** will be pushed. Any time a return instruction is executed, the **cstack** is popped. The **cstack** should be accessable on a single-cycle notice and thus should exist in processor memory. Because it is only pushed and popped in regular amounts, it deserves a place on-die as its own processor element, though this is not absolutely necessary.

The reason for including a dedicated call stack is because parameters need to be passed on the [dstack](dstack.html). If the return address was passed on the [dstack](dstack.html), it would need to be pushed before the parameters so that after the parameters are used the user can return and it would require that nothing be returned from the function. The first requirement disallows the return address to be infered when calling, while the second requirement is simply absurd, because returning things from a function is essential. The dedicated call stack ensures that returning from functions is always a trivial matter and no instruction overhead is incured dealing with return addresses (aside from the return instruction itself).

If the **cstack** is pushed due to an interrupt, the interrupt bit is set on the **cstack** so that the interrupt flag can be appropriately set on a return from the interrupt and so that the processor can internally switch the mode back for the [conveyor](conveyor.html). The interrupt bit allows the main program to poll for if an interrupt has been handled in a loop, which can reduce the overhead of the polling in the loop to a single branch instruction.
