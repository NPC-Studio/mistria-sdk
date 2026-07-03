## Fonts

A font is a raw font file (ttf or otf). We don't do much with these automatically except load them into our font engine.

There **must be a matching `ttf` or `otf` file next to this meta file of the same file stem**. For example, if there is a meta-file `Times New Roman.meta.toml`, then there must also be a `Times New Roman.ttf` or `Times New Roman.otf` in the same directory. If there is both a `ttf` and an `otf` file present, we will prefer the `ttf` file.

A font has *no* required fields, so a full font file could be just:

```toml
[meta_properties]
id = "1a071ee8ccda4cf2"
asset_kind = "Font"

[asset_properties]
```

Here is an entry with all optional fields:

```toml
[meta_properties]
id = "1a071ee8ccda4cf2"
asset_kind = "Font"

[asset_properties]
minimum_expected_size = 12
renders_at_multiples_only = true
allow_shading = false
```

### `minimum_expected_size`

This is an _optional field_.

This is a hint to the game as to what size the font should render at. The game is free to make whatever decisions it would like. Having no value implies that the font renders well at any em value size.

### `renders_at_multiples_only`

This is an _optional field_.

Fonts which can only render at multiples of their `minimum_expected_size` should set this field. Setting this field to true without also having a `minimum_expected_size` is a logical error.

## `allow_shading`

This is an _optional field_.

When set, fonts are allowed to have within their outline. For pixel fonts, you'll often want to set this value to false.

If `allow_shading` is missing, it will be assumed to be `true`.