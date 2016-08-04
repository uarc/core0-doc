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
|`00` - `03`|skip#|2|` -- `|`dc# += imm`|
|`04` - `07`|raread#|1|`a -- mem[dc# + a]`| |
|`08` - `0B`|reread#|1|`a -- mem[mem[dc#] + a]`| |
|`0C`|inc|1|`a -- (a + 1)`|`c`, `o`|
|`0D`|dec|1|`a -- (a - 1)`|`c`, `o`|
|`0E`|carry|1|`v -- (v + c)`|`c`, `o`|
|`0F`|borrow|1|`v -- (v + c - 1)`|`c`, `o`|
|`10`|inv|1|`v -- ~v`| |
|`11`|break|1|` -- `|Pops the [lstack](architecture/lstack.html) and goes to end of loop|
|`12`|reads|1|`a -- mem[a]`|Synchronous read|
|`13`|return|1|` -- `|pop [cstack](architecture/cstack.html)|
|`14`|continue|1|` -- `|Goes to the next loop iteration|
|`15`|inten|1|` -- `|Enables only selected interrupts|
|`16`|intrecv|1|` -- `|Interrupt sync; `cv <- bus, v`|
|`17`|iloop|3|` -- `|`ls <- pc + 1, pc + imm, ∞, 0`|
|`18`|kill|1|` -- `|Kill all selected cores|
|`19`|intwait|1|` -- `|Waits for an interrupt before continuing|
|`1A`|getbp|1|`b -- perm`|Gets the [permission](uarc.md) on bus `b`|
|`1B`|getba|1|`b -- addr`|Gets the [address](uarc.md) on bus `b`|
|`1C`|calli|1 + WORD|` -- `|`pc = imm`; push [cstack](architecture/cstack.html)|
|`1D`|jmpi|1 + WORD|` -- `|`pc = imm`|
|`1E`|bra|3|` -- `|`pc += imm`|
|`1F`|RESERVED|1|` -- `| |
|`20` - `2F`|cv#|1|` -- cv#`|cv# synchronizes|
|`30` - `33`|read#|2|` -- mem[dc#]`|`dc# += imm`|
|`34` - `37`|rareadi#|3|` -- mem[dc# + imm[0]]`|`dc# += imm[1]`|
|`38` - `3B`|rereadi#|3|` -- mem[mem[dc#] + imm[0]]`|`dc# += imm[1]`|
|`3C` - `3F`|get#|1|` -- dc#`| |
|`40` - `43`|writepre#|2|`v -- `|`dc# += imm`; `mem[dc#] = v`|
|`43` - `47`|writepst#|2|`v -- `|`mem[dc#] = v`; `dc# += imm`|
|`48` - `4B`|set#|1|`a -- `|`dc# = a`|
|`4C` - `4F`|rawrite#|3|`v -- `|`mem[dc# + imm[0]] = v`; `dc# += imm[1]`|
|`50` - `53`|rewritei#|3|`v -- `|`mem[mem[dc#] + imm[0]] = v`; `dc# += imm[1]`|
|`54`|add|1|`a b -- (a + b)`|`c`, `o`|
|`55`|sub|1|`a b -- (a - b)`|`c`, `o`|
|`56`|lsl|1|`a b -- (a << b)`| |
|`57`|lsr|1|`a b -- (a >> b)`| |
|`58`|csl|1|`a b -- ((a << b) or (a >> (b - WORD)))`| |
|`59`|csr|1|`a b -- ((a >> b) or (a << (b - WORD)))`| |
|`5A`|asr|1|`a b -- (a >>> b)`| |
|`5B`|and|1|`a b -- (a & b)`| |
|`5C`|or|1|`a b -- (a or b)`| |
|`5D`|xor|1|`a b -- (a ^ b)`| |
|`5E`|reada|1|`a -- `|`cv <- mem[a]`|
|`5F`|RESERVED|1|`_ -- `| |
|`60` - `63`|rewrite#|1|`v a -- `|`mem[mem[dc#] + a] = v`|
|`64`|write|1|`v a -- `|`mem[a] = v`|
|`65`|writep|1|`v a -- `|`progmem[a] = v`|
|`66`|writepi|1|`ins a -- `|`progmem[a] = ins`|
|`67`|beq|3|`a b -- `|if `a == b` then `pc += imm`|
|`68`|bne|3|`a b -- `|if `a != b` then `pc += imm`|
|`69`|bles|3|`a b -- `|if `a < b` then `pc += imm`|
|`6A`|bleq|3|`a b -- `|if `a <= b` then `pc += imm`|
|`6B`|blesu|3|`a b -- `|if `a < b` then `pc += imm`|
|`6C`|blequ|3|`a b -- `|if `a <= b` then `pc += imm`|
|`6D`|recv|1|`n a -- `|Stream in to `a`; `cv <- bus`|
|`6E`|send|1|`n a -- `|Stream n words to buses from a|
|`6F`|incept|1|`n a -- `|Incept target cores; see send|
|`70`|set|1|`m s -- `|Clear ifile and set register `s` to `m`|
|`71`|sel|1|`m s -- `|Ors `m` with register `s` of ifile|
|`72`|setpa|1|`perm addr -- `|Sets UARC permission and address delegation|
|`73`|ddrop|1|`_ _ -- `|Drops two elements from the stack|
|`74`|mul|1|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`75`|mulu|1|`a b -- `|`cv <- low(a * b), high(a * b)`|
|`76`|div|1|`a b -- `|`cv <- a / b, a % b`|
|`77`|divu|1|`a b -- `|`cv <- a / b, a % b`|
|`7D`|RESERVED|1|`_ _ -- `| |
|`7E`|RESERVED|1|`_ _ -- `| |
|`7F`|RESERVED|1|`_ _ -- `| |
|`80`|addi|1 + WORD|`a -- (a + imm)`|`c`, `o`|
|`81`|subi|1 + WORD|`a -- (imm - a)`|`c`, `o`|
|`82`|lsli|2|`a -- (a << imm)`| |
|`83`|lsri|2|`a -- (a >> imm)`| |
|`84`|csli|2|`a -- ((a << imm) or (a >> (imm - WORD)))`| |
|`85`|asri|2|`a -- (a >>> imm)`| |
|`86`|andi|1 + WORD|`a -- (a & b)`| |
|`87`|ori|1 + WORD|`a -- (a or b)`| |
|`88`|xori|1 + WORD|`a -- (a ^ b)`| |
|`89`|RESERVED|1|` -- `| |
|`8A`|bc|3|` -- `|if `c` then `pc += imm`|
|`8B`|bnc|3|` -- `|if `~c` then `pc += imm`|
|`8C`|bo|3|` -- `|if `o` then `pc += imm`|
|`8D`|bno|3|` -- `|if `~o` then `pc += imm`|
|`8E`|bi|3|` -- `|if `i` then `pc += imm`; `i = 0`|
|`8F`|bni|3|` -- `|if `~i` then `pc += imm`; `i = 0`|
|`90` - `93`|i#|1|` -- i#`| |
|`94`|imm8|2|` -- imm`| |
|`95`|imm16|3|` -- imm`| |
|`96`|imm32|5|` -- imm`| |
|`97`|imm64|9|` -- imm`|Only supported on u0-64|
|`98`|getp|1|` -- perm`|Get UARC permission|
|`99`|geta|1|` -- addr`|Get UARC address|
|`9A`|RESERVED|1|` -- _`| |
|`9B`|RESERVED|1|` -- _`| |
|`9C`|RESERVED|1|` -- _`| |
|`9D`|RESERVED|1|` -- _`| |
|`9E`|RESERVED|1|` -- _`| |
|`9F`|RESERVED|1|` -- _`| |
|`A0`|call|1|`a -- `|`pc = a`; push [cstack](architecture/cstack.html)|
|`A1`|jmp|1|`a -- `|`pc = a`|
|`A2`|iset|1|`p -- `|Set selected interrupt addresses to `p`|
|`A3`|seb|1|`b -- `|Set a single bus|
|`A4`|slb|1|`b -- `|Select an additional UARC bus|
|`A5`|usb|1|`b -- `|Unselect a UARC bus|
|`A6`|intsend|1|`v -- `|Send value to selected buses|
|`A7`|loop|3|`n -- `|`ls <- pc + 1, pc + imm, n, 0`|
|`A8`|bzi|3|`n -- `|if `n == 0` then `pc += imm`|
|`A9`|bnzi|3|`n -- `|if `n != 0` then `pc += imm`|
|`AA`|sef|2|`a -- `|Sets fault `imm` handler to `a`|
|`AB`|reset|1|`p -- `|`pc = p`|
|`AC`|drop|1|`_ -- `|Drops one element from the stack|
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
