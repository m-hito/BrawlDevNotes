# 🚀 BrawlDev Notes (Episodes 1-3) - Core Roblox Systems

> **"Server validates. Client suggests. Systems scale."**
> Raw dev notes → Production knowledge base

## 🎯 **01: Client vs Server Fundamentals**

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

text

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

📡 05: RemoteEvents (One-Way Communication)
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

🔄 06: RemoteFunctions (Two-Way Communication)
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

text

| **Property** | **Purpose** | **Example** |
|--------------|-------------|-------------|
| `Part.Position` | World location | `Vector3.new(10, 5, 20)` |
| `Part.Size` | Object dimensions | `Vector3.new(4, 4, 4)` |
| `Part.Orientation` | Rotation | `Vector3.new(45, 90, 0)` |

**Vector3 vs CFrame:**
Vector3 = Position only (WHERE?)
CFrame = Position + Rotation (WHERE? + FACING?)

text

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

text

**Analogy:**
Script = Reading 1 book page-by-page

Coroutines = Reading 2+ books (page 1 → book 1, page 1 → book 2)

text

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

text

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
2. Registry Pattern (LIVE List)
text
❌ Manual: table.insert(weapons, sword) -- Breaks on destroy
✅ Auto: CS:GetInstanceAddedSignal("Weapons") -- Always current
Spawn weapon → Auto-registered → Auto-handled → Auto-cleaned

3. Observer Pattern
text
CS:GetInstanceAddedSignal("Boss")     -- Subject notifies
Your handler                    -- Observer reacts
Mental Model:

text
OOP builds sword → Tags identify role → CollectionService routes logic
Status: Tags + signals = Scalable object systems
Next: OOP ModuleScripts → Production architecture

"OOP organizes 1 object. Tags manage 1000."


