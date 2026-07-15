# Local Bonsai assistant setup

This guide configures Bonsai Local Studio without publishing or copying personal data. Models, LoRAs, chats, documents, browser history, optional agent profiles, and any external-search API key remain on the operator's machine.

## Recommended workflow

Use a trusted coding harness to inspect the repository, install dependencies, launch the two local services, and verify the health endpoints. ChatGPT Work, Claude Work, Antigravity, Goose, and OpenCode are examples. Review every command before it runs.

You can give a harness this request:

> Clone `ProfEngel/Bonsai-Local-Studio`. Set up the Bonsai Image Studio locally. On macOS use the Bonsai Image MLX model and `prism-ml/Bonsai-27B-mlx-1bit` through MLX. On Windows use the Bonsai Image Windows path and a local Bonsai-27B GGUF server. Do not upload local files, LoRAs, chats, documents, API keys, or model weights. Verify `http://127.0.0.1:3000` and the local model endpoint only.

## macOS / Apple Silicon

1. Clone this fork and run `./setup.sh` to install the Studio and download the Bonsai Image model.
2. In a separate terminal, run an MLX-compatible Bonsai-27B server:

   ```bash
   python3 -m pip install -U mlx-lm
   mlx_lm.server --model prism-ml/Bonsai-27B-mlx-1bit --port 8081
   ```

3. Start the Studio:

   ```bash
   ./scripts/serve.sh
   ```

4. Open `http://127.0.0.1:3000`, then confirm the endpoint and model identifier under **Settings**.

## Windows

1. Follow the existing [Windows setup](../scripts/windows.md) to install and start the Bonsai Image backend.
2. Start a local OpenAI-compatible GGUF server for Bonsai-27B, for example with `llama-server`:

   ```powershell
   llama-server -m C:\Models\Bonsai-27B.gguf --port 8081
   ```

3. Enter `http://127.0.0.1:8081/v1` and the model name reported by the server in **Settings**.

## Local agents and LoRAs

- Put optional chat-agent profiles below `~/.bonsai-studio/agents/<agent>/chat/profile.json` or set `BONSAI_CHAT_AGENTS_DIR` to an alternate local directory.
- Put Flux/Klein LoRA files in a local `loras/` directory configured through `MFLUX_STUDIO_LORA_DIR`. No adapters are tracked by Git.
- The Studio reads profile JSON only; it does not execute agent scripts.

## Privacy and external research

Image generation, model inference, documents, chat history, LoRAs, and agent profiles are local. When **Web search** is enabled, only the current question is sent to the configured search provider. In Studio, open the gear icon, choose a provider under **Web research**, and enter a key if needed. Manually entered keys remain on the local machine with owner-only file permissions; the browser receives only the configured/not-configured status. Never commit or paste keys into an issue.

## Update-safe local settings

Studio keeps preferences independently from the cloned or updated application folder. Non-secret settings such as local endpoints, selected model, provider choice, and the chat/prompt-optimizer instructions are saved in `~/.config/bonsai-studio/settings.json`; web-search credentials remain separately in `~/.config/bonsai-studio/web-search.json`. Both files use owner-only permissions. Existing browser-only preferences are migrated automatically on the next Studio page load, chat request, or image-generation session.

## Optional Goose agent harness

Selecting a local agent can run it through Goose instead of merely adding its prompt to the chat. The Studio invokes a short-lived `goose run --no-profile` process using the selected local model and injects only that agent's declarative profile, `SKILL.md`, rules, workflows, current chat context, and explicitly prepared local tool results. It deliberately does **not** expose Goose's shell, computer, browser, or sending extensions to the model.

This is a safe orchestration boundary, not an autonomous action engine: an agent must never claim that it sent mail or changed files. Integrations that need a local fact (for example an unread-mail count) should be implemented as a narrowly scoped, read-only Studio connector and its result passed to the harness. Configure the local Goose provider and model with `BONSAI_GOOSE_PROVIDER` and `BONSAI_GOOSE_MODEL` before launching Studio when the defaults do not match your installation.

The Studio offers **Automatisch** (Tavily → Brave → public fallback), **Tavily**, **Brave Search**, and **Öffentliche Fallback-Suche** in Settings. For unattended or scripted launches, export one or both keys before starting the Studio:

```bash
export TAVILY_API_KEY='…'        # optional, preferred for source-grounded research
export BRAVE_SEARCH_API_KEY='…'  # optional, independent fallback
./scripts/serve.sh
```

Environment variables and the Settings screen are compatible; this is useful when a local launcher already manages a token.

An explicitly selected provider without its local key fails clearly; it never silently substitutes a different provider. The public fallback remains available for key-free experiments, but is deliberately shown as such in the answer.
