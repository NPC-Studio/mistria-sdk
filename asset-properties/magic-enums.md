# MagicEnums
Some enums in the game have "magic" done to them by the runtime. You can see the current magic list in `assets/etc/magic_enums.meta.toml`.

There are two kinds of magic here:

## Enum to String

Any GML Enum included in the `enum_to_string` array will get generated functions (a magic std function) such as the following, where `enum` is the snake_case name of the enum:

- `try_enum_snake_case_to_string`, which returns an `Option<String>`
- `enum_snake_case_to_string`, which returns a String or throws (and just calls the `try_` variant)
- `try_string_to_enum_snake_case`, which returns an `Option<Enum>`
- `string_to_enum_snake_case`, which returns an Enum or throws (and just calls the `try_` variant)

In all cases above, we both take and return the snake_case version of each enum member name.

So for example, `try_string_to_object_id("worn_table")` will return the number assigned to `ObjectId.WornTable`.

Additionally, the `PascalCase` name of the enum is added to the global table for format items, so you can do:

```c
var my_output = format("This is a {ObjectId}", ObjectId.WornTable);
assert_eq(my_output, "This is a worn_table");

assert_eq(string_to_object_id("worn_table"), ObjectId.WornTable);
```

Enums are expected to be `PascalCase`, and we will convert function names to be snake_case
based on https://docs.rs/heck/latest/heck/ conversions.

## Generated Enums
This is our fanciest trick and is the underlying machinery which allows for simple `TOML` entries to [create new objects and item ids](create-objects-items.md).

Any file path listed within `enums_to_generate` will generate an enum based on the table names within the file or folder it refers to. If it refers to a folder, we will recursively go through each file and sub-folder within that folder to build the enum.

The enum members will be generated alphabetically, and they'll have a `LEN` attribute at the end. Any header file named `default` will be skipped and will not be added to the enum members list.

Let's go through an example:

```toml
enums_to_generate = [
    { name = "LocationId", path = "locations.toml" },
]
```

This entry will generate an enum named `LocationId` based on the path `locations.toml`. All paths are relative to the `fiddle` folder, so this total path is `assets/fiddle/locations.toml`.

Here is a sample of that file:

```toml
# This contains the default properties for locations. Any fields you omit from a location below
# will be filled in with these!
[default]
	# The name of this map shown in the UI. Only needed for outdoor maps.
	name = "MISSING NAME"
	
	# -- snip --

[aldaria]

[farm]
	# -- snip --

[town]
	name = "Mistria"
	# -- snip --

[bathhouse]
	name = "Bathhouse"
	# -- snip --

[bathhouse_bedroom]
	# -- snip --
```

The enum generated will look like:

```cpp
enum LocationId {
	Aldaria,
	Bathhouse,
	BathhouseBedroom,
	Farm,
	Town,
	LEN
}
```

Notice that there is **no** entry named `Default` and that the order of the enumeration is in alphabetical order by the *header* name (the string inside the `[square brackets]`). In this file, each table has a field named `name` but this is totally irrelevant to the magic enum generator, which only looks at the header names. In this file's case, the `name` field refers to how the map name is shown in the UI, not how the enum generator makes enums.
