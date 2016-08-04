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
|`00` - `03`|raread#|`a -- mem[dc# + a]`| |
|`04` - `07`|addi#|`a -- (a + mem[dc#])`|dc# advances; `c`, `o`|
|`08`|inc|`a -- (a + 1)`|`c`, `o`|
|`09`|dec|`a -- (a - 1)`|`c`, `o`|
|`0A`|carry|`v -- (v + c)`|`c`, `o`|
|`0B`|borrow|`v -- (v + c - 1)`|`c`, `o`|
|`0C`|inv|`v -- ~v`| |
|`0D`|break|` -- `|Pops the lstack and goes to end of loop|
|`0E`|reads|`a -- mem[a]`|Synchronous read|
|`0F`|iloopi|` -- `|dc0 = mem[dc0]; push [lstack](architecture/lstack.html)|
|`10`|continue|` -- `|Goes to the next loop iteration|
|`11`|inten|` -- `|Enables only selected interrupts|
|`12`|intrecv|` -- `|Interrupt sync; `cv <- bus, v`|
|`13`|set0i|` -- `|`dc0 = mem[dc0]`; preserve dc0; `dc0` forwards|
|`14`|kill|` -- `|Kill all selected cores|
|`15`|intwait|` -- `|Waits for an interrupt before continuing|
|`16`|getbp|`b -- perm`|Gets the [permission](uarc.md) on bus `b`|
|`17`|getba|`b -- addr`|Gets the [address](uarc.md) on bus `b`|
|`18`|calli|` -- `|`dc0 -> pc`; push [lstack](architecture/lstack.html)|
|`19`|jmpi|` -- `|`dc0 -> pc`|
|`1A`|bci|` -- `|if `c` then `dc0 -> pc`|
|`1B`|bnci|` -- `|if `~c` then `dc0 -> pc`|
|`1C`|boi|` -- `|if `o` then `dc0 -> pc`|
|`1D`|bnoi|` -- `|if `~o` then `dc0 -> pc`|
|`1E`|binti|` -- `|if `iflag` then `dc0 -> pc`|
|`1F`|bninti|` -- `|if `~iflag` then `dc0 -> pc`|
|`20` - `2F`|cv#|` -- cv#`|cv# synchronizes|
|`30` - `33`|read#|` -- mem[dc#]`|dc# advances|
|`34` - `37`|get#|` -- dc#`| |
|`38` - `3B`|i#|` -- i#`| |
|`3C`|p0|` -- 0`| |
|`3D`|ireadi|` -- mem[mem[dc0]]`| |
|`3E`|getp|` -- perm`|Get UARC permission|
|`3F`|geta|` -- addr`|Get UARC address|
|`40` - `43`|write#|`v -- `|`mem[dc#] = v`; dc# advances|
|`44` - `47`|setf#|`a -- `|`dc# = a`; dc# is write post-inc|
|`48` - `4B`|setb#|`a -- `|`dc# = a`; dc# is write pre-dec|
|`4C`|add|`a b -- (a + b)`|`c`, `o`|
|`4D`|sub|`a b -- (a - b)`|`c`, `o`|
|`4E`|lsl|`a b -- (a << b)`| |
|`4F`|lsr|`a b -- (a >> b)`| |
|`50`|csl|`a b -- ((a << b) or (a >> (b - WORD)))`| |
|`51`|csr|`a b -- ((a >> b) or (a << (b - WORD)))`| |
|`52`|asr|`a b -- (a >>> b)`||
|`53`|and|`a b -- (a & b)`| |
|`54`|or|`a b -- (a or b)`| |
|`55`|xor|`a b -- (a ^ b)`| |
|`56`|reada|`a -- `|`cv <- mem[a]`|
|`57`|call|`a -- `|`pc = a`; push [lstack](architecture/lstack.html)|
|`58`|jmp|`a -- `|`pc = a`|
|`59`|iseti|`d -- `|Set selected interrupt addresses and dc0s|
|`5A`|seb|`b -- `|Set a single bus|
|`5B`|slb|`b -- `|Select an additional UARC bus|
|`5C`|usb|`b -- `|Unselect a UARC bus|
|`5D`|intsend|`v -- `|Send value to selected buses|
|`5E`|iwritei|`v -- `|`mem[mem[dc0]] = v`|
|`5F`|RESRRVED|` -- `| |
|`60` - `63`|rawrite#|`v a -- `|`mem[dc# + a] = v`|
|`64` - `67`|rewrite#|`v a -- `|`mem[mem[dc#] + a] = v`|
|`68`|write|`v a -- `|`mem[a] = v`|
|`69`|writep|`v a -- `|`progmem[a] = v`|
|`6A`|writepi|`ins a -- `|`progmem[a] = ins`|
|`6B`|beqi|`a b -- `|if `a == b` then `dc0 -> pc`|
|`6C`|bnei|`a b -- `|if `a != b` then `dc0 -> pc`|
|`6D`|blesi|`a b -- `|if `a < b` then `dc0 -> pc`|
|`6E`|bleqi|`a b -- `|if `a <= b` then `dc0 -> pc`|
|`6F`|blesui|`a b -- `|if `a < b` then `dc0 -> pc`|
|`70`|blequi|`a b -- `|if `a <= b` then `dc0 -> pc`|
|`71`|recv|`n a -- `|Stream in to `a`; `cv <- bus`|
|`72`|send|`n a -- `|Stream n words to buses from a|
|`73`|incept|`n a -- `|Incept target cores; see send|
|`74`|set|`m s -- `|Clear ifile and set register `s` to `m`|
|`75`|sel|`m s -- `|Ors `m` with register `s` of ifile|
|`76`|setpa|`perm addr -- `|Sets UARC permission and address delegation|
|`77`|loopi|`n d -- `|`ls <- n, dc0, 0`; `dc0 = d`; end from DC0|
|`78`|sef|`a f -- `|Sets fault `f` handler to `a`|
|`79`|mul|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`7A`|mulu|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`7B`|div|`a b -- `|`cv <- a / b, a % b`|
|`7C`|divu|`a b -- `|`cv <- a / b, a % b`|
|`7D`|RESERVED|`_ _ -- `| |
|`7E`|RESERVED|`_ _ -- `| |
|`7F`|reset|`d p -- `|`pc = p, dc0 = d`|
|`80`|RESERVED|` -- `| |
|`81`|RESERVED|` -- `| |
|`82`|RESERVED|` -- `| |
|`83`|RESERVED|` -- `| |
|`84`|RESERVED|` -- `| |
|`85`|RESERVED|` -- `| |
|`86`|RESERVED|` -- `| |
|`87`|RESERVED|` -- `| |
|`88`|RESERVED|` -- `| |
|`89`|RESERVED|` -- `| |
|`8A`|RESERVED|` -- `| |
|`8B`|RESERVED|` -- `| |
|`8C`|RESERVED|` -- `| |
|`8D`|RESERVED|` -- `| |
|`8E`|RESERVED|` -- `| |
|`8F`|RESERVED|` -- `| |
|`90`|RESERVED|` -- _`| |
|`91`|RESERVED|` -- _`| |
|`92`|RESERVED|` -- _`| |
|`93`|RESERVED|` -- _`| |
|`94`|RESERVED|` -- _`| |
|`95`|RESERVED|` -- _`| |
|`96`|RESERVED|` -- _`| |
|`97`|RESERVED|` -- _`| |
|`98`|RESERVED|` -- _`| |
|`99`|RESERVED|` -- _`| |
|`9A`|RESERVED|` -- _`| |
|`9B`|RESERVED|` -- _`| |
|`9C`|RESERVED|` -- _`| |
|`9D`|RESERVED|` -- _`| |
|`9E`|RESERVED|` -- _`| |
|`9F`|RESERVED|` -- _`| |
|`A0`|RESERVED|`_ -- `| |
|`A1`|RESERVED|`_ -- `| |
|`A2`|RESERVED|`_ -- `| |
|`A3`|RESERVED|`_ -- `| |
|`A4`|RESERVED|`_ -- `| |
|`A5`|RESERVED|`_ -- `| |
|`A6`|RESERVED|`_ -- `| |
|`A7`|RESERVED|`_ -- `| |
|`A8`|RESERVED|`_ -- `| |
|`A9`|RESERVED|`_ -- `| |
|`AA`|RESERVED|`_ -- `| |
|`AB`|RESERVED|`_ -- `| |
|`AC`|RESERVED|`_ -- `| |
|`AD`|RESERVED|`_ -- `| |
|`AE`|RESERVED|`_ -- `| |
|`AF`|RESERVED|`_ -- `| |
|`B0`|RESERVED|`_ _ -- `| |
|`B1`|RESERVED|`_ _ -- `| |
|`B2`|RESERVED|`_ _ -- `| |
|`B3`|RESERVED|`_ _ -- `| |
|`B4`|RESERVED|`_ _ -- `| |
|`B5`|RESERVED|`_ _ -- `| |
|`B6`|RESERVED|`_ _ -- `| |
|`B7`|RESERVED|`_ _ -- `| |
|`B8`|RESERVED|`_ _ -- `| |
|`B9`|RESERVED|`_ _ -- `| |
|`BA`|RESERVED|`_ _ -- `| |
|`BB`|RESERVED|`_ _ -- `| |
|`BC`|RESERVED|`_ _ -- `| |
|`BD`|RESERVED|`_ _ -- `| |
|`BE`|RESERVED|`_ _ -- `| |
|`BF`|RESERVED|`_ _ -- `| |
|`C0` - `DF`|rot#|`v #.. -- #.. v`| |
|`E0` - `FF`|copy#|`v #.. -- v #.. v`| |
