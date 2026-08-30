# CardinalDL CLI reference

This is the complete reference for the CardinalDL command-line interface. Use the [Index](#index) to jump to a specific argument.

The CLI shares the same account and settings as the CardinalDL GUI (it reads the same `storage.db`).  
Anything you do not pass on the command line falls back to what the GUI has configured, and only then to a built-in default.  
See [How defaults work](#how-defaults-work) for the full picture.

```
cardinaldl [options]
```

> [!IMPORTANT]
> You must be signed in before the CLI can download or list anything. Sign in from the GUI, or use [`--login`](#login) once from the command line.  
> `--service` is required for every mode except [`--login`](#login) and [`--listlangs`](#listlangs).

---

## Modes at a glance

The CLI does one of five things per run, decided by which arguments you pass:

| Mode | Trigger | Example |
| :-- | :-- | :-- |
| Download | [`--item`](#item) (plus [`--episode`](#episode) for a series) | `cardinaldl --service crunchy -i ABC -s 1 -e 5` |
| List an item | [`--listitem`](#listitem) (`--srz`) | `cardinaldl --service crunchy --srz ABC` |
| List a season by ID | [`--season`](#season) with a season ID (no `--listitem`) | `cardinaldl --service crunchy -s SEASON_ID` |
| Sign in | [`--login`](#login) | `cardinaldl --login --username you --password pw` |
| List languages | [`--listlangs`](#listlangs) | `cardinaldl --listlangs` |

---

## Index

- [Global & required](#global--required)
    - [`--service`](#service)
    - [`--configpath`](#configpath)
- [Selecting what to download](#selecting-what-to-download)
    - [`--item`, `-i`](#item)
    - [`--season`, `-s`](#season)
    - [`--episode`, `-e`](#episode)
- [Video](#video)
    - [`--videoquality`, `--vq`](#videoquality)
    - [`--hybrid`](#hybrid)
    - [`--fallback`](#fallback)
    - [`--novideo`](#novideo)
- [Audio](#audio)
    - [`--audioquality`, `--aq`](#audioquality)
    - [`--dublang`, `--dl`](#dublang)
    - [`--noaudio`](#noaudio)
- [Subtitles](#subtitles)
    - [`--dlsubs`, `--ds`](#dlsubs)
    - [`--forcesubformat`](#forcesubformat)
    - [`--nosubs`](#nosubs)
- [Output & muxing](#output--muxing)
    - [`--outputformat`](#outputformat)
    - [`--filename`, `--fn`](#filename)
    - [`--skipvidmux`](#skipvidmux)
    - [`--skipaudiomux`](#skipaudiomux)
    - [`--skipsubmux`](#skipsubmux)
- [Paths](#paths)
    - [`--dlpath`, `--dlp`](#dlpath)
    - [`--temppath`, `--tp`](#temppath)
- [Decryption](#decryption)
    - [`--dectool`, `--dt`](#dectool)
- [Listing & info](#listing--info)
    - [`--listitem`, `--srz`](#listitem)
    - [`--full`](#full)
    - [`--workers`, `-w`](#workers)
    - [`--jsonoutput`, `--jo`](#jsonoutput)
    - [`--listlangs`](#listlangs)
- [Authentication](#authentication)
    - [`--login`](#login)
    - [`--username`](#username)
    - [`--password`](#password)
- [Built-ins](#built-ins)
    - [`--help`, `-h`](#help)
    - [`--version`, `-V`](#version)
- [Example commands](#example-commands)
- [How defaults work](#how-defaults-work)

---

## Global & required

#### <a id="service"></a>`--service`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | choice | `adn`, `akiba`, `amazon`, `animebox`, `crunchy`, `disney`, `hbomax`, `hidive`, `jonuplay`, `mgm`, `netflix`, `oceanveil`, `paramount`, `peacock`, `playerpl`, `rtl` | none (required) |

The service to use, by short name. The value is lower-cased, so `CRUNCHY` and `crunchy` are the same.  
Required for every mode except [`--login`](#login) and [`--listlangs`](#listlangs). Omitting it there exits with `Missing required argument: service`.  
If the service exists but is not in your account's allowed services, the run exits after login with a "not available for this account" message.  
Run [`--help`](#help) to see the exact list your build supports.

#### <a id="configpath"></a>`--configpath`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--cp`, `--configPath` | string | n/a | platform default storage location |

Path to a `storage.db` to use instead of the default.  

---

## Selecting what to download

#### <a id="item"></a>`--item`, `-i`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `-i` | string | n/a | none |

The item (title) ID to download. Passing this puts the CLI in download mode. If the item is a movie, it downloads straight away.  
Passing [`--season`](#season) or [`--episode`](#episode) for a movie is an error. If the item is a series, you must also pass [`--episode`](#episode).

#### <a id="season"></a>`--season`, `-s`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `-s` | string or number | n/a | `1` (when downloading a series) |

Season number or season ID. When downloading a series and this is omitted, it defaults to season `1`.  
If two seasons share the same number, pass the season ID instead.  
Passed on its own with a season ID (without [`--listitem`](#listitem)), it lists a season directly by its ID, for services that support that.

#### <a id="episode"></a>`--episode`, `-e`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `-e` | string | n/a | none (required to download a series episode) |

Which episode(s) to download. Accepts a single number (`5`), an episode ID, a range (`01-24`), a comma list (`1,3,5`), or a mix (`1-3,7`).  
Episodes named in a range that do not exist are skipped with a warning rather than failing the whole run. Ignored for movies.

---

## Video

#### <a id="videoquality"></a>`--videoquality`, `--vq`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--vq`, `--videoQuality` | string (`resolution@codec@range`) | n/a | GUI Quality page |

Video quality as `resolution@codec@range`. Examples: `1080p`, `2160p@hevc@dv`, `1080p@@dv` (empty codec slot).  
Use `highest` as the resolution to take the best available. Any slot you leave empty follows the GUI Quality page. Resolutions snap to the nearest known tier.  
A specific resolution you type is treated as an exact requirement and fails if it is missing, unless you also pass [`--fallback`](#fallback).

#### <a id="hybrid"></a>`--hybrid`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | boolean | n/a | GUI Quality page (`createHybridByDefault`) |

Generate a hybrid video track when the service supports it.

#### <a id="fallback"></a>`--fallback`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | boolean | n/a | `false` |

When a requested quality cannot be met, fall through the GUI Quality page priorities and take the next-best track instead of failing.  
Applies to both video and audio selection. When a fallback happens, the CLI prints what it had to give up (resolution, codec, or range).

#### <a id="novideo"></a>`--novideo`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | boolean | n/a | `false` |

Do not download the video track.  
Combining `--novideo`, [`--noaudio`](#noaudio), and [`--nosubs`](#nosubs) all at once is an error, since it leaves nothing to download.

---

## Audio

#### <a id="audioquality"></a>`--audioquality`, `--aq`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--aq`, `--audioQuality` | string (`LANG:format@channels@kbps`) | n/a | GUI Quality page |

Per-language audio quality as `LANG:format@channels@kbps`. Examples: `EN:atmos,FR:eac3@5.1` or `EN:@5.1` (empty format slot).  
Comma-separate to list more than one language. Any slot you leave empty follows the GUI Quality page.  
A language named here is added to the dubs to download even if it is not in [`--dublang`](#dublang).

#### <a id="dublang"></a>`--dublang`, `--dl`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--dl`, `--dubLang` | string list | n/a | GUI stored dubs (`defaultDubs`) |

Dub language(s) to download. Accepts a comma list (`JP,EN`) or a JSON array (`["JP","EN"]`). Use `all` to take every available dub.  
Codes you type here are strict: if a title does not have one, that episode fails.  
Codes that come from the GUI defaults are treated as a preference: a missing one is skipped with a warning and the rest still download.  
Cleared by [`--noaudio`](#noaudio). Unknown codes are rejected before any download starts. Run [`--listlangs`](#listlangs) to see valid codes.

#### <a id="noaudio"></a>`--noaudio`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | boolean | n/a | `false` |

Do not download any audio track. This clears [`--dublang`](#dublang) for the run.

---

## Subtitles

#### <a id="dlsubs"></a>`--dlsubs`, `--ds`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--ds` | string list | n/a | GUI stored subs (`defaultSubs`) |

Subtitle language(s) to download, same code format as [`--dublang`](#dublang). Use `all` to take every (non-forced) subtitle.  
Add a variant tag per language to pick a specific track:

- `:full` for the full subtitle track
- `:cc`, `:sdh`, `:caption` for the SDH/CC track
- `:both` for both variants

A bare code (for example `EN`) follows the GUI SDH/CC setting. Example: `--dlsubs EN:cc,DE:full`.  
An unknown variant tag stops the run with a message listing the valid tags.

#### <a id="forcesubformat"></a>`--forcesubformat`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--forceSubFormat` | choice | `srt`, `ass`, `vtt`, `auto`, `raw`, `original` | GUI setting, else `raw` |

Force subtitles into a specific output format. When not set, the CLI uses the GUI's stored subtitle format if it is one of these, otherwise `raw`.

#### <a id="nosubs"></a>`--nosubs`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | boolean | n/a | `false` |

Do not download any subtitles.

---

## Output & muxing

#### <a id="outputformat"></a>`--outputformat`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--outputFormat` | choice | `mkv`, `mp4` | GUI setting, else `mkv` |

Container to write the finished file into. When not set, the CLI uses the GUI's stored output format if it is one of these, otherwise `mkv`.

#### <a id="filename"></a>`--filename`, `--fn`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--fn`, `--fileName` | string | n/a | service default naming |

Output filename. Accepts naming placeholders such as `{seriesName}` or `{episodeNumberDD}`. When not set, the service's default naming is used.

#### <a id="skipvidmux"></a>`--skipvidmux`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | boolean | n/a | `false` |

Keep the video as its own file instead of muxing it into the output container.

#### <a id="skipaudiomux"></a>`--skipaudiomux`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | boolean | n/a | `false` |

Keep every audio track as its own file instead of muxing them in.

#### <a id="skipsubmux"></a>`--skipsubmux`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | boolean | n/a | `false` |

Keep every subtitle track as its own file instead of muxing them in.

---

## Paths

#### <a id="dlpath"></a>`--dlpath`, `--dlp`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--dlp` | string | n/a | GUI download path, else current folder |

Where to write the finished file. When downloading ([`--item`](#item)), the path must already exist or the run exits.  
When not set, the CLI uses the GUI's stored download path if this machine can use it, otherwise the current folder.  
A stored Windows path on a non-Windows machine is ignored with a warning and the current folder is used instead.  
The CLI always writes the file directly into this path. It does not build a per-series/season subfolder the way the GUI does.

#### <a id="temppath"></a>`--temppath`, `--tp`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--tp`, `--tempPath` | string | n/a | configured temp folder |

Override the temp folder used for in-progress segments before the final file is assembled.

---

## Decryption

#### <a id="dectool"></a>`--dectool`, `--dt`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--dt`, `--decTool` | choice | `mp4decrypt`, `shaka` | configured decryption tool |

Decryption tool to use for this run.

---

## Listing & info

#### <a id="listitem"></a>`--listitem`, `--srz`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--srz` | string | n/a | none |

Output the series/season/episode information for an item and exit without downloading. For a series it lists seasons and episodes.  
Pass [`--season`](#season) to focus on one season. Output goes to stdout as text unless [`--jsonoutput`](#jsonoutput) is set.

#### <a id="full"></a>`--full`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | boolean | n/a | `false` |

Include richer per-track detail (available audio tracks, subtitle variants, and qualities) in [`--listitem`](#listitem) output.  
This loads manifests, so it takes longer to return. Use [`--workers`](#workers) to speed it up.

#### <a id="workers"></a>`--workers`, `-w`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `-w` | number | n/a | `1` |

Maximum parallel workers for [`--full`](#full) listings. Only used with `--full`. An invalid value falls back to `1` with a warning.

#### <a id="jsonoutput"></a>`--jsonoutput`, `--jo`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `--jo`, `--jsonOutput` | string (file path) | n/a | none (stdout) |

Write listing data as JSON to the given file path instead of printing to stdout. Only affects [`--listitem`](#listitem) and season listings.

#### <a id="listlangs"></a>`--listlangs`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | boolean | n/a | `false` |

Print every language code with its display name and aliases, then exit.  
Works without a service and without being signed in, since it only reads the built-in language table.

---

## Authentication

#### <a id="login"></a>`--login`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | boolean | n/a | `false` |

Sign in to your CardinalDL account from the command line. Requires both [`--username`](#username) and [`--password`](#password).  
This clears any stored account and device keys first, then signs in fresh. `--service` is not required in this mode.

#### <a id="username"></a>`--username`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | string | n/a | none |

Account username, used only with [`--login`](#login).

#### <a id="password"></a>`--password`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| none | string | n/a | none |

Account password, used only with [`--login`](#login).

---

## Built-ins

#### <a id="help"></a>`--help`, `-h`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `-h` | boolean | n/a | n/a |

Show the generated help, including the live list of services and choices for your build, then exit.

#### <a id="version"></a>`--version`, `-V`

| Aliases | Type | Choices | Default |
| :-- | :-- | :-- | :-- |
| `-V` | boolean | n/a | n/a |

Print the CardinalDL version and exit.

---

## Example commands

`crunchy` is used as the example service throughout. Swap in any short name from [`--service`](#service). Replace `ID` with the real item ID.

Download a single episode (season 1, episode 5):

```
cardinaldl --service crunchy -i ID -s 1 -e 5
```

Download a whole season (episodes 1 through 24):

```
cardinaldl --service crunchy -i ID -s 1 -e 1-24
```

Download a selection of episodes (a list or a mix of lists and ranges):

```
cardinaldl --service crunchy -i ID -s 1 -e 1,3,5
cardinaldl --service crunchy -i ID -s 1 -e 1-3,7
```

Download a movie (no season or episode):

```
cardinaldl --service crunchy -i ID
```

Pick quality, dubs, and subtitles:

```
cardinaldl --service crunchy -i ID -s 1 -e 1 --vq 1080p@hevc --dublang JP,EN --dlsubs EN:full
```

Download every available dub and subtitle:

```
cardinaldl --service crunchy -i ID -s 1 -e 1 --dublang all --dlsubs all
```

Custom filename and MP4 output:

```
cardinaldl --service crunchy -i ID -s 1 -e 1 --filename "{seriesName} - {episodeNumberDD}" --outputformat mp4
```

Keep every track as its own file instead of muxing:

```
cardinaldl --service crunchy -i ID -s 1 -e 1 --skipvidmux --skipaudiomux --skipsubmux
```

List a series as JSON, with full per-track detail, using 4 workers:

```
cardinaldl --service crunchy --srz ID --full --workers 4 --jsonoutput series.json
```

List every language code:

```
cardinaldl --listlangs
```

Sign in from the command line:

```
cardinaldl --login --username you@example.com --password 'your-password'
```

Download all subtitles for the episode and keep the subtitles as individual ASS files:
```
cardinaldl --service crunchy -i ID -s 1 -e 1 --dlsubs all --novideo --noaudio --forcesubformat ass
```

---

## How defaults work

The CLI reads the same `storage.db` as the GUI (override with [`--configpath`](#configpath)). For most settings the resolution order is:

**explicit flag > GUI stored value > built-in default.**

If you pass a flag, that wins. If you leave it out, the CLI uses whatever the GUI has configured.  
If the GUI has nothing set, it uses the built-in default in the table below.

| Setting | Flag | Falls back to (GUI) | Built-in default |
| :-- | :-- | :-- | :-- |
| Video quality | [`--videoquality`](#videoquality) | `qualityPreferences.resolutionPreference` | best available for empty slots |
| Hybrid track | [`--hybrid`](#hybrid) | `qualityPreferences.createHybridByDefault` | off |
| Audio quality | [`--audioquality`](#audioquality) | `qualityPreferences` audio priorities | service default |
| Subtitle SDH/CC variant | [`--dlsubs`](#dlsubs) tag | `qualityPreferences.sdhSubtitles` | n/a |
| Dubs | [`--dublang`](#dublang) | `defaultDubs` | none |
| Subtitles | [`--dlsubs`](#dlsubs) | `defaultSubs` | none |
| Subtitle format | [`--forcesubformat`](#forcesubformat) | `defaultSubtitleFormat` | `raw` |
| Output container | [`--outputformat`](#outputformat) | `DefaultOutputFormat` | `mkv` |
| Download path | [`--dlpath`](#dlpath) | `downloadPath` | current folder |
| Season | [`--season`](#season) | n/a | `1` |
| Full-listing workers | [`--workers`](#workers) | n/a | `1` |
