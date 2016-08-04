# ifile

The **ifile** is a register file present in c0 that holds state information about each UARC bus connected to the core. The **ifile** holds is if a given bus is selected, if a bus has interrupts enabled, and the bus' interrupt address. These things can all be accessed through the [UARC instructions](instruction_reference/uarc.html) on the u0-x.
