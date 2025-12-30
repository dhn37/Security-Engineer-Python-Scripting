# 1. Log Analysis & Failed Authentication Detection

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

**Parse web server log entries and identify IP addresses that generated authentication failures. Count the number of 401 and 403 responses per IP to detect suspicious login activity.**

</aside>

### Input

Assume input as a list of logs

```jsx
logs = [
    '192.168.1.10 - - [10/Oct/2023:13:55:36 +0000] "GET /login HTTP/1.1" 200 1234',
    '10.0.0.5 - - [10/Oct/2023:13:56:01 +0000] "POST /login HTTP/1.1" 401 532',
    '192.168.1.10 - - [10/Oct/2023:13:56:45 +0000] "GET /admin HTTP/1.1" 403 210'
]
```

### Get IP and status code

```python
# Split each line of the log by using split()
for i in logs:
	each_log = i.split()     # now each_log contains a list of separated items from logs[i]
	ip = each_log[0]
	status_code = each_log[-2]
```

### Count 403 and 401 and putting it in a dictionary

But before that we need to check if it already exists or not.

```python
if status_code = 403 or status_code = 401:
	if ip in result:
		result_dict[ip]+=1      
	else:
		result_dict[ip]=1
```

### Final Code

```python
result_dict={}

logs = [
    '192.168.1.10 - - [10/Oct/2023:13:55:36 +0000] "GET /login HTTP/1.1" 401 1234',
    '10.0.0.5 - - [10/Oct/2023:13:56:01 +0000] "POST /login HTTP/1.1" 401 532',
    '192.168.1.10 - - [10/Oct/2023:13:56:45 +0000] "GET /admin HTTP/1.1" 403 210'
]

for i in logs:
    each_log = i.split()
    ip = each_log[0]
    status_code = each_log[-2]
    if status_code == "403" or status_code == "401":
        if ip in result_dict:
            result_dict[ip]+=1
        else:
            result_dict[ip]=1

print(result_dict)
```

### Screenshot

![image.png](1%20Log%20Analysis%20&%20Failed%20Authentication%20Detection/image.png)