![[3_(System_Boot).pdf]]

  

**Trap Tables:**

- The Routines are part of the kernel code
- **Definition**
    - A trap table is a data structure in memory set up by the OS, containing addresses of trap handlers.
- **Setup**
    - The OS initializes the trap table with pointers to various handlers.
    - It stores the table's location in a special CPU register (e.g., trap vector base register).
- **Operation**
    - When a trap occurs (e.g., system call, exception), the CPU:
        - Identifies the trap number.
        - Uses it as an index to locate the corresponding handler in the trap table.
        - Jumps to the handler to execute the required routine.
- **Purpose**
    - Enables **system calls** handling.
    - Manages **exceptions & faults** (e.g., division by zero, page faults).
    - Supports **hardware interrupts** (e.g., keyboard, network events)

  

# Secure Boot

After the CPU starts executing from an immutable ROM and UEFI/BIOS firmware loads from **SPI flash** on the motherboard. (Something like **Intel Boot Guard / AMD PSP** Cryptographically verifies UEFI firmware before execution.):

  

## **1. Hash Verification (Checking Bootloader Integrity)**

- A **hash** (e.g., SHA-256) is a fixed-size fingerprint of a file.
- UEFI stores **precomputed hashes** of trusted bootloaders in its **Signature Database (DB)**.
- When the system boots:
    1. UEFI **computes the hash** of the bootloader file.
    2. It checks if this hash is in the **DB** (trusted list).
    3. If the hash matches → Boot continues. If not → Boot fails.

✅ **Why?** Hash verification ensures that the bootloader **hasn’t been tampered with** (e.g., no malware injection).

---

## **2. Asymmetric Encryption (Signature Verification)**

Secure Boot also uses **digital signatures** to verify authenticity using **asymmetric encryption (public/private key cryptography).**

### **How It Works in Secure Boot:**

- The OS vendor (e.g., Microsoft, Ubuntu) signs bootloader files using a **private key**.
- The signature is stored alongside the bootloader.
- UEFI firmware has the **public key** in its **DB** (Signature Database).
- During boot:
    1. UEFI extracts the **digital signature** from the bootloader.
    2. It **decrypts** the signature using the **public key** (RSA/ECC).
    3. If decryption succeeds → It proves that the bootloader was signed by a trusted entity.
    4. If the signature is invalid → Boot fails.

✅ **Why?** Asymmetric encryption ensures that only bootloaders from **trusted sources** (e.g., Microsoft, Red Hat) can run, blocking unauthorized software.

**Common Algorithms Used:**

- **RSA-2048, RSA-4096** → Used for signature verification.
- **ECC (Elliptic Curve Cryptography)** → More efficient alternative to RSA.
- **SHA-256, SHA-512** → Hashing algorithms used in digital signatures.

---

## **3. TPM (Measured Boot & Remote Attestation)**

A **Trusted Platform Module (TPM)** is a secure hardware chip that helps verify system integrity.

### **How TPM Works with Secure Boot:**

- TPM does not enforce Secure Boot, but it **records boot measurements** for security auditing.
- Each stage of boot (UEFI, bootloader, OS) generates a **cryptographic hash** of its code.
- These hashes are stored in **Platform Configuration Registers (PCRs)** inside TPM.

✅ **Why?** This allows:

1. **Measured Boot** → The OS can check TPM logs to detect boot modifications.
2. **Remote Attestation** → A remote server can verify if the system booted securely before granting access (used in enterprise security).

**Example Use Case:**

- Windows BitLocker uses TPM to check if Secure Boot was bypassed. If the hashes don’t match, it refuses to decrypt the disk.