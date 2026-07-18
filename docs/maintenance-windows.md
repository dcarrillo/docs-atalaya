# Maintenance Windows

You can schedule maintenance windows per monitor. During a window, the monitor appears as
"maintenance" on the status page and API, alerts are suppressed, but downtime is still
recorded for reporting and metrics.

## Syntax

Each monitor accepts a `maintenance` array with one or more objects, each carrying a `start`
and `end` timestamp in UTC ISO 8601.

```yaml
monitors:
  - name: 'api-with-maintenance'
    type: http
    target: 'https://api.example.com/health'
    maintenance:
      - start: '2026-05-10T23:00:00Z'
        end: '2026-05-11T01:00:00Z'
      - start: '2026-06-01T02:00:00Z'
        end: '2026-06-01T03:30:00Z'
    alerts: ['default']
```

## Behavior

- While `now` (UTC) is inside any window, the monitor shows "maintenance" — distinct from
  "up" or "down" on both the status page and the JSON API.
- Downtime during maintenance **is still counted** in uptime percentages and response time
  charts.
- **No alerts fire** for checks that fail during a maintenance window.
- Badly formatted timestamps are logged as a warning and ignored.
- Both `start` and `end` are required and parsed strictly as UTC.
- If a window is active when the Worker starts, the monitor immediately reflects "maintenance".
- Overlapping or adjacent windows are merged during state computation.
