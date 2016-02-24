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

## Instruction Listing By Opcode

|Op|Instruction|dstack|Side Effects|
|:---:|:---:|:---:|:---:|:---:|
|`40`|add|`a b -- (a + b)`|Carry is set|
|`41`|addc|`a b -- (a + b + c)`|Carry is set|
|`42`|sub|`a b -- (a - b)`|Carry is set|
|`43`|subc|`a b -- (a - b + c)`|Carry is set|
|`44`|asl|`a b -- (a << b)`|Overflow is set|
|`45`|asr|`a b -- (a >> b)`| |
|`46`|mul|`a b -- (a * b)`|Sets HILO register|
|`47`|mulu|`a b -- (a * b)`|Sets HILO register|
|`60`|div|`a b -- `|`a / b, a % b` to conveyor|
|`61`|divi|`a b -- `|`a / b, a % b` to conveyor|
