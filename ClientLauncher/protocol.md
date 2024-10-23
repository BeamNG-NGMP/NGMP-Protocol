# Client <-> Launcher protocol

## Packet design
All packets follow the same basic design.
**Packets always start with a standardized header.**<br>
This header starts with 2 bytes containing a unique packet identifier (2 UTF8 characters), followed by 4 bytes containing the packet length as an unsigned 32 bit integer, stored in Little Endian order.
All actual data is an lpack-encoded string to reduce client computation requirements by as much as possible.

TODO: Provide an example packet here.


## Connection flow
1. C->L - ClientInfo packet
2. L->C - Version packet
3. L->C - AuthenticationInfo packet
4. L->C - ServerList packet
5. C->L - JoinServer packet
6. L->C - ModList packet
7. C->L - ModList packet

<- Repeat if needed ->

8. L->C - ModProgress packet

<-->

9. L->C - LoadMap packet
10. C->L - Confirmation packet


## Packet types
### Generic
**Confirmation packet**<br>
Protocol: TCP/UDP<br>
Identifier: CC
Format: Binary
```
confirm_id:         uint16
```

### Handshake/early connection
**Version packet**<br>
Protocol: TCP<br>
Identifier: VC
Format: Binary
```
confirm_id:         uint16
protocol_version:   uint16
```

**ClientInfo packet**<br>
Protocol: TCP<br>
Identifier: CI
Format: JSON
```
confirm_id:         uint16
client_version:     uint16
userfolder:         char-array
```

**AuthenticationInfo packet**<br>
Protocol: TCP<br>
Identifier: AI
Format: JSON
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
Format:
```
confirm_id:         uint16
ip_addresses:       char-array-array
```
Note: ^ TODO

**JoinServer packet**<br>
Protocol: TCP<br>
Identifier: HJ
Format: Binary
```
confirm_id:         uint16
ip_address:         char-array
```

**ModList packet**<br>
Protocol: TCP<br>
Identifier: ML
Format: Binary
```
confirm_id:         uint16
mod_list:           char-array
```
Note: The modlist is a list of filenames (minus the .zip extension) and the respective SHA-256 hash concatenated together, separated by colons for one mod and split up by slashes.
This is reused for two-way communication.

**LoadMap packet**<br>
Protocol: TCP<br>
Identifier: LM
Format: Binary
```
confirm_id:         uint16
map_string:         char-array
```
Note: The map-string should look something like "/levels/west_coast_usa/info.json"

**ModProgress packet**<br>
Protocol: TCP<br>
Identifier: MP
Format: JSON
```
confirm_id:         uint16
progress:           uint8
mod_name:           char-array
```
Note: Progress is in %. 100% means ready to mount.
