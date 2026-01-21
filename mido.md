# Mido: Comprehensive Reference Documentation

## Abstract

This document provides comprehensive technical reference material for Mido (MIDI Objects for Python), a Python library designed for working with MIDI messages, ports, and files. Mido offers a Pythonic interface to MIDI functionality, supporting real-time MIDI I/O, Standard MIDI File manipulation, and message parsing. The library abstracts platform-specific MIDI APIs through a backend system, currently supporting RtMidi, PortMidi, and Pygame backends. This documentation, referencing Mido version 1.3.2+, encompasses all major components, classes, methods, functions, and design patterns.

## 1. Introduction

### 1.1 Overview and Philosophy

Mido (short for MIDI Objects) is architected around the principle of treating MIDI data as Python objects. The design philosophy emphasizes simplicity, Pythonic idioms, and type safety. Messages are represented as objects with comprehensive validation, ensuring validity according to MIDI specifications.

### 1.2 Core Design Principles

- **Message-as-Object**: MIDI messages are first-class Python objects with attributes and methods
- **Type Safety**: All message parameters undergo validation at creation and modification
- **Interchangeable Ports**: Unified API across all port types enables polymorphic usage
- **Streaming Architecture**: Built-in support for real-time MIDI with proper timing
- **Complete MIDI Standard**: Full implementation of all 18 MIDI message types

### 1.3 Channel Numbering Convention

Mido numbers MIDI channels 0-15 internally, diverging from the traditional 1-16 numbering used in musical contexts. This zero-indexed approach aligns with Python conventions. Applications presenting channel information to users may need to add 1 for display purposes.

## 2. Message Class

### 2.1 Message Construction

**Signature:**
```python
Message(type, **attributes) -> Message
```

**Description:**

The Message class represents MIDI channel voice messages and system messages. Messages are created by specifying a type string and optional keyword arguments for attributes.

**Parameters:**

- `type` (str): Message type identifier (e.g., 'note_on', 'control_change')
- `**attributes`: Message-specific attributes as keyword arguments

**Default Values:**

- Most attributes default to 0
- `velocity` defaults to 64 (recommended MIDI default for devices without velocity sensitivity)
- `data` (for sysex messages) defaults to empty tuple ()
- `time` defaults to 0

**Example:**
```python
import mido

# Create note_on message with explicit attributes
msg = mido.Message('note_on', channel=0, note=60, velocity=100)

# Create with defaults
msg = mido.Message('program_change', program=5)
```

### 2.2 Message Attributes

All messages contain a `type` attribute identifying the message kind. Additional attributes vary by message type. Common attributes include:

- `channel` (int, 0-15): MIDI channel
- `note` (int, 0-127): Note number
- `velocity` (int, 0-127): Note velocity
- `control` (int, 0-127): Controller number
- `value` (int, 0-127): Controller value
- `program` (int, 0-127): Program/instrument number
- `time` (int or float): Delta time (usage context-dependent)

Attributes are accessible via standard Python attribute access:

```python
msg = mido.Message('note_on', note=60)
print(msg.note)  # 60
print(msg.velocity)  # 64 (default)
print(msg.type)  # 'note_on'
```

### 2.3 Message Modification

**Method: copy(**attributes)**

**Signature:**
```python
copy(**attributes) -> Message
```

**Description:**

Creates a new message with modified attributes. This is the preferred method for message modification, as it maintains immutability principles.

**Example:**
```python
msg = mido.Message('note_on', note=60, velocity=64)
msg2 = msg.copy(note=62, velocity=100)
# msg is unchanged, msg2 has new values
```

### 2.4 Message Serialization

**Method: bytes()**

**Signature:**
```python
bytes() -> list
```

**Description:**

Returns MIDI message as a list of integers representing raw MIDI bytes.

**Example:**
```python
msg = mido.Message('note_on', channel=0, note=60, velocity=64)
data = msg.bytes()  # [144, 60, 64]
```

**Method: bin()**

**Signature:**
```python
bin() -> bytearray
```

**Description:**

Returns message as a bytearray object.

**Method: hex()**

**Signature:**
```python
hex() -> str
```

**Description:**

Returns hexadecimal string representation of message bytes.

**Example:**
```python
msg = mido.Message('note_on', note=60, velocity=64)
print(msg.hex())  # '90 3C 40'
```

### 2.5 Message Parsing

**Class Method: from_bytes()**

**Signature:**
```python
Message.from_bytes(bytes) -> Message
```

**Description:**

Creates a message from raw MIDI bytes. Bytes must contain exactly one complete message.

**Example:**
```python
msg = mido.Message.from_bytes([0x90, 0x3C, 0x40])
print(msg)  # Message('note_on', channel=0, note=60, velocity=64, time=0)
```

**Class Method: from_hex()**

**Signature:**
```python
Message.from_hex(hex_string) -> Message
```

**Description:**

Creates a message from hexadecimal string representation.

**Example:**
```python
msg = mido.Message.from_hex('90 3C 40')
```

### 2.6 Message Type Checking

**Method: is_meta()**

**Returns:** Boolean indicating if message is a meta message

**Method: is_cc(control=None)**

**Signature:**
```python
is_cc(control=None) -> bool
```

**Description:**

Checks if message is a control change message. If control number is specified, checks for that specific controller.

**Example:**
```python
msg = mido.Message('control_change', control=7, value=100)
if msg.is_cc():
    print("Control change message")
if msg.is_cc(7):
    print("Volume control")
```

### 2.7 System Exclusive Messages

System Exclusive (SysEx) messages have special handling due to variable length:

**Example:**
```python
# Create SysEx message
msg = mido.Message('sysex', data=[0x7E, 0x00, 0x09, 0x01])

# Data attribute is tuple
print(msg.data)  # (126, 0, 9, 1)

# Extend data
msg.data += (0x7F,)
print(msg.hex())  # 'F0 7E 00 09 01 7F F7'
```

**Data Parameter:**

The data parameter accepts any iterable yielding integers 0-127:
- Lists: `[65, 66, 67]`
- Tuples: `(65, 66, 67)`
- Generator expressions: `(i + 65 for i in range(3))`
- Bytearrays: `bytearray(b'ABC')`
- Bytes (Python 3): `b'ABC'`

## 3. Message Types

### 3.1 Channel Voice Messages

#### note_off
**Attributes:** `channel`, `note`, `velocity`, `time`

Terminates a playing note. Velocity represents release velocity.

#### note_on
**Attributes:** `channel`, `note`, `velocity`, `time`

Initiates note playback. Note that velocity=0 is equivalent to note_off on most devices.

#### polytouch
**Attributes:** `channel`, `note`, `value`, `time`

Polyphonic aftertouch (key pressure) for individual notes.

#### control_change
**Attributes:** `channel`, `control`, `value`, `time`

Modifies continuous controller values. Control numbers 0-127 represent different controllers (e.g., 7 = volume, 10 = pan).

#### program_change
**Attributes:** `channel`, `program`, `time`

Selects instrument/patch. Program numbers 0-127 correspond to General MIDI instruments when applicable.

#### aftertouch
**Attributes:** `channel`, `value`, `time`

Channel-wide aftertouch (pressure applied to all notes).

#### pitchwheel
**Attributes:** `channel`, `pitch`, `time`

Pitch bend control. Pitch values range from -8192 to +8191, with 0 representing no bend.

### 3.2 System Common Messages

#### sysex
**Attributes:** `data`, `time`

System Exclusive message for manufacturer-specific data. Data is tuple of bytes.

#### quarter_frame
**Attributes:** `frame_type`, `frame_value`, `time`

SMPTE time code quarter frame message.

#### songpos
**Attributes:** `pos`, `time`

Song position pointer for MIDI sequence synchronization.

#### song_select
**Attributes:** `song`, `time`

Selects MIDI song/sequence number.

#### tune_request
**Attributes:** `time`

Requests analog synthesizers to tune their oscillators.

### 3.3 System Real-Time Messages

#### clock
**Attributes:** `time`

MIDI timing clock pulse (24 per quarter note).

#### start
**Attributes:** `time`

Start MIDI sequence playback from beginning.

#### continue
**Attributes:** `time`

Continue MIDI sequence playback from current position.

#### stop
**Attributes:** `time`

Stop MIDI sequence playback.

#### active_sensing
**Attributes:** `time`

Indicates connection is active (sent every 300ms when present).

#### reset
**Attributes:** `time`

Reset all devices to power-on state.

## 4. Meta Messages

### 4.1 MetaMessage Class

**Signature:**
```python
MetaMessage(type, **attributes) -> MetaMessage
```

**Description:**

Meta messages are used exclusively in MIDI files to convey non-MIDI data such as tempo, time signatures, lyrics, and markers. They do not appear in real-time MIDI streams.

**Example:**
```python
from mido import MetaMessage

# Set tempo meta message
tempo_msg = MetaMessage('set_tempo', tempo=500000, time=0)

# Track name
name_msg = MetaMessage('track_name', name='Piano', time=0)
```

### 4.2 Common Meta Message Types

#### set_tempo
**Attributes:** `tempo`, `time`

Sets tempo in microseconds per quarter note. Default is 500000 (120 BPM).

#### time_signature
**Attributes:** `numerator`, `denominator`, `clocks_per_click`, `notated_32nd_notes_per_beat`, `time`

Defines time signature. Denominator is power of 2 (e.g., 2=quarter note, 3=eighth note).

#### key_signature
**Attributes:** `key`, `time`

Key signature specified as string (e.g., 'C', 'Am', 'F#', 'Ebm'). Minor keys append 'm'.

#### text
**Attributes:** `text`, `time`

Generic text annotation.

#### copyright
**Attributes:** `text`, `time`

Copyright notice.

#### track_name
**Attributes:** `name`, `time`

Track or sequence name.

#### instrument_name
**Attributes:** `name`, `time`

Instrument name for track.

#### lyrics
**Attributes:** `text`, `time`

Lyric text synchronized to music.

#### marker
**Attributes:** `text`, `time`

Marker for specific point in sequence.

#### cue_marker
**Attributes:** `text`, `time`

Cue point marker.

#### device_name
**Attributes:** `name`, `time`

Target device name.

#### sequence_number
**Attributes:** `number`, `time`

Sequence/pattern number (rarely used).

#### channel_prefix
**Attributes:** `channel`, `time`

Default channel for subsequent meta messages.

#### midi_port
**Attributes:** `port`, `time`

MIDI output port designation.

#### end_of_track
**Attributes:** `time`

Marks end of MIDI track. Automatically added if absent.

#### smpte_offset
**Attributes:** `frame_rate`, `hours`, `minutes`, `seconds`, `frames`, `sub_frames`, `time`

SMPTE time code offset.

### 4.3 UnknownMetaMessage

Unimplemented or unrecognized meta messages are returned as UnknownMetaMessage objects:

**Attributes:**
- `type_byte`: Byte identifying message type
- `data`: Message data as list of bytes
- `time`: Delta time

These messages are preserved exactly when reading and writing files, ensuring no data loss.

## 5. Frozen Messages

### 5.1 Concept and Usage

**Function: freeze_message()**

**Signature:**
```python
freeze_message(message) -> FrozenMessage
```

**Description:**

Creates an immutable, hashable version of a message. Frozen messages can be used as dictionary keys and in sets, providing protection against accidental modification.

**Example:**
```python
from mido import freeze_message, thaw_message

msg = mido.Message('note_on', note=60)
frozen = freeze_message(msg)

# Can use as dictionary key
note_registry = {frozen: "Middle C"}

# Convert back to mutable
mutable = thaw_message(frozen)
```

**Function: thaw_message()**

**Signature:**
```python
thaw_message(frozen_message) -> Message
```

**Description:**

Creates a mutable copy from a frozen message.

**Function: is_frozen()**

**Signature:**
```python
is_frozen(message) -> bool
```

**Description:**

Returns True if message is frozen, False otherwise.

### 5.2 Frozen Message Classes

- `FrozenMessage`: Frozen version of Message
- `FrozenMetaMessage`: Frozen version of MetaMessage  
- `FrozenUnknownMetaMessage`: Frozen version of UnknownMetaMessage

## 6. Ports

### 6.1 Port Concepts

Mido ports represent endpoints for MIDI communication. All port types implement a unified API, enabling polymorphic usage regardless of underlying implementation (device, network socket, multi-port aggregate).

### 6.2 Opening Ports

**Function: open_input()**

**Signature:**
```python
open_input(name=None, virtual=False, callback=None, **kwargs) -> InputPort
```

**Description:**

Opens a MIDI input port for receiving messages.

**Parameters:**

- `name` (str, optional): Port name. If None, opens default input port.
- `virtual` (bool): Create virtual port that other applications can connect to (RtMidi only, not Windows).
- `callback` (callable, optional): Function called for each received message (backend-dependent).
- `**kwargs`: Backend-specific parameters.

**Environment Variables:**

`MIDO_DEFAULT_INPUT` can override the default input port.

**Example:**
```python
# Open default input
inport = mido.open_input()

# Open specific port
inport = mido.open_input('SH-201')

# Virtual port
inport = mido.open_input('My Virtual Port', virtual=True)
```

**Function: open_output()**

**Signature:**
```python
open_output(name=None, virtual=False, autoreset=False, **kwargs) -> OutputPort
```

**Description:**

Opens a MIDI output port for sending messages.

**Parameters:**

- `name` (str, optional): Port name. If None, opens default output port.
- `virtual` (bool): Create virtual port (RtMidi only, not Windows).
- `autoreset` (bool): Automatically send all_notes_off and reset_all_controllers when closing.
- `**kwargs`: Backend-specific parameters.

**Environment Variables:**

`MIDO_DEFAULT_OUTPUT` can override the default output port.

**Example:**
```python
# Open default output
outport = mido.open_output()

# Specific port with autoreset
outport = mido.open_output('Integra-7', autoreset=True)
```

**Function: open_ioport()**

**Signature:**
```python
open_ioport(name=None, virtual=False, callback=None, autoreset=False, **kwargs) -> IOPort
```

**Description:**

Opens a bidirectional MIDI port supporting both input and output operations.

**Environment Variables:**

`MIDO_DEFAULT_IOPORT` can override the default I/O port.

### 6.3 Port Discovery

**Function: get_input_names()**

**Signature:**
```python
get_input_names() -> list
```

**Description:**

Returns list of available input port names.

**Example:**
```python
print(mido.get_input_names())
# ['Midi Through Port-0', 'SH-201', 'Integra-7']
```

**Function: get_output_names()**

**Signature:**
```python
get_output_names() -> list
```

**Description:**

Returns list of available output port names.

**Function: get_ioport_names()**

**Signature:**
```python
get_ioport_names() -> list
```

**Description:**

Returns list of available bidirectional port names.

### 6.4 Input Port Methods

**Method: receive(block=True)**

**Signature:**
```python
receive(block=True) -> Message
```

**Description:**

Receives a single message from the port. If block=True, waits until message available. If block=False, returns None if no message ready.

**Example:**
```python
with mido.open_input() as inport:
    msg = inport.receive()
    print(msg)
```

**Method: iter_pending()**

**Signature:**
```python
iter_pending() -> iterator
```

**Description:**

Non-blocking iteration over currently available messages.

**Example:**
```python
for msg in inport.iter_pending():
    print(msg)
```

**Method: poll()**

**Signature:**
```python
poll() -> bool
```

**Description:**

Returns True if messages are available, False otherwise. Non-blocking.

**Iteration Support:**

Input ports support iteration, blocking until messages arrive:

```python
with mido.open_input('SH-201') as inport:
    for msg in inport:
        print(msg)
        if msg.type == 'note_off':
            break
```

### 6.5 Output Port Methods

**Method: send(message)**

**Signature:**
```python
send(message) -> None
```

**Description:**

Sends a MIDI message through the port. The message is copied, so the original remains unmodified.

**Example:**
```python
with mido.open_output() as outport:
    msg = mido.Message('note_on', note=60, velocity=64)
    outport.send(msg)
```

**Method: reset()**

**Signature:**
```python
reset() -> None
```

**Description:**

Sends all_notes_off and reset_all_controllers on all 16 channels. Useful for cleaning up after playback or experimentation.

**Method: panic()**

**Signature:**
```python
panic() -> None
```

**Description:**

Sends all_sounds_off on all channels, immediately terminating all sounding notes.

### 6.6 Port Context Management

All ports support the context manager protocol:

```python
with mido.open_input() as inport:
    for msg in inport:
        print(msg)
# Port automatically closed
```

**Method: close()**

**Signature:**
```python
close() -> None
```

**Description:**

Explicitly closes the port. Idempotent—safe to call multiple times.

### 6.7 Port Attributes

**Attribute: name**

Port name as string. May be None for some port types.

**Attribute: closed**

Boolean indicating whether port is closed.

## 7. Advanced Port Types

### 7.1 MultiPort

**Import:**
```python
from mido.ports import MultiPort
```

**Signature:**
```python
MultiPort(ports, yield_ports=False) -> MultiPort
```

**Description:**

Aggregates multiple input ports, receiving messages from all as if they were one port.

**Parameters:**

- `ports`: Iterable of input ports
- `yield_ports`: If True, receive() returns (port, message) tuples

**Example:**
```python
from mido.ports import MultiPort

port1 = mido.open_input('Port 1')
port2 = mido.open_input('Port 2')
multi = MultiPort([port1, port2])

for msg in multi:
    print(msg)
```

### 7.2 SocketPort (Client)

**Import:**
```python
from mido.sockets import SocketPort
```

**Signature:**
```python
SocketPort(host, port) -> SocketPort
```

**Description:**

Connects to a MIDI server over TCP/IP, providing network-based MIDI communication.

**Example:**
```python
from mido.sockets import SocketPort

port = SocketPort('localhost', 8080)
port.send(mido.Message('note_on', note=60))
msg = port.receive()
port.close()
```

### 7.3 PortServer

**Import:**
```python
from mido.sockets import PortServer
```

**Signature:**
```python
PortServer(host, port, backlog=1) -> PortServer
```

**Description:**

Creates a MIDI server that accepts TCP/IP connections. Acts as I/O port, broadcasting sent messages to all connected clients.

**Example:**
```python
from mido.sockets import PortServer

server = PortServer('localhost', 8080)
for msg in server:
    print(f"Received: {msg}")
    server.send(msg)  # Echo back to all clients
```

### 7.4 IOPort

**Import:**
```python
from mido.ports import IOPort
```

**Signature:**
```python
IOPort(input_port, output_port) -> IOPort
```

**Description:**

Combines separate input and output ports into a single bidirectional port.

**Example:**
```python
from mido.ports import IOPort

inport = mido.open_input('Input Device')
outport = mido.open_output('Output Device')
ioport = IOPort(inport, outport)

ioport.send(mido.Message('note_on', note=60))
msg = ioport.receive()
```

## 8. Parser

### 8.1 Parser Class

**Signature:**
```python
Parser(data=None) -> Parser
```

**Description:**

Parses MIDI byte streams into Message objects. Used internally by input ports but also available for custom implementations.

**Example:**
```python
parser = mido.Parser()
parser.feed([0x90, 0x3C, 0x40])

if parser.pending():
    msg = parser.get_message()
    print(msg)  # Message('note_on', ...)
```

### 8.2 Parser Methods

**Method: feed(data)**

**Signature:**
```python
feed(data) -> None
```

**Description:**

Feeds MIDI bytes to the parser. Accepts any iterable yielding integers 0-255.

**Method: feed_byte(byte)**

**Signature:**
```python
feed_byte(byte) -> None
```

**Description:**

Feeds a single MIDI byte (integer 0-255) to the parser.

**Method: get_message()**

**Signature:**
```python
get_message() -> Message or None
```

**Description:**

Retrieves next parsed message. Returns None if no complete message available.

**Method: pending()**

**Signature:**
```python
pending() -> int
```

**Description:**

Returns count of complete messages ready to be retrieved.

**Iterator Support:**

Parsers support iteration, yielding all available messages:

```python
parser.feed([0x90, 0x3C, 0x40, 0x80, 0x3C, 0x00])
for msg in parser:
    print(msg)
```

### 8.3 Utility Functions

**Function: parse()**

**Signature:**
```python
parse(data) -> Message
```

**Description:**

Parses MIDI data and returns first message found. Remaining data is ignored.

**Example:**
```python
msg = mido.parse([0x90, 0x3C, 0x40])
```

**Function: parse_all()**

**Signature:**
```python
parse_all(data) -> list
```

**Description:**

Parses MIDI data and returns list of all messages found.

**Example:**
```python
messages = mido.parse_all([0x90, 0x3C, 0x40, 0x80, 0x3C, 0x00])
# Returns list with note_on and note_off messages
```

## 9. MIDI Files

### 9.1 MidiFile Class

**Signature:**
```python
MidiFile(filename=None, file=None, type=1, ticks_per_beat=480, 
         charset='latin1', clip=False, **kwargs) -> MidiFile
```

**Description:**

Represents a Standard MIDI File, providing methods for reading, writing, and playback. Files are loaded entirely into memory for manipulation.

**Parameters:**

- `filename` (str, optional): Path to MIDI file
- `file` (file object, optional): File-like object containing MIDI data
- `type` (int): MIDI file type (0, 1, or 2)
- `ticks_per_beat` (int): PPQN resolution (default 480)
- `charset` (str): Character encoding for text meta messages
- `clip` (bool): Clip out-of-range values instead of raising exceptions

**File Types:**

- Type 0: Single track containing all events
- Type 1: Multiple tracks, first track often contains tempo/time signature
- Type 2: Multiple independent sequences (rarely used)

**Example:**
```python
# Read existing file
mid = mido.MidiFile('song.mid')

# Create new file
mid = mido.MidiFile(type=1, ticks_per_beat=960)
```

### 9.2 MidiFile Attributes

**Attribute: tracks**

List of MidiTrack objects. Each track is a list of messages.

**Attribute: ticks_per_beat**

Pulses Per Quarter Note (PPQN) resolution. Typical values: 96, 192, 480, 960.

**Attribute: type**

MIDI file type (0, 1, or 2).

**Attribute: length**

Total duration in seconds (read-only).

**Attribute: charset**

Character encoding for text meta messages.

### 9.3 MidiFile Methods

**Method: save(filename=None, file=None)**

**Signature:**
```python
save(filename=None, file=None) -> None
```

**Description:**

Saves MIDI file to disk or file object.

**Example:**
```python
mid = mido.MidiFile()
# ... add tracks and messages ...
mid.save('output.mid')

# Save to file object
from io import BytesIO
buffer = BytesIO()
mid.save(file=buffer)
```

**Method: play(meta_messages=False)**

**Signature:**
```python
play(meta_messages=False) -> iterator
```

**Description:**

Yields messages in playback order with time attributes set to delta time in seconds. Suitable for real-time playback.

**Example:**
```python
mid = mido.MidiFile('song.mid')
outport = mido.open_output()

for msg in mid.play():
    outport.send(msg)
```

**Method: __iter__()**

**Description:**

Iterating over a MidiFile yields all messages in playback order with time in seconds.

**Example:**
```python
for msg in mid:
    print(msg)
```

### 9.4 MidiTrack Class

**Signature:**
```python
MidiTrack(messages=None) -> MidiTrack
```

**Description:**

Subclass of list representing a MIDI track. Contains messages with time attributes as delta times in ticks.

**Attribute: name**

Track name extracted from track_name meta message, or empty string if absent.

**Example:**
```python
from mido import MidiFile, MidiTrack, Message, MetaMessage

mid = MidiFile()
track = MidiTrack()
mid.tracks.append(track)

track.append(MetaMessage('track_name', name='Piano', time=0))
track.append(Message('program_change', program=0, time=0))
track.append(Message('note_on', note=60, velocity=64, time=0))
track.append(Message('note_off', note=60, velocity=64, time=480))

mid.save('output.mid')
```

### 9.5 Tempo and Timing

**MIDI Tempo vs BPM:**

MIDI specifies tempo in microseconds per quarter note, not beats per minute. Default tempo is 500000 μs/qn (120 BPM).

**Conversion Functions:**

**Function: bpm2tempo()**

**Signature:**
```python
bpm2tempo(bpm) -> int
```

**Description:**

Converts beats per minute to microseconds per quarter note.

**Example:**
```python
tempo = mido.bpm2tempo(120)  # 500000
```

**Function: tempo2bpm()**

**Signature:**
```python
tempo2bpm(tempo) -> float
```

**Description:**

Converts microseconds per quarter note to beats per minute.

**Example:**
```python
bpm = mido.tempo2bpm(500000)  # 120.0
```

### 9.6 Time Conversion

**Function: tick2second()**

**Signature:**
```python
tick2second(tick, ticks_per_beat, tempo) -> float
```

**Description:**

Converts MIDI ticks to seconds given tempo and resolution.

**Parameters:**

- `tick` (int): Time in ticks
- `ticks_per_beat` (int): PPQN resolution
- `tempo` (int): Microseconds per quarter note

**Function: second2tick()**

**Signature:**
```python
second2tick(second, ticks_per_beat, tempo) -> int
```

**Description:**

Converts seconds to MIDI ticks. May require rounding.

### 9.7 Track Manipulation

**Function: merge_tracks()**

**Signature:**
```python
merge_tracks(tracks) -> MidiTrack
```

**Description:**

Merges multiple tracks into a single track with messages in chronological order. Delta times are converted to absolute times, sorted, then converted back to delta times.

**Example:**
```python
from mido.midifiles import merge_tracks

merged = merge_tracks(mid.tracks)
```

## 10. SYX Files

### 10.1 Reading SYX Files

**Function: read_syx_file()**

**Signature:**
```python
read_syx_file(filename) -> list
```

**Description:**

Reads binary or plain text SYX file containing System Exclusive messages. Returns list of sysex Message objects. Non-sysex data is ignored.

**Example:**
```python
messages = mido.read_syx_file('patch_data.syx')
for msg in messages:
    print(msg)
```

### 10.2 Writing SYX Files

**Function: write_syx_file()**

**Signature:**
```python
write_syx_file(filename, messages, plaintext=False) -> None
```

**Description:**

Writes SysEx messages to binary or plain text SYX file.

**Parameters:**

- `filename` (str): Output file path
- `messages` (iterable): SysEx messages to write
- `plaintext` (bool): Write as plain text hex dump if True

**Example:**
```python
messages = [
    mido.Message('sysex', data=[0x7E, 0x00, 0x09, 0x01]),
    mido.Message('sysex', data=[0x7F, 0x00, 0x09, 0x02])
]
mido.write_syx_file('output.syx', messages)
```

## 11. Message String Encoding

### 11.1 Message Format Strings

**Function: format_as_string()**

**Signature:**
```python
format_as_string(message, include_time=True) -> str
```

**Description:**

Formats message as string representation matching Message's repr() output.

**Example:**
```python
msg = mido.Message('note_on', note=60, velocity=64, time=0.5)
s = mido.format_as_string(msg)
# "Message('note_on', channel=0, note=60, velocity=64, time=0.5)"
```

### 11.2 String Parsing

**Function: parse_string()**

**Signature:**
```python
parse_string(string) -> Message
```

**Description:**

Parses string representation back into Message object. Accepts output from format_as_string() or repr().

**Example:**
```python
msg = mido.parse_string("Message('note_on', note=60)")
print(msg.note)  # 60
```

**Function: parse_string_stream()**

**Signature:**
```python
parse_string_stream(string_or_iterable) -> list
```

**Description:**

Parses multiple message strings separated by whitespace. Accepts strings or iterables yielding strings.

**Example:**
```python
text = """
Message('note_on', note=60)
Message('note_off', note=60)
"""
messages = mido.parse_string_stream(text)
```

## 12. Backend System

### 12.1 Backend Selection

**Function: set_backend()**

**Signature:**
```python
set_backend(name=None, api=None, load=False) -> Backend
```

**Description:**

Selects MIDI backend implementation. Must be called before opening ports. Cannot be changed after ports are opened.

**Parameters:**

- `name` (str, optional): Backend module name ('mido.backends.rtmidi', 'mido.backends.portmidi', 'mido.backends.pygame')
- `api` (str, optional): Backend-specific API selection
- `load` (bool): Force reimport of backend module

**Environment Variables:**

`MIDO_BACKEND` can specify default backend.

**Example:**
```python
mido.set_backend('mido.backends.rtmidi')
mido.set_backend('mido.backends.rtmidi', api='UNIX_JACK')
```

**Function: get_backend()**

**Signature:**
```python
get_backend() -> Backend
```

**Description:**

Returns currently active backend object.

### 12.2 Available Backends

#### RtMidi Backend
Default backend providing real-time MIDI I/O. Supports Windows, macOS, and Linux.

**APIs:**
- Windows: Windows MM
- macOS: CoreMIDI
- Linux: ALSA, JACK

**Features:**
- Virtual ports (except Windows)
- Callback support
- Low latency

#### PortMidi Backend
Alternative backend using PortMidi library.

**APIs:**
- Windows: MMSystem
- macOS: CoreMIDI
- Linux: ALSA

**Features:**
- Broad platform support
- Stable, mature codebase
- Higher latency than RtMidi

#### Pygame Backend
Backend using pygame.midi module.

**Features:**
- Useful for pygame-based applications
- Moderate latency
- Limited features compared to RtMidi

### 12.3 Backend Selection Priority

Mido attempts backend selection in this order:
1. `MIDO_BACKEND` environment variable
2. RtMidi (if available)
3. PortMidi (if available)
4. Pygame (if available)

## 13. Utility Functions

### 13.1 Type Checking

**Function: is_meta()**

**Signature:**
```python
is_meta(message) -> bool
```

**Description:**

Returns True if message is a meta message.

### 13.2 Message Lists

**Function: get_message_types()**

**Signature:**
```python
get_message_types() -> list
```

**Description:**

Returns list of all valid message type names.

**Example:**
```python
types = mido.get_message_types()
# ['note_off', 'note_on', 'polytouch', ...]
```

**Function: get_meta_message_types()**

**Signature:**
```python
get_meta_message_types() -> list
```

**Description:**

Returns list of all valid meta message type names.

## 14. Constants

### 14.1 MIDI Limits

```python
# MIDI value ranges (all inclusive)
MIN_CHANNEL = 0
MAX_CHANNEL = 15

MIN_PITCHWHEEL = -8192
MAX_PITCHWHEEL = 8191

MIN_SONGPOS = 0
MAX_SONGPOS = 16383

MIN_DATA = 0
MAX_DATA = 127  # For most data bytes
```

### 14.2 Default Values

```python
DEFAULT_TICKS_PER_BEAT = 480
DEFAULT_TEMPO = 500000  # microseconds per beat (120 BPM)
```

## 15. Advanced Usage Patterns

### 15.1 Real-Time Recording

```python
from mido import Message, MidiFile, MidiTrack, MetaMessage
import time

# Record from MIDI input
mid = MidiFile()
track = MidiTrack()
mid.tracks.append(track)

track.append(MetaMessage('track_name', name='Recorded Track'))
track.append(MetaMessage('set_tempo', tempo=500000))

start_time = None

with mido.open_input() as inport:
    print("Recording... Press Ctrl+C to stop")
    try:
        for msg in inport:
            if start_time is None:
                start_time = time.time()
                elapsed = 0
            else:
                elapsed = time.time() - start_time
            
            # Convert to ticks
            ticks = mido.second2tick(elapsed, mid.ticks_per_beat, 500000)
            msg.time = ticks
            track.append(msg)
            
    except KeyboardInterrupt:
        pass

track.append(MetaMessage('end_of_track'))
mid.save('recorded.mid')
```

### 15.2 MIDI File Editing

```python
# Transpose all notes up one octave
mid = mido.MidiFile('input.mid')

for track in mid.tracks:
    for msg in track:
        if msg.type in ('note_on', 'note_off'):
            msg.note = min(127, msg.note + 12)

mid.save('transposed.mid')
```

### 15.3 Tempo Changes

```python
# Double playback speed by halving tempo
mid = mido.MidiFile('input.mid')

for track in mid.tracks:
    for msg in track:
        if msg.type == 'set_tempo':
            msg.tempo = msg.tempo // 2

mid.save('faster.mid')
```

### 15.4 Message Filtering

```python
# Remove all program changes
mid = mido.MidiFile('input.mid')

for track in mid.tracks:
    track[:] = [msg for msg in track if msg.type != 'program_change']

mid.save('no_program_changes.mid')
```

### 15.5 Custom Port Implementation

```python
from mido.ports import BaseInput, BaseOutput

class CustomInput(BaseInput):
    def _open(self, **kwargs):
        # Initialize hardware/connection
        pass
    
    def _receive(self, block=True):
        # Return Message or None
        pass
    
    def _close(self):
        # Cleanup
        pass

class CustomOutput(BaseOutput):
    def _open(self, **kwargs):
        pass
    
    def _send(self, message):
        # Send message to hardware
        pass
    
    def _close(self):
        pass
```

### 15.6 Network MIDI Bridge

```python
from mido.sockets import PortServer
import mido

# Server side
def run_server():
    with PortServer('localhost', 8080) as server:
        with mido.open_input() as inport:
            # Forward local input to network clients
            for msg in inport:
                print(f"Forwarding: {msg}")
                server.send(msg)

# Client side
def run_client():
    from mido.sockets import SocketPort
    
    with SocketPort('localhost', 8080) as port:
        with mido.open_output() as outport:
            # Receive from network and play locally
            for msg in port:
                print(f"Received: {msg}")
                outport.send(msg)
```

### 15.7 Multi-Source Aggregation

```python
from mido.ports import MultiPort

# Combine multiple input sources
with mido.open_input('Keyboard') as kb, \
     mido.open_input('Drum Pad') as dp, \
     mido.open_input('Controller') as ctrl:
    
    multi = MultiPort([kb, dp, ctrl], yield_ports=True)
    
    for port, msg in multi:
        print(f"{port.name}: {msg}")
```

## 16. Performance Considerations

### 16.1 Message Creation Overhead

Message validation occurs at creation time. For performance-critical code processing many messages:

```python
# Faster: reuse and modify messages
base_msg = mido.Message('note_on', velocity=64)
for note in range(60, 73):
    msg = base_msg.copy(note=note)
    outport.send(msg)

# Slower: create new messages each time
for note in range(60, 73):
    msg = mido.Message('note_on', note=note, velocity=64)
    outport.send(msg)
```

### 16.2 File I/O Optimization

For large MIDI files, consider:

```python
# Load only essential data
mid = mido.MidiFile('large_file.mid')

# Process tracks iteratively
for i, track in enumerate(mid.tracks):
    print(f"Processing track {i}")
    # Process messages
    del track[:]  # Free memory if track no longer needed
```

### 16.3 Port Buffer Management

Backend buffer sizes affect latency and reliability:

```python
# RtMidi allows buffer size specification
inport = mido.open_input(buffer_size=1024)
```

## 17. Error Handling

### 17.1 Common Exceptions

**IOError**: Port opening failures, file access errors

**ValueError**: Invalid message parameters, out-of-range values

**OSError**: System-level MIDI errors

**EOFError**: Incomplete MIDI file data

### 17.2 Exception Handling Patterns

```python
import mido

try:
    inport = mido.open_input('NonexistentPort')
except IOError as e:
    print(f"Failed to open port: {e}")
    # Fallback to default
    inport = mido.open_input()

try:
    msg = mido.Message('note_on', note=200)  # Out of range
except ValueError as e:
    print(f"Invalid message: {e}")

try:
    mid = mido.MidiFile('corrupted.mid')
except (IOError, EOFError) as e:
    print(f"Failed to read MIDI file: {e}")
```

## 18. Best Practices

### 18.1 Resource Management

Always use context managers for automatic cleanup:

```python
# Good
with mido.open_input() as inport:
    for msg in inport:
        process(msg)

# Acceptable with explicit cleanup
inport = mido.open_input()
try:
    for msg in inport:
        process(msg)
finally:
    inport.close()
```

### 18.2 Message Time Handling

Understand time attribute context:

- **MIDI files**: Delta time in ticks
- **Port iteration**: Delta time in seconds  
- **Manual creation**: Application-defined

```python
# File context: time in ticks
track.append(mido.Message('note_on', note=60, time=480))

# Port context: time in seconds
for msg in mid.play():
    time.sleep(msg.time)
    outport.send(msg)
```

### 18.3 Channel Indexing

Remember channels are 0-15 internally:

```python
# Internal representation
msg = mido.Message('note_on', channel=0)  # Channel 1 in UI

# Display to user
user_channel = msg.channel + 1
print(f"Channel {user_channel}")

# Parse from user
msg.channel = user_channel - 1
```

### 18.4 File Type Selection

Choose appropriate MIDI file type:

- **Type 0**: Simple songs, single instrument
- **Type 1**: Multi-track projects, separate instruments
- **Type 2**: Rare; avoid unless specifically needed

### 18.5 Tempo Management

Handle tempo properly in MIDI files:

```python
# Always specify initial tempo
track = mido.MidiTrack()
track.append(mido.MetaMessage('set_tempo', tempo=mido.bpm2tempo(120)))

# Track tempo changes during playback
current_tempo = 500000
for msg in track:
    if msg.type == 'set_tempo':
        current_tempo = msg.tempo
```

## 19. Debugging

### 19.1 Message Inspection

```python
# Full message details
print(repr(msg))

# Hexadecimal bytes
print(msg.hex())

# Individual attributes
for attr in ['type', 'channel', 'note', 'velocity', 'time']:
    if hasattr(msg, attr):
        print(f"{attr}: {getattr(msg, attr)}")
```

### 19.2 Port Monitoring

```python
# Log all messages with timestamps
import time

with mido.open_input() as inport:
    for msg in inport:
        timestamp = time.time()
        print(f"[{timestamp:.3f}] {msg}")
```

### 19.3 File Validation

```python
# Verify file structure
mid = mido.MidiFile('test.mid')
print(f"Type: {mid.type}")
print(f"Ticks per beat: {mid.ticks_per_beat}")
print(f"Duration: {mid.length:.2f} seconds")
print(f"Tracks: {len(mid.tracks)}")

for i, track in enumerate(mid.tracks):
    print(f"\nTrack {i}: {track.name}")
    print(f"  Messages: {len(track)}")
    
    # Check for required end marker
    if not track or track[-1].type != 'end_of_track':
        print("  WARNING: Missing end_of_track")
```

## 20. Migration Guide

### 20.1 From pygame.midi

```python
# pygame.midi
pygame.midi.init()
inport = pygame.midi.Input(device_id)
events = inport.read(10)

# mido
inport = mido.open_input()
messages = list(inport.iter_pending())
```

### 20.2 From python-rtmidi

```python
# python-rtmidi
midiin = rtmidi.MidiIn()
midiin.open_port(0)

# mido
inport = mido.open_input()
```

### 20.3 From music21

```python
# music21 MIDI export
stream.write('midi', 'output.mid')

# mido creation
mid = mido.MidiFile()
track = mido.MidiTrack()
mid.tracks.append(track)
# Add messages...
mid.save('output.mid')
```

## 21. Troubleshooting

### 21.1 No Ports Available

**Symptoms**: Empty lists from get_input_names() or get_output_names()

**Solutions**:
- Verify MIDI hardware is connected
- Check system MIDI settings
- Try different backend: `mido.set_backend('mido.backends.portmidi')`
- On Linux, verify user is in audio group: `sudo usermod -a -G audio $USER`

### 21.2 Permission Denied

**Symptoms**: IOError when opening ports

**Solutions**:
- Linux: Add user to audio group
- macOS: Grant MIDI permissions in System Preferences
- Windows: Check device manager for driver issues

### 21.3 High Latency

**Symptoms**: Delayed message processing

**Solutions**:
- Use RtMidi backend for lowest latency
- Reduce buffer sizes
- Check system load and audio settings
- On Linux, consider JACK for professional audio

### 21.4 File Parsing Errors

**Symptoms**: EOFError or corrupted data when reading MIDI files

**Solutions**:
- Verify file is valid MIDI format
- Try clip=True parameter to handle out-of-range values
- Check for incomplete file download
- Test with different MIDI software

---
