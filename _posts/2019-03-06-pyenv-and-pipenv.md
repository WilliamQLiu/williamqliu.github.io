---
layout: post
title: Pyenv and Pipenv
---


# {{ page.title }}

You will likely need to manage multiple versions of Python and different
Python packages.

## Virtualenv and Virtualenvwrapper

Previously you would use virtualenv to create new 'environments' (and specifying
the Python version when creating the env). Install libraries into your environment.
Use `workon myenv` to work on your environment.

## Pyenv on Ubuntu

Install pyenv

    curl -L https://raw.githubusercontent.com/pyenv/pyenv-installer/master/bin/pyenv-installer | bash

Add to `~/.bashrc`

    export PATH="~/.pyenv/bin:$PATH"
    eval "$(pyenv init -)"
    eval "$(pyenv virtualenv-init -)"

Install versions

    pyenv install 3.5.0
    Downloading Python-3.5.0.tar.xz...
    -> https://www.python.org/ftp/python/3.5.0/Python-3.5.0.tar.xz
    Installing Python-3.5.0...


Check python versions

    pyenv versions

Set the global python versions

    pyenv global 3.5.0

    pyenv versions

Note: You can use pyenv with virtualenvs:

    pyenv virtualenv 3.7.0 myenv
    pyenv virtualenvs  # list all virtualenv
    pyenv activate myenv
    pyenv deactivate
    pyenv uninstall myenv  # uninstall virtualenv


# Pipenv

Pipenv automatically creates and manages a virtualenv for your projects.
You can add or remove packages from a `Pipfile` as you install/uninstall packages.
You generate a `Pipfile.lock`, which produces deterministic builds.
You don't have to use `pip` and `virtualenv` separately.

## Install 

Mac with

    brew install pipenv

Ubuntu
    
    sudo apt install python3-pip python3-dev
    pip3 install --user pipenv

## Add pipenv to PATH

    # Add pipenv (and other python scripts) to PATH
    echo "PATH=$HOME/.local/bin:$PATH" >> ~/.bashrc

## Autocomplete

Add to your `.bashrc` or `.bash_profile` for bash auto complete.

    eval "$(pipenv --completion)"

## Usage

    pipenv install requests

    pipenv lock

