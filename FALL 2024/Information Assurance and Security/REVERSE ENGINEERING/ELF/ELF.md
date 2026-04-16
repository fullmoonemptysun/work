# Sections and Segments

1. Sections gather all needed information to link a given object file and build an executable, while Program Headers split the executable into segments with different attributes, which will eventually be loaded into memory.
2. Segments can be seen as a tool to make the linux loader’s work easier. It just groups sections by attributes into single segments in order to make loading process more efficient. (instead of loading each section individually in memory)

  

![[Image5.png]]

  

_**PERSONAL NOTES ON ELF:**_

1. ELF files are made of three major components:
    1. ELF Header
    2. Sections
    3. Segments

  
2. **ELF Header:**

- Contains general information about the binary itself.
- The ELF header is denoted by an Elfxx_Ehdr struct. Mainly, this contains general information about the binary. Definitions of these structure’s fields are the following:

![[Image1.png]]

- e_ident: Array of 16 bytes containing identification flags about the file, which serve to decode and interpret file’s contents:
    - Magic
    - File Class
    - File’s data encoding
    - File’s version
    - ABI version
    - Start of padding bytes
    - Size of ei_ident
- e_type: Type of executable
- e_machine: file’s architecture
- e_version: object file version
- e_entry: entry point of the application
- e_phoff: file offset of the program header table
- e_shoff: file offset of the section header table
- e_flags: processor-specific flags associated with the file
- e_ehsize: ELF header size.
- e_phentsize: Program Header entry size in Program Header Table.
- e_phnum: Number of Program Headers.
- e_shentsize: Section Header entry size in Section Header Table.
- e_shnum: Number of Section Headers.
- e_shstrndx: index in Section Header Table Denoting Section dedicated to Hold Section names.

To see the ELF headers, we can do `readelf -h`

  
3. **ELF Sections:**

- Present in every ELF
- All info needed for linking a target object file in order to build a working executable.
- Needed during linktime, not during runtime.
- The header table is an Array of structs, with one entry per section.
    
    ![[Image2.png]]
    
- The fields are as follows:
    - sh_name: index of section name in section header string table.
    - sh_type: section type.
    - sh_flags: section attributes.
    - sh_addr: virtual address of section.
    - sh_offset: section offset in disk.
    - sh_size: section size.
    - sh_link: section link index.
    - sh_Info: Section extra information.
    - sh_addralign: section alignment.
    - sh_entsize: size of entries contained in section.
- Some common sections:
    - .text: code.
    - .data: initialised data.
    - .rodata: initialised read-only data.
    - .bss: uninitialized data.
    - .plt: PLT (Procedure Linkage Table) (IAT equivalent).
    - .got: GOT entries dedicated to dynamically linked global variables.
    - .got.plt: GOT entries dedicated to dynamically linked functions.
    - .symtab: global symbol table.
    - .dynamic: Holds all needed information for dynamic linking.
    - .dynsym: symbol tables dedicated to dynamically linked symbols.
    - .strtab: string table of .symtab section.
    - .dynstr: string table of .dynsym section.
    - .interp: RTLD embedded string.
    - .rel.dyn: global variable relocation table.
    - .rel.plt: function relocation table

To see the section headers we can do `readelf -S`

1. **ELF Segments:**

- AKA, program headers
- break down the ELF into chunks to prepare the executable to be loaded into memory
- Not needed on linktime
- Also a struct:
    
    ![[Image3.png]]
    
- The fields are as follows:
    - p_type: Segment type.
    - p_flags: Segment attributes.
    - p_offset: File offset of segment.
    - p_vaddr: Virtual address of segment.
    - p_paddr: Physical address of segment.
    - p_filesz: Size of segment on disk.
    - p_memsz: Size of segment in memory.
    - P_align: segment alignment in memory.
- Common types:
    - PT_NULL: unassigned segment (usually first entry of Program Header Table).
    - PT_LOAD: Loadable segment.
    - PT_INTERP: Segment holding .interp section.
    - PT_TLS: Thread Local Storage segment (Common in statically linked binaries).
    - PT_DYNAMIC: Holding .dynamic section.