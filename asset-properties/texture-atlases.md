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
        placement = [1, 59, 48, 56, 14, 14]
```

There **must be a matching `png` file next to this meta file of the same file stem**. For example, if there is a meta-file `AnimalAtlas_0.meta.toml`, then there must also be a `AnimalAtlas_0.png` in the same directory.

## Trimming

Images are, by `maybe` in `v0.8` or greater, *trimmed*, which is to say, all whitespace (defined as any `RGBA` pixel where `A` is `0`, regardless of what RGB is) surrounding the opaque pixels is removed. This can be trivially done manually in tools like Photoshop and Aseprite. We remove whitespace from all sides of the image.

To support trimming on TextureAtlases, we have some optional fields in `placement` now. See the below documentation on it.

### `dimensions`

This is a **required field**.

The total texel size of the png. Unlike in Animations, this is required.

### `filter_kind`

This is an _optional field_.

See [Animation `filter_kind`](./animations##filter_kind) for more. When an Animation is on an atlas, their version of this property is *ignored* and *only the Atlas's property matters.*

## `wrap`

This is an _optional field_.

See Animation's `wrap` for more. When an Animation is on an atlas, their version of this property is *ignored* and *only the Atlas's property matters.*

### `srgb`

This is an _optional field_.

See Animation's `srgb` for more. When an Animation is on an atlas, their version of this property is *ignored* and *only the Atlas's property matters.*

### `animations`

This is the main property of a texture atlas and is **required**.

This property is an array of objects, which in `TOML` are represented with [array of table](https://toml.io/en/v1.0.0#array-of-tables) syntax like `[[asset_properties.animations]]`.

There are two fields within each `.animations` entry and both are **required**. These fields describe each *sub-image* placed on the TextureAtlas, which is used by `maybe` to determine which Animations are included in the atlas at all.

### `texture_ids`
This is an array of `TextureId`s, represented as a string like `AnimationId::FrameNumber`. The `AnimationId` is simply the `id` of the Animation and the `FrameNumber` is the frame itself. 

For example, the `TextureId` `"abcdefabcdefabcd::27"` would be a valid `TextureId` referring to the `Animation` `abcdefabcdefabcd` and to frame `27` within that animation.

This is an **array** because we often have **duplicates** of the same frame, made multiple times. For frames to be in this array together, they must be *pixel for pixel identical*. Since each string only refers to a given frame, this means an animation can have frame0 as a unique frame and frame1 as a duplicate frame.

### `placement`
This is an array of 8 *or four* numbers.
- The first element of the array is the `x` coordinate where the sub-image is placed on the `TextureAtlas`.
- The second element is the `y` coordinate where the image is placed on the `TextureAtlas`. Like all coordinates in `.meta.toml` files, this `y`-coordinate is relative to the top of the file, like in most image editing applications.
- The third element is the `size` of the sub-image. When multiple texture ids share one `placement`, they *must all have been the same size.* Failure to adhere to this can create visual bugs.
- The fourth element is the `size` of the sub-image. When multiple texture ids share one `placement`, they *must all have been the same size.* Failure to adhere to this can create visual bugs.

So for the `placement` of `[1, 59, 48, 56]`, we'd expect to find this sub-image located at `[1, 59]` and for its width and height to be `[48, 56]` and for it to have not been trimmed at all.

It is permissable to use simply atlasing and only have these 4 elements. However, Fields of Mistria itself will add four more entries to this array for more optimal atlasing starting in `v0.16.4`:

- The fifth *optional ement* is the `original_width` of the sub-image. If the sub-image was trimmed before being placed in the atlas, then the original size of the sub-image should be notated here. When multiple texture ids share one `placement`, they *must all have had the same original_size.* Failure to adhere to this can create visual bugs.
- The sixth *optional ement* is the `original_height` of the sub-image. If the sub-image was trimmed before being placed in the atlas, then the original size of the sub-image should be notated here. When multiple texture ids share one `placement`, they *must all have had the same original_size.* Failure to adhere to this can create visual bugs.
- The seventh *optional element* is the `minimum_x_trim` of the sub-image. If the sub-image has been trimmed *before* it was placed within the atlas, then this should show the amount trimmed from the `left` side of the image. When multiple texture ids share one `placement`, they *must all have the same minimum_x_trim.* Failure to adhere to this can create visual bugs.
- The eighth *optional element* is the `minimum_y_trim` of the sub-image. If the sub-image has been trimmed *before* it was placed within the atlas, then this should show the amount trimmed from the `top` side of the image. When multiple texture ids share one `placement`, they *must all have the same minimum_x_trim.* Failure to adhere to this can create visual bugs.

When there are no elements [4..8], we assume that `size` and `original_size` are the same, and that no trim has occured. This should allow modded atlases built against `v0.16.3` to work on `v0.16.4`. There will be a loading error if any sized array except `4` or `8` is given.

This space will continue to have updates. To allow for even better packing in the future, we will allow placements to share texels which differ in `trim` and `original_size`.