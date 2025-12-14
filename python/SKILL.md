---
name: python
description: Use this skills for managing python projects and developing python code.
---

# Python Development Skill

This skill provides guidance for quality focused python development and project management.

All the instructions in this document ARE MANDATORY and must be followed at all times.

## Project management


### Project structure

* uv must be used to manage the project, the dependencies and the configuration.
* projet.toml must be used to define the project configuration.

### Build and Tooling

* uv must be uses to create virtual environments and install dependencies.
* uv must be used to build run the project.
* virtual environment must be created and used.
* No python package must be installed globally.
* uv is already installed

## Python development

### Python code

* Code formatting must adhere to the PEP8 style guide.
* Type hints must ALWAYS be used.
* Code must be properly documented using docstrings and additional comments
  to clearly explain the purpose and functionality of the code.
* Error handling must be done properly.
* All exceptions must be handled properly.
* Use ruff for linting and formatting.
* Use mypy for type checking.
* **MANDATORY**: ALWAYS check with API reference of the package you are using to see if there is a function class or method that does what you need and then
  verify that the code is correct by checking with the API reference and/or example code.
* **MANDATORY**: before adding or editing any code **ALWAYS** verify with the API reference and code examples that the code is correct and that the
  right methods and funcctions are called and the right data types are uses. After modifying code check again that the code is correct and that the
  right mehtods and functions are called and the right data types are used.


### Python code editing

1. Plan edit.
2. Check in the local site-packages, API reference, examples or throught web search
   that the code you are about to write is correct.
3. After editing code use ruff for linting and mypy to check that typing is correct.
4. Run 'uv run -m py_compile' on the edited file to check that the code is correct.
5. If any error occurs go back to step 1.


## Software design

* Code must be modular and well-structured.
* Seprate concerns must be clearly defined and separated.
* Design documents must be created to document the architecture and design of the project.
* Mermaid diagrams must be used to visually represent the architecture and design.

## Documentation

In addition to the requirements in the 'Python development' section, proper
development documentaiton must be created.
