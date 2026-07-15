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

Image generation, model inference, documents, chat history, LoRAs, and agent profiles are local. When **Web search** is enabled, only the current question is sent to the configured search provider. Keep API keys in local environment variables or the operating system keychain; never commit or paste them into an issue.

The Studio offers **Automatisch** (Tavily → Brave → public fallback), **Tavily**, **Brave Search**, and **Öffentliche Fallback-Suche** in Settings. Export one or both keys before starting the Studio:

```bash
export TAVILY_API_KEY='…'        # optional, preferred for source-grounded research
export BRAVE_SEARCH_API_KEY='…'  # optional, independent fallback
./scripts/serve.sh
```

An explicitly selected provider without its local key fails clearly; it never silently substitutes a different provider. The public fallback remains available for key-free experiments, but is deliberately shown as such in the answer.
