# LevelUP! Tome expansion guide. (LevelUP! v1.4+)
LevelUP! by HappyNatalie is a datapack I made that I was sure to make very easy to make extensions for! Check out the modrinth page to give it a try.
Check out the modrinth page for more info https://modrinth.com/datapack/levelup!/

## Documentation for skill tomes!

Skill tomes are primarily based on item data. Before I show the data structure I would like to note a few conventions I would like to keep for any custom skill tomes. Your obviously not required to do this but it will make different tome packs look more clean when used together.

---

## Lore Structure
* SKILLNAME : (make this gray)
* DESCRIPTION (for stat changes do 1 per line, `dark_red` is negative effects, `dark_green` is positive, more complex abilities in `dark_aqua`)
* COST (make this blue)
* Incompatable with : SKILLNAME (make this `red` only show if it has incompatable ids)
* Requires : SKILLNAME (make this `green` only show if it has required ids)

For more of a better description of what I mean look at the lore structure of the example tome because I am bad at describing things!

> **Note:** When describing static move speed buffs, use numbers 10x of what you actually are putting in to make it a more accurate scale with the player's base speed. If using vanilla textures, a **written book** is best.

---

## Function Tags
**DO NOT USE TAGS LIKE `#minecraft:tick` or `#minecraft:load`.** Use the following instead. This will prevent your extension from messing up the main pack or scorefixer. Some features will not work properly if you do not do this.

I strongly recommend using the pack's dependency [score-fixer](https://modrinth.com/datapack/score-fixer) for any scores that are important to keep around, especially long term. Don't worry about lantern load or any problems as long as you use the function tags and triggers from the base datapack, it will work.

* `hn_levelpath_external:extension_load_message` - This function is run while showing the pack's load message. Just put a single tellraw with the contents `tellraw @a "> Extension Name"` so that it shows on the extension list.
* `hn_levelpath_external:on_first_join` - Runs when the player first joins the server.
* `hn_levelpath_external:on_stat_apply` - Runs when the player's stats from the crucible get reapplied.
* `hn_levelpath_external:on_update` - Runs when the players tomes are updated after a server restart or the `/reload` command was run.
* `hn_levelpath_external:on_loot_drop` - Runs when the player is dropped a tome via the `hn_levelpath_dev:loot_drop` function.
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
    * `apply_function` - Runs when the skill tome first applies. Recommend adding a tag to the player and applying stat modifiers here. Whenever datapacks reload the `apply_function` and `remove_function` functions will trigger again. (Skill tomes get reapplied to check for updates)
    * `remove_function` - Runs when the skill tome is unequipped. Remember to clear any tags/modifiers/scores. Whenever datapacks reload the `apply_function` and `remove_function` functions will trigger again. (Skill tomes get reapplied to check for updates)
    * `respawn_function` - Runs when the player respawns. Reapply custom stat modifiers here as death can clear them.
    * `take_damage_function` - Runs when the player takes damage.
    * `deal_damage_function` - Runs when the player deals damage.
    * `kill_enemy_function` - Runs when the player kills something.
    * `tick_function` - Runs every tick.
    * `update_function` This function is run when your tomes are refreshed via a server restart or /reload command getting run. If you leave this unset the pack will run `remove_function` and then `apply_function` instead.
    * `die_function` - Runs when the player dies.
    * `tome_loot_table` - Loot table containing the tome. This is what will be given back to the player when the tome is uneqipped. ALWAYS SET THIS
    * `id` - This number is used to indentify a skill tome and check for compatability. All skill tomes are compatable with themselves by default. Do not ever set this to 0. Takes any valid value for a scoreboard.
    * `incompatible_ids` - This is an array of tome `id` values that the tome is incompatable with. An `id` value being in this array stops you from eqipping the skill tome while a tome with that `id` value is currently equipped. Never put a tomes id in its own `incompatible_ids` or it just will be impossible to apply.
    * `required_ids` -  This is an array of tome `id` values that the tome requires to be eqipped. An `id` value being in this array stops you from eqipping the skill tome unless a tome with that `id` value is already currently equipped. Uneqipping a skill tome that is required by a different skill tome will also unequip that skill tome. Never put a tomes id in its own `required_ids` or it just will break.
    * `ignore_refund` If set to true this will stop the tome from being given back to the player when uneqipped. Always set `tome_loot_table` regardless of this in order to prevent loss of tome data when players update.
    * `ignore_refund_on_update_fail` If set to true this will stop the tome from being given back to the player if an update to the game makes it so they can no longer equip their set of tomes.
    * `custom_data` The storage location for data used by the `store_data` and `fetch_data` functions. Note that this field is reset whenever the tome is removed so not setting your `update_function` will result in the tome data getting wiped when the server restarts regardless of what your `override_custom_data_on_update` is set to.
    * `override_custom_data_on_update` Defines if your `custom_data` will be replaced when the players tomes update. If set to true whenever the players tomes are updated the tomes `custom_data` will be set to the default as defined in the loot table.
    * `tome_loot_display` If using `hn_levelpath_dev:loot_drop` functions this is the name that will display in chat when the drop happens. Only takes strings not JSON text elements.
    * `tome_loot_display_rarity` If using `hn_levelpath_dev:loot_drop` functions this will determine the type of drop message the tome displays. Look to valid tome rarities section to see all of the avalable rarities.
    * `tome_loot_display_custom_drop_message` If using the `custom` value for `tome_loot_display_rarity` this will display where the "<RARITY> Drop >" part of the loot drop notification would appear.

---

## Functions
As of update 1.4 there are now a series of utility functions for you to use.
- `hn_levelpath_dev:fetch_data {tome_id:<INT>,destination:<STRING>}` Running this function will copy the `custom_data` element from the tome with the `id` specified in the `tome_id` field to a storage specified in the `destination` field. (Must be run as a player)
- `hn_levelpath_dev:store_data {tome_id:<INT>,source:<STRING>}` Running this function will copy the `custom_data` element from the storage specified in the `source` field to the tome with the `id` specified in the `tome_id` field. (Must be run as a player)
- `hn_levelpath_dev:fetch_element {tome_id:<INT>,element:<STRING>}` Function will return the result of running a data get command on `custom_data.<element>` for more conveniently fetching single data points when you do not need to modify `custom_data`.
- `hn_levelpath_dev:loot_drop {tome_loot_table:<STRING>}` Will drop the tome specified in the `tome_loot_table` to the player with a different drop message depending on the rarity. Name and rarity are displayed automatically by scanning the `tome_loot_display` and `tome_loot_display_rarity` element of the tome. For more details check the using the drop system section below.

---

## Display Overriding
By default the actionbar has the LevelUP! HUD contained in it but if you want to temporarily override it without potental flickering set the `contenets` element in the `hn_levelpath:display_override` storage to the JSON text element you want to override the display. You will have to do this every tick that you want the display to be overwritten. This has to be a structured JSON text element and cannot be a string so `{"text":"Hello World!"}` would work but something like `"Hello World!"` would not. 

---

## Using the drop system
While making your tomes avalable through ways other than crafting I recomend using a combanation of advancement triggers and LevelUP!s in build drop system command to avoid conflicts with other packs or mods that modify vanilla loot tables. Also it generally looks nice for most addons to have tomes drop in a similar way. The following is a simple example for how to make a tome have a 10% chance to drop from a zombie.<br>
<br>`loot_advancement.json`
```
{
  "criteria": {
    "killed_zombie_chance": {
      "trigger": "minecraft:player_killed_entity",
      "conditions": {
        "entity": [
          {
            "condition": "minecraft:entity_properties",
            "entity": "this",
            "predicate": {
              "type": "minecraft:zombie"
            }
          },
          {
            "condition": "minecraft:random_chance",
            "chance": 0.1
          }
        ]
      }
    }
  },
  "requirements": [
    [
      "killed_zombie_chance"
    ]
  ],
  "rewards": {
    "function": "my_extension:loot_function"
  }
}
```
<br>`loot_function.mcfunction`
```
advancement revoke @s only my_extension:loot_advancement
function hn_levelpath_dev:loot_drop {tome_loot_table:"my_extension:tome_loot_table"}
```

### Valid Rarities
The following are all of the valid rarities that a tome can have and what their drop message looks like. This is for use with the `hn_levelpath_dev:loot_drop` command and is not related to the rarity component. Your defined rarity goes in the `tome_loot_display_rarity` element as specified earlier. There aren't many notes on the `common`, `uncommon`, `rare`, `legendary`, and `mythical` rarities because they are meant to based on how many KP a tome costs hence the numbers listed next to each of them.

 * `common` <10KP<img width="520" height="30" alt="COMMON" src="https://github.com/user-attachments/assets/a72dfdc2-9bca-498c-b9f7-7ab6c430a5ac" />
 * `uncommon`10-19KP<img width="560" height="30" alt="UNCOMMON" src="https://github.com/user-attachments/assets/493477d5-410f-4d76-a1ef-4be759cffd87" />
 * `rare`20-39KP<img width="478" height="33" alt="RARE" src="https://github.com/user-attachments/assets/d644cb02-6950-4a33-8d21-ea4c5761bc9f" />
 * `legendary`40-59<img width="578" height="30" alt="LEGENDARY" src="https://github.com/user-attachments/assets/2d3f4188-510f-404d-905d-8dbba2f00236" />
 * `mythical`>=60<img width="525" height="35" alt="MYTHIC" src="https://github.com/user-attachments/assets/56d59db2-b214-41e1-844d-f1c8d1c1ad72" />
 * `tome_drop` Just a generic message that could be used for any tome really.<img width="474" height="27" alt="TOMEDROP" src="https://github.com/user-attachments/assets/3526877d-e991-4b52-b48f-21df6eace77a" />
 * `achivement` Meant to be used for one time rewards that the player can get by doing achivements or completing collections.<img width="580" height="28" alt="ADVANCEMENT" src="https://github.com/user-attachments/assets/5991f1ad-5427-46c5-b82c-7a1d4809734f" />
 * `special` Meant to be used for items that are gotten by doing spesific things for instance killing a drowned with a trident.<img width="520" height="29" alt="SPECIAL" src="https://github.com/user-attachments/assets/176a32b7-1069-4801-8881-d1b81850be79" />
 * `custom` For displaying any drop message that is not on this list. Uses the json element from `tome_loot_display_custom_drop_message` as the starting part of the drop message. Below is an example with `"[{\"text\":\"Custom Drop >\"}]"`.<img width="465" height="29" alt="CUSTOM" src="https://github.com/user-attachments/assets/34d46879-351f-4abe-9568-ef24190b7fe2" />


---

## Taken ID Ranges
This is just a list of known ranges of IDs that mods have already used please feel free to dm me to add something on here.
 - -10 - -1 (For testing tomes)
 - 1 - 100 (LevelUP! Tome Expansion 1-2)
 - 101 - 200 (ItsYourImp's Tomes)

---
  
## Future Planned Features
- add `command_pripority` field to allow people to give certain tomes priority over others as it currently functions based on the slot the tome is in
- Anything you ask for! (if im smart enough to code it) Reach out to @happynatalie on discord if you have a good idea for a feature. I did all of my own ideas lol.

