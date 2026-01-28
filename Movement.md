this movement small system i made was just pratice for OOPS to be honest, im kinda obsessed with system/ structure thinking rather than "working scripts", i could use ai path finding service to make movement system
but i made a movement custom module for each npc handling.

movement.lua (modulescript)
this is core movement script which decide the behaviour of each npc/ construct here. Auto cleans up npc on dying, when making this movement module i jus wanted to make the npc move so i made move() function
and give it to constructor NPC()

NPC = { move() }

tried to do some state handling, well for obvious benefits and it works but its on ur imagination how u can extend it im just abandoning it not till perfection just as "something exists", FindNearestPlayer()
essential for ai detecting player to follow/ why make npc move if u dont want to detect player?

NPC = { move(), FindNearestPlayer() }

now u can add patrol points just use chatgpt/ ai to add that if ur really confused but jus understand it at the end.
i made it without any help so i left it empty 

Noob.lua (modulescript)
its jus a child of movement to keep folder organized like yeah i dont know what i was thinking but inheritence thats it, custom logic and stuff but not actually using it, but yeah scalable
devs love scalability i guess, i stopped even tho it was uesless to make but the word "scalability" made me want make this script

movementSetup.lua
just setting up our npc, it depends on u how u want to set it up, i just wanted to make it work, and yeah runService was good choice with delta lag i guess 


