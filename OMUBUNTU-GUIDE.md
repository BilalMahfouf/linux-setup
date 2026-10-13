# Ombuntu Guide — This Machine

> Generated 2026-10-13 from a read-only audit of this installation, updated 2026-10-14 for the COSMIC-style shortcut overlay.
> It describes **your actual system**, not a generic Hyprland setup.
> Shortcuts follow **COSMIC (System76) semantics** via `~/.config/hypr/cosmic.conf` (strict mode), loaded after the Omarchy defaults.
> Conventions: **(verified)** = seen in your config or confirmed by a command on this machine (`hyprctl binds` = 254 bind lines after the overlay).
> **(upstream default)** = standard behavior of the installed component, not customized here.
> **(unverified)** = could not be confirmed — treated as unknown.

## Table of contents

1. [Overview of this installation](#1-overview-of-this-installation)
2. [Quick start — the shortcuts that matter most](#2-quick-start--the-shortcuts-that-matter-most)
3. [Keyboard shortcut reference](#3-keyboard-shortcut-reference)
4. [Daily desktop usage](#4-daily-desktop-usage)
5. [Developer workflow](#5-developer-workflow)
6. [Configuration file map](#6-configuration-file-map)
7. [Troubleshooting and recovery](#7-troubleshooting-and-recovery)
8. [Installed components and their roles](#8-installed-components-and-their-roles)
9. [Verified vs inferred vs unknown](#9-verified-vs-inferred-vs-unknown)
10. [Diagnostic appendix](#10-diagnostic-appendix)

---

## 1. Overview of this installation

| Item | Value (verified) |
|---|---|
| OS | Ubuntu 26.04.1 LTS (Resolute), kernel `7.0.0-38-generic` |
| Session (login screen → Ombuntu) | `/usr/share/wayland-sessions/ombuntu.desktop` → `uwsm start … Hyprland` |
| Compositor / window manager | Hyprland 0.53.3 (dynamic tiler, Wayland-only) |
| Layout in use | `dwindle` (verified via `hyprctl getoption general:layout`) |
| Other installed sessions | `hyprland`, `hyprland-uwsm`, `niri`, `ubuntu` (GNOME). Nothing was removed — you can pick another session at login |
| Shell | Bash (`/bin/bash`), with Omarchy defaults + starship prompt, `set -o vi`, fastfetch greeting |
| Default terminal | Alacritty (`omarchy-default-terminal` → `alacritty`) |
| Default browser | Chrome (`omarchy-default-browser` → `chrome`; `brave-browser` also installed) |
| Default editor | Neovim v0.12.6 (`omarchy-default-editor` → `nvim`) |
| File manager | Nautilus (opens new windows) |
| App launcher | Walker (+ Elephant backend; both running as user services) |
| Top bar | Waybar v0.15.0 |
| Notifications | mako (+ `makoctl`) |
| Wallpaper | `swaybg`, image `~/.config/omarchy/current/background` → Ombuntu horizon PNG |
| Theme | `tokyo-night` (`~/.config/omarchy/current/theme.name`) |
| Lock / idle | hyprlock + hypridle (screensaver ~2.5 min, lock ~5 min) |
| Monitor | One laptop panel `eDP-1`, 1920×1080@60, scale 1 |
| Base system | Upstream Omarchy pinned at `v3.8.4`, adapted to Ubuntu = "Ombuntu" v0.1.4 (`~/ombuntu/`) |
| Command dispatcher | `~/.local/bin/omarchy` and `~/.local/bin/ombuntu` are symlinks to the same script; every `omarchy-*` command is also reachable as `ombuntu-*` |

**How the pieces fit (one paragraph):** `uwsm` starts and supervises your graphical session. Hyprland tiles your windows and owns all global shortcuts. Walker launches apps, Waybar shows status, mako shows notifications, swaybg paints the wallpaper, hypridle/hyprlock handle idle and locking, PipeWire + SwayOSD handle audio/brightness feedback. Alacritty + Bash is where you type; tmux (optional) multiplexes terminals inside one window.

---

## 2. Quick start — the shortcuts that matter most

`Super` = the Windows key (between Ctrl and Alt). COSMIC-style: `Super` alone opens the launcher. All rows verified in live binds.

| Keys | Action | Plain English |
|---|---|---|
| `Super` (tap) / `Super` + `Space` / `Super` + `/` | Walker launcher | App launcher / search (type, `Enter` to open) |
| `Super` + `T` (`Enter` also works) | Terminal | Open Alacritty in the current project's directory |
| `Super` + `F` | Files | Open Nautilus |
| `Super` + `B` | Browser | Open the default browser (Chrome) |
| `Super` + `Q` | `killactive` | Close the focused window |
| `Super` + `←↑↓→` or `H`/`J`/`K`/`L` | `movefocus` | Move focus between tiled windows |
| `Super` + `1`…`9` / `Super` + `0` | `workspace 1…9` / last workspace | Jump to workspace N, or back to where you were |
| `Super` + `Shift` + `1`…`9` / `0` | `movetoworkspace` | Send the focused window there and follow it |
| `Super` + `G` / `Super` + `M` | float / maximize toggle | Free a window from tiling / maximize it (`F11` = fullscreen) |
| `Super` + `Esc` | Lock now | `omarchy-system-lock` → hyprlock |
| `Super` + `K` | Keybinding list | Searchable list of your real bindings (via Walker) |
| `Super` + `Shift` + `K` | Cheatsheet | Beginner cheatsheet in a floating browser window |

Survival extras: `Print` = screenshot, `Super` + `Tab` = window switcher, `Super` + `S` = window group (stacking), `Super` + `Shift` + `S` = scratchpad (relocated sticky window).
Omarchy → COSMIC moved keys: `W` close → `Q` · `T` float → `G` · `F` fullscreen → `F11` · `S` scratchpad → `Shift+S` · `Esc` system menu → lock · `Tab` next-workspace → window switcher.

---

## 3. Keyboard shortcut reference

Sources (all verified on disk + live): your `~/.config/hypr/bindings.conf` (app launchers), `~/.config/hypr/cosmic.conf` (COSMIC overlay, strict mode), plus the shipped defaults `~/.local/share/omarchy/default/hypr/bindings/tiling-v2.conf`, `utilities.conf`, `media.conf`, `clipboard.conf`. Total: 254 binds (`hyprctl binds | grep -c "^bind"`).

**Reading the tables:** `code:10…19` means the physical number-row keys 1–0, so bindings work regardless of keyboard layout. Mouse binds (`Super` + scroll / drag) are included where configured.

### 3.1 Opening things

| Keys | Runs (verified) | Notes |
|---|---|---|
| `Super` + `Enter` | `xdg-terminal-exec --dir="$(ombuntu-cmd-terminal-cwd)"` | Terminal rooted at the active window's project dir (kept as compat alias) |
| `Super` + `T` | same terminal command | COSMIC primary terminal key |
| `Super` + `F` | `nautilus --new-window` | COSMIC primary file-manager key (was fullscreen) |
| `Super` + `Alt` + `Enter` | same terminal + `tmux attach \|\| tmux new -s Work` | Jump straight into (or create) tmux session `Work` |
| `Super` + `Shift` + `Enter` / `Super` + `Shift` + `B` | `ombuntu-launch-browser` | Default browser (Chrome); COSMIC primary is the simpler `Super` + `B` |
| `Super` + `B` | `omarchy-launch-browser` | COSMIC primary browser key |
| `Super` + `Shift` + `Alt` + `B` | browser `--private` | Private window |
| `Super` + `Shift` + `F` | `nautilus --new-window` | File manager |
| `Super` + `Shift` + `Alt` + `F` | nautilus at terminal cwd | File manager in current project dir |
| `Super` + `Shift` + `N` | `ombuntu-launch-editor` | Neovim in terminal |
| `Super` + `Shift` + `D` | `ombuntu-launch-tui lazydocker` | **Broken ref:** `lazydocker` is not installed |
| `Super` + `Shift` + `G` | Signal via `ombuntu-launch-or-focus` | Focuses Signal if already open, else launches |
| `Super` + `Shift` + `O` | Obsidian | **Broken ref:** `obsidian` not installed |
| `Super` + `Shift` + `M` / `+Alt` + `M` | Spotify / `cliamp` TUI | **Broken refs:** neither is installed |
| `Super` + `Shift` + `W` | Typora | **Broken ref:** `typora` not installed |
| `Super` + `Shift` + `A/Y/X/P`, `+Alt` variants | Web apps (ChatGPT, YouTube, X, Photos, WhatsApp, …) | Open as Chrome app windows; need a Chromium browser (you have Chrome ✓) |

### 3.2 Windows: focus, move, resize, float

| Keys | Action | Notes |
|---|---|---|
| `Super` + `Q` / `Alt` + `F4` | Close focused window | Same as clicking ✕ (`Super`+`W` now opens the overview — see §3.4) |
| `Super` + `Alt` + `Esc` | Terminate window | `killactive` |
| `Ctrl` + `Alt` + `Delete` | Close **all** windows | Destructive — use deliberately |
| `Super` + `←↑↓→` or `H`/`J`/`K`/`L` | Focus neighbor | Core tiling navigation (HJKL added by COSMIC overlay) |
| `Super` + `Shift` + `←↑↓→` or `H`/`J`/`K`/`L` | Swap window with neighbor | Reorders tiles (COSMIC "Move"; arrows were already bound, HJKL added) |
| `Super` + `X`, then arrows/HJKL, `Esc`/`Enter` to exit | Swap mode | COSMIC SwapWindow emulation via submap |
| `Super` + drag with left mouse | Move window | `Super` + right-drag resizes |
| `Super` + `R` / `Super` + `Shift` + `R`, then arrows/HJKL, `Esc`/`Enter` to exit | Resize mode (outwards/inwards) | COSMIC resize emulation via submap (replaces `Super` + `-`/`=`, which still work underneath) |
| `Super` + `-` / `=` | Widen/narrow 100 px | Kept from defaults; `-` expands left, `=` shrinks |
| `Super` + `Shift` + `-` / `=` | Taller/shorter 100 px | Kept from defaults |
| `Super` + `O` | `togglesplit` | Flip split direction of the tile (was `Super`+`J`) |
| `Super` + `G` | Toggle floating/tiling | Floating windows drag freely, stay centered 875×600 (was `Super`+`T`) |
| `Super` + `M` / `Super` + `F11` | Maximize (keep gaps) / fullscreen | COSMIC pair; `Esc` or repeat to exit |
| `Super` + `S` / `Super` + `Alt` + `G` | Group / ungroup windows | COSMIC stacking = Hyprland tabbed groups; `Super`+`Alt`+`Tab` cycles inside a group (was `Super`+`G`) |
| `Super` + `Y` | Toggle workspace layout | dwindle ↔ alternate via `omarchy-hyprland-workspace-layout-toggle` (was `Super`+`L`) |
| `Super` + `P` | Pseudo-tile (dwindle) | Kept from defaults |
| `Super` + `Tab` / `Super` + `Shift` + `Tab` | Cycle windows on workspace | COSMIC window switcher + bring-to-top (was workspace cycling; `Alt`+`Tab` also still works) |
| `Super` + `U` / `I` | — | No Hyprland equivalent to COSMIC tiling-tree select — intentionally unmapped |

### 3.3 Workspaces, monitors, scratchpad

10 workspaces exist, but COSMIC uses 9 + last: `Super`+`1`…`9` jump, `Super`+`0` returns to the last workspace (workspace 10 is no longer bound).

| Keys | Action | Notes |
|---|---|---|
| `Super` + `1`…`9` / `Super` + `0` | Go to workspace 1–9 / last workspace | `0` = `workspace, previous` |
| `Super` + `Shift` + `1`…`9` / `0` | Move window there **and follow** | `0` = `movetoworkspace, previous` |
| `Super` + `Shift` + `Alt` + `1`…`9` / `0` | Move window there **silently** (stay put) | Best for parking builds/logs |
| `Super` + `Ctrl` + `←↑↓→` or `H`/`J`/`K`/`L` | Previous / next workspace | COSMIC workspace navigation |
| `Super` + `Shift` + `Ctrl` + `←↑↓→` or `H`/`J`/`K`/`L` | Move window to previous / next workspace | COSMIC move-across-workspaces |
| `Super` + `Alt` + `←↑↓→` or `H`/`J`/`K`/`L` | Focus monitor in that direction | COSMIC output navigation (replaces `moveintogroup` on these keys — grouping is on `S` now) |
| `Super` + `Tab` | Window switcher, not workspace cycling | See §3.2 |
| `Super` + `scroll` | Scroll through workspaces | Kept from defaults |
| `Super` + `Shift` + `S` | Toggle scratchpad | Relocated — `Super`+`S` is stacking now |
| `Super` + `Shift` + `Alt` + `S` | Send window to scratchpad | Relocated |
| `Super` + `Shift` + `Alt` + `←↑↓→` | Move workspace to another monitor | Kept from defaults; single-monitor machine: no visible effect today |
| `Ctrl` + `Alt` + `Tab` (+`Shift`) | Focus next/previous monitor | Kept from defaults; ditto |
| `Super` + `/` (`+Alt` = reverse) | Cycle monitor scaling | Handy when you plug in an external display |

### 3.4 Menus, bar, settings panels

| Keys | Action |
|---|---|
| `Super` (tap) / `Super` + `Space` / `Super` + `/` | Walker launcher: apps, files (`.` prefix), symbols (`:`), calculator (`=`), web (`@`), clipboard (`$`), provider list (`/`) |
| `Super` + `A` | Walker (COSMIC App Library approximation — Walker has no separate grid) |
| `Super` + `W` | Walker (COSMIC Workspace Overview approximation — Walker has no overview provider; `Super`+`Tab` is the real switcher) |
| `Super` + `Alt` + `Space` | `omarchy-menu` (the master menu: System, Style, Setup, Install, Learn, …) |
| `Super` + `Esc` | Lock now (`omarchy-system-lock`); system menu moved to `Super` + `Alt` + `Space` → System |
| `Super` + `K` | Searchable keybinding list (`omarchy-menu-keybindings` — reads your live config) |
| `Super` + `Shift` + `K` | Beginner cheatsheet (`ombuntu-cheatsheet`, needs Chrome — installed ✓) |
| `Super` + `Ctrl` + `E` | Emoji/symbol picker |
| `Super` + `Ctrl` + `C/O/H` | Capture / Toggle / Hardware menus |
| `Super` + `Ctrl` + `A/B/W` | Audio / Bluetooth / Wi-Fi panels (floating TUIs) |
| `Super` + `Ctrl` + `T` | `btop` system monitor (floating) |
| `Super` + `Ctrl` + `Space` / `+Shift` +`Ctrl` + `Space` | Background menu / Theme menu |
| `Super` + `Shift` + `Space` | Show/hide Waybar |
| `Super` + `Backspace` (+`Shift`/`+Ctrl`) | Toggle window transparency / gaps / single-window square ratio |
| `Super` + `Ctrl` + `,`, `Super` + `,` … | Dismiss/invoke/restore/silence mako notifications (see §4.9) |

### 3.5 Capture, copy/paste, lock

| Keys | Action | Backend (verified) |
|---|---|---|
| `Print` | Screenshot (`omarchy-capture-screenshot`) | grim + slurp + satty (annotate) — all installed ✓ |
| `Alt` + `Print` | Screen-record menu | `omarchy-capture-screenrecording` |
| `Super` + `Print` | Color picker | `hyprpicker` ✓ (copies hex) |
| `Super` + `Ctrl` + `Print` | OCR: extract text from a region | `omarchy-capture-text-extraction` |
| `Super` + `C` / `V` / `X` | Universal copy / paste / cut | Sends `Ctrl`+`Insert` / `Shift`+`Insert` / `Ctrl`+`X` to the focused window |
| `Super` + `Ctrl` + `V` | Clipboard history (Walker) | |
| In terminals | `Ctrl`+`Shift`+`C` / `Ctrl`+`Shift`+`V` | (upstream default — no custom Alacritty keybinds here) |
| `Super` + `Ctrl` + `L` | Lock now (kept alias) | `omarchy-system-lock` → hyprlock; COSMIC primary is the simpler `Super` + `Esc`; lid-close on external monitors can also trigger internal-display handling |
| `Super` + `Shift` + `Esc` | Log out | `omarchy-system-logout` (COSMIC binding) |
| `XF86PowerOff` key | Power menu | `omarchy-menu system` |

### 3.6 Sound, brightness, playback keys

All verified in `bindings/media.conf`; volume/brightness show an on-screen display (SwayOSD, running ✓).

| Keys | Action |
|---|---|
| Volume up/down/mute, mic mute | `XF86Audio*` keys |
| `Alt` + volume/brightness keys | Fine 1 % steps |
| Brightness up/down (`Shift` = min/max) | `XF86MonBrightness*` via `omarchy-brightness-display` |
| Keyboard backlight up/down/cycle | `XF86KbdBrightness*` |
| Next/pause/play/previous | `XF86Audio*` via playerctl |
| `Super` + mute key | Switch audio output |
| Touchpad toggle/on/off keys | `omarchy-toggle-touchpad` |

### 3.7 Misc (toggles, zoom, reminders)

| Keys | Action | Caveat |
|---|---|---|
| `Super` + `Ctrl` + `I` | Toggle lock-on-idle | |
| `Super` + `Ctrl` + `N` | Toggle nightlight (hyprsunset) | |
| `Super` + `Ctrl` + `Delete` (+`Alt`) | Toggle laptop display (mirror) | Lid-switch binds automate this with external monitors |
| `Super` + `=` / `-` (or `.` / `,`) | Zoom in / out | Cursor zoom factor (COSMIC binding; replaces `Super`+`Ctrl`+`Z` as primary) |
| `Super` + `Ctrl` + `Z` / `+Alt` + `Z` | Zoom in / reset zoom | Cursor zoom factor (kept alias; reset kept on `Super`+`Ctrl`+`Alt`+`Z`) |
| `Super` + `Ctrl` + `R` / combos | Set / show / clear reminders | |
| `Super` + `Ctrl` + `S` | Share menu | |
| `Super` + `Ctrl` + `.` | Transcode menu | |
| `Super` + `Ctrl` + `X`, `F9` hold | Dictation (voxtype) | **Broken ref:** `voxtype` binary is **not** installed; the Waybar mic icon will be inert |
| `Super` + `Ctrl` + `Alt` + `T/B/W` | Notify: time / battery / weather | Click Waybar clock/icons for the same |

### 3.8 tmux (inside a terminal)

Config: `~/.config/tmux/tmux.conf`. **Prefix = `Ctrl`+`Space`** (classic `Ctrl`+`B` also works). "Prefix then key" means press prefix, release, press key.

| Keys | Action |
|---|---|
| `Ctrl`+`Space`, `?` (upstream) | List all tmux keys — the in-app help |
| `Alt`+`Enter` / `Alt`+`Shift`+`Enter` | Split down / right (no prefix needed) |
| prefix `h` / `v`, `Alt`+`Esc` / prefix `x` | Split down / right, kill pane |
| `Ctrl`+`Alt`+arrows (no prefix) | Jump between panes; add `Shift` to resize |
| prefix `c`, `Alt`+`1`…`9`, `Alt`+`←/→` | New window, jump to window N, prev/next window |
| prefix `r` / `k` | Rename / kill window |
| prefix `C` / `K`, `Alt`+`↑/↓`, prefix `P/N` | New / kill session, prev/next session |
| prefix `q` | Reload tmux config |
| prefix `[`, `v`+`y`, prefix `]` | Enter copy mode, select + copy (vi keys), paste |
| Mouse | **On** — click/drag panes, scroll, resize |

> No Hyprland binding reloads the compositor (verified by grep — there is none).
> Use `hyprctl reload` in a terminal after editing Hyprland config (standard Hyprland 0.53 command; safe: it only re-reads files).
> Bar: `omarchy-restart-waybar`. Launcher: `omarchy-restart-walker`. tmux: prefix `q`.

---

## 4. Daily desktop usage

### 4.1 Log in, look around

1. At the login screen choose the **Ombuntu** session (other sessions like Ubuntu/Niri still exist and are untouched).
2. Top bar (Waybar, 26 px): left = Ombuntu logo (click → master menu) + workspaces 1–5; center = clock, weather, update icon, status indicators; right = tray, Bluetooth, network, sound, CPU (click → btop), battery (click → power options).
3. Press `Super` + `K` anytime to search your real keybindings; `Super` + `Shift` + `K` opens the illustrated cheatsheet.

### 4.2 Open applications

- `Super` (tap), `Super` + `Space`, or `Super` + `/`: type a few letters, `Enter`. Walker learns; prefixes: `.` files, `=` calculator, `:` symbols, `@` web search, `$` clipboard history.
- Or use direct keys from §3.1 (`Super`+`T` terminal, `Super`+`F` files, `Super`+`B` browser, `Super`+`Enter` terminal alias).
- Check *is it installed first*: launcher entries for Spotify, Obsidian, Typora, LazyDocker have no backend on this machine (see §9) — prefer `Super` + `Space` to see what truly exists.

### 4.3 Terminals

- `Super` + `Enter` → Alacritty, starting in the focused window's project directory (cwd-aware). `Super` + `T` does the same (COSMIC primary).
- `Super` + `Alt` + `Enter` → straight into tmux (`attach || new -s Work`).
- Everyday shell helps (verified in `~/.local/share/omarchy/default/bash/`): `ls/lt` (eza), `cd` = zoxide smart-jump (`z <fragment>`), `ff` fuzzy-find files, `n` opens nvim, `t` attaches tmux, `g` = git, `d` = docker alias (docker itself isn't installed — see §9).

### 4.4 Tiled windows

Windows tile automatically (dwindle: splits alternate, `preserve_split`, `force_split=2`). Focus with `Super` + arrows/HJKL; swap with `Super` + `Shift` + arrows/HJKL or `Super` + `X` swap mode; resize with `Super` + `R` mode (legacy `Super` + `-`/`=` still works); new windows take roughly half the space. Gaps are 5 px in / 10 px out with a 2 px cyan-green active border (theme `tokyo-night`).

### 4.5 Floating windows

`Super` + `G` frees a window (drag by holding `Super` + left mouse; resize with `Super` + right-drag). Dialogs/file pickers, calculators, TUI panels (audio/bluetooth/wifi/btop), screenshots tools and the cheatsheet float automatically by rule. `Super` + `S` groups windows into tabs instead.

### 4.6 Workspaces

- Go: `Super` + `1`…`9`, back: `Super` + `0`. Step: `Super` + `Ctrl` + arrows/HJKL. Switch windows: `Super` + `Tab`.
- **Move a window:** `Super` + `Shift` + `N` (you follow it) or `Super` + `Shift` + `Alt` + `N` (it goes silently — ideal for parking builds). Step to neighbor: `Super` + `Shift` + `Ctrl` + arrows.
- **Scratchpad:** `Super` + `Shift` + `Alt` + `S` stashes a window (e.g. a scratch terminal); `Super` + `Shift` + `S` summons/hides it on any workspace.

### 4.7 Find installed applications

`Super` + `Space` and browse; `~/.local/share/applications/` holds your web-app shortcuts (ChatGPT, YouTube, WhatsApp, …). To audit what's installed: `command -v <name>` or the appendix commands.

### 4.8 Multiple terminal sessions (tmux in 60 seconds)

1. `Super` + `Alt` + `Enter` → you're in session `Work`.
2. `Alt` + `Enter` splits panes (server / tests side by side); prefix `c` opens windows (editor, git, logs); `Alt` + `1..9` jumps.
3. Detach with prefix `d` (everything keeps running); reattach with the `t` alias or the same shortcut. Session list: `tmux ls`.

### 4.9 Copy & paste (three layers — don't mix them up)

1. **Apps:** `Super` + `C/V/X`, or the app's own keys.
2. **Terminal:** `Ctrl` + `Shift` + `C/V` (Alacritty defaults; nothing custom here).
3. **tmux:** prefix `[` → move (vi keys) → `v` select → `y` copy → prefix `]` paste. (Mouse selection + `set-clipboard on` also forwards to the system clipboard.)
4. **History:** `Super` + `Ctrl` + `V` (Walker clipboard provider).

### 4.10 Sound, brightness, network, display

- Keys (§3.6) with OSD; `Alt` for 1 % steps. Panels: `Super` + `Ctrl` + `A` (audio), `B` (bluetooth), `W` (wifi); or click the Waybar icons.
- External monitor: plug in — `omarchy-hyprland-monitor-watch` (autostarted, verified) applies settings; lid-close disables the laptop panel when an external display is present. Scale stepping: `Super` + `/`. Base config: `~/.config/hypr/monitors.conf` (preferred resolution, scale 1).

### 4.11 Lock, logout, restart, shutdown

- Lock: `Super` + `Esc` (or `Super` + `Ctrl` + `L`), or `Super` + `Alt` + `Space` → System → Lock. Password entry is hyprlock over a blurred wallpaper.
- Everything else: `Super` + `Alt` + `Space` → System → Suspend / Logout / Restart / Shutdown (these run `systemctl suspend` and `omarchy-system-logout/-reboot/-shutdown` — verified). Quick logout: `Super` + `Shift` + `Esc`.
- Auto: screensaver after 150 s idle, lock after ~152 s (`~/.config/hypr/hypridle.conf`); toggle lock-on-idle with `Super` + `Ctrl` + `I`.

### 4.12 Reload the compositor config

No keybinding exists for this (verified). In a terminal: `hyprctl reload`. If the bar misbehaves: `omarchy-restart-waybar`. If the launcher misbehaves: `omarchy-restart-walker`. If tmux acts up: prefix `q`.

### 4.13 Recover from a broken config / unusable session

Do **not** reinstall anything. In order:

1. If you can open a terminal: `hyprctl reload` to test; `hyprctl clients` / `hyprctl binds` to inspect.
2. If Hyprland won't start or the screen is unusable: `Ctrl` + `Alt` + `F3` (TTY3), log in, fix or restore the file (see §7), then `sudo systemctl restart <display-manager>` or reboot.
3. At the login screen you can always pick the **Ubuntu** session to get a working desktop while you repair Hyprland configs.

### 4.14 Closing things — six different meanings

| Action | What happens |
|---|---|
| `Super` + `Q` (close window) | Kills the window (and its process if it has no other windows) |
| `exit` / `Ctrl`+`D` in shell | Ends that shell; terminal window closes if it was the last process |
| tmux detach (prefix `d`) | Session **keeps running** in background — reattach later |
| tmux kill (prefix `K` / `tmux kill-session`) | Session and all its processes **end** |
| Logout (system menu) | Entire graphical session ends; all apps close |
| Reboot/shutdown | Whole machine restarts/powers off |

---

## 5. Developer workflow

### 5.1 Suggested workspace map (uses your real bindings)

| Workspace | Role | How to fill it |
|---|---|---|
| 1 | Editor + project | `Super`+`Shift`+`N` (nvim) or browser-based IDE; keep one project per workspace |
| 2 | Terminals: build, test, dev server | `Super`+`Alt`+`Enter` → tmux `Work`: window 1 server, window 2 tests, window 3 git |
| 3 | Docs + browser research | `Super`+`Shift`+`Enter`; move links here silently with `Super`+`Shift`+`Alt`+`3` |
| 4 | Database / logs / monitoring | `btop` (`Super`+`Ctrl`+`T`), `lazygit`, log tails in tmux panes |
| 10 or scratchpad | Scratch terminal | Stash with `Super`+`Shift`+`Alt`+`S`, summon with `Super`+`Shift`+`S` |

Move windows between them with `Super` + `Shift` (+`Alt`) + number; hop with `Super` + number, `Super` + `0` to jump back, `Super` + `Tab` to switch windows.

### 5.2 Who does what (compositor vs terminal vs shell vs tmux)

- **Hyprland:** window placement, workspaces, global shortcuts, monitor/scale, idle/lock. Config: `~/.config/hypr/`.
- **Alacritty:** font (MesloLGS NF 13 pt), scrollback (9000 lines), window opacity; its own tab/window keys (`Ctrl`+`Shift`+`T` etc.) — separate from Hyprland's.
- **Bash:** aliases, prompt (starship), fuzzy finding, directory jumping. Config: `~/.bashrc` → Omarchy `default/bash/*`.
- **tmux:** persistent sessions/panes/windows that survive window closes and detaches. Config: `~/.config/tmux/tmux.conf`.

### 5.3 Tooling status on this machine (verified)

Ready: Git, `gh`, Neovim 0.12.6, lazygit, Node 26.11.1 via mise (+npm), Python 3, btop, fzf, ripgrep, fd, eza, zoxide, bat, starship, Chrome/Brave, Signal.
**Not installed:** Docker, lazydocker, .NET SDK, Rider, PostgreSQL client (`psql`), SQL Server tools (`sqlcmd`), VS Code (`code`), Obsidian, Spotify, Typora. Install helpers exist for later (`omarchy-install-docker-dbs`, `omarchy-install-dev-env`, `omarchy-install-vscode` — document here only; nothing was run).
.NET debugging note: Rider isn't installed, so native .NET debugging via Rider is not available today; Neovim + `dotnet` CLI (once installed) covers editing/builds, Rider would cover debugging.

### 5.4 Safe inspection commands (all read-only)

```bash
hyprctl monitors | head -20      # displays
hyprctl workspaces               # workspace overview
hyprctl clients | head -40       # windows and their classes
hyprctl binds | grep -i <word>   # search live bindings, e.g. workspace
omarchy-menu-keybindings         # searchable binding browser
systemctl --user list-units --type=service --state=running  # session services
command -v <tool>                # is it installed?
```

---

## 6. Configuration file map

### 6.1 Hyprland include order (from `~/.config/hypr/hyprland.conf` — verified)

1. `~/.local/share/omarchy/default/hypr/autostart.conf` — session autostart (hypridle, mako, waybar, swaybg, polkit agent, first-run, power-profiles, monitor-watch)
2. `…/bindings/media.conf`, `clipboard.conf`, `tiling-v2.conf`, `utilities.conf` — all global shortcuts
3. `…/envs.conf` — Wayland env vars (GDK/QT/Electron → Wayland, cursor size)
4. `…/looknfeel.conf` — gaps, borders, blur, animations, dwindle tuning
5. `…/input.conf` — keyboard layout `us`, compose:caps, touchpad
6. `…/windows.conf` → `apps.conf` → 20 per-app rule files — floating/opacity/fullscreen rules
7. `~/.config/omarchy/current/theme/hyprland.conf` — theme colors (don't edit; switch themes instead)
8. **Your overrides:** `~/.config/hypr/{monitors,input,bindings,cosmic,looknfeel,autostart}.conf` — edit these (`cosmic.conf` is the COSMIC shortcut overlay, sourced right after `bindings.conf`; it `unbind`s repurposed Omarchy keys first, then binds COSMIC keys — all `unbind`s must stay before all `bindd`s in that file)
9. `~/.local/state/omarchy/toggles/hypr/*.conf` — feature flags (empty today)

Separate daemons: `~/.config/hypr/{hypridle,hyprlock,hyprsunset,xdph}.conf`.

### 6.2 What controls what

| Behavior | File |
|---|---|
| Shortcuts (apps) | `~/.config/hypr/bindings.conf` |
| Shortcuts (COSMIC overlay) | `~/.config/hypr/cosmic.conf` (strict mode; `unbind` Omarchy keys, then `bindd`/`bindr` COSMIC keys + resize/swap submaps) |
| Shortcuts (tiling/menus/media) | `~/.local/share/omarchy/default/hypr/bindings/*.conf` (reference; overridden via `bindings.conf`/`cosmic.conf` + `unbind`) |
| Monitors/scale | `~/.config/hypr/monitors.conf` |
| Keyboard/touchpad | `~/.config/hypr/input.conf` |
| Gaps/borders/animations | `~/.config/hypr/looknfeel.conf` (defaults in `default/hypr/looknfeel.conf`) |
| Autostart extras | `~/.config/hypr/autostart.conf` (yours is empty — defaults carry the load) |
| Bar content | `~/.config/waybar/config.jsonc`; theme CSS via omarchy theme |
| Launcher | `~/.config/walker/config.toml` |
| Terminal | `~/.config/kitty/kitty.conf` (your config) — but default terminal is **Alacritty** (`~/.local/share/omarchy/config/alacritty/alacritty.toml`) |
| Shell | `~/.bashrc` → `~/.local/share/omarchy/default/bash/{envs,shell,aliases,functions,init}` |
| tmux | `~/.config/tmux/tmux.conf` |
| Notifications style | `~/.config/mako/config` → includes omarchy `default/mako/core.ini`; current theme `tokyo-night` colors |
| Lock screen | `~/.config/hypr/hyprlock.conf` (+ theme include) |
| Idle/suspend | `~/.config/hypr/hypridle.conf` |
| Wallpaper | `~/.config/omarchy/current/background` (symlink; managed by theme/background menu) |
| Theme | `~/.config/omarchy/current/` (symlink farm) + `theme.name` |
| Default apps | `~/.config/hyprland-mimeapps.list`, `~/.config/mimeapps.list` (Chrome=web, Nautilus=dirs, nvim=text, mpv=video, imv=images) |
| Fonts | `~/.config/fontconfig/fonts.conf`; picker via `omarchy-menu` → Font |
| Your helpers | `~/.local/bin/` (omarchy/ombuntu dispatcher symlinks + dev tools) |

### 6.3 Safe edit routine (for later — nothing was changed today)

```bash
cp ~/.config/hypr/bindings.conf{,.bak}   # back up first
cp ~/.config/hypr/cosmic.conf{,.bak}     # and the COSMIC overlay
nvim ~/.config/hypr/cosmic.conf          # edit ONLY files under ~/.config
hyprctl reload                           # apply; watch the terminal for errors
```

Validate: Hyprland reports config errors to its log/journal on reload — re-run `hyprctl reload` and check output; keep the `.bak` until the session survives a logout/login.

---

## 7. Troubleshooting and recovery

| Symptom | Cause (on this machine) | Fix |
|---|---|---|
| Shortcut does nothing | App in the binding isn't installed (§9 list) | Run the command manually to see the error; install the app later |
| `Super`+`Shift`+`D/M/O/W`, dictation keys, some webapps | Missing backend (`lazydocker`, `spotify`, `obsidian`, `typora`, `voxtype`) | Expected — not a config bug |
| Waybar frozen/gone | `waybar` crash or toggled off | `omarchy-restart-waybar` (or `Super`+`Shift`+`Space` if merely hidden) |
| Launcher won't open | Walker/Elephant down | `omarchy-restart-walker`; check `systemctl --user status walker elephant` |
| Change broke login/appearance | Your edit under `~/.config/hypr/` | TTY (`Ctrl`+`Alt`+`F3`), restore `*.bak`, `hyprctl reload` or reboot |
| Total graphical failure | Session won't start | Login screen → pick **Ubuntu** session; repair from a working desktop |
| Screen sharing shows "cancelled" | Old share-picker reference | Already fixed in your `xdph.conf` (uses Ubuntu's portal picker) — keep it |
| No sound/brightness OSD | `swayosd-server` down | `systemctl --user restart swayosd-server` |

**TTY primer:** `Ctrl` + `Alt` + `F3` → text login → fix files with `nvim`/`nano` → back with `Ctrl` + `Alt` + `F1` (or F2). Your display manager: LightDM-family flow with uwsm session wrapper.

**Logs without leaking secrets:** prefer `journalctl --user -u <service> --since "10 min ago"` (e.g. `-u waybar`, `-u mako`, `-u hypridle`) and `hyprctl` queries; never paste tokens/keys from `~/.ssh`, `~/.gnupg`, browser profiles, or shell history into bug reports.

---

## 8. Installed components and their roles

| Component | Version | Role | Autostart (verified) |
|---|---|---|---|
| Hyprland | 0.53.3 | Compositor, tiler, shortcut owner | via uwsm session |
| uwsm | installed | Session supervisor (`uwsm-app` wraps GUI launches) | login entry |
| Waybar | 0.15.0 | Top bar | `exec-once … waybar` + `waybar.service` |
| Walker / Elephant | local builds | Launcher + search backend | `walker.desktop` autostart + `elephant.service`, `dsearch.service` |
| mako | installed | Notifications | `exec-once … mako` + `mako.service` |
| swaybg | installed | Wallpaper | `exec-once swaybg -i …/background` |
| hypridle / hyprlock | installed | Idle → screensaver → lock | `exec-once hypridle` |
| SwayOSD | installed | Volume/brightness OSD | `swayosd-server.service` |
| xdg-desktop-portal-hyprland + gtk | installed | Screen share, file pickers | user services |
| Alacritty | installed | Default terminal | on demand |
| Nautilus | installed | File manager | on demand |
| Chrome (+Brave) | installed | Default browser + web-app runtime | on demand |
| Neovim 0.12.6 | /opt | Default editor | on demand |
| tmux 3.6 | system | Terminal multiplexer | on demand (`Work` session) |
| PipeWire/WirePlumber | running | Audio | user services |
| nm-applet, blueman | running | Network/Bluetooth trays | autostart services |
| GNOME keyring, ssh-agent, gvfs, evolution-* | running | Secrets, files, calendar plumbing | user services |

---

## 9. Verified vs inferred vs unknown

**Verified (seen on disk or by command):** everything in §§1–8 marked verified; all shortcut tables come from the actual `bindd` lines; autostart list; theme/background symlinks; session files; missing-program list from `command -v`.

**Inferred (upstream defaults, no local override found):** Alacritty `Ctrl`+`Shift`+`C/V` copy/paste (no custom `key_bindings` in omarchy's `alacritty.toml`); tmux `[`/`]` copy-mode keys (not rebound, stock tmux behavior retained); Walker prefix semantics from `config.toml`; dwindle split behavior from documented Hyprland semantics matching the tuned values.

**Unknown / unresolved:**
1. `omarchy-menu power` (Waybar battery click target) — no `power` submenu was confirmed in `omarchy-menu`; likely resolves to the System menu, unconfirmed.
2. Whether `1password`/`bitwarden` window rules matter here — neither password manager's presence was checked.
3. `voxtype` (dictation) absence — the Waybar mic module and `F9`/`Super`+`Ctrl`+`X` binds are inert; unclear if intentional (Ombuntu may drop it) or pending install.
4. Exact TTY-return key (`F1` vs `F2`) and display-manager name on this box — LightDM is the Ombuntu-documented default but wasn't directly confirmed.
5. Multi-monitor behavior is configured but untested (single panel present).
6. `hyprctl reload` documents as the reload path (standard for Hyprland 0.53) and was executed 2026-10-14 for the COSMIC overlay (`ok`, 254 bind lines).
7. Known COSMIC gaps with no Hyprland equivalent (by design, documented in `cosmic.conf`): `Super`+`U`/`I` tiling-tree select, a true workspace-overview applet (Walker opens instead), context-aware cross-monitor move (kept `movecurrentworkspacetomonitor` on `Shift`+`Super`+`Alt`+arrows), screen reader (`Super`+`Alt`+`S`).

**Broken references (no repair attempted):** `lazydocker`, `obsidian`, `spotify`, `cliamp`, `typora`, `voxtype`, `docker`, `dotnet`, `rider`, `psql`, `sqlcmd`, `code`, `ghostty`, `foot`, `cliphist`, `hyprshot`, `brave` (basename only; `brave-browser` exists), `omarchy-cheatsheet` (use `ombuntu-cheatsheet`), `omarchy-show-keybindings` (use `omarchy-menu-keybindings`).

---

## 10. Diagnostic appendix

```bash
# Session & compositor
loginctl show-session $(loginctl | awk 'NR>1{print $1; exit}') -p Type -p Desktop -p Display
hyprctl version | head -5; hyprctl monitors; hyprctl workspaces; hyprctl clients | head -40
hyprctl binds | grep -i "workspace"     # search any binding family
hyprctl getoption general:layout        # confirm dwindle

# Services & logs (read-only)
systemctl --user list-units --type=service --state=running
journalctl --user -u waybar --since "15 min ago" | tail -30
journalctl --user -u mako -u hypridle -u walker --since "15 min ago" | tail -30

# Config & defaults resolution
omarchy-default-terminal; omarchy-default-browser; omarchy-default-editor
omarchy-menu-keybindings                # interactive binding search
cat ~/.config/omarchy/current/theme.name; ls -l ~/.config/omarchy/current/background
command -v <tool>                       # installed? (e.g. docker, dotnet, psql, lazydocker)

# Back up before editing (for later use)
cp ~/.config/hypr/bindings.conf{,.bak} && cp ~/.config/hypr/cosmic.conf{,.bak} && hyprctl reload
```

*End of guide. Happy tiling — `Super`+`K` is your memory.*
