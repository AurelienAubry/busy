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

This header is followed by the message payload (up to 2^12 = 4096 bytes).

Finally, the message ends with a 2 bytes CRC code.

## Medium Access Control (MAC)

BUSY uses Carrier-Sense Multiple Access (CSMA) Medium Access Control (MAC) protocol.
Before sending data, a node verifies that the medium is free by sensing it : if 16 bits are read, the medium is considered free, and the node transmits his message.

When teh receiver receives the message, he will compute the CRC of the message, and compare it with the received CRC. If the values match, he sends an `ACK` message to the sender. Otherwise, he sends a `NACK` message.
If the sender receives a `NACK` message, or no message at all, he will try to retransmit the lost message.
The retransmission his scheduled using an exponential backoff algorithm : a random time is picked within the `[0-CW]` range, where `CW = 2**attempt_nb` and `CW <= 256 = 2**8`.
