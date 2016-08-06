# Arithmetic Instructions

## `inc`
`a -- (a + 1)`

#### Description
`inc` increments the top of the [dstack](architecture/dstack.html) by 1. The overflow and carry bits are set, but not consumed.

#### Side Effects
- `c = (a + 1 == 0)`
- `o = (a + 1 == 1 << (WORD - 1))`

#### Examples
```
h7FFFFFFF inc
```
- `( -- h80000000)`
- `o` is set to 1 because a positive number became negative by wrapping
- `c` is set to 0

----------

## `dec`
`a -- (a + 1)`

#### Description
`dec` decrements the top of the [dstack](architecture/dstack.html) by 1. The overflow and carry bits are set, but not consumed.

#### Side Effects
- `c = (a - 1 != ~0)`
- `o = (a == 1 << (WORD - 1))`

#### Examples
```
0 dec
```
- `( -- -1)`
- `o` is set to 0 because no overflow occurred
- `c` is set to 1 because a borrow was incurred

----------

## `carry`
`a -- (a + c)`

#### Description
`carry` is used when the carry bit needs to be accumulated to a more significant word after a less significant operation. It affects the `o` and `c` similarly to `inc`.

#### Side Effects
- `c = c == 1 && (a + c == 0)`
- `o = c == 1 && (a == -1)`

#### Examples
```
~0 inc
0 carry
```
- `( -- 0 1)`
- `c` is set to 0
- `o` is set to 0

----------

## `borrow`
`a -- (a + c - 1)`

#### Description
`borrow` is used when a borrow must be applied to a more significant word after a less significant operation. It affects the `o` and `c` bits similarly to `dec`.

#### Side Effects
- `c = c == 1 || (a - 1 != ~0)`
- `o = c == 1 || (a == 1 << (WORD - 1))`

#### Examples
```
0 dec
1 borrow
```
- `( -- 0 0)`
- `c` is set to 0
- `o` is set to 0

----------

## `add`
`a b -- (a + b)`

#### Description
`add` takes parameters `a` and `b` from the stack and adds them. The carry bit is set by this instruction, but is not consumed.

#### Side Effects
- `c = (a + b)[WORD]`
- `o = (a[WORD - 1] ^ b[WORD - 1]) ? 0 : a[WORD - 1] ^ (a + b)[WORD - 1]`

#### Examples
```
2 3 add
```
- `( -- 5)`
- Carry is set to 0

----------

## `sub`
`a b -- (a - b)`

####  Description
`sub` subtracts parameter `b` from `a`. The carry bit is set by this instruction to `1` when no borrow occurs. The carry bit is not consumed by this instruction.

#### Side Effects
`c = (a - b)[WORD]`

#### Examples
```
2 3 sub
```
- `( -- -1)`
- Carry is set to 0
  - Which indicates a borrow

----------

## `asr`
`a b -- (a >>> b)`

#### Description
`asr` shifts `a` by `b` bits and sign extends the result. If `-1` is shifted right, it will continue to stay `-1`.

#### Examples (with WORD of 32)
```
h80000000 1 asr
```
- `( -- hC000000)`

----------

## `addi`
`a -- (a + imm)`

#### Immediate (WORD)
As an immediate instruction, the initial opcode byte is followed by a full processor word. This word is signed.

#### Description
`addi` takes parameter `a` from the stack and adds it with the immediate value `imm`. The carry bit is set by this instruction, but is not consumed.

#### Side Effects
- `c = (a + imm)[WORD]`
- `o = (a[WORD - 1] ^ imm[WORD - 1]) ? 0 : a[WORD - 1] ^ (a + imm)[WORD - 1]`

#### Examples
```
2 add:3
```
- `( -- 5)`
- Carry is set to 0

----------

## `subi`
`a -- (imm - a)`

#### Immediate (WORD)
As an immediate instruction, the initial opcode byte is followed by a full processor word. This word is signed.

####  Description
`subi` subtracts the immediate value `imm` from `a`. This instruction can simultaneously be called 'immediate' and 'inverse'. It would be redundant to have an actual immediate subtract, since an immediate add of the negative number accomplishes the same thing, but there are situations where a number needs to be subtracted from an immediate value. This instruction can be used to negate a number in one instruction. The carry bit is set by this instruction to `1` when no borrow occurs. The carry bit is not consumed by this instruction.

#### Side Effects
`c = (imm - a)[WORD]`

#### Examples
```
3 subi:2
```
- `( -- -1)`
- Carry is set to 0
  - Which indicates a borrow

  ----------

## `asri`
`a -- (a >>> imm)`

#### Immediate (WORD)
As an immediate instruction, the initial opcode byte is followed by one octet. The octet is unsigned and represents the shift amount.

#### Description
`asri` shifts `a` by `imm` bits and sign extends the result. If `-1` is shifted right, it will continue to stay `-1`.

#### Examples (with WORD of 32)
```
h80000000 asri:1
```
- `( -- hC000000)`
