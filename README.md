# nginx-log-analyzer

A small Bash script that reads an Nginx access log and shows the top results for four things:

- IP addresses with the most requests
- Most requested paths
- Response status codes
- User agents

Built for the [Nginx Log Analyser](https://roadmap.sh/projects/nginx-log-analyser) project on roadmap.sh.

## Requirements

- Bash 4+
- Standard tools: `awk`, `sort`, `uniq`, `grep`
- An Nginx or Apache access log in the **combined** format

## Usage

```bash
chmod +x nginx-log-analyzer.sh
./nginx-log-analyzer.sh [log_file]
```

If no file is given, it reads `nginx.log` in the current directory.

The script asks how many results to show:

```
How many top results? [5]
```

Press Enter for 5, or type any whole number from 1 up.

## Demo

`nginx.log` is the sample access log provided by the roadmap.sh project, so you can try it straight away:

```bash
./nginx-log-analyzer.sh nginx.log
```

Example output (your numbers will differ):

```
Top 5 IP addresses with the most requests:
178.128.94.113 - 42 requests
...

Top 5 requested paths:
/v1-health - 42 requests
...

Top 5 response status codes:
200 - 90 requests
...

Top 5 user agents:
DigitalOcean Uptime Probe 0.22.0 (https://digitalocean.com) - 42 requests
...
```

## Log format

The script expects each line to look like this:

```
178.128.94.113 - - [04/Oct/2024:00:01:18 +0000] "GET /v1-health HTTP/1.1" 200 51 "-" "DigitalOcean Uptime Probe 0.22.0 (https://digitalocean.com)"
```

| Value | Where it is read from |
|---|---|
| IP address | Field 1 |
| Path | Field 7 |
| Status code | Field 9 |
| User agent | Last quoted field |

## How it works

```
log file ──awk──► one column ──sort──► uniq -c ──sort -nr──► awk (top N) ──► report
```

Each report uses the same helper function, `top_n`: pick a column, count how often each value appears, rank the counts, and print the first N.

## Notes

- Read-only. The script never changes your log file.
- Run it, do not `source` it.
- The prompt waits for a person, so it will hang under cron or a pipe.
- Lines that do not match the combined format may give odd results.

## Files

| File | Purpose |
|---|---|
| `nginx-log-analyzer.sh` | The script |
| `nginx.log` | Demo log for testing |
| `README.md` | This file |

## Author

tamilore-dev
