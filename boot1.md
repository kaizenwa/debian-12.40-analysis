## Walkthrough of Linux Kernel 6.1.66's Real-mode Boot Code

### Contents

1. Source Code Commentary

### Source Code Commentary

#### main (linux/arch/x86/boot/main.c:136)

```txt
Control Flow:
main <-- Here
```

#### init\_default\_io\_ops (linux/arch/x86/boot/io.h:26)

```txt
Control Flow:
main
    init_default_io_ops <-- Here

28-30: Sets the global pio_ops structure's fctn pointers
       to __inb, __outb, and __outw.

       The global pio_ops structure is declared in
       /arch/x86/boot/compressed/misc.c on line 51.
```

#### copy\_boot\_params (linux/arch/x86/boot/main.c:31)

```txt
Control Flow:
main
    copy_boot_params <-- Here
    ...

37-38: The value of OLD_CL_ADDRESS is 0x020, which is the address relative
       to the data segment %ds. In our case, %ds = 0x1000 which means
       OLD_CL_ADDRESS is located at 0x10020. This is contained within the
       kernel bootsector section of memory.

41: We copy the boot header hdr (/arch/x86/boot/header.S:283) to
    the boot_params structure (/arch/x86/boot/main.c:18) in the
    kernel image. Note that hdr is globally defined on line 282
    of /arch/x86/boot/header.S and defined externally on line 33
    of /arch/x86/boot/boot.h.
    
51-54: We set the command line segment to either 0x1000 or 0x9000
       depending on the offset of the command line.
```

#### console\_init (linux/arch/x86/boot/early\_serial\_console.c:148)

```txt
Control Flow:
main
    copy_boot_params
    console_init <-- Here

150: Calls parse_earlyprintk.

152-153: Calls parse_console_uart8250.
```

#### parse\_earlyprintk (linux/arch/x86/boot/early\_serial\_console.c:46)

```txt
Control Flow:
main
    copy_boot_params
    console_init
        parse_earlyprintk <-- Here

48: Default baud rate for printk is 9600

53-95: If the command line contains the earlyprintk argument, we
       drop into the loop to initialize its type, serial port,
       and baud rate. Here we assume the default case where
       the tuple is given by {serial,0x3f8,115200}.

98: Initializes the serial port with the following settings:
        * 8n1
        * no interrupt
        * no fifo
        * DTR + RTS
        * baud = 115200

    Also sets the variable early_serial_base to 0x3f8.
```

#### init\_heap (linux/arch/x86/boot/main.c:117)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap  <-- Here

121-124: If we can use the heap, we set stack_end = %esp - 1024.

125-128: If the heap plus an additional sector overlaps with the
         stack, we simply set heap_end = stack_end.
```

#### validate\_cpu (linux/arch/x86/boot/cpu.c:73)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu <-- Here
```

#### set\_bios\_mode (linux/arch/x86/boot/main.c:105)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu
    set_bios_mode <-- Here

110-113: This code calls an obscure BIOS function called
         Detect Target Operating Mode Callback which is
         described in the following forum post:
         https://forum.osdev.org/viewtopic.php?f=1&t=20445&start=10
```

#### detect\_memory (linux/arch/x86/boot/main.c:116)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu
    set_bios_mode
    detect_memory <-- Here

118: Calls detect_memory_e820.

120: Calls detect_memory_e801.

122: Calls detect_memory_88.
```

#### detect\_memory\_e820 (/arch/x86/boot/memory.c:18)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu
    set_bios_mode
    detect_memory
        detect_memory_e820 <-- Here

25-67: Uses the BIOS int 0x15 AX=0xe820 routine to create a map of
       available memory and copies it to boot_params.e820_table.
```

#### detect\_memory\_e801 (linux/arch/x86/boot/memory.c:72)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu
    set_bios_mode
    detect_memory
        detect_memory_e820
        detect_memory_e801 <-- Here
```


#### detect\_memory\_88 (linux/arch/x86/boot/memory.c:105)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu
    set_bios_mode
    detect_memory
        detect_memory_e820
        detect_memory_e801
        detect_memory_88 <-- Here
```

#### keyboard\_init (/arch/x86/boot/main.c:66)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu
    set_bios_mode
    detect_memory
    keyboard_init <-- Here

75-76: Sets typematic rate to max with BIOS int 0x16 ax=0x0305.
```

#### query\_ist (linux/arch/x86/boot/main.c:82)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu
    set_bios_mode
    detect_memory
    keyboard_init
    query_ist <-- Here
```

#### query\_apm\_bios (linux/arch/x86/boot/apm.c:19)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu
    set_bios_mode
    detect_memory
    keyboard_init
    query_ist
    query_apm_bios <-- Here
```

#### query\_edd (linux/arch/x86/boot/edd.c:120)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu
    set_bios_mode
    detect_memory
    keyboard_init
    query_ist
    query_apm_bios
    query_edd <-- Here
```

#### set\_video (/arch/x86/boot/video.c:317)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu
    set_bios_mode
    detect_memory
    keyboard_init
    query_ist
    query_edd
    set_video <-- Here
```

#### go\_to\_protected\_mode (linux/arch/x86/boot/pm.c:102)

```txt
Control Flow:
main
    copy_boot_params
    console_init
    init_heap
    validate_cpu
    set_bios_mode
    detect_memory
    keyboard_init
    query_ist
    query_edd
    set_video
    go_to_protected_mode <-- Here

105: Calls realmode_switch_hook.

108: Calls enable_a20.

114: Calls reset_coprocessor.

117: Calls mask_all_interrupts.

120: Calls setup_idt.

121: Calls setup_gdt.

122-123: Calls protected_mode_jump.
```

#### realmode\_switch\_hook (/arch/x86/boot/pm.c:20)

```txt
Control Flow:
main
    ...
    go_to_protected_mode
        realmode_switch_hook <-- Here

22-30: This function calls boot_params.hdr.realmode_swtch if it exists
       to complete any other real-mode tasks before we switch to protected
       mode. Otherwise, it just disables interrupts and NMI.
```

#### enable\_a20 (linux/arch/x86/boot/a20.c:128)

```txt
Control Flow:
main
    ...
    go_to_protected_mode
        realmode_switch_hook
        enable_a20 <-- Here
```

#### reset\_coprocessor (linux/arch/x86/boot/pm.c:47)

```txt
Control Flow:
main
    ...
    go_to_protected_mode
        realmode_switch_hook
        enable_a20
        reset_coprocessor <-- Here
```

#### mask\_all\_interrupts (linux/arch/x86/boot/pm.c:36)

```txt
Control Flow:
main
    ...
    go_to_protected_mode
        realmode_switch_hook
        enable_a20
        reset_coprocessor
        mask_all_interrupts <-- Here
```

#### setup\_idt (linux/arch/x86/boot/pm.c:93)

```txt
Control Flow:
main
    ...
    go_to_protected_mode
        realmode_switch_hook
        enable_a20
        reset_coprocessor
        mask_all_interrupts
        setup_idt <-- Here

95-96: Loads the null idt {0,0}.
```

#### setup\_gdt (linux/arch/x86/boot/pm.c:64)

```txt
Control Flow:
main
    ...
    go_to_protected_mode
        realmode_switch_hook
        enable_a20
        reset_coprocessor
        mask_all_interrupts
        setup_idt
        setup_gdt <-- Here

68-77: Statically allocates boot_gdt as a u64 integer array
       that contains a null segment, two flat 4GiB segments,
       and a TSS segment. To fill this array, the code
       specifies an entry and assigns it directly with the
       GDT_ENTRY (/arch/x86/include/asm/segment.h:12) macro.

       Given GDT_ENTRY_BOOT_CS = 2 and GDT_ENTRY_BOOT_DS = 3,
       [GDT_ENTRY_BOOT_CS] corresponds to boot_gdt[2] and
       [GDT_ENTRY_BOOT_DS] corresponds to boot_gdt[3].

       Recall that each GDT entry is 8 bytes long, which means
       Linux has two null segments at the beginning of the GDT
       instead of one.

82-87: Loads boot_gdt using the statically allocated gdt_ptr
       structure.
```

#### protected\_mode\_jump (linux/arch/x86/boot/pmjump.S:24)

```txt
Control Flow:
main
    ...
    go_to_protected_mode
        realmode_switch_hook
        enable_a20
        reset_coprocessor
        mask_all_interrupts
        setup_idt
        setup_gdt
        go_to_protected_mode <-- Here
```
```asm
/*
 * void protected_mode_jump(u32 entrypoint, u32 bootparams);
 */
SYM_FUNC_START_NOALIGN(protected_mode_jump)
	movl	%edx, %esi		# Pointer to boot_params table

	xorl	%ebx, %ebx
	movw	%cs, %bx        # %ebx = %cs = 1000h
	shll	$4, %ebx        # %ebx = 10000h
	addl	%ebx, 2f        # %ebx = address of 2f
	jmp	1f			        # Short jump to serialize on 386/486
1:

	movw	$__BOOT_DS, %cx    # %cx = 24
	movw	$__BOOT_TSS, %di   # %di = 32

	movl	%cr0, %edx
	orb	$X86_CR0_PE, %dl	# Protected mode
	movl	%edx, %cr0      # Enable protected mode

	# Transition to 32-bit mode
	.byte	0x66, 0xea		# ljmpl opcode
2:	.long	.Lin_pm32		# offset
	.word	__BOOT_CS		# segment
ENDPROC(protected_mode_jump)

	.code32
	.section ".text32","ax"
GLOBAL(.Lin_pm32)
	# Set up data segments for flat 32-bit mode
	movl	%ecx, %ds
	movl	%ecx, %es
	movl	%ecx, %fs
	movl	%ecx, %gs
	movl	%ecx, %ss
	# The 32-bit code sets up its own stack, but this way we do have
	# a valid stack if some debugging hack wants to use it.
	addl	%ebx, %esp

	# Set up TR to make Intel VT happy
	ltr	%di

	# Clear registers to allow for future extensions to the
	# 32-bit boot protocol
	xorl	%ecx, %ecx
	xorl	%edx, %edx
	xorl	%ebx, %ebx
	xorl	%ebp, %ebp
	xorl	%edi, %edi

	# Set up LDTR to make Intel VT happy
	lldt	%cx

	jmpl	*%eax			# Jump to the 32-bit entrypoint

                            # The entrypoint is startup_32 on line 74
                            # of /arch/x86/compressed/head_64.S.
SYM_FUNC_END(.Lin_pm32)
```
