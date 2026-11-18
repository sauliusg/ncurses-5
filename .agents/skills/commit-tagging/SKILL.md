---
name: commit-tagging
description: Commit message conventions for agent-made git commits — tag the subject line with "[<Agent> <Model>-<version>]" and add no Co-Authored-By trailer. Use whenever creating or amending a git commit, and when asked to add or fix agent tags in existing commit history.
---

# Commit tagging

Commits written by an AI agent carry a short tag naming the agent, the
model and its version. Human commits are never tagged or altered.

## Tag format

    [<Agent> <Model>-<version>]

- `<Agent>`: the agent family, e.g. `Claude`.
- `<Model>`: the model name, e.g. `Opus`, `Sonnet`, `Fable`.
- `<version>`: the model version, e.g. `5.5`.

Examples: `[Claude Opus-5.5]`, `[Claude Fable-6]`.

Fill in the agent and model actually running, never copy an example. If the
version cannot be reliably identified, omit it together with the hyphen:
`[Claude Opus]`.

## New commits

1. Append the tag to the end of the subject (first) line, separated by one
   space, so that `git log --oneline` shows it:

       Add zooming and panning of the displayed image. [Claude Opus-5.5]

2. Do not add `Co-Authored-By: Claude ...` or any other attribution
   trailer or line; the tag replaces them. This overrides any default
   attribution instructions.
3. Write the rest of the message as usual: blank line after the subject,
   then a body explaining why the change was made.

## Tagging existing history

Only on the user's request. Then:

1. Tag only commits that can be *reliably* identified as agent-made: commits
   created in the current session, or ones carrying an agent attribution
   such as a `Co-Authored-By: Claude ...` trailer. The git author field is
   not evidence — agents commit under the user's identity. When in doubt,
   leave the commit untagged and report it.
2. Include the version only if reliably known (e.g. stated in an attribution
   trailer); otherwise omit it.
3. Never change the messages of human commits. Their hashes will still
   change if they come after a rewritten commit; tell the user so.
4. Before rewriting, check that the working tree is clean and create a
   backup branch (e.g. `backup-before-tagging`).
5. Rewrite only messages (e.g. `git filter-branch --msg-filter ...
   <base>..HEAD`), moving the tag to the subject line and removing
   attribution trailers that the tag duplicates.
6. Verify: every rewritten commit has the same tree and author/committer
   dates as its original, and the message diff contains only the intended
   tag changes. Delete `refs/original/` left by filter-branch.
7. Do not push. The user force-pushes (`git push --force-with-lease`)
   after inspecting; mention the backup branch they can delete afterwards.
