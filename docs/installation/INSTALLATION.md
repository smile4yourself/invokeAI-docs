# Installation and Updating Overview

Before installing, review the [installation requirements] to ensure your system is set up properly.

See the [FAQ] for frequently-encountered installation issues.

If you need more help, join our [discord] or [create an issue].

<h2>Automatic Install & Updates </h2>

✅ The automatic install is the best way to run InvokeAI. Check out the [installation guide] to get started.

⬆️ The same installer is also the best way to update InvokeAI - Simply rerun it for the same folder you installed to.

The installation process simply manages installation for the core libraries & application dependencies that run Invoke.
Any models, images, or other assets in the Invoke root folder won't be affected by the installation process.

<h2>Manual Install</h2>

If you are familiar with python and want more control over the packages that are installed, you can [install InvokeAI manually via PyPI].

Updates are managed by reinstalling the latest version through PyPi.

<h2>Developer Install</h2>

If you want to contribute to InvokeAI, consult the [developer install guide].

<h2>Docker Install</h2>

This method is recommended for those familiar with running Docker containers.

We offer a method for creating Docker containers containing InvokeAI and its dependencies. This method is recommended for individuals with experience with Docker containers and understand the pluses and minuses of a container-based install.

See the [docker installation guide].

<h2>Other Installation Guides</h2>

- [PyPatchMatch](060_INSTALL_PATCHMATCH.md)
- [Installing Models](050_INSTALLING_MODELS.md)

[install InvokeAI manually via PyPI]: 020_INSTALL_MANUAL.md
[developer install guide]: INSTALL_DEVELOPMENT.md
[docker installation guide]: 040_INSTALL_DOCKER.md
[installation guide]: 010_INSTALL_AUTOMATED.md
[FAQ]: ../help/FAQ.md
[discord]: discord.gg/invoke-ai
[create an issue]: https://github.com/invoke-ai/InvokeAI/issues
[installation requirements]: INSTALL_REQUIREMENTS.md
