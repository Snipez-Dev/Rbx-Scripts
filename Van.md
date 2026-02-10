# VanUI - Roblox UI Library

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Roblox](https://img.shields.io/badge/platform-Roblox-red.svg)](https://www.roblox.com)

A modern, feature-rich UI library for Roblox game development with built-in themes, config system, and mobile support.

---

## 📋 Table of Contents

- [Features](#-features)
- [Installation](#-installation)
- [Quick Start](#-quick-start)
- [API Reference](#-api-reference)
  - [Window](#window)
  - [Tabs](#tabs)
  - [Sections](#sections)
  - [Controls](#controls)
- [Themes](#-themes)
- [Config System](#-config-system)
- [Examples](#-examples)
- [Mobile Support](#-mobile-support)

---

## ✨ Features

- **Modern UI Design** - Clean, professional interface with smooth animations
- **Built-in Themes** - 7 pre-made themes (Winter, Easter, Neon, Forest, Sunset, Mono, Ocean)
- **Config System** - Save/load user settings with JSON serialization
- **Mobile Support** - Fully responsive with touch controls
- **Profile System** - Multiple value profiles per tab (TopTabs)
- **Notifications** - Toast-style notification system
- **Draggable & Resizable** - Customizable window positioning and sizing
- **Auto-scaling** - Adapts to different screen resolutions
- **Rich Controls** - Sliders, toggles, dropdowns, textboxes, buttons, and more

---

## 📦 Installation

### Method 1: Direct Load (Recommended)
```lua
local Vanith = loadstring(game:HttpGet("https://raw.githubusercontent.com/Snipez-Dev/Rbx-Scripts/refs/heads/main/VanUI"))()
```

### Method 2: Local Copy
1. Download the `VanUI` file
2. Save it to your workspace
3. Load it with `loadstring()`

---

## 🚀 Quick Start

### Basic Example

```lua
local Vanith = loadstring(game:HttpGet("https://raw.githubusercontent.com/Snipez-Dev/Rbx-Scripts/refs/heads/main/VanUI"))()

-- Create Window
local Window = Vanith:CreateWindow({
    Title = "My Script",
    Size = Vector2.new(649, 417),
    ThemePreset = "Neon", -- Optional: Use pre-made theme
    Draggable = true,
    AutoLoadConfig = true,
})

-- Create Tab
local MainTab = Window:CreateTab("Main")

-- Create Section
local Section = MainTab:CreateSection("Features", "LeftTop")

-- Add Controls
Section:CreateToggle({
    Name = "ESP",
    Default = false,
    Callback = function(enabled)
        print("ESP:", enabled)
    end
})

Section:CreateSlider({
    Name = "Speed",
    Min = 16,
    Max = 100,
    Default = 16,
    Step = 1,
    Format = function(v) return v .. " Speed" end,
    Callback = function(value)
        print("Speed:", value)
    end
})
```

---

## 📖 API Reference

### Window

#### `Vanith:CreateWindow(options)`

Creates a new window instance.

**Parameters:**
```lua
{
    Title = "Window Title",            -- Window title (default: "Vanith")
    Size = Vector2.new(649, 417),      -- Window size (default: 649x417)
    Position = UDim2.fromScale(0.5, 0.5), -- Window position
    ThemePreset = "Neon",              -- Pre-made theme name
    Theme = {                          -- Custom theme colors
        Accent = Color3.fromRGB(...),
        BG0 = Color3.fromRGB(...),
        -- ... see Themes section
    },
    Draggable = true,                  -- Allow dragging (default: true)
    AutoLoadConfig = true,             -- Auto-load default config
    ConfigFolder = "MyConfigs",        -- Config folder name
    ConfigExtension = "json",          -- Config file extension
    DefaultConfig = "default",         -- Default config name
    MinimizeText = "Open MyScript",    -- Text for minimize button
}
```

**Returns:** Window object

**Methods:**
```lua
Window:CreateTab(name)                -- Create new tab
Window:SetTheme(themeTable)           -- Update theme colors
Window:SetThemePreset(name)           -- Apply pre-made theme
Window:SetAccent(color)               -- Change accent color
Window:SetVisible(bool)               -- Show/hide window
Window:Minimize()                     -- Minimize window
Window:Restore()                      -- Restore window
Window:Destroy()                      -- Destroy window
Window:Notify(config)                 -- Show notification
Window:SaveConfig(name)               -- Save config
Window:LoadConfig(name)               -- Load config
Window:DeleteConfig(name)             -- Delete config
Window:ListConfigs()                  -- Get list of configs
```

---

### Tabs

#### `Window:CreateTab(name)`

Creates a new tab in the sidebar.

**Parameters:**
- `name` (string): Tab name

**Returns:** Tab object

**Methods:**
```lua
Tab:CreateSection(title, slot, topTab) -- Create section
Tab:CreateTopTab(name)                 -- Create top tab (profile)
Tab:SetTopTab(topTabObj)               -- Switch to top tab
Tab:SetTopTabByName(name)              -- Switch by name
Tab:GetActiveTopTabName()              -- Get current top tab name
```

**Slot Options:**
- `"LeftTop"` - Top-left quadrant
- `"LeftBottom"` - Bottom-left quadrant
- `"RightTop"` - Top-right quadrant
- `"RightBottom"` - Bottom-right quadrant
- `nil` - Auto-stack vertically

---

### Sections

#### `Tab:CreateSection(title, slot, topTab)`

Creates a section within a tab.

**Parameters:**
- `title` (string): Section title
- `slot` (string): Section position (see Slot Options)
- `topTab` (TopTab|string|nil): Associated top tab

**Returns:** Section object

**Methods:**
```lua
Section:CreateSlider(config)    -- Create slider
Section:CreateToggle(config)    -- Create toggle
Section:CreateButton(config)    -- Create button
Section:CreateTextbox(config)   -- Create textbox
Section:CreateDropdown(config)  -- Create dropdown
Section:CreateText(config)      -- Create text label
```

---

### Controls

#### Slider

```lua
Section:CreateSlider({
    Name = "Player Speed",
    Min = 0,
    Max = 100,
    Default = 16,
    Step = 1,
    Format = function(value)
        return value .. " Speed"  -- Optional: Custom display format
    end,
    Callback = function(value)
        print("New speed:", value)
    end
})
```

**Returns:** Slider object
```lua
slider:Set(value)   -- Set value programmatically
slider:Get()        -- Get current value
```

---

#### Toggle

```lua
Section:CreateToggle({
    Name = "Enable ESP",
    Default = false,
    Small = false,  -- Use checkbox style instead of switch
    Callback = function(enabled)
        print("ESP enabled:", enabled)
    end
})
```

**Returns:** Toggle object
```lua
toggle:Set(bool)   -- Set state
toggle:Get()       -- Get state
```

---

#### Button

```lua
Section:CreateButton({
    Name = "Execute Action",
    Callback = function()
        print("Button clicked!")
    end
})
```

**Returns:** Button object

---

#### Textbox

```lua
Section:CreateTextbox({
    Default = "Default text",
    Placeholder = "Enter text...",
    Callback = function(text, enterPressed)
        print("Text:", text)
        print("Enter pressed:", enterPressed)
    end
})
```

**Returns:** Textbox object
```lua
textbox:SetText(text)  -- Set text
textbox:GetText()      -- Get text
```

---

#### Dropdown

**Single Select:**
```lua
Section:CreateDropdown({
    Name = "Weapon",
    Options = {"Sword", "Bow", "Staff"},
    Default = "Sword",
    Multi = false,  -- Single selection
    MaxHeight = 200,  -- Max dropdown height (optional)
    Callback = function(selected)
        print("Selected:", selected)
    end
})
```

**Multi Select:**
```lua
Section:CreateDropdown({
    Name = "Features",
    Options = {"ESP", "Aimbot", "Speed"},
    Default = {"ESP"},  -- Array for multi
    Multi = true,
    Callback = function(selectedArray)
        print("Selected:", table.concat(selectedArray, ", "))
    end
})
```

**Returns:** Dropdown object
```lua
dropdown:Set(value)        -- Set value (string or table)
dropdown:Get()             -- Get value (string or table)
dropdown:SetOptions(array) -- Update options
```

---

#### Text Label

```lua
Section:CreateText({
    Text = "This is informational text",
    Align = "left",  -- "left", "center", "right"
    Size = 11,       -- Text size
    Wrap = true,     -- Text wrapping
})
```

**Returns:** Text object
```lua
text:SetText(newText)  -- Update text
text:GetText()         -- Get text
```

---

## 🎨 Themes

### Pre-made Themes

VanUI includes 7 built-in themes:

| Theme | Description |
|-------|-------------|
| **Winter** | Blue tones with falling snow particles |
| **Easter** | Pastel colors with bubble decorations |
| **Neon** | Cyberpunk style with animated lines |
| **Forest** | Green tones with light spots |
| **Sunset** | Warm orange/red gradient |
| **Mono** | Grayscale minimalist |
| **Ocean** | Blue tones with wave animation |

### Using Pre-made Themes

```lua
-- At window creation
local Window = Vanith:CreateWindow({
    Title = "My Script",
    ThemePreset = "Neon"
})

-- Change theme later
Window:SetThemePreset("Winter")
```

### Custom Theme

```lua
Window:SetTheme({
    Accent  = Color3.fromRGB(165, 105, 255),  -- Accent color
    BG0     = Color3.fromRGB(4, 4, 6),        -- Background layer 0
    BG1     = Color3.fromRGB(10, 10, 14),     -- Background layer 1
    BG2     = Color3.fromRGB(10, 10, 14),     -- Background layer 2
    Stroke  = Color3.fromRGB(40, 40, 50),     -- Border color
    Text    = Color3.fromRGB(230, 230, 238),  -- Primary text
    Muted   = Color3.fromRGB(140, 140, 150),  -- Secondary text
    DimText = Color3.fromRGB(95, 95, 110),    -- Tertiary text
    Track   = Color3.fromRGB(22, 22, 28),     -- Slider track
    Field   = Color3.fromRGB(16, 16, 20),     -- Input field background
})
```

### Registering Custom Themes

```lua
Vanith:RegisterTheme("MyTheme", {
    Theme = {
        Accent = Color3.fromRGB(255, 100, 100),
        -- ... other colors
    },
    Background = function(win)
        -- Optional: Custom background animation
        -- Return cleanup function
        return function()
            -- Cleanup code
        end
    end
})

-- Use it
Window:SetThemePreset("MyTheme")
```

---

## 💾 Config System

### Auto Config Setup

The easiest way to add config functionality:

```lua
local Window = Vanith:CreateWindow({
    Title = "My Script",
    AutoLoadConfig = true,  -- Auto-load on startup
    ConfigFolder = "MyScriptConfigs",
    DefaultConfig = "default"
})

-- Create config section
Vanith:Config({
    Window = Window,
    Tab = YourTab,  -- Optional: specific tab
    Title = "Config Manager",
    Slot = "RightBottom"
})
```

This creates a ready-to-use config section with:
- Save button
- Load button
- Delete button
- Config name input
- Config list dropdown

### Manual Config Management

```lua
-- Save current settings
Window:SaveConfig("myconfig")

-- Load saved settings
Window:LoadConfig("myconfig")

-- Delete config
Window:DeleteConfig("myconfig")

-- List all configs
local configs = Window:ListConfigs()
for _, name in ipairs(configs) do
    print(name)
end
```

### How Configs Work

- **Automatic Saving**: All control values are tracked automatically
- **Profile Support**: Different values per TopTab are saved separately
- **JSON Format**: Configs are stored as `.json` files
- **Location**: Saved in `workspace/{ConfigFolder}/` directory

**What Gets Saved:**
- Slider values
- Toggle states
- Dropdown selections
- Textbox content
- All values across different TopTabs (profiles)

---

## 📱 Mobile Support

VanUI automatically detects mobile devices and adjusts:

### Features
- **Touch Controls**: Full touch support for all interactions
- **Auto-scaling**: UI scales down to 70% on mobile devices
- **Drag Support**: Touch-drag for window movement
- **Optimized Layout**: Scrollable sections adapt to small screens

### Detection

```lua
if Window.IsMobile then
    print("Running on mobile device")
end
```

---

## 📚 Examples

### Complete Example

```lua
local Vanith = loadstring(game:HttpGet("https://raw.githubusercontent.com/Snipez-Dev/Rbx-Scripts/refs/heads/main/VanUI"))()

-- Create Window
local Window = Vanith:CreateWindow({
    Title = "ESP & Aimbot Script",
    ThemePreset = "Neon",
    AutoLoadConfig = true,
})

-- Create Tabs
local MainTab = Window:CreateTab("Main")
local SettingsTab = Window:CreateTab("Settings")

-- Create Top Tabs (Profiles)
local Profile1 = MainTab:CreateTopTab("Profile 1")
local Profile2 = MainTab:CreateTopTab("Profile 2")

-- Sections
local ESPSection = MainTab:CreateSection("ESP Features", "LeftTop", Profile1)
local AimbotSection = MainTab:CreateSection("Aimbot", "RightTop", Profile1)
local MiscSection = MainTab:CreateSection("Misc", "LeftBottom", Profile1)
local ConfigSection = SettingsTab:CreateSection("Config", "RightBottom")

-- ESP Controls
local espEnabled = ESPSection:CreateToggle({
    Name = "Enable ESP",
    Default = false,
    Callback = function(enabled)
        print("ESP:", enabled)
    end
})

ESPSection:CreateDropdown({
    Name = "ESP Mode",
    Options = {"Box", "Skeleton", "Both"},
    Default = "Box",
    Callback = function(mode)
        print("ESP Mode:", mode)
    end
})

ESPSection:CreateSlider({
    Name = "Max Distance",
    Min = 100,
    Max = 5000,
    Default = 1000,
    Step = 100,
    Format = function(v) return v .. "m" end,
    Callback = function(value)
        print("Max Distance:", value)
    end
})

-- Aimbot Controls
AimbotSection:CreateToggle({
    Name = "Enable Aimbot",
    Default = false,
    Callback = function(enabled)
        print("Aimbot:", enabled)
    end
})

AimbotSection:CreateSlider({
    Name = "FOV",
    Min = 50,
    Max = 500,
    Default = 150,
    Step = 10,
    Callback = function(value)
        print("FOV:", value)
    end
})

AimbotSection:CreateDropdown({
    Name = "Target Part",
    Options = {"Head", "Torso", "Nearest"},
    Default = "Head",
    Callback = function(part)
        print("Target:", part)
    end
})

-- Misc
MiscSection:CreateButton({
    Name = "Clear Terrain",
    Callback = function()
        workspace.Terrain:Clear()
        Window:Notify({
            Title = "Success",
            Text = "Terrain cleared!",
            Duration = 3
        })
    end
})

local speedValue = 16
MiscSection:CreateSlider({
    Name = "Walk Speed",
    Min = 16,
    Max = 100,
    Default = 16,
    Step = 1,
    Callback = function(value)
        speedValue = value
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.WalkSpeed = value
        end
    end
})

-- Config System
Vanith:Config({
    Window = Window,
    Section = ConfigSection
})

-- Notifications
Window:Notify({
    Title = "Script Loaded",
    Text = "VanUI Example loaded successfully!",
    Duration = 4
})
```

### TopTabs (Profiles) Example

```lua
local Tab = Window:CreateTab("Main")

-- Create multiple profiles
local Legit = Tab:CreateTopTab("Legit")
local Rage = Tab:CreateTopTab("Rage")

-- Section tied to "Legit" profile
local LegitSection = Tab:CreateSection("Settings", "LeftTop", Legit)

LegitSection:CreateSlider({
    Name = "Smoothness",
    Min = 0,
    Max = 10,
    Default = 5,
    -- This value will be different for "Legit" vs "Rage"
})

-- Section tied to "Rage" profile
local RageSection = Tab:CreateSection("Settings", "LeftTop", Rage)

RageSection:CreateSlider({
    Name = "Smoothness",
    Min = 0,
    Max = 10,
    Default = 1,  -- Different default for rage mode
})

-- Switch profiles programmatically
Tab:SetTopTabByName("Rage")
```

### Notification Examples

```lua
-- Basic notification
Window:Notify({
    Title = "Info",
    Text = "This is a notification",
    Duration = 3
})

-- Custom colors
Window:Notify({
    Title = "Warning",
    Text = "Something went wrong!",
    Duration = 5,
    AccentColor = Color3.fromRGB(255, 100, 100),
    TextColor = Color3.fromRGB(255, 255, 255)
})

-- Global notification (without window)
Vanith:Notify({
    Title = "System",
    Text = "Script loaded",
    Duration = 2
})
```

---

## 🔧 Advanced Features

### Dynamic Control Updates

```lua
-- Create controls
local speedSlider = Section:CreateSlider({...})
local espToggle = Section:CreateToggle({...})
local weaponDropdown = Section:CreateDropdown({...})

-- Update programmatically
speedSlider:Set(50)
espToggle:Set(true)
weaponDropdown:Set("Bow")

-- Get current values
local currentSpeed = speedSlider:Get()
local isEspEnabled = espToggle:Get()
local selectedWeapon = weaponDropdown:Get()
```

### Window Management

```lua
-- Minimize/Restore
Window:Minimize()  -- Hide window, show mini button
Window:Restore()   -- Show window again

-- Visibility
Window:SetVisible(false)  -- Hide everything
Window:SetVisible(true)   -- Show again

-- Destroy
Window:Destroy()  -- Clean up everything
```

### Theme Helpers

```lua
-- Get all registered themes
local themes = Vanith:GetThemes()
for name, def in pairs(themes) do
    print(name)
end

-- Get specific theme
local neonTheme = Vanith:GetTheme("Neon")

-- Quick accent change
Window:SetAccent(Color3.fromRGB(255, 0, 255))
```

---

## ⚙️ Configuration Options

### Window Config

```lua
{
    -- Basic
    Title = "My Script",
    Size = Vector2.new(649, 417),
    Position = UDim2.fromScale(0.5, 0.5),
    
    -- Theme
    ThemePreset = "Neon",
    Theme = { ... },
    
    -- Behavior
    Draggable = true,
    AutoLoadConfig = true,
    
    -- Config System
    ConfigFolder = "MyConfigs",
    ConfigExtension = "json",
    DefaultConfig = "default",
    
    -- UI Text
    MinimizeText = "Open MyScript",
}
```

### Section Slots

| Slot | Position | Best For |
|------|----------|----------|
| `LeftTop` | Top-left quadrant | Main features |
| `LeftBottom` | Bottom-left quadrant | Secondary features |
| `RightTop` | Top-right quadrant | Combat settings |
| `RightBottom` | Bottom-right quadrant | Config/Misc |
| `nil` | Auto-stack | Multiple small sections |

---

## 🐛 Troubleshooting

### Common Issues

**"Config not saving"**
- Ensure `writefile` is available in your executor
- Check that `ConfigFolder` doesn't contain invalid characters

**"UI not showing"**
- Check that your executor supports `gethui()` or `CoreGui`
- Verify the script loaded without errors

**"Theme not applying"**
- Ensure the theme name is spelled correctly
- Use `Window:SetThemePreset("ThemeName")` after window creation

**"Controls not working"**
- Make sure callbacks are defined as functions
- Check console for errors

### Debug Mode

```lua
-- Check if window exists
if Window then
    print("Window created successfully")
end

-- List all tabs
for i, tab in ipairs(Window.Tabs) do
    print("Tab", i, ":", tab.Name)
end

-- Check mobile detection
print("Mobile:", Window.IsMobile)
```

---

## 📄 License

This project is licensed under the MIT License.

---

## 🤝 Contributing

Contributions are welcome! Feel free to:
- Report bugs
- Suggest features
- Submit pull requests

---

## 💬 Support

For support and updates, join the community:
- **Discord**: [Your Discord Link]
- **GitHub Issues**: [Report Issues](https://github.com/Snipez-Dev/Rbx-Scripts/issues)

---

## 🙏 Credits

- **Author**: Snipez-Dev
- **Library Name**: VanUI / Vanith
- **Version**: 1.0

---