# Agentic Engineering Patterns: Complete Guide

## Overview

Agentic engineering is the practice of developing software with the assistance of coding agents. Coding agents are tools that can both write and execute code, with popular examples including Claude Code, OpenAI Codex, and Gemini CLI.

This guide is a comprehensive resource on best practices for working with AI coding agents, compiled by Simon Willison.

---

## Part 1: Core Concepts

### What is an Agent?

Agents run tools in a loop to achieve a goal. The agent is software that calls an LLM with your prompt and passes it a set of tool definitions, then calls any tools that the LLM requests and feeds the results back into the LLM.

For coding agents, those tools include one that can execute code, and code execution is the defining capability that makes agentic engineering possible.

### Agentic Engineering vs. Vibe Coding

It's important to understand the distinction:

- **Vibe Coding**: Prompting LLMs to write code while disregarding code quality (unreviewed, prototype-quality output)
- **Agentic Engineering**: Professional software engineers using coding agents to amplify their expertise while maintaining code quality standards

Agentic engineering is concerned with figuring out what code to write, providing agents with the right tools, specifying problems at the right level of detail, and verifying and iterating on results until they address problems robustly.

### The Core Challenge: Code is Now Cheap

The biggest challenge in adopting agentic engineering practices is getting comfortable with the consequences of the fact that writing code is cheap now, as code has always been expensive and many engineering habits are built around this core constraint.

#### What Changed?

**Traditional Development**: Producing hundreds of lines of clean, tested code takes a full day or more
**With Coding Agents**: Writing initial code now costs almost nothing in terms of developer time

This dramatically shifts the cost-benefit analysis of development decisions:
- Should I refactor this? Previously a multi-hour investment, now seconds
- Should I add documentation? Now feasible to do comprehensively
- Should I write tests for edge cases? Cost has dropped substantially
- Should I build a debug interface? Previously prohibitive, now worth considering

#### Good Code Still Has a Cost

While delivering new code has dropped in price to almost free, delivering good code remains significantly more expensive than that.

Good code requires:
- Working code that does what it's meant to do, without bugs
- Proof that the code works (tests, verification)
- Solving the right problem
- Graceful error handling with informative messages
- Simplicity and minimal design
- Tests that serve as regression suites
- Up-to-date documentation
- Future-friendly design (YAGNI principle)
- Quality attributes (accessibility, security, reliability, scalability, maintainability, observability, usability)

#### New Habits Needed

The best current approach is to second-guess instincts that say "don't build that, it's not worth the time" by firing off a prompt in an asynchronous agent session where the worst outcome is checking back ten minutes later.

---

## Part 2: How Coding Agents Work Under the Hood

### The Architecture

A coding agent is a harness that extends an LLM with additional capabilities through invisible prompts and callable tools.

### Large Language Models (LLMs)

An LLM is a machine learning model that can complete a sentence of text. Give the model a phrase like "the cat sat on the " and it will suggest "mat" as the next word.

**Key Points About LLMs**:
- They work with tokens (integer representations of text sequences), not words directly
- Larger models trained on more data can complete increasingly complex sequences
- Many modern models are multimodal—they can process images, not just text
- Vision LLMs accept images as input, converting them to token sequences for processing

**Cost Model**: LLM providers charge based on the number of tokens processed, and models have limits on how many tokens they can consider at once.

### Chat-Templated Prompts

The first LLMs worked as completion engines, but models mostly switched to using chat templated prompts, which represent communication as a simulated conversation.

Example format:
```
user: write a python function to download a file from a URL
assistant:
```

**Important**: LLMs are stateless: every time they execute a prompt they start from the same blank slate, so to maintain conversation simulation, the entire existing conversation must be replayed every time the user enters a new prompt.

### Token Caching Optimization

Model providers offer cheaper rates for cached input tokens—common token prefixes processed within a short time period can be charged at a lower rate because the infrastructure can cache and reuse expensive calculations.

Coding agents are designed with this optimization in mind—they avoid modifying earlier conversation content to ensure the cache is used as efficiently as possible.

### Calling Tools

A tool is a function that the agent harness makes available to the LLM.

**How it works**:
1. The system prompt instructs the LLM how to request tools
2. The LLM generates a tool call in its response
3. The agent harness extracts the tool call and executes it
4. Results are fed back into the LLM as part of the conversation

For coding agents, critical tools include:
- Bash tool for executing terminal commands
- Python tool for running Python code
- File system access tools
- Often a dozen or more specialized tools

### The System Prompt

Coding agents usually start every conversation with a system prompt, which is not shown to the user but provides instructions telling the model how it should behave.

These system prompts can be hundreds of lines long. They define available tools, expected behavior patterns, and constraints.

### Reasoning (Extended Thinking)

One of the big advances in 2025 was the introduction of reasoning to frontier model families, when a model spends additional time generating text that talks through the problem and its potential solutions before presenting a reply to the user.

**Benefits for Coding**:
- Allows models to spend more time working on complex problems
- Particularly useful for debugging code
- Enables models to navigate complex code paths and trace issues
- Most coding agents include options to dial up or down reasoning effort

### The Loop

The fundamental mechanics of a coding agent consist of: LLM + system prompt + tools in a loop, achievable with just a few dozen lines of code on top of an LLM API, though a good tool loop requires considerably more work.

---

## Part 3: Practical Patterns and Best Practices

### Pattern 1: Hoard Things You Know How to Do

Maintain a collection of patterns, code snippets, and solutions you've developed. When agents can recombine these effectively, they become amplifiers of your expertise rather than replacements for it.

**Why This Matters**: Used effectively, coding agents can help you be much more ambitious with the projects you take on and help produce more, better quality code that solves more impactful problems.

### Pattern 2: Code Quality Over Speed

Focus on using agents to improve code quality in dimensions that were previously too time-consuming:
- Adding comprehensive tests
- Improving error handling
- Better documentation
- Code refactoring for clarity
- Building debugging interfaces

### Pattern 3: Work with Git

Agents should leverage version control:
- Make atomic, reversible changes
- Use meaningful commit messages
- Enable easy rollback if an approach doesn't work
- Allow parallel exploration of different approaches

### Pattern 4: Use Subagents

Divide work among specialized agents:
- **Explore subagents**: For discovery and initial investigation
- **Parallel subagents**: Multiple agents working on different aspects simultaneously
- **Specialist subagents**: Agents optimized for specific domains or tasks

### Pattern 5: Test-First Development (Red/Green TDD)

Red/green TDD describes how test-first development helps agents write more succinct and reliable code with minimal extra prompting.

The pattern:
1. Write tests that define expected behavior (red phase)
2. Have agents implement code to pass those tests (green phase)
3. Iterate to improve code quality and coverage

### Pattern 6: Manual Testing with Agents

Agents can perform manual testing by:
- Using browser automation for web UIs
- Taking notes during testing (using tools like Showboat)
- Verifying user-facing behavior
- Identifying edge cases

### Pattern 7: Code Understanding

#### Linear Walkthroughs
Have agents walk through code execution step-by-step, explaining what happens at each point. Tools like Showboat and Present can capture and display this.

#### Interactive Explanations
Create interactive visualizations to help understand code behavior, including concepts like word clouds or system diagrams.

---

## Part 4: Working Habits and Anti-patterns

### Anti-pattern: Unreviewed Code to Collaborators

Never inflict unreviewed AI-generated code on team members. Always:
- Review all code before sharing with collaborators
- Understand what the agent generated
- Verify correctness and code quality
- Document any special implementation decisions

### New Workflows

This represents a new era of coding agent development where the defining feature is that they can both generate and execute code, allowing them to test that code and iterate on it independently of turn-by-turn guidance from their human supervisor.

The engineering model shifts:
- From "write all the code manually" to "guide agents toward better code"
- From "code completion" to "code generation and verification"
- From linear development to parallel, exploratory approaches

---

## Part 5: The Broader Picture

### What Remains Human?

Writing code has never been the sole activity of a software engineer. The craft has always been figuring out what code to write. Any given software problem has dozens of potential solutions, each with their own tradeoffs. The job is to navigate those options and find the ones that are the best fit for unique circumstances and requirements.

The human role in agentic engineering:
1. **Problem Definition**: Clearly specifying what needs to be built
2. **Tool Configuration**: Providing agents with the right tools and context
3. **Direction Setting**: Guiding agents toward appropriate solutions
4. **Verification**: Testing and validating results
5. **Decision Making**: Choosing between tradeoffs when multiple valid approaches exist

### The State of the Field

I'm using Agentic Engineering to refer to building software using coding agents where the defining feature is that they can both generate and execute code, allowing them to test that code and iterate on it independently.

This is distinct from "vibe coding" in that it represents professional software engineering practices applied at scale with powerful new tools.

---

## Key Tools Mentioned

- **Claude Code**: Primary coding agent from Anthropic
- **OpenAI Codex**: Alternative coding agent system
- **Gemini CLI**: Google's coding agent interface
- **Showboat**: Tool for capturing agent actions during testing
- **Present**: Tool for interactive code walkthroughs
- **Git**: Version control system (essential for agentic workflows)

---

## Resources

**Learn More**:
- Original announcement: https://simonwillison.net/2026/Feb/23/agentic-engineering-patterns/
- Guide home: https://simonwillison.net/guides/agentic-engineering-patterns/
- Simon Willison's broader AI content: 345+ posts tagged with ai-assisted-programming

**Notable Implementation Detail**: Simon Willison's blog itself was substantially built using Claude Opus 4.6 running in Claude Code for web accessed via iPhone, particularly the Guide, Chapter, and ChapterChange models and associated Django views.

---

## Conclusion

Agentic engineering represents a fundamental shift in how professional developers interact with AI-assisted code generation. Rather than treating agents as code-writing shortcuts, they're positioned as amplifiers of existing expertise that enable developers to tackle more ambitious problems while maintaining or improving code quality.

The field is still young and evolving rapidly, but the core patterns—proper problem specification, tool configuration, quality verification, and iterative refinement—emerge as essential practices for getting the best results from these powerful new tools.
