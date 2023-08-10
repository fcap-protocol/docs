# Protocol Specification
The below outlines the protocol specification. Including packet format and any implementation requirements.

## Assumptions
* Datalink will indicate start of packet

## Justifications
* Meaningful values are 1-byte aligned to simplify the encoding and decoding
* Multi-byte values are big-endian to align with the [TCP/IP RFC 1700](https://www.rfc-editor.org/rfc/rfc1700) standard

# Packet Format

At its very core, the minimum requirement for a packet is:
* 1x Header
* \>= 1 key-type-value (KTV)

<img src="docs/format.png">

## Header

The header is always 1 byte and contains the following sections
* version
* rtr
* num_keys

Potential Changes
* Add preamble field

<img src="docs/header_format.png">

### Number of Keys

**Type:** uint5

**Description:** The number of KTVs within this packet. This, along with the rtr status, is used to determine the length of the packet.

### Version
**Type:** 4-bit enum

**Description:** The version of the protocol this packet aligns to.

The versions are outlined below for verbosity.

| Version | Binary | Notes |
|---------|--------|-------|
| undef | 000 | Will match any packet with any version, used for development |
| 1 | 001 | 
| 2 | 010 |
| 3 | 011 |
| 4 | 100 |
| 5 | 101 |
| 6 | 110 |
| 7 | 111 |


### Message ID

**Type:** uint7

**Description:** A cyclic count of requests from the sender. When responding to a message, it will set this to the ID of the message it is responding to. 

A special ID of 0 is used for broadcast packets

### Message type

**Type:** 1-bit enum

**Description:** Is this message a request or a reponse. 

| enum  | type     | 
|-------|----------|
| 0     | Request  |
| 1     | Response |

## KTV

Each KTV contains a unique key, the data type of the value and the value itself. The key is unique amongst other keys, and must be one of the pre-defined keys outlined below. Any key can have any type. A key can only appear once in a given packet.

It is recommended that keys [0-25] are mapped to [A-Z] (case insensitive) and the remaining keys may be left to the implementation to decide.

A non-binary typed KTV has the following packet format

<img src="docs/ktv_format_normal.png">

</br>
A binary typed KTV has the following packet format

<img src="docs/ktv_format_binary.png">

### Key

**Type:** uint5

**Description:** Unique key to identify information

There are 32 available unique keys for the user to use. 0-31 inclusive. Each key can only exist once in each packet.

### Type

**Type:** 3-bit enum

**Description:** The data type the following value should be interpreted as.

There are 8 types available for the user to use. These are outlined below. Note that the 0 type is binary and allows the user to send up to 2^8 number of bytes in a single value blob. Therefore, if the user used all 32 keys as binary, they would be able to send 32*2^8 number of bytes of user data. Both clients must be configured to support this.

| enum  (decimal) | enum (binary) | type | length (bytes) |
|-------|------|-----------|-----|
| 0     | 000  | binary    | N/A |
| 1     | 001  | uint8     | 1   |
| 2     | 010  | uint16    | 2   |
| 3     | 011  | int16     | 2   |
| 4     | 100  | int32     | 4   |
| 5     | 101  | int64     | 8   |
| 6     | 110  | float     | 4   |
| 7     | 111  | double    | 8   |


### Value

**Type:** N/A

**Description:** The information associated with the preceding key.

Multi-byte values are sent in **big-endian** to match the [TCP/IP RFC 1700](https://www.rfc-editor.org/rfc/rfc1700)

Binary data is not manipulated from the user.