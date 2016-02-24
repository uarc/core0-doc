# Arithmetic Instructions

|Instruction|Op|dstack|Side Effects|
|:---:|:---:|:---:|:---:|:---:|
|add|`40`|`a b -- (a + b)`|Carry is set|
|addc|`41`|`a b -- (a + b + c)`|Carry is set|
|sub|`42`|`a b -- (a - b)`|Carry is set|
|subc|`43`|`a b -- (a - b + c)`|Carry is set|
|asl|`44`|`a b -- (a << b)`|Overflow is set|
|asr|`45`|`a b -- (a >> b)`| |
|mul|`46`|`a b -- (a * b)`|Sets HILO register|
|mulu|`47`|`a b -- (a * b)`|Sets HILO register|
|div|`60`|`a b -- `|Adds quotient and remainder to conveyor|
|divi|`61`|`a b -- `|Adds quotient and remainder to conveyor|
