# mac-llm-server

Scripts I use to run a local LLM server on a Mac, including:

*   Ansible playbook that configures:
    *   Homebrew,
    *   Ollama,
    *   Open WebUI for Ollama,

## Notes

*   I'm repurposing a Mac Mini M1 16GB as a dedicated LLM host
*   I started from a clean macOS install, and configured as so:
    *   Enabled Automatic Login
    *   Enabled Remote Login (SSH) under Settings > General > Sharing.
    *   The official Apple Remote Desktop client costs £80. I don't need this yet, but if I do, this will include VNC or similar.
    *   If you shut down the server, you'll have to hook up a display and keyboard to unlock FileVault.
*   Preferring MLX models, to take advantage of Neural Engine
*   At time of writing, it's unclear if running Asahi can access Neural Engine (github.com/eiln/ane has a reverse-engineered driver). Linux would be a better choice for a server.
