# Animations

Animations are "sprites" in other engines. These are images, or textures, which get fed into an `mwe::Sprite` eventually. Here is a standard Animation `TOML` entry:

```toml
frame_size = [100, 100]
frame_len = 10
dimensions = [1000, 100]
filter_kind = "Nearest"
mipmap_filter_kind = "Nearest"
wrap = "Repeat"
duration = 0.1
offset.horizontal = "middle"
offset.vertical = "top"
```

Many of these properties are not needed, and can be inferred. In fact,
here is the minimum viable Animation meta file:

```toml
frame_size = [100, 100]
```

There **must be a matching `png` file next to this meta file of the same file stem.** For example, if there is a meta-file `spr_player.meta.toml`, then there must also be a `spr_player.png` in the same directory.

Let's go through each property:

### `frame_size`

This is a **required field**.

This is the size in pixels on the texture ("texels") that each frame of animation takes up. If this animation has only one frame (ie, does not actually animate), then this number will be the size of the texture's total dimensions.

### `frame_len`

This is an _optional field_.

This is the total number of frames within the animation. If it is _missing_, we will assume a value of `1` (ie, an Animation with no animation).

### `dimensions`

This is an _optional field_.

This is the total texel width and height of the Animation's sheet of all frames. Most of the time, this can be omitted as long as the Animation is a single-row based Animation -- if the Animation is composed of multiple rows, then the dimensions will need to be written. Otherwise, dimensions are calculated as `[frame_len * frame_size.x, frame_size.y]`.

### `filter_kind`

This is an _optional field_.

This describes how the graphics card will handle in-exact texture coordinates. There are two options:

- `Nearest` or `NearestNeighbor`: select this for pixel art.
- `Linear`: select this for non-pixel art cases.

If `filter_kind` is missing, it will be assumed to be `Nearest`.

### `wrap`

This is an _optional field_.

The `wrap` field tells the GPU what to do for texture lookups that go outside the texture's texel dimensions (ie, sampling texel 200 on a texture that is only 100 texels wide).

There are three options:

- `Clamp`: Reads outside the texture's dimensions will be clamped to the texture's range.
  For example, trying to read (1.32, 1.21) will instead read (1, 1).
  More particularly, it will be clamed to `(1 / 2N)..(1 - 1/2N)`, where `N`
  is the size of the axis. So technically the maximum and minimum values aren't quite
  0 and 1, but the nearest `1 / 2N` of those values.

- `Repeat`: Reads outside the texture's dimensions will repeat the texture. For example, reading `1.23` will instead read `.23`. In fact, all we do is ignore the whole integer and only read
  the fractional part of the texture uv.

- `Mirror`: Reads outside the texture's dimensions will seemingly mirror the texture.

If `wrap` is missing, it will be assumed to be `Repeat`.

### `srgb`

This is an _optional field_.

When set, we assume that the Animation's pixel data is sRGB encoded. Almost all normal images should be sRGB encoded if they were made in Aseprite or Photoshop without any special settings.

The exceptions to this rule are `LUT` textures or other "data" images, where the image really is a collection of data rather than a visual artifact. For these, `srgb = false` should be present on them.

If `srgb` is missing, it will be assumed to be `true`.

### `duration`

This is an _optional field_.

Animation duration can be either a single or variable per-frame.
Here's a single duration:

```toml
duration = 0.5
```

Variable frame-rate animation is possible by declaring an array of times
per frame:

```toml
duration = [0.5, 0.5, 1.0, 2.7, 0.1]
```

In variable frame-rate animations, the number of entries in the array _must_
equal `frame_len`. If it does not, an error will be reported and the Animation
will not load.

The value in `duration`, whether it's a single value or an array, is a number of seconds that each frame takes up in _real world time_. In applications like Aseprite, these frames are often reported in whole milliseconds, but we instead give these with floating point precision of a second.

As a special note, durations of `0.0` are _legal_ for single frame-rate animations,
but _illegal_ in per-frame frame-rate animations.

```toml
single_animation.duration = 0.0 # this is legal and fine
single_animation.duration = [1.0, 0.0] # this is illegal and will make the sprite fail to load
```

If `duration` is missing, it will be assumed to be `0.0`.

### `offset`

This is an _optional field_.

Offset is the amount of _negative_ pixels that we shift the animation by when it is rendered in terms of world-space pixels. Don't overthink this -- if you have an offset of `-20`, and the Animation is trying to be draw at coordinate `100`, it will instead be drawn at coordinate `80`.
In other engines, this is often called the `origin`. This allows for better depth sorting and more logical reasoning about certain characters. For example, in *Fields of Mistria*, most of the NPCs have their origins at the NPCs *feet*, which allows for better depth sorting and more reasonable positioning of the NPCs.

There are two offset values, and either or both can be provided:

```toml
offset.horizontal = "Left"
offset.vertical = 2
```

For horizontal and vertical offsets, there are four possible values:

- `Left` / `Top`: A texel value of `0`.
- `Middle`: A texel value of `dimension * 0.5`.
- `Right` / `Bottom`: A texel value of `dimension`.
- A custom value.

Custom values can be any number, including negative numbers, and including numbers with decimal point accuracy. Even if these numbers correspond to `Left`, `Top`, `Middle`, `Right`, or `Bottom`, the hard-coded number will still be used. That means if you later resize the Animation (and update the other properties as expected), a hard-coded offset might be worse than use `Right`, as an example, since it won't "automatically" update with the new dimensions.

All of these are valid offset values:

```toml
example_one.offset.vertical = -1.5
example_two.offset.vertical = 1.5
example_three.offset.vertical = 100000000
example_four.offset.vertical = 0
example_five.offset.vertical = -100.0
```

The _origin_ for all `vertical` offsets is _top-left_ in Meta files. So an origin of:

```toml
offset.vertical = "Top"
```

is equivalent to an origin of:

```rs
offset.vertical = 0
```

which always points at the texel which is to the top-left on an texture.

If `offset` is not present, we will assume an offset of `Top` and `Left`.

### `atlas`

Animations can have an explicit atlas name on them. This is purely informative for tools like `maybe atlas create --tagged` and `maybe atlas --list` to work with, which they use to create atlases.

Ultimately what is or is not in any `TextureAtlas` is information that should be found  *within that TextureAtlas*, so this field should be thought of more as a request.

If missing, we assume that the Animation is not on any atlas.

### `tags`

Animations can have tags on them, which can describe any arbitrary property which the user wishes.

```toml
tags = ["keep_cpu_texture", "load_first"]
```

A single tag does not need to be in an array:

```toml
tags = "keep_cpu_texture"
```

If no tags are present, then the animation will have no tags on it, as if you'd written `tags = []`.
