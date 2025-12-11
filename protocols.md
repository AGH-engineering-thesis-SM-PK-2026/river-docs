# Protocols

Reference for all packets and commands used in the system.

The definitions of downstream and upstreams are:

```
downstream: RIV ---packet--> PC
upstream:   RIV <--packet--- PC
```

These are rules to everypacket structure:
1. first character of the packet is its type ID;
2. last character is always a line feed ( *\n* ) character;
3. fields are separated by comma ( *,* ) character;

## 1. Downstream 

### 1.1. Debug packet

Debug packet containing program counter value and all registers values except the `x0` which is hardwired to zero.

##### Structure:
```
P0000008C,00000001,FFFFFFA0,/ ... /,AAAAAAAA,DEADBEEF\n
^{  PC  } {  x1  }^{  x2  } \     \ { x30  } { x31  }^^
|                 |         /     /                   |
start-of-packet   field separator         end-of-packet
```

## 2. Upstream

### 2.1. Start command packet

Run core at maximum speed. No response generated.

##### Structure:
```
Z\n
```

### 2.2. Halt command packet

Halt core. Should leave core in FETCH state as indicated by the LED lights on the main board (0 0 0 1). No response generated.

##### Structure:
```
H\n
```

### 2.3. Reset command packet

Resets the core, does not halt or start the core. No response generated.

##### Structure:
```
R\n
```

### 2.4. Step command packet

Step through CPU states or instruction. The mode can be selected by second character of the packet with the following:

| char | mode             |
| ---- | ---------------- |
| `1`  | next CPU state   |
| `>`  | next instruction |

When selecting the next instruction the core should stop at next the FETCH CPU state, thus this operation might have to finish the execution of current cycle, after the core has been stepped through in CPU state mode. No reponse generated.

##### Structure:
```
S1\n  - step one CPU state
S>\n  - step one instruction
```

### 2.5. Program upload packet

Upload program data to the core, via the UART. This is the preffered method of programming the system. The core should not generate a response to that packet, althought this might change in the future.

Notice that the program stream ends with `]` character preceding the newline character.

##### Structure:
```
[00002137,50000013,/ ... /,00000013]\n
^{instr0} {instr1} \     \ {instrN}^
|                  /     /         |
start of upload        end of upload
```

### 2.6. Print command packet

Request debug info. Should generate response in the form of one debug packet.

##### Structure:
```
P\n
```

## 3. Behaviours

### 3.1. Print debug info

##### Flow:
```
PC                      RIV
| ---print cmd. [2.6.]--> |  request
|                         |
| <--debug pkt. [1.1.]--- |  response
.                         .
```

### 3.2. Core control

##### Flow:
```
PC                       RIV
| ---core cmd. [2.1-4.]--> | request
.                          .
```

### 3.3. Program upload

##### Flow:
```
PC                       RIV
| ---upload cmd. [2.5.]--> | request
|                          |
| ------run cmd. [2.1.]--> | (optional)
.                          .
```
