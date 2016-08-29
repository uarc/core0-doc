# UARC Instructions

## `intien`
` -- `

#### Description
`intien` enables interrupts in the [ifile](architecture/ifile.html) only for buses which are presently selected.

#### Side Effects
- All selected buses now have interrupts enabled.
- All non-selected buses now have interrupts disabled.

----------

## `intrecv`
` -- `

#### Description
`intrecv` accepts any interrupt synchronously without an interrupt handler from any selected bus. The bus ID (`bus`) and the interrupt value (`v`) are added to the [conveyor](architecture/conveyor.html) in that order.

#### Side Effects
- `cv <- bus, v`
- Synchronizes execution with the next interrupt from a selected bus
- Until this receives an interrupt, unselected buses cannot interrupt at all

----------

## `kill`
` -- `

#### Description
`kill` causes every single core connected to every selected bus and every single one of their children to stop before the instruction completes. This instruction takes `2 * n` cycles, where `n` is the depth of the longest chain of incepted cores. This will not succeed unless the core has [permission](uarc.html) over the target cores. If the target does not have the proper [permission](uarc.html), it will report success without doing anything.

#### Side Effects
- All selected cores for which this core has permission will be stopped before this instruction finishes.

----------

## `intwait`
` -- `

#### Description
`intwait` allows the code to synchronously handle interrupts by waiting the core until the next interrupt. If an interrupt had already been received, the `i` will already be `1`, thus this will immediately continue.

#### Side Effects
- Synchronizes with the first received interrupt or continues if `i` is already set.
- `i` is cleared by this instruction to indicate that the interrupt is known to be handled.

----------

## `getbp`
`b -- perm`

#### Description
`getbp` places the [permission](uarc.html) of bus `b` onto the stack.

----------

## `getba`
`b -- addr`

#### Description
`getba` places the [address](uarc.html) of bus `b` onto the stack. If the `AND` of `addr` with this core's [permission](uarc.html) is equal to this core's [address](uarc.html) with the [permission](uarc.html) then this core has [privilege](uarc.html) over the core on this bus.

----------

## `recv`
`n a -- `

#### Description
`recv` receives a stream from any selected UARC bus. `a` is the address at which up to `n` words are to be streamed to. The bus ID of the core that streamed to this core is placed on the conveyor.

#### Side Effects
- Execution is synchronized with the completion of the stream operation.
- `cv <- bus`

----------

## `send`
`n a -- `

#### Description
`send` sends a stream to all selected UARC buses. `n` is the number of words able to be streamed out and `a` is the address at which those words are to be streamed from.

#### Side Effects
- Execution is synchronized with the completion of the stream operation.

----------

## `incept`
`n a -- `

#### Description
`incept` sends a stream to all selected UARC buses. `n` is the number of words able to be streamed out and `a` is the address at which those words are to be streamed from. This will only work on a core which is not presently running or the core has **permission** over the core and the stream will become the instructions to be executed by the target core. By incepting cores again, the system can maintain **permission** over cores allocated on a chip for a process and prevent those cores from incepting cores they aren't supposed to. If a chip is to be given up by the system to a process, the system can incept all cores with the **permission** of the process and run an idle routine.

#### Side Effects
- Execution is synchronized with the completion of the stream operation.

----------

## `set`
`m s -- `

#### Description
`set` clears all buses from being selected and then sets a specific set of UARC buses to be selected. `m` is a mask for which cores to enable using an `OR` operation and `s` is a selector to choose which register to apply the mask to.

#### Side Effects
- Specific UARC buses are now selected and all others are unselected.

----------

## `sel`
`m s -- `

#### Description
`sel` selects a specific set of UARC buses. `m` is a mask for which cores to enable using an `OR` operation and `s` is a selector to choose which register to apply the mask to.

#### Side Effects
- Specific UARC buses are now added to the selection.

----------

## `setpa`
`perm addr -- `

#### Description
`setpa` sets the [permission](uarc.html) that is to be delegated to all incepted UARC cores. This value defaults to the same [permission](uarc.html) and [address](uarc.html) that this core receives on inception. This instruction will only `OR` the bits of this core's [permission](uarc.html) with `perm`, so it can only restrict the [permission](uarc.html). Any bits from this core's [address](uarc.html) which are masked by the [permission](uarc.html) will be automatically set the same in the delegation address, while the only bits that will be changed are the ones in `addr` masked by the inverse of the [permission](uarc.html).

#### Side Effects
- All cores incepted after this instruction is executed will have the set [permission](uarc.html) and [address](uarc.html).

----------

## `expect`
`v b -- `

#### Description
`expect` sends the value `v` to bus `b` and expects a response. This instruction does not complete until `v` is received. A location is allocated on the conveyor for the response of the target core. When the conveyor location is accessed, it will synchronize with the interrupt sent back on bus `b`. This can be used to efficiently implement coprocessors, even at the software level from another u0-x. This instruction can receive the value and handle the interrupt asynchronously while in an interrupt handler.

This can be used for all multi-cycle operations, such as square root, multiply, divide, floating point operations, external memory operations, etc. It is expected that multiply is provided over at least one bus in this way.

#### Side Effects
- Bus `b` is sent `v`.
- Execution is synchronized with the completion of sending `v`.
- `cv <- response`

----------

## `sendp`
`n a -- `

#### Description
`sendp` sends a stream to all selected UARC buses. `n` is the number of words able to be streamed out and `a` is the address at which those words are to be streamed from. This streams from program memory one processor word at a time.

#### Side Effects
- Execution is synchronized with the completion of the stream operation.

----------

## `inceptp`
`n a -- `

#### Description
`inceptp` sends a stream to all selected UARC buses. `n` is the number of words able to be streamed out and `a` is the address at which those words are to be streamed from program memory. This will only work on a core which is not presently running or the core has **permission** over the core and the stream will become the instructions to be executed by the target core. By incepting cores again, the system can maintain **permission** over cores allocated on a chip for a process and prevent those cores from incepting cores they aren't supposed to. If a chip is to be given up by the system to a process, the system can incept all cores with the **permission** of the process and run an idle routine.

#### Side Effects
- Execution is synchronized with the completion of the stream operation.

----------

## `getp`
` -- perm`

#### Description
`getp` places the [permission](uarc.html) of the current core on the stack. This value is a mask of the [address](uarc.html). The core cannot delegate a [permission](uarc.html) on a child core which has any bits unset from its own [permission](uarc.html). Any delegated [address](uarc.html) must have the same [address](uarc.html) bits which are masked by the delegating core's [permission](uarc.html).

----------

## `geta`
` -- addr`

#### Description
`geta` places the [address](uarc.html) of the current core on the stack. This value is 32 bits and will be placed in the least significant bits of the word. The bits of the [address](uarc.html) masked by the [permission](uarc.html) must be the same in all child cores.

#### Notes
- This instruction is supported for u0-32 and up.

----------

## `intset`
`p -- `

#### Description
`intset` sets the interrupt address of all selected buses to `p`.

#### Side Effects
- Any further interrupts on the selected UARC buses now call the routine at `p`.

----------

## `seb`
`b -- `

#### Description
`seb` sets the only UARC bus to be selected as `b`.

#### Side Effects
- All UARC calls now affect only `b`.

----------

## `slb`
`b -- `

#### Description
`slb` adds the UARC bus `b` to the selected cores.

#### Side Effects
- All UARC calls now also affect `b`.

----------

## `usb`
`b -- `

#### Description
`usb` removes the UARC bus `b` from the selected cores.

#### Side Effects
- All UARC calls no longer affect `b`.

----------

## `intsend`
`v -- `

#### Description
`intsend` sends a value to all selected UARC buses, and waits for all buses to accept the interrupt before continuing.

#### Side Effects
- Execution is synchronized with the acceptance of all interrupts sent.
