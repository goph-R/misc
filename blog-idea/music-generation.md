# AI Music Generation Guide for 6GB VRAM

## Overview

Yes! Several AI music generators work great with 6GB VRAM. This guide covers the best options for generating music on budget hardware.

---

## Music Generation Models for 6GB VRAM

### 1. Riffusion (Best for 6GB, Easiest)

**Based on Stable Diffusion 1.5!** - Since you already have SD running, this is perfect.

**VRAM:** ~2-3GB
**Speed:** ~10-30 seconds per clip on GTX 1660/RTX 4050
**Quality:** Good for ambient, background music, loops

**How it works:**
- Converts text to music spectrograms
- Uses SD 1.5 architecture
- Generates 5-10 second clips
- Can loop for longer tracks

**Setup:**
```bash
pip install riffusion
```

**Web interface:**
https://github.com/riffusion/riffusion

**Example prompts:**
```
"lo-fi hip hop beat, chill, relaxing"
"fantasy RPG music, orchestral, epic"
"8-bit chiptune, upbeat, retro game music"
"ambient piano, calm, peaceful"
```

**Perfect for:** VN background music, loops, ambient tracks

---

### 2. MusicGen by Meta (Best Quality)

**VRAM Requirements:**
- Small (300M): ~2GB VRAM - Fast, decent quality
- Medium (1.5B): ~4-5GB VRAM - Better quality
- Large (3.3B): ~8GB VRAM - Too big for 6GB
- Melody (1.5B): ~4-5GB VRAM - Can use melody as input

**Speed (Medium model, GTX 1660):** ~30-60 seconds for 30 sec clip
**Quality:** Excellent for local generation

**Setup:**
```bash
pip install audiocraft
```

**Python example:**
```python
from audiocraft.models import MusicGen
import torch

model = MusicGen.get_pretrained('facebook/musicgen-medium')
model.set_generation_params(duration=30)  # 30 seconds

descriptions = ['lo-fi hip hop beat with piano']
wav = model.generate(descriptions)

# Save to file
import torchaudio
torchaudio.save('output.wav', wav[0].cpu(), model.sample_rate)
```

**Perfect for:** Higher quality music, longer tracks

---

### 3. AudioLDM (Text-to-Audio & Music)

**VRAM:** ~4-5GB
**Speed:** ~20-40 seconds per clip
**Quality:** Good for both music and sound effects

**Setup:**
```bash
pip install audioldm
```

**Usage:**
```bash
audioldm -t "lo-fi music for studying" -s 42 -d 30
```

**Perfect for:** Sound effects + music combo

---

### 4. Bark (Sound Effects + Some Music)

**VRAM:** ~4-5GB
**Speed:** Fast
**Quality:** Primarily for TTS and sound effects, basic music

**Setup:**
```bash
pip install git+https://github.com/suno-ai/bark.git
```

**Perfect for:** Sound effects, voice, simple music elements

---

## VRAM Requirements Comparison

| Model | VRAM | Speed (30s clip) | Quality | Best For |
|-------|------|------------------|---------|----------|
| Riffusion | 2-3GB | 10-30s | Good | Loops, ambient |
| MusicGen Small | 2GB | 20-40s | Good | Fast generation |
| MusicGen Medium | 4-5GB | 30-60s | Excellent | Best balance |
| AudioLDM | 4-5GB | 20-40s | Good | Music + SFX |
| Bark | 4-5GB | Fast | Basic | SFX, voice |

---

## Recommended Setup for VN Development

### Option 1: Riffusion (Easiest)

Since you already have SD 1.5 running:
```bash
pip install riffusion
```

Generate 10-second loops, stitch them together for longer tracks.

**Pros:**
- Uses SD 1.5 (you already know this!)
- Very low VRAM
- Good for background loops
- Fast generation

**Cons:**
- Limited to short clips
- Not as high quality as MusicGen

### Option 2: MusicGen Medium (Best Quality)

```bash
pip install audiocraft
```

**Pros:**
- Best quality for 6GB VRAM
- Generates longer tracks (up to 30s, can extend)
- Very versatile
- Great prompt following

**Cons:**
- Slower than Riffusion
- Uses more VRAM

### Option 3: Use Both!

- **Riffusion** for quick loops and ambient sounds
- **MusicGen** for main theme/important tracks

---

## Example Music Generation Workflow

### For Visual Novel:

**1. Main Menu Music:**
```python
# MusicGen
prompt = "gentle piano melody, soft, welcoming, main menu music"
duration = 60  # 1 minute, loopable
```

**2. Background Music (School Scene):**
```python
# Riffusion
prompt = "cheerful acoustic guitar, light, daytime, slice of life"
# Generate 10s loop, repeat
```

**3. Dramatic Scene:**
```python
# MusicGen
prompt = "emotional violin and piano, melancholic, slow, dramatic"
```

**4. Sound Effects:**
```python
# AudioLDM
prompt = "door opening sound effect"
prompt = "footsteps on wooden floor"
```

---

## Web Interfaces Available

### Riffusion Web UI:

```bash
git clone https://github.com/riffusion/riffusion-app
cd riffusion-app
pip install -r requirements.txt
python -m riffusion.server
```

Access at `http://localhost:3000`

### AudioCraft (MusicGen) Web UI:

```bash
pip install audiocraft
python -m audiocraft.app
```

Or use Gradio interface:
```python
import gradio as gr
from audiocraft.models import MusicGen

model = MusicGen.get_pretrained('facebook/musicgen-medium')

def generate_music(prompt, duration):
    model.set_generation_params(duration=duration)
    wav = model.generate([prompt])
    return (model.sample_rate, wav[0].cpu().numpy())

demo = gr.Interface(
    fn=generate_music,
    inputs=[
        gr.Textbox(label="Prompt"),
        gr.Slider(5, 30, value=10, label="Duration (seconds)")
    ],
    outputs=gr.Audio(label="Generated Music")
)

demo.launch()
```

---

## Quality Expectations

### Riffusion:
- ✅ Great for: Loops, ambient, atmospheric
- ✅ Works well: Electronic, lo-fi, ambient, simple melodies
- ❌ Struggles with: Complex arrangements, vocals, very long pieces

### MusicGen Medium:
- ✅ Great for: Most genres, longer coherent pieces
- ✅ Works well: Orchestral, electronic, rock, jazz, acoustic
- ✅ Can do: Basic vocals (not lyrics, but vocalizations)
- ❌ Struggles with: Very specific/complex arrangements

### AudioLDM:
- ✅ Great for: Sound effects, environmental sounds
- ✅ Works well: Simple music, ambient
- ❌ Not as good as MusicGen for pure music

---

## Example Prompts for VN Music

### Background/Ambient:
```
"lo-fi hip hop beat, chill, calm, study music"
"soft piano melody, peaceful, gentle"
"acoustic guitar, warm, cozy cafe atmosphere"
"ambient synth pad, atmospheric, dreamy"
```

### Emotional Scenes:
```
"sad piano and strings, emotional, melancholic"
"uplifting orchestral music, hopeful, inspiring"
"tense strings, suspenseful, mysterious"
```

### Action/Energetic:
```
"upbeat electronic music, energetic, positive"
"fast-paced rock guitar, exciting"
"8-bit chiptune, retro game music, energetic"
```

### Specific Genres:
```
"jazz piano trio, smooth, sophisticated"
"classical piano sonata, elegant"
"synthwave, retro 80s, nostalgic"
```

### Sound Effects (AudioLDM):
```
"door opening sound effect"
"footsteps on wooden floor"
"rain falling on window"
"wind blowing through trees"
"keyboard typing sounds"
"phone notification beep"
```

---

## Quick Start: Riffusion

```bash
# Install
pip install riffusion

# Generate music
python
>>> from riffusion.spectrogram_params import SpectrogramParams
>>> from riffusion.riffusion_pipeline import RiffusionPipeline

>>> pipeline = RiffusionPipeline.from_pretrained("riffusion/riffusion-model-v1")
>>> pipeline = pipeline.to("cuda")

>>> prompt = "lo-fi hip hop beat, chill"
>>> audio = pipeline(prompt)

>>> audio.export("output.wav")
```

---

## Quick Start: MusicGen

```bash
# Install
pip install audiocraft

# Generate music
python
>>> from audiocraft.models import MusicGen
>>> import scipy

>>> model = MusicGen.get_pretrained('facebook/musicgen-medium')
>>> model.set_generation_params(duration=15)

>>> descriptions = ['lo-fi hip hop beat with piano, chill, relaxing']
>>> wav = model.generate(descriptions)

>>> for idx, one_wav in enumerate(wav):
>>>     scipy.io.wavfile.write(f"output_{idx}.wav", model.sample_rate, one_wav.cpu().numpy())
```

---

## Quick Start: AudioLDM

```bash
# Install
pip install audioldm

# Command line usage
audioldm -t "lo-fi music for studying" -s 42 -d 30 -o output.wav

# Python usage
from audioldm import text_to_audio, build_model

audioldm = build_model()
waveform = text_to_audio(
    audioldm,
    "lo-fi hip hop beat, chill",
    duration=10,
    guidance_scale=2.5
)
```

---

## Performance on GTX 1660 6GB

### Riffusion:
- 10 second clip: ~15-25 seconds generation time
- VRAM: ~2GB
- Can run alongside other tasks

### MusicGen Medium:
- 30 second clip: ~45-90 seconds generation time
- VRAM: ~4-5GB
- Best to run alone

### MusicGen Small:
- 30 second clip: ~30-45 seconds generation time
- VRAM: ~2GB
- Faster but lower quality

### AudioLDM:
- 10 second clip: ~20-30 seconds generation time
- VRAM: ~4GB
- Good balance of speed and quality

---

## Performance on RTX 4050 6GB

### Riffusion:
- 10 second clip: ~10-15 seconds generation time
- VRAM: ~2GB

### MusicGen Medium:
- 30 second clip: ~30-45 seconds generation time
- VRAM: ~4-5GB

### MusicGen Small:
- 30 second clip: ~15-25 seconds generation time
- VRAM: ~2GB

---

## Integration with Love2D

### Simple Music Playback:

```lua
-- main.lua

function love.load()
    -- Load AI-generated music
    bgm_menu = love.audio.newSource("music/menu_loop.wav", "stream")
    bgm_scene1 = love.audio.newSource("music/school_day.wav", "stream")

    -- Set looping
    bgm_menu:setLooping(true)
    bgm_scene1:setLooping(true)

    -- Play menu music
    bgm_menu:play()
end

function changeScene(scene)
    -- Stop all music
    love.audio.stop()

    -- Play scene-specific music
    if scene == "menu" then
        bgm_menu:play()
    elseif scene == "school" then
        bgm_scene1:play()
    end
end
```

### Cross-fade Between Tracks:

```lua
function crossfade(from_source, to_source, duration)
    local elapsed = 0

    function love.update(dt)
        elapsed = elapsed + dt
        local progress = math.min(elapsed / duration, 1.0)

        -- Fade out old track
        from_source:setVolume(1.0 - progress)

        -- Fade in new track
        to_source:setVolume(progress)

        if progress >= 1.0 then
            from_source:stop()
        end
    end

    to_source:play()
end
```

---

## Music Generation Workflow for VN

### Step 1: Plan Your Music Needs

**List scenes and moods:**
- Main Menu - Welcoming, gentle
- School Day - Cheerful, light
- Evening - Calm, peaceful
- Dramatic Scene - Tense, emotional
- Happy Ending - Uplifting, joyful

### Step 2: Generate with Riffusion (Quick Loops)

```python
from riffusion.riffusion_pipeline import RiffusionPipeline

pipeline = RiffusionPipeline.from_pretrained("riffusion/riffusion-model-v1")
pipeline = pipeline.to("cuda")

prompts = [
    ("menu", "gentle piano melody, soft, welcoming"),
    ("school_day", "cheerful acoustic guitar, light, upbeat"),
    ("evening", "calm ambient music, peaceful"),
    ("dramatic", "tense strings, suspenseful"),
    ("happy_ending", "uplifting orchestral music, joyful")
]

for name, prompt in prompts:
    audio = pipeline(prompt)
    audio.export(f"music/{name}_loop.wav")
```

### Step 3: Generate with MusicGen (Main Themes)

```python
from audiocraft.models import MusicGen
import scipy

model = MusicGen.get_pretrained('facebook/musicgen-medium')
model.set_generation_params(duration=30)

# Main theme
descriptions = ['emotional piano and strings, nostalgic, main theme']
wav = model.generate(descriptions)
scipy.io.wavfile.write("music/main_theme.wav", model.sample_rate, wav[0].cpu().numpy())
```

### Step 4: Generate Sound Effects (AudioLDM)

```bash
audioldm -t "door opening sound" -d 2 -o sfx/door_open.wav
audioldm -t "footsteps on floor" -d 3 -o sfx/footsteps.wav
audioldm -t "school bell ringing" -d 5 -o sfx/school_bell.wav
```

### Step 5: Edit and Loop (Audacity)

- Import generated clips
- Trim to exact loop points
- Apply fade in/out
- Normalize volume
- Export as OGG (smaller file size)

---

## Tips for Best Results

### Prompt Engineering:

**Good prompts:**
- "lo-fi hip hop beat with piano, chill, relaxing, 80 bpm"
- "acoustic guitar melody, warm, cozy cafe, gentle"
- "orchestral strings, emotional, cinematic, slow"

**Bad prompts:**
- "nice music" (too vague)
- "happy sad music" (contradictory)
- "music like [specific song]" (models don't know specific songs)

### Genre-Specific Keywords:

**Lo-fi:**
```
"lo-fi, chill, hip hop beat, vinyl crackle, jazzy, relaxing"
```

**Orchestral:**
```
"orchestral, cinematic, strings, brass, epic, dramatic"
```

**Piano:**
```
"piano solo, melodic, gentle, emotional, classical"
```

**Electronic:**
```
"electronic, synthesizer, ambient, atmospheric, dreamy"
```

**8-bit/Chiptune:**
```
"8-bit, chiptune, retro game music, NES style, upbeat"
```

---

## Recommended Tool Combinations

### For Quick Projects:
- **Riffusion** only - Fast, good enough quality

### For Better Quality:
- **MusicGen Medium** - Best quality for 6GB VRAM

### For Complete Audio:
- **MusicGen** - Music
- **AudioLDM** - Sound effects
- **Bark** - Voice/TTS (optional)

### For Maximum Quality (if time permits):
- **MusicGen** - Generate base track
- **Audacity** - Edit and enhance
- **Sound effects libraries** - Add professional SFX

---

## Cost Breakdown

### All Free!

| Tool | Cost | License |
|------|------|---------|
| Riffusion | Free | MIT License |
| MusicGen | Free | MIT License |
| AudioLDM | Free | Academic/Research |
| Bark | Free | MIT License |
| Audacity | Free | GPL |

**Total: $0** - Completely free and open source!

---

## Comparison with Commercial Options

| Option | Cost | Quality | Offline | License |
|--------|------|---------|---------|---------|
| **Riffusion (Local)** | Free | Good | Yes | Royalty-free |
| **MusicGen (Local)** | Free | Excellent | Yes | Royalty-free |
| Epidemic Sound | $15/mo | Professional | No | Subscription |
| Artlist | $9.99/mo | Professional | No | Subscription |
| AudioJungle | $1-50/track | Professional | Yes | Per-track |

**Local AI wins for:**
- ✅ Zero cost
- ✅ Unlimited generations
- ✅ Complete offline use
- ✅ No licensing worries
- ✅ Custom music for your exact needs

---

## Licensing for Generated Music

### Riffusion:
- Generated music is yours to use
- No attribution required
- Can use commercially
- Model is MIT licensed

### MusicGen (Meta):
- Generated music is yours to use
- Check Meta's terms for commercial use
- Generally permissive for indie projects

### AudioLDM:
- Check license for commercial use
- Generally okay for personal/indie projects

**Always check current license terms when using commercially!**

---

## Example VN Music Library

### Minimum (5 tracks):
1. Main Menu - Gentle, welcoming
2. Daily Life - Cheerful, light
3. Emotional - Sad, touching
4. Dramatic - Tense, suspenseful
5. Ending - Hopeful, conclusive

### Recommended (10 tracks):
1. Main Menu
2. Morning/Day Scene
3. Evening Scene
4. School/Work Scene
5. Happy Moment
6. Sad Moment
7. Tense/Suspenseful
8. Romantic Scene
9. Climax/Dramatic
10. Ending Theme

### Complete (15+ tracks):
- All of the above, plus:
11. Flashback Scene
12. Comic Relief
13. Mystery/Investigation
14. Action/Chase
15. Bad Ending
16. True Ending

---

## Tutorial Integration

### Add to Your VN Tutorial:

**Part 6: Generate Background Music**

**Time:** ~30-60 minutes
**Cost:** $0

**Steps:**
1. Install Riffusion
2. Generate 5 mood-based loops
3. Install MusicGen (optional, for better quality)
4. Generate main theme
5. Integrate with Love2D

**Result:** Complete VN with AI-generated music!

---

## Resources & Links

### Official Repositories:
- **Riffusion:** https://github.com/riffusion/riffusion
- **MusicGen:** https://github.com/facebookresearch/audiocraft
- **AudioLDM:** https://github.com/haoheliu/AudioLDM
- **Bark:** https://github.com/suno-ai/bark

### Audio Editing:
- **Audacity:** https://www.audacityteam.org/ (Free audio editor)
- **LMMS:** https://lmms.io/ (Free DAW for music production)

### Learning Resources:
- Riffusion Discord community
- AudioCraft documentation
- r/AIMusic subreddit

---

## Troubleshooting

### "Out of memory" error:

**Solution:**
- Use MusicGen Small instead of Medium
- Reduce duration (generate 15s instead of 30s)
- Close other applications
- Reduce batch size to 1

### Low quality output:

**Solution:**
- Use better prompts (more descriptive)
- Try MusicGen instead of Riffusion
- Increase guidance scale
- Use higher sample rate

### Music doesn't loop smoothly:

**Solution:**
- Use Audacity to create seamless loops
- Fade in/out at loop points
- Generate longer clips and find natural loop points

### Slow generation:

**Solution:**
- Use smaller models (MusicGen Small, Riffusion)
- Generate shorter clips
- Reduce sample rate
- Use GPU acceleration (should be automatic)

---

## My Final Recommendation

**For Your VN Tutorial:**

1. **Start with Riffusion**
   - Easiest to setup (uses SD 1.5)
   - Fast generation
   - Good enough quality for tutorials

2. **Generate 5 basic loops:**
   - Menu music
   - Happy scene
   - Sad scene
   - Normal conversation
   - Dramatic moment

3. **If you want better quality, add MusicGen:**
   - Main theme
   - Ending theme

4. **Total time investment:** ~1 hour
5. **Total cost:** $0

This completes your tutorial with:
- ✅ AI-generated art (SD 1.5)
- ✅ AI-assisted coding (Qwen)
- ✅ AI-written dialogue (Qwen)
- ✅ AI-generated music (Riffusion/MusicGen)

**100% AI-assisted indie game development on $500 hardware!**

---

*Document created: 2026-02-05*
*Hardware: GTX 1660 6GB / RTX 4050 6GB compatible*
*All tools: Free and open source*
