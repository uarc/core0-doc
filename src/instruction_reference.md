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
  - Format is (beginning)
- `if` - [ifile](architecture/ifile.html)
- `n..` - `n` anonymous words on the stack
- `imm` - Immediate value

## Instruction Listing By Opcode

|Op|Instruction|Octets|dstack|Side Effects|
|:---:|:---:|:---:|:---:|:---:|
|`00` - `03`|raread#|1|`a -- mem[dc# + a]`| |
|`04`|inc|1|`a -- (a + 1)`|`c`, `o`|
|`05`|dec|1|`a -- (a - 1)`|`c`, `o`|
|`06`|carry|1|`v -- (v + c)`|`c`, `o`|
|`07`|borrow|1|`v -- (v + c - 1)`|`c`, `o`|
|`08`|inv|1|`v -- ~v`| |
|`09`|break|1|` -- `|Pops the cstack and goes to end of loop|
|`0A`|reads|1|`a -- mem[a]`|Synchronous read|
|`0B`|return|1|` -- `|pop [cstack](architecture/cstack.html)|
|`0C`|continue|1|` -- `|Goes to the next loop iteration|
|`0D`|inten|1|` -- `|Enables only selected interrupts|
|`0E`|intrecv|1|` -- `|Interrupt sync; `cv <- bus, v`|
|`0F`|iloop|3|` -- `|`ls <- pc + 1, pc + imm, ∞, 0`|
|`10`|kill|1|` -- `|Kill all selected cores|
|`11`|intwait|1|` -- `|Waits for an interrupt before continuing|
|`12`|getbp|1|`b -- perm`|Gets the [permission](uarc.md) on bus `b`|
|`13`|getba|1|`b -- addr`|Gets the [address](uarc.md) on bus `b`|
|`14`|calli|5|` -- `|`pc = imm`; push [cstack](architecture/cstack.html)|
|`15`|jmpi|5|` -- `|`pc = imm`|
|`16`|bc|3|` -- `|if `c` then `pc += imm`|
|`17`|bnc|3|` -- `|if `~c` then `pc += imm`|
|`18`|bo|3|` -- `|if `o` then `pc += imm`|
|`19`|bno|3|` -- `|if `~o` then `pc += imm`|
|`1A`|bi|3|` -- `|if `i` then `pc += imm`; `i = 0`|
|`1B`|bni|3|` -- `|if `~i` then `pc += imm`; `i = 0`|
|`1C`|RESERVED|1|` -- `| |
|`1D`|RESERVED|1|` -- `| |
|`1E`|RESERVED|1|` -- `| |
|`1F`|RESERVED|1|` -- `| |
|`20` - `2F`|cv#|1|` -- cv#`|cv# synchronizes|
|`30` - `33`|read#|2|` -- mem[dc#]`|after: `dc# += imm`|
|`34` - `37`|rareadi#|2|` -- mem[dc# + imm]`| |
|`38` - `3B`|get#|1|` -- dc#`| |
|`3C` - `3F`|i#|1|` -- i#`| |
|`40` - `43`|writepre#|2|`v -- `|`dc# += imm`; `mem[dc#] = v`|
|`43` - `47`|writepst#|2|`v -- `|`mem[dc#] = v`; `dc# += imm`|
|`48` - `4B`|set#|1|`a -- `|`dc# = a`|
|`4C` - `4F`|rawrite#|2|`v -- `|`mem[dc# + imm] = v`|
|`4C`|add|1|`a b -- (a + b)`|`c`, `o`|
|`4D`|sub|1|`a b -- (a - b)`|`c`, `o`|
|`4E`|lsl|1|`a b -- (a << b)`| |
|`4F`|lsr|1|`a b -- (a >> b)`| |
|`50`|csl|1|`a b -- ((a << b) or (a >> (b - WORD)))`| |
|`51`|csr|1|`a b -- ((a >> b) or (a << (b - WORD)))`| |
|`52`|asr|1|`a b -- (a >>> b)`||
|`53`|and|1|`a b -- (a & b)`| |
|`54`|or|1|`a b -- (a or b)`| |
|`55`|xor|1|`a b -- (a ^ b)`| |
|`56`|reada|1|`a -- `|`cv <- mem[a]`|
|`57`|call|1|`a -- `|`pc = a`; push [cstack](architecture/cstack.html)|
|`58`|jmp|1|`a -- `|`pc = a`|
|`59`|iseti|1|`d -- `|Set selected interrupt addresses and dc0s|
|`5A`|seb|1|`b -- `|Set a single bus|
|`5B`|slb|1|`b -- `|Select an additional UARC bus|
|`5C`|usb|1|`b -- `|Unselect a UARC bus|
|`5D`|intsend|1|`v -- `|Send value to selected buses|
|`5E`|iwritei|1|`v -- `|`mem[mem[dc0]] = v`|
|`5F`|loop|3|`n -- `|`ls <- pc + 1, pc + imm, n, 0`|
|`64` - `67`|rewrite#|1|`v a -- `|`mem[mem[dc#] + a] = v`|
|`68`|write|1|`v a -- `|`mem[a] = v`|
|`69`|writep|1|`v a -- `|`progmem[a] = v`|
|`6A`|writepi|1|`ins a -- `|`progmem[a] = ins`|
|`6B`|beq|3|`a b -- `|if `a == b` then `pc += imm`|
|`6C`|bne|3|`a b -- `|if `a != b` then `pc += imm`|
|`6D`|bles|3|`a b -- `|if `a < b` then `pc += imm`|
|`6E`|bleq|3|`a b -- `|if `a <= b` then `pc += imm`|
|`6F`|blesu|3|`a b -- `|if `a < b` then `pc += imm`|
|`70`|blequ|3|`a b -- `|if `a <= b` then `pc += imm`|
|`71`|recv|1|`n a -- `|Stream in to `a`; `cv <- bus`|
|`72`|send|1|`n a -- `|Stream n words to buses from a|
|`73`|incept|1|`n a -- `|Incept target cores; see send|
|`74`|set|1|`m s -- `|Clear ifile and set register `s` to `m`|
|`75`|sel|1|`m s -- `|Ors `m` with register `s` of ifile|
|`76`|setpa|1|`perm addr -- `|Sets UARC permission and address delegation|
|`77`|RESERVED|1|`_ -- `| |
|`79`|mul|1|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`7A`|mulu|1|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`7B`|div|1|`a b -- `|`cv <- a / b, a % b`|
|`7C`|divu|1|`a b -- `|`cv <- a / b, a % b`|
|`7D`|RESERVED|1|`_ _ -- `| |
|`7E`|RESERVED|1|`_ _ -- `| |
|`7F`|reset|1|`d p -- `|`pc = p, dc0 = d`|
|`80`|addi|5|`a b -- (a + b)`|`c`, `o`|
|`81`|subi|5|`a b -- (a - b)`|`c`, `o`|
|`82`|lsli|2|`a b -- (a << imm)`| |
|`83`|lsri|2|`a b -- (a >> imm)`| |
|`84`|csli|2|`a b -- ((a << imm) or (a >> (imm - WORD)))`| |
|`85`|csri|2|`a b -- ((a >> imm) or (a << (imm - WORD)))`| |
|`86`|asri|2|`a b -- (a >>> imm)`| |
|`87`|andi|5|`a b -- (a & b)`| |
|`88`|ori|5|`a b -- (a or b)`| |
|`89`|xori|5|`a b -- (a ^ b)`| |
|`8A`|RESERVED|1|` -- `| |
|`8B`|RESERVED|1|` -- `| |
|`8C`|RESERVED|1|` -- `| |
|`8D`|RESERVED|1|` -- `| |
|`8E`|RESERVED|1|` -- `| |
|`8F`|RESERVED|1|` -- `| |
|`90`|imm8|2|` -- imm`| |
|`91`|imm16|3|` -- imm`| |
|`92`|imm32|5|` -- imm`| |
|`93`|ireadi|1|` -- mem[mem[dc0]]`| |
|`94`|getp|1|` -- perm`|Get UARC permission|
|`95`|geta|1|` -- addr`|Get UARC address|
|`96`|RESERVED|1|` -- _`| |
|`97`|RESERVED|1|` -- _`| |
|`98`|RESERVED|1|` -- _`| |
|`99`|RESERVED|1|` -- _`| |
|`9A`|RESERVED|1|` -- _`| |
|`9B`|RESERVED|1|` -- _`| |
|`9C`|RESERVED|1|` -- _`| |
|`9D`|RESERVED|1|` -- _`| |
|`9E`|RESERVED|1|` -- _`| |
|`9F`|RESERVED|1|` -- _`| |
|`A0`|bzi|3|`n -- `|if `n == 0` then `pc += imm`|
|`A1`|bnzi|3|`n -- `|if `n != 0` then `pc += imm`|
|`A2`|sef|2|`a -- `|Sets fault `imm` handler to `a`|
|`A3`|RESERVED|1|`_ -- `| |
|`A4`|RESERVED|1|`_ -- `| |
|`A5`|RESERVED|1|`_ -- `| |
|`A6`|RESERVED|1|`_ -- `| |
|`A7`|RESERVED|1|`_ -- `| |
|`A8`|RESERVED|1|`_ -- `| |
|`A9`|RESERVED|1|`_ -- `| |
|`AA`|RESERVED|1|`_ -- `| |
|`AB`|RESERVED|1|`_ -- `| |
|`AC`|RESERVED|1|`_ -- `| |
|`AD`|RESERVED|1|`_ -- `| |
|`AE`|RESERVED|1|`_ -- `| |
|`AF`|RESERVED|1|`_ -- `| |
|`B0`|RESERVED|1|`_ _ -- `| |
|`B1`|RESERVED|1|`_ _ -- `| |
|`B2`|RESERVED|1|`_ _ -- `| |
|`B3`|RESERVED|1|`_ _ -- `| |
|`B4`|RESERVED|1|`_ _ -- `| |
|`B5`|RESERVED|1|`_ _ -- `| |
|`B6`|RESERVED|1|`_ _ -- `| |
|`B7`|RESERVED|1|`_ _ -- `| |
|`B8`|RESERVED|1|`_ _ -- `| |
|`B9`|RESERVED|1|`_ _ -- `| |
|`BA`|RESERVED|1|`_ _ -- `| |
|`BB`|RESERVED|1|`_ _ -- `| |
|`BC`|RESERVED|1|`_ _ -- `| |
|`BD`|RESERVED|1|`_ _ -- `| |
|`BE`|RESERVED|1|`_ _ -- `| |
|`BF`|RESERVED|1|`_ _ -- `| |
|`C0` - `DF`|rot#|`v #.. -- #.. v`| |
|`E0` - `FF`|copy#|`v #.. -- v #.. v`| |
