---
name: ai-voice-audio
description: Generate voice narration, music, and sound effects using ElevenLabs API. Text-to-speech with V3 model (recommended), voice cloning, multilingual support, music generation, SFX creation. Use when the user wants to create voiceovers, narrate videos, generate background music, create sound effects, clone a voice, or add audio to any project. Works with ai-video-editor for adding narration to videos.
allowed-tools: Bash, Read, Write, Glob
---

# AI Voice & Audio (ElevenLabs)

Generate professional voice narration, music, and sound effects — directly from Claude Code.

## Setup

1. **Get your API key** at [elevenlabs.io](https://elevenlabs.io) → Profile → API Keys
2. Set it:
   ```bash
   # Windows (PowerShell)
   $env:ELEVEN_API_KEY="your-api-key-here"

   # macOS / Linux
   export ELEVEN_API_KEY=your-api-key-here
   ```

**Pricing:** Free tier gives ~10,000 characters/month. Starter plan ($5/mo) gives 30,000. Enough for learning.

## Models — Use V3 (Recommended)

| Model | ID | Best For |
|-------|-----|----------|
| **Eleven V3** ⭐ | `eleven_v3` | **Best quality. Use this.** Natural, expressive, all languages. |
| Multilingual V2 | `eleven_multilingual_v2` | Good quality, 29 languages |
| Flash V2.5 | `eleven_flash_v2_5` | Real-time apps, ~75ms latency |
| Turbo V2.5 | `eleven_turbo_v2_5` | Speed + quality balance |

**Rule: Always default to `eleven_v3` unless the user specifically needs low latency.**

---

## Text-to-Speech (TTS)

### Step 1: Pick a Voice

List available voices:
```bash
curl -s "https://api.elevenlabs.io/v1/voices" \
  -H "xi-api-key: $ELEVEN_API_KEY"
```

**Recommended premade voices:**
| Voice | Style | ID |
|-------|-------|-----|
| Roger | Laid-back, casual | `CwhRBWXzGAHq8TQ4Fs17` |
| Sarah | Mature, confident | `EXAVITQu4vr4xnSDxMaL` |
| Charlie | Deep, energetic | `IKne3meq5aSn9XLyUdCD` |
| George | Warm storyteller | `JBFqnCBsd6RMkjVDRZzb` |
| Laura | Enthusiastic | `FGY2WhTYpPnrIDTdsKH5` |

### Step 2: Generate Speech

```bash
curl -X POST "https://api.elevenlabs.io/v1/text-to-speech/CwhRBWXzGAHq8TQ4Fs17" \
  -H "xi-api-key: $ELEVEN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Your text here. This will be spoken naturally.",
    "model_id": "eleven_v3",
    "voice_settings": {
      "stability": 0.5,
      "similarity_boost": 0.75,
      "style": 0.3,
      "use_speaker_boost": true
    }
  }' \
  --output ./output/narration.mp3
```

### Voice Settings Guide

| Setting | Range | Low Value | High Value |
|---------|-------|-----------|------------|
| **stability** | 0–1 | More expressive, emotional | More consistent, monotone |
| **similarity_boost** | 0–1 | More variation | Closer to original voice |
| **style** | 0–1 | Neutral delivery | Exaggerated style |
| **speed** | 0.7–1.2 | Slower | Faster |

**Quick presets:**
- **Narration/Podcast**: stability 0.5, similarity 0.75, style 0.2
- **Marketing/Ad**: stability 0.3, similarity 0.8, style 0.5
- **Calm/Meditation**: stability 0.8, similarity 0.7, style 0.1
- **Character/Acting**: stability 0.2, similarity 0.6, style 0.7

---

## Voice Cloning (Best Results)

Clone your own voice or a client's voice for consistent, personalized narration.

### Instant Clone (quick, from a short sample)

```bash
# Upload a voice sample (at least 30 seconds of clean speech)
curl -X POST "https://api.elevenlabs.io/v1/voices/add" \
  -H "xi-api-key: $ELEVEN_API_KEY" \
  -F "name=My Voice" \
  -F "description=My cloned voice for course narration" \
  -F "files=@voice-sample.mp3"
```

The response includes a `voice_id` — use it in TTS requests like any other voice.

### Tips for Better Voice Cloning

1. **Record 1-3 minutes** of clear speech (no background noise)
2. **Speak naturally** — don't read robotically
3. **Use a good microphone** — phone is OK, but a USB mic is better
4. **Quiet environment** — no AC, fans, or background chatter
5. **Multiple samples** — upload 2-3 different recordings for better results
6. **Consistent tone** — use the same energy level across samples

### Use Your Cloned Voice

```bash
curl -X POST "https://api.elevenlabs.io/v1/text-to-speech/YOUR_CLONED_VOICE_ID" \
  -H "xi-api-key: $ELEVEN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "This sounds exactly like me, but AI is reading it.",
    "model_id": "eleven_v3",
    "voice_settings": {
      "stability": 0.5,
      "similarity_boost": 0.85
    }
  }' \
  --output ./output/my-voice.mp3
```

**Higher similarity_boost (0.8-0.9)** for cloned voices — makes it sound more like the original.

---

## Music Generation

```bash
curl -X POST "https://api.elevenlabs.io/v1/music-generation" \
  -H "xi-api-key: $ELEVEN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Calm lo-fi hip hop beat with vinyl crackle, soft piano, and light drums",
    "duration_seconds": 30,
    "prompt_influence": 0.5
  }' \
  --output ./output/background-music.mp3
```

### Music Prompt Examples

| Mood | Prompt |
|------|--------|
| **Corporate/Tech** | "Clean modern corporate music, light synths, inspiring, upbeat" |
| **Podcast intro** | "Short energetic podcast intro, electronic beats, 10 seconds" |
| **Calm/Focus** | "Ambient lo-fi music, soft piano, rain sounds, study vibes" |
| **Epic/Cinematic** | "Cinematic orchestral piece, dramatic build-up, strings and brass" |
| **Social media** | "Trendy upbeat electronic music, catchy, Instagram reel energy" |

---

## Sound Effects (SFX)

```bash
curl -X POST "https://api.elevenlabs.io/v1/sound-generation" \
  -H "xi-api-key: $ELEVEN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Notification chime, modern and clean, tech app style",
    "duration_seconds": 2.0,
    "prompt_influence": 0.3
  }' \
  --output ./output/notification.mp3
```

### SFX Prompt Examples

| Type | Prompt |
|------|--------|
| **UI/App** | "Soft click sound, modern mobile app interface" |
| **Transition** | "Swoosh transition sound, smooth and fast" |
| **Success** | "Achievement unlock sound, positive chime, game style" |
| **Ambient** | "Coffee shop background noise, gentle chatter and cups" |
| **Nature** | "Ocean waves gently crashing on a beach, relaxing" |

---

## Integration with Video Editor (Day 3)

Generate narration, then add it to your video:

```bash
# Step 1: Generate narration
curl -X POST "https://api.elevenlabs.io/v1/text-to-speech/VOICE_ID" \
  -H "xi-api-key: $ELEVEN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"text": "Welcome to our product showcase...", "model_id": "eleven_v3"}' \
  --output narration.mp3

# Step 2: Add narration to video (using ai-video-editor / ffmpeg)
ffmpeg -i video.mp4 -i narration.mp3 \
  -filter_complex "[1:a]volume=1.0[narr];[0:a][narr]amix=inputs=2:duration=first" \
  -c:v copy output.mp4
```

Or just tell Claude Code:
```
Generate a voiceover saying "Welcome to our product..." using the Roger voice,
then add it to D:/Videos/product-demo.mp4 as narration
```

---

## Best Practices

1. **Always use V3 model** (`eleven_v3`) — best quality, all languages
2. **Clone your voice** for brand consistency — sounds way better than premade
3. **Keep text natural** — write how people talk, not how they write
4. **Break long text** into paragraphs — generates better pacing
5. **Test with a short sentence** before generating a full script
6. **Save voice_id** of your cloned voice — reuse it across projects
7. **Output to ./output/** directory to keep files organized
8. **Check your credits** at elevenlabs.io before large generations

## Common Use Cases

1. **Video narration** — voiceover for tutorials, product demos, courses
2. **Podcast intro/outro** — branded audio with your voice or a character
3. **Social media** — narrated reels, stories, TikToks
4. **Background music** — custom tracks for any project
5. **Sound design** — UI sounds, transitions, notifications
6. **Voice cloning** — consistent brand voice across all content
7. **Multilingual** — same voice, different languages (V3 supports 32+)
8. **Accessibility** — generate audio versions of written content
