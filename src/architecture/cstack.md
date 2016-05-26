# cstack

|State|Result on Pop|
|:---:|:--:|
|PC|Moves PC back to location in caller after the jump/call|
|DCs|Restores all DCs to previous locations|
|DC modifications|Tracks if the DCs were set in the previous call|
|DC directions|The direction the DCs move when written|
|interrupt|Set the `i` flag to allow checks for interrupt handling|

The cstack is a call stack that contains state information required to return from a subroutine call. This includes things such as the PC, the DCs, and if the last call was to an interrupt. The status bits are not preserved in a call. If an interrupt is issued and accepted or either of the call instructions are executed, the cstack will be pushed. Any time a return instruction is executed, the cstack is popped. The cstack should be accessable on a single-cycle notice and thus should exist in processor memory. Because it is only pushed and popped in regular amounts, it deserves a place on-die as its own processor element, though this is not absolutely necessary, and the stack can be spilled into main memory if the need arises.

If the cstack is pushed due to an interrupt, the interrupt bit is set on the cstack so that the interrupt flag can be appropriately set on a return from the interrupt and so that the processor can internally switch the mode back for the [conveyor](conveyor.html). The cstack also tracks if new DCs are set by this subroutine. If any particular DC is set in the subroutine, a flag is set which indicates this, and on returning from the subroutine the previous DC is restored. However, if the flag is not set, then the DC is not restored. The purpose of this is to allow a subroutine to use the DC of the parent caller and upon returning to the parent continue where the child left off. This is important for inlining functions and allowing a child to consume part of a vector pointed to by a DC. In all other cases, if the child sets its own DC, it is necessary to restore the DCs to their previous locations.
