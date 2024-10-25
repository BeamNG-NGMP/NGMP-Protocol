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
6. S->L - LoadMap packet
7. L->S - Confirmation packet


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

### Server Data
**PlayerData packet**<br>
Protocol: TCP<br>
Identifier: PD
```json
{
    "players": []
}
```
Note: Each entry in the player list looks as following:
```json
{
    "name": "big gaming 123",
    "steam_id": 890123489189
}
```

### Gameplay
**VehicleSpawn packet**<br>
Protocol: TCP<br>
Identifier: VS
```
confirm_id:         uint16
vehicle_data:       char-array (json)
```

**VehicleConfirm packet**<br>
Protocol: TCP<br>
Identifier: VA
```
confirm_id:         uint16
vehicle_id:         uint16
obj_id:             uint64
```

**VehicleDelete packet**<br>
Protocol: TCP<br>
Identifier: VD
```
player_id:          uint64
vehicle_id:         uint16
```
