# 6. Regex IOC Extraction (IPs, Domains, URLs)

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

Given messy, unstructured security text (alerts/log snippets), extract Indicators of Compromise (IOCs) using regex—specifically **IPv4 addresses**, **domains**, and **URLs**—and return them in a structured dictionary.

</aside>

### Input

```python
text = """
[ALERT] Possible phishing. User clicked: http://login-microsoft-secure.com/account/verify
DNS query observed: api.github.com and cdn.bad-site.net
Suspicious connection from 10.0.0.5 to 172.16.0.3:443
Ignore private note: test-domain.local
More hits: https://evil.co/path?x=1 and http://sub.evil.co/reset
Also saw IP 192.168.1.10 and 8.8.8.8 in logs.
"""
```

### Logic of the code

`r` is used for python to treat the string as a raw string so that it does not treat the backslashes as special characters.

Here, set is used to get unique elements in the list

```python
ip_pattern = r"\b\d{1,3}(?:\.\d{1,3}){3}\b"
url_pattern = r"https?://[^\s]+"
domain_pattern = r"\b[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}\b"

ip = re.findall(ip_pattern,text)
url = re.findall(url_pattern,text)
domain = re.findall(domain_pattern,text)

result_dict["ips"]=set(ip)
result_dict["domains"]=set(domain)
result_dict["urls"]=set(url)
```

### Final Code

```python
import re
result_dict={}

text = """
[ALERT] Possible phishing. User clicked: http://login-microsoft-secure.com/account/verify
DNS query observed: api.github.com and cdn.bad-site.net
Suspicious connection from 10.0.0.5 to 172.16.0.3:443
Ignore private note: test-domain.local
More hits: https://evil.co/path?x=1 and http://sub.evil.co/reset
Also saw IP 192.168.1.10 and 8.8.8.8 in logs and 192.168.1.10.
"""

ip_pattern = r"\b\d{1,3}(?:\.\d{1,3}){3}\b"
url_pattern = r"https?://[^\s]+"
domain_pattern = r"\b[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}\b"

ip = re.findall(ip_pattern,text)
url = re.findall(url_pattern,text)
domain = re.findall(domain_pattern,text)

result_dict["ips"]=set(ip)
result_dict["domains"]=set(domain)
result_dict["urls"]=set(url)

print(result_dict)
```

### Screenshot

![image.png](6%20Regex%20IOC%20Extraction%20(IPs,%20Domains,%20URLs)/image.png)