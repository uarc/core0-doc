# Instruction Reference

### c0 Instruction Formats

| Format | Layout |
|:------:|:------:|
|I (Implicit)|`OOOO OOOO`|
|L (Location)|`OOOL LLLL`|

 - O - Opcode bit
 - L - Location bit

c0, being a simple architecture, has a simple instruction layout. All instructions are 8-bit words and come in two formats.

For L type instructions, 32 locations can be randomly addressed. This means that 32 places can be copied and rotated on the [dstack](architecture/dstack.md) and 32 places can be read and written on the [tstack](architecture/tstack.md). Any I type instruction has completely implicit parameters and destinations.
