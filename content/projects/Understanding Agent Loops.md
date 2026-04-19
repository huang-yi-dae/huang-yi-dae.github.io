---
title: "Understanding Agent Loops"
date: 2026-04-10T10:00:00+08:00
draft: true
share: true
tags:
  - agent
  - llm
  - tool-use
  - python
  - ai
summary: "A cleaned-up blog version of my notes on how a minimal agent loop works, keeping the original thought process while reorganizing it into a clearer structure."
---

# Understanding Agent Loops

When I first looked at a minimal `agent_loop` implementation, what I wanted was not a polished framework explanation.
I wanted to understand what the file was _actually doing_.

This post is my cleaned-up understanding of that file.
I keep the original way I reasoned through it, but I reorganize it into a more standard blog structure so the whole picture is easier to follow.

In one sentence, the core idea is this:

> An agent loop is just a repeated cycle of **model -> tool call -> tool result -> model** until the model stops asking for tools.

---

## 1. What this file is really trying to do

At the most basic level, the file is trying to build a very small coding agent.

Its job is:

- accept user input from the terminal
- append that input into `history`
- send `system + messages + tools` to the model
- let the model request a tool when needed
- run that tool locally
- send the result back to the model
- continue until the model finishes

That is the first important simplification for me.
An “agent” here is not some mysterious thing.
It is just a loop with memory, tool access, and a stopping condition.

The `history` variable is the center of this process.
It stores the session content, and that content is not only plain text.
For the basic case, it mainly contains three kinds of things:

- `text`
- `tool_call`
- `tool_result`

In `text`, there are system prompts, user inputs, and assistant responses.
In `tool_call`, the model asks to use a tool.
In `tool_result`, the local runtime sends back the execution result.
And the connection between a tool call and its result is made by a unique ID.

That small detail already reveals an important idea:
this is not just chat history.
It is **structured interaction history**.

---

## 2. The local runtime and the model do different jobs

One of the most useful clarifications for me was this:

> the LLM does not directly run commands.

It first requests a tool.
Then the Python code receives that request, executes the command locally, and returns the output.

So in this architecture:

- the **model** is the decision maker
- the **local Python runtime** is the executor

This separation matters a lot.
If you do not see that boundary clearly, the whole file feels magical.
Once you do see it, the file becomes much easier to understand.

The tool-related part makes this explicit.
For now, there is only one real tool: Bash.
So you define a helper like `run_bash()` or `bash_execute()`.
It accepts the tool request, runs the command, and packages the result back into the protocol.

That means the real flow is not:

`model decides and directly acts`

It is:

`model decides -> runtime executes -> runtime reports back`

This is the core execution pattern behind many more advanced agent systems too.
They may look much more complicated, but a lot of them are still doing this same thing underneath.

---

## 3. Before the loop runs: configuration also matters

Before the main loop really starts, the file has to solve a few setup problems.

First, it loads environment variables such as:

- `BASE_URL`
- `API_KEY`
- model-related config

This seems boring at first, but it is actually part of correctness.
A lot of runtime bugs do not come from the loop logic itself.
They come from configuration confusion.

One subtle point I noticed is this:
if `ANTHROPIC_BASE_URL` exists, the file may be using a custom endpoint instead of the default Anthropic endpoint.
So the code removes `ANTHROPIC_AUTH_TOKEN` to avoid environment conflicts.

That is easy to miss, but it teaches a practical lesson:

> application correctness is not only about control flow, but also about clean runtime configuration.

There is also a `readline` setup before the main loop.
This part is not the “intelligence” of the agent.
But it improves terminal input behavior, especially for UTF-8, Chinese input, and special keys.

That also changed how I think about agent systems.
A usable CLI agent is not only about the model loop.
If the terminal experience is bad, the whole thing feels broken even when the logic is right.

---

## 4. `run_bash()` is the execution boundary

When I looked at the file, one function stood out immediately: `run_bash()`.

This is the bridge between tool requests and real local execution.
It receives a command, runs it with `subprocess.run`, and returns the output as text.

That sounds simple, but conceptually it is extremely important.
This function is where reasoning crosses into action.

The file also adds several basic controls here:

- block some obviously dangerous commands
- capture `stdout` and `stderr`
- return plain text
- set a timeout
- truncate overly long output

For example, the code blocks commands like:

- `rm -rf /`
- `sudo`
- `shutdown`
- `reboot`

This does **not** mean the system is fully safe.
The filter is still limited.
But it shows the right design instinct:

> when you give tools to an LLM, you must think not only about capability, but also about boundary.

I also noticed several practical implementation details:

- `cwd` is set to the current project folder, so commands run in the right place
- `capture_output=True` collects stdout and stderr
- `text=True` converts output into plain strings
- `timeout=120` prevents the command from hanging forever

And after all that, the output is converted into manageable text and appended back into history.

That last step matters a lot.
Tool execution is only useful if the result can be fed back into the conversation in a format the model can continue to use.

---

## 5. Why there are two loops instead of one

This was one of the most important realizations for me.
There are **two loops**, and they are doing different jobs.

### Outer loop

The outer loop lives in `__main__`.
Its job is to:

- read user input
- append the input into `history`
- handle exit conditions like `q`, `exit`, or empty input
- call `agent_loop(history)`
- print the final assistant text

So the outer loop is basically the **CLI session loop**.

### Inner loop

The inner loop lives inside `agent_loop(messages)`.
Its job is to handle a **single user request** that may require multiple tool calls.

This is the key distinction:

- one user input may create many model-tool rounds
- so one user turn is not always one model call

That is why this is called an _agent loop_ rather than a single completion call.
A single request may go like this:

1. send messages to the model
2. the model asks to use a tool
3. execute the tool
4. send tool result back
5. the model asks for another tool
6. execute again
7. continue until no more tools are needed

This explains the architecture much better than thinking in terms of simple prompt-response chat.

---

## 6. Why `messages` is mutated in place

Another subtle but useful point in the file is the note that `messages` is mutable.

That means `agent_loop(messages)` does not mainly compute a return value.
Instead, it keeps appending new conversation blocks into the same shared list.

So instead of writing something like:

```python
messages = agent_loop(messages)
```

the code just keeps mutating the existing `messages` list.

This matters because it changes how you mentally model the function.
It is not a clean pure function taking input and producing output.
It is more like a process that continuously updates shared conversation state.

That is why you keep seeing `messages.append(...)` again and again.

Once I understood this, the whole file felt less confusing.
The main object is not a final return value.
The main object is the evolving conversation state itself.

---

## 7. System prompt and tool schema: what the model is actually given

Inside the inner loop, the file defines two important things:

- `SYSTEM`
- `TOOLS`

`SYSTEM` tells the model where it is working and what style to follow.
For example, it may say that the agent is in the current working directory, should use Bash to solve tasks, and should act instead of over-explaining.

`TOOLS` describes what tools the model is allowed to use.
In this file, there is only one tool for now: Bash.
And Bash has an `input_schema`.

That schema matters because the model is not supposed to produce random text like:

> maybe run ls and then grep

Instead, it must produce structured tool-use data that matches the schema.
That is why later the program can safely read something like:

```python
block.input["command"]
```

So one big lesson here is:

> tool use is not free-form natural language; it is structured protocol.

That is also why agent frameworks often feel more complicated than plain chat APIs.
They are managing structured blocks, schemas, and control flow, not just text.

---

## 8. The response is structured, not just plain text

After sending `model + system + messages + tools + max_tokens` into the API call, the response is not just one plain string.

It may contain different block types, especially:

- `text`
- `tool_use`

This is why the program appends `response.content` directly into history instead of flattening everything into one response string.

That point helped me a lot.
The conversation state is not storing only natural language.
It is storing structured content blocks that the runtime can interpret.

This is one of the clearest signs that an agent loop is different from ordinary chat.
The runtime is not just displaying text.
It is also checking whether the model is trying to act.

---

## 9. `stop_reason` is the core control signal

One of the main control points in the file is `stop_reason`.

The logic is basically:

- if `response.stop_reason != "tool_use"`, the round is done
- if `response.stop_reason == "tool_use"`, keep going

This is the signal that tells the runtime whether the model has finished its turn or is waiting for a tool result before it can continue.

So the bottom logic of the inner loop is really:

- send current messages to the model
- get response
- check whether a tool is requested
- if yes, execute the tool
- send the tool result back
- ask the model again

That is the real mechanical skeleton of the file.

Without this control signal, the runtime would not know whether to stop or continue.
So `stop_reason` is not a minor API field.
It is one of the main switches controlling the whole loop.

---

## 10. Why the code iterates over `response.content`

The file creates a `results = []` list before processing tool outputs.
At first glance this may seem unnecessary, but it solves an important protocol issue.

One response can contain more than one block.
And among those blocks, there may be more than one `tool_use` block.

So the code should not assume:

- one response = one tool call

Instead, it iterates through `response.content` and handles each block separately.

This teaches another useful lesson:

> in agent protocols, you should think in terms of block sequences, not single strings.

That also explains why the tool result handling is list-based.
The runtime may need to send back multiple tool results together.

---

## 11. How `tool_use` becomes `tool_result`

For every block where `type == "tool_use"`, the program does roughly this:

1. print the command as a simple log
2. run `run_bash(block.input["command"])`
3. wrap the output into a `tool_result` object

This is the exact place where the reasoning side and the execution side connect.

The model asks.
The runtime acts.
Then the runtime translates the action result back into the conversation protocol.

This is the moment where an LLM-based system stops being only a language generator and becomes an actual agent loop.

---

## 12. Why `tool_use_id` is so important

The most important field inside `tool_result` is `tool_use_id`.
It is bound to the `id` of the corresponding `tool_use` block.

Why is this necessary?
Because if one model response contains multiple tool calls, then later the model needs to know which execution result belongs to which request.

So the ID is the binding mechanism.
Without it, the relation between request and result becomes ambiguous.

This is one of the most important concepts in structured tool protocols.
It is a very small field, but it holds the whole mapping together.

---

## 13. Why `tool_result` is appended as a user message

This part looks strange at first.
After collecting tool results, the code appends something like:

```python
{
    "role": "user",
    "content": results
}
```

At first I also thought:
why is `tool_result` added as `user` instead of `assistant`?

But the protocol logic explains it.
From the model's perspective, the assistant already made a request.
Now the runtime is feeding back the execution result as the next input to continue the turn.

So semantically, it is not a fresh assistant answer.
It is runtime feedback entering the next round.

Once I understood this, the message history structure became much easier to read.

---

## 14. The full picture in one chain

If I compress the whole file into one chain, it becomes this:

```text
user input
-> append to history
-> call model
-> inspect response blocks
-> if tool_use exists, run local command
-> package tool_result
-> append tool_result back into history
-> call model again
-> print final text
```

That is the whole skeleton.

The outer loop handles user interaction.
The inner loop handles one request until tool use is complete.
The SDK talks to the model service.
`run_bash()` talks to the local machine.
`history` carries text, tool calls, and tool results.
`tool_use_id` binds request and result together.

Once this chain is clear, many more advanced agent systems become easier to understand.
They may add more tools, more abstractions, more planning, and more safety layers.
But a lot of them still rely on this same core loop.

---

## 15. What I noticed while studying it

While studying the file, I also noticed a few issues and caveats.
They do not remove the learning value, but they are worth keeping in mind.

- some indentation and wording are inconsistent
- some comments are grammatically rough
- the dangerous-command filter is basic and easy to bypass
- `shell=True` increases execution risk
- the system prompt says “Use bash” even when the real runtime environment may differ

So I would not describe this file as production-grade.
But that is actually part of why it is useful for learning.
It is small enough that the architecture is visible.

---

## Conclusion

What this file taught me is that an agent loop is not mysterious.
It is just a structured runtime pattern.

The key ideas are:

- preserve conversation state in `history`
- define tools through schemas
- let the model request tools instead of acting directly
- execute tools through a controlled boundary like `run_bash()`
- send results back as structured protocol data
- continue until the model no longer requests tools

If I had to summarize the biggest insight in one sentence, it would be this:

> A minimal agent is not “AI magic.” It is a loop that coordinates memory, tool requests, local execution, and structured feedback.

That simple picture makes the rest of the ecosystem much easier to understand.
