# Introduction to AI Speech Concepts — AI-900 Study Notes

## Overview

This document summarizes:
- speech-enabled AI solutions
- speech recognition
- speech synthesis
- Azure AI Speech capabilities

Optimized for:
- AI-900 certification study
- offline Markdown notes
- Obsidian/Git repositories
- rapid revision
- future flashcard generation

---
---

# Chapter 1 — Speech-Enabled Solutions

## Core Concept

Speech-enabled AI systems allow humans to interact naturally with software using spoken language.

Two foundational capabilities:
1. Speech recognition
2. Speech synthesis

Azure provides these through Azure AI Speech.

---

# Common Speech AI Scenarios

## Voice Assistants
Examples:
- smart speakers
- copilots
- mobile assistants

Pipeline:
1. Speech input
2. Speech-to-text
3. NLP/LLM processing
4. Text-to-speech response

---

## Automated Captioning

Used for:
- subtitles
- accessibility
- meeting transcription
- call center recordings

Important distinction:
- Speech recognition converts audio → text
- Translation is separate

---

## Accessibility Solutions

Examples:
- screen readers
- voice dictation
- hands-free interaction

---

## Real-Time Translation

Combines:
- speech recognition
- translation
- speech synthesis

Use cases:
- multilingual meetings
- support systems
- travel apps

---

# Azure AI Speech Capabilities

| Capability | Description |
|---|---|
| Speech-to-text | Audio → text |
| Text-to-speech | Text → audio |
| Speech translation | Spoken translation |
| Speaker recognition | Speaker identification |
| Custom speech | Domain-specific tuning |
| Custom neural voice | Branded voices |

---

# AI-900 Key Distinctions

| Capability | Input | Output |
|---|---|---|
| Speech recognition | Audio | Text |
| Speech synthesis | Text | Audio |
| Speech translation | Audio | Translated speech/text |

---

# Key Takeaways

- Speech AI enables natural interaction.
- Core capabilities:
  - STT
  - TTS
- Azure AI Speech supports:
  - transcription
  - synthesis
  - translation
  - speaker recognition
  - custom voices

---
---

# Chapter 2 — Speech Recognition

## Core Concept

Speech recognition converts spoken audio into machine-readable text.

Also called:
- speech-to-text (STT)

---

# Recognition Pipeline

Audio  
→ Acoustic Analysis  
→ Language Modeling  
→ Text Output

---

# Acoustic Model vs Language Model

## Acoustic Model
Analyzes:
- phonemes
- sounds
- pronunciation

## Language Model
Analyzes:
- grammar
- word probability
- sentence structure

Purpose:
Predict the most likely sentence.

---

# Recognition Challenges

| Challenge | Description |
|---|---|
| Accents | Pronunciation differences |
| Noise | Environmental interference |
| Multiple speakers | Speaker overlap |
| Domain vocabulary | Specialized terminology |
| Speaking speed | Fast/slow speech |

---

# Recognition Scenarios

## Real-Time Recognition
Examples:
- live captions
- assistants
- meetings

## Batch Transcription
Examples:
- podcasts
- recorded calls
- lectures

## Command Recognition
Examples:
- “Turn on lights”
- “Play music”

---

# Azure Speech Recognition Features

| Feature | Purpose |
|---|---|
| Real-time transcription | Live STT |
| Batch transcription | Offline processing |
| Conversation transcription | Multi-speaker meetings |
| Custom speech | Specialized tuning |
| Language detection | Spoken language detection |

---

# Important AI-900 Distinction

| Capability | Function |
|---|---|
| Speech recognition | Audio → text |
| NLP | Meaning extraction |

Speech recognition alone does NOT understand intent.

---

# Key Takeaways

- STT converts speech into text.
- Systems combine:
  - acoustic models
  - language models
  - deep learning
- Azure supports:
  - live transcription
  - batch transcription
  - custom speech

---
---

# Chapter 3 — Speech Synthesis

## Core Concept

Speech synthesis converts text into spoken audio.

Also called:
- text-to-speech (TTS)

Modern systems use:
- neural networks
- deep learning
- neural voice models

---

# Synthesis Pipeline

Text  
→ Linguistic Analysis  
→ Voice Modeling  
→ Audio Output

---

# Important Concepts

## Pronunciation
Determines how words should sound.

Challenges:
- acronyms
- names
- abbreviations

---

## Prosody

Controls:
- rhythm
- emphasis
- intonation
- pacing

Prosody is essential for natural speech.

---

# Neural Text-to-Speech

Advantages:
- realistic speech
- natural intonation
- smoother audio
- emotional expressiveness

Azure uses:
- neural voices

---

# SSML (Speech Synthesis Markup Language)

SSML controls:
- pauses
- emphasis
- speaking rate
- pitch
- pronunciation
- voice selection

---

# Common TTS Scenarios

## Voice Assistants
AI-generated spoken responses.

## Accessibility
Examples:
- screen readers
- read-aloud apps
- audiobook narration

## Automated Announcements
Examples:
- airports
- kiosks
- transportation systems

---

# Azure Speech Synthesis Features

| Feature | Purpose |
|---|---|
| Neural voices | Natural speech |
| Multilingual voices | Multiple languages |
| Custom neural voice | Branded voices |
| SSML support | Voice customization |

---

# Responsible AI Considerations

Risks:
- impersonation
- deepfake voices
- misinformation

Azure applies:
- governance
- restricted access
- responsible AI controls

---

# AI-900 Distinction

| Capability | Input | Output |
|---|---|---|
| Speech recognition | Audio | Text |
| Speech synthesis | Text | Audio |

---

# Key Takeaways

- TTS converts text into audio.
- Neural voices improve realism.
- SSML customizes generated speech.
- Responsible AI is important in synthetic voice systems.

---
---

# Official References

## Microsoft Learn

### Speech Solutions
https://learn.microsoft.com/en-us/training/modules/introduction-ai-speech/2-speech-solutions?pivots=text

### Speech Recognition
https://learn.microsoft.com/en-us/training/modules/introduction-ai-speech/3-speech-recognition?pivots=text

### Speech Synthesis
https://learn.microsoft.com/en-us/training/modules/introduction-ai-speech/4-speech-synthesis?pivots=text

## Azure AI Speech Documentation
https://learn.microsoft.com/en-us/azure/ai-services/speech-service/

## Text-to-Speech Documentation
https://learn.microsoft.com/en-us/azure/ai-services/speech-service/text-to-speech

## Speech-to-Text Documentation
https://learn.microsoft.com/en-us/azure/ai-services/speech-service/speech-to-text

## SSML Documentation
https://learn.microsoft.com/en-us/azure/ai-services/speech-service/speech-synthesis-markup

https://learn.microsoft.com/en-us/training/modules/recognize-synthesize-speech
