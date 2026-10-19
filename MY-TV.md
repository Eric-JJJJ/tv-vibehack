# My TV: Xiaomi MiTV-MOOR2

Paste the prompt below into Claude Code on a computer connected to the same Wi-Fi as the TV, from a
clone of this repo checked out on the `claude/busy-wright-j2lodv` branch.

Before you start, on the TV: Settings → Device Preferences → About → press OK on "Build" 7 times →
back → Developer options → turn on USB debugging. Keep the TV on, and write down its IP address
(Settings → Network & Internet → your Wi-Fi).

---

I want a full makeover of my Xiaomi MiTV-MOOR2 (Google TV / Android 11): remove the ads and bloat,
and install the launcher from this repo as the default home screen. Read README.md, PROMPT.md and
examples/xiaomi-mitv-moor2/debloat.txt first, and reuse tools/ and launcher/ instead of writing
new ones. The TV's IP is <TV IP HERE>. Work on the real TV step by step, check each step with a
screenshot pulled from the TV, and ask me before doing anything not covered below.

What I already decided:
- Dock, in this order: "My Application" (an app on the TV with that label), Netflix, YouTube, Prime Video.
  launcher/app/src/main/res/values/config.xml already lists the last three. Find the package name of
  "My Application" on the TV (`pm list packages -3`, then check labels with `dumpsys package`), and put it
  in place of MY_APPLICATION_PACKAGE. If it is missing or has no leanback launcher entry, tell me
  before going further.
- Keep Google Assistant and voice search enabled.
- Keep the look as it is: black background, no theme, no wallpaper.
- Live TV is com.mitv.livetv. It is already set in config.xml; leave it enabled, because the
  HDMI input switcher is part of it.
- Ask me about Chromecast, Xiaomi firmware updates and the preloaded apps in the list (ZEE5, Mi Music,
  Play Games) before disabling them.

Steps:
1. Set up tools/venv, connect with `tools/tv.py --probe`, and tell me when to accept the
   "Allow USB debugging?" prompt on the TV. Confirm the model really is MiTV-MOOR2.
2. Back up first: the package list, the disabled packages, getprop and all settings, into a folder
   that git ignores.
3. Check what is installed against examples/xiaomi-mitv-moor2/debloat.txt. Ask me about any
   bloat-looking package that isn't in the list. Then apply the list except the setupwraith line at
   the end, in small batches, with a screenshot after each batch.
4. Build the launcher and install it:
   `INPUT_KEY_SERVICE=1 STOCK_LAUNCHER=com.google.android.apps.tv.launcherx launcher/build-and-install.sh`
   On this TV the setup wizard (com.google.android.tungsten.setupwraith) can take over Home once
   launcherx is disabled. If the script says so, disable setupwraith (the last line of the list)
   and run it again. Fix the JDK 17 and Android SDK paths for my OS if the script can't find them.
5. Check the result with key presses and screenshots: Home opens the new launcher, the dock order is
   right, Up/Down move one row, the Inputs pill lists the HDMI ports, the remote's Input button works,
   and the voice button still opens Assistant.
6. When it is done, give me the exact restore command for this TV and remind me to turn off
   USB debugging.

---

To undo everything:

```bash
TV_HOST=<tv ip> tools/restore.sh examples/xiaomi-mitv-moor2/debloat.txt com.google.android.apps.tv.launcherx
```
