# Manim Constants Reference Guide

## Frame and Resolution Constants

### Frame Dimensions
**ASPECT_RATIO**: Defines the width-to-height ratio of the coordinate frame. Default value is 16.0/9.0, corresponding to standard widescreen format.

**FRAME_HEIGHT**: Specifies the height of the coordinate frame in logical units. Default value is 8.0 units, establishing the vertical extent of the scene coordinate system.

**FRAME_WIDTH**: Computed as FRAME_HEIGHT × ASPECT_RATIO, this constant defines the horizontal extent of the coordinate frame in logical units.

**FRAME_Y_RADIUS**: Half of FRAME_HEIGHT, representing the distance from the origin to the top or bottom edge of the frame.

**FRAME_X_RADIUS**: Half of FRAME_WIDTH, representing the distance from the origin to the left or right edge of the frame.

**FRAME_SHAPE**: A tuple containing (FRAME_WIDTH, FRAME_HEIGHT), providing convenient access to both frame dimensions simultaneously.

### Pixel Resolution
**DEFAULT_PIXEL_HEIGHT**: Default vertical resolution in pixels. Standard value is 1080 pixels for high-definition output.

**DEFAULT_PIXEL_WIDTH**: Default horizontal resolution in pixels. Standard value is 1920 pixels for high-definition output.

**DEFAULT_RESOLUTION**: A tuple containing (DEFAULT_PIXEL_WIDTH, DEFAULT_PIXEL_HEIGHT), representing the default rendering resolution.

**DEFAULT_FPS**: Default frame rate for animations, set to 30 frames per second.

## Directional Vectors

### Primary Directions
**ORIGIN**: Three-dimensional zero vector np.array([0.0, 0.0, 0.0]), representing the center of the coordinate system.

**UP**: Unit vector np.array([0.0, 1.0, 0.0]) pointing in the positive Y direction.

**DOWN**: Unit vector np.array([0.0, -1.0, 0.0]) pointing in the negative Y direction.

**RIGHT**: Unit vector np.array([1.0, 0.0, 0.0]) pointing in the positive X direction.

**LEFT**: Unit vector np.array([-1.0, 0.0, 0.0]) pointing in the negative X direction.

**IN**: Unit vector np.array([0.0, 0.0, -1.0]) pointing away from the camera in three-dimensional space.

**OUT**: Unit vector np.array([0.0, 0.0, 1.0]) pointing toward the camera in three-dimensional space.

### Axis Vectors
**X_AXIS**: Equivalent to RIGHT, denoting the positive X-axis direction.

**Y_AXIS**: Equivalent to UP, denoting the positive Y-axis direction.

**Z_AXIS**: Unit vector np.array([0.0, 0.0, 1.0]), denoting the positive Z-axis direction.

### Diagonal Directions
**UL**: Compound vector UP + LEFT, indicating the upper-left diagonal direction.

**UR**: Compound vector UP + RIGHT, indicating the upper-right diagonal direction.

**DL**: Compound vector DOWN + LEFT, indicating the lower-left diagonal direction.

**DR**: Compound vector DOWN + RIGHT, indicating the lower-right diagonal direction.

### Frame Edge Positions
**TOP**: Positioned at FRAME_Y_RADIUS in the UP direction, representing the top edge of the frame.

**BOTTOM**: Positioned at FRAME_Y_RADIUS in the DOWN direction, representing the bottom edge of the frame.

**LEFT_SIDE**: Positioned at FRAME_X_RADIUS in the LEFT direction, representing the left edge of the frame.

**RIGHT_SIDE**: Positioned at FRAME_X_RADIUS in the RIGHT direction, representing the right edge of the frame.

## Angular Constants

**PI**: Mathematical constant π (approximately 3.14159), representing the ratio of a circle's circumference to its diameter. Aliases numpy.pi.

**TAU**: Mathematical constant τ = 2π (approximately 6.28318), representing the ratio of a circle's circumference to its radius. Facilitates more intuitive angle calculations.

**DEGREES**: Conversion factor TAU/360, enabling conversion between degrees and radians. Used as a multiplier (e.g., 90 * DEGREES).

**DEG**: Alias for DEGREES, providing a shorter notation for degree-to-radian conversion.

**RADIANS**: Constant with value 1, used for readability when expressing angles in radians (e.g., 1.5 * RADIANS).

## Buffer and Spacing Constants

**SMALL_BUFF**: Minimal spacing constant, value 0.1. Used for tight spacing between objects or minimal padding.

**MED_SMALL_BUFF**: Medium-small spacing constant, value 0.25. Provides moderate spacing in compact arrangements.

**MED_LARGE_BUFF**: Medium-large spacing constant, value 0.5. Used for comfortable spacing in standard layouts.

**LARGE_BUFF**: Maximum standard spacing constant, value 1.0. Provides generous separation between elements.

**DEFAULT_MOBJECT_TO_EDGE_BUFFER**: Default padding when positioning objects relative to frame edges. Equals MED_LARGE_BUFF (0.5).

**DEFAULT_MOBJECT_TO_MOBJECT_BUFFER**: Default spacing between adjacent mobjects. Equals MED_SMALL_BUFF (0.25).

## Geometry and Rendering Defaults

**START_X**: Initial X-coordinate value, set to 30, used in certain internal coordinate calculations.

**START_Y**: Initial Y-coordinate value, set to 20, used in certain internal coordinate calculations.

**DEFAULT_DOT_RADIUS**: Standard radius for Dot objects, value 0.08 units.

**DEFAULT_SMALL_DOT_RADIUS**: Reduced radius for smaller dot representations, value 0.04 units.

**DEFAULT_DASH_LENGTH**: Standard length of individual dashes in dashed lines, value 0.05 units.

**DEFAULT_ARROW_TIP_LENGTH**: Standard length of arrow tip geometry, value 0.35 units.

**DEFAULT_STROKE_WIDTH**: Default line thickness for stroked objects. Value determined by configuration settings.

## Timing Constants

**DEFAULT_POINTWISE_FUNCTION_RUN_TIME**: Default duration for pointwise function animations, value 3.0 seconds.

**DEFAULT_WAIT_TIME**: Default pause duration in animations, value 1.0 second.

## Text Style Constants

**NORMAL**: String constant "NORMAL" for standard text weight and style.

**ITALIC**: String constant "ITALIC" for italicized text rendering.

**OBLIQUE**: String constant "OBLIQUE" for oblique (slanted) text style.

**BOLD**: String constant "BOLD" for bold text weight.

### Extended Font Weights (Pango)
**THIN**: String constant "THIN" for minimal font weight.

**ULTRALIGHT**: String constant "ULTRALIGHT" for very light font weight.

**LIGHT**: String constant "LIGHT" for light font weight.

**SEMILIGHT**: String constant "SEMILIGHT" for slightly lighter than normal weight.

**BOOK**: String constant "BOOK" for book-style font weight.

**MEDIUM**: String constant "MEDIUM" for medium font weight.

**SEMIBOLD**: String constant "SEMIBOLD" for moderately bold weight.

**ULTRABOLD**: String constant "ULTRABOLD" for very bold weight.

**HEAVY**: String constant "HEAVY" for heavy font weight.

**ULTRAHEAVY**: String constant "ULTRAHEAVY" for maximum font weight.

## Color Constants

### Primary Colors
**BLACK**: Base black color value.

**WHITE**: Base white color value.

**GRAY** / **GREY**: Neutral gray tone.

**LIGHT_GRAY** / **LIGHT_GREY**: Lighter gray variant.

**DARK_GRAY** / **DARK_GREY**: Darker gray variant.

**DARKER_GRAY** / **DARKER_GREY**: Even darker gray variant.

**GREY_BROWN**: Gray with brown undertones.

### Blue Spectrum
**BLUE**: Standard blue, aliases BLUE_C.

**BLUE_A**: Lightest blue variant in the spectrum.

**BLUE_B**: Light blue variant.

**BLUE_C**: Medium blue, the canonical blue reference.

**BLUE_D**: Dark blue variant.

**BLUE_E**: Darkest blue variant in the spectrum.

**DARK_BLUE**: Alternative dark blue tone.

### Green Spectrum
**GREEN**: Standard green, aliases GREEN_C.

**GREEN_A**: Lightest green variant.

**GREEN_B**: Light green variant.

**GREEN_C**: Medium green, the canonical green reference.

**GREEN_D**: Dark green variant.

**GREEN_E**: Darkest green variant.

**GREEN_SCREEN**: Chroma key green for compositing applications.

### Red Spectrum
**RED**: Standard red, aliases RED_C.

**RED_A**: Lightest red variant.

**RED_B**: Light red variant.

**RED_C**: Medium red, the canonical red reference.

**RED_D**: Dark red variant.

**RED_E**: Darkest red variant.

### Yellow Spectrum
**YELLOW**: Standard yellow, aliases YELLOW_C.

**YELLOW_A**: Lightest yellow variant.

**YELLOW_B**: Light yellow variant.

**YELLOW_C**: Medium yellow, the canonical yellow reference.

**YELLOW_D**: Dark yellow variant.

**YELLOW_E**: Darkest yellow variant.

### Teal Spectrum
**TEAL**: Standard teal, aliases TEAL_C.

**TEAL_A**: Lightest teal variant.

**TEAL_B**: Light teal variant.

**TEAL_C**: Medium teal, the canonical teal reference.

**TEAL_D**: Dark teal variant.

**TEAL_E**: Darkest teal variant.

### Purple Spectrum
**PURPLE**: Standard purple, aliases PURPLE_C.

**PURPLE_A**: Lightest purple variant.

**PURPLE_B**: Light purple variant.

**PURPLE_C**: Medium purple, the canonical purple reference.

**PURPLE_D**: Dark purple variant.

**PURPLE_E**: Darkest purple variant.

### Maroon Spectrum
**MAROON**: Standard maroon, aliases MAROON_C.

**MAROON_A**: Lightest maroon variant.

**MAROON_B**: Light maroon variant.

**MAROON_C**: Medium maroon, the canonical maroon reference.

**MAROON_D**: Dark maroon variant.

**MAROON_E**: Darkest maroon variant.

### Gold Spectrum
**GOLD**: Standard gold, aliases GOLD_C.

**GOLD_A**: Lightest gold variant.

**GOLD_B**: Light gold variant.

**GOLD_C**: Medium gold, the canonical gold reference.

**GOLD_D**: Dark gold variant.

**GOLD_E**: Darkest gold variant.

### Additional Colors
**PINK**: Standard pink color.

**ORANGE**: Standard orange color.

**DARK_BROWN**: Dark brown tone.

**LIGHT_BROWN**: Light brown tone.

**DEFAULT_MOBJECT_COLOR**: Default color applied to mobjects when no color is specified. Typically set to WHITE.

**DEFAULT_LIGHT_COLOR**: Default color for light sources in three-dimensional scenes. Typically set to a gray variant.

### Extended Color Palettes
**AS2700**: Module containing colors from the Australian Standard AS2700, accessed via AS2700.COLOR_NAME.

**BS381**: Module containing colors from British Standard BS381C, accessed via BS381.COLOR_NAME.

**DVIPSNAMES**: Module containing DVIPS color names for LaTeX compatibility.

**SVGNAMES**: Module containing SVG standard color names.

**X11**: Module containing X11 windowing system color names.

**XKCD**: Module containing colors from the XKCD color survey (nearly 1000 named colors), accessed via XKCD.COLOR_NAME.

## Image Resampling Constants

**RESAMPLING_ALGORITHMS**: Dictionary mapping string identifiers to PIL resampling algorithms, used for image scaling operations:
- "nearest" / "none": Resampling.NEAREST
- "lanczos" / "antialias": Resampling.LANCZOS
- "bilinear" / "linear": Resampling.BILINEAR
- "bicubic" / "cubic": Resampling.BICUBIC
- "box": Resampling.BOX
- "hamming": Resampling.HAMMING

## Enumeration Constants

### RendererType
**CAIRO**: String value "cairo", specifying the Cairo-based rendering backend.

**OPENGL**: String value "opengl", specifying the OpenGL-based rendering backend.

### LineJointType
**AUTO**: Integer value 0, automatic joint type selection.

**ROUND**: Integer value 1, rounded line joints.

**BEVEL**: Integer value 2, beveled line joints.

**MITER**: Integer value 3, mitered line joints.

### CapStyleType
**AUTO**: Integer value 0, automatic cap style selection.

**ROUND**: Integer value 1, rounded line caps.

**BUTT**: Integer value 2, flat line caps flush with endpoints.

**SQUARE**: Integer value 3, square line caps extending beyond endpoints.

## Message Constants

**SCENE_NOT_FOUND_MESSAGE**: Template message displayed when a requested scene cannot be located in the specified module.

**CHOOSE_NUMBER_MESSAGE**: Template message prompting user to select a scene number from available options.

**INVALID_NUMBER_MESSAGE**: Template message displayed when an invalid scene selection is provided.

**NO_SCENE_MESSAGE**: Template message indicating that no scenes exist within the specified module.

---
