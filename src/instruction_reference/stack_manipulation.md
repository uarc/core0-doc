# Stack Manipulation Instructions

## `rot#`
`v #.. -- #.. v`

#### Description
`rot#` rotates a value `0` to `31` elements deep in the stack to the top of the stack. The value is removed from that location and elements under that value are not shifted in the stack. `rot0` has no effect and uses a cycle doing nothing.

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
