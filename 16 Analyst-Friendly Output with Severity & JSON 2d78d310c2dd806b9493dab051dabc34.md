# 16. Analyst-Friendly Output with Severity & JSON

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

Enhance your CLI security tool to output structured JSON with severity levels, so results can be consumed by SIEMs, SOAR, or downstream automation.

</aside>

### Input

Same as Day 14
Just take the file and number of retries from the user

Also give an option for json, for json output

`logs.txt`

```python
User login failed from 8.8.8.8
Internal scan from 10.0.0.5
Another hit from 1.1.1.1
```

### Final Code

```python
import random
import time
import argparse
import re
import json

parser=argparse.ArgumentParser("secscan.py")
parser.add_argument('file_name', metavar="<file_name>", type=str, help="Enter a log file name.")
parser.add_argument('max_retries', metavar="<max_retries>", type=int, help="Max number of retries")
parser.add_argument('--json',action='store_true', help="Enter for json output")
args=parser.parse_args()
filename=args.file_name
maxretries=args.max_retries

def flaky_enrich_ip(ip):
    # 50% chance of failure
    if random.random() < 0.5:
        raise Exception("Temporary API failure")
    return {
        "ip": ip,
        "confidence": 50,
        "source": "threat_intel"
    }

ip_pattern = r"\b\d{1,3}(?:\.\d{1,3}){3}\b"
ip_dict={}

try:
    with open(filename) as file:
        content=file.read()
        ip_addr = re.findall(ip_pattern,content)
        
except FileNotFoundError:
    print("File not found")
    

def enrich_with_retry(ip, maxretries=3, base_delay=1):
    retry = 0
    tmp_dict={}
    while retry < maxretries:
        try:
            result = flaky_enrich_ip(ip)
            if result["confidence"]>=80:
                tmp_dict["ip"]=result["ip"]
                tmp_dict["confidence"]=result["confidence"]
                tmp_dict["severity"]="HIGH"
                tmp_dict["alert"]=True
                
            elif result["confidence"]>=50 and result["confidence"]<80:
                tmp_dict["ip"]=result["ip"]
                tmp_dict["confidence"]=result["confidence"]
                tmp_dict["severity"]="MEDIUM"
                tmp_dict["alert"]=False
                
            else:
                tmp_dict["ip"]=result["ip"]
                tmp_dict["confidence"]=result["confidence"]
                tmp_dict["severity"]="LOW"
                tmp_dict["alert"]=False
            break

        except Exception:
            retry += 1

            if retry == maxretries:
                tmp_dict["ip"]=ip
                tmp_dict["error"]="enrichment_failed"
                tmp_dict["severity"]="NONE"
                tmp_dict["alert"]=False
                break

            time.sleep(base_delay * (2 ** (retry - 1)))
    return tmp_dict        
    
for i in ip_addr:
    ip_dict=enrich_with_retry(i, maxretries=3, base_delay=1)
    if args.json:
        print(json.dumps(ip_dict, indent=2))
    else:
        print(ip_dict)
```

### Screenshot

Screenshot is with `--json` option 

![image.png](16%20Analyst-Friendly%20Output%20with%20Severity%20&%20JSON/image.png)