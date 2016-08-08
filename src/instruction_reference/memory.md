# Memory Instructions

## `move#`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by an octet. The immediate value is signed and biased by `+1`.

#### Description
`move#` advances `dc#` by `imm` bytes, where `imm` can be negative, thus it can go backwards.

----------

## `raread#`
`a -- mem[dc# + a]`

#### Description
`raread#` performs a random read starting from `dc#` and indexing by `a`. The result is returned synchronously to the stack because it is assumed that this value is held in the cache, since memory at a DC is generally cached.

----------

## `rereadi#`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by an octet. The immediate value is unsigned.

#### Description
`rereadi#` performs a random read starting from `mem[dc#]` and indexing by `imm`. The result is placed on the conveyor because only the memory at the DC itself is cached.

#### Side Effects
- Word is asynchronously put on the conveyor.

----------

## `read#`
` -- mem[dc#]`

#### Immediate (WORD)
The initial opcode byte is followed by an octet. The immediate value is signed and is biased by `+1`.

#### Description
`read#` performs a read directly at `dc#`. `dc#` is then advanced by the signed immediate value `imm`.

----------

## `rareadi#`
` -- mem[dc# + imm]`

#### Immediate (WORD)
The initial opcode byte is followed by an octet. The immediate value is unsigned.

#### Description
`raread#` performs a random read starting from `dc#` and indexing by `imm`. The result is returned synchronously to the stack because it is assumed that this value is held in the cache, since memory at a DC is generally cached.

----------

## `get#`
` -- dc#`

#### Description
`get#` places the address `dc#` onto the stack. This allows the programmer to manipulate the DC for dynamically allocating memory on a stack or performing pointer arithmetic for optimization problems.

----------

## `writepre#`
`v -- `

#### Immediate (WORD)
The initial opcode byte is followed by an octet. The immediate value is signed and is not biased.

#### Description
`writepre#` performs a write directly at `dc#`. Doing this advances `dc#` by the signed immediate value `imm` before the write. This allows the DC to be set up as a stack.

----------

## `writepst#`
`v -- `

#### Immediate (WORD)
The initial opcode byte is followed by an octet. The immediate value is signed and is biased by `+1`.

#### Description
`writepst#` performs a write directly at `dc#`. Doing this advances `dc#` by the signed immediate value `imm` after the write.

----------

## `set#`
`a -- `

#### Description
`set#` sets `dc#` to `a`.

----------

## `rawritei#`
`v -- `

#### Immediate (WORD)
The initial opcode byte is followed by an octet. The immediate value is unsigned.

#### Description
`rawritei#` performs a random write relative to `dc#`. This means that `mem[dc# + imm] = v`. This may be cached.

#### Side Effects
- `mem[dc# + imm] = v`

----------

## `rewritei#`
`v -- `

#### Immediate (WORD)
The initial opcode byte is followed by an octet. The immediate value is unsigned.

#### Description
`rewritei#` performs a random write relative to `mem[dc#]`. This means that `mem[mem[dc#] + imm] = v`. This may be cached.

#### Side Effects
- `mem[mem[dc#] + imm] = v`

----------

## `reread#`
`a -- `

#### Immediate (WORD)
The initial opcode byte is followed by an octet. The immediate value is signed and biased by `+1`.

#### Description
`reread#` performs a random read relative to `mem[dc#]` or `a` depending on perspective. `dc#` is advanced by `imm`. The result is placed on the conveyor because only the memory at the DC itself is cached.

#### Side Effects
- `mem[mem[dc#] + a]` is asynchronously put on the conveyor.

----------

## `reada`
`a -- `

#### Description
`reada` performs an asynchronous read. This means that the value read will not necessarily complete before the next instruction and the result is not put on the stack. Instead, the value is promised in a slot on the [conveyor](architecture/conveyor.html). When this slot is read from, the read is synchronized and the result is placed on the stack.

#### Side Effects
- `cv <- mem[a]`

#### Examples
```
0 reada
# Do other stuff here
cv0
```
- `( -- mem[0])`
- Between `reada` and `cv0`, any operations happen while the read is occurring.

----------

## `rewrite#`
`v a -- `

#### Description
`rewrite#` performs a random write relative to `a` or `dc#` depending on perspective. This means that `mem[mem[dc#] + a] = v`. This may be cached.

#### Side Effects
- `mem[mem[dc#] + a] = v`

----------

## `rawrite#`
`v a -- `

#### Description
`rawrite#` performs a random write relative to `dc#`. This means that `mem[dc# + a] = v`. This may be cached.

#### Side Effects
- `mem[dc# + a] = v`

----------

## `write`
`v a -- `

#### Description
`write` performs a random write. This means that `mem[a] = v`. This may be cached.

#### Side Effects
- `mem[a] = v`

----------

## `writep`
`v a -- `

#### Description
`writep` performs a random write to program memory in little-endian byte-order. This means that `progmem[a] = v`. Program memory is ordered into octets, but this instruction can write several octets in one word, therefore the word byte-order is little-endian. The addresses are aligned to octets. This operation may not influence the instruction executed next.

#### Side Effects
- `progmem[a] = v`

----------

## `writepo`
`ins a -- `

#### Description
`writepo` performs a random write of an octet to program memory using a program address. This means that `progmem[a] = ins`, but only the lowest 8 bits of `ins` are written. Program memory is ordered into octets, which are written and addressed individually by this word. The addresses are aligned to octets. This operation may not influence the instruction executed next.

#### Side Effects
- `progmem[a] = ins`

----------

## `writepi`
`v -- `

#### Immediate (WORD)
The initial opcode byte is followed by a word. The immediate value is a program address.

#### Description
`writepi` performs a random write to program memory in little-endian byte-order at the address specified by the immediate value `imm`. This means that `progmem[imm] = v`. Program memory is ordered into octets, but this instruction can write several octets in one word, therefore the word byte-order is little-endian. The addresses are aligned to octets. This operation may not influence the instruction executed next.

#### Side Effects
- `progmem[imm] = v`

----------

## `writepri`
`v -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is a signed relative offset.

#### Description
`writepri` performs a random write to program memory in little-endian byte-order at the address `pc + imm`. This means that `progmem[pc + imm] = v`. Program memory is ordered into octets, but this instruction can write several octets in one word, therefore the word byte-order is little-endian. The addresses are aligned to octets. This operation may not influence the instruction executed next.

#### Side Effects
- `progmem[pc + imm] = v`
