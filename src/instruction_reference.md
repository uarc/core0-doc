# Instruction Reference

### c0 Instruction Formats

| Format | Layout |
|:------ |:------:|
|I (Implicit)|`OSSO OOOO`|
|L (Loop)|`OSSO OOLL`|
|D (DC)|`OSSO OOLL`|
|C (Conveyor)|`OSSO LLLL`|
|R (Random Access)|`OOOL LLLL`|

 - `O` - Opcode bit
 - `S` - Stack bit
 - `L` - Location bit

### Stack bits
- `00` - The stack is not popped or pushed.
- `01` - The stack is pushed once.
- `10` - The stack is popped once.
- `11` - The stack is popped twice.

### `I` - Implicit
Implicit instructions have no explicit sources or destinations and all can be assumed from state information in the system. Such instructions include `add`, which takes parameters from the stack and places the result back on the stack.

### `L` - Loop
Loop instructions allow random read access to the loop index of the top 4 loops in the [lstack](architecture/lstack.md). This means that loops can be nested 4 times and are still able to retrieve the index in one instruction.

### `D` - Data Counter
Data Counter instructions are instructions that operate on a random one of the 4 available DCs. These can be set, read, written, randomly read, and randomly written.

### `C` - Conveyor
Conveyor instructions allow random read access to the 16 things on the conveyor belt. The things on the conveyor may actually not be present and when accessed, the completion of the operation corresponding to that spot on the conveyor will be synchronized. This allows several asynchronous operations to be linked to different locations on the conveyor and read randomly when they are needed.

### `R` - Random
For R type instructions, 32 locations can be randomly addressed. This means that 32 places can be copied and rotated on the [dstack](architecture/dstack.md) and 32 places can be read and written on the [tstack](architecture/tstack.md).

## Key Words
- `WORD` - Data word width in use
- `c` - Carry bit
- `cv` - Conveyor Belt
- `dc[0-3]` - Data Counters
- `pc` - Program Counter
- `i[0-3]` - Loop Indices
- `cv[0-7]` - Conveyor Belt Values

## Instruction Listing By Opcode

|Op|Instruction|dstack|Side Effects|
|:---:|:---:|:---:|:---:|:---:|
|`00` - `03`|rread#|`a -- mem[dc# + a]`||
|`20` - `2F`|cv#|` -- cv#`|cv# synchronizes|
|`30` - `33`|read#|` -- mem[dc#]`|dc# advances|
|`34` - `37`|i#|` -- i#`| |
|`40` - `43`|write#|`v -- `|`memory[dc#] = v`; dc# advances|
|`44` - `47`|setf#|`a -- `|`dc# = a`; dc# is write post-inc|
|`48` - `4B`|setb#|`a -- `|`dc# = a`; dc# is write pre-dec|
|`40`|add|`a b -- (a + b)`|`c`, `o`|
|`41`|addc|`a b -- (a + b + c)`|`c`, `o`|
|`42`|sub|`a b -- (a - b)`|`c`, `o`|
|`43`|subc|`a b -- (a - b + c)`|`c`, `o`|
|`44`|lsl|`a b -- (a << b)`|`c`, `o`|
|`45`|lsr|`a b -- (a >> b)`|`c`, `o`|
|`46`|asr|`a b -- (a >>> b)`|`c`, `o`|
|`47`|mul|`a b -- (a * b)`|`cv <- (a * b)[2*WORD-1:WORD]`|
|`48`|mulu|`a b -- (a * b)`|`cv <- (a * b)[2*WORD-1:WORD]`|
|`60` - `63`|rwrite#|`v a -- `|`memory[dc# + a] = v`|
|`64`|les|`a b -- `|if `a < b` then `dc0 -> pc`|
|`65`|leq|`a b -- `|if `a <= b` then `dc0 -> pc`|
|`66`|div|`a b -- `|`cv <- a / b, a % b`|
|`67`|divi|`a b -- `|`cv <- a / b, a % b`|
