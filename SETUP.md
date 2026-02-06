SETUP.md - Quick Start Instructions for Windows Users
======================================================

This file contains step-by-step instructions for getting the OPD Slack Alert Bot running on Windows.

PREREQUISITES
=============
- Windows 10 or Windows 11
- Python 3.7 or higher installed
  (Download from https://www.python.org/downloads/)
  [IMPORTANT: Check "Add Python to PATH" during installation]
- A Slack workspace (free tier is fine)

STEP 1: Get Your Slack Webhook URL (5 minutes)
==============================================

1. Go to https://api.slack.com/apps
2. Click "Create New App"
3. Select "From scratch"
4. App name: "OPD Alert Bot" (or your preference)
5. Choose your workspace
6. Click "Create App"
7. In left sidebar, click "Incoming Webhooks"
8. Toggle "Activate Incoming Webhooks" ON
9. Click "Add New Webhook to Workspace"
10. Select the channel (e.g., #opd-alerts, or create a new channel)
11. Click "Allow"
12. Copy the Webhook URL from the table (looks like):
    https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX

STEP 2: Set Environment Variable (Windows)
===========================================

Right-click on "This PC" or "My Computer" on your desktop:

1. Click "Properties"
2. Click "Advanced system settings" (on left side)
3. Click "Environment Variables..." button (bottom right)
4. Under "User variables" section, click "New..."
5. Variable name:  SLACK_WEBHOOK_URL
6. Variable value: [Paste your webhook URL from Step 1]
7. Click "OK" on all dialogs
8. Restart your computer (or log out and log back in)

VERIFY: Open Command Prompt and type:
  echo %SLACK_WEBHOOK_URL%
  
You should see your URL printed.

STEP 3: Create Project Folder
=============================

Open Command Prompt (Win + R, type "cmd", press Enter):

  mkdir C:\OpdBot
  cd C:\OpdBot

STEP 4: Download Bot Files
==========================

Place these files in C:\OpdBot:
- opd_slack_bot.py
- run_bot.bat
- README.md
- SETUP.md (this file)

STEP 5: Create Python Virtual Environment
==========================================

In Command Prompt (still in C:\OpdBot):

  python -m venv venv

This creates a "venv" folder with an isolated Python environment.

STEP 6: Activate Virtual Environment and Install Dependencies
==============================================================

Still in Command Prompt:

  venv\Scripts\activate

You should see "(venv)" at the start of your command prompt line.

Then install required packages:

  pip install requests xmltodict python-dateutil

Wait for installation to complete. You should see:
  Successfully installed requests-X.X.X xmltodict-X.X.X python-dateutil-X.X.X

STEP 7: Test the Bot
====================

Type:
  python opd_slack_bot.py

You should see output like:
  ======================================================================
  Orlando Police Department Active Calls Slack Alert Bot
  ======================================================================
  Feed URL: https://www1.cityoforlando.net/opd/activecalls/activecadpolice.xml
  Poll interval: 60 seconds
  Slack webhook configured: True
  Watch zone keywords: MILLS, ORANGE, ORLANDO AVE, 17/92, US 17, LEE RD, VIRGINIA DR, HORATIO
  ======================================================================
  2025-01-01 14:30:45,123 [INFO] Polling feed at 2025-01-01 14:30:45

Press Ctrl+C to stop testing.

STEP 8: Use the Batch Launcher (Recommended for Regular Use)
============================================================

Instead of manually activating the venv each time, just double-click:
  run_bot.bat

This will:
- Verify venv exists
- Activate it automatically
- Start the bot
- Show warnings if anything is missing

The bot will continue polling every 60 seconds until you:
- Press Ctrl+C
- Close the Command Prompt window
- Restart your computer

WHAT TO EXPECT
==============

The bot shows console output like:

  2025-01-01 14:30:47,456 [INFO] Found 47 incidents in feed
  2025-01-01 14:30:47,789 [DEBUG] Already seen incident 202501011234566
  2025-01-01 14:30:48,123 [DEBUG] Incident 202501011234567 location 'SEMORAN BLVD' does not match corridor
  2025-01-01 14:30:49,456 [INFO] New matching incident found: 202501011234568
  2025-01-01 14:30:50,123 [INFO] Posted to Slack: 202501011234568

When a match is found, a formatted Slack message appears in your chosen channel:

  🚨 New OPD Active Call
  
  Incident: 202501011234568
  Type: Accident - Property Damage
  Time: 2025-01-01 14:28:30
  Location: MILLS AVE & ORANGE AVE
  District: District 2
  
  [View on Maps] button

CUSTOMIZING WATCH ZONE
======================

The default watch zone keywords are:
- MILLS      (Mills Avenue)
- ORANGE     (Orange Avenue / Boulevard)
- ORLANDO AVE (Orlando Avenue)
- 17/92      (US-17/92)
- US 17      (US-17)
- LEE RD     (Lee Road)
- VIRGINIA DR (Virginia Drive)
- HORATIO    (Horatio Street)

To change these:

1. Open opd_slack_bot.py in a text editor (Notepad, VS Code, etc.)
2. Find the section:
   CORRIDOR_KEYWORDS = [
       "MILLS",
       "ORANGE",
       ...
   ]
3. Modify as needed. For example, to watch Dean Rd instead of Mills:
   CORRIDOR_KEYWORDS = [
       "DEAN RD",
       "ORANGE",
       ...
   ]
4. Save the file
5. The next time you run the bot, it will use the new keywords

CHANGING POLL INTERVAL
=====================

Default is 60 seconds. To change it, before running the bot:

  set POLL_INTERVAL=30

This makes it poll every 30 seconds. Then run:

  python opd_slack_bot.py

Or edit the batch file run_bot.bat to include this at the top.

RUNNING ON STARTUP (Windows Task Scheduler)
============================================

To run the bot automatically when Windows starts:

1. Press Win + R, type "taskschd.msc", press Enter
2. Click "Create Basic Task..." on the right
3. Name: "OPD Alert Bot"
4. Description: "Monitor Orlando Police active calls"
5. Click Next
6. Trigger: Choose "At startup"
7. Click Next
8. Action: Choose "Start a program"
9. Program/script: C:\OpdBot\run_bot.bat
10. Start in (optional): C:\OpdBot
11. Click Next
12. Check "Run with highest privileges"
13. Click Finish

Now the bot will start automatically each time Windows boots.

TROUBLESHOOTING
===============

Problem: "python is not recognized"
Solution: Reinstall Python and check "Add Python to PATH"
          Or use full path: C:\Python311\python opd_slack_bot.py

Problem: "ModuleNotFoundError: No module named 'requests'"
Solution: Make sure venv is activated and dependencies installed:
          venv\Scripts\activate
          pip install requests xmltodict python-dateutil

Problem: "SLACK_WEBHOOK_URL" environment variable not found
Solution: 
  - Did you restart after setting the env var? (required on Windows)
  - Try setting it temporarily: set SLACK_WEBHOOK_URL=https://...
  - Or run_bot.bat will warn you about this

Problem: No Slack messages appearing but bot is running
Solution:
  - Check your Slack webhook URL is correct
  - Verify incidents are matching your corridor keywords
  - Look at console output for "[INFO] New matching incident found"
  - The bot only alerts on NEW incidents (new to the current session)
  - If you haven't seen an incident in the feed that matches your keywords,
    it won't alert. Check if the incident exists in the feed first.

Problem: Bot exits immediately
Solution:
  - Run from Command Prompt directly to see error messages
  - Check that all files are in the same directory
  - Verify Python version: python --version (should be 3.7+)

RESETTING ALERTS
================

If you want to get alerted on incidents the bot has already seen,
delete the seen_incidents.json file:

  del seen_incidents.json

Next time the bot runs, it will alert on all matching incidents again.

NEED HELP?
==========

Check the README.md file for more detailed information.

If the bot's behavior seems wrong, look at the console output for errors.
Most common issues are:
1. Slack webhook URL not set or incorrect
2. Corridor keywords don't match incident locations
3. Looking at old incidents (delete seen_incidents.json to reset)

---
Created: January 2025
Compatible: Windows 10+, Python 3.7+
