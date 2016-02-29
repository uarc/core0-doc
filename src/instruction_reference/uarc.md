# UARC Instructions

## `ien`
` -- `

#### Description
`ien` enables interrupts in the [ifile](architecture/ifile.html) for buses which are presently selected.

#### Side Effects
- All selected buses now have interrupts enabled.

----------

## `idi`
` -- `
#### Description
`idi` disables all interrupts currently enabled.

#### Side Effects
- All buses now have interrupts disabled.

----------

## `recv`
` -- `

#### Description
`recv` accepts any interrupt synchronously without an interrupt handler from any selected bus. The bus ID and the interrupt value are added to the [conveyor](architecture/conveyor.html) in that order.

#### Side Effects
- `cv <- bus, v`
- Synchronizes execution with the next interrupt

----------

## `in`
`a -- b`

#### Description
`in` receives a stream from any selected UARC bus. `a` is the address at which those words are to be streamed to. `b` is the bus ID of the particular bus that streamed these values.

#### Side Effects
- Execution is synchronized with the completion of the stream operation

----------

## `seb`
`b -- `

#### Description
`seb` clears all buses from being selected and then sets a specific set of UARC buses to be selected. The upper `WORD/2` bits of `b` select randomly from a register file of select registers and the lower `WORD/2` bits are a mask that represent which bits to specifically assign to that select register.

#### Side Effects
- Specific UARC buses are now selected.

----------

## `slb`
`b -- `

#### Description
`seb` selects a specific set of UARC buses. The upper `WORD/2` bits of `b` select randomly from a register file of select registers and the lower `WORD/2` bits are a mask that represent which bits to specifically OR to that select register.

#### Side Effects
- Specific UARC buses are now added to the selection.

----------

## `iset`
`a -- `

#### Description
`iset` sets the interrupt address of all selected UARC buses to `a`.

#### Side Effects
- Any further interrupts on the selected UARC buses now call the routine at `a`.

----------

## `send`
`v -- `

#### Description
`send` sends a value to all selected UARC buses, and waits for all buses to accept the interrupt before continuing.

#### Side Effects
- Execution is synchronized with the acceptance of all interrupts sent.

----------

## `out`
`n a -- `

#### Description
`out` sends a stream to all selected UARC buses. `n` is the number of words able to be streamed out and `a` is the address at which those words are to be streamed from.

#### Side Effects
- Execution is synchronized with the completion of the stream operation
