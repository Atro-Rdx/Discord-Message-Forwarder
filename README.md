  <h1 align="center">🚀 Discord Message Forwarder</h1>

<p align="center">
  <strong>Ultra‑fast, feature‑packed message forwarding with intelligent rate limiting, CDN uploads, watermarking, role mapping, and a live dashboard.</strong>
</p>

<p align="center">
  <a href="https://python.org"><img src="https://img.shields.io/badge/python-3.8+-blue.svg" alt="Python 3.8+"></a>
  <a href="https://github.com/yourusername/discord-message-forwarder/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-green.svg" alt="MIT License"></a>
<img src="https://img.shields.io/badge/Discord-User_Token-5865F2?logo=discord&logoColor=white" alt="Discord">
  <a href="https://github.com/yourusername/discord-message-forwarder/stargazers"><img src="https://img.shields.io/github/stars/Atro-Rdx/DDoS?style=social" alt="GitHub stars"></a>
</p>

---

<p align="center">
  <img src="https://cloud.medshop.clinic/f/xDuP/Screenshot%202026-02-13%203.21.01%20AM.png" alt="Discord Forwarder Dashboard" width="800" style="border-radius:50px;" onerror="this.style.borderRadius='50px'">
</p>

---

## 📋 Table of Contents
- [✨ Overview](#-overview)
- [🖥️ Dashboard Control Panel](#️-dashboard-control-panel)
- [🔥 Core Features](#-core-features)
  - [📨 Message Forwarding Engine](#-message-forwarding-engine)
  - [🛡️ Advanced Rate Limiting System](#️-advanced-rate-limiting-system)
  - [🖼️ CDN & Image Processing Suite](#️-cdn--image-processing-suite)
  - [🔐 Content Security & Filtering](#-content-security--filtering)
  - [👥 Role Mapping & Cross‑Server Sync](#-role-mapping--cross-server-sync)
  - [🌐 Public Channel Forwarding](#-public-channel-forwarding)
  - [📊 Real‑Time Statistics & Monitoring](#-real-time-statistics--monitoring)
- [⚙️ Complete Configuration Reference](#️-complete-configuration-reference)
- [🚀 Quick Start](#-quick-start)
- [📈 Performance & Optimisation](#-performance--optimisation)
- [🏷️ Tags](#️-tags)
- [📄 License](#-license)

---

## ✨ Overview

**Discord Message Forwarder** is a high‑performance Python application that bridges Discord channels and servers. It monitors source channels and instantly forwards messages – including text, images, files, and embeds – to target channels. Unlike official bots, this tool works with **user tokens** and offers advanced features rarely found elsewhere:

- Parallel multi‑channel processing with intelligent staggering.
- Built‑in CDN support with automatic watermarking, resizing, and format conversion.
- Military‑grade rate limiting with per‑route quotas and exponential backoff.
- Live dashboard that shows uptime, message counts, queue sizes, and token status.
- Hot‑reload configuration – change settings without restarting.
- Role ID mapping across different servers (by ID or name).
- Prohibited word filtering with case‑sensitive/insensitive options.
- Public Discord channel access without a bot account.
- And dozens of minor but powerful details (see below).

---

## 🖥️ Dashboard Control Panel

The screenshot above shows the built‑in dashboard (provided by your companion web interface). Every element is designed for at‑a‑glance operational awareness.

| Section              | Elements                                                                 | Description                                                                 |
|----------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| **Navigation**       | Dashboard, Token Config, Rules, Settings, Messages, Logs, Word Filter, Public Servers | Switch between configuration and monitoring views.                         |
| **Control Panel**    | RUNNING indicator, Uptime counter, Start/Stop buttons, Add Rule button   | One‑click control of the forwarder engine.                                 |
| **Active Rules**     | Messages Forwarded counter, Active Rules count, Uptime                   | High‑level performance metrics.                                             |
| **OKLINE**           | Config Status, Auto‑Sync                                                | Shows if config is synced with file and last sync time.                    |
| **Token Status**     | Valid/Invalid badge, optional message                                   | Instantly tells if your Discord token is accepted.                         |

> 💡 *The dashboard communicates with the forwarder via a lightweight HTTP API – all real‑time data is pushed from the running Python thread.*

---

## 🔥 Core Features

### 📨 Message Forwarding Engine
- **Parallel channel scanning** – Up to 5 channels simultaneously (configurable).
- **Message queuing** – Maintains strict chronological order even when multiple messages arrive at once.
- **Old message catch‑up** – Specify how many previous messages to forward when a new rule is created (`forward_old_count`).
- **Per‑rule toggles** – Enable/disable rules without deleting them.
- **Bot filtering** – Option to ignore messages from bots (`ignore_bots`).
- **Attachment forwarding** – Images, videos, and other files; non‑image files are sent as plain links.
- **Embed processing** – Extracts thumbnails and images from embeds, uploads them to CDN, and appends them to the forwarded message.
- **Discord link removal** – Strips all `discord.com`, `discord.gg`, `cdn.discordapp.com` links and channel mentions (`<#123>`).
- **Mention preservation** – User mentions (`@username`) and role mentions (`@rolename`) are kept as clickable mentions after role mapping.

### 🛡️ Advanced Rate Limiting System
- **Global rate limit** – 50 requests per second (Discord’s limit).
- **Per‑route quotas**  
  - `channels` – 10 requests / 10 seconds  
  - `guilds` – 10 requests / 10 seconds  
  - `messages` – 5 messages / 5 seconds  
- **Exponential backoff** – After a `429` response, wait time doubles: 1s → 2s → 4s → 8s … up to 60s.
- **Dynamic channel check interval** – If a channel has recent activity, check every 2 seconds; otherwise gradually increase to 10 seconds.
- **Rate limit tracking** – Count of `429` responses shown in stats.
- **Request timestamps** – Rolling window per route ensures you never burst over limits.
- **Backoff reset** – After a successful request, backoff returns to 1s.

### 🖼️ CDN & Image Processing Suite
- **Automatic upload** – Images are downloaded, optionally watermarked, and uploaded to your own CDN (configurable endpoint).
- **Watermark presets** – Choose from `small`, `medium`, `large`, `subtle`, `bold`, `transparent`, `diagonal`.
- **Custom watermark settings**  
  - Text, font size, font style (regular, bold, italic, bold_italic)  
  - Position (top‑left, top‑right, bottom‑left, bottom‑right, center, top‑center, bottom‑center)  
  - Opacity (0–1), rotation (0–360), scale factor  
  - Font color, background color, background opacity, padding, margin  
- **Image optimisation** – Resize if exceeding `max_width` / `max_height`, adjust quality, convert to WEBP/JPEG/PNG.
- **Force CDN mode** – If `force_cdn: true`, only images successfully uploaded are forwarded; otherwise the original Discord URL is omitted.
- **Parallel uploads** – Up to 3 images simultaneously (configurable).
- **Download timeout & upload timeout** – Configurable to avoid hanging.
- **Font caching** – LRU cache for watermark fonts, cleared when CDN config changes.

### 🔐 Content Security & Filtering
- **Prohibited words list** – Manage via `prohibited_words` array.
- **Per‑rule activation** – `filter_prohibited_words` enables/disables filtering for each rule.
- **Case sensitivity** – Global toggle `prohibited_words_case_sensitive`.
- **Block or redact** – `block_prohibited_messages: true` completely discards the message; `false` replaces words with `[REDACTED]`.
- **Word boundary detection** – Uses regex `\b` to avoid partial matches (e.g., “ass” does not block “assembly”).
- **Statistics** – Counts messages checked, filtered, and blocked.
- **Test utility** – Built‑in method `test_prohibited_words(text)` shows original vs filtered.

### 👥 Role Mapping & Cross‑Server Sync
- **Manual ID mapping** – Define `role_mappings` in config: `"source_role_id": "target_role_id"`.
- **Automatic name‑based mapping** – `auto_map_roles(source_guild, target_guild)` finds roles with identical names and creates mappings.
- **Fallback behaviour** – If no mapping exists, role mention becomes `**@RoleName**` plain text.
- **Role cache** – Guild roles are cached for 5 minutes to reduce API calls.
- **Guild ID resolution** – Automatically resolves guild ID from channel ID when needed.

### 🌐 Public Channel Forwarding
- **No bot required** – Access public Discord channels using:
  - **Guest token** – obtained from Discord’s public API (valid for a few hours).
  - **User token** – fallback to main token.
- **Separate rules** – Defined in `public_rules` array, each with its own `public_channel_id`, `target_channel_id`, and `access_method`.
- **Independent rate limits** – Public endpoints have their own backoff tracking.
- **Old message forwarding** – Same as private channels.

### 📊 Real‑Time Statistics & Monitoring
- **Session stats** – Total cycles, average cycle time, messages per second.
- **Queue sizes** – Number of messages pending per source channel.
- **Rate limited requests** – Total count of 429 responses handled.
- **Image/CDN stats** – Images processed, upload successes/failures.
- **Uptime** – Human‑readable (hours, minutes, seconds) since start.
- **Token validity** – Last check time and current status.
- **Public stats** – Separate counters for public channel forwards.
- **All stats available** via `get_stats()` method (used by the dashboard).

---

## ⚙️ Complete Configuration Reference

Below is the **full** `config.json` structure with every possible option (defaults shown).

```json
{
  "token": "YOUR_DISCORD_USER_TOKEN",
  "settings": {
    "check_interval": 2.0,
    "max_message_length": 2000,
    "forward_old_messages": false,
    "old_messages_count": 10,
    "max_workers": 3,
    "request_timeout": 15,
    "rate_limit_delay": 0.5
  },
  "forwarding_rules": [
    {
      "id": 1,
      "name": "Example Rule",
      "source_channel_id": "123456789012345678",
      "target_channel_id": "876543210987654321",
      "enabled": true,
      "include_timestamp": true,
      "include_sender": true,
      "ignore_bots": false,
      "forward_attachments": true,
      "forward_embeds": true,
      "forward_links": true,
      "enable_links": true,
      "filter_prohibited_words": true,
      "block_prohibited_messages": false,
      "forward_old_count": 0,
      "old_messages_forwarded": false
    }
  ],
  "public_rules": [
    {
      "id": 101,
      "name": "Public Channel Example",
      "public_channel_id": "112233445566778899",
      "target_channel_id": "998877665544332211",
      "enabled": true,
      "access_method": "guest_token",
      "guest_token": "optional_guest_token_here",
      "include_timestamp": true,
      "include_sender": true,
      "ignore_bots": true,
      "forward_attachments": true,
      "filter_prohibited_words": true,
      "block_prohibited_messages": false,
      "forward_old_count": 0,
      "old_messages_forwarded": false,
      "last_message_id": null
    }
  ],
  "prohibited_words": [
    "badword1",
    "badword2"
  ],
  "prohibited_words_enabled": false,
  "prohibited_words_case_sensitive": false,
  "block_prohibited_messages": false,
  "role_mappings": {
    "111111111111111111": "222222222222222222"
  },
  "cdn_config": {
    "enabled": true,
    "cdn_url": "https://your-cdn.com/uploads",
    "upload_endpoint": "https://your-cdn.com/upload.php",
    "api_key": "",
    "watermark_enabled": true,
    "watermark_text": "Forwarded by DiscordBot",
    "watermark_position": "bottom-right",
    "watermark_opacity": 0.7,
    "watermark_font_size": 50,
    "watermark_font_style": "regular",
    "watermark_font_color": "#FFFFFF",
    "watermark_background_color": "#000000",
    "watermark_background_opacity": 0.5,
    "watermark_padding": 10,
    "watermark_margin": 20,
    "watermark_rotation": 0,
    "watermark_scale": 1.0,
    "resize_images": true,
    "max_width": 1920,
    "max_height": 1080,
    "quality": 85,
    "format": "webp",
    "force_cdn": true,
    "hide_original_urls": true,
    "parallel_uploads": 3,
    "download_timeout": 15,
    "upload_timeout": 30
  },
  "public_stats": {
    "total_messages": 0
  }
}
```

**Minor but important config details**:
- `forward_old_count` – After setting a value, the rule automatically sets `old_messages_forwarded: true` to prevent re‑forwarding.
- `last_message_id` – Stored per public rule to resume where you left off.
- `rate_limit_delay` – Minimum time between requests to same endpoint (0.5s default).
- `hide_original_urls` – If `true`, the original Discord CDN link is **not** appended (only the CDN link is shown).

---

## 🚀 Quick Start

1. **Clone & install**
   ```bash
   git clone https://github.com/yourusername/discord-message-forwarder.git
   cd discord-message-forwarder
   pip install -r requirements.txt
   ```

2. **Create `config.json`** (or let the script generate a default one).
3. **Insert your Discord user token** – obtain from browser developer tools (network tab → `Authorization` header).
4. **Add at least one forwarding rule** (channel IDs must be numeric strings).
5. **Run the forwarder**
   ```bash
   python discord_forwarder.py
   ```
6. **Access the dashboard** – open `http://localhost:5000` (if you use the companion web UI) or monitor console output.

---

## 📈 Performance & Optimisation

- **Cycle time with 20 active rules**: 4–6 seconds (down from 12+ seconds before rate‑limit tuning).
- **Image processing**: 2–5 seconds per image (depending on size and watermark complexity).
- **Memory footprint**: ~120–200 MB under heavy load.
- **CPU usage**: 1–2 cores during parallel image processing.
- **Network**: Respects Discord’s global 50 req/s; never triggers account restrictions when configured correctly.

**Tuning tips**:
- Reduce `max_workers` to 2 or 3 if you still see 429s.
- Increase `check_interval` if you monitor many quiet channels.
- Use `force_cdn: true` to avoid leaking Discord CDN links.
- Enable `prohibited_words_enabled` only when needed – it adds a small regex overhead.

---

## 🏷️ Tags

`python` `discord` `discord-forwarder` `discord-bot` `message-forwarder` `cdn` `watermark` `rate-limiting` `threading` `asyncio` `aiohttp` `pillow` `image-processing` `discord-api` `automation` `discord-tools` `python3` `parallel-processing` `queue-system` `role-mapping` `content-filter` `prohibited-words` `discord-channels` `guild-management` `json-config` `hot-reload` `exponential-backoff` `429-handling` `discord-cdn` `image-upload` `watermarking` `preset-config` `performance-monitoring` `real-time-dashboard` `uptime-tracker` `rule-manager` `public-channels` `guest-token` `discord-user-token`

---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` for more information.

---

<p align="center">
  <sub>Built with ❤️ by ATRO RDX - the Discord api/automation community.</sub><br>
  <sub>⭐ Star this repository if it helps you!</sub>
</p>

