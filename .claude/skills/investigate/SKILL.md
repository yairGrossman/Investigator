---
name: investigate
description: Trace data flows through a codebase and write structured documentation to Investigators/*.md. Accepts optional "from" (start point) and "to" (end layer/function).
argument-hint: [from <start> to <end> | feature: <name>]
---

Use the `investigator` subagent to handle this request. You MUST delegate entirely to the investigator subagent using the Agent tool — do not attempt to trace the code yourself.

Pass this request verbatim to the subagent: $ARGUMENTS
