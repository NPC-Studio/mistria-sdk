# AudioBank

These are simple files which describe [FMOD Audio Banks](https://www.fmod.com/docs/2.03/api/studio-api-bank.html).

There **must be a matching `.bank` file next to this meta file of the same file stem**. For example, if there is a meta-file `Master.meta.toml`, then there must also be a `Master.bank` in the same directory.

An AudioBank has *no* required fields, so a full AudioBank file could be just:

```toml
[meta_properties]
id = "74540ee62104b2d6"
asset_kind = "AudioBank"

[asset_properties]
```

Here is an entry with all optional fields:

```toml
[meta_properties]
id = "74540ee62104b2d6"
asset_kind = "AudioBank"

[asset_properties]
load_sync = false
```

### `load_sync`

When set, we load audio banks in a single-threaded fashion. Otherwise, we will load audio banks asynchronously. In that case, the game must make sure that the banks have loaded in at some point.

If `load_sync` is missing, it will be assumed to be `true`.
