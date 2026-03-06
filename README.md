# TEC Lab Remote Desktop Setup
**College of DuPage**

Connect to TEC Lab computers remotely using HP Anyware.

## Quick Start

1. **Download** this package: click the green **Code** button above, then **Download ZIP**
2. **Extract** the zip file (right-click > Extract All)
3. **Open** `0-Instructions-With-Pictures.html` for step-by-step setup with screenshots

## What's Included

| File | Description |
|------|-------------|
| `0-Instructions-With-Pictures.html` | Visual setup guide with screenshots (open this first) |
| `1-READ-THIS-FIRST.txt` | Text-only version of the instructions |
| `2-Run-This-To-Connect.bat.txt` | Windows setup script (rename to `.bat` before running) |
| `DO-NOT-RUN-Mac-Instructions.txt` | Manual setup steps for Mac users |

## Before You Start

You **must** set up Microsoft Authenticator on your phone before using HP Anyware.

1. Install **Microsoft Authenticator** on your phone ([iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458) / [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator))
2. Go to [mysignins.microsoft.com/security-info](https://mysignins.microsoft.com/security-info)
3. Sign in with your COD email (yourname@dupage.edu)
4. Add Microsoft Authenticator as a sign-in method
5. Set your default sign-in method to **"App based authentication - notification"**

> **Important:** Do NOT select "code" as your default. HP Anyware only works with push notifications.

## Windows Setup

1. Download and extract this package
2. Rename `2-Run-This-To-Connect.bat.txt` to `2-Run-This-To-Connect.bat`
   - Can't see the `.txt` extension? In File Explorer, click **View** > check **File name extensions**
3. Double-click `2-Run-This-To-Connect.bat`
4. The script will automatically download and install HP Anyware, configure the connection, and launch TEC Lab

After the first run, use the **TEC Lab** shortcut on your desktop to reconnect.

## Mac Setup

1. Download HP Anyware: [pcoip-client_latest.dmg](https://dl.anyware.hp.com/DeAdBCiUYInHcSTy/pcoip-client/raw/names/pcoip-client-dmg/versions/latest/pcoip-client_latest.dmg)
2. Install the app (drag to Applications)
3. Grant Accessibility access when prompted
4. Set Security mode to **Low** (gear icon > Advanced)
5. Add connection: `hpanyware.cod.edu`

See `DO-NOT-RUN-Mac-Instructions.txt` for full details.

## Connecting to TEC Lab

1. Enter your COD username (just `smithj`, not the full email)
2. Enter your COD password
3. Click **Connect**
4. Click **Request Push**
5. Approve the notification on your phone
6. Select an available computer

## Internet Requirements

| Quality | Average | Peak |
|---------|---------|------|
| 30 fps (minimum) | 5 Mbps | 50 Mbps |
| 60 fps (recommended) | 10 Mbps | 100 Mbps |

Test your speed at [speedtest.net](https://speedtest.net)

## Troubleshooting

| Problem | Solution |
|---------|----------|
| "Request Push" doesn't work | Default sign-in must be "notification" not "code". Change it at [mysignins.microsoft.com/security-info](https://mysignins.microsoft.com/security-info) |
| SmartScreen blocks the script | Click "More info" then "Run anyway" |
| Installation fails | Check internet connection. Click "Yes" on admin prompts |
| Can't connect | Check internet. On campus, use COD-WiFi or ethernet |

## Need Help?

**COD Help Desk**
- Email: helpdesk@cod.edu
- Phone: (630) 942-2999
