# Comprehensive ManimCE Reference Cheatsheet

## 1. Core Scene Structure

### 1.1 Scene Class
The fundamental container for all animations. All animation logic resides within the `construct()` method.

```python
from manim import *

class MyScene(Scene):
    def construct(self):
        # Animation code here
        pass
```

### 1.2 Scene Methods

**`self.add(*mobjects)`** — Instantly adds mobjects to the scene without animation.
```python
circle = Circle()
self.add(circle)
```

**`self.remove(*mobjects)`** — Instantly removes mobjects from the scene.
```python
self.remove(circle)
```

**`self.play(*animations, **kwargs)`** — Executes animations with specified parameters.
```python
self.play(Create(square), run_time=2, rate_func=linear)
```

**`self.wait(duration=1)`** — Pauses the animation for a specified duration in seconds.
```python
self.wait(2)  # Pause for 2 seconds
```

---

## 2. Mobjects (Mathematical Objects)

### 2.1 Basic Geometric Shapes

#### 2.1.1 Circle
A circular shape with customizable radius and appearance.

```python
circle = Circle(radius=1.0, color=BLUE, fill_opacity=0.5)
circle = Circle().set_fill(RED, opacity=0.8)
```

**Key Parameters:**
- `radius` (float): Circle radius
- `color` (ParsableManimColor): Stroke and fill color
- `fill_opacity` (float): Fill transparency (0-1)
- `stroke_width` (float): Border thickness

**Methods:**
- `point_at_angle(angle)`: Returns point coordinates at specified angle
- `surround(mobject, buffer_factor=1.2)`: Surrounds another mobject

#### 2.1.2 Square
A regular square polygon.

```python
square = Square(side_length=2.0, color=GREEN, fill_opacity=1)
```

**Key Parameters:**
- `side_length` (float): Side length
- `color`, `fill_opacity`, `stroke_width`: As per Circle

#### 2.1.3 Rectangle
A rectangular polygon with independent width and height.

```python
rect = Rectangle(width=3, height=2, color=RED)
rect = Rectangle(height=2, width=4, grid_xstep=0.5, grid_ystep=0.25)
```

**Key Parameters:**
- `width` (float): Horizontal dimension
- `height` (float): Vertical dimension
- `grid_xstep` (float): Vertical grid line spacing
- `grid_ystep` (float): Horizontal grid line spacing

#### 2.1.4 RoundedRectangle
A rectangle with rounded corners.

```python
rounded_rect = RoundedRectangle(width=3, height=2, corner_radius=0.5)
```

#### 2.1.5 Triangle
An equilateral triangle.

```python
triangle = Triangle(color=YELLOW)
```

#### 2.1.6 RegularPolygon
A regular polygon with n sides.

```python
hexagon = RegularPolygon(n=6, color=PURPLE)
pentagon = RegularPolygon(n=5, radius=2)
```

**Key Parameters:**
- `n` (int): Number of sides

#### 2.1.7 Ellipse
An elliptical shape.

```python
ellipse = Ellipse(width=4, height=2, color=ORANGE)
```

#### 2.1.8 Annulus
A ring-shaped object.

```python
ring = Annulus(inner_radius=0.5, outer_radius=1.5, color=BLUE)
```

**Key Parameters:**
- `inner_radius` (float): Inner circle radius
- `outer_radius` (float): Outer circle radius

#### 2.1.9 Sector
A pie-slice shaped object.

```python
sector = Sector(outer_radius=2, angle=PI/2, color=GREEN)
```

#### 2.1.10 Arc
A curved arc segment.

```python
arc = Arc(radius=2, start_angle=0, angle=PI/2, color=RED)
```

**Key Parameters:**
- `start_angle` (float): Starting angle in radians
- `angle` (float): Arc span in radians

### 2.2 Line Objects

#### 2.2.1 Line
A straight line segment between two points.

```python
line = Line(start=LEFT, end=RIGHT, color=WHITE)
line = Line(start=np.array([0,0,0]), end=np.array([2,1,0]))
```

**Key Parameters:**
- `start` (Point3D): Starting point coordinates
- `end` (Point3D): Ending point coordinates
- `path_arc` (float): Arc angle for curved path

#### 2.2.2 DashedLine
A line rendered with dashes.

```python
dashed = DashedLine(start=UP, end=DOWN, dash_length=0.2)
```

**Key Parameters:**
- `dash_length` (float): Length of each dash segment

#### 2.2.3 Arrow
A line with an arrowhead.

```python
arrow = Arrow(start=ORIGIN, end=RIGHT*2, color=YELLOW)
arrow = Arrow(LEFT, UP, buff=0.1)
```

**Key Parameters:**
- `buff` (float): Space between arrow and endpoints

#### 2.2.4 DoubleArrow
A line with arrowheads on both ends.

```python
double_arrow = DoubleArrow(start=LEFT, end=RIGHT)
```

#### 2.2.5 Vector
A positioned arrow representing a vector.

```python
vector = Vector(direction=UP*2, color=GREEN)
```

#### 2.2.6 Angle
An angle indicator between two lines.

```python
angle = Angle(line1, line2, radius=0.5, color=BLUE)
```

#### 2.2.7 RightAngle
A right angle indicator.

```python
right_angle = RightAngle(line1, line2, length=0.3)
```

### 2.3 Dots and Points

#### 2.3.1 Dot
A filled circular point.

```python
dot = Dot(point=ORIGIN, radius=0.08, color=RED)
dot = Dot(UP*2, color=BLUE, sheen_factor=0.5)
```

**Key Parameters:**
- `point` (Point3D): Position coordinates
- `radius` (float): Dot size
- `sheen_factor` (float): Shininess effect (-1 to 1)

#### 2.3.2 LabeledDot
A dot with an attached label.

```python
labeled_dot = LabeledDot(label="A", point=ORIGIN)
```

### 2.4 Text and Mathematical Typography

#### 2.4.1 Text
Regular text rendered using system fonts via Pango.

```python
text = Text("Hello World", font_size=48, color=WHITE)
text = Text("Example", font="Arial", weight=BOLD, slant=ITALIC)
```

**Key Parameters:**
- `text` (str): Text content
- `font` (str): Font family name
- `font_size` (float): Size in points
- `color` (ParsableManimColor): Text color
- `weight` (str): Font weight (NORMAL, BOLD, THIN, HEAVY)
- `slant` (str): Font style (NORMAL, ITALIC, OBLIQUE)
- `line_spacing` (float): Spacing between lines

**Advanced Parameters:**
- `t2c` (dict): Text-to-color mapping for substring coloring
- `t2f` (dict): Text-to-font mapping for substring fonts
- `t2w` (dict): Text-to-weight mapping
- `t2s` (dict): Text-to-slant mapping
- `t2g` (dict): Text-to-gradient mapping for color gradients

```python
text = Text(
    "Hello World",
    t2c={"Hello": RED, "World": BLUE},
    t2f={"World": "Courier"},
    t2w={"Hello": BOLD}
)
```

**Methods:**
- `set_color_by_text(text, color)`: Colors specific substring

#### 2.4.2 MarkupText
Text with HTML/XML-style markup formatting.

```python
markup = MarkupText(
    'Normal <i>Italic</i> <b>Bold</b> <span foreground="blue">Blue</span>'
)
```

#### 2.4.3 Tex
LaTeX text in normal (text) mode.

```python
tex = Tex(r"The horse does not eat cucumber salad.")
tex = Tex(r"Hello $\LaTeX$", font_size=72, color=BLUE)
```

**Key Usage:**
- Use raw strings (`r"..."`) to avoid escaping backslashes
- Enclose math within `$...$` for mathematical notation
- Accepts multiple arguments for substring separation

```python
tex = Tex("Hello", r"$\bigstar$", r"\LaTeX")
tex.set_color_by_tex("star", RED)
```

#### 2.4.4 MathTex
LaTeX mathematical expressions (rendered in math mode).

```python
formula = MathTex(r"\int_a^b f'(x)\,dx = f(b) - f(a)")
equation = MathTex(r"E = mc^2", font_size=96)
```

**Key Features:**
- Automatically in math mode (align* environment)
- Double braces `{{ }}` isolate substrings for transformations

```python
equation = MathTex(r"{{ a^2 }} + {{ b^2 }} = {{ c^2 }}")
# Creates 5 submobjects: a^2, +, b^2, =, c^2
```

**Methods:**
- `set_color_by_tex(tex, color)`: Colors specific LaTeX substring
- `get_part_by_tex(tex)`: Retrieves substring mobject

**Common Patterns:**
```python
# Multi-line aligned equations
equation = MathTex(
    r"f(x) &= x^2 + 2x + 1 \\",
    r"&= (x+1)^2"
)

# Custom templates
from manim import TexTemplate, TexFontTemplates
custom_template = TexTemplate()
custom_template.add_to_preamble(r"\usepackage{amsmath}")
tex = MathTex(r"\mathscr{L}", tex_template=custom_template)

# French cursive
tex = Tex(r"$f: A \rightarrow B$", tex_template=TexFontTemplates.french_cursive)
```

---

## 3. Mobject Transformation and Positioning

### 3.1 Position and Movement Methods

**`shift(vector)`** — Translates mobject by vector offset (relative movement).
```python
circle.shift(UP*2 + RIGHT*3)
circle.shift(np.array([1, 2, 0]))
```

**`move_to(point_or_mobject)`** — Moves center to absolute position.
```python
square.move_to(ORIGIN)
square.move_to(circle)  # Move to circle's center
square.move_to(UP*2 + LEFT*3)
```

**`next_to(mobject, direction, buff=0.25)`** — Positions relative to another mobject.
```python
text.next_to(circle, DOWN, buff=0.5)
arrow.next_to(square, RIGHT)
```

**`align_to(mobject_or_point, direction)`** — Aligns edge/border to reference.
```python
rect1.align_to(rect2, LEFT)   # Align left edges
rect1.align_to(rect2, UP)     # Align top edges
```

**`to_edge(edge, buff=0.5)`** — Moves to screen edge.
```python
text.to_edge(UP)        # Move to top edge
circle.to_edge(LEFT)    # Move to left edge
```

**`to_corner(corner, buff=0.5)`** — Moves to screen corner.
```python
text.to_corner(UL)  # Upper-left corner
logo.to_corner(DR)  # Down-right corner
```

**`center()`** — Centers at ORIGIN.
```python
mobject.center()
```

### 3.2 Scaling and Sizing

**`scale(factor, **kwargs)`** — Scales size by multiplicative factor.
```python
circle.scale(2)           # Double size
text.scale(0.5)           # Halve size
group.scale(1.5, about_point=ORIGIN)
```

**`scale_to_fit_width(width)`** — Scales to specific width.
```python
rect.scale_to_fit_width(5)
```

**`scale_to_fit_height(height)`** — Scales to specific height.
```python
rect.scale_to_fit_height(3)
```

**`stretch(factor, dim)`** — Non-uniform scaling along dimension.
```python
rect.stretch(2, 0)  # Stretch horizontally (x-axis)
rect.stretch(0.5, 1)  # Compress vertically (y-axis)
```

### 3.3 Rotation

**`rotate(angle, axis=OUT, **kwargs)`** — Rotates by angle in radians.
```python
square.rotate(PI/4)                    # 45 degrees
square.rotate(90*DEGREES)              # Using DEGREES constant
square.rotate(PI/2, about_point=ORIGIN)
```

**`rotate_about_origin(angle)`** — Rotates around coordinate origin.
```python
mobject.rotate_about_origin(PI)
```

### 3.4 Flipping and Mirroring

**`flip(axis=UP)`** — Mirrors mobject across axis.
```python
triangle.flip()           # Flip vertically
triangle.flip(RIGHT)      # Flip horizontally
```

### 3.5 Styling and Appearance

**`set_color(color)`** — Sets both stroke and fill color.
```python
circle.set_color(RED)
square.set_color("#FF5733")
```

**`set_fill(color, opacity=1.0)`** — Sets fill color and opacity.
```python
circle.set_fill(BLUE, opacity=0.5)
square.set_fill(GREEN, opacity=1)
```

**`set_stroke(color, width, opacity=1.0)`** — Sets stroke properties.
```python
circle.set_stroke(WHITE, width=3)
rect.set_stroke(YELLOW, width=5, opacity=0.8)
```

**`set_opacity(opacity)`** — Sets overall transparency.
```python
mobject.set_opacity(0.5)
```

**`set_sheen(factor, direction=UL)`** — Adds glossy sheen effect.
```python
circle.set_sheen(0.7, direction=UR)
```

### 3.6 Properties (Getters)

**`get_center()`** — Returns center coordinates.
```python
center = circle.get_center()
```

**`get_top()`, `get_bottom()`, `get_left()`, `get_right()`** — Edge coordinates.
```python
top_point = rect.get_top()
```

**`get_corner(corner)`** — Corner coordinates.
```python
corner = square.get_corner(UL)
```

**`get_width()`, `get_height()`** — Dimensions.
```python
width = rect.get_width()
height = rect.get_height()
```

---

## 4. Animation Classes

### 4.1 Creation Animations

**`Create(mobject, **kwargs)`** — Draws mobject progressively.
```python
self.play(Create(circle))
self.play(Create(line, run_time=2))
```

**`Uncreate(mobject, **kwargs)`** — Reverse of Create, removes progressively.
```python
self.play(Uncreate(square))
```

**`Write(mobject, **kwargs)`** — Writing effect for text/equations.
```python
self.play(Write(text))
self.play(Write(equation, run_time=3))
```

**`Unwrite(mobject, **kwargs)`** — Reverse of Write.
```python
self.play(Unwrite(text))
```

**`DrawBorderThenFill(mobject, **kwargs)`** — Draws border then fills interior.
```python
self.play(DrawBorderThenFill(square))
```

**`ShowIncreasingSubsets(group, **kwargs)`** — Reveals submobjects sequentially.
```python
self.play(ShowIncreasingSubsets(vgroup))
```

**`ShowSubmobjectsOneByOne(group, **kwargs)`** — Shows each submobject individually.
```python
self.play(ShowSubmobjectsOneByOne(group, run_time=3))
```

**`AddTextLetterByLetter(text, **kwargs)`** — Types text letter by letter.
```python
self.play(AddTextLetterByLetter(text, time_per_char=0.1))
```

**`AddTextWordByWord(text, **kwargs)`** — Types text word by word.
```python
self.play(AddTextWordByWord(text))
```

**`SpiralIn(mobject, **kwargs)`** — Spiraling entrance animation.
```python
self.play(SpiralIn(circle))
```

### 4.2 Fading Animations

**`FadeIn(mobject, shift=None, scale=1, **kwargs)`** — Fades in with optional shift/scale.
```python
self.play(FadeIn(circle))
self.play(FadeIn(square, shift=DOWN))
self.play(FadeIn(text, scale=0.5))
self.play(FadeIn(dot, target_position=circle))
```

**Key Parameters:**
- `shift` (Vector3D): Direction and distance of fade
- `target_position`: Position to fade from
- `scale` (float): Initial scale factor

**`FadeOut(mobject, shift=None, scale=1, **kwargs)`** — Fades out with optional effects.
```python
self.play(FadeOut(circle))
self.play(FadeOut(square, shift=UP))
self.play(FadeOut(text, scale=0.5))
```

### 4.3 Growing Animations

**`GrowFromCenter(mobject, **kwargs)`** — Grows from center point.
```python
self.play(GrowFromCenter(circle))
```

**`GrowFromEdge(mobject, edge, **kwargs)`** — Grows from specified edge.
```python
self.play(GrowFromEdge(rectangle, LEFT))
```

**`GrowFromPoint(mobject, point, **kwargs)`** — Grows from specific point.
```python
self.play(GrowFromPoint(square, ORIGIN))
```

**`GrowArrow(arrow, **kwargs)`** — Specialized growth for arrows.
```python
self.play(GrowArrow(arrow))
```

**`SpinInFromNothing(mobject, **kwargs)`** — Spinning growth animation.
```python
self.play(SpinInFromNothing(circle))
```

### 4.4 Transform Animations

**`Transform(mobject, target_mobject, **kwargs)`** — Morphs mobject into target shape.
```python
self.play(Transform(square, circle))
# square now has properties of circle but retains identity
```

**Important:** After transformation, original mobject adopts target's appearance but maintains its variable reference.

**`ReplacementTransform(mobject, target_mobject, **kwargs)`** — Replaces with target.
```python
self.play(ReplacementTransform(square, circle))
# square removed from scene, circle added
```

**Important:** Original mobject removed; target becomes active scene object.

**Key Parameters for Transforms:**
- `path_arc` (float): Arc angle for transformation path (radians)
- `path_func` (Callable): Custom path function

```python
self.play(
    ReplacementTransform(square, circle, path_arc=PI/2)
)
```

**`FadeTransform(mobject, target, **kwargs)`** — Fades between objects.
```python
self.play(FadeTransform(text1, text2))
```

**`TransformMatchingShapes(mobject, target, **kwargs)`** — Matches similar shapes.
```python
self.play(TransformMatchingShapes(shape1, shape2))
```

**`TransformMatchingTex(mobject, target, **kwargs)`** — For LaTeX transformations.
```python
eq1 = MathTex(r"{{ a }} + {{ b }} = {{ c }}")
eq2 = MathTex(r"{{ c }} = {{ a }} + {{ b }}")
self.play(TransformMatchingTex(eq1, eq2))
```

**`TransformFromCopy(mobject, target, **kwargs)`** — Copies and transforms.
```python
self.play(TransformFromCopy(source, destination))
```

**`ClockwiseTransform(mobject, target, **kwargs)`** — Clockwise rotation transform.
```python
self.play(ClockwiseTransform(square, circle))
```

**`CounterclockwiseTransform(mobject, target, **kwargs)`** — Counter-clockwise transform.
```python
self.play(CounterclockwiseTransform(square, circle))
```

**`CyclicReplace(*mobjects)`** — Cycles mobjects through positions.
```python
self.play(CyclicReplace(mob1, mob2, mob3))
```

**`Swap(*mobjects)`** — Swaps positions of mobjects.
```python
self.play(Swap(circle, square))
```

### 4.5 Indication Animations

**`Indicate(mobject, **kwargs)`** — Brief scale pulse to highlight.
```python
self.play(Indicate(text))
self.play(Indicate(circle, scale_factor=1.5, color=YELLOW))
```

**`Circumscribe(mobject, **kwargs)`** — Draws shape around mobject.
```python
self.play(Circumscribe(text))
self.play(Circumscribe(equation, color=YELLOW, buff=0.2, shape=Circle))
```

**Key Parameters:**
- `shape`: Rectangle (default), Circle, or custom
- `color`: Outline color
- `stroke_width`: Line thickness
- `buff`: Space around mobject
- `time_width`: Animation duration

**`Flash(mobject, **kwargs)`** — Flash effect at mobject position.
```python
self.play(Flash(dot, color=YELLOW, flash_radius=0.5))
```

**`FocusOn(mobject, **kwargs)`** — Focus circle that contracts.
```python
self.play(FocusOn(text))
```

**`ShowPassingFlash(mobject, **kwargs)`** — Light passes along mobject.
```python
self.play(ShowPassingFlash(line))
```

**`Wiggle(mobject, **kwargs)`** — Wiggling motion.
```python
self.play(Wiggle(arrow))
```

**`ApplyWave(mobject, **kwargs)`** — Wave distortion effect.
```python
self.play(ApplyWave(line))
```

**`Broadcast(mobject, **kwargs)`** — Expanding wave broadcast effect.
```python
self.play(Broadcast(dot, focal_point=dot.get_center(), n_mobs=3))
```

**Key Parameters:**
- `focal_point`: Wave origin point
- `n_mobs`: Number of wave circles
- `initial_opacity`, `final_opacity`: Opacity range

### 4.6 Movement Animations

**`MoveAlongPath(mobject, path, **kwargs)`** — Moves along specified path.
```python
self.play(MoveAlongPath(dot, arc))
```

**`Rotate(mobject, angle, **kwargs)`** — Rotation animation.
```python
self.play(Rotate(square, PI/2))
self.play(Rotate(square, 90*DEGREES, about_point=ORIGIN))
```

**`Rotating(mobject, **kwargs)`** — Continuous rotation (updater-based).
```python
self.play(Rotating(square, radians=2*PI, run_time=3))
```

### 4.7 Specialized Animations

**`Restore(mobject, **kwargs)`** — Restores to saved state.
```python
square.save_state()
self.play(Transform(square, circle))
self.play(Restore(square))
```

**`ApplyFunction(function, mobject, **kwargs)`** — Applies custom function.
```python
def move_up(mob):
    return mob.shift(UP*2)

self.play(ApplyFunction(move_up, square))
```

**`ApplyMatrix(matrix, mobject, **kwargs)`** — Linear transformation via matrix.
```python
matrix = [[1, 0.5], [0, 1]]
self.play(ApplyMatrix(matrix, square))
```

**`MoveToTarget(mobject, **kwargs)`** — Moves to predefined target.
```python
square.generate_target()
square.target.shift(UP*2).scale(2)
self.play(MoveToTarget(square))
```

### 4.8 Animation Composition

**`AnimationGroup(*animations, **kwargs)`** — Plays animations simultaneously.
```python
self.play(AnimationGroup(
    Create(circle),
    FadeIn(square),
    lag_ratio=0
))
```

**`LaggedStart(*animations, lag_ratio=0.5, **kwargs)`** — Staggers animation starts.
```python
self.play(LaggedStart(
    Create(circle1),
    Create(circle2),
    Create(circle3),
    lag_ratio=0.5
))
```

**`Succession(*animations)`** — Plays animations sequentially.
```python
self.play(Succession(
    Create(square),
    Transform(square, circle),
    FadeOut(square)
))
```

---

## 5. The .animate Syntax

### 5.1 Basic Usage

The `.animate` property converts method calls into animations.

```python
circle = Circle()
self.add(circle)

# Animate method application
self.play(circle.animate.shift(UP*2))
self.play(circle.animate.scale(2))
self.play(circle.animate.rotate(PI/4))
```

### 5.2 Method Chaining

Multiple transformations in single animation:

```python
self.play(
    square.animate.shift(RIGHT*2).rotate(PI/4).scale(1.5)
)
```

### 5.3 Animation Parameters

Pass kwargs immediately after `.animate`:

```python
self.play(
    circle.animate(run_time=2, rate_func=smooth).shift(RIGHT*3)
)

# Different behaviors for simultaneous animations
self.play(
    mob1.animate(run_time=2).rotate(PI),
    mob2.animate(rate_func=there_and_back).shift(RIGHT)
)
```

### 5.4 Applicable Methods

Nearly any mobject method that modifies properties:
- Position: `shift()`, `move_to()`, `next_to()`
- Scale: `scale()`, `stretch()`
- Rotation: `rotate()`
- Styling: `set_color()`, `set_opacity()`, `set_fill()`
- Custom: User-defined transformation methods

**Important:** Cannot chain `.animate` calls across different mobjects simultaneously in same `play()` call.

---

## 6. Grouping and Organization

### 6.1 VGroup

Vector mobject group for collective manipulation.

```python
group = VGroup(circle, square, triangle)
group = VGroup(*[Circle() for _ in range(5)])
```

**Methods:**

**`arrange(direction=RIGHT, buff=0.25, **kwargs)`** — Arranges submobjects.
```python
group.arrange(DOWN, buff=0.5)
group.arrange(RIGHT, aligned_edge=UP)
```

**`arrange_in_grid(rows, cols, **kwargs)`** — Grid arrangement.
```python
group.arrange_in_grid(rows=2, cols=3, buff=0.5)
```

**`add(*mobjects)`** — Adds mobjects to group.
```python
group.add(new_circle, new_square)
```

**`remove(*mobjects)`** — Removes mobjects from group.
```python
group.remove(circle)
```

**Indexing:**
```python
group[0]      # First element
group[1:3]    # Slice
```

### 6.2 Group

General-purpose container (not vectorized).

```python
group = Group(circle, text, equation)
```

Similar methods to VGroup but for mixed mobject types.

---

## 7. Rate Functions

Control animation timing/easing. Specified via `rate_func` parameter.

```python
from manim import *

self.play(Create(circle), rate_func=smooth)
self.play(square.animate.shift(UP), rate_func=linear)
```

**Common Rate Functions:**
- `linear`: Constant speed
- `smooth`: Smooth acceleration/deceleration (default)
- `rush_into`: Accelerates into animation
- `rush_from`: Decelerates from animation
- `there_and_back`: Goes forward then returns
- `there_and_back_with_pause`: With pause at midpoint
- `running_start`: Starts with momentum
- `wiggle`: Oscillating motion
- `ease_in_sine`, `ease_out_sine`, `ease_in_out_sine`: Sinusoidal easing
- `ease_in_quad`, `ease_out_quad`, `ease_in_out_quad`: Quadratic easing
- `ease_in_cubic`, `ease_out_cubic`, `ease_in_out_cubic`: Cubic easing

```python
self.play(
    circle.animate.move_to(RIGHT*3),
    rate_func=there_and_back,
    run_time=4
)
```

---

## 8. Coordinate System and Constants

### 8.1 Directional Constants

Pre-defined unit vectors for positioning:

```python
ORIGIN   # np.array([0, 0, 0])
UP       # np.array([0, 1, 0])
DOWN     # np.array([0, -1, 0])
LEFT     # np.array([-1, 0, 0])
RIGHT    # np.array([1, 0, 0])
IN       # np.array([0, 0, -1])
OUT      # np.array([0, 0, 1])

# Diagonal directions
UL, UR, DL, DR  # Combinations of UP/DOWN with LEFT/RIGHT
```

**Usage:**
```python
circle.shift(UP*2 + RIGHT*3)
text.move_to(DL*2)
```

### 8.2 Mathematical Constants

```python
PI       # 3.14159...
TAU      # 2*PI
DEGREES  # PI/180 (for degree conversion)
```

**Usage:**
```python
square.rotate(90*DEGREES)
circle.rotate(PI/4)
```

### 8.3 Color Constants

```python
# Basic colors
WHITE, BLACK, RED, GREEN, BLUE, YELLOW, PURPLE, ORANGE
PINK, GOLD, GREY, TEAL, MAROON

# Extended palette
RED_A, RED_B, RED_C, RED_D, RED_E
BLUE_A, BLUE_B, BLUE_C, BLUE_D, BLUE_E
# Similar patterns for other colors

# Hex codes
color = "#FF5733"
mobject.set_color(color)
```

---

## 9. Graphing and Plotting

### 9.1 Axes

Coordinate axes for mathematical visualization.

```python
axes = Axes(
    x_range=[-5, 5, 1],
    y_range=[-3, 3, 1],
    x_length=10,
    y_length=6,
    axis_config={"color": BLUE}
)
```

**Key Parameters:**
- `x_range`: [min, max, step]
- `y_range`: [min, max, step]
- `x_length`, `y_length`: Physical dimensions
- `axis_config`: Dictionary of styling parameters

**Methods:**

**`plot(function, x_range=None, **kwargs)`** — Plots function.
```python
curve = axes.plot(lambda x: x**2, color=YELLOW)
curve = axes.plot(np.sin, x_range=[-TAU, TAU], color=RED)
```

**`plot_line_graph(x_values, y_values, **kwargs)`** — Discrete data points.
```python
line_graph = axes.plot_line_graph(
    x_values=[1, 2, 3, 4],
    y_values=[1, 4, 9, 16],
    line_color=GREEN,
    add_vertex_dots=True
)
```

**`get_graph_label(graph, label, **kwargs)`** — Labels curve.
```python
label = axes.get_graph_label(curve, label=MathTex(r"f(x) = x^2"))
```

**`coords_to_point(x, y)`** — Converts coordinates to scene point.
```python
point = axes.coords_to_point(2, 4)
dot = Dot(point, color=RED)
```

**`point_to_coords(point)`** — Converts scene point to coordinates.
```python
coords = axes.point_to_coords(np.array([1, 2, 0]))
```

**`get_vertical_line(x, **kwargs)`** — Vertical line at x.
```python
v_line = axes.get_vertical_line(axes.coords_to_point(2, 4))
```

**`get_horizontal_line(y, **kwargs)`** — Horizontal line at y.
```python
h_line = axes.get_horizontal_line(axes.coords_to_point(2, 4))
```

**`get_T_label(x, graph, **kwargs)`** — T-shaped coordinate label.
```python
t_label = axes.get_T_label(x=2, graph=curve, label=MathTex("x_0"))
```

**`get_area(graph, x_range, **kwargs)`** — Shaded region under curve.
```python
area = axes.get_area(curve, x_range=[0, 3], color=BLUE, opacity=0.3)
```

**`get_riemann_rectangles(graph, x_range, dx, **kwargs)`** — Riemann sum visualization.
```python
rects = axes.get_riemann_rectangles(
    curve, 
    x_range=[0, 3], 
    dx=0.5, 
    color=BLUE,
    stroke_width=0.5
)
```

### 9.2 NumberLine

One-dimensional numerical axis.

```python
number_line = NumberLine(
    x_range=[-5, 5, 1],
    length=10,
    include_numbers=True,
    include_tip=True
)
```

**Methods:**

**`n2p(number)`** — Number to point conversion.
```python
point = number_line.n2p(3)
```

**`p2n(point)`** — Point to number conversion.
```python
number = number_line.p2n(np.array([2, 0, 0]))
```

**`get_number_mobject(number, **kwargs)`** — Number label mobject.
```python
label = number_line.get_number_mobject(3.14)
```

### 9.3 NumberPlane

2D coordinate grid.

```python
plane = NumberPlane(
    x_range=[-7, 7, 1],
    y_range=[-5, 5, 1],
    background_line_style={
        "stroke_color": TEAL,
        "stroke_width": 2,
        "stroke_opacity": 0.6
    }
)
```

**Methods:**
Similar to Axes, plus:

**`prepare_for_nonlinear_transform(num_inserted_curves)`** — Prepares for transformations.
```python
plane.prepare_for_nonlinear_transform(100)
plane.apply_function(lambda p: p + np.array([np.sin(p[1]), 0, 0]))
```

### 9.4 ComplexPlane

Complex number coordinate system.

```python
complex_plane = ComplexPlane(
    x_range=[-4, 4, 1],
    y_range=[-3, 3, 1]
)
```

**Methods:**

**`n2p(complex_number)`** — Complex to point.
```python
point = complex_plane.n2p(2 + 3j)
```

**`p2n(point)`** — Point to complex.
```python
z = complex_plane.p2n(np.array([2, 3, 0]))
```

---

## 10. Parametric Functions and Curves

### 10.1 ParametricFunction

Parametric curve from parameter to point mapping.

```python
curve = ParametricFunction(
    function=lambda t: np.array([
        np.cos(t),
        np.sin(t),
        0
    ]),
    t_range=[0, TAU],
    color=YELLOW
)
```

**Key Parameters:**
- `function` (Callable): t → Point3D mapping
- `t_range`: [t_min, t_max, step]
- `color`, `stroke_width`: Styling

**Usage for complex curves:**
```python
# Lissajous curve
lissajous = ParametricFunction(
    lambda t: np.array([
        2*np.sin(3*t),
        2*np.sin(4*t),
        0
    ]),
    t_range=[0, TAU],
    color=PURPLE
)
```

### 10.2 ImplicitFunction

Curve defined by implicit equation f(x,y) = 0.

```python
from manim import ImplicitFunction

# Circle: x² + y² = 4
circle_implicit = ImplicitFunction(
    lambda x, y: x**2 + y**2 - 4,
    color=GREEN
)

# Hyperbola: x²/a² - y²/b² = 1
hyperbola = ImplicitFunction(
    lambda x, y: x**2/4 - y**2/4 - 1,
    color=YELLOW
)
```

---

## 11. 3D Scenes and Objects

### 11.1 ThreeDScene

Base class for three-dimensional visualizations.

```python
class My3DScene(ThreeDScene):
    def construct(self):
        axes = ThreeDAxes()
        self.set_camera_orientation(phi=75*DEGREES, theta=30*DEGREES)
        self.play(Create(axes))
```

**Methods:**

**`set_camera_orientation(phi, theta, distance, **kwargs)`** — Positions camera.
```python
self.set_camera_orientation(
    phi=75*DEGREES,    # Angle from xy-plane
    theta=30*DEGREES,  # Rotation around z-axis
    distance=15        # Distance from origin
)
```

**`begin_ambient_camera_rotation(rate, about)`** — Continuous camera rotation.
```python
self.begin_ambient_camera_rotation(rate=0.1, about="theta")
```

**`stop_ambient_camera_rotation(about)`** — Stops rotation.
```python
self.stop_ambient_camera_rotation(about="theta")
```

**`move_camera(phi, theta, distance, **kwargs)`** — Animated camera movement.
```python
self.play(self.camera.animate.set_phi(60*DEGREES))
self.move_camera(phi=70*DEGREES, theta=45*DEGREES, run_time=3)
```

### 11.2 ThreeDAxes

Three-dimensional coordinate axes.

```python
axes_3d = ThreeDAxes(
    x_range=[-5, 5, 1],
    y_range=[-5, 5, 1],
    z_range=[-3, 3, 1],
    x_length=10,
    y_length=10,
    z_length=6
)
```

### 11.3 3D Shapes

**Surface** — Parametric surface.
```python
surface = Surface(
    lambda u, v: np.array([
        u,
        v,
        u**2 - v**2
    ]),
    u_range=[-2, 2],
    v_range=[-2, 2],
    resolution=(20, 20),
    fill_opacity=0.7
)
```

**Sphere** — Three-dimensional sphere.
```python
sphere = Sphere(radius=2, resolution=(20, 20), color=BLUE)
```

**Cube** — Three-dimensional cube.
```python
cube = Cube(side_length=2, fill_opacity=0.7, color=RED)
```

**Cone** — Three-dimensional cone.
```python
cone = Cone(base_radius=1, height=2, color=GREEN)
```

**Cylinder** — Three-dimensional cylinder.
```python
cylinder = Cylinder(radius=1, height=3, color=YELLOW)
```

**Torus** — Torus (donut shape).
```python
torus = Torus(major_radius=2, minor_radius=0.5, color=PURPLE)
```

**Prism** — Extruded 2D shape into 3D.
```python
prism = Prism(dimensions=[2, 2, 3], color=ORANGE)
```

---

## 12. Tables and Matrices

### 12.1 Table

Tabular data display.

```python
table = Table(
    [
        ["Name", "Age", "City"],
        ["Alice", "30", "NYC"],
        ["Bob", "25", "LA"]
    ],
    include_outer_lines=True
)
```

**Methods:**

**`get_cell(row, col)`** — Retrieves cell mobject.
```python
cell = table.get_cell((2, 1))
```

**`get_row(row)`** — Retrieves row mobjects.
```python
row = table.get_row(1)
```

**`get_col(col)`** — Retrieves column mobjects.
```python
col = table.get_col(2)
```

**`get_highlighted_cell(row, col, **kwargs)`** — Highlights specific cell.
```python
highlight = table.get_highlighted_cell((1, 1), color=YELLOW)
```

### 12.2 Matrix

Mathematical matrix representation.

```python
matrix = Matrix(
    [[1, 2], [3, 4]],
    left_bracket="[",
    right_bracket="]"
)

# Alternative brackets
matrix = Matrix([[a, b], [c, d]], left_bracket="(", right_bracket=")")
```

**Methods:**

**`get_entries()`** — Returns entry mobjects.
```python
entries = matrix.get_entries()
```

**`get_brackets()`** — Returns bracket mobjects.
```python
brackets = matrix.get_brackets()
```

**`add_background_to_entries()`** — Adds background rectangles.
```python
matrix.add_background_to_entries(color=BLUE, opacity=0.3)
```

### 12.3 IntegerMatrix, DecimalMatrix, MobjectMatrix

Specialized matrix types:

```python
int_matrix = IntegerMatrix([[1, 2], [3, 4]])
decimal_matrix = DecimalMatrix([[1.5, 2.7], [3.1, 4.9]])
mobject_matrix = MobjectMatrix([[circle, square], [triangle, star]])
```

---

## 13. Code Display

### 13.1 Code

Syntax-highlighted code display.

```python
code = Code(
    code="def factorial(n):\n    return 1 if n == 0 else n * factorial(n-1)",
    language="python",
    font="Monospace",
    font_size=24,
    style="monokai"
)
```

**Key Parameters:**
- `code` (str): Source code string
- `file_name` (str): Alternative to `code`, loads from file
- `language` (str): Programming language
- `style` (str): Pygments style theme
- `background` (str): Background color/pattern
- `insert_line_no` (bool): Line numbers
- `line_spacing` (float): Vertical spacing

**Methods:**

**`from_file(file_path, **kwargs)`** — Loads code from file.
```python
code = Code.from_file("example.py", language="python")
```

**Example with highlighting:**
```python
code = Code(
    """
def quicksort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quicksort(left) + middle + quicksort(right)
    """,
    language="python",
    style="github-dark",
    insert_line_no=True,
    line_spacing=0.5
)
```

---

## 14. Value Trackers and Updaters

### 14.1 ValueTracker

Invisible mobject that tracks numerical value for animations.

```python
tracker = ValueTracker(0)

# Animate value change
self.play(tracker.animate.set_value(5), run_time=3)

# Get current value
current = tracker.get_value()
```

**Use Case: Dynamic Updates**
```python
tracker = ValueTracker(0)
decimal = DecimalNumber(0)
decimal.add_updater(lambda m: m.set_value(tracker.get_value()))

self.add(decimal, tracker)
self.play(tracker.animate.set_value(10), run_time=5)
```

### 14.2 Updaters

Functions that update mobjects each frame.

**`add_updater(update_function)`** — Adds continuous updater.
```python
def update_func(mob, dt):
    mob.rotate(PI * dt)  # dt = delta time per frame

square.add_updater(update_func)
self.add(square)
self.wait(3)
square.clear_updaters()
```

**Static updater (no dt parameter):**
```python
circle = Circle()
tracker = ValueTracker(0)

circle.add_updater(lambda m: m.set_fill(
    opacity=abs(np.sin(tracker.get_value()))
))

self.add(circle, tracker)
self.play(tracker.animate.set_value(TAU), run_time=3)
```

**`remove_updater(update_function)`** — Removes specific updater.
```python
square.remove_updater(update_func)
```

**`clear_updaters()`** — Removes all updaters.
```python
square.clear_updaters()
```

**`suspend_updating()`** / **`resume_updating()`** — Pause/resume updaters.
```python
square.suspend_updating()
# ... other animations
square.resume_updating()
```

---

## 15. Brace and Labeling

### 15.1 Brace

Curly brace for indicating dimensions or relationships.

```python
line = Line(LEFT*2, RIGHT*2)
brace = Brace(line, direction=UP, color=YELLOW)
```

**Key Parameters:**
- `mobject` (Mobject): Object to brace
- `direction` (Vector3D): Brace orientation (UP, DOWN, LEFT, RIGHT)
- `buff` (float): Distance from mobject

**Methods:**

**`get_text(text, **kwargs)`** — Adds text label to brace.
```python
brace_text = brace.get_text("Width")
```

**`get_tex(tex, **kwargs)`** — Adds LaTeX label.
```python
brace_label = brace.get_tex(r"\Delta x")
```

**Example Usage:**
```python
rect = Rectangle(width=4, height=2)
top_brace = Brace(rect, UP)
top_label = top_brace.get_text("Width = 4")
side_brace = Brace(rect, LEFT)
side_label = side_brace.get_tex(r"h = 2")

self.play(
    Create(rect),
    GrowFromCenter(top_brace),
    Write(top_label)
)
```

### 15.2 BraceLabel

Combined brace and label mobject.

```python
brace_label = BraceLabel(
    line,
    text="Distance",
    brace_direction=DOWN,
    label_constructor=Text
)
```

### 15.3 BraceText and BraceTex

Convenience constructors:

```python
brace_text = BraceText(line, "Length", brace_direction=UP)
brace_tex = BraceTex(square, r"\ell", brace_direction=RIGHT)
```

---

## 16. Specialized Mobjects

### 16.1 DecimalNumber and Integer

Dynamic numerical displays.

```python
number = DecimalNumber(
    3.14159,
    num_decimal_places=3,
    font_size=48,
    color=YELLOW
)
```

**Methods:**

**`set_value(value)`** — Updates displayed number.
```python
self.play(number.animate.set_value(2.71828))
```

**Example with ValueTracker:**
```python
tracker = ValueTracker(0)
decimal = DecimalNumber(0, num_decimal_places=2)
decimal.add_updater(lambda d: d.set_value(tracker.get_value()))

self.add(decimal)
self.play(tracker.animate.set_value(100), run_time=5)
```

**Integer:**
```python
counter = Integer(0, font_size=72)
self.play(counter.animate.set_value(42))
```

### 16.2 NumberLine with Tracker

Animated number line indicator:

```python
number_line = NumberLine(x_range=[0, 10, 1], length=10)
tracker = ValueTracker(0)
dot = always_redraw(lambda: Dot(
    number_line.n2p(tracker.get_value()),
    color=RED
))

self.add(number_line, dot)
self.play(tracker.animate.set_value(8), run_time=4)
```

### 16.3 VectorField

Vector field visualization.

```python
field = VectorField(
    func=lambda pos: np.array([
        pos[1],   # dx/dt = y
        -pos[0],  # dy/dt = -x
        0
    ]),
    x_range=[-5, 5, 0.5],
    y_range=[-5, 5, 0.5],
    colors=[BLUE, GREEN]
)

self.add(field)
```

### 16.4 StreamLines

Fluid flow visualization.

```python
stream_lines = StreamLines(
    func=lambda p: np.array([p[1], -p[0], 0]),
    x_range=[-4, 4, 0.5],
    y_range=[-4, 4, 0.5],
    stroke_width=3,
    max_anchors_per_line=30
)

self.add(stream_lines)
stream_lines.start_animation(warm_up=True, flow_speed=1.5)
self.wait(5)
```

### 16.5 BarChart

Bar chart visualization.

```python
chart = BarChart(
    values=[3, 7, 5, 9, 4],
    bar_names=["A", "B", "C", "D", "E"],
    y_range=[0, 10, 2],
    bar_colors=[RED, BLUE, GREEN, YELLOW, PURPLE]
)
```

**Methods:**

**`change_bar_values(values)`** — Animates bar height changes.
```python
self.play(chart.animate.change_bar_values([8, 2, 6, 3, 7]))
```

---

## 17. Camera and Configuration

### 17.1 Camera Methods (2D Scene)

**`frame_center`** — Current camera center position.
```python
self.camera.frame_center
```

**`frame_width`, `frame_height`** — Visible area dimensions.
```python
self.camera.frame_width  # Default: 14.222...
self.camera.frame_height # Default: 8.0
```

**Animating Camera:**
```python
# Move camera
self.play(self.camera.frame.animate.move_to(RIGHT*5 + UP*3))

# Zoom camera
self.play(self.camera.frame.animate.scale(0.5))  # Zoom in
self.play(self.camera.frame.animate.scale(2))    # Zoom out
```

### 17.2 MovingCamera Scene

Scene with animated camera movements.

```python
class ZoomScene(MovingCameraScene):
    def construct(self):
        square = Square()
        self.play(Create(square))
        self.play(self.camera.frame.animate.scale(0.3).move_to(square))
```

### 17.3 Configuration Constants

Access via `config` object:

```python
from manim import config

# Resolution
config.pixel_width   # Horizontal resolution
config.pixel_height  # Vertical resolution
config.frame_rate    # FPS

# Output
config.output_file   # Output filename
config.background_color  # Scene background

# Quality presets affect these automatically
```

---

## 18. Advanced Techniques

### 18.1 Custom Mobjects

Subclass Mobject or VMobject:

```python
class CustomShape(VMobject):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        # Define shape using points
        self.set_points_as_corners([
            ORIGIN, UP, UP+RIGHT, RIGHT, ORIGIN
        ])
        self.set_fill(BLUE, opacity=0.5)
        self.set_stroke(WHITE, width=2)
```

### 18.2 Boolean Operations

Combine/subtract shapes:

```python
from manim import Union, Difference, Intersection, Exclusion

circle = Circle(radius=1.5, fill_opacity=0.5)
square = Square(side_length=2, fill_opacity=0.5)

# Union (OR)
union = Union(circle, square, color=GREEN, fill_opacity=0.5)

# Difference (subtract)
diff = Difference(square, circle, color=RED, fill_opacity=0.5)

# Intersection (AND)
intersect = Intersection(circle, square, color=BLUE, fill_opacity=0.5)

# Exclusion (XOR)
exclusion = Exclusion(circle, square, color=YELLOW, fill_opacity=0.5)
```

### 18.3 Layering (z-index)

Control rendering order:

```python
circle.set_z_index(1)   # Render above
square.set_z_index(-1)  # Render below

# Or during creation
circle = Circle().set_z_index(2)
```

### 18.4 Custom Animations

Subclass Animation class:

```python
class CustomTransform(Animation):
    def __init__(self, mobject, **kwargs):
        super().__init__(mobject, **kwargs)
        
    def interpolate_mobject(self, alpha):
        # alpha ranges from 0 to 1 during animation
        # Modify self.mobject based on alpha
        self.mobject.scale(1 + alpha)
        self.mobject.rotate(alpha * TAU)
```

### 18.5 SVG Import

Import SVG files as mobjects:

```python
logo = SVGMobject("path/to/logo.svg", color=WHITE)
logo.scale(2)
self.play(DrawBorderThenFill(logo))
```

### 18.6 Image Import

Import raster images:

```python
img = ImageMobject("path/to/image.png")
img.scale(2)
self.play(FadeIn(img))
```

---

## 19. Render Command Reference

### 19.1 Basic Syntax

```bash
manim [options] <file.py> <SceneClass>
```

### 19.2 Quality Options

```bash
-ql, --quality l      # Low quality (480p, 15fps)
-qm, --quality m      # Medium quality (720p, 30fps)
-qh, --quality h      # High quality (1080p, 60fps)
-qk, --quality k      # 4K quality (2160p, 60fps)
```

### 19.3 Output Options

```bash
-p, --preview         # Preview after rendering
-f, --show_in_file_browser  # Open output directory
-s, --save_last_frame # Save last frame as image
--format png          # Render as PNG sequence
--format gif          # Render as GIF
```

### 19.4 Rendering Control

```bash
-n <number>           # Render from frame <number>
--from_animation_number <n>  # Start from animation n
-a                    # Render all scenes in file
--scene_names <name>  # Render specific scene
```

### 19.5 Performance

```bash
--renderer opengl     # Use OpenGL renderer (real-time)
--write_all           # Cache all animations
--flush_cache         # Clear cached partial movies
```

### 19.6 Configuration

```bash
--config_file <path>  # Custom config file
-c <key> <value>      # Set config value
--background_color <color>  # Set background
```

**Example Commands:**
```bash
# High quality with preview
manim -pqh scene.py MyScene

# Low quality, save last frame
manim -sql scene.py MyScene

# Render all scenes, medium quality
manim -aqm scenes.py

# Custom frame rate
manim -c frame_rate 120 -qh scene.py MyScene
```

---

## 20. Best Practices and Patterns

### 20.1 Scene Organization

Structure complex scenes hierarchically:

```python
class ComplexScene(Scene):
    def construct(self):
        self.setup_objects()
        self.introduce_concepts()
        self.demonstrate_relationships()
        self.conclude()
    
    def setup_objects(self):
        # Initialize mobjects
        pass
    
    def introduce_concepts(self):
        # First animation sequence
        pass
```

### 20.2 Reusable Components

Create helper functions:

```python
def create_labeled_point(coords, label_text):
    dot = Dot(coords, color=YELLOW)
    label = Text(label_text, font_size=24)
    label.next_to(dot, UR, buff=0.1)
    return VGroup(dot, label)

# Usage
point_a = create_labeled_point(LEFT*2, "A")
self.play(FadeIn(point_a))
```

### 20.3 Performance Optimization

Minimize rendering overhead:

```python
# Group similar animations
self.play(
    *[Create(obj) for obj in object_list],
    lag_ratio=0.1
)

# Use low quality during development
# manim -ql script.py MyScene

# Add caching for complex computations
@cache
def expensive_calculation(n):
    # ...
    return result
```

### 20.4 Debugging Techniques

```python
# Visualize coordinates
def show_point(point, label=""):
    dot = Dot(point, color=RED)
    text = Text(label, font_size=20).next_to(dot, UR)
    self.add(dot, text)
    return VGroup(dot, text)

# Pause and inspect
self.wait()  # Add waits to examine intermediate states

# Print coordinates
print(f"Center: {mobject.get_center()}")
```

---

## Appendix: Quick Reference Tables

### Common Animation Durations

| Animation Type | Typical run_time |
|---------------|------------------|
| Create/Write | 1-2 seconds |
| Transform | 1-2 seconds |
| FadeIn/Out | 0.5-1 second |
| Indicate | 0.5 seconds |
| Complex sequence | 3-5 seconds |

### Default buff Values

| Context | Default buff |
|---------|--------------|
| next_to() | 0.25 |
| to_edge() | 0.5 |
| to_corner() | 0.5 |
| VGroup.arrange() | 0.25 |

### Coordinate Extremes (Default Camera)

| Direction | Approximate Range |
|-----------|-------------------|
| x-axis | -7.11 to 7.11 |
| y-axis | -4.0 to 4.0 |
| Diagonal corners | (±7.11, ±4.0) |

---
