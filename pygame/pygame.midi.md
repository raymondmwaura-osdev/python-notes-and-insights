# pygame.midi: Comprehensive Reference Documentation

## Abstract

This document provides comprehensive technical reference material for the `pygame.midi` module, which facilitates Musical Instrument Digital Interface (MIDI) input and output operations within the pygame framework. The module enables developers to interface with both physical and virtual MIDI devices, supporting real-time MIDI message transmission and reception through the PortMidi library. This documentation encompasses all module functions, classes, methods, and properties, along with detailed architectural information and platform-specific considerations.

## 1. Introduction

### 1.1 Module Overview

The `pygame.midi` module represents pygame's interface to MIDI functionality, providing comprehensive support for MIDI input and output operations. First introduced in pygame version 1.9.0, this module enables applications to communicate with MIDI hardware and software synthesizers, facilitating music production, interactive musical applications, and MIDI-based control systems.

### 1.2 Architectural Foundation

The module is built upon the PortMidi library, a cross-platform MIDI I/O library that abstracts platform-specific MIDI APIs. Currently, pygame utilizes pyportmidi bindings (included with pygame distributions), though future versions may implement direct bindings. This architecture ensures portability across Windows, Mac OS X, and Linux platforms, each utilizing platform-specific MIDI subsystems:

- **Windows**: MMSystem API
- **Mac OS X**: CoreMIDI framework
- **Linux**: ALSA (Advanced Linux Sound Architecture)

### 1.3 MIDI Protocol Context

MIDI (Musical Instrument Digital Interface) is a technical standard that describes a communications protocol, digital interface, and electrical connectors for electronic musical instruments and related devices. MIDI messages consist of status bytes (indicating message type) and data bytes (containing message parameters). The pygame.midi module provides high-level abstractions for common MIDI operations while also supporting raw MIDI message transmission.

### 1.4 Device Architecture

MIDI devices are identified by integer device IDs, ranging from 0 to `get_count() - 1`. Devices may function as input sources, output destinations, or both. The module maintains metadata for each device, including interface type, device name, and current operational state.

## 2. Module-Level Functions

### 2.1 pygame.midi.init()

**Signature:**
```python
init() -> None
```

**Description:**

This function initializes the pygame.midi module, establishing connections to the underlying PortMidi library and enumerating available MIDI devices. Initialization is mandatory prior to utilizing any other midi module functionality.

**Behavior:**

The function is idempotent; multiple invocations are safe and will not result in error conditions or resource conflicts. Upon initialization, the internal PortMidi timer is reset to zero.

**Example:**
```python
import pygame.midi

pygame.midi.init()
# Module is now ready for use
```

### 2.2 pygame.midi.quit()

**Signature:**
```python
quit() -> None
```

**Description:**

This function uninitializes the pygame.midi module, releasing system resources and closing connections to MIDI devices. If `init()` was invoked, this function will be called automatically upon program termination through the atexit mechanism.

**Behavior:**

The function is idempotent and safe to call multiple times. It attempts to close all open MIDI streams before releasing resources.

**Platform Considerations:**

Stream closure is particularly challenging on Windows platforms due to MMSystem API limitations. Developers should explicitly close streams when possible rather than relying solely on automatic cleanup.

**Example:**
```python
pygame.midi.quit()
# MIDI module resources released
```

### 2.3 pygame.midi.get_init()

**Signature:**
```python
get_init() -> bool
```

**Description:**

This function queries the initialization state of the pygame.midi module.

**Returns:**

Boolean value: `True` if the module is currently initialized, `False` otherwise.

**Version History:**

New in pygame 1.9.5.

**Example:**
```python
if pygame.midi.get_init():
    print("MIDI module is initialized")
else:
    pygame.midi.init()
```

### 2.4 pygame.midi.get_count()

**Signature:**
```python
get_count() -> int
```

**Description:**

This function returns the total number of MIDI devices available on the system.

**Returns:**

Integer representing the count of available MIDI devices. Device IDs range from 0 to `get_count() - 1`.

**Example:**
```python
device_count = pygame.midi.get_count()
print(f"Available MIDI devices: {device_count}")
```

### 2.5 pygame.midi.get_default_input_id()

**Signature:**
```python
get_default_input_id() -> int
```

**Description:**

This function retrieves the device ID of the default MIDI input device as configured by the operating system or user environment.

**Returns:**

Integer device ID, or -1 if no input devices are available.

**Device Selection Mechanism:**

The default device is determined through a hierarchical process:

1. **Environment Variable**: On Windows, the `PM_RECOMMENDED_INPUT_DEVICE` environment variable may specify a device number.
2. **Registry (Windows)**: If the environment variable is not found, the system queries the Windows registry at `HKEY_LOCAL_MACHINE/SOFTWARE/PortMidi/Recommended_Input_Device`.
3. **Substring Matching**: The registry value is treated as a substring to match against device names or interfaces. Format: `"interface, device name"` (e.g., `"MMSystem, In USB MidiSport 1x1"`).
4. **Default Fallback**: If no configuration is found, the first available input device (lowest device ID) is selected.

**Example:**
```python
default_input = pygame.midi.get_default_input_id()
if default_input != -1:
    midi_in = pygame.midi.Input(default_input)
```

### 2.6 pygame.midi.get_default_output_id()

**Signature:**
```python
get_default_output_id() -> int
```

**Description:**

This function retrieves the device ID of the default MIDI output device. The selection mechanism is identical to `get_default_input_id()`, utilizing the `PM_RECOMMENDED_OUTPUT_DEVICE` environment variable or corresponding registry entry.

**Returns:**

Integer device ID, or -1 if no output devices are available.

**Example:**
```python
default_output = pygame.midi.get_default_output_id()
if default_output != -1:
    midi_out = pygame.midi.Output(default_output)
```

### 2.7 pygame.midi.get_device_info()

**Signature:**
```python
get_device_info(an_id) -> tuple | None
```

**Description:**

This function retrieves comprehensive metadata for a specified MIDI device.

**Parameters:**

- `an_id` (int): Device ID to query

**Returns:**

If the device ID is valid, returns a tuple: `(interf, name, input, output, opened)`

- `interf` (str): Interface name (e.g., "ALSA", "CoreMIDI", "MMSystem")
- `name` (str): Human-readable device name (e.g., "Midi Through Port-0")
- `input` (int): 1 if device supports input, 0 otherwise
- `output` (int): 1 if device supports output, 0 otherwise
- `opened` (int): 1 if device is currently opened, 0 otherwise

Returns `None` if the device ID is out of range.

**Example:**
```python
for device_id in range(pygame.midi.get_count()):
    info = pygame.midi.get_device_info(device_id)
    if info:
        interf, name, is_input, is_output, opened = info
        direction = []
        if is_input:
            direction.append("input")
        if is_output:
            direction.append("output")
        
        print(f"Device {device_id}: {name.decode()} "
              f"[{interf.decode()}] ({', '.join(direction)})")
```

### 2.8 pygame.midi.midis2events()

**Signature:**
```python
midis2events(midi_events, device_id) -> list
```

**Description:**

This function converts a sequence of MIDI events into pygame event objects, facilitating integration of MIDI input with pygame's event handling system.

**Parameters:**

- `midi_events` (sequence): Sequence of MIDI events in the format `[((status, data1, data2, data3), timestamp), ...]`
- `device_id` (int): MIDI device ID to associate with the generated events

**Returns:**

List of pygame Event objects with event type `MIDIIN`.

**Example:**
```python
midi_events = [
    ((0x90, 60, 100, 0), 1000),  # Note On, Middle C, velocity 100
    ((0x80, 60, 0, 0), 1500)      # Note Off, Middle C
]
pygame_events = pygame.midi.midis2events(midi_events, device_id=0)
```

### 2.9 pygame.midi.time()

**Signature:**
```python
time() -> int
```

**Description:**

This function retrieves the current time from the PortMidi internal timer.

**Returns:**

Integer representing elapsed milliseconds since the pygame.midi module was initialized. The timer resets to 0 upon each call to `init()`.

**Usage:**

This timer is essential for timestamp synchronization in MIDI output operations, particularly when utilizing latency-compensated playback.

**Example:**
```python
current_time = pygame.midi.time()
# Schedule a note for 1 second from now
future_time = current_time + 1000
midi_out.write([[[0x90, 60, 100], future_time]])
```

### 2.10 pygame.midi.frequency_to_midi()

**Signature:**
```python
frequency_to_midi(frequency) -> int
```

**Description:**

This function converts a frequency value in Hertz to the nearest MIDI note number using equal temperament tuning (A440 standard).

**Parameters:**

- `frequency` (float): Frequency in Hertz

**Returns:**

Integer MIDI note number (0-127). Values are rounded to the nearest semitone.

**Version History:**

New in pygame 1.9.5.

**Example:**
```python
# A0 = 27.5 Hz
note = pygame.midi.frequency_to_midi(27.5)
print(note)  # Output: 21
```

### 2.11 pygame.midi.midi_to_frequency()

**Signature:**
```python
midi_to_frequency(midi_note) -> float
```

**Description:**

This function converts a MIDI note number to its corresponding frequency in Hertz using equal temperament tuning (A440 standard).

**Parameters:**

- `midi_note` (int): MIDI note number (0-127)

**Returns:**

Float representing frequency in Hertz.

**Version History:**

New in pygame 1.9.5.

**Example:**
```python
# MIDI note 21 = A0
freq = pygame.midi.midi_to_frequency(21)
print(freq)  # Output: 27.5
```

### 2.12 pygame.midi.midi_to_ansi_note()

**Signature:**
```python
midi_to_ansi_note(midi_note) -> str
```

**Description:**

This function converts a MIDI note number to its ANSI note name representation (e.g., "C4", "A#5").

**Parameters:**

- `midi_note` (int): MIDI note number (0-127)

**Returns:**

String representing the ANSI note name with octave designation.

**Version History:**

New in pygame 1.9.5.

**Example:**
```python
note_name = pygame.midi.midi_to_ansi_note(21)
print(note_name)  # Output: "A0"

middle_c = pygame.midi.midi_to_ansi_note(60)
print(middle_c)  # Output: "C4"
```

## 3. Input Class

### 3.1 Class Definition

**Signature:**
```python
Input(device_id) -> Input
Input(device_id, buffer_size) -> Input
```

**Description:**

The Input class provides an interface for receiving MIDI data from input devices. It maintains an internal buffer for incoming MIDI messages and provides methods for polling and reading MIDI events.

**Parameters:**

- `device_id` (int): MIDI device ID for the input device
- `buffer_size` (int, optional): Number of input events to buffer

**Usage:**

Input objects should be properly closed when no longer needed to ensure system resources are released.

### 3.2 Input.close()

**Signature:**
```python
close() -> None
```

**Description:**

This method closes the MIDI input stream, flushing any pending buffers and releasing associated resources.

**Behavior:**

PortMidi attempts to close streams automatically upon application termination, though this is particularly challenging on Windows platforms. Explicit closure is recommended.

**Example:**
```python
midi_in = pygame.midi.Input(device_id)
# Use the input device
midi_in.close()
```

### 3.3 Input.poll()

**Signature:**
```python
poll() -> bool
```

**Description:**

This method checks whether MIDI data is available in the input buffer without consuming it.

**Returns:**

Boolean: `True` if data is available, `False` otherwise.

**Exceptions:**

Raises `MidiException` on error conditions.

**Example:**
```python
if midi_in.poll():
    events = midi_in.read(10)
    # Process events
```

### 3.4 Input.read()

**Signature:**
```python
read(num_events) -> list
```

**Description:**

This method reads MIDI events from the input buffer.

**Parameters:**

- `num_events` (int): Maximum number of events to read

**Returns:**

List of MIDI events in the format: `[[[status, data1, data2, data3], timestamp], ...]`

Each event consists of:
- MIDI message: `[status, data1, data2, data3]`
- `timestamp`: Integer representing milliseconds from PortMidi timer

**Example:**
```python
events = midi_in.read(32)
for event in events:
    midi_data, timestamp = event
    status, data1, data2, data3 = midi_data
    print(f"Status: {status:02X}, Data: {data1}, {data2}, {data3}, Time: {timestamp}")
```

## 4. Output Class

### 4.1 Class Definition

**Signature:**
```python
Output(device_id) -> Output
Output(device_id, latency=0) -> Output
Output(device_id, buffer_size=256) -> Output
Output(device_id, latency, buffer_size) -> Output
```

**Description:**

The Output class provides an interface for transmitting MIDI data to output devices. It supports buffered output with optional latency compensation for precise timing control.

**Parameters:**

- `device_id` (int): MIDI device ID for the output device
- `latency` (int, optional, default=0): Latency in milliseconds for timestamp-based scheduling
- `buffer_size` (int, optional, default=256): Number of output events to buffer

**Latency Behavior:**

- **latency = 0**: Timestamps are ignored; all output is delivered immediately
- **latency > 0**: Output is delayed until message timestamp plus latency value
- **latency < 0**: Treated as 0

**Timing Considerations:**

Timestamps are absolute values from the PortMidi timer, not relative delays. The latency parameter helps synchronize MIDI output with audio playback by compensating for system timing variations.

### 4.2 Output.abort()

**Signature:**
```python
abort() -> None
```

**Description:**

This method immediately terminates all outgoing MIDI messages, potentially resulting in transmission of partial messages.

**Behavior:**

The output port should be closed immediately after calling this method. This function is intended for emergency shutdown scenarios. There is no equivalent abort function for Input devices, as applications can simply ignore buffered input messages.

**Example:**
```python
midi_out.abort()
midi_out.close()
```

### 4.3 Output.close()

**Signature:**
```python
close() -> None
```

**Description:**

This method closes the MIDI output stream, flushing pending buffers and releasing associated resources.

**Example:**
```python
midi_out = pygame.midi.Output(device_id)
# Use the output device
midi_out.close()
```

### 4.4 Output.note_on()

**Signature:**
```python
note_on(note, velocity=None, channel=0) -> None
```

**Description:**

This method sends a MIDI Note On message to the output stream.

**Parameters:**

- `note` (int): MIDI note number (0-127)
- `velocity` (int, optional): Note velocity/volume (0-127). If None, a default value is used.
- `channel` (int, default=0): MIDI channel (0-15)

**Preconditions:**

The specified note must be in the "off" state for correct operation.

**Example:**
```python
# Play middle C (MIDI note 60) at full velocity on channel 0
midi_out.note_on(60, 127, 0)
```

### 4.5 Output.note_off()

**Signature:**
```python
note_off(note, velocity=None, channel=0) -> None
```

**Description:**

This method sends a MIDI Note Off message to the output stream.

**Parameters:**

- `note` (int): MIDI note number (0-127)
- `velocity` (int, optional): Release velocity (0-127). If None, a default value is used.
- `channel` (int, default=0): MIDI channel (0-15)

**Preconditions:**

The specified note must be in the "on" state for correct operation.

**Example:**
```python
# Stop middle C on channel 0
midi_out.note_off(60, 0, 0)
```

### 4.6 Output.set_instrument()

**Signature:**
```python
set_instrument(instrument_id, channel=0) -> None
```

**Description:**

This method sends a MIDI Program Change message to select an instrument (also known as a "patch" or "preset").

**Parameters:**

- `instrument_id` (int): Instrument number (0-127) conforming to General MIDI specification
- `channel` (int, default=0): MIDI channel (0-15)

**General MIDI Instruments:**

Standard General MIDI instrument assignments include:
- 0: Acoustic Grand Piano
- 19: Church Organ
- 40: Violin
- 56: Trumpet
- 128-255: Percussion instruments (channel 9/10)

**Example:**
```python
GRAND_PIANO = 0
CHURCH_ORGAN = 19
midi_out.set_instrument(GRAND_PIANO, channel=0)
```

### 4.7 Output.pitch_bend()

**Signature:**
```python
pitch_bend(value=0, channel=0) -> None
```

**Description:**

This method adjusts the pitch of all notes on the specified channel.

**Parameters:**

- `value` (int, default=0): Pitch bend value (-8192 to +8191)
  - 0: No pitch change
  - +4096: Typically one semitone higher
  - -8192: Typically one whole tone lower
  - Actual musical intervals depend on synthesizer configuration
- `channel` (int, default=0): MIDI channel (0-15)

**Version History:**

New in pygame 1.9.4.

**Example:**
```python
# Bend pitch up by a semitone
midi_out.pitch_bend(4096, channel=0)

# Return to normal pitch
midi_out.pitch_bend(0, channel=0)
```

### 4.8 Output.write()

**Signature:**
```python
write(data) -> None
```

**Description:**

This method transmits a sequence of MIDI messages to the output device.

**Parameters:**

- `data` (list): MIDI message data in the format:
  `[[[status, data1=0, data2=0, ...], timestamp], ...]`
  
  Data bytes are optional and default to 0 if omitted.

**Exceptions:**

Raises `IndexError` if the data list contains more than 1024 elements.

**Timing Behavior:**

- If latency = 0, timestamps are ignored
- For immediate playback, use timestamps from `pygame.midi.time()`

**Example:**
```python
# Program change at time 20000, then note on at 20500
midi_out.write([
    [[0xC0, 0, 0], 20000],      # Program change
    [[0x90, 60, 100], 20500]    # Note on
])

# Equivalent with optional data fields omitted:
midi_out.write([
    [[0xC0], 20000],
    [[0x90, 60, 100], 20500]
])
```

### 4.9 Output.write_short()

**Signature:**
```python
write_short(status) -> None
write_short(status, data1=0, data2=0) -> None
```

**Description:**

This method transmits MIDI messages consisting of three bytes or fewer.

**Parameters:**

- `status` (int): MIDI status byte (e.g., 0x90 for Note On, 0xC0 for Program Change)
- `data1` (int, optional, default=0): First data byte
- `data2` (int, optional, default=0): Second data byte

**Status Byte Examples:**

- `0x90`: Note On
- `0x80`: Note Off
- `0xC0`: Program Change
- `0xE0`: Pitch Bend

**Example:**
```python
# Note 65 on with velocity 100
midi_out.write_short(0x90, 65, 100)

# Program change to instrument 5
midi_out.write_short(0xC0, 5)
```

### 4.10 Output.write_sys_ex()

**Signature:**
```python
write_sys_ex(when, msg) -> None
```

**Description:**

This method transmits a timestamped System Exclusive (SysEx) MIDI message, used for manufacturer-specific commands and bulk data transfers.

**Parameters:**

- `when` (int): Timestamp in milliseconds
- `msg` (list[int] or str): SysEx message data

**SysEx Message Format:**

SysEx messages begin with 0xF0 and terminate with 0xF7. The enclosed data is manufacturer-specific.

**Example:**
```python
# Using byte string
midi_out.write_sys_ex(0, '\xF0\x7D\x10\x11\x12\x13\xF7')

# Equivalent using list notation
midi_out.write_sys_ex(
    pygame.midi.time(),
    [0xF0, 0x7D, 0x10, 0x11, 0x12, 0x13, 0xF7]
)
```

## 5. Event Types

### 5.1 MIDIIN

**Description:**

pygame event type reserved for MIDI input. This event type is generated by `pygame.midi.midis2events()` when converting MIDI events to pygame events.

**Usage:**

Applications can handle MIDIIN events within pygame's standard event loop, enabling seamless integration of MIDI input with keyboard, mouse, and other input sources.

**Example:**
```python
for event in pygame.event.get():
    if event.type == pygame.MIDIIN:
        # Handle MIDI input event
        print(f"MIDI event received: {event}")
```

### 5.2 MIDIOUT

**Description:**

pygame event type reserved for MIDI output operations. This event type is currently defined but not actively utilized by the module's standard functions.

## 6. Exception Classes

### 6.1 pygame.midi.MidiException

**Signature:**
```python
MidiException(errno) -> None
```

**Description:**

Exception class raised by pygame.midi functions and classes when errors occur during MIDI operations.

**Parameters:**

- `errno` (int): Error code indicating the nature of the failure

**Usage:**

Applications should implement exception handling for MIDI operations, particularly for device access operations which may fail due to permission issues or device unavailability.

**Example:**
```python
try:
    midi_in = pygame.midi.Input(device_id)
except pygame.midi.MidiException as e:
    print(f"Failed to open MIDI input: {e}")
```

## 7. Usage Patterns and Best Practices

### 7.1 Initialization and Cleanup

Proper initialization and cleanup sequences are essential for reliable MIDI operation:

```python
import pygame.midi

def midi_application():
    pygame.midi.init()
    try:
        # MIDI operations
        midi_out = pygame.midi.Output(pygame.midi.get_default_output_id())
        try:
            # Use midi_out
            midi_out.note_on(60, 127)
            pygame.time.wait(1000)
            midi_out.note_off(60, 127)
        finally:
            midi_out.close()
    finally:
        pygame.midi.quit()
```

### 7.2 Device Enumeration

Before opening MIDI devices, applications should enumerate available devices and present options to users:

```python
pygame.midi.init()

print("Available MIDI devices:")
for i in range(pygame.midi.get_count()):
    info = pygame.midi.get_device_info(i)
    if info:
        interf, name, is_input, is_output, opened = info
        device_type = []
        if is_input:
            device_type.append("INPUT")
        if is_output:
            device_type.append("OUTPUT")
        
        print(f"{i}: {name.decode()} [{', '.join(device_type)}]")
```

### 7.3 Latency Management

For applications requiring precise timing (e.g., music sequencers), utilize latency compensation:

```python
# Create output with 20ms latency
midi_out = pygame.midi.Output(device_id, latency=20)

# Schedule notes with precise timing
base_time = pygame.midi.time()
quarter_note = 500  # milliseconds

midi_out.write([
    [[0x90, 60, 100], base_time],
    [[0x80, 60, 0], base_time + quarter_note],
    [[0x90, 64, 100], base_time + quarter_note],
    [[0x80, 64, 0], base_time + quarter_note * 2]
])
```

### 7.4 Input Event Processing

Integrate MIDI input with pygame's event system:

```python
def process_midi_input(midi_in):
    if midi_in.poll():
        midi_events = midi_in.read(10)
        pygame_events = pygame.midi.midis2events(midi_events, device_id=0)
        for event in pygame_events:
            pygame.event.post(event)

# In main loop:
while running:
    process_midi_input(midi_in)
    for event in pygame.event.get():
        if event.type == pygame.MIDIIN:
            # Handle MIDI event
            pass
```

### 7.5 Error Handling

Implement comprehensive error handling for production applications:

```python
def safe_midi_operation():
    if not pygame.midi.get_init():
        pygame.midi.init()
    
    device_id = pygame.midi.get_default_output_id()
    if device_id == -1:
        print("No MIDI output devices available")
        return
    
    try:
        midi_out = pygame.midi.Output(device_id)
    except pygame.midi.MidiException as e:
        print(f"Failed to open MIDI device: {e}")
        return
    
    try:
        # MIDI operations
        midi_out.set_instrument(0)
        midi_out.note_on(60, 127)
    except pygame.midi.MidiException as e:
        print(f"MIDI operation failed: {e}")
    finally:
        midi_out.close()
```

## 8. MIDI Message Reference

### 8.1 Channel Voice Messages

These messages affect sound generation on MIDI channels:

| Status Byte | Message Type | Data Bytes | Description |
|------------|--------------|------------|-------------|
| 0x80-0x8F | Note Off | note, velocity | Terminate a playing note |
| 0x90-0x9F | Note On | note, velocity | Begin playing a note |
| 0xA0-0xAF | Polyphonic Aftertouch | note, pressure | Apply pressure to specific note |
| 0xB0-0xBF | Control Change | controller, value | Modify controller parameter |
| 0xC0-0xCF | Program Change | program | Select instrument |
| 0xD0-0xDF | Channel Aftertouch | pressure | Apply pressure to all notes |
| 0xE0-0xEF | Pitch Bend | LSB, MSB | Adjust pitch of all notes |

Note: The lower 4 bits of the status byte specify the MIDI channel (0-15).

### 8.2 System Messages

| Status Byte | Message Type | Description |
|------------|--------------|-------------|
| 0xF0 | System Exclusive | Manufacturer-specific data stream |
| 0xF1 | MIDI Time Code | Synchronization data |
| 0xF2 | Song Position Pointer | Position in MIDI sequence |
| 0xF3 | Song Select | Select MIDI sequence |
| 0xF6 | Tune Request | Request analog oscillator tuning |
| 0xF7 | End of SysEx | Terminates SysEx message |
| 0xF8 | Timing Clock | Synchronization pulse |
| 0xFA | Start | Begin sequence playback |
| 0xFB | Continue | Resume sequence playback |
| 0xFC | Stop | Halt sequence playback |
| 0xFE | Active Sensing | Verify connection integrity |
| 0xFF | System Reset | Reset all devices |

## 9. Platform-Specific Considerations

### 9.1 Windows

**Interface:** MMSystem (Multimedia System Services)

**Characteristics:**
- Limited to MMSystem API capabilities
- Stream closure is particularly challenging
- Default device configuration via environment variables or registry
- Registry path: `HKEY_LOCAL_MACHINE/SOFTWARE/PortMidi/`

**Recommendations:**
- Explicitly close streams rather than relying on automatic cleanup
- Test thoroughly with target MIDI hardware
- Consider Windows security policies that may restrict MIDI device access

### 9.2 macOS

**Interface:** CoreMIDI

**Characteristics:**
- Native integration with macOS audio architecture
- Robust device detection and enumeration
- Support for virtual MIDI devices via IAC Driver

**Recommendations:**
- Verify CoreMIDI permissions in system preferences
- Test with both hardware and virtual MIDI devices
- Consider latency implications of CoreMIDI architecture

### 9.3 Linux

**Interface:** ALSA (Advanced Linux Sound Architecture)

**Characteristics:**
- Direct ALSA sequencer integration
- Support for JACK virtual MIDI connections
- Flexible routing through `aconnect` and similar tools

**Recommendations:**
- Ensure ALSA development packages are installed
- Consider JACK for low-latency professional applications
- Test device enumeration with various hardware configurations

## 10. Performance Considerations

### 10.1 Buffering Strategy

Appropriate buffer sizing impacts both latency and reliability:

- **Small buffers** (32-128 events): Lower latency, higher CPU overhead
- **Medium buffers** (256-512 events): Balanced performance for most applications
- **Large buffers** (1024+ events): Higher latency, better for high-throughput scenarios

### 10.2 Polling vs. Event-Driven Architecture

**Polling Approach:**
```python
# Check input periodically
if midi_in.poll():
    events = midi_in.read(32)
```

**Event-Driven Approach:**
```python
# Convert to pygame events for integration with event loop
midi_events = midi_in.read(32)
pygame_events = pygame.midi.midis2events(midi_events, device_id)
for event in pygame_events:
    pygame.event.post(event)
```

Event-driven architectures integrate more naturally with pygame's design and reduce polling overhead.

### 10.3 Timing Precision

MIDI timing precision depends on multiple factors:

- **PortMidi Timer**: Provides millisecond resolution
- **System Scheduling**: Operating system scheduling granularity affects actual timing
- **Hardware Latency**: MIDI interface hardware introduces variable latency
- **Latency Compensation**: The Output latency parameter helps compensate for system timing variations

For critical timing applications, consider:
- Using dedicated real-time operating systems
- Minimizing system load during MIDI operations
- Profiling actual timing performance with target hardware

## 11. Advanced Topics

### 11.1 System Exclusive Messages

System Exclusive (SysEx) messages enable manufacturer-specific functionality:

```python
# Roland GS Reset
roland_gs_reset = [0xF0, 0x41, 0x10, 0x42, 0x12, 0x40, 0x00, 0x7F, 0x00, 0x41, 0xF7]
midi_out.write_sys_ex(pygame.midi.time(), roland_gs_reset)

# Yamaha XG Reset
yamaha_xg_reset = [0xF0, 0x43, 0x10, 0x4C, 0x00, 0x00, 0x7E, 0x00, 0xF7]
midi_out.write_sys_ex(pygame.midi.time(), yamaha_xg_reset)
```

### 11.2 MIDI File Playback

While pygame.midi does not directly support Standard MIDI File (SMF) playback, applications can parse MIDI files and use the module for playback:

```python
# Pseudocode for MIDI file playback
def play_midi_file(filename, midi_out):
    events = parse_midi_file(filename)  # External parser required
    start_time = pygame.midi.time()
    
    for event in events:
        midi_msg, delta_time = event
        current_time = pygame.midi.time()
        target_time = start_time + delta_time
        
        # Wait until target time
        while pygame.midi.time() < target_time:
            pygame.time.wait(1)
        
        midi_out.write([midi_msg])
```

### 11.3 Virtual MIDI Devices

Creating virtual MIDI connections varies by platform:

**macOS:**
- Use Audio MIDI Setup application
- Create IAC Driver buses for inter-application MIDI routing

**Linux:**
- Use ALSA virtual MIDI devices
- `aconnect` utility for routing
- JACK for professional audio/MIDI routing

**Windows:**
- Third-party virtual MIDI drivers (e.g., loopMIDI, MIDI Yoke)

### 11.4 Multi-threaded MIDI Operations

For complex applications, MIDI operations may occur on separate threads:

```python
import threading

class MidiInputThread(threading.Thread):
    def __init__(self, device_id):
        super().__init__()
        self.device_id = device_id
        self.running = False
        self.midi_in = None
    
    def run(self):
        pygame.midi.init()
        self.midi_in = pygame.midi.Input(self.device_id)
        self.running = True
        
        while self.running:
            if self.midi_in.poll():
                events = self.midi_in.read(10)
                # Process events
                self.handle_events(events)
            pygame.time.wait(10)
        
        self.midi_in.close()
        pygame.midi.quit()
    
    def stop(self):
        self.running = False
    
    def handle_events(self, events):
        # Event processing logic
        pass
```

**Thread Safety Considerations:**
- PortMidi is generally not thread-safe
- Use separate Input/Output instances per thread
- Implement proper synchronization for shared resources

## 12. Troubleshooting

### 12.1 No Devices Found

**Symptoms:** `get_count()` returns 0

**Solutions:**
- Verify MIDI hardware is connected and powered
- Check system device manager / audio MIDI setup
- Ensure proper drivers are installed
- Verify pygame.midi module is initialized
- Check system permissions for MIDI access

### 12.2 Device Access Errors

**Symptoms:** MidiException when opening devices

**Solutions:**
- Verify device is not already opened by another application
- Check device permissions (particularly on Linux)
- Ensure device_id is valid (0 to get_count()-1)
- Try closing and reopening the device

### 12.3 Timing Issues

**Symptoms:** Irregular timing, jitter, or delayed messages

**Solutions:**
- Use latency parameter in Output constructor
- Reduce system load during critical operations
- Use smaller buffer sizes for lower latency
- Consider system timer resolution limitations
- Profile actual timing with diagnostic tools

### 12.4 Windows-Specific Issues

**Symptoms:** Streams fail to close, resource leaks

**Solutions:**
- Always explicitly close streams
- Call quit() before program termination
- Use context managers or try-finally blocks
- Restart application if streams become corrupted

## 13. Code Examples

### 13.1 Complete MIDI Monitor

```python
import pygame
import pygame.midi

def print_devices():
    pygame.midi.init()
    count = pygame.midi.get_count()
    print(f"\nFound {count} MIDI devices:")
    
    for i in range(count):
        info = pygame.midi.get_device_info(i)
        if info:
            interf, name, is_input, is_output, opened = info
            io_type = []
            if is_input:
                io_type.append("INPUT")
            if is_output:
                io_type.append("OUTPUT")
            status = "OPEN" if opened else "CLOSED"
            print(f"  {i}: {name.decode()} [{', '.join(io_type)}] - {status}")

def monitor_input(device_id):
    pygame.midi.init()
    midi_input = pygame.midi.Input(device_id)
    
    print(f"\nMonitoring MIDI input on device {device_id}")
    print("Press Ctrl+C to stop\n")
    
    try:
        while True:
            if midi_input.poll():
                events = midi_input.read(10)
                for event in events:
                    midi_data, timestamp = event
                    status = midi_data[0]
                    
                    # Decode message type
                    msg_type = status & 0xF0
                    channel = status & 0x0F
                    
                    if msg_type == 0x90:
                        note, velocity = midi_data[1:3]
                        note_name = pygame.midi.midi_to_ansi_note(note)
                        freq = pygame.midi.midi_to_frequency(note)
                        print(f"Note ON:  Ch{channel} {note_name} (#{note}) "
                              f"vel={velocity} freq={freq:.2f}Hz @ {timestamp}ms")
                    elif msg_type == 0x80:
                        note, velocity = midi_data[1:3]
                        note_name = pygame.midi.midi_to_ansi_note(note)
                        print(f"Note OFF: Ch{channel} {note_name} (#{note}) "
                              f"vel={velocity} @ {timestamp}ms")
                    elif msg_type == 0xC0:
                        program = midi_data[1]
                        print(f"Program Change: Ch{channel} program={program} @ {timestamp}ms")
                    else:
                        print(f"MIDI: Status=0x{status:02X} Data={midi_data[1:]} @ {timestamp}ms")
            
            pygame.time.wait(10)
    
    except KeyboardInterrupt:
        print("\nStopping monitor...")
    finally:
        midi_input.close()
        pygame.midi.quit()

if __name__ == "__main__":
    print_devices()
    
    # Monitor default input device
    device_id = pygame.midi.get_default_input_id()
    if device_id >= 0:
        monitor_input(device_id)
    else:
        print("\nNo default input device found")
```

### 13.2 MIDI Synthesizer Test

```python
import pygame
import pygame.midi
import time

def play_scale(midi_out, start_note=60, instrument=0):
    """Play a C major scale"""
    midi_out.set_instrument(instrument)
    
    # C major scale intervals
    scale = [0, 2, 4, 5, 7, 9, 11, 12]
    
    for interval in scale:
        note = start_note + interval
        note_name = pygame.midi.midi_to_ansi_note(note)
        print(f"Playing {note_name}")
        
        midi_out.note_on(note, 100)
        time.sleep(0.5)
        midi_out.note_off(note)
        time.sleep(0.1)

def play_chord(midi_out, root=60, chord_type="major"):
    """Play a chord"""
    if chord_type == "major":
        intervals = [0, 4, 7]  # Major triad
    elif chord_type == "minor":
        intervals = [0, 3, 7]  # Minor triad
    else:
        intervals = [0, 4, 7, 11]  # Major 7th
    
    # Play all notes simultaneously
    for interval in intervals:
        midi_out.note_on(root + interval, 80)
    
    time.sleep(2.0)
    
    # Release all notes
    for interval in intervals:
        midi_out.note_off(root + interval)

def main():
    pygame.midi.init()
    
    # Get output device
    output_id = pygame.midi.get_default_output_id()
    if output_id == -1:
        print("No MIDI output device available")
        return
    
    print(f"Using MIDI output device: {output_id}")
    info = pygame.midi.get_device_info(output_id)
    if info:
        print(f"Device name: {info[1].decode()}")
    
    midi_out = pygame.midi.Output(output_id)
    
    try:
        # Test different instruments
        instruments = {
            0: "Acoustic Grand Piano",
            40: "Violin",
            56: "Trumpet",
            73: "Flute"
        }
        
        for inst_id, inst_name in instruments.items():
            print(f"\nTesting {inst_name}:")
            play_scale(midi_out, start_note=60, instrument=inst_id)
            time.sleep(0.5)
        
        # Test chords
        print("\nTesting chords:")
        midi_out.set_instrument(0)  # Piano
        
        print("C Major")
        play_chord(midi_out, 60, "major")
        time.sleep(0.5)
        
        print("A Minor")
        play_chord(midi_out, 57, "minor")
        time.sleep(0.5)
        
        print("G Major 7")
        play_chord(midi_out, 55, "major7")
        
    finally:
        midi_out.close()
        pygame.midi.quit()

if __name__ == "__main__":
    main()
```

### 13.3 MIDI Echo/Through

```python
import pygame
import pygame.midi

def midi_through(input_id, output_id):
    """Echo MIDI input to output"""
    pygame.midi.init()
    
    midi_in = pygame.midi.Input(input_id)
    midi_out = pygame.midi.Output(output_id, latency=0)
    
    print(f"MIDI Through: Device {input_id} -> Device {output_id}")
    print("Press Ctrl+C to stop")
    
    try:
        while True:
            if midi_in.poll():
                events = midi_in.read(10)
                
                # Forward events to output
                for event in events:
                    midi_data, timestamp = event
                    # Write immediately (ignore timestamp)
                    midi_out.write_short(midi_data[0], 
                                        midi_data[1], 
                                        midi_data[2])
            
            pygame.time.wait(1)  # Minimal latency
    
    except KeyboardInterrupt:
        print("\nStopping MIDI through...")
    finally:
        midi_in.close()
        midi_out.close()
        pygame.midi.quit()

if __name__ == "__main__":
    pygame.midi.init()
    
    input_id = pygame.midi.get_default_input_id()
    output_id = pygame.midi.get_default_output_id()
    
    if input_id >= 0 and output_id >= 0:
        midi_through(input_id, output_id)
    else:
        print("MIDI input or output device not available")
    
    pygame.midi.quit()
```

## 14. Version History Summary

- **pygame 1.9.0**: Initial release of pygame.midi module
- **pygame 1.9.4**: Added `Output.pitch_bend()` method
- **pygame 1.9.5**: Added utility functions:
  - `get_init()`
  - `frequency_to_midi()`
  - `midi_to_frequency()`
  - `midi_to_ansi_note()`
- **pygame 2.0.0**: Added MIDIIN and MIDIOUT event types

---
