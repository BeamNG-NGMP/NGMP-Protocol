# Client <-> Launcher protocol

## Packet design
All packets follow the same basic design.
**Packets always start with a standardized header.**<br>
This header starts with 2 bytes containing a unique packet identifier (2 UTF8 characters), followed by 4 bytes containing the packet length as an unsigned 32 bit integer, stored in Little Endian order.
All actual data is an lpack-encoded string to reduce client computation requirements by as much as possible.

The launcher also hosts an HTTP server that the client is free to use for GET and POST requests.

TODO: Provide an example packet here.


## Connection flow
### Startup
1. C->L - ClientInfo packet
2. L->C - Version packet
3. C->L - LoginRequest packet
4. L->C - AuthenticationInfo packet

### Join Server
1. C->L - JoinServer packet
2. L->C - ServerInfo packet (or ConnectionError packet)
3. C->L - ModRequest packet (Repeat if needed)
4. L->C - ModProgress packet (Repeat if needed)
5. L->C - LoadMap packet
6. C->L - Confirmation packet

### Vehicle
1. C->L - VehicleSpawn packet
2. L->C - VehicleConfirm packet
3. L->C - VehicleDelete packet (if vehicle invalid)
4. C->L - VehicleUpdate packet (Repeat if needed)
5. C->L - VehicleDisplay packet (Repeat if needed)
6. C->L - VehicleDelete packet (Repeat if needed)

### Leave Server
1. C->L - ExitServer packet

## Packet types
### Generic
**Confirmation packet**<br>
Identifier: CC<br>
Format: Binary
```
confirm_id:         uint16
```

### Handshake/early connection
**Version packet**<br>
Identifier: VC<br>
Format: Binary
```
confirm_id:         uint16
protocol_version:   uint16
```

**ClientInfo packet**<br>
Identifier: CI<br>
Format: JSON
```
confirm_id:         uint16
client_version:     uint16
userfolder:         char-array
```

**LoginRequest packet**<br>
Identifier: LR<br>
Format: Binary
```
confirm_id:         uint16
```

**AuthenticationInfo packet**<br>
Identifier: AI<br>
Format: JSON
```
confirm_id:         uint16
avatar_hash:        char-array
success:            bool
steam_id:           uint64
player_name:        char-array
```
Note: This should never contain any important data as the authenticator is Steam.
Authentication is handled solely by the Launcher. The client receives this packet for cosmetic reasons.

### Post-auth connection
**JoinServer packet**<br>
Identifier: HJ<br>
Format: Binary
```
confirm_id:         uint16
ip_address:         char-array
```

**ConnectionError packet**<br>
Identifier: CE<br>
Format: Binary
```
confirm_id:         uint16
error:              char-array
```

**ServerInfo packet**<br>
Identifier: HI<br>
Format: Binary
```
confirm_id:         uint16
http_port:          uint16
```

**ExitServer packet**<br>
Identifier: HX<br>
Format: Binary
```
confirm_id:         uint16
```

**LoadMap packet**<br>
Identifier: LM<br>
Format: Binary
```
confirm_id:         uint16
map_string:         char-array
```
Note: The map-string should look something like "/levels/west_coast_usa/info.json"

**ModRequest packet**<br>
Identifier: MR<br>
Format: Binary
```
confirm_id:         uint16
mod_name:           char-array
```

**ModProgress packet**<br>
Identifier: MP<br>
Format: JSON
```
progress:           uint8
mod_name:           char-array
```
Note: Progress is in %. 100% means ready to mount.

### Server Data
**PlayerData packet**<br>
Identifier: PD
```json
{
    "players": []
}
```
Note: Each entry in the player list looks as following:
```json
{
    "avatar_hash": bcf7541f4566aaf4ad5ffd35764def2f035acb25
    "name": "big gaming 123",
    "steam_id": 890123489189
}
```

### Gameplay
**VehicleSpawn packet**<br>
Identifier: VS
Format: Binary
```
confirm_id:         uint16
vehicle_data:       char-array (json)
```

**VehicleConfirm packet**<br>
Identifier: VA
Format: Binary
```
confirm_id:         uint16
vehicle_id:         uint16
```

**VehicleDelete packet**<br>
Identifier: VR
Format: Binary
```
confirm_id:         uint16
vehicle_id:         uint16
steam_id:           uint64
```

**VehicleUpdate packet**<br>
Identifier: VU
Format: Binary
```
vehicle_data:       char-array (json)
```

**VehicleDisplay packet**<br>
Identifier: VD
Format: Binary
```
vehicle_data:       char-array (json)
```
Note: This is used for license plate and paints.
