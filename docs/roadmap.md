# Roadmap

What dsh-superagent builds next, and what it does not. This document uses only the concepts named in [001 Domain model](001-domain.md) through [008 Cookbook](008-cookbook.md); it introduces no new one.

The rule above every row is [000 Philosophy](000-philosophy.md:5): take as little human attention as possible, and give as much value as possible. A direction that cannot name the attention it saves is a preference, not a candidate.

## Current state

| Area | What exists today | Doc |
| --- | --- | --- |
| Sources | GitHub notifications, subscribed Issues and pull requests, CI, and optional receive-all switches; a Feishu bot for chat requests. Each Source holds its read position and its own identity | 001, 002 |
| Policy | Versioned assessment rules, each Source's allowed list, membership sync, bot list and admission table | 002 |
| Eval | Gate and assessment, one Eval per Thread, deadline and requeue, and a Decision of accept, stop, ignore or unavailable | 002 |
| Ticket | Mandate, Subscriptions, resume and stop, retry and recovery, and the pull-request completion rule | 001, 003 |
| Execution | One Executor Session and one directory per Ticket, a worker model, concurrency, a prepare command and blocked commands | 003 |
| Panel | Six tabs: Overview, Source, Trigger, Eval, Execution, Settings. Attention first, history after | 005 |
| Prompts | Team text, built-in parts, skills and versions, with the assembled prompt and its history | 004 |
| Deployment | A dedicated DeepSeek Harness profile, an agent GitHub account, machine limits and startup checks. The worker runs as the operator's OS user, with no process isolation | 006, 007 |

## Candidates

Every direction below is named in *Exploring Super-agent Design* and is not in this prototype. The section in the last column is where the article names it. One more direction, speculative execution, fails the gate; it sits in Rejected and carries no score.

| Id | Direction | Today | Article |
| --- | --- | --- | --- |
| C1 | Credentials the agent uses but never holds | The worker runs as the operator's OS user and can read the home directory | §8 |
| C2 | A person stops or redirects work from the surface it came from, without opening the Session | Panel and Session, no mid-turn redirect from GitHub | §4, §8 |
| C3 | A watchdog that spots a stalled or looping Ticket and repairs it | Failure recovery reacts to a `blocked` or `failed` Report; no progress check | §6 |
| C4 | Inline and Spill, with chain recall, for context beyond one window | The Session prompt is assembled per turn; no spill | §3 |
| C5 | Long-term memory across weeks or months | No memory beyond the Thread, Ticket and Session history | §8 |
| C6 | An async ReAct loop with a responsive foreground | Implemented in DeepSeek Harness, off by default | §4 |
| C7 | The agent loop cloud-side, the repository on the operator's machine, joined by a local daemon | Everything runs in one local profile | §5 |
| C8 | Extra Router filter layers, or a horizontal split, when one Router Session saturates | One main Session; no measured saturation | §8 |
| C9 | Worker-to-worker messages at the same layer | Work moves through Subscriptions and the Executor | §8 |
| C10 | Several isolated instances that collaborate, with context boundaries for a growing organization | One deployment serves one repository | §8 |

## Scores

Value and Reach run 1 to 5, higher is better. Cost runs 1 to 5, lower is better: it counts the new concepts and settings the direction adds. The Gate comes from [000 Philosophy](000-philosophy.md:11): a direction fails when it needs the agent to act with neither a request nor a written rule. A failed direction goes to Rejected; it is not scored low.

| Id | Direction | Value | Reach | Cost | Gate |
| --- | --- | --- | --- | --- | --- |
| C1 | Credentials the agent uses but never holds | 5 | 5 | 3 | pass |
| C2 | Stop or redirect from the domain surface | 4 | 4 | 3 | pass |
| C3 | Watchdog for stalled or looping work | 4 | 4 | 2 | pass |
| C4 | Inline and Spill, chain recall | 4 | 4 | 4 | pass |
| C5 | Long-term memory | 4 | 4 | 5 | pass |
| C6 | Async ReAct loop | 5 | 5 | 1 | pass |
| C7 | Cloud loop, local daemon | 3 | 4 | 5 | pass |
| C8 | Router scaling | 2 | 3 | 3 | pass |
| C9 | Worker-to-worker messages | 2 | 2 | 3 | pass |
| C10 | Multi-instance collaboration | 2 | 3 | 5 | pass |

## Order

### Now

Three items. Each one names the document that will describe it; the document lands with the change.

1. **C1, credentials the agent uses but never holds.** Value 5, Reach 5, Cost 3. This is the one item whose failure costs a repository rather than a review. It extends [006 Deployment](006-deployment.md), section "Permissions and isolation", which already records the gap. It adds no setting: the account wrapper stays the one place credentials are chosen.
2. **C2, stop or redirect from the domain surface.** Value 4, Reach 4, Cost 3. Today a person must find the Session to intervene. It extends [005 Panel](005-panel.md), the "does anything need me now" question, and adds no new tab.
3. **C3, a watchdog for stalled or looping work.** Value 4, Reach 4, Cost 2. A Ticket that stops making progress costs attention without asking for any. It extends [003 Execution](003-execution.md), section "Failure and recovery", and acts only inside a Ticket someone already authorized.

### Next

- **C4, Inline and Spill with chain recall.** High reach, but the cost is a new context mechanism; score it again after C1 to C3 land. Document: `003-execution.md`.
- **C5, long-term memory.** The article calls it unsolved. Start with the rule that decides what is remembered, not with storage. Document: `003-execution.md`.
- **C6, async ReAct loop.** Cheap here, because DeepSeek Harness already implements it; the work is turning it on and proving the Executor keeps its turn semantics. Owner is upstream until it is on by default. Document: `003-execution.md`.

### Later

- **C7, cloud loop with a local daemon.** It changes where the agent runs, so it follows the deployment isolation question in [007 Architecture](007-architecture.md:614) rather than leading it.

## Rejected

| Direction | Rule or reason |
| --- | --- |
| C11, speculative execution | [000 Philosophy](000-philosophy.md:11): it works with no request and no written rule. It may return only as a written rule with a record-only mode, and then it is a new candidate, not this one |
| C8, Router scaling | [000 Philosophy](000-philosophy.md:19): extra filter layers and a split are new concepts, and one repository shows no saturation to pay for them |
| C9, worker-to-worker messages | [001 Domain model](001-domain.md:26): Subscriptions route work and Correlation only adds context. A second path would need a measured case that neither covers |
| C10, multi-instance collaboration | The prototype serves one repository. A context boundary between instances adds a concept for a deployment that does not exist yet |

## Decision rule

**How a direction gets in.** A pull request adds one row to Candidates with the four scores and one line saying which attention it saves. A maintainer merges it or rejects it. A direction that fails the gate goes to Rejected, never to Scores.

**How an item gets cut.** An item is cut when it adds a concept not tied to a Source or a prompt ([000 Philosophy](000-philosophy.md:19)), when it adds a setting a default would cover ([000 Philosophy](000-philosophy.md:20)), or when its document is still unwritten at the next review. "Now" holds three items; a fourth enters only by displacing one.

## Done when

A contributor reads this in five minutes and can name the next three things we build, the three we will not build, and why.

- [x] Every candidate has a score on all four factors.
- [x] Every "Now" item names the doc that will describe it.
- [x] No item adds a setting where a default would work.
- [x] The rejected table cites a rule or a reason, not a preference.
- [x] "Now" has at most 3 items.
- [x] A reader needs no new concept to read the roadmap.
- [x] The roadmap is linked from `README.md`.
