# AudioStrings

These are simple files which describe [FMOD Strings Bank](https://www.fmod.com/docs/2.03/api/glossary.html#studio-strings-bank).

There **must be a matching `.strings.bank` file next to this meta file of the same file stem**. For example, if there is a meta-file `Master.strings.meta.toml`, then there must also be a `Master.strings.bank` in the same directory.

An AudioStrings has *no* required fields, so a full AudioStrings file could be just:

```toml
[meta_properties]
id = "fd960812e681f6e0"
asset_kind = "AudioStrings"

[asset_properties]
```

Here is an entry with all optional fields:

```toml
[meta_properties]
id = "fd960812e681f6e0"
asset_kind = "AudioStrings"

[asset_properties]
load_sync = false
```

### `load_sync`

This is an _optional field_.

See [AudioBank's `load_sync`](./audio-bank.md) property for more.

If `load_sync` is missing, it will be assumed to be `true`.
