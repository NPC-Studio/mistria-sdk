The name of the engine which Fields of Mistria uses is `maybe`. Additionally, there is a utility program which you can find in the `tools` folder of this repository called `maybe`, for interacting with the engine-side guts of Fields of Mistria.

Run:

```sh
path/to/maybe --help; 
```

The following is an overview of the commands available for `maybe`:

**Usage:** `maybe <COMMAND>`

###### **Subcommands:**

* `new` — Creates a new asset from simple binary files
* `atlas` — Subcommands for creating, editing, and viewing atlas data
* `asset-id` — Prints a new random AssetId

## `maybe new`

Creates a new asset from simple binary files

**Usage:** `maybe new [OPTIONS] <PATH>`
**Command Aliases:** `add`, `a`, `n`
###### **Arguments:**
* `<PATH>` — The path to a binary file to create meta files for. If given a folder, we'll make meta files for everything in the folder (rewriting any that already exist)
###### **Options:**
* `-f`, `--force` — When given a directory, we will overwrite all of the meta files within the directly *only* if `force` is true

## `maybe atlas`

Subcommands for creating, editing, and viewing atlas data

**Usage:** `maybe atlas <COMMAND>`
###### **Subcommands:**

* `create` — Build a new `TextureAtlas`, either with specific paths or with an atlas tag
* `list` — Lists information on animations with or without an explicit atlas tag
* `set` — Set a new `atlas` on animations



## `maybe atlas create`

Build a new TextureAtlas, either with specific paths or with an atlas tag

**Usage:** `maybe atlas create [OPTIONS] <ATLAS_TO_MAKE>`

###### **Arguments:**

* `<ATLAS_TO_MAKE>` — The path to place the new meta file for the atlas we're going to create. The file extension doesn't matter.

###### **Options:**

* `--opaque-deduplicator` — We try to deduplicate images, and this is done pixel-by-pixel. If this flag is passed, we instead consider all non-clear pixels to be identical, and all clear pixels to be identical. For certain masks where artists use colors for authorship purposes, but whose color value is only as an opaque in-game, passing this can reduce the number of images in-game
* `-p`, `--padding <PADDING>` — How much padding between entries. Must be divisible by 2. 0 is a valid input

  Default value: `2`
* `-a`, `--animation-paths <ANIMATION_PATHS>` — The paths to the Animation meta files to add to the Texture Atlas. These can end in `.meta`, `.png`, or they can be a folder. We will recurse through folders finding all Animation meta files.

   Mutually exclusive with `--tag`. Only one of the two can be provided. If neither is provided, then we'll find a tag with the name of `atlas_to_make`.
* `-t`, `--tag <TAG>` — Build the atlas from every animation matching this tag instead of explicit paths.

   Mutually exclusive with `--animation-paths`. Only one of the two can be provided. If neither is provided, then we'll find a tag with the name of `atlas_to_make`.

## `maybe atlas list`

Lists information on animations with or without an explicit atlas tag

**Usage:** `maybe atlas list [OPTIONS]`
###### **Options:**

* `--filter <FILTER>` — Filters our list down to just those whose tag is the name as this filter. This can be useful when paired with `paths`
* `--untagged` — When set, we will show those animations which are untagged
* `--paths` — List out the names of all files which match the given query. This can end up listing quite a lot of names
* `--machine` — When set, we'll output our list with newlines separating each atlas group. Incompatible with `untagged` and `paths`

## `maybe atlas set`

Set a new `atlas` on animations

**Usage:** `maybe atlas set [OPTIONS] <--tag <TAG>|--untag>`
###### **Options:**
* `--tag <TAG>` — The new tag to set on animations.

   Mutually incompatible with `--untag`. Exactly one of the two must be provided.
* `--untag` — Untag the asset.

   Mutually exclusive with `--tag`. Exactly one of the two must be provided.
* `--paths <PATHS>` — The paths to the Animation meta files which we'll set the tag on. You can also provide a folder, which we'll recursively edit
* `-v`, `--verbose` — When set, we will show exactly which paths were set

## `maybe asset-id`

Prints a new random `AssetId`

**Usage:** `maybe asset-id`

