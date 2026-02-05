# Visual Novel Game Development Guide

## Table of Contents
1. [Using AI NPCs in Visual Novels](#using-ai-npcs-in-visual-novels) (Advanced concept)
2. [Traditional Visual Novel Features](#traditional-visual-novel-features) (Standard approach)
3. [Using AI as Development Assistant](#using-ai-as-development-assistant) (Recommended for tutorial)

---

# Using AI NPCs in Visual Novels

## Project Idea: Visual Novel with AI-Powered Characters

Create a visual novel game with locally run AI for dynamic NPC conversations on a relatively cheap laptop/PC.

### Hardware Compatibility

**GTX 1660 6GB Performance:**

| GPU | VRAM | Performance | Price Range |
|-----|------|-------------|-------------|
| RTX 4050 | 6GB | 100% (baseline) | $800-1200 |
| GTX 1660 | 6GB | ~70% speed | $150-250 used |
| GTX 1660 Ti | 6GB | ~75% speed | $180-280 used |
| GTX 1660 Super | 6GB | ~73% speed | $170-270 used |

**All GTX 1660 variants work!** They have CUDA support (required for llama.cpp and SD).

### Expected Performance on GTX 1660 6GB

**LLM (Qwen 2.5 Coder 7B Q4):**
- Speed: ~8-15 tokens/sec (vs 15-25 on RTX 4050)
- Perfectly usable for dialogue!
- VRAM: ~4-4.5GB

**Stable Diffusion 1.5:**
- Speed: ~5-8 sec/image at 512x512 (vs 3-5 sec on RTX 4050)
- Still very fast for pre-generation
- VRAM: ~2GB

**Verdict:** GTX 1660 is **perfect for visual novel projects!**

---

## AI-Powered VN Development Workflow

For a VN, you don't need both running simultaneously! Here's the workflow:

### Phase 1: Content Generation (Offline)

**1. Generate Character Sprites**
```bash
# Generate character expressions/poses with SD 1.5
# Multiple angles, emotions, outfits
```

**2. Generate Backgrounds**
```bash
# Generate location backgrounds
# Different times of day, weather
```

**3. (Optional) Pre-generate Some Dialogue**
```bash
# Generate story outline, character personalities
# Using Qwen for writing assistance
```

### Phase 2: Runtime (Game Playing)

**Only LLM runs during gameplay:**
- Player types dialogue
- LLM generates NPC responses
- Display pre-generated sprites/backgrounds
- Very low VRAM usage (~4-5GB for Qwen 7B)

**This means even 4GB GPUs can run the game!**

---

## Visual Novel Engines with AI Integration

### Option 1: Ren'Py (Most Popular, Easiest)

**Ren'Py** - https://www.renpy.org/
- Python-based (easy to integrate AI!)
- Free and open source
- Used for 1000+ published VNs

**AI Integration Example:**
```python
# In Ren'Py script
init python:
    import requests

    def ask_ai(prompt):
        # Call local llama-server
        response = requests.post('http://localhost:8080/v1/chat/completions',
            json={
                "messages": [{"role": "user", "content": prompt}],
                "temperature": 0.8
            })
        return response.json()['choices'][0]['message']['content']

label chat_with_npc:
    $ player_input = renpy.input("What do you say?")
    $ ai_response = ask_ai(f"You are Sakura, a kind schoolgirl. Player said: {player_input}")

    show sakura happy
    sakura "[ai_response]"
```

### Option 2: GDevelop (No Code, Visual)

**GDevelop** - https://gdevelop.io/
- Drag-and-drop interface
- Can call APIs (integrate with llama-server)
- Export to web/desktop
- Free and open source

### Option 3: Unity/Godot (More Complex)

**Unity** - Full game engine
- More control, steeper learning curve
- C# scripting

**Godot** - https://godotengine.org/
- GDScript (Python-like)
- Free and open source
- Lighter than Unity

### Option 4: Custom HTML/JavaScript (Web-based)

```javascript
// Simple web-based VN with AI
async function talkToNPC(playerMessage) {
    const response = await fetch('http://localhost:8080/v1/chat/completions', {
        method: 'POST',
        body: JSON.stringify({
            messages: [{role: "user", content: playerMessage}]
        })
    });
    const data = await response.json();
    return data.choices[0].message.content;
}
```

**Advantages:**
- Works in browser
- Easy to distribute
- Can use libraries like Visual Novel Maker

---

## Recommended Tech Stack for AI-Powered VN

### Setup:

**1. Art Generation (Pre-production):**
- Stable Diffusion 1.5 with your models
- AUTOMATIC1111 or ComfyUI
- Generate all sprites/backgrounds ahead of time

**2. LLM Server (Runtime):**
```bash
./llama-server.exe -m qwen2.5-coder-7b-instruct-q4_k_m.gguf -ngl 999 -c 4096
```

**3. Game Engine:**
- **Ren'Py** (recommended for beginners)
- **Godot** (if you want more control)

**4. Character Memory/Personality:**
- Use system prompts to define character personalities
- Store conversation history
- Use character cards (like CharacterAI format)

---

## Complete AI Visual Novel Workflow

### Step 1: Character Creation

**Define character in JSON:**
```json
{
  "name": "Sakura",
  "personality": "kind, cheerful, slightly shy",
  "background": "high school student, loves reading",
  "speaking_style": "polite, uses casual Japanese phrases",
  "sprites": {
    "happy": "sakura_happy.png",
    "sad": "sakura_sad.png",
    "angry": "sakura_angry.png",
    "surprised": "sakura_surprised.png"
  }
}
```

**Generate sprites with SD 1.5:**
```
Prompt: anime girl, school uniform, [emotion], high quality,
by wlop, clean background, character sheet style, consistent character

Generate for: happy, sad, angry, surprised, neutral, etc.
```

### Step 2: Background Generation

```
Prompt: school hallway, anime style, by makoto shinkai,
highly detailed, no people, clean, bright lighting
```

Generate for each location in your VN.

### Step 3: Runtime AI Integration

**System prompt for character:**
```
You are Sakura, a kind and cheerful high school student.
Personality: Kind, cheerful, slightly shy, loves reading
Speaking style: Polite, casual, sometimes uses "um..." or "..." when nervous
Background: You're in your final year of high school. You love literature.
Current scene: School hallway after class

Respond naturally as Sakura would. Keep responses 1-3 sentences.
Show emotion through your words.
```

**During gameplay:**
1. Player types message
2. Send to LLM with character context
3. Parse response
4. Determine emotion from response (happy/sad/etc)
5. Show appropriate sprite
6. Display dialogue

### Step 4: Emotion Detection (Optional)

Add emotion tags to AI responses:
```python
def detect_emotion(ai_response):
    # Simple keyword detection
    if any(word in ai_response.lower() for word in ['happy', 'glad', 'excited', '!']):
        return 'happy'
    elif any(word in ai_response.lower() for word in ['sad', 'sorry', 'unfortunately']):
        return 'sad'
    # ... etc
    return 'neutral'
```

Or ask the AI to tag emotions:
```
System: End each response with [EMOTION: happy/sad/angry/surprised/neutral]
```

---

## Budget PC/Laptop Options for VN Development

### Desktop Options (Best Value)

**Budget Build (~$400-600):**
- Used GTX 1660 6GB (~$150-200)
- Ryzen 5 5600 (~$100-130)
- 16GB RAM (~$40-50)
- 500GB SSD (~$30-40)
- Case + PSU (~$80-100)

**Better Build (~$600-800):**
- Used RTX 3060 12GB (~$250-300)
- Ryzen 5 5600/7600 (~$130-180)
- 32GB RAM (~$70-90)
- 1TB SSD (~$50-70)
- Case + PSU (~$100-120)

### Laptop Options

**Budget (~$500-700):**
- GTX 1650 4GB laptops (can run Qwen 3B)
- Used gaming laptops with GTX 1660 Ti
- AMD 780M iGPU laptops (slower but works)

**Recommended (~$700-900):**
- RTX 4050 6GB laptops (new)
- Used RTX 3060 laptops
- Better for development workflow

---

## Example Ren'Py + AI Implementation

### Complete Example:

```python
# File: game/script.rpy

# Character definitions
define s = Character("Sakura", color="#c8ffc8")

# AI helper
init python:
    import requests
    import json

    class AICharacter:
        def __init__(self, name, personality, context):
            self.name = name
            self.personality = personality
            self.context = context
            self.history = []

        def respond(self, player_message):
            # Build conversation history
            messages = [
                {"role": "system", "content": f"You are {self.name}. {self.personality}"},
                {"role": "system", "content": f"Context: {self.context}"}
            ]

            # Add conversation history
            for msg in self.history[-6:]:  # Last 3 exchanges
                messages.append(msg)

            messages.append({"role": "user", "content": player_message})

            # Call local LLM
            response = requests.post('http://localhost:8080/v1/chat/completions',
                json={"messages": messages, "temperature": 0.8, "max_tokens": 100})

            ai_response = response.json()['choices'][0]['message']['content']

            # Save to history
            self.history.append({"role": "user", "content": player_message})
            self.history.append({"role": "assistant", "content": ai_response})

            return ai_response

    # Initialize characters
    sakura_ai = AICharacter(
        "Sakura",
        "You are kind, cheerful, and slightly shy. You love reading.",
        "You're in the school hallway after class."
    )

# Game start
label start:
    scene school_hallway
    show sakura neutral

    s "Oh, hello! I didn't expect to see you here."

    label chat_loop:
        $ player_input = renpy.input("What do you say?", length=200)

        if player_input.lower() in ['bye', 'goodbye', 'see you']:
            jump goodbye

        $ ai_response = sakura_ai.respond(player_input)

        # Simple emotion detection
        if any(word in ai_response.lower() for word in ['!', 'happy', 'great']):
            show sakura happy
        elif any(word in ai_response.lower() for word in ['sad', 'sorry']):
            show sakura sad
        else:
            show sakura neutral

        s "[ai_response]"

        jump chat_loop

label goodbye:
    show sakura happy
    s "See you later!"
    return
```

---

## Advanced Features for AI-Powered VN

### 1. Character Memory
- Store important facts the player mentions
- Reference them in future conversations
- Use vector database (ChromaDB) for long-term memory

### 2. Multiple Characters
- Each character has own AI instance
- Different personalities and memory
- Characters can "remember" what others said

### 3. Story Branching
- AI can make decisions affecting the story
- Generate dynamic plot points
- Player choices influence character relationships

### 4. Dynamic Art Generation
- Generate new character expressions on-the-fly
- (Slower, but possible with SD 1.5 in background)

### 5. Voice Synthesis (Optional)
- Use local TTS (Coqui TTS, Piper)
- Voice each character's dialogue
- Adds immersion

---

## Resource Requirements Summary

### Development Phase (Art Generation):
- GPU: GTX 1660 6GB or better
- RAM: 16GB minimum (32GB recommended)
- Storage: 50-100GB (for models + generated assets)
- Time: Can generate 100+ images in a few hours

### Runtime (Playing the Game):
- GPU: GTX 1660 6GB (for Qwen 7B)
  - OR: Can use smaller model (Qwen 3B) on 4GB GPUs
- RAM: 8GB minimum (16GB recommended)
- Storage: 1-5GB (game + assets)
- VRAM: ~4-5GB for LLM only

### Distribution (For Players):
**Option A: LLM Required**
- Players need 6GB GPU
- Distribute with model download instructions
- ~5GB download

**Option B: Pre-generated Dialogue**
- Generate many dialogue variations ahead of time
- No GPU required for players
- Larger download but works on any PC

**Option C: Cloud Hybrid**
- Host LLM on your server
- Players connect for AI responses
- Any PC works, but needs internet

---

## Recommended Model Sizes for AI NPCs

### For 6GB VRAM (GTX 1660, RTX 4050):
- **Qwen 2.5 7B Q4** - Best quality
- **Qwen 2.5 3B Q4** - Faster, still good
- **Phi-3.5 Mini 3.8B** - Great for dialogue

### For 4GB VRAM (GTX 1650):
- **Qwen 2.5 3B Q4** - Recommended
- **Qwen 2.5 1.5B Q4** - Faster, acceptable quality

### For AMD iGPU (780M):
- **Qwen 2.5 3B Q4** - Works well
- **Phi-3.5 Mini** - Alternative

---

## Example AI-VN Project Timeline

### Week 1-2: Setup & Planning
- Install tools (SD, llama.cpp, Ren'Py)
- Design characters and story outline
- Test AI integration prototype

### Week 3-4: Art Generation
- Generate character sprites (all emotions)
- Generate backgrounds
- Create UI elements

### Week 5-6: Development
- Implement Ren'Py scripts
- Integrate AI dialogue system
- Test conversations

### Week 7-8: Polish
- Add sound effects/music
- Test gameplay flow
- Optimize performance

**Total: 2 months for a small VN with 3-5 AI characters**

---

# Traditional Visual Novel Features

## Core Features (Must Have)

### 1. Text Box / Dialogue System
- Character name display
- Text that appears character-by-character (typewriter effect)
- Click/Enter to continue
- Auto-advance option (optional)

### 2. Character Sprites
- Multiple poses/expressions
- Fade in/out
- Position on screen (left, center, right)
- Simple animations (optional)

### 3. Backgrounds
- Static background images
- Fade transitions between scenes
- Day/night variants (optional)

### 4. Choices / Branching
- Present 2-4 choices to player
- Branch story based on selection
- Track player decisions

### 5. Save/Load System
- Save current progress
- Load previous saves
- Multiple save slots

---

## Nice-to-Have Features

### 6. Main Menu
- Start Game
- Load Game
- Settings
- Quit

### 7. Character Gallery
- Unlock CG (special art)
- View collected images

### 8. Sound
- Background music
- Sound effects (click, whoosh)
- Voice acting (rare in indie VNs)

### 9. Settings
- Text speed adjustment
- Volume controls
- Fullscreen toggle

### 10. Skip/Auto Mode
- Skip previously read text
- Auto-advance dialogue

### 11. Backlog
- Review previous dialogue
- Go back to previous choices

---

## Simple Visual Novel Structure

### Minimal VN (Good for Tutorial):

```
Features to implement:
├── Text box with character name
├── Typewriter text effect
├── Character sprites (2-3 expressions)
├── Background images
├── Simple choices (2-3 branch points)
├── Basic scene transitions
└── Simple menu (Start/Quit)
```

**This is manageable for a Love2D tutorial!**

---

# Using AI as Development Assistant

## The Right Approach: AI Helps You Build

Use your local Qwen 2.5 Coder to **help you build** the game, not run at runtime.

### 1. Code Generation

**Ask AI:**
```
"Write a Love2D dialogue system that displays text character by character"
```

**Ask AI:**
```
"Create a simple choice system in Lua that branches the story"
```

**Ask AI:**
```
"Implement a save/load system using Lua tables and JSON"
```

### 2. Asset Generation

**With Stable Diffusion:**
- Generate character sprites
- Generate backgrounds
- Generate UI elements (text boxes, buttons)

**With AI writing assistant (Qwen):**
```
"Write dialogue for a shy high school girl meeting someone new"
```

```
"Generate 3 story choices for a scene where the player finds a mysterious letter"
```

### 3. Bug Fixing

**Ask AI:**
```
"This Lua code isn't working: [paste code]. What's wrong?"
```

### 4. Documentation

**Ask AI:**
```
"Explain how Love2D's draw order works"
```

```
"What's the best way to structure a VN in Love2D?"
```

---

## Love2D Visual Novel - Basic Structure

### File Structure:

```
my_vn/
├── main.lua              # Entry point
├── conf.lua              # Love2D configuration
├── dialogue.lua          # Dialogue system
├── choice.lua            # Choice system
├── assets/
│   ├── images/
│   │   ├── backgrounds/
│   │   │   ├── school.png
│   │   │   └── park.png
│   │   ├── characters/
│   │   │   ├── sakura_happy.png
│   │   │   ├── sakura_sad.png
│   │   │   └── sakura_neutral.png
│   │   └── ui/
│   │       └── textbox.png
│   ├── fonts/
│   │   └── font.ttf
│   └── sounds/
│       └── bgm.mp3
└── story.lua             # Story script/data
```

### Minimal Example (main.lua):

```lua
-- main.lua
function love.load()
    -- Load assets
    background = love.graphics.newImage("assets/images/backgrounds/school.png")
    textbox = love.graphics.newImage("assets/images/ui/textbox.png")
    font = love.graphics.newFont("assets/fonts/font.ttf", 24)

    -- Story state
    currentLine = 1
    displayedText = ""
    fullText = "Welcome to my visual novel!"
    charIndex = 0
    textSpeed = 0.05
    timer = 0
end

function love.update(dt)
    -- Typewriter effect
    if charIndex < #fullText then
        timer = timer + dt
        if timer >= textSpeed then
            charIndex = charIndex + 1
            displayedText = fullText:sub(1, charIndex)
            timer = 0
        end
    end
end

function love.draw()
    -- Draw background
    love.graphics.draw(background, 0, 0)

    -- Draw textbox
    love.graphics.draw(textbox, 50, 400)

    -- Draw text
    love.graphics.setFont(font)
    love.graphics.print(displayedText, 100, 450)
end

function love.keypressed(key)
    if key == "space" then
        -- Next line
        charIndex = 0
        -- (simplified - would advance story here)
    end
end
```

---

## AI-Assisted Development Workflow

### Step 1: Planning (Use AI)

**Prompt to Qwen:**
```
I'm making a simple visual novel in Love2D. It should have:
- 3 characters
- 5 scenes
- 2 branching points
- Simple save/load

Help me create a story outline with character descriptions.
```

### Step 2: Art Generation (Use SD 1.5)

**Generate backgrounds:**
```
# Use Realistic Vision
Prompt: school hallway, anime style, by makoto shinkai, no people
```

**Generate character sprites:**
```
# Use Anything v5
Prompt: anime girl, school uniform, happy expression, full body,
white background, character sheet style
```

### Step 3: Code Generation (Use AI)

**Prompt to Qwen:**
```
Write a Love2D module for a dialogue system with:
- Typewriter effect
- Character names
- Click to continue
- Support for multiple speakers
```

**AI will generate the code, you review and integrate.**

### Step 4: Dialogue Writing (Use AI)

**Prompt to Qwen:**
```
Write a conversation between two high school students meeting for the first time.
Format as Lua table:
{
  {speaker = "Sakura", text = "..."},
  {speaker = "Player", text = "..."}
}
```

### Step 5: Bug Fixing (Use AI)

When you encounter errors:
```
This Love2D code throws error: "attempt to index nil value"
[paste code]
What's wrong?
```

### Step 6: Features (Use AI)

**Add more features:**
```
Add a save system to this Love2D visual novel code using JSON
```

---

## Example Story Structure (Lua Table)

```lua
-- story.lua
story = {
    {
        scene = "school_hallway",
        background = "school.png",
        character = nil,
        speaker = "Narrator",
        text = "It's your first day at the new school."
    },
    {
        scene = "school_hallway",
        background = "school.png",
        character = "sakura_neutral.png",
        speaker = "???",
        text = "Oh! You must be the new student!"
    },
    {
        scene = "school_hallway",
        background = "school.png",
        character = "sakura_happy.png",
        speaker = "Sakura",
        text = "I'm Sakura! Nice to meet you!"
    },
    {
        type = "choice",
        text = "What do you say?",
        choices = {
            {text = "Nice to meet you too!", goto = 5},
            {text = "Leave me alone.", goto = 10}
        }
    },
    -- ... continues
}
```

---

## Love2D Advantages for VN Development

### Why Love2D is Great:

✅ **Simple**: Easy Lua scripting
✅ **Lightweight**: Small download (~5MB)
✅ **Cross-platform**: Windows, Mac, Linux, Android, iOS
✅ **Fast**: Good performance
✅ **Free**: Open source
✅ **Good for learning**: Great documentation

### Common VN Engines Comparison:

| Engine | Pros | Cons |
|--------|------|------|
| **Ren'Py** | VN-specific, easy | Python, less flexible |
| **Love2D** | Flexible, lightweight | More code needed |
| **Unity** | Powerful | Overkill for VN, large |
| **Godot** | Good middle ground | Steeper learning curve |

**For a tutorial: Love2D is perfect!** Shows how to build from scratch.

---

## Tutorial Structure Idea

### Blog Post: "Build a Visual Novel with Local AI Tools"

**Part 1: Setup**
- Install Love2D
- Install llama.cpp + Qwen
- Install Stable Diffusion

**Part 2: Generate Assets**
- Use SD to generate 3 character sprites
- Generate 2 backgrounds
- Create simple UI elements

**Part 3: Basic Framework**
- Use AI to generate dialogue system code
- Implement typewriter effect
- Display backgrounds and characters

**Part 4: Story Content**
- Use AI to write initial dialogue
- Create story structure
- Implement branching choices

**Part 5: Polish**
- Add transitions
- Add sound effects
- Create main menu

**Result:** Working VN demo in ~500 lines of Lua

---

## What AI Can Help With

### Code Tasks (Qwen 2.5 Coder):
✅ Generate boilerplate code
✅ Explain Love2D concepts
✅ Debug errors
✅ Optimize code
✅ Add features (save/load, etc.)

### Writing Tasks (Qwen):
✅ Character descriptions
✅ Dialogue writing
✅ Story outlines
✅ Branching narratives
✅ Item/location descriptions

### Art Tasks (Stable Diffusion):
✅ Character sprites
✅ Backgrounds
✅ UI elements
✅ CG images
✅ Title screen art

---

## Simple Feature Checklist

For your tutorial VN:

**Minimal (Can complete in a day):**
- [ ] Display text in a box
- [ ] Typewriter effect
- [ ] Show character sprite
- [ ] Show background
- [ ] Click to advance
- [ ] 1 simple choice

**Basic (Weekend project):**
- [ ] All minimal features
- [ ] 3 character sprites with emotions
- [ ] 3 backgrounds
- [ ] 2-3 branching choices
- [ ] Simple menu
- [ ] Scene transitions

**Complete (Week project):**
- [ ] All basic features
- [ ] Save/load system
- [ ] Multiple characters
- [ ] 5+ scenes
- [ ] Background music
- [ ] Settings menu
- [ ] Polish & UI

---

## Quick Love2D Learning Resources

**Official:**
- https://love2d.org/ - Main site
- https://love2d.org/wiki/ - Wiki
- https://love2d.org/forums/ - Community

**Tutorials:**
- "How to LÖVE" - Official tutorial book
- Sheepolution's Love2D tutorial
- CS50's Game Development (has Love2D section)

**Examples:**
- Many VNs made with Love2D on itch.io
- "Summer Memories VN Engine" (Love2D)

---

## Recommended Tutorial Approach

**Make a simple 5-minute VN demo showing:**

1. **Story**: Simple meet-cute scenario
   - 2 characters
   - 3 locations
   - 1 branching point

2. **Features**:
   - Dialogue system
   - Character sprites (2 emotions each)
   - Backgrounds
   - Simple choice
   - Basic menu

3. **Show AI workflow**:
   - Use Qwen to generate dialogue system code
   - Use SD to generate all art
   - Use Qwen to write story dialogue
   - Use Qwen to debug/optimize

4. **Blog post structure**:
   - "I used $500 hardware to build a VN"
   - "AI generated 90% of the assets"
   - "AI wrote 50% of the code"
   - "Total time: 1 weekend"

This would be a **great tutorial** showing the power of local AI for game dev!

---

## Cost Breakdown Summary

### Minimum Budget Setup:

| Item | Cost | Notes |
|------|------|-------|
| Used GTX 1660 6GB | $150-200 | or RTX 4050 laptop |
| PC Components (if building) | $250-400 | CPU, RAM, SSD, case, PSU |
| **Total Hardware** | **$400-600** | Desktop build |
| Love2D | Free | Open source |
| Stable Diffusion | Free | Open source |
| llama.cpp | Free | Open source |
| Models (Qwen, SD 1.5) | Free | Open source |
| **Total Software** | **$0** | All free! |

**Grand Total: $400-600 for complete setup**

Or **$500-800 for RTX 4050 laptop** (all-in-one solution)

---

## Final Recommendation

**For Visual Novel Development Tutorial:**

1. **Hardware**: Used GTX 1660 desktop ($400-600 total) or RTX 4050 laptop ($700-900)
2. **LLM**: Qwen 2.5 Coder 7B Q4 (for code generation and dialogue writing)
3. **SD**: Your current models (Realistic Vision, Anything v5, etc.)
4. **Engine**: Love2D (perfect for teaching fundamentals)
5. **Workflow**: Use AI as development assistant, not runtime feature

**This approach shows:**
- ✅ How to use AI to generate code
- ✅ How to use AI to generate art assets
- ✅ How to use AI to write dialogue
- ✅ Complete workflow from idea to playable game
- ✅ All on budget hardware (~$500-800)
- ✅ Completely offline

**Tutorial Result:**
- Simple but complete VN demo
- ~500 lines of Love2D/Lua code
- All assets AI-generated
- Playable in 5-10 minutes
- Built in 1 weekend

This is an excellent way to demonstrate the power of local AI for indie game development!

---

*Document created: 2026-02-05*
*Focus: Using local AI (LLMs + SD 1.5) for visual novel game development*
*Hardware: GTX 1660 6GB / RTX 4050 6GB compatible*
