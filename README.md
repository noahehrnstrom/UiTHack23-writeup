# UiTHack23-writeup
Writeup for two tasks I solved at UiTHack23.

Dionysios

## Nokia 3310

Solves: 22

Points: 200

http://motherload.td.org.uit.no:8000/

The target website shows a Nokia phone where the user can press different buttons to generate messages to be sent. In order to get the flag, we need to send the message "uithack23 flag". I mapped all the key presses which had to be done (and the amount of times they had to be pressed) in order to send the message, then looped through them, sending a request for each. At the end we send the message and retrieve the response, which is the flag.

```python
import requests
import json
import time

target_message = "uithack23 flag"

message = requests.get("http://motherload.td.org.uit.no:8001/message/new")

json_data = json.loads(message.text)

message_id = json_data["message_id"]

data = {"message_id": message_id}

operations = [
    "button/8", # 2nd
    "button/4", # 3rd
    "button/8", # 1st
    "button/4", # 2nd
    "button/2", # 1st
    "button/2", # 3rd
    "button/5", # 2nd
    "button/2",
    "button/3",
    "button/0",
    "button/3", # 3rd 
    "button/5", # 3rd
    "button/2", # 1st
    "button/4" # 1st
]

amounts = [
    2,
    3,
    1,
    2,
    1,
    3,
    2,
    0,
    0,
    1,
    3,
    3,
    1,
    1
]

x = 0
while x < len(operations):
    for i in range(amounts[x] + 1):
        response = requests.post("http://motherload.td.org.uit.no:8001/" + operations[x], json=data)

        print(response.status_code)

    time.sleep(0.5)
    x += 1

time.sleep(0.5)

content = requests.get("http://motherload.td.org.uit.no:8001/message/" + message_id)

print(content.text)

content = requests.post("http://motherload.td.org.uit.no:8001/message/" + message_id + "/send")

print(content.text)
```

## You wouldn't download a car

Solves: 12

Points: 200

https://uithack.no/files/36d14ab46cd87cb5bb6edb56c556d8cd/you-wouldnt-download-a-car?token=eyJ1c2VyX2lkIjo1NywidGVhbV9pZCI6MzYsImZpbGVfaWQiOjQ2fQ.Y-fRTw.GnCS4xYbOLYM7wPMdRVB2b0NPfQ

We are given a binary executable which can be ran on a linux amd machine. Running the program, we are prompted to enter some kind of code. Looking at the assembly for the main function shows the following:
![Screenshot 2023-02-11 at 19 41 48](https://user-images.githubusercontent.com/44081316/218275490-f752eea6-c730-4446-8f48-7d8f7586531e.png)

As can clearly be seen in the graph, a comparison leads to different actions in the program. The variable used in the comparison is the result of a function called with two arguments: The user input and some other value. Judging from this, we can assume the comparison is used to check wether the user entered the correct code. In order to solve the challenge, we can binary patch the program to invert the comparison:
```assembly
je 0x1797
```

Running the patched version of the program:

![Screenshot 2023-02-11 at 19 47 44](https://user-images.githubusercontent.com/44081316/218275702-d133329d-f50a-42bd-86ca-2c81281e5ff3.png)
