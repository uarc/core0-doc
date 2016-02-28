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

## `dec`
`a -- (a + 1)`

#### Description
`dec` decrements the top of the [dstack](architecture/dstack.html) by 1. The overflow and carry bits are set, but not consumed.

#### Side Effects
- `c = (a - 1 == ~0)`
- `o = (a + 1 == 1 << (WORD - 1))`

#### Examples
```
0 dec
```
- `( -- -1)`
- `o` is set to 0 because no overflow occurred
- `c` is set to 1 because a borrow was incurred

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

## `addc`
`a b -- (a + b + c)`

#### Description
`addc` takes parameters `a` and `b` from the stack and adds them with `c`. Unlike `add`, `addc` consumes the carry, so ensure it is correctly set before issuing this instruction.

#### Side Effects
`c = (a + b)[WORD]`

#### Examples (with WORD of 32)
```
h80000000 h80000000 add
0 0 addc
```
- `( -- 0 1)`
- Carry is set to 0

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

## `subc`
`a b -- (a - b + c)`

#### Description
`subc` subtracts parameter `b` from `a` and adds `c`. The carry bit is set by this instruction to `1` when no borrow occurs.

#### Side Effects
`c = (a - b)[WORD]`

#### Examples
```
2 3 sub
1 0 subc
```
- `( -- -1 0)`
- Carry is set to 1 (indicating no borrow)

## `asr`
`a b -- (a >>> b)`

#### Description
`asr` shifts `a` by `b` bits and sign extends the result. If `-1` is shifted right, it will continue to stay `-1`, but `o` will not be set. The `o` bit is set if any 1 bits are lost in a positive number or 0 bits lost in a negative number.

#### Side Effects
- `o` is 1 if any significant bits were lost

#### Examples (with WORD of 32)
```
h80000000 1 asr
```
- `( -- hC000000)`
- `o` is set to `1` because `0` is a significant bit and is lost
