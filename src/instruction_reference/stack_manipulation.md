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
