---

title: "OverTheWire - Natas : Level 1 -> Level 2"

categories: [OvertTheWire,Natas]

tags: [scripting,web]
---


# Natas : Level 1 -> Level 2

1. Browse to the URL natas1.natas.labs.overthewire.org

2. Enter the username as `natas1` and  password obtained from `natas0` level for `natas1`. 


3. View page source of the HTML page to get the password to next level `natas2`.


Below is the python script to Automate using request library 

```python
import requests as r
import re

def auth(username ,password, URL) :
	with r.session() as s :
		resp = s.get(URL, auth=(username,password))
		content = resp.text
		print(re.findall("<!--The password for natas2 is (.*) -->", content)[0])

def main():
	username = 'natas1'
	password = 'g9D9cREhslqBKtcA2uocGHPfMZVzeFK6' # natas1 password obtained from natas0
	URL = f"http://{username}.natas.labs.overthewire.org"
	results = auth(username, password, URL)

main()
```
