# AES Key Reconstruction

This repository includes an AES key schedule generator from a given key as well as a random schedule generator. Additionally, it includes an erasure and bit flipping functionnality that emulates the key schedule passing through a noisy channel. Finally, it includes an error correcting algorithm for the binary erasure, binary symmetric, and Z-channels. 

## Compilation

It suffices to execute 'make'. The binary files will be located at ./bin

## Execution

### 1. Key Schedule Generator
To generate an AES key schedule, execute 

    ./bin/keygen [KEY] [OPTIONS]

Where KEY represents an AES-128/192/256 key in hexadecimal and OPTIONS include (for now) -v=false to disable verbose

If no input is given, then a random AES-128 key schedule is generated.

### 2. Noisy Channels
To modify a schedule, execute

    ./bin/keymod <file> <probability> <channel_t> [options]

Where 

- 'file' contains an AES key schedule as formatted as ./bin/keygen 's output (see './samples/aes-128-schedule.txt' for an example)
- 'probability' indicates how likely each bit is to flip/be erased
- 'channel_t' indicates the type of noisy channel the key schedule goes through. Accepted types include 'bin-sym' for binary symmetric, 'bin-erasure' for binary erasure, and 'z-channel'
- options include -v=false to disable verbose

### 3. Error Correction

#### 3.1 Binary Erasure Channel

To correct a key schedule that lost bits through the binary erasure channel, execute

    ./bin/correct_bec <file> [options]

Where filename contains an AES key schedule that went through the binary erasure channel. For an example of formatting see './samples/aes-128-bin_erasure.txt' (it corresponds to the output of './bin/keymod ./samples/aes-128-schedule.txt 0.125 bin-erasure -v=false'). The key schedule is represented as a grid of binary strings. Missing bits are represented with an 'X'.

### 3.2 Z-Channel

To correct a key schedule whose bits have flipped as follow 

![Wikimedia Diagram Showing Z-Channel](https://upload.wikimedia.org/wikipedia/commons/0/0e/Z-channel.svg)

Execute

    ./bin/correct_z <file> <probability> [options]

Where filename contains an AES key schedule that went through the Z noisy channel. For an example of formatting see './samples/aes-128-z_channel.txt' (it corresponds to the output of './bin/keymod ./samples/aes-128-schedule.txt 0.125 z-channel -v=false'). The key schedule is represented as a grid of 32-bit-long hexadecimal values.

## Improvements

### Binary Erasure Channel

- In the core resolution cycle we can add a 4th case that includes the analysis of the first column
- We can immediatly determine the AES key given a round key for AES-128 and two round keys for AES-256
- Correct AES-256 key schedule (each round should contain 128 bit blocks)
- Once we know 4 consecutive words of an AES-128 key schedule we can derive all other subkeys