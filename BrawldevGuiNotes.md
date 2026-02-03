# GUI Master Notes - Organized by System

**Purpose**: Reference guide to match GUI components with game systems (Shop, Combat, Inventory, etc.)

---

## 📋 TABLE OF CONTENTS

1. **GUI Foundation** (Episodes 1-2)
2. **Text Components** (Episodes 2-3)
3. **Input Systems** (Episode 5)
4. **Visual Components** (Episode 6-7)
5. **Container Systems** (Episode 7-8)
6. **World UI** (Episodes 8-9)
7. **Interaction Systems** (Episode 12)
8. **Layout Systems** (Episode 13)
9. **Styling & Polish** (Episode 14)
10. **Responsive Design** (Episode 15)
11. **3D/2D Integration** (Episode 16)

---

## 🎯 FOUNDATION (Episode 1-2)

### The Three GUI Types

| Type | Where? | Use Case | Visibility |
|------|--------|----------|------------|
| **ScreenGui** | Player's screen | HUD, menus, shops, inventory | Fixed to screen |
| **SurfaceGui** | Part surface | Name tags, health bars, doors | Front-facing only |
| **BillboardGui** | 3D space above part | Floating labels, damage numbers | All camera angles |

### The Critical Golden Rule

```lua
-- ❌ WRONG - Modifies template (no effect)
game.StarterGui:WaitForChild("MainGui"):Destroy()

-- ✅ CORRECT - Modifies player's copy
game.Players:WaitForChild("Player").PlayerGui:WaitForChild("MainGui"):Destroy()
```

**Why**: StarterGui is the template. PlayerGui is what each player sees.

### Structure Overview

```
StarterGui (Template)
  ↓
  When player joins
  ↓
PlayerGui (Player's copy - MODIFY THIS)
```

### Key ScreenGui Properties

| Property | Purpose | Values |
|----------|---------|--------|
| `Enabled` | Toggle on/off | true/false |
| `IgnoreGuiInset` | Remove top bar gap | true/false |
| `ResetOnSpawn` | Reset to template on respawn | true/false |
| `DisplayOrder` | Priority when overlapping | 1, 2, 3... |

---

## 🏷️ TEXT COMPONENTS (Episode 2-3)

### TextLabel (Display Text)

**Use when**: You need to show text (HP, coins, titles, messages)

**Key Properties**:
```lua
TextLabel.Text = "Hello World"
TextLabel.TextColor3 = Color3.fromRGB(255, 255, 255)  -- White text
TextLabel.TextSize = 24
TextLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  -- Black background
TextLabel.BackgroundTransparency = 0.5  -- Semi-transparent
TextLabel.BorderSizePixel = 2
TextLabel.BorderColor3 = Color3.fromRGB(100, 100, 100)
TextLabel.TextScaled = true  -- RECOMMENDED: auto-scale text
TextLabel.ZIndex = 1  -- Priority vs other labels
TextLabel.Visible = true  -- Toggle on/off
```

**Example Usage**:
```lua
-- Coins display
coinsLabel.Text = "Coins: " .. player.leaderstats.Coins.Value
coinsLabel.TextColor3 = Color3.fromRGB(255, 215, 0)  -- Gold

-- HP display
hpLabel.Text = player.Character.Humanoid.Health .. " / 100"
hpLabel.TextColor3 = Color3.fromRGB(0, 255, 0)  -- Green
```

### TextButton (Clickable Button)

**Use when**: Player needs to click something (Buy, Equip, Accept)

**Key Scripting Properties**:
```lua
TextButton.MouseButton1Click:Connect(function()
    print("Button clicked!")
end)

TextButton.MouseEnter:Connect(function()
    print("Mouse entered button")
    TextButton.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
end)

TextButton.MouseLeave:Connect(function()
    print("Mouse left button")
    TextButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
end)
```

**Key Display Properties**:
```lua
TextButton.Text = "Buy Sword"
TextButton.TextScaled = true  -- Auto-scale text
TextButton.TextSize = 18  -- For precise control
TextButton.AutoButtonColor = true  -- Darkens on hover
TextButton.TextWrapped = true  -- Text to next line
TextButton.TextTruncate = Enum.TextTruncate.AtEnd  -- "Butt..." if too long

-- Alignment
TextButton.TextXAlignment = Enum.TextXAlignment.Center
TextButton.TextYAlignment = Enum.TextYAlignment.Center
```

**Example Usage - Buy Button**:
```lua
buyButton.MouseButton1Click:Connect(function()
    local success = RemoteFunction:InvokeServer("Sword")
    if success then
        statusLabel.Text = "✓ Purchased!"
        statusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    else
        statusLabel.Text = "✗ Not enough coins"
        statusLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
    end
end)
```

---

## 📝 INPUT SYSTEMS (Episode 5)

### TextBox (User Input)

**Use when**: Player types something (chat, quiz, username)

**Key Properties**:
```lua
TextBox.PlaceholderText = "Enter your name..."
TextBox.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
TextBox.TextEditable = true  -- Player can type
TextBox.ClearTextOnFocus = true  -- Clear when clicked
TextBox.MultiLine = true  -- Allow multiple lines
TextBox.TextSize = 16
```

**Key Events**:
```lua
-- When player clicks in box
TextBox.Focused:Connect(function()
    print("TextBox is now focused")
end)

-- When player clicks away or presses Enter
TextBox.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        print("Player pressed Enter: " .. TextBox.Text)
    else
        print("Player clicked away")
    end
end)
```

**Key Functions**:
```lua
TextBox:CaptureFocus()  -- Force focus on textbox
TextBox:ReleaseFocus(true)  -- Force unfocus, treat as Enter pressed
TextBox:ReleaseFocus(false)  -- Force unfocus, treat as clicked away
local isFocused = TextBox:IsFocused()  -- Check current focus state
```

**Example - Quiz System**:
```lua
quizBox.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        local answer = quizBox.Text
        RemoteFunction:InvokeServer("SubmitAnswer", answer)
        quizBox.Text = ""  -- Clear for next question
    end
end)
```

**Example - Chat System**:
```lua
chatBox.FocusLost:Connect(function(enterPressed)
    if enterPressed and chatBox.Text ~= "" then
        RemoteEvent:FireServer("SendMessage", chatBox.Text)
        chatBox.Text = ""
    end
end)
```

---

## 🖼️ VISUAL COMPONENTS (Episode 6-7)

### ImageLabel (Display Image/Logo)

**Use when**: You need logos, icons, decorative elements

**Key Properties**:
```lua
ImageLabel.Image = "rbxassetid://6808677329"  -- Set image
ImageLabel.ImageTransparency = 0  -- 0=opaque, 1=invisible
ImageLabel.BackgroundTransparency = 0  -- Background transparency
ImageLabel.ImageColor3 = Color3.fromRGB(255, 255, 255)  -- Default white (no tint)

-- Apply tint/color effect
ImageLabel.ImageColor3 = Color3.fromRGB(255, 0, 0)  -- Red tint
ImageLabel.ImageColor3 = Color3.fromRGB(200, 200, 200)  -- Dim effect
```

**Practical Effects**:
```lua
-- Fade in effect
for i = 0, 1, 0.05 do
    ImageLabel.ImageTransparency = 1 - i
    task.wait(0.05)
end

-- Status indicator (green = healthy, red = low HP)
if health > 75 then
    statusIcon.ImageColor3 = Color3.fromRGB(0, 255, 0)  -- Green
elseif health > 25 then
    statusIcon.ImageColor3 = Color3.fromRGB(255, 255, 0)  -- Yellow
else
    statusIcon.ImageColor3 = Color3.fromRGB(255, 0, 0)  -- Red
end

-- Damage effect (dim + red)
damageIcon.ImageColor3 = Color3.fromRGB(255, 0, 0)
damageIcon.ImageTransparency = 0.5
task.wait(0.3)
damageIcon.ImageColor3 = Color3.fromRGB(255, 255, 255)
damageIcon.ImageTransparency = 0
```

### ImageButton (Clickable Image/Icon)

**Use when**: Skill buttons, menu icons, interactive buttons

**Key Properties**:
```lua
ImageButton.Image = "rbxassetid://13656985949"  -- Default image
ImageButton.HoverImage = "rbxassetid://13656985950"  -- On hover
ImageButton.PressedImage = "rbxassetid://13656985951"  -- On click
ImageButton.ImageTransparency = 0
```

**Example - Ability Button**:
```lua
skillButton.MouseButton1Click:Connect(function()
    RemoteFunction:InvokeServer("CastAbility", "Fireball")
end)

skillButton.MouseEnter:Connect(function()
    skillButton.Image = "rbxassetid://13656985950"  -- Hover image
end)

skillButton.MouseLeave:Connect(function()
    skillButton.Image = "rbxassetid://13656985949"  -- Normal image
end)
```

---

## 📦 CONTAINER SYSTEMS (Episode 7-8)

### Frame (Organize UI Elements)

**Use when**: Grouping related UI elements (shop panel, inventory section)

**Key Concept**: Children positions are relative to Frame origin, not screen origin

**Key Properties**:
```lua
Frame.ClipDescendants = true  -- Hide anything outside frame bounds
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.BackgroundTransparency = 0.3
Frame.BorderSizePixel = 0
```

**Structure Example**:
```
ScreenGui
└─ ShopFrame
   ├─ SwordButton
   ├─ AxeButton
   └─ ShieldButton

-- When you move ShopFrame, all buttons move with it
```

### ScrollingFrame (Scrollable List)

**Use when**: Long lists (inventory, leaderboard, chat)

**Key Properties**:
```lua
ScrollingFrame.CanvasSize = UDim2.new(0, 0, 5, 0)  -- 5x height for scrolling
ScrollingFrame.ScrollBarThickness = 8  -- Scrollbar width
ScrollingFrame.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
ScrollingFrame.ScrollBarImageTransparency = 0.3
```

**With UIListLayout** (Auto-arrange items):
```lua
-- Structure: ScrollingFrame → UIListLayout → TextLabel items
local listLayout = Instance.new("UIListLayout")
listLayout.Parent = ScrollingFrame
listLayout.Padding = UDim.new(0, 5)  -- Space between items
listLayout.FillDirection = Enum.FillDirection.Vertical
listLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
listLayout.VerticalAlignment = Enum.VerticalAlignment.Top

-- Auto-expand canvas as items are added
listLayout.Padding = UDim.new(0, 10)
ScrollingFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y
```

**Example - Inventory List**:
```lua
for _, item in pairs(player.Backpack:GetChildren()) do
    local itemLabel = Instance.new("TextLabel")
    itemLabel.Text = item.Name
    itemLabel.Parent = ScrollingFrame
    -- UIListLayout auto-positions it
end
```

---

## 🌍 WORLD UI (Episode 8-9)

### SurfaceGui (GUI on Part Surface)

**Use when**: Doors, signs, wall displays, NPC dialogue boxes

**Key Concept**: GUI appears on a specific face of a part, visible only from front

**Structure**:
```
Workspace
└─ DoorPart
   └─ SurfaceGui
      └─ TextLabel (displays on door)
```

**Key Properties**:
```lua
SurfaceGui.Adornee = DoorPart  -- Which part to attach to
SurfaceGui.Face = Enum.NormalId.Front  -- Which face (Front, Back, Top, etc.)
SurfaceGui.AlwaysOnTop = false  -- Can be hidden behind objects
SurfaceGui.MaxDistance = 50  -- How far away players can see it
SurfaceGui.ToolPunchThroughDistance = 5  -- Interaction distance with tool
SurfaceGui.LightInfluence = 0  -- Not affected by sun (always visible)
```

**Example - Door Label**:
```lua
doorGui.Adornee = doorPart
doorGui.Face = Enum.NormalId.Front
doorGui.MaxDistance = 20  -- Only see from 20 studs away

local textLabel = doorGui:FindFirstChildOfClass("TextLabel")
textLabel.Text = "Door - Press E to Open"
```

**Example - NPC Shop Sign**:
```lua
shopGui.Adornee = npcHead  -- Attach to NPC head
shopGui.Face = Enum.NormalId.Front
shopGui.MaxDistance = 15
shopGui.ZOffset = 2  -- Offset from surface
```

### BillboardGui (Floating 3D Label)

**Use when**: Damage numbers, player names, floating labels, quest markers

**Key Concept**: Visible from ALL camera angles, floats in 3D space

**Structure**:
```
Workspace
└─ Enemy
   └─ Head
      └─ HealthBar (BillboardGui)
         └─ Frame
            ├─ Background
            └─ HealthMeter
```

**Key Properties**:
```lua
BillboardGui.Adornee = enemyHead  -- Attach to part
BillboardGui.MaxDistance = 100  -- Visible from 100 studs
BillboardGui.Size = UDim2.new(4, 0, 2, 0)  -- Width=4 studs, Height=2 studs
BillboardGui.StudOffset = Vector3.new(0, 3, 0)  -- 3 studs above head
BillboardGui.SizeOffset = Vector2.new(0, 0.5)  -- Extra vertical offset
BillboardGui.PlayerToHideFrom = player  -- Hide from specific player
```

**Example - Enemy Health Bar**:
```lua
local healthBar = Instance.new("BillboardGui")
healthBar.Adornee = enemy.Head
healthBar.MaxDistance = 100
healthBar.Size = UDim2.new(4, 0, 0.5, 0)
healthBar.StudOffset = Vector3.new(0, 3, 0)  -- 3 studs above head
healthBar.Parent = enemy.Head

local frame = Instance.new("Frame")
frame.Size = UDim2.new(1, 0, 1, 0)
frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
frame.Parent = healthBar

local meter = Instance.new("Frame")
meter.Size = UDim2.new(1, 0, 1, 0)
meter.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
meter.Parent = frame

-- Update health
meter.Size = UDim2.new(enemy.Humanoid.Health / enemy.Humanoid.MaxHealth, 0, 1, 0)
```

**Example - Damage Number**:
```lua
local damageLabel = Instance.new("BillboardGui")
damageLabel.Adornee = hit.Parent.Head
damageLabel.MaxDistance = 100
damageLabel.Size = UDim2.new(3, 0, 2, 0)
damageLabel.Parent = hit.Parent.Head

local textLabel = Instance.new("TextLabel")
textLabel.Text = "-" .. damage
textLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
textLabel.TextSize = 24
textLabel.BackgroundTransparency = 1
textLabel.Parent = damageLabel

-- Fade out animation
for i = 1, 0, -0.05 do
    textLabel.TextTransparency = 1 - i
    damageLabel.StudOffset = damageLabel.StudOffset + Vector3.new(0, 0.1, 0)
    task.wait(0.05)
end
damageLabel:Destroy()
```

---

## 🎮 INTERACTION SYSTEMS (Episode 12)

### ProximityPrompt (Press E to Interact)

**Use when**: Doors, NPCs, pickups, anything requiring player input at close range

**Key Concept**: Shows prompt when player is within range

**Key Properties**:
```lua
ProximityPrompt.KeyboardKeyCode = Enum.KeyCode.E  -- Which key
ProximityPrompt.ActionText = "Open"  -- What action
ProximityPrompt.ObjectText = "Door"  -- What you're interacting with
ProximityPrompt.MaxActivationDistance = 20  -- How close
ProximityPrompt.HoldDuration = 0  -- 0=instant, >0=hold to trigger
ProximityPrompt.Exclusivity = Enum.ProximityPromptExclusivity.OneGlobally  -- Show one at a time
```

**Key Events**:
```lua
ProximityPrompt.Triggered:Connect(function(player)
    print(player.Name .. " triggered the prompt")
end)

ProximityPrompt.TriggerEnded:Connect(function(player)
    print(player.Name .. " stopped holding")
end)
```

**Structure** (Professional):
```
Workspace
└─ DoorPart
   └─ Attachment (positioned at interaction point)
      └─ ProximityPrompt
```

**Example - Door Opener**:
```lua
local prompt = door:WaitForChild("Attachment"):WaitForChild("ProximityPrompt")
prompt.ActionText = "Open"
prompt.ObjectText = "Door"
prompt.MaxActivationDistance = 20

prompt.Triggered:Connect(function(player)
    RemoteEvent:FireServer("OpenDoor", door)
end)
```

**Example - Item Pickup**:
```lua
pickupPrompt.ActionText = "Take"
pickupPrompt.ObjectText = "Sword"
pickupPrompt.MaxActivationDistance = 10

pickupPrompt.Triggered:Connect(function(player)
    RemoteEvent:FireServer("PickupItem", itemName)
end)
```

---

## 🎨 LAYOUT SYSTEMS (Episode 13)

### UIGridLayout (Grid Arrangement)

**Use when**: Inventory, shop display, gallery

```lua
local grid = Instance.new("UIGridLayout")
grid.Parent = containerFrame
grid.CellPadding = UDim2.new(0, 5, 0, 5)  -- Space between items
grid.CellSize = UDim2.new(0, 100, 0, 100)  -- Item size
grid.FillDirection = Enum.FillDirection.Horizontal
grid.StartCorner = Enum.StartCorner.TopLeft
grid.HorizontalAlignment = Enum.HorizontalAlignment.Center
grid.VerticalAlignment = Enum.VerticalAlignment.Top
```

**Example - Shop Grid**:
```lua
local shopFrame = Instance.new("Frame")
local gridLayout = Instance.new("UIGridLayout")
gridLayout.Parent = shopFrame
gridLayout.CellSize = UDim2.new(0, 80, 0, 80)
gridLayout.CellPadding = UDim2.new(0, 10, 0, 10)

for _, item in pairs(SHOP_ITEMS) do
    local button = Instance.new("TextButton")
    button.Text = item.name
    button.Parent = shopFrame
    -- Grid automatically positions it
end
```

### UIListLayout (Vertical/Horizontal List)

**Use when**: Menus, chat, leaderboards

```lua
local list = Instance.new("UIListLayout")
list.Parent = containerFrame
list.Padding = UDim.new(0, 10)  -- Space between items
list.FillDirection = Enum.FillDirection.Vertical
list.HorizontalAlignment = Enum.HorizontalAlignment.Center
list.SortOrder = Enum.SortOrder.LayoutOrder  -- Child order matters

-- Auto-expand container
containerFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y
```

**Example - Leaderboard**:
```lua
local leaderboardFrame = Instance.new("ScrollingFrame")
local listLayout = Instance.new("UIListLayout")
listLayout.Parent = leaderboardFrame
listLayout.Padding = UDim.new(0, 5)
listLayout.FillDirection = Enum.FillDirection.Vertical
leaderboardFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y

for i, player in pairs(topPlayers) do
    local row = Instance.new("TextLabel")
    row.Text = i .. ". " .. player.Name .. " - " .. player.Coins
    row.LayoutOrder = i
    row.Parent = leaderboardFrame
end
```

### UIPageLayout (Tabs/Pages)

**Use when**: Shop categories, character customization, tutorials

```lua
local pageLayout = Instance.new("UIPageLayout")
pageLayout.Parent = containerFrame
pageLayout.Padding = UDim.new(0, 10)
pageLayout.TweenTime = 0.5
pageLayout.EasingStyle = Enum.EasingStyle.Quint
pageLayout.FillDirection = Enum.FillDirection.Horizontal
```

**Example - Shop Categories**:
```lua
local shopFrame = Instance.new("Frame")
local pageLayout = Instance.new("UIPageLayout")
pageLayout.Parent = shopFrame
pageLayout.TweenTime = 0.5

-- Weapons page
local weaponsPage = Instance.new("Frame")
weaponsPage.Parent = shopFrame

-- Armor page
local armorPage = Instance.new("Frame")
armorPage.Parent = shopFrame

-- Navigation
weaponsButton.MouseButton1Click:Connect(function()
    pageLayout:JumpToPage(1)  -- Go to weapons page
end)

armorButton.MouseButton1Click:Connect(function()
    pageLayout:JumpToPage(2)  -- Go to armor page
end)
```

---

## ✨ STYLING & POLISH (Episode 14)

### UICorner (Rounded Corners)

```lua
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)  -- 8 pixels
corner.Parent = button
```

### UIStroke (Border/Outline)

```lua
local stroke = Instance.new("UIStroke")
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(100, 100, 100)
stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
stroke.Parent = button
```

### UIPadding (Internal Spacing)

```lua
local padding = Instance.new("UIPadding")
padding.PaddingTop = UDim.new(0, 10)
padding.PaddingBottom = UDim.new(0, 10)
padding.PaddingLeft = UDim.new(0, 10)
padding.PaddingRight = UDim.new(0, 10)
padding.Parent = button
```

### UIGradient (Color Gradient)

```lua
local gradient = Instance.new("UIGradient")
gradient.Color = ColorSequence.new(
    Color3.fromRGB(255, 0, 0),      -- Red
    Color3.fromRGB(0, 0, 255)       -- Blue
)
gradient.Rotation = 45
gradient.Parent = button
```

**Complete Professional Button Example**:
```lua
local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 200, 0, 50)
button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
button.TextColor3 = Color3.fromRGB(255, 255, 255)
button.Text = "Buy Sword"
button.Parent = parent

-- Add corner
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = button

-- Add stroke
local stroke = Instance.new("UIStroke")
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(100, 200, 100)
stroke.Parent = button

-- Add padding
local padding = Instance.new("UIPadding")
padding.PaddingTop = UDim.new(0, 10)
padding.PaddingBottom = UDim.new(0, 10)
padding.Parent = button

-- Result: Professional button ✨
```

---

## 📐 RESPONSIVE DESIGN (Episode 15)

### Positioning & Sizing (Udim2)

**The Core Concept**:
```lua
Element.Position = UDim2.new(scaleX, offsetX, scaleY, offsetY)
Element.Size = UDim2.new(scaleW, offsetW, scaleH, offsetH)

-- Scale = 0 to 1 (relative to parent)
-- Offset = pixels (fixed)

-- Example: Center on screen
element.Position = UDim2.new(0.5, -100, 0.5, -50)  -- Half screen - half element size
element.Size = UDim2.new(0, 200, 0, 100)  -- 200x100 pixels
```

**Key Principle**: Use scale for responsiveness, offset for fine-tuning

### UIAspectRatioConstraint (Lock Proportions)

```lua
local aspect = Instance.new("UIAspectRatioConstraint")
aspect.AspectRatio = 1  -- 1:1 square
aspect.Parent = imageButton
```

### UISizeConstraint (Min/Max Size)

```lua
local constraint = Instance.new("UISizeConstraint")
constraint.MinSize = Vector2.new(100, 100)  -- Minimum size
constraint.MaxSize = Vector2.new(400, 400)  -- Maximum size
constraint.Parent = button
```

### AutomaticSize (Dynamic Sizing)

```lua
frame.AutomaticSize = Enum.AutomaticSize.Y  -- Grow based on children height
frame.AutomaticSize = Enum.AutomaticSize.XY  -- Grow in both directions
```

---

## 🎬 3D/2D INTEGRATION (Episode 16)

### ViewportFrame (Display 3D Model in 2D)

**Use when**: Character preview, item showcase, spinning model display

**Structure**:
```
ScreenGui
└─ ViewportFrame
   └─ Camera
   └─ Model/Part
```

**Key Code**:
```lua
-- Create viewport
local viewport = Instance.new("ViewportFrame")
viewport.Size = UDim2.new(0, 300, 0, 400)
viewport.Parent = parent

-- Create custom camera
local camera = Instance.new("Camera")
camera.Parent = viewport

-- Clone model into viewport
local model = game.ServerStorage:FindFirstChild("SwordModel"):Clone()
model.Parent = viewport

-- Set camera
viewport.CurrentCamera = camera
camera.CFrame = CFrame.new(0, 2, 5)  -- Position camera
```

**Example - Rotating Item Display**:
```lua
local viewport = Instance.new("ViewportFrame")
viewport.Size = UDim2.new(0, 300, 0, 300)
viewport.Parent = screenGui

local camera = Instance.new("Camera")
camera.Parent = viewport
viewport.CurrentCamera = camera

local model = game.ServerStorage.Sword:Clone()
model.Parent = viewport

camera.CFrame = CFrame.new(0, 0, 10)

-- Continuous rotation
game:GetService("RunService").RenderStepped:Connect(function()
    model:SetPrimaryPartCFrame(model.PrimaryPart.CFrame * CFrame.Angles(0, math.rad(1), 0))
end)
```

**Example - Hover to Zoom**:
```lua
viewport.MouseEnter:Connect(function()
    -- Move camera closer
    local tween = game:GetService("TweenService"):Create(
        camera,
        TweenInfo.new(0.5),
        {CFrame = CFrame.new(0, 0, 5)}
    )
    tween:Play()
end)

viewport.MouseLeave:Connect(function()
    -- Move camera back
    local tween = game:GetService("TweenService"):Create(
        camera,
        TweenInfo.new(0.5),
        {CFrame = CFrame.new(0, 0, 10)}
    )
    tween:Play()
end)
```

---

## 🎮 MATCHING GUI TO YOUR SYSTEMS

### SYSTEM 1: Charging Combat Tool

**GUI Components You Need**:
- BillboardGui (damage numbers floating above enemy)
- BillboardGui (health bar above enemy)
- TextLabel (current charge % on screen)
- ImageLabel (charge indicator visual)

**Example Setup**:
```lua
-- HUD charge indicator
local chargeBar = Instance.new("Frame")
chargeBar.Size = UDim2.new(0, 200, 0, 20)
chargeBar.Position = UDim2.new(0.5, -100, 0.9, -30)
chargeBar.Parent = screenGui

local chargeFill = Instance.new("Frame")
chargeFill.Size = UDim2.new(0, 0, 1, 0)
chargeFill.BackgroundColor3 = Color3.fromRGB(255, 215, 0)
chargeFill.Parent = chargeBar

-- Update charge
while charging do
    chargeAmount = chargeAmount + 1
    chargeFill.Size = UDim2.new(chargeAmount / 100, 0, 1, 0)
    task.wait(0.1)
end
```

### SYSTEM 2: Advanced Shop

**GUI Components You Need**:
- TextLabel (welcome text)
- UIGridLayout (arrange items)
- ImageButton (item buttons)
- TextLabel (price display)
- TextButton (buy button)
- TextLabel (status message)

**Example Setup**:
```lua
-- Shop frame
local shopFrame = Instance.new("Frame")
shopFrame.Size = UDim2.new(0, 400, 0, 500)
shopFrame.Position = UDim2.new(0.5, -200, 0.5, -250)
shopFrame.Parent = screenGui

-- Grid layout
local grid = Instance.new("UIGridLayout")
grid.CellSize = UDim2.new(0, 100, 0, 100)
grid.CellPadding = UDim2.new(0, 10, 0, 10)
grid.Parent = shopFrame

-- Items
for _, item in pairs(SHOP_ITEMS) do
    local button = Instance.new("TextButton")
    button.Text = item.name .. "\n$" .. item.price
    button.Parent = shopFrame
end
```

### SYSTEM 3: Inventory & Equipment

**GUI Components You Need**:
- ScrollingFrame (inventory list)
- UIListLayout (arrange items)
- TextButton (equip/unequip)
- TextLabel (stat display)
- ImageLabel (item icon)

### SYSTEM 4: Quest System

**GUI Components You Need**:
- TextLabel (quest title)
- TextLabel (progress text)
- UIListLayout (arrange objectives)
- TextButton (accept/turn in)
- BillboardGui (quest marker on NPC)

### SYSTEM 5: PvP Arena

**GUI Components You Need**:
- BillboardGui (enemy health bar)
- TextLabel (arena timer)
- TextLabel (score/kills)
- TextButton (challenge button)

---

## 📝 QUICK REFERENCE CHECKLIST

### When building Shop GUI:
- [ ] ScreenGui in StarterGui
- [ ] Frame for shop container
- [ ] TextLabel for welcome message
- [ ] UIGridLayout for item arrangement
- [ ] TextButton/ImageButton for each item
- [ ] TextLabel for price display
- [ ] TextLabel for status message
- [ ] UICorner + UIStroke for polish

### When building Combat HUD:
- [ ] TextLabel for damage display
- [ ] TextLabel/Frame for charge bar
- [ ] BillboardGui for damage numbers (WorldModel)
- [ ] BillboardGui for enemy health bars (WorldModel)
- [ ] ImageLabel for visual effects

### When building Inventory:
- [ ] ScreenGui for inventory panel
- [ ] ScrollingFrame for item list
- [ ] UIListLayout for automatic arrangement
- [ ] TextButton for each item
- [ ] TextLabel for stats display

---

**Master These Pattern and You Can Build ANY UI System** 🎮
