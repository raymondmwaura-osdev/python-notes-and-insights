# Manim CLI Command Reference

## Global Options

These options are available for the root `manim` command, regardless of which subcommand is invoked.

### `manim [OPTIONS] COMMAND [ARGS]`

**Global Flags:**
- `--version` — Display the current Manim version and exit
- `--show-splash` — Print splash message with version information (default behavior)
- `--hide-splash` — Suppress the splash message on startup
- `--help` / `-h` — Display help information and exit

**Note:** If no command is specified, Manim defaults to the `render` command.

---

## Main Commands

Manim provides five primary subcommands, each serving a distinct purpose in the animation workflow.

### Command Overview

| Command | Purpose |
|---------|---------|
| `render` | Render scene(s) from a Python script (default command) |
| `cfg` | Manage Manim configuration files |
| `checkhealth` | Verify installation and dependencies |
| `init` | Initialize new projects or create scene templates |
| `plugins` | Manage Manim plugins |

---

## Render Command (Primary)

The `render` command is the primary interface for creating animations. It processes Python scripts containing scene definitions and generates video or image output.

### Basic Syntax

```bash
manim render [OPTIONS] FILE [SCENE_NAMES]...
# or equivalently (render is default):
manim [OPTIONS] FILE [SCENE_NAMES]...
```

### Arguments

**`FILE`** (Required)
Path to the Python script containing scene definitions.

**`SCENE_NAMES`** (Optional)
One or more scene class names to render. 
- If omitted and the file contains a single scene, that scene is rendered automatically
- If multiple scenes exist without specification, Manim prompts for interactive selection
- Multiple scene names can be specified to render several scenes in sequence

---

### Quality Flags (Mutually Exclusive)

These flags control the resolution and frame rate of the rendered output. Only one quality flag should be used per invocation.

**`-ql` / `--quality l`**
Low quality rendering for rapid prototyping.
- Resolution: 854×480 pixels
- Frame rate: 15 FPS

**`-qm` / `--quality m`**
Medium quality rendering for standard previews.
- Resolution: 1280×720 pixels (720p)
- Frame rate: 30 FPS

**`-qh` / `--quality h`**
High quality rendering for final output.
- Resolution: 1920×1080 pixels (1080p)
- Frame rate: 60 FPS

**`-qp` / `--quality p`**
Production quality rendering.
- Resolution: 2560×1440 pixels (2K)
- Frame rate: 60 FPS

**`-qk` / `--quality k`**
4K quality rendering for highest fidelity.
- Resolution: 3840×2160 pixels (4K)
- Frame rate: 60 FPS

---

### Output Control Flags

**`-o <filename>` / `--output_file <filename>`**
Specifies the output filename for the rendered scene(s). Allows custom naming instead of using the scene class name.

**`-0 <padding>` / `--zero_pad <padding>`**
Controls zero padding for PNG file names when rendering individual frames (range: 0-9).

**`--format [png|gif|mp4|webm|mov]`**
Determines the output file format. Default is MP4 for animations.
- `png` — Renders each frame as a separate PNG image
- `gif` — Produces an animated GIF file
- `mp4` — Standard MP4 video format (default)
- `webm` — WebM video format
- `mov` — QuickTime MOV format

**`-s` / `--save_last_frame`**
Renders only the final frame of the scene and saves it as a PNG image. Useful for quick previews of static compositions.

**`-g` / `--save_pngs`**
Saves every frame of the animation as individual PNG files in addition to the video output.

**`--save_sections`**
Creates separate video files for each section defined in the scene code, in addition to the complete movie file.

---

### Animation Range Flags

**`-n <start>,<end>` / `--from_animation_number <start>,<end>`**
Controls which animations within a scene are rendered.
- `<start>` — Index of the first animation to render (0-indexed)
- `<end>` — Index of the last animation to render (optional; if omitted, renders from start to end of scene)
- Example: `-n 3,7` renders animations 3 through 7

**`-a` / `--write_all`**
Renders all scenes present in the input file, rather than requiring individual scene selection.

---

### Preview and Display Flags

**`-p` / `--preview`**
Automatically opens the rendered output after completion.
- For Cairo renderer: Opens the video file in the system's default media player
- For OpenGL renderer: Displays a live preview in a popup window during rendering

**`-f` / `--show_in_file_browser`**
Opens the output directory in the system's file browser after rendering completes.

**`--preview_command <command>`**
Specifies a custom command to preview the output file (e.g., `vlc` for video files).

---

### Resolution and Frame Rate Flags

**`-r <width>,<height>` / `--resolution <width>,<height>`**
Overrides default resolution with custom dimensions (in pixels).
- Example: `-r 1920,1080` sets Full HD resolution

**`--fps <rate>`**
Sets a custom frame rate (frames per second) for the animation.

---

### Scene Appearance Flags

**`-c <color>` / `--background_color <color>`**
Sets the background color of the scene.
- Accepts color names (e.g., `WHITE`, `RED`, `BLUE`) or hex codes
- Example: `-c "#FF5733"`

**`--background_opacity <value>`**
Controls background opacity (0.0 for fully transparent, 1.0 for fully opaque).

**`-t` / `--transparent`**
Renders the scene with a transparent background (alpha channel).
- For videos: Outputs in MOV format with transparency
- For images: Outputs PNG with transparent background

---

### Renderer Selection

**`--renderer [cairo|opengl]`**
Chooses the rendering backend.
- `cairo` — Default renderer, produces high-quality static renders
- `opengl` — GPU-accelerated renderer with real-time preview capabilities

**`--write_to_movie`**
When using the OpenGL renderer, writes the rendered output to a video file (by default, OpenGL only shows live preview).

**`--use_projection_fill_shaders`**
Enables shaders for OpenGLVMobject fill that are compatible with transformation matrices.

**`--use_projection_stroke_shaders`**
Enables shaders for OpenGLVMobject stroke compatible with transformation matrices.

---

### Caching Flags

**`--disable_caching`**
Disables the use of cached partial movie files. Cached files are still generated but not used to speed up rendering.

**`--flush_cache`**
Deletes all cached partial movie files before rendering begins.

---

### Directory and File Management

**`--config_file <path>`**
Specifies an alternative configuration file to use instead of the default `manim.cfg`.

**`--custom_folders`**
Uses the custom folder structure defined in the `[custom_folders]` section of the configuration file.

**`--media_dir <path>`**
Sets the directory path for storing rendered videos and LaTeX output.

**`--log_dir <path>`**
Specifies the directory path for storing render logs.

**`--log_to_file`**
Redirects terminal output to a log file for debugging purposes.

---

### Progress and Verbosity Flags

**`--progress_bar [display|leave|none]`**
Controls progress bar display behavior.
- `display` — Shows progress bars during rendering (default)
- `leave` — Shows progress bars and keeps them displayed after completion
- `none` — Disables progress bar display

**`-v <level>` / `--verbosity <level>`**
Sets the logging verbosity level.
- Levels: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`
- Example: `-v WARNING` shows only warnings and errors

---

### Advanced Options

**`--dry_run`**
Performs a dry run without actually rendering frames. Useful for testing configuration and scene structure.

**`--notify_outdated_version`**
Enables notifications when a newer version of Manim is available.

**`--jupyter`**
Indicates that Manim is being invoked from a Jupyter notebook magic command (used internally).

---

### Window Configuration (OpenGL Renderer)

**`--window_position <position>`**
Sets the position of the OpenGL preview window.
- Can use directional keywords: `UL`, `UR`, `DL`, `DR`, `ORIGIN`, `LEFT`, `RIGHT`, `UP`, `DOWN`
- Or specify pixel coordinates: `<x>,<y>` (e.g., `960,540`)

**`--window_size <width>,<height>`**
Defines the size of the OpenGL preview window.
- Use `default` to automatically scale based on display monitor

**`--window_monitor <number>`**
Specifies which monitor to display the OpenGL window on (for multi-monitor setups).

**`--fullscreen`**
Expands the OpenGL preview window to maximum screen size.

**`--force_window`**
Forces window display when using the OpenGL renderer, even if other settings would suppress it.

**`--enable_wireframe`**
Enables wireframe debugging mode in the OpenGL renderer.

---

### Render Command Examples

**Example 1: Basic Low-Quality Preview**
```bash
manim -pql scene.py MyScene
```
Renders `MyScene` from `scene.py` at low quality and previews the result.

**Example 2: High-Quality with Custom Output Name**
```bash
manim -qh -o final_animation scene.py MyScene
```
Renders at 1080p 60fps and saves as `final_animation.mp4`.

**Example 3: Transparent Background as GIF**
```bash
manim -t --format gif -o transparent_scene scene.py MyScene
```
Renders with transparency and outputs as an animated GIF.

**Example 4: Render Specific Animation Range**
```bash
manim -n 5,10 -qm scene.py MyScene
```
Renders only animations 5 through 10 at medium quality.

**Example 5: Render All Scenes in File**
```bash
manim -a -qh scene.py
```
Renders all scenes in `scene.py` at high quality.

**Example 6: Last Frame Only**
```bash
manim -s -qh scene.py MyScene
```
Renders only the last frame as a high-resolution PNG image.

**Example 7: Custom Resolution and Frame Rate**
```bash
manim -r 2560,1440 --fps 120 scene.py MyScene
```
Renders at 2K resolution with 120 frames per second.

**Example 8: OpenGL with Live Preview**
```bash
manim --renderer opengl scene.py MyScene
```
Uses GPU-accelerated OpenGL renderer with live preview window.

---

## Configuration Management Commands

### `manim cfg`

Manages Manim configuration files. This command provides subcommands for viewing, exporting, and creating configuration files.

**Syntax:**
```bash
manim cfg [SUBCOMMAND] [OPTIONS]
```

**Available Subcommands:**

#### `manim cfg show`
Displays the current configuration settings being used by Manim.

**Options:**
- `-h` / `--help` — Show help message and exit

**Purpose:** Shows the effective configuration, including values from all configuration sources (library defaults, user config, folder config, command-line arguments).

---

#### `manim cfg write`
Creates a configuration file in the specified location.

**Options:**
- `-l <level>` / `--level [user|cwd]` — Specify if this config is for the user's home directory or the current working directory
- `-o` / `--open` — Open the configuration file in the default text editor after creation
- `-h` / `--help` — Show help message and exit

**Purpose:** Facilitates quick creation of configuration files without manual file creation.

**Example:**
```bash
# Create a config file in the current directory
manim cfg write -l cwd

# Create a user-wide config file and open it
manim cfg write -l user -o
```

---

#### `manim cfg export`
Exports the current configuration to a file.

**Options:**
- `-d <directory>` / `--dir <directory>` — Specify the directory to export the configuration file to
- `-h` / `--help` — Show help message and exit

**Purpose:** Allows users to save their current effective configuration for reuse or sharing.

---

## Utility Commands

### `manim checkhealth`

Verifies that Manim is installed correctly and that all dependencies are functional.

**Syntax:**
```bash
manim checkhealth [OPTIONS]
```

**Options:**
- `-h` / `--help` — Show help message and exit

**Purpose:** Diagnostic tool to validate installation integrity. This command checks:
- Python environment and Manim installation
- FFmpeg availability and functionality
- LaTeX installation (for text rendering)
- Other required system dependencies

**When to Use:** Run this command when:
- First installing Manim
- Troubleshooting rendering issues
- Verifying system configuration after updates
- Diagnosing missing dependencies

**Example Output:** The command provides detailed feedback on each dependency, indicating whether it's correctly installed and functional.

---

### `manim init`

Initializes a new Manim project or creates scene templates.

**Syntax:**
```bash
manim init [SUBCOMMAND] [OPTIONS] [PROJECT_NAME]
```

**Available Subcommands:**

#### `manim init project <project_name> [OPTIONS]`
Creates a complete new project with directory structure and template files.

**Arguments:**
- `<project_name>` — Name of the project directory to create

**Options:**
- `--default` — Use default settings without prompting for configuration
- `-h` / `--help` — Show help message and exit

**What It Creates:**
- Project directory with the specified name
- `main.py` — Template Python file with sample scene
- `manim.cfg` — Configuration file with basic settings
- Standard folder structure for outputs

**Example:**
```bash
# Create a project with interactive prompts
manim init project my-animation

# Create a project with default settings
manim init project my-animation --default
```

---

#### `manim init scene` *(if available)*
Inserts a new scene template into an existing project file.

**Purpose:** Provides boilerplate code for new scenes without manually writing repetitive imports and class structures.

---

### `manim plugins`

Manages Manim plugins, allowing users to extend Manim's functionality with third-party extensions.

**Syntax:**
```bash
manim plugins [OPTIONS]
```

**Options:**
- `-l` / `--list` — List all available (installed) plugins in the current environment
- `-h` / `--help` — Show help message and exit

**Purpose:** Provides visibility into which plugins are installed and available for use.

**Example:**
```bash
# List all installed plugins
manim plugins -l
```

**Sample Output:**
```
Plugins:
• manim_rubikscube
• manim_physics
• manim_slides
```

**Enabling Plugins:**

Plugins are not automatically enabled after installation. To enable a plugin, you must specify it in one of two ways:

**Method 1: Configuration File**
Add the plugin to your `manim.cfg` file:
```ini
[CLI]
plugins = manim_rubikscube
```

For multiple plugins, use comma-separated values:
```ini
[CLI]
plugins = manim_rubikscube, manim_physics, manim_slides
```

**Method 2: Command-Line Flag** *(if supported)*
Some versions may support enabling plugins via command-line arguments.

**Important Note:** The plugin name used in configuration should be the **module name** of the plugin, not the PyPI package name (though these are often identical).

---

## Additional Notes

### Default Behavior
- When no command is specified, `manim` defaults to the `render` command
- When no scene name is provided and the file contains only one scene, that scene is rendered automatically
- If multiple scenes exist without specification, Manim prompts for interactive selection

### Flag Combinations
- Quality flags can be combined with `-p` (preview) as shortcuts: `-pql`, `-pqh`, etc.
- Multiple flags can be combined in a single command
- Example: `manim -pql -c WHITE -o output scene.py Scene`

### Performance Considerations
- Use `-ql` for rapid iteration and testing
- Enable `--disable_caching` if experiencing cache-related issues
- Use `--dry_run` to validate scene structure without rendering
- OpenGL renderer (`--renderer opengl`) provides real-time preview for faster iteration

### Troubleshooting
- Run `manim checkhealth` to diagnose installation issues
- Use `-v DEBUG` for detailed logging output
- Check `manim.cfg` for conflicting configuration settings
- Consult `manim --help` or `manim <command> --help` for command-specific documentation

---
