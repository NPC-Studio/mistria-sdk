# vaultc
A tool for unpacking and repacking Fields of Mistria `.sav` files.

Run:

```pwsh
& path/to/vaultc.exe --help; 
```

or on linux:

```sh
path/to/vaultc --help;
```

You can also run:

```sh
# on powershell
& path/to/vaultc.exe pack --help;
& path/to/vaultc.exe unpack --help;

# on sh
path/to/vaultc pack --help;
path/to/vaultc unpack --help;
```

The following is an overview of the commands available for `vaultc`:
###### **Subcommands:**
* `pack` — Packs a directory of JSON + Farm Buffer data into single .sav file, which Fields of Mistria has been using since 0.11.5
* `unpack` — Unpacks a .sav file into a directory of JSON + Farm Buffer data. This resembles the only save files
* `edit` — Edits a given vault path with a given key-value pair
* `view` — View a given value in a vault with the path given

## `vaultc pack`

Packs a directory of JSON + Farm Buffer data into single .sav file, which Fields of Mistria has been using since `v0.11.5`.

**Usage:** `vaultc pack <INPUT_FILEPATH> <OUTPUT_FILEPATH>`

###### **Arguments:**

* `<INPUT_FILEPATH>` — The input path for a save folder, such as `FieldsOfMistria/saves/game-0/autosave`. This should be a directory
* `<OUTPUT_FILEPATH>` — The path to output the sav buffer to, such as `game-1234-autosave.sav`

## `vaultc unpack`
Unpacks a .sav file into a directory of JSON + Farm Buffer data. This resembles the old save files.
**Usage:** `vaultc unpack <INPUT_FILEPATH> <OUTPUT_FILEPATH>`

###### **Arguments:**

* `<INPUT_FILEPATH>` — The input path for a sav buffer, such as `FieldsOfMistria/saves/game-0-autosave.sav`
* `<OUTPUT_FILEPATH>` — The output path to put the unpacked files, such as `"./game-1234-5678.sav"`

## `vaultc edit`

Edits a given vault path with a given key-value pair.
This is given as a convenience to avoid having to pack and unpack saves for simple edits.
**Usage:** `vaultc edit <SAV_FILEPATH> <KEY> <VALUE>`
###### **Arguments:**
* `<SAV_FILEPATH>` — The input path for a sav buffer, such as `FieldsOfMistria/saves/game-0-autosave.sav`
* `<KEY>` — The Key can be a pseudo-filename such as "player", or it can be a full path to an object, such as "player/spells_learned". You can also address fields within an object such as "player/misc/foo". There is currently no way to address a single location within an array, however.
   If the path doesn't exist, it will be created on demand, but if the file_name does not exist (the first component of the path), an error will be returned. For example, doing `player/foo` will create a new field called `foo` on the player. However, doing `foo/player` will report an error (generally) since there is no top-level file called `foo`. (If you unpack the save and make a file called `foo`, then, of course, that would work, or if Fields of Mistria decides to start making a "foo" file.)
* `<VALUE>` — This is a *JSON* parsed string. You can input, for example, an empty object with `{}`, or an array of numbers with `[0, 0]`. We will naively parse this as best as we can and write this to the `key` above

## `vaultc view`

View a given value in a vault with the path given.
This is given as a convenience to avoid having to unpack saves for simple lookups.

**Usage:** `vaultc view <SAV_FILEPATH> <KEY>`

###### **Arguments:**

* `<SAV_FILEPATH>` — The input path for a sav buffer, such as `FieldsOfMistria/saves/game-0-autosave.sav`
* `<KEY>` — The Key can be a pseudo-filename such as "player", or it can be a full path to an object, such as "player/spells_learned". You can also address fields within an object such as "player/misc/foo". There is currently no way to address a single location within an array, however.
   If the path does not exist, or any component in the path does not exist, then this will return an error. As a result, `null` and not existing keys are clearly distinguished.
