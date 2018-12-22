---
layout: post
title: Linux Tracing
---


# {{ page.title }}

I read Julia Evans' [post on Linux Tracing Systems](https://jvns.ca/blog/2017/07/05/linux-tracing-systems/)
and was inspried to learn more about the topic of tracing and debugging.

## Summary (Linux Tracing Systems)

Linux Tracing Systems and how they fit together:

* __Data Sources__ - kernel functions (k probes), kernel tracepoints, uprobes (userspace c functions), usdt /dtrace probes, ltting userspace tracing
* __Ways to extract tracing data__ - perf, ftrace, LTTng, system tap, eBPDF, sysdig
* __Frontends__ - pef, ftrace, trace-cmd, catapult, kernelshark, trace compass, bcc, sysdig, LTTng, system tap

### Data Sources

__Probes__

Probes are embedded "markers" that can be observed by say a DTrace or SystemTap script so you can easily monitor
what the process on a system is really doing.

__k probes__ - __k__ stands for kernel. __KProbes__ is a way to debug the Linux kernel by monitoring events inside a production system.
__u probes__ - __u__ stands for user. __UProbes__ is user-level dynamic tracing; it lets you trace user-level functions

__Tracepoints__

A __tracepoint__ is something you compile into your program. When someone using your program wnats to see
what happens when that tracepoint is hit and extract data, they can 'enable'/'activate' that tracepoint.

1. Compile a tracepoint into your program
2. As long as nobody activates it, no overhead
3. Your users can activate the tracepoints with tools (e.g. _ftrace_ / _dtrace_) to know what your program is doing

Tracepoints for userspace programs

* `dtrace` probes / `USDT` (User-Level Statically Defined Tracing) probes - You can compile Python (e.g. CPython) + other programs with dtrace probes
* `ltting-ust`

Tracepoints for the kernel

* kernel tracepoints

### Ways to extract tracing data 

There's a few ways to collect tracing data. These 3 are built into the Linux kernel:

* ftrace, uprobes, kprobes, kernel tracing, where you read from and write to files at `/sys/kernel/debug/tracing/`

    /sys/kernel/debug/tracing# ls
    available_events            events                    options              set_event_pid       stack_trace         trace_stat
    available_filter_functions  free_buffer               per_cpu              set_ftrace_filter   stack_trace_filter  tracing_cpumask
    available_tracers           function_profile_enabled  printk_formats       set_ftrace_notrace  trace               tracing_max_latency
    buffer_size_kb              hwlat_detector            README               set_ftrace_pid      trace_clock         tracing_on
    buffer_total_size_kb        instances                 saved_cmdlines       set_graph_function  trace_marker        tracing_thresh
    current_tracer              kprobe_events             saved_cmdlines_size  set_graph_notrace   trace_marker_raw    uprobe_events
    dyn_ftrace_total_info       kprobe_profile            saved_tgids          snapshot            trace_options       uprobe_profile
    enabled_functions           max_graph_depth           set_event            stack_max_size      trace_pipe

* `perf_events`, kernel tracepoints works by 1.) calling the `perf_event_open` syscall 2.) kernel writes data to a ring buffer `perf buffer`
* eBPF, uprobes, kprobes, dtrace probes, kernel tracepoints - 1.) Write a eBPF program 2.) Ask Linux to attach it to a kprobe/uprobe/tracepoint 3.) eBPF program sends data to userspace with ftrace/perf/BPF maps

Other ways to extract data:

* `SystemTap` - write some C code, compile it into a custom kernel module, insert that module into the kernel
* `LTTng` - Insert the LTTng kernel module, then use the LTTng tools to get it to collect data for you
* `sysdig` - just traces the system calls

### Frontends

Frontends are tools to help you:

* tell the kernel what data to collect / programs to run
* display the data in a useful way

These include:

`perf trace` - `perf` can use `perf_event_open` and also `ftrace` to record tracing data. You can use `perf trace` to trace syscalls.
`ftrace` - no frontend, just cat this text file
`trace-cmd` - command line user interface to ftrace
`perf-tools` - a collection of tools by Brendan Gregg for perf and ftrace. check out the kprobe and uprobe scripts.

Additional frontends:

`bcc` for eBPF - Python framework to help write eBPF programs at: https://github.com/iovisor/bcc

## `strace` command

The `strace` command traces **system calls** and **signals**. Strace is used in simple examples like running a command until it exits, to diagnostics (like **profiling**) and **debugging** like finding race conditions.

### `strace` example

When we look up the manual page (`man strace`), we see this example:

    $strace cat /dev/null

The man pages output says that the output should be

    open("/dev/null", O_RDONLY) = 3

When you do the command on an actual system, the output looks like this...

    execve("/bin/cat", ["cat", "/dev/null"], [/* 51 vars */]) = 0
    brk(NULL)
    access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory)
    mmap(NULL, 8192, PROT_READ|PROT_WRITE)......
    ...
    open("/dev/null", O_RDONLY) = 3

#### `strace` results explained

So what is going on?

* `execve()` executes the program pointed to by the filename; program either needs to be a binary executable or a script starting with `#!` (e.g. a bash script). Here we see that we ran the program `cat` with the arguments `/dev/null`.
* `brk()` and `sbrk()` change the location of the program break, which defines the end of the process's data segment; increasing the program break allocates memory to the process and decreasing the break deallocates memory.
* `mmap` creates a new mapping in the virtual address space of the calling process. Regions of RAM are mapped for the process.
* `open` tells us how we can open (and possibly create) the file; we see that this is in read only mode.

So why use `strace`?

Sometimes you need to investigate production systems where you cannot afford to run programs under a debugger.

### strace options

Here are a few common uses of strace:

#### strace with timings and output to file

To get timings and output to a file: `strace -ttTo data.txt cat /dev/null`

* `strace -t` to prefix each line with the time (`-tt` means to also include microseconds)
* `strace -T` to see how much time was spent on each system call.
* `strace -o outputfile programname` to return the results of a program to an output file

The **data.txt** file looks like:

    18:12:58.879824 execve("/bin/cat", ["cat", "/dev/null"], [/* 51 vars */]) = 0 <0.000479>
    18:12:58.880708 brk(NULL)               = 0x1caa000 <0.000021>
    18:12:58.880928 access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory) <0.000028>
    18:12:58.881069 mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f3fde9e1000 <0.000025>
    18:12:58.881233 access("/etc/ld.so.preload", R_OK) = -1 ENOENT (No such file or directory) <0.000025>
    18:12:58.881392 open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3 <0.000029>
    18:12:58.881532 fstat(3, {st_mode=S_IFREG|0644, st_size=147141, ...}) = 0 <0.000020>
    18:12:58.881647 mmap(NULL, 147141, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f3fde9bd000 <0.000022>
    18:12:58.881749 close(3)                = 0 <0.000018>
    18:12:58.881852 access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory) <0.000020>
    18:12:58.881969 open("/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3 <0.000025>
    18:12:58.882076 read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\t\2\0\0\0\0\0"..., 832) = 832 <0.000022>
    18:12:58.882183 fstat(3, {st_mode=S_IFREG|0755, st_size=1868984, ...}) = 0 <0.000017>
    18:12:58.882287 mmap(NULL, 3971488, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f3fde3f4000 <0.000025>
    18:12:58.882391 mprotect(0x7f3fde5b4000, 2097152, PROT_NONE) = 0 <0.000036>
    18:12:58.882501 mmap(0x7f3fde7b4000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1c0000) = 0x7f3fde7b4000 <0.000030>
    18:12:58.882618 mmap(0x7f3fde7ba000, 14752, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f3fde7ba000 <0.000023>
    18:12:58.882728 close(3)                = 0 <0.000018>
    18:12:58.882848 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f3fde9bc000 <0.000020>
    18:12:58.882950 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f3fde9bb000 <0.000020>
    18:12:58.883050 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f3fde9ba000 <0.000018>
    18:12:58.883148 arch_prctl(ARCH_SET_FS, 0x7f3fde9bb700) = 0 <0.000018>
    18:12:58.883363 mprotect(0x7f3fde7b4000, 16384, PROT_READ) = 0 <0.000025>
    18:12:58.883469 mprotect(0x60b000, 4096, PROT_READ) = 0 <0.000024>
    18:12:58.883573 mprotect(0x7f3fde9e3000, 4096, PROT_READ) = 0 <0.000026>
    18:12:58.883672 munmap(0x7f3fde9bd000, 147141) = 0 <0.000036>
    18:12:58.883942 brk(NULL)               = 0x1caa000 <0.000017>
    18:12:58.884035 brk(0x1ccb000)          = 0x1ccb000 <0.000021>
    18:12:58.884139 open("/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3 <0.000030>
    18:12:58.884258 fstat(3, {st_mode=S_IFREG|0644, st_size=2981280, ...}) = 0 <0.000019>
    18:12:58.884359 mmap(NULL, 2981280, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f3fde11c000 <0.000025>
    18:12:58.884472 close(3)                = 0 <0.000018>
    18:12:58.884637 fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 3), ...}) = 0 <0.000020>
    18:12:58.884795 open("/dev/null", O_RDONLY) = 3 <0.000031>
    18:12:58.884929 fstat(3, {st_mode=S_IFCHR|0666, st_rdev=makedev(1, 3), ...}) = 0 <0.000018>
    18:12:58.885035 fadvise64(3, 0, 0, POSIX_FADV_SEQUENTIAL) = 0 <0.000019>
    18:12:58.885134 mmap(NULL, 139264, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f3fde9bf000 <0.000022>
    18:12:58.885241 read(3, "", 131072)     = 0 <0.000019>
    18:12:58.885339 munmap(0x7f3fde9bf000, 139264) = 0 <0.000030>
    18:12:58.885445 close(3)                = 0 <0.000022>
    18:12:58.885558 close(1)                = 0 <0.000018>
    18:12:58.885652 close(2)                = 0 <0.000018>
    18:12:58.885748 exit_group(0)           = ?
    18:12:58.885936 +++ exited with 0 +++

* `strace -c` to see the count of system calls by time, number of calls, number of errors (e.g. mmap, open, close, read)

The command: `strace -c ls -l` output looks like:

    will@xps ~ $ strace -c ls -l
    total 96
    drwxr-xr-x  2 will will  4096 May 13 12:02 Desktop
    drwxr-xr-x  2 will will  4096 May 23 10:58 Documents
    drwxr-xr-x  6 will will 12288 Jul  9 15:53 Downloads
    drwxrwxr-x  2 will will  4096 May 19 08:12 Envs
    -rw-r--r--  1 will will  8980 May 13 12:01 examples.desktop
    drwxrwxr-x 20 will will  4096 Jul  9 17:06 GitHub
    drwxrwxr-x  6 will will  4096 Jun  9 07:02 go
    drwxr-xr-x  2 will will  4096 May 19 16:54 Music
    drwxrwxr-x 12 will will  4096 Jul  5 18:28 nltk_data
    drwxrwxr-x  2 will will  4096 Jul  9 18:20 notes
    -rw-rw-r--  1 will will  1773 Jun 20 09:55 pgadmin.log
    drwxr-xr-x  2 will will  4096 May 13 12:02 Pictures
    drwxr-xr-x  2 will will  4096 May 13 12:02 Public
    drwxrwxr-x  2 will will  4096 Jun  6 21:37 Snapshots
    drwxr-xr-x  2 will will  4096 May 13 12:02 Templates
    drwxr-xr-x  2 will will  4096 May 13 12:02 Videos
    % time     seconds  usecs/call     calls    errors syscall
    ------ ----------- ----------- --------- --------- ----------------
     97.27    0.000713         357         2           getdents
      2.73    0.000020           1        16           lstat
      0.00    0.000000           0        17           read
      0.00    0.000000           0        17           write
      0.00    0.000000           0        27         5 open
      0.00    0.000000           0        28           close
      0.00    0.000000           0        15           stat
      0.00    0.000000           0        24           fstat
      0.00    0.000000           0         5           lseek
      0.00    0.000000           0        35           mmap
      0.00    0.000000           0        20           mprotect
      0.00    0.000000           0         5           munmap
      0.00    0.000000           0         3           brk
      0.00    0.000000           0         2           rt_sigaction
      0.00    0.000000           0         1           rt_sigprocmask
      0.00    0.000000           0         2           ioctl
      0.00    0.000000           0        11        11 access
      0.00    0.000000           0         4           socket
      0.00    0.000000           0         4         4 connect
      0.00    0.000000           0         1           execve
      0.00    0.000000           0         1           getrlimit
      0.00    0.000000           0         2         2 statfs
      0.00    0.000000           0         1           arch_prctl
      0.00    0.000000           0        30        30 getxattr
      0.00    0.000000           0        16        16 lgetxattr
      0.00    0.000000           0         1           futex
      0.00    0.000000           0         1           set_tid_address
      0.00    0.000000           0         1           set_robust_list
    ------ ----------- ----------- --------- --------- ----------------
    100.00    0.000733                   292        68 total

* `strace -e access ls -l` to filter by system call, e.g. look at the `access` call (can be other calls like `open`, `read`, etc.) when we run the command `ls -l`. For this example, we want to look at access because we see 11 errors in our previous system call.

An example of filtering output looks like:

    will@xps ~ $ strace -e access ls -l
    access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
    access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
    access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
    access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
    access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
    access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
    access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
    access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
    access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
    access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
    access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
    total 96
    drwxr-xr-x  2 will will  4096 May 13 12:02 Desktop
    drwxr-xr-x  2 will will  4096 May 23 10:58 Documents
    drwxr-xr-x  6 will will 12288 Jul  9 15:53 Downloads
    drwxrwxr-x  2 will will  4096 May 19 08:12 Envs
    -rw-r--r--  1 will will  8980 May 13 12:01 examples.desktop
    drwxrwxr-x 20 will will  4096 Jul  9 17:06 GitHub
    drwxrwxr-x  6 will will  4096 Jun  9 07:02 go
    drwxr-xr-x  2 will will  4096 May 19 16:54 Music
    drwxrwxr-x 12 will will  4096 Jul  5 18:28 nltk_data
    drwxrwxr-x  2 will will  4096 Jul  9 18:20 notes
    -rw-rw-r--  1 will will  1773 Jun 20 09:55 pgadmin.log
    drwxr-xr-x  2 will will  4096 May 13 12:02 Pictures
    drwxr-xr-x  2 will will  4096 May 13 12:02 Public
    drwxrwxr-x  2 will will  4096 Jun  6 21:37 Snapshots
    drwxr-xr-x  2 will will  4096 May 13 12:02 Templates
    drwxr-xr-x  2 will will  4096 May 13 12:02 Videos
    +++ exited with 0 +++

* `strace -f someprogram` to also trace the child processes as they are created from the results of `fork()`, `vfork()`, and `clone()`
* `strace -p somepid` to trace a specific process id

## g++

**g++** is a nix based C++ compiler.

### g++ usage

* You can run with: `g++ somefilename`
* The default output of g++ is **a.out**. You can specify the name of the executable file by using the `-o outputfilename`, e.g. `g++ somefilename -o outputfilename`

## GNU Project Debugger (gdb)

The **GNU Project Debugger** (**gdb**) is a debugger that helps you see what is going on inside another program while it executes.


