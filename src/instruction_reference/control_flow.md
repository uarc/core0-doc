# Control Flow

## `ret`
` -- `

#### Description
`ret` returns from a subroutine call. It pops the [cstack](architecture/cstack.html), moving the PC, DCs, and [tstack](architecture/tstack.html) back to the position it was at in the caller to continue execution. This instruction returns from interrupts as well as normal subroutines, since an interrupt is a simulated subroutine call. Using `ret` in an interrupt also allows more interrupts to be serviced unless they are explicitly disabled in the interrupt.

#### Side Effects
- [cstack](architecture/cstack.html) is popped
  - DCs are returned to their previous values
  - PC is returned to its previous value
  - [tstack](architecture/tstack.html) is popped until it is at its previous position
  - If this is the initial subroutine of an interrupt
    - It is finished servicing
    - The interrupt [conveyor](architecture/conveyor.html) is replaced with the normal [conveyor](architecture/conveyor.html)

#### Notes
- The [lstack](architecture/lstack.html) is NOT returned to its previous state.

#### Examples
```
subr calli
ret

subr:
  ret
```
- `( -- )`
- The example calls `subr` before returning to the original caller

```
subr jumpi

subr:
  ret
```
- `( -- )`
- A more efficient version of the previous example by utilizing tail-call elimination

----------

## `calli`
` -- `

#### Description
`calli` is an immediate call. In this situation, immediate means that the PC to jump to is taken from `dc0`. This also increments `dc0` and puts the incremented copy of `dc0` onto the [cstack](architecture/cstack.html), along with the other state elements preserved in a call.

#### Side Effects
- [cstack](architecture/cstack.html) is pushed
  - DCs are preserved
  - PC is preserved
  - [tstack](architecture/tstack.html) position is preserved
  - Whether an interrupt is being serviced or not is preserved

#### Examples
```
subr calli
ret

subr:
  ret
```
- `( -- )`
- The example calls `subr` before returning to the original caller

----------

## `jmpi`
` -- `

#### Description
`jmpi` jumps to an immediate value stored at `dc0`. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value
- `dc0` is incremented

----------

## `jc`
` -- `

#### Description
`jc` jumps to an immediate value stored at `dc0` if the `c` bit is `1`. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value if `c` is `1`
- `dc0` is incremented always

----------

## `jnc`
` -- `

#### Description
`jnc` jumps to an immediate value stored at `dc0` if the `c` bit is `0`. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value if `c` is `0`
- `dc0` is incremented always

----------

## `jo`
` -- `

#### Description
`jo` jumps to an immediate value stored at `dc0` if the `o` bit is `1`. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value if `o` is `1`
- `dc0` is incremented always

----------

## `jno`
` -- `

#### Description
`jno` jumps to an immediate value stored at `dc0` if the `o` bit is `0`. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value if `o` is `0`
- `dc0` is incremented always

----------

## `call`
`a -- `

#### Description
`call` calls a subroutine. The address `a` is the destination the PC is set to. This pushes the [cstack](architecture/cstack.html), storing all the state elements preserved in a call.

#### Side Effects
- [cstack](architecture/cstack.html) is pushed
  - DCs are preserved
  - PC is preserved
  - [tstack](architecture/tstack.html) position is preserved
  - Whether an interrupt is being serviced or not is preserved

#### Examples
```
subr read0 call
ret

subr:
  ret
```
- `( -- )`
- The example calls `subr` before returning to the original caller

----------

## `jmp`
`a -- `

#### Description
`jmp` jumps to the address `a`.

#### Side Effects
- PC is moved to `a`

----------

## `jeq`
`a b -- `

#### Description
`jeq` jumps to an immediate value stored at `dc0` if `a` and `b` are equal. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value if `a == b`
- `dc0` is incremented always

----------

## `jne`
`a b -- `

#### Description
`jne` jumps to an immediate value stored at `dc0` if `a` and `b` are not equal. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value if `a != b`
- `dc0` is incremented always

----------

## `les`
`a b -- `

#### Description
`les` jumps to an immediate value stored at `dc0` if `a` is less than `b` in a signed comparison. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value if `a < b`
- `dc0` is incremented always

----------

## `leq`
`a b -- `

#### Description
`leq` jumps to an immediate value stored at `dc0` if `a` is less than or equal to `b` in a signed comparison. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value if `a <= b`
- `dc0` is incremented always

----------

## `lesu`
`a b -- `

#### Description
`lesu` jumps to an immediate value stored at `dc0` if `a` is less than `b` in an unsigned comparison. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value if `a < b`
- `dc0` is incremented always

----------

## `lequ`
`a b -- `

#### Description
`lequ` jumps to an immediate value stored at `dc0` if `a` is less than or equal to `b` in an unsigned comparison. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value if `a <= b`
- `dc0` is incremented always

----------

## `lequ`
`a b -- `

#### Description
`lequ` jumps to an immediate value stored at `dc0` if `a` is less than or equal to `b` in an unsigned comparison. This also increments `dc0` in the process.

#### Side Effects
- PC is moved to immediate value if `a <= b`
- `dc0` is incremented always

----------

## `loop`
`n e -- `

#### Description
`loop` pushes a new loop into onto the [lstack](architecture/lstack.html). `n` represents the number of iterations of the loop, while `e` represents the end address of the loop. The loop index (`i0`) begins at `0`.

#### Side Effects
- [lstack](architecture/lstack.html) is pushed

----------

## `loopi`
`n -- `

#### Description
`loopi` pushes a new loop into onto the [lstack](architecture/lstack.html). `n` represents the number of iterations of the loop. The end address is taken from `dc0` and `dc0` is incremented. The loop index (`i0`) begins at `0`.

#### Side Effects
- [lstack](architecture/lstack.html) is pushed
- `dc0` is incremented
