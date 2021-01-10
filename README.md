# BUSY
A minimalist half-duplex communication protocol

# Specification

## Message format
```
 0                   1                   2                                         N                  N+1
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3                                   0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  DST  |  SRC  | OPCODE|      DATA LENGTH      |      DATA: up to 4096 bytes     |              CRC-16             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

Each message starts with a header, composed of:
- The destination address (0-127), 4 bits (0-3)
- The source address (0-127), 4 bits (4-7)
- An OpCode, describing the message type (DATA, ACK, NACK...), 4 bits (8-11)
- The payload length, in bytes (0-4096), 12 bits (12-23)

This header is followed by the messsage payload (up to 2^12 = 4096 bytes).

Finally, the message ends with a 2 bytes CRC code.

## Message transmission
