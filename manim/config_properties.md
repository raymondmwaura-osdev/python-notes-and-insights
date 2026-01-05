# Manim Configuration Options Reference

## Overview

Manim Community Edition utilizes an extensive configuration system that allows customization of rendering behavior, output settings, and scene properties. Configuration options can be set through multiple methods:

1. **Configuration files** (`.cfg` format, INI-style)
2. **Command-line arguments** (CLI flags)
3. **Programmatic assignment** (via the `config` object in Python code)

The configuration hierarchy follows this precedence order (lowest to highest):
- Library-wide defaults
- User-wide configuration file
- Folder-wide configuration file  
- Command-line arguments
- Programmatic changes

---

## Configuration File Format

Configuration options are specified in `.cfg` files under the `[CLI]` section header:

```ini
[CLI]
background_color = WHITE
frame_rate = 60
pixel_height = 1080
pixel_width = 1920
quality = high_quality
```

---

## Complete Configuration Options (Alphabetical)

### **aspect_ratio**
**Type:** Float  
**CLI Flag:** `-r` / `--resolution` (indirectly affects this)  
**Description:** The aspect ratio of the output frame, calculated as width divided by height in pixels. Automatically computed from pixel dimensions. When resolution is changed, this value updates accordingly to maintain consistency.  
**Valid Values:** Any positive float (typically 16/9 = 1.778 for widescreen)

---

### **assets_dir**
**Type:** String (directory path)  
**CLI Flag:** None  
**Description:** Directory path where video assets (images, vector graphics, sound files) used in scenes are located. Used by `ImageMobject` and `SVGMobject` to locate external media files.  
**Default:** Typically a subdirectory within the media folder

---

### **background_color**
**Type:** String or Color object  
**CLI Flag:** `-c` / `--background_color`  
**Description:** The background color of rendered scenes. Accepts color names (e.g., `WHITE`, `RED`, `BLUE`) or hexadecimal color codes (e.g., `#FF5733`).  
**Valid Values:** Named colors from Manim's color constants, hex codes, or RGB tuples  
**Example:** `background_color = WHITE` or `background_color = #1a1a1a`

---

### **background_opacity**
**Type:** Float  
**CLI Flag:** None (use `-t` / `--transparent` for 0.0 opacity)  
**Description:** Controls the opacity of the scene background, where 0.0 represents fully transparent and 1.0 represents fully opaque. When set below 1.0, the movie file extension automatically resolves to a format supporting transparency (typically `.mov`).  
**Valid Values:** 0.0 to 1.0  
**Default:** 1.0 (fully opaque)

---

### **bottom**
**Type:** Tuple (3D vector)  
**CLI Flag:** None (computed property)  
**Description:** Coordinate representing the center bottom edge of the frame. This is a computed property based on frame dimensions and cannot be directly set. Used for positioning mobjects relative to frame boundaries.  
**Read-only:** Yes (derived from `frame_height`)

---

### **custom_folders**
**Type:** Boolean  
**CLI Flag:** `--custom_folders`  
**Description:** When enabled, uses the custom folder structure defined in the `[custom_folders]` section of the configuration file instead of the default output directory organization. Allows users to specify custom paths for videos, images, tex files, etc.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **disable_caching**
**Type:** Boolean  
**CLI Flag:** `--disable_caching`  
**Description:** When enabled, disables the use of cached partial movie files during rendering. Cached files are still generated but not used to speed up subsequent renders. Useful for troubleshooting caching issues or ensuring a completely fresh render.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **disable_caching_warning**
**Type:** Boolean  
**CLI Flag:** None  
**Description:** When enabled, suppresses warnings about excessive submobjects during hashing operations in the caching system. Used to reduce console clutter when working with complex scenes containing many objects.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **dry_run**
**Type:** Boolean  
**CLI Flag:** `--dry_run`  
**Description:** Performs a dry run of the rendering process without actually outputting any image or video files. Useful for testing scene structure, validating syntax, and checking for errors without the time cost of full rendering. Also disables the preview window.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **enable_gui**
**Type:** Boolean  
**CLI Flag:** `--enable_gui`  
**Description:** Enables GUI (Graphical User Interface) interaction mode, providing interactive controls and visual tools during rendering. Particularly useful for the OpenGL renderer, allowing real-time manipulation of scenes. May impact rendering performance.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **enable_wireframe**
**Type:** Boolean  
**CLI Flag:** `--enable_wireframe`  
**Description:** Enables wireframe debugging mode when using the OpenGL renderer. Displays the wireframe edges of 3D models and geometry, helping developers understand scene structure and debug rendering issues. Only functional with OpenGL renderer.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **ffmpeg_loglevel**
**Type:** String  
**CLI Flag:** None (controlled by `--verbosity` flag indirectly)  
**Description:** Sets the verbosity level for FFmpeg's logging output. Controls how much information FFmpeg displays during video encoding. Automatically set based on Manim's overall verbosity setting.  
**Valid Values:** `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`  
**Default:** `ERROR`

---

### **flush_cache**
**Type:** Boolean  
**CLI Flag:** `--flush_cache`  
**Description:** When enabled, deletes all cached partial movie files before rendering begins. Ensures a completely clean slate for rendering, useful when cache corruption is suspected or when testing changes that might be masked by cached content.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **force_window**
**Type:** Boolean  
**CLI Flag:** `--force_window`  
**Description:** Forces the preview window to open when using the OpenGL renderer, even if other settings would normally suppress it. Intended primarily for debugging purposes. May impact rendering performance.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **format**
**Type:** String  
**CLI Flag:** `--format`  
**Description:** Specifies the output file format for rendered animations. Determines both the file extension and the encoding method used.  
**Valid Values:** `png`, `gif`, `mp4`, `webm`, `mov`  
**Default:** `mp4`  
**Notes:** 
- `png` renders each frame as a separate image
- `gif` creates animated GIF files
- `mov` supports transparency when used with `--transparent` flag

---

### **frame_height**
**Type:** Float  
**CLI Flag:** None (computed from other properties)  
**Description:** The height of the frame in logical units (not pixels). Manim's coordinate system uses this value to determine the vertical extent of the scene. Changing `frame_y_radius` automatically updates this value (frame_height = 2 × frame_y_radius).  
**Default:** 8.0 logical units  
**Read-only:** Primarily computed (can be set but affects other properties)

---

### **frame_rate**
**Type:** Integer or Float  
**CLI Flag:** `--fps` / `--frame_rate`  
**Description:** The number of frames rendered per second in the output video. Higher frame rates produce smoother motion but increase rendering time and file size.  
**Valid Values:** Any positive integer (common values: 15, 24, 30, 60, 120)  
**Default:** Depends on quality setting (15 for low, 30 for medium, 60 for high/production/4K)

---

### **frame_size**
**Type:** Tuple of two integers  
**CLI Flag:** None (computed property)  
**Description:** A tuple containing `(pixel_width, pixel_height)`. Read-only property that combines the pixel dimensions into a single value. Used internally for frame dimension queries.  
**Read-only:** Yes (computed from `pixel_width` and `pixel_height`)

---

### **frame_width**
**Type:** Float  
**CLI Flag:** None (computed from aspect ratio and height)  
**Description:** The width of the frame in logical units (not pixels). Automatically computed from `frame_height` and `aspect_ratio`. Changing this value will zoom the camera in or out, affecting how much of the scene is visible.  
**Default:** ~14.22 logical units (for 16:9 aspect ratio with height 8.0)  
**Notes:** Modifying this is not recommended; use camera transformations instead

---

### **frame_x_radius**
**Type:** Float  
**CLI Flag:** None (computed property)  
**Description:** Half of the frame width in logical units. Represents the distance from the center of the frame to its left or right edge. Computed as `frame_width / 2`.  
**Read-only:** Yes (derived from `frame_width`)

---

### **frame_y_radius**
**Type:** Float  
**CLI Flag:** None  
**Description:** Half of the frame height in logical units. Represents the distance from the center of the frame to its top or bottom edge. Setting this value automatically updates `frame_height` to maintain consistency (frame_height = 2 × frame_y_radius).  
**Default:** 4.0 logical units

---

### **from_animation_number**
**Type:** Integer  
**CLI Flag:** `-n` / `--from_animation_number`  
**Description:** Specifies the starting animation number for rendering. When combined with `upto_animation_number`, allows rendering of specific animation ranges within a scene. Animations are zero-indexed.  
**Valid Values:** Any non-negative integer  
**Default:** 0  
**Example:** `-n 3,7` renders animations 3 through 7

---

### **fullscreen**
**Type:** Boolean  
**CLI Flag:** `--fullscreen`  
**Description:** Expands the OpenGL preview window to maximum screen size (fullscreen mode). Only applicable when using the OpenGL renderer with live preview enabled.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **gui_location**
**Type:** String  
**CLI Flag:** `--gui_location`  
**Description:** Specifies the starting location for the GUI interface when `enable_gui` is active. Allows positioning of the GUI window on screen.  
**Valid Values:** Position identifiers (e.g., `center`, `topleft`, `topright`, `bottomleft`, `bottomright`) or pixel coordinates  
**Default:** System-dependent

---

### **images_dir**
**Type:** String (directory path)  
**CLI Flag:** None  
**Description:** Directory path where rendered image outputs (PNG files from `-s` / `--save_last_frame` or individual frames) are stored. Part of the output folder structure.  
**Default:** `{media_dir}/images/{module_name}`  
**Notes:** Uses f-string syntax with variables like `{media_dir}` and `{module_name}`

---

### **input_file**
**Type:** String or Path object  
**CLI Flag:** Positional argument (file path)  
**Description:** The path to the Python script file containing the scene(s) to be rendered. Set automatically from the command-line positional argument.  
**Valid Values:** Valid file path to a `.py` file  
**Required:** Yes (when using CLI)

---

### **left_side**
**Type:** Tuple (3D vector)  
**CLI Flag:** None (computed property)  
**Description:** Coordinate representing the middle of the left edge of the frame. Used for positioning mobjects relative to frame boundaries.  
**Read-only:** Yes (derived from `frame_width`)

---

### **log_dir**
**Type:** String (directory path)  
**CLI Flag:** `--log_dir`  
**Description:** Directory path where rendering log files are stored when `log_to_file` is enabled. Useful for debugging and keeping records of rendering sessions.  
**Default:** System temp directory or `{media_dir}/logs`

---

### **log_to_file**
**Type:** Boolean  
**CLI Flag:** `--log_to_file`  
**Description:** When enabled, redirects terminal output to a log file stored in the `log_dir`. Useful for debugging, capturing error messages, and maintaining render history.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **max_files_cached**
**Type:** Integer  
**CLI Flag:** None  
**Description:** Maximum number of partial movie files to keep in the cache before older files are removed. Controls cache size to prevent unlimited disk space consumption.  
**Valid Values:** Any positive integer  
**Default:** 100

---

### **media_dir**
**Type:** String (directory path)  
**CLI Flag:** `--media_dir`  
**Description:** Root directory for all Manim output files, including videos, images, TeX files, and cache. All other directory paths are typically derived from this base directory.  
**Default:** `./media`  
**Example:** `media_dir = /home/user/manim_output`

---

### **media_embed**
**Type:** Boolean  
**CLI Flag:** None (Jupyter-specific)  
**Description:** When using Manim in Jupyter notebooks, controls whether rendered videos are embedded directly in the notebook output. Only relevant in Jupyter environments.  
**Valid Values:** `True` / `False`  
**Default:** `True` (in Jupyter)

---

### **media_width**
**Type:** String  
**CLI Flag:** None (Jupyter-specific)  
**Description:** Specifies the display width of embedded media in Jupyter notebooks. Accepts CSS-style width values.  
**Valid Values:** CSS width specifications (e.g., `100%`, `800px`)  
**Default:** `100%`

---

### **movie_file_extension**
**Type:** String  
**CLI Flag:** None (set via `--format`)  
**Description:** The file extension for rendered movie files. Automatically determined based on the `format` setting and whether transparency is required.  
**Valid Values:** `.mp4`, `.mov`, `.webm`  
**Default:** `.mp4`  
**Notes:** Automatically changes to `.mov` when transparency is enabled

---

### **no_latex_cleanup**
**Type:** Boolean  
**CLI Flag:** `--no_latex_cleanup`  
**Description:** Prevents deletion of intermediate LaTeX files (`.aux`, `.dvi`, `.log`) produced during TeX and MathTex rendering. Useful for debugging LaTeX compilation issues.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **notify_outdated_version**
**Type:** Boolean  
**CLI Flag:** `--notify_outdated_version` / `--silent`  
**Description:** When enabled, Manim checks for and displays warnings about outdated installations, alerting users when newer versions are available.  
**Valid Values:** `True` / `False`  
**Default:** `True`

---

### **output_file**
**Type:** String  
**CLI Flag:** `-o` / `--output_file`  
**Description:** Custom filename for the rendered output. Overrides the default naming scheme (which uses the scene class name). File extension should be omitted; it will be added automatically based on the format.  
**Valid Values:** Any valid filename string  
**Example:** `-o my_animation` produces `my_animation.mp4`

---

### **partial_movie_dir**
**Type:** String (directory path)  
**CLI Flag:** None  
**Description:** Directory where cached partial movie files are stored. These files represent rendered portions of scenes and enable faster re-rendering when only parts of a scene change.  
**Default:** `{video_dir}/partial_movie_files/{scene_name}`  
**Notes:** Uses f-string syntax with variables like `{video_dir}` and `{scene_name}`

---

### **pixel_height**
**Type:** Integer  
**CLI Flag:** `-r` / `--resolution` (as part of `WIDTH,HEIGHT`)  
**Description:** The height of the output frame in pixels. Together with `pixel_width`, determines the final resolution of rendered videos and images.  
**Valid Values:** Any positive integer (common values: 480, 720, 1080, 1440, 2160)  
**Default:** Depends on quality setting (480 for low, 720 for medium, 1080 for high)

---

### **pixel_width**
**Type:** Integer  
**CLI Flag:** `-r` / `--resolution` (as part of `WIDTH,HEIGHT`)  
**Description:** The width of the output frame in pixels. Together with `pixel_height`, determines the final resolution of rendered videos and images.  
**Valid Values:** Any positive integer (common values: 854, 1280, 1920, 2560, 3840)  
**Default:** Depends on quality setting (854 for low, 1280 for medium, 1920 for high)

---

### **plugins**
**Type:** List of strings  
**CLI Flag:** None (set in config file)  
**Description:** Comma-separated list of plugin module names to enable. Plugins extend Manim's functionality with additional features, mobjects, or utilities.  
**Valid Values:** Valid Python module names of installed Manim plugins  
**Example:** `plugins = manim_physics, manim_slides`  
**Notes:** Use module names, not PyPI package names (though often identical)

---

### **preview**
**Type:** Boolean  
**CLI Flag:** `-p` / `--preview`  
**Description:** Automatically opens the rendered output after completion. Behavior depends on renderer: Cairo opens the video file in the system's default media player; OpenGL displays a live preview window during rendering.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **preview_command**
**Type:** String  
**CLI Flag:** `--preview_command`  
**Description:** Custom command to use for previewing output files. Allows specification of a preferred media player or viewer application.  
**Valid Values:** Any valid system command (e.g., `vlc`, `mpv`, `mplayer`)  
**Example:** `preview_command = vlc`

---

### **progress_bar**
**Type:** String  
**CLI Flag:** `--progress_bar`  
**Description:** Controls the display behavior of progress bars during rendering. Provides visual feedback on rendering progress.  
**Valid Values:** `display`, `leave`, `none`  
- `display`: Show progress bars during rendering (default)
- `leave`: Show progress bars and keep them after completion
- `none`: Disable progress bars entirely  
**Default:** `display`

---

### **quality**
**Type:** String  
**CLI Flag:** `-q` / `--quality`  
**Description:** Preset quality level that simultaneously sets `pixel_width`, `pixel_height`, and `frame_rate`. Provides convenient shorthand for common resolution/framerate combinations.  
**Valid Values:** `l`, `m`, `h`, `p`, `k` (or full names: `low_quality`, `medium_quality`, `high_quality`, `production_quality`, `fourk_quality`)  
- `l`: 854×480, 15 FPS
- `m`: 1280×720, 30 FPS
- `h`: 1920×1080, 60 FPS
- `p`: 2560×1440, 60 FPS
- `k`: 3840×2160, 60 FPS  
**Default:** `h` (high quality)

---

### **renderer**
**Type:** String (RendererType enum)  
**CLI Flag:** `--renderer`  
**Description:** Selects the rendering backend. Cairo is CPU-based and produces high-quality static renders; OpenGL is GPU-accelerated with real-time preview capabilities.  
**Valid Values:** `cairo`, `opengl`  
**Default:** `cairo`  
**Notes:** Capitalization is irrelevant (`Cairo`, `cairo`, `CAIRO` all work)

---

### **right_side**
**Type:** Tuple (3D vector)  
**CLI Flag:** None (computed property)  
**Description:** Coordinate representing the middle of the right edge of the frame. Used for positioning mobjects relative to frame boundaries.  
**Read-only:** Yes (derived from `frame_width`)

---

### **save_as_gif**
**Type:** Boolean  
**CLI Flag:** `-i` / `--save_as_gif` (deprecated; use `--format gif`)  
**Description:** When enabled, saves the rendered animation as an animated GIF file instead of a video. Now deprecated in favor of using `--format gif`.  
**Valid Values:** `True` / `False`  
**Default:** `False`  
**Status:** Deprecated (use `format = gif` instead)

---

### **save_last_frame**
**Type:** Boolean  
**CLI Flag:** `-s` / `--save_last_frame`  
**Description:** Renders only the final frame of the scene and saves it as a PNG image. Useful for quickly previewing scene compositions without full animation rendering. Fastest rendering option.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **save_pngs**
**Type:** Boolean  
**CLI Flag:** `-g` / `--save_pngs` (deprecated)  
**Description:** When enabled, saves every frame of the animation as individual PNG files. Useful for frame-by-frame analysis or external video editing.  
**Valid Values:** `True` / `False`  
**Default:** `False`  
**Status:** Deprecated (use `--format png` instead)

---

### **save_sections**
**Type:** Boolean  
**CLI Flag:** `--save_sections`  
**Description:** Creates separate video files for each section defined in the scene code (using `self.next_section()`), in addition to the complete movie file. Useful for modular scene construction and editing.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **scene_names**
**Type:** List of strings  
**CLI Flag:** Positional arguments (after file path)  
**Description:** List of scene class names to render from the input file. If empty and only one scene exists in the file, that scene is rendered automatically. If multiple scenes exist without specification, Manim prompts for selection.  
**Valid Values:** Valid scene class names from the input file  
**Example:** `manim file.py Scene1 Scene2 Scene3`

---

### **sections_dir**
**Type:** String (directory path)  
**CLI Flag:** None  
**Description:** Directory where individual section video files are stored when `save_sections` is enabled. Each section defined with `self.next_section()` generates a separate file here.  
**Default:** `{video_dir}/sections`

---

### **show_in_file_browser**
**Type:** Boolean  
**CLI Flag:** `-f` / `--show_in_file_browser`  
**Description:** Opens the output directory in the system's file browser after rendering completes. Provides quick access to rendered files.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **sound**
**Type:** Boolean or path  
**CLI Flag:** None  
**Description:** Controls sound-related behavior. Can enable notification sounds when rendering completes, or specify paths to audio files.  
**Valid Values:** `True` / `False` or file path  
**Default:** `False`  
**Notes:** Implementation may vary by version

---

### **tex_dir**
**Type:** String (directory path)  
**CLI Flag:** None  
**Description:** Directory where TeX-related files (LaTeX source, compiled SVG output) are stored. Used by `Tex` and `MathTex` objects.  
**Default:** `{media_dir}/Tex`

---

### **tex_template**
**Type:** TexTemplate object  
**CLI Flag:** `--tex_template` (file path)  
**Description:** Template object used when rendering LaTeX with `Tex` and `MathTex`. Contains preamble, document structure, and LaTeX packages to load. Can be customized for specialized mathematical notation or styling.  
**Valid Values:** TexTemplate instance  
**Example:** `tex_template = TexTemplate()` or load from file

---

### **tex_template_file**
**Type:** String (file path)  
**CLI Flag:** `--tex_template`  
**Description:** Path to a custom TeX template file. When specified, loads the template from the file instead of using the default. Useful for projects requiring specific LaTeX packages or custom preambles.  
**Valid Values:** Valid file path to a `.tex` template file  
**Example:** `tex_template_file = ./my_template.tex`

---

### **text_dir**
**Type:** String (directory path)  
**CLI Flag:** None  
**Description:** Directory where text-related output files are stored. Used for text rendering operations that don't involve LaTeX.  
**Default:** `{media_dir}/texts`

---

### **top**
**Type:** Tuple (3D vector)  
**CLI Flag:** None (computed property)  
**Description:** Coordinate representing the center top edge of the frame. Used for positioning mobjects relative to frame boundaries.  
**Read-only:** Yes (derived from `frame_height`)

---

### **transparent**
**Type:** Boolean  
**CLI Flag:** `-t` / `--transparent`  
**Description:** Renders the scene with a transparent background (alpha channel). For videos, automatically switches output format to `.mov` to support transparency. For images, outputs PNG with transparent background.  
**Valid Values:** `True` / `False`  
**Default:** `False`  
**Notes:** Equivalent to setting `background_opacity = 0.0`

---

### **upto_animation_number**
**Type:** Integer  
**CLI Flag:** `-n` / `--from_animation_number` (second value)  
**Description:** Specifies the ending animation number for rendering. Used together with `from_animation_number` to define an animation range. When omitted, renders from the start number to the end of the scene.  
**Valid Values:** Any non-negative integer  
**Example:** `-n 3,7` sets this to 7

---

### **use_projection_fill_shaders**
**Type:** Boolean  
**CLI Flag:** `--use_projection_fill_shaders`  
**Description:** Enables shaders for OpenGLVMobject fill that are compatible with transformation matrices. Affects rendering quality and behavior when using the OpenGL renderer. Technical option for advanced users.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **use_projection_stroke_shaders**
**Type:** Boolean  
**CLI Flag:** `--use_projection_stroke_shaders`  
**Description:** Enables shaders for OpenGLVMobject stroke that are compatible with transformation matrices. Affects rendering quality and behavior when using the OpenGL renderer. Technical option for advanced users.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **verbosity**
**Type:** String  
**CLI Flag:** `-v` / `--verbosity`  
**Description:** Sets the logging verbosity level for Manim's console output. Controls how much information is displayed during rendering, from detailed debug messages to only critical errors.  
**Valid Values:** `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`  
**Default:** `INFO`  
**Notes:** Also affects FFmpeg log level unless explicitly overridden

---

### **video_dir**
**Type:** String (directory path)  
**CLI Flag:** None  
**Description:** Directory where rendered video files are stored. Typically organized by module name and quality setting.  
**Default:** `{media_dir}/videos/{module_name}/{quality}`  
**Notes:** Uses f-string syntax; requires `module_name` and `quality` context

---

### **window_monitor**
**Type:** Integer  
**CLI Flag:** None  
**Description:** Specifies which monitor (display) to show the OpenGL preview window on in multi-monitor setups. Monitors are numbered starting from 0.  
**Valid Values:** Non-negative integer (monitor index)  
**Default:** 0 (primary monitor)

---

### **window_position**
**Type:** String  
**CLI Flag:** None  
**Description:** Sets the position of the OpenGL preview window on screen. Can use directional keywords or explicit pixel coordinates.  
**Valid Values:** 
- Directional: `UL`, `UR`, `DL`, `DR`, `ORIGIN`, `LEFT`, `RIGHT`, `UP`, `DOWN`
- Coordinates: `x,y` pixel position (e.g., `960,540`)  
**Example:** `window_position = UR` or `window_position = 100,100`

---

### **window_size**
**Type:** String  
**CLI Flag:** None  
**Description:** Defines the size of the OpenGL preview window. Can specify explicit dimensions or use automatic scaling.  
**Valid Values:** `width,height` in pixels (e.g., `1920,1080`) or `default` for automatic scaling  
**Default:** `default`

---

### **write_all**
**Type:** Boolean  
**CLI Flag:** `-a` / `--write_all`  
**Description:** Renders all scenes present in the input file, rather than requiring individual scene selection. Processes each scene class found in the Python file sequentially.  
**Valid Values:** `True` / `False`  
**Default:** `False`

---

### **write_to_movie**
**Type:** Boolean  
**CLI Flag:** `--write_to_movie`  
**Description:** When using the OpenGL renderer, controls whether to write the rendered output to a video file. By default, OpenGL only shows a live preview; this flag enables file output.  
**Valid Values:** `True` / `False`  
**Default:** `False` for OpenGL, `True` for Cairo

---

### **zero_pad**
**Type:** Integer  
**CLI Flag:** `-0` / `--zero_pad`  
**Description:** Controls zero padding for PNG file names when rendering individual frames. Specifies how many digits to use for frame numbering (e.g., with zero_pad=5, frame 1 becomes `00001.png`).  
**Valid Values:** 0 to 9  
**Default:** 0  
**Example:** `-0 5` produces files like `00001.png`, `00002.png`, etc.

---

## Configuration File Sections

Beyond the `[CLI]` section, Manim configuration files may include additional sections:

### **[custom_folders]**
Defines custom output folder structure when `custom_folders = True`. Override default directory paths:

```ini
[custom_folders]
media_dir = /custom/media
video_dir = /custom/videos
images_dir = /custom/images
text_dir = /custom/text
tex_dir = /custom/tex
log_dir = /custom/logs
partial_movie_dir = /custom/cache
```

### **[ffmpeg]**
FFmpeg-specific settings:

```ini
[ffmpeg]
loglevel = ERROR
```

### **[jupyter]**
Jupyter notebook-specific settings:

```ini
[jupyter]
media_embed = True
media_width = 100%
```

---

## Computed Properties

Several configuration options are **read-only** or **computed** from other values:

- `aspect_ratio` — Computed from `pixel_width` / `pixel_height`
- `frame_size` — Tuple of `(pixel_width, pixel_height)`
- `frame_width` — Computed from `frame_height` × `aspect_ratio`
- `frame_x_radius` — Computed as `frame_width / 2`
- `top`, `bottom`, `left_side`, `right_side` — Frame boundary coordinates

---

## Usage Examples

### Example 1: Basic Configuration File

```ini
[CLI]
# Output settings
output_file = my_scene
format = mp4
quality = high_quality

# Appearance
background_color = BLACK
transparent = False

# Rendering
frame_rate = 60
disable_caching = False

# Directories
media_dir = ./output
```

### Example 2: Programmatic Configuration

```python
from manim import *

# Set configuration programmatically
config.background_color = WHITE
config.frame_rate = 30
config.pixel_height = 1080
config.pixel_width = 1920
config.output_file = "custom_output"

class MyScene(Scene):
    def construct(self):
        # Scene code here
        pass
```
