# ifile

The ifile is a register file present in c0 that holds state information about each UARC bus connected to the core. The most important thing the ifile holds is if a given port is enabled, has interrupts enabled, and the interrupt address. These three things can all be access through the [UARC instructions](instruction_reference/uarc.html) on the c0.
