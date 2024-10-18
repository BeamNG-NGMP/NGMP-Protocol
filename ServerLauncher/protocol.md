# Server <-> Launcher protocol

## Packet design
All packets follow the same basic design.
**Packets always start with a standardized header.**<br>
This header starts with **4 bytes** containing the packet length (unsigned 32 bit integer, little endian), followed by 2 bytes containing a unique packet identifier (2 ascii characters) to inform the receiver what packet it is.

TODO: Provide an example packet here.


## Connection flow
1. L->S - Version packet
2. S->L - Confirmation packet
3. L->S - Authentication packet
4. S->L - Confirmation packet


## Packet types
### Generic
**Confirmation packet**<br>
Identifier: CC
```
packet_id:      uint16
```

### Connection
**Version packet**<br>
Identifier: VC
```
packet_id:      uint16
client_version: uint16
```

**Authentication packet**<br>
Identifier: AA
```
packet_id:      uint16
auth_code:      char-array
```
