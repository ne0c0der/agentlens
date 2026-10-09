# Trace format

`index.html` loads `trace.json` from the same directory and draws one row per `steps` entry, in array order. Replace that file with your own run and reload. Serve the folder over HTTP (`npx serve` or `python3 -m http.server`); opening the HTML file from disk will not load the JSON.

## Run

| Field | Viewer |
| --- | --- |
| `task` | Page title. |
| `agent` | Start of the subtitle. |
| `run_id` | Middle of the subtitle. |
| `steps` | Timeline. The subtitle ends with this array's length. |
| `started_at` | On the demo file (`2026-10-09T00:00:00Z`). Not read. |

## Step

| Field | Viewer |
| --- | --- |
| `type` | `thought`, `tool`, or `escalation`. Sets the icon and, unless `status` is `error`, the dot color. Any other string still renders, with a `•` icon. |
| `label` | Row title. |
| `status` | Badge text. Styled values are `ok`, `error`, and `waiting`. `error` paints the dot red and overrides the type color. |
| `duration_ms` | Milliseconds. A non-zero number is shown as seconds with one decimal (`2100` → `2.1s`). `0` or omitted is shown as `—`. |
| `detail` | Body text. Blank when missing or empty. |
| `result` | When non-empty, a following line: `→` plus the text. |
| `id` | On the demo file (`1`–`8`). Not read. |

Thoughts, tool calls, retries, errors, timing, and escalations are ordinary steps. There is no extra field for them.

- **Thought.** `type` `thought`. The demo uses `label` `Thinking`, `status` `ok`, the text in `detail`, and no `result`.
- **Tool call.** `type` `tool`. `label` is the tool name. `detail` is the arguments string (JSON text in the demo). `result` is the return value string. Demo tools: `calendar.check_availability`, `places.search_dentists`, `places.get_reviews`.
- **Error.** `status` `error`. The demo stores the message in `result` (`Rate limited — retrying` on `places.get_reviews`).
- **Retry.** The next step, not a type. The demo repeats `places.get_reviews` with the same `detail` (`{"place_id": "dentist_2"}`). The failed step stays; the next one is `status` `ok` with `result` `4.8 stars, 212 reviews`.
- **Timing.** That step's `duration_ms` only. Nothing is summed.
- **Escalation.** `type` `escalation`. The demo uses `label` `Needs you`, `status` `waiting`, the question in `detail`, `duration_ms` `0` (shown as `—`), and no `result`.

## Example

Copied from the demo (`id` 1, 5, 6, and 8). The file on disk also has steps 2–4 (calendar check, a thought, dentist search) and step 7 (a thought).

```json
{
  "agent": "demo-agent",
  "run_id": "run_001",
  "started_at": "2026-10-09T00:00:00Z",
  "task": "Book a dentist appointment for Tuesday morning",
  "steps": [
    {
      "id": 1,
      "type": "thought",
      "label": "Thinking",
      "detail": "User wants a dentist appointment Tuesday morning. I should check the calendar first, then search for dentists.",
      "status": "ok",
      "duration_ms": 1200
    },
    {
      "id": 5,
      "type": "tool",
      "label": "places.get_reviews",
      "detail": "{\"place_id\": \"dentist_2\"}",
      "result": "Rate limited — retrying",
      "status": "error",
      "duration_ms": 800
    },
    {
      "id": 6,
      "type": "tool",
      "label": "places.get_reviews",
      "detail": "{\"place_id\": \"dentist_2\"}",
      "result": "4.8 stars, 212 reviews",
      "status": "ok",
      "duration_ms": 750
    },
    {
      "id": 8,
      "type": "escalation",
      "label": "Needs you",
      "detail": "Book Tuesday 9:30 at Bright Smile Dental?",
      "status": "waiting",
      "duration_ms": 0
    }
  ]
}
```
