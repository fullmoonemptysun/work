**1. What is VirtIO?**

- VirtIO is a framework for **paravirtualized I/O devices** in virtualized environments.
- It provides a **standardized software interface** between the guest and the hypervisor for efficient communication.
- Instead of emulating real hardware devices, VirtIO uses **shared memory** and **simple protocols** to avoid the overhead of emulation.

**2. What is virtio-blk?**
- `virtio-blk` is a **paravirtualized block device driver** in the guest OS.
- It is used for accessing block storage devices (like virtual disks) efficiently.
- The guest OS uses this driver to communicate with the hypervisor's backend disk handler through **virtqueues**.
- This replaces traditional disk drivers like IDE, SATA, or SCSI.

**3. What happens without VirtIO (full emulation):**
- The hypervisor (e.g., QEMU) **emulates a real hardware controller** such as IDE or SATA.
- The guest uses a **native hardware driver** (e.g., ata_piix) and executes **I/O instructions** like `IN`, `OUT`, `MOV`, etc.
- Each I/O instruction targeting hardware (e.g., I/O ports 0x1F0 for IDE) causes a **VM exit**.
- On each VM exit, the hypervisor must **emulate the hardware behavior**, update fake device registers, simulate interrupts, and more.
- This process results in **dozens or hundreds of VM exits per I/O operation**.
- The guest is unaware that it is in a virtualized environment and believes it is interacting with real hardware.
- After emulation, the hypervisor writes the data to the actual disk image file (e.g., `disk.img`).

**4. What happens with virtio-blk:**
- The guest uses the `virtio-blk` driver which knows it is communicating with a paravirtualized device.
- The guest allocates an I/O request in a **virtqueue**, which is a ring buffer in **shared memory** between the guest and host.
- The request includes operation type (e.g., write), data buffer address, and sector number.
- The guest may notify the hypervisor by writing to a memory-mapped I/O register (MMIO). This may cause one VM exit, or none if polling is used.
- The hypervisor reads the request from shared memory and performs the actual disk I/O on the disk image (e.g., `disk.img`).
- The result is placed back into the virtqueue and the guest is notified, possibly through an interrupt.
- No hardware emulation is required. The hypervisor is not simulating an IDE controller.

>[!ERROR] Related concept
> **RING BUFFER/CIRCULAR QUEUE**
> - Used in systems because no chance of segmentation fault. (no mallocs).
> - Basically array size, a head and a tail.
> - Once head becomes one less that tail, it wraps around to the start (0)


![[Pasted image 20250705201405.png]]
How virtio works
1. **VM:** I want to go to google.com. Hey virtio-net, can you tell the host to retrieve this webpage for me?
2. **Virtio-net:** Ok. Hey host, can you pull up this webpage for us?
3. **Host:** Ok. I’m grabbing the webpage data now.
4. **Host:** Here’s the requested webpage data.
5. **Virtio-net:** Thanks. Hey VM, here’s that webpage you requested.


## VirtIO architecture
![[Pasted image 20250705201457.png]]

1. VirtIO Drivers (live inside the kernel as modules) for each virt device are the front end to this architecure. Their job:
	- accept I/O requests from user processes
	- transfer those I/O requests to the corresponding back-end virtIO device
	- retrieve completed requests from its virtIO device counterpart.

**Example**
> 	For example, an I/O request from virtio-scsi might be a user wanting to retrieve a document from storage. The virtio-scsi driver accepts the request to retrieve said document and sends the request to the virtio-scsi device (back-end). Once the VirtIO device has completed the request, the document is then made available to the VirtIO driver. The VirtIO driver retrieves the document and makes it available to the user.


2. VirtIO devices(exist in the hypervisor and run inside the hypervisor process) are the back end. Their job:
	- accept I/O reqs from virtIO drivers
	- handle requests by offloading the I/O operations to the host's physical hardware
	- make the processed requested data available to the virtIO driver.

**Example**
>Returning to the virtio-scsi example; the virtio-scsi driver notifies its device counterpart, letting the device know that it needs to go and retrieve the requested document in storage on actual physical hardware. The virtio-scsi device accepts this request and performs the necessary calls to retrieve the data from the physical hardware. Lastly, the device makes the data available to the driver by putting the retrieved data onto its shared _VirtQueue_.


## VirtQueues
1. Data structures that assist devices and drivers in performing various _VRing_ operations. 
2. Shared in guest physical memory and each driver-device pair access the same page in memory.
3. The main feature is the VRings, they are the actual data structure that help the transfer of data between a device and a driver. 

![[Pasted image 20250707121021.png]]
Qemu's VirtIO framework structure

4. Also contains the use of various flags, indices, and handlers (call back functions). All of these are used for VRing operations one way or the other.
5. The organization of a VirtQueue is specific to the guest OS and whether the virtIO devices live in the hypervisor (like above) or in the host kernel.

6. Operations on virtQueues are also specific to the VirtIO configuration.

![[Pasted image 20250708092657.png]]
Linux kernel's VirtQueue version

## VRings
1. Core data structure in VirtQueues.
2. Hold the actual data being transferred.
3. Essentially arrays (queues) that wrap back around to the beginning of itself once the last entry was written to.
4. Also known as _areas_.

5. Each VirtQueue usually has up to 3 types of VRings:
	- Descriptor ring (descriptor area)
	- Available ring (driver area)
	- Used ring (device area)


## Descriptor Ring 
