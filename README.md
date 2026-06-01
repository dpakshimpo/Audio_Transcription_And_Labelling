# Audio_Transcription_And_Labelling
Audio transcription and labelling using OpenAI WhisperX and Pyannaudio DIarization pipeline

# Audio Transcription with WhisperX and Speaker Diarization

This repository documents a practical workflow for transcribing multi-speaker counselling practice audio using **WhisperX** for transcription/alignment and **pyannote.audio** for speaker diarization.

The use case here is a mock career-counselling session between a counsellor and a student, where transcript fidelity and speaker separation are important for certification/evaluation submission.

---

## What This Workflow Does

The pipeline performs the following steps:

1. Uploads a WAV audio file.
2. Verifies that the file is suitable for diarization.
3. Transcribes the audio using WhisperX.
4. Aligns words to timestamps internally.
5. Runs speaker diarization using pyannote.audio.
6. Assigns speaker labels to transcript segments.
7. Exports a clean transcript without timestamps, in this format:

```text
Speaker 1: Good morning. How are you today?

Speaker 2: Good morning ma'am. I am doing well.
```

Speaker labels are machine-generated and should be manually verified before final submission.

---

## Environment Used

The workflow was tested on:

- Google Colab
- Python 3.12
- Tesla T4 GPU
- WhisperX
- pyannote.audio 4.x
- Hugging Face token with access to gated pyannote models

---

## Important Notes

### Whisper vs Diarization

Whisper/WhisperX transcribes speech into text. It does **not** inherently know who is speaking.

Speaker identification is done separately using **pyannote.audio diarization**, and then WhisperX maps the diarized speaker regions back to the aligned transcript.

### Hugging Face Access

pyannote diarization models are gated. You must:

1. Create a Hugging Face account.
2. Create a read-access token.
3. Accept model usage terms for:
   - `pyannote/speaker-diarization-3.1`
   - `pyannote/segmentation-3.0` if prompted

If access is not granted, the pipeline may fail with a `403 Forbidden` or `GatedRepoError`.

### pyannote 4.x Compatibility

In pyannote.audio 4.x, the diarization pipeline returns a `DiarizeOutput` object. WhisperX expects a dataframe-like object with:

```text
start | end | speaker
```

So the diarization output must be converted before calling:

```python
whisperx.assign_word_speakers(...)
```

---
