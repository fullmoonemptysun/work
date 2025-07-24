![[2_(Design_privileges_concepts).pdf]]

1. In a microkernel, system services like file management, networking, and device drivers run as user-space programs (servers) instead of inside the kernel. These servers communicate via IPC (inter process communication), improving modularity and fault isolation but adding performance overhead.