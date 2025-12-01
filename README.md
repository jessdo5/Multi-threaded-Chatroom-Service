# Multi-threaded UDP Chatroom Service

A simple and scalable **multi-threaded chatroom** implemented in **C** using **UDP sockets** for asynchronous communication. It demonstrates practical concepts in concurrency, messaging, and dynamic resource management, including:

* UDP socket communication
* Structured packet protocol
* Dispatcher thread design pattern
* Per-room worker threads
* Thread-safe message queues
* Dynamic room creation and cleanup
* Basic message broadcasting and logging

---

## Overview

This project includes:

* A **single chat server**
* Multiple **chat clients**

Clients can:

* Join chat rooms
* Send broadcast messages
* Leave rooms
* Exit safely

Each chat room is managed by its own **dedicated worker thread**, enabling concurrency and scalable design.

---

## Packet Format

A lightweight, custom packet structure is used instead of TCP streams:

```c
typedef enum PacketType {
    JOIN,
    LEAVE,
    MESSAGE,
    ACK
} packetType;

typedef struct Packet {
    packetType type;
    char room[64];
    char username[32];
    char message[512];
} packet;
```

## How It Works

### Server

* Listens on a fixed UDP port (**default: 5000**)
* One **dispatcher thread**:

  * Receives client packets
  * Routes them to the appropriate chatroom
* Chat rooms created **on demand**
* Each room has:

  * Worker thread
  * User list
  * Message queue
  * Persistent message log
* Rooms self-destruct when empty

### Client

* Single UDP socket instance
* **Receiver thread**: listens for server messages
* **Main thread**: reads user input and sends packets
* Simple command-based interaction

---

## 🛠 Compilation

### Server:

```bash
gcc ChatroomServer.c -o server -lpthread
```

### Client:

```bash
gcc ChatClient.c -o client -lpthread
```

> If you see warnings about `close()`, add:

```c
#include <unistd.h>
```

---

## ▶ Running the Program

### Start the Server

```bash
./server
```

Expected:

```
Server running on port 5000...
```

### Start Clients

```bash
./client <server_ip> <server_port> <username>
```

Example:

```bash
./client 127.0.0.1 5000 Alice
./client 127.0.0.1 5000 Bob
```

---

##  Client Commands

| Command            | Description                |
| ------------------ | -------------------------- |
| `/join <roomname>` | Join or create a chat room |
| `/leave`           | Leave the current room     |
| `/quit`            | Exit the client            |
| Any other text     | Sends a chat message       |

---

## Example Session

**Server Terminal**

```
./server
Server running on port 5000...
```

**Client 1**

```
./client 127.0.0.1 5000 Alice
/join first
Hello everyone!
```

**Client 2**

```
./client 127.0.0.1 5000 Bob
/join first
Hi Alice!
```

**Output Broadcast**

```
[first] Alice: Hello everyone!
[first] Bob: Hi Alice!
```

---

## Architecture Summary

| Component                | Responsibility                       |
| ------------------------ | ------------------------------------ |
| Dispatcher Thread        | Handles all incoming UDP packets     |
| Room Manager             | Tracks room existence + active users |
| Worker Thread (per-room) | Broadcasts and logs messages         |
| Client Receiver Thread   | Displays server messages             |
| Client Main Thread       | Handles input and packet generation  |

---

##  Features

* Multi-client support
* Multi-room support
* UDP socket communication
* Dynamic room creation and destruction
* Thread-safe message delivery
* Lightweight custom protocol

---

