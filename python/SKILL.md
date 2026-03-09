---
name: python
description: Rules for managing python projects and for developing python code. Use this skills for managing python projects and developing python code.
---

# Python Development Skill

This skill provides guidance for quality focused python development and project management.

All the instructions in this document ARE MANDATORY and must be followed at all times.

## Project management


### Project structure

* uv must be used to manage the project, the dependencies and the configuration.
* projet.toml must be used to define the project configuration.

### Build and Tooling

* uv must be used to create virtual environments and install dependencies.
  * run `uv venv` to create a virtual environment.
* uv must be used to build and run the project.
  * run all the scripts with `uv run` or `uv run -m <module_name>`.
* **MANDATORY**: DO NOT USE pip: use `pyproject.toml` to define the dependencies and manage the project.
* use `uv add` instead of `pip install` to add dependencies.
* virtual environment must be created and used.
* No python package must be installed globally.
* uv is already installed
* **MANDATORY**: use hatchling as the build backend.

## Python development

### Python code

* Minimize the use of nested if/else statements by using the match statement.
* Do not use global variables. Encapulate settings, constants and configuration in dataclasses.
* Code formatting must adhere to the PEP8 style guide.
* Type hints must ALWAYS be used.
* Code must be properly documented using docstrings and additional comments
  to clearly explain the purpose and functionality of the code.
* All methods and functions accepting parameters must have all the parameters documented.
* All methods and functions returning values must have all the returned values documented.
* Error handling must be done properly.
* All exceptions must be handled properly.
* Use ruff for linting and formatting.
* Use mypy for type checking.
* **MANDATORY**: ALWAYS check with the API reference of the package you are using or the local site package code or sample code to see if there is a function class or method that does what you need and then
  verify that the code is correct by checking with the API reference and/or example code.
* **MANDATORY**: before adding or editing any code **ALWAYS** verify with the API reference, the local site packages, code examples or web references that the code is correct and that the
  right methods and funcctions are called and the right data types are uses. After modifying code check again that the code is correct and that the
  right mehtods and functions are called and the right data types are used.
* Follow the Hatchlings rulles to organise the project structure and decide where source files should be placed.


### Python code editing

1. Plan edit: double check that the code is correct before making changes.
2. Verify that the required packages are installed.
3. Check in the local site packages, API reference, examples or throught web search
   that the code you are about to write is correct.
4. After editing code use ruff for linting and mypy to check that typing is correct.
5. Run `uv run -m py_compile` on the edited file to check that the code is correct.

6. If any error occurs go back to step 1.

### Additional guidelines for python development

* Do not add support for logging by default. Only if explicitly requested.
* Do not add support for Docker or containers by default. Only if explicitly requested.
* Always assume that the information you have in your memory about packages and Python in general is INCORRECT and verify through parsing of local site packages, API reference, examples or through web search that the code you are about to write is correct.
* Stick to the requirements, do not add additional features unless explicitly requested, but feel free to ask the author for advice.
* Do not take shortcuts.
* Follow the requirements and be thorough not quick.

## Software design

* Code must be modular and well-structured.
* Seprate concerns must be clearly defined and separated.
* Design documents must be created to document the architecture and design of the project.
* Mermaid diagrams must be used to visually represent the architecture and design.

## Documentation

In addition to the requirements in the 'Python development' section, proper
development documentaiton must be created.
