# 星绽开源社区贡献赛

本仓库为感兴趣参加[星绽开源社区贡献赛](https://www.gitlink.org.cn/competitions/track1_2026Asterinas?login=ppr2eztn8&websiteName=educoder)的同学提供一些选题的idea。欢迎提出自己的idea，可以在[Github Discussions](https://github.com/asterinas/asterinas/discussions)上提问和讨论。

本页面会定期更新。

## 一、增加内核子系统

* **增加 Linux Security Module (LSM) 子系统，增加 YAMA** — 目前内核缺少 LSM 框架，`ptrace` 访问检查处有明确的 TODO 提到需要 YAMA（`kernel/src/process/posix_thread/alien_access.rs:57`）【高】
* **增加 device mapper 子系统，并增加 dm-crypt、dm-verity 等安全虚拟存储** 【高】
* **增加 seccomp 子系统** — 目前完全没有 seccomp 实现，这是容器安全的关键组件 【高】
* **增加 io_uring 异步 I/O 子系统** — 目前缺少 `io_uring_setup`、`io_uring_enter`、`io_uring_register` 等系统调用，这是现代 Linux 高性能 I/O 的核心 【中】
* **增加 Network namespace** — 目前网络命名空间未实现，是容器网络隔离所必需的 【中】
* **增加 IPC namespace 和 Cgroup namespace** — 在 nsproxy 中标记为 TODO（`kernel/src/process/namespace/nsproxy.rs:76`）【中】
* **增加 coredump 支持** — 目前 `PR_GET_DUMPABLE` 始终返回 DISABLE，`PR_SET_DUMPABLE` 为空操作（`kernel/src/syscall/prctl.rs:38-46`）【中】

## 二、增加硬件支持和设备驱动

* **增加对 ARM（AArch64）体系架构的支持** 【高】
* **完善 RISC-V 架构支持** — 目前标记为 Tier 2，有大量 TODO：FPU 上下文、IOMMU、硬件随机数生成器等（`ostd/src/arch/riscv/`）【中-高】
* **完善 LoongArch 架构支持** — 目前标记为 Tier 3，中断处理、SMP 支持、FPU 上下文、定时器等都有 TODO（`ostd/src/arch/loongarch/`），FIXME 提到需要支持 SMP（`ostd/src/arch/loongarch/irq/chip/mod.rs:11`）【中-高】
* **增加多 PCIe segment group 支持** — 目前只假定一个 segment group（`ostd/src/arch/x86/kernel/acpi/mod.rs:133`，`kernel/comps/pci/src/arch/riscv/mod.rs:55`）【中】
* **增加 IOMMU 支持完善** — 目前 x86 上 IOMMU 启用有 bug（[Issue #1517](https://github.com/asterinas/asterinas/issues/1517)），RISC-V 和 LoongArch 的 IOMMU 未实现 【中-高】
* **支持 virtio 块设备多队列（Multi-Queue）** — 当前 virtio-blk 驱动有 FIXME 提到缺少多队列支持（`kernel/comps/virtio/src/device/block/device.rs:226`）【中】
* **增加 virtio-mmio 设备在 TDX 环境中的支持** — 当前标记为 TODO（`kernel/comps/virtio/src/transport/mmio/bus/mod.rs:30`）【中】

## 三、完善现有功能点

### 文件系统

* **完善 procfs，增加缺失的 inodes** — 如 `/proc/meminfo`（[Issue #946](https://github.com/asterinas/asterinas/issues/946)）、`/proc/<tid>` 线程信息（[Issue #2940](https://github.com/asterinas/asterinas/issues/2940)），以及 `/proc/[pid]/status` 和 `/proc/[pid]/stat` 中标记为 FIXME 的未实现字段（`kernel/src/fs/fs_impls/procfs/pid/task/status.rs:22`、`stat.rs:24`）【高】
* **增加 devtmpfs** — 在 `/dev` 下实现内核管理的 tmpfs，替代当前临时方案（[Issue #1990](https://github.com/asterinas/asterinas/issues/1990)）【高】
* **增加 ext4 文件系统支持** — 目前只有 ext2，缺少现代 Linux 最常用的 ext4 【高】
* **完善 VFS 路径解析中的负 dentry 缓存** — 当前标记为 TODO，负 dentry 膨胀是性能问题（`kernel/src/fs/vfs/path/dentry.rs:295,573`）【中】
* **实现 mount propagation（shared/private/slave/unbindable）** — 目前仅实现了 private 传播类型（`kernel/src/fs/vfs/path/mount.rs:35`）【中-高】
* **增加 `O_TMPFILE` 标志支持** — 当前明确标记为 TODO（`kernel/src/fs/file/file_attr/creation_flags.rs:6`）【低】

### 网络

* **增加 IPv6 支持** — 目前仅支持 IPv4，代码中有明确 TODO（`kernel/src/net/socket/ip/addr.rs:26`）【高】
* **完善 TCP socket 实现** — 存在大量 TODO，如 shutdown listening stream（`kernel/src/net/socket/ip/stream/mod.rs:504`）、`MSG_NOSIGNAL` 处理（`:567`）、控制消息收发（`:561,582`）、keep-alive idle 跟踪（`:750`）等 【中】
* **完善 UDP socket 实现** — 当前有 TODO 涉及选项集和多种操作（`kernel/src/net/socket/ip/datagram/mod.rs:50-244`）【中】
* **完善 Unix domain socket** — 控制消息（SCM_RIGHTS、SCM_CREDENTIALS）实现不完整（`kernel/src/net/socket/unix/ctrl_msg.rs` 中有多处 TODO）、SEQPACKET 类型缺失 【中】
* **支持多网卡和路由表** — 当前硬编码单个网络设备，FIXME 提到应从路由表获取广播信息（`kernel/src/net/iface/init.rs:33`、`broadcast.rs:11`）【中-高】
* **完善 Netlink route 内核实现** — 当前有 TODO 指出应为 per-namespace socket（`kernel/src/net/socket/netlink/route/kernel/mod.rs:73`）、ACK 标志处理未完成（`:50`）【中】

### 进程与调度

* **完善 capability 检查机制** — 重新设计 capability 检查 API（[Issue #2381](https://github.com/asterinas/asterinas/issues/2381)），支持 bounding 和 ambient capability set（`kernel/src/process/credentials/mod.rs:32`）【中】
* **完善 futex 实现** — 当前缺少 `FUTEX_WAKE_OP` 操作，robust futex 支持也有待完善（`kernel/src/process/posix_thread/futex.rs` 多处 FIXME）【中】
* **完善 rlimit 资源限制执行** — 多数 RLIMIT_* 常量未实际执行限制检查（[Issue #2841](https://github.com/asterinas/asterinas/issues/2841)），含约 16 个子任务 【低-中】（每个子任务）
* **增加 `SCHED_DEADLINE` 调度策略** — 目前实现了 CFS、FIFO、RR，但缺少 DEADLINE 和 BATCH 策略 【中-高】
* **增加 NUMA 支持** — 当前 `getcpu` 系统调用有 TODO 表明不支持 NUMA（`kernel/src/syscall/getcpu.rs:13`）【高】
* **完善信号处理** — `siginfo_t` 中 POSIX timer 信号缺少 `si_timerid` 和 `si_overrun`（[Issue #2913](https://github.com/asterinas/asterinas/issues/2913)），信号上下文有多处 FIXME（`kernel/src/process/signal/mod.rs`）【低-中】
* **增加 load tracking 调度** — 实现类似 CFS 的负载追踪（[Issue #1912](https://github.com/asterinas/asterinas/issues/1912)）【中-高】

### 系统调用完善

* **支持 `rename` 的 `NOREPLACE`/`EXCHANGE`/`WHITEOUT` 标志** — 当前标记为 TODO（`kernel/src/syscall/rename.rs:31`）【低】
* **完善 `statx` 系统调用** — 多个字段为虚拟实现（`kernel/src/syscall/statx.rs:149`），缺少 birth time 支持（`:163`）【低-中】
* **增加 `mlock`/`mlockall` 系统调用** — 目前未实现内存锁定 【中】
* **增加 `membarrier` 系统调用** — 对高性能并发程序重要 【中】
* **支持 `getrandom` 的 `GRND_NONBLOCK` 和 `GRND_INSECURE` 标志** — 当前标记为 TODO（`kernel/src/syscall/getrandom.rs:23`）【低】
* **完善 `access`/`faccessat` 系统调用** — 当前实现标记为 dummy（`kernel/src/syscall/access.rs:108`）【低-中】
* **完善 `clone3` 的 `set_tid`/`cgroup` 支持** — 当前标记为 TODO（`kernel/src/syscall/clone.rs:89`）【中】
* **完善 `mremap` 系统调用** — 当前有 FIXME 和 TODO（`kernel/src/syscall/mremap.rs:81,94`）【中】
* **支持 `MAP_32BIT` mmap 标志** — 当前标记为 TODO（`kernel/src/syscall/mmap.rs:89`）【低-中】
* **完善 `setns` 系统调用** — 多处 TODO 表明命名空间切换不完整（`kernel/src/syscall/setns.rs:95,128,172,201`）【中】
* **实现 Go 标准库所需的全部系统调用** — 有详细追踪 Issue（[Issue #1888](https://github.com/asterinas/asterinas/issues/1888)），含优先级和复杂度评级，可逐个完成 【中】

### 设备与终端

* **支持多 TTY 终端** — 实现 Ctrl+Alt+Fn 虚拟终端切换（[Issue #2820](https://github.com/asterinas/asterinas/issues/2820)）【中】
* **完善 TTY line discipline** — 未实现输出标志处理（`kernel/src/device/tty/line_discipline.rs:160`），canonical 模式切换行为不正确（`:244`）【中】
* **完善 evdev 事件设备** — 缺少设备节点删除功能（`kernel/src/device/evdev/mod.rs:284`），多个 ioctl 操作未实现（`evdev/file.rs`）【中】
* **增加 /dev/random 真随机性支持** — 当前标记为 TODO，需要收集环境噪声（`kernel/src/device/mem/file.rs:42`）【中-高】
* **增加多用户支持（login 等）** — 实现 `adduser`、`passwd`、`/etc/passwd`、`/etc/shadow`、访问控制和文件隔离（[Issue #1430](https://github.com/asterinas/asterinas/issues/1430)）【高】

### 内存管理

* **让 frame allocator 回收 bootloader 内存区域** — 当前引导程序使用的内存未被回收（[Issue #322](https://github.com/asterinas/asterinas/issues/322)）【低】
* **支持内核空间大页映射** — 当前标记为 TODO（`ostd/src/mm/kspace/mod.rs:261`）【中-高】
* **实现 TLB ASID 管理** — 减少 TLB flush 次数，提升上下文切换性能（[Issue #969](https://github.com/asterinas/asterinas/issues/969)）【中-高】
* **重构 page cache 系统** — 改进页面缓存架构（[Issue #2937](https://github.com/asterinas/asterinas/issues/2937)）【高】

### Cgroup

* **增加更多 cgroup controller** — 目前仅实现 Memory、CPUSet、PIDs 三个控制器（`kernel/src/fs/fs_impls/cgroupfs/controller/mod.rs`），缺少 CPU、Devices、Freezer、Blkio、Net_cls 等常用控制器 【中】（每个控制器）

---

## 四、性能优化

* **实现可扩展的引用计数（scalable refcount）** — 类似 RadixVM 的 RefCache，提升多核下页面引用计数的可扩展性（[Issue #1529](https://github.com/asterinas/asterinas/issues/1529)）【高】
* **实现队列自旋锁（queued spinlock）** — 类似 Linux 的 qspinlock，改善锁竞争下的性能（[Issue #1528](https://github.com/asterinas/asterinas/issues/1528)）【高】
* **零原子操作的文件表查找** — 消除文件描述符表查找中的原子操作开销（[Issue #1550](https://github.com/asterinas/asterinas/issues/1550)）【中-高】
* **减少 read/write 系统调用路径中的堆分配和内存拷贝** — 目前有额外开销（[Issue #1057](https://github.com/asterinas/asterinas/issues/1057)）【中】
* **调查并修复 SMP=8 下 sqlite 性能退化** — 多核扩展性问题（[Issue #2485](https://github.com/asterinas/asterinas/issues/2485)）【中-高】
* **用内联汇编替换 `read_volatile`/`write_volatile`** — 底层优化（[Issue #2948](https://github.com/asterinas/asterinas/issues/2948)）【低-中】
* **评估 PGO（Profile-Guided Optimization）对内核的效果** — 研究型任务（[Issue #760](https://github.com/asterinas/asterinas/issues/760)）【中】
* **优化 XArray 的空节点清理** — 当前 cursor 操作不清理空内部节点（`kernel/libs/xarray/src/cursor.rs:470`）【低-中】
* **优化 segment tree 实现** — 当前 range counter 用的是简单实现，TODO 建议用线段树（`ostd/src/util/range_counter.rs:5`）【中】

## 五、识别和修复 Bug

* **修复 gvisor 测试集中目前[被 blocked](https://github.com/asterinas/asterinas/tree/main/test/initramfs/src/syscall/gvisor/blocklists) 的测试用例** — 共 59 个 blocklist 文件、673 个测试用例被 block，涵盖文件操作、socket、信号、内存映射等多个子系统 【低-高】（视具体测试而定）
* **修复 LTP 测试集中目前[被 blocked](https://github.com/asterinas/asterinas/blob/main/test/initramfs/src/syscall/ltp/testcases/) 的测试用例** — 其中 ext2 有 55 个、exFAT 有 98 个测试被 block 【低-高】（视具体测试而定）

## 六、测试与基础设施

* **集成 packetdrill 网络测试套件** — Google 的网络协议测试工具（[Issue #2858](https://github.com/asterinas/asterinas/issues/2858)）【中】
* **全面启用 RISC-V CI 测试** — 当前 RISC-V 测试覆盖有限（[Issue #2546](https://github.com/asterinas/asterinas/issues/2546)）【中】
* **优化单元测试执行速度** — 单元测试是 CI 瓶颈（[Issue #1904](https://github.com/asterinas/asterinas/issues/1904)）【中】
* **实现细粒度日志过滤** — 支持按模块设置日志级别（[Issue #1503](https://github.com/asterinas/asterinas/issues/1503)），已有详细设计方案 【中】
* **引入动态调试框架** — 运行时可配置的调试日志（[Issue #2941](https://github.com/asterinas/asterinas/issues/2941)）【中】
