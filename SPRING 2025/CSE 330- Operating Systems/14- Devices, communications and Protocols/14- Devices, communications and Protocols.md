![[14_(Devices_communication_and_protocols)-compressed.pdf]]

CPU polling vs interrupts:  
  

### **Polling**

- CPU actively and repeatedly checks the device’s status register to see if it is ready.
- Common in simple systems or early OS designs.
- The CPU stays in a loop, doing nothing else while waiting.
- This causes:
    - CPU cycle waste
    - Poor multitasking
    - High energy usage
- It blocks the CPU from doing other tasks while waiting for I/O.
- Usually avoided in multitasking operating systems.

---

### **Interrupts**

- The device sends an interrupt signal to notify the CPU when it’s ready or needs attention.
- The CPU stops its current task, handles the device through an interrupt service routine, and then resumes.
- This allows:
    - Efficient CPU usage
    - Better multitasking
    - Lower latency and power consumption
- Requires more setup (interrupt vectors, handlers), but is much better for modern OS design.