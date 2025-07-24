  

# Properties:

## Communication: Confidentiality

![[image 25.png|image 25.png]]

Malory can intercept and we want to keep the data confidential.

  

## Integrity:

![[image 1 6.png|image 1 6.png]]

Guarantee that the data has not been changed.

  

## Authenticity:

![[image 2 7.png|image 2 7.png]]

Has alice actually sent this message? or is malory just saying they did?

  

1. Cryptography helps use make these guarantees.

  

  

# Symmetric Encryption:

## Encryption

![[image 3 7.png|image 3 7.png]]

## Decryption

![[image 4 5.png|image 4 5.png]]

  

1. One good example of symmetric encryption is one time padding (OTP):

![[image 5 5.png|image 5 5.png]]

  

1. Issue: Have to share the key first securely

  

## Encryption Properties:

1. Confusion: Each bit of the cipertext depends on several parts of the key. Obscuring the connections between the two. Basically, a little bit of change in key will have major impact on the cipher.
2. **Diffusion:** Change in the plain text has a major impact on the Cipher Text.

  

## Advanced Encryption Standard (AES) Scheme:

![[image 6 5.png|image 6 5.png]]

  

  

Enctyption/Decryption with AES:

![[image 7 4.png|image 7 4.png]]

1. Properties of AES:

- Substituiton Permutation Network
- Key Size: (128/192/256) - bits how long the key is does not depend on data size.
- Block Size: 128 bits - 16 byte chunks. 128 bit data gets ciphered into 128 bit data. 13 bytes wont work, must pad it with 0s.

![[image 8 4.png|image 8 4.png]]

- The problem with this approach is that there is no way to know how many bytes are padded and which ones are meant for the actual content.
- Solution: Padding with PKC#7: Padd with the number of padding instead of 0s. Example: if three bytes of padding, pad with 030303.
    
    ![[image 9 4.png|image 9 4.png]]
    
- Can decrypt and encrypt in this way.

  

- What if the data is bigger?:
    - Just pad the data to make it a multiple of 16 bytes
    - Split it into 16 byte pieces
    - Encrypt/Decrypt

  

## Electronic Codebook (ECB):

![[image 10 4.png|image 10 4.png]]

  

1. Same key is used to encrypt/decrypt blocks of data.
2. Problem with ECB: Same plain text will give same cipher text which can give us patterns, which we don’t want in cryptography.
    
    ![[image 11 4.png|image 11 4.png]]
    
    We know its a penguin.
    
    Solution…:
    

  

## Cipher Block Chaining (CBC):

![[image 12 4.png|image 12 4.png]]

1. Initialization vector is a random 16 byte vector that works as a ciphertext for the first block.
2. After the 1st, each ciphertext is taken to the next block and XORed with the data as a vector.
3. Initialization vector must be provided with encrypted data
4. Performance is low with big data, because encryption is sequential after each block.

  

Solution….:

  

## Counter (CTR) Method:

![[image 13 4.png|image 13 4.png]]

1. Start with random bits and increase it every block.
2. Encrypt these bits and then XOR with plain text.
3. Blocks can run in parallel.

  

  

# **Key Exchange:**

A. How do we exchange keys securely?

B. Physically meet and exchange keys. (Not viable)

  

## Paint Exchange:

1. Alice has a private blue
2. Bob has a private red
3. They agree on a public yellow
4. They share yellow, mallory intercepts and gets yellow
5. Alice and Bob mix their private colors with yellow, green, and orange
6. Alice and bob share these new colors, now Alice has Green, blue, yellow and orange, Bob has Red, Yellow, Orange and Green.
7. Mallory has Green, Orange and yellow
8. Now alice and bob can mix the new colors with their private colors to create the same color on both sides, which mallory will never know about.

  

## Commutative Property:

1. An operation is commutative if and only if x o y = y o x for each x and y

  

1. **Interesting properties:**
    
    - 7^n % 13 : Prime modulus of a number raised to some power
    - This will give use all the numbers in n % 12, but in a chaotic order.
    - Base is carefully chose (7) does not work with all bases. Perimetric roots
    - Discrete log problem is going back to the original number. Very hard to decrypt.
    - We can have a very large prime number and it becomes almost impossible to brute force.
    
      
    

## Diffie-Hellman Key Exchange:

1. Change the paint exchange with math
2. Alice and Bob publicly agree to use a modulus p and base g, where p is prime and g is a primitive root modulo p.
3. Alice chooses a secret integer a then sends bob A = $g^a \mod p$
4. Bob does the same thing with B = $g^b \mod p$
5. Alice computes s = $B^a \mod p$
6. Bob computes s = $A^b \mod p$
7. Now Alice and Bob have a common s.
8. This works because of commutative laws that they end up with a common s.
9. Mallory cannot decrypt A and B into a and b, even alice and bob cannot decrypt it. All they can do is create a common key s. Mallory cannot even do that because you need a or b for that. How will you know what x is given, $7^x \mod 13 = k$. This is the **Discrete log problem.**

![[image 14 4.png|image 14 4.png]]

  

  

  

  

# **Asymmetric Encryption:**

1. Conept of public and different private keys.
2. With public key: only encrypt. To decrypt, must have private key.

  

## Fermat’s Little Theorem

For $7 ^ n \mod 13$

1. $a^p \cong a$ (mod p), where is p is prime
2. Basically, $a^p \mod p$ is the same as $a \mod p$

  

1. We can also say:
    
    $a ^ {(p-1)} \cong 1$, p is prime, a doest not divide p
    
      
    

## Euler’s Theorem

$a^{(p-1)(q-1)} \cong 1$ (mod pq), where p is prime and q is prime.

⇒ $a^{(p-1)(q-1)+1} \cong a$ (multiply bs by a)

  

$a^r \cong a$ (mod pq)

where $r \cong 1$ (mod (p-1)(q-1))

  

## RSA (Rivest Shamir Adlemen)

1. m: message
2. $(m^e)^d \cong m$ (mod n)
    
    $n = pq$, p is prime and q is prime
    
    $ed \cong 1$ (mod (p-1)(q-1))
    
    ![[image 15 4.png|image 15 4.png]]
    

  

## RSA Key Generation:

1. Choose 2 large prime numbers p and q
2. Compute n = pq
3. Compute $\phi (n) = (p-1)(q-1)$
4. Choose e such that gcd(e, \phi(n)) = 1 (they are co prime)
    
    most common value of e = 0x10001 - 65537
    
5. Compute $d = e^{-1} (mod \phi(n))$

  

## RSA Encryption:

![[image 16 3.png|image 16 3.png]]

**Quick RSA math:**

- $m \equiv (m^e)^d$ (mod n)
- $(m^e)^d \equiv m^{ed} \equiv (m^d)^e$ (mod n)
- $(m^d)^e \equiv m$ (mod n)

  

**RSA Signing:**

- $s \equiv m^d$ (mod n)

where, s is signature, d is priv key exponent.

Now this can give us a way to verify that the signature was created by someone with the private key:

  

**RSA Signature Verification:**

- Confirm by $m \equiv s^e$ (mod n)

  

  

# **Hashing**

![[image 17 3.png|image 17 3.png]]

1. Example: Put in lorem ipsum, out comes a hashed data.
2. Changing a little bit of the message will have an avalanche affect on the hash value.
3. It is one way, no decryption.

  

## Resistance Properties:

1. **Pre image resistance:** Given a hash value h, it should be difficult to find any input message m such that h = hash(m)
2. **Second pre image resistance:** Given an input message m1 it should be difficult to find a different input message m2 such that hash(m1) = hash(m2)
3. **Collision Resistance:** It should be difficult to find two different input messages m1 and m2 such that hash(m1) = hash(m2)

  

## SHA256

![[image 18 3.png|image 18 3.png]]

Imagine a blender that will hash something and hold the above properties

## Applications:

1. **Password Hashing:** someone entered their password for account registration > we do not want to store the password in the DB, so we store the hashed version. This hash cannot yield the original password.
    
    **Password Cracking:** table of commonly used passwords with their hashes and apply. (john)
    
    **Password Hashing with salt:** Instead of just having the hash by itself add a bunch of random bits, in this case, ‘s4lt’
    
      
    
    [https://docs.google.com/presentation/d/1_DJHHllHplxalW8DCgR-BPF9D_PJxoozxbLzrVYCe18/edit#slide=id.g148e71449ac_0_0](https://docs.google.com/presentation/d/1_DJHHllHplxalW8DCgR-BPF9D_PJxoozxbLzrVYCe18/edit#slide=id.g148e71449ac_0_0)
    
2. **Proof of work:** Crypto Mining:

- challenge is received, could be anything
- need to add something to the challenge so that the hashed output has some properties, e.g: starts with 4 0s or ends with something, etc.
- Brtueforce and run thousands and millions of computations to find these.
- Earn some cryptocurrency for the computing power.

  

  

  

# Challenge Notes:

**General Remarks:**

1. XOR is very often used in crypto because it is self inversed.
2. We use ASCII encoding for the latin alphabet, it is as follows, each character is 1 byte
    - uppercase letters are `0x40+letter_index` (e.g., A is `0x41`, F is `0x46`, and Z is `0x5a`), lowercase letters are `0x60+letter_index` (a is `0x61`, f is `0x66`, and z is `0x7a`), and numbers (yes, the numeric characters you're seeing are _not_ bytes of those values, they are ASCII-encoded number characters) are `0x30+number`, so 0 is `0x30` and 7 is `0x37`. Newline is 0x0a and like that…

  

1. ASCII STRINGS: `from Crypto.Util.strxor import strxor` is PyCryptodome. The strxor function helps us xor strings directly (byte by byte).  
      
      
    `strxor(b"AAA", b"16/")`
    
      
    
    - b” “ means raw byte data. When we do b”AAA” since python understands that this is A and it is \x41 in byte data, it will take it as valid.
    - For characters that are not visible, its better to use the bytes as b“\xsomething”, this is usually how non ascii byte data is represented.
    
      
    
    - Key Differences between Unicode and byte raw data
    
    |   |   |   |
    |---|---|---|
    |Feature|**Unicode String (**`**str**`**)**|**Byte String (**`**b""**`**)**|
    |**Type**|`str`|`bytes`|
    |**Represents**|Human-readable text (characters)|Raw binary data (bytes)|
    |**Encoding**|Abstract representation (Unicode code points)|Already encoded as bytes (e.g., UTF-8, ASCII)|
    |**Usage**|Used for text processing|Used for raw data or encoded text|
    |**Character Range**|Supports any Unicode character|Supports byte values (0-255) only|
    |**Example**|`"Hello, 世界"`|`b"Hello, \xe4\xb8\x96\xe7\x95\x8c"`|
    |**Conversion**|Can be encoded to bytes (using `.encode()`)|Can be decoded to a Unicode string (using `.decode()`)|
    
      
    
    > [!important] **Unicode is a character set that maps lot of characters to numbers. It has different encoding schemes such as utf - 8 that can use 1-4 bytes to store a single unicode character.**
    > 
    >   
    > 
    > **ASCII on the other hand, is also a character set that maps only 128 characters. The encoding scheme used is also called ASCII only. It uses 1 byte to store one character.**
    > 
    >   
    > Thus, Unicode is like an extended set of ASCII that can use more than 1 byte per character. (The first 128 characters in unicode are ASCII.)  
    
      
    

  

  

**AES-XXXXX: Advanced encryption standard scheme**

1. AES-ECB(Electronic codebook)-CPA(controlled plaintext attack): Write a python script that gets the mapping for each character by encrypting them one at a time, then start comparing each character of the flag to the map and then decrypt them.

  

1. AES-ECB-CPA-HTTP: Python script, requests and regex

  

1. AES-ECB-CPA-PREFIX

- **Element Count**: The `16` refers to the 16th byte from the end of the byte string, and Python treats it as such regardless of the data type.
- **Byte Strings vs. Strings**: While the contents are bytes, the slicing operation itself works the same way as with regular strings or lists. Each byte is treated as a single element, so `[-16:]` effectively gives you the last 16 bytes of the byte string.
- PLEASE LOOK AT NOTES AND SYNTAX. DAYS WERE WASTED.

  

1. AES-ECB-CPA-PREFIX 2:

- Is a little complicated. The codebook formation needs to be modified too apart from slight changes in the main code.
- Once numBytes ≥ 16 which means we have pushed 16 bytes (1 block), after that, we write 16 byte blocks for each iteration in the codebook and so we receive encryption with the padding at the end, which needs to be trimmed before storing in the codebook.

  

1. AES-ECB-CPA-PREFIX-MINIBOSS:
    
    Doubt : Why do we get b’ ‘ byte object after encoding a byte object in base64. (Not really encoding):
    
    Just like **hex** is a way to represent byte values using the characters `0-9` and `A-F`, **Base64** is another **notation** (or encoding) that represents byte values using its own 64-character set (like `A-Z`, `a-z`, `0-9`, `+`, `/`). In both cases, you're just changing how the **same underlying data** is represented.
    
    - **Hex** notation represents byte values in a base-16 system.
    - **Base64** notation represents byte values in a base-64 system.
    
    The important thing is: **the underlying value stays the same**—you're just changing how it's displayed.
    
    For example:
    
    - The byte value `\xbb` in **hex** is `bb`.
    - The byte value `\xbb` in **Base64** is represented as `uw==`.
    
    They all represent the **same binary data** (`10111011`), just with different notations!
    
      
    
    - IMPORTANT: **yes**—when you **Base64 encode** a string and then **decode** it, you will get back the original **byte values** of the characters, which correspond to their **ASCII values** in hexadecimal. **But in these challenges, base64 is not the only thing that happens. Plain text is also encrypted with a key. Bse64 is just notation.**
    
      
    
    - Solution: