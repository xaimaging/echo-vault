# Uliya Skill Kit

This repository contains the `transcribe-and-archive-audio` Skill for UGREEN NAS. It processes audio recordings on demand; it does not watch the folder continuously.

## Purpose

The Skill finds accessible `.m4a` recordings directly inside `/nas/record_aud/`, transcribes them, saves verified transcripts as `.txt` files, and archives the original audio only after explicit user confirmation.

## Skill location

```text
transcribe-and-archive-audio/
    SKILL.md
```

## Example request

```text
Transcribe the new .m4a recordings in /nas/record_aud/.
```

The Skill may accept a transcription language and expected speaker count. It ignores the `transcribed/` and `archive/` subdirectories, skips inaccessible or changing files, and does not overwrite an existing transcript.

## Processing flow

1. Find eligible `.m4a` files in the source directory.
2. Transcribe each file and require a completed result without reported failures.
3. Save a verified same-basename `.txt` file in `/nas/record_aud/transcribed/`.
4. Show one confirmation plan containing only successfully saved transcripts.
5. After confirmation, move the corresponding audio files to `/nas/record_aud/archive/`.

Example confirmation:

```text
Ready to archive 2 files:
/nas/record_aud/recording-001.m4a -> /nas/record_aud/archive/recording-001.m4a
/nas/record_aud/recording-002.m4a -> /nas/record_aud/archive/recording-002.m4a
Confirm move? (yes/no)
```

Transcript saving and audio moving may require separate approvals.

## Safety and edge cases

Files are not moved when transcription or transcript saving fails, is partial, or has an unresolved result. Existing `.txt` files, duplicate basenames, inaccessible files, and recordings that appear to be changing are reported and left in the source folder. Rejected confirmations leave source audio in place. Partial moves are reported per file rather than being claimed as complete. Audio recordings may contain personal or confidential information, so apply appropriate access, retention, and consent policies.

## Validation

Run the bundled static checker from the repository root:

```powershell
node uliya-skill-kit/scripts/check-skill.mjs transcribe-and-archive-audio
```

A static pass verifies the Skill structure and declared capability mappings; it does not prove runtime access, backend availability, or execution on a real NAS.
