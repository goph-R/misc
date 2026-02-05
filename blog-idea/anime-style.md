# Style Consistency Guide for SD 1.5
## Maintaining Consistent Styles Across Different Models

This guide helps you generate backgrounds and characters with different Stable Diffusion models while maintaining visual consistency.

---

## Universal Style Keywords

### For Lighting Consistency
```
golden hour, soft lighting, rim lighting, cinematic lighting,
ambient occlusion, volumetric lighting, dramatic shadows
```

### For Color Palette Consistency
```
warm color palette, cool tones, muted colors, vibrant colors,
pastel colors, desaturated, high contrast, color grading
```

### For Atmospheric Consistency
```
atmospheric, foggy, hazy, depth of field, bokeh,
film grain, chromatic aberration
```

### For Art Style Consistency
```
highly detailed, sharp focus, 8k, octane render, unreal engine,
concept art, digital painting, matte painting, artstation
```

---

## Style Matching Strategies

### Strategy 1: Artist Name Keywords (Most Powerful)

Use the same artist names in both character and background prompts:

**For Semi-Realistic Anime Style:**
```
by makoto shinkai, by greg rutkowski, studio ghibli style
```

**For Painterly Style:**
```
by alphonse mucha, art nouveau, by james gurney
```

**For Detailed Fantasy:**
```
by wlop, by artgerm, by rossdraws
```

**For Cinematic:**
```
by simon stalenhag, blade runner 2049 style, denis villeneuve
```

### Strategy 2: Film/Photography References

```
shot on canon eos r5, 35mm film, kodak portra 400,
anamorphic lens, shallow depth of field
```

### Strategy 3: Time of Day + Weather

```
sunset, overcast sky, blue hour, rainy day, misty morning
```

---

## Specific Style Combinations

### Anime Character + Realistic Background

**Character prompt (Anything v5):**
```
anime style character, cel shaded, by makoto shinkai,
soft lighting, pastel colors, detailed eyes, clean lines,
golden hour lighting, depth of field, cinematic
```

**Background prompt (Realistic Vision):**
```
photorealistic background, by makoto shinkai, golden hour lighting,
soft focus, pastel colors, cinematic, depth of field, atmospheric,
no people
```

**Key matching words:** `makoto shinkai, golden hour, pastel colors, cinematic, depth of field`

### Both Anime (Different Models)

**Character (CounterfeitV3.0):**
```
anime girl, vibrant colors, studio lighting, highly detailed,
sharp focus, by wlop, artstation, soft shadows
```

**Background (AbyssOrangeMix3):**
```
anime background, vibrant colors, highly detailed, sharp focus,
by wlop, artstation, soft shadows, atmospheric, no people
```

**Key matching words:** `vibrant colors, wlop, artstation, soft shadows`

### Semi-Realistic (Both Ways)

**Mix 1:**
```
semi-realistic, painted style, concept art, by greg rutkowski,
warm lighting, high detail, artstation, muted colors
```

**Mix 2:**
```
2.5D style, slight cel shading, realistic proportions,
soft lighting, muted colors, cinematic
```

---

## Recommended Keyword Combos by Style

### Cyberpunk/Sci-Fi
```
neon lights, blade runner style, cyberpunk city,
purple and blue color scheme, rain, reflective surfaces,
cinematic lighting
```

### Fantasy
```
magic atmosphere, ethereal, mystical, by alan lee,
soft glowing light, muted earth tones, volumetric fog
```

### Studio Ghibli Style
```
studio ghibli, by hayao miyazaki, watercolor style,
soft colors, pastoral, peaceful atmosphere, hand painted
```

### Modern Urban
```
urban photography, overcast day, muted colors, cinematic,
architectural photography, clean lines, minimal
```

### Cozy/Slice of Life
```
cozy atmosphere, warm lighting, soft focus, peaceful,
by makoto shinkai, sunset, comfortable, inviting
```

---

## Advanced Techniques

### 1. Use ControlNet
- Generate background first
- Use depth map or line art as ControlNet input for character
- Ensures spatial consistency

### 2. Color Grading LUTs (Post-processing)
- Apply same color LUT to both in Photoshop/GIMP
- Unifies color palette instantly

### 3. Shared LoRAs
Use the same LoRA across both models:
```
<lora:add_detail:0.5> <lora:film_grain:0.3>
```

Common LoRAs that work across models:
- `add_detail` - adds consistent detail level
- `more_art` - consistent artistic style
- `epi_noiseoffset` - consistent lighting/contrast
- `detail_tweaker` - fine-tune detail consistency

### 4. Negative Prompts (Keep Consistent!)
```
Negative: worst quality, low quality, normal quality,
lowres, oversaturated, oversaturated colors,
jpeg artifacts, blurry
```

---

## Quick Reference Chart

| Goal | Key Keywords | Artist Reference |
|------|-------------|------------------|
| Anime + Real Mix | `cinematic lighting, depth of field, soft focus` | makoto shinkai |
| Full Anime | `cel shaded, vibrant colors, clean lines` | Studio Trigger |
| Semi-Realistic | `painted style, concept art, muted colors` | greg rutkowski |
| Dark Fantasy | `dark atmosphere, rim lighting, moody` | zdzisław beksiński |
| Bright Fantasy | `ethereal, magical, soft lighting, pastel` | Studio Ghibli |
| Cyberpunk | `neon, reflections, rain, purple and blue` | Blade Runner 2049 |
| Pastel Aesthetic | `soft pastel colors, dreamy, hazy, gentle` | pastel colors |

---

## Recommended Workflow

### Step-by-Step Process

1. **Generate background first** with your realistic/scene model
2. **Note the lighting conditions** (time of day, weather)
3. **Use same lighting keywords** for character generation
4. **Add matching artist name** to both prompts
5. **Use same color palette descriptors**
6. **Apply consistent LoRAs** if available
7. **Post-process** both with same color grading

---

## Complete Example Workflow

### Example: Cozy Apartment Scene

**Step 1 - Background (Realistic Vision v6.0B1):**

```
Prompt:
modern apartment interior, large windows, sunset, golden hour lighting,
warm color palette, by makoto shinkai, cinematic, depth of field,
highly detailed, cozy atmosphere, no people

Negative:
people, characters, oversaturated, low quality, blurry, worst quality
```

**Step 2 - Character (Anything v5):**

```
Prompt:
anime girl, 1girl, casual clothes, golden hour lighting,
warm color palette, by makoto shinkai, cinematic, depth of field,
soft shadows, cozy atmosphere, highly detailed

Negative:
oversaturated, low quality, bad anatomy, bad hands, worst quality, blurry
```

**Matching keywords:**
- `golden hour lighting`
- `warm color palette`
- `by makoto shinkai`
- `cinematic`
- `depth of field`
- `cozy atmosphere`

**Expected Result:** ~80-90% style consistency between background and character

---

## Model-Specific Tips

### Realistic Vision v6.0B1 (Backgrounds)
- Excels at: Architectural detail, lighting, photorealism
- Best for: Outdoor scenes, interiors, cityscapes
- Tip: Add "architectural photography" for cleaner results

### Anything v5 (Characters)
- Excels at: Clean anime style, versatile character designs
- Works well with: Most artist names, flexible style matching
- Tip: Use "clean lines" for sharper character edges

### CounterfeitV3.0 (Characters)
- Excels at: High-quality anime, beautiful lighting
- Best for: Detailed character art, portraits
- Tip: Slightly more realistic than Anything v5

### AbyssOrangeMix3 (Backgrounds/Scenes)
- Excels at: Painterly anime backgrounds, vibrant colors
- Best for: Fantasy scenes, artistic backgrounds
- Tip: Use A1 variant for most versatility

---

## Common Artist References

### Anime/Manga Artists
- **Makoto Shinkai** - Your Name, Weathering With You style (great for matching real+anime)
- **Hayao Miyazaki** - Studio Ghibli style
- **WLOP** - Detailed fantasy anime
- **Artgerm** - Semi-realistic anime portraits
- **Rossdraws** - Vibrant, painterly anime
- **Ilya Kuvshinov** - Clean, modern anime style

### Digital Artists
- **Greg Rutkowski** - Fantasy concept art (very versatile)
- **Alphonse Mucha** - Art nouveau style
- **James Gurney** - Painterly, dinosaur artist
- **Simon Stalenhag** - Sci-fi, nostalgic atmosphere

### Photographers/Cinematographers
- **Roger Deakins** - Cinematic lighting
- **Blade Runner 2049** - Cyberpunk aesthetic
- **Denis Villeneuve** - Moody, atmospheric

---

## Color Palette Keywords

### Warm Palettes
```
golden hour, warm tones, orange and yellow, sunset colors,
warm color palette, cozy lighting
```

### Cool Palettes
```
blue hour, cool tones, teal and blue, moonlight,
cool color palette, night scene
```

### Muted/Desaturated
```
muted colors, desaturated, washed out colors,
subtle colors, pastel, soft colors
```

### Vibrant
```
vibrant colors, saturated, rich colors,
colorful, vivid, bold colors
```

### Monochromatic
```
monochrome, limited color palette, two-tone,
[specific color] color scheme
```

---

## Lighting Keywords

### Natural Lighting
```
golden hour, blue hour, sunset, sunrise, overcast,
soft daylight, natural light, window light
```

### Artificial Lighting
```
studio lighting, neon lights, streetlights,
indoor lighting, warm interior lights
```

### Dramatic Lighting
```
rim lighting, dramatic shadows, high contrast lighting,
chiaroscuro, spotlight, backlight
```

### Soft Lighting
```
soft lighting, diffused light, gentle lighting,
even lighting, ambient light
```

### Atmospheric Lighting
```
volumetric lighting, god rays, light rays,
atmospheric, light through fog
```

---

## Weather & Atmosphere Keywords

### Clear Weather
```
clear sky, sunny day, bright, cloudless
```

### Cloudy/Overcast
```
overcast, cloudy sky, diffused light, soft shadows
```

### Rain
```
rainy, wet streets, puddles, reflections,
rain drops, stormy
```

### Fog/Mist
```
foggy, misty, hazy, atmospheric fog, morning mist
```

### Snow
```
snowing, snow covered, winter scene, snowfall
```

---

## Composition Keywords

### Depth & Focus
```
depth of field, bokeh, shallow depth of field,
sharp focus, soft focus, blurred background
```

### Camera Angles
```
low angle, high angle, bird's eye view,
dutch angle, eye level
```

### Shot Types
```
close-up, medium shot, wide shot, full body,
portrait, landscape orientation
```

### Framing
```
centered composition, rule of thirds,
symmetrical, dynamic composition
```

---

## Technical Quality Keywords

### Resolution/Detail
```
8k, 4k, highly detailed, ultra detailed,
intricate details, sharp focus
```

### Rendering Style
```
octane render, unreal engine, ray tracing,
photorealistic, concept art, matte painting
```

### Art Platform
```
artstation, pixiv, deviantart, trending on artstation
```

---

## Troubleshooting Style Mismatches

### Problem: Colors don't match
**Solution:**
- Add specific color palette keywords (`warm color palette`, `muted colors`)
- Use same time of day (`golden hour`, `blue hour`)
- Post-process with same color grading/LUT

### Problem: Lighting feels different
**Solution:**
- Match lighting type exactly (`golden hour lighting`, `soft lighting`)
- Add same shadow keywords (`soft shadows`, `dramatic shadows`)
- Specify light direction (`backlight`, `rim lighting`)

### Problem: Art style too different
**Solution:**
- Use same artist name in both prompts
- Add style descriptors (`semi-realistic`, `painterly`, `cel shaded`)
- Use same LoRAs if possible

### Problem: Atmosphere doesn't match
**Solution:**
- Match weather/atmosphere keywords (`foggy`, `atmospheric`, `hazy`)
- Use same mood keywords (`cozy`, `moody`, `peaceful`)
- Add same post-processing effects (`film grain`, `chromatic aberration`)

---

## Pro Tips

1. **Always generate background first** - easier to match character to background than vice versa

2. **Keep a style library** - save successful prompt combinations that work well together

3. **Use img2img** - generate both separately, then use img2img to unify style slightly

4. **Layer in Photoshop/GIMP** - adjust colors, add same filters to both layers

5. **Test with same seed** - when testing style matching, use same seed to reduce variables

6. **ControlNet is your friend** - use depth/normal maps from background for character placement

7. **Start simple** - begin with 3-5 matching keywords, add more as needed

8. **Negative prompts matter** - keep them consistent across both generations

9. **LCM/Turbo models** - great for quick style testing iterations

10. **Save your workflows** - document what works for future reference

---

## Recommended LoRA Resources

### Where to Find LoRAs
- **Civitai** - https://civitai.com (largest collection)
- **Hugging Face** - https://huggingface.co
- **Discord communities** - model-specific servers

### Popular Style LoRAs
- `add_detail` / `add_more_details` - universal detail enhancement
- `epi_noiseoffset2` - better lighting and contrast
- `film_grain` - adds consistent film-like texture
- `flatBedColorsFP16` / `stylized` - specific art styles
- Artist-specific LoRAs - search for your favorite artists

### How to Use LoRAs
```
In your prompt:
<lora:add_detail:0.7> <lora:film_grain:0.3> your prompt here
```

Weights typically range from 0.3 to 1.0. Lower = subtle, higher = stronger effect.

---

## Example Prompt Library

### Scenario 1: Modern City Anime Character

**Background (Realistic Vision):**
```
Prompt: modern city street, tokyo, sunset, golden hour lighting,
urban photography, cinematic, depth of field, no people,
by makoto shinkai, warm color palette, soft focus

Negative: people, characters, oversaturated, low quality
```

**Character (Anything v5):**
```
Prompt: anime girl, 1girl, modern clothes, city background,
golden hour lighting, by makoto shinkai, cinematic,
warm color palette, depth of field, highly detailed

Negative: oversaturated, bad anatomy, low quality
```

### Scenario 2: Fantasy Forest Scene

**Background (Realistic Vision):**
```
Prompt: enchanted forest, magical atmosphere, soft glowing light,
volumetric fog, by alan lee, muted earth tones,
ethereal, highly detailed, mystical, no people

Negative: people, oversaturated, modern, low quality
```

**Character (CounterfeitV3.0):**
```
Prompt: anime elf girl, 1girl, forest background, soft glowing light,
by wlop, muted earth tones, ethereal, mystical,
highly detailed, magical atmosphere

Negative: modern, oversaturated, bad anatomy, low quality
```

### Scenario 3: Cyberpunk Night Scene

**Background (Realistic Vision):**
```
Prompt: cyberpunk city, night scene, neon lights, rain,
wet streets, reflections, blade runner 2049 style,
purple and blue color scheme, cinematic lighting, no people

Negative: daylight, people, low quality
```

**Character (AbyssOrangeMix3):**
```
Prompt: anime girl, 1girl, cyberpunk outfit, night scene,
neon lights, purple and blue color scheme, rain,
cinematic lighting, highly detailed, reflective surfaces

Negative: daylight, oversaturated, bad anatomy, low quality
```

### Scenario 4: Cozy Bedroom Interior

**Background (Realistic Vision):**
```
Prompt: cozy bedroom, large window, morning light,
soft lighting, warm atmosphere, comfortable, inviting,
by makoto shinkai, pastel colors, highly detailed, no people

Negative: people, dark, messy, low quality
```

**Character (Anything v5):**
```
Prompt: anime girl, 1girl, pajamas, bedroom, morning light,
soft lighting, warm atmosphere, by makoto shinkai,
pastel colors, cozy, comfortable, highly detailed

Negative: dark, oversaturated, bad anatomy, low quality
```

### Scenario 5: Beach Sunset

**Background (Realistic Vision):**
```
Prompt: beach at sunset, ocean, golden hour, orange sky,
waves, peaceful, by makoto shinkai, cinematic,
warm color palette, highly detailed, no people

Negative: people, stormy, dark, low quality
```

**Character (Anything v5):**
```
Prompt: anime girl, 1girl, summer dress, beach, sunset,
golden hour, orange sky, by makoto shinkai, cinematic,
warm color palette, peaceful, highly detailed

Negative: stormy, dark, bad anatomy, low quality
```

---

## Conclusion

Achieving style consistency across different SD 1.5 models requires:

1. **Shared keywords** - lighting, color, atmosphere
2. **Artist references** - same artist names in both prompts
3. **Consistent negatives** - avoid same problems in both
4. **Matching technical specs** - same detail level, rendering style
5. **Post-processing** - final color grading to unify

With practice, you can achieve 80-90% consistency, with the remaining 10-20% fixed in post-production.

**Remember:** The key is finding what works for YOUR specific model combinations and saving those prompt patterns!

---

*Document created: 2026-02-05*
*Models referenced: Realistic Vision v6.0B1, Anything v5, CounterfeitV3.0, AbyssOrangeMix3*
