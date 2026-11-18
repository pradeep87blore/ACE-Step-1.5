# ACE-Step Studio

A local instrumental music studio built on [ACE-Step 1.5](https://github.com/ace-step/ACE-Step-1.5). You write one music prompt. The studio writes the tracks with no vocals, shows progress, and saves a WAV master plus an MP3 for each track.

Open http://127.0.0.1:7870 after the server starts.

## What you can do

- Describe the music in one prompt. Lyrics are not used. Every track is forced to be instrumental.
- Ask for 1 to 10 tracks from that same prompt.
- Leave quality on **High**, which writes a 32-bit float WAV and a 320 kbps MP3, both at 48 kHz. Standard is 16-bit WAV plus 256 kbps MP3. Compact is 16-bit WAV plus 192 kbps MP3.
- Watch one progress bar for the whole batch. Tracks that finish appear on the page with a player and download links.
- Let the studio generate more than one track at a time when the GPU has room. If a batch runs out of memory, it continues one track at a time.

Finished audio is written under `studio_outputs/`. Each batch gets its own folder, named from the opening of the prompt, such as `peaceful-indian-village-morning-lo-fi`. A repeat of the same name gets `-2`, `-3`, and so on.

## Models

On startup the studio loads:

- `acestep-v15-xl-turbo` when all four weight files are in `checkpoints/acestep-v15-xl-turbo`. Otherwise it uses `acestep-v15-turbo`.
- `acestep-5Hz-lm-1.7B` as the planner, with Think mode on, through the PyTorch backend.

XL turbo uses 8 inference steps. On a 16 GB GPU the audio model stays quantized and some components are offloaded so the weights fit.

## Run it

Python 3.11 or 3.12 and [uv](https://docs.astral.sh/uv/) are required. MP3 export needs `ffmpeg` on `PATH`.

```bash
uv sync
uv run acestep-download
uv run acestep-download --model acestep-v15-xl-turbo --skip-main
uv run python studio/server.py
```

The first command installs the project. The next two download the base checkpoints (including the 1.7B planner) and the XL turbo weights. The server then listens on `127.0.0.1:7870` and loads the models in the background. The page stays on “Starting the studio” until that load finishes.

Generated audio, the virtual environment, and `checkpoints/` stay on this machine. They are not part of the git repository.

## Where this came from

This repository is a fork of ACE-Step 1.5. The Gradio app and API from the original project are still in the tree. This copy’s interface is the studio in `studio/`.
