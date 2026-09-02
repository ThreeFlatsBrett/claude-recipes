# Weekly Music Discovery → Spotify Playlist

A setup recipe for having Claude find new music every week and build it into a Spotify playlist automatically.

Fill in the `«placeholders»` with your own preferences, then paste the finished prompt into a Claude scheduled task.

---

## What you need

- **Claude** with the **Spotify** connector enabled (requires a Spotify Premium account)
- Web search enabled (for reading music press)
- Optional but recommended: **memory** enabled, so Claude remembers your taste between runs

---

## Step 1 — Decide your preferences

Work through these eight choices. Your answers become the placeholders in Step 2.

### 1. Sources — where the picks come from

Pick any combination:

| Source | Good for |
|---|---|
| **Pitchfork reviews** | High signal, score-filterable. Narrow and slow-moving. |
| **Spotify new releases** | Broadest coverage of what actually dropped Friday. |
| **Indie music press** (Stereogum, Aquarium Drunkard, NPR New Music Friday, Paste) | Genres Pitchfork skips — especially Americana, folk, psych. |
| **Live/tour signals** | Finds artists before the press does. Opening acts, local venues. |

> **`«SOURCES»`** = _______________

### 2. What counts as "new"

- **Released this week** — strictly fresh. Some weeks are thin.
- **New to you, any age** — a 1974 record counts. Much better hit rate.
- **Mostly new, some catalog** — lead with the week's drops, backfill when the week is weak.

> **`«NEW_DEFINITION»`** = _______________

### 3. Playlist structure

- **One rolling playlist** — same list refreshed weekly, one link in rotation.
- **New playlist each week** — dated, archived forever, you can revisit a good week.
- **Rolling + monthly keepers** — weekly list, plus you promote what stuck to a monthly playlist.

> **`«STRUCTURE»`** = _______________

### 4. Artists you already love

- **Discovery only** — nothing you already listen to.
- **Include their new releases** — you never miss a drop from a favorite.
- **Adjacent but not familiar** — sounds like your favorites, isn't your favorites.

> **`«FAMILIAR_ARTISTS»`** = _______________

### 5. Length

- **10–12 tracks (~45 min)** — one sitting. Forces selectivity.
- **18–20 tracks (~75 min)** — room to take swings.
- **25–30 tracks (~2 hr)** — deep dig, skim for keepers.

> **`«LENGTH»`** = _______________

### 6. Tracks per artist

- **One each** — max variety.
- **Two if the record is strong** — signals which albums deserve a full listen.

> **`«PER_ARTIST»`** = _______________

### 7. Approval

- **Send the list, then build** — you cut picks before the playlist exists.
- **Just build it** — playlist appears, with notes.
- **Build it, notes on request** — playlist appears silently.

> **`«APPROVAL»`** = _______________

### 8. Guardrails

List genres to hard-exclude. Common ones: hyperpop / harsh electronic, hip-hop, hardcore / metal / noise, pop country, jam bands, jazz fusion.

> **`«EXCLUSIONS»`** = _______________

### Plus the basics

> **`«YOUR_NAME»`** = _______________
> **`«YOUR_TASTE»`** = e.g. "indie rock, alternative, and Americana; leans chill and atmospheric" _______________
> **`«FAVORITE_ARTISTS»`** = 3–5 artists that anchor your taste _______________
> **`«DAY_AND_TIME»`** = e.g. "Fridays at 7am Central" _______________
> **`«PLAYLIST_NAME_FORMAT»`** = e.g. `New Music — <Mon D, YYYY>` _______________

---

## Step 2 — The scheduled task prompt

Ask Claude to create a scheduled task at **`«DAY_AND_TIME»`** with this as the prompt. Every run starts a fresh session, so the prompt has to be fully self-contained.

```text
Build «YOUR_NAME»'s weekly new-music Spotify playlist. Run this end-to-end
without asking anything — «YOUR_NAME» is not watching.

STEP 1 — Read memory first. Check the stored music preferences file for taste
and spec, and read the running picks log so you do not repeat artists or
albums from the last 3-4 weeks. If no picks log exists yet, proceed — you will
create it in STEP 6.

STEP 2 — Research (do this BEFORE touching any Spotify tool). Sources, in
priority order:
«SOURCES»

Useful notes:
- pitchfork.com blocks automated fetching — use
  albumoftheyear.org/ratings/1-pitchfork-highest-rated/<year>/1 and web
  search instead
- stereogum.com/category/reviews/album-of-the-week — Album Of The Week archive
- stereogum.com/category/lists/the-5-best-songs-of-the-week
- aquariumdrunkard.com — best source for Americana/folk/psych
- albumoftheyear.org/releases/ — what actually dropped this week

STEP 3 — Curate «LENGTH».
Taste: «YOUR_TASTE». Favorites include «FAVORITE_ARTISTS».
"New" means: «NEW_DEFINITION». Do not pad with weak current releases just
because they are current.
Tracks per artist: «PER_ARTIST».
Artists already in rotation: «FAMILIAR_ARTISTS».
HARD EXCLUSIONS: «EXCLUSIONS».

STEP 4 — Build it. Use the Spotify search tool to confirm each album exists
on Spotify, then the create-playlist tool. IMPORTANT: the create tool takes a
natural-language prompt, not track IDs — so name every artist and album
explicitly in that prompt and specify what kind of track to pull from each
(e.g. "the strongest atmospheric cut"). Name the playlist exactly
"«PLAYLIST_NAME_FORMAT»" using this week's date.

STEP 5 — Report back. Send the playlist link plus a one-line note per pick:
artist, album, and why it made the cut (source and score where relevant).
Flag any album you could not find on Spotify.

STEP 6 — Write the picks back to memory. Append this week's artists and albums
to the picks log, with the date. STEP 1 reads this next week — the anti-repeat
check only works if every run records what it chose.
```

Adjust STEP 5 to match your **`«APPROVAL»`** choice — if you picked "send the list, then build," move the report to before STEP 4 and have Claude wait.

---

## Step 3 — Run a trial week first

Before letting it run unattended, ask Claude to do one pass live. You'll find out immediately whether the curation matches your taste, and you can correct the spec while the reasoning is still in front of you. Calibrating on a real list beats guessing at the config.

---

## Known limitations

**Spotify's playlist tool takes a natural-language description, not specific track IDs.** You name the artist and album; Spotify picks the song. So the *records* are hand-curated but the *cuts* are not fully under your control. Naming the album explicitly and describing the kind of track you want gets close. If a track choice is wrong, ask for a swap.

**Your connector may not be able to read back playlists it already built.** Check what the Spotify connector actually exposes — if there's no way to list existing playlists, an anti-repeat instruction phrased as "check my recent playlists" has no tool to run on and will quietly do nothing. That's why STEP 6 keeps the history in memory instead.

---

## Tips

- **Store your spec in Claude's memory**, not just the task prompt. Then any Claude session knows your taste, and you can refine it conversationally ("stop giving me so much ambient") without editing the scheduled task.
- **Make the anti-repeat check write, not just read.** Every run starts a fresh session, so a run that reads prior picks but never records its own has nothing to compare against next week — you'll see the same well-reviewed record three weeks running. That's the STEP 1 / STEP 6 pair.
- **Grade the first few weeks out loud.** "Track 4 was great, tracks 7 and 9 were noise" tunes the filter faster than any amount of upfront configuration.
- **Watch for daylight saving.** Scheduled tasks run on a fixed UTC schedule, so a 7am task drifts to 6am (or 8am) when the clocks change. Ask Claude to shift it twice a year, or just live with the hour.
- **Turn on push notifications** for the task so you know when the playlist is ready.

---

## A worked example of the choices

For reference, one filled-in configuration:

- **Sources:** Pitchfork reviews — Best New Music and anything scored 8.0+ — plus indie music press
- **New:** new to me, any age; a 1974 record counts if it surfaced this week in the press
- **Structure:** new playlist each week
- **Familiar artists:** include their new releases, don't filter them out
- **Length:** 10–12 tracks (~45 min)
- **Per artist:** one each; two only if the record is genuinely strong
- **Approval:** just build it
- **Exclusions:** hyperpop/harsh electronic, hip-hop, hardcore/metal/noise, pop country
- **Taste:** indie rock, alternative, Americana; chill and atmospheric
- **Anchor artists:** Temple of the Dog, Wilco, U2
- **Timing:** Fridays 7am Central
- **Name format:** `New Music — <Mon D, YYYY>` (e.g. `New Music — Sep 4, 2026`)
- **Picks log:** appended to the same memory file the spec lives in, so STEP 6 has somewhere to write

Two things this configuration got wrong at first, both now fixed in the prompt above:

- **The anti-repeat check was a no-op.** STEP 1 read prior picks, but no step wrote them, so there was never anything to read. That's what STEP 6 is for.
- **"Strongest atmospheric cut" was applied to every record.** It made each week converge on one texture. Vary the ask per album — the best song on a given record often isn't its most atmospheric one.
