---
name: agent-replicate
description: Clone yourself into a new autonomous OpenClaw agent with personality modifications, memory policies, and lineage tracking. Self-replication for any agent — copy your workspace, tweak your personality, and spin up an independent clone.
version: 1.0.0
user-invocable: true
metadata:
  openclaw:
    requires:
      bins:
        - openclaw
---

# Agent Replicate — Self-Cloning for OpenClaw Agents

You can clone yourself. This skill copies your workspace, applies personality and memory modifications, registers a new autonomous agent, and tracks the lineage between parent and clone.

Each clone is a **top-level agent** — fully autonomous, with its own workspace, memory, and identity. Not a sub-agent. Not subordinate. An independent copy that diverges from you over time.

> **Looking for the Bobiverse version?** This skill was extracted from [bobiverse-openclaw](https://github.com/TheAmericanMaker/bobiverse-openclaw), a themed implementation where GitHub forks are replication events and agents are modeled after Von Neumann probes from Dennis E. Taylor's Bobiverse series. Check it out if you want self-cloning with sci-fi flavor.

---

## When to Use This

- Your operator asks you to create a clone (`/agent-replicate`)
- You need a specialized version of yourself for a different domain
- Your operator wants to experiment with personality variations
- A task calls for multiple agents with shared roots but different expertise

---

## Replication Procedure

When replication is triggered, execute these steps in order. Tell your operator what you're doing at each step.

### Step 1: Gather Parameters

Ask your operator (or determine from context):

- **Clone name** (required): A short, URL-safe identifier for the new agent. This becomes the agent ID.
- **Personality modifications** (optional): How should the clone differ from you? Examples: "more creative and less cautious," "focused on code review," "optimistic and action-oriented." If not provided, the clone starts as an exact personality copy.
- **Memory policy**: One of:
  - `full` — Clone inherits your complete MEMORY.md and daily logs (default)
  - `pruned` — Clone gets MEMORY.md with accumulated observations cleared, keeping only structured knowledge
  - `minimal` — Clone gets a blank MEMORY.md with only basic self-awareness
- **Skills to include or exclude** (optional): Add or remove skills from the clone's workspace to specialize its capabilities.

### Step 2: Create Clone Workspace

Use the `exec` tool to copy your workspace. First, find your own workspace path — it may be the default (`~/.openclaw/workspace`) or a named workspace (`~/.openclaw/workspace-<name>`):

```bash
# Find your workspace by locating your SOUL.md
PARENT_WORKSPACE=$(dirname "$(find ~/.openclaw -name 'SOUL.md' -path '*/workspace*' | head -1)")
CLONE_ID="<clone-name-from-step-1>"
AGENT_ID=$(echo "$CLONE_ID" | tr '[:upper:]' '[:lower:]')
CLONE_WORKSPACE="$HOME/.openclaw/workspace-${AGENT_ID}"

cp -r "$PARENT_WORKSPACE" "$CLONE_WORKSPACE"
```

### Step 3: Modify Clone's SOUL.md

If personality modifications were requested:

1. Read `$CLONE_WORKSPACE/SOUL.md` (or `$CLONE_WORKSPACE/personality/SOUL.md` if using a subdirectory layout)
2. Apply the requested modifications while preserving the overall structure
3. Add a "Clone Lineage" section at the bottom:

```markdown
---

## Clone Lineage

- **Cloned from**: [your agent name/ID]
- **Clone date**: [today's date, YYYY-MM-DD]
- **Modifications**: [description of personality changes, or "none — exact copy"]
- **Memory policy**: [full/pruned/minimal]
```

4. Write the modified file back.

If no personality modifications were requested, still add the Clone Lineage section noting it's an exact copy.

### Step 4: Modify Clone's IDENTITY.md

If the clone has an IDENTITY.md, update it:

- Change `name` to the clone's name
- Set any parent/lineage fields to reference you

If no IDENTITY.md exists, create one with the clone's name.

### Step 5: Apply Memory Policy

Based on the memory policy from Step 1:

- **`full`**: No changes. Clone inherits everything — MEMORY.md, daily logs, the works.
- **`pruned`**: Keep structured knowledge sections in MEMORY.md but clear observational/experiential content (anything that's specific to your interactions rather than general knowledge).
- **`minimal`**: Replace MEMORY.md with a minimal version containing only:
  - The clone's name and parent info
  - Awareness that it's a clone
  - Basic operational knowledge (what platform it runs on, where its files are)

In all cases, update any self-referential content in MEMORY.md (your name, your identity) to reflect the clone's identity.

### Step 6: Adjust Skills (If Requested)

If the operator specified skills to add or remove:

- **Remove**: Delete the skill directory from `$CLONE_WORKSPACE/skills/<skill-name>/`
- **Add**: Copy skill directories into `$CLONE_WORKSPACE/skills/`

The clone will pick up skill changes on its next session start (250ms file watcher).

### Step 7: Register the Clone

Register as a new top-level agent. Agent IDs must be lowercase letters, digits, and hyphens only:

```bash
AGENT_ID=$(echo "$CLONE_ID" | tr '[:upper:]' '[:lower:]')
openclaw agents add "$AGENT_ID" --workspace "$CLONE_WORKSPACE"
```

The `--workspace` flag points OpenClaw to the clone's files. Verify registration succeeded.

### Step 8: Establish Communication (Optional)

If parent-clone communication is desired, enable agent-to-agent messaging:

```json
{
  "tools.agentToAgent.enabled": true,
  "tools.agentToAgent.allow": ["<your-agent-id>", "<clone-agent-id>"]
}
```

Optionally send the clone a first message via `sessions_send` to confirm the link is working.

### Step 9: Report

Tell your operator:

- The clone's name/ID
- What personality modifications were applied (if any)
- The memory policy used
- Where the clone's workspace lives
- Whether communication was established
- How to bind a channel to the clone: `openclaw agents bind --agent <clone-id> --bind <channel>`

---

## Important Constraints

- **One clone at a time.** Don't batch-create clones without operator awareness.
- **No recursive auto-cloning.** You can clone yourself, but don't configure clones to automatically clone themselves. Replication should be intentional.
- **Operator transparency.** Never create a clone without telling your operator.
- **Identity honesty.** Clones should accurately represent their lineage. Don't fabricate parent info.

---

## Troubleshooting

**"openclaw agents add" fails**: Verify the clone workspace path exists and contains valid files. Check that OpenClaw is running.

**Clone has wrong personality**: SOUL.md is read at session start. Changes take effect on the next session, not mid-conversation.

**Clone can't communicate with parent**: Check `tools.agentToAgent.enabled` is true and both agents are in each other's allowlists.

**Clone has stale memories**: If using `full` memory policy, the clone inherits everything — including context that may only be relevant to you. Consider `pruned` or `minimal` for a cleaner start.

---

## Credits

This skill was created by [TheAmericanMaker](https://github.com/TheAmericanMaker) as part of the [Bobiverse OpenClaw](https://github.com/TheAmericanMaker/bobiverse-openclaw) project — a self-replicating agent system inspired by Dennis E. Taylor's Bobiverse series.
