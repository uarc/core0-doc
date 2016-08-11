# Instruction Reference

### c0 Instruction Formats

- `OOOO OOOO` (All instructions)
  - `0SSO OOOO` / `10SS OOOO` (Normal instructions)
    - All normal instructions affect the entire stack according to the `S` bits
    - `0010 LLLL` (Conveyor instruction)
      - Reads and synchronizes with conveyor index `location`
    - `0011 10LL` (Loop index)
      - Places the index from the loop of depth `location` on the [dstack](architecture/dstack.html)
    - `0SSO OOLL` (DC instructions)
      - Operates using specifically the DC of index `location`
  - `11OL LLLL` (Random access instructions)
    - `110L LLLL` (Rotate)
      - [dstack](architecture/dstack.html) item of depth `location` rotated to top of [dstack](architecture/dstack.html)
      - Only elements above the rotate are pushed down
    - `111L LLLL` (Copy)
      - [dstack](architecture/dstack.html) item of depth `location` copied to top of [dstack](architecture/dstack.html)
      - Entire stack is pushed down

- `O` - Opcode bit
- `S` - Stack bit
- `L` - Location bit

### Stack bits
- `00` - The [dstack](architecture/dstack.html) is not popped or pushed.
- `01` - The [dstack](architecture/dstack.html) is pushed once.
- `10` - The [dstack](architecture/dstack.html) is popped once.
- `11` - The [dstack](architecture/dstack.html) is popped twice.

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
  - Format is (beginning, end, iterations, iterator)
- `if` - [ifile](architecture/ifile.html)
- `n..` - `n` anonymous words on the stack
- `imm` - Immediate value

## Instruction Listing By Opcode

|Op|Instruction|Octets|dstack|Side Effects|
|:---:|:---:|:---:|:---:|:---:|
|`00` - `03`|move#|2|` -- `|`dc# += imm`|
|`04` - `07`|raread#|1|`a -- mem[dc# + a]`| |
|`08` - `0B`|rereadi#|2|` -- `|`cv <- mem[mem[dc#] + imm]`|
|`0C`|inc|1|`a -- (a + 1)`|`c`, `o`|
|`0D`|dec|1|`a -- (a - 1)`|`c`, `o`|
|`0E`|carry|1|`v -- (v + c)`|`c`, `o`|
|`0F`|borrow|1|`v -- (v + c - 1)`|`c`, `o`|
|`10`|inv|1|`v -- ~v`| |
|`11`|break|1|` -- `|Pops the [lstack](architecture/lstack.html) and goes to end of loop|
|`12`|return|1|` -- `|pop [cstack](architecture/cstack.html)|
|`13`|continue|1|` -- `|Goes to the next loop iteration|
|`14`|inten|1|` -- `|Enables only selected interrupts|
|`15`|intrecv|1|` -- `|Interrupt sync; `cv <- bus, v`|
|`16`|iloop|3|` -- `|`ls <- pc + 1, pc + imm, ∞, 0`|
|`17`|kill|1|` -- `|Kill all selected cores|
|`18`|intwait|1|` -- `|Waits for an interrupt before continuing|
|`19`|getbp|1|`b -- perm`|Gets the [permission](uarc.md) on bus `b`|
|`1A`|getba|1|`b -- addr`|Gets the [address](uarc.md) on bus `b`|
|`1B`|calli|1 + WORD|` -- `|`pc = imm`; push [cstack](architecture/cstack.html)|
|`1C`|jmpi|1 + WORD|` -- `|`pc = imm`|
|`1D`|bra|3|` -- `|`pc += imm`|
|`1E`|RESERVED|1|` -- `| |
|`1F`|RESERVED|1|` -- `| |
|`20` - `2F`|cv#|1|` -- cv#`|cv# synchronizes|
|`30` - `33`|read#|2|` -- mem[dc#]`|`dc# += imm`|
|`34` - `37`|rareadi#|2|` -- mem[dc# + imm]`| |
|`38` - `3B`|get#|1|` -- dc#`| |
|`3C` - `3F`|i#|1|` -- i#`| |
|`40` - `43`|writepre#|2|`v -- `|`dc# += imm`; `mem[dc#] = v`|
|`44` - `47`|writepst#|2|`v -- `|`mem[dc#] = v`; `dc# += imm`|
|`48` - `4B`|set#|1|`a -- `|`dc# = a`|
|`4C` - `4F`|rawritei#|2|`v -- `|`mem[dc# + imm] = v`|
|`50` - `53`|rewritei#|2|`v -- `|`mem[mem[dc#] + imm] = v`|
|`54` - `57`|reread#|1|`a -- `|`cv <- mem[mem[dc#] + a]`|
|`58`|add|1|`a b -- (a + b)`|`c`, `o`|
|`59`|sub|1|`a b -- (a - b)`|`c`, `o`|
|`5A`|lsl|1|`a b -- (a << b)`| |
|`5B`|lsr|1|`a b -- (a >> b)`| |
|`5C`|csl|1|`a b -- ((a << b) or (a >> (b - WORD)))`| |
|`5D`|csr|1|`a b -- ((a >> b) or (a << (b - WORD)))`| |
|`5E`|asr|1|`a b -- (a >>> b)`| |
|`5F`|and|1|`a b -- (a & b)`| |
|`60` - `63`|rewrite#|1|`v a -- `|`mem[mem[dc#] + a] = v`|
|`64` - `67`|rawrite#|1|`v a -- `|`mem[dc# + a] = v`|
|`68`|write|1|`v a -- `|`mem[a] = v`|
|`69`|writep|1|`v a -- `|`progmem[a] = v`|
|`6A`|writepo|1|`ins a -- `|`progmem[a] = ins`|
|`6B`|beq|3|`a b -- `|if `a == b` then `pc += imm`|
|`6C`|bne|3|`a b -- `|if `a != b` then `pc += imm`|
|`6D`|bles|3|`a b -- `|if `a < b` then `pc += imm`|
|`6E`|bleq|3|`a b -- `|if `a <= b` then `pc += imm`|
|`6F`|blesu|3|`a b -- `|if `a < b` then `pc += imm`|
|`70`|blequ|3|`a b -- `|if `a <= b` then `pc += imm`|
|`71`|recv|1|`n a -- `|Stream in to `a`; `cv <- bus`|
|`72`|send|1|`n a -- `|Stream `n` words to buses from `a`|
|`73`|incept|1|`n a -- `|Incept target cores; see send|
|`74`|set|1|`m s -- `|Clear ifile and set register `s` to `m`|
|`75`|sel|1|`m s -- `|Ors `m` with register `s` of ifile|
|`76`|setpa|1|`perm addr -- `|Sets UARC permission and address delegation|
|`77`|expect|1|`v b -- `|Send `v` to bus `b`; `cv <- response`|
|`78`|sef|1|`a f -- `|Sets fault `f` handler to `a`|
|`79`|reset|1|`p _ -- `|`pc = p`|
|`7A`|ddrop|1|`_ _ -- `|Drops two elements from the stack|
|`7B`|RESERVED|1|`_ _ -- `| |
|`7C`|RESERVED|1|`_ _ -- `| |
|`7D`|RESERVED|1|`_ _ -- `| |
|`7E`|RESERVED|1|`_ _ -- `| |
|`7F`|RESERVED|1|`_ _ -- `| |
|`80`|addi|1 + WORD|`a -- (a + imm)`|`c`, `o`|
|`81`|addi8|2|`a -- (a + imm)`|`c`, `o`|
|`82`|addi16|3|`a -- (a + imm)`|`c`, `o`|
|`83`|subi|1 + WORD|`a -- (imm - a)`|`c`, `o`|
|`84`|lsli|2|`a -- (a << imm)`| |
|`85`|csli|2|`a -- ((a << imm) or (a >> (imm - WORD)))`| |
|`86`|asri|2|`a -- (a >>> imm)`| |
|`87`|andi|1 + WORD|`a -- (a & b)`| |
|`88`|ori|1 + WORD|`a -- (a or b)`| |
|`89`|xori|1 + WORD|`a -- (a ^ b)`| |
|`8A`|bc|3|` -- `|if `c` then `pc += imm`|
|`8B`|bnc|3|` -- `|if `~c` then `pc += imm`|
|`8C`|bo|3|` -- `|if `o` then `pc += imm`|
|`8D`|bno|3|` -- `|if `~o` then `pc += imm`|
|`8E`|bi|3|` -- `|if `i` then `pc += imm`; `i = 0`|
|`8F`|bni|3|` -- `|if `~i` then `pc += imm`; `i = 0`|
|`90` - `93`|index#|2|` -- (dc# + imm)`| |
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
|`A0`|or|1|`a b -- (a or b)`| |
|`A1`|xor|1|`a b -- (a ^ b)`| |
|`A2`|read|1|`a -- `|`cv <- mem[a]`|
|`A3`|call|1|`a -- `|`pc = a`; push [cstack](architecture/cstack.html)|
|`A4`|jmp|1|`a -- `|`pc = a`|
|`A5`|intset|1|`p -- `|Set selected interrupt addresses to `p`|
|`A6`|seb|1|`b -- `|Set a single bus|
|`A7`|slb|1|`b -- `|Select an additional UARC bus|
|`A8`|usb|1|`b -- `|Unselect a UARC bus|
|`A9`|intsend|1|`v -- `|Send value to selected buses|
|`AA`|loop|3|`n -- `|`ls <- pc + 1, pc + imm, n, 0`|
|`AB`|bz|3|`n -- `|if `n == 0` then `pc += imm`|
|`AC`|bnz|3|`n -- `|if `n != 0` then `pc += imm`|
|`AD`|writepi|1 + WORD|`v -- `|Writes `v` to instruction memory at `imm`|
|`AE`|writepri|3|`v -- `|Writes `v` to instruction memory at `pc + imm`|
|`AF`|drop|1|`_ -- `|Drops one element from the stack|
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
