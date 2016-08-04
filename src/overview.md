# Overview

UARC core0 (u0-x) is the primary general purpose ISA for the UARC standardized core collection. Although it is an ISA, a reference implementation is provided via emulator and SystemVerilog. The 'x' in the name refers to the bit width (e.g. u0-32 (32-bit implementation)).

This architecture is targeted towards small size and efficiency in shuffling data around across the UARC bus. No floating point instructions are included since it is expected that such algorithms will not run on u0-x, but will instead be offloaded to another UARC compatible core with floating point support. Due to the nature of UARC, it is possible to implement floating point operations using a coprocessor that connects over the UARC bus and provides support to multiple cores at once.

u0-x's ISA is designed to use whatever word size is required, but it is primarily intended to be used as a 32-bit architecture (u0-32). Currently, it provides no virtual memory system, and isn't compatible with SMP operating systems. A 64-bit implementation might be useful if >16GB of memory (4G words) is in use per core or if 64-bit data is used often, though both of these cases seem unlikely (except in scientific applications).
