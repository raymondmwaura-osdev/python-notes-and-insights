# Manim Coordinate System

## 1. Introduction

Manim (Mathematical Animation Engine) employs a sophisticated coordinate system architecture that facilitates the creation of mathematical animations through programmatic specification of object positions and transformations. The fundamental scene structure comprises an 8×14 grid, addressed using NumPy arrays in the form [x, y, z]. This coordinate framework serves as the foundational abstraction layer between mathematical concepts and their visual representations.

## 2. Scene Coordinate System

### 2.1 Default Scene Dimensions

The camera is configured with a default height of 8 units, with the aspect ratio maintained at 16:9, permitting straightforward calculation of the width dimension. The default frame dimensions are established as config.frame_width = 14.222 and config.frame_height = 8, selected to maintain the 16:9 aspect ratio characteristic of contemporary display devices. These dimensions are independent of the rendering resolution specified for output generation.

### 2.2 Coordinate Space Configuration

The scene grid extends from -7 to 8 along the horizontal axis and from -4 to 5 along the vertical axis. The coordinate origin is positioned at the center of the screen, with the positive vertical direction oriented upward and the positive horizontal direction oriented rightward. This differs fundamentally from many graphics software packages that position the origin at the upper-left corner with inverted vertical orientation.

### 2.3 Coordinate Representation

Coordinates within Manim are represented as three-dimensional NumPy arrays, even when constructing two-dimensional animations. For two-dimensional animations, only the x and y axes are utilized, with the z-coordinate typically set to zero. The general form for coordinate specification is:

```python
coordinate = np.array([x, y, z])
```

### 2.4 Predefined Coordinate Constants

Manim provides predefined constants representing common directional vectors: UP = np.array([0, 1, 0]), DOWN = np.array([0, -1, 0]), LEFT = np.array([-1, 0, 0]), RIGHT = np.array([1, 0, 0]), UL = np.array([-1, 1, 0]), DL = np.array([-1, -1, 0]), UR = np.array([1, 1, 0]), DR = np.array([1, -1, 0]). These constants facilitate intuitive object positioning and support arithmetic operations for coordinate manipulation.

### 2.5 Coordinate Arithmetic

Coordinate constants support scalar multiplication and vector addition operations, enabling flexible position specifications:

```python
position = LEFT * 3 + UP * 2
position = RIGHT * 3.75 + DOWN
```

This arithmetic framework permits the construction of arbitrary positions through linear combinations of directional vectors.

## 3. Mathematical Coordinate Systems

### 3.1 CoordinateSystem Abstract Base Class

The CoordinateSystem class serves as the abstract base class for Axes and NumberPlane implementations, accepting parameters including x_range, y_range, x_length, y_length, and dimension. This abstraction provides the foundational interface for coordinate transformation operations.

### 3.2 NumberLine: One-Dimensional Foundation

The NumberLine class provides the fundamental one-dimensional coordinate mapping underlying all coordinate systems in Manim, implementing methods including number_to_point (n2p) for converting numeric values to scene coordinates and point_to_number (p2n) for the inverse transformation. Additional functionality includes tick mark generation based on x_range specification and numeric label addition.

### 3.3 Axes: Cartesian Coordinate System

The Axes class creates a standard Cartesian coordinate system with x and y axes, accepting configuration parameters x_range and y_range specifying numeric ranges for each axis, x_length and y_length defining physical lengths in scene units, and axis_config providing common configuration for both axes.

#### 3.3.1 Range and Length Specifications

The range parameters follow the format [min, max, step], where step determines the spacing of tick marks. The step size additionally determines the decimal places displayed in numeric labels. The length parameters specify the physical extent of each axis in scene coordinate units.

#### 3.3.2 Coordinate Transformation Methods

The coords_to_point method accepts coordinates from the axes and returns a point with respect to the scene coordinate system, supporting both individual coordinate specification and array-based batch transformations. The inverse operation is provided by the point_to_coords method, which accepts scene coordinates and returns the corresponding axes coordinates.

The coords_to_point method may be invoked using the abbreviated alias c2p, and supports the operator overload syntax ax @ (x, y) for enhanced readability.

### 3.4 NumberPlane: Enhanced Cartesian System

The NumberPlane class extends Axes to create a Cartesian plane with background grid lines, accepting parameters including x_range in the format [x_min, x_max, x_step], y_range in the format [y_min, y_max, y_step], and styling parameters for background and faded grid lines. When x_length or y_length are not explicitly defined, they are automatically calculated such that one unit on each axis corresponds to one Manim unit in length.

### 3.5 ThreeDAxes: Three-Dimensional Extension

ThreeDAxes extends the Axes class to three dimensions, incorporating a z-axis and supporting three-dimensional coordinate transformations, with parameters including z_range specifying [z_min, z_max, z_step], z_length defining the z-axis physical length, z_axis_config for z-axis specific configuration, and z_normal determining the normal direction.

For three-dimensional scene rendering, two additional constants are provided: OUT representing the positive z-direction and IN representing the negative z-direction, requiring the use of ThreeDScene rather than the standard Scene class.

### 3.6 PolarPlane: Polar Coordinate System

The PolarPlane class implements polar coordinate transformations through the polar_to_point method, which converts polar coordinates (radius, azimuth) to Cartesian point coordinates. This specialized coordinate system facilitates the creation of animations involving circular or angular relationships.

### 3.7 ComplexPlane: Complex Number Coordinates

ComplexPlane provides a specialized coordinate system for complex number operations, extending NumberPlane with methods specific to complex arithmetic and visualization. This implementation permits direct manipulation of complex-valued expressions within the animation framework.

## 4. Coordinate Transformation Architecture

### 4.1 Transformation Pipeline

The coordinate transformation pipeline in Manim operates through a hierarchical structure. User-specified mathematical coordinates are first transformed to axes-relative coordinates, which are subsequently transformed to scene coordinates for rendering. This multi-stage transformation permits independent scaling and positioning of mathematical coordinate systems within the scene.

### 4.2 Scaling Transformations

Coordinate systems support configurable scaling through the specification of x_length and y_length parameters, which determine the physical extent in scene units, permitting independent control of coordinate density along each axis. The unit_size parameter provides an alternative specification method, defining the scene units per coordinate unit.

### 4.3 Automatic Unit Calculation

When physical dimensions are not explicitly specified, NumberPlane automatically calculates appropriate lengths to ensure one coordinate unit corresponds to one scene unit. This automatic scaling facilitates intuitive coordinate system construction while permitting override when specific proportions are required.

## 5. Frame and Pixel Coordinate Systems

### 5.1 Frame Dimensions

The frame dimensions config.frame_width and config.frame_height are distinct from pixel dimensions, maintaining default values of 14.222 and 8 units respectively to achieve a 16:9 aspect ratio. The coordinate center is positioned at the scene center, with frame coordinates extending equally in positive and negative directions from this origin.

### 5.2 Pixel Resolution Independence

The 8-unit camera height is independent of rendering resolution specifications such as 480p, 720p, or higher definitions. This resolution independence ensures that scene specifications remain consistent across different output quality settings, with the rendering engine handling the mapping from scene coordinates to pixel coordinates.

### 5.3 Aspect Ratio Management

The resize_frame_shape method adjusts frame dimensions to match pixel aspect ratios, with a fixed_dimension parameter determining whether frame_height or frame_width remains constant while the other dimension is scaled accordingly. When fixed_dimension equals zero, height scales with respect to width; otherwise, width scales with respect to height.

## 6. Practical Coordinate System Usage

### 6.1 Object Positioning

Objects may be positioned using absolute coordinates relative to the scene origin or relative coordinates measured from existing objects. The move_to method employs absolute positioning measured from the origin, while the next_to method uses relative positioning measured from a reference mobject.

### 6.2 Boundary Considerations

Objects may be positioned outside the visible boundary; however, such objects will not appear in the rendered output. This permits computational manipulation of off-screen objects while maintaining rendering efficiency.

### 6.3 Coordinate Systems for Graphing

Axes are composed of two NumberLine instances, permitting configuration of each axis using NumberLine options, with primary configuration focusing on scale definition through x_range and y_range parameters, and size specification through x_length and y_length parameters. The axis_config parameter applies settings to both axes simultaneously, while x_axis_config and y_axis_config permit axis-specific customization.

### 6.4 Function Plotting Integration

The plot_parametric_curve method accepts a parametric function mapping a parameter to points in the coordinate system, with the use_vectorized parameter enabling efficient array-based computation. Additional plotting methods include plot for expression-based function visualization and plot_implicit_curve for implicit function representation.

## 7. Three-Dimensional Coordinate Considerations

### 7.1 Camera Orientation

Three-dimensional scene visualization requires explicit camera orientation specification. The set_camera_orientation method controls viewing angles, accepting phi and theta parameters for spherical coordinate specification of the camera position. Ambient camera rotation may be initiated using begin_ambient_camera_rotation for dynamic visualization.

### 7.2 Z-Axis Coordinate Transformation

The c2p coordinate transformation method on Axes does not incorporate z-coordinate transformation; explicit addition of z-components using the OUT constant is required for three-dimensional positioning. ThreeDAxes provides full three-dimensional coordinate transformation through its coords_to_point implementation.

### 7.3 Performance Considerations

Three-dimensional rendering in Manim Community Edition is not optimized for complex scenes and relies solely on CPU-based rendering. Alternative frameworks such as ManimGL offer GPU-accelerated rendering for improved performance in computationally intensive three-dimensional visualizations.

## 8. Advanced Coordinate System Topics

### 8.1 Custom Coordinate Transformations

The coordinate system architecture permits the implementation of custom transformations through subclassing of CoordinateSystem and overriding of the coordinate transformation methods. This extensibility enables the creation of specialized coordinate systems for domain-specific applications.

### 8.2 Coordinate System Composition

Multiple coordinate systems may coexist within a single scene, each maintaining independent transformation properties. The distinction between axes coordinates and scene coordinates is explicitly demonstrated through comparison of dots positioned using axes coordinates versus scene coordinates.

### 8.3 Scaling Systems

Linear and logarithmic scaling transformations are supported through the LinearBase and LogBase scale classes, permitting scientific visualization of data spanning multiple orders of magnitude.

## 9. Implementation Details

### 9.1 Type System

The coordinate_systems module defines the LineType TypeVar for type-safe line operations within the coordinate system hierarchy. This type system ensures compile-time verification of coordinate transformation operations.

### 9.2 Coordinate Array Handling

Coordinate transformation methods support both individual coordinate specifications and array-based batch transformations. When provided with arrays of coordinates, methods return arrays of transformed points maintaining the input array structure, facilitating efficient processing of multiple points simultaneously.

### 9.3 Method Aliases

For enhanced code conciseness, abbreviated method names are provided: c2p for coords_to_point, p2c for point_to_coords, n2p for number_to_point, and p2n for point_to_number. These aliases reduce verbosity while maintaining semantic clarity.

---
