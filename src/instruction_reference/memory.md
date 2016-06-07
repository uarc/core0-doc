# Memory Instructions

## `ldi0`
` -- `

#### Description
`ldi0` loads dc0 from the location pointed to by the current dc0. The old dc0 is incremented before being changed and the restored dc0 when returning from this call will be that value. This means that this instruction can be the first instruction of a function which needs a unique dc0.

----------

## `rread#`
`a -- mem[dc# + a]`

#### Description
`rread#` performs a random read starting from `dc#` and indexing by `a`. The result is returned synchronously to the stack because it is assumed that this value is held in the cache, since memory at a dc is generally cached.

----------

## `reads`
`a -- mem[a]`

#### Description
`reads` performs a synchronous read, meaning that this operation does not complete until the value is on the stack. `a` is an absolute address.

----------

## `read#`
` -- mem[dc#]`

#### Description
`read#` performs a read directly at `dc#`. Doing this increments `dc#` after the read.

----------

## `get#`
` -- dc#`

#### Description
`get#` places the address dc# is currently pointing to onto the stack. This allows the programmer to manipulate the dc for allocating memory on a stack or advancing the dc between elements in a vector for use with `rread#`.

----------

## `write#`
`v -- `

#### Description
`write#` performs a write directly at `dc#`. Doing this advances `dc#` depending on how it was set. If it was set with `setf`, then `dc#` will write and then increment. If it was set with `setb`, then `dc#` will decrement and then write. Due to this behavior, using setb allows a dc to be set up as a pushdown stack.

----------

## `setf#`
`a -- `

#### Description
`setf#` sets `dc#` to `a`. It also sets `dc#` to be in post-increment write mode.

----------

## `setb#`
`a -- `

#### Description
`setf#` sets `dc#` to `a`. It also sets `dc#` to be in pre-decrement write mode.

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

## `rwrite#`
`v a -- `

#### Description
`rwrite#` performs a random write relative to `dc#`. This means that `mem[dc# + a] = v`. This may be cached.

#### Side Effects
- `mem[dc# + a] = v`

----------

## `write`
`v a -- `

#### Description
`write` performs a random write. This means that `mem[a] = v`. This may be cached.

#### Side Effects
- `mem[a] = v`
