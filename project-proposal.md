# EchoVault

**Project:** EchoVault — Safe Audio Transcription for UGREEN NAS  
**Skill:** `transcribe-and-archive-audio`

## 1. What does your Skill do?

EchoVault's `transcribe-and-archive-audio` Skill finds `.m4a` recordings on a UGREEN NAS, creates verified `.txt` transcripts, and moves only confirmed, successfully processed audio files to an archive folder.

## 2. Who is your target user, and what is their problem?

The target user is a NAS owner who stores voice recordings and needs searchable text without manually processing each file. Their problem is repetitive transcription and unsafe file cleanup.

## 3. What is your solution?

The Skill provides a controlled batch workflow: discover recordings, transcribe them, save same-basename text files, present a move plan, and request confirmation before archiving the original audio.

## 4. What is the main user flow?

The user asks the Skill to process `/nas/record_aud/`. The Skill finds eligible `.m4a` files, transcribes each one, saves verified output in `/nas/record_aud/transcribed/`, then asks for one confirmation before moving successful source files to `/nas/record_aud/archive/`.

## 5. How is your idea different?

It combines transcription with safe NAS lifecycle management. Common transcription tools stop after producing text; this workflow preserves traceability, prevents premature movement, handles partial batches, and requires explicit confirmation before changing file locations.

## 6. Which UGREEN AI NAS features does it use?

`ug-filesystem` finds audio files, checks paths and metadata, and moves files. `ug-knowledge/audio-transcription` produces transcripts and quality information. `ug-knowledge/save-text` writes verified `.txt` output. The workflow uses `file-agent` and `knowledge-agent` through the registered delegation model.

## 7. What is your technical approach?

The Skill uses fixed source, transcript, and archive paths, optional language and speaker-count inputs, and separate delegated stages. It requires completed transcription and verified text output before proposing a move. Transcript saving and moving remain approval-controlled. It never deletes source audio or claims success from partial results.

## 8. What have you built so far?

The EchoVault pre-test `transcribe-and-archive-audio/SKILL.md` is implemented and passes the local static checker with zero errors and warnings. The workflow has not yet been tested against a real UGREEN NAS, real `.m4a` files, or runtime permission and approval behavior.

## 9. How will you test it as a Finalist?

Core test 1: process several valid `.m4a` files and verify matching `.txt` files and a confirmation-based archive move. Core test 2: process a batch with different languages and one successful retry. Edge case: include an existing transcript and an audio file still being written; verify both remain in the source folder and are reported.

## 10. What are the known limits, risks, or dependencies?

Runtime support for `.m4a`, NAS permissions, transcription quality, and folder availability require hardware validation. Recordings may contain sensitive personal data, so access and retention policies are required. The Skill is on-demand, not a background watcher. No third-party runtime components are bundled.

## Disclosure

The Skill uses the UGREEN Skill Developer Package and its documented NAS capabilities. No external libraries, credentials, binaries, or personal data are included. OpenAI Codex was used as an authoring assistant; it is not a runtime dependency.
