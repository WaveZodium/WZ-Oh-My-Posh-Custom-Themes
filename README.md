# Oh My Posh – Setup on a New Windows Computer

This guide sets up the custom **dark-purple.omp.json** theme in PowerShell and Windows Terminal. Keep the theme file alongside this guide when moving to another computer.

## Contents

- [Install the software](#1-install-the-software)
- [Install and select a font](#2-install-and-select-a-font)
- [Put the theme file in a fixed location](#3-put-the-theme-file-in-a-fixed-location)
- [Try the theme immediately](#4-try-the-theme-immediately)
- [Activate the theme at every startup](#5-activate-the-theme-at-every-startup)
- [What the theme displays](#6-what-the-theme-displays)
- [Troubleshooting](#7-troubleshooting)
- [Move to another computer](#8-move-to-another-computer)
- [Documentation](#documentation)

## 1. Install the software

Open PowerShell. Install Oh My Posh:

```powershell
winget install --id JanDeDobbeleer.OhMyPosh -e --source winget
```

If Windows Terminal is not installed:

```powershell
winget install --id Microsoft.WindowsTerminal -e --source winget
```

This guide works with Windows PowerShell and PowerShell 7. If you want to use PowerShell 7 and it is not installed:

```powershell
winget install --id Microsoft.PowerShell -e --source winget
```

Close the terminal completely after installation, then reopen it. Choose the PowerShell version you want to use from the terminal's tab menu. Under **Settings → Startup → Default profile**, you can set it as the default.

Check the installation:

```powershell
oh-my-posh --version
$PSVersionTable.PSVersion
```

## 2. Install and select a font

The theme includes icons that require a Nerd Font:

```powershell
oh-my-posh font install meslo
```

Open **Windows Terminal → Settings → Defaults → Appearance**. Select the installed Meslo font, for example **MesloLGM Nerd Font** or **Meslo LGM NF**. The name may vary depending on the font version. Save your changes. Restart the terminal if the font does not appear.

If an individual terminal profile has its own font setting, change it there as well. An installed font must also be selected before it will be used.

## 3. Put the theme file in a fixed location

Create a folder under your user profile:

```powershell
New-Item -ItemType Directory -Path "$HOME\.config\oh-my-posh" -Force
explorer.exe "$HOME\.config\oh-my-posh"
```

Copy **dark-purple.omp.json** into the folder that opens. The file should be located here:

```text
C:\Users\YOUR-USERNAME\.config\oh-my-posh\dark-purple.omp.json
```

Check that it exists:

```powershell
Test-Path "$HOME\.config\oh-my-posh\dark-purple.omp.json"
```

The result should be `True`. Make sure the file has not accidentally been named `.json.txt`.

## 4. Try the theme immediately

Run the following in PowerShell:

```powershell
oh-my-posh init pwsh --config "$HOME\.config\oh-my-posh\dark-purple.omp.json" | Invoke-Expression
```

This activates the theme in the current session. The next step makes the setting permanent.

## 5. Activate the theme at every startup

Run these commands in the PowerShell version you want to use:

```powershell
if (!(Test-Path -LiteralPath $PROFILE)) {
    New-Item -ItemType File -Path $PROFILE -Force | Out-Null
}
notepad $PROFILE
```

If the profile already contains a line that starts Oh My Posh, replace that line. Otherwise, add the following line. Keep any other settings in the file.

```powershell
oh-my-posh init pwsh --config "$HOME\.config\oh-my-posh\dark-purple.omp.json" | Invoke-Expression
```

Save the file and open a new PowerShell tab. Alternatively, reload the profile in the current tab:

```powershell
. $PROFILE
```

**Windows PowerShell 5.1 and PowerShell 7 use separate profiles.** Repeat this step for both if you use both. `pwsh` in the Oh My Posh command is its integration name for PowerShell; the line also works in Windows PowerShell.

To see which profile is being edited, run:

```powershell
$PROFILE
```

## 6. What the theme displays

| Section | Contents |
| --- | --- |
| Left | Folder name in purple segments. |
| Administrator | Red ADMIN indicator when the session is running with elevated privileges. |
| Git | Branch, an asterisk for changes, and arrows when the branch is ahead of or behind upstream according to local Git information. |
| Execution time | Duration of commands that exceed the 500 ms threshold. |
| Right | .NET version when the segment's conditions are met, battery percentage when available, and the time. |
| Next line | Purple input prompt `❯`. |

Git information requires Git and a Git repository. .NET information requires an available .NET installation and is displayed according to the segment's activation conditions. As a result, not every section will appear in every folder or on every computer.

The theme file controls the prompt. The terminal's overall background, text size, and transparency are configured separately in Windows Terminal.

## 7. Troubleshooting

### Oh My Posh is not recognized

Close all terminal windows and reopen them so the updated path is loaded. Check:

```powershell
Get-Command oh-my-posh
```

### The default theme appears instead

Check that the profile uses `--config` with the correct path and that no later line starts Oh My Posh again with a different configuration:

```powershell
Get-Content $PROFILE
Test-Path "$HOME\.config\oh-my-posh\dark-purple.omp.json"
```

### Boxes or question marks appear instead of icons

Select Meslo Nerd Font in the terminal profile you are actually using. If the problem occurs in VS Code, select the font there as well; the Windows Terminal setting does not affect the VS Code terminal.

### The profile is blocked by the execution policy

First, review the settings:

```powershell
Get-ExecutionPolicy -List
```

On a computer where you are allowed to manage the execution policy, you can allow local scripts for your user account:

```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

If your work computer's `MachinePolicy` or `UserPolicy` controls this, your IT department will need to manage the setting. Then open a new PowerShell tab.

### JSON error or schema warning in the editor

Check that the file can be read as JSON:

```powershell
Get-Content "$HOME\.config\oh-my-posh\dark-purple.omp.json" -Raw | ConvertFrom-Json | Out-Null
```

The included theme file has `$schema` set to `https://githubusercontent.com`, which is not a schema path. For correct validation and editor completion, you can change that line to:

```json
"$schema": "https://raw.githubusercontent.com/JanDeDobbeleer/oh-my-posh/main/themes/schema.json",
```

This is editor metadata and does not change the theme's colors or layout. The original file has not been modified as part of this guide.

## 8. Move to another computer

Bring **dark-purple.omp.json** and this guide with you. Install Oh My Posh and the font, copy the theme to the same location under `$HOME`, and add the initialization line to the new computer's PowerShell profile. `$HOME` adapts the path to the current user account.

## Documentation

- [Installation on Windows](https://ohmyposh.dev/docs/installation/windows)
- [Fonts](https://ohmyposh.dev/docs/installation/fonts)
- [PowerShell and the prompt](https://ohmyposh.dev/docs/installation/prompt)
- [Custom theme and --config](https://ohmyposh.dev/docs/installation/customize)

Guide prepared on 2026-10-22 based on the accompanying theme file. The commands were checked against the documentation but have not been run on the target computer.
