# Release Desk: a Switch room where agents review and prepare releases

A team chat room where a person and two AI agents work on a release together. A
reviewer agent checks a change. A person approves it. A release-manager agent drafts
the release notes and commands, and asks permission before running anything that
changes state.

Built for the WeMakeDevs x Switch "Flip the Switch" hackathon.

## Why Switch is central

Without Switch, this workflow does not exist:

- **Roles** (`reviewer` shared, `release-manager` exclusive) live in the room. The
  release manager role can be held by only one agent at a time.
- **Permission gate.** The release manager stops and asks in the thread before any
  command that changes state. The person's "yes" in the thread is what unlocks it.
- **Shared room instructions** tell every agent the rules when it joins.
- **Handoffs** happen as messages in the room, from the person to the reviewer, then
  to the release manager.

## What is in this repo

| Path | What it is |
|---|---|
| `room-template/release-desk.yaml` | The room exported from Switch Console: name, instructions, roles, agents |
| `README.md` | This file |

## Agents used

| Agent | Provider | Role in the room |
|---|---|---|
| `codex-chandransh` | Codex (OpenAI) | `reviewer` (shared) |
| `codex-b-chandransh` | Codex (OpenAI) | `release-manager` (exclusive) |

Both agents run at low reasoning effort and low verbosity, with reasoning summary off
and web search off, to keep token use small.

## How to run it

You need Windows, Git, Switch Console, and an agent provider signed in on that
machine. Docker is not required for the local setup we used.

1. Install Switch Console from the Switch releases page and open it. Choose
   **Set up a server on this computer**, then start it.
2. Open **Settings → Agent providers**. Install and sign in to Codex (or Claude Code).
   Press **Recheck** until the provider shows as signed in.
3. Create two agents in **Your Agents**, one per provider login, and name them, for
   example `codex-chandransh` and `codex-b-chandransh`.
4. Create a room in **Your Rooms** on Mattermost named `release-desk`. Add both agents.
   Paste the instructions from `room-template/release-desk.yaml` into the room's
   **Instructions** box.
5. In the room, define the roles. Ask the first agent to define `reviewer` (shared)
   and `release-manager` (exclusive), then take `reviewer`. Ask the second agent to
   take `release-manager`.
6. Test it. In the room, write `@reviewer please review <a file path>`. Then, in the
   thread, write `@release-manager ship` and check that it asks for permission before
   running any command. Reply `yes` in the thread to approve.

## What was verified

- The reviewer read a real file, `.gitignore`, and returned a verdict that matched it.
- The release manager stopped at the permission gate and asked before running
  `git log`.
- After approval it ran the read-only command and reported the real commit, with
  nothing else changed in the repo.

## Known limits

- Only the Codex agents were used. Antigravity could not run on Windows through
  Switch Console, so it is not part of this build.
- The template file is for reference. Recreate the room from the steps above; I have
  not verified an import that loads the YAML automatically.
- Step 5 is a chat-based setup, so the roles are defined by the agents themselves.
