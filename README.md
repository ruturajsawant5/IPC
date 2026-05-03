# 📡 Inter-Process Communication (IPC) in Linux

A hands-on collection of IPC mechanisms implemented in C on Linux. This repository explores how processes communicate with each other using **Unix Domain Sockets** and **POSIX Message Queues**, progressing from simple blocking I/O to multiplexed, non-blocking server designs.

---

## 📂 Repository Structure

```
IPC/
├── 1_Unix_Domain_Sockets/
│   ├── blocking/            # Single-client blocking server
│   │   ├── server.c
│   │   ├── client.c
│   │   └── Makefile
│   └── non-blocking/        # Multi-client server using select()
│       ├── server.c
│       ├── client.c
│       └── Makefile
└── 3_Message_Queue/
    ├── sender.c             # POSIX mq sender
    ├── receiver.c           # POSIX mq receiver with select()
    └── Makefile
```

---

## 🔌 1 — Unix Domain Sockets

Unix Domain Sockets (`AF_UNIX`) provide high-performance, local-only IPC through the filesystem namespace (e.g. `/tmp/DemoSocket`). Both examples implement a **summation service**: the client sends integers to the server, sends `0` to signal completion, and the server returns the cumulative sum.

### Blocking (Single-Client)

A straightforward stream socket server that accepts **one client at a time**. The server loops on `accept()` → `read()` → `write()`, blocking at each system call until data arrives.

| Concept | Details |
|---|---|
| Socket type | `SOCK_STREAM` (connection-oriented) |
| Concurrency | Single client — sequential processing |
| Key syscalls | `socket`, `bind`, `listen`, `accept`, `read`, `write` |

### Non-Blocking (Multi-Client with `select()`)

An evolution of the blocking server that handles **up to 32 concurrent clients** using the `select()` multiplexing syscall. The server maintains an `fd_set` of monitored file descriptors and dispatches work based on which FD becomes ready, including stdin for console input.

| Concept | Details |
|---|---|
| Socket type | `SOCK_STREAM` (connection-oriented) |
| Concurrency | Up to 32 clients via `select()` I/O multiplexing |
| Key syscalls | `socket`, `bind`, `listen`, `accept`, `select`, `read`, `write` |
| FD management | Custom array-backed FD set with add/remove helpers |

---

## 📬 3 — POSIX Message Queues

Demonstrates asynchronous, kernel-managed message passing using the POSIX `mqueue` API (`<mqueue.h>`). The sender and receiver are fully decoupled — they communicate through a named queue (e.g. `/myqueue`) without needing a direct socket connection.

| Concept | Details |
|---|---|
| API | POSIX message queues (`mq_open`, `mq_send`, `mq_receive`) |
| Queue attributes | Max 10 messages, 256 bytes each |
| Receiver model | Blocks on `select()` waiting for incoming messages |
| Naming | Queue name passed as a CLI argument (`/queue-name`) |

---

## 🛠️ Build & Run

Each sub-project includes its own `Makefile`. Build with:

```bash
cd 1_Unix_Domain_Sockets/blocking
make
```

### Unix Domain Sockets

```bash
# Terminal 1 — start the server
./server

# Terminal 2 — connect a client
./client
# Enter integers, then 0 to get the sum
```

### Message Queues

```bash
cd 3_Message_Queue
make

# Terminal 1 — start receiver
./receiver /myqueue

# Terminal 2 — send a message
./sender /myqueue
```

> **Note:** POSIX message queues require linking with `-lrt` on some systems. The provided Makefiles handle this automatically where needed.

---

## 📋 Prerequisites

- **OS:** Linux
- **Compiler:** GCC
- **Libraries:** POSIX (`<sys/socket.h>`, `<sys/un.h>`, `<mqueue.h>`)

---

## 📚 Key Takeaways

| IPC Mechanism | Strengths | Trade-offs |
|---|---|---|
| **Unix Domain Sockets** | Low latency, bidirectional, familiar socket API | Local-only, requires FD management for concurrency |
| **POSIX Message Queues** | Kernel-buffered, decoupled sender/receiver, priority support | Fixed-size messages, system-wide queue limits |