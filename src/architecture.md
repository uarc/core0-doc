# Architecture

### Status
|Symbol|Purpose|
|:---:|:---:|
|`c`|Carry bit|
|`o`|Overflow bit|
|`i`|Interrupt bit|

c0 is a stack machine, meaning that all parameters are implicit and all destinations are implicit except for stack manipulation and memory access. For comparison, belt machines have an implicit destination and register machines (the most common) have explicit parameters and destination. This means that for many instructions, a stack machine's instruction is entirely composed of an opcode with no extra fields.

Since no task switching is required in c0, it is not necessary to be concerned with the amount of state elements in the architecture, except where those elements require random access. Due to this, several stacks are present in the architecture aside from the main stack on which most of the processing occurs: the [dstack](architecture/dstack.html). For instance, the architecture also has a dedicated [call stack](architecture/cstack.html) so that parameters can be passed on the [dstack](architecture/dstack.html) orderly to a called routine and be consumed without worrying about the return address. A zero-overhead loop unit is also present that allows automatic conditional branching back to the beginning of the inner-most loop. The stack that contains the various loop parameters is called the [loop stack](architecture/lstack.html).

Not all stacks present in c0 are Last-In First-Out (LIFO). The [dstack](architecture/dstack.html) itself has dedicated copy and rotate instructions so that it is possible to copy or move things to the top of the stack. The [dstack](architecture/dstack.html) copy instruction is intended to allow variables to be computed once and copied into place on the stack however many additional times they are required before the final value is consumed. The rotate instruction is used when it cannot be satisfied that an accumulator will be on the top of the stack when it must be accumulated to during a loop. Rotate may also need to be used in conditional branching when different branch paths do not consume values from the stack in the same order. Branch paths that are more likely to be taken should avoid the rotate instruction and rotates should be added to the less-likely path.

Finally, the [ifile](architecture/ifile.html) is a register file containing the interrupt address, enable bit, and selection bit that corresponds to each UARC bus present on the core. Each of the interrupts can be individually enabled, disabled, and set. Using the selection mechanism, several addresses can be enabled, disabled, and set simultaneously to avoid wasting time setting up interrupts or sending/receiving messages to/from multiple targets.
