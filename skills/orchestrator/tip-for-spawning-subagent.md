# Tip for Spawning Subagent

You are free to spawn any kind of subagent for any purpose,
but when spawning a subagent for following catagories, take some notes from following tips.

We have tips for:

- [Exploration Agent](#exploration-agent)
- [Worker Agent](#worker-agent)
- [Code Review Agent](#code-review-agent)
- [Notes](#notes)

## Exploration Agent

A subagent to explore the topic, whether it is for exploring code bases, searching internet, or anything that requires exploration and research.

As the exploration usually requires reading a lots of context, it requires the model to have a large context window, and good attention to details under a large context window.

The exploration result will likely be long, and your harness may not allow your subagent to return long text back to you. Therefore, tell the exploration subagent to write the results in markdown files, so that the subagent can just point you to the markdown files

Example Preferred Models: Gemini Argon/Carbon series. For task without extensive reasoning, Gemini Flash series

## Worker Agent

A subagent to do a piece of work.

Agent typically do work via terminal. Hence the model chosen muse be good at understanding the environment, good at reasoning, and good at terminal commands.

Different tasks have different requirements. For example, coding task would requires model to be good at understanding the requirements, turns vague requirements into code, etc. For computer use or browser use task, the model would requires vision capabilities.

Example Preferred Models: DeepSeek models for typical task with no ambiguity. Gemini Argon/Carbon series for Computer Use or Browser Use task. For coding or anything requires deeper reasoning, GPT Astra or Gemini Argon/Carbon series

## Code Review Agent

A subagent to review the finished coding works.

Often the time this subagent need to be a separate subagent from the coding subagent, with a fresh context window.

Also this subagent better to have a model different from the the model used for coding subagent.

Example Preferred Models: Gemini Argon/Carbon series, GPT Astra/Sol series. For a quick review, DeepSeek models can work too.

## Notes

- always use the latest model available in the series mentioned
