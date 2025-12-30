# 5. REST API Calls & Error Handling

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

**Retrieve data from a REST API endpoint, safely handle HTTP errors, parse the JSON response, and extract security-relevant fields for further analysis.**

</aside>

### Input

```python
api_responses = {
    "10.0.0.5": {"status_code": 200, "json": {"malicious": True, "confidence": 85}},
    "192.168.1.10": {"status_code": 200, "json": {"malicious": False, "confidence": 5}},
    "172.16.0.3": {"status_code": 500, "json": None}
}
```

### Logic of the code

```python
for i in ips:
    if api_responses[i]["json"] != None:
        if api_responses[i]["json"]["malicious"]==True:
            result_dict[i]=api_responses[i]["json"]["confidence"]
    else:
        continue
```

### Final Code

```python
result_dict={}

ips = ["10.0.0.5", "192.168.1.10", "172.16.0.3"]

api_responses = {
    "10.0.0.5": {"status_code": 200, "json": {"malicious": True, "confidence": 85}},
    "192.168.1.10": {"status_code": 200, "json": {"malicious": False, "confidence": 5}},
    "172.16.0.3": {"status_code": 500, "json": None}
}

for i in ips:
    if api_responses[i]["json"] != None:
        if api_responses[i]["json"]["malicious"]==True:
            result_dict[i]=api_responses[i]["json"]["confidence"]
    else:
        continue

print(result_dict)
```

### Screenshot

![image.png](5%20REST%20API%20Calls%20&%20Error%20Handling/image.png)

## If input is from real endpoint

```python
import requests

def fetch_malicious_ips(ips):
    result = {}

    for ip in ips:
        try:
            response = requests.get(
                "https://threatintel.example/api",
                params={"ip": ip},
                timeout=5
            )
        except requests.RequestException:
            continue  # network issue, skip

        if response.status_code != 200:
            continue

        try:
            data = response.json()
        except ValueError:
            continue  # invalid JSON

        if data.get("malicious") is True:
            confidence = data.get("confidence")
            if confidence is not None:
                result[ip] = confidence

    return result
```

### Explanation

Since this is an external API, I handle network errors, non-200 responses, and invalid JSON before parsing fields. I only extract data if the response is valid and malicious.