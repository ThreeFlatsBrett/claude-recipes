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

Work through these choices. Your answers become the placeholders in Step 3.

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
- **New to you, any age** — an older record counts if it surfaced in the press. Much better hit rate.
- **Mostly new, some catalog** — lead with the week's drops, backfill when the week is weak.

> **`«NEW_DEFINITION»`** = _______________

Whichever you pick, exclude canonical radio staples. "New to you at any age" is meant to surface overlooked records, not the classic-rock canon — the test is radio ubiquity, not release date.

### 3. Playlist structure — not actually a choice

**A new playlist each week**, dated, so you can revisit a good week.

The connector can create playlists but not modify them, so a rolling list you refresh weekly isn't automatable. Re-check if yours ever gains an add-tracks action.

### 4. Artists you already love

- **Discovery only** — nothing you already listen to.
- **Include their new releases** — you never miss a drop from a favorite.
- **Adjacent but not familiar** — sounds like your favorites, isn't your favorites.

> **`«FAMILIAR_ARTISTS»`** = _______________

If any of your anchor artists are themselves radio staples, this choice collides with the no-canon rule above — one says include them, the other says exclude them, and the prompt is left to guess. Resolve it explicitly. The usual resolution: a **brand-new release** from a staple artist is eligible, a well-worn **catalog cut** from that same artist is not.

### 5. Length

- **10–12 tracks (~45 min)** — one sitting. Forces selectivity.
- **18–20 tracks (~75 min)** — room to take swings.
- **25–30 tracks (~2 hr)** — deep dig, skim for keepers.

> **`«LENGTH»`** = _______________

Treat the lower bound as a hard floor, not a target. Spotify drops picks it can't match, so a 10-track intent can ship as 7 — STEP 5 checks for exactly that. State the floor once and have the check refer to it. The prompt in Step 3 does this — STEP 5 says "fewer than the lower bound of «LENGTH»" rather than repeating the number — and it is worth keeping that way. Two copies of the same number drift, and a floor of 18 paired with a trigger of 10 is a verification step that passes the runs it exists to catch.

Then decide what happens on a thin week, because "hard floor" and "don't pad with weak releases" will eventually conflict:

- **Floor holds** — never ship short. Reach wider: more tracks from the strong records, older records that surfaced in the press, secondary sources.
- **Quality holds** — ship short and flag the count. The floor is really a target.
- **Lower the floor** — often the honest fix. A high floor combined with one-track-per-artist needs that many *distinct* artists clearing your exclusions every single week.

> **`«THIN_WEEK»`** = _______________

### 6. Tracks per artist

- **One each** — max variety.
- **Two if the record is strong** — signals which albums deserve a full listen.

> **`«PER_ARTIST»`** = _______________

This trades against your floor: one-each at a 20-track floor means finding 20 distinct artists a week. If you chose "floor holds" above, taking another track from a record that earns it is usually the cheapest way to reach the count.

### 7. Approval

- **Send the list, then build** — you cut picks before the playlist exists.
- **Just build it** — playlist appears, with notes.
- **Build it, notes on request** — playlist appears silently.

> **`«APPROVAL»`** = _______________

### 8. Guardrails

List genres to hard-exclude. Common ones: hyperpop / harsh electronic, hip-hop, hardcore / metal / noise, pop country, jam bands, jazz fusion.

> **`«EXCLUSIONS»`** = _______________

Genre exclusions collide with your own favorites more often than you would expect — a pop-country exclusion can quietly disqualify an artist you actively love. Name the artists that always survive the exclusions. Start with every artist in your taste list, since those are the ones you would most hate to lose to a genre label.

> **`«ALLOWLIST»`** = _______________

Scope this deliberately: it should override the genre list, not the no-canon rule. Otherwise an allowlisted staple readmits its own greatest hits.

### Plus the basics

> **`«YOUR_NAME»`** = _______________
> **`«YOUR_TASTE»`** = e.g. "indie rock, alternative, and Americana; leans chill and atmospheric" _______________
> **`«FAVORITE_ARTISTS»`** = 3–5 artists that anchor your taste _______________
> **`«DAY_AND_TIME»`** = e.g. "Fridays at 7am Central" _______________
> **`«PLAYLIST_NAME_FORMAT»`** = e.g. `New Music — <Mon D, YYYY>` _______________
> **`«MEMORY_PATH»`** = where the spec lives, e.g. `/topics/music.md` _______________

---

## Step 2 — Put your spec in memory

The task prompt reads your spec from memory instead of restating it, so you can refine your taste in any chat without editing the scheduled task. Set this up first — ask Claude in a normal conversation, not the task:

```text
Save this to memory at «MEMORY_PATH», replacing whatever is there:

# Music

## Taste
«YOUR_TASTE». Anchor artists: «FAVORITE_ARTISTS».

## Hard exclusions
«EXCLUSIONS»
No canonical radio staples from an artist's back catalog, however old. A
brand-new release from a staple artist is still fine.

## Always allowed
«ALLOWLIST» — never blocked by the hard exclusions above. Overrides the genre
list only; the no-canon rule still applies to them.

## Weekly playlist spec
- «LENGTH». «PER_ARTIST».
- "New" means: «NEW_DEFINITION».
- Familiar artists: «FAMILIAR_ARTISTS».
- Sources: «SOURCES».
- Name: "«PLAYLIST_NAME_FORMAT»", that Friday's date.

## Weekly playlist picks log (anti-repeat: do not reuse artists or albums from the last 3-4 weeks)
(One dated subheading per week, then artist — track — album for every track that
actually shipped. The Friday task appends here. Entries marked UNVERIFIED are
intended picks that could not be confirmed.)
```

Then ask Claude to read it back. If the path is wrong or the file is empty, STEP 1 of the task gets nothing and the anti-repeat check can't work — and it fails silently.

Keep the anti-repeat rule inside the heading rather than trimming it. The constraint then travels with the data, so it's in context every time the log is read.

---

## Step 3 — The scheduled task prompt

Ask Claude to create a scheduled task at **`«DAY_AND_TIME»`** with this as the prompt. Every run starts a fresh session, so the prompt has to be fully self-contained.

```text
Build «YOUR_NAME»'s weekly new-music Spotify playlist. Run this end-to-end
without asking anything — «YOUR_NAME» is not watching.

STEP 1 — Read memory first. Check the stored music preferences file for taste
and spec, and read the running picks log so you do not repeat artists or
albums from the last 3-4 weeks. If no picks log exists yet, proceed — you will
create it in STEP 7.

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

STEP 3 — Curate «LENGTH». The lower bound is a hard floor, not a target.
Taste: «YOUR_TASTE». Favorites include «FAVORITE_ARTISTS».
"New" means: «NEW_DEFINITION». Do not pad with weak current releases just
because they are current.
Old records qualify only if they genuinely surfaced in this week's press.
CANON RULE: exclude canonical radio staples from an artist's back catalog
regardless of age — the test is radio ubiquity, not release date. A brand-new
release from a staple artist is still eligible; a well-worn catalog cut from
that same artist is not.
Tracks per artist: «PER_ARTIST».
Artists already in rotation: «FAMILIAR_ARTISTS».
If the week is thin and the floor is not reachable on the above: «THIN_WEEK».
Name in STEP 6 which of those measures you had to use.
HARD EXCLUSIONS: «EXCLUSIONS».
ALWAYS ALLOWED: «ALLOWLIST». These artists are never blocked by the hard
exclusions. This overrides the genre list only — the CANON RULE still applies
to them, so a new release is eligible and a well-worn catalog cut is not.

STEP 4 — Build it. Use the Spotify search tool to confirm every pick exists on
Spotify first. If a pick cannot be confirmed, replace it with another
researched pick — never ship short.

Then call the create-playlist tool. It takes a natural-language prompt, not
track IDs, so make that prompt maximally explicit: a numbered list naming the
artist, the album, and the exact track for every entry, ending with an
instruction to include exactly those tracks and to add or substitute nothing.
Anything vaguer gets you the engine's guess instead of your curation.

Name the playlist exactly "«PLAYLIST_NAME_FORMAT»" using this week's date.

STEP 5 — Verify what actually shipped. Spotify substitutes and drops tracks,
so the playlist you asked for is not necessarily the playlist you got. Capture
the ACTUAL tracklist — from the create call's response, or by reading the
playlist back — and compare it against your intended picks.

Adjust and rebuild if any of these hold:
- fewer tracks than the lower bound of «LENGTH»
- anything from HARD EXCLUSIONS got in
- a back-catalog radio staple got in
- fewer than 70% of your intended picks are present

If you cannot retrieve the actual tracklist this run, mark the result
UNVERIFIED and say so. Never assume it matched.

STEP 6 — Report the actual playlist, not the intended one. Send the link, then
one terse line per real track: artist, track, album, why it made the cut
(source and score where relevant). Flag every substitution Spotify made, every
album you could not find, and any short count. If the run is UNVERIFIED, lead
with that.

STEP 7 — Log what actually shipped. Append the verified tracklist to the picks
log with the date — real artists, tracks and albums, not the intended picks.
STEP 1 reads this next week, so logging intent instead of reality silently
breaks the anti-repeat check. If the run is UNVERIFIED, log the intended picks
and label them UNVERIFIED.
```

Adjust STEP 6 to match your **`«APPROVAL»`** choice — if you picked "send the list, then build," move the report to before STEP 4 and have Claude wait.

---

## Step 4 — Run a trial week first

Before letting it run unattended, ask Claude to do one pass live. You'll find out immediately whether the curation matches your taste, and you can correct the spec while the reasoning is still in front of you. Calibrating on a real list beats guessing at the config.

---

## Known limitations

**Spotify's playlist tool takes a natural-language description, not specific track IDs.** You name the artist, album and track; the engine still substitutes, drops, and can come up short. The playlist you asked for is not reliably the playlist you get — which is why STEP 5 verifies the result instead of trusting it, and STEP 7 logs what shipped rather than what was intended.

**Verification depends on what the create call returns.** If your connector can't read a playlist back and the create response carries only a link, every run lands in the UNVERIFIED branch. Test that on one run before trusting the picks log.

**The connector may not be able to list playlists it already built.** An anti-repeat instruction phrased as "check my recent playlists" then has no tool to run on and silently does nothing — which is why STEP 7 keeps history in memory instead.

---

## Tips

- **Refine the spec conversationally.** Because it lives in memory (Step 2), "stop giving me so much ambient" in any chat carries into the next run — no need to edit the task.
- **Never trust the build.** The gap between the picks you chose and the tracks that shipped is where this recipe fails quietly. STEP 5 exists to close it.
- **Make the anti-repeat check write, not just read.** Each run starts fresh, so one that reads prior picks but never records its own has nothing to compare against next week. That's the STEP 1 / STEP 7 pair.
- **Grade the first few weeks out loud.** "Track 4 was great, tracks 7 and 9 were noise" tunes the filter faster than any amount of upfront configuration.
- **Watch for daylight saving.** Scheduled tasks run on a fixed UTC schedule, so a 7am task drifts to 6am (or 8am) when the clocks change. Ask Claude to shift it twice a year, or just live with the hour.
- **Turn on push notifications** for the task so you know when the playlist is ready.

---

## A worked example of the choices

For reference, one filled-in configuration:

- **Sources:** Pitchfork reviews — Best New Music and anything scored 8.0+ — plus indie music press
- **New:** new to me, any age, provided the record surfaced in this week's press
- **Structure:** new playlist each week
- **Familiar artists:** include their new releases, don't filter them out
- **Length:** 10–12 tracks (~45 min); 10 is a hard floor, not a target
- **Per artist:** one each; two only if the record is genuinely strong
- **Thin week:** floor holds — reach wider rather than ship short, taking extra tracks from the strong records first
- **Approval:** just build it
- **Exclusions:** hyperpop/harsh electronic, hip-hop, hardcore/metal/noise, pop country
- **Allowlist:** every artist in the taste list, so a genre label can't disqualify one of them
- **Taste:** indie rock, alternative, Americana; chill and atmospheric
- **Anchor artists:** The National, Big Thief, Fleet Foxes
- **Timing:** Fridays 7am Central
- **Name format:** `New Music — <Mon D, YYYY>` (e.g. `New Music — Sep 4, 2026`)
- **Picks log:** appended to the same memory file the spec lives in; records the tracks that actually shipped

Four things it got wrong at first, all fixed in the prompt above:

- **The anti-repeat check was a no-op** — STEP 1 read prior picks, but nothing wrote them. Hence STEP 7.
- **"Strongest atmospheric cut" on every record** made each week converge on one texture. Vary the ask per album.
- **A canonical staple slipped in.** "New to me at any age" needs an explicit no-canon guard.
- **The floor and the rebuild trigger drifted apart.** The length said 10 was the floor, but STEP 5's short-count check was left at a number from an earlier draft — so runs that missed the floor still passed verification. State the floor once and have the check refer to it.
