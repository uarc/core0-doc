# Arithmetic Instructions

## `add#`
`a -- (a + dc#)`

#### Description
`add#` adds the top of the [dstack](architecture/dstack.html) with the immediate value from `dc#`. The `o` and `c` bits are set, but not consumed. `dc#` is incremented.

#### Side Effects
- `c = (a + dc#)[WORD]`
- `o = (a[WORD - 1] ^ dc#[WORD - 1]) ? 0 : a[WORD - 1] ^ (a + dc#)[WORD - 1]`

----------

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

## `subc`
`a b -- (a - b + c - 1)`

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

## `mul`
`a b -- (a * b)`

#### Description
`mul` multiplies two signed numbers (`a` and `b`) with width `WORD` and produces a result with width `2 * WORD`. The lower `WORD` and the higher `WORD` bits (both including a sign bit) are placed on the [conveyor](architecture/conveyor.html).

#### Side Effects
- The lowest `WORD` and highest `WORD` bits are placed onto the [conveyor](architecture/conveyor.html) in that order
- `o` is `1` if any significant bits are placed into the conveyor

#### Examples (with WORD of 32)
```
h80000000 2 mul cv1 cv0
```
- `( -- h80000000 hFFFFFFFE)`

----------

## `mulu`
`a b -- (a * b)`

#### Description
`mulu` multiplies two unsigned numbers (`a` and `b`) with width `WORD` and produces a result with width `2 * WORD`. The lower `WORD` and higher `WORD` bits of the result are placed on the [conveyor](architecture/conveyor.html).

#### Side Effects
- The lowest `WORD` and highest `WORD` bits are placed onto the [conveyor](architecture/conveyor.html) in that order

#### Examples
```
~0 2 mul cv1 cv0
```
- `( -- ~0 1)`

----------

## `div`
`a b -- `

#### Description
`div` divides two signed numbers (`a` and `b`) with width `WORD` and produces a result with width `2 * WORD`. The higher/whole `WORD` and the lower/fractional `WORD` bits of the result are placed on the [conveyor](architecture/conveyor.html). Both the higher and the lower results are signed.

#### Side Effects
- The higher/whole `WORD` and lower/fractional `WORD` bits are placed onto the [conveyor](architecture/conveyor.html) in that order

#### Examples (with WORD of 32)
```
h80000000 2 div cv1 cv0
```
- `( -- hC0000000 hBFFFFFFF)`

----------

## `divu`
`a b -- `

#### Description
`divu` divides two unsigned numbers (`a` and `b`) with width `WORD` and produces a result with width `2 * WORD`. The higher/whole `WORD` and the lower/fractional `WORD` bits of the result are placed on the [conveyor](architecture/conveyor.html).

#### Side Effects
- The higher/whole `WORD` and lower/fractional `WORD` bits are placed onto the [conveyor](architecture/conveyor.html) in that order

#### Examples (with WORD of 32)
```
~0 2 divu cv1 cv0
```
- `( -- h7FFFFFFF h80000000)`
