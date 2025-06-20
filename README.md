# bs-hsv: Hit Score Visualization

[![PyPI version](https://img.shields.io/pypi/v/bs-hsv.svg)](https://pypi.org/project/bs-hsv/)
[![Python Version](https://img.shields.io/pypi/pyversions/bs-hsv.svg)](https://pypi.org/project/bs-hsv/)
[![License](https://img.shields.io/pypi/l/bs-hsv.svg)](https://github.com/CodeSoftGit/bs-hsv/blob/main/LICENSE)

**bs-hsv** is a Pydantic-based Python module for creating, managing, and visualizing hit score judgments with beautiful, intuitive syntax. Perfect for rhythm games, scoring systems, and visualizations that require threshold-based text display.

## Features

- 🎯 **Type-Safe**: Built with Pydantic for robust data validation
- 🎨 **Flexible Colors**: Support for hex, RGB, RGBA, and named colors
- 📊 **Intuitive API**: Clean, chainable methods for a great developer experience
- 🖼️ **Image Support**: Convert images to colored text art
- 🔄 **Easy Serialization**: Simple JSON import/export
- 🧩 **Modular Design**: Well-structured codebase with clear separation of concerns

## Installation

```bash
pip install bs-hsv
```

## Quick Start

```python
from bs_hsv import HSVConfig, Color

# Create a new configuration
config = HSVConfig()

# Add judgments with method chaining and various color formats
config.add(115, "PERFECT", "#FF00FF", fade=True) \
      .add(110, "EXCELLENT", [0, 1, 0, 1]) \
      .add(100, "GOOD", Color(r=1, g=0.5, b=0))

# Add an image-based judgment
config.add_image(0, "logo.png", max_width=40)

# Save the configuration
config.save("hsv_config.json")

# Load a configuration
loaded_config = HSVConfig.load("hsv_config.json")
print(loaded_config)

# Get judgment for a specific score
score = 115
judgment = config.get_judgment_for_score(score)
if judgment:
    print(f"Score {score} gets judgment: {judgment.text} (color: {judgment.color})")
```

## Core Components

### Color Class

The `Color` class represents RGBA colors with values between 0.0 and 1.0:

```python
from bs_hsv import Color

# Various ways to create colors
white = Color.white()  # Predefined color
red = Color.red()      # Predefined color
blue = Color.from_hex("#0000FF")  # From hex string
green = Color.from_rgb(0, 255, 0)  # From RGB values (0-255)
custom = Color(r=0.5, g=0.7, b=1.0, a=0.8)  # Direct RGBA (0-1)

# Color manipulation
lighter = custom.lighten(0.1)
darker = custom.darken(0.2)
transparent = custom.with_alpha(0.5)

# Color conversion
hex_code = custom.to_hex()           # "#80B3FF"
hex_with_alpha = custom.to_hex(include_alpha=True)  # "#80B3FFCC"
rgba_list = custom.to_rgba()         # [0.5, 0.7, 1.0, 0.8]
rgb_tuple = custom.to_rgb_tuple()    # (128, 179, 255)
```

### Judgment Class

The `Judgment` class represents a hit score judgment with threshold, text, and styling:

```python
from bs_hsv import Judgment, Color

# Create a judgment
judgment = Judgment(
    threshold=115,
    text="EXCELLENT",
    color=Color.from_hex("#00FF00"),
    fade=True
)

# Judgments are comparable based on threshold
judgments = [
    Judgment(threshold=100, text="GOOD"),
    Judgment(threshold=115, text="PERFECT"),
    Judgment(threshold=110, text="GREAT")
]
sorted_judgments = sorted(judgments, reverse=True)  # Highest threshold first
```

### HSVConfig Class

The `HSVConfig` class manages a collection of judgments:

```python
from bs_hsv import HSVConfig, Color

# Create a configuration
config = HSVConfig()

# Add judgments with method chaining
config.add(115, "PERFECT", "#FF00FF", fade=True) \
      .add(110, "EXCELLENT", [0, 1, 0, 1]) \
      .add(100, "GOOD", Color(r=1, g=0.5, b=0))

# Find a judgment for a score
score = 110
judgment = config.get_judgment_for_score(score)
if judgment:
    print(f"Text: {judgment.text}, Color: {judgment.color}")

# Remove a judgment
config.remove(100)

# Save and load
config.save("config.json")
loaded = HSVConfig.load("config.json")

# Create a copy
config_copy = config.clone()
```

### TextArtGenerator

The `TextArtGenerator` class provides utilities for generating text art:

```python
from bs_hsv import TextArtGenerator

# Create text art from an image
text_art = TextArtGenerator.from_image("logo.png", max_width=40)

# Create gradient text
gradient = TextArtGenerator.gradient_text(
    "RAINBOW TEXT",
    start_color="#FF0000",
    end_color="#0000FF"
)

# Create pattern
pattern = TextArtGenerator.create_pattern(
    width=10,
    height=3,
    pattern_type="checkerboard",
    color1="#FFFF00",
    color2="#000000"
)
```

## Utility Functions

The HSV module includes various utility functions:

```python
from bs_hsv.utils import merge_configs, generate_color_scheme, color_interpolate

# Merge configurations
merged = merge_configs([config1, config2], strategy="unique")

# Generate a color scheme
base_color = Color.from_hex("#3498DB")
colors = generate_color_scheme(base_color, "analogous", count=5)

# Interpolate between colors
mid_color = color_interpolate(Color.red(), Color.blue(), 0.5)
```

## Advanced Examples

### Creating a Game Scoring System

```python
from bs_hsv import HSVConfig, Color

# Create a scoring system for a rhythm game
scoring = HSVConfig()

# Define judgments with different colors and thresholds
scoring.add(115, "PERFECT", "#FFFF00", fade=True) \
       .add(112, "EXCELLENT", "#00FFFF") \
       .add(110, "GREAT", "#00FF00") \
       .add(100, "GOOD", "#0000FF") \
       .add(99, "BAD", "#FF0000")

# Save configuration
scoring.save("rhythm_game_scoring.json")

# Example of how to use in a game
def display_hit_result(hit_score):
    judgment = scoring.get_judgment_for_score(hit_score)
    if judgment:
        # In a real game, you would display this text with the specified color
        print(f"Hit score: {hit_score} → {judgment.text}")
        
        # The judgment provides text, color, and fade information
        text = judgment.text
        color = judgment.color.to_rgba()  # [r, g, b, a] format
        fade = judgment.fade  # Whether to animate with fade effect
        
        # Game engine would use these values to display the judgment

# Test some hit scores
for score in [115, 112, 110, 100, 99]
    display_hit_result(score)
```

### Using Image-Based Judgments

```python
from bs_hsv import HSVConfig

# Create a configuration with image-based text art
image_config = HSVConfig()

# Add judgments with images converted to text art
image_config.add_image(115, "perfect.png", fade=True)
image_config.add_image(110, "great.png")
image_config.add_image(100, "good.png")
# Save the configuration
image_config.save("image_judgments.json")
```

### Using Color Schemes

```python
from bs_hsv import HSVConfig, Color
from bs_hsv.utils import generate_color_scheme

# Create a configuration with a color scheme
config = HSVConfig()

# Generate a color scheme based on a base color
base_color = Color.from_hex("#3498DB")  # Blue
color_scheme = generate_color_scheme(base_color, "triadic", count=3)

# Create judgments with different colors from the scheme
thresholds = [115, 110, 100]
texts = ["PERFECT", "GREAT", "GOOD"]

for i, (threshold, text) in enumerate(zip(thresholds, texts)):
    config.add(threshold, text, color_scheme[i])

# Save the configuration
config.save("color_scheme_config.json")
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.