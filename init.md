## Walkthrough of Linux Kernel 6.1.66's Initialization Code

#### start\_kernel (/init/main.c:937)

```txt
Control Flow:
start_kernel <-- Here

942: Calls set_task_stack_end_magic.

943: Calls smp_setup_processor_id.

944: Calls debug_objects_early_init.

945: Calls init_vmlinux_build_id.

947: Calls cgroup_init_early.

949: Calls local_irq_disable.

956: Calls boot_cpu_init.

957: Calls page_address_init.

958: Calls pr_notice.

959: Calls early_security_init.

960: Calls setup_arch.

961: Calls setup_boot_config.

962: Calls setup_command_line.

963: Calls setup_nr_cpu_ids.

964: Calls setup_per_cpu_areas.

965: Calls native_smp_prepare_boot_cpu.

966: Calls boot_cpu_hotplug_init.

968: Calls build_all_zonelists.

969: Calls page_alloc_init.

973: Calls jump_label_init.

974: Calls parse_early_param.

975-978: Calls parse_args.

979: Calls print_unknown_bootoptions.

988: Calls random_init_early.

994: Calls setup_log_buf.

995: Calls vfs_caches_init_early.

996: Calls sort_main_extable.

997: Calls trap_init.

998: Calls mm_init.

999: Calls poking_init.

1000: Calls ftrace_init.

1003: Calls early_trace_init.

1010: Calls sched_init.

1012: Calls irqs_disabled and WARN.

1015: Calls radix_tree_init.

1016: Calls maple_tree_init.

1022: Calls housekeeping_init.

1029: Calls workqueue_init_early.

1031: Calls rcu_init.

1034: Calls trace_init.

1036-1037: Calls initcall_debug_enable.

1039: Calls context_tracking_init.

1041: Calls early_irq_init.

1042: Calls init_IRQ.

1043: Calls tick_init.

1044: Calls rcu_init_nohz.

1045: Calls init_timers.

1046: Calls srcu_init.

1047: Calls hrtimers_init.

1048: Calls softirq_init.

1049: Calls timekeeping_init.

1050: Calls time_init.

1053: Calls random_init.

1056: Calls kfence_init.

1057: Calls boot_init_stack_canary.

1059: Calls perf_event_init.

1060: Calls profile_init.

1061: Calls call_function_init.

1065: Calls local_irq_enable.

1067: Calls kmem_cache_init_late.

1074: Calls console_init.

1079: Calls lockdep_init.

1086: Calls locking_selftest.

1097: Calls setup_per_cpu_pageset.

1098: Calls numa_policy_init.

1099: Calls acpi_early_init.

1100-1101: Calls late_time_init.

1102: Calls sched_clock_init.

1103: Calls calibrate_delay.

1105: Calls arch_cpu_finalize_init.

1107: Calls pid_idr_init.

1108: Calls anon_vma_init.

1110-1111: Calls efi_enter_virtual_mode.

1113: Calls thread_stack_cache_init.

1114: Calls cred_init.

1115: Calls fork_init.

1116: Calls proc_caches_init.

1117: Calls uts_ns_init.

1118: Calls key_init.

1119: Calls security_init.

1120: Calls dbg_late_init.

1121: Calls net_ns_init.

1122: Calls vfs_caches_init.

1123: Calls pagecache_init.

1124: Calls signals_init.

1125: Calls seq_file_init.

1126: Calls proc_root_init.

1127: Calls nsfs_init.

1128: Calls cpuset_init.

1129: Calls cgroup_init.

1130: Calls taskstats_init_early.

1131: Calls delayacct_init.

1133: Calls acpi_subsystem_init.

1134: Calls arch_post_acpi_subsys_init.

1135: Calls kcsan_init.

1138: Calls arch_call_rest_init.

1140: Calls prevent_tail_call_optimization.
```

#### set\_task\_stack\_end\_magic (linux/kernel/fork.c:965)

```txt
Control Flow:
start_kernel
    set_task_stack_end_magic <-- Here
```

#### smp\_setup\_processor\_id (linux/init/main.c:776)

```txt
Control Flow:
start_kernel
    set_task_stack_end_magic
    smp_setup_processor_id <-- Here

This is a no-op.
```

#### debug\_objects\_early\_init (linux/lib/debugobjects.c:660)

```txt
Control Flow:
start_kernel
    set_task_stack_end_magic
    smp_setup_processor_id
    debug_objects_early_init <-- Here
```

#### init\_vmlinux\_build\_id (linux/lib/buildid.c:183)

```txt
Control Flow:
start_kernel
    set_task_stack_end_magic
    smp_setup_processor_id
    debug_objects_early_init
    init_vmlinux_build_id <-- Here
```

#### cgroup\_init\_early (linux/kernel/cgroup/cgroup.c:6033)

```txt
Control Flow:
start_kernel
    set_task_stack_end_magic
    smp_setup_processor_id
    debug_objects_early_init
    init_vmlinux_build_id
    cgroup_init_early <-- Here
```

#### local\_irq\_disable (linux/include/linux/irqflags.h:211)

```txt
Control Flow:
start_kernel
    ...
    smp_setup_processor_id
    debug_objects_early_init
    init_vmlinux_build_id
    cgroup_init_early
    local_irq_disable <-- Here
```

#### boot\_cpu\_init (linux/kernel/cpu.c:2718)

```txt
Control Flow:
start_kernel
    ...
    debug_objects_early_init
    init_vmlinux_build_id
    cgroup_init_early
    local_irq_disable
    boot_cpu_init <-- Here
```

#### page\_address\_init (linux/mm/highmem.c:804)

```txt
Control Flow:
start_kernel
    ...
    init_vmlinux_build_id
    cgroup_init_early
    local_irq_disable
    boot_cpu_init
    page_address_init <-- Here
```

#### pr\_notice (linux/include/linux/printk.h:519)

```txt
Control Flow:
start_kernel
    ...
    cgroup_init_early
    local_irq_disable
    boot_cpu_init
    page_address_init
    pr_notice <-- Here

519-520: #define pr_notice(fmt, ...) \
             printk(KERN_NOTICE pr_fmt(fmt), ##__VA_ARGS__)
```

#### early\_security\_init (linux/security/security.c:370)

```txt
Control Flow:
start_kernel
    ...
    local_irq_disable
    boot_cpu_init
    page_address_init
    pr_notice
    early_security_init <-- Here
```

#### setup\_arch (linux/arch/x86/kernel/setup.c:860)

```txt
Control Flow:
start_kernel
    ...
    boot_cpu_init
    page_address_init
    pr_notice
    early_security_init
    setup_arch <-- Here
```

#### setup\_boot\_config (linux/init/main.c:411)

```txt
Control Flow:
start_kernel
    ...
    page_address_init
    pr_notice
    early_security_init
    setup_arch
    setup_boot_config <-- Here
```

#### setup\_command\_line (linux/init/main.c:620)

```txt
Control Flow:
start_kernel
    ...
    pr_notice
    early_security_init
    setup_arch
    setup_boot_config
    setup_command_line <-- Here
```

#### setup\_nr\_cpu\_ids (linux/kernel/smp.c:1108)

```txt
Control Flow:
start_kernel
    ...
    early_security_init
    setup_arch
    setup_boot_config
    setup_command_line
    setup_nr_cpu_ids <-- Here
```

#### setup\_per\_cpu\_areas (linux/arch/x86/kernel/setup\_percpu.c:119)

```txt
Control Flow:
start_kernel
    ...
    setup_arch
    setup_boot_config
    setup_command_line
    setup_nr_cpu_ids
    setup_per_cpu_areas <-- Here
```

#### native\_smp\_prepare\_boot\_cpu (linux/arch/x86/kernel/smpboot.c:1472)

```txt
Control Flow:
start_kernel
    ...
    setup_boot_config
    setup_command_line
    setup_nr_cpu_ids
    setup_per_cpu_areas
    native_smp_prepare_boot_cpu <-- Here
```

#### boot\_cpu\_hotplug\_init (linux/kernel/cpu.c:2736)

```txt
Control Flow:
start_kernel
    ...
    setup_command_line
    setup_nr_cpu_ids
    setup_per_cpu_areas
    native_smp_prepare_boot_cpu
    boot_cpu_hotplug_init <-- Here
```

#### build\_all\_zonelists (linux/mm/page\_alloc.c:6678)

```txt
Control Flow:
start_kernel
    ...
    setup_nr_cpu_ids
    setup_per_cpu_areas
    native_smp_prepare_boot_cpu
    boot_cpu_hotplug_init
    build_all_zonelists <-- Here
```

#### page\_alloc\_init (linux/mm/page\_alloc.c:8636)

```txt
Control Flow:
start_kernel
    ...
    setup_per_cpu_areas
    native_smp_prepare_boot_cpu
    boot_cpu_hotplug_init
    build_all_zonelists
    page_alloc_init <-- Here
```

#### jump\_label\_init (linux/include/linux/jump\_label.h:262)

```txt
Control Flow:
start_kernel
    ...
    native_smp_prepare_boot_cpu
    boot_cpu_hotplug_init
    build_all_zonelists
    page_alloc_init
    jump_label_init <-- Here
```

#### parse\_early\_param (linux/init/main.c:760)

```txt
Control Flow:
start_kernel
    ...
    boot_cpu_hotplug_init
    build_all_zonelists
    page_alloc_init
    jump_label_init
    parse_early_param <-- Here
```

#### parse\_args (linux/kernel/params.c:161)

```txt
Control Flow:
start_kernel
    ...
    build_all_zonelists
    page_alloc_init
    jump_label_init
    parse_early_param
    parse_args <-- Here
```

#### print\_unknown\_bootoptions (linux/init/main.c:894)

```txt
Control Flow:
start_kernel
    ...
    page_alloc_init
    jump_label_init
    parse_early_param
    parse_args
    print_unknown_bootoptions <-- Here
```

#### random\_init\_early (linux/drivers/char/random.c:821)

```txt
Control Flow:
start_kernel
    ...
    jump_label_init
    parse_early_param
    parse_args
    print_unknown_bootoptions
    random_init_early <-- Here
```

#### setup\_log\_buf (linux/kernel/printk/printk.c:1071)

```txt
Control Flow:
start_kernel
    ...
    parse_early_param
    parse_args
    print_unknown_bootoptions
    random_init_early
    setup_log_buf <-- Here
```

#### vfs\_caches\_init\_early (linux/fs/dcache.c:3333)

```txt
Control Flow:
start_kernel
    ...
    parse_args
    print_unknown_bootoptions
    random_init_early
    setup_log_buf
    vfs_cache_init_early <-- Here
```

#### sort\_main\_extable (linux/kernel/extable.c:36)

```txt
Control Flow:
start_kernel
    ...
    print_unknown_bootoptions
    random_init_early
    setup_log_buf
    vfs_cache_init_early
    sort_main_extable <-- Here
```

#### trap\_init (linux/arch/x86/kernel/traps.c:1458)

```txt
Control Flow:
start_kernel
    ...
    random_init_early
    setup_log_buf
    vfs_cache_init_early
    sort_main_extable
    trap_init <-- Here
```

#### mm\_init (linux/init/main.c:831)

```txt
Control Flow:
start_kernel
    ...
    setup_log_buf
    vfs_cache_init_early
    sort_main_extable
    trap_init
    mm_init <-- Here
```

#### poking\_init (linux/arch/x86/mm/init.c:825)

```txt
Control Flow:
start_kernel
    ...
    vfs_cache_init_early
    sort_main_extable
    trap_init
    mm_init
    poking_init <-- Here
```

#### ftrace\_init (linux/kernel/trace/ftrace.c:7417)

```txt
Control Flow:
start_kernel
    ...
    sort_main_extable
    trap_init
    mm_init
    poking_init
    ftrace_init <-- Here
```

#### early\_trace\_init (linux/kernel/trace/trace.c:10423)

```txt
Control Flow:
start_kernel
    ...
    trap_init
    mm_init
    poking_init
    ftrace_init
    early_trace_init <-- Here
```

#### sched\_init (linux/kernel/sched/core.c:9665)

```txt
Control Flow:
start_kernel
    ...
    mm_init
    poking_init
    ftrace_init
    early_trace_init
    sched_init <-- Here
```

#### irqs\_disabled (linux/include/linux/irqflags.h:258)

```txt
Control Flow:
start_kernel
    ...
    poking_init
    ftrace_init
    early_trace_init
    sched_init
    irqs_disabled <-- Here

258-263: #define irqs_disabled()                    \
             ({                      \
                 unsigned long _flags;           \
                 raw_local_save_flags(_flags);      \
                 raw_irqs_disabled_flags(_flags);   \
             })
```

#### WARN (linux/include/asm-generic/bug.h:130)

```txt
Control Flow:
start_kernel
    ...
    ftrace_init
    early_trace_init
    sched_init
    irqs_disabled
    WARN <-- Here

130-135: #define WARN(condition, format...) ({                  \
             int __ret_warn_on = !!(condition);             \
             if (unlikely(__ret_warn_on))                   \
                 __WARN_printf(TAINT_WARN, format);         \
             unlikely(__ret_warn_on);                   \
         })
```

#### radix\_tree\_init (linux/lib/radix-tree.c:1592)

```txt
Control Flow:
start_kernel
    ...
    early_trace_init
    sched_init
    irqs_disabled
    WARN
    radix_tree_init <-- Here
```

#### maple\_tree\_init (linux/lib/maple\_tree.c:6230)

```txt
Control Flow:
start_kernel
    ...
    sched_init
    irqs_disabled
    WARN
    radix_tree_init
    maple_tree_init <-- Here
```

#### housekeeping\_init (linux/kernel/sched/isolation.c:82)

```txt
Control Flow:
start_kernel
    ...
    irqs_disabled
    WARN
    radix_tree_init
    maple_tree_init
    housekeeping_init <-- Here
```

#### workqueue\_init\_early (linux/kernel/workqueue.c:6004)

```txt
Control Flow:
start_kernel
    ...
    WARN
    radix_tree_init
    maple_tree_init
    housekeeping_init
    workqueue_init_early <-- Here
```

#### rcu\_init (linux/kernel/rcu/tiny.c:262)

```txt
Control Flow:
start_kernel
    ...
    radix_tree_init
    maple_tree_init
    housekeeping_init
    workqueue_init_early
    rcu_init <-- Here
```

#### trace\_init (linux/kernel/trace/trace.c:10439)

```txt
Control Flow:
start_kernel
    ...
    maple_tree_init
    housekeeping_init
    workqueue_init_early
    rcu_init
    trace_init <-- Here
```

#### initcall\_debug\_enable (linux/init/main.c:1259)

```txt
Control Flow:
start_kernel
    ...
    housekeeping_init
    workqueue_init_early
    rcu_init
    trace_init
    initcall_debug_enable <-- Here
```

#### context\_tracking\_init (linux/kernel/context\_tracking.c:719)

```txt
Control Flow:
start_kernel
    ...
    workqueue_init_early
    rcu_init
    trace_init
    initcall_debug_enable
    context_tracking_init <-- Here
```

#### early\_irq\_init (linux/kernel/irq/irqdesc.c:525)

```txt
Control Flow:
start_kernel
    ...
    rcu_init
    trace_init
    initcall_debug_enable
    context_tracking_init
    early_irq_init <-- Here
```

#### init\_IRQ (linux/arch/x86/kernel/irqinit.c:74)

```txt
Control Flow:
start_kernel
    ...
    trace_init
    initcall_debug_enable
    context_tracking_init
    early_irq_init
    init_IRQ <-- Here
```

#### tick\_init (linux/kernel/time/tick-common.c:574)

```txt
Control Flow:
start_kernel
    ...
    initcall_debug_enable
    context_tracking_init
    early_irq_init
    init_IRQ
    tick_init <-- Here
```

#### rcu\_init\_nohz (linux/kernel/rcu/tree\_nocb.h:1210)

```txt
Control Flow:
start_kernel
    ...
    context_tracking_init
    early_irq_init
    init_IRQ
    tick_init
    rcu_init_nohz <-- Here
```

#### init\_timers (liux/kerne/time/timer.c:2075)

```txt
Control Flow:
start_kernel
    ...
    early_irq_init
    init_IRQ
    tick_init
    rcu_init_nohz
    init_timers <-- Here
```

#### srcu\_init (linux/kernel/rcu/srcutiny.c:261)

```txt
Control Flow:
start_kernel
    ...
    init_IRQ
    tick_init
    rcu_init_nohz
    init_timers
    srcu_init <-- Here
```

#### hrtimers\_init (linux/kernel/time/hrtimer.c:2266)

```txt
Control Flow:
start_kernel
    ...
    tick_init
    rcu_init_nohz
    init_timers
    srcu_init
    hrtimers_init <-- Here
```

#### softirq\_init (linux/kernel/softirq.c:906)

```txt
Control Flow:
start_kernel
    ...
    rcu_init_nohz
    init_timers
    srcu_init
    hrtimers_init
    softirq_init <-- Here
```

#### timekeeping\_init (linux/kernel/time/timekeeping.c:1632)

```txt
Control Flow:
start_kernel
    ...
    init_timers
    srcu_init
    hrtimers_init
    softirq_init
    timekeeping_init <-- Here
```

#### time\_init (linux/arch/x86/kernel/time.c:119)

```txt
Control Flow:
start_kernel
    ...
    srcu_init
    hrtimers_init
    softirq_init
    timekeeping_init
    time_init <-- Here
```

#### random\_init (linux/drivers/char/random.c:862)

```txt
Control Flow:
start_kernel
    ...
    hrtimers_init
    softirq_init
    timekeeping_init
    time_init
    random_init <-- Here
```

#### kfence\_init (linux/mm/kfence/core.c:862)

```txt
Control Flow:
start_kernel
    ...
    softirq_init
    timekeeping_init
    time_init
    random_init
    kfence_init <-- Here
```

#### boot\_init\_stack\_canary (linux/arch/x86/include/asm/stackprotector.h:51)

```txt
Control Flow:
start_kernel
    ...
    timekeeping_init
    time_init
    random_init
    kfence_init
    boot_init_stack_canary <-- Here
```

#### perf\_event\_init (linux/kernel/events/core.c:13601)

```txt
Control Flow:
start_kernel
    ...
    time_init
    random_init
    kfence_init
    boot_init_stack_canary
    perf_event_init <-- Here
```

#### profile\_init (linux/kernel/profile.c:100)

```txt
Control Flow:
start_kernel
    ...
    random_init
    kfence_init
    boot_init_stack_canary
    perf_event_init
    profile_init <-- Here
```

#### call\_function\_init (linux/kernel/smp.c:149)

```txt
Control Flow:
start_kernel
    ...
    kfence_init
    boot_init_stack_canary
    perf_event_init
    profile_init
    call_function_init <-- Here
```

#### local\_irq\_enable (linux/include/linux/irqflags.h:205)

```txt
Control Flow:
start_kernel
    ...
    boot_init_stack_canary
    perf_event_init
    profile_init
    call_function_init
    local_irq_enable <-- Here
```

#### kmem\_cache\_init\_late (linux/mm/slab.c:1269)

```txt
Control Flow:
start_kernel
    ...
    perf_event_init
    profile_init
    call_function_init
    local_irq_enable
    kmem_cache_init_late <-- Here
```

#### console\_init (linux/kernel/printk/printk.c:3291)

```txt
Control Flow:
start_kernel
    ...
    profile_init
    call_function_init
    local_irq_enable
    kmem_cache_init_late
    console_init <-- Here
```

#### lockdep\_init (linux/kernel/locking/lockdep.c:453)

```txt
Control Flow:
start_kernel
    ...
    call_function_init
    local_irq_enable
    kmem_cache_init_late
    console_init
    lockdep_init <-- Here
```

#### locking\_selftest (linux/lib/locking-selftest.c:2857)

```txt
Control Flow:
start_kernel
    ...
    local_irq_enable
    kmem_cache_init_late
    console_init
    lockdep_init
    locking_selftest <-- Here
```

#### setup\_per\_cpu\_pageset (linux/mm/page\_alloc.c:7281)

```txt
Control Flow:
start_kernel
    ...
    kmem_cache_init_late
    console_init
    lockdep_init
    locking_selftest
    setup_per_cpu_pageset <-- Here
```

#### numa\_policy\_init (linux/mm/mempolicy.c:2899)

```txt
Control Flow:
start_kernel
    ...
    console_init
    lockdep_init
    locking_selftest
    setup_per_cpu_pageset
    numa_policy_init <-- Here
```

#### acpi\_early\_init (linux/drivers/acpi/bus.c:1177)

```txt
Control Flow:
start_kernel
    ...
    lockdep_init
    locking_selftest
    setup_per_cpu_pageset
    numa_policy_init
    acpi_early_init <-- Here
```

#### late\_time\_init ()

```txt
Control Flow:
start_kernel
    ...
    locking_selftest
    setup_per_cpu_pageset
    numa_policy_init
    acpi_early_init
    late_time_init <-- Here
```

#### sched\_clock\_init (linux/kernel/sched/clock.c:205)

```txt
Control Flow:
start_kernel
    ...
    setup_per_cpu_pageset
    numa_policy_init
    acpi_early_init
    late_time_init
    sched_clock_init <-- Here
```

#### calibrate\_delay (linux/init/calibrate.c:275)

```txt
Control Flow:
start_kernel
    ...
    numa_policy_init
    acpi_early_init
    late_time_init
    sched_clock_init
    calibrate_delay <-- Here
```

#### arch\_cpu\_finalize\_init (linux/arch/x86/kernel/cpu/common.c:2399)

```txt
Control Flow:
start_kernel
    ...
    acpi_early_init
    late_time_init
    sched_clock_init
    calibrate_delay
    arch_cpu_finalize_init <-- Here
```

#### pid\_idr\_init (linux/kernel/pid.c:650)

```txt
Control Flow:
start_kernel
    ...
    late_time_init
    sched_clock_init
    calibrate_delay
    arch_cpu_finalize_init
    pid_idr_init <-- Here
```

#### anon\_vma\_init (linux/mm/rmap.c:459)

```txt
Control Flow:
start_kernel
    ...
    sched_clock_init
    calibrate_delay
    arch_cpu_finalize_init
    pid_idr_init
    anon_vma_init <-- Here
```

#### efi\_enter\_virtual\_mode (linux/arch/x86/platform/efi/efi.c:834)

```txt
Control Flow:
start_kernel
    ...
    calibrate_delay
    arch_cpu_finalize_init
    pid_idr_init
    anon_vma_init
    efi_enter_virtual_mode <-- Here
```

#### thread\_stack\_cache\_init (linux/kernel/fork.c:408)

```txt
Control Flow:
start_kernel
    ...
    arch_cpu_finalize_init
    pid_idr_init
    anon_vma_init
    efi_enter_virtual_mode
    thread_stack_cache_init <-- Here
```

#### cred\_init (linux/kernel/cred.c:689)

```txt
Control Flow:
start_kernel
    ...
    pid_idr_init
    anon_vma_init
    efi_enter_virtual_mode
    thread_stack_cache_init
    cred_init <-- Here
```

#### fork\_init (linux/kernel/fork.c:911)

```txt
Control Flow:
start_kernel
    ...
    anon_vma_init
    efi_enter_virtual_mode
    thread_stack_cache_init
    cred_init
    fork_init <-- Here
```

#### proc\_caches\_init (linux/kernel/fork.c:3048)

```txt
Control Flow:
start_kernel
    ...
    efi_enter_virtual_mode
    thread_stack_cache_init
    cred_init
    fork_init
    proc_caches_init <-- Here
```

#### uts\_ns\_init (linux/kernel/utsname.c:169)

```txt
Control Flow:
start_kernel
    ...
    thread_stack_cache_init
    cred_init
    fork_init
    proc_caches_init
    uts_ns_init <-- Here
```

#### key\_init (linux/security/keys/key.c:1201)

```txt
Control Flow:
start_kernel
    ...
    thread_stack_cache_init
    fork_init
    proc_caches_init
    uts_ns_init
    key_init <-- Here
```

#### security\_init (linux/security/security.c:370)

```txt
Control Flow:
start_kernel
    ...
    thread_stack_cache_init
    proc_caches_init
    uts_ns_init
    key_init
    security_init <-- Here
```

#### dbg\_late\_init (linux/kernel/debug/debug\_core.c:1030)

```txt
Control Flow:
start_kernel
    ...
    proc_caches_init
    uts_ns_init
    key_init
    security_init
    dbg_late_init <-- Here
```

#### net\_ns\_init (linux/net/core/net\_namespace.c:1091)

```txt
Control Flow:
start_kernel
    ...
    uts_ns_init
    key_init
    security_init
    dbg_late_init
    net_ns_init <-- Here
```

#### vfs\_caches\_init (linux/fs/dcache.c:3344)

```txt
Control Flow:
start_kernel
    ...
    key_init
    security_init
    dbg_late_init
    net_ns_init
    vfs_caches_init <-- Here
```

#### pagecache\_init (linux/mm/filemap.c:1033)

```txt
Control Flow:
start_kernel
    ...
    security_init
    dbg_late_init
    net_ns_init
    vfs_caches_init
    pagecache_init <-- Here
```

#### signals\_init (linux/kernel/signal.c:4760)

```txt
Control Flow:
start_kernel
    ...
    dbg_late_init
    net_ns_init
    vfs_caches_init
    pagecache_init
    signals_init <-- Here
```

#### seq\_file\_init (linux/fs/seq\_file.c:1147)

```txt
Control Flow:
start_kernel
    ...
    net_ns_init
    vfs_caches_init
    pagecache_init
    signals_init
    seq_file_init <-- Here
```

#### proc\_root\_init (linux/fs/proc/root.c:285)

```txt
Control Flow:
start_kernel
    ...
    vfs_caches_init
    pagecache_init
    signals_init
    seq_file_init
    proc_root_init <-- Here
```

#### nsfs\_init (linux/fs/nsfs.c:300)

```txt
Control Flow:
start_kernel
    ...
    pagecache_init
    signals_init
    seq_file_init
    proc_root_init
    nsfs_init <-- Here
```

#### cpuset\_init (linux/kernel/cgroup/cpuset.c:3469)

```txt
Control Flow:
start_kernel
    ...
    signals_init
    seq_file_init
    proc_root_init
    nsfs_init
    cpuset_init <-- Here
```

#### cgroup\_init (linux/kernel/cgroup/cgropu.c:6070)

```txt
Control Flow:
start_kernel
    ...
    seq_file_init
    proc_root_init
    nsfs_init
    cpuset_init
    cgroup_init <-- Here
```

#### taskstats\_init\_early (linux/kernel/taskstats.c:696)

```txt
Control Flow:
start_kernel
    ...
    proc_root_init
    nsfs_init
    cpuset_init
    cgroup_init
    taskstats_init_early <-- Here
```

#### delayacct\_init (linux/kernel/delayacct.c:39)

```txt
Control Flow:
start_kernel
    ...
    nsfs_init
    cpuset_init
    cgroup_init
    taskstats_init_early
    delayacct_init <-- Here
```

#### acpi\_subsystem\_init (linux/drivers/acpi/bus.c:1248)

```txt
Control Flow:
start_kernel
    ...
    cpuset_init
    cgroup_init
    taskstats_init_early
    delayacct_init
    acpi_subsystem_init <-- Here
```

#### arch\_post\_acpi\_subsys\_init (linux/arch/x86/kernel/process.c:929)

```txt
Control Flow:
start_kernel
    ...
    cgroup_init
    taskstats_init_early
    delayacct_init
    acpi_subsystem_init
    arch_post_acpi_subsys_init <-- Here
```

#### kcsan\_init (linux/kernel/kcsan/core.c:794)

```txt
Control Flow:
start_kernel
    ...
    taskstats_init_early
    delayacct_init
    acpi_subsystem_init
    arch_post_acpi_subsys_init
    kcsan_init <-- Here
```

#### arch\_call\_rest\_init (linux/init/main.c:889)

```txt
Control Flow:
start_kernel
    ...
    delayacct_init
    acpi_subsystem_init
    arch_post_acpi_subsys_init
    kcsan_init
    arch_call_rest_init <-- Here

891: Calls rest_init.
```

#### rest\_init (linux/init/main.c:685)

```txt
Control Flow:
start_kernel
    ...
    delayacct_init
    acpi_subsystem_init
    arch_post_acpi_subsys_init
    kcsan_init
    arch_call_rest_init
        rest_init <-- Here

690: Calls rcu_scheduler_starting.

696: Calls user_mode_thread for kernel_init.

703: Calls find_task_by_pid_ns.

705: Calls set_cpus_allowed_ptr.

708: Calls numa_default_policy.

709: Calls kernel_thread for kthreadd.

711: calls find_task_by_pid_ns.

723: Calls complete.

729: Calls schedule_preempt_disabled.

731: Calls cpu_startup_entry.
```

#### rcu\_scheduler\_starting (linux/kernel/rcu/srcutiniy.c:251)

```txt
Control Flow:
start_kernel
    ...
    acpi_subsystem_init
    arch_post_acpi_subsys_init
    kcsan_init
    arch_call_rest_init
    rcu_scheduler_starting <-- Here
```

#### user\_mode\_thread (linux/kernel/fork.c:2748)

```txt
Control Flow:
start_kernel
    ...
    arch_post_acpi_subsys_init
    kcsan_init
    arch_call_rest_init
    rcu_scheduler_starting
    user_mode_thread <-- Here
```

#### kernel\_init (linux/init/main.c:1503)

```txt
Control Flow:
kernel_init <-- Here (user_mode_thread)

1510: Calls wait_for_completion.

1512: Calls kernel_init_freeable.

1514: Calls async_synchronize_full.

1517: Calls kprobe_free_init_mem.

1518: Calls ftrace_free_init_mem.

1519: Calls kgdb_free_init_mem.

1520: Calls exit_boot_config.

1521: Calls free_initmem.

1522: Calls mark_readonly.

1528: Calls pti_finalize.

1531: Calls numa_default_policy.

1533: calls rcu_end_inkernel_boot.

1535: Calls do_sysctl_args.

1538: Calls run_init_process.

1552: Calls run_init_process.

1560: Calls run_init_process.

1568: Calls try_to_run_init_process.
```

#### wait\_for\_completion (linux/kernel/sched/completion.c:136)

```txt
Control Flow:
kernel_init
    wait_for_completion <-- Here

138: Calls wait_for_common.
```

#### wait\_for\_common (linux/kernel/sched/completion.c:115)

```txt
Control Flow:
kernel_init
    wait_for_completion
        wait_for_common <-- Here

117: return __wait_for_common(x, schedule_timeout, timeout, state);
```

#### \_\_wait\_for\_common (linux/kernel/sched/completion.c:98)

```txt
Control Flow:
kernel_init
    wait_for_completion
        wait_for_common
            __wait_for_common <-- Here
```

#### kernel\_init\_freeable (linux/init/main.c:1593)

```txt
Control Flow:
kernel_init
    wait_for_completion
    kernel_init_freeable <-- Here
```

#### async\_synchronize\_full (linux/kernel/async.c:239)

```txt
Control Flow:
kernel_init
    wait_for_completion
    kernel_init_freeable
    async_synchronize_full <-- Here

241: Calls async_synchronize_full_domain.
```

#### async\_synchronize\_full\_domain (linux/kernel/async.c:252)

```txt
Control Flow:
kernel_init
    wait_for_completion
    kernel_init_freeable
    async_synchronize_full
        async_synchronize_full_domain <-- Here
```

#### kprobe\_free\_init\_mem (linux/kernel/kprobes.c:2683)

```txt
Control Flow:
kernel_init
    wait_for_completion
    kernel_init_freeable
    async_synchronize_full
    kprobe_free_init_mem <-- Here
```

#### ftrace\_free\_init\_mem (linux/kernel/trace/ftrace.c:7402)

```txt
Control Flow:
kernel_init
    wait_for_completion
    kernel_init_freeable
    async_synchronize_full
    kprobe_free_init_mem
    ftrace_free_init_mem <-- Here
```

#### kgdb\_free\_init\_mem (linux/kernel/debug/debug\_core.c:444)

```txt
Control Flow:
kernel_init
    ...
    kernel_init_freeable
    async_synchronize_full
    kprobe_free_init_mem
    ftrace_free_init_mem
    kgdb_free_init_mem <-- Here
```

#### exit\_boot\_config (linux/init/main.c:465)

```txt
Control Flow:
kernel_init
    ...
    async_synchronize_full
    kprobe_free_init_mem
    ftrace_free_init_mem
    kgdb_free_init_mem
    exit_boot_config <-- Here
```

#### free\_initmem (linux/init/main.c:1498)

```txt
Control Flow:
kernel_init
    ...
    kprobe_free_init_mem
    ftrace_free_init_mem
    kgdb_free_init_mem
    exit_boot_config
    free_initmem <-- Here
```

#### mark\_readonly (linux/init/main.c:1487)

```txt
Control Flow:
kernel_init
    ...
    ftrace_free_init_mem
    kgdb_free_init_mem
    exit_boot_config
    free_initmem
    mark_readonly <-- Here
```

#### pti\_finalize (linux/arch/x86/mm/pti.c:654)

```txt
Control Flow:
kernel_init
    ...
    kgdb_free_init_mem
    exit_boot_config
    free_initmem
    mark_readonly
    pti_finalize <-- Here
```

#### numa\_default\_policy (linux/mm/mempolicy.c:2953)

```txt
Control Flow:
kernel_init
    ...
    exit_boot_config
    free_initmem
    mark_readonly
    pti_finalize
    numa_default_policy <-- Here
```

#### rcu\_end\_inkernel\_boot (linux/kernel/rcu/update.c:195)

```txt
Control Flow:
kernel_init
    ...
    free_initmem
    mark_readonly
    pti_finalize
    numa_default_policy
    rcu_end_inkernel_boot <-- Here
```

#### do\_sysctl\_args (linux/fs/proc/proc\_sysctl.c:1935)

```txt
Control Flow:
kernel_init
    ...
    mark_readonly
    pti_finalize
    numa_default_policy
    rcu_end_inkernel_boot
    do_sysctl_args <-- Here
```

#### run\_init\_process (linux/init/main.c:1416)

```txt
Control Flow:
kernel_init
    ...
    pti_finalize
    numa_default_policy
    rcu_end_inkernel_boot
    do_sysctl_args
    run_init_process <-- Here
```

#### try\_to\_run\_init\_process (linux/init/main.c:1431)

```txt
Control Flow:
kernel_init
    ...
    numa_default_policy
    rcu_end_inkernel_boot
    do_sysctl_args
    run_init_process
    try_to_run_init_process <-- Here
```

#### prevent\_tail\_call\_optimization (linux/include/linux/compiler.h:244)

```txt
Control Flow:
start_kernel
    ...
    acpi_subsystem_init
    arch_post_acpi_subsys_init
    kcsan_init
    user_mode_thread (kernel_init)
    prevent_tail_call_optimization <-- Here
```

