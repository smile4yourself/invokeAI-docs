# Manual Install

!!! warning "This is for Advanced Users"

    **Python experience is mandatory.**

## Introduction

InvokeAI is distributed as a python package on PyPI, installable with `pip`. There are a few things that are handled by the installer and launcher that you'll need to manage manually, described in this guide.

### Requirements

Before you start, go through the [installation requirements](./INSTALL_REQUIREMENTS.md).

### Installation Walkthrough

1. Create a directory to contain your InvokeAI library, configuration
    files, and models. This is known as the "runtime" or "root"
    directory, and often lives in your home directory under the name `invokeai`.

    We will refer to this directory as `INVOKEAI_ROOT`. For convenience, create an environment variable pointing to the directory.

    === "Linux/macOS"

        ```bash
        export INVOKEAI_ROOT=~/invokeai
        mkdir $INVOKEAI_ROOT
        ```

    === "Windows (PowerShell)"

        ```bash
        Set-Variable -Name INVOKEAI_ROOT -Value $Home/invokeai
        mkdir $INVOKEAI_ROOT
        ```

1. Enter the root (invokeai) directory and create a virtual Python environment within it named `.venv`.

    !!! warning "Virtual Environment Location"

        While you may create the virtual environment anywhere in the file system, we recommend that you create it within the root directory as shown here. This allows the application to automatically detect its data directories.

        If you choose a different location for the venv, then you _must_ set the `INVOKEAI_ROOT` environment variable or specify the root directory using the `--root` CLI arg.

    ```terminal
    cd $INVOKEAI_ROOT
    python3 -m venv .venv --prompt InvokeAI
    ```

1. Activate the new environment:

    === "Linux/macOS"

        ```bash
        source .venv/bin/activate
        ```

    === "Windows"

        ```ps
        .venv\Scripts\activate
        ```

    !!! info "Permissions Error (Windows)"

        If you get a permissions error at this point, run this command and try again

        `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`

    The command-line prompt should change to to show `(InvokeAI)` at the beginning of the prompt.

    The following steps should be run while inside the `INVOKEAI_ROOT` directory.

1. Make sure that pip is installed in your virtual environment and up to date:

    ```bash
    python3 -m pip install --upgrade pip
    ```

1. Install the InvokeAI Package. The base command is `pip install InvokeAI --use-pep517`, but you may need to change this depending on your system and the desired features.

    - You may need to provide an [extra index URL](https://pip.pypa.io/en/stable/cli/pip_install/#cmdoption-extra-index-url). Select your platform configuration using [this tool on the PyTorch website](https://pytorch.org/get-started/locally/). Copy the `--extra-index-url` string from this and append it to your install command.

        !!! example "Install with an extra index URL"

            ```bash
            pip install InvokeAI --use-pep517 --extra-index-url https://download.pytorch.org/whl/cu121
            ```

    - If you have a CUDA GPU and want to install with `xformers`, you need to add an option to the package name. Note that `xformers` is not necessary. PyTorch includes an implementation of the SDP attention algorithm with the same performance.

        !!! example "Install with `xformers`"

            ```bash
            pip install "InvokeAI[xformers]" --use-pep517
            ```

1. Deactivate and reactivate your runtime directory so that the invokeai-specific commands become available in the environment:

    === "Linux/macOS"

        ```bash
        deactivate && source .venv/bin/activate
        ```

    === "Windows"

        ```ps
        deactivate
        .venv\Scripts\activate
        ```

1. Run the application:

    Run `invokeai-web` to start the UI. You must activate the virtual environment before running the app.

    !!! warning

        If the virtual environment is _not_ inside the root directory, then you _must_ specify the path to the root directory with `--root \path\to\invokeai` or the `INVOKEAI_ROOT` environment variable.
