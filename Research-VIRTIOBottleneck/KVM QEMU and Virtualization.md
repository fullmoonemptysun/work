## QEMU + KVM + VirtIO: Simple Explanation

### What is KVM?

- KVM is a Linux kernel module.
- It turns the Linux kernel into a hypervisor using hardware virtualization (VT-x, AMD-V)
- It creates VMs, vCPUs, and runs guest code in VMX/secure mode.
- It exposes a device file: `/dev/kvm`.
- Userspace (like QEMU) interacts with it using `ioctl()` system calls.
---
### What is QEMU?
- QEMU is a machine emulator and virtualizer.
- When used with KVM, it acts as:
    - A controller for creating and managing VMs
    - A device emulator (NICs, disks, USB, etc.)
- QEMU talks to `/dev/kvm` to manage VM state and enter/exit guest code.
---
### What is VirtIO?

- VirtIO is a standard for **paravirtualized devices**.
- It's used for **efficient communication between guest and host**.
- Instead of emulating a real device, guest and host share memory buffers.
- Used for devices like disk (`virtio-blk`), network (`virtio-net`), etc.
- Guest must have VirtIO drivers (Linux includes them).
---
## Step-by-Step Flow (QEMU + KVM + VirtIO)

1. QEMU opens `/dev/kvm`  
    → checks KVM version
2. QEMU creates a VM  
    → `ioctl(KVM_CREATE_VM)`
3. QEMU allocates guest RAM using `mmap`  
    → tells KVM with `KVM_SET_USER_MEMORY_REGION`
4. QEMU creates vCPUs  
    → `ioctl(KVM_CREATE_VCPU)`
5. QEMU sets vCPU state (registers, CPUID, etc.)  
    → `KVM_SET_REGS`, `KVM_SET_SREGS`, etc.
6. QEMU enters guest loop  
    → `ioctl(KVM_RUN)`  
    → KVM runs guest CPU directly
7. When guest uses VirtIO devices:  
    → Guest writes to shared virtqueue  
    → KVM exits to QEMU  
    → QEMU reads request, processes it (e.g., disk write)  
    → QEMU returns to guest with `KVM_RUN`
    
---
## Without VirtIO (Legacy Emulation)

- If VirtIO is not used:
    
    - QEMU must **emulate full hardware** (e.g., an IDE controller or RTL8139 NIC).
    - Guest thinks it's running on real hardware.
    - All I/O is **trapped and emulated slowly**.
- Performance is much worse
    - More VM exits
    - Slower disk/network


## What is a vCPU?

- A vCPU represents **one virtual processor** in the guest.
- KVM creates one vCPU per core with `KVM_CREATE_VCPU`.
- QEMU creates one thread per vCPU.
- Each vCPU stores:
    - General-purpose registers (RAX, RIP, etc.)
    - Segment registers
    - Control registers (CR3, CR0)
    - APIC state (for interrupts)
- QEMU uses `KVM_RUN` to enter guest code for each vCPU
- vCPUs are scheduled on real CPUs by the Linux scheduler.
    

---

## Code Example (Minimal Toy VM with KVM)


```
// Simple toy VM: sets up 1 vCPU, no devices
#include <fcntl.h>
#include <linux/kvm.h>
#include <sys/ioctl.h>
#include <sys/mman.h>
#include <stdio.h>
#include <stdint.h>
#include <string.h>
#include <unistd.h>

#define GUEST_MEM_SIZE 0x1000

int main() {
    int kvm = open("/dev/kvm", O_RDWR);
    int api = ioctl(kvm, KVM_GET_API_VERSION);

    int vm = ioctl(kvm, KVM_CREATE_VM, 0);

    // Allocate guest RAM
    void *guest_mem = mmap(NULL, GUEST_MEM_SIZE, PROT_READ | PROT_WRITE,
                           MAP_PRIVATE | MAP_ANONYMOUS, -1, 0);
    struct kvm_userspace_memory_region memreg = {
        .slot = 0,
        .guest_phys_addr = 0x1000,
        .memory_size = GUEST_MEM_SIZE,
        .userspace_addr = (uint64_t)guest_mem,
    };
    ioctl(vm, KVM_SET_USER_MEMORY_REGION, &memreg);

    // Create vCPU
    int vcpu = ioctl(vm, KVM_CREATE_VCPU, 0);
    int mmap_size = ioctl(kvm, KVM_GET_VCPU_MMAP_SIZE, 0);
    struct kvm_run *run = mmap(NULL, mmap_size, PROT_READ | PROT_WRITE,
                               MAP_SHARED, vcpu, 0);

    // Set guest code: infinite loop
    uint8_t code[] = { 0xf4 }; // hlt
    memcpy(guest_mem + 0x1000, code, sizeof(code));

    struct kvm_regs regs = {
        .rip = 0x1000,
        .rflags = 0x2,
    };
    ioctl(vcpu, KVM_SET_REGS, &regs);

    // Run guest
    printf("Running guest...\n");
    ioctl(vcpu, KVM_RUN, 0);

    if (run->exit_reason == KVM_EXIT_HLT) {
        printf("Guest halted\n");
    } else {
        printf("Unexpected exit: %d\n", run->exit_reason);
    }

    return 0;
}

```




This toy VM:

- Allocates memory
- Sets a single instruction (`hlt`)
- Starts the guest using `KVM_RUN`
- Exits when the guest halts

---

## Summary Notes

- KVM = fast CPU/memory virtualization (in kernel)
- QEMU = userspace VM manager and device emulator
- VirtIO = efficient way to handle guest I/O using shared memory
- vCPU = each virtual core of the guest, backed by a host thread
- Without VirtIO = legacy device emulation, slower
- With VirtIO = fewer exits, better performance