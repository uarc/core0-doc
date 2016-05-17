# Instruction Reference

### c0 Instruction Formats

- `OOOO OOOO` (All instructions)
  - `0SSO OOOO` (Normal instructions)
    - All normal instructions affect the entire stack according to the `S` bits
    - `0010 LLLL` (Conveyor instruction)
      - Reads and synchronizes with conveyor index `location`
    - `0011 10LL` (Loop index)
      - Places the index from the loop of depth `location` on the [dstack](architecture/dstack.html)
    - `0SSO OOLL` (DC instructions)
      - Operates using specifically the DC of index `location`
  - `1OLL LLLL` (Random access instructions)
    - `10LL LLLL` (Rotate)
      - [dstack](architecture/dstack.html) item of depth `location + 1` rotated to top of [dstack](architecture/dstack.html)
      - Only elements above the rotate are pushed down
    - `11LL LLLL` (Copy)
      - [dstack](architecture/dstack.html) item of depth `location + 1` copied to top of [dstack](architecture/dstack.html)
      - Entire stack is pushed down

- `O` - Opcode bit
- `S` - Stack bit
- `L` - Location bit

### Stack bits
- `00` - The stack is not popped or pushed.
- `01` - The stack is pushed once.
- `10` - The stack is popped once.
- `11` - The stack is popped twice.

## Key
- `WORD` - Data word width in use
- `c` - Carry bit
- `cv` - [Conveyor Belt](architecture/conveyor.html)
- `dc[0-3]` - Data Counters
- `pc` - Program Counter
- `iflag` - Interrupt Flag
- `i[0-3]` - Loop Indices
- `cv[0-F]` - [Conveyor Belt](architecture/conveyor.html) Values
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
|`12`|recv|` -- `|Interrupt sync; `cv <- bus, v`|
|`13`|in|`a -- b`|Stream in to `a`|
|`14`|kill|` -- `|Kill all selected cores|
|`15`|wait|` -- `|Waits for an interrupt before continuing|
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
|`5A`|iset|`a -- `|Set selected interrupt addresses|
|`5B`|seb|`b -- `|Set a single UARC bus|
|`5C`|slb|`b -- `|Select an additional UARC bus|
|`5D`|usb|`b -- `|Unselect a UARC bus|
|`5E`|send|`v -- `|Send value to selected buses|
|`5F`|loopi|`n -- `|`ls <- n, dc0, 0`|
|`60` - `63`|rwrite#|`v a -- `|`mem[dc# + a] = v`|
|`64`|write|`v a -- `|`mem[a] = v`|
|`65`|jeq|`a b -- `|if `a == b` then `dc0 -> pc`|
|`66`|jne|`a b -- `|if `a != b` then `dc0 -> pc`|
|`67`|les|`a b -- `|if `a < b` then `dc0 -> pc`|
|`68`|leq|`a b -- `|if `a <= b` then `dc0 -> pc`|
|`69`|lesu|`a b -- `|if `a < b` then `dc0 -> pc`|
|`6A`|lequ|`a b -- `|if `a <= b` then `dc0 -> pc`|
|`6B`|out|`n a -- `|Stream n words to buses from a|
|`6C`|incept|`n a -- `|Incept target cores; same as out|
|`6D`|set|`m s -- `|Clear ifile and set register `s` to `m`|
|`6E`|sel|`m s -- `|Ors `m` with register `s` of ifile|
|`6F`|setp|`priv addr -- `|Sets UARC permission delegation|
|`70`|mul|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`71`|mulu|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`72`|div|`a b -- `|`cv <- a / b, a % b`|
|`73`|divu|`a b -- `|`cv <- a / b, a % b`|
|`74`|loop|`n e -- `|`ls <- n, e, 0`|
|`75`|sef|`a f -- `|Sets fault `f` handler to `a`|
|`80` - `BF`|rot#|`v (# + 1).. -- (# + 1).. v`| |
|`C0` - `FF`|copy#|`v (# + 1).. -- v (# + 1).. v`| |
