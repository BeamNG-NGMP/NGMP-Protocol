# Client <-> Launcher protocol

## Packet design
All packets follow the same basic design.
**Packets always start with a standardized header.**<br>
This header starts with **4 bytes** containing the packet length (unsigned 32 bit integer, little endian), followed by 2 bytes containing a unique packet identifier (2 ascii characters) to inform the receiver what packet it is.
All actual data is an lpack-encoded string to reduce client computation requirements by as much as possible.

TODO: Provide an example packet here.


## Connection flow
1. L->C - Version packet
2. L->C - Authentication info packet
3. L->C - ServerList packet
4. C->L - JoinServer packet
5. L->C - ModList packet

<- Repeat if needed ->

6. L->C - ModProgress packet

<-->

7. L->C - LoadMap packet
8. C->L - Confirmation packet


## Packet types
### Generic
**Confirmation packet**<br>
Protocol: TCP/UDP<br>
Identifier: CC
```
<<<<<<< HEAD
confirm_id:       uint16
=======
confirm_id:         uint16
>>>>>>> e2e676b (client_version -> protocol_version)
```

### Handshake/early connection
**Version packet**<br>
Protocol: TCP<br>
Identifier: VC
```
<<<<<<< HEAD
confirm_id:       uint16
protocol_version: uint16
=======
confirm_id:         uint16
protocol_version:   uint16
>>>>>>> e2e676b (client_version -> protocol_version)
```

**Authentication info packet**<br>
Protocol: TCP<br>
Identifier: AI
```
confirm_id:         uint16
success:            bool
player_name:        char-array
```
Note: This should never contain any important data as the authenticator is Steam.
Authentication is handled solely by the Launcher. The client receives this packet for cosmetic reasons.

### Post-auth connection
**ServerList packet**<br>
Protocol: TCP<br>
Identifier: HL
```
confirm_id:         uint16
ip_addresses:       char-array-array
```
Note: ^ TODO

**JoinServer packet**<br>
Protocol: TCP<br>
Identifier: HJ
```
confirm_id:         uint16
ip_address:         char-array
```

**ModList packet**<br>
Protocol: TCP<br>
Identifier: ML
```
confirm_id:         uint16
mod_list:           char-array
```
Note: The modlist is simply a list of filenames (minus the .zip extension) concatenated together, separated by semicolons.

**LoadMap packet**<br>
Protocol: TCP<br>
Identifier: LM
```
confirm_id:         uint16
map_string:         char-array
```
Note: The map-string should look something like "/levels/west_coast_usa/info.json"

**ModProgress packet**<br>
Protocol: TCP<br>
Identifier: MR
```
confirm_id:         uint16
mod_name:           char-array
progress:           uint8
```
Note: Progress is in %. 100% means ready to mount.
