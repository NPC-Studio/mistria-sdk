# TextureAtlases

TextureAtlases are an *optimization* for the game's rendering pipeline. TextureAtlases are never required, and Animations *do not* need to be on a TextureAtlas at all.

Atlases improve the game's performance in two main, major ways:

1. Startup times are greatly improved, since we only load a single image (the TextureAtlas) rather than dozens or hundreds of loose files. Loading one hundred 1mb files is far, far slower than loading one 100mb file.
2. In-game performance is generally much better, since we can "batch" our draw calls more efficiently. This mostly means that the FPS will generally improve, particularly on low-end hardware.

Generally, TextureAtlases do **not** use `required_assets` in their `meta_properties` -- this allows for TextureAtlases to become "out dated" as assets are added and removed, without needing to be remade. In practice, in `Fields of Mistria,` TextureAtlases are made as part of the build pipeline and are always up to date.

TextureAtlases are **not** hand-made -- these second-order assets are made with `maybe atlas create`.

Here is an example:

```toml
[meta_properties]
    id = "822f100d9f831a16"
    asset_kind = "TextureAtlas"

[asset_properties]
    dimensions = [4096, 4096]
    filter_kind = "Nearest"
    texture_wrap = "Repeat"
    mipmap_filter_kind = "Nearest"
    srgb = true
    
    [[asset_properties.animations]]
        texture_ids = ["51c9c384bf06bed9::2", "ca4f7e1896daaf62::2"]
        top_left_dimensions = [1, 59, 48, 56]
```

There **must be a matching `png` file next to this meta file of the same file stem**. For example, if there is a meta-file `AnimalAtlas_0.meta.toml`, then there must also be a `AnimalAtlas_0.png` in the same directory.

### `dimensions`

This is a **required field**.

The total texel size of the png. Unlike in Animations, this is required.

### `filter_kind`

This is an _optional field_.

See [Animation `filter_kind`](./animations##filter_kind) for more. When an Animation is on an atlas, their version of this property is *ignored* and *only the Atlas's property matters.*

### `wrap`

This is an _optional field_.

See Animation's `wrap` for more. When an Animation is on an atlas, their version of this property is *ignored* and *only the Atlas's property matters.*

## `srgb`

This is an _optional field_.

See Animation's `srgb` for more. When an Animation is on an atlas, their version of this property is *ignored* and *only the Atlas's property matters.*

## `animations`

This is the main property of a texture atlas and is **required**.

This property is an array of objects, which in `TOML` are represented with [array of table](https://toml.io/en/v1.0.0#array-of-tables) syntax like `[[asset_properties.animations]]`.

There are two fields within each `.animations` entry and both are **required**. These fields describe each *sub-image* placed on the TextureAtlas, which is used by `maybe` to determine which Animations are included in the atlas at all.

### `texture_ids`
This is an array of `TextureId`s, represented as a string like `AnimationId::FrameNumber`. The `AnimationId` is simply the `id` of the Animation and the `FrameNumber` is the frame itself. 

For example, the `TextureId` `"abcdefabcdefabcd::27"` would be a valid `TextureId` referring to the `Animation` `abcdefabcdefabcd` and to frame `27` within that animation.

This is an **array** because we often have **duplicates** of the same frame, made multiple times. For frames to be in this array together, they must be *pixel for pixel identical*. Since each string only refers to a given frame, this means an animation can have frame0 as a unique frame and frame1 as a duplicate frame.

### `top_left_dimensions`
This is an array of 4 numbers.
- The first element of the array (**very confusingly**) is the `x` coordinate where the sub-image is placed on the `TextureAtlas`. Arguably, this field should be called `left_top_dimensions` as a result, but it is not.
- The second element is the `y` coordinate where the image is placed on the `TextureAtlas`. Like all coordinates in `.meta.toml` files, this `y`-coordinate is relative to the top of the file.
- The third element is the `width` of the sub-image.
- The fourth element is the `height` of the sub-image.

So for the `top_left_dimensions` of `[1, 59, 48, 56]`, we'd expect to find this sub-image located at `[1, 59]` and for its width and height to be `[48, 56]`.