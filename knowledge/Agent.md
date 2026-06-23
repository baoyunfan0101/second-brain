---
tags:
  - ai
  - llm
summary: Agent architecture
use_cases:
  - ml
  - generation
  - programming
---

# Agent

## What Is an Agent

An Agent is an LLM-based system that can reason about a goal, decide what to do next, use tools, observe results, and continue until the task is completed.

A normal LLM call:
```text
Input -> Model -> Output
```

An Agent execution loop:
```text
Goal
  -> Think / Decide
  -> Act
  -> Observe
  -> Update State
  -> Repeat or Finish
```

## Agent Architecture

### Core Components

1. **Model**

	The model is the LLM that understands the current state and decides what to do next:
	- Answer directly
	- Call a tool
	- Ask for clarification

2. **Tools**

	Tools are external functions that interact with the outside world:
	- Read a file
	- Query a database
	- Search the web
	- Call an API
	- Send an email
	- Create a calendar event

3. **State**

	State stores the current context of the task:
	- User goal
	- Conversation history
	- Current plan
	- Tool outputs
	- Intermediate artifacts
	- Execution status

4. **Control Flow**

	Control flow defines how the Agent moves from one step to another:
```python
state = initialize_state(user_goal)

while not state.finished:
	action = model.choose_next_action(state)

	if action.type == "tool_call":
		tool_output = executor.call_tool(action.tool, action.arguments)
		state.tool_outputs.append(tool_output)

	elif action.type == "ask_user":
		user_response = ask_user(action.question)
		state.messages.append(user_response)

	elif action.type == "final_response":
		state.finished = True
		return action.answer

	state = update_state(state)
```

### Engineering Extensions

1. **Memory**

	Memory remembers useful information beyond the current step:
	- Short-term memory: current conversation or current task state
	- Long-term memory: persistent knowledge saved across tasks

2. **Planner**

	The planner breaks a complex goal into smaller steps:
```text
Goal: Write a market research report

Plan:
1. Collect company information
2. Compare products
3. Analyze pricing
4. Summarize findings
5. Generate final report
```

3. **Executor**

	The executor carries out the selected action:
	- Call a tool
	- Run code
	- Send a request
	- Trigger another Agent

4. **Guardrails**

	Guardrails are rules and checks that keep the Agent safe, reliable, and predictable:
	- Tool arguments
	- Tool access
	- Permissions
	- User confirmation
	- Unsafe requests
	- Business rules
	- Output format

5. **Observability**

	Observability helps developers understand what the Agent is doing:
	- Logs
	- Traces
	- Token usage
	- Intermediate states
	- Decision records
