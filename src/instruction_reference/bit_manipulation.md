# Bit Manipulation

## `inv`
`v -- ~v`

#### Description
`inv` inverts the bits of v.

#### Examples
```
~0 inv
```
- `( -- 0)`

----------

## `lsl`
`a b -- (a << b)`

#### Description
`lsl` performs a logical shift left. This operation zero-fills.

#### Examples (with WORD of 32)
```
hFFFFFFFF 1 lsl
```
- `( -- hFFFFFFFE)`

----------

## `lsr`
`a b -- (a >> b)`

#### Description
`lsr` performs a logical shift right. This operation zero-fills.

#### Examples
```
3 1 lsr
```
- `( -- 1)`

----------

## `csl`
`a b -- ((a << b) or (a >> (WORD - b)))`

#### Description
`csl` performs a circular shift left. Only the amount of bits needed are used for the shift amount. For example, with a 32-bit `WORD`, only the least significant `5` bits are masked out of `b`.

#### Examples (with WORD of 32)
```
hC0000000 1 csl
```
- `( -- h80000001)`

----------

## `csr`
`a b -- ((a >> b) or (a << (WORD - b)))`

#### Description
`csr` performs a circular shift right. Only the amount of bits needed are used for the shift amount. For example, with a 32-bit `WORD`, only the least significant `5` bits are masked out of `b`.

#### Examples (with WORD of 32)
```
3 1 csr
```
- `( -- h80000001)`

----------

## `and`
`a b -- (a & b)`

#### Description
`and` finds the binary AND of `a` and `b`.

#### Examples
```
5 4 and
```
- `( -- 4)`

----------

## `or`
`a b -- (a or b)`

#### Description
`or` finds the binary OR of `a` and `b`.

#### Examples
```
5 2 or
```
- `( -- 7)`

----------

## `xor`
`a b -- (a ^ b)`

#### Description
`xor` finds the binary XOR of `a` and `b`.

#### Examples
```
5 3 xor
```
- `( -- 6)`

----------

## `lsli`
`a -- (a << imm)`

#### Immediate (WORD)
The initial opcode byte is followed by one octet. The octet is signed and represents the shift amount. Since the octet is signed, this makes it possible to perform a right or left shift with this opcode.

#### Description
`lsli` performs a logical shift left with a signed immediate shift amount. This operation zero-fills.

#### Examples (with WORD of 32)
```
hFFFFFFFF lsli:1
```
- `( -- hFFFFFFFE)`

----------

## `csli`
`a -- ((a << imm) or (a >> (WORD - imm)))`

#### Immediate (WORD)
The initial opcode byte is followed by one octet. The octet is unsigned and represents the shift amount. A circular right shift can be accomplished by setting the immediate value to `-n` where `n` is the right shift amount.

#### Description
`csli` performs a circular shift left. Only the amount of bits needed are used for the shift amount. For example, with a 32-bit `WORD`, only the least significant `5` bits are masked out of `b`.

#### Examples (with WORD of 32)
```
hC0000000 csli:1
```
- `( -- h80000001)`

----------

## `andi`
`a -- (a & imm)`

#### Immediate (WORD)
The initial opcode byte is followed by a full processor word. This word is unsigned.

#### Description
`andi` finds the binary AND of `a` and `imm`.

#### Examples
```
5 andi:4
```
- `( -- 4)`

----------

## `ori`
`a -- (a or imm)`

#### Immediate (WORD)
The initial opcode byte is followed by a full processor word. This word is unsigned.

#### Description
`ori` finds the binary OR of `a` and `imm`.

#### Examples
```
5 ori:2
```
- `( -- 7)`

----------

## `xori`
`a -- (a ^ imm)`

#### Immediate (WORD)
The initial opcode byte is followed by a full processor word. This word is unsigned.

#### Description
`xori` finds the binary XOR of `a` and `imm`.

#### Examples
```
5 xori:3
```
- `( -- 6)`
