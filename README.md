<div align="center">

# 🎮 Epic Games Launcher Auto-Installer

**One double-click. Silent install. Epic Games Launcher ready to go.**

A lightweight Windows utility that downloads, silently installs, and launches the Epic Games Launcher — built for first-boot setups, freshly imaged machines, or any PC that needs it fast.

![Platform](https://img.shields.io/badge/platform-Windows%2010%2F11-0078D6?logo=windows&logoColor=white)
![Shell](https://img.shields.io/badge/PowerShell-5.1%2B-5391FE?logo=powershell&logoColor=white)
![License](https://img.shields.io/badge/license-Free%20%26%20Open%20Source-green)

</div>

---

## ✨ Overview

No install wizard. No clicking through prompts. No manually hunting for the current download link.

Just double-click one file, approve the admin prompt, and the Epic Games Launcher installs itself in the background — opening automatically the moment it's done.

## 📁 Project Structure

```
epic-games-auto-installer/
├── Get-EpicGamesLauncher.bat        # Double-click entry point — handles elevation
├── Install-EpicGamesLauncher.ps1    # Core logic — download, install, launch
└── README.md
```

## ⚙️ How It Works

```
 ┌─────────────────────────┐  elevates (UAC)  ┌───────────────────────────────┐
 │ Get-EpicGamesLauncher.bat│ ───────────────► │ Install-EpicGamesLauncher.ps1 │
 └─────────────────────────┘                   └───────────────┬───────────────┘
                                                                 │
                                   ┌─────────────────────────────┼─────────────────────────────┐
                                   ▼                             ▼                             ▼
                          Download current MSI            Silent install               Launch the Launcher
                          via Epic's redirect link
```

1. **`Get-EpicGamesLauncher.bat`** checks for admin rights and self-elevates via UAC if needed.
2. It hands off to **`Install-EpicGamesLauncher.ps1`**, which:
   - Downloads the installer from Epic's own "always latest" redirect link (no hardcoded/stale version numbers)
   - Installs it silently via `msiexec /qn`
   - Deletes the installer once finished
   - Launches the Epic Games Launcher automatically

## 🚀 Usage

1. Clone or download this repo.
2. Keep `Get-EpicGamesLauncher.bat` and `Install-EpicGamesLauncher.ps1` **in the same folder**.
3. Double-click **`Get-EpicGamesLauncher.bat`**.
4. Approve the UAC prompt.
5. Done — the Launcher installs and opens on its own. Sign in to your Epic account from there.

<details>
<summary>Prefer the command line?</summary>

```powershell
powershell.exe -ExecutionPolicy Bypass -File .\Install-EpicGamesLauncher.ps1
```

</details>

## ✅ Requirements

- Windows 10 or later
- Administrator privileges (machine-wide install)
- Active internet connection

## 🛠 Notes

- The Epic Games Launcher is installed system-wide, available to all users on the machine.
- The download link always resolves to the current installer, so this script doesn't go stale as new versions release.
- If the script can't locate `EpicGamesLauncher.exe` to auto-launch it, it'll print a warning — but the install itself will still have completed successfully.
- This installs the Launcher only — you'll still need to sign in (or create an account) to access your games.

## 📄 License

Free and open source — use, modify, and share it however you'd like.

## ☕ Support

If this saved you some time, consider buying me a coffee:

[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-FFDD00?logo=buymeacoffee&logoColor=black)](https://buymeacoffee.com/impxcts)

---

<div align="center">
<sub>Built for the "plug it in and it just works" first-boot workflow.</sub>
</div>
