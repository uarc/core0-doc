# ifile

The **ifile** is a register file present in c0 that holds state information about each UARC bus connected to the core. The most important thing the ifile holds is if a given port is enabled, if a port has interrupts enabled, and the port's interrupt address. These things can all be accessed through the [UARC instructions](instruction_reference/uarc.html) on the u0-x.
