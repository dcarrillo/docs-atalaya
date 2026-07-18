# Data Retention

Atalaya stores two levels of data in D1:

| Data | Retention |
|---|---|
| Raw check results | 7 days |
| Hourly aggregates | 90 days |

A cron job (`0 * * * *`) runs once an hour to roll up raw results into hourly aggregates
and purge expired records. You do not need to configure this — it is baked into the Worker.

Raw results include every check attempt, response time, status code, and error message.
Hourly aggregates collapse those into summary stats: average response time, uptime percentage,
and error count per monitor per hour.

## Why two tiers

Raw data is useful for debugging recent failures and seeing exactly what happened during
the last few check intervals. Keeping it forever would bloat the D1 database without adding
much value, since trend analysis almost always works on hourly resolution or coarser.

The 90-day window for aggregates is a deliberate choice. Cloudflare D1 is billed by storage
and reads. Keeping per-minute resolution for a year would cost more and make the status
page slower. If you need longer retention, you are better off exporting the data to an
external database or log platform.
