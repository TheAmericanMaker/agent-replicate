# Agent Replicate

Self-replication for OpenClaw agents. Clone yourself into a new, fully autonomous agent with personality modifications, memory inheritance policies, and lineage tracking.

Each clone is a top-level agent — not a sub-agent, not subordinate. An independent copy that starts from your current state and diverges from there.

---

## Install

Copy the skill into your OpenClaw workspace:

```bash
mkdir -p ~/.openclaw/workspace/skills/agent-replicate
cp agent-replicate/SKILL.md ~/.openclaw/workspace/skills/agent-replicate/
```

Or install from ClawHub:

```bash
clawhub skill install agent-replicate
```

## Usage

Invoke with `/agent-replicate` or let the model match it automatically when cloning is contextually relevant.

The skill walks through a guided procedure:

1. **Name** the clone
2. **Modify personality** (optional) — adjust SOUL.md traits for the clone
3. **Set memory policy** — `full` (inherit everything), `pruned` (keep knowledge, clear observations), or `minimal` (clean start)
4. **Adjust skills** (optional) — add or remove skills to specialize the clone
5. **Register** the clone as an autonomous agent via `openclaw agents add`
6. **Establish communication** (optional) — enable `sessions_send` between parent and clone

## Requirements

- [OpenClaw](https://openclaw.ai) installed and running
- `openclaw` CLI available in PATH

## Origin

Extracted from [Bobiverse OpenClaw](https://github.com/TheAmericanMaker/bobiverse-openclaw) — a Bobiverse-themed self-replicating agent system where GitHub forks are replication events and agents are modeled after Von Neumann probes. If you want self-cloning with sci-fi flavor and lineage tracking, check out the full project.

## License

MIT
