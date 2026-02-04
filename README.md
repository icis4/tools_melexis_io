# Melexis.IO

Single‑page Melexis.IO demo application that runs in your browser using the Web Serial API. Open `index.html` over HTTPS or `http://localhost` in Chrome/Edge (desktop), connect to a serial device, and send/receive text commands.

## What’s inside

`index.html` contains everything:
- UI with a main pane (connection controls, log, input area) and an integrated right sidebar (quick commands + history)
- Single compact input row (Clear / Save... / TX field / Send) — input behavior toggles moved to Settings
- Web Serial logic (connect / disconnect with robust stream teardown, read/write piping)
- Command history persisted in a cookie (newest first)
- Color‑coded log rendering (customizable via CSS variables)

## Features

- Serial connection settings:
  - Baud rate (default 115200)
  - Data bits: 7 or 8
  - Parity: none/even/odd (default odd)
  - Stop bits: 1 or 2 (default 2)
  - Flow control: none or hardware (RTS/CTS)
- Quick commands (enabled when connected):
  - `*IDN?`, `:SYST:INFO`, `:SYST:LOG`, `:SYST:HELP`, `*RST`
  - Reset flow: waits for `*RESET*` and then `(OK)>`, disconnects, waits ~5s, and auto‑reconnects to the same VID:PID
- Log viewer with colors (dark theme):
  - Local echo (TX): themed blue
  - Device responses (RX): dark text
  - `(OK)>` prompt: electric green
  - Non‑OK prompts: red
  - Errors: deep red
    - Status line shows USB VID:PID (hex), serial (if available) and the first *IDN? response line (device description). (OS COM port name/path isn't exposed by Web Serial.)
- Clear / Save... buttons in Terminal; input behavior options relocated to Settings tab and persisted.
- Command history:
  - Stores only typed input (not button/auto commands)
  - Newest‑first; up/down arrows to navigate; click a history item to resend
  - Clearable; persisted in a cookie (bounded size)
  

**Settings tab**

- Two-column responsive layout:
  - Left: Connection fieldset (baud, data bits, parity, stop bits, flow control) – persisted automatically.
  - Right: Input options fieldset + Actions fieldset.
- Input options (persisted): End‑of‑line (No EOL / LF / CR / CRLF), Local echo, Enter sends, Auto‑scroll. Defaults: LF, echo ON, enter sends ON, auto‑scroll ON.
- Actions fieldset: Reset to defaults, Export settings, Import settings, (future) Save/Load EEPROM.
- Reset to defaults button: 115200 baud, 7 data bits, odd parity, 2 stop bits, no flow control, and input options (EOL=LF, Local echo ON, Enter sends ON, Auto‑scroll ON).
 - Export settings button: downloads a JSON bundle (version 5) containing command history (newest‑first), connection parameters, and input options.
 - Import settings button: restores history, connection parameters, and input options. Backward‑compatible with older files where applicable.

## Requirements

- Browser: Chrome or Edge on desktop with Web Serial support
- Context: HTTPS or `http://localhost` (file:// will NOT work for Web Serial)
- Only one app can hold a given serial port at a time

## Run locally

Serve the folder with any static web server and open in Chrome/Edge.

Windows (PowerShell):

```powershell
python -m http.server 8000
```

Node.js (PowerShell):

```powershell
npx serve -l 8000
```

Linux/macOS (bash):

```bash
python3 -m http.server 8000
```

Node.js (bash):

```bash
npx serve -l 8000
```

Then visit: http://localhost:8000/

Tip: If your server serves directory indexes, `index.html` at the project root will open automatically.

## How to use

1. Start a local server (see above) and open the page in Chrome/Edge.
2. Click “Connect…”, pick your serial device, and adjust settings if needed.
3. Type in the input box; choose EOL (LF/CR/CRLF) if your device expects it.
4. Press Enter (when “Enter sends” is checked) or click Send.
5. Use quick command buttons for `*IDN?`, `:SYST:INFO`, `:SYST:LOG`, `:SYST:HELP`, `*RST` (not stored in history).
6. Toggle Local echo and Auto‑scroll as desired; use Clear / Save... to manage the log.


## Keyboard

- Enter: send (when “Enter sends” is enabled)
- Arrow Up/Down: navigate history (newest → oldest)

## Troubleshooting

- “Web Serial API not supported” → Use Chrome/Edge desktop, latest version.
- “Failed to connect” or no ports listed → Ensure your device driver is installed and the port isn’t in use by another app.
- No output / device doesn’t respond → Check baud/format and the EOL option (many devices require LF or CR).
- Opened from file system and Connect is disabled → Serve over HTTPS or `http://localhost`.
 

## Privacy & data

- No telemetry or analytics; everything runs client‑side.
- Command history is stored locally in a cookie for this site.
- Settings (connection + input options) are stored in `localStorage`.
- Serial communication is between your browser and the device; the server only serves static files.

## Layout & UI specifics

| Region | Description |
|--------|-------------|
| Connection controls | Top fieldset with only Connect / Disconnect + status line. |
| Tabs | Terminal, Settings |
| Log | Monospaced scrollback (`#log`) at fixed height (~45vh; capped by viewport calc). |
| Input row | Single compact row: Clear, Save..., TX field, Send (behavior options moved to Settings). |
| Sidebar (right) | Quick command buttons + history list + clear history. |
 

 

On very narrow screens, wrapping will stack controls; the TX + Send pair stays together while secondary controls wrap.

## Theming & customization

Theme and palette are defined via CSS variables in `styles/melexis.css`. Update button, prompt, and panel colors by editing those variables. Prompt colors are controlled by `.log-okprompt`, `.log-badprompt`, `.log-error`. Log height can be adjusted in `index.html` under the `#log` style.


## Connection behavior

- The app always requests a new port selection on Connect (simplifies stale handle issues).
- Disconnect waits for stream piping promises (readable/writable) before closing the port to avoid `InvalidStateError` / "port already open" problems.
- Auto query (`*idn?`) sent immediately after successful open (not added to history).


## Accessibility & UX notes

- High contrast dark palette with sufficient differentiation for prompts and errors.
- Auto‑scroll can be disabled for manual review.
- History buttons are regular `<button>` elements; keyboard users can tab through and press Enter/Space to resend.

## Future ideas (not implemented yet)

- Light/dark theme toggle
- Search/filter within the log
 

Contributions or suggestions welcome via issues / pull requests.

- Command history is stored locally in a browser cookie for this site; you can clear it from the UI.

---

This project is intentionally minimal: a single `index.html` with inline CSS/JS for easy drop‑in use. Customize by editing that one file.
