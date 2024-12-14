## Walkthrough of Linux Kernel 6.1.66's Kernel Decompression Code

#### startup\_32 (linux/arch/x86/boot/compressed/head\_64.S:75)

```txt
Control Flow:
startup_32 <-- Here

93-96: Calculates the physical address of startup_32 by subtracting
       the offset of the label 1 relative to startup_32 from label
       1's physical address.

       The easiest way to understand this is to use an example.
	   If you know that label 1 is 16 bytes from startup_32 and you
       know label 1's physical address, then to obtain the physical
       address of startup_32 you simply subtract label 1's physical
       address by 16.

99-101: Loads the GDT defined on line 724 using a 32-bit descriptor
        contained in its null entry. This can be potentially confusing
        if you are used to reading source code where the null entry
        is actually null.

104-112: Updates the segment registers and stack pointer.

114-118: Updates the code segment register.

121: Calls startup32_load_idt.

     Initializes the  VMM Communication Exception for SEV-ES. If the
     kernel is not running SEV-ES, this function call is a no-op
     that just returns.

124: Calls verify_cpu.

     Checks if the CPU supports longmode using the CPUID instruction
     and identifies the processor we are running on.

138-149: Assigns the physical address of startup_32 to %ebx.

152-156: Aligns %ebx to the kernel alignment value in the boot parameters
         (2MB default).

164-165: Assigns the target relocation address for decompression to
         %ebx.

172-174: Enables Page Address Extension (PAE).

         Reference: https://wiki.osdev.org/PAE

184: Calls get_sev_encryption_bit.

     Assigns the sev encryption bit into the %eax register if it exists.

187-190: Assigns the sev encryption mask to %edx if it exists.

203-206: Initializes 6 kernel page tables to zero.

209-212: Initializes the first entry in the Level 4 page table to point
         to the Level 3 page table with user, present, RW set.

215-223: Initializes the first four entries in the Level 3 page table
         to point to the 4 subsequent kernel page tables with user,
         present, RW set. 

226-234: Initializes the first two Level 2 page tables, or equivalently
         the first 2048 Level 2 page table entries, with present, RW,
         PS, and AVL set.

         For the meaning of the paging flags consult:
         https://wiki.osdev.org/images/1/1e/Page_directory_entry.png

237-238: Loads the address of the Level 4 page table into the CR3
         register.

241-244: Sets Long Mode bit in the Extended Feature Enable Register

         rdmsr - Reads the contents of a 64-bit model specific register (MSR)
                 specified in the ECX register into registers EDX:EAX.
                 (https://www.felixcloutier.com/x86/rdmsr)

         wrsmr - Writes the contents of registers EDX:EAX into the 64-bit mode
                 specific register (MSR) specified in the ECX register.
                 (https://c9x.me/x86/html/file_module_x86_id_326.html)

247-250: Loads the boot LLDT and TSS (Task-state Segment).

286: Calls startup32_check_sev_cbit.

262-293: Enters long mode and calls startup_64.
```

#### startup32\_load\_idt (linux/arch/x86/boot/compressed/head\_64.S:914)

```txt
Control Flow:
startup_32
    startup32_load_idt <-- Here

917-919: Initializes the VMM Communication exception.

922-924: Loads the IDT table defined on line 753 with the IDT
         descriptor defined on line 748.
```

#### startup32\_set\_idt\_entry (linux/arch/x86/boot/compressed/head\_64.S:881)

```txt
Control Flow:
startup_32
    startup32_load_idt
        startup32_set_idt_entry <-- Here

886-888: Assigns the address of the IDT entry to %ebx. We left-shift the
         exception number %edx by 3 since each IDT entry is 8 bytes long.

891-906: Builds the IDT entry that dispatches the interrupt to the
         startup32_vc_handler routine.
```

#### verify\_cpu (linux/arch/x86/kernel/verify\_cpu.S:34)

```txt
Control Flow:
startup_32
    startup32_load_idt
        startup32_set_idt_entry
        verify_cpu <-- Here
```

#### startup32\_vc\_handler (linux/arch/x86/boot/compressed/mem\_encrypt.S:101)

```txt
No Control Flow: Looking at this for curiosity's sake
```

#### get\_sev\_encryption (linux/arch/x86/boot/compressed/mem\_encrypt.c:18)

```txt
Control Flow:
startup_32
    startup32_load_idt
    verify_cpu
    get_sev_encryption <-- Here
```

#### startup32\_check\_sev\_cbit (linux/arch/x86/compressed/head\_64.S:930)

```txt
Control Flow:
startup_32
    startup32_load_idt
    verify_cpu
    get_sev_encryption
    startup32_check_sev_cbit <-- Here
```

#### startup\_64 (linux/arch/x86/boot/compressed/head\_64.S:336)

```txt
Control Flow:
startup_32
    ...
    startup_64 <-- Here

352-357: Sets all data registers to 0.

374-386: Assigns the physical address of startup_32 to %rbp.

389-393: Aligns %rbp to a 2MB boundary.

397: Assigns LOAD_PHYSICAL_ADDR to %rbp if the physical address of
     startup_32 is greater than LOAD_PHYSICAL_ADDR.

401-403: Assigns target address for decompression to $rbx.

406: Sets up the kernel stack.

434-436: Loads the gdt64 table defined on line 719.

439-444: Reloads the code segment register.

447: Loads the boot IDT (line 740) with the VMM Communication exception
     initialized. Note that this is the C code version of the IDT
     initialization code used in startup_32, and it is defined on line 7
     of /arch/x86/boot/compressed/idt_64.c

463: Calls sev_enable.

482: Calls paging_prepare.

482-486: Initializes the 32-bit trampoline and assigns its address to
         %rcx.

492: Loads the relative offset of trampoline_return from %rip into
     %rdi, a destination pointer.

495-498: Long returns to the 32-bit trampoline code to switch to
         compatibility mode (CS.L = 0 CS.D = 1).

         The trampoline will use %rdi to return to the main code at
         line 499, so we can continue without considering the details
         of the trampoline code.

501: Switches to the boot stack.

513: Assigns the address of the top kernel page table to %rdi.

514: Restores the trampoline's memory if it contained the top level
     kernel page table at its base.

525-531: Moves the compressed kernel to the end of the buffer so it
         can safely be decompressed.

         leaq (_bss-8)(%rip),%rsi ==> %rsi = physical address of _bss-8

         leaq rva(_bss-8)(%rbx),%rdi ==> %rdi = physical address of the
                                                end of the decompress
                                                buffer

         Note that this can be confusing if you are not aware of x86-64's
         RIP relative addressing and how it specifies PHYSICAL addresses
         when used with symbols like _bss.

         Furthermore, this is hard to understand if you do not remember
         that %rbx is the target decompression address. When we add
         rva(_bss-8) to %rbx we are specifying the end of the
         decompression buffer. 

533: Calls cleanup_trampoline.

540-543: Reloads the 64-bit GDT in case it was overwritten.

548-549: Jumps to the relocated compressed kernel.

579: Loads the boot-time IDT with the page fault and VMM communication
     exception entries initialized.

583: Initializes the identity mappings for the kernel page tables.

598: Calls load_stage2_idt.

602: Calls initialize_identity_maps.

615: Calls extract_kernel.

621: Jumps to the decompressed kernel entry point startup_64 located
     on line 44 of /arch/x86/kernel/head_64.S.
```

#### sev\_enable (linux/arch/x86/boot/compressed/sev.c:273)

```txt
Control Flow:
startup_32
    ...
    startup_64
        load_stage1_idt
        sev_enable <-- Here
```

#### snp\_init (linux/arch/x86/boot/compressed/sev.c:396)

```txt
Control Flow:
startup_32
    ...
    startup_64
        load_stage1_idt
        sev_enable
            snp_init <-- Here
```

#### setup\_cpuid\_table (linux/arch/x86/kernel/sev-shared.c:967)

```
Control Flow:
startup_32
    ...
    startup_64
        load_stage1_idt
        sev_enable
            snp_init
                setup_cpuid_table <-- Here
```

#### paging\_prepare (linux/arch/x86/boot/compressed/ptable\_64.c:109)

```txt
Control Flow:
startup_32
    ...
    startup_64
        load_stage1_idt
        sev_enable
        paging_prepare <-- Here

128-133: Sets paging_config.l5_required if we need to initialize
         5-level page tables.

135: Obtains the 32-bit trampoline's address.

140-143: Preserves the data in the 32-bit trampoline in the static
         buffer trampoline_save and then bzeroes it.

146-147: Copies the trampoline code into low memory.

163: Returns early if we do not need 5-level paging and it is not
     supported.

167-171: Copies the current contents of CR3 into the first entry
         of the trampoline's page table. This means the Level 5
         page table points to the Level 4 page table we initialized
         in startup_32.

172-186: If 5-level paging is not required but it is supported, we
         copy the top level page table we initialized in startup_32
         into the trampoline.

189: Returns the initialized paging_config structure.
```

#### find\_trampoline\_placement (linux/arch/x86/boot/compressed/pgtable\_64.c:39)

```txt
Control Flow:
startup_32
    ...
    startup_64
        load_stage1_idt
        sev_enable
        paging_prepare
            find_trampoline_placement <-- Here

Note: EBDA = Extended BIOS Data Area

58-62: Assigns the ebda_start and bios_start variables if we were
       NOT loaded by an EFI system.

       Consult the following link to understand why we left-shift
       this BIOS data area fields:

       https://wiki.osdev.org/Memory_Map_(x86)#BIOS_Data_Area_.28BDA.29

70: Page align bios_start.

73-103: Searches the e820 table for the first region of usable memory
        below the BIOS that is large enough for the 32-bit trampoline
        and sets bios_start = entry->addr + entry->size.

        Hence, we find the lowest memory region large enough for the
        trampoline and point bios_start to the end of it.

106: Returns the address of the 32-bit trampoline.
```

#### cleanup\_trampoline (linux/arch/x86/boot/compressed/pgtable\_64.c:192)

```txt
Control Flow:
startup_32
    ...
    startup_64
        load_stage1_idt
        sev_enable
        paging_prepare
        cleanup_trampoline <-- Here

202-208: Uses the data from trampoline_save to restore the trampoline's
         memory if it contains the top level kernel page table.
```

#### load\_stage2\_idt (linux/arch/x86/boot/compressed/idt\_64.c:59)

```txt
Control Flow:
startup_32
    ...
    startup_64
        load_stage1_idt
        sev_enable
        paging_prepare
        cleanup_trampoline
        load_stage2_idt <-- Here
```

#### initialize\_identity\_maps (linux/arch/x86/boot/compressed/ident\_map\_64.c:110)

```txt
Control Flow:
startup_32
    ...
    startup_64
        load_stage1_idt
        sev_enable
        paging_prepare
        cleanup_trampoline
        load_stage2_idt
        initialize_identity_maps <-- Here
```

#### extract\_kernel (linux/arch/x86/boot/compressed/misc.c:350)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel <-- Here

361: Assigns the boot parameters to the global variable
     boot_params.

376-377: Assigns the original nb of rows and columns from
         boot_params to global variables lines and cols
         respectively.

379: Calls init_default_io_ops, which is the same function
     discussed in boot1.md.

387: Checks if we are running in a Trusted Domain Extensions
     (TDX) guest environment.

389: Calls console_init to set up the early printk
     environment. This is the same console_init routine
     from boot1.md.

396: Calls get_rsdp_addr.

     Obtains the Root System Description Pointer (RSDP)
     from either EFI or BIOS.

387: Detects if this code is running in a Trusted Domain
     Extensions environment before initializing the console.

414-416: Calculates the amount of memory the decrompressed
         kernel needs.

420-425: Calls debug_putaddr to print information on the
         kernel's initial position.

432: Calls choose_random_location.

457: Calls __decompress, which is a wrapper for the bunzip2
     function from /lib/decompress_bunzip2.c.

459: Calls parse_elf.

460: Calls handle_relocations.

464: Calls cleanup_exception_handling.

NOTE: SEV_ES stands for Secure Encrypted Virtualized (SEV)
      with Encrypted State (ES). GHCB stands for Guest-Host
      Communication Block.
```

#### sanitize\_boot\_params (linux/arch/x86/include/asm/bootparam\_utils.h:35)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
        sanitize_boot_params <-- Here
```

#### init\_default\_io\_ops (linux/arch/x86/boot/io.h:26)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
        sanitize_boot_params
        init_default_io_ops <-- Here

28-30: Sets the global pio_ops structure's fctn pointers
       to __inb, __outb, and __outw.

       The global pio_ops structure is declared in
       /arch/x86/boot/compressed/misc.c on line 51.
```

#### early\_tdx\_detect (linux/arch/x86/boot/compressed/tdx.c:64)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect <-- Here

70: Returns early if the cpuid's signature is not "IntelTDX    ",
    which means we are not running in a TDX guest environment.

74-76: Updates the pio_ops structure's fields to tdx_inb, tdx_outb,
       and tdx_outw respectively. These functions can be found at
       /arch/x86/boot/compressed/tdx.c.
```

#### console\_init (linux/arch/x86/boot/early\_serial\_console.c:148)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init <-- Here

Same as before. I'll do this later.
```

#### get\_rsdp\_addr (linux/arch/x86/boot/compressed/misc.c:155)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr <-- Here
```

#### debug\_putstr (linux/arch/x86/boot/compressed/misc.h:63)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr <-- Here

63: #define debug_putstr(__x)  __putstr(__x)
```

#### \_\_putstr (linux/arch/x86/boot/compressed/misc.c:118)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
                __putstr <-- Here
```

#### debug\_putaddr (linux/arch/x86/boot/compressed/misc.h:65)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr <-- Here

65-68: #define debug_putaddr(__x) { \
               debug_putstr(#__x ": 0x"); \
               debug_puthex((unsigned long)(__x)); \
               debug_putstr("\n"); \
           }
```

#### debug\_puthex (linux/arch/x86/boot/compressed/misc.h:64)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
                debug_puthex <-- Here

64: #define debug_puthex(__x)  __puthex(__x)
```

#### \_\_puthex (linux/arch/x86/boot/compressed/misc.c:167)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
                debug_puthex
                    __puthex <-- Here
```

#### choose\_random\_location (linux/arch/x86/boot/compressed/kaslr.c:826)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location <-- Here

839: Set the KASLR flag in the boot parameter's loadflags field.

841-842: Sets mem_limit to KERNEL_IMAGE_SIZE (512MiB) for 32-bit machines. 

847: Calls mem_avoid_init.

854-856: Assigns the minimum address to be the minimum of 512MiB and the
         output address (kernel's decompression address) we calculated in
         startup_32.

859: Calls find_random_phys_addr.

862-866: Sets the output address (kernel decompression address) to the
         random physical address.

870-871: Calls find_random_virt_addr.

872: Assigns the random physical address as the virtual address that maps
     LOAD_PHYSICAL_ADDR.
```

#### mem\_avoid\_init (linux/arch/x86/boot/compressed/kaslr.c:383)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
                mem_avoid_init <-- Here
```

#### find\_random\_phys\_addr (linux/arch/x86/boot/compressed/kaslr.c:776)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
                mem_avoid_init
                find_random_phys_addr <-- Here

791: Calls process_efi_entries.

792: Calls process_e820_entries.

     Walks through each entry of the e820 map and adds the valid RAM
     entries to the slot array, which contains MAX_SLOT_AREA = 100
     entries.

794: Calls slots_fetch_random.
```

#### process\_efi\_entries (linux/arch/x86/boot/compressed/kaslr.c:680)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
                mem_avoid_init
                find_random_phys_addr
                    process_efi_entries <-- Here
```

#### process\_e820\_entries (linux/arch/x86/boot/compressed/kaslr.c:756)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
                mem_avoid_init
                find_random_phys_addr
                    process_efi_entries
                    process_e820_entries <-- Here
```

#### slots\_fetch\_random (linux/arch/x86/boot/compressed/kaslr.c:556)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
                mem_avoid_init
                find_random_phys_addr
                    process_efi_entries
                    process_e820_entries
                    slots_fetch_random <-- Here

565: Calls kaslr_get_random_long.
```

#### kaslr\_get\_random\_long (linux/arch/x86/lib/kaslr.c:49)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
                mem_avoid_init
                find_random_phys_addr
                    process_efi_entries
                    process_e820_entries
                    slots_fetch_random
                        kaslr_get_random_long <-- Here
```

#### find\_random\_virt\_addr (linux/arch/x86/boot/compressed/kaslr.c:805)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
                mem_avoid_init
                find_random_phys_addr
                find_random_virt_addr <-- Here
```

#### \_\_decompress (linux/lib/decompress\_bunzip2.c:747)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
            __decompress <-- Here

754: return bunzip2(buf, len - 4, fill, flush, outbuf, pos, error);
```

#### bunzip2 (linux/lib/decompress\_bunzip2.c:679)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
            __decompress
                bunzip2 <-- Here
```

#### parse\_elf (linux/arch/x86/boot/compressed/misc.c:280)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
            __decompress
            parse_elf <-- Here
```

#### handle\_relocations (linux/arch/x86/boot/compressed/misc.c:185)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
            __decompress
            parse_elf
            handle_relocations <-- Here

241: Assigns the value of the reloc entry, the virtual address of a
     pointer we need to update, to extended.

243: extended = vaddr of ptr + delta - __START_KERNEL_MAP
              = vaddr of ptr + delta - __PAGE_OFFSET
              = physical address of pointer

245: ptr = physical address of pointer

246-247: Checks if the physical address of the pointer is within the
         kernel image.

249: Increments the value of the pointer by delta to update it.

...
```

#### cleanup\_exception\_handling (linux/arch/x86/boot/compressed/idt\_64.c:79)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
            __decompress
            parse_elf
            handle_relocations
            cleanup_exception_handling <-- Here

NOTE: SEV_ES stands for Secure Encrypted Virtualized (SEV)
      with Encrypted State (ES). GHCB stands for Guest-Host
      Communication Block.

85: Calls sev_es_shutdown_ghcb.

88-90: Loads the null-idt using the boot_idt_desc, which are
       both defined on lines 735 and 740 of
       /arch/x86/boot/compressed/head_64.S.

90: Calls load_boot_idt.
```

#### sev\_es\_shutdown\_ghcb (linux/arch/x86/boot/compressed/sev.c:196)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
            __decompress
            parse_elf
            handle_relocations
            cleanup_exception_handling
                sev_es_shutdown_ghcb <-- Here
```

#### load\_boot\_idt (linux/arch/x86/boot/compressed/idt\_64.c:25)

```txt
Control Flow:
startup_32
    ...
    startup_64
        ...
        extract_kernel
            sanitize_boot_params
            init_default_io_ops
            early_tdx_detect
            console_init
            get_rsdp_addr
            debug_putstr
            debug_putaddr
            choose_random_location
            __decompress
            parse_elf
            handle_relocations
            cleanup_exception_handling
                sev_es_shutdown_ghcb
                load_boot_idt <-- Here
```
