# Arithmetic Instructions

|Instruction|Op|dstack|Side Effects|Purpose|
|:---:|:---:|:---:|:---:|:---:|
|add|`40`|`a b -- (a + b)`|Carry is set|Adds two integers|
|addc|`41`|`a b -- (a + b + c)`|Carry is set|Adds two integers and the carry bit|
|sub|`42`|`a b -- (a - b)`|Carry is set|Subtracts two integers|
|subc|`43`|`a b -- (a - b + c)`|Carry is set|Subtracts two integers and adds the carry bit|
|asl|`44`|`a b -- (a << b)`|Overflow is set|Arithmetic shift left is the same as logical|
|asr|`45`|`a b -- (a >> b)`| |Arithmetic shift right duplicates the sign bit|
|mul|`46`|`a b -- (a * b)`|Sets HILO register|Multiplies two signed numbers and puts the overflow with sign in HILO|
|mulu|`47`|`a b -- (a * b)`|Sets HILO register|Multiplies two unsigned numbers and puts the overflow bits in HILO|
|div|`60`|`a b -- `|Adds quotient and remainder to conveyor|Divides two numbers asynchronously and places the results on the conveyor|
