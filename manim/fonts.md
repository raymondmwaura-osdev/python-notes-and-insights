# Manim Fonts Reference Document

## 1. Text Classes

### 1.1 Text
The primary class for rendering non-LaTeX text using the Pango library. Text objects behave as VGroup-like iterables, supporting slicing operations on individual characters.

**Class signature:**
```python
Text(text, fill_opacity=1.0, stroke_width=0, *, color=ManimColor('#FFFFFF'), 
     font_size=48, line_spacing=-1, font='', slant='NORMAL', weight='NORMAL', 
     t2c=None, t2f=None, t2g=None, t2s=None, t2w=None, gradient=None, 
     tab_width=4, warn_missing_font=True, height=None, width=None, 
     should_center=True, disable_ligatures=False, use_svg_cache=False)
```

### 1.2 MarkupText
A variant of Text that processes PangoMarkup, an HTML-like markup language enabling text styling through XML tags.

**Class signature:**
```python
MarkupText(text, fill_opacity=1, stroke_width=0, color=None, font_size=48, 
           line_spacing=-1, font='', slant='NORMAL', weight='NORMAL', 
           justify=False, gradient=None, tab_width=4, height=None, width=None, 
           should_center=True, disable_ligatures=False, warn_missing_font=True)
```

## 2. Font Management

### 2.1 Font Selection
The font parameter accepts either system fonts or fonts registered via register_font(). Font family names may differ across operating systems.

**Example:**
```python
text = Text("Hello", font="Arial")
text = Text("Hello", font="sans-serif")
```

### 2.2 Listing Available Fonts
The manimpango.list_fonts() function returns a sorted alphabetical list of fonts available to Pango, including system fonts and those added through register_font() or fc_register_font().

**Example:**
```python
import manimpango
available_fonts = manimpango.list_fonts()
```

### 2.3 Registering Custom Fonts
The register_font() function registers font files using native OS APIs, making fonts available for Pango. On Linux, it aliases fc_register_font(); on Windows and macOS, it utilizes native APIs.

**Context manager usage:**
```python
with register_font("path/to/font_file.ttf"):
    text = Text("Hello", font="Custom Font Name")
```

This functionality requires ManimPango >= v0.2.3 for macOS compatibility. Earlier versions raise AttributeError on macOS.

### 2.4 Google Fonts Integration
The manim-fonts plugin enables automatic downloading and registration of Google Fonts.

**Installation:**
```bash
pip install manim-fonts
```

**Usage:**
```python
from manim_fonts import *

class Example(Scene):
    def construct(self):
        with RegisterFont("Poppins") as fonts:
            text = Text("Hello World", font=fonts[0])
            self.play(Write(text))
```

Downloaded fonts are cached and reused in subsequent executions.

## 3. Font Properties

### 3.1 Slant
Slant specifies text style: NORMAL (default), ITALIC (Roman Style), or OBLIQUE (Italic Style). For many fonts, ITALIC and OBLIQUE appear similar.

**Constants:**
- `NORMAL`
- `ITALIC`
- `OBLIQUE`

**Example:**
```python
text = Text("Italic Text", slant=ITALIC)
```

### 3.2 Weight
Weight is an enumeration specifying font boldness, ranging numerically from 100 to 1000 with predefined values.

**Available weights (manimpango.Weight):**
- `THIN` (100)
- `ULTRALIGHT` (200)
- `LIGHT` (300)
- `BOOK` (380)
- `NORMAL` (400)
- `MEDIUM` (500)
- `SEMIBOLD` (600)
- `BOLD` (700)
- `ULTRABOLD` (800)
- `HEAVY` (900)
- `ULTRAHEAVY` (1000)

**Example:**
```python
text = Text("Bold Text", weight=BOLD)
text = Text("Heavy Text", weight=HEAVY, font="Open Sans")
```

**Accessing all weights programmatically:**
```python
import manimpango as mp

weight_list = dict(sorted(
    {weight: mp.Weight(weight).value for weight in mp.Weight}.items(),
    key=lambda x: x[1]
))
```

### 3.3 Font Size
Default: 48

**Example:**
```python
text = Text("Large Text", font_size=144)
```

### 3.4 Line Spacing
Default: -1

**Example:**
```python
text = Text("Multi-line\nText", line_spacing=1.3)
```

## 4. Selective Styling

### 4.1 Text-to-Color (t2c)
The t2c dictionary applies colors to specific text portions. Keys support Python slice notation (e.g., [2:-1], [4:8]) or exact text strings.

**Example:**
```python
text = Text("Hello World", t2c={"Hello": BLUE, "World": RED})
text = Text("Hello", t2c={"[1:-1]": BLUE})
```

### 4.2 Text-to-Font (t2f)
The t2f parameter assigns different fonts to text substrings.

**Example:**
```python
text = Text("Mixed Fonts", t2f={"Mixed": "Open Sans", "Fonts": "Courier"})
text = Text("Manim", t2f={"[2:-1]": "Open Sans"})
```

### 4.3 Text-to-Gradient (t2g)
The t2g parameter applies color gradients to text portions.

**Example:**
```python
text = Text("Gradient", t2g={"Gradient": (RED, BLUE)})
text = Text("Hello", t2g={"[1:-1]": (RED, GREEN)})
```

### 4.4 Text-to-Slant (t2s)
The t2s parameter applies slant styles to text substrings.

**Example:**
```python
text = Text("Italic Word", t2s={"Italic": ITALIC})
text = Text("Manim", t2s={"[2:-1]": ITALIC})
```

### 4.5 Text-to-Weight (t2w)
The t2w parameter applies weight styles to text substrings.

**Example:**
```python
text = Text("Bold Text", t2w={"Bold": BOLD}, font="Open Sans")
text = Text("Manim", t2w={"[2:]": HEAVY}, font="Open Sans")
```

## 5. Color and Gradients

### 5.1 Global Color
**Example:**
```python
text = Text("Colored Text", color=RED)
```

### 5.2 Global Gradient
**Example:**
```python
text = Text("Gradient Text", gradient=(BLUE, GREEN, RED))
```

## 6. Ligatures

### 6.1 Disabling Ligatures
Ligatures may cause issues with character-to-submobject mapping. The disable_ligatures parameter ensures one-to-one correspondence between characters and submobjects.

**Example:**
```python
text = Text("fl ligature", disable_ligatures=True)
```

When ligatures are enabled, character combinations like "fl" may render as single glyphs, complicating character iteration.

## 7. Text Iteration

Text objects function as iterables, enabling per-character manipulation. However, ligatures can disrupt this behavior.

**Example:**
```python
text = Text("Colors", font_size=96)
for letter in text:
    letter.set_color(random_bright_color())
```

## 8. MarkupText Specifics

### 8.1 PangoMarkup Tags
MarkupText supports HTML-like tags for formatting: &lt;b&gt; (bold), &lt;i&gt; (italic), &lt;u&gt; (underline), &lt;span&gt; (with attributes like foreground, font_family), and &lt;gradient&gt;.

**Example:**
```python
text = MarkupText('<span foreground="blue">Blue</span> <i>italic</i> <b>bold</b>')
text = MarkupText('all in sans <span font_family="serif">except this</span>', font="sans")
```

### 8.2 Color Specification
Colors in &lt;span&gt; tags accept hex triplets (#aabbcc) or CSS color names. For Manim constants (RED, BLUE_A), use Python f-strings.

**Example:**
```python
text = MarkupText(f'<span foreground="{RED_A}">Red text</span>')
```

### 8.3 Special Characters
HTML entities must be used for special characters: `&lt;` for <, `&gt;` for >, `&amp;` for &.

**Example:**
```python
text = MarkupText("special char &gt; or &lt;")
```

## 9. Multi-Language Support

Pango enables rendering of non-English scripts including Arabic, Chinese, Japanese, Korean, Hindi, and Tamil.

**Example:**
```python
japanese = Text("日本へようこそ", font="sans-serif")
arabic = Text("صباح الخير", font="sans-serif")
hindi = Text("नमस्ते", font="sans-serif")
```

**Warning:** Avoid mixing right-to-left (RTL) and left-to-right (LTR) languages in single Text objects.

## 10. Font Warnings

The warn_missing_font parameter (default: True) issues warnings when specified fonts are absent from manimpango.list_fonts(). Font names are case-sensitive.

**Example:**
```python
text = Text("Text", font="NonexistentFont", warn_missing_font=False)
```

## 11. LaTeX Fonts

### 11.1 TexFontTemplates
The TexFontTemplates class provides predefined LaTeX font templates for mathematical typesetting, based on the mathastext package.

**Usage:**
```python
from manim import Tex, TexFontTemplates

formula = Tex("E = mc^2", tex_template=TexFontTemplates.comic_sans)
```

**Available templates include:**
- american_typewriter
- apple_chancery
- baskerville_it
- comic_sans
- ecf_augie
- And numerous others

### 11.2 Custom LaTeX Templates
Custom templates can be created by modifying TexTemplate preambles.

**Example:**
```python
from manim import TexTemplate, Tex

my_template = TexTemplate()
my_template.add_to_preamble(r"\usepackage{custom_package}")

class CustomTex(Tex):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, tex_template=my_template, **kwargs)
```

## 12. Best Practices

1. **Font compatibility:** Verify font installation on the target system before deployment.
2. **Cross-platform considerations:** Font family names vary across operating systems; test accordingly.
3. **Ligature management:** Disable ligatures when per-character manipulation is required.
4. **Custom fonts:** Utilize register_font() within context managers for proper resource management.
5. **Performance:** Leverage use_svg_cache parameter for repeated text rendering optimization.
6. **Multi-language text:** Ensure fonts support required character sets and avoid RTL/LTR mixing.

## 13. Common Issues

### 13.1 Font Not Found
Verify font availability using manimpango.list_fonts(). Font names are case-sensitive.

### 13.2 Distorted Rendering
Some fonts may exhibit rendering artifacts (distorted letters). Attempt alternative font weights or variants.

### 13.3 Ligature Problems
When character-level access is necessary, explicitly disable ligatures via disable_ligatures=True.

## 14. Additional Parameters

- **fill_opacity:** Float (0.0-1.0), default 1.0
- **stroke_width:** Float, default 0
- **height:** Float or None, constrains text height
- **width:** Float or None, constrains text width
- **should_center:** Boolean, default True
- **tab_width:** Integer, default 4
- **use_svg_cache:** Boolean, default False

---
