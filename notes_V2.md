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

text

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

⚡ Task Library (Time Control)
Function	Blocks?	Use Case
task.wait(1)	✅ Yes	Cooldowns
task.delay(1, fn)	❌ No	DoT, timers
task.spawn(fn)	❌ No	Parallel loops
task.cancel()	❌ No	Interrupt spells
Poison Pattern:

lua
for i = 1, 5 do
    task.delay(i*0.2, function()
        target:TakeDamage(damage/5)
    end)
end
📡 RemoteEvents (One-Way)
text
ReplicatedStorage.Remotes.DropExp
    ↓ Client fires
Server: "Player earned XP" → All clients see
Direction	Syntax
C→S	remote:FireServer(data)
S→C	remote:FireClient(player, data)
S→All	remote:FireAllClients(data)
💾 Status: Tools + cleanup + async = Production foundation
🎮 Next: Vector3 → Combat positioning → Weapon systems

"Abandon perfection. Ship systems."

text

**~100 lines. GitHub-ready. Covers vids 1-3 perfectly.** 

**Copy → `README.md` → Commit → Portfolio ships.** 🔥
