# RawFrame

A screen, webcam and audio recorder for Windows. RawFrame records your whole desktop, a single
monitor or a single application window to an MP4 file, with an optional webcam overlay and audio
from your microphone and your speakers — locally, with no account and no internet connection.

Version **1.0.1** · Windows 10 (build 19041+) and Windows 11, x64 · single `.exe`, no installer

---

## Features

- Three capture sources: entire screen (all monitors as one frame), one monitor, or one application
  window.
- Optional webcam overlay composited into the recording, with a visual layout editor.
- Optional floating webcam preview on your screen that is excluded from the recording itself.
- Microphone and system-audio recording, mixed into one audio track, each with its own volume.
- Live input level meters for microphone and system audio while recording.
- Pause and resume — paused time is removed from the finished file, so audio and video stay in sync.
- Four quality presets plus a custom mode for resolution, frame rate and bitrate.
- Hardware-accelerated H.264 encoding when the machine can provide it, with automatic fallback to
  software so a recording is never lost to a missing GPU feature.
- Recording history with search, date and source filters, and per-recording open / reveal / delete.
- Taskbar progress while recording, and the option to minimise the window during a recording.
- Optional start with Windows.
- Local log files for troubleshooting, pruned automatically after 14 days.

## Recording and audio

**Video capture** uses the Windows Graphics Capture API (the same mechanism Windows itself uses for
window and screen sharing), composited on the GPU with Direct3D 11. The mouse cursor can be
included or excluded per recording.

**Webcam overlay** — when enabled, the camera picture is drawn into every recorded frame. The layout
editor lets you set:

- position: four corner presets, or drag the picture anywhere on the frame
- size: 8% to 50% of the frame width, aspect ratio locked
- shape: rectangle, rounded rectangle or circle, with adjustable corner rounding
- corner gap (margin) for the corner presets
- border: on/off, colour and thickness
- mirror the camera in the recording, independently of how the preview looks

The floating webcam window is always on top, can be dragged anywhere, resized from its bottom-right
corner and hidden with a double-click. It is marked as excluded from screen capture, so it never
appears in the recording or in anyone else's screen share.

**Audio** — two independent sources, both optional and both mixed into the single AAC track:

| Source | What it records | Volume |
| --- | --- | --- |
| Microphone | any input device on the machine, chosen per recording | 0–200% |
| System audio | loopback of the **default playback device** | 0–200% |

Audio is captured through WASAPI and normalised to 48 kHz, stereo, 16-bit before encoding. Devices
whose native format differs are resampled and channel-converted automatically.

## Resolution, frame rate and output format

| Preset | Resolution | Frame rate | Bitrate |
| --- | --- | --- | --- |
| Low | 720p | 30 FPS | 5 000 kbps |
| Standard | 1080p | 30 FPS | 8 000 kbps |
| High | 1080p | 60 FPS | 12 000 kbps |
| Ultra | 1440p | 60 FPS | 24 000 kbps |
| Custom | source / 720p / 1080p / 1440p / 4K | 24, 30 or 60 FPS | 1 500 – 80 000 kbps |

Three rules apply to every recording:

- **Never upscaled.** A preset is a ceiling, not a target — a 1080p source recorded at Ultra stays
  1080p.
- **Aspect ratio preserved.** The preset names the height; the width follows from the source, so an
  ultrawide monitor stays ultrawide.
- **Even dimensions.** Both sides are rounded down to an even number, which H.264 requires.

**Output:** MP4 container, H.264 video, AAC audio at 160 kbps. Files are named
`Recording_YYYY-MM-DD_HH-MM-SS.mp4`; a name that already exists gets `_2`, `_3` and so on. The
default folder is `Videos\RawFrame`, and can be changed per recording or in Settings.

## Controls and hotkeys

**RawFrame has no keyboard shortcuts and no global hotkeys.** Every action — start, stop, pause,
resume, choosing sources and devices — is a button or a control in the window. This is deliberate:
nothing is registered system-wide, so RawFrame cannot interfere with shortcuts in the application
you are recording.

Standard Windows keyboard navigation works as usual: `Tab` moves between controls, `Space` or
`Enter` activates the focused button, and the arrow keys adjust sliders and dropdowns.

Mouse controls in the webcam overlay editor: drag the picture to move it, drag the grip on its
bottom-right corner to resize it.

## Settings

Settings are saved to `settings.json` when you press **Save settings**. Everything on the Record
page can also be saved as the new defaults with **Save as defaults**.

- **General** — start RawFrame with Windows; minimise the window while recording; show recording
  progress on the taskbar and bring the window back when the recording finishes.
- **Recording defaults** — default source, quality preset, and (for Custom) resolution, frame rate
  and bitrate; whether to include the mouse cursor.
- **Video encoder** — Automatic, NVIDIA NVENC, Intel Quick Sync, AMD AMF or Software. The page shows
  which adapter this machine has, and **Check this machine** runs a capability probe that reports
  what the machine can actually do without writing anything to disk.
- **Webcam** — include the camera by default; show the floating preview by default.
- **Audio** — enable microphone and/or system audio by default, and their default volumes. The
  devices themselves are chosen per recording on the Record page.
- **Remembered devices** — shows the last camera and audio devices used, with a button to forget
  them.
- **Directories** — save folder, and shortcuts to the log folder and the settings file.
- **Reset to defaults** restores every setting above.

## Installation and usage

1. Download `RawFrame.exe` from the [Releases](../../releases) page.
2. Run it. There is nothing to install — it is a single self-contained executable and does **not**
   require .NET to be installed.
3. The first launch takes a second or two longer than later ones, because the native components
   unpack into your temporary folder once.

The executable is not code-signed, so Windows SmartScreen may warn you the first time you run it.
Choose *More info → Run anyway* if you are happy to proceed.

**Recording:**

1. On the **Record** page, pick a source — entire screen, a monitor, or a window — and check the
   preview.
2. Choose a quality preset, and turn on the microphone, system audio and webcam if you want them.
3. Press **Start recording**. Pause and resume as needed, then press **Stop recording**.
4. When the recording is saved, **Open video** and **Show in folder** appear. Everything you have
   recorded is also listed on the **History** page.

## System requirements

| | |
| --- | --- |
| Operating system | Windows 10 version 2004 (build 19041) or newer, or Windows 11 |
| Architecture | x64 only |
| Runtime | none — .NET is included in the executable |
| Graphics | a Direct3D 11 capable adapter; hardware H.264 encoding when available, software otherwise |
| Permissions | runs as a normal user; no administrator rights required |
| Optional hardware | any webcam and microphone Windows recognises |

## Privacy and security

- **No network access whatsoever.** RawFrame contains no networking code: no telemetry, no
  analytics, no accounts, no update checks, no crash reporting, nothing uploaded anywhere.
- **Your recordings never leave your machine.** They are written only to the folder you choose.
- **Settings and history** are plain files in `%APPDATA%\RawFrame` (`settings.json` and
  `history.json`). The history file records file paths, sizes and timings — never video or audio.
- **Logs** are written to `%LOCALAPPDATA%\RawFrame\logs\app-YYYYMMDD.log` and deleted automatically
  after 14 days. They contain device names, file paths and recording statistics, not media.
- **Camera and microphone** are opened only while you have them enabled for a recording, and
  released when it ends.
- **Start with Windows** is a per-user registry value under
  `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`, written only when you turn the switch on and
  removed when you turn it off. Nothing is installed system-wide.
- Uninstalling is deleting the `.exe`; to remove everything, also delete `%APPDATA%\RawFrame` and
  `%LOCALAPPDATA%\RawFrame`.

## Known limitations

- **Window recording uses a fixed frame size.** An MP4 file holds one frame size from beginning to
  end, so the size is decided when you press Start. If the window is made larger during the
  recording, the extra area is cropped; if it is made smaller, it is centred with a black margin.
  Neither case is rescaled. *Set the window to the size you want before pressing Start.*
- **Minimising a recorded window** stops Windows from delivering new frames; RawFrame holds the last
  frame, so the file has no gap and the timeline stays correct.
- **Closing a recorded window** ends the capture; the recording is finalised and stays playable.
- **A specific hardware encoder cannot be forced.** Windows chooses the encoder from the installed
  driver, and the only real control an application has is hardware on or off. Picking a vendor is
  therefore a request rather than a guarantee, and *Automatic* is the honest choice.
- **System audio is the default playback device's loopback only.** There is no per-application audio
  and no way to record a non-default output device.
- One microphone and one system-audio source per recording.
- Changes to the webcam overlay layout made during a recording apply to the *next* recording, not to
  the file already being written.
- **MP4 (H.264 + AAC) is the only output format.** No other container or codec, no GIF, and no still
  screenshots.
- **No editing.** RawFrame records; it does not trim, cut, merge or post-process.
- **No installer and no auto-update.** New versions are downloaded manually.
- **x64 Windows only.** There is no 32-bit or ARM64 build, and no macOS or Linux version.
- No keyboard shortcuts or global hotkeys (see *Controls and hotkeys* above).

## Download

Compiled releases are published on the [Releases](../../releases) page.

| File | Description |
| --- | --- |
| `RawFrame.exe` | Windows x64, self-contained single file. Download and run. |

Only the compiled executable is published. **The source code of this project is private and is not
distributed.**

## Screenshots

<!-- Screenshots to be added. -->

| | |
| --- | --- |
| _Record page_ | _screenshot coming soon_ |
| _History page_ | _screenshot coming soon_ |
| _Settings page_ | _screenshot coming soon_ |

## Changelog

### 1.0.1

- Fixed the window maximising over the taskbar. Because the window draws its own title bar it is a
  popup window, which Windows maximises to the whole monitor rather than to the area above the
  taskbar; the bottom of the sidebar — the author credit, the links and the version — and the bottom
  of every scrollable page ended up behind the always-on-top taskbar. The window now maximises to the
  monitor's work area, on whichever monitor it is on and wherever the taskbar is docked.

### 1.0.0

First public release.

- Screen, monitor and application-window recording to MP4 (H.264 + AAC).
- Microphone and system-audio recording with per-source volume and live level meters.
- Webcam overlay with a visual layout editor, and an optional floating preview that is excluded from
  the capture.
- Four quality presets plus custom resolution, frame rate and bitrate.
- Hardware encoder selection with automatic fallback to software.
- Pause and resume, with paused time removed from the finished file.
- Recording history with search, filters and file management.
- Settings for defaults, directories, start with Windows, and an on-demand encoder capability check.
- Local logging with automatic 14-day cleanup.

## License

Copyright © 2026 Josh Mercado. All rights reserved.

RawFrame is proprietary software. You are granted permission to download and use the published
executable free of charge, for personal or internal use, on any number of machines you own or
administer.

You may **not**:

- re-upload, mirror, publish or otherwise distribute RawFrame or any part of it, on any platform;
- sell, rent, license, sublicense or offer RawFrame in exchange for money, credit or any other
  consideration, whether on its own or bundled with anything else;
- rebrand, rename, re-skin, repackage or present RawFrame as your own work, or as another product;
- claim authorship or ownership of RawFrame, or remove or alter its name, icon, credits or
  attribution;
- decompile, disassemble, reverse engineer, modify or create derivative works from the executable,
  except where such a restriction is expressly prohibited by applicable law.

No source code, and no rights other than those stated above, are granted. All other rights are
reserved by the author. This permission ends automatically if you break any of the terms above.

## Disclaimer

RawFrame is provided "as is", without warranty of any kind, express or implied, including but not
limited to the warranties of merchantability, fitness for a particular purpose and
non-infringement. The author is not liable for any claim, damage, data loss, lost recording or
other liability arising from the use of this software.

You are responsible for how you use it. Recording screens, audio, cameras or other people may be
subject to laws, workplace policies or terms of service where you are — make sure you have the
consent and the right to record before you do.

## Project status

**Complete and in active personal use.** 1.0.0 was the first release and 1.0.1 is the current one;
every feature described above is implemented and working. Development is occasional: fixes and
improvements are released when they are needed.

The source code is private. Only compiled `.exe` releases are published here, so there are no pull
requests — but bug reports and suggestions are welcome through
[Issues](../../issues).

---

Made by [Josh Mercado](https://github.com/Khali-0725)
