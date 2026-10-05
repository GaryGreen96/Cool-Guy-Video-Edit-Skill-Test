# Cool Guy video-edit skill

This repo is the `/video-edit` skill (SKILL.md at the root; `.claude/skills/video-edit` links back here).

- Before editing any reel, read `references/house-style.md`: the creator's own defaults (restrained edit, ~6-10 shot
  changes per minute, 1.1-1.2x max zoom on 1080p, clean captions, 2-4 special typography moments, natural pacing,
  transcription preflight, draft render before the full-quality one, batched revisions). It overrides the busier
  example in `assets/template/build.py`.
- Project names and spellings live in `vocabulary.txt`; the transcription scripts feed it to Whisper. Add new
  names there.
- Edit projects (`reel-edits/`) hold large media and are not committed.
