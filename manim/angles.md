# Manim Angles Reference

## Default Unit System

Manim utilizes **radians** as the default unit for angular measurements. All angle parameters in functions, methods, and object constructors accept radian values unless explicitly converted.

## Angle Constants

### Primary Constants
- **`PI`**: Equivalent to π (≈3.14159), representing a half-circle rotation
- **`TAU`**: Equivalent to 2π (≈6.28318), representing a full-circle rotation
- **`DEGREES`**: Conversion constant calculated as `TAU / 360`

### Conversion Mechanism
To convert degree measurements to radians, multiply by the `DEGREES` constant:
```python
angle = 45 * DEGREES  # Converts 45° to radians
```

## Coordinate System Orientation

### Zero Point Reference
The zero-degree position (0°) is located at the **positive x-axis**, extending rightward from the origin. This orientation conforms to standard mathematical conventions used in trigonometry and the unit circle.

### Directional Vectors
- **0° (0 radians)**: `RIGHT` – positive x-axis
- **90° (π/2 radians)**: `UP` – positive y-axis
- **180° (π radians)**: `LEFT` – negative x-axis
- **270° (3π/2 radians)**: `DOWN` – negative y-axis

## Rotation Direction Convention

### Standard Rotation
- **Positive angles**: Counterclockwise rotation
- **Negative angles**: Clockwise rotation

This convention applies universally across Manim's rotation functions, geometric constructions, and animation transformations.

### Documentation Example
From the `Transform` class documentation with `path_arc` parameter:
> "Positive angles move counter-clockwise, negative angles move clockwise."

## Angular Parameters in Common Classes

### Arc-Based Geometries

**Arc, ArcBetweenPoints, CurvedArrow, CurvedDoubleArrow**
- `start_angle`: Initial angle position (radians)
- `angle`: Angular span of the arc (radians)
- Default `angle` value: `TAU/4` (π/2, equivalent to 90°)

**Sector, AnnularSector**
- `start_angle`: Starting angle for the sector (radians)
- `angle`: Angular extent of the sector (radians)
- Negative `angle` values produce clockwise-drawn sectors

**Example:**
```python
sector = AnnularSector(
    inner_radius=1.5,
    outer_radius=2,
    angle=45 * DEGREES,
    start_angle=0
)
```

### Angle Measurement Object

**Angle Class**
- Constructs visual angle indicators between two line objects
- `other_angle`: Boolean parameter to select between two possible angles
- `get_value(degrees=True)`: Retrieves angle measurement, optionally in degrees

**Example:**
```python
angle = Angle(line1, line2, radius=0.4)
value = angle.get_value(degrees=True)  # Returns angle in degrees
```

### Rotation Animations

**Rotate Animation**
- `angle`: Rotation magnitude (radians)
- `axis`: Rotation axis vector (default: `[0, 0, 1]` for z-axis)
- Positive angles rotate counterclockwise about the specified axis

**Rotating Animation**
- `radians`: Total rotation amount (radians)
- Continuous rotation animation

**Example:**
```python
self.play(Rotate(square, angle=PI/4))  # 45° counterclockwise
```

### Transform Animations

**Transform, ClockwiseTransform, CounterclockwiseTransform**
- `path_arc`: Angular path for transformation trajectory (radians)
- Positive values create counterclockwise arcs
- Negative values create clockwise arcs

**Example:**
```python
self.play(Transform(obj1, obj2, path_arc=90 * DEGREES))
```

### 3D Camera Orientation

**ThreeDScene Methods**
- `phi`: Polar angle – vertical rotation from the XY-plane (radians)
- `theta`: Azimuthal angle – horizontal rotation about the z-axis (radians)
- `gamma`: Camera roll angle (radians)

**Example:**
```python
self.set_camera_orientation(phi=75 * DEGREES, theta=-45 * DEGREES)
```

**Ambient Camera Rotation**
- `rate`: Rotation speed in radians per second
- Positive rate: counterclockwise rotation
- Negative rate: clockwise rotation

## Polar Coordinate System

### PolarPlane Configuration
- `azimuth_units`: Labeling system for angular coordinates
  - `"PI radians"`: Labels in interval [0, 2π]
  - `"TAU radians"`: Labels in interval [0, τ]
  - `"degrees"`: Labels in interval [0, 360]
  - `"gradians"`: Labels in interval [0, 400]
- `azimuth_offset`: Angular offset for the azimuth axis (radians)
- `azimuth_direction`: `"CCW"` (counterclockwise) or `"CW"` (clockwise)

### Coordinate Conversion
```python
polarplane.polar_to_point(radius, azimuth)  # azimuth in radians
```

## Practical Usage Guidelines

1. **Default to radians**: Unless working with user-facing degree displays, utilize radian values directly for computational efficiency
2. **Explicit degree conversion**: When degrees are required, multiply by `DEGREES` constant at the point of use
3. **Maintain consistency**: Within a single scene or calculation, use one unit system to prevent conversion errors
4. **Leverage constants**: Prefer `PI`, `TAU`, and fractional expressions (e.g., `TAU/4`) over numeric literals for clarity and precision
5. **Verify rotation direction**: Positive angles consistently produce counterclockwise motion; negative angles produce clockwise motion

## Common Angle Values

| Degrees | Radians | Manim Expression |
|---------|---------|------------------|
| 0° | 0 | `0` |
| 30° | π/6 | `PI/6` or `30 * DEGREES` |
| 45° | π/4 | `PI/4` or `45 * DEGREES` |
| 60° | π/3 | `PI/3` or `60 * DEGREES` |
| 90° | π/2 | `PI/2` or `TAU/4` or `90 * DEGREES` |
| 180° | π | `PI` or `180 * DEGREES` |
| 270° | 3π/2 | `3*PI/2` or `3*TAU/4` or `270 * DEGREES` |
| 360° | 2π | `TAU` or `2*PI` or `360 * DEGREES` |

---
