# CardinalDL CLI setup

The CLI downloads from the same services as the desktop app, straight from your terminal. This page gets you set up and running. For the full list of flags and examples, see the [CLI reference](./CDL-CLI-DOCS.md).

## How it works

The CLI and the desktop app (the GUI) share one file: `storage.db`. That file holds your account login, your per-service logins, and your defaults.  
The CLI reads it and reuses whatever the GUI has set up.

Because of that, the CLI has no login screen of its own for the streaming services. You set those up once in the GUI, and the CLI uses them.  
Anything you do not pass on the command line falls back to your GUI defaults, and only then to a built in default.

## Before you start

Do this once in the desktop app:

1. Install and open the GUI.
2. Sign in to your CardinalDL account.
3. Log into each streaming service you plan to use.
4. Set your defaults if you want them (quality, dubs, subs, download path). These become the CLI fallbacks.

On the same machine the CLI can now use all of that right away, because it reads the same `storage.db`.

## Where storage.db lives

| System | Path |
| :-- | :-- |
| Windows | `C:\Users\<you>\.cardinaldl\storage\storage.db` |
| Linux | `/home/<you>/.cardinaldl/storage/storage.db` |
| macOS | `/Users/<you>/.cardinaldl/storage/storage.db` |
| Docker | `/config/storage/storage.db` inside the container |

## First run

Check the CLI runs at all. This one works without an account, since it only reads the built in language table:

```
cardinaldl --listlangs
```

Then try a real listing against a service you have set up:

```
cardinaldl --service crunchy --srz SOME_ITEM_ID
```

If you see this message:

```
Not logged in, please login from the GUI before using the CLI!
```

then the `storage.db` the CLI is reading has no signed in account. Open the GUI and sign in, or use `--login` below.

## Moving from Windows to Linux

To set up the CLI on another machine, use the Export function in the desktop app to save a copy of your database. It writes a `.db` snapshot of your `storage.db`. Move that file to the new machine and rename it to `storage.db`. Put it at the path from [the table above](#where-storagedb-lives), or keep it in a folder of your choice and point the CLI at it with [`--configpath`](./CDL-CLI-DOCS.md#configpath).

Your service logins and defaults come across in that file. Your CardinalDL account login does not, so sign in once on the new machine:

```
cardinaldl --login --username you@example.com --password 'your-password'
```

That signs you in and the CLI works normally after. You only do this once per machine, and it does not touch your service logins.

## Full reference

Every flag, every mode, and a long list of example commands are in the [CLI reference](./CDL-CLI-DOCS.md).
