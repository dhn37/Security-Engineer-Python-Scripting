# 13. Controlled Parallel Processing with Threads

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

Understand when and how to use parallelism (threads) to speed up IOC processing **without violating rate limits or overwhelming external services**.

</aside>

### Input

```python
ips = [
    "8.8.8.8",
    "1.1.1.1",
    "10.0.0.5",
    "172.16.0.3",
    "9.9.9.9",
    "4.4.4.4"
]

import time

def mock_enrich_ip(ip):
    time.sleep(0.5)  # simulate network latency
    return {
        "ip": ip,
        "confidence": 50,
        "source": "threat_intel"
    }
```

### Final Code

```python
import time
from concurrent.futures import ThreadPoolExecutor, as_completed

ips = [
    "8.8.8.8",
    "1.1.1.1",
    "10.0.0.5",
    "172.16.0.3",
    "9.9.9.9",
    "4.4.4.4"
]

def mock_enrich_ip(ip):
    time.sleep(0.5)  # simulate network latency
    return {"ip": ip, "confidence": 50, "source": "threat_intel"}

results = []

with ThreadPoolExecutor(max_workers=2) as executor:
    futures = [executor.submit(mock_enrich_ip, ip) for ip in ips]

    for future in as_completed(futures):
        results.append(future.result())

print(results)

```

### Screenshot

![image.png](13%20Controlled%20Parallel%20Processing%20with%20Threads/image.png)