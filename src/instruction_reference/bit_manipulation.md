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
`a b -- ((a << b) or (a >> (b - WORD)))`

#### Description
`csl` performs a circular shift left. Only the amount of bits needed are used for the shift amount. For example, with a 32-bit `WORD`, only the least significant `5` bits are masked out of `b`.

#### Examples (with WORD of 32)
```
hC0000000 1 csl
```
- `( -- h80000001)`

----------

## `csr`
`a b -- ((a >> b) or (a << (b - WORD)))`

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
