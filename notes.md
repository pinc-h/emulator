# Notes
### [DMG-01 Section 3.1](https://rylev.github.io/DMG-01/public/book/cpu/registers.html)  
Oct. 13 2024, Alex  
Defining registers and manipulating them... not flagging yet.  
Implementing the registers, creating functions that combine the 8bit registers tin order to read & write 16 bits.  
`get_bc` reads and appends the registers B and C, shifting B's values to the left and adding C's values to the end  
`set_bc` creates a mutable 16bit integer that writes values to B then shifts to the right, then writes values to C  
0xFF00 = 1111111100000000  
Shifting right makes: 00000000111111111 ?? I think?  
0xFF = 11111111 appending makes `get_bc` (or self?) equal to: 1111111111111111  
Again, not sure how this works but trying to understand  
```rust,noplaypen
impl Registers {
  fn get_bc(&self) -> u16 {
    (self.b as u16) << 8
    | self.c as u16
  }

  fn set_bc(&mut self, value: u16) {
    self.b = ((value & 0xFF00) >> 8) as u8;
    self.c = (value & 0xFF) as u8;
  }
}
```  
Oct. 24 2024, Alex  
Continuing w/ section 3.1
[Read the wikipedia for CPU registers later](https://en.wikipedia.org/wiki/Processor_register)   
Learning about the flags register (Register 'f'). First 4 bits (upper nibble) have specific meanings, lower nibble always zeros.  
The upper nibble can be defined by:  
```rust,noplaypen
struct FlagsRegister {
    zero: bool,
    subtract: bool,
    half_carry: bool,
    carry: bool
}
```
Diagram of flag register:  
     V-lowernibble  
1111 0000  
^-uppernibble   
  
 
This encodes for the functions of the upper nibble. Still confused about what all thsi means   
```rust,noplaypen
const ZERO_FLAG_BYTE_POSITION: u8 = 7;
const SUBTRACT_FLAG_BYTE_POSITION: u8 = 6;
const HALF_CARRY_FLAG_BYTE_POSITION: u8 = 5;
const CARRY_FLAG_BYTE_POSITION: u8 = 4;

impl std::convert::From<FlagsRegister> for u8  {
    fn from(flag: FlagsRegister) -> u8 {
        (if flag.zero       { 1 } else { 0 }) << ZERO_FLAG_BYTE_POSITION |
        (if flag.subtract   { 1 } else { 0 }) << SUBTRACT_FLAG_BYTE_POSITION |
        (if flag.half_carry { 1 } else { 0 }) << HALF_CARRY_FLAG_BYTE_POSITION |
        (if flag.carry      { 1 } else { 0 }) << CARRY_FLAG_BYTE_POSITION
    }
}
```
First, defining a few constants that will related the FlagsRegister bools with a numerical value (the position, for ex. zero is 7)
Then the impl function, for 'flag' in FlagsRegister (each bool), if flag is false, or set to 0, the bit is constant is right-shifted.** I DONT UNDERSTAND WHAT THAT DOES **  
<br>
Okay next section:
```rust, noplaypen
impl std::convert::From<u8> for FlagsRegister {
    fn from(byte: u8) -> Self {
        let zero = ((byte >> ZERO_FLAG_BYTE_POSITION) & 0b1) != 0;
        let subtract = ((byte >> SUBTRACT_FLAG_BYTE_POSITION) & 0b1) != 0;
        let half_carry = ((byte >> HALF_CARRY_FLAG_BYTE_POSITION) & 0b1) != 0;
        let carry = ((byte >> CARRY_FLAG_BYTE_POSITION) & 0b1) != 0;

        FlagsRegister {
            zero,
            subtract,
            half_carry,
            carry
        }
    }
}
```
This sets each bool in FlagsRegister equal to a byte (0 or 1, same as a bool) & the BYTE_POSITION constants, if they aren't equal to 0.  
'0b' is notation for binary, so this function is creating bytes for each item in FlagRegister.    
WHAT I DONT UNDERSTAND IS: the byte is left-shifted to the BYTE_POSITION constant. Also this left-shifted byte & the value 0b1 (binary value of 1) must not be equal to zero. I also don't understand what the flag defining of the FlagsRegister does, is this the return for the function?
