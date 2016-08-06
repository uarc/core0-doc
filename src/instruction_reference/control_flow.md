# Control Flow

## `break`
` -- `

#### Description
`break` immediately pops the [lstack](architecture/lstack.html) and moves the PC to after the loop.

#### Side Effects
- [lstack](architecture/lstack.html) is popped unconditionally

----------

## `return`
` -- `

#### Description
`return` returns from a subroutine call. It pops the [cstack](architecture/cstack.html), moving the PC and DCs back to the position it was at in the caller to continue execution. This instruction returns from interrupts as well as normal subroutines, since an interrupt is a simulated subroutine call. Using `return` in an interrupt also allows more interrupts to be serviced unless they are explicitly disabled in the interrupt.

#### Side Effects
- [cstack](architecture/cstack.html) is popped
  - PC is returned to its previous value
  - If this is the initial subroutine of an interrupt
    - It is finished servicing
    - The interrupt [conveyor](architecture/conveyor.html) is replaced with the normal [conveyor](architecture/conveyor.html)
    - The `i` bit is set

#### Notes
- The [lstack](architecture/lstack.html) is NOT returned to its previous state.

#### Examples
```
calli:subr
return

:subr
  return
```
- `( -- )`
- The example calls `subr` before returning to the original caller

```
jumpi:subr

subr:
  return
```
- `( -- )`
- A more efficient version of the previous example by utilizing tail-call elimination

----------

## `continue`
` -- `

#### Description
`continue` immediately goes to the next loop iteration and moves the PC to the beginning of the loop. If this is the last iteration it goes to the end of the loop.

#### Side Effects
- Moves PC to beginning of loop normally or after the loop if this is the last iteration.

----------

## `iloop`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is unsigned and is added to the PC to find the relative position of the end of the loop.

#### Description
`iloop` pushes a new infinite loop into onto the [lstack](architecture/lstack.html). This loop, being infinite, will never end until the break instruction is issued. The loop index (`i0`) begins at `0` and will wrap around indefinitely.

#### Side Effects
- [lstack](architecture/lstack.html) is pushed.

#### Examples
```
iloop:+
rot:0
:+
```
- Enters an infinite loop with a NOP.

----------

## `calli`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by a processor word. This word is the address of the function that is being called.

#### Description
`calli` is an immediate call. The immediate value is the address to call into.

#### Side Effects
- [cstack](architecture/cstack.html) is pushed
  - Old PC is preserved.
  - Whether an interrupt is being serviced or not is preserved.

#### Examples
```
calli:subr
return

subr:
  return
```
- `( -- )`
- The example calls `subr` before returning to the original caller

----------

## `jmpi`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by a processor word. This word is the address to execute next.

#### Description
`jmpi` jumps to the immediate address `imm`.

#### Side Effects
- PC is moved to immediate value.

----------

## `bra`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bra` performs a relative branch to address `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm`.

----------

## `call`
`a -- `

#### Description
`call` calls a subroutine. The address `a` is the destination the PC is set to. This pushes the [cstack](architecture/cstack.html), storing all the state elements preserved in a call.

#### Side Effects
- [cstack](architecture/cstack.html) is pushed
  - Old PC is preserved.
  - Indicates whether an interrupt is being serviced or not is preserved.

#### Examples
```
.subr call
return

subr:
  return
```
- `( -- )`
- The example calls `subr` before returning to the original caller.

----------

## `sef`
`a f -- `

#### Description
`sef` sets the fault handler for a particular fault.

#### Side Effects
- Future faults of type `f` will immediately call `a`

#### Fault types
|#|Name|Explanation|
|:---:|:---:|:---:|
|`00`|Data Stack Overflow|Triggers when an operation would otherwise have overflowed the [dstack](architecture/dstack.html)|
|`01`|Data Stack Underflow|Triggers when an operation would otherwise have underflowed the [dstack](architecture/dstack.html)|
|`02`|Signed Divide by Zero|Triggers when an operation reads a divide by zero from the [conveyor](architecture/conveyor.html)|
|`03`|Unsigned Divide by Zero|Triggers when an operation reads a divide by zero from the [conveyor](architecture/conveyor.html)|
|`04`|Segfault|Triggers when an operation attempts to read from an invalid memory address and places it on the stack|

----------

## `beq`
`a b -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`beq` performs a relative branch if `a == b`. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `a == b`.

----------

## `bne`
`a b -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bne` performs a relative branch if `a != b`. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `a != b`.

----------

## `bles`
`a b -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bles` performs a relative branch if `a < b` where both operands are signed. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `a < b`.

----------

## `bleq`
`a b -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bleq` performs a relative branch if `a <= b` where both operands are signed. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `a <= b`.

----------

## `blesu`
`a b -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`blesu` performs a relative branch if `a < b` where both operands are unsigned. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `a < b`.

----------

## `blequ`
`a b -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`blequ` performs a relative branch if `a <= b` where both operands are unsigned. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `a <= b`.

----------

## `bc`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bc` performs a relative branch if the `c` bit is `1`. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `c` is `1`.

----------

## `bnc`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bnc` performs a relative branch if the `c` bit is `0`. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `c` is `0`.

----------

## `bo`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bo` performs a relative branch if the `o` bit is `1`. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `o` is `1`.

----------

## `bno`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bno` performs a relative branch if the `o` bit is `0`. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `o` is `0`.

----------

## `bi`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bi` performs a relative branch if the `i` bit is `1`. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `i` is `1`.

----------

## `bni`
` -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bni` performs a relative branch if the `i` bit is `0`. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `i` is `0`.

----------

## `jmp`
`a -- `

#### Description
`jmp` jumps to an address. The address `a` is the destination the PC is set to.

#### Examples
```
.subr jmp

subr:
  return
```
- `( -- )`
- The example calls `subr` with a tail call optimization to return to the original caller.

----------

## `loop`
`n -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is unsigned and is the relative offset of the end of the loop.

#### Description
`loop` pushes a new loop into onto the [lstack](architecture/lstack.html). `n` represents the number of iterations of the loop, while the immediate value `imm` is the unsigned relative position of the end of the loop. The loop index (`i0`) begins at `0`.

#### Side Effects
- [lstack](architecture/lstack.html) is pushed.

----------

## `reset`
`p -- `

#### Description
Resets the whole processor, setting `p` as the new PC. Everything is reinitialized, such as stack depth.

----------

## `bz`
`a -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bz` performs a relative branch if `a == 0`. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `a == 0`.

----------

## `bnz`
`a -- `

#### Immediate (WORD)
The initial opcode byte is followed by two octets. The immediate value is signed and is the relative offset to branch by.

#### Description
`bnz` performs a relative branch if `a != 0`. The `pc` to branch to is `pc + imm`.

#### Side Effects
- PC is moved to `pc + imm` value if `a != 0`.
