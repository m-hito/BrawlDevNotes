# 🚀 BrawlDev Notes (Roblox advanced scripting 1 - 36) - Core Roblox Systems (playlist) https://www.youtube.com/playlist?list=PLQ1Qd31Hmi3WKkVHnadvhOOjz04AuMYAf

> **"Server validates. Client suggests. Systems scale."**
> Raw dev notes → Production knowledge base

## 🎯 **01: Client vs Server Fundamentals** https://youtu.be/dRgvhjr2Dx0?si=kJFJALltUqxEomwY

| **Aspect** | **LocalScript (Client)** | **ServerScript (Server)** |
|------------|--------------------------|---------------------------|
| **Runs On** | Player's PC | Roblox Servers |
| **Affects** | Only that player | **All players** |
| **Security** | ❌ **Hackable** | ✅ **Trusted** |
| **Use Case** | UI, input, effects | **Coins, damage, data** |
| **Output** | Blue | **Green** |

**Core Rule:** Client fires → Server **validates** → Client reacts
Client: "Give me 999 coins!"
Server: "Nah, only +10 earned" ✅

## 🗑️ **02: Instance Cleanup (Memory Mastery)**

| **Method** | **What it does** | **Use When** |
|------------|------------------|--------------|
| `❌ :Remove()` | Parent = nil (leak) | **Never** |
| `✅ :Destroy()` | Full cleanup | **Immediate delete** |
| `✅ Debris:AddItem(obj, 5)` | Delete after 5s | **Spawned objects** |

**System Built:** `CoinZone` → Collect → `Debris:AddItem(coin, 5)` → No leaks

## 🛠️ **03: Tools System (Production Ready)**

StarterPack → Player.Backpack → Character (equipped)
↑ ↓
Tool dropped → Workspace ← Tool picked up

**Tool Flow:** `Equipped` → `Activated` → `Unequipped`

### **Key Properties:**
```lua
tool.ToolTip = "Picaxe"              -- Hover text
tool.TextureId = "rbxassetid://ID"   -- Icon
tool.RequiresHandle = true           -- Needs part
tool.CanBeDropped = false            -- Locked
tool.ManualActivationOnly = true     -- No auto-fire
Critical Events:
lua
tool.Equipped:Connect()    -- 1️⃣ Equip
tool.Activated:Connect()   -- 2️⃣ Click  
tool.Unequipped:Connect()  -- 3️⃣ Unequip
System Built: Charging tool → Scale + clicks + sound + cooldown
```
## ⚡ **04: Task Library (Time Control)**

| **Function** | **Blocks?** | **Use Case** |
|--------------|-------------|--------------|
| `task.wait(1)` | ✅ **Yes** | Cooldowns |
| `task.delay(1, fn)` | ❌ **No** | **DoT, timers** |
| `task.spawn(fn)` | ❌ **No** | **Parallel loops** |
| `task.cancel()` | ❌ **No** | **Interrupt spells** |

**Poison Pattern:**
```lua
for i = 1, 5 do
    task.delay(i*0.2, function()
        target:TakeDamage(damage/5)
    end)
end
```

System Built: Poison system in charging tool

## 📡** 05: RemoteEvents (One-Way Communication) **
text
ReplicatedStorage.Remotes.DropExp
    ↓ Client fires
Server: "Player earned XP" → All clients see
Direction	Syntax
C→S	remote:FireServer(data)
S→C	remote:FireClient(player, data)
S→All	remote:FireAllClients(data)
Use Cases:

C→S: Buy item, deal damage, use ability

S→C: Personal notifications

S→All: Global announcements

## 🔄 **06: RemoteFunctions (Two-Way Communication)** 
Aspect	RemoteEvent	RemoteFunction
Client→Server	:FireServer(args)	:InvokeServer(args)
Server Receive	:OnServerEvent	:OnServerInvoke
Response	Separate event	Return value
Client Waits	No	YES
Shop Example:

lua
-- Client
local success = shopFunc:InvokeServer("Sword", 50)
if success then print("Bought sword!") end

-- Server  
shopFunc.OnServerInvoke = function(player, item, cost)
    if player.leaderstats.Coins.Value >= cost then
        return true -- Client continues
    end
    return false
end
Status: Tools + async + networking = Combat-ready foundation
Next: Vector3 → Positioning → Knockback systems

"Ship systems, not perfection."

## 📐 **07: Vector3 (3D Positioning)**

**Definition:** Stores **X, Y, Z** coordinates for position/size in 3D space
X = Left/Right Y = Up/Down Z = Forward/Backward

| **Property** | **Purpose** | **Example** |
|--------------|-------------|-------------|
| `Part.Position` | World location | `Vector3.new(10, 5, 20)` |
| `Part.Size` | Object dimensions | `Vector3.new(4, 4, 4)` |
| `Part.Orientation` | Rotation | `Vector3.new(45, 90, 0)` |

**Vector3 vs CFrame:**
Vector3 = Position only (WHERE?)
CFrame = Position + Rotation (WHERE? + FACING?)

**Use Cases:**
- ✅ **Movement:** Projectiles, enemies, player walking
- ✅ **Positioning:** Spawning, teleporting  
- ✅ **Physics:** Velocity, forces, raycasting
- ✅ **Distance:** `(target - npc).Magnitude`
- ✅ **Direction:** `(mousePos - gunPos).Unit`

## 🧵 **08: Coroutines (Advanced Threading)**

**Concept:** Multiple execution paths in **one script**
Normal: while true do end → Blocks everything below
Coroutines: Multiple while loops → Run simultaneously

**Analogy:**
Script = Reading 1 book page-by-page

Coroutines = Reading 2+ books (page 1 → book 1, page 1 → book 2)

### **Core Functions:**
```lua
1. local co = coroutine.create(fn)     -- Create thread
2. coroutine.resume(co, args)          -- Start thread  
3. coroutine.yield()                   -- Pause thread
4. coroutine.wrap(fn)()                -- Create + run
5. coroutine.close(co)                 -- Kill thread
Wrap Patterns:
```

```lua
coroutine.wrap(fn)()                    -- Direct call
local task = coroutine.wrap(fn); task() -- Variable
Status Check:
```
```lua
print(coroutine.status(co)) -- suspended → running → dead
```
System Potential:
Use Case	Coroutine Pattern
Wave Spawning	Each wave = 1 coroutine
Turn-Based Combat	Each player turn = 1 thread
Ability Cooldowns	1 thread per ability
Self-Discovery Patterns:

State Machine: Idle → Attack → Resume

Producer-Consumer: Frames + input sync

Debounce: Natural input limiting

Status: Positioning + threading = Combat physics ready
Next: CollectionService → Dynamic object management

"Coroutines = while loops that don't block."

## 🏷️ **10: CollectionService (Dynamic Object Management)**

**Concept:** One script controls **100+ objects** by **tags** (not folders)

OOP = "How to build 1 sword" 🧠
CollectionService = "Find/manage 100 swords" 🔍

### **Core Components:**
| **Type** | **Purpose** | **Example** |
|----------|-------------|-------------|
| **🏷️ Tags** | Group membership | `fist → "weapon", "melee"` |
| **🧾 Attributes** | Object properties | `weaponType = "Fist"` |
| **🧠 OOP** | Individual behavior | Sword attack logic |
| **📋 Table** | Temp memory | `{swords = {}}` |

### **Key Syntax:**
```lua
local CS = game:GetService("CollectionService")

1. CS:GetTagged("KillBricks")              -- Get all tagged
2. CS:GetTags(part)                        -- Part's tags
3. CS:GetAllTags()                         -- All game tags
4. CS:AddTag(part, "Enemy")                -- Add tag
5. CS:HasTag(part, "Weapon")               -- Boolean check
6. CS:GetInstanceAddedSignal("Enemy")      -- Dynamic spawn
7. CS:GetInstanceRemovedSignal("Enemy")    -- Dynamic cleanup
```

✅ Understood Patterns:
Added Signal: Auto-setup on spawn

Static Collection: for _, brick in CS:GetTagged("KillBricks")

Dynamic Collection: GetInstanceAddedSignal()

💡 Advanced Patterns (To Master):
1. Archetype System
text
❌ Folders: Weapons/Fist, Weapons/Sword
✅ Tags: "Weapon+Melee", "Weapon+Ranged"

Fist = Weapon + Melee
Gun = Weapon + Ranged  
Boss = Enemy + Weapon
```lua
CS:GetInstanceAddedSignal("Enemy"):Connect(HandleEnemy)
CS:GetInstanceAddedSignal("Weapon"):Connect(HandleWeapon)
```
## 2. Registry Pattern (LIVE List)

❌ Manual: table.insert(weapons, sword) -- Breaks on destroy
✅ Auto: CS:GetInstanceAddedSignal("Weapons") -- Always current
Spawn weapon → Auto-registered → Auto-handled → Auto-cleaned

## 3. Observer Pattern

CS:GetInstanceAddedSignal("Boss")     -- Subject notifies
Your handler                    -- Observer reacts
Mental Model:

OOP builds sword → Tags identify role → CollectionService routes logic
Status: Tags + signals = Scalable object systems
Next: OOP ModuleScripts → Production architecture

"OOP organizes 1 object. Tags manage 1000."

## 🧠 **11: OOP Mastery (ModuleScripts) - CODE REUSE**

**Definition:** **Related data + behavior** inside **one object**  
**Most important topic** for **time-saving + scalability**

### **Core Concepts:**
| **Concept** | **Meaning** | **Example** |
|-------------|-------------|-------------|
| **Class** | **Blueprint** | House blueprint |
| **Properties** | **Data/State** | Color, size, rooms |
| **Methods** | **Behaviors** | Open door, paint |
| **Instance** | **Real object** | Built house |
| **Instantiation** | **Build process** | Blueprint → house |

**House Analogy:**
Class = Blueprint (properties + methods)
Object = Built house (unique instance)
Instantiation = Construction process

text

**Luau Reality:** **No classes** → Use **tables + metatables**

### **Colon Syntax (Syntactic Sugar):**
```lua
local module = {name = "Hgsis"}

function module.Method(self)      -- self = module table
    print(self.name)
end

module:Method()  -- Auto-passes module as "self"
-- Same as: module.Method(module)
```
Status: OOP foundation → ModuleScript architecture ready

## ⌨️ 13: UserInputService (Client-Side Input)
**Client-only. ** Fires remote events to server. Detects all devices.

Quick Reference:
Method	Use Case	Returns
InputBegan	Key press down	Input object
InputEnded	Key release	Input object
IsKeyDown()	Key held	Boolean
JumpRequest	Jump button	None
TouchEnabled	Mobile check	Boolean
Core Pattern (1 Code = All Methods):
```lua
UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if gameProcessedEvent then return end -- **Skip Roblox GUI**
    
    -- **Keyboard:** input.KeyCode == Enum.KeyCode.R
    -- **Mouse:** input.UserInputType == Enum.UserInputType.MouseButton1
    -- **Combo:** UserInputService:IsKeyDown(Enum.KeyCode.Q) and input.KeyCode == Enum.KeyCode.R
    
    if input.KeyCode == Enum.KeyCode.R then
        print("R pressed!")
    end
end)
```
Device Detection:

```lua
if UserInputService.TouchEnabled then
    print("**Mobile** - Adjust GUI")
elseif UserInputService.GamepadEnabled then
    print("**Console** - Gamepad controls")
end
```
Pro Tips:

gameProcessedEvent = Skip Roblox UI clicks

tick() variable = Double-tap detection

JumpRequest = Universal jump (all devices)

**Implementation:***
Device-specific GUI scaling
Different tool behavior per device
Double-press combos (tick() timing)

Status: Input → OOP → Production combat input systems
Next: DataStores → Persistent player data

"OOP = Code reuse. InputService = Universal controls."

## 💾 **17: DataStoreService (Persistent Data)**

**Purpose:** **Save player progress** across sessions (coins, levels, etc.)

### **Core Methods:**
| **Method** | **Purpose** | **Example** |
|------------|-------------|-------------|
| `GetDataStore("PlayerData")` | **Access data bucket** | `local dataStore = DataStoreService:GetDataStore("PlayerData")` |
| `SetAsync(key, value)` | **Save data** | `dataStore:SetAsync(player.UserId .. "_Coins", 100)` |
| `GetAsync(key)` | **Load data** | `local coins = dataStore:GetAsync(player.UserId .. "_Coins")` |
| `IncrementAsync(key, amount)` | **Add to existing** | `dataStore:IncrementAsync("Coins", 5)` |
| `RemoveAsync(key)` | **Delete data** | `dataStore:RemoveAsync("Coins")` |

### **Safety Pattern (pcall):**
```lua
local success, result = pcall(function()
    dataStore:SetAsync(player.UserId .. "_Coins", coins)
end)

if not success then
    warn("**Data save failed!**")
end
```

Key Insight: Always wrap DataStore calls in pcall (handles Roblox outages)

## 🎬 20: Animations (Character Liveliness)
### **Rig Types:**
| **Type** | **Parts** | **Use Case** |
|----------|-----------|--------------|
| **R15** | **15 parts** | **Modern games, detailed hands** |
| **R6** | **6 parts** | **Legacy games, simpler** |

**⚠️ Critical:** **R15 ≠ R6 animations** (incompatible)

### **Animation Instance vs Track:**
❌ Animation Instance: Just AnimationId (no control)
✅ Animation Track: Full control (play/stop/speed/events)
Always use: humanoid:LoadAnimation(animation) → Track

### **Priority Hierarchy (Low → High):**
Core → Movement → Idle → Action → MovementActions → Weapon
Best Practice: Set custom animations to "Action" priority


### **Core Pattern:**
```lua
local anim = Instance.new("Animation")
anim.AnimationId = "rbxassetid://ID"
local track = humanoid:LoadAnimation(anim)
track:Play()  -- Speed, Looped, Weight controllable
```
Critical Properties:
| **Property** | **Purpose**| Default |
|----------|-----------|--------------|
| Health | Current HP |	100 |
| MaxHealth	| Max HP | 100 |
| WalkSpeed	| Movement speed | 16 |
| JumpPower	| Jump height | 50 |
| Sit |	Sitting state |	false |

Key Methods:
```lua
1. humanoid.StateChanged:Connect(function(old, new)
    if new == Enum.HumanoidStateType.Jumping then
        print("**Jumping!**")
    end
end)

2. humanoid:ChangeState(Enum.HumanoidStateType.Freefall) -- Force state
3. humanoid:GetState() -- Current state
4. humanoid.HealthChanged:Connect(function(health) end)
5. humanoid:MoveTo(Vector3.new(10, 5, 20)) -- Smooth move
6. humanoid.Died:Connect(function() end) -- Cleanup
7. humanoid:TakeDamage(25) -- Deal damage
```

### Double Jump Pattern:
```lua
humanoid.StateChanged:Connect(function(_, newState)
    if newState == Enum.HumanoidStateType.Freefall then
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)
```
Status: Data + Animations + Humanoids = Complete character systems
Next: Combat systems → Full player/enemy integration

"DataStores save progress. Humanoids make characters alive."

