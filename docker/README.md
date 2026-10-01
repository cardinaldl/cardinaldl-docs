# Running CardinalDL with Docker

The Docker image runs CardinalDL as a server. There is no desktop window. Instead you open the web UI in your browser and use it the same way you would use the desktop app. You sign in, log into your services, set your defaults, and start downloads from there.

This folder has everything you need to get going:

- `docker-compose.yaml` is a ready to use compose file.
- `.env-example` is a template for the settings you are most likely to change.

## What you need

- Docker and Docker Compose.
- A CardinalDL account.

## Quick start

1. Copy this folder (or just `docker-compose.yaml` and `.env-example`) to your server.

2. Make your own `.env` from the template and set a password:

   ```
   cp .env-example .env
   ```

   Open `.env` and set `REMOTE_ACCESS_PASSWORD` to something strong. This is the password you type to open the web UI.

3. Start it:

   ```
   docker compose up -d
   ```

   The first start pulls the `cardinaldl/cardinaldl:latest` image, so it can take a minute.

4. Open `http://YOUR-SERVER-IP:4173` in a browser and enter the password from step 2.

5. Sign in to your CardinalDL account, log into the streaming services you want, and set your defaults. This is the same setup you would do in the desktop app, and it is saved so you only do it once. See Logging into services below for one exception.

## Logging into services

Most services log in right here in the web UI. Services that use a username and password, or a pasted cookie, or a device code all work on the Docker server.

Crunchyroll is the exception. Its login opens an OAuth sign in window, and only the desktop app can open that window. On the Docker server the Crunchyroll login button returns an error instead.

To use Crunchyroll under Docker, sign in once in the desktop app, then copy that machine's `storage.db` into `./cdl/config/storage/storage.db` next to your compose file. The Crunchyroll login is saved in that file, so it comes across and downloads work. Stop the container first, drop the file in, then start it again.

## Ports

| Port | What it is | Change it on `.env-example` with |
| :-- | :-- | :-- |
| `4173` | The web UI and the download server | `PORT` |
| `9117` | The *arr (Torznab) indexer, for Sonarr, Radarr, and Prowlarr | `ARR_PORT` |

The numbers you set only change the host side. Inside the container the app always uses 4173 and 9117.

If you do not use the *arr integration you can drop the `9117` line from the compose file.

## Volumes

Everything is a bind mount, so the files sit in a `cdl` folder next to your compose file and you can open, edit, or replace them directly.  
The folders are created on first start if they do not exist.

| Host folder | Container path | What lives there |
| :-- | :-- | :-- |
| `./cdl/config` | `/config` | Your `storage.db` (account, service logins, settings). Keep this one. |
| `./cdl/downloads` | `/downloads` | Finished downloads |
| `./cdl/temp` | `/tmp` | Work in progress files before muxing |
| `./cdl/arr/arr-torrents` | `/data/torrents` | The *arr watch folder for incoming grabs |
| `./cdl/arr/arr-completed` | `/data/completed` | The *arr completed folder |

Your `storage.db` sits at `./cdl/config/storage/storage.db` on the host. That is the one file to back up if you want to keep your account and settings, and it is the file you swap in when moving a login over from the desktop app.

## Environment variables

The tunable ones are listed in `.env-example` with their defaults, so copy that to `.env` and change only what you need. You can also set any of these in the `environment` block of the compose file, or with `-e` on `docker run`. When an environment variable is set it wins over the matching setting in the web UI, and in most cases the web UI will show that the value is locked by the environment.

### Core

| Variable | Default | What it does |
| :-- | :-- | :-- |
| `PORT` | `4173` | Port the web UI and download server listen on |
| `CONFIG_PATH` | `/config` | Base folder for `storage.db` and other data. The image sets this for you. |
| `DOWNLOAD_PATH` | `/downloads` | Where finished files go. Written into your settings on start. |
| `TEMP_PATH` | `/tmp` | Where in progress files go. Written into your settings on start. |
| `LISTEN_ADDRESS` | `0.0.0.0` | Address the server binds to. The default listens on every interface, which is what you want inside a container. Advanced. |

### Remote access

| Variable | Default | What it does |
| :-- | :-- | :-- |
| `REMOTE_ACCESS_PASSWORD` | none | The password for the web UI. See Web UI password below. |

### *arr integration

| Variable | Default | What it does |
| :-- | :-- | :-- |
| `ARR_INTEGRATION_ENABLED` | `false` | Turn the Torznab indexer on or off. Off by default, set it to `true` in `.env` to enable. |
| `ARR_PORT` | `9117` | Port for the Torznab indexer |
| `ARR_TORRENT_FOLDER` | `/data/torrents` | Folder watched for incoming `.torrent` grabs |
| `ARR_WATCH_FOLDER` | `/data/completed` | Folder where finished files are placed for the *arr app to import |
| `ARR_API_KEY` | auto | API key for the indexer. If you do not set one, a random key is created and printed in the container logs on start. |
| `ARR_DUB_LANGUAGES` | your defaults | Comma list of dub languages for *arr grabs, for example `JP,EN`. Falls back to your default dubs. |
| `ARR_SUBTITLE_LANGUAGES` | your defaults | Comma list of subtitle languages for *arr grabs. Falls back to your default subs. |

## Web UI password

The web UI is protected by a password so that only you can reach it.

- Set `REMOTE_ACCESS_PASSWORD` to choose the password. While this variable is set you cannot change the password from inside the web UI, because the environment controls it.
- If you leave it empty, CardinalDL creates a random password on first start and saves it. You will not be shown that password, so set your own instead.
- If you ever set the password from inside the web UI rather than the environment, it needs to be at least 8 characters.

## *arr integration (Sonarr, Radarr, Prowlarr)

CardinalDL can act as a Torznab indexer, which lets Sonarr, Radarr, or Prowlarr search and grab from it like any other indexer.

It is off by default. To use it, set `ARR_INTEGRATION_ENABLED=true` in your `.env`. Then point your *arr app at:

```
http://YOUR-SERVER-IP:9117/api?apikey=YOUR-API-KEY
```

Find the API key in the container logs on start, or set your own with `ARR_API_KEY`. The two data folders (`/data/torrents` and `/data/completed`) are shared between CardinalDL and your *arr app. Grabs land as files in the torrent folder, CardinalDL downloads them, and the finished files show up in the completed folder ready to import.

If you never use *arr, you can also remove the `9117` port line and the two `/data` mounts from the compose file to keep things tidy.

## Updating

```
docker compose pull
docker compose up -d
```

Your account, settings, and downloads live in those bind-mounted folders, so they survive an update.
