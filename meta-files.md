# Asset Data

All assets in `Fields of Mistria` use `.meta.toml` files to describe assets. Some assets are *only* a `.meta.toml` file, while others have binary data associated with them.

## The Meta File Format

Every `TOML` file has two major subsections, `meta_properties` and `asset_properties`.

```toml
[meta_properties]
    id = "6d8ebd6fbe11cfaf"
    asset_kind = "SOME_ASSET_KIND"
    required_assets = []

[asset_properties]
    # here would be the asset properties, which vary per asset type. See below for more.
```

For many assets, `asset_properties` is entirely empty, but `meta_properties` will always be
filled.

Let's go through each `meta_properties` field first:

### `id`

**_Required_**

The `id` is a `u64` id, such as `6d8ebd6fbe11cfaf`, rendered in a hex format, to uniquely identify
a given asset. Assets refer to other assets by this `u64` id always, which allows assets to move around in the asset folder as users would like without breaking inter-asset connections. See more about how assets interact with each other in [required_assets](#required_assets).

Ultimately, the `id` **is** the asset. Projects are free to arrange and re-arrange their assets in any folders they'd like, and even rename assets, but the `id` uniquely identifies each asset.

### `asset_kind`

**_Required_**

The `asset_kind` uniquely identifies what kind of `.meta.toml` file the system is working with.
The following `asset_kind`s exist:

- [`"Animation"`](./asset-properties/animations.md)
- [`"TextureAtlas"`](./asset-properties/texture-atlases.md)
- [`"AudioBank"`](./asset-properties/audio-bank.md)
- [`"AudioStrings"`](./asset-properties/audio-strings.md)
- [`"Font"`](./asset-properties/fonts.md)
- [`"Shape"`](./asset-properties/shapes.md)
- [`"MagicEnums"`](./asset-properties/magic-enums.md)

There are as well other kinds of files in *Fields of Mistria* which have not yet been documented. You can learn more about them by analyzing the ones which already exist in the project. Many of these assets are "singeltons" or specialized assets which we don't expect there to be more than one of.

Attempting to make a `.meta.toml` file with the wrong `asset_kind` will likely lead to errors, panics,
or difficult bugs. Attempting to make a `.meta.toml` with an unrecognized `asset_kind` will lead to
the asset failing to load, which can lead to a crash on startup.

### `required_assets`

**_Not Required_** *(ironically)*

Some assets reference other assets, which determines how we load in resources. The `AssetDatabase` requires those assets to be loaded before another asset can reference them. To that end, the `required_assets` array tells the system which assets should be loaded in first. Attempting, in an asset implementation, to read an asset which is _not_ listed in `required_assets` could possibly crash a game, as the order of assets loaded in is random.

This list of assets is always in `u64`s. As an example, Texture Atlases reference each Animation which is placed upon them. As a result, they must list out those assets, such as:

```toml
required_assets = ["4b287ef9888a372f", "76ac2327437c3aff"]
```
