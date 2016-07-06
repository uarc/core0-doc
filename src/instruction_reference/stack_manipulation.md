# Stack Manipulation Instructions

## `p0`
` -- 0`

#### Description
`p0` pushes a `0` onto the top of the stack. This doesn't theoretically need to exist, but it makes it practically easy for the processor to add a `0` to the stack on inception to receive the data stream from the inceptor. It also has other uses, such as initializing an accumulator value and comparisons with `0`.

----------

## `rot#`
`v #.. -- #.. v`

#### Description
`rot#` rotates a value `0` to `63` elements deep in the stack to the top of the stack. The value is removed from that location and elements under that value are not shifted in the stack. `rot0` has no effect and uses a cycle doing nothing.

#### Examples
```
1 2 3 rot2
```
- `( -- 2 3 1)`

----------

## `copy#`
`v #.. -- v #.. v`

#### Description
`copy#` copies a value `0` to `63` elements deep in the stack to the top of the stack. This pushes all elements down on the stack. `copy0` is a duplicate operation.

#### Examples
```
1 2 3 copy2
```
- `( -- 1 2 3 1)`
