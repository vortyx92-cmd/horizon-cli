<p align="center">
  <img src="logo.png" alt="Horizon CLI" width="480">
</p>

# Horizon CLI

[![Discord](https://img.shields.io/badge/Discord-Join%20for%20support-5865F2?logo=discord&logoColor=white)](https://discord.gg/ENayFjKwHe)

**if you do not trust us, feel free to run through [VirusTotal](virustotal.com)**

A terminal chat client for free AI model APIs. Horizon finds the free models available to your API keys, ranks them, and routes each message to the best one. When a model is rate limited or fails, it moves to the next one and keeps the conversation intact.

Supports Windows and macOS. Works with NVIDIA NIM, OpenRouter, Groq, Cerebras, Google AI Studio, Mistral, and any OpenAI-compatible endpoint.

**[Download the latest release](../../releases/latest)**

For help, bug reports, or feature requests, join the [Discord server](https://discord.gg/ENayFjKwHe).

## Features

- Live discovery of free models from each provider
- Ranking based on model quality, measured latency, remaining quota, and context size
- Automatic failover on rate limits, quota exhaustion, errors, and timeouts
- Conversation history carried across model switches, with older messages summarized when a model has a smaller context window
- A configurable handover prompt sent to each new model, with its response shown so you can confirm it has the context
- Pinned instructions and per-model prompts
- Saved, resumable chat sessions
- One-shot mode with stdin support for use in scripts

## Installation

Download the file for your system from the [releases page](../../releases/latest):

| System | File |
|---|---|
| Windows (x64) | `horizon-windows-amd64.exe` |
| Windows (ARM) | `horizon-windows-arm64.exe` |
| macOS (Intel and Apple Silicon) | `horizon-macos` |

### Windows

Run the executable. Windows SmartScreen may show a warning because the binary is not code signed; choose **More info**, then **Run anyway**. Accept the install prompt to add `hz` to your PATH.

### macOS

```sh
chmod +x horizon-macos
xattr -d com.apple.quarantine horizon-macos
./horizon-macos
```

The `xattr` command removes the Gatekeeper quarantine flag, since the binary is not notarized. Accept the install prompt to add `hz` to your PATH, then open a new terminal.

## API keys

The first run walks you through adding keys. Each provider offers a free tier. At least one key is required, and two or more are recommended so failover has somewhere to go.

| Provider | Key page |
|---|---|
| Groq | https://console.groq.com/keys |
| NVIDIA NIM | https://build.nvidia.com/settings/api-keys |
| OpenRouter | https://openrouter.ai/settings/keys |
| Google AI Studio | https://aistudio.google.com/apikey |
| Cerebras | https://cloud.cerebras.ai/platform |
| Mistral | https://console.mistral.ai/api-keys |

Free-tier limits are set by each provider and may change.

## Usage

```sh
hz                        # start an interactive chat
hz chat --resume          # resume the most recent chat
hz chat --model <name>    # prefer a specific model
hz prefer quality         # rank models by quality, speed, coding, or balanced
hz ask "question"         # single response, then exit
cat log.txt | hz ask "summarize this"
hz rank --probe           # benchmark and rank available models
hz models                 # list discovered models
hz setup                  # add or change API keys
hz sessions               # list saved chats
hz help                   # full command list
```

### Chat commands

```
/model                        show the current and next model
/switch [name]                switch models now
/use <name> | auto            prefer a model, or return to automatic selection
/models                       show the current ranking
/pin <text>                   add an instruction for this chat (/pin! for all chats)
/pins, /unpin <n>             list or remove pins
/onswitch [text]              view or set the handover prompt
/onswitch-for <pattern> = <text>   handover prompt for matching models
/modelprompt <pattern> = <text>    standing instructions for matching models
/report [note]                flag that the handover prompt failed on a model
/handover visible | silent    show or hide the handover exchange
/prefer [mode] [--once]       set the ranking mode (saved as default unless --once)
/context                      show context usage
/help                         list all commands
```

Patterns support `*` wildcards and match either `provider/model` or the model name, for example `*deepseek*`.

End a line with `\` or wrap text in `"""` for multi-line input. Press Ctrl+C to stop a response.

## How model switching works

Horizon stays on the current model as long as it is responding. When a request fails, the model is placed on a short cooldown and the message is retried on the next-ranked model.

Before the first message to a new model, Horizon sends a handover prompt and displays the reply:

```
◉ crossed horizon → nim/qwen3-235b  (groq/llama-3.3-70b: 429 rate limit)
  ┌ handover → nim/qwen3-235b
  │ sent:  You are taking over an ongoing conversation...
  │ reply: Caught up: we're debugging the login script in auth.py.
  └ didn't land? /report
```

If a model does not follow the handover, `/report` marks it so it receives a stricter prompt in future. A custom prompt can also be set for it with `/onswitch-for`. The visible handover uses one additional request per switch; `/handover silent` includes the prompt in the next request instead.

Latency and reliability are recorded from normal use and factored into future rankings.

### Ranking modes

| Mode | Behavior |
|---|---|
| `balanced` | Default. Weighs quality, speed, remaining quota, and context size. |
| `quality` | Favors the most capable models, even when they are slower. |
| `speed` | Favors the fastest-responding models. |
| `coding` | Like `quality`, with extra weight for models known for code. |

Set the default with `hz prefer <mode>` or `/prefer <mode>`. Use `--prefer <mode>` on `hz chat`, `hz ask`, or `hz rank`, or `/prefer <mode> --once`, to apply a mode without changing the default.

## Configuration

`hz config edit` opens the configuration file, which controls the system prompt, pins, handover prompts, ranking weights, and per-provider rate limits. Additional OpenAI-compatible endpoints can be added:

```toml
[[custom]]
name = "lmstudio"
base_url = "http://localhost:1234/v1"
```

## Data and privacy

API keys are stored in the operating system keychain (Windows Credential Manager or macOS Keychain) and are sent only to their respective providers. Chats, settings, and usage data are stored locally:

- Windows: `%AppData%\horizon-cli\`
- macOS: `~/Library/Application Support/horizon-cli/`

Horizon has no telemetry and no backend. Messages are sent to whichever provider is handling the request and are subject to that provider's terms. Some free tiers may use submitted data for training.

## Updating

Run the newly downloaded binary. It will offer to replace the installed version. Keys and chats are preserved.

## Uninstalling

```sh
hz uninstall          # remove the program
hz uninstall --all    # remove the program, PATH entry, keys, chats, and settings
```

## Verifying downloads

SHA-256 checksums for each release are listed in [SHA256SUMS.txt](SHA256SUMS.txt).

```sh
shasum -a 256 horizon-macos                # macOS
Get-FileHash horizon-windows-amd64.exe     # Windows PowerShell
```

---

Horizon CLI is not affiliated with any of the providers listed above.
