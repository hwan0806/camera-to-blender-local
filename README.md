# Camera to 3D

Use a webcam to photograph an object and watch a 3D model appear in Blender in under a minute.

Take photo → background removed → 3D model generated → auto-imported into Blender.

This checkout is configured for local-only use on the computer where Blender is running. The server listens on `127.0.0.1` and is not exposed to the LAN or internet.

## You'll need

- [Pixi](https://pixi.sh/latest/installation/)
- Blender 3.0+
- A [Tripo3D](https://platform.tripo3d.ai/) API key — required, this makes the 3D models
- A [Gemini](https://aistudio.google.com/) API key — optional, removes the photo background
- A built-in or connected webcam

## Setup

### 1. Install

```bash
cp .env.example .env
pixi install --locked
```

Put your API keys in `.env`:

```
TRIPO_API_KEY=your_tripo_api_key_here
GEMINI_API_KEY=your_gemini_api_key_here
```

### 2. Start the local-only server

```bash
pixi run server
```

Open `http://127.0.0.1:8001` in a browser on the same computer. Do not run ngrok or add router port forwarding.

### 3. Connect Blender

1. **Edit → Preferences → Add-ons → Install**, pick `ws_import_addon.zip`, enable it
2. Press `N` in the 3D viewport → **WS Import** tab
3. Use this local WebSocket URL:

   ```
   ws://127.0.0.1:8001/ws?client=blender
   ```

4. Click **Connect**. It should say **Status: Connected**

### 4. Use it

Open `http://127.0.0.1:8001`, allow camera access, and point the webcam at an object. Take the photo, confirm it, then wait ~30–60 seconds. When the model is ready, click **Send to Blender**.

## Settings

All optional except the Tripo key. Set these in `.env`:

| Variable | Description |
| --- | --- |
| `TRIPO_API_KEY` | Required. Generates the 3D models |
| `GEMINI_API_KEY` | Removes the photo background. Leave empty to skip it and use the photo as-is |
| `PORT` | Server port, defaults to `8001` |

## Local-only limitation

A phone cannot use this configuration: `localhost` on a phone refers to the phone itself. Keep the server local and use a webcam on the Blender computer.

## If something breaks

**Camera won't open** — confirm that the browser has camera permission for `http://127.0.0.1:8001`.

**"Failed to send to Blender"** — the add-on isn't connected. Check that the WS Import panel says Connected and uses `ws://127.0.0.1:8001/ws?client=blender`.

**Background removal fails** — usually a Gemini quota or key problem. You can leave `GEMINI_API_KEY` empty to skip it.

## What's in here

```
relay_server.py          The server: talks to the APIs, relays models to Blender
pixi.toml / pixi.lock    Reproducible Python 3.12 server environment
webapp/                  The phone camera app
blender_addon/           Add-on source
ws_import_addon.zip      The add-on to install in Blender
```

If you edit the add-on source, repackage it before installing:

```bash
zip -r ws_import_addon.zip blender_addon -x "*/__pycache__/*" "*.pyc"
```

## License

MIT — see [LICENSE](LICENSE).
