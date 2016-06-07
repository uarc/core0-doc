# ifile

The ifile is a register file present in c0 that holds state information about each UARC bus connected to the core. The most important thing the ifile holds is if a given port is enabled, has interrupts enabled, the interrupt address, and the `dc0` to set on an interrupt to that bus. These things can all be accessed through the [UARC instructions](instruction_reference/uarc.html) on the c0.
