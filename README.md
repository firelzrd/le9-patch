# le9 Unofficial

Working set protection that is compatible with both the traditional LRU and the Multi-Gen LRU (a.k.a. lru_gen)

## Demo

https://youtu.be/FaEc5AeJEAU

## Forked from

https://github.com/hakavlad/le9-patch

## Multi-Gen LRU compatibility
le9uo 1.3 and above comes with a long-waited **Multi-Gen LRU** (MGLRU, or lru_gen) compatibility.
It comes with the working set protection features like it has to the traditional LRU.

Please be aware that there is an MGLRU-specific limitation.
At the latest Linux kernel (version 6.7.5 at the time this is written), Multi-gen LRU lacks the ability to comply with the `vm.swappiness` sysctl knob like it was initially designed.
Almost regardless of what value is put in `vm.swappiness` (as long as greater than 0), it seems to evict whatever it finds first.
This behavior is coming from MGLRU's page-scanner design/implementation, and it causes to start to thrash much earlier and easier than the traditional LRU.
MGLRU does rather temporal approach called `min_ttl`, but this design has another problem; it's much more difficult to estimate each system's optimal effective value than traditional LRU + le9's spacial approach, and when the value is out of the effective range, it easily results either in too early invocation of OOM killer, or thrashing.

le9uo does not fix this issue, but greatly mitigates it so that these limitations due to MGLRU's design/implementation isn't a problem anymore.

