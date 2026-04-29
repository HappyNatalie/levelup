# LevelUP! Tome expansion guide.
LevelUP! by HappyNatalie is a datapack I made that I was sure to make very easy to make extensions for! Check out the modrinth page to give it a try.
Check out the modrinth page for more info https://modrinth.com/datapack/levelup!/

## Documentation for skill tomes!

Skill tomes are primarily based on item data. Before I show the data structure I would like to note a few conventions I would like to keep for any custom skill tomes. Your obviously not required to do this but it will make different tome packs look more clean when used together.

## Lore Structure
* SKILLNAME : (make this gray)
* DESCRIPTION (for stat changes do 1 per line, `dark_red` is negative effects, `dark_green` is positive, more complex abilities in `dark_aqua`)
* COST (make this blue)
* Incompatable with : SKILLNAME (make this yellow only show if it has incompatable ids)
* Requires : SKILLNAME (make this yellow only show if it has required ids)

For more of a better description of what I mean look at the lore structure of the example tome because I am bad at describing things!

> **Note:** When describing static move speed buffs, use numbers 10x of what you actually are putting in to make it a more accurate scale with the player's base speed. If using vanilla textures, a **written book** is best.

---

## Function Tags
**DO NOT USE TAGS LIKE `#minecraft:tick` or `#minecraft:load`.** Use the following instead. This will prevent your extension from messing up the main pack or scorefixer. Some features will not work properly if you do not do this.

I strongly recommend using the pack's dependency [score-fixer](https://modrinth.com/datapack/score-fixer) for any scores that are important to keep around, especially long term. Don't worry about lantern load or any problems as long as you use the function tags and triggers from the base datapack, it will work.

* `hn_levelpath_external:extension_load_message` - This function is run while showing the pack's load message. Just put a single tellraw with the contents `tellraw @a "> Extension Name"` so that it shows on the extension list.
* `hn_levelpath_external:on_first_join` - Runs when the player first joins the server.
* `hn_levelpath_external:on_stat_apply` - Runs when the player's stats from the crucible get reapplied.
* `hn_levelpath_external:on_respawn` - Runs when player respawns.
* `hn_levelpath_external:on_entity_hurt_player` - Runs when an entity hurts the player.
* `hn_levelpath_external:on_player_hurt_entity` - Runs when a player hurts an entity from the `registers_with_tome_triggers` tag.
* `hn_levelpath_external:on_player_killed_entity` - Runs when the player kills an entity (runs before `hn_levelpath_external:on_player_hurt_entity`).
* `hn_levelpath_external:on_player_death` - Runs when the player dies.
* `hn_levelpath_external:on_player_tick` - Runs as every player every tick (before `hn_levelpath_external:server_tick`).
* `hn_levelpath_external:server_tick` - Runs after the main pack's tick function.
* `hn_levelpath_external:server_load` - Runs after the main pack's load function.

## Entity Tags
* `hn_levelpath_external:registers_with_tome_triggers` - Mobs in this tag will trigger the `deal_damage_function` and `kill_enemy_function` triggers contained in the tome's item data. By default contains a list of all mobs in the game + players (see [Minecraft Wiki](https://minecraft.wiki/w/Mob)).

## Data Structure
The following is the data structure and what you need to put in the custom data for a skill tome to work. For the different function triggers, if you don't want to use them, simply do not set them and the pack won't attempt to run them. All of the following is contained within "minecraft:custom_data".

* `hn_itemid:"hn_levelpath:skill_tome"`  
  *THIS IS REQUIRED!* This is how the pack understands if something is a tome. Do not touch it.

* `hn_tome_data`: This is where all the functionality is.
    * `cost` - The KP cost to apply the tome. Can be any positive number.
    * `menu_display_name` - The name shown in the crucible menu while having the tome equipped. (Use `\n` for new lines). Only takes strings, no JSON text elements.
    * `menu_description` - The description shown in the crucible menu. Do not put the cost here; the pack automatically shows KP usage. Only takes strings, no JSON text elements.
    * `apply_function` - Runs when the skill tome first applies. Recommend adding a tag to the player and applying stat modifiers here. Whenever datapacks reload the `apply_function` and `remove_function` functions will trigger again. (Skill tomes get reapplied to check for updates)
    * `remove_function` - Runs when the skill tome is unequipped. Remember to clear any tags/modifiers/scores. Whenever datapacks reload the `apply_function` and `remove_function` functions will trigger again. (Skill tomes get reapplied to check for updates)
    * `respawn_function` - Runs when the player respawns. Reapply custom stat modifiers here as death can clear them.
    * `take_damage_function` - Runs when the player takes damage.
    * `deal_damage_function` - Runs when the player deals damage.
    * `kill_enemy_function` - Runs when the player kills something.
    * `tick_function` - Runs every tick.
    * `die_function` - Runs when the player dies.
    * `tome_loot_table` - Loot table containing the tome. This is what will be given back to the player when the tome is uneqipped. ALWAYS SET THIS
    * `id` - This number is used to indentify a skill tome and check for compatability. All skill tomes are compatable with themselves by default. Do not ever set this to 0. Takes any valid value for a scoreboard.
    * `incompatible_ids` - This is an array of tome `id` values that the tome is incompatable with. An `id` value being in this array stops you from eqipping the skill tome while a tome with that `id` value is currently equipped. Never put a tomes id in its own `incompatible_ids` or it just will be impossible to apply.
    * `required_ids` -  This is an array of tome `id` values that the tome requires to be eqipped. An `id` value being in this array stops you from eqipping the skill tome unless a tome with that `id` value is already currently equipped. Uneqipping a skill tome that is required by a different skill tome will also unequip that skill tome. Never put a tomes id in its own `required_ids` or it just will break.
    * `ignore_refund` If set to true this will stop the tome from being given back to the player when uneqipped. Always set `tome_loot_table` regardless of this in order to prevent loss of tome data when players update.
  
## Display Overriding
By default the actionbar has the LevelUP! HUD contained in it but if you want to temporarily override it without potental flickering set the `contenets` element in the `hn_levelpath:display_override` storage to the JSON text element you want to override the display. You will have to do this every tick that you want the display to be overwritten. This has to be a structured JSON text element and cannot be a string so `{"text":"Hello World!"}` would work but something like `"Hello World!"` would not. 
 
## Taken ID Ranges
This is just a list of known ranges of IDs that mods have already used please feel free to dm me to add something on here.
 - -10 - -1 (For testing tomes)
 - 1 - 100 (LevelUP! Tome Expansion 1-2)
 - 101 - 200 (ItsYourImp's Tomes)
  
## Future Planned Features
- add `command_pripority` field to allow people to give certain tomes priority over others as it currently functions based on the slot the tome is in
- Anything you ask for! (if im smart enough to code it) Reach out to @happynatalie on discord if you have a good idea for a feature. I did all of my own ideas lol.
