---
name: transcribe-and-archive-audio
description: Use when a user asks to transcribe and archive .m4a recordings stored on a NAS.
---

# Goal

Process `.m4a` files found directly under `/nas/record_aud/`. Save each verified transcript as a same-basename `.txt` file under `/nas/record_aud/transcribed/`. After all eligible transcripts are successfully saved, show a batch move plan and ask the user to confirm moving the corresponding source audio to `/nas/record_aud/archive/`.

# Required input

- Process the files present when the Skill is invoked; do not assume a background watcher.
- Accept an optional transcription language and speaker count.
- Use the fixed source, transcript, and archive roots above. Do not silently substitute another path.
- If a source file is inaccessible, still being written, or has a duplicate basename, report it and leave it in place.
- If a target transcript already exists, do not overwrite it; report the conflict and leave the source audio in place.

# Generation-time capability evidence

- `ug-filesystem` → `file-agent`
- `ug-knowledge` → `knowledge-agent`

`ug-filesystem` supports finding files by path, pattern, and audio type, checking file metadata, and moving a source to a destination. `ug-knowledge` supports audio transcription with optional language and speaker count, and saving text content with a filename hint.

- The catalog example for `audio-transcription` uses `.m4a`; actual backend availability and NAS permissions remain runtime concerns.

# Orchestration

Pass only real upstream results to downstream delegations. Never reconstruct paths, transcripts, statuses, or completion claims when an expert did not return them.

1. Delegate discovery to `file-agent` using the verified `ug-filesystem` capability. Find `.m4a` files directly in `/nas/record_aud/` and return verified real paths plus metadata. Do not search `transcribed` or `archive`. If no files are found, complete with an empty-result report.
   `delegate --target file-agent --instruction 'Find accessible .m4a files directly in /nas/record_aud/ and return verified real paths and metadata. Exclude transcribed and archive subdirectories, report an empty result without expanding scope, and flag files that appear inaccessible or still changing.'`
2. For each eligible source, delegate transcription to `knowledge-agent` using `ug-knowledge/audio-transcription`. Preserve the source path, optional language, and optional speaker count. Do not infer success from non-empty text; require the delegated expert to report `completed` with a verified transcript and no reported failures. A partial result, unresolved result, or reported failure is not eligible for saving or archiving.
   `delegate --target knowledge-agent --instruction 'Transcribe each verified eligible .m4a source path with the supplied optional language and speaker count. Return the exact source path, transcript reference or text, status, coverage, quality, warnings, and failures. Do not claim completed when the result is partial, unresolved, or has failures.'`
3. For each completed transcription, delegate saving to `knowledge-agent` using `save-text`, targeting `/nas/record_aud/transcribed/` with the original basename and a `.txt` suffix. Require the saved result to be verified, to report an output file ending in `.txt`, and to avoid overwriting an existing file. This write may require its own workflow approval.
4. Build one confirmation plan containing only sources whose transcript was completed and whose `.txt` output was verified. Show each exact source and archive destination, the count, and any files skipped with reasons. Wait for explicit user approval; this is a separate approval from transcript saving when required.
5. After approval, delegate the move to `file-agent` using the verified `ug-filesystem` move capability. Use `move`, never delete independently, and report per-file results. If approval is rejected, leave every source in place.

# Completion and failure

Report only verified results from delegated experts. Handle `completed`, `failed`, `cancelled`, `waiting_user`, and `waiting_approval` as distinct states. If no files match, report an empty result without claiming work was performed. If transcription or transcript saving fails, preserve the source file and continue only with independent files. Treat warnings as warnings, but never ignore reported failures or partial status. If a move partially fails, report every succeeded and failed path; never claim the batch completed when any required move is unresolved.
