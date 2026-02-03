# 🚀 NPC Movement System Blueprint (v0.1)

> **Practice project for OOP + systems thinking.**  
> AI pathfinding exists but custom = more control. Tweakable blueprint.

## 🎯 **Core Philosophy**
Working scripts < Structured systems

Blueprint exists → extend with AI/your logic

Scalable folder structure

State handling foundation

text

## 📁 **File Structure & Purpose**

| File | Role | Key Features |
|------|------|--------------|
| **`movement.lua`** | **Core AI Brain** | `NPC = { move(), FindNearestPlayer() }`<br>Auto-cleanup on death |
| **`Noob.lua`** | **Movement Child** | Inheritance pattern<br>Custom logic ready |
| **`movementSetup.lua`** | **NPC Factory** | RunService + deltaTime<br>Easy setup |

## 🔧 **movement.lua - The Engine**
```lua
-- Core constructor pattern
NPC = { 
    move(),           -- Physics/behavior  
    FindNearestPlayer() -- AI detection
}
Auto-cleans NPC on death. Essential for AI → player interaction.

🧠 Noob.lua - Scalability Demo
text
Inheritance: movement.lua → Noob.lua
- Folder organization win
- Custom movement types ready
- "Scalability" flex (devs love this)
🚀 movementSetup.lua - Production Ready
text
RunService + deltaTime = smooth
Delta lag compensation
Plug-and-play NPC factory
💡 Extensions (AI/You Handle)
text
✅ Add weapons to NPCs  
✅ Patrol points system
✅ Custom movement types (Noob.lua → Pro.lua → Boss.lua)
✅ State machine expansion
🎮 Why This Exists
text
"I made it without help so I left it raw"
- Working prototype 
- OOP foundation solid
- Extendable by design
- Abandoning at "good enough"
Status: Movement exists → AI detection works → Scalable structure
Next dev: Weapons + patrol points via ChatGPT tweaks.

"Devs love scalability" → Delivered.

