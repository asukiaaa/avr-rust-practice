# Setup memo
Commands that I executed.

# Environment
## PC
```
uname -a
# Linux asuki-ThinkPad-S1-Yoga 4.13.0-19-generic #22-Ubuntu SMP Mon Dec 4 11:58:07 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
```

## Arduino
An Arduino that contains atmega328p likes Arduino Uno, Arduino Nano or so on.

# Setup rust
```
curl https://sh.rustup.rs -sSf | sh
```

In my case, rust was ver 1.22.1.
```
rustc --version
# rustc 1.22.1 (05e2e1c41 2017-11-22)
```

https://www.rust-lang.org/install.html

# Setup avr-rust
```
# Create working directory
mkdir -p ~/gitprojects/avr-rust
cd ~/gitprojects/avr-rust

# Took about 0.5 hours
git clone https://github.com/avr-rust/rust.git
cd rust
mkdir build && cd build
../rust/configure \
  --enable-debug \
  --disable-docs \
  --enable-llvm-assertions \
  --enable-debug-assertions \
  --enable-optimize \
  --prefix=/opt/avr-rust

# Took about 2 hours
make

# Create directory to run make install without sudo
sudo mkdir /opt/avr-rust
sudo chmod ${USER} /opt/avr-rust

# Install
make install

# Add avr-toolcain to rustup
rustup toolchain link avr-toolchain $(realpath $(find . -name 'stage1'))
```

https://github.com/avr-rust/rust

# Test avr-toolchain with using sample blink program
```
# Downlad sample program
cd ~/gitprojects/avr-rust
git clone https://github.com/avr-rust/blink.git
cd blink

# Install dependent programs
cargo install xargo
sudo apt install avr-gcc avr-libc avrdude

# Build elf file
XARGO_RUST_SRC=/opt/avr-rust rustup run avr-toolchain xargo build --target avr-atmega328p --release

# Convert elf to hex
cd target/avr-atmega328p/release
avr-objcopy -O ihex -R .eeprom blink.elf blink.hex

# Upload hex to Arduino Uno
avrdude -p m328p -P /dev/ttyACM0 -c arduino -b 115200 -U flash:w:blink.hex

# Upload hex to Arduino Nano
avrdude -p m328p -P /dev/ttyUSB0 -c arduino -b 57600 -U flash:w:blink.hex
```

- https://github.com/avr-rust/blink
- https://qiita.com/kktk-KO/items/5098edfb95c2b71996b0
- http://zhehaomao.com/blog/2012/07/25/programming-avr.html
- http://www.avrfreaks.net/forum/programming-arduino-nano-avrdude

# Create this project
```
cargo new avr-rust-practice
```
