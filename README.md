# Multi-threaded-Chatroom-Service
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
