# Instruction Reference

### c0 Instruction Formats

| Format | Layout |
|:------ |:------:|
|I (Implicit)|`OOOO OOOO`|
|L (Loop)|`OOOO OOLL`|
|C (Conveyor)|`OOOO LLLL`|
|R (Random Access)|`OOOL LLLL`|

 - `O` - Opcode bit
 - `L` - Location bit
 - `I` - Index bit

c0, being a simple architecture, has a simple instruction layout. All instructions are 8-bit words and come in four formats.

### `I` - Implicit
Implicit instructions have no explicit sources or destinations and all can be assumed from state information in the system. Such instructions include `add`, which takes parameters from the stack and places the result back on the stack.

### `L` - Loop
Loop instructions allow random read access to the loop index of the top 4 loops in the [lstack](architecture/lstack.md). This means that loops can be nested 4 times and are still able to retrieve the index in one instruction.

### `C` - Conveyor
Conveyor instructions allow random read access to the 16 things on the conveyor belt. The things on the conveyor may actually not be present and when accessed, the completion of the operation corresponding to that spot on the conveyor will be synchronized. This allows several asynchronous operations to be linked to different locations on the conveyor and read randomly when they are needed.

### `R` - Random
For R type instructions, 32 locations can be randomly addressed. This means that 32 places can be copied and rotated on the [dstack](architecture/dstack.md) and 32 places can be read and written on the [tstack](architecture/tstack.md).

## Key Words
- `WORD` - Data word width in use
- `c` - Carry bit
- `cv` - Conveyor Belt

## Instruction Listing By Opcode

|Op|Instruction|dstack|Side Effects|
|:---:|:---:|:---:|:---:|:---:|
|`40`|add|`a b -- (a + b)`|`c`, `o`|
|`41`|addc|`a b -- (a + b + c)`|`c`, o|
|`42`|sub|`a b -- (a - b)`|`c`, `o`|
|`43`|subc|`a b -- (a - b + c)`|`c`, `o`|
|`44`|les|`a b -- `|if `a < b` then `pc <- dc0`|
|`44`|lsl|`a b -- (a << b)`|`c`, `o`|
|`45`|lsr|`a b -- (a >> b)`|`c`, `o`|
|`46`|asr|`a b -- (a >>> b)`|`c`, `o`|
|`47`|mul|`a b -- (a * b)`|`cv <- (a * b)[2*WORD-1:WORD]`|
|`48`|mulu|`a b -- (a * b)`|`cv <- (a * b)[2*WORD-1:WORD]`|
|`60`|div|`a b -- `|`cv <- a / b, a % b`|
|`61`|divi|`a b -- `|`cv <- a / b, a % b`|
