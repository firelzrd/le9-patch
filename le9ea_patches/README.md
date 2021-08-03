
# le9ea patch

The kernel does not provide a way to protect the working set under memory pressure. A certain amount of anonymous and clean file pages is required by the userspace for normal operation. First of all, the userspace needs a cache of shared libraries and executable binaries. If the amount of the clean file pages falls below a certain level, then thrashing and even livelock can take place. The patch provides sysctl knobs for protecting the working set (anonymous and clean file pages) under memory pressure. Protection of clean file pages may be used to prevent thrashing and reducing I/O under memory pressure. Hard protection of clean file pages may be used to avoid high latency and prevent livelock in near-OOM conditions.

The `vm.anon_min_kbytes` sysctl knob provides *hard* protection of anonymous pages. The anonymous pages on the current node won't be reclaimed under any conditions when their amount is below `vm.anon_min_kbytes`. The default value is defined by `CONFIG_ANON_MIN_KBYTES` (suggested 0 in Kconfig).

The `vm.clean_low_kbytes` sysctl knob provides *best-effort* protection of clean file pages. The clean file pages on the current node won't be reclaimed under memory pressure when their amount is below `vm.clean_low_kbytes` *unless* we threaten to OOM or have no free swap space or vm.swappiness=0. Setting it to a high value may result in a early eviction of anonymous pages into the swap space by attempting to hold the protected amount of clean file pages in memory. The default value is defined by `CONFIG_CLEAN_LOW_KBYTES` (suggested 0 in Kconfig).

The `vm.clean_min_kbytes` sysctl knob provides *hard* protection of clean file pages. The clean file pages on the current node won't be reclaimed under memory pressure when their amount is below `vm.clean_min_kbytes`. Setting it to a high value may result in a early out-of-memory condition due to the inability to reclaim the protected amount of clean file pages when other types of pages cannot be reclaimed. The default value is defined by `CONFIG_CLEAN_MIN_KBYTES` (suggested 0 in Kconfig).