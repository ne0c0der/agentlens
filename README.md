# agent-trace-viz

See what your AI agent is actually doing. Paste in a run trace, get a timeline of every thought, tool call, and escalation — with durations and errors highlighted.

## Run it

```bash
cd agent-trace-viz
npx serve   # or: python3 -m http.server
```

Open the URL it prints. That's it — one HTML file, one JSON file, no build step.

## The trace format

`trace.json` is the whole schema:

```json
{
  "agent": "demo-agent",
  "run_id": "run_001",
  "task": "What the agent was asked to do",
  "steps": [
    {
      "id": 1,
      "type": "thought | tool | escalation",
      "label": "calendar.check_availability",
      "detail": "Why it did this / what it sent",
      "result": "What came back",
      "status": "ok | error | waiting",
      "duration_ms": 340
    }
  ]
}
```

Swap in your own trace and reload. Adapters for real agent frameworks (LangGraph, CrewAI, raw OpenAI tool loops) are the obvious next step — PRs welcome.

## Why

Agents fail in ways you can't see from the final answer. A timeline of tool calls shows you where it went wrong: the retry loop, the slow call, the escalation that never came. Watch it think.
