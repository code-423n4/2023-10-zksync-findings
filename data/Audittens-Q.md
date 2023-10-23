### Overflow in the `bootloader::lengthRoundedByWords` function

In the `bootloader::lengthRoundedByWords` function the result is zero for `len >= ((2**256) - 31)`. This creates possibilities for attack vectors.