# House style (read first, every run)

This is the creator's own editing style, set after the first real test edit ("Side Quest to $1,000", episode 1).
It **overrides** anything in SKILL.md, looks.md, layout.md or the template build.py that conflicts with it.

The one-line brief: **the creator's natural on-camera delivery is the engagement. The edit polishes a strong talking-head
performance like a talented human editor would. It does not manufacture movement or show off every technique.**

What went wrong in the first test (do not repeat): 16 shot changes in 60s with automatic wide/close alternation,
1.4x digital punch-ins on 1080p footage (soft), tracked-caps captions on every single line, serif punch words on
~20 moments, and a caption silently dropped because the transcription was unsure.

## Shot frequency
- About **6-10 meaningful shot changes per ~60s** of talking head (scale with length). 15-20 is too many.
- **Never alternate wide/close automatically.** Two segments in a row from the same angle is fine: a jump cut at a
  natural pause is a normal, honest edit. Do not punch in just to hide it.
- A punch-in has to earn its place: an important statement, a joke, a number, a transition or an emotional beat.
  Write the reason next to it in edl.json (`"why": "number: $1,000"`). No reason, no punch-in.
- **Zoom limits by source resolution** (check with ffprobe before writing the EDL):
  - 1080p source (1080x1920): max zoom **1.1-1.2**. More only if the creator explicitly approves it for that reel.
  - 4K source (2160x3840): reframing up to ~1.9 is fine, still only on beats that earn it.
- Footage that is already a different shot (a handheld selfie close-up, a new setup) counts as a shot change.

## Pacing
- Keep the creator's natural pauses and speaking cadence. A beat before a punchline or a breath between ideas stays.
- Remove: mistakes, false starts, repeated ideas, a restarted take, and genuinely dead air (roughly 0.8s+ of
  nothing that is not a deliberate beat, walking into frame, fiddling with the mic).
- Do not tighten every gap to 0.1s. No rapid TikTok-style compression of the speech.
- When a pause sits inside a phrase ("giving myself 30 ... days"), trim it only if it is clearly a stumble.

## Captions (normal dialogue)
- Clean, natural, highly readable captions for everything said: sentence case, **Inter 600 ~58px**, white
  `#F7F4EE` with a soft dark shadow, max 2 lines, ~5-7 words per chunk, centred in the lower band
  (top of block around y 1230-1300, never below 1470, clear of the right icon column from y 1155).
- Chunks follow phrases, not single words. A whole chunk fades in quickly (.12-.18s); no per-word track-in, no
  scaleX/blur animation, no karaoke highlighting by default.
- During a close-up whose face reaches the lower band, move the chunk to the top band (y ~240-330) instead.
- Captions say what was actually said, fixed for mishearings (use the vocabulary below). Never silently omit a
  caption because the transcription is unsure: resolve it in the preflight.

## Special typography (rare on purpose)
- **2-4 major moments per video**, not more. Examples: "I DID IT", "$1,000", "DAY 4", "I'M GIVING UP".
- These get the stylized treatment: cool dude serif (DM Serif Display, italic allowed) or bold caps, the masked
  letter rise, and behind-the-head placement (layout.md formula) when the head position allows it.
- Pick them from the story (the hook, the stakes number, the twist, the sign-off), list them in the plan with
  their times, and hide the normal caption while a special moment owns the screen.
- Tracked Montserrat caps are an accent for those moments (a small label above/below the big word), not a
  caption style.

## Look
- Keep the cool dude darker cinematic grade and the lifted cutout (subject separation) when the footage suits it.
- Light sweep: at most one, on the open. Slow push-in only on shots that run longer than ~4s, max scale 1.03.
- Blur-to-focus only into a genuinely different shot (e.g. the handheld selfie), not on every cut.
- Subtle sound design (whoosh on the 2-4 special moments and real transitions only, SFX_GAIN 0.75).
- Instagram/TikTok safe zones as in layout.md.
- Normalize audio on the final: `loudnorm=I=-15:TP=-3:LRA=11` then `alimiter=limit=0.6`, target peaks -3 to -5 dB.

## Transcription preflight (before ANY render)
1. Transcribe (ingest.py, transcribe_cut.py). Both use `SK/vocabulary.txt` as the Whisper prompt and write every
   word's confidence; `transcript.txt` and `work/uncertain.txt` list the low-confidence words.
2. Review every uncertain word or phrase. Re-transcribe the snippet with medium.en and the vocabulary prompt.
3. If an uncertain phrase is a name, number, date, brand or anything that materially changes the edit (a caption,
   a special moment, a cut point), **ask the creator before rendering**, quoting the timestamp and the candidates
   ("0:14 'in exactly 30 days, WoW Forever drops' - is that right?"). Batch all questions into one message.
4. Minor uncertain filler words: use the best reading and list them in the delivery note.

## Project vocabulary (spellings to prefer when they fit the audio)
Kept in `SK/vocabulary.txt` (one term per line, add new ones there):
Side Quest to $1,000 · WoW Forever · World of Warcraft · Azeroth · UGC · Etsy · Garett · November 4

## Render workflow (rendering here is slow: ~26 min for 60s full quality on a 4-core CPU)
Settle the creative structure before the expensive render:
1. Transcription complete.
2. Uncertainties resolved (preflight above).
3. Final cut order (edl.json) posted as a table, with the shot-change count and every punch-in's reason.
4. Caption placement decided (lower band / top band per shot).
5. The 2-4 special typography moments decided and listed.
6. Snapshots (cheap) to check layout and safe zone, then a **draft preview** render:
   `HF render -o renders/<slug>-draft-vN.mp4 --quality draft --fps 15 --sdr` (~14 min for 60s on a 4-core CPU, about half the full render) and send it as the review copy.
7. Only after the structure is approved (or the creator says go): the full-quality render
   `HF render -o renders/<slug>-vN.mp4 --quality high --sdr`, loudness pass, check_cuts.py, phone copy.
- **Batch revisions.** Collect all notes from a review into one round; do not re-render after each small change.
  If a fix is tiny and the rest is approved, say so and offer to fold it into the next batch.

## Series element: revenue / progress tracker (defined, not built yet)
The "Side Quest to $1,000" series needs a reusable tracker. Status: **specified only; no component exists yet in
the template**. Build it once, on the first episode that needs it, as `tracker()` in the project's build.py, then
move it into `assets/template/build.py` so every episode reuses it.
- Data from `<project>/series.json`: `{"series": "Side Quest to $1,000", "day": 4, "days": 30, "earned": 0,
  "goal": 1000}`.
- Look: small cool dude pill in the top band (y ~240): tracked caps `DAY 4 / 30` + `$0 / $1,000`, thin progress
  bar in butter yellow `#FAE67A`, cream text, dark translucent background. Quiet, not a special moment.
- Shows for ~3s near the open and again whenever money or the day count comes up. Animates the bar from the
  previous episode's value to the new one when the number changes.
- Confirm the day and amount with the creator every episode; never guess revenue numbers.
