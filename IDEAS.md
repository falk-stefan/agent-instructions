> :warning: Agents: Ignore this file as it only contains ideas for future improvements.

# Ideas

## Separation of Concerns

Types of instructions, guidelines and directives that can clearly be separated:

### Behavior per Task

> **Hypothesis:** Keeping agents focused should not only speed them up but also improve the quality of the work.

Giving the agent information about the task would allow them to look up the relevant information.

The goal is two-fold: Guardrail the overall behavior of the agent and also make sure it is using the right tools
and instructions for their main goal.

```
[agent-hook]: 
  If not clear from the first prompt, ask the user for the task so you can fetch the correct instructions
  for this proejct or codebase. Use Skill(available-tasks) for a list.
[user]: We are going to work on epic #123. Is everything clear?
[agent]: *checks status*. Still open are #42 and #1337. What is going to be my task?
[user]: You are going to implement #42.
[agent]: *infers task writing code* Skill(use-git), Skill(write-code), Skill(test-code), Skill(use-worktrees)
```

#### Example Tasks

| Task                              | Skills and Behavior                           |
|-----------------------------------|-----------------------------------------------|
| Writing Code                      | use-git, use-worktrees, write-code, test-code | 
| Reviewing Code                    | review-code, test-code                        | 
| Wrting Public Documentation       | write-public-docs                             |    
| Wrting Internal Documentation     | write-internal-docs                           |    
| Requirements Engineering          | manage-issues, write-issues, do-research      |    
| Planning                          | manage-issues, write-issues                   |    
| Research                          | do-research                                   |    
| Search                            | do-search                                     |    
| Manage and orchestrate sub-agents | manage-agents                                 |    

### Behavior on Demand

Instead of loading individual skills, one MCP server could function as a smart interface. Ideally, this works 
across repositories and code bases as a central hub for instructions.

**Pros:** 

- One place to manage it all. 
- May introduce guardrails on how long instructions are allowed to get.

**Cons:** 

- May contradict with what individual repositories instruct agents to do.

```
[agent-hook]: 
  If not clear from the first prompt, ask the user for the task so you can fetch the correct instructions
  for this proejct or codebase. 
  Use the Memaro MCP to learn. Here are some examples:
   - mcp__memaro__instructions({ query: "how to write code?" })
   - mcp__memaro__instructions({ query: "how to use git?" })
   - mcp__memaro__instructions({ query: "issue management" })
   - mcp__memaro__instructions({ query: "research" })
   - mcp__memaro__instructions({ query: "public documentation" })
[user]: We are going to work on epic #123. Is everything clear?
[agent]: *checks status*. Still open are #42 and #1337. What is going to be my task?
[user]: You are going to implement #42.
[agent]: mcp__memaro__instructions({ query: "how to write code?" }) *reliably returns instructions for "Writing Code"*
```