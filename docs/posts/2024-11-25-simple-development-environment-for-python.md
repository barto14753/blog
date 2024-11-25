---
layout: default
title: "Simple Development Environment for Python"
date: 2024-11-25 12:00:00
---

# Simple Development Environment

Python is a versatile programming language that is widely used in various fields such as web development, data science, machine learning, and more. Setting up a development environment for Python is essential to ensure smooth coding and testing of your projects. In this guide, we will walk through the steps to set up a Python development environment on your local machine using `nix` package manager and its wrapper `devenv`tool.

## Nix Package Manager

[Nix](https://nixos.org/) is a powerful package manager for Linux and other Unix systems that provides a functional, reliable, and reproducible package management system. It allows you to install, upgrade, and remove software packages in a declarative and atomic way. Nix ensures that your development environment is isolated, reproducible, and consistent across different machines.

## DevEnv Tool

[DevEnv](https://devenv.sh) is a wrapper tool for Nix that simplifies the process of setting up development environments for various programming languages and frameworks. It provides a user-friendly interface to manage your development environments, including creating scripts, tasks and services.

## Setting Up Python Development Environment

#### 1. **Install devenv**

First, you need to install the `devenv` tool by running the following command:

```bash
curl -L https://devenv.sh | sh
```

#### 2. **Init devenv**

Initialize the `devenv` tool by running the following command:

```bash
devenv init
```

It will create:

- `devenv` - directory in your home directory
- `devenv.nix` - file containing defintion of your development environment
- `devenv.yaml` - file containing imported packages

#### 3. **Update devenv.nix**

Edit `devenv.nix` file to define your development environment. For example, to set up a Python development environment, you can add the following lines:

```nix
 { pkgs, lib, config, inputs, ... }:
 {
   packages = [
   ];

   languages.python = {
   enable = true;
   version = "3.12";
   venv.enable = true;
   venv.requirements = ./requirements.txt;
   };

   enterShell = ''
   jupyter lab
   '';
}

```

Example `requirements.txt` file:

```txt
numpy==2.1.3
pandas==2.2.3
jupyterlab==4.3.0
scikit-learn==1.5.2
matplotlib==3.9.2
seaborn==0.13.2
pandas==2.2.3
```

#### 4. **Enter Development Environment**

To enter the Python development environment, run the following command:

```bash
devenv shell
```

This will activate the Python environment and you can start coding and testing your projects.
