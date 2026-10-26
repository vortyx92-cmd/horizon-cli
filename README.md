<p align="center">
  <img src="logo.png" alt="Horizon CLI" width="520">
</p>

<h1 align="center">Horizon CLI</h1>

<p align="center">
  <b>The best free AI models in your terminal, switching automatically so you never hit a wall.</b><br>
  Windows · macOS · free models only · your keys stay on your machine
</p>

<p align="center">
  <a href="../../releases/latest"><b>⬇ Download the latest release</b></a>
</p>

---

## What it does

Free AI APIs are great until you hit a rate limit mid-conversation. Horizon CLI fixes that:

- **Finds every free model** across NVIDIA NIM, OpenRouter, Groq, Cerebras, Google AI Studio and Mistral, live, every time.
- **Ranks them** on quality, real measured speed, how much quota you have left, and context size, then picks the best one.
- **Switches automatically** when a model hits a limit, errors or times out, and resends your message so you don't notice.
- **Keeps your context** across switches. If the next model has a smaller window, older messages get compressed into a summary instead of lost.
- **Briefs the new model** on every switch, and shows you its reply so you can see it's caught up.

```
› which file did we say was broken?
◉ crossed horizon → nim/qwen3-235b  (groq/llama-3.3-70b: 429 rate limit)
  ┌ handover → nim/qwen3-235b
  │ sent:  You are taking over an ongoing conversation...
  │ reply: Caught up: we're debugging the login script in auth.py.
  └ didn't land? /report
It was auth.py: the token check on line 42.
◉ nim/qwen3-235b · 39/40 rpm · ctx 2k/128k
```

## Download

| Your computer | File |
|---|---|
| Windows (most PCs) | [`horizon-windows-amd64.exe`](../../releases/latest/download/horizon-windows-amd64.exe) |
| Windows on ARM | [`horizon-windows-arm64.exe`](../../releases/latest/download/horizon-windows-arm64.exe) |
| Mac (Intel or Apple Silicon) | [`horizon-macos`](../../releases/latest/download/horizon-macos) |

Not sure which Windows you have? Pick **amd64**.

## Install

### Windows
1. Double-click `horizon-windows-amd64.exe`.
2. If you see **"Windows protected your PC"**, click **More info**, then **Run anyway**. The app isn't code-signed yet, so Windows doesn't recognize it.
3. Say **yes** when it offers to install. Then open any terminal and type `hz`.

### macOS
```sh
chmod +x ~/Downloads/horizon-macos
xattr -d com.apple.quarantine ~/Downloads/horizon-macos
~/Downloads/horizon-macos
```
Say **yes** when it offers to install, then open a new Terminal window and type `hz`.

> The `xattr` line clears macOS's "unidentified developer" block. The app isn't notarized yet.

## Get free API keys

The first run walks you through this. Each key is free and takes about a minute. You need at least one, and **two or more** gives auto-switching something to switch to.

| Provider | Get a key | Good for |
|---|---|---|
| **Groq** | [console.groq.com/keys](https://console.groq.com/keys) | Very fast, generous daily limits, the best first key |
| **NVIDIA NIM** | [build.nvidia.com](https://build.nvidia.com/settings/api-keys) | Big models, no daily cap |
| **OpenRouter** | [openrouter.ai/settings/keys](https://openrouter.ai/settings/keys) | Widest model selection |
| **Google AI Studio** | [aistudio.google.com/apikey](https://aistudio.google.com/apikey) | Gemini, huge context |
| **Cerebras** | [cloud.cerebras.ai](https://cloud.cerebras.ai/platform) | Fastest output, small context |
| **Mistral** | [console.mistral.ai/api-keys](https://console.mistral.ai/api-keys) | Needs phone verification |

Free-tier limits are set by each provider and change often.

## Usage

```sh
hz                          # start chatting
hz chat --resume            # pick up your last chat
hz ask "explain DNS"        # one quick answer
cat error.log | hz ask "why is this crashing?"
hz rank --probe             # test and rank every free model
hz setup                    # add or change keys
hz help                     # everything else
```

### In a chat

| Command | What it does |
|---|---|
| `/model` | See which model is answering, and which is next |
| `/switch` | Move to the next best model now |
| `/use <name>` | Prefer a model (`/use auto` to go back) |
| `/pin <text>` | A fact every model always sees in this chat (`/pin!` for every chat) |
| `/onswitch <text>` | Change the briefing sent to a model when it takes over |
| `/onswitch-for <pattern> = <text>` | A takeover briefing just for certain models |
| `/modelprompt <pattern> = <text>` | Always-on instructions for certain models |
| `/report [note]` | The takeover briefing didn't work on this model |
| `/handover visible\|silent` | Show or hide the takeover exchange |
| `/prefer speed\|quality\|coding` | Change how models are ranked |
| `/context` | See how much of the conversation is kept |
| `/help` | All commands |

Patterns use `*` as a wildcard, e.g. `/modelprompt *deepseek* = Keep your reasoning short.`

Multi-line input: end a line with `\`, or wrap a block in `"""`. **Ctrl+C** stops a reply.

## How switching works

1. Every turn goes to the current model if it's still healthy. Horizon doesn't hop around for no reason.
2. If it fails (rate limit, quota used up, error, timeout, empty reply), that model goes on a cooldown and the next best one gets your message.
3. The new model gets a **takeover briefing** and you see its reply. If it clearly didn't take, type `/report`. That model gets a stronger briefing from then on, and you can write it its own with `/onswitch-for`.
4. Speed and reliability are measured from real use, so the ranking gets better the more you use it.

> Visible takeovers cost one extra request per switch. `/handover silent` sends the briefing inside the next request instead.

## Privacy

- API keys are stored in your system keychain (Windows Credential Manager or macOS Keychain) and are only sent to the provider they belong to.
- Chats, settings and usage counters are saved locally:
  - Windows: `%AppData%\horizon-cli\`
  - macOS: `~/Library/Application Support/horizon-cli/`
- There's no telemetry, account or server. Horizon talks only to the AI providers you add.
- What you type goes to whichever provider is answering, under that provider's terms. Some free tiers may use prompts for training.

## Config

Run `hz config edit` to open `config.toml`. There you can change the system prompt, pins, takeover briefings, per-model prompts, ranking weights, per-provider limits, and add any OpenAI-compatible endpoint (like a local LM Studio server):

```toml
[[custom]]
name = "lmstudio"
base_url = "http://localhost:1234/v1"
```

## Verify your download

Checksums for each release are in [`SHA256SUMS.txt`](SHA256SUMS.txt).

```sh
# macOS
shasum -a 256 horizon-macos
# Windows (PowerShell)
Get-FileHash horizon-windows-amd64.exe
```

## Updating

Download the new version and run it. It will offer to **replace your installed `hz`**. Your keys and chats are kept.

## Uninstall

```sh
hz uninstall          # remove the program, keep your keys and chats
hz uninstall --all    # remove everything: program, PATH entry, keys, chats, settings
```

`--all` asks you to type `yes` before deleting anything.

---

<p align="center"><sub>Horizon CLI isn't affiliated with any AI provider. Free tiers are provided by each service and can change at any time.</sub></p>
