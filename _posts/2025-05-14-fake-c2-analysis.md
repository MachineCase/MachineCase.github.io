---
title: Fake C2 for Controlling Go Malware
date: 2025-05-14 16:30:00 +0300
categories: [Malware Analysis, C2]
tags: [Go, malware, C2, reversing, macOS]
---

In this post, we explore how to set up a **fake command and control (C2)** server to interact with a malware sample written in Go, targeting macOS.

The sample connects to `2.56.246.203:5556` and expects specific commands such as:

- `KILL`
- `STOP`
- `LAUNCH`
- `UPDATE`
- `HTTPFLOOD`
- `PINGFLOOD`

To simulate a C2 server, we can use a simple Python script:

```python
import socket
import threading

def handle_client(client_socket):
    print(f"[+] Bot connected: {client_socket.getpeername()}")
    try:
        while True:
            cmd = input("Command: ")
            client_socket.sendall(cmd.encode())
    except:
        client_socket.close()

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(('0.0.0.0', 5556))
server.listen(5)
print("[+] C2 listening on port 5556")

while True:
    client, _ = server.accept()
    threading.Thread(target=handle_client, args=(client,)).start()
