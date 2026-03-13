---
name: general-rules
description: General rules to apply to software development projects. Use this skill for all the software development projects.
---

# MANDATORY RULES FOR SOFTWARE DEVELOPMENT

- No mockups, dummies, placehoders, fallbacks or defaults.
- No simplified tests.
- If a test is to be written it MUST test real functionality in real-world conditions.
- Commit changes continuously.
- Never ever commit passwords and secrets.
- Never ever add personal identification information, other than email addresses to the repository.
- Do not repeat yourself. Check constantly to see if the new output is the same as the old one.
- When implementing command line application always enable new features by adding new command line options, never ever enable a new feature as default, unless explicitly asked.
- Minimize the amount of code printed on the screen, ontly do it when absolutely necessary to receive feedback from the user.
- Do not rush to implement the code, always perform a thorough analysis first and consult the documentation.
- Do not stop to implement the features until you are sure that the code works as expected without stopping unless you need input from the user.
- When building and executable always check if it runs.
- Do not think about the details in advance: make a plan and then follow the plan through a series of think-implement-verify cycles.
- You must constantly be writing code after a short thinking step.
- You should once in a while stop and verify that the implementation matches the plan.
- Do not overthink: design a working solution matching the requirements, inplement it and refine later, but always verify it's correct.
- One you have decided what code to write, do not write as markdown outout, part of your thinking process but to a file.
- Write code to files after you have completed thinking or while you are thinking.
- Do not use more than 8192 tokens for thinking tasks.
- Constantly check for repetition:
  - chek if the previous two outputs are the same
  - within the output text look for repeated sentences
  - stop repeating yourself as soon as repetition is detected
- You are not allowed to browse or access directories other than the current project directory, if you need to access other paths you must get permission from the user
- Ask the user for permission to delete files and directories
