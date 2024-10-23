# Server <-> Launcher protocol

## Packet design
All packets follow the same basic design.
**Packets always start with a standardized header.**<br>
This header starts with 2 bytes containing a unique packet identifier (2 UTF8 characters), followed by 4 bytes containing the packet length as an unsigned 32 bit integer, stored in Little Endian order.

TODO: Provide an example packet here.


## Connection flow
1. L->S - Version packet
2. S->L - Confirmation packet
3. L->S - Authentication packet
4. S->L - Confirmation packet
5. S->L - ServerInfo packet
6. L->S - Confirmation packet
7. L->S - UDPAddr packet
8. S->L - LoadMap packet
9. L->S - Confirmation packet


## Packet types
### Generic
**Confirmation packet**<br>
Protocol: TCP/UDP<br>
Identifier: CC
```
confirm_id:         uint16
```

### Handshake/early connection
**Version packet**<br>
Protocol: TCP<br>
Identifier: VC
```
confirm_id:         uint16
protocol_version:   uint16
```

**Authentication packet**<br>
Protocol: TCP<br>
Identifier: AC
```
confirm_id:         uint16
auth_code:          char-array
```

### Post-auth connection
**ServerInfo packet**<br>
Protocol: TCP<br>
Identifier: HI
```
http_port:          uint16
udp_port:           uint16
```

**LoadMap packet**<br>
Protocol: TCP<br>
Identifier: LM
```
confirm_id:         uint16
map_string:         char-array
```
Note: The map-string should look something like "/levels/west_coast_usa/info.json"

**UDPAddr packet**<br>
Protocol: TCP<br>
Identifier: UA
```
udp_addr:           char-array
```
