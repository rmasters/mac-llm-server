# mac-llm-server

Scripts I use to run a local LLM server on a Mac, including:

*   Ansible playbook that:
    *   Installs Homebrew,
    *   Installs Ollama, and configures it to run on boot as a launchd service,
    *   Installs Podman, to run container images (replaces Docker),
    *   Installs Open WebUI for Ollama, and runs on boot as a launchd service.

By default, Ollama will run on 0.0.0.0:8080, and Open WebUI runs on 0.0.0.0:8000.

Open WebUI ships with a container image, but it [seems like Apple Silicon GPUs aren't accessible in Docker/Podman][docker-apple-gpus] (only CUDA & ROC). So we run Open Web UI in a container, and Ollama on the host.
[docker-apple-gpus]: https://chariotsolutions.com/blog/post/apple-silicon-gpus-docker-and-ollama-pick-two/

## Notes

*   I'm repurposing a Mac Mini M1 16GB as a dedicated LLM host
*   I started from a clean macOS install, and configured as so:
    *   Enabled Automatic Login
    *   Enabled Remote Login (SSH) under Settings > General > Sharing.
    *   The official Apple Remote Desktop client costs £80. I don't need this yet, but if I do, this will include VNC or similar.
    *   If you shut down the server, you'll have to hook up a display and keyboard to unlock FileVault.
*   Preferring MLX models, to take advantage of Neural Engine
*   At time of writing, it's unclear if running Asahi can access Neural Engine (github.com/eiln/ane has a reverse-engineered driver). Linux would be a better choice for a server.
*   The first time you run this, there's a bunch of password requests, network access requests that appear to be UI only.

## Quickstart

1.  I'm using Python 3.12, earlier versions may work.
2.  Create a virtualenv: `python -m venv .venv/`
3.  Use the Python in this virtualenv: `source .venv/bin/activate`
4.  Install dependencies: `pip install requirements.lock`
    *   Contrary to the Ansible docs, the `ansible` package should contain the `community.general` and `containers.podman` collections. Report an issue if not.
5.  If you are running Ansible on the same machine you want to run Ollama on, simply run:  
    `ansible-playbook -K -i local.inventory.yml playbooks/install.yml`
6.  If the target machine is another computer on your network, ensure you can access it via SSH, and run:  
    `ansible-playbook --inventory=192.168.1.x --user=ssh_user playbooks/install.yml`  
    (where `192.168.1.x` and `ssh_user` are the IP address and remote user of the target machine respectively).
    *   It's suggested that you create an inventory 

### Suggested repository setup

To keep your remote machine configuration as code, e.g. managing other Ansible playbooks, Ollama Modelfiles, etc., it's suggested you use a repository structure like this:

-   Create a new Git repo, and add this repo as a Git submodule, e.g.:  
    `git submodule add git@github.com:rmasters/mac-llm-server`
-   Add an [Ansible inventory file](https://docs.ansible.com/ansible/latest/getting_started/get_started_inventory.html) with the configuration of your target machine(s),
-   Add your custom files and playbooks to install models to the base repository, similar to how playbooks/llama3.yml configures Ollama with a llama3 model.

You can see an example at https://github.com/rmasters/mac-mini-m1-16gb-llms.

On the rare occasion I make a change to this repository, you'll need to update the submodule - the easiest way is to change into the submodule directory, run `git pull` (or `git checkout $tag`, change back into the base repo and commit the changes to the updated submodules file.
