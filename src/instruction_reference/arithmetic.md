# Arithmetic Instructions

## `add`
`a b -- (a + b)`

#### Description
`add` takes parameters `a` and `b` from the stack and adds them. The carry bit is set by this instruction, but is not consumed.

#### Side Effects
`c = (a + b)[WORD]`


## `addc`
`a b -- (a + b + c)`

#### Description
`addc` takes parameters `a` and `b` from the stack and adds them with `c`. Unlike `add`, `addc` consumes the carry, so ensure it is correctly set before issuing this instruction.

#### Side Effects
`c = (a + b)[WORD]`

## `sub`
`a b -- (a - b)`

####  Description
`sub` subtracts parameter `b` from `a`. The carry bit is set by this instruction to `1` when no borrow occurs. The carry bit is not consumed by this instruction.

## `subc`
`a b -- (a - b + c)`

#### Description
`subc` subtracts parameter `b` from `a` and adds `c`. The carry bit is set by this instruction to `1` when no borrow occurs.
