# UARC Instructions

## `ien`
` -- `

#### Description
`ien` enables interrupts in the [ifile](architecture/ifile.html) only for buses which are presently selected.

#### Side Effects
- All selected buses now have interrupts enabled.
- All non-selected buses now have interrupts disabled.

----------

## `recv`
` -- `

#### Description
`recv` accepts any interrupt synchronously without an interrupt handler from any selected bus. The bus ID (`bus`), address (`addr`), and the interrupt value (`v`) are added to the [conveyor](architecture/conveyor.html) in that order.

#### Side Effects
- `cv <- bus, addr, v`
- Synchronizes execution with the next interrupt from a selected bus

----------

## `in`
`a -- b`

#### Description
`in` receives a stream from any selected UARC bus. `a` is the address at which those words are to be streamed to. `b` is the bus ID of the particular bus that streamed these values.

#### Side Effects
- Execution is synchronized with the completion of the stream operation

----------

## `kill`
` -- `

#### Description
`kill` causes every single core connected to every selected bus and every single one of their children to stop before the instruction completes. This instruction takes `2 * n` cycles, where `n` is the depth of the longest chain of incepted cores. This will not succeed unless the core has [permission](uarc.html) over the target cores. If the target does not have the proper [permission](uarc.html), it will report success without doing anything.

#### Side Effects
- All selected cores for which this core has permission will be stopped before this instruction finishes.

----------

## `wait`
` -- `

#### Description
`wait` allows the code to synchronously handle interrupts by waiting the core until the next interrupt. This will allow interrupts through from selected buses rather than buses with interrupts enabled. If an interrupt had already been received, the `iflag` will already be `1`. After this instruction executes, the `iflag` is unchanged to indicate that no interrupts have executed that the kernel has not witnessed. Normal interrupts cannot interrupt the core while it is waiting, but they can interrupt the core immediately after `wait` catches an interrupt.

#### Side Effects
- One interrupt from a selected bus will have been handled before this instruction completes.
- While it is waiting it blocks normal interrupts

----------

## `getbp`
`b -- perm`

#### Description
`getbp` places the [permission](uarc.html) of the core on bus `b` onto the stack.

----------

## `getba`
`b -- addr`

#### Description
`getba` places the [address](uarc.html) of the core on bus `b` onto the stack. If the `AND` of `addr` with this core's [permission](uarc.html) is equal to this core's [address](uarc.html) with the [permission](uarc.html) then this core has [privilege](uarc.html) over the core on this bus. This check shouldn't need to be performed manually, but that is the process.

----------

## `getp`
` -- perm`

#### Description
`getp` places the [permission](uarc.html) of the current core on the stack. This value is a mask of the [address](uarc.html). The core cannot delegate a [permission](uarc.html) on a child core which has any bits unset from its own [permission](uarc.html). Any delegated [address](uarc.html) must have the same [address](uarc.html) bits which are masked by the delegating core's [permission](uarc.html).

----------

## `geta`
` -- addr`

#### Description
`geta` places the [address](uarc.html) of the current core on the stack. This value is 31 bits and will be placed in the least significant bits of the word. The bits of the [address](uarc.html) masked by the [permission](uarc.html) must be the same in all child cores.

#### Notes
- This instruction is not supported for a `WORD` that is less than 32 bits.

----------

## `iset`
`a -- `

#### Description
`iset` sets the interrupt address of all selected UARC buses to `a`.

#### Side Effects
- Any further interrupts on the selected UARC buses now call the routine at `a`.

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

## `setp`
`perm addr -- `

#### Description
`setp` sets the [permission](uarc.html) that is to be delegated to all incepted UARC cores. This value defaults to the same [permission](uarc.html) and [address](uarc.html) that this core receives on inception. This instruction will only `OR` the bits of the old [permission](uarc.html) with `perm`, so it can only restrict the [permission](uarc.html). Any bits from this core's [address](uarc.html) which are masked by the [permission](uarc.html) will be automatically set the same in the delegation address, while the only bits that will be changed are the ones in `addr` masked by the inverse of the [permission](uarc.html).

#### Side Effects
- All cores incepted after this instruction is executed will have the set [permission](uarc.html) and [address](uarc.html).
