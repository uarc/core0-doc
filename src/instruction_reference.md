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
Conveyor instructions allow random read access to the 16 things on the [conveyor belt](architecture/conveyor.md). The things on the conveyor may actually not be present and when accessed, the completion of the operation corresponding to that spot on the conveyor will be synchronized. This allows several asynchronous operations to be linked to different locations on the conveyor and read randomly when they are needed.

### `R` - Random
For R type instructions, 32 locations can be randomly addressed. This means that 32 places can be copied and rotated on the [dstack](architecture/dstack.md) and 32 places can be read and written on the [tstack](architecture/tstack.md).

## Key Words
- `WORD` - Data word width in use
- `c` - Carry bit
- `cv` - [Conveyor Belt](architecture/conveyor.md)
- `dc[0-3]` - Data Counters
- `pc` - Program Counter
- `i[0-3]` - Loop Indices
- `cv[0-F]` - [Conveyor Belt](architecture/conveyor.md) Values
- `ts` - [tstack](architecture/tstack.md)
- `if` - [ifile](architecture/ifile.md)

## Instruction Listing By Opcode

|Op|Instruction|dstack|Side Effects|
|:---:|:---:|:---:|:---:|:---:|
|`00` - `03`|rread#|`a -- mem[dc# + a]`||
|`04`|inc|`a -- (a + 1)`|`c`, `o`|
|`05`|dec|`a -- (a - 1)`|`c`, `o`|
|`06`|flush|` -- `|Synchronizes cache flush|
|`07`|reads|`a -- mem[a]`|Synchronous read|
|`08`|ret|` -- `|Pops [cstack](architecture/cstack.md)|
|`09`|ien|` -- `|Enables selected interrupts|
|`0A`|idi|` -- `|Disables selected interrupts|
|`0B`|tcopy|`v -- v`|Pushes a copy of v to [tstack](architecture/tstack.md)|
|`0C`|recv|` -- `|Interrupt sync; `cv <- bus, v`|
|`16`|calli|` -- `|`dc0 -> pc`; push [cstack](architecture/cstack.md)|
|`17`|jmpi|` -- `|`dc0 -> pc`|
|`20` - `2F`|cv#|` -- cv#`|cv# synchronizes|
|`30` - `33`|read#|` -- mem[dc#]`|dc# advances|
|`34` - `37`|i#|` -- i#`| |
|`38`|p0|` -- 0`| |
|`39`|dup|`a -- a a`| |
|`40` - `43`|write#|`v -- `|`mem[dc#] = v`; dc# advances|
|`44` - `47`|setf#|`a -- `|`dc# = a`; dc# is write post-inc|
|`48` - `4B`|setb#|`a -- `|`dc# = a`; dc# is write pre-dec|
|`4C`|add|`a b -- (a + b)`|`c`, `o`|
|`4D`|addc|`a b -- (a + b + c)`|`c`, `o`|
|`4E`|sub|`a b -- (a - b)`|`c`, `o`|
|`4F`|subc|`a b -- (a - b + c)`|`c`, `o`|
|`50`|lsl|`a b -- (a << b)`|`c`, `o`|
|`51`|lsr|`a b -- (a >> b)`|`c`, `o`|
|`52`|asr|`a b -- (a >>> b)`|`c`, `o`|
|`53`|mul|`a b -- (a * b)`|`cv <- (a * b)[2*WORD-1:WORD]`|
|`54`|mulu|`a b -- (a * b)`|`cv <- (a * b)[2*WORD-1:WORD]`|
|`55`|reada|`a -- `|`cv <- mem[a]`|
|`56`|call|`a -- `|`pc = a`; push [cstack](architecture/cstack.md)|
|`57`|jmp|`a -- `|`pc = a`|
|`58`|tpush|`a -- `|`t`|
|`59`|seb|`b -- `|`if[b[WORD-1:WORD/2]] = b[WORD/2-1:0]`|
|`5A`|slb|`b -- `|`b[WORD/2-1:0]` to `if[b[WORD-1:WORD/2]]`|
|`5B`|iset|`a -- `|Set selected interrupt addresses|
|`5C`|send|`v -- `|Send value to selected buses|
|`5D`|in|`n a -- b`|Stream n words in and push bus|
|`60` - `63`|rwrite#|`v a -- `|`mem[dc# + a] = v`|
|`64`|write|`v a -- `|`mem[a] = v`|
|`65`|jeq|`a b -- `|if `a == b` then `dc0 -> pc`|
|`66`|jne|`a b -- `|if `a != b` then `dc0 -> pc`|
|`67`|les|`a b -- `|if `a < b` then `dc0 -> pc`|
|`68`|leq|`a b -- `|if `a <= b` then `dc0 -> pc`|
|`69`|out|`n a -- `|Stream n words to buses from a|
|`69`|div|`a b -- `|`cv <- a / b, a % b`|
|`6A`|divu|`a b -- `|`cv <- a / b, a % b`|
