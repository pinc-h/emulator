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
