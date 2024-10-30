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
Identifier: CC
```json
{
    "confirm_id": 0
}
```

### Handshake/early connection
**Version packet**<br>
Identifier: VC
```json
{
    "confirm_id": 0,
    "protocol_version": 1
}
```

**ClientInfo packet**<br>
Identifier: CI
```json
{
    "client_version": 1,
    "userfolder": "C:/Users..."
}
```

**AuthenticationInfo packet**<br>
Identifier: AI
```json
{
    "avatar_hash": "408fa5f4aa2011080a86aef18f69395a3b2a661d",
    "player_name": "Zeit",
    "steam_id": "76561198892918470",
    "success": true
}
```
Note: This never contains any important data as the authenticator is Steam.
Authentication is handled solely by the Launcher. The client receives this packet for cosmetic reasons.

**LoginRequest packet**<br>
Identifier: LR

### Post-auth connection
**JoinServer packet**<br>
Identifier: HJ
```json
{
    "ip_address": "127.0.0.1:42630"
}
```

**ConnectionError packet**<br>
Identifier: CE
```json
{
    "error": "/"
}
```

**ExitServer packet**<br>
Identifier: HX

**PlayerKick packet**<br>
Identifier: PK
```json
{
    "reason": "/"
}
```

**LoadMap packet**<br>
Identifier: LM
```json
{
    "confirm_id": 0
    "map_string": "/levels/gridmap_v2/info.json"
}
```
Note: The map-string should always look something like "/levels/west_coast_usa/info.json".
Some maps may use an old .mis format. In this case the map path corresponds to the .mis file.

**ModRequest packet**<br>
Identifier: MR
```json
{
    "mod_name": "mod"
}
```

**ModProgress packet**<br>
Identifier: MP
```json
{
    "progress": 0.54321,
    "mod_name": "mod"
}
```
Note: Progress is in %.
0.5 ≙ 50%; 1 ≙ 100%

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
    "avatar_hash": "bcf7541f4566aaf4ad5ffd35764def2f035acb25",
    "name": "big gaming 123",
    "steam_id": "890123489189"
}
```

### Gameplay
**VehicleSpawn packet**<br>
Identifier: VS
```json
{
    "confirm_id": 0,
    "steam_id": "76561198892918470",
    "vehicle_id": 0,
    "vehicle_data": [
        "Jbeam": "bastion",
        "object_id": 32512,
        "paints": "...",
        "partConfig": "...",
        "pos": [1,1,1],
        "rot": [1,1,1,1]
    ]
}
```
Note: "paints" and "partConfig" are serialized strings for use in BeamNG only. They most likely cannot be read by other means.

**VehicleConfirm packet**<br>
Identifier: VA
```json
{
    "confirm_id": 0,
    "object_id": 32512,
    "vehicle_id": 0,
}
```

**VehicleDelete packet**<br>
Identifier: VR
```json
{
    "steam_id": "76561198892918470"
    "vehicle_id": 0
}
```

**VehicleUpdate packet**<br>
Identifier: VU
```json
{
    "steam_id": "76561198892918470",
    "runtime_data": "<json encoded data>",
    "vehicle_id": 0
}
```
Note: "runtime_data" is a JSON encoded string in of itself.
This is done for performance reasons to avoid an extra encode + decode step during transfer from GELua to VLua.

**VehicleTransform packet**<br>
Identifier: VT
```json
{
    "steam_id": "76561198892918470",
    "transform": "<json encoded data>",
    "vehicle_id": 0
}
```
Note: "transform" is a JSON encoded string in of itself.
This is done for performance reasons to avoid an extra encode + decode step during transfer from GELua to VLua.

Note: The transform data looks as following:
```json
{
    "pos": [1,1,1],
    "rot": [1,1,1,1],
    "rvel": [1,1,1],
    "vel": [1,1,1]
}
```

**VehicleDisplay packet**<br>
Identifier: VD
```json
{
    "steam_id": "76561198892918470",
    "dislay_data": {
        "license_text": "Zeit",
        "paints": "..."
    },
    "vehicle_id": 0
}
```
Note: "paints" is a serialized string for use in BeamNG only. It most likely cannot be read by other means.
