# IOPS & Throughput Calculator

A single-page storage sizing calculator. Open `index.html` in any browser; there is nothing to install or build.

## What it does

| Module | What it calculates |
|---|---|
| **Workload** | Presets (OLTP, VDI, virtual servers, email, file server, analytics, backup, video) that fill in a typical block size and read/write mix |
| **IOPS ↔ Throughput** | Solves any one of IOPS, block size or throughput from the other two |
| **Block size table** | Throughput and IOPS at block sizes from 512 B to 1 MiB |
| **Array IOPS** | Usable IOPS for a RAID 0/1/10/5/6/50/60 array from drive count, IOPS per drive and read/write mix; drives needed for a target IOPS |
| **Capacity** | Raw and usable capacity (TB and TiB), efficiency, parity/mirror overhead, hot spares and span groups |
| **Queue depth & latency** | Little's law: queue depth = IOPS × latency |
| **System bottleneck** | IOPS ceilings for disks, CPU, controllers and network; the lowest one is the system limit |

## Formulas

- Throughput = IOPS × block size
- Usable array IOPS = drives × IOPS per drive ÷ (read% + write% × RAID write penalty)
- Write penalty: RAID 0 = 1, RAID 1/10 = 2, RAID 5/50 = 4, RAID 6/60 = 6
- Read cache hit ratio = RAM cache ÷ hot working set (capped at 95%); cached reads skip the disks:
  disk IOPS = drives × IOPS per drive ÷ (read% × (1 − hit) + write% × penalty)
- CPU limit = nodes × CPUs × cores × GHz × % CPU for storage ÷ CPU cycles per I/O
- Controller limit = min(controllers × IOPS each, controllers × GB/s each ÷ block size)
- Network limit = nodes × ports × port bandwidth × usable % ÷ block size
- System IOPS = min(disks, CPU, controllers, network)
- Queue depth = IOPS × latency

## Caveats

Drive, CPU-cost and controller figures are typical values, not measurements. The model ignores controller write cache, SSD write amplification and full-stripe writes, and assumes reads are spread evenly over the working set. Use it for planning, then confirm with a benchmark (for example `fio`) on your own hardware.
