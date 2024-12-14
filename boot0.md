## Walkthrough of Linux Kernel 6.1.66's Generalized Bootloader Sequence

### Contents

1. Memory Maps
2. Source Code Commentary

### Memory Maps

#### General Memory Map (linux/Documentation/x86/boot.rst)

```txt
For a modern bzImage kernel with boot protocol version >= 2.02, a
memory layout like the following is suggested:

_________________________
|                       |
| Protected mode kernel |
|_______________________| 100000h
|                       |
| I/O memory hole       |
|_______________________| A0000h
|                       |
| Reserved for BIOS     | <- Leave as much as possible unused
|_______________________|
|                       |
|                       |
| Command Line          | <- Can also be below 10000h mark
|                       |
|_______________________| X + 10000h
|                       |
|                       |
| Stack/heap            | <- For use by real-mode code.
|                       |
|_______________________| X + 8000h
|                       |
| Kernel setup          | <- The kernel real-mode code
| Kernel bootsector     | <- The kernel legacy bootsector
|                       |
|_______________________| X
|                       |
|                       |
| Boot loader           | <- Boot sector entry point 0000:7C00
|                       |
|_______________________| 1000h
|                       |
| Reserved for MBR/BIOS |
|_______________________| 800h
|                       |
| Typically used by MBR |
|_______________________| 600h
|                       |
| BIOS use only         |
|_______________________| 0h

where the address X is as low as the design of the boot loader
permits.
```
#### Specific Memory Map

```txt
_________________________
|                       |
| Protected-mode kernel |
|_______________________| 0x00100000
|                       |
| Motherboard BIOS      |
|_______________________| 0x000f0000
|                       |
| BIOS Expansions       |
|_______________________| 0x000c8000
|                       |
| Video BIOS            |
|_______________________| 0x000c0000
|                       |
| I/O memory hole       |
|_______________________| 0x000a0000
|                       |
| Reseved for BIOS      |
|_______________________| 0x00080000
|                       |
|                       |
| Command Line          | <-- Can also be below the 0x00020000 mark
|                       |
|_______________________| 0x00020000
|                       |
|                       |
| Stack/heap            | <-- For use by the kernel real-mode code
| (%esp = 0x0000fffc)   |
|                       |
|_______________________| 0x00018000
|                       |
|                       |
| Kernel setup          | <-- The kernel real-mode code
| (header.S)            |
|_______________________| 0x00010200
|                       |
|                       |
| Kernel boot sector    | <-- The kernel legacy boot sector
| (header.S)            |
|_______________________| 0x00010000 (for GRUB2 bootloader)
|                       |
|                       |
| Boot loader           | <-- Boot sector entry point 0000:7c00
|                       |
|_______________________| 0x00001000
|                       |
| Reserved for MBR/BIOS |
|_______________________| 0x00000800
|                       |
| Typically used by MBR |
|_______________________| 0x00000600
|                       |
| BIOS use only         |
|_______________________| 0x00000000
```

### Source Code Commentary

#### start\_of\_setup (linux/arch/x86/boot/header.S:588)

```asm
start_of_setup:
# Force %es = %ds
	movw	%ds, %ax
	movw	%ax, %es
	cld

# Apparently some ancient versions of LILO invoked the kernel with %ss != %ds,
# which happened to work by accident for the old code.  Recalculate the stack
# pointer if %ss is invalid.  Otherwise leave it alone, LOADLIN sets up the
# stack behind its own code, so we can't blindly put it directly past the heap.

	movw	%ss, %dx
	cmpw	%ax, %dx	# %ds == %ss?
	movw	%sp, %dx
	je	2f				# -> assume %sp is reasonably set

	# Invalid %ss, make up a new stack
	movw	$_end, %dx
	testb	$CAN_USE_HEAP, loadflags
	jz	1f
	movw	heap_end_ptr, %dx
1:	addw	$STACK_SIZE, %dx
	jnc	2f
	xorw	%dx, %dx	# Prevent wraparound

2:	# Now %dx should point to the end of our stack space
	andw	$~3, %dx		# dword align (might as well...)
	jnz	3f
	movw	$0xfffc, %dx	# Make sure we're not zero
3:	movw	%ax, %ss
	movzwl	%dx, %esp		# Clear upper half of %esp
	sti						# Now we should have a working stack

# We will have entered with %cs = %ds+0x20, normalize %cs so
# it is on par with the other segments.
	pushw	%ds
	pushw	$6f
	lretw
6:

# Check signature at end of setup
	cmpl	$0x5a5aaa55, setup_sig
	jne	setup_bad

# Zero the bss
	movw	$__bss_start, %di
	movw	$_end+3, %cx
	xorl	%eax, %eax
	subw	%di, %cx
	shrw	$2, %cx
	rep; stosl

# Jump to C code (should not return)
	calll	main
```
