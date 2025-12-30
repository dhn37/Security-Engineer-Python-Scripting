# 18. Exporting Results for SIEMs (CSV & Unix Pipelines)

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

Extend your security CLI tool so its output can be ingested by SIEMs or chained into other tools using standard Unix pipelines.

</aside>

### Input

Same as previous day

We need to add the `--csv` along with `--output` to output into a csv file

It cannot coexist with `--json` 

### Final Code

```python
import random
import time
import argparse
import re
import json
import csv

# ---------------- CLI ----------------
parser = argparse.ArgumentParser("secscan.py")
parser.add_argument("file_name", help="Enter a log file name.")
parser.add_argument("max_retries", type=int, help="Max number of retries")
parser.add_argument("--json", action="store_true", help="Output JSON")
parser.add_argument("--output", "-o", help="Output file (optional)")
parser.add_argument("--csv", action="store_true", help="Output CSV")
args = parser.parse_args()

# ---------------- Enrichment ----------------
def flaky_enrich_ip(ip):
    if random.random() < 0.5:
        raise Exception("Temporary API failure")
    return {
        "ip": ip,
        "confidence": 50,
        "source": "threat_intel"
    }

# ---------------- Read file ----------------
ip_pattern = r"\b\d{1,3}(?:\.\d{1,3}){3}\b"

try:
    with open(args.file_name) as f:
        content = f.read()
        ip_addr = re.findall(ip_pattern, content)
except FileNotFoundError:
    print("File not found")
    exit(1)

# ---------------- Retry logic ----------------
def enrich_with_retry(ip, max_retries=3, base_delay=1):
    retry = 0
    entry = {}

    while retry < max_retries:
        try:
            result = flaky_enrich_ip(ip)

            entry["ip"] = result["ip"]
            entry["confidence"] = result["confidence"]

            if result["confidence"] >= 80:
                entry["severity"] = "HIGH"
                entry["alert"] = True
            elif result["confidence"] >= 50:
                entry["severity"] = "MEDIUM"
                entry["alert"] = False
            else:
                entry["severity"] = "LOW"
                entry["alert"] = False

            return entry

        except Exception:
            retry += 1
            if retry == max_retries:
                entry["ip"] = ip
                entry["error"] = "enrichment_failed"
                entry["severity"] = "NONE"
                entry["alert"] = False
                return entry

            time.sleep(base_delay * (2 ** (retry - 1)))

# ---------------- Collect results ----------------
results = []

for ip in ip_addr:
    results.append(enrich_with_retry(ip, args.max_retries))

# ---------------- Output ----------------

# Mutually exclusive check
if args.json and args.csv:
    raise SystemExit("Error: --json and --csv cannot be used together")

# ---------------- CSV OUTPUT ----------------
if args.csv:
    if not args.output:
        raise SystemExit("Error: --csv requires --output <file.csv>")

    if not args.output.endswith(".csv"):
        raise SystemExit("Error: CSV output file must end with .csv")

    with open(args.output, "w", newline="") as file:
        columns = ["ip", "confidence", "severity", "alert", "error"]
        writer = csv.DictWriter(file, fieldnames=columns)
        writer.writeheader()

        for r in results:
            writer.writerow({
                "ip": r["ip"],
                "confidence": r.get("confidence", ""),
                "severity": r["severity"],
                "alert": str(r["alert"]).lower(),
                "error": r.get("error", "")
            })

# ---------------- JSON OUTPUT ----------------
elif args.json:
    output_data = json.dumps(results, indent=2)
    if args.output:
        with open(args.output, "w") as f:
            f.write(output_data)
    else:
        print(output_data)

# ---------------- TEXT OUTPUT ----------------
else:
    output_data = "\n".join(
        f"[{'ALERT' if r['alert'] else 'OK'}] {r['ip']} "
        f"severity={r['severity']} confidence={r.get('confidence', 'N/A')}"
        if "error" not in r
        else f"[OK] {r['ip']} enrichment_failed"
        for r in results
    )

    if args.output:
        with open(args.output, "w") as f:
            f.write(output_data)
    else:
        print(output_data)
```

### Screenshot

![image.png](18%20Exporting%20Results%20for%20SIEMs%20(CSV%20&%20Unix%20Pipelin/image.png)