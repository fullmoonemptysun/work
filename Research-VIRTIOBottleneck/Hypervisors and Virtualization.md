1. Can be software, hardware or firmware, also known as VMM (virtual machine manager).

2. Types of virtualization include:
	- **Full virtualization**: All hardware required for guest os and apps is emulated. Guest systems don't know they're running on a vm
	- **Paravirtualizaton**: guest apps are executed in their own isolated domains as if they're running on a separate system but hardware environment is not simulated. Guest programs need to be specifically designed for vms.
	- **Hybrid v12n**: basically full v12n but utilized parav12n drivers to increase vm performance. 

## Full Virtualization
![[Pasted image 20250702233938.png]]
1. Pool physical computer resources into one or more instances. Each running a virtual environment where any software capable of execution on the raw hardware can be run in the virtual machine.

2. 2 types of full v12n techniques:
- ***Binary translation:-*** hypervisor dynamically modifies OS code (specially privileged instructions) on the fly to replace instructions that pierce the vm with a different, vm safe sequence of instructions. Was done when CPUs did not support virtualization directly. (Emulation)
- ***Hardware assisted full v12n:-*** Allows guest OS to run in isolation with virtually no modification to the guest OS. CPU supports virtualization and traps privileged instructions to the hypervisor.

>[!IMPORTANT] Some Explaination
>Earlier, when CPUs such as x86 did not support virtualization, binary translation was used to rewrite some instructions that do not trap in rings 1-3 (because hypervisor used ring 0 and so the guest kernel had to be run in 1-3). In these CPUs only privilege levels (0-3) existed. However, with newer CPUs that support virtualization, there is an additional privilege layer for Virtualization: VMX  root mode and VMX non root mode. 
>
>- Hypervisor sets a bit in a Model specific register to enable VMX operations
>- After this, the two modes are enabled (VMX root and VMX non root). 
>- VMX uses a special hardware managed data structure called the VMCS which stores the guest's state (registers, flags, segment selectors, etc.). 
>- Guest OS runs in ring 0 inside VMX non root mode. All privileged instructions that require interception cause VM exits (traps) to the hypervisor.
>- The hypervisor then handles these exits (perform, reject, etc.).

### Example to understand the exact difference between binary translation and Hardware-assisted virtualization:
#### 1. **Binary Translation (No VMX)**

- The hypervisor **scans guest code** and sees an instruction like:

    `MOV CR3, eax`

- Since this is a **privileged instruction** (modifies page tables), and **won’t trap** in Ring 1 or 3 (where guest runs), the hypervisor **replaces it** in a **code cache** with:

    `CALL hyper_emulate_mov_cr3`

- So now, the guest doesn’t execute the original `MOV CR3`, but instead calls a **hypervisor-provided function** that:
    - Validates access
    - Updates the virtual CR3 in the guest state
    - Handles any side effects

> ✔️ So yes, in **binary translation**, the CPU just executes the **replaced function call**, unaware that anything was modified.

---

#### 2. **Hardware-Assisted Virtualization (VMX)**

- The guest runs in **VMX non-root mode**, and **thinks** it's in Ring 0.
- When it executes:

    `MOV CR3, eax`

- The CPU checks the **VMCS control fields**, sees that `MOV to CR3` is a **trap-worthy instruction**, and performs a **VM exit**.

- On VM exit:
    
    - CPU **saves guest state** into the VMCS
    - **Loads the hypervisor’s state**
    - Jumps to a **predefined hypervisor entry point**
- The hypervisor now runs in **VMX root mode**, reads the **exit reason** ("MOV to CR3"), and can then call:

    `hyper_emulate_mov_cr3();`

- After it handles the instruction, it issues a **VMRESUME**, and the guest continues from the next instruction.

> ✔️ So yes, in **hardware-assisted virtualization**, the **CPU itself triggers the trap** and **transfers control** to the hypervisor to handle the instruction.


3. There are 2 types of hypervisors:
	- Bare metal: No host OS, just a hypervisor and guest machines. (used mostly in cloud infra)
	- Host kernel: Mostly client computers.





## Paravirtualization
1. Paravirtualization is a virtualization technique in which the OS is modified to be aware that it is running in Virtual Env. Instead of executing real hardware instructions, it uses explicit calls to the hypervisor (called hypercalls) to request privileged operations.
	- A slightly different type of OS <-> Hardware interface is introduced. 
	- OS does not interact directly with hardware as it would in full virtualization.
	- For the example above: `MOV CR3`, the OS code will directly call `hyper_set_cr3(new value)`.

2. Paravirtualization requires that the guest OS is specifically ported for a paravirtualizing VMM (must be aware.) conventional OS will not work on a parav12n vmm.

3. Even if the OS itself can't be changed, there are components available that can enable many of the performance benefits of parav12n.

# Containerization
Operating-system-level virtualization, also known as [containerization](https://en.wikipedia.org/wiki/Containerization_\(computing\) "Containerization (computing)"), refers to an [operating system](https://en.wikipedia.org/wiki/Operating_system "Operating system") feature in which the [kernel](https://en.wikipedia.org/wiki/Kernel_\(computer_science\) "Kernel (computer science)") allows the existence of multiple isolated [user-space](https://en.wikipedia.org/wiki/User-space "User-space") instances. Such instances, called containers,[[25]](https://en.wikipedia.org/w/index.php?title=Virtualization&_welcomesurveytoken=0ubm3rdimpmgmfqm0tg8lfrr9rrkr4vb&source=welcomesurvey-originalcontext#cite_note-25) partitions, virtual environments (VEs) or jails ([FreeBSD jail](https://en.wikipedia.org/wiki/FreeBSD_jail "FreeBSD jail") or [chroot jail](https://en.wikipedia.org/wiki/Chroot_jail "Chroot jail")), may look like (physical) computers from the point of view of programs running in them. A computer program running on an ordinary operating system can see all resources (connected devices, files and folders, CPU power, quantifiable hardware capabilities) of that computer. However, programs running inside a container can only see the container's contents and devices assigned to the container.

This provides many of the benefits that virtual machines have such as standardization and scalability, while using less resources as the kernel is shared between containers.






