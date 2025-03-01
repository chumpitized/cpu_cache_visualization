# CPU Cache Simulation

<img src="https://github.com/user-attachments/assets/d71c2b7b-0ab6-4075-bdaf-718e34588432" alt="Alt Text" width="590" height="520">

This is a simple visualization of a direct-mapped CPU cache with 8-bit cache lines. It also uses a write-back policy for data storage, which you can see as you step through the fixed instruction set.

Unfortunately, the "simulation" aspect of this project is hard-coded, but the results should match the expected behavior.

## Direct-Mapped Cache
A direct-mapped CPU cache maps memory addresses to specific indexes / memory blocks (AKA cache lines) in the cache. Since our example features 16 bytes of RAM but only 4 bytes of cache storage, we need to find a way to map 4 RAM addresses to each cache line. (The image above shows this mapping.)

We do this by using the modulo operator. For each address in RAM, we simply modulo by the cache size (in this case, 4), which will return a value between 0 and 3. This value represents the index of the mapped cache line.

Of note are the "index" and "tag" labels beneath the cache. Index refers to the selected cache line -- 0 in the image above -- which matches the two least significant bits of that line's RAM-mapped addresses. `0000`, `0100`, `1000`, and `1100` all map to index `00` in the cache. Tag, on the other hand, contains the two most significant bits of the address mapped to a given cache line. Index and tag combine to form a full address, which allows the CPU to discover data in the cache before resorting to main memory. 

Stepping through the program should help make sense of how these processes work.

## Write-Allocate and Write-Back Policies
A "write-allcoate" policy guarantees that when a memory allocation is made, and the address of that allocation does not exist in the cache, it will be written to the CPU cache. (Whether the allocation is also written to main memory depends on a secondary policy, described below.) In contrast, a "no-write-allocate" policy will write the allocation only to main memory. 

A "write-back" policy promises that data in the cache will only be written to main memory once it's been evicted. This means that the cache and main memory may "disagree" on the data stored at a given address; however, the source of truth in these matters will always be the CPU cache. Alternatively, a "write-through" policy guarantees that cache allocations will be followed by allocations to main memory. With "write-through" policies, the CPU cache and main memory always maintain parity.

In general, "write-allocate" and "write-back" policies are faster than their alternatives because they entail fewer trips to main memory.

## Dirty Flag
Because we're using a write-back policy, we need a "dirty" flag to determine when to write an evicted cache line to main memory. When the dirty flag is set, meaning that a cache line is out of step (i.e., "ahead") of main memory, the CPU will write that line to RAM on eviction. When a cache line is "clean", no write will occur because the cache and RAM contain the same value.
