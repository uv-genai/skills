---
name: arcade-lib
description: >
  Provides knowledge and navigation of the official Python Arcade game library
  (api.arcade.academy) documentation, including the Arcade Book, API reference,
  tutorials, and example code. Use when building games with the arcade library,
  especially for sprite handling, physics, collision detection, views,
  tilemaps, cameras, sound, and GUI widgets.
---

# Python Arcade Library Skill

## Overview

This skill helps the coding agent navigate the **[Python Arcade Library](https://api.arcade.academy/)** documentation. Arcade is an easy-to-learn Python library for creating 2D video games. It provides a friendly API for beginners and experts alike.

### Key Links

| Resource | URL |
|----------|-----|
| Latest docs (v4.0.0-dev) | https://api.arcade.academy/en/latest/ |
| Stable docs (v3.3.3) | https://api.arcade.academy/en/stable/ |
| API Reference | https://api.arcade.academy/en/latest/api_docs/arcade.html |
| Quick API Index | https://api.arcade.academy/en/latest/api_docs/quick_index.html |
| General Index | https://api.arcade.academy/en/latest/genindex.html |
| Examples Index | https://api.arcade.academy/en/latest/example_code/index.html |
| Tutorials Index | https://api.arcade.academy/en/latest/tutorials/index.html |
| GitHub Repo | https://github.com/pythonarcade/arcade |
| Arcade Book (Learn) | https://learn.arcade.academy/ |

### Installation

```bash
pip install arcade
```

Requires OpenGL 3.3+. See [install docs](https://api.arcade.academy/en/latest/get_started/install.html).

---

## Documentation Structure

The docs are organized into these main sections:

### 1. Get Started
- [Installation](https://api.arcade.academy/en/latest/get_started/install.html)
- [The Arcade Book](https://api.arcade.academy/en/latest/get_started/arcade_book.html) — links to the comprehensive tutorial book at learn.arcade.academy

### 2. API Reference (`api_docs/`)
- [`arcade.Window`](https://api.arcade.academy/en/latest/api_docs/api/window.html) — The main game window class
- [`arcade.Sprite`](https://api.arcade.academy/en/latest/api_docs/api/sprites.html) — Sprite class (position, scale, rotation, collision)
- [`arcade.SpriteList`](https://api.arcade.academy/en/latest/api_docs/api/sprite_list.html) — Optimized sprite container
- [`arcade.Scene` / `SpriteScene`](https://api.arcade.academy/en/latest/api_docs/api/sprite_scenes.html) — Scene management
- [`arcade.Texture`](https://api.arcade.academy/en/latest/api_docs/api/texture.html) — Texture loading
- [`arcade.View`](https://api.arcade.academy/en/latest/api_docs/api/window.html#arcade.View) — View/screen management
- [`Camera2D`](https://api.arcade.academy/en/latest/api_docs/api/camera_2d.html) — 2D camera with scrolling
- [Physics Engines](https://api.arcade.academy/en/latest/api_docs/api/physics_engines.html) — `PhysicsEngineSimple`, `PhysicsEnginePlatformer`
- [Sound](https://api.arcade.academy/en/latest/api_docs/api/sound.html) — Sound loading & playback
- [Drawing Primitives](https://api.arcade.academy/en/latest/api_docs/api/drawing_primitives.html) — `draw_circle_filled()`, `draw_rectangle_filled()`, etc.
- [Tilemap](https://api.arcade.academy/en/latest/api_docs/api/tilemap.html) — Tiled map loading
- [Path Finding](https://api.arcade.academy/en/latest/api_docs/api/path_finding.html) — A* pathfinding
- [GUI Widgets](https://api.arcade.academy/en/latest/api_docs/api/gui_widgets.html) — UIManager, UIButton, UITextArea, etc.
- [Easing](https://api.arcade.academy/en/latest/api_docs/api/easing.html) — Animation easing functions
- [Colors](https://api.arcade.academy/en/latest/api_docs/arcade.color.html) — Built-in color constants
- [Key constants](https://api.arcade.academy/en/latest/api_docs/arcade.key.html) — Keyboard key codes

### 3. Programming Guides (`programming_guide/`)
- [Sprites](https://api.arcade.academy/en/latest/programming_guide/sprites/index.html)
- [SpriteLists](https://api.arcade.academy/en/latest/programming_guide/sprites/spritelists.html)
- [Advanced Sprites](https://api.arcade.academy/en/latest/programming_guide/sprites/advanced.html)
- [Event Loop](https://api.arcade.academy/en/latest/programming_guide/event_loop.html)
- [Cameras](https://api.arcade.academy/en/latest/programming_guide/camera.html)
- [Textures](https://api.arcade.academy/en/latest/programming_guide/textures.html)
- [GUI](https://api.arcade.academy/en/latest/programming_guide/gui/index.html)
- [Keyboard Input](https://api.arcade.academy/en/latest/programming_guide/input/index.html)
- [Sound](https://api.arcade.academy/en/latest/programming_guide/sound.html)
- [Performance Tips](https://api.arcade.academy/en/latest/programming_guide/performance_tips.html)

### 4. Example Code (`example_code/index.html`)
The examples page has categories including:
- **Starting Templates** — minimal arcade.Window apps
- **Primitives** — drawing shapes, ShapeElementLists
- **Sprites** — player movement, NPC movement, easing, sprite properties
- **Platformers** — basic platformers, Tiled map editor maps
- **Audio** — sound effects, music
- **Cameras** — resizable windows, scrolling cameras
- **View Management** — instruction screens, game over screens, View sections
- **Procedural Generation** — maze generation, cave generation
- **GUI** — widgets, experimental widgets
- **Grid-Based Games** — array-backed grids
- **Physics** — PyMunk integration
- **Particle Systems** — emitters, fireworks
- **Shaders** — OpenGL shader examples

### 5. Tutorials (`tutorials/index.html`)
- [Simple Platformer](https://api.arcade.academy/en/latest/tutorials/platform_tutorial/index.html) — 14-step tutorial (install → multiple levels)
- [Pymunk Platformer](https://api.arcade.academy/en/latest/tutorials/pymunk_platformer/index.html) — Physics-based platformer
- [Card Game](https://api.arcade.academy/en/latest/tutorials/card_game/index.html)
- [Menu with GUI](https://api.arcade.academy/en/latest/tutorials/menu/index.html)
- [Views](https://api.arcade.academy/en/latest/tutorials/views/index.html) — Screen/view management
- [Hex Map](https://api.arcade.academy/en/latest/tutorials/hex_map/index.html)
- [Lights](https://api.arcade.academy/en/latest/tutorials/lights/index.html)
- [Raycasting](https://api.arcade.academy/en/latest/tutorials/raycasting/index.html)
- [CRT Filter](https://api.arcade.academy/en/latest/tutorials/crt_filter/index.html)
- [Shader Tutorials](https://api.arcade.academy/en/latest/tutorials/shader_tutorials.html)

### 6. The Arcade Book (learn.arcade.academy)
A separate tutorial site covering drawing, animation, sprites, sound, and more:
- https://learn.arcade.academy/en/latest/

---

## Common Game Development Tasks

### Opening a Window
```python
import arcade

SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
SCREEN_TITLE = "My Game"

class MyGame(arcade.Window):
    def __init__(self):
        super().__init__(SCREEN_WIDTH, SCREEN_HEIGHT, SCREEN_TITLE)
        arcade.set_background_color(arcade.color.BLACK)

    def on_draw(self):
        self.clear()
        # Draw things here

    def on_update(self, delta_time):
        # Game logic here
        pass

MyGame()
arcade.run()
```

### Sprites & SpriteLists
```python
# Create a sprite
player = arcade.Sprite(":resources:images/animated_characters/female_adventurer/femaleAdventurer_idle.png", scale=0.5)
player.center_x = 400
player.center_y = 300

# Create a SpriteList and add
sprite_list = arcade.SpriteList()
sprite_list.append(player)

# Draw all sprites
sprite_list.draw()

# Update all sprites
sprite_list.on_update(delta_time)
```

### Collision Detection
```python
# Check collision between two sprites
if arcade.check_for_collision(player, enemy):
    print("Hit!")

# Check collision with a list
hits = arcade.check_for_collision_with_list(player, coins)
for coin in hits:
    coin.remove_from_sprite_list()
    score += 1

# Check collision with lists (bullets → enemies)
hits = arcade.check_for_collision_with_lists(bullet, [enemy_list, wall_list])
```

### Keyboard Input
```python
def on_key_press(self, key, modifiers):
    if key == arcade.key.UP or key == arcade.key.W:
        self.player.change_y = MOVEMENT_SPEED
    elif key == arcade.key.LEFT or key == arcade.key.A:
        self.player.change_x = -MOVEMENT_SPEED
    elif key == arcade.key.RIGHT or key == arcade.key.D:
        self.player.change_x = MOVEMENT_SPEED

def on_key_release(self, key, modifiers):
    if key in (arcade.key.UP, arcade.key.W, arcade.key.S, arcade.key.DOWN):
        self.player.change_y = 0
    elif key in (arcade.key.LEFT, arcade.key.A, arcade.key.RIGHT, arcade.key.D):
        self.player.change_x = 0
```

### Views (Screens)
```python
class MenuView(arcade.View):
    def on_show_view(self):
        self.window.background_color = arcade.color.BLACK

    def on_draw(self):
        self.clear()
        arcade.draw_text("Press SPACE to start", 400, 300,
                        arcade.color.WHITE, 20, anchor_x="center")

    def on_key_press(self, key, _modifiers):
        if key == arcade.key.SPACE:
            game_view = GameView()
            self.window.show_view(game_view)

class GameView(arcade.View):
    def __init__(self):
        super().__init__()
        # Initialize game state

    def on_draw(self):
        self.clear()
        # Draw game
```

### Physics (Platformer)
```python
# Simple platformer physics
self.physics_engine = arcade.PhysicsEnginePlatformer(
    self.player_sprite,
    gravity_constant=0.5,
    walls=self.wall_list
)

def on_update(self, delta_time):
    self.physics_engine.update()
```

### Loading from Tiled Map
```python
tile_map = arcade.load_tilemap("map.tmx")
self.scene = arcade.Scene.from_tilemap(tile_map)
```

### Taking a Screenshot
```python
import arcade

class MyGame(arcade.Window):
    def on_key_press(self, key, modifiers):
        if key == arcade.key.F12:  # Press F12 to take screenshot
            # Returns a PIL Image
            image = arcade.get_image()
            image.save("screenshot.png")
```

Full signature:
```python
# Full-screen screenshot (default)
image = arcade.get_image()

# Crop a region
image = arcade.get_image(x=0, y=0, width=800, height=600)

# RGB instead of RGBA (saves ~25% file size)
image = arcade.get_image(components=3)

# Save
image.save("screenshot.png")
```

Returns a `PIL.Image.Image`, so all standard Pillow operations apply.

---

## How to Use This Skill

When the agent needs to learn how to implement a specific feature in Arcade:

1. **Search for the relevant API page** using web search tools (e.g., `brave-search "site:api.arcade.academy/en/latest/ arcade.sprite collision"`)
2. **Fetch the relevant doc page** content (e.g., `linkup-search fetch "https://api.arcade.academy/en/latest/api_docs/api/sprites.html" --json`)
3. **Check the example code** by searching `site:api.arcade.academy/en/latest/example_code/ <topic>`
4. **Reference the tutorials** for full walkthroughs
5. **Pull latest version info** with `brave-search "python arcade latest version pypi"`

The official GitHub repo also has the full example source at:
`https://github.com/pythonarcade/arcade/tree/development/arcade/examples/`
