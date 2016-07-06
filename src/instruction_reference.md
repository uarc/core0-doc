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
|`04` - `07`|add#|`a -- (a + mem[dc#])`|dc# advances; `c`, `o`|
|`08`|inc|`a -- (a + 1)`|`c`, `o`|
|`09`|dec|`a -- (a - 1)`|`c`, `o`|
|`0A`|carry|`v -- (v + c)`|`c`, `o`|
|`0B`|borrow|`v -- (v + c - 1)`|`c`, `o`|
|`0C`|inv|`v -- ~v`| |
|`0D`|break|` -- `|Pops the lstack and goes to end of loop|
|`0E`|reads|`a -- mem[a]`|Synchronous read|
|`0F`|ret|` -- `|Pops [cstack](architecture/cstack.html)|
|`10`|continue|` -- `|Goes to the next loop iteration|
|`11`|ien|` -- `|Enables only selected interrupts|
|`12`|recv|` -- `|Interrupt sync; `cv <- bus, v`|
|`13`|ld0i|` -- `|`dc0 = mem[dc0]`; preserve dc0; `dc0` forwards|
|`14`|kill|` -- `|Kill all selected cores|
|`15`|wait|` -- `|Waits for an interrupt before continuing|
|`16`|getbp|`b -- perm`|Gets the [permission](uarc.md) on bus `b`|
|`17`|getba|`b -- addr`|Gets the [address](uarc.md) on bus `b`|
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
|`3D`|readi|` -- mem[mem[dc0]]`| |
|`3E`|getp|` -- perm`|Get UARC permission|
|`3F`|geta|` -- addr`|Get UARC address|
|`40` - `43`|write#|`v -- `|`mem[dc#] = v`; dc# advances|
|`44` - `47`|setf#|`a -- `|`dc# = a`; dc# is write post-inc|
|`48` - `4B`|setb#|`a -- `|`dc# = a`; dc# is write pre-dec|
|`4C`|add|`a b -- (a + b)`|`c`, `o`|
|`4D`|sub|`a b -- (a - b)`|`c`, `o`|
|`4E`|lsl|`a b -- (a << b)`||
|`4F`|lsr|`a b -- (a >> b)`||
|`50`|csl|`a b -- ((a << b) or (a >> (b - WORD)))`| |
|`51`|csr|`a b -- ((a >> b) or (a << (b - WORD)))`| |
|`52`|asr|`a b -- (a >>> b)`||
|`53`|and|`a b -- (a & b)`| |
|`54`|or|`a b -- (a or b)`| |
|`55`|xor|`a b -- (a ^ b)`| |
|`56`|reada|`a -- `|`cv <- mem[a]`|
|`57`|call|`a -- `|`pc = a`; push [cstack](architecture/cstack.html)|
|`58`|jmp|`a -- `|`pc = a`|
|`59`|iset|`d -- `|Set selected interrupt addresses and dc0s|
|`5A`|seb|`b -- `|Set a single bus|
|`5B`|slb|`b -- `|Select an additional UARC bus|
|`5C`|usb|`b -- `|Unselect a UARC bus|
|`5D`|send|`v -- `|Send value to selected buses|
|`5E`|writei|`v -- `|`mem[mem[dc0]] = v`|
|`5F`|loopi|`n -- `|`ls <- n, dc0, 0`|
|`60` - `63`|rawrite#|`v a -- `|`mem[dc# + a] = v`|
|`64` - `67`|rewrite#|`v a -- `|`mem[mem[dc#] + a] = v`|
|`68`|write|`v a -- `|`mem[a] = v`|
|`69`|writep|`v a -- `|`progmem[a] = v`|
|`6A`|writepi|`ins a -- `|`progmem[a] = ins`|
|`6B`|jeq|`a b -- `|if `a == b` then `dc0 -> pc`|
|`6C`|jne|`a b -- `|if `a != b` then `dc0 -> pc`|
|`6D`|les|`a b -- `|if `a < b` then `dc0 -> pc`|
|`6E`|leq|`a b -- `|if `a <= b` then `dc0 -> pc`|
|`6F`|lesu|`a b -- `|if `a < b` then `dc0 -> pc`|
|`70`|lequ|`a b -- `|if `a <= b` then `dc0 -> pc`|
|`71`|in|`n a -- `|Stream in to `a`; `cv <- bus`|
|`72`|out|`n a -- `|Stream n words to buses from a|
|`73`|incept|`n a -- `|Incept target cores; same as out|
|`74`|set|`m s -- `|Clear ifile and set register `s` to `m`|
|`75`|sel|`m s -- `|Ors `m` with register `s` of ifile|
|`76`|seta|`perm addr -- `|Sets UARC permission and address delegation|
|`77`|loop|`n e -- `|`ls <- n, e, 0`|
|`78`|sef|`a f -- `|Sets fault `f` handler to `a`|
|`79`|mul|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`7A`|mulu|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`7B`|div|`a b -- `|`cv <- a / b, a % b`|
|`7C`|divu|`a b -- `|`cv <- a / b, a % b`|
|`7F`|reset|`d p -- `|`pc = p, dc0 = d`|
|`80` - `BF`|rot#|`v #.. -- #.. v`| |
|`C0` - `FF`|copy#|`v #.. -- v #.. v`| |
