# LevelUP! Tome expansion guide.
LevelUP! by HappyNatalie is a datapack I made that I was sure to make very easy to make extensions for! Check out the modrinth page to give it a try.
Check out the modrinth page for more info https://modrinth.com/datapack/levelup!/

## Documentation for custom skill tomes!

Skill tomes are primarily based on item data. Before I say the data I would like to note a few conventions I would like to keep for any custom skill tomes. Your obviously not required to do this but it will make different tome packs look more clean when used together.

## Lore Structure
* SKILLNAME : (make this gray)
* DESCRIPTION (for stat changes do 1 per line, `dark_red` is negative effects, `dark_green` is positive, more complex abilities in `dark_aqua`)
* COST (make this blue)

For more a better description of what I mean look at the lore structure of the example tome because I am bad at describing things!

> **Note:** When describing static move speed buffs, use numbers 10x of what you actually are putting in to make it a more accurate scale with the player's base speed. If using vanilla textures, a **written book** is best.

---

## Function Tags
**DO NOT USE TAGS LIKE `#minecraft:tick` or `#minecraft:load`.** Use the following instead. This will prevent the pack from messing up the main pack.

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

---

## Entity Tags
* `hn_levelpath_external:registers_with_tome_triggers` - Mobs in this tag will trigger the `deal_damage_function` and `kill_enemy_function` triggers contained in the tome's item data. By default contains a list of all mobs in the game + players (see [Minecraft Wiki](https://minecraft.wiki/w/Mob)).

---

## Data Structure
The following is the data structure and what you need to put in the custom data for a skill tome to work. For the different function triggers, if you don't want to use them, simply do not set them and the pack won't attempt to run them. All of the following is contained within "minecraft:custom_data".

* `hn_itemid:"hn_levelpath:skill_tome"`  
  *THIS IS REQUIRED!* This is how the pack understands if something is a tome. Do not touch it.

* `hn_tome_data`: This is where all the functionality is.
    * `cost` - The KP cost to apply the tome. Can be any positive number.
    * `menu_display_name` - The name shown in the crucible menu while having the tome equipped. (Use `\n` for new lines). Only takes strings, no JSON text elements.
    * `menu_description` - The description shown in the crucible menu. Do not put the cost here; the pack automatically shows KP usage. Only takes strings, no JSON text elements.
    * `apply_function` - Runs when the skill tome first applies. Recommend adding a tag to the player and applying stat modifiers here.
    * `remove_function` - Runs when the skill tome is unequipped. Remember to clear any tags/modifiers/scores.
    * `respawn_function` - Runs when the player respawns. Reapply custom stat modifiers here as death can clear them.
    * `take_damage_function` - Runs when the player takes damage.
    * `deal_damage_function` - Runs when the player deals damage.
    * `kill_enemy_function` - Runs when the player kills something.
    * `tick_function` - Runs every tick.
    * `die_function` - N/A.
    * `tome_loot_table` - This loot table is given to the player on tome removal via the crucible to return tomes to them.
    * `id` - This number decides skill tome compatibility. Do not ever set this to 0. Takes any valid value for a scoreboard.
