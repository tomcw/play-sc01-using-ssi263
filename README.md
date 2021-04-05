# play-sc01-using-ssi263

TL;DR: Playback SC-01 phoneme-based phrases using the SSI263 speech chip.

### Overview:

Scan slots from 7 to 1 looking for a 6522-based card (Mockingboard or Phasor) with either/both a SC-01 or SSI263.
For each card it finds:
- If it detects an SC-01, then use keys 1-7 to playback actual SC-01 phrases (from games) on the SC-01 chip
- If it detects an SSI263, then use keys A-G to playback the SC-01 phrases using the SSI263
  - additionally for the SSI263, use R to increment (or CTRL+R to decrement) the SSI263 4-bit Rate attribute
- After playback, then the phrase duration ("Time") is displayed as a 24-bit cycle count
- Use ESC to exit this card and continue scanning slots

### Details:

The SSI263 is "kicked" with a PAUSE (0x00) phoneme to start the playback in the IRQ handler. But the 4 attribute registers are also setup at this point, and the SSI263 IRQ handler only ever writes to reg-0 (the phoneme register). The 4-bit Rate attribute is part of this setup, and can be configured in the main UI.

The code has both an SC-01 and an SSI263 IRQ handler to handle playback on both chip types. When using the SSI263 IRQ handler, then if the `isrTranslate` flag is set, the handler will convert the SC-01 6-bit phoneme to an equivalent SSI263 phoneme using a 64-byte look-up table (ie. the very same conversation table in AppleWin).

The ISR handlers also count 6522 Timer1 underflow interrupts (from a starting latched value of 0xFFFF) and record the Timer1 h/l values at the handler start. These are used to calculate the phoneme phrase duration.

### Examples:

The 7 phrases at Rate=$B, [here](https://user-images.githubusercontent.com/6696896/113566294-57ae2c00-9604-11eb-8506-812713ae52b8.mp4).

The 7 phrases at Rate=$C, [here](https://user-images.githubusercontent.com/6696896/113566315-60066700-9604-11eb-8b9a-c3d38beb51bb.mp4).
