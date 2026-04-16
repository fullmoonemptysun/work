# Modules: What? install? load????

### **Kernel Modules:**

### **1. What Are Kernel Modules?**

- Dynamically loadable pieces of code (`.ko` files) that extend kernel functionality.
- Used for **device drivers, filesystems, networking**, etc.

### **2. Installing vs. Loading a Module**

|   |   |
|---|---|
|**Action**|**What Happens?**|
|**Installing**|Copies `.ko` file to `/lib/modules/<kernel-version>/`.|
|**Loading**|Inserts the module into the running kernel (`modprobe`).|

### **3. How Modules Work with the Kernel**

- Modules **register themselves** with the kernel (e.g., a driver for a new device).
- Kernel **allocates memory** for the module when loaded.
- Modules can interact with kernel functions via **exported symbols**.

### **4. Common Commands**

- **List loaded modules:** `lsmod`
- **Load a module:** `sudo modprobe <module_name>`
- **Unload a module:** `sudo modprobe -r <module_name>`
- **Manually insert a module:** `sudo insmod <path-to-module.ko>`
- **Prevent a module from loading:** Add to `/etc/modprobe.d/blacklist.conf`

### **5. What Happens When a Module Loads?**

1. Kernel loads and allocates memory.
2. The module’s `module_init()` function runs.
3. It registers itself (e.g., as a driver, filesystem, or protocol).
4. Kernel starts using it.

### **6. Example: GPU Driver (**`**nvidia.ko**`**)**

1. `modprobe nvidia` loads the module.
2. It registers as a GPU driver.
3. Xorg/Wayland detects it and enables **hardware acceleration**.
4. **These modules have the code to directly interact with the specific hardware.**