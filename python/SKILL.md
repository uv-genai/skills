---
name: python
description: Rules for managing python projects and for developing python code. Use this skills for managing python projects and developing python code.
---

# Python Development Skill

This skill provides guidance for quality focused python development and project management.

All the instructions in this document **ARE MANDATORY** and must be followed at all times.

## Project management


### Project structure

* uv must be used to manage the project, the dependencies and the configuration.
* projet.toml must be used to define the project configuration.
* you are using hatchling as the build backend, follow the hatchling guidelines for project structure.

### Build and Tooling

* uv must be use uv to create virtual environments and install dependencies.
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

* Do not rush to implement the code, perform proper analysis and use the MCP tools (e.g. Serena) as directed.
* Always verify that the code you write is supported by the modules you import:
  * verify that functions and methods are present in the modules you import before using them,
  * verify that all constants and classess are present in the modules you import before using them.
* Prioritize **proper architecture and process** over speed and simplicity.
* Minimize the use of nested if/else statements by using the match statement.
* Do not use standalone global variables. Encapulate settings, constants and configuration in dataclasses.
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
* Use py_compile to check that the code is correct.
* Never ever ignore the output from ruff, mypy and py_compile, always consider the output from these tools to be true and correct.
* Never ever remove type hints, or make changes to make the code pass mypy, ruff and py_compile.
* Do not change the code to make errors and warnings go away, fix the errors and warnings instead.
* **MANDATORY**: ALWAYS check with the API reference of the package you are using or the local site package code or sample code to see if there is a function class or method that does what you need and then
  verify that the code is correct by checking with the API reference and/or example code.
* **MANDATORY**: before adding or editing any code **ALWAYS** verify with the API reference, the local site packages, code examples or web references that the code is correct and that the
  right methods and functions are called and the right data types are uses. After modifying code check again that the code is correct and that the
  right mehtods and functions are called and the right data types are used.
* Follow the Hatchlings rulles to organise the project structure and decide where source files should be placed.


### Python code editing

0. If available use the Serena MCP to search and edit the code.
1. Plan edit: double check that the code is correct before making changes.
2. Verify that the required packages are installed.
3. Check in the local site packages, API reference, examples or throught web search
   that the code you are about to write is correct.
4. After editing code use ruff for linting and mypy to check that typing is correct.
5. Run `uv run -m py_compile` on the edited file to check that the code is correct.
6. If any error occurs go back to step 1.
7. Do not rush to implement the code, perform proper analysis and use the MCP tools (e.g. Serena) as directed.
8. Verify that the all the functions, methods, classes, constantst and variables you use are present in the modules you import.

### Additional guidelines for python development

* Follow a proper development process, do not rush to implement the code.
* Prioritize **proper architecture and process** over speed and simplicity.
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

## Documentation

In addition to the requirements in the 'Python development' section, proper
development documentaiton must be created.
