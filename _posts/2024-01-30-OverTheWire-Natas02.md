---

title: "OverTheWire - Natas : Level 2 -> Level 3"

categories: [OvertTheWire,Natas]

tags: [scripting,web]
---

# Natas : Level 2 -> Level 3


1. Browse to the URL natas1.natas.labs.overthewire.org
2. Enter the username as `natas2 and password obtained from `natas1` level for `natas2`.
3. On the Homepage , it displays text  `There is nothing on this page` 

    ![Hompage](imgs/natas/natas2_3/n2.png)

4. Right click → View page source , observe the tag `img src="files/pixel.png">` 

    ![Untitled](imgs/natas/natas2_3/n2_1.png)

5. Copy the endpoint `/files`  and browse to the URL `[natas2.natas.labs.overthewire.org/files](http://natas2.natas.labs.overthewire.org/files)`  . the directory listing of files is enabled on the domain.

    ![Untitled](imgs/natas/natas2_3/n2_2.png)

6. Access the file  `users.txt`  . The file contains the password for next level i.e `natas3` 

    ![Untitled](imgs/natas/natas2_3/n2_3.png)


**Below is the python script to Automate using request library**

```python
# level 2 -> level 3

import requests as r
import re

def auth(username,password,URL):

    with r.session() as s:
        response = s.get(URL, auth =(username,password))
        content =response.text
        print(re.findall("natas3:(.*)", content)[0])

def main():
    username = 'natas2'
    password = 'h4ubbcXrWqsTo7GGnnUMLppXbOogfBZ7'
    URL = f"http://{username}.natas.labs.overthewire.org/files/users.txt"
    results = auth(username,password,URL)
    
main()
```