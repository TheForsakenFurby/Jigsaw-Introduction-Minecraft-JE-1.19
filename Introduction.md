# **An Introduction to Jigsaw Blocks**
### **(Minecraft Java Edition 1.19.2)**

*(Disclaimer: I am not an expert with jigsaw blocks nor with structure generation. I'm more or less just trying to explain what I understand, using a couple of imaginery examples. Even once you have the basics down, I recommend messing around with the system a bit until you really get a good idea for how it works and how best to use it. **I am also assuming that the reader alreadly knows how to make custom structures generate using structure blocks and the relevant json files.**)*

### **Useful Links**

• Minecraft Wiki page for custom structures, including explanations for many of the files involved in structure generation: https://minecraft.fandom.com/wiki/Custom_structure

• Minecraft Wiki page for jigsaw blocks: https://minecraft.fandom.com/wiki/Jigsaw_Block?so=search

• Misode's guide for how to add custom structures (including an explanation of template pools): https://misode.github.io/guides/adding-custom-structures/

• Jacobsjo's Minecraft Jigsaw Preview— doesn't explain things but is very valuable for quickly visualizing how your jigsaw structures would generate: https://jigsaw.jacobsjo.eu/

# **Example 1 / General Explanation**

For this section, I'll go through the jigsaw block UI and explain what each field, in order, does generally; I'll also explain what I'd set each field to be in this example, and why.

The general idea I'm going for in this example is something similar to the Battle Towers mod, with jigsaws randomly selecting different options for each of the different floors. The specific jigsaw in question would be the connection between floor 1 and floor 2, aiming to get a variant of floor 2 that includes loot chests. *(This context will help to explain why I've set things certain ways.)*

### **Target Pool**

The "Target Pool" field is a reference to the **namespaced** `template_pool` file you want the next structure file/room/etc. to be from. (I'll briefly talk about template pools themselves further down.)

This should be in the format `<namespace>:<file>` or `<namespace>:<subfolder>/<file>`. (You can also nest subfolders, so `<namespace>:<subfolder>/<subfolder>/<file>`, etc., will also work.) Subfolders aren't necessary, but can be helpful for organization.

In my tower example, the next structure piece I want is a second floor that contains loot. The template pool I would want to call would have the filepath `worldgen > template_pool > towers` in my datapack, and within the `towers` folder it would be named `floor2_with_loot.json`. So, I would set the "Target Pool" field to `furbypack:towers/floor2_with_loot`. (Note that the `worldgen` and `template_pool` folders aren't included. This is because Target Pool can only reference files in those folders of the datapack in the first place.)

### **Name**

The "Name" field is the name *of the jigsaw block itself.* **It does not refer to any file,** but is used together with the following field to decide which jigsaw block in the next room to connect to. (You need a jigsaw block on each side of the connection!)

You don't need to namespace this field (or the next one), but if you leave them un-namespaced the game will automatically add `minecraft:` to the beginning when you save the data. I personally choose to namespace these fields.

In my tower example, this jigsaw block is part of the nbt file for floor 1, and serves as the connector to floor 2, so I'd set the "Name" field to `furbypack:floor1_top`.

### **Target Name**

The "Target Name" field is the name of the jigsaw block to search for in the structure of the next template pool. Essentially, the jigsaw block we connect to should say whatever you've put here in its own "Name" field.

This field is important because once the next structure is chosen, we want to make sure that the jigsaw block from our current structure connects to the correct jigsaw block from the next structure. (To show this importance, imagine that one of the possibilities for my second floor has a secret room off to the side, which has another jigsaw block facing down to connect to a treasure room next to floor 1. We would want to make sure that the ladder leading from floor 1 to floor 2 connects to the bottom of floor 2's main room, instead of to the secret room that's supposed to lead to treasure. So we would give the treasure room connection a different name from the main tower connection, and specify the name of the main connection in our Target Name field here.)

In my tower example, the jigsaw block in floor 2 facing downwards into floor 1 would have `furbypack:floor2_bottom` in its "Name" field, so I'd set the "Target Name" field here to say the same thing.

### **Turns into**

The "Turns into" field is the block that the jigsaw block should become after the structure generations. You can also specify block states using brackets. Using this, you can have jigsaw connections in walls, in small spaces with no air blocks, etc., while still controlling what you want the structure to look like and how it connects.

In my tower example, I want there to be a ladder to move between the two floors, and I want the connection to be at this ladder, so I set "Turns into" to `minecraft:ladder[facing=north,waterlogged=false]`. (NOTE: Directional block-states, such as `facing=north` here, will be rotated along with the structure, so you don't need to worry about things breaking because of which way is technically north or whatever.)

*(Also note that the `waterlogged` blockstate won't prevent waterlogging in every case. With how custom structure generation currently works, any water-loggable blocks are automatically waterlogged in places where there was water from aquifers, the ocean, etc. before, and there is currently no way to prevent it even through processor lists. If your blocks are sometimes being waterlogged when you don't want them to, it's much more likely that this is the issue you're running into, rather than it being a problem with your jigsaw blocks.)* 

### **Joint Type**

The "Joint Type" field is only present for vertical connections. If you have it set to `Aligned`, the next structure will **always** face the same direction relative to the structure that leads into it. If you have it set to `Rollable`, the next structure will be rotated randomly in relation to the one it's coming from.

On the structure block itself, the **line on the side of the block** (rather than one of the three arrows) determines how the structures will be connected if this field is set to `Aligned` (if set to `Rollable`, that line doesn't really do anything). Specifically, the line on each of the two connecting jigsaw blocks will be lined up so that they form one longer line when the structure itself is generated. This also means that **cardinal directions don't matter when you're making and saving the structure pieces; rotation is handled entirely by these lines and this field**.

`Rollable` is a good option for "structure pieces" which only contain mobs and no blocks, or for any structures where the jigsaw blocks are in the exact center and rotation doesn't matter. Otherwise, `Aligned` should be used. This field defaults to `Rollable`.

In my example, my connection I want my tower to be standing straight, meaning I don't want the next floor to be rotated randomly, so I set the Joint Type to `Aligned`.

### *Everything Else*

The final three fields aren't related to the saved data of the structure block itself, and are instead used to **manually** generate structures (such as for testing purposes). I personally don't ever use these, because I find Jacobsjo's Minecraft Jigsaw Preview (https://jigsaw.jacobsjo.eu/) to be much faster, user-friendly, and easier to comprehend.

But, for the sake of completeness, I will still explain these fields here:

**Levels**: This slider specifies how many steps to manually generate. So, if you set it to `1`, for example, the next piece of the structure will generate, but none after that, even if normally there would be more. If you set it to `2`, the next piece will generate alone with every piece that comes directly after it, but then the process stops. *Remember that this only concerns manual generation, so you don't need to touch it at all when setting up your custom structures. Naturally-spawning custom structures will always have as many levels as you specify in the `structure` json file.*

**Keep Jigsaws**: When this toggle is set to `ON`, the jigsaw blocks will remain after the structure is manually generated. When set to `OFF`, the jigsaw blocks will turn into the blocks specified in the "Turns into" field above. *Remember that this only concerns manual generation, so you don't need to touch it at all when setting up your custom structures. Naturally-spawning custom structures will always replace jigsaw blocks on their own.*

**Generate**: This is just the button you click when you're ready to manually generate.

And, of course, there is **Done**, which exits the block's UI and saves any changes made to the first five fields; and **Cancel**, which exits the block's UI and ignores those changes. (The "Levels" and "Keep Jigsaws" fields never have their data saved.)

# **Jigsaw Block Texture and Structure Piece Placement**

You want to make sure you place your jigsaw blocks correctly so that the connections work as intended.

There are four types of faces to a jigsaw block: the puzzle piece, the arrow (these are on 3 of the faces), the line, and the blank face.

The puzzle piece shows where the connection itself is: the next structure's jigsaw block will be touching the puzzle piece of this jigsaw block (both puzzle piece faces will be touching each other).

The arrows say the same thing in a different way: it's pointing in the direction that the next jigsaw block (and the next structure) will be in. Three of the six sides are arrows pointing the same direction.

The line is used for alignment, as detailed in the "Joint Type" section above. If your Joint Type is `Aligned`, the lines on each jigsaw block will connect together to make one longer line when the structure generates. If your Joint is `Rollable`, this line doesn't matter because it will be ignored (unless you have another possible connection that *is* `Aligned` targeting this jigsaw block, in which case the line would matter for that connection, but not this one). The line will always be facing upwards for horizontal connections as the "Joint Type" field doesn't apply to them. **Reminder: Coordinates and cardinal directions do not matter for alignment, only this line.**

The blank face is just the opposite face of the puzzle piece.

**Regarding structure piece placement**, one important thing to keep in mind is that a piece will fail to generate if there is *any* overlap between its bounding box and any other piece's bounding box, even if the overlap is just structure voids or air. Make sure you plan your structures out so that pieces don't get cancelled more often than you'd like! **The exception to this** is that if a piece generates fully inside the bounding box of the piece that called it (e.g. through a jigsaw block in the center of the previous piece), it and any other pieces that come from it will still place so long as they stay fully within that encompassing piece's bounding box, and so long as they don't overlap with each other.

# **Example 2**

For this second example, I'll just list all the settings I would have and explain a couple things.

In this scenario, I'm imagining a castle with lots of winding hallways randomly connecting to each other, resulting in something like a maze.

### **Settings**

**Target Pool**: `furbypack:castle_hallways`

**Name**: `furbypack:hallway`

**Target Name**: `furbypack:hallway`

**Turns into**: `minecraft:air`

*Because this is a horizontal connection, there is no **Joint Type** field. The connection will always behave as if it were `Aligned`.*

### **Explanation**

My Target Pool isn't in any subfolders this time. No real reason for this, just wanted to remind you that subfolders aren't required.

**The Name and the Target Name are the same!** This is because, unlike my tower in example 1, I really don't care in what order these hallways generate, or which connects to which. So long as they're all hallways, it's good. Keep in mind that making the Name and the Target Name the same also means that the structure piece this jigsaw block is a part of can even connect to another copy of itself, so long as it's in the template pool.

# **Template Pools**

Before I get into this, I ask that you please read the template pool section of Misode's custom structure guide; it's really well done: https://misode.github.io/guides/adding-custom-structures/#the-template-pool

Okay now that you've read that, there's a couple more things I'd like to add on that are relevant here:

1) While Misode's guide specifically refers to a starting pool, everything they say can be applied to all template pools. You can (and honestly, if you're using jigsaw blocks, you probably should) have multiple different files in most template pools. The format is the same as shown in the guide, just add more objects with the same format inside the `"elements"` brackets (and remember to put commas in between those objects).

2) The `"name"` field is just the name of this template pool's json file itself. For some reason Minecraft needs this, **⚠️BUT VERY SOON IT WON'T⚠️**. If you're working in the 1.19.3 snapshots (or really any version after 1.19.2), don't include this field and remove it from any files you do have.

3) The `"fallback"` field is the namespaced path to a **different** template pool (in exactly the same format as you'd use for the "Target Pool" field inside jigsaw blocks themselves). If *nothing* in your template pool can generate— or if it's the last step of structure generation— it will try everything in the referenced fallback pool instead. If nothing in the fallback pool can generate, nothing will be placed at all. ***You cannot nest fallback pools; I've tried.** The `"fallback"` field of your fallback pool seems to be ignored entirely (at least when it's called as a fallback; if you have the same pool be the standard pool for a different jigsaw block, the `"fallback"` field should still work).*

# **Conclusion**

Jigsaw blocks are very powerful. It might take some time to get used to them but with enough practice you'll get an intuition for how to best use template pools and name your jigsaw blocks' "Name" and "Target Name" fields to achieve what you want.

If you have any corrections, suggestions, or questions, feel free to reach out (though I can't promise I'll be quick to respond as I'm not on this website often...).

Good luck with your structure creation!
