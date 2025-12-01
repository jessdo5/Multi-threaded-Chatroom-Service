# Multi-threaded Chatroom Service

A simple UDP-based, multi-threaded chatroom system implemented in C for Unix/Linux environments.  
This project was created for a computer networks assignment and demonstrates:

- UDP communication  
- Structured packet design  
- A dispatcher thread  
- Per-room worker threads  
- Message queues  
- User lists  
- Basic message logging  

---

This system implements a **single chat server** and multiple **chat clients**.  
Clients can join chat rooms, send messages, and leave rooms.  
Each chat room is served by its own dedicated thread.

Unlike TCP, this system uses **UDP sockets** and a custom structured packet format:

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
/project
│── ChatroomServer.c
│── ChatClient.c
│── README.md
How It Works
Server

Listens on a fixed UDP port (default: 5000).

Uses a dispatcher thread to receive packets from clients and forward them to the appropriate chat room.

Creates chat rooms on demand.

Each chat room runs in its own worker thread:

Maintains a message queue and message log.

Broadcasts messages to all users in the room.

Deletes itself when empty.

Client

Single UDP socket bound to a local port.

Receiver thread listens for messages from the server.

Main thread reads user input, constructs packets, and sends them to the server.

Users can join or leave rooms and send messages.Compilation

Server:

gcc ChatroomServer.c -o server -lpthread


Client:

gcc ChatClient.c -o client -lpthread


If close() warnings appear, add #include <unistd.h> at the top.

Running the Programs
Start the Server
./server


Expected output:

Server running on port 5000...

Start Clients
./client <server_ip> <server_port> <username>


Example:

./client 127.0.0.1 5000 Alice
./client 127.0.0.1 5000 Bob

Client Commands
Command	Description
/join <roomname>	Join or create a chat room
/leave	Leave the current room
/quit	Exit the client
anything else	Sends a chat message
Example Workflow

Server Terminal:

./server
Server running on port 5000...


Client 1 Terminal:

./client 127.0.0.1 5000 Alice
/join first
Hello everyone!


Client 2 Terminal:

./client 127.0.0.1 5000 Bob
/join first
Hi Alice!


Output Example:

Alice sees: [first] Bob: Hi Alice!

Bob sees: [first] Alice: Hello everyone!

Design Summary
Server

Dispatcher thread receives all client packets.

Maintains a room manager storing all chat rooms.

Each room contains:

Message queue

Log queue

User list

Worker thread

Client

Single UDP socket.

Receiver thread listens for server messages.

Main thread handles user input and sends structured packets.

Features

Supports multiple clients and chat rooms.

Rooms created on demand.

Messages are broadcasted to all users in a room.

Message history is maintained.

Rooms are deleted when empty.

Thread-safe queues and UDP communication.

Enum-based packet protocol ensures consistency.
