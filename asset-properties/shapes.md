# Shapes

Shapes are sometimes called "masks" or "collisions" in other engines. This is just a floating shape or polygon (`poly_`) without any reference to other assets. We use this in the physics engine for *Fields of Mistria* to define collisions, within the User-Interface to define mouse-clicking and centering-areas, and in other miscellaneous systems like Fishing and Audio Emitters.

To make a shape, you must indicate the `kind` of the shape and then provide
the necessary detail.

### `kind`

**_Required_**

This is the kind of the shape. What kind determines what other fields are required.
There are three possible options: `"box"`, `"circle"`, and `"empty"`.

These determine what other requirements are needed.

### `offset`

This is a **_required property_** when `kind` is `"box"` or `"circle"`.

The offset of this shape from whatever body it may be attached, to the **top-left** point of a box, or to the **center** of a circle. `Y` is _down_, as for all numbers in a [`Shape`].

When this shape is attached to an Animation, for example, this is the offset from that Animation's origin point. For example, if we wanted a `[10, 5]` dimensional box which is horizontally centered and whose bottom touches an Animation's origin point, such as:

```txt
.-----------.
|           |
|           |
|           |
|           |
|           |
|_____x_____.
      ↑
    origin
```

We would need an offset of `[-5, -5]`. The x-axis would have an offset of `-5`
to center it about the origin, and since `Y` is down in this asset's origin space,
we need to move the top-left corner of this box up 5.

On the other hand, if we wanted to have the box's bottom touching the origin:

```txt
    origin
      ↓
.-----x-----.
|           |
|           |
|           |
|           |
|           |
|___________.
```

Then we would want the `offset` to be `[-5, 0]`. The y-axis would _no offset_, since the top-left
of the box is already in the correct position.

### `dimensions`

This is a **_required property_** when `kind` is "box".

The width and height of the box. This value must be greater than zero in both axes.

### `radius`

This is a **_required property_** when `kind` is "circle".

`radius` is `C / 2π`. This value must be greater than zero.
