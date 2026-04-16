Not a one-off action like sending data.

**What happens when you call `server.listen(3000)`:**

1. Node.js asks the OS to **open a socket** and bind it to port 3000
2. The OS registers that socket in its **connection table** — basically a lookup table of "which process owns which port"
3. The OS creates an **incoming connection queue** (called the backlog) for that port
4. Your process tells the OS "notify me when something arrives" and then **suspends itself** — this is why the terminal hangs

**When a client connects:**

1. A packet arrives at the network card and travels up the stack (Physical → Link → IP → TCP) as normal
2. TCP layer checks the destination port (3000) against the OS connection table
3. OS finds your process owns port 3000, places the connection in the queue
4. OS **wakes your process up** via an interrupt/event notification
5. Your callback fires with `req` and `res`

**Why the terminal stays open:**

Your process isn't actually doing anything — it's just registered with the OS as the owner of port 3000. The OS is doing the real waiting. The terminal stays open because the process hasn't exited. Node.js specifically runs an **event loop** that keeps the process alive as long as there are active listeners.

So listening is essentially: _"OS, hold this port for me and wake me up when something comes in."_ The process itself is mostly idle in between.