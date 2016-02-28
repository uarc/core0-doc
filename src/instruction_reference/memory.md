# Memory Instructions

## `flush`
` -- `

#### Description
`flush` forces all dirty cache to be written to memory before this instruction completes execution.

#### Side Effects
- Dirty cache is flushed to main memory

#### Examples
```
write flush send
```
- `(send_val write_val address -- )`
- On a ccNUMA architecture, the other core can now access the value `write_val` at `address`
- The other core is interrupted only after the value has been flushed to memory
