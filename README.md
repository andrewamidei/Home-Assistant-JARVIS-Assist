# Home-Assistant-Jarvis-Assist
> Written for 2025.6.0

Jarvis integrated as the voice of Home Assistant. This repository contains audio and prompts to complete the Jarvis experience alongside a great project that emulates Jarvis's voice from Marvel movies. This brings together many great projects to get Jarvis working as an AI-powered assistant for your smart home.

Check out the easter egg phrases [here](https://github.com/andrewamidei/Home-Assistant-JARVIS-Assist/blob/main/easter_eggs_list.md).

## Parts of this project

### Requirements
- **Home Assistant setup.** - [HAOS](https://www.home-assistant.io/installation/#about-installation-methods) is what I use and what this is written around. Please check these links out if you are new to [Home Assistant](https://www.home-assistant.io/) or [Assist](https://www.home-assistant.io/voice_control/).
- **Ollama or other LLM running** that can integrate with Home Assistant through the Assist.
- Access to the Home Assistant file system through [samba share](https://github.com/home-assistant/addons/blob/master/samba/DOCS.md) or an alternative. Pipeline.
- [Wisper](https://www.home-assistant.io/integrations/whisper/) - Speech-to-text
- [Piper](https://www.home-assistant.io/integrations/piper/) - Text-to-speech

#### To use a [satellite](https://www.home-assistant.io/integrations/assist_satellite/)
- [Wyoming Satellite](https://github.com/rhasspy/wyoming-satellite) or [ESPHome Voice Satellite](https://github.com/Djelle/ESPHomeVoiceSatellite) - I don't have a guide for this right now.

### Aspects of Jarvis
- **Prompts** - These prompts are currently optimized for smaller LLMs (Used Llama 3.2) that don't have control over Home Assistant (Explained Later).
- **Easter Egg Responses** - These responses are triggered by quoting parts of the movies and Jarvis will respond with the matching quote. Their Easter eggs are also optimized so the wake word can be used alongside the easter egg for a more natural conversation.
- **Wake Responses** - Audio clips are used as feedback when the wake word is heard by a satellite speaker.
- **Feedback sound effects** - Sound effects when the model has heard your response.
- [**Jarvis Voice**](https://huggingface.co/jgkawell/jarvis) - Trained by [jgkawell](https://huggingface.co/jgkawell).

## Set-up
Firstly I don't have a full guide on how to set all parts of this up, but I plan to one day. If you are interested in a full guide please react to the issue [here](https://github.com/andrewamidei/Home-Assistant-JARVIS-Assist/issues/1).

### Setting up model with LLM
- On the Home Assistant dashboard, navigate to `Settings>Voice Assistants>Add Assistant`.
- In the `Add Assistant` configuration set the name and language for your assistant
- In the `Conversation agent` select your LLM, if none shows up, you need to check if you have a compatible LLM.
- After selecting the LLM hit the settings cog next to the dropdown and paste in your desired prompt.
    - If you want the LLM to control your smart home directly enable Control Home Assistant Assist. This is not recommended unless you have a larger model (roughly 20B Parameters or more).
    - Keep the default context windows suze unless you have a small LLM or want to speed up the response time of the LLM (I recommend 4096).
    - For `max history Messages` and `Keep Alive` you need to set them based on how you want things to be remembered. Max history messages will work on models hours later while Keep alive will remeber the active conversation until the model is purged from memory.
        - **For example**: I set max history Messages to zero and Keep Alive to 60 so an active conversation holds context for 60 seconds but if I ask it something much later it won't remember the last conversation. This keeps the response times fast.
    - Hit submit.
- For speech-to-text, I found `faster-wisper` the best balance for speed and accuracy running on a CPU.
- Hit `update` for now.
- Move on to the next section to give Jarvis his voice.

### Giving Jarvis his voice
- Download the [Jarvis Voice Model](https://huggingface.co/jgkawell/jarvis)
- Navigate to the folder with the `.onnx` file. There is a medium and high model and I would grab them both.
- Using [samba share](https://github.com/home-assistant/addons/blob/master/samba/DOCS.md), place the `.onnx` and the `.onnx.json` files in the share/piper folder for each model you want (you may need to make the piper folder).
- You will need to restart Piper but I prefer restarting Home Assistant so do one of those now.
- After Home Assistant has restarted, navigate to `Settings>Add-ons>Piper>Configuration`.
- These settings affect how the voice sounds and I have found what appears to work best with this Jarvis model:
    - Length Scale = 1.2
    - Noise Scale = 0.367
    - Speaking Cadence = 0.333
    - Maximum Piper Process = 1
- Save these settings
- Navigate back to `Settings>Voice Assistants>Add Assistant`.
- Select the model you created in the list and scroll down to `Text-to-speech`.
- In the Text-to-speech dropdown, select piper.
- For Jarvis, you will need the language set to `en_GB`.
- In the Voice dropdown, select `jarvis-medium (medium)` or `jarvis-high (high)`.
- You can try the voice by hitting `Try Voice` and entering in some text.

### Adding Easter Eggs
- Copy the files from the `audio clips` folder and place them into the media forlder on the smb share using [samba share](https://github.com/home-assistant/addons/blob/master/samba/DOCS.md).
- Use the `jarvis_easter_egg.yaml` file provided or the button below to import the easter eggs.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fandrewamidei%2FHome-Assistant-jarvis-Assist%2Fblob%2Fmain%2Fjarvis_easter_eggs.yaml)

## Optimization Recommendations
- When exposing entities, if a device is called `bedroom lamp` add and alias called `the bedroom lamp` to clear up issues when using the word "the" in commands.
- Run LLM's on GPU resourses. Especially for a voice assistant.

