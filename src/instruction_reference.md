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
Loop instructions allow random read access to the loop index of the top 4 loops in the [lstack](architecture/lstack.html). This means that loops can be nested 4 times and are still able to retrieve the index in one instruction.

### `D` - Data Counter
Data Counter instructions are instructions that operate on a random one of the 4 available DCs. These can be set, get, read, written, randomly read, and randomly written.

### `C` - Conveyor
Conveyor instructions allow random read access to the 16 things on the [conveyor belt](architecture/conveyor.html). The things on the conveyor may actually not be present and when accessed, the completion of the operation corresponding to that spot on the conveyor will be synchronized. This allows several asynchronous operations to be linked to different locations on the conveyor and read randomly when they are needed.

### `R` - Random
For R type instructions, 32 locations can be randomly addressed. This means that 32 places can be copied and rotated on the [dstack](architecture/dstack.html) and 32 places can be read and written on the [tstack](architecture/tstack.html).

## Key
- `WORD` - Data word width in use
- `c` - Carry bit
- `cv` - [Conveyor Belt](architecture/conveyor.html)
- `dc[0-3]` - Data Counters
- `pc` - Program Counter
- `iflag` - Interrupt Flag
- `i[0-3]` - Loop Indices
- `cv[0-F]` - [Conveyor Belt](architecture/conveyor.html) Values
- `ts` - [tstack](architecture/tstack.html)
- `ls` - [lstack](architecture/lstack.html)
- `if` - [ifile](architecture/ifile.html)
- `n..` - `n` anonymous words on the stack

## Instruction Listing By Opcode

|Op|Instruction|dstack|Side Effects|
|:---:|:---:|:---:|:---:|:---:|
|`00` - `03`|rread#|`a -- mem[dc# + a]`||
|`04` - `07`|add#|`a -- (a + dc#)`|dc# advances; `c`, `o`|
|`08`|inc|`a -- (a + 1)`|`c`, `o`|
|`09`|dec|`a -- (a - 1)`|`c`, `o`|
|`0A`|carry|`v -- (v + c)`|`c`, `o`|
|`0B`|borrow|`v -- (v + c - 1)`|`c`, `o`|
|`0C`|inv|`v -- ~v`| |
|`0D`|flush|` -- `|Synchronizes cache flush|
|`0E`|reads|`a -- mem[a]`|Synchronous read|
|`0F`|ret|` -- `|Pops [cstack](architecture/cstack.html)|
|`10`|ien|` -- `|Enables selected interrupts|
|`11`|idi|` -- `|Disables all interrupts|
|`12`|tcopy|`v -- v`|Pushes a copy of v to [tstack](architecture/tstack.html)|
|`13`|recv|` -- `|Interrupt sync; `cv <- bus, v`|
|`14`|in|`a -- b`|Stream in to `a`|
|`15`|kill|` -- `|Kill all selected cores|
|`16`|wait|` -- `|Waits for an interrupt before continuing|
|`18`|calli|` -- `|`dc0 -> pc`; push [cstack](architecture/cstack.html)|
|`19`|jmpi|` -- `|`dc0 -> pc`|
|`1A`|jc|` -- `|if `c` then `dc0 -> pc`|
|`1B`|jnc|` -- `|if `~c` then `dc0 -> pc`|
|`1C`|jo|` -- `|if `o` then `dc0 -> pc`|
|`1D`|jno|` -- `|if `~o` then `dc0 -> pc`|
|`1E`|ji|` -- `|if `iflag` then `dc0 -> pc`|
|`1F`|jni|` -- `|if `~iflag` then `dc0 -> pc`|
|`20` - `2F`|cv#|` -- cv#`|cv# synchronizes|
|`30` - `33`|read#|` -- mem[dc#]`|dc# advances|
|`34` - `37`|get#|` -- dc#`| |
|`38` - `3B`|i#|` -- i#`| |
|`3C`|p0|` -- 0`| |
|`3D`|dup|`v -- v v`| |
|`3E`|getp|` -- priv`|Get UARC privilege|
|`3F`|geta|` -- addr`|Get UARC address|
|`40` - `43`|write#|`v -- `|`mem[dc#] = v`; dc# advances|
|`44` - `47`|setf#|`a -- `|`dc# = a`; dc# is write post-inc|
|`48` - `4B`|setb#|`a -- `|`dc# = a`; dc# is write pre-dec|
|`4C`|add|`a b -- (a + b)`|`c`, `o`|
|`4D`|addc|`a b -- (a + b + c)`|`c`, `o`|
|`4E`|sub|`a b -- (a - b)`|`c`, `o`|
|`4F`|subc|`a b -- (a - b + c)`|`c`, `o`|
|`50`|lsl|`a b -- (a << b)`|`o`|
|`51`|lsr|`a b -- (a >> b)`|`o`|
|`52`|csl|`a b -- ((a << b) or (a >> (b - WORD)))`| |
|`53`|csr|`a b -- ((a >> b) or (a << (b - WORD)))`| |
|`54`|asr|`a b -- (a >>> b)`|`o`|
|`55`|and|`a b -- (a & b)`| |
|`56`|or|`a b -- (a or b)`| |
|`57`|reada|`a -- `|`cv <- mem[a]`|
|`58`|call|`a -- `|`pc = a`; push [cstack](architecture/cstack.html)|
|`59`|jmp|`a -- `|`pc = a`|
|`5A`|tpush|`a -- `|`ts <- a`|
|`5B`|iset|`a -- `|Set selected interrupt addresses|
|`5C`|send|`v -- `|Send value to selected buses|
|`5D`|loopi|`n -- `|`ls <- n, dc0, 0`|
|`60` - `63`|rwrite#|`v a -- `|`mem[dc# + a] = v`|
|`64`|write|`v a -- `|`mem[a] = v`|
|`65`|jeq|`a b -- `|if `a == b` then `dc0 -> pc`|
|`66`|jne|`a b -- `|if `a != b` then `dc0 -> pc`|
|`67`|les|`a b -- `|if `a < b` then `dc0 -> pc`|
|`68`|leq|`a b -- `|if `a <= b` then `dc0 -> pc`|
|`69`|lesu|`a b -- `|if `a < b` then `dc0 -> pc`|
|`6A`|lequ|`a b -- `|if `a <= b` then `dc0 -> pc`|
|`6B`|out|`n a -- `|Stream n words to buses from a|
|`6C`|seb|`m s -- `|Clear ifile and set register `s` to `m`|
|`6D`|slb|`m s -- `|Ors `m` with register `s` of ifile|
|`6E`|mul|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`6F`|mulu|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`70`|div|`a b -- `|`cv <- a / b, a % b`|
|`71`|divu|`a b -- `|`cv <- a / b, a % b`|
|`72`|loop|`n e -- `|`ls <- n, e, 0`|
|`73`|setp|`priv addr -- `|Sets UARC permission delegation|
|`80` - `9F`|rot#|`v (# + 1).. -- (# + 1).. v`| |
|`A0` - `BF`|copy#|`v (# + 1).. -- v (# + 1).. v`| |
|`C0` - `DF`|tread#|` -- ts[#]`|Address 0 is the top of ts|
|`E0` - `FF`|twrite#|`v -- `|`ts[#] = v`|
