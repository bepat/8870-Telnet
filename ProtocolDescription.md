# Communication protocol
The 8870 polls the VDU periodically. The poll can either be a dummy message (Command = 0x00) or a message with real content. The VDU has to answer with an ACK0 or ACK1 (in change) in a specified timerange. Otherwise the message is repeated by the 8870.

## Message format
Every message has at least a header. The following data bytes are optionally. So the minimum message length is 4 bytes.


| Position | Description                |
|----------|----------------------------|
| 0        | Sync (always 0xFF)         |
| 1        | first 4 bits: Device (Master, Slave, Printer, ...), last 4 bits: Command|
| 2        | Data length* (n). All bytes starting with Position 4. |
| 3        | LPC1 (sum of bytes 0 to 2) |
| [4]      | Optional data              |
| [...]    | Optional data              |
| [4 + n]  | Optional LPC2 (sum of bytes 0 to 4+n) |

[] = Optional bytes<br />
*In case of device data (0x05) the length is one additional byte.

## Command byte
The command byte is splitted into the higher 4 bits and the lower 4 bits. The higher bits represents the device address (Display, Printer, other SAS devices). The lover 4 bits represents the Command.

### Commands (lower 4 bits)
| Value | Description  | Sent by  |
|-------|--------------|----------|
| 0x00  | No operation | 8870     |
| 0x01  | ENQ          | 8870     |
| 0x02  | Keyboard input mode: normal (text visible, local echo) | 8870 |
| 0x03  | Keyboard input mode: hidden (no local echo) | 8870 |
| 0x07  | Keyboard input mode: ???    | 8870 |
| 0x07  | Keyboard input mode: cancel | 8870 |
| 0x05  | Device data  | 8870     |
| 0x06  | ACK0         | VDU |
| 0x09  | ACK1         | VDU |
| 0x0D  | Loading done | 8870     |

### Devices (higher 4 bits)
| Value | Description |
|-------|-------------|
| 0x00  | Master VDU  |
| 0x01  | Slave VDU   |
| 0x04  | first printer  |
| 0x05  | second printer |

## Example messages
```
Dummy poll from the 8870
FF 00 00 FF

ACK0 from VDU
FF 06 00 05

ACK1 from VDU
FF 09 00 08

Display data (Text: "Kennwort bitte:") for the master VDU from the 8870
FF 05 14 18 4B 65 6E 6E 77 6F 72 74 20 62 69 74 74 65 20 20 20 20 20 3A 20 BA
```

# Reply to 8870 messages
Each message from the 8870 has to be acknowledged by the VDU. Otherwise the message will be repeatet. The Acknowledgement is signalized by ACK0 or ACK1 in the control byte. These two values has to be send in change.
If The VDU wants to send data like keyboard input or response data from the printer back to the 8870, these data have to be included in an ACK message.

## Example response messages
```
Send keyboard text "MANAGER\n" from the master VDU to the 8870
FF 09 08 10 4D 41 4E 41 47 45 52 0D 28
```



# Basic commands
All screen manipulations are done by basic commands (see basic programming manual).
Thera are two types of commands. 
One type of command always starts with a lead-in code (0x1E) followed by the command code and optional parameters.
The other type of command is a single byte command without any parameters.

## Lead-in code commands
| Name                     | Oct. Code | Description                            | Parameter | Used* | ANSI Escape Sequence|
| -------------------------|-----------|----------------------------------------|-----------|-------|----------------------|
| Save cursor position     | 211       | Save current cursor position.          | -         | Y     | Esc[s |
| Back to saved position   | 212       | Go back to saved cursor position.      | -         | Y     | Esc[u |
| Clear foreground         | 235       | Clear screen foreground.               | -         | N     | |
| Clear screen             | 234       | Clear screen, move cursor to 0, 0.     | -         | Y     | Esc[2J Esc[H |
| Cursor home              | 222       | Move cursor to position 0, 0.          | -         | Y     | Esc[H 
| Delete line              | 223       | Deletes the specified line.            | -         | N     | Esc[1M |
| Insert line              | 232       | Inserts a line.                        | -         | N     | Esc[1L \015|
| Start background         | 231       | Starts writing to screen background, different color.| - | Y | Esc[0m |
| Start foreground         | 237       | Starts writing to screen foreground, different color.| - | Y | Esc[1m |
| Tab command              | 221       | Moves the cursor to the given coordinates | 0: row number </br> 1: column number | Y | Esc[(row)>;(col)H|
| Get cursor Position      | 236 204   | Gets the current cursor coordinates from the VDU | | | |
| Define Window            | 236 205   | Defines a window (row based, columns are ignored). Have to be followed by 2 tab commands. | Tab cmd 1: start row<br /> Tab cmd 2: end row | Y | |

Sources: http://www.skepticfiles.org/cowtext/ansi/ansicode.htm, http://ascii-table.com/ansi-escape-sequences.php

\* Used in my test cases (TAMOS)

 
 ## Single byte commands
| Bezeichnung                   | Octal Code  | Beschreibung                                  | Used* | ANSI code |
| ------------------------------|-------------|-----------------------------------------------|-------|-----------|
| Backspace                     | 210         | Moves cursor backwards, deletes one char.     | Y     | \b        |
| Carriage return               | 215         | Moves cursor to the next line, column 0.      | Y     | \r\n      |
| Ring keyboard bell            | 207         | Rings the keyboard bell                       | Y     | \a        |
| Tab to next foreground field  | 211         | Moves the cursor to the next foreground field | N     |           |

# Keyboard codes
...

# Hardware
## ALME V.24 15 pin connector
| Pin | Function |
|-----|----------|
| 1   | |
| 2   | |
| 3   | |
| 4   | |
| 5   | |
| 6   | |
| 7   | |
| 8   | |
| 9   | |
| 10  | |
| 11  | |
| 12  | |
| 13  | |
| 14  | |
| 15  | |

