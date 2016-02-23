# Overview

UARC core0 (c0) is the primary general purpose ISA for the UARC standardized core collection. Although it is an ISA, a reference implementation is provided via emulator and VHDL.

This architecture is targeted towards small size and efficiency in shuffling data around across the UARC bus. No floating point instructions are included since it is expected that such algorithms will not run on core0, but will instead be offloaded to another UARC compatible core with floating point support. Due to the nature of UARC, it is possible to implement floating point operations using a coprocessor that connects over the UARC bus if desired.

It is important to note that although this core can be used for an OS that spreads cross multiple UARC cores, it is not intended to be the target of user space applications, except where those applications need to shuffle data around between UARC cores or only require integer arithmetic. c0 demonstrates the UARC principle while simultaneously acting as a mediator of data between UARC cores.

c0's ISA is designed to use whatever word size is required, but it is intended to be used as a 32-bit architecture.
