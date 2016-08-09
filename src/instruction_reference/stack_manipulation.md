# Stack Manipulation Instructions

## `i#`
` -- i#`

#### Description
`i#` places the index of a hardware controlled loop on the top of the stack. The inner-most loop index is `i0`.

#### Examples
```
3 loop:+
i0
:+
```
- `( -- 0 1 2)`

----------

## `ddrop`
`_ _ -- `

#### Description
`ddrop` drops two elements off the stack. Since the stack can be popped twice, this is a situational use of removing two values that reduces two drops into one.

#### Examples
```
8 3 2 ddrop
```
- `( -- 8)`

----------

## `imm8`
` -- imm`

#### Description
`imm8` places the immediate signed 8-bit value on the stack.

#### Examples
```
8
```
- `( -- 8)`

----------

## `imm16`
` -- imm`

#### Description
`imm16` places the immediate signed 16-bit value on the stack.

#### Examples
```
-30131
```
- `( -- -30131)`

----------

## `imm32`
` -- imm`

#### Description
`imm32` places the immediate signed 32-bit value on the stack.

#### Examples
```
1202303404
```
- `( -- 1202303404)`

----------

## `imm64`
` -- imm`

#### Description
`imm64` places the immediate signed 64-bit value on the stack. This is only supported on u0-64. On u0-32, this has undefined behavior.

#### Examples
```
1202303404505606
```
- `( -- 1202303404505606)`

----------

## `drop`
`_ -- `

#### Description
`drop` drops one element off the stack. This is useful after loops with accumulators where the accumulator is no longer required and in other niche situations where excess data is left on the stack.

#### Examples
```
8 3 2 drop
```
- `( -- 8 3)`

----------

## `rot#`
`v #.. -- #.. v`

#### Description
`rot#` rotates a value `0` to `31` elements deep in the stack to the top of the stack. The value is removed from that location and elements under that value are not shifted in the stack. `rot0` has no effect and uses a cycle doing nothing, but this is not the recommended method of performing a NOP, since significant power may be consumed internally due to the stack random access MUX.

#### Examples
```
1 2 3 rot2
```
- `( -- 2 3 1)`

----------

## `copy#`
`v #.. -- v #.. v`

#### Description
`copy#` copies a value `0` to `31` elements deep in the stack to the top of the stack. This pushes all elements down on the stack. `copy0` is a duplicate operation.

#### Examples
```
1 2 3 copy2
```
- `( -- 1 2 3 1)`
