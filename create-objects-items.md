# Object and Item Ids

Adding new objects and items into *Fields of Mistria* is extremely easy post `v0.16.0`. This document serves as a guide for modders and hackers.

First, objects are things which appear on the *Grid*, such as furniture, crops, trees, coops, barns, and grass.

Items are things which appear in inventories, floating in the world as "loose" items, or sold in shops. They do not have categories and all items decide their interaction based on `item_use`, which is inferred on startup based on the properties of that item.

## Adding a new Object

To add a new object, you should find and edit `assets/fiddle/object_prototypes`. In the past, there was an `extras` category, but this has been removed as native FIDDLE editing is now possible.

Let's make a new `furniture` object. To do so, open `assets/fiddle/object_prototypes/furniture.toml`. Take a moment to enjoy how large this file is.

To add a new object, you need to simply add a new entry anywhere in the file (though preferably at the bottom):

```toml
[wheedle_statue]
    # data goes here:
```

In the past, modders would explicitly list the `category`, but it is inferred from the file. In this case, because we've edited the `furniture` file, the object will be a `Furniture` object. A category defines the *capabilities* and *interactions* of a given object. Furniture, however, has a substantial amount of customized behavior defined per furniture item. You can see all the types of `object_prototypes` by checking each file inside that directory.

In the above example, `wheedle_statue` is the *name* of the object. All names should be a [snake_case](https://en.wikipedia.org/wiki/Snake_case) string. This name will be used in serialization. The object will also be assigned a numerical id at compile time, since it is a [Magic Enum](./asset-properties/magic-enums.md). These names **can** be the same names as Items, as they are understood to be in a different namespace. See the example of a crop and seed below.

To fill in the data, you should look at the other examples within each file. We recommend copy-pasting to start.

### Full Object Example

Here's a full example of some furniture:

```toml
[wheedle_statue]
    size = [3, 2]
    collision_grid = "2"
    
    [wheedle_statue.south]
        sprite = "spr_decor_dragon_statue_v1_spring_south"
        offset = [12, 8]

    [wheedle_statue.north]
        sprite = "spr_decor_dragon_statue_v1_spring_north"
        offset = [12, 8]
```

## Adding a new Item

To add a new item, similar to Objects, one must edit a file under `fiddle/items`. Modders should edit `fiddle/items/other`. Unlike in Objects, there can be many files within this folder `other` (or any other `items` folder). At one point, there was only one items folder, and it became actually too big, to now they're split into a folder.

Start with an empty file for a new item under `fiddle/items/other`, named something like `my_cool_mod.toml`.

To add a new Item, you need to add a new table entry to the file, such as:

```toml
[golden_plant]
    # data goes here:
```

In the above example, `golden_plant` is the *name* of the item. All names should be a [snake_case](https://en.wikipedia.org/wiki/Snake_case) string. This name will be used in serialization. The item will also be assigned a numerical id at compile time, since it is a [Magic Enum](./asset-properties/magic-enums.md). These names **can** be the same names as Objects, as they are understood to be in a different namespace. See the example of a crop and seed below.

To fill in the data, you should look at the other examples within each file. We recommend copy-pasting to start.

### Full Item Example

Here's a full example of an item which can be placed to make furniture:

```toml
[wheedle_statue_item] # note: `_item` is unnecessary, but here to not confuse
    icon_sprite = "spr_ui_item_dragon_statue_replica_v1"
    name = "Wheedle Statue"
    description = "I don't remember buying this..."
    object = "wheedle_statue" # this would refer to an object of the name `wheedle_statue`.
    tags = [ "furniture", "mistrian_history_set", "misc_decor" ]
   	value = { bin = "self.recipe*1.1" }
   	recipe = [
        { count = 100, item = "ore_stone" },
        { count = 2, item = "monster_core" },
        { count = 2, item = "monster_horn" },
        { hours = 0, minutes = 30 },
   	]
   	crafting_level_requirement = 20
```

### Localization Strings

In the past, modders could either supply simple non-localized names, or they could work with our localization system directly. Now, you can simply write the names of anything in *English*. Since Fields of Mistria is developed in English, all localization strings in FIDDLE files are assumed to be in English. In the future, we will provide guidance for editing a translation of the game.

## A Crop Example

This section contains a full example of adding a crop object, a crop seed item, and a crop harvest-able item. We simply re-use sprites which already exist in the game, but if modders add animations to the game, those could be used here instead by file name:

```toml
# in the file `assets/fiddle/object_prototypes/crop.toml`:
[my_crop]
    day_to_stage = [0, 1, 1, 1, 2]
    sprites = [
    	"spr_forage_basil_seed",
    	"spr_forage_basil_stage3",
    	"spr_forage_basil_stage4",
    ]
    seasons = "spring"
    regrow_days = 4
    currency = 3

# in the file `assets/fiddle/items/other/my_cool_mod.toml`:
[my_seed]
    name = "My Seed"
    description = "My Fruit seed. Hello world!"
    crop_object = "my_crop"
    mini_sprite = "spr_ui_tool_tip_icon_basil"
    icon_sprite = "spr_ui_item_bagseed_basil_icon"
    sow_sprite = "spr_ui_item_bagseed_basil_mini"
    value = { bin = 15 }
    tags = ["seed"]

[my_fruit]
    name = "My Fruit"
    description = "This is the fruit which is harvested from the crop my_crop!"
    icon_sprite = "spr_ui_item_basil"
    restore = "rare_forage_restore"
    tags = ["forageable"]
    value = { bin = 3000, store = 1 }
```

The above creates a crop called `my_crop`, which is made by an item `my_seed`, which harvests into a item, `my_fruit`. To get the `my_seed` in-game, one could edit their save file with [vaultc](https://github.com/NPC-Studio/vaultc),
edit some other part of the game to make that item accessible, or add it using [bugger](./bugger.md). For furniture, using the appropriate tags will allow the furniture to appear in the crafting menu.

## Missing Objects and Items

If an id is present in a save file, but no longer declared, we do two different strategies. For objects, we ignore them entirely. For items, we replace them with `unknown_item`. If the user saves again, this `unknown_item` is permanently saved over their original ID, and they'll need to find an old save to get those lost ids again.

In both cases, an error is emitted in the logs. See [CLI](./CLI.md) for more information on seeing those.

Good luck, happy hacking!
