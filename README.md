# EnglishTV

A curated, hand-tuned English-language IPTV playlist with a matching XMLTV guide file.

## Files

| File | Purpose |
|---|---|
| `EnglishTV.m3u` | The playlist. Point your IPTV player at this. |
| `EnglishTV.xml.gz` | Matching XMLTV guide (channel metadata; see [EPG](#epg) below for what it does and doesn't contain). |
| `channels.csv` | Full channel list in spreadsheet form — number, name, group, country, stream URL, etc. |
| `channel-map.csv` | Traceability notes on where individual channels came from / why they're in or out. |
| `processing-report.md` | Build notes from the most recent pass. |

## Sources

This playlist is assembled from two upstream sources, not written from scratch:

- **[iptv-org/iptv](https://github.com/iptv-org/iptv)** (`languages/eng.m3u`) — the general pool of English-language channels: US, UK, Canada, and international streams, plus the movie/drama/comedy/kids/documentary/news/sports/music category channels.
- **[i.mjh.nz/nzau](https://i.mjh.nz/nzau/raw-tv.m3u8)** — a curated, actively maintained feed of official Australian and New Zealand broadcaster and FAST-channel streams (Seven, Nine, Ten, ABC, SBS, Sky Open NZ, Three, and their spin-off/catch-up channels). This replaced the Australian entries that originally came from iptv-org, and is the sole source for the New Zealand entries.

Channels pulled from `i.mjh.nz` require a specific `User-Agent` header to play — this is preserved per-channel in the `.m3u` file as `#EXTVLCOPT` lines directly under the relevant `#EXTINF` line. A player that ignores VLC options may fail to play those specific streams; anything sourced from iptv-org doesn't need this.

## Channel ordering

Numbering roughly follows this shape, but it's a loose guide rather than a strict rule — channels are hand-arranged and interspersed rather than mechanically sorted, so don't expect clean category boundaries:

- **Start of the list** — Australian free-to-air channels, arranged to roughly resemble how they sit on an actual Australian TV tuner (ABC / SBS / Seven / Nine / Ten and their multichannels), with other channels (news, streaming add-ons) mixed in between rather than strictly segregated.
- **~100 onward** — Sports channels.
- **After that** — British and American channels.
- **Then** — more general streaming/entertainment channels.
- **End of the list** — Music channels.

Numbers are **not recompacted** after channels are removed — gaps in the sequence are intentional (either a channel was culled and the slot left empty, or deliberately reserved) rather than a sign something broke.

## What's been removed

- **Geo-restricted streams**: a large number of channels were dropped because they're geo-blocked and won't actually play from an Australian connection. Keeping them in the list would just mean dead entries.
- **Religious, shopping, and government/legislative channels**: filtered out categorically from the upstream sources.
- **Offline/unreliable feeds**: channels flagged as not broadcasting 24/7, or with broken/placeholder streams in the source data.
- **Hyperlocal public-access channels**: small-market US community/council-access stations that add clutter without real value in a general package.
- **Duplicate/near-duplicate streams**: where the same channel was offered multiple times (different quality tags, mirrored feeds, or the same show on two different underlying provider feeds), only the best single version was kept.

## EPG

`EnglishTV.xml.gz` is a valid XMLTV file, but it's worth understanding exactly what it does and doesn't give you:

- It contains a `<channel>` entry (id, display name, logo) for **every** channel in the playlist, matched to the same `tvg-id` used in the `.m3u`. Loading it in your player should get logos and channel names to display correctly, and it's what your player needs to know which guide entries belong to which channel.
- It does **not** contain real programme/schedule data (no "what's on now/next") for most channels. A genuinely complete, freely-hosted programme guide that matches this exact channel set doesn't exist — the project that used to generate one (`iptv-org/epg`) stopped publishing pre-built guides, and building one from scratch requires running a scraper against dozens of individual TV-guide websites.

**For real programme data on the Australian/New Zealand channels specifically**, add a **second** EPG source in your player pointing at:

```
https://i.mjh.nz/nzau/epg.xml.gz
```

This is published by the same source as the AU/NZ streams and uses the same channel IDs, so no remapping is needed — most players support adding more than one XMLTV URL and will merge them automatically.

For the remaining (mostly US/UK/CA) channels, partial third-party guide coverage exists through providers like TV Guide, Sky, and Freeview, but requires running the `iptv-org/epg` grabber yourself against a scoped channel list — this isn't something a hosted file can provide out of the box.

## Using this playlist

Point your player at the raw file URLs (not the GitHub page URL — that's an HTML wrapper, not the file itself):

```
https://raw.githubusercontent.com/<your-username>/<your-repo>/main/EnglishTV.m3u
https://raw.githubusercontent.com/<your-username>/<your-repo>/main/EnglishTV.xml.gz
```

If your player has a "catch-up/archive/timeshift" setting, leave it set to **None** — none of these streams support catch-up playback, and forcing a timeshift protocol will just break playback rather than enable anything.
