---
title: "Chapter 1 - The CPU in Consumer Desktop PCs"
parent: "English Edition"
nav_order: 1
---

<details open markdown="block">
  <summary>Chapter contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

> **Note on Data Updates.** This chapter is updated as of **July 2026**. Architectural concepts and protocols (PCIe, sockets, cache, IPC) are stable over time; what changes rapidly are **prices**, **the latest available generations**, and **availability**. In 2026, in particular, there is an anomalous market condition: a **severe shortage of DDR5 memory**, with RAM prices having risen by 5–8 times compared to normal. This heavily distorts the "total platform cost" that we will discuss, to the point that often *a kit of fast RAM costs more than the CPU itself*. All indicated prices should therefore be taken as an indicative order of magnitude and verified at the time of purchase.

---

## 1. What the CPU Does

### 1.1 Definition and Role in the System

The acronym **CPU** stands for *Central Processing Unit*. It is the component that executes program instructions: it is, literally, the "brain" that coordinates and controls the rest of the computer. Everything the PC does — opening a file, calculating a formula, moving the cursor, deciding what to send to the video card — passes in some form through the CPU, which orchestrates its flow.

To understand *how* a CPU works, one must start from the fundamental cycle it repeats billions of times per second, called **fetch-decode-execute**. In the *fetch* phase, the CPU retrieves the next instruction to execute from memory; in the *decode* phase, it interprets it, understanding what operation it is and what data it involves; in the *execute* phase, it actually performs it, for example, adding two numbers or writing a value to memory. Once execution is complete, the result is written (the *write-back* phase) and the cycle restarts with the next instruction. This process, trivial when described in words, is what happens billions of times per second inside each core, and is the reason why frequency ("GHz") has such a direct impact on performance: more cycles per second, more instructions processed.

The CPU does not work alone: it constantly communicates with other system components, and understanding this communication is essential for making sensible purchases. With **RAM** (*Random Access Memory*, the volatile working memory), the CPU exchanges the data it is currently using: RAM is fast but empties when powered off, and the CPU critically depends on it because that's where it keeps the "hot" data of the running program. With **storage** (the storage unit, typically an NVMe SSD today), the CPU interacts to read and write permanent data, but storage is enormously slower than RAM, so data is loaded from storage to RAM before being processed. With the **GPU** (*Graphics Processing Unit*, the graphics processing unit, i.e., the video card), the CPU cooperates in graphical loads: the CPU prepares the scene, calculates game logic, physics, enemy artificial intelligence, and drawing instructions, then "hands over" this work to the GPU which transforms it into pixels on the screen. In a video game, therefore, the CPU and GPU continuously pass the baton, and if one of them is too slow compared to the other, the phenomenon of *bottleneck* occurs, which we will discuss in the section dedicated to selection.

### 1.2 CPU vs GPU: Two Computing Paradigms (Serial vs. Parallel)

One of the most common confusions for those new to hardware is thinking that CPU and GPU are "the same thing, one more powerful than the other." In reality, they are two profoundly different computing philosophies, and this difference explains why both are needed.

The CPU is optimized for **complex serial computation**: it has a few very sophisticated cores (typically 4 to 24 in consumer desktops), each capable of performing a wide variety of operations and making complex decisions quickly, managing conditional jumps, unexpected events, and instruction dependencies. It's like having a few brilliant engineers, each capable of solving difficult and diverse problems, but one at a time with great care.

The GPU, on the other hand, is optimized for **massive parallel computation**: it has thousands of much simpler computing units, each individually not very intelligent, but all together capable of performing the same operation on huge amounts of data simultaneously. It's like having an army of thousands of workers, each only capable of performing a simple task, but who together can paint a huge wall in a few seconds because they all work in parallel. This is exactly what is needed for graphics (calculating the color of millions of pixels simultaneously) and, not coincidentally, also for training artificial intelligence neural networks, which is parallel computation on matrices.

The practical consequence is clear: *sequential* and decision-rich tasks (game logic, code compilation, opening a spreadsheet) run better on the CPU; *massively parallel and repetitive* tasks (3D rendering, graphic computation, deep learning) run much better on the GPU. Neither replaces the other, which is why a balanced PC needs both properly sized.

---

## 2. Internal CPU Architecture

### 2.1 Physical Cores: What They Are and Why They Matter

A **core** is a complete and independent processing unit within the CPU: it is, in practice, a small autonomous processor, capable of executing its own fetch-decode-execute cycle independently. A CPU with 8 cores contains 8 of these computing engines side-by-side on the same piece of silicon.

The number of cores matters because it determines how many *truly independent* tasks the CPU can perform simultaneously. With a single core, the computer can execute one instruction at a time (and simulates multitasking by rapidly switching between programs); with multiple cores, it can truly execute multiple workflows in parallel. Be aware, however, of a common misconception: *not all programs can use multiple cores*. Software is called "multi-threaded" when it is written to divide its work into parallel portions; many games, especially older ones, and many everyday applications, however, remain predominantly "single-threaded," meaning they only effectively utilize one or two cores. This is why, for gaming, "more cores" beyond a certain threshold (today 6–8 cores are more than sufficient for the vast majority of titles) offers diminishing returns, while for video rendering, compilation, virtualization, and scientific computing, additional cores are invaluable.

### 2.2 Threads and SMT/Hyper-Threading: Difference Between Cores and Threads

A **thread** is a single, ordered stream of instructions. The confusion between "core" and "thread" arises from a technology called **SMT** (Simultaneous Multi-Threading), which Intel markets under the name **Hyper-Threading**. Thanks to SMT, a single physical core can handle *two threads simultaneously*, presenting itself to the operating system as if it were two "logical" cores.

The trick works because a core, while executing a thread, often has inactive internal units: perhaps it's waiting for data from memory, and in the meantime, some of its computational circuits remain idle. SMT fills those "gaps" with the work of a second thread, making better use of existing resources. The typical gain is on the order of 15–30% more throughput in well-parallelized workloads: it doesn't double performance (two threads on one core are not equivalent to two cores), but it's a free increase in efficiency.

When do threads help and when do they not? They help in heavily multi-threaded and parallel workloads (rendering, compression, virtualization, heavy editing), where keeping the core's units constantly occupied provides a concrete advantage. They help little or not at all in many games and single-thread workloads, where what matters is the speed of the single thread. In some extreme cases, in the past, SMT could even marginally reduce game performance due to resource contention, and it's one of the reasons why it was sometimes recommended to disable it for specific e-sports competitions. A notable and recent fact: **Intel, with the Arrow Lake architecture (Core Ultra series 2, from 2024), has abandoned Hyper-Threading on desktops**, focusing instead on a larger number of efficient cores. AMD, on the other hand, maintains SMT across its entire Ryzen range. Consequently, today, counting "threads" to directly compare Intel and AMD has become misleading: one must look at the overall architecture and benchmarks.

### 2.3 Base Frequency vs. Boost Frequency

**Clock frequency** measures how many cycles per second the core completes, expressed in gigahertz (GHz, billions of cycles per second). Modern CPUs do not have just one frequency but at least two declared values. The **base frequency** is the one guaranteed under prolonged load conditions, within standard power consumption and temperature limits: it's the "bare minimum" that the CPU always maintains. The **boost frequency** (or *turbo*) is the maximum that the CPU opportunistically reaches when it has thermal and power headroom, to accelerate work peaks.

It is crucial to distinguish between two types of boost. **Single-core boost** is the maximum frequency achievable when only one core (or very few) is engaged: it is high because heat is concentrated in one spot and there is ample headroom, and it is the value prominently advertised on the box. **All-core boost**, on the other hand, is the sustained frequency when *all* cores are working together: it is always lower than the single-core peak, because the heat produced by all cores simultaneously is much greater and needs to be dissipated. This distinction has important practical consequences: for a game, which often stresses few cores, single-core boost matters; for a rendering that loads all cores to 100%, all-core boost and the cooler's ability to maintain it matter. A common mistake is to be captivated by the "5.7 GHz" written on the package without understanding that this value applies only to one core, for a few moments, and not under the intended workload.

### 2.4 L1, L2, L3 Caches and the Case of 3D V-Cache

**Cache** is a very small but very fast memory, integrated within the CPU, which stores frequently used data and instructions to avoid having to request them from RAM every time, which is much slower. The speed difference is enormous: accessing the cache costs the CPU a few cycles, accessing RAM costs hundreds. Every time the sought-after data is already in cache (a situation called *cache hit*), the CPU saves a huge wait; when it's not (*cache miss*), it has to fetch it from RAM, wasting precious time. Optimizing the cache is therefore one of the most effective ways to increase real performance.

Caches are organized in levels, in increasing order of size and decreasing order of speed. **L1 cache** is the smallest (a few tens of kilobytes per core) and fastest, private to each core. **L2 cache** is larger (from hundreds of kilobytes to a few megabytes per core) and slightly slower, also typically private to the core. **L3 cache** is the largest (from several megabytes up to over 100 MB) and is normally *shared* among all cores: it acts as a large common reserve, reducing RAM accesses for the entire processor.

| Cache Level | Typical Size | Speed | Scope |
|---|---|---|---|
| L1 | 32–80 KB per core | Maximum | Core-private |
| L2 | 512 KB – 3 MB per core | High | Core-private |
| L3 | 16 – 128 MB and above | Medium | Shared among cores |

The reason why a large L3 cache matters so much is most strikingly exemplified by AMD's **3D V-Cache** technology. In essence, AMD vertically "stacks" an additional block of SRAM cache memory *on top of* (or, in more recent versions, below) the compute die, increasing the L3 cache to enormous values — for example, 96 MB on a Ryzen 7 with 3D V-Cache. This has proven to be a tremendous advantage, especially in gaming, because many games have data sets that, if they all fit in cache, avoid continuous and very costly trips to RAM, with frame rate gains that can be double-digit percentages. This is why AMD models with the **X3D** suffix (like the famous Ryzen 7 9800X3D and the more recent 9850X3D from 2026) are considered among the best gaming CPUs ever. It should be noted, however, that 3D V-Cache does not help *every* game equally, and the benefit tends to decrease as the resolution increases (at 4K, the bottleneck shifts to the GPU). Furthermore, historically, the first X3D generations had to slightly reduce their frequency to manage the heat of the stacked cache, penalizing productivity workloads; with more recent generations, this compromise has been greatly mitigated.

### 2.5 IPC: why frequency alone is not enough

**IPC** stands for *Instructions Per Clock*: it measures how much useful work a core can accomplish in a single cycle. It's the concept that debunks the myth that "more GHz = faster." The intuitive formula for a core's performance is indeed *performance ≈ IPC × frequency*: two multiplied factors, not just one.

This means that a modern CPU at 4.5 GHz can easily beat a ten-year-old CPU at 5.0 GHz, because the former executes many more instructions per cycle thanks to a more efficient architecture (better branch prediction, more execution units, larger caches, optimized pipelines). IPC is the reason why you cannot compare GHz across generations or between different brands: these numbers mean different things depending on the underlying architecture. Every new CPU generation typically boasts a "+X% IPC" over the previous one, and it's that gain — not the GHz — that makes the real difference. The practical lesson for the buyer is clear: never compare CPUs by GHz, but by benchmarks, which capture the real-world IPC × frequency product.

### 2.6 TDP and real consumption: PL1/PL2 and PPT

**TDP** (*Thermal Design Power*) is a value expressed in watts that, originally, indicated how much heat the cooler must be able to dissipate for the CPU to operate within specifications. In modern practice, it has become an ambiguous and often misleading figure, because it **does not coincide with the CPU's actual maximum consumption** under heavy load.

Intel manages power consumption through two limits called **PL1** and **PL2** (*Power Limit 1* and *Power Limit 2*). PL1 is the long-term sustained power limit and roughly corresponds to the nominal TDP. PL2 is the much higher peak limit that the CPU can draw for short intervals (governed by a timer called *Tau*) during boost. This is why a CPU with "TDP 125 W" can actually draw 250 W or more at peak: that number on the box describes the sustained regime, not the peak. Many motherboards, moreover, default to "unlocked" limits that let the CPU consume as much as it wants, further altering the picture.

AMD uses a different and more honest metric called **PPT** (*Package Power Tracking*), which indicates the effective power ceiling that the entire CPU package can draw from the socket. PPT is typically about 1.35 times the nominal TDP: an AMD CPU with "TDP 105 W" generally has a PPT around 142 W, which represents the real maximum consumption under full load.

The practical lesson is twofold. First: TDP is not enough to size the power supply or the cooler; you need to look at the real peak consumption (PL2 or PPT). Second, and crucial for the buyer: buying a powerful CPU and then pairing it with an inadequate cooler means throwing away performance, because the CPU, unable to dissipate heat, automatically reduces its frequency (a phenomenon called *thermal throttling*) and never maintains the declared boost.

### 2.7 Intel Hybrid Architectures: P-cores and E-cores

Since the twelfth generation, Intel has introduced a **hybrid** architecture, which mixes two types of cores on the same processor. **P-cores** (*Performance cores*) are large, powerful, and power-hungry cores, designed for heavy and latency-sensitive tasks, such as the main thread of a game. **E-cores** (*Efficient cores*) are smaller, more frugal cores, designed to efficiently handle many background tasks and highly parallel workloads, occupying little space and little energy: many can be placed in the space of one P-core.

The idea is to entrust the operating system (via a hardware component called *Thread Director* that suggests how to distribute the work) with assigning the right tasks to the right cores: critical tasks to P-cores, secondary and massive tasks to E-cores. The result, when it works well, is an excellent balance between peak performance and multi-thread efficiency. However, practical implications exist. The first is that the number of Intel cores must be read carefully: a "Core Ultra 9 285K" with "24 cores" actually has 8 P-cores and 16 E-cores, with very different performance characteristics. The second is that, in the early days, some old software (particularly certain game anti-cheat systems and old programs) got confused in thread assignment, leading to performance drops; the problem has been largely resolved with Windows updates and drivers, but it's useful to know. AMD, for its part, adopts a different approach: in consumer desktops, it uses all "full-size" identical cores, possibly combining multiple groups of cores (the so-called CCDs, the compute chiplets), which simplifies scheduling but introduces other subtleties in dual-chiplet models.

### 2.8 Integrated iGPU: when it's needed and when it's not

Many CPUs integrate an **iGPU** (*integrated GPU*): a small video card built into the processor itself. It doesn't have the power of a dedicated video card, but it's sufficient for displaying the desktop, playing videos, browsing, and playing light games. Its presence or absence is directly encoded in the commercial suffixes, and is a source of costly purchasing errors.

For **Intel**, the suffix **F** (for example, "Core Ultra 5 245KF") indicates a CPU *without a functional iGPU*: it costs a few tens of euros less, but absolutely requires a dedicated video card to function, otherwise nothing will be displayed on screen. For **AMD**, the reasoning is reversed and more insidious: most "normal" desktop Ryzen CPUs only have a minimal iGPU, just good enough for the desktop; models truly designed for gaming without a dedicated video card are **APUs** marked with the suffix **G** (like the Ryzen 7 8700G), which integrate much more powerful Radeon graphics, capable of running several games decently at 1080p with reduced details.

When is an iGPU (or an APU) needed? It's needed for an office, a media center, a secondary PC, or an inexpensive PC to use while waiting to buy a video card, and it serves as a "safety net" for diagnosing dedicated GPU failures. It's not needed, and it's useless to pay for it, if a powerful dedicated video card will be installed for gaming anyway: in that case, choosing a model with an F suffix (Intel) can save some money. The classic mistake, which we will discuss at the end, is buying an "F" CPU thinking of saving money and then realizing you don't have any video card to turn on the PC.

---

## 3. The socket: compatibility between CPU and motherboard

### 3.1 What a socket physically is

The **socket** is the physical connector on the motherboard where the CPU is inserted: it is the mechanical and electrical interface that connects the thousand-plus contacts of the processor to the traces on the board. The socket rigidly determines *which* CPUs can be mounted on a given motherboard: a CPU can only work on a socket for which it was designed, period. It is the first and most absolute compatibility rule of the entire system.

There are two main design philosophies. In **LGA** (*Land Grid Array*), the pins — i.e., the metal contacts — are *on the motherboard socket*, while the CPU only has flat pads (the *lands*) that rest on them. In **PGA** (*Pin Grid Array*), it's the opposite: the pins are *on the CPU*, and the motherboard socket has corresponding holes. The difference has practical consequences for fragility: in PGA, if a pin bends, the expensive CPU is damaged; in LGA, a bent pin is on the motherboard socket (still difficult to repair, but often it's the mobo that's at risk). Intel has used the LGA format for years; AMD long used PGA on the AM4 platform but switched to LGA with the new AM5 platform.

### 3.2 Current and recent AMD sockets: AM4 and AM5

AMD has built an enormous reputation on **platform longevity**, meaning its ability to support many generations of CPUs on the same socket, allowing upgrades without changing the motherboard. The **AM4** socket (PGA format) is the prime example: introduced in 2016, it supported an impressive range of Ryzen generations, from the first Ryzen 1000 up to the Ryzen 5000 (and AMD continued to launch new models on it even years later, celebrating its "10 years" in 2026 with special editions). Those who bought a good AM4 motherboard in 2017 could, years later, install a much more powerful CPU with a simple firmware update.

The **AM5** socket (LGA format, since 2022) is AMD's current platform for Ryzen 7000 and 9000 series (and their X3D models). It introduces mandatory support for **DDR5** memory and **PCIe 5.0**. Here too, AMD has promised and confirmed long longevity: **AM5 support is guaranteed until at least 2029**, which makes an AM5 motherboard purchased today a reasonably future-proof investment, capable of hosting even upcoming generations. It's worth noting that next-generation CPUs based on the Zen 6 architecture (the "Ryzen 10000" family, codenamed "Olympic Ridge") have been, according to mid-2026 rumors, **postponed to 2027**, and will still remain on the AM5 socket: those who buy AM5 today will most likely be upgradeable to that generation as well.

### 3.3 Current and Recent Intel Sockets: LGA1700 and LGA1851

Intel has historically had a policy opposite to AMD's: it **changes sockets much more frequently**, typically every one or two generations, often forcing those upgrading their CPU to also change their motherboard. The number in the socket name (all in LGA format) indicates the number of contacts. The **LGA1700** socket hosted Intel generations from the twelfth to the fourteenth (12th, 13th, and 14th gen Core), with support for both DDR4 and DDR5 depending on the motherboard.

The current socket is **LGA1851**, introduced with the **Core Ultra 2 series "Arrow Lake"** (2024) and with their update **"Arrow Lake Refresh" / Core Ultra 200S Plus** in March 2026 (models like the 270K Plus and 250K Plus). LGA1851 exclusively supports DDR5. Here, however, the classic warning about Intel's policy kicks in: **LGA1851 is already end-of-life**. The 800 series chipsets are the last generation for this socket, and Intel will introduce a new socket (referred to as **LGA1954**) for the next "Nova Lake" generation in the second half of 2026. In practical terms, buying an Intel LGA1851 platform today means acquiring, in all likelihood, a "dead end" in terms of future CPU upgrades: you will only be able to change CPUs within the current family. This is a decisive factor when choosing between Intel and AMD for those planning upgrades years down the line.

| Socket | Manufacturer | Type | CPU Generations | Memory | PCIe (from CPU) | Status (Jul. 2026) |
|---|---|---|---|---|---|---|
| AM4 | AMD | PGA | Ryzen 1000–5000 | DDR4 | 4.0 | Legacy, but still sold (budget segment) |
| AM5 | AMD | LGA | Ryzen 7000–9000 (and future) | DDR5 | 5.0 | **Current**, supported until at least 2029 |
| LGA1700 | Intel | LGA | Core 12th–14th gen | DDR4/DDR5 | 5.0 | Previous, still available |
| LGA1851 | Intel | LGA | Core Ultra 200S / 200S Plus | DDR5 | 5.0 | **Current**, but end-of-cycle (LGA1954 coming) |

### 3.4 How to check compatibility: socket + chipset + BIOS

Checking compatibility requires checking *three* levels, not just the socket. The first level is, of course, the **socket**: CPU and motherboard must have the same socket. The second level is the **chipset**, which is the "controller" on the motherboard that manages peripheral connections (ports, chipset PCIe lanes, overclocking functions): even with the same socket, not all chipset/CPU combinations are valid or convenient. On AMD, for example, high-end chipsets (X series, like X670E or X870E) enable more features and overclocking, while mid-range ones (B series) are cheaper but limited; on Intel, there is a similar distinction, with "Z" chipsets (e.g., Z890) unlocking overclocking and cheaper "B" chipsets that do not. The third level, often forgotten, is the **BIOS/UEFI version** (the motherboard firmware): a motherboard released before a certain CPU might not recognize it until its firmware is updated. It's a very common mistake to buy a motherboard that is "compatible on paper" and find it unresponsive because it has a BIOS that is too old for the newly purchased CPU.

The correct way to proceed is to consult the **CPU support list** (the official list of supported CPUs) for that specific model on the motherboard manufacturer's website, which indicates the minimum BIOS version required for each CPU. It's a two-minute check that saves days of frustration.

### 3.5 Backward compatibility and BIOS updates without CPU (flashback)

The "BIOS too old" problem has an elegant solution offered by many mid-range and high-end motherboards: **BIOS Flashback** (also called *Q-Flash Plus* or similar depending on the brand). This function allows you to **update the BIOS without having a CPU, RAM, or graphics card installed**: just download the BIOS file to a USB stick, insert it into a specific port, press a dedicated button on the board, and the board updates itself using only power. It is invaluable in the classic case: I bought a new CPU and an older motherboard that doesn't recognize it; without flashback, I would be stuck, because to update the BIOS I would need a CPU that is already supported to install temporarily. With flashback, I solve everything in ten minutes. Anyone buying a new platform or planning upgrades should consider the presence of this feature an almost indispensable requirement when choosing a motherboard.

---

## 4. PCI Express: the complete treatment

This is the densest section of the chapter, because **PCIe** is the "nervous system" that connects the CPU to the graphics card, fast SSDs, and other expansions, and it is a source of enormous misunderstandings.

### 4.1 What is PCIe: a point-to-point serial bus

**PCIe** stands for *Peripheral Component Interconnect Express*: it is the high-speed interconnection standard that allows CPUs and peripherals to exchange data. Its fundamental architectural characteristic is that it is a **serial** and **point-to-point** bus. "Serial" means that data travels in a line over narrow but very fast paths, instead of in parallel over many wires as in older standards: paradoxically, at these frequencies, sending data in series over a few very fast channels is more efficient and reliable than spreading it over many parallel wires that risk "desynchronizing." "Point-to-point" means that each device has its own dedicated connection with the host, without having to share it and without contending for bandwidth with others, as was the case with shared buses of the past.

### 4.2 Lanes: what x1, x4, x8, x16 mean

The basic unit of PCIe is the **lane**. A lane is a single bidirectional serial connection, physically composed of two pairs of wires: one pair for transmitting and one for receiving, so that data can flow in both directions simultaneously (*full duplex*). Lanes can be aggregated to increase bandwidth: the designations **x1, x4, x8, x16** indicate how many lanes are grouped for a given connection. An "x16" slot uses 16 lanes in parallel and therefore offers sixteen times the bandwidth of a single lane; an "x4" slot uses four, and so on.

This has a dual nature, *physical* and *electrical*, which causes much confusion. A slot can be **physically** as long as an x16 (i.e., have the mechanical space for 16 lanes) but be **electrically** connected only to x4 or x8, meaning it actually has a lower number of wired lanes. This is a very common situation on inexpensive motherboards: the second long slot, which looks like an x16, actually operates at x4. The device fits and works anyway, but at the reduced bandwidth of the lanes actually present. For this reason, when reading the technical specifications of a motherboard, one must always distinguish the physical length of the slot from the actual number of electrical lanes (often indicated with notations such as "x16 (x8 electrical)").

### 4.3 Generations: PCIe 3.0, 4.0, 5.0 and a brief mention of 6.0

Each new generation of PCIe **doubles** the bandwidth per lane compared to the previous one, for the same number of lanes. This generational doubling is the key to understanding everything else. The following table summarizes the approximate unidirectional bandwidth per configuration (the actual "useful" values are slightly lower due to encoding overhead, but the order of magnitude and ratios are correct):

| Generation | Bandwidth per lane (~) | x4 (typical NVMe) | x16 (typical GPU) | Diffusion (Jul. 2026) |
|---|---|---|---|---|
| PCIe 3.0 | ~1 GB/s | ~4 GB/s | ~16 GB/s | Legacy, still common on chipsets |
| PCIe 4.0 | ~2 GB/s | ~8 GB/s | ~32 GB/s | Consolidated standard, excellent q/p ratio |
| PCIe 5.0 | ~4 GB/s | ~16 GB/s | ~64 GB/s | Current consumer top (high-end GPU and SSD) |
| PCIe 6.0 | ~8 GB/s | ~32 GB/s | ~128 GB/s | Emerging, currently in server/datacenter environments |

PCIe 6.0, which doubles again, is technically defined and is arriving in the enterprise world, but on consumer desktops in mid-2026 it is not yet relevant: consumer CPUs and motherboards reach PCIe 5.0. We will discuss this in more detail in the chapter on workstations and servers, where extreme bandwidth truly matters.

### 4.4 Backward compatibility between generations and slots

One of PCIe's great virtues is **full backward and forward compatibility**. A newer generation device inserted into an older generation slot (for example, a PCIe 5.0 GPU in a PCIe 4.0 slot) works without issues, simply negotiating the speed of the lower of the two generations. Conversely, an older device in a newer slot works at its own speed. Furthermore, a device with fewer lanes fits into a longer slot: an x4 card works in an x16 slot (using only the lanes it needs). However, the mechanical inverse does not work: an x16 card does not physically fit into a shorter x1 slot, unless the slot is "open" at the back. The practical message is reassuring: PCIe "makes do" almost always, adapting to the lowest common denominator, and there is no risk of damage by mixing generations; at most, you lose a bit of bandwidth.

### 4.5 Who provides the lanes: CPU versus chipset, and why it matters

This is the most subtle and important point for making informed choices. PCIe lanes in a system come from **two different sources**, with very different characteristics. Some lanes are provided **directly by the CPU**: these are the fastest and lowest-latency, connected directly to the processor without intermediaries, and are reserved for the most bandwidth-hungry devices, typically the main graphics card slot (x16) and one or two primary NVMe slots. Other lanes are provided by the motherboard's **chipset**, which is connected to the CPU via a limited-bandwidth link (a "pipe" called *DMI* on Intel or a dedicated link on AMD).

The crucial point is that **all chipset lanes share that single pipe to the CPU**. If many devices connected to the chipset are loaded simultaneously (secondary SSDs, network cards, fast USB), they can saturate the link and contend for bandwidth, which does not happen on the direct CPU lanes. This is why a golden rule of assembly is: **the graphics card and the main NVMe SSD go into the slots directly connected to the CPU**, not into the chipset slots. The motherboard manual always indicates which slots are "CPU-attached" and which are "chipset-attached"; ignoring this distinction can lead, for example, to installing a fast SSD in a slot that halves its performance.

### 4.6 How many lanes do consumer CPUs have versus HEDT and server

Modern consumer CPUs provide a **limited** number of direct PCIe lanes: typically **20 to 28 lanes** (for example, 16 for the GPU, 4–8 for NVMe SSDs, and some for the chipset connection). This might seem few, and indeed it is: it's a positioning choice, as AMD and Intel reserve an abundance of lanes for higher-end, more expensive platforms. **HEDT** (*High-End Desktop*) and **server** platforms — such as AMD Threadripper and EPYC, or Intel Xeon — offer an enormously greater number of lanes, on the order of **64, 128, or more**. This abundance is what allows a workstation to simultaneously mount multiple GPUs, dozens of SSDs, capture cards, 100-gigabit network cards, and more, all at full bandwidth and without contention. This is a topic we will explore in more detail in the chapter dedicated to workstations; for now, suffice it to say that *the number of lanes is one of the invisible walls separating the consumer world from the professional one*, and it is often the real reason why certain "extreme" users need to upgrade their platform.

### 4.7 Bifurcation: what it is and when it's needed

**Bifurcation** is the ability to **split a single x16 slot into multiple smaller independent connections**, for example, into two x8s, or four x4s. The CPU has a certain number of physical lanes; bifurcation allows them to be reallocated differently from the default grouping. The typical consumer use case is to install an expansion card that hosts multiple NVMe SSDs (a "quad M.2 card") into a single x16 slot, asking the motherboard to divide those 16 lanes into four x4 groups, one for each SSD. It is also useful for those who want two graphics cards at x8/x8 instead of just one at x16. Bifurcation is not mandatory, and not all motherboards support it (it needs to be checked in the BIOS and specifications): it's a feature to check *before* purchasing if you have one of these scenarios in mind. For the average user, who installs a GPU and one or two SSDs in their respective slots, it is not a relevant factor.

### 4.8 Practical impact: GPU on x8 vs x16, NVMe Gen4 vs Gen5

Let's conclude with the most frequent practical questions. The first: **does a graphics card on x8 lose performance compared to x16?** The answer, surprising to many, is: almost always no, or very little. Modern graphics cards, even high-end ones, rarely saturate the bandwidth of a recent-generation x16 slot; if the slot is of a modern generation (PCIe 4.0 or 5.0), running it at x8 of that generation still leaves enormous bandwidth, and the actual frame rate loss is typically in the order of a few percentage points, often imperceptible. It only becomes more significant on very old generations or with cards at the limit of their bandwidth. This reassures those who, to install more devices, find their GPU running at x8.

The second: **is an NVMe PCIe 5.0 (Gen5) SSD worth it compared to a Gen4?** On paper, Gen5 doubles sequential bandwidth, and in benchmarks involving huge transfers, the difference is noticeable. In real-world daily use, however, the advantage is much less perceptible than the numbers suggest, because most common operations (system boot, program opening, game loading) depend more on random small-file performance than on peak sequential bandwidth. In addition, Gen5 SSDs tend to cost more, run hotter, and require more serious heatsinks. For the typical user, a good Gen4 currently offers the best value for money; Gen5 makes sense for specific professional video editing workloads with gigantic files or for those who simply want the absolute best regardless.

---

## 5. Commercial Nomenclature: How to Read a CPU Name

Knowing how to "decode" a CPU's name is a practical skill that prevents many errors. Let's look at the two manufacturers.

### 5.1 Intel: From Core i to Core Ultra

For almost fifteen years, Intel used the **Core i3 / i5 / i7 / i9** scheme, where the number indicates the increasing performance tier: i3 entry, i5 mid-range, i7 high-end, i9 enthusiast. The model was read as follows: after the designation came the **generation** (for example, "14" in "i7-14700K" indicated the fourteenth generation), followed by the model identification number and a letter **suffix**.

From 2023–2024, Intel changed its branding: the **Core Ultra** nomenclature was introduced, with **Core Ultra 5 / 7 / 9** tiers (and "Core" without Ultra for lower tiers), abandoning the old "i". On desktops, this new family is the **Core Ultra 200S "Arrow Lake"** series (for example, "Core Ultra 9 285K"), with the 2026 refresh identified by the **"Plus"** suffix (for example, "Core Ultra 7 270K Plus"). The reading logic remains similar: tier (5/7/9), then the model number, then the suffixes.

**Intel suffixes** are the part that most often causes confusion. Here's their meaning:

| Intel Suffix | Meaning |
|---|---|
| **K** | Unlocked multiplier: **overclockable**. Higher frequencies, requires "Z" chipset and good cooling. |
| **KF** | Like K (unlocked) but **without iGPU**: requires a dedicated graphics card. |
| **F** | **Without functional iGPU**: requires a dedicated graphics card. Costs a bit less. |
| **T** | **Low power** version (reduced TDP), for compact/silent systems; lower performance. |
| **(no suffix)** | Standard model, with iGPU, not overclockable but efficient. |
| **S** | Historically indicated "special edition" versions; today "S" primarily identifies the *desktop series* (200**S**). |

A "Core Ultra 5 245KF" is therefore read as: mid-range (5), model 245, **K**=overclockable, **F**=without integrated graphics (requires a dedicated GPU).

### 5.2 AMD: The Ryzen Logic

AMD uses the **Ryzen 3 / 5 / 7 / 9** scheme, with the same principle of increasing tiers: Ryzen 3 entry, Ryzen 5 mid-range, Ryzen 7 high-end, Ryzen 9 enthusiast. After the tier comes a **four-digit** number where the first digit indicates the **series/generation**: 5000, 7000, 9000. Be aware of a trap: the 8000 series for desktops (8700G APUs, etc.) is, in terms of architecture, somewhat out of sequence compared to the 7000/9000, and is primarily dedicated to APUs with powerful graphics. In AMD's case, the final suffixes are also crucial:

| AMD Suffix | Meaning |
|---|---|
| **X** | Higher frequencies and better boost than the base model; all desktop Ryzen CPUs are overclockable anyway. |
| **X3D** | Equipped with **3D V-Cache**: huge L3 cache, top for **gaming** (e.g., 9800X3D, 9850X3D). |
| **G** | **APU**: powerful integrated Radeon graphics, plays at 1080p without a dedicated graphics card (e.g., 8700G). |
| **GE** | Like G but **low power** (efficient version of APUs). |
| **F** | **No integrated graphics** (rare in AMD desktop; e.g., 8700F), requires a dedicated GPU. |
| **(no suffix)** | Standard "non-X" model, slightly lower frequencies but often excellent price/performance ratio. |

A "Ryzen 7 9800X3D" therefore reads as: high-end (7), 9000 series, **X3D**=with 3D V-Cache designed for gaming. Important and sometimes counter-intuitive note: in AMD desktops, *all* CPUs have an unlocked multiplier and are in principle overclockable (unlike Intel, where a "K" is needed); the "X" only indicates better binning and boost out of the box, not "unlocking."

### 5.3 Threadripper and Xeon: just a mention

Beyond consumer lines, there are professional lines: **Ryzen Threadripper** (AMD) and **Xeon** (Intel) are HEDT and server processors with many cores and many PCIe lanes, dedicated sockets, support for large amounts of RAM (often with ECC error correction), and prices of a different magnitude. They are not designed for gaming or home use, but for professional workstations, computing, and datacenters. We will cover them extensively in the chapter dedicated to workstations; here it suffices to know that they exist and represent a world separate from what is described in this chapter.

---

## 6. Brand Comparison: Intel vs. AMD

It should be noted that "who is better" constantly changes with each generation, and the following considerations reflect the situation in mid-2026: they should be re-verified at the time of purchase.

### 6.1 Current Strengths

In **pure gaming**, AMD currently has a rather clear advantage thanks to **X3D** models with 3D V-Cache, which are considered the fastest gaming CPUs on the market (the Ryzen 7 9800X3D and the more recent 9850X3D from 2026 dominate frame rate rankings). Intel, after a disappointing Arrow Lake debut in gaming, has recovered ground with the "200S Plus" refresh in 2026 and with software optimizations (such as the game code optimization tool introduced with the refresh), but in top-tier gaming, AMD remains the benchmark.

In **multi-thread productivity** (rendering, compilation, computation), the two brands are comparable depending on the model and price: high-core Ryzen 9s and Intel CPUs with an abundance of E-cores are both very valid, and it's advisable to look at specific benchmarks for your software. On the **energy efficiency** front, Intel has made significant progress with Arrow Lake, which consumes and heats up much less than the disastrous previous generations (13th/14th gen Core CPUs were known for high power consumption and degradation issues); AMD remains very competitive, and its X3D models in particular offer very high gaming performance at contained power consumption.

On the **price/performance ratio**, Intel's 2026 refresh brought very aggressive models back into play: the Core Ultra 5 250K Plus at around $199 and the 270K Plus at around $299 were welcomed as excellent deals for productivity. AMD responds with its consolidated lineup and, above all, with the long-term platform value, which we'll discuss immediately.

### 6.2 Platform: Total Cost and Longevity

The comparison should not only be made on the CPU, but on the **total platform cost**, which includes CPU + motherboard + RAM. Here, the issue of **socket longevity** plays a decisive role. Buying AMD **AM5** today means purchasing a platform with guaranteed support until at least 2029: an AM5 motherboard will also be able to host future CPUs (Zen 6/Ryzen 10000, expected in 2027), allowing for a future CPU-only upgrade without changing the motherboard and RAM. Buying Intel **LGA1851** today, on the other hand, means purchasing a socket that is already end-of-life, which will be replaced by LGA1954 in the second half of 2026: future CPU upgrades will be limited to the current family, and to move up a generation, a motherboard change will be required. This is, for many, the single strongest argument in favor of AMD for those thinking long-term.

However, there is a huge contingent factor to consider in 2026: the aforementioned **DDR5 memory price crisis**. With RAM prices even multiples of normal, the "platform cost" is currently dominated by memory more than by the CPU or motherboard. In some cases, a good kit of fast RAM costs more than the CPU itself. This advises, at this particular historical moment, not to oversize RAM beyond what is necessary and to give even greater weight to choosing a long-lasting platform, in order to dilute an investment that is particularly expensive today over time.

### 6.3 Use Cases

For **pure gaming**, especially at high frame rates and at 1080p/1440p, the most sensible choice today is an AMD **X3D** CPU (even a previous generation model often represents a good deal). For **streaming and gaming together**, where extra cores are needed for encoding as well as gaming, both Ryzen 7/9 and Intel CPUs with many E-cores are suitable, provided there is a good thread count. For **editing and rendering**, cores and threads matter: one looks for the best multi-thread score per euro, and here both brands have strong offerings depending on the budget. For **virtualization**, cores, threads, and the amount of supported RAM matter (and, if possible, ECC support, which is easier to find on consumer AMD): multi-core CPUs are preferable. For a **budget build**, today there are excellent options from both sides, including AMD "G" APUs that allow starting without a graphics card, and low-end Intel with a good q/p ratio; here the choice depends heavily on current local prices.

---

## 7. How to Evaluate and Choose a CPU

### 7.1 Benchmarks: What to Look For and What Not to Trust

**Benchmarks** are standardized tests that measure performance. They should be interpreted carefully, choosing those relevant to *your* specific use. **Cinebench** measures rendering performance and is a good indicator for productive multi-threaded workloads, providing both single-core and multi-core scores. **Geekbench** is a more general synthetic benchmark, useful for a quick overview but less representative of specific real-world workloads. For gaming, the most significant test is **1080p gaming benchmarks** with a very powerful graphics card: at low resolution, the GPU is not the bottleneck, so the measured differences truly reflect the CPU's power. It might seem counterintuitive to test gaming CPUs at 1080p instead of 4K, but this is precisely how the CPU's contribution is isolated: at 4K, many different CPUs yield the same frame rate because the GPU is the limiting factor.

What not to trust? Do not trust **manufacturer-provided benchmarks** without independent verification, as they are chosen to highlight their strengths. Do not trust a **single synthetic number** as a universal metric: a CPU might shine in one test and disappoint in the software you actually use. Do not trust **GHz** as already extensively explained. The right approach is to consult independent reviews that test the applications and games you are interested in, looking at averages across multiple titles and *frame time* values (consistency, not just the average), which better reflect real fluidity than average frame rate.

### 7.2 CPU/GPU Balancing: Avoiding Bottlenecks

A **bottleneck** occurs when one component is too slow compared to another and limits its performance: the chain is only as strong as its weakest link. In a gaming PC, a **CPU bottleneck** means the graphics card could produce more frames, but the CPU isn't feeding it data fast enough (typical at high frame rates and low resolution, or with weak CPUs and powerful GPUs); a **GPU bottleneck** is the opposite, with the CPU waiting for the graphics card (typical at high resolutions). A certain degree of GPU bottleneck is actually desirable in high-resolution gaming, as it means you are fully utilizing the graphics card.

Avoiding a bottleneck means **balancing** the CPU and GPU according to the intended use. A common mistake is to spend almost the entire budget on the GPU, pairing it with a cheap CPU (or vice versa): the expensive component will be throttled by the other. The practical rule is to size the two components to the same "tier" level, considering the resolution at which you will be gaming: at 1080p with high refresh rates, a strong CPU is needed; at 4K, a slightly more modest CPU can be afforded, and the budget concentrated on the GPU.

### 7.3 Overclocking: What's Needed and If It's Still Worth It

**Overclocking** (OC) is the practice of running the CPU at frequencies higher than factory settings to extract more performance. To do this on Intel, you need a CPU with a **K** suffix (unlocked multiplier) and a motherboard with a **Z** chipset; on AMD, all desktop CPUs are unlocked, but an adequate chipset is still needed (generally B or X series) and, in both cases, **robust cooling** and a good power supply, because OC increases power consumption and heat.

Is it still worth it? Much less than in the past. Modern CPUs already perform intelligent and aggressive "auto-overclocking" through boost algorithms, pushing themselves to the limit of available thermal headroom: the remaining manual gain is often modest and comes at the cost of significantly higher power consumption and heat, as well as stability risks. Today, it makes more sense to invest in a good cooler (which allows the CPU to *sustain* its automatic boosts for longer) and, on the memory front, activate the RAM's **XMP/EXPO** profiles (which are a "certified overclock" of the memory, simple and safe) rather than dedicating oneself to manual CPU overclocking. For the vast majority of users, extreme overclocking has become a hobby for enthusiasts rather than a necessity.

### 7.4 Budget-tier recommendations

The following prices are indicative orders of magnitude for the CPU alone (European market, July 2026) and **should be verified**, especially in the current context of high volatility. Remember also to always add the motherboard and RAM for the real cost of the platform.

| Tier | CPU Budget (indicative) | Use Case | Recommended Approach |
|---|---|---|---|
| **Entry** | ~100–150 € | Office, study, light gaming, secondary PC | Recent 6-core CPU; consider AMD "G" APU to start without a dedicated GPU; inexpensive (B) chipset, stock cooler often sufficient. |
| **Mid** | ~200–350 € | Solid gaming, daily productivity, light streaming | 6–8 performing cores; for gaming, an AMD X3D is often the best investment; good quality B motherboard, decent air cooler. |
| **High-end** | 400 €+ | Top-tier gaming, heavy editing/rendering, light workstation | Top models (X3D for gaming, many cores for productivity); high-end motherboard if expansions are needed; liquid or high-level air cooling. |

A cross-cutting tip: for *gaming*, often the best use of money is to get a mid-to-high-end X3D CPU and focus the rest of the budget on the graphics card, because the GPU plays the lion's share in frame rates at high resolutions. For *productivity*, conversely, investing in CPU cores yields more.

---

## 8. Common buyer mistakes

We conclude with the mistakes that, in practice, ruin the most builds and waste the most money.

The first, almost a classic, is **buying a CPU with an "F" (or "KF") suffix without having a dedicated graphics card**. Attracted by the small saving, one ends up with a PC that gives no video signal, because the CPU lacks integrated graphics. If a dedicated GPU is not planned, this suffix should be absolutely avoided.

The second is **socket or BIOS compatibility error**: buying a CPU and a motherboard with different sockets, or a motherboard that is "compatible on paper" but with a BIOS too old to recognize the CPU, without the flashback function to update it cold. This is avoided by checking the socket, chipset, and the motherboard's official CPU support list, with the minimum required BIOS version.

The third is **pairing a powerful CPU with an inadequate cooler** (or relying on the included "stock" cooler when it's not up to the task). The result is thermal throttling: the CPU overheats, cuts frequencies, and never maintains the boost you paid for. High-end CPUs, especially when overclocked or under full load, require serious cooling; saving money here means throwing away performance paid for elsewhere.

The fourth is **neglecting RAM in relation to the IMC**. The **IMC** (*Integrated Memory Controller*) is the part of the CPU that communicates with the RAM, and system performance heavily depends on the CPU-memory pairing. Installing slow RAM, in single channel (a single module instead of two, losing *dual channel* which doubles bandwidth), or forgetting to activate the **XMP/EXPO** profile in the BIOS (thus leaving the RAM at very low base frequencies) are errors that bottleneck an otherwise capable CPU. AMD architectures, in particular, are historically sensitive to RAM speed and latency. In 2026, however, the flip side must be added: with DDR5 prices skyrocketing, overdoing it with ultra-fast and ultra-high-capacity RAM can cost more than the CPU itself; the right balance is to aim for a good dual-channel kit at a sensible frequency, activate its XMP/EXPO profile, and not unnecessarily oversize.

The fifth error is the **CPU/GPU imbalance** already discussed: spending everything on one component and bottlenecking it with the other. The sixth, more subtle, is **paying for PCIe lanes or features that won't be used** (very expensive motherboards bought for simple gaming) or, conversely, **installing the fast SSD or GPU in the wrong slots** (chipset slots instead of those directly connected to the CPU), losing bandwidth without realizing it. Finally, an error in perspective: **chasing the latest generation at all costs**, when often the previous generation model, now discounted, offers 90% of the performance at a fraction of the price — a particularly valid consideration today, with new AMD generations postponed to 2027 and the current Intel socket already at end-of-life.

---

## Operational Summary — CPU Decision Checklist

Before purchasing, answer these questions in order:

1. **Define the primary use.** Pure gaming, productivity/rendering, mixed use, or office/budget? This question guides everything else (for gaming, look at AMD X3D; for productivity, count the cores).

2. **Set the budget for the entire platform, not just the CPU.** Sum CPU + motherboard + RAM. In 2026, carefully check the price of DDR5, which can be the most significant item.

3. **Choose the platform with longevity in mind.** AM5 (AMD) is supported at least until 2029 and will host future CPUs; LGA1851 (Intel) is at the end of its cycle. If you plan future CPU-only upgrades, this weighs heavily.

4. **Verify compatibility on three levels:** identical socket, chipset adequate for the use (Z/X for overclocking, B for saving money), and supported BIOS version (check the official motherboard CPU support list). Prefer motherboards with BIOS Flashback.

5. **Check the CPU suffix.** Do you avoid "F/KF" if you don't have a dedicated GPU? Do you need a "G" APU to start without a graphics card? Do you want an "X3D" for gaming?

6. **Size your cooling solution for actual power consumption (PL2/PPT), not TDP.** Pair your chosen CPU with an adequate cooler, or you will lose performance due to throttling.

7. **Balance CPU and GPU** based on gaming resolution to avoid bottlenecks. Don't concentrate all your budget on a single component.

8. **Plan RAM and PCIe correctly:** RAM in dual channel with XMP/EXPO profile active, without over-sizing in 2026; main NVMe SSD and GPU in slots directly connected to the CPU, not to the chipset.

9. **Trust independent benchmarks** for the loads you will actually use (1080p gaming to isolate the CPU, Cinebench for productivity), not GHz or manufacturer slides.

10. **Consider discounted previous generations.** They often offer much of the performance for much less, especially in a market phase like the current one.

---

*Final note: references to models, sockets, and prices reflect the market status in July 2026 (Arrow Lake Refresh "200S Plus", Ryzen 9850X3D, AM5 until 2029, Intel LGA1954/Nova Lake socket coming soon, Zen 6/Ryzen 10000 expected in 2027, DDR5 price crisis). Always check availability and updated price lists before purchasing.*

---

[All chapters](./) · [Next →](02-motherboard.html)
