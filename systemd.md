## Walkthrough of Debian 12.40's _systemd_ Program 

#### main (systemd/src/core/main.c:2690)

```txt
Control Flow:
main <-- Here

2714: Calls redirect_telinit.

2717: Calls dual_timestamp_from_monotonic.

2718: Calls dual_timestamp_get.

2722: calls early_skip_setup_check.

2728: Calls __prctl.

2731: Calls save_argc_argv.

2735: Calls save_env.

2742: Calls log_set_upgrade_syslog_to_journal.

2744: Calls getpid_cached.

2749: Calls umask.

2753: Calls log_set_prohibit_ipc.

2759: Calls log_set_always_reopen_console.

2760: Calls detect_container.

2764: Calls log_set_target on LOG_TARGET_KMSG.

2765: Calls log_open.

2767: Calls in_initrd.

2771: Calls mount_setup_early.

2780: Calls log_open.

2782: Calls disable_printk_ratelimit.

2784-2788: Calls initialize_security.

2793: Calls mac_selinux_init.

2798-2799: Calls initialize_clock.

2805: Calls log_set_target on LOG_TARGET_JOURNAL_OR_KMSG.

2819: Calls initialize_coredump.

2821: Calls fixup_environment.

2830: Calls colors_enabled and log_show_color.

2832: Calls make_null_stdio.

2837-2838: Calls kmod_setup.

2841: Calls mount_setup.

2848: Calls efi_take_random_seed.

2851-2852: Calls cache_efi_options_variable.

2876: Calls save_rlimits.

2879: Calls reset_all_signal_handlers.

2880: Calls ingore_signals.

2882: Calls parse_configuration.

2884: Calls parse_argv.

2890: Calls safety_checks.

2894-2895: Calls pager_open.

2928: Calls apply_clock_update.

2931: Calls cmdline_take_random_seed.

2935: Calls initialize_core_pattern.

2938: Calls log_close.

2941: Calls collect_fds.

2946: Calls setup_console_terminal.

2949: Calls log_open.

2952: Calls log_execution_mode.

2954-2958: Calls initialize_runtime.

2962-2964: Calls manager_new.

2977: Calls set_manager_defaults.

2978: Calls set_manager_settings.

2979: Calls manager_set_first_boot.

2980: Calls manager_set_switching_root.

2985: Calls now.

2987: Calls manager_startup.

2994: Calls fdset_free.

2995: Calls safe_fclose.

2997-2998: Calls do_queue_default_job.

3003: Calls now.

3005: Calls log_full.

3015-3023: Calls invoke_main_loop.

(I might continue from here if I study shutdown in the future)
```

#### redirect\_telinit (sytsemd-src/core/main.c:1453)

```txt
Control Flow:
main
    redirect_telinit <-- Here
```

#### dual\_timestamp\_from\_monotonic (systemd/src/basic/time-util.c:159)

```txt
Control Flow:
main
    redirect_telinit
    dual_timestamp_from_monotonic <-- Here
```

#### dual\_timestamp\_get (systemd/src/basic/time-util.c:67)

```txt
Control Flow:
main
    redirect_telinit
    dual_timestamp_from_monotonic
    dual_timestamp_get <-- Here
```

#### early\_skip\_setup\_check (systemd/src/core/main.c:2663)

```txt
Control Flow:
main
    redirect_telinit
    dual_timestamp_from_monotonic
    dual_timestamp_get
    early_skip_setup_check <-- Here
```

#### prctl (glibc/sysdepsunix/sysv/linux/prctl.c:29)

```txt
Control Flow:
main
    redirect_telinit
    dual_timestamp_from_monotonic
    dual_timestamp_get
    early_skip_setup_check
    prctl <-- here

38: return INLINE_SYSCALL_CALL (prctl, option, arg2, arg3, arg4, arg5);
```

#### \_\_prctl (linux/kernel/sys.c:2364)

```txt
Control Flow:
main
    redirect_telinit
    dual_timestamp_from_monotonic
    dual_timestamp_get
    early_skip_setup_check
    __prctl
        prctl <-- Here
``` 

#### save\_argc\_argv (systemd/src/basic/util.h:11)

```txt
Control Flow:
main
    ...
    dual_timestamp_from_monotonic
    dual_timestamp_get
    early_skip_setup_check
    __prctl
    save_argc_argv <-- Here
```

#### save\_env (systemd/src/core/main.c:2679)

```txt
Control Flow:
main
    ...
    dual_timestamp_get
    early_skip_setup_check
    __prctl
    save_argc_argv
    save_env <-- Here
```

#### log\_set\_upgrade\_syslog\_to\_journal (systemd/src/basic/log.c:1450)

```txt
Control Flow:
main
    ...
    early_skip_setup_check
    __prctl
    save_argc_argv
    save_env
    log_set_upgrade_syslog_to_journal <-- Here
```

#### getpid\_cached (systemd/src/basic/process-util.c:1153)

```txt
Control Flow:
main
    ...
    __prctl
    save_argc_argv
    save_env
    log_set_upgrade_syslog_to_journal
    getpid_cached <-- Here
```

#### umask ()

```txt
Control Flow:
main
    ...
    save_argc_argv
    save_env
    log_set_upgrade_syslog_to_journal
    getpid_cached
    umask <-- Here
```

#### log\_set\_prohibit\_ipc (systemd/src/basic/log.c:1466)

```txt
Control Flow:
main
    ...
    save_env
    log_set_upgrade_syslog_to_journal
    getpid_cached
    umask
    log_set_prohibit_ipc <-- Here

1471: prohibit_ipc = b;
```

#### log\_set\_always\_reopen\_console (systemd/src/basic/log.c:1462)

```txt
Control Flow:
main
    ...
    log_set_upgrade_syslog_to_journal
    getpid_cached
    umask
    log_set_prohibit_ipc
    log_set_always_reopen_console <-- Here

1463: always_reopen_console = b;
```

#### detect\_container (systemd/src/basic/virt.c:659)

```txt
Control Flow:
main
    ...
    getpid_cached
    umask
    log_set_prohibit_ipc
    log_set_always_reopen_console
    detect_container <-- Here
```

#### log\_set\_target (systemd/src/basic/log.c:329)

```txt
Control Flow:
main
    ...
    umask
    log_set_prohibit_ipc
    log_set_always_reopen_console
    detect_container
    log_set_target <-- Here
```

#### log\_open (systemd/src/basic/log.c:251)

```txt
Control Flow:
main
    ...
    log_set_prohibit_ipc
    log_set_always_reopen_console
    detect_container
    log_set_target
    log_open <-- Here
```

#### in\_initrd (systemd/src/basic/util.c:53)

```txt
Control Flow:
main
    ...
    log_set_always_reopen_console
    detect_container
    log_set_target
    log_open
    in_initrd <-- Here
```

#### mount\_setup\_early (systemd/src/shared/mount-setup.c:233)

```txt
Control Flow:
main
    ...
    detect_container
    log_set_target
    log_open
    in_initrd
    mount_setup_early <-- Here
```

#### disable\_printk\_ratelimit (systemd/src/core/manager.c:4014)

```txt
Control Flow:
main
    ...
    log_set_target
    log_open
    in_initrd
    mount_setup_early
    disable_printk_ratelimit <-- Here
```

#### initialize\_security (systemd/src/core/main.c:2575)

```txt
Control Flow:
main
    ...
    log_open
    in_initrd
    mount_setup_early
    disable_printk_ratelimit
    initialize_security <-- Here
```

#### mac\_selinux\_init (systemd/src/shared/selinux-util.c:149)

```txt
Control Flow:
main
    ...
    in_initrd
    mount_setup_early
    disable_printk_ratelimit
    initialize_security
    mac_selinux_init <-- Here
```

#### initialize\_clock (systemd/src/core/main.c:1559)

```txt
Control Flow:
main
    ...
    mount_setup_early
    disable_printk_ratelimit
    initialize_security
    mac_selinux_init
    initialize_clock <-- Here
```

#### initialize\_coredump (systemd/src/core/main.c:2819)

```txt
Control Flow:
main
    ...
    disable_printk_ratelimit
    initialize_security
    mac_selinux_init
    initialize_clock
    initialize_coredump <-- Here
```

#### fixup\_environment (systemd/src/core/main.c:1419)

```txt
Control Flow:
main
    ...
    initialize_security
    mac_selinux_init
    initialize_clock
    initialize_coredump
    fixup_environment <-- Here
```

#### colors\_enabled (systemd/src/basic/terminal-util.h:160)

```txt
Control Flow:
main
    ...
    mac_selinux_init
    initialize_clock
    initialize_coredump
    fixup_environment
    colors_enabled <-- Here

163: return get_color_mode() != COLOR_OFF;
```

#### get\_color\_mode (systemd/src/basic/terminal-util.c:1262)

```txt
Control Flow:
main
    ...
    mac_selinux_init
    initialize_clock
    initialize_coredump
    fixup_environment
    colors_enabled
        get_color_mode <-- Here
```

#### log\_show\_color (systemd/src/basic/log.c:1236)

```txt
Control Flow:
main
    ...
    initialize_clock
    initialize_coredump
    fixup_environment
    colors_enabled
    log_show_color <-- Here

1237: return show_color > 0; /* Defaults to false. */
```

#### make\_null\_stdio (systemd/src/basic/fd-util.h:88)

```txt
Control Flow:
main
    ...
    initialize_coredump
    fixup_environment
    colors_enabled
    log_show_color
    make_null_stdio <-- Here

89: return rearrange_stdio(-1, -1, -1);
```

#### rearrange\_stdio (systemd/src/basic/fd-util.c:633)

```txt
Control Flow:
main
    ...
    initialize_coredump
    fixup_environment
    colors_enabled
    log_show_color
    make_null_stdio
        rearrange_stdio <-- Here
```

#### kmod\_setup (systemd/src/core/kmod-setup.c:92)

```txt
Control Flow:
main
    ...
    fixup_environment
    colors_enabled
    log_show_color
    make_null_stdio
    kmod_setup <-- Here
```

#### mount\_setup (systemd/src/shared/mount-setup.c:503)

```txt
Control Flow:
main
    ...
    colors_enabled
    log_show_color
    make_null_stdio
    kmod_setup
    mount_setup <-- Here
```

#### efi\_take\_random\_seed (systemd/src/core/efi-random.c:43)

```txt
Control Flow:
main
    ...
    log_show_color
    make_null_stdio
    kmod_setup
    mount_setup
    efi_take_random_seed <-- Here
```

#### cache\_efi\_options\_variable (systemd/src/basic/efivars.c:379)

```txt
Control Flow:
main
    ...
    make_null_stdio
    kmod_setup
    mount_setup
    efi_take_random_seed
    cache_efi_options_variable <-- Here
```

#### save\_rlimits (systemd/src/core/main.c:2298)

```txt
Control Flow:
main
    ...
    kmod_setup
    mount_setup
    efi_take_random_seed
    cache_efi_options_variable
    save_rlimits <-- Here
```

#### reset\_all\_signal\_handlers (systemd/src/basic/signal-util.c:15)

```txt
Control Flow:
main
    ...
    mount_setup
    efi_take_random_seed
    cache_efi_options_variable
    save_rlimits
    reset_all_signal_handlers <-- Here
```

#### ignore\_signals (systemd/src/basic/signal-util.h:13)

```txt
Control Flow:
main
    ...
    efi_take_random_seed
    cache_efi_options_variable
    save_rlimits
    reset_all_signal_handlers
    ignore_signals <-- Here

13-20: #define ignore_signals(...)                                             \
         sigaction_many_internal(                                        \
                         &(const struct sigaction) {                     \
                                 .sa_handler = SIG_IGN,                  \
                                 .sa_flags = SA_RESTART                  \
                         },                                              \
                         __VA_ARGS__,                                    \
                         -1)
```

#### parse\_configuration (systemd/src/core/main.c:2488)

```txt
Control Flow:
main
    ...
    cache_efi_options_variable
    save_rlimits
    reset_all_signal_handlers
    ignore_signals
    parse_configuration <-- Here
```

#### parse\_argv (systemd/src/core/main.c:789)

```txt
Control Flow:
main
    ...
    save_rlimits
    reset_all_signal_handlers
    ignore_signals
    parse_configuration
    parse_argv <-- Here
```

#### safety\_checks (systemd/src/core/main.c:2531)

```txt
Control Flow:
main
    ...
    reset_all_signal_handlers
    ignore_signals
    parse_configuration
    parse_argv
    safety_checks <-- Here
```

#### pager\_open (sytemd/src/shared/pager.c:86)

```txt
Control Flow:
main
    ...
    ignore_signals
    parse_configuration
    parse_argv
    safety_checks
    pager_open <-- Here
```

#### apply\_clock\_update (systemd/src/core/main.c:1608)

```txt
Control Flow:
main
    ...
    parse_configuration
    parse_argv
    safety_checks
    pager_open
    apply_clock_update <-- Here
```

#### cmdline\_take\_random\_seed (systemd/src/core/main.c:1625)

```txt
Control Flow:
main
    ...
    parse_argv
    safety_checks
    pager_open
    apply_clock_update
    cmdline_take_random_seed <-- Here
```

#### initialize\_core\_pattern (systemd/src/core/main.c:1671)

```txt
Control Flow:
main
    ...
    safety_checks
    pager_open
    apply_clock_update
    cmdline_take_random_seed
    initialize_core_pattern <-- Here
```

#### log\_close (systemd/src/basic/log.c:343)

```txt
Control Flow:
main
    ...
    pager_open
    apply_clock_update
    cmdline_take_random_seed
    initialize_core_pattern
    log_close <-- Here
```

#### collect\_fds (systemd/src/core/main.c:2618)

```txt
Control Flow:
main
    ...
    apply_clock_update
    cmdline_take_random_seed
    initialize_core_pattern
    log_close
    collect_fds <-- Here
```

#### setup\_console\_terminal (systemd/src/core/main.c:2646)

```txt
Control Flow:
main
    ...
    cmdline_take_random_seed
    initialize_core_pattern
    log_close
    collect_fds
    setup_console_terminal <-- Here
```

#### log\_execution\_mode (systemd/src/core/main.c:2042)

```txt
Control Flow:
main
    ...
    initialize_core_pattern
    log_close
    collect_fds
    setup_console_terminal
    log_execution_mode <-- Here
```

#### initialize\_runtime (systemd/src/core/main.c:2119)

```txt
Control Flow:
main
    ...
    log_close
    collect_fds
    setup_console_terminal
    log_execution_mode
    initialize_runtime <-- Here
```

#### manager\_new (systemd/src/core/manager.c:812)

```txt
Control Flow:
main
    ...
    collect_fds
    setup_console_terminal
    log_execution_mode
    initialize_runtime
    manager_new <-- Here
```

#### set\_manager\_defaults (systemd/src/core/main.c:710)

```txt
Control Flow:
main
    ...
    setup_console_terminal
    log_execution_mode
    initialize_runtime
    manager_new
    set_manager_defaults <-- Here
```

#### set\_manager\_settings (systemd/src/core/main.c:755)

```txt
Control Flow:
main
    ...
    log_execution_mode
    initialize_runtime
    manager_new
    set_manager_defaults
    set_manager_settings <-- Here
```

#### manager\_set\_first\_boot (systemd/src/core/manager.c:4165)

```txt
Control Flow:
main
    ...
    initialize_runtime
    manager_new
    set_manager_defaults
    set_manager_settings
    manager_set_first_boot <-- Here
```

#### manager\_set\_switching\_root (systemd/src/core/manager.c:808)

```txt
Control Flow:
main
    ...
    manager_new
    set_manager_defaults
    set_manager_settings
    manager_set_first_boot
    manager_set_switching_root <-- Here

809: m->switching_root = MANAGER_IS_SYSTEM(m) && switching_root;
```

#### now (systemd/src/basic/time-util.c:51)

```txt
Control Flow:
main
    ...
    set_manager_defaults
    set_manager_settings
    manager_set_first_boot
    manager_set_switching_root <-- Here
    now <-- Here

56: return timespec_load(&ts);
```

#### timespec\_load (systemd/src/basic/time-util.c:206)

```txt
Control Flow:
main
    ...
    set_manager_defaults
    set_manager_settings
    manager_set_first_boot
    manager_set_switching_root <-- Here
    now
        timespec_load <-- Here
```
#### manager\_startup (systemd/src/core/manager.c:1839)

```txt
Control Flow:
main
    ...
    set_manager_settings
    manager_set_first_boot
    manager_set_switching_root
    now
    manager_startup <-- Here

1846-1848: Calls lookup_paths_init_or_warn.

1852: Calls manager_timestamp_initrd_mangle and dual_timestamp_get.

1853: Calls manager_run_environment_generators.

1854-1855: Calls manager_run_generators.

1856: Calls manager_timestamp_initrd_mangle and dual_timestamp_get.

1860: Calls manager_preset_all.

182: Calls lookup_paths_logs.

1869-1870: Calls rm_rf.

1878: Calls manager_timestamp_initrd_mangle and dual_timestamp_get.

1879: Calls manager_enumerate_perpetual.

1880: Calls manager_enumerate.

1881: Calls manager_timestamp_initrd_mangle and dual_timestamp_get.

1893: Calls manager_distribute_fds.

1896: Calls manager_setup_notify.

1901: Calls manager_setup_cgroups_agent.

1906: Calls manager_setup_user_lookup_fd.

1912: Calls manager_setup_bus.

1915: Calls bus_track_coldplug.

1918: Calls strv_free.

1920: Calls manager_varlink_init.

1925: Calls manager_coldplug.

1928: Calls manager_vacuum.

1936: Calls manager_ready.

1938: Calls manager_set_switching_root.

1940: Returns zero.
```

#### lookup\_paths\_init\_or\_warn (systemd/src/basic/path-lookup.c:744)

```txt
Control Flow:
main
    ...
    manager_startup
        lookup_paths_init_or_warn <-- Here
```

#### manager\_timestamp\_initrd\_mangle (systemd/src/core/manager.c:4661)

```txt
Control Flow:
main
    ...
    manager_startup
        lookup_paths_init_or_warn
        manager_timestamp_initrd_mangle <-- Here
```

#### manager\_run\_environment\_generators (systemd/src/core/manager.c:3718)

```txt
Control Flow:
main
    ...
    manager_startup
        lookup_paths_init_or_warn
        manager_timestamp_initrd_mangle
        manager_run_environment_generators <-- Here
```

#### manager\_run\_generators (systemd/src/core/manager.c:3804)

```txt
Control Flow:
main
    ...
    manager_startup
        lookup_paths_init_or_warn
        manager_timestamp_initrd_mangle
        manager_run_environment_generators
        manager_run_generators <-- Here
```

#### manager\_preset\_all (systemd/src/core/manager.c:1780)

```txt
Control Flow:
main
    ...
    manager_startup
        lookup_paths_init_or_warn
        manager_timestamp_initrd_mangle
        manager_run_environment_generators
        manager_run_generators
        manager_preset_all <-- Here
```

#### lookup\_paths\_logs (systemd/src/basic/path-lookup.c:779)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_timestamp_initrd_mangle
        manager_run_environment_generators
        manager_run_generators
        manager_preset_all
        lookup_paths_log <-- Here
```

#### rm\_rf (systemd/src/shared/rm-rf.c:428)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_run_environment_generators
        manager_run_generators
        manager_preset_all
        lookup_paths_log
        rm_rf <-- Here
```

#### manager\_enumerate\_perpetual (systemd/src/core/manager.c:1638)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_run_generators
        manager_preset_all
        lookup_paths_log
        rm_rf
        manager_enumerate_perpetual <-- Here
```

#### manager\_enumerate (systemd/src/core/manager.c:1656)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_preset_all
        lookup_paths_log
        rm_rf
        manager_enumerate_perpetual
        manager_enumerate <-- Here
```

#### manager\_distribute\_fds (systemd/src/core/manager.c:1717)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        lookup_paths_log
        rm_rf
        manager_enumerate_perpetual
        manager_enumerate
        manager_distribute_fds <-- Here
```

#### manager\_setup\_notify (systemd/src/core/manager.c:998)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        rm_rf
        manager_enumerate_perpetual
        manager_enumerate
        manager_distribute_fds
        manager_setup_notify <-- Here
```

#### manager\_setup\_cgroups\_agent (systemd/src/core/manager.c:1062)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_enumerate_perpetual
        manager_enumerate
        manager_distribute_fds
        manager_setup_notify
        manager_setup_cgroups_agent <-- Here
```

#### manager\_setup\_user\_lookup\_fd (systemd/src/core/manager.c:1136)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_enumerate
        manager_distribute_fds
        manager_setup_notify
        manager_setup_cgroups_agent
        manager_setup_user_lookup_fd <-- Here
```

#### manager\_setup\_bus (systemd/src/core/manager.c:1761)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_distribute_fds
        manager_setup_notify
        manager_setup_cgroups_agent
        manager_setup_user_lookup_fd
        manager_setup_bus <-- Here
```

#### bus\_track\_coldplug (systemd/src/core/dbus.c:1140)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_setup_notify
        manager_setup_cgroups_agent
        manager_setup_user_lookup_fd
        manager_setup_bus
        bus_track_coldplug <-- Here
```

#### strv\_free (systemd/src/basic/strv.c:66)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_setup_cgroups_agent
        manager_setup_user_lookup_fd
        manager_setup_bus
        bus_track_coldplug
        strv_free <-- Here
```

#### manager\_varlink\_init (systemd/src/core/core-varlink.c:615)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_setup_user_lookup_fd
        manager_setup_bus
        bus_track_coldplug
        strv_free
        manager_varlink_init <-- Here

616: return MANAGER_IS_SYSTEM(m) ? manager_varlink_init_system(m) : manager_varlink_init_user(m);
```

#### manager\_varlink\_init\_system ()

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_setup_user_lookup_fd
        manager_setup_bus
        bus_track_coldplug
        strv_free
        manager_varlink_init
            manager_varlink_init_system <-- Here
```

#### manager\_coldplug (systemd/src/core/manager.c:1676)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        manager_setup_bus
        bus_track_coldplug
        strv_free
        manager_varlink_init
        manager_coldplug <-- Here
```

#### manager\_vacuum (systemd/src/core/manager.c:4441)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        bus_track_coldplug
        strv_free
        manager_varlink_init
        manager_coldplug
        manager_vacuum <-- Here
```

#### manager\_ready (systemd/src/core/manager.c:1805)

```txt
Control Flow:
main
    ...
    manager_startup
        ...
        strv_free
        manager_varlink_init
        manager_coldplug
        manager_vacuum
        manager_ready <-- Here
```

#### fdset\_free (systemd/src/shared/fdset.c:75)

```txt
Control Flow:
main
    ...
    manager_set_first_boot
    manager_set_switching_root
    now
    manager_startup
    fdset_free <-- Here
```

#### safe\_fclose (systemd/src/basic/fd-util.c:121)

```txt
Control Flow:
main
    ...
    manager_set_switching_root
    now
    manager_startup
    fdset_free
    safe_fclose <-- Here
```

#### do\_queue\_default\_job (systemd/src/core/main.c:2232)

```txt
Control Flow:
main
    ...
    now
    manager_startup
    fdset_free
    safe_fclose
    do_queue_default_job <-- Here
```

#### log\_full (systemd/src/basic/log.h:229)

```txt
Control Flow:
main
    ...
    manager_startup
    fdset_free
    safe_fclose
    do_queue_default_job
    log_full <-- Here

229-234: #define log_full(level, fmt, ...)                                      \
                 ({                                                             \
                          if (BUILD_MODE_DEVELOPER)                              \
                                  assert(!strstr(fmt, "%m"));                    \
                          (void) log_full_errno_zerook(level, 0, fmt, ##__VA_ARGS__); \
                 })
```

#### invoke\_main\_loop (systemd/src/core/mainc:1898)

```txt
Control Flow:
main
    ...
    fdset_free
    safe_fclose
    do_queue_default_job
    log_full
    invoke_main_loop <-- Here
```

