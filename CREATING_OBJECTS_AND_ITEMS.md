# Object and Item Ids

***This document reflects version v0.13.3 of Fields of Mistria. It is currently maintained per release.***

***Fields of Mistria itself does not support or maintain mods. We are releasing this document to help those mods which already exist, but we will not provide customer support on any mods or for mod creation.***

Adding new objects and items into *Fields of Mistria* is relatively easy post `v0.13.3`. This document serves as a guide for modders
and hackers.

First, objects are things which appear on the *Grid*, such as furniture, crops, trees, coops, barns, and grass.

Items are things which appear in inventories, floating in the world as "loose" items, or sold in shops. They do not have
categories and all items decide their interaction based on `item_use`, which is inferred on startup based on the properties of that item.

## Adding a new Object

To add a new object, one must edit the `__fiddle__.json` new `extras/objects` key (and the `extras` and `objects` related keys) to fill in the array.

Here is what it will look like by default:

```json
{
    "extras/objects": [],
}
```

To add a new object, you need to add to the array a new JSON object with the following keys:

```json
{
    "extras/objects": [
        {
            "name": "wheedle_statue",
            "category": "furniture",
            "data": {
                // --snip
            }
        }
    ],
}
```

The three keys are:

- `name`: this should be a **snake_case** (more [here](https://en.wikipedia.org/wiki/Snake_case)) string for the name of the object. This name will be used in serialization. The object will also be assigned an object id based on its order in the list. Serialized data, however, will always use a string, so changing the order of this list between save files should not cause loading problems. These names **can** be the same names as Items, as they are understood to be in a different namespace. See the example of a crop and seed below.
- `category`: Objects must be of a given *category*. The categories are:

  ```json
  [
      "breakable",
      "building",
      "bush",
      "crop",
      "dig_site",
      "furniture",
      "grass",
      "rock",
      "stump",
      "tree",
  ]
  ```

  A category defines the *capabilities* and *interactions* of a given object. Furniture, however, has a substantial amount of customized behavior defined
  per furniture item.
- `data`: This is the data an object should have to define it. You can find examples of other objects and their data in `__fiddle__.json` -- we highly recommend using copy and paste to start.

### Full Object Example

Here's a full example of some furniture:

```json
{
    "extras/objects": [
        {
            "name": "wheedle_statue",
            "category": "furniture",
            "data": {
                "size": [
                    3,
                    2
                ],
                "collision_grid": "2",
                "south": {
                    "sprite": "spr_decor_dragon_statue_v1_spring_south",
                    "offset": [
                        12,
                        8
                    ]
                },
                "north": {
                    "sprite": "spr_decor_dragon_statue_v1_spring_north",
                    "offset": [
                        12,
                        8
                    ]
                }
            }
        }
    ],
}
```

## Adding a new Item

To add a new item, similar to Objects, one must edit the `__fiddle__.json` new `extras/items` key (and the `extras` and `items` related keys) to fill in the array.

Here is what it will look like by default:

```json
{
    "extras/items": [],
}
```

To add a new Item, you need to add to the array a new JSON object with the following keys:

```json
{
    "extras/items": [
        {
            "name": "golden_plant",
            "data": {
                // --snip
            }
        }
    ],
}
```

The two keys are:

- `name`: this should be a **snake_case** (more [here](https://en.wikipedia.org/wiki/Snake_case)) string for the name of the item. This name will be used in serialization. The item will also be assigned an item id based on its order in the list. Serialized data, however, will always use a string, so changing the order of this list between save files should not cause loading problems. These names **can** be the same names as Objects, as they are understood to be in a different namespace. See the example of a crop and seed below.
- `data`: This is the data an item should have to define it. You can find examples of other items and their data in `__fiddle__.json` -- we highly recommend using copy and paste to start.

### Full Item Example

Here's a full example of an item which can be placed to make furniture:

```json
{
    "extras/items": [
        {
            "name": "wheedle_statue",
            "data": {
                "icon_sprite": "spr_ui_item_dragon_statue_replica_v1",
                "name": "Wheedle Statue",
                "description": "items/furniture/mistrian_history_set/dragon_statue_replica_v1/description",
                "object": "dragon_statue_replica_v1",
                "tags": [
                    "furniture",
                    "mistrian_history_set",
                    "misc_decor"
                ],
                "recipe_key": "dragon_statue_replica",
                "crafting_level_requirement": 20,
                "recipe": [
                    {
                        "count": 100,
                        "item": "ore_stone"
                    },
                    {
                        "count": 2,
                        "item": "monster_core"
                    },
                    {
                        "count": 2,
                        "item": "monster_horn"
                    },
                    {
                        "hours": 0,
                        "minutes": 30
                    }
                ],
                "value": {
                    "bin": "self.recipe*1.1"
                }
            }
        }
    ],
}
```

### Localization Strings

In the `__fiddle__.json` file, all English language name and descriptions are actually keys within a file called `localization.json`. If you set the value of `"name"` or `"description"` in `item/data` to one of these `localization.json` keys, we will use that. If instead you just write an English language name and description, we will use that directly instead.

In the above example, you can see that `"data/name"` is a simple English language name, but `"data/description"` is instead a key which can be found in `localization.json`. You can mix and match these.

To be totally clear, however, the top level `"name"` attribute, which in the above example is set to `wheedle_statue` (ie, the ones that should be `snake_case`) are not human readable names and should be kept in snake case. These names have nothing to do with localization at all.

## A Crop Example

This section contains a full example of adding a crop object, a crop seed item, and a crop harvestable item. We simply re-use sprites which already exist
in the game, but if modders have some method of describing other sprites, those could be used here too.

```json
{
    "extras/objects": [
        {
            "name": "my_fruit",
            "category": "crop",
            "data": {
                "harvest": "my_fruit",
                "day_to_stage": [
                    0,
                    1,
                    1,
                    1,
                    2
                ],
                "sprites": [
                    "spr_forage_basil_seed",
                    "spr_forage_basil_stage3",
                    "spr_forage_basil_stage4"
                ],
                "seasons": "spring",
                "regrow_days": 4,
                "currency": 3
            }
        }
    ],
    "extras/items": [
        {
            "name": "my_seed",
            "data": {
                "name": "My Seed",
                "description": "My Fruit seed. Wait, where'd you get this?",
                "crop_object": "my_fruit",
                "mini_sprite": "spr_ui_tool_tip_icon_basil",
                "icon_sprite": "spr_ui_item_bagseed_basil_icon",
                "sow_sprite": "spr_ui_item_bagseed_basil_mini",
                "tags": [
                    "seed"
                ],
                "value": {
                    "bin": 15
                }
            }
        },
        {
            "name": "my_fruit",
            "data": {
                "description": "items/other/crops_and_forage/basil/description",
                "name": "items/other/crops_and_forage/basil/name",
                "icon_sprite": "spr_ui_item_basil",
                "restore": "rare_forage_restore",
                "tags": [
                    "forageable"
                ],
                "value": {
                    "bin": 3000
                }
            }
        }
    ]
}
```

The above creates a crop, which is made by an item `my_seed`, which harvests into a item, `my_fruit`.
To get the `my_seed` in-game, one could edit their save file with [vaultc](https://github.com/NPC-Studio/vaultc)
or edit some other part of the game to make that item accessible. For furniture, using the appropriate tags will allow
the furniture to appear in the crafting menu.

Good luck, happy hacking!
