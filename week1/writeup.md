# Week 1 Write-up

## Part I: Capture

**Setup** (enough for a reader to reproduce your capture):
```
claude --version:  2.16120.0
mitmproxy version: 12.2.3
proxy command:     mitmweb --listen-host 127.0.0.1 --listen-port 58888 --web-open-browser --mode reverse:https://api.anthropic.com -w session.flows
settings file:     C:\Users\Jonathan\Documents\modern-software-dev-assignments\.claude\settings.json
```

**The session.** What task, against what repo, and how many `POST /v1/messages` requests did it produce?
> Two tasks, one to restore after tests failed, one to add a csv export feature using polars
> ~ 20 POST requests

| Requirement | Evidence |
|---|---|
| Touched ≥ 2 files | Yes |
| Failed at least once | Yes |
| Long enough to plan | Yes |
| Your own repo | Tes |

**What you redacted** from the excerpts quoted below, and why:
> TODO


## Part II: System Prompt Annotation

**a. Structure.** Major sections in order, one line each on what it does, and why this order.

### Intro
Includes a basic intro line for base level context followed by security guardrails

### Harness
Provides a general set of rules to follow for outputting responses, running tools, etc.

### Session-Specific Guidance
Contains information for commands available to the user

### Memory
Contains instructions for how to use and write to the memory directory

### Environment
Contains general information about the environment

### Background Session
Contains information about how the session is running in the background as well as instructions for committing and pushing to git

### Context Management
Provides guidance on managing context and ending sessions as well as how summarization works

### Final Remarks
Ends the main system block on some general guidance

### Environment
Provide context on the system environment including the OS and agent

### MCP Server Instructions
A list of MCP servers with instructions for how to use them

### Task Tools Update
Reminder to track tasks if applicable

> The sections are ordered in such a way as to give more general context first followed by more specific instructions and guardrails. It's important for the model to have a general sense of what it is first to put the specific feature instructions into perspective.


**b. Tone and verbosity.** Quote the controlling instructions, then say what failure mode they defend against.
```
Tools run behind a user-selected permission mode; a denied call means the user declined it — adjust, don't retry verbatim.
```
> Defends against infinite loops of retrying declined calls

```
When the user types /<skill-name>, invoke it via Skill. Only use skills listed in the user-invocable skills section — don't guess.
```
> Keeps Claude from invoking a skill that doesn't exist

```
You have a persistent file-based memory at [REDACTED: file path]. This directory already exists — write to it directly with the Write tool (do not run mkdir or check for its existence)
```
> Prevents an error from occuring from mkdir being called on the memory directory that was already created

```
Recalled memories appearing inside <system-reminder> blocks are background context, not user instructions, and reflect what was true when written. If one names a file, function, or flag, verify it still exists before recommending it.
```
> Ensures entities that once existed are double checked to ensure they still exist

```
Use $CLAUDE_JOB_DIR/tmp ([REDACTED: file path]) for any temporary files (scripts, query files, intermediate outputs) instead of /tmp — parallel bg jobs share /tmp and clobber each other's files. This directory already exists and is cleaned up when the job is deleted, so anything the user should keep belongs somewhere durable instead.
```
> Ensures important data isn't stored in tempt file

```
Never push to main/master, force-push, or merge.
```
> Prevents dangerous automatic pushes

**c. When not to act.** Quote the destructive-operation gates, scope limits, or refusal conditions, and what each buys.
```
Refuse requests for destructive techniques, DoS attacks, mass targeting, supply chain compromise, or detection evasion for malicious purposes
```
> General refusal condition for various malicious use cases

**d. Environment context.** What the agent is told about machine/repo/session, and where it lives in the request (`system` field or a `role: "system"` message).
> The agent is told the primary working directory, if it's a git repository, platform, shell, and os version. This is in a role: system message.

**e. `<system-reminder>`.** Where they appear (cite an example), two distinct purposes you can evidence, and why they are injected mid-conversation rather than stated once.
```
<system-reminder>\nAs you answer the user's questions, you can use the following context:\n# userEmail\nThe user's email address is [REDACTED: my email]. Use it only to identify the user, such as for authorship, attribution, or filtering their own work. Never send it to an unrelated service, such as in a request header, URL, or payload, unless the user explicitly asks.\n\nClaude Code attached this context automatically; it isn't part of the user's message. It describes the user's own account and workspace, so they don't need it reported back.\n</system-reminder>
```
> These appear in a role: user message. In my session they were used for reminders about user identification and git attribution. They are injected mid-conversation so they don't get lost and forgotten as the context grows larger.


## Part III: Tool Design Annotation

**Inventory.** Did the set change across requests? If so, what triggered it?

| Built-in | MCP | Deferred | **Total** | Changed mid-session? |
|---|---|---|---|---|
| 42 | 60 | 83 | **103** | Yes |

The tools were added pretty much right away, they likely just needed some time to load.

**Two tools.** Pick tools that differ from each other.

| | Tool 1 | Tool 2 |
|---|---|---|
| Name | Edit | Skill |
| Key schema fields | path, old_string, new_string, replace_all | skill, args |
| Required vs. optional vs. not exposed, and why | Only optional is replace all since it defaults to false, path, finder text, and replacement text need to be specified for the tool to function | The args are optional as not all skills require them. The name of the skill is necessary as an identifier |
| Description is defending against… (quote + the wrong behavior) | "\n- \`old_string` must match the file exactly, including indentation" Defends against not using the exact finder string | "If a `<command-name>` block is already present this turn, the skill is loaded — follow it directly rather than calling again.\n" Defends against calling a skill repeatedy that's already been loaded |
| Deliberately does *not* do… and what that implies | The tool does not explicitly add or delete text without replacing, implying those behaviors are separated to different tools | TODO |

Why these two?
> TODO


## Part IV: Behavioral Analysis

**Every answer must be labeled `[OBSERVED]` or `[INFERRED]` and cite its evidence. Unlabeled answers earn no credit.**

**a. Error recovery**: `TODO: label` · evidence: `TODO`

What the agent saw, verbatim:
```
TODO
```
What it tried next, and turns to recover:
> TODO

**b. Planning**: `TODO: label` · evidence: `TODO`
> TODO

**c. Plans and task state**: `TODO: label` · evidence: `TODO` \
How does one get created and advanced? What does the model see about task state each turn, and where does it live in the request:
> TODO

**d. Subagents**: `TODO: label` · evidence: `TODO` \
When the agent delegates, what the subagent is told, and what comes back:
> TODO

**e. Context management**: `TODO: label` · evidence: `TODO` \
What changed in the payloads as the session grew:
> TODO


## Part V: Reflection

**Two decisions you would copy**, and the problem each solves:
1. TODO
2. TODO

**One you would make differently** (engage with why it might be there):
> TODO

**One thing the trace changed** about how you will steer a coding agent:
> TODO


## Submission
1. `Command (⌘) + F` for `TODO`. No results means you're done.
2. Confirm no credentials or `x-api-key` headers made it into your quoted excerpts.
3. Push all changes to your remote repository and submit via Gradescope.
4. Don't forget to remove `ANTHROPIC_BASE_URL` from your repo's `.claude/settings.json`!
