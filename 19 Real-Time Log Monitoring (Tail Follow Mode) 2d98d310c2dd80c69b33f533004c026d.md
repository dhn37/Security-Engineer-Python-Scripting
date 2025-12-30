# 19. Real-Time Log Monitoring (Tail / Follow Mode)

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

Extend your security scanner so it can continuously monitor a log file, detect new IOCs as they appear, and process them in real time.

</aside>

### Input

Same as previous problem.
Use `--follow` in the input

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
parser.add_argument("--csv", action="store_true", help="Output CSV")
parser.add_argument("--output", "-o", help="Output file (optional)")
parser.add_argument("--follow", action="store_true", help="Follow log file in real time")
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

# ---------------- Regex ----------------
ip_pattern = r"\b\d{1,3}(?:\.\d{1,3}){3}\b"

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

# ---------------- MUTUAL EXCLUSION ----------------
if args.json and args.csv:
    raise SystemExit("Error: --json and --csv cannot be used together")

# ======================================================================
#                           FOLLOW MODE (DAY 19)
# ======================================================================
if args.follow:
    with open(args.file_name) as f:
        f.seek(0, 2)  # 🔑 jump to end of file

        print("[*] Follow mode enabled. Waiting for new log entries...\n")

        while True:
            line = f.readline()

            if not line:
                time.sleep(1)
                continue

            ips = re.findall(ip_pattern, line)

            for ip in ips:
                result = enrich_with_retry(ip, args.max_retries)

                # Follow mode → always stdout, one line at a time
                if "error" in result:
                    print(f"[OK] {result['ip']} enrichment_failed")
                else:
                    status = "ALERT" if result["alert"] else "OK"
                    print(
                        f"[{status}] {result['ip']} "
                        f"severity={result['severity']} "
                        f"confidence={result['confidence']}"
                    )

# ======================================================================
#                           BATCH MODE (DAY 18)
# ======================================================================
else:
    # -------- Read full file --------
    try:
        with open(args.file_name) as f:
            content = f.read()
            ip_addr = re.findall(ip_pattern, content)
    except FileNotFoundError:
        print("File not found")
        exit(1)

    # -------- Process all IPs --------
    results = []
    for ip in ip_addr:
        results.append(enrich_with_retry(ip, args.max_retries))

    # -------- CSV OUTPUT --------
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

    # -------- JSON OUTPUT --------
    elif args.json:
        output_data = json.dumps(results, indent=2)
        if args.output:
            with open(args.output, "w") as f:
                f.write(output_data)
        else:
            print(output_data)

    # -------- TEXT OUTPUT --------
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