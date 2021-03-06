# Spec for VM <-> Elixir communication.

## Request Header
Requests can travel in either direction:

|Seg|Description |Width             |Notes                     |
|---|------------|------------------|--------------------------|
| 0 |Channel Num | 2 bytes          | Uint16, big endian       |
| 1 |Namespace   | 4 bytes          | ASCII operation namespace|
| 3 |Operation   | 5 bytes          | ASCII operation name     |
| 4 |Payload size| 2 bytes          | Uint16, big endian       |
| 5 |CLRF        | 2 bytes          | Y'know, `\r\n`           |

Followed by a "payload"

## Request Payload

This is based on the size of segment 3 in the header and is typically used for
paramter storage.

## Response (6 bytes)

|Seg|Description           |Width   |Notes                       |
|---|----------------------|--------|----------------------------|
|  0|Channel Num           | 2 bytes| Originating channel number |
|  1|Return status code    | 2 bytes| Implementation specific    |
|  2|Return value          | 2 bytes| Uint16, big endian         |
|  3|CLRF                  | 2 bytes|                            |

## Operation Listing

|Namespace|Operation|Request Payload          |Return  |
|---------|---------|-------------------------|--------|
|CODE     |WRITE    |CeleryScript JSON        |Code ID |
|CODE     |RM       |Code ID                  |Status  |
|PROC     |KILL     |Process ID               |Status  |
|PROC     |PAUSE    |Process ID               |Status  |
|PROC     |RUN      |Process ID               |Status  |
|PROC     |START    |Code ID                  |Pid     |
|REGISTER |NEW      |Still up for discussion. |        |
|SLICE    |NEW      |Still up for discussion. |        |

## Hypervisor Calls

### System control
|Namespace |Operation     |Request Payload |Return |
|----------|--------------|----------------|-------|
|SYS       |CHECK_UPDATES | NONE           |       |
|SYS       |FACTORY_RESET | Package        |       |
|SYS       |POWER_OFF     | NONE           |       |
|SYS       |REBOOT        | NONE           |       |

### Firmware interaction
|Namespace |Operation         |Request Payload                  |Return |
|----------|------------------|---------------------------------|-------|
|SYS       |MOVE_ABSOLUTE     | X, Y, Z, Xspeed, Yspeed, Zspeed |       |
|SYS       |MOVE_RELATIVE (?) | Just forward to Moveabs?        |       |
|SYS       |CALIBRATE         | Axis Enum                       |       |
|SYS       |FIND_HOME         | Axis Enum                       |       |
|SYS       |HOME              | Axis Enum                       |       |
|SYS       |ZERO              | Axis Enum                       |       |
|SYS       |SET_SERVO_ANGLE   | Angle                           |       |
|SYS       |TOGGLE_PIN        | Pin                             |       |
|SYS       |WRITE_PIN         | Pin                             |       |

### Configuration interaction and communication.
|Namespace |Operation     |Request Payload         |Return |
|----------|--------------|------------------------|-------|
|SYS       |CONFIG_UPDATE | Package, config, value |       |
|SYS       |SET_USER_ENV  | config, value          |       |
|SYS       |SEND_MESSAGE  | Message                |       |

### Farmware
|Namespace |Operation                    |Request Payload       |Return |
|----------|-----------------------------|----------------------|-------|
|SYS       |INSTALL_FIRST_PARTY_FARMWARE | NONE                 |       |
|SYS       |TAKE_PHOTO                   | NONE                 |       |
|SYS       |EXECUTE_SCRIPT               | Package (not really) |       |
|SYS       |INSTALL_FARMWARE             | Package (not really) |       |
|SYS       |REMOVE_FARMWARE              | Package (not really) |       |
|SYS       |UPDATE_FARMWARE              | Package (not really) |       |

### RPI GPIO
|Namespace |Operation       |Request Payload |Return |
|----------|----------------|----------------|-------|
|SYS       |REGISTER_GPIO   | Depricated?    |       |
|SYS       |UNREGISTER_GPIO | Depricated?    |       |

### Control
|Namespace |Operation                  |Request Payload |Return |
|----------|---------------------------|----------------|-------|
|SYS       |WAIT (possible CPU bound?) | Milliseconds   |       |
|SYS       |SLEEP                      | WIP?           |       |
|SYS       |EXIT                       | WIP?           |       |

## Enums

Used in the request payloads of some IPC messages.

|Name   |0       |1 |2|3  |
|-------|--------|--|-|---|
|Axis   |X       |Y |Z|ALL|
|Package|Firmware|OS| |   |
w
