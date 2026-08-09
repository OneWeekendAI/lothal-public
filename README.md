# Lothal

**Build an FPV drone from real parts. Fly it. Feel the difference.**

![Lothal: the build panel's live stats, and a run at the first gate](preview.gif)

Lothal is a drone design workbench and flight simulator. You assemble a quadcopter from a
catalog of real components — frames, motors, propellers, batteries, ESCs, flight controllers —
and every choice changes the physics. Swap a 4S pack for 6S and the thrust-to-weight, hover
throttle and flight time all recompute; then you fly it and feel the difference.

Nothing is a canned asset. Prop rotation is integrated from actual RPM, voltage sag is computed
from the pack's internal resistance and the current the motors are really drawing, and the
flight model runs at 1 kHz off measured manufacturer thrust data.

---

## Download

Current release: **v0.2.0**, macOS 11+ (Universal — Intel and Apple Silicon).

**Windows is still on v0.1.0.** v0.2.0 was packaged for macOS only, so the Windows block below
deliberately stays on the older version — it is the newest Windows build that exists.

Each block below downloads the release, checks it against the published SHA-256, and installs it.
Paste the whole thing; it is written to stop rather than continue if the checksum does not match.

### macOS

```bash
VER=0.2.0
curl -fLO "https://dl.meetdev.in/v$VER/Lothal-$VER-macos-universal.zip"
curl -fLO "https://dl.meetdev.in/v$VER/SHA256SUMS.txt"
shasum -a 256 -c --ignore-missing SHA256SUMS.txt || { echo "CHECKSUM FAILED — do not open it"; return 2>/dev/null || exit 1; }
ditto -x -k "Lothal-$VER-macos-universal.zip" /Applications
xattr -dr com.apple.quarantine /Applications/Lothal.app
open /Applications/Lothal.app
```

Use `ditto`, not the Finder or `unzip`, to unpack it. `ditto` is how the archive was created, it
extracts the app bundle straight to `/Applications` without leaving a `__MACOSX` folder behind,
and it is the extraction path the release is tested against.

### Windows (PowerShell)

Still v0.1.0 — see the note above.

```powershell
$Ver = "0.1.0"
$Zip = "Lothal-$Ver-windows-x64.zip"
Invoke-WebRequest "https://dl.meetdev.in/v$Ver/$Zip" -OutFile $Zip
Invoke-WebRequest "https://dl.meetdev.in/v$Ver/SHA256SUMS.txt" -OutFile "SHA256SUMS.txt"

$Want = (Select-String -Path SHA256SUMS.txt -Pattern "windows-x64").Line.Split(" ")[0]
$Got  = (Get-FileHash $Zip -Algorithm SHA256).Hash
if ($Got -ine $Want) { throw "CHECKSUM FAILED - do not run it" }

Expand-Archive $Zip -DestinationPath "$env:LOCALAPPDATA\Lothal" -Force
Remove-Item "$env:LOCALAPPDATA\Lothal\._*" -ErrorAction SilentlyContinue
& "$env:LOCALAPPDATA\Lothal\Lothal.exe"
```

The archive is built on a Mac, so it contains two stray `._Lothal.*` metadata files. They do
nothing on Windows; the `Remove-Item` line above just tidies them away.

### Always-current links

This redirects to whatever the newest release is, so it does not go stale — useful for a browser
or a script that should not pin a version:

- **macOS** — <https://dl.meetdev.in/latest/macos>

There is no `latest/windows` while v0.2.0 is macOS-only; it would have to resolve to a v0.1.0
payload, and a link called "latest" that quietly hands over an older release is worse than no
link. Use the versioned Windows block above.

It redirects to a versioned filename. If you fetch them with `curl`, use `-o` to name the file
yourself: `curl -fLo Lothal.zip https://dl.meetdev.in/latest/macos`, because `curl -O` would save
it as a file literally called `macos`.

---

## About the security warnings

Lothal is **not signed** by Apple or Microsoft. Code signing certificates cost money per year,
and this is an early release — that is coming, but it is not here yet. Both operating systems
will warn you, and you should know exactly what the warning means before you click past it.

**The warning is real, and it is not specific to Lothal.** It says the OS cannot verify who
published this software. That is true. The way to close the gap yourself is the checksum, which
is why it is built into the commands above rather than left as an optional extra step: if the
hash matches, the file you have is the file that was published.

**macOS.** The bundle is ad-hoc signed, so it will run — but Gatekeeper quarantines anything
downloaded from a browser or `curl`. The `xattr -dr com.apple.quarantine` line above clears that.
If you installed it some other way, right-click the app and choose **Open** once instead.

**Windows.** SmartScreen will show **"Windows protected your PC"** the first time. Click
**More info** → **Run anyway**. This persists until the download builds reputation or an EV
certificate exists.

### Updates

Lothal checks for new releases on startup and shows a one-line bar when one exists. It never
downloads or installs anything on its own — the bar opens the release page in your browser and
you decide from there. The release manifest is cryptographically signed, so a compromised
download host cannot point your copy of Lothal at a payload that is not ours.

---

## Activation, and the one thing we store

Lothal asks you to activate it once, with a Google sign-in, before it opens.

**What is stored: your email address. That is the whole list.** No name, no telephone number, no
usage data, no telemetry, and nothing about what you build or fly. It is held in Google Firebase
Authentication, and it is used for one purpose — telling you when a new version of Lothal is
released. It is not sold, not shared with anyone, and not used for anything else.

How it works:

1. You open the activation page and sign in with Google.
2. The page gives you an activation key — a small signed file.
3. You paste it into Lothal, or open the downloaded `.lothalkey` file.

The key is verified on your own machine, using arithmetic and a key built into the application.
**Lothal never contacts the internet to check it** — not when you activate, and not at any launch
afterwards. Once activated, Lothal works offline permanently, and it keeps working even if this
project's servers go away entirely. You need a connection for step 1 and never again.

Signing in again always returns the same key, so you can activate as many of your own machines as
you like, and losing the key costs you nothing more than signing in a second time.

Your address is shown in the application as `Activated — you@example.com`, so you can always see
which account a copy is activated against.

**To have your address removed**, [open an issue](https://github.com/OneWeekendAI/lothal-public/issues)
or contact us at the address in [NOTICE](NOTICE). It is deleted from the authentication table, and
your existing installation carries on working — because nothing is checked at launch, removal
cannot break a copy of Lothal you already activated.

---

## Requirements

- **macOS** 11 Big Sur or later, Intel or Apple Silicon
- **Windows** 10 or later, 64-bit
- A GPU with Vulkan support (anything from roughly 2016 onward)
- **A gamepad is strongly recommended.** Keyboard input is digital — usable for testing,
  not a way to fly well.

---

## Controls

|  | Gamepad | Keyboard |
| --- | --- | --- |
| Roll / pitch | Right stick | Arrow keys |
| Yaw | Left stick ←→ | <kbd>A</kbd> <kbd>D</kbd> |
| Throttle | Left stick ↑↓ | <kbd>W</kbd> <kbd>S</kbd> |
| Angle ↔ acro | <kbd>A</kbd> button | <kbd>Space</kbd> |
| Hide build panel | — | <kbd>Tab</kbd> |

---

## Lab and Sim

Lothal opens on **Lab** — the garage. Nothing flies there. You pick parts from the catalog
rails and the airframe on screen is generated from them: arms as long as the spec says, motor
bells the size of the stator, propeller blades twisted to the angle the pitch implies. Every
derived stat and compatibility warning moves with each change. There is no apply button.

Because the render is built from real dimensions, it doubles as the fit check. Put 7" props on
a 3" frame and you can watch them intersect the arms — the warning in the panel and the picture
are the same fact.

**Sim** is the field, reached through its tab. It flies exactly what Lab built, from the same
numbers. Fly the eight-gate circuit and find out whether your build is actually faster.

There are also four benches — thrust, pack, ESC and frame — where a single component is tested
in isolation against the rest of your build.

---

## What Lothal is not

It is a simulator, and its physics, component models and plausibility checks are approximations.
Several of them say so inside the app, naming the assumption and what would settle it. Lothal is
built to be honest about that rather than to feel authoritative: where a number rests on an
unvalidated constant, it tells you which one.

**It is not engineering advice.** Build, test and fly real aircraft safely and lawfully.

---

## Feedback

Bug reports and feature requests: [open an issue](https://github.com/OneWeekendAI/lothal-public/issues).

Useful things to include: your OS and version, your build (the parts list), and what you
expected to happen. If it is a physics complaint, the numbers you expected and where they came
from are worth more than anything else you could send.

---

## License

© 2026 Meetdev. All rights reserved.

Lothal is proprietary software. You may install and use the released binaries on machines you
own or control. Redistribution, reverse engineering and derivative works are not permitted.
See [NOTICE](NOTICE) for the full terms.

Built with the [Godot Engine](https://godotengine.org) (MIT).
