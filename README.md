# mac-llm-server

An Ansible playbook I use to run a local LLM server. Includes:

* [x] Ollama, installed from Homebrew and configured to run in launchd
* [x] Podman, to run container images
* [x] Open WebUI for Ollama, running in Podman
* [ ] [exo](https://github.com/exo-explore/exo)

By default, Ollama will run on 0.0.0.0:11434, and Open WebUI runs on 0.0.0.0:8080.

I chose to run Ollama on the host, to give it the [best possible access to Apple Silicon][docker-apple-gpus].

[docker-apple-gpus]: https://chariotsolutions.com/blog/post/apple-silicon-gpus-docker-and-ollama-pick-two/

I make tweaks to this project infrequently and as-needed; you may prefer to fork it to suit your needs.

## Notes

*   Tested and running on a Mac Mini M1 16GB. Patiently waiting for M4s.
*   Starting from a clean macOS install, you just need to enable Remote Login (SSH) under Settings > General > Sharing.
    *   I have tried this on Asahi, and while Linux is preferable Apple Silicon support is pretty limited atm.
*   You will need a display and input to authorise the installation of the xcode development tools.

## Quickstart

I'm using Python 3.12 to run Ansible.

You can install Ansible and dependencies in the usual way, but [rye](https://rye-up.com) is recommended.
```
python -m venv .venv/
source .venv/bin/activate
pip install requirements.lock
```

To setup your local machine, run:  
```ansible-playbook -K -i local.inventory.yml playbooks/install.yml```

To target another machine over SSH:  
```ansible-playbook --inventory=192.168.1.x --user=ssh_user playbooks/install.yml```

### Suggested repository setup

To keep your remote machine configuration as code, e.g. managing other Ansible plays, Ollama Modelfiles, etc., it's suggested you use a repository structure like this:

-   Create a new Git repo, and add this repo as a Git submodule:  
    `git submodule add git@github.com:rmasters/mac-llm-server`
-   Add an [Ansible inventory file](https://docs.ansible.com/ansible/latest/getting_started/get_started_inventory.html) with the configuration of your target machine(s),
-   Add your custom files and playbooks to install models to the base repository, similar to how playbooks/llama3.yml configures Ollama with a llama3 model.
