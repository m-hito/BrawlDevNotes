--[[ 
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
==================== Server side vs local side=====================================================================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📝 LOCALSCRIPT vs SERVERSCRIPT COMPARISON
Aspect					LocalScript									ServerScript
Runs 					On	CLIENT (player's computer)				SERVER (Roblox servers)
Affects					Only that player							All players
Replication				No automatic sync							Yes, automatic sync
Security				NOT SECURE (hackable)						SECURE (can't hack)
Use Case				UI, local feedback, input					Game logic, data, important things
Trust					❌ Don't trust								✅ Always trust
Output Color				BLUE										GREEN
Location				StarterGui, Tools							ServerScriptService
Speed					Fast (no network delay)						Slight delay (network)
Can Access				Player's local data							Everything in game

📊 THE CORE CONCEPTS
 ---- refer to Notes on each ep: 
 
Main topic: to change sensitive information like giving player coin/ progress detail fireevent from local side to server side
this ensures server change data of coins to required unit otherwise on local side hacker can change it locally and cheat
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
=========================================== 02_Delete Instances ====================================================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Q. what are delete instance?
-> Way of handling unnecessary instances and how we can handle them with elligance without casuing memory leakage

3 methods of delete instances: 
❌ Part:Remove()     → Sets parent = nil (memory leak, don't use!)
✅ Part:Destroy()    → Complete cleanup (use for immediate deletion!)
✅ Game.Debris:AddItem(Part, Time)     → Delete after delay (use for spawned objects!)

System built: A coinZone with proper memory allocation and a delayed function to delete the coin after 5 seconds + give 
the coin to user
Refer to: Workspace/CoinZone(model)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
================================================🕹️ 03_Tools ========================================================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Core concept: what are tools?
--> tool are equippable inventory items that player can equip and interact with the world and cause events to fire from local 
side to server side and catch that event, any fire system or build or grab something sword, picaxe, move tool etc

Quick info: Player vs character, workspace has the character refrence game.Players has the actual player refrence
Interesting Note: A player having a tool while tester, the tool is in players.Player.Backpack (actual player refrence) but 
when the tool is equipped it is moved into workspace.player (this where player character lies)

🕹️System Architecture
Tools flow through your game in this sequence:

📌StarterPack (global default) → Each Player.Backpack → Character (when equipped)
This is why understanding parent relationships is essential—when tool.Parent changes from Backpack to Character, 
the Equipped event fires automatically.

Tool when dropped its parent is set to workspace but when pickup again (equipped) parent is player character uneqipped parent is 
set to actual player refrence in game.players

🔎 Tool properties:
tool.ToolTip = "Picaxe" -- str value
tool.TextureId = "rbxassetid://1234566" -- when hovered show a text
tool.RequiresHandle = true/ false -- depending on need
tool.CanBeDropped = true/ false -- if false tool can't be dropped
tool.ManualActivationOnly = true/false -- if script can activate the tool or only client can (for auto attack system)
tool.Scale = 2 (size multiplier for tool)

📌 Tool events:
tool.Equipped:Connect(function() end) -- fires when tool is equipped
tool.Unequipped:Connect(function() end) -- fires when tool is unequipped
tool.Activated:Connect(function() end) -- fires when tool is clicked
tool.Deactivated:Connect(function() end) -- fires when tool is clicked again

We can also force activate the tool on equip by adding tool:Activate() in the tool.Equipped event

❓ StarterGear: give a specific group of people/ player a gear which is exclusive from starterPack
startergear given when join + respawn it is used when a player bought it/ reward

✅ System BUILT: Charging tool, cooldown tool (broken rn), player stats tracking on activate the chare tool continuosly increase clicks
also scale the tool per chargin rate and play a sound on equip the tool (also change the color per charging rate)

✨ System Potential: Combat Weapon System - Full damage system with cooldown, animations, and particle effects
Item Shop - Purchase tools with currency that persist across respawns
Team-Based Equipment - Different weapons for Red/Blue teams
Quest Progression - Unlock tools by completing objectives
Tool Durability - Tools degrade, requiring repair/crafting
Dual-Wield Combat - Equip two weapons with synchronized attacks
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
================================== 04_Task functions wait() spawn() delay() ==========================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Conception: consider 4 tasks to be executed by compiler,
Task1 -- wait() -- task2 , if wait statement is used run task1 -- wait -- run task2 then task3
Task1 -- Delay() -- task2 -- task3, run task1 run task3 (if delay is long) run task4 then run task2 
Run 2 task at same time: task1 -- spawn() -- task2 -- task3, run task1 run task2 and task3 at same time

print("task 1") print("task2") print("task3 ") --task1 is run task2 is run after task1 task3 is run after task2 
this is synchronous model each task wait for prev to finish But what if?

# we need to have attack cooldown without freezing the game?
# poison ticking to damage 5 wwithout preventing player to move?

📌5 Task function (in order of importance) -- need time control? Use these functions 
1) task.wait(x) -- stop all task for x seconds
when to use: attack cooldown, poison ticking, spawn delay etc

2) local delayTask = task.delay(x, function() end) -- run function after x seconds but dont stop all task for x seconds
When to use: spawn timer, Damage over time(DoT), Floating damage numbers
Patter: u can store delay task in variable to cancel it (or use it inside function as seen in poison system no var needed)

☢️ Real example: Poison system -- refer too charging tool -- applyPoison

3) task.cancel(delayTask) -- cancels the delay on certain condition (lets say cancel poison when the player hits another
player this can be rlly cool feature )
When to Use:
✓ Interrupt casting spells
✓ Cleanse status effects
✓ Interrupt channel abilities
✓ Cancel buffs if condition changes

4) task.spawn(function() end) -- it blocks nothing but creates parallel timeline to run another function with the compiler
	Use when u need to use 2 or more function/ loops at the same time.
When to use:
	Enemy wave system each enemy get its own ai, without spawn function each enemy attack one at a time 
	
5) task.defer(function) -- execute this code in last
	Use to clean up code (after all events fire) / save progress or load ui element / logging (after game stabalizes) 
	
Core Patterns You'll Use Constantly
Pattern 1: Cooldown System (task.wait - BLOCKING)
Pattern 2: DamageOverTime system (local dealyFunction = task.delay(time, function))
Pattern 3: Parallel Loops 
Pattern 4: Interrupting spells (task.cancel(delayFunction))
Pattern 5: Batch Updates (task.defer - CLEANUP)

✅System built: Poison system in charging tool
❓can make wave system save for later.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
==================== #05_Remote events (one way communication) =====================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Conception: Communication between server and client / server to server to share information or catch events for example client
fires an event server gives the data to client is called one way communication

ReplicatedStorage: best place to store remoteEvents 

Client to server
----Client RemoteEvent:FireServer(data ) 
----ServerScript RemoteEvent.OnServerEvent:Connect(function(player, data) end) ) -- server knows the player 
Use Cases:
✓ Buy item (server validates coins)
✓ Deal damage (server validates hit)
✓ Use ability (server checks cooldown)
✓ Create object (server authorizes)

Server to client
----ServerScript RemoteEvent:FireClient(player, data) 
---- Client RemoteEvent.OnClientEvent:Connect(function(data) end) -- client knows the player because client is player lol 
Use Cases:

✓ Personal greeting
✓ Individual notification
✓ Private UI update

Server to all client
----ServerScript RemoteEvent:FireAllClients(data) 
Use Cases:
✓ Global announcements
✓ Round start/end
✓ Server-wide events

Fixed poison script with remote events
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
========================= #06_Remote functions (Two Way communication) ===============================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Conception: it is a two way communication between server and client suppose a client buys a sword for 50 coins this is
Client to server; server will deduct clients 50 coins and give the sword to client this is Server to client communicate 

✅Syntax Comparison:				 Events vs	Functions
Aspect							RemoteEvent														RemoteFunction
Create						Instance.new("RemoteEvent")										Instance.new("RemoteFunction")
Client 						sends event:FireServer(args)									func:InvokeServer(args)
Server 						receives event.OnServerEvent:Connect(function(player, args)		func.OnServerInvoke = function(player, args)
How to end						end)															end (no parenthesis)
Response						Need separate event												return value
Client waits					No																YES

Client to server to client: Client invokes server, waits for response from server server returns true or false then the client rest of the code is executed
---- Local side : local result = RemoteFunction:InvokeServer() 
					if result then (execute task) end
---- server side : RemoteFunction.OnServerInvoke = function(player) return true/ false end
Use: Buy item; player interact with shop npc (CLIENT TO SERVER) , shop gives player item and displays a UI on player screen (SERVER TO CLIENT) -- 2 way communication 

❌ Server to client to server is not needed because server already knows everything client knows

Event vs CallBack
Event just fires an event to server/client and thats it, example: client says to server give damage to enemy please
Callback function waits for response from server, example: client says can u give this player damage? server checks if he should give damage if yes then gives damage to player
and can also do other events along with it like blinding his screen
Callback function client waits for server response and doesnt execute till response achieved

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
================================================ #7_Vector3 ====================================================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Conception: 🎯 WHAT IS VECTOR3?
Definition: A data type that stores 3 numbers (X, Y, Z) representing position/size/rotation in 3D space.
X Axis = Left/Right movement
Y Axis = Up/Down movement
Z Axis = Forward/Backward movement

📊 VECTOR3 BREAKDOWN
Properties using Vector3:
Property					What it does				Example
Part.Position				Where part is in world		Vector3.new(10, 5, 20)
Part.Size					How big part is				Vector3.new(4, 4, 4)
Part.Orientation			How rotated part is			Vector3.new(45, 90, 0)
Humanoid.RootPart.Position	Player character location	Can be changed to teleport

🔎 When to use vector3? when rotation is not included, if its included then use cframe
Vector3 = Position only (where is it?)
CFrame = Position + Rotation (where is it + which way is it facing?)

🤔 WHEN TO USE VECTOR3
Cases WHERE YOU'LL USE IT:
✅ Movement Systems
Player walking, jumping, falling
Projectiles flying
Enemies patrolling

✅ Positioning
Spawning items at location
Teleporting players
Placing objects on ground

✅ Physics
Velocity (direction + speed)
Forces (gravity, explosions)
Raycasting (shooting rays)

✅ Distance Checks
Is enemy in range? (use .Magnitude)
Is item close? Check distance

✅ Direction Finding
Which way should NPC face? (target - npc)
Where should projectile go? (mousePos - gunPos)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
================================================#8_corutines ======================================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📌 Basic Conception: 
to run multiple task at the same time we create a thread, and resume it. Corutines allow us to create multiple
execution path so that while true can run simultaneously in the same script 
Basically advanced threading.

Foundation: What Are Coroutines?
🔎 Advanced Conception: (BrawlDev #8)
A coroutine is a thread—a separate execution path inside your script. Normally, Lua runs code line-by-line. 
If you have a while true do loop, nothing below it runs until the loop ends (which it never does). 
Coroutines let you create multiple execution paths so that different while loops can run simultaneously 
in the same script. 

Think of it like this:
	Normal Script: You're reading a book page-by-page. You can't start a second book until you finish the first.
	Coroutines: You're reading two books simultaneously—you read a page in Book 1, then a page in Book 2, 
				then back to Book 1.


✅ Key function: 
1) local task = corutine.create(function(param1, param2) -- treat it like normal function) -- create a thread
2) corutine.resume(task, "Hit", true) -- resume the thread/ pass the req paramaeters here.
3) corutine.yield(task) -- pause the thread (outside corutine) we can direct yield it inside corutine and pass a parameter
	corutine.yield(true, "param1", 5) -- inside corutine we pass this to return to the variable who called this 

	local success, result1, result2 = corutine.resume(variable) -- access the yield return value 

4) corutine.wrap()() -- wrap a variable containing function/ direct function and execute it using resume/ () direct
5) corutine.close(task) -- close the thread


🔎 -- corutine.wrap() + Methods to call it/ create it 

1) local task = corutine.wrap(function() -- normal function )
task() -- call like u call a method inside oop/

2) corutine.wrap(function() -- anothre way to call this wrap function )()

3) local myFunction = function() -- normal function end 
corutine.wrap(myFunction)() -- wrap a variable containing function inside corutines () to call it 

📜 -- Corutine status: print(corutine.status(myFunction)) -- we can check before resuming it so it shows suspended
printing after execting the function it will show dead, printing inside function will show running 
1) suspended
2) running
3) dead

🔎 pattern discovered:
	coroutines' cooperative scheduling.
	Status Monitor (Idea born from prev one)
	
	
✨System potential:
1) Wave Spawning (Tower Defense): 
Each wave is a coroutine. While Wave 1 is active, Wave 2 is sleeping (yielded). When Wave 1 ends, resume Wave 2.

2) Turn-Based Combat: 
Each player's turn is a coroutine. Player 1 attacks, yields. Player 2 attacks, yields. Back to Player 1.

3) Ability Cooldowns: 
One coroutine per ability, each counting down independently.

❗ SELF LEARNING PATH ❗
Patterns You'll Discover (Self-Learning Path)
As you practice coroutines, you'll naturally find:

1) The State Machine Pattern: Each coroutine represents a "state" (Idle, Running, Attacking). 
Switching between them is just resuming/pausing threads.

2) The Producer-Consumer Pattern: One coroutine produces animation frames (yields after each frame). 
Another consumes input (yields after each input check). They coordinate without blocking.

3) The Debounce Pattern: Coroutines naturally debounce. If a coroutine yields while checking for input, you 
can't spam the same input twice in one frame.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
=============================================10_collection-service=========================================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Conception: We want 10 behaviour objects and run a single script/ combined script logic on one, we can do it using
oops but we still make 10 diffrent objects and give them behaviour of combined logic, what collection service does
is it will find the brickInstance and give the logic easily.

Tags (🏷️ A sticker): Describes a group of object : Does this object belong to this group❓ (boolean) 
	Ex. fist --> Tag = "weapon", "melee". We can share this tag with other weapon (archetype system)
	
Attributes (🧾 An ID card): it describes property of object. Fact attached to the instance itself, not hardcoded. 
	Question : What propertyy does this object have❓ 
	Ex. fist --> attribute = key/value weapontype["Fist"] 
	
OOPS (🧠 The brain): When we need a behaviour from an object: What can this object do ❓
	Ex. behaviour, function, algorithms, inheritence
	
Table: Temporary memory allocated 

❗ OOP VS COLLECTION SERVICE 
They do not compete, they complement each other and needs both for production!

💭 	OOP = How to build one sword
	CollectionService = How to find and manage 100 swords


🔎 OOP is for:	Organization								✨ CollectionService is for: Logic

- Creating one reusable blueprint					- Finding groups of existing instances
- Managing behavior of individual instances			- Applying shared behavior to many instances at once
- Inheritance (KillBrick → SpikeKillBrick)	- Dynamic addition/removal (spawn new enemy → auto-tagged → auto-handled)
 
✅ Key Syntaxes:
✨ 1) local killBricks = collectionService:GetTagged("KillBricks") -- get all instance tagged with killbricks
2) local AlltagsOfInstance = collectionService:GetTags(workspace.KillBricks.Part1) -- get all tags returns table
3) local allTags = collectionService:GetAllTags() -- store all tags inside this game in this variable
4) CollectionService:AddTag(newPart, "KillStones") -- manually add tag to an instance
5) CollectionService:HasTag(newPart, "CustomTag") -- check if he has the tag returns true/false
6) CollectionService:GetInstanceAddedSignal("CustomTag"):Connect(function()) -- Fires when added signal like an event
7) CollectionService:GetInstanceRemovedSignal("CustomTag"):Connect(function()) -- Fires when removed signal (event)

✅ Patterns (Understood): 
1) Passing an instance while added signal, we can setUp the event when tag was added. No manual function 
calls will be required

2) Static collection: We use getTagged(tagName) to get all instance tagged with this and setUp its behaviour
	By using for loop (Note we get the instances using for loop, SetUp behaviour inside that)

3) Dynamic Collection: Create instance and tag them via scripts. Handler must use GetInstanceAddedSignal()
	to catch the event of dynamically adding instances
	
==============================================================================
❌ Patterns didnt understood (yet to discover):

💡 Pattern 1: The Archetype System 💡
"What kind of sword is this?" We dont group by folder or class we group by Identity. 
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔑 Mental model: 
	├─ OOP = how one sword attacks
	├─ Archetype = what role this object plays in the world
	├─ CollectionService = how the world recognizes that role

🧠 Your metaphor applied:
	OOP builds the sword
	Archetype tells the game what bucket this sword belongs to
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Current naive Approach:
	Weapons
	├─Fist
	├─Sword
	├─Guns

This is organization not behaviour routing.
	
Archetype Approach:
	Tags
	├-- Weapon
	|	|--- 
	├-- Melee
	├-- Ranged
	├-- Enemy

An object can be;
 weapon + Melee, Weapon + ranged, Enemy + Weapon 

Classes struggle with this.
Tags do this effortlessly.

lua
-- Before: Logic scattered in individual scripts
-- After: One handler per "type"/ Combining individual logic to make an object
CollectionService:GetInstanceAddedSignal("Enemy"):Connect(HandleEnemy)
CollectionService:GetInstanceAddedSignal("Weapon"):Connect(HandleWeapon)
CollectionService:GetInstanceAddedSignal("Trap"):Connect(HandleTrap)

💡 Pattern 2: The Registry Pattern 💡
(“CollectionService is a LIVE LIST, not a query”)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔑 Mental model:
	├─ CollectionService is not asking “where are my swords?”
	├─ It is maintaining a constantly-updated register of swords
	Its like: Player list, enemy list, active list 

🧠 Your metaphor:
	├─ OOP = build sword
	├─ Registry = who currently EXISTS as a sword
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Old way (manual registry – fragile) : table = {} 
	table.insert(weapons, Sword)
	table.insert(weapons, Fist)
	
This breaks when:
━ things get destroyed
━ things respawn 
━ things are cloned dynamically

✅ CollectionService (automatic registery): local collectionService = game:GetService("CollectionService")
	local weaponRegistery = {}
	weaponRegistery:GetAll() return collectionService:GetTagged("Weapons") end

Using oops we can get all instances tagged with "Weapons"


Real thing registery pattern active ✨
	CollectionService:GetInstanceAddedSignal("Weapons"):Connect() -- auto registered 
	CollectionService:GetInstanceRemovedSignal("Weapons"):Connect() -- auto fire this when removed 
	
Now:
	spawn weapon → auto-registered
	destroy weapon → auto-removed
	unequip tool → auto-handled (if tag removed)

No bookkeeping. No cleanup bugs. This is not inheritence it is existance based membership...


lua
-- At any point in the game:
CollectionService:AddTag(newBoss, "Boss") -- Automatically spawned and configured
CollectionService:RemoveTag(defeatedBoss, "Boss") -- Automatically cleaned up

💡 Pattern 3: Implicit Observer Pattern 💡
Using GetInstanceAddedSignal() / GetInstanceRemovedSignal() is the Observer Pattern—objects notify watchers 
when they're added/removed.

lua
-- This is implicitly the Observer Pattern
-- The "Enemy" collection is the "subject"
-- Your handler is the "observer" watching for changes
===============================================================================


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
================================= #11_OBJECT ORIENTED PROGRAMMING (Module scripts) Mastery ==================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MOST IMPORTANT TOPIC TO SAVE TIME AND CODE REUSE 
Q. what is object oriented programming?
-- its a way of organizing code so that related behaviour and data are contained inside the same object 

# Key Concepts ✨
Concept					Meaning									Example
Class				Blueprint for an object					A house blueprint
Properties			Data/state of an object					House color, size, rooms
Methods				Behaviors/actions of an object			Open door, paint walls
Instance			Actual object created from class		An actual house built from blueprint 
Instantiation		Creating an object from a class			Building the house

📌 Class: it has a list of properties in methods; properties define state of an object while methods define behaviour of an object 
think of classes as a blueprint of an object house But we cant enter the house from a blueprint, we need to construct the house first
Objects: its a collection of properties and methods defined using classes it can contain its own unique feature but classes contain genral features, basically
		 object is what we are building in this case; ✅ house itself is the object, classes is related data, behaviour we give to object and class is blueprint of house
Process of constructing the house is called as instantiation 

Luau doesnt have classes we do it by using table and metatable 

🔎  Tanget, Colon call syntax  
make a table contatining all the properties local module = {name: "Hgsis"}
make a function outside table containing method function module.Method(self) print(`{self.name} `) end 
we can also do module:Method() print(`{self.name}`) end
auto pass self and acces using print

now target:method() or target.method(self) the colon method is called syntactic sugar it automatically passes the module table as first argument
----- please refer to OOP IMP document specifically for this 

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
======================================== #13 UserInputServices =====================================================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Q. is inputservide server side or client side? what is userInpt service?
--> It is taking input from user from client side locally and giviing it to server via remote events/
other methods detecting input from player/ detecting the device user is playing on
NOTE: userInputService only works on local script and not on server script

Quick Summary:

Method								When to Use					Returns
userInputSvc.InputBegan				Detect key press down			input object
userInputSvc.InputEnded				Detect key release				input object
userInputSvc:IsKeyDown(key)			Check if key held				boolean
userInputSvc:JumpRequest()			Detect jump button				none
userInputSvc.TouchEnabled			Check if mobile					boolean
userInputSvc.GamepadEnabled			Check if console				boolean
userInputSvc:GetMouseLocation()		Get screen position				Vector2
userInputSvc:GetMouseDelta()		Get mouse movement				Vector2

🔎 Core Concept
UserInputService detects any input from any device (keyboard, mouse, gamepad, mobile touch).
Always use in LocalScript (client-side only).
​
📌 Key methods: 1 code explain all methods!
✨ 1) UserInputService.InputBegan:Connect(function(input, gameProcessedEvent) 
	if gameProcessedEvent return end -- do nothing when clicked on roblox gui dont detect input
	if UserInputService:IsKeyDown(Enum.KeyCode.Q) and input.KeyCode == Enum.KeyCode.R then 
	-- Implies user is pressing R while holding Q 
	end 
	-- do for genral input 
	
	end)

✨ 2) UserInputService.InputEnded:Connect(function(input, gameProcessedEvent) --fire when let go of key)

When does InputBegan Fire?
--> whenever user gives an input (including mouse position, wasd, arrow keys any input from user)

But How we can detect the input we need? How to detect the inputType not necessarily its from keyboard.
--> ✨ input.KeyCode == Enum.KeyCode.R (You can see from library the list) -- keyboard keys 
--> ✨ input.UserInputType == Enum.UserInputType.MouseButton1 (Detect inputType like game pad, click, etc)
--> ✨ UserInputService:IsKeyDown(Enum.KeyCode.Q) -- detect if player is holding Q 
gameProcessedEvent is used to detect the player input on roblox gui and dont count it as input

To get the type of device user is playing on we got better method; (Device detection)
✨ 3) if UserInputService.TouchEnabled/GamepadEnabled then -- this user is on mobile end 

✨ 4) UserInputService.JumpRequest:Connect(function() end) -- fire when user presses jump button

✨ 5) UserInputService:GetMouseLocation() --returns the current mouse position on screen in OffSet val

Implementation: we can detect user device and fit gui according to it for better performance, 
diffrent behaviour of tool depending on the device , etc

Detect double press by a tick variable 

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
===================================================== #17_Datastore ==============================================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Conception: we need database to store player progress/ information rather than using local variables, we use datastore service
provided by roblox/ methods to make our datastore asyncs. 

DatastoreService: to access datastore service
DatastoreService:GetDataStore("PlayerData") -- get a key value pair of dictionary to store player data.
DataStoreService:SetAsync("Coins", 100) -- set coins to 100 and next time join still there, set it only once 
DataStore:GetAsync("Coins") -- get coins value
DataStore:IncrementAsync("Coins", 5) -- increment a data like coin which already exist instead of setting it again

DataStore:RemoveAsync("Coins") -- remove coins data, remove this key pair from dictionary itself

Pattern: if we set a data once roblox fails then it will be bad, to prevent that we use pcalls to set data/use
dataStore Service till its success or fails



━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
===================================================== #20_ Animations =======================================================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✨ 1. Animation Fundamentals
Q. What Are Animations in Roblox?
Animations give characters and NPCs "liveliness" and realism. Every player movement (walking, jumping, idle) is an animation.

Q. What is a Rig?
A rig is an object with a skeletal structure that allows you to manipulate parts and add animations. It's the foundation for all character animation.

Two Rig Types:
R15: 15 individual parts (head, torso sections, arms, legs, hands, feet)
R6: 6 parts (head, torso, two arms, two legs)
Critical: You cannot mix R15 and R6 animations. An R15 rig cannot play R6 animations and vice versa.

2. How to create an animation; Watch tutorial for comprehensive guide.

✨ 3. ANIMATION INSTANCES vs. ANIMATION TRACKS
🔎 Animation Instance
What it is: A basic container with only one property: AnimationId
Limitations: Cannot control playback, speed, or detect events

🔎 Animation Track
What it is: An enhanced version of the Animation instance with full control methods and properties
local animationTrack = humanoid:LoadAnimation(animation)
Advantages:
Play/Stop/Pause animations
Adjust playback speed
Detect animation events (markers)
Monitor animation state

# Key Insight: Always convert animations to tracks for scripting control. Animation instances alone are insufficient 
for game implementation.

✨ 4. ANIMATION PRIORITY SYSTEM
Why Priority Matters
When multiple animations play simultaneously (e.g., idle animation + custom animation), Roblox prioritizes one over the other. Same-priority animations create weird blends.

Priority Hierarchy (Low to High)
Core (lowest priority)
Movement (walking, jumping)
Idle (default character standing pose)
Action (recommended for most custom animations)
Movement Actions (running + action blend)
Weapon (highest priority - combat actions)

📌 Best Practice: Set most custom animations to "Action" to override idle animations.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
============================================ #29_Humanoid states, properties, methods ==============================================
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Conception: humanoids are character model which give functionality to a model/ player character in this case
Q. what is a humanoid? what happens when it is removed?
--> humanoid is a animation controller for a character it controls the animation and movement and allows a model 
to inetract with the world, without it a character is just a model humanoid sets apart from character and model 

📊 HUMANOID PROPERTIES (What you need to know)
Critical Properties for Enemy System

Property				What it does			Default	Used for
Health					Current health			100	How much damage enemy took    Humanoid.Health
MaxHealth 			Maximum health	100			Enemy's health pool				Humanoid.MaxHealth
WalkSpeed			How fast walks	16			Enemy movement speed			Humanoid.WalkSpeed
JumpPower			How high jumps	50			Enemy jumping ability			Humanoid.JumpPower
Sit					Is character sitting?		false	Seating in vehicles		**seat:Sit() --(need a seat )
Parent				Where humanoid is located	Character model	Accessing the humanoid

✨ UseFul methods:
1) humanoid.StateChanged:Connect(function(oldState, newState) 
	if newState == Enum.HumanoidStateType.Jumping then -- Do something with certain states/ methods
end) 
	Enum.HumanoidStateType.Jumping
	Enum.HumanoidStateType.Dead
	Enum.HumanoidStateType.Running
	Enum.HumanoidStateType.Freefalling
	Enum.HumanoidStateType.Landed 

🔎2) Humanoid:ChangeState(Enum.HumanoidStateType) -- Force change humanoid state (useful)

3) local getState = humanoid:GetState() -- Returns the current state of the humanoid

4) Humanoid.HealthChanged:Connect(function(health) end) -- Fires when health changes
5) Humanoid:MoveTo(Vector3.new()) -- smoothly move to a position
   Humanoid.Parent:MoveTo(Vector3.new()) -- Teleports to the vector value (humanoid parent is modelCharacter refrence)
6) Humanoid.Died:Connect(function() end) -- cleanUp by remove humanoidRootPart/ other uses

7) Humanoid:TakeDamage(10) -- (damage) is the amount of damage taken
8) Humanoid:EquipTool(tool) -- tool is a tool instance (tool must be in character model)

Easy system: Double jump system -- humanoid.StateChanged --> humanoid:GetState() if freefalling Humanoid:ChangeState
(this will get more complex when actually coding it but u get the basic idea)

]]
