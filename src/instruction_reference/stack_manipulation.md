# Stack Manipulation Instructions

## `tcopy`
`v -- v`

#### Description
`tcopy` pushes a copy of `v` onto the [tstack](architecture/tstack.html).

#### Side Effects
- `v` is pushed onto the `tstack`, moving everything down relative to the top

#### Examples
```
1 tcopy tread0
```
- `( -- 1)`

----------

## `p0`
` -- 0`

#### Description
`p0` pushes a `0` onto the top of the stack. This doesn't theoretically need to exist, but it makes it practically easy for the processor to add a `0` to the stack on inception to receive the data stream from the inceptor. It also has other uses, such as initializing an accumulator value and comparisons with `0`.

----------

## `dup`
`v -- v v`

#### Description
`dup` fills a void where `copy#` cannot, which is copying the top of the stack. Since `copy#` and `rot#` address starting from the element directly under the top of stack, this instruction is dedicated to allow copying the top of the stack.

----------

## `tpush`
`a -- `

#### Description
`tpush` removes `a` from the stack and places it on the [tstack](architecture/tstack.html). This should be used when a loop is about to execute and requires a temporary value to be accessed repeatedly, but the value is not otherwise consumed from the stack.

----------

## `rot#`
`v (# + 1).. -- (# + 1).. v`

#### Description
`rot#` rotates a value `1` to `32` elements deep in the stack to the top of the stack. The value is removed from that location and elements under that value are not shifted in the stack.

#### Examples
```
1 2 3 rot1
```
- `( -- 2 3 1)`

----------

## `copy#`
`v (# + 1).. -- v (# + 1).. v`

#### Description
`copy#` copies a value `1` to `32` elements deep in the stack to the top of the stack. This pushes all elements down on the stack.

#### Examples
```
1 2 3 copy1
```
- `( -- 1 2 3 1)`
