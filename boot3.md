## Walkthrough of Linux Kernel 6.1.66's Decompressed Kernel Boot Code

### startup\_64 (linux/arch/x86/kernel/head\_64.S:44)

```txt
Control Flow:
startup_64 <-- Here

65: Initializes the stack pointer to __end_init_task - 168. What are the
    units? Words?

67: Assigns physical address of _text global variable to %rdi, or in other
    words, %rdi = &_text.

75-82: Adjusts the global pointer initial_gs. 

       %rax = initial_gs - _text
            = initial_gs's offset rel to _text

       %rax = initial_gs's offset + phys addr of _text
            = phys addr of initial_gs

       %rdx = %rax << 32

            = [ 32 zero ext bits | upper 32 bits of %rdx ]

                    GS Model Specific Register 

85: Calls startup_64_setup_env.

89-92: Sets the code segment selector to __KERNEL_CS.

106: Calls sme_enable.

111: Calls verify_cpu.

121: Calls __startup_64 to perform pagetable fixups.

125: Assigns the compiled physical address to %rax.

191-198: Enables PAE, PGE, and LA57 if applicable.

201: Increments %rax by the adjusted phys_base address to obtain the
     physical address of the kernel page tables.

213: Calls sev_verify_cbit.

224: Loads the physical address of the kernel page tables into the CR3
     register.

230-239: Flushes the TLB.

250: Reloads the GDT using a 64-bit linear address.

253-264: Initializes the data segments.

273-276: Reinitializes MSR_GS_BASE.

282: Reinitializes the boot time stack.

286: Calls early_setup_idt to reload the IDT.

295: What is EFER (Extended Feature Enable Register)?

325: Assigns the real-mode pointer to %rdi.

353-358: Long returns to x86_64_start_kernel.
```

#### startup\_64\_setup\_env (linux/arch/x86/kernel/head64.c:625)

```txt
Control Flow:
startup_64
    startup_64_setup_env <-- Here

628-629: Calls fixup_pointer.

         Loads startup_gdt (line 72) using the startup_gdt_descr
         structure (line 82).

629: Calls native_load_gdt.

632-635: Reloads data segment registers since we just loaded the
         startup_gdt.

636: Calls startup_64_load_idt.
```

#### fixup\_pointer (linux/arch/x86/kernel/head64.c:89)

```txt
Control Flow:
startup_64
    startup_64_setup_env
        fixup_pointer <-- Here

91: Returns the adjusted value of the ptr argument using the following
    calculations:

    x = ptr - (void *)_text
      = ptr's offset rel to _text label

    y = x + (void *)physaddr
      = ptr's offset rel to _text label + _text's physical address
      = ptr's physical address

    return y
```

#### native\_load\_gdt (linux/arch/x86/include/asm/desc.h:208)

```txt
Control Flow:
startup_64
    startup_64_setup_env
        fixup_pointer
        native_load_gdt <-- Here

210: asm volatile("lgdt %0"::"m" (*dtr));
```

#### startup\_64\_load\_idt (linux/arch/x86/kernel/head64.c:591)

```txt
Control Flow:
startup_64
    startup_64_setup_env
        fixup_pointer
        native_load_gdt
        startup_64_load_idt <-- Here
```

#### sme\_enable (linux/arch/x86/mm/mem\_encrypt\_identity.c:505)

```txt
Control Flow:
startup_64
    startup_64_setup_env
    sme_enable <-- Here

516: Calls snp_init.

...
```

#### snp\_init (linux/arch/x86/kernel/sev.c:2125)

```txt
Control Flow:
startup_64
    startup_64_setup_env
    sme_enable
        snp_init <-- Here
```

#### verify\_cpu (linux/arch/x86/kernel/verify\_cpu.S:34)

```txt
Control Flow:
startup_64
    startup_64_setup_env
    sme_enable
    verify_cpu <-- Here
```

#### \_\_startup\_64 (linux/arch/x86/kernel/head64.c:179)

```txt
Control Flow:
startup_64
    startup_64_setup_env
    sme_enable
    verify_cpu
    __startup_64 <-- Here

193: Calls check_la57_support.

210: Calls sme_get_me_mask.

215: Calls pgd_index.

317: Calls fixup_long.

319: return sme_postprocess_startup(bp, pmd);
```

#### check\_la57\_support (linux/arch/x86/kernel/head64.c:105)

```txt
Control Flow:
startup_64
    startup_64_setup_env
    sme_enable
    verify_cpu
    __startup_64
        check_la57_support <-- Here
```

#### sme\_get\_me\_mask (linux/arch/x86/include/asm/mem\_encrypt.h:108)

```txt
Control Flow:
startup_64
    startup_64_setup_env
    sme_enable
    verify_cpu
    __startup_64
        check_la57_support
        sme_get_me_mask <-- Here
```

#### pgd\_index (linux/include/linux/pgtable.h:86)

```txt
Control Flow:
startup_64
    startup_64_setup_env
    sme_enable
    verify_cpu
    __startup_64
        check_la57_support
        sme_get_me_mask
        pgd_index <-- Here
```

#### fixup\_long (linux/arch/x86/kernel/head64.c:94)

```txt
Control Flow:
startup_64
    startup_64_setup_env
    sme_enable
    verify_cpu
    __startup_64
        check_la57_support
        sme_get_me_mask
        pgd_index
        fixup_long <-- Here

96: return fixup_pointer(ptr, physaddr);
```

#### sme\_postprocess\_startup (linux/arch/x86/kernel/head64.c:130)

```txt
Control Flow:
startup_64
    startup_64_setup_env
    sme_enable
    verify_cpu
    __startup_64
        check_la57_support
        sme_get_me_mask
        pgd_index
        fixup_long
        sme_postprocess_startup <-- Here
```

#### sev\_verify\_cbit (linux/arch/x86/kernel/sev\_verify\_cbit.S:22)

```txt
Control Flow:
startup_64
    startup_64_setup_env
    sme_enable
    verify_cpu
    __startup_64
    sev_verify_cbit <-- Here
```

#### early\_setup\_idt (linux/arch/x86/kernel/head64.c:610)

```txt
Control Flow:
startup_64
    startup_64_setup_env
    sme_enable
    verify_cpu
    __startup_64
    sev_verify_cbit
    early_setup_idt <-- Here
```

#### x86\_64\_start\_kernel (linux/arch/x86/kernel/head64.c:474)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel <-- Here

490: Calls cr4_init_shadow.

493: Calls reset_early_page_tables.

495: Calls clear_bss.

501: Calls clear_page.

508: Calls sme_early_init.

510: Calls kasan_early_init.

520: Calls __native_tlb_flush_global.

522: Calls idt_setup_early_handler.

525: Calls tdx_early_init.

527: Calls copy_bootdata.

532: Calls load_ucode_bsp.

537: Calls x86_64_start_reservations.
```

#### cr4\_init\_shadow (linux/arch/x86/include/asm/tlbflush.h:164)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow <-- Here
```

#### reset\_early\_page\_tables (linux/arch/x86/kernel/head64.c:323)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables <-- Here
```

#### clear\_bss (linux/arch/x86/kernel/head64.c:429)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss <-- Here
```

#### clear\_page (linux/arch/x86/include/asm/page\_32.h:24)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss
        clear_page <-- Here
```

#### sme\_early\_init (linux/arch/x86/mm/mem\_encrypt\_amd.c:489)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss
        clear_page
        sme_early_init <-- Here
```

#### kasan\_early\_init (linux/arch/x86/mm/kasan\_init\_64.c:288)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss
        clear_page
        sme_early_init
        kasan_early_init <-- Here
```

#### \_\_native\_tlb\_flush\_global (linux/arch/x86/include/asm/tlbflush.h:362)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss
        clear_page
        sme_early_init
        kasan_early_init
        __native_tlb_flush_global <-- Here
```

#### idt\_setup\_early\_handler (linux/arch/x86/kernel/idt.c:311)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss
        clear_page
        sme_early_init
        kasan_early_init
        __native_tlb_flush_global
        idt_setup_early_handler <-- Here
```

#### tdx\_early\_init (linux/arch/x86/coco/tdx/tdx.c:783)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss
        clear_page
        sme_early_init
        kasan_early_init
        __native_tlb_flush_global
        idt_setup_early_handler
        tdx_early_init <-- Here
```

#### copy\_bootdata (linux/arch/x86/kernel/head64.c:446)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss
        clear_page
        sme_early_init
        kasan_early_init
        __native_tlb_flush_global
        idt_setup_early_handler
        tdx_early_init
        copy_bootdata <-- Here
```

#### load\_ucode\_bsp (linux/arch/x86/kernel/cpu/microcode/core.c:143)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss
        clear_page
        sme_early_init
        kasan_early_init
        __native_tlb_flush_global
        idt_setup_early_handler
        tdx_early_init
        copy_bootdata
        load_ucode_bsp <-- Here
```

#### x86\_64\_start\_reservations (linux/arch/x86/kernel/head64.c:540)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss
        clear_page
        sme_early_init
        kasan_early_init
        __native_tlb_flush_global
        idt_setup_early_handler
        tdx_early_init
        copy_bootdata
        load_ucode_bsp
        x86_64_start_reservations <-- Here

543-544: Calls copy_bootdata.

546: Calls x86_early_init_platform_quirks.

550: Calls x86_intel_mid_early_setup.

556: Calls start_kernel.
```

#### x86\_early\_init\_platform\_quirks (linux/arch/x86/kernel/platform-quirks.c:8)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss
        clear_page
        sme_early_init
        kasan_early_init
        __native_tlb_flush_global
        idt_setup_early_handler
        tdx_early_init
        copy_bootdata
        load_ucode_bsp
        x86_64_start_reservations
            x86_early_init_platform_quirks <-- Here
```

#### x86\_intel\_mid\_early\_setup (linux/arch/x86/platform/intel-mid/intel-mid.c:92)

```txt
Control Flow:
startup_64
    ...
    x86_64_start_kernel
        cr4_init_shadow
        reset_early_page_tables
        clear_bss
        clear_page
        sme_early_init
        kasan_early_init
        __native_tlb_flush_global
        idt_setup_early_handler
        tdx_early_init
        copy_bootdata
        load_ucode_bsp
        x86_64_start_reservations
            x86_early_init_platform_quirks
            x86_intel_mid_early_setup <-- Here
```

#### startup\_64\_load\_idt (linux/arch/x86/kernel/head64.c:591)

```txt
593-606: Loads the bringup_idt_table (line 573) using the bringup_idt_descr
         structure (line 571).

597-603: Calls set_bringup_idt_handler to initialize the X86_TRAP_VC
         IDT entry for CONFIG_AND_MEM_ENCRYPT.
```

#### set\_bringup\_idt\_handler (linux/arch/x86/kernel/head64.c:578)

```txt
584-586: Initializes the nth IDT entry in the table pointed to by the idt
         argument.
```

#### sme\_enable (linux/arch/x86/mm/mem\_encrpyt\_identity.c:505)

```txt
516: Calls snp_init.

```

#### snp\_init (linux/arch/x86/kernel/sev.c:2093)

```txt

```

#### verify\_cpu (linux/arch/x86/kernel/verify\_cpu.S:34)

```txt
https://www.youtube.com/watch?v=DBt5nFQqXpY
```

#### \_\_startup\_64 (linux/arch/x86/kernel/head64.c:179)

```txt
203: Initializes the load_delta variable using the following calculations:

     load_delta = physaddr - (unsigned long)(_text - __START_KERNEL_map)

                = physaddr - (vaddr of _text - PAGE_OFFSET)

                = physaddr - (compiled addr of _text)

214-317: Uses load_delta to update the physical addresses in the page
         tables.

228-229: pud[511] is used for kernel high mappings, but what about
         pud[510]?

231-232: "Fix-Mapped addresses are a set of special compile-time addresses whose
          corresponding physical addresses do not have to be a linear address
          minus __START_KERNEL_map.

          Each fix-mapped address maps one page frame and the kernel uses them
          as pointers that never change their address.

          That is the main point of these addresses. As the comment says: to
          have a constant address at compile time, but to set the physical
          address only in the boot process."

          Source: https://0xax.gitbooks.io/linux-insides/content/MM/linux-mm-2.html

317: Calls fixup_long to assign the unadjusted value of physaddr and increments
     it by load_delta - sme_get_me_mask.
```

#### fixup\_long (linux/arch/x86/kernel/head64.c:94)

```txt
96: Calls fixup_pointer by passing its arguments. This function is called by
    __startup_64 to assign physaddr to phys_base.
```

#### sme\_postprocess\_startup (linux/arch/x86/kernel/head64.c:130)

```txt
136: Calls sme_encrypt_kernel.

159: Calls early_snp_set_memory_shared.
```

#### sme\_encrypt\_kernel (linux/arch/x86/mm/mem\_encrypt\_identity.c:292)

```txt

```

#### early\_snp\_set\_memory\_shared (linux/arch/x86/kernel/sev.c:723)

```txt

```

#### early\_setup\_idt (linux/arch/x86/kernel/head64.c:610)

```txt
613-615:

618-619: Loads the bringup_idt_table using the bringup_idt_descr
         structure.
```

#### setup\_ghcb (linux/arch/x86/kernel/sev.c:1248)

```txt
I will do this later.
```

#### x86\_64\_start\_kernel (linux/arch/x86/kernel/head64.c:474)

```txt
490: Initializes the CR4 shadow for the boot CPU.

493: Calls reset_early_page_tables to remove identity mappings.

501: Calls clear_page to bzero the init_top_pgt page.

535: Assigns early_top_pgt[511] to init_top_pgt[511] to set kernel
     high mappings.
```

#### reset\_early\_page\_tables (linux/arch/x86/kernel/head64.c:323)

```txt
325: Bzeroes the user space entries in the kernel pgt to zero.

327: Calls write_cr3 to reassign early_top_pgt to the CR3 register.
```

#### sme\_early\_init (linux/arch/x86/mm/mem\_encrypt\_amd.c:488)

```txt

```

#### kasan\_early\_init (linux/arch/x86/mm/kasan\_init\_64.c:288)

```txt
291-310: Initializes the kasan_early_shadow_pte, kasan_early_shadow_pmd,
         and kasan_early_shadow_pud using the kasan_early_shadow_page
         structure (/mm/kasan/init.c:29).

315-316: Calls kasan_map_early_shadow to map early_to_pgt and init_top_pgt.
```

#### kasan\_map\_early\_shadow (linux/arch/x86/mm/kasan\_init\_64.c:231)

```txt
I will do this later.
```

#### kasan\_early\_p4d\_populate (linux/arch/x86/mm/kasan\_init\_64.c:204)

```txt
I will do this later.
```

#### idt\_setup\_early\_handler (linux/arch/x86/kernel/idt.c:311)

```txt
315-316: Initializes the first 32 IDT entries in the idt_table array
         (line 167) using the early_idt_handler_array structure
         (/arch/x86/kernel/head_64.S: 431).

321: Loads the idt_table array using the idt_descr structure (line 169).
```

#### set\_intr\_gate (linux/arch/x86/kernel/idt.c:200)

```txt
Self explanatory. Very similar to setup_bringup_idt_handler.
```

#### tdx\_early\_init (linux/arch/x86/coco/tdx/tdx.c:748)

```txt
I might do this depending on my mood.
```

#### copy\_bootdata (linux/arch/x86/kernel/head64.c:446)

```txt
457: Copies the real-mode data from %rsi to the global variable
     boot_params (/arch/x86/boot/main.c: 18).

459-462: Copies the command line from the real-mode data to the
         global variable boot_command_line (/init/main.c: 148).

471: Removes SME mappings from the real_mode_data so that the memory
     can be reclaimed.
```

#### x86\_64\_start\_reservations (linux/arch/x86/kernel/head64.c:540)

```txt
556: Calls start_kernel to begin kernel initialization.
```

#### x86\_early\_init\_platform\_quirks (linux/arch/x86/kernel/platform-quirks.c:8)

```txt
https://www.youtube.com/watch?v=LNPwbH1MyaQ
```

#### x86\_intel\_mid\_early\_setup (linux/arch/x86/kernel/head64.c:92)

```txt
https://www.youtube.com/watch?v=sL5t47NZ3bM
```
