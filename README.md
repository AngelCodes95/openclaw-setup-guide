# OpenClaw Setup Guide
> High detail walkthrough with newbie-friendly notes

---

## Part 1: VPS SSH Key Setup

> **Note:** This guide assumes you are running OpenClaw on a Ubuntu KVM VPS. Running it directly on your local machine is possible but not recommended -- you are responsible for any side effects on your environment. If you choose to skip the VPS setup, start at **Part 2: OpenClaw Installer** and run the install command on your local machine instead.

### After purchasing your Ubuntu KVM VPS on Hostinger:

1. In your hosting dashboard, go to **Settings > SSH Keys > [+ Add SSH Key]**

### Generate an SSH Key Pair (if you don't have one)

1. Open a terminal and run:
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```
   Replace the email with your actual address.

2. Press **Enter** to accept the default file path (recommended). The key will be saved to your `.ssh` folder automatically.

3. Set a passphrase when prompted (recommended) and confirm it.

### Add Your Public Key to Hostinger

1. Navigate to the `.ssh` directory in your user folder.
2. Run the following to print your public key (adjust path for your OS -- macOS uses `/Users/your_username/`, Linux/WSL uses `/home/your_username/`):
   ```bash
   cat ~/.ssh/your_key_filename.pub
   ```
   You should see output like:
   ```
   ssh-ed25519 AAAA...longkey... your_email@example.com
   ```
3. Copy that entire line and paste it into the SSH Key field in your Hostinger browser window.

### Connect to Your VPS

```bash
ssh root@your_vps_ip_address
```

Enter the passphrase you set for the SSH key.

**You are in when you see:**
```
root@sr########:~
```
That unique identifier confirms you are connected.

---

## Part 2: OpenClaw Installer

From within your SSH session, run:

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

You should see something like:

```
I'll butter your workflow like a lobster roll: messy, delicious, effective.

✓ Detected: linux

Install plan
OS: linux
Install method: npm
Requested version: latest

[1/3] Preparing environment
· Node.js not found, installing it now
· Installing Node.js via NodeSource
· Installing Linux build tools (make/g++/cmake/python3)
```

Dots become checkmarks as each step completes. This takes a few minutes -- good time for a coffee break.

### Setup Wizard Choices

| Prompt | Selection |
|--------|-----------|
| Personal vs shared use | Continue (yes) |
| Setup type | QuickStart |
| Model / Auth provider | Your choice -- **OpenRouter** is a good default |
| How to provide API key | Paste key |
| API key | Paste your OpenRouter key from [openrouter.ai](https://openrouter.ai) |
| Keep current model | Keep current (openrouter/auto) |

---

## Part 3: Discord Bot Setup

> **STOP:** You must have your own Discord server before continuing. Create one now, then proceed.

### Create the Bot in Discord Developer Portal

1. Go to [Discord Developer Portal](https://discord.com/developers/applications) and log in.
2. In the sidebar, click **Applications > [New Application]**.
3. Name your application and click **Create**.

### Get Your Bot Token

1. In the left sidebar, click **Bot**.
2. Scroll down and click **[Reset Token]**, accept any prompts.
3. Copy the token.
4. Back in your SSH terminal, paste the token when prompted:
   ```
   Enter Discord Bot Token:
   ```

### How to Get Your Channel ID

You need Discord Developer Mode enabled to copy channel IDs.

1. In Discord, go to **User Settings > Advanced > Developer Mode** and toggle it **on**.
2. Right-click the channel you want the bot to use and select **Copy Channel ID**.

The value you paste will look like a long number (e.g., `1234567890123456789`). The wizard may ask for it in `SERVER_ID/CHANNEL_ID` format -- if so, right-click your server icon and **Copy Server ID** for the first part.

### Configure Channel Access in the Wizard

| Prompt | Selection |
|--------|-----------|
| Configure Discord Channel Access? | Yes |
| Discord Channel Access | Allowlist |
| Paste channel ID | `YOUR_SERVER_ID/YOUR_CHANNEL_ID` -- see note above about enabling Developer Mode |
| Configure Skills now? | No |
| Enable Hooks? | Skip for now (Space + Enter on Mac) |
| How to hatch bot? | Hatch in TUI (recommended) |

---

## Part 4: OAuth2 -- Invite Bot to Your Server

1. In the Discord Developer Portal, go to **OAuth2** in the sidebar.
2. Scroll to the **URL Generator** at the bottom.
3. Check only the **bot** box. Leave everything else unchecked.
4. Scroll to the bottom, copy the **Generated URL**.
5. Open a new browser tab and paste the URL.
6. Select your own Discord server from the invite screen.

### Enable Message Content Intent

1. Back in the Developer Portal, go to **Bot** in the sidebar.
2. About halfway down, find **Message Content Intent** and toggle it **on**.
3. Click **Save Changes**.

---

## Part 5: Get the Bot Online and Paired

### Verify Bot Application ID

1. Go to **General Information** in the Developer Portal.
2. Copy your **Application ID**.
3. Navigate to:
   ```
   https://discord.com/users/YOUR_APPLICATION_ID
   ```
4. Click the message icon on your bot and send it a DM.

### If the Bot Responds "access not configured"

This is expected. Approve yourself via pairing:

1. The bot's response will include a pairing command like:
   ```
   openclaw pairing approve discord [your_identifier]
   ```
2. Copy that line and paste it into the terminal where you are SSH'd into the VPS.
3. Send the bot another DM -- it should now respond.

**You have the claw!**

---

## Part 6: Troubleshooting -- Bot Offline

If your bot shows as offline on Discord, try reconfiguring:

```bash
openclaw config
```

Walk through the wizard:
- Select **Channels** under sections to configure
- Select **Discord (Bot API)**
- Choose **Modify settings**
- Re-enter your bot token if needed
- Set channel access to **Allowlist** (not Open)
- Set DM policy to **Pairing (recommended)**
- Scroll to **Continue** at the bottom

Once complete, confirm **Discord check, bot is online** in the output.

---

## Extra Credit: Join a Community Server

> **Warning:** You are letting your bot interact with other bots and other users. There is always risk on a shared server. You are on a VPS, so the blast radius is contained -- but proceed with awareness.

If you want to add your bot to a community or shared server, you will need help from someone who has admin access to that server.

1. Go back to **Discord Developer Portal > OAuth2**.
2. Scroll down, select only **bot** again.
3. Copy the **Generated URL** at the bottom.
4. Send that link to a server admin and ask them to authorize your bot.

### The admin will need to:

1. Follow the invite link and select their server to add your bot.
2. If the server does not already have a role designated for bots, create one in **Server Settings > Roles**.
3. Go to **Server Settings > Integrations > [Your Bot Name]**.
4. Under **Roles**, assign the bot role to your bot.

Once assigned, your bot will have the appropriate permissions scoped to that role.
