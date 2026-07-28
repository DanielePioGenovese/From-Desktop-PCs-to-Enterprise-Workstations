---
title: "Chapter 5 - Storage"
parent: "English Edition"
nav_order: 5
---

<details open markdown="block">
  <summary>Chapter contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## 1. Overview: What is storage and why is it different from memory

Every time you turn on a computer, something we take for granted happens: the machine "remembers" itself. It finds the operating system, the installed programs, the documents written the day before, the photos from ten years ago. This ability to remember beyond shutdown is the task of **storage**, i.e., mass memory, and to truly understand what it does, one must first understand what RAM *doesn't* do.

RAM (Random Access Memory) is **volatile**: it retains data only as long as it is powered. The exact moment we cut power, the contents of the DRAM cells vanish, because each bit is stored as a tiny electrical charge in a capacitor that discharges in milliseconds if not continuously "refreshed." RAM is extremely fast and completely amnesic. Storage is the exact opposite: it is **non-volatile**, meaning it retains information even without power, for months or years, but it pays for this persistence with latencies that, compared to RAM, are astronomical. To give a mental scale: an access to RAM costs about 80–100 nanoseconds; an access to a good NVMe SSD costs 50,000–80,000 nanoseconds (i.e., 50–80 microseconds); an access to a mechanical hard disk costs 8,000,000–15,000,000 nanoseconds (8–15 milliseconds). Between RAM and hard disk, there are five orders of magnitude: it's the difference between picking up a book from your desk and ordering it by mail.

The computer is therefore built as a **memory hierarchy**, where each level decreases in speed and increases in capacity and persistence: CPU registers → L1/L2/L3 cache → RAM → storage → (possibly) remote archiving or offline backup. Storage is the step where the pyramid becomes permanent. Everything we want to find again tomorrow must, sooner or later, land there.

The role of storage in the system is twofold. On one hand, it is the **archive**: it contains the operating system, executables, and user data. On the other hand, it is the **bottleneck of perceived responsiveness**. This second point is what users most underestimate. When a PC is turned on, when a heavy program is opened, when a video game loads a level, or when a development project compiles thousands of source files, what determines the seconds of waiting is almost never the raw power of the CPU: it is the speed with which data travels from storage to RAM. A high-end processor paired with a mechanical disk gives a feeling of slowness that no amount of cores can compensate for, because the CPU spends most of its time waiting (in jargon, in *I/O wait*, input/output wait). This is why, in the priority scale of an assembly, the transition from HDD to SSD is historically the upgrade with the best cost-to-perceived-improvement ratio ever in the history of personal computers.

### 1.1 The evolution: HDD → SATA SSD → NVMe SSD

The history of consumer storage over the last twenty years is the story of two successive revolutions, and it is useful to retrace it because it explains why today's market has the shape it does, with its remnants and its pitfalls.

**Phase One: The Reign of the HDD (up to ~2010).** For decades, the only economically sensible mass storage was the magnetic hard disk drive, an electromechanical device with moving parts. All PC software architecture — the way operating systems order read requests, the very concept of "defragmentation," caching strategies — was designed around the physical limitations of a mechanical arm moving over a spinning platter.

**Phase Two: The Arrival of SATA SSDs (2008–2015).** SSDs (Solid State Drives) replace moving parts with flash memory. But the first consumer SSDs emerged in a world where the standard interface was **SATA** (Serial ATA, Serial Advanced Technology Attachment), an interface designed *for hard drives*. SSDs thus entered the PC disguised as hard drives: same cable, same connector, same 2.5-inch form factor, same software protocol (AHCI, which we will discuss). The result was still a perceived revolution — boot times plummeted from a minute to ten seconds — but it was a half-revolution, because the SSD was speaking a language designed for a mechanical device. The SATA III bandwidth limit, about 550 MB/s real-world, was saturated almost immediately by the first generations of decent SSDs, and from then on, all SATA SSDs in the world — from the cheapest to the most expensive — reported more or less the same sequential numbers. The bottleneck had shifted from the drive to the interface.

**Phase Three: NVMe and PCIe (2015–present).** The solution is to remove the legacy interface and connect the flash memory directly to the fastest bus available in the system, **PCIe** (Peripheral Component Interconnect Express), the same bus to which the graphics card is connected. A new protocol, **NVMe** (Non-Volatile Memory Express), is defined over PCIe, designed from scratch for flash memory: massive parallelism, multiple queues, reduced latencies, no mechanical heritage. The result, within a few years, is a bandwidth leap from 550 MB/s to 3,500 MB/s (PCIe 3.0), then 7,400 MB/s (PCIe 4.0), then 14,000 MB/s (PCIe 5.0), with latencies decreasing accordingly.

Today, in 2026, the consumer landscape is this: NVMe SSDs are the de facto standard for system drives, SATA SSDs survive as inexpensive secondary storage and in older machines, and HDDs have retreated into a precise but still very important niche — high-capacity, low-cost-per-terabyte archiving. None of these three technologies are "dead." They are simply specialized.

> **[volatile data]** Since the end of 2025, the storage market has been experiencing an anomalous phase of sharp price increases. The demand for memory generated by artificial intelligence infrastructures has siphoned off NAND and DRAM production capacity towards the enterprise segment, resulting in double-digit percentage price increases for consumer SSDs and reduced availability for some product lines. During the same period, Micron announced its exit from the consumer market with the Crucial brand, to focus on datacenters and OEM customers: the Crucial models mentioned in this chapter remain valid as a technical reference and may still be available as residual stock, but should no longer be considered an evolving product line. All prices indicated below should therefore be taken as orders of magnitude to be verified at the time of purchase, not as reliable values.

---

## 2. HDD vs. SSD: Two Different Physical Architectures

### 2.1 How a Hard Disk Works

An **HDD** (Hard Disk Drive) is, literally, a precision turntable sealed in an aluminum box. Inside, there are one or more **platters**, rigid aluminum or glass disks coated with a very thin layer of ferromagnetic material. The platters are mounted on a single spindle connected to a motor that rotates them at a constant speed: typically 5,400 Revolutions Per Minute (RPM) in archival and laptop models, 7,200 RPM in desktop and NAS models, up to 10,000 or 15,000 RPM in older, now obsolete enterprise drives.

Information is stored by orienting the magnetization of tiny regions of the coating: one direction is "0", the other is "1". **Heads**, mounted at the end of mechanical arms that move radially over the surface of the platters, like the arm of a record player, read and write these orientations. Each surface of each platter has its own head, and all arms move together, in unison. The heads never touch the platter: they "fly" a few tens of nanometers from the surface, supported by the air cushion generated by the rotation. If a head touches the moving platter — due to a bump, for example — a notorious *head crash* occurs, which scrapes away the magnetic coating and destroys data in that area.

Data is organized into concentric circles called **tracks**, divided into **sectors** (historically 512 bytes, now 4096 physical bytes in the *Advanced Format* standard). The set of tracks that are at the same distance from the center on all platters forms a **cylinder**.

The slowness of the HDD directly stems from this architecture. To read data, the disk must perform two physical movements in sequence:

1.  **Seek time**: the arm must move the head to the correct track. This typically costs 8–12 milliseconds on a desktop drive.
2.  **Rotational latency**: once on the correct track, one must wait for the desired sector to pass under the head due to the platter's rotation. On average, one waits half a rotation. At 7,200 RPM, one rotation takes approximately 8.3 ms, so the average latency is about 4.2 ms; at 5,400 RPM, it rises to about 5.6 ms.

Summing up, a "cold" access to a random disk location costs on the order of **10 milliseconds**. And here's the crucial point: that time is **physical**, dictated by mechanics, and cannot be reduced by any electronic improvement. A modern 24 TB HDD reads sequential data at 280 MB/s, a speed that is by no means negligible—it's half that of a SATA SSD. But if we ask it to read four thousand small files scattered across the disk, as an operating system does at startup, it has to pay 10 ms for each: the throughput drops to values in the order of **1–2 MB/s**, and this is why a PC with a hard disk seems bogged down in molasses. It's not slow to read: it's slow to *seek*.

Random access performance is measured in **IOPS** (Input/Output Operations Per Second). A 7,200 RPM HDD performs 75–120 IOPS. A mid-range NVMe SSD performs **one million**. The ratio is approximately 1 to 10,000, and no number in this chapter is more important than this.

### 2.2 How an SSD works

An **SSD** (Solid State Drive) has no moving parts. It is a specialized small computer, composed of three fundamental elements.

The first is **NAND flash memory**, the chips that physically contain the data. NAND stores information by trapping electrons in an isolated structure called a *floating gate* (or, in more modern implementations, a *charge trap*) within a transistor cell. The presence or absence of charge—or, more precisely, the *level* of charge—determines the stored value. Since the insulator retains electrons even without power, the memory is non-volatile. This is a remarkable but not eternal property: an SSD left unplugged for years slowly loses charge and, eventually, data. For normal use, this is irrelevant; for long-term storage in a drawer, it is not (we will return to this).

Cells are organized into **pages** (typically 4–16 KB), and pages into **blocks** (hundreds of pages, for a few megabytes). Here lies the fundamental asymmetry of flash, which explains almost everything strange an SSD does: **data is read and written by pages, but erased only by entire blocks**. You cannot overwrite an already written page: you must first erase the entire block containing it. Consequently, when the operating system "modifies" a file, the SSD actually writes the new version to a free page elsewhere, marks the old one as obsolete, and sooner or later will have to clean up by consolidating valid data and erasing blocks now full of garbage. This cleanup is called **garbage collection**, and together with the **TRIM** command (with which the operating system communicates to the SSD which blocks belong to deleted files and therefore no longer need to be preserved) it is what allows the drive to maintain decent performance over time.

The second element is the **controller**, the SSD's processor. It manages everything: the translation between the logical addresses seen by the operating system and the actual physical locations in the NAND (a table called a *mapping table*, managed by the **FTL** (Flash Translation Layer) firmware), garbage collection, error correction via **ECC** (Error Correcting Code), and **wear leveling**, which is the uniform distribution of writes across all cells so that none wear out before the others. The controller is, no less than the NAND, what determines the quality of an SSD: two units with the same memory chips but different controllers can behave radically differently under load.

The third element — present only in mid-to-high-end models — is the **DRAM cache**: a DRAM chip (often 1 GB per 1 TB of capacity) that the controller uses to keep the mapping table in fast memory. We will discuss this in detail in section 5, because it is one of the most important discriminators between a good SSD and a mediocre one.

It follows that accessing data on an SSD does not require any physical movement: the controller consults the table, identifies the chip and page, and queries them electrically. Latency is in the order of tens of microseconds and is **substantially identical** whether the data is "at the beginning" or "at the end" of the disk, and whether a large file or a thousand small files are being read. It is the disappearance of seek cost, not higher bandwidth, that is the real reason why SSDs have changed the computer user experience. From this also derives the fact that **defragmenting an SSD is useless** and even harmful, because fragmentation is only a problem for those who have to move a read/write head, and unnecessary writes consume cells.

### 2.3 When HDD still makes sense

Having said all this, it would be a mistake to conclude that the hard disk is obsolete. The HDD still has one robust advantage and one secondary advantage.

The robust advantage is the **cost per terabyte**. A 20 TB hard disk today costs, as an order of magnitude, less than a third of what the same capacity in SSD would cost *(variable data, and made more favorable to HDD by the ongoing NAND price increase)*. For a library of 40 TB of videos, backups, and archives, the difference is not a detail: it is the difference between a feasible project and an unfeasible one.

The secondary advantage is **long-term data retention without power**. A properly stored hard disk retains data for years without being powered on; an SSD, which relies on electrical charges trapped in an insulator, tends to degrade more quickly if left disconnected for very long periods, especially if the cells are already worn and if storage temperatures are high. For a "cold" backup to put in a drawer and forget, an HDD is still the most sensible choice — provided, of course, that it is connected and checked periodically, and that you never rely on a single copy.

The scenarios where HDD is still the right answer are therefore:

- **Cold storage**: media collections, photo archives, rarely accessed and sequentially consulted material.
- **Backup**: destination drive for periodic backups, where capacity matters more than latency.
- **Home NAS and small servers** (Network Attached Storage): here, HDDs reign, both for cost and because the bottleneck is often the network itself. A gigabit network transfers a maximum of about 110 MB/s: an HDD saturates it effortlessly, and putting expensive SSDs behind such a connection brings no bandwidth benefit (it does, however, bring a benefit in latency and IOPS, which matters if the NAS hosts virtual machines or databases, not if it hosts movies).
- **Video surveillance**: continuous, sequential recording of 24/7 video streams, for which specific drives exist.

What the HDD should **not** do anymore, in 2026, is host the operating system. There is no debate or budget margin on this: even the cheapest PC must boot from an SSD.

### 2.4 HDD / SATA SSD / NVMe SSD Summary Table

| Characteristic | HDD 7,200 RPM | SATA SSD | NVMe PCIe 4.0 SSD |
|---|---|---|---|
| Sequential Read | 150–280 MB/s | ~550 MB/s | 5,000–7,400 MB/s |
| Sequential Write | 150–280 MB/s | ~520 MB/s | 4,000–7,000 MB/s |
| Random Read 4K IOPS | 75–120 | 80,000–100,000 | 600,000–1,500,000 |
| Typical Latency | 8–15 ms | ~100 µs | 40–80 µs |
| Moving Parts | Yes | No | No |
| Shock Resistance | Low | High | High |
| Noise Level | Audible | Silent | Silent |
| Typical Power Consumption | 5–9 W | 2–4 W | 4–9 W (peaks) |
| Max Consumer Capacity | 24–30 TB | 8 TB | 8 TB (rare), 4 TB common |
| Cost per TB | Lowest | Intermediate | Highest |
| Ideal Use | Archive, backup, NAS | Secondary storage, old PC upgrade | OS, games, work |

---

## 3. SATA vs. NVMe: Interface and Protocol

Here, a distinction that many buyers confuse is essential. A storage unit is characterized by **two different things**:

- the **physical and electrical interface**: the wires on which bits travel (SATA, PCIe);
- the **logical protocol**: the language with which the operating system and the unit communicate over those wires (AHCI, NVMe).

Historically, the two things went hand in hand — SATA used AHCI — and so the habit of treating them as synonyms spread. But, as we will see with M.2, today they are not at all, and that is precisely where purchasing errors lie.

### 3.1 SATA III and AHCI

**SATA** (Serial ATA) is the interface introduced in the early 2000s to replace the old parallel ATA with its ribbon cables. In its third revision, **SATA III** (or SATA 6 Gb/s), it offers a raw bandwidth of 6 gigabits per second over a single serial channel. Six gigabits seem like a lot, but the line coding used (8b/10b: for every 8 bits of data, 10 are transmitted, for synchronization reasons) takes up 20% of the bandwidth, and the protocol overhead eats up the rest. The practical result is a ceiling of about **550–560 MB/s** in sequential read. This number is **the wall**: any SATA SSD, today as ten years ago, stops there. There is no "faster" SATA SSD in sequential, because the limit is not in the drive but in the pipe.

Physically, SATA uses **two separate cables**: a thin, seven-contact data cable that runs from the drive to a SATA port on the motherboard, and a fifteen-contact power connector that comes from the power supply. This is a trivial detail but it's the primary cause of panic for novice builders who connect only the data cable and don't understand why the drive isn't detected.

Logically, SATA uses **AHCI** (Advanced Host Controller Interface), a protocol designed in the era of hard drives. AHCI has a characteristic that today seems grotesque: it manages **only one command queue**, with a maximum depth of **32 commands** (a feature known as NCQ, Native Command Queuing). The historical reason is obvious: what was the point of sending a thousand simultaneous requests to a device that has only one read/write head and can serve only one at a time? The single queue served to reorder requests to minimize arm movements, not to exploit parallelism that didn't exist. But flash memory is intrinsically parallel: an SSD has dozens of NAND chips connected to the controller on multiple independent channels, and could serve many requests simultaneously. Forcing it into AHCI means bottlenecking it. Furthermore, AHCI requires the CPU to perform management tasks (register accesses, interrupt handling) that introduce non-negligible software latency when operations per second reach hundreds of thousands.

### 3.2 NVMe

**NVMe** (Non-Volatile Memory Express) is the protocol born to solve exactly these problems. It runs **directly on the PCIe bus**, completely bypassing the motherboard's SATA controller, and at a logical level, it is built around three ideas.

The first is **massive parallelism**: NVMe supports up to **65,535 queues**, each up to **65,535 commands** deep. These are hyperbolic numbers that no consumer use even remotely approaches, but the point is not the number: it's that the protocol is no longer a bottleneck. An SSD can receive thousands of in-flight requests and reorder them internally to distribute them across its NAND channels.

The second is **core affinity**: each CPU core can have its own dedicated queue, without having to contend for locks with other cores. This eliminates a serialization point that, at high I/O frequencies, had become dominant.

The third is **reduced software path**: NVMe requires far fewer register accesses per command compared to AHCI, supports MSI-X interrupts, and drastically reduces overhead per operation.

The combined result is that an NVMe SSD is not just "faster in bandwidth": it is **faster in latency** and, above all, handles loads with deep queues much better, i.e., when many requests arrive together.

### 3.3 Real performance comparison: where it matters and where it doesn't

Now we come to the part that deserves the utmost honesty, because it's where marketing lies by omission.

The numbers printed on the box are the **sequential ones**: "up to 7,400 MB/s read." These are obtained by reading a huge continuous stream, with deep queues, under ideal conditions, on an empty and cold drive. They correspond to real-world use in very few cases: copying a gigantic file from one fast NVMe to another fast NVMe, working with uncompressed ultra-high-resolution video streams, loading enormous datasets into memory.

The workload that **dominates daily use**, however, is completely different: **small random reads and writes** (the famous "random 4K"), often with **few requests in flight at a time** (low queue depth, QD1–QD4). Opening an application, starting the operating system, loading game textures, compiling a project, opening a project in an IDE: these are all sequences of thousands of small, scattered reads.

And here's the uncomfortable truth: in **low queue depth random 4K**, the difference between a good SATA SSD and an excellent NVMe PCIe 5.0 is not tenfold, as sequential numbers might suggest. It's typically **two or three times** – significant, but not miraculous. And the difference between a mid-range NVMe PCIe 4.0 and a top-tier PCIe 5.0, under the same load, is often **just a few percentage points**, completely invisible to the naked eye.

To summarize brutally, but usefully:

| Transition | Perceived Improvement |
|---|---|
| HDD → SATA SSD | **Huge.** The PC feels like a different computer. |
| SATA SSD → NVMe PCIe 3.0/4.0 | **Modest but real.** Slightly faster boot and load times, much faster file copies, much more headroom under heavy load. |
| NVMe Gen4 → NVMe Gen5 | **Almost negligible for the average user.** Measurable in benchmarks, invisible in use, except for specific professional workloads. |
| DRAM-less QLC SSD → DRAM TLC SSD | **Noticeable when it counts** (prolonged writes, full disk, heavy multitasking). |

This last point deserves emphasis because it is the core of the chapter: **the class of an SSD matters more than its PCIe generation**. A good Gen3 with DRAM and TLC NAND will outperform, in real-world use, a cheap DRAM-less Gen4 with QLC NAND almost every time the workload gets serious, despite the latter having double the sequential numbers on the box.

**DirectStorage** technology on Windows and its console equivalents deserve a separate mention. This technology allows games to load compressed assets from the SSD directly into GPU memory, reducing the need to pass through the CPU. On paper, it's the technology that would finally justify faster NVMes in gaming. In practice, adoption has been much slower than expected, and the measured benefits on the few titles that implement it remain modest on systems with decent CPUs. It's a reason not to buy an HDD or a SATA for games; it is **not** a reason to pay for a Gen5.

---

## 4. Form factor, M.2 slot, and physical compatibility

### 4.1 Physical formats

Consumer storage drives today come in three main physical forms.

The **3.5-inch** format is for desktop hard drives: an aluminum brick approximately 147 × 102 × 26 mm, which screws into a case cage, powered via SATA power and connected via SATA data. It is the only format where enormous capacities (12, 16, 20, 24 TB) are found.

The **2.5-inch** format is for SATA SSDs and laptop hard drives: approximately 100 × 70 mm, with a thickness of 7 mm (sometimes 9.5 mm on HDDs). Here too, two SATA cables. It is the format for classic upgrades on machines a few years old.

The **M.2** format is a flat card that plugs directly into a slot on the motherboard and is secured with a screw (or, on recent boards, with a tool-less snap fastener). It has no cables: power and data pass through the slot. It is the dominant format for modern SSDs and is also the main source of confusion, for the reason that follows.

### 4.2 The Number One Trap: M.2 SATA vs. M.2 NVMe

**The M.2 slot is a physical form factor, not a protocol.** There are M.2 SSDs that, despite having an identical shape, speak **SATA** and not NVMe: internally, they have a SATA controller, are limited to the usual 550 MB/s, and use the AHCI protocol. They appeared during the transition years and can still be found in marketplaces and in "too good to be true" offers.

The result is that an inexperienced buyer might purchase an "M.2 SSD," install it, and find themselves with the performance of a ten-year-old SATA drive, without understanding why. Worse: many motherboards have M.2 slots that support **only NVMe** and not SATA. In that case, the M.2 SATA drive is simply not detected, and the user spends an afternoon looking for non-existent faults.

The practical rule: **when buying an M.2 SSD, you must explicitly read "NVMe" and "PCIe" in the description.** If it's not written, assume it isn't. And when choosing a motherboard, you should read the manual to see what each M.2 slot supports, because on many boards, the primary slot is NVMe only, while a secondary one accepts both.

### 4.3 M.2 Keys

The M.2 connector has **notches** (*keys*) that prevent it from being inserted into incompatible slots. There are three that are relevant to storage.

The **B key** has the notch in a position that, electrically, corresponds to a maximum of **two PCIe lanes** plus SATA signals. By itself, it is now rare in modern SSDs.

The **M key** has the notch in another position and corresponds to **four PCIe lanes**. This is what fast NVMe SSDs use: if a drive has only one notch, and it's on the right when looking at the connector, it's an M key.

The **B+M key** has **two notches** and is therefore mechanically compatible with both B and M slots. B+M units are mostly M.2 SATA or NVMe limited to two PCIe lanes, because the double notch implies the lowest common electrical denominator. **An SSD with two notches is never a high-end NVMe x4.** This is a very useful visual clue: if you see two notches in the product photo, you are looking at a slow or otherwise limited unit.

It should be added, for completeness, that the M.2 form factor does not only host SSDs: Wi-Fi cards (usually A or E key, shorter), 5G modules, and more. These slots are physically similar but electrically different, and the keys serve precisely to prevent inserting the wrong thing in the wrong place.

### 4.4 Dimensions: 2280 and Others

The numerical designation of an M.2 encodes width and length in millimeters: **2280** means 22 mm wide and 80 mm long. It is by far the most common format and, except in special cases, it is the one you should buy.

| Designation | Dimensions | Typical Use |
|---|---|---|
| 2230 | 22 × 30 mm | Handheld consoles (Steam Deck, ROG Ally), ultrabooks |
| 2242 | 22 × 42 mm | Compact laptops, some mini-PCs |
| 2260 | 22 × 60 mm | Rare, some laptops |
| **2280** | **22 × 80 mm** | **Desktop and laptop standard: 95% of the market** |
| 22110 | 22 × 110 mm | Enterprise/workstation, often with PLP |

The **22110** format (110 mm in length) is almost exclusively found in enterprise environments, where the extra space is used to house the capacitors of the **PLP** (Power Loss Protection): a bank of capacitors that, in the event of a sudden power outage, provides the necessary energy to write data that was still in the volatile cache to the NAND, preventing corruption. This is a valuable feature in servers and practically absent in consumer products, where at most a "partial" PLP is found that only protects metadata.

Please note: desktop motherboard slots usually have mounting holes for 2280 and sometimes for shorter lengths, but almost never for 22110. If you want to repurpose a 110 mm enterprise SSD, you need to verify that the motherboard can physically accommodate it.

### 4.5 PCIe Generations in SSDs

Each **PCIe** generation doubles the bandwidth per lane. A consumer NVMe SSD almost always uses **four lanes** (x4).

| Generation | Bandwidth per Lane | x4 Bandwidth (Theoretical) | Typical Real Sequential Speed |
|---|---|---|---|
| PCIe 3.0 | ~985 MB/s | ~3.9 GB/s | 3,000–3,500 MB/s |
| PCIe 4.0 | ~1.97 GB/s | ~7.9 GB/s | 5,000–7,400 MB/s |
| PCIe 5.0 | ~3.94 GB/s | ~15.8 GB/s | 10,000–14,900 MB/s |
| PCIe 6.0 | ~7.88 GB/s | ~31.5 GB/s | Not yet in consumer *[volatile data]* |

PCIe is **backward compatible in both directions**: a Gen4 SSD inserted into a Gen3 slot will work, limited to Gen3 speed; a Gen3 SSD in a Gen5 slot will work, at Gen3 speed. Nothing breaks, nothing is lost except bandwidth that would not have been used anyway. This is important to know because it allows you to buy a Gen4 drive today even on a Gen3 platform, with a view to a future motherboard change.

**Who really needs Gen5?** The honest answer is: very few consumer users. Concrete benefits are seen in scenarios of transferring very large files between two Gen5 drives, in very high-resolution video editing with lightly compressed codecs, in professional data analysis workloads where tens of gigabytes are read in streaming, and in some AI workloads (loading model weights, datasets). For gaming, browsing, office productivity, programming, the difference compared to a good Gen4 is within the margin of error.

Gen5 also has two concrete drawbacks. The first is the **price**, significantly higher for the same capacity. The second is **heat**: first and second-generation Gen5 controllers consume a lot (peaks of 10–12 W have been observed compared to 6–8 W for a Gen4), get very hot, and **absolutely** require a serious heatsink, sometimes bulky enough to interfere with the graphics card or the CPU cooler. Some Gen5 models with more efficient controllers have improved the situation, but the issue remains.

### 4.6 Heatsinks and Thermal Throttling

**Thermal throttling** is the automatic reduction of performance that a device applies to itself when it exceeds a certain temperature, to prevent damage. In NVMe SSDs, this primarily concerns the **controller**, which is the component that truly heats up.

There's a counter-intuitive subtlety: while the controller wants to stay cool, **NAND performs better when hot** (cells write more easily at high temperatures) but suffers when *retaining* data when hot and powered off. For this reason, sensors and thresholds are primarily calibrated for the controller.

When is a heatsink needed?

- A **Gen3** SSD practically never needs one in a case with minimal airflow.
- A **Gen4** SSD benefits from one under prolonged loads; many mid-to-high-end motherboards include an integrated one, which is more than sufficient. Buying an aftermarket one is rarely necessary.
- A **Gen5** SSD **always** needs one. Without adequate dissipation, it will throttle within a few seconds of sustained writing and end up being slower than a well-cooled Gen4: one of the saddest spectacles in modern computing.

Two practical warnings. First: if the motherboard includes its own M.2 heatsink, there's **no need** to buy an SSD with a pre-installed heatsink — in fact, the two will conflict, and one will need to be removed. Second: if the SSD is intended for a **PlayStation 5**, a heatsink is mandatory but must respect the height limits imposed by the slot (approximately 11.25 mm total), so you need to buy models explicitly stated as compatible, not just any desktop heatsink.

Finally, a detail on installation: do aftermarket heatsinks require removing the SSD's label? **No.** The label of many SSDs contains a copper or aluminum layer that aids in dissipation, and removing it can, in some cases, void the warranty. The thermal pad should be applied over the label.

### 4.7 Lane sharing: when an SSD disables SATA ports

This is where the storage chapter intertwines with the motherboard chapter, and it should be read carefully because it's the source of the most insidious assembly errors.

**PCIe lanes** are a finite resource. The CPU provides a certain number (typically 20–28 usable on modern consumer platforms: 16 for the graphics card, 4 or 8 for one or two direct M.2 slots, plus the link to the chipset). The **chipset** (the "southbridge," the support chip on the motherboard) provides others, but all of these must pass through the connection between the chipset and the CPU, which is itself limited and shared with USB, network, audio, and the rest.

Since lanes are never enough, motherboard designers **multiplex** them: certain resources are alternatives to each other. Typical configurations found in manuals are:

- Populating the second or third M.2 slot **disables two or four SATA ports** (because those lanes were shared).
- Populating a certain M.2 slot **reduces the PCIe slot of the graphics card from x16 to x8** (impact on GPU performance: in most cases negligible on Gen4/Gen5, but not on Gen3).
- Populating a second PCIe slot **disables** an M.2.

The practical consequence: you install the new NVMe in the wrong slot, and suddenly the old SATA SSD or archive hard drive **disappears from the BIOS**. The PC works, but half of the storage is gone. Those unfamiliar with the phenomenon spend hours blaming cables, the power supply, or the drive itself.

The rule: **before buying, open the motherboard manual PDF and look for the "M.2 and SATA configuration" table (or similar)**. It's always there, usually on a page nobody reads, and it clarifies exactly which slot shares what. A second practical criterion: the M.2 slot **directly connected to the CPU** (usually the first one, immediately below the video card slot) is the fastest and typically **does not** steal SATA ports; that's where the system drive goes.

---

## 5. NAND technology and key components

### 5.1 SLC, MLC, TLC, QLC: how many bits per cell

We've said that a NAND cell stores information as a trapped electrical charge level. The crucial question is: **how many distinct levels can we distinguish in a cell?** Because the more levels we distinguish, the more bits we can store in the same physical cell, and thus the less each gigabyte costs.

- **SLC** (Single-Level Cell): 2 levels, **1 bit** per cell.
- **MLC** (Multi-Level Cell): 4 levels, **2 bits** per cell.
- **TLC** (Triple-Level Cell): 8 levels, **3 bits** per cell.
- **QLC** (Quad-Level Cell): 16 levels, **4 bits** per cell.
- **PLC** (Penta-Level Cell): 32 levels, 5 bits — announced for years, not yet a significant consumer product.

The density gain is obvious. The cost, less so. Imagine having to distinguish the water level in a glass: if you only need to say "empty or full" (SLC), you can be quite wrong and still be right. If you need to distinguish sixteen different levels (QLC) in the same glass, every extra drop or less counts, the measurement takes more time, and the slightest evaporation (i.e., charge loss due to wear or time) makes you slip into the wrong level.

There are three important consequences:

1. **Durability.** Each write/erase cycle slightly damages the cell's insulator. The more levels that need to be distinguished, the less degradation can be tolerated before the levels become ambiguous. Hence the order of magnitude of tolerable cycles: SLC ~100,000, MLC ~10,000, TLC ~1,000–3,000, QLC ~300–1,000.
2. **Write speed.** Writing requires bringing the charge to the exact level, with subsequent verifications. More levels mean slower cell programming. "Native" QLC writing (i.e., not cache-assisted) is dramatically slow: values in the order of **80–150 MB/s** are seen, which is worse than a hard drive.
3. **Price.** In the opposite direction: QLC is the cheapest per gigabyte, which is why it exists.

| Type | Bits/cell | Indicative P/E cycles | Native write speed | Typical use today |
|---|---|---|---|---|
| SLC | 1 | ~100,000 | Very high | Enterprise only, cache |
| MLC | 2 | ~10,000 | High | Practically disappeared from consumer |
| TLC | 3 | ~1,000–3,000 | Medium | **The consumer quality standard** |
| QLC | 4 | ~300–1,000 | Low | High-capacity budget SSDs |

The operational conclusion is clear: **for the system drive, buy TLC.** QLC has its place — a large 4 TB secondary drive intended to host a game library that is read a lot and written to little is a perfectly legitimate use of QLC, and in that scenario, the savings are real. But a QLC drive as the main unit of a PC that writes continuously (updates, browser caches, temporary files, compilations, video editing) is a choice that comes at a cost in both performance and longevity.

A technical note should be added for completeness: today, all consumer NAND is **3D NAND** (or V-NAND, in Samsung's terminology), meaning the cells are no longer arranged on a single plane but stacked in vertical towers of tens or hundreds of layers (it has gone from 32 layers to over 200–300 layers in recent generations). This has allowed for increased density without further shrinking the cells, which paradoxically has *improved* reliability compared to older planar NAND with tiny nodes. This is why modern TLC is more reliable than MLC from ten years ago.

### 5.2 DRAM cache versus DRAM-less and HMB

An SSD controller must maintain the **mapping table**: the table that associates each logical address (what the operating system sees) with its actual physical location in the NAND. This table is large: as a rule of thumb, about **1 MB of table for every GB of capacity**, so a 1 TB SSD has a mapping table of about 1 GB.

Where is it stored?

An SSD **with DRAM cache** mounts a dedicated DRAM chip (typically 1 GB for 1 TB) and stores the entire table there. Every time the controller needs to translate an address, it consults the DRAM: latency in the order of nanoseconds. Immediate.

A **DRAM-less** SSD does not have that chip. The table lives in the NAND itself and is consulted from there. Consulting the NAND to *find* where the data is, before reading the data, means making two accesses to the NAND instead of one: latency doubles, or worse.

To mitigate the problem, **HMB** (Host Memory Buffer) was introduced: an NVMe protocol feature that allows the SSD to **borrow a small portion of system RAM** (typically 32–64 MB, in some cases a few hundred) to store part of the mapping table. It works, and that's why the best modern DRAM-less SSDs (WD SN770, for example) are surprisingly good. But it has three limitations: (a) 64 MB is not 1 GB, so only the "hot" part of the table can be cached, and on a disk used in a disorganized way, misses increase; (b) access to system RAM still goes through the PCIe bus, so it's slower than onboard DRAM; (c) HMB requires operating system support — it's present in modern Windows and Linux, but in particular environments or old installations, it might not be, and in that case, the SSD falls back to very poor performance.

When does the difference become noticeable? Not in sequential benchmarks, where a DRAM-less drive can shine. It's noticeable in **workloads with many random requests on a full and fragmented disk**, which is exactly the condition of a system disk after two years of use. It's noticeable in latency under mixed load. It's noticeable in performance consistency.

**Practical rule: for the system disk, an SSD with DRAM cache is preferred. If the budget doesn't allow it, at least choose a reputable DRAM-less SSD with HMB (SN770, not some nameless newcomer), and in any case with TLC NAND.** The combination to avoid as a primary disk is **DRAM-less + QLC**: it's the bottom of the barrel, and it's exactly what many "bargain" 1 TB drives offer at rock-bottom prices.

### 5.3 Dynamic SLC cache and speed collapse

There's a phenomenon that baffles many users: they copy a 200 GB file to a new SSD, see 5,000 MB/s for the first thirty seconds, then suddenly the speed **drops** to 900 MB/s, or 200, or 90. Is the SSD faulty? No. It's working exactly as designed.

The mechanism is **dynamic SLC cache** (*pseudo-SLC cache*, or SLC caching). The controller takes a portion of the TLC or QLC NAND and makes it operate **in SLC mode**, meaning it writes only one bit per cell instead of three or four. In SLC mode, writing is very fast because there's no need to precisely position the charge among eight or sixteen levels, but only to distinguish between "charged" and "not charged." All incoming writes are therefore dumped into this fast area. Then, during idle moments, the controller **reprocesses** the data in the background, rewriting it in native TLC/QLC mode and freeing up the cache.

The problem is that the SLC cache is **dynamic**: its size depends on the free space. An empty 2 TB SSD can have an SLC cache of 200–600 GB; the same SSD 85% full might only have 20–30 GB, or almost nothing. And when the cache runs out, it writes **directly** to the native NAND, at its real speed. For a decent TLC, this means 900–1,500 MB/s (still good), but for a QLC, it means **80–150 MB/s**: worse than a hard drive, for hours, with the system seemingly frozen.

From this, three operational lessons follow:

1. **Sustained write tests matter more than peak numbers.** Serious reviews show a graph of write speed over a transfer of hundreds of gigabytes: you immediately see where the floor gives way and at what height.
2. **Don't fill the SSD.** We'll discuss this in the next section, but this is where the most dramatic effect is seen.
3. **QLC should be avoided for heavy and continuous write loads.** A user who downloads, edits videos, moves large archives, with an almost full QLC will have a terrible experience.

### 5.4 TBW and MTBF: how to interpret declared endurance

Two acronyms appear in specifications and are regularly misunderstood.

**TBW** (Terabytes Written) is the total amount of data that the manufacturer guarantees can be written to the drive within the warranty period. A mid-range 1 TB SSD typically declares 600 TBW; a high-end one 1,200 TBW; a cheap QLC might stop at 200–400 TBW. TBW increases proportionally with capacity, because the more cells there are, the more writes can be distributed.

The question everyone asks is: **is it a lot or a little?** Let's do the math. A normal home user writes between 10 and 30 GB per day to the system drive (updates, cache, documents, downloads). Let's take the worst case, 30 GB per day: that's about 11 TB per year. With 600 TBW declared, the drive would last **over fifty years**. Even a developer who compiles continuously, or a videographer who writes 200 GB per day, remains in the order of **eight to ten years**.

The conclusion, which must be stated clearly because it contrasts with widespread anxiety, is that **for the consumer user, TBW is almost never the limiting factor**. A modern SSD, in the vast majority of cases, becomes obsolete or is replaced long before its cells wear out. The real exceptions are enterprise workloads (databases with continuous writes, server caches, storage nodes) and some extreme professional workloads. If you fall into those categories, TBW is the first number to look at — and you probably need an enterprise unit, not a consumer one.

It should be remembered that TBW also has a **contractual** role: the warranty expires upon reaching the TBW *or* the declared number of years, whichever comes first. It is a legal limit even before it is a physical one: once the TBW is exceeded, the unit usually continues to function normally.

A related indicator is **DWPD** (Drive Writes Per Day), more commonly used in enterprise settings: it states how many times per day the entire capacity of the disk can be rewritten for the entire duration of the warranty. A consumer SSD is around 0.3 DWPD; a "write intensive" enterprise one can reach 3 or 10 DWPD.

**MTBF** (Mean Time Between Failures) is a number that should be interpreted with great caution, as it misleads almost everyone. An SSD typically declares 1,500,000 hours of MTBF. Divided by 8,760 hours in a year, that's **171 years**. No one seriously believes that an SSD will last 171 years, and indeed, that's not what the number means.

MTBF is a **statistical population measure**, not one of individual longevity. Roughly speaking, it means: if I put 1,500,000 units into operation for one hour each, I expect one failure. Or: if I put 1,000 into operation for one year, I expect about 5–6 failures (8,760,000 unit-hours / 1,500,000 ≈ 5.8). This data is useful for those who manage fleets of thousands of disks to dimension spare parts. It **says absolutely nothing** about how long *your* disk will last, and it does not account for aging wear, because it is measured during the product's "useful" life phase, excluding infant mortality and final wear.

The advice: look at the TBW and the **warranty years** (5 years is the standard for good quality; 3 years indicates an entry-level product). Ignore MTBF as a purchasing criterion.

One last thing, which is the most important of this entire section: **no durability number is a substitute for backup**. SSDs, unlike hard drives, tend to fail **suddenly and totally**, often due to a controller or firmware issue, without the noises and progressive bad sectors that gave warning on HDDs. A dying SSD often becomes an unreadable brick, and professional data recovery from NAND is enormously more difficult and expensive than from a magnetic platter. The **3-2-1 rule** (three copies of data, on two different media, one of which is off-site) is not a recommendation for paranoids: it is the bare minimum for anyone who cares about their data.

---

## 6. Capacity and configuration strategy

### 6.1 How much capacity

The question "how much space do I need?" has an answer that has changed rapidly in recent years, primarily for one reason: **game sizes have exploded**. Modern AAA titles regularly occupy 100–150 GB, and some exceed 200 GB with high-resolution texture packs. Just one of these games consumes a tenth of a 1 TB drive.

The absolute minimum acceptable for a system drive today is **500 GB**, and it must be said that this is already tight: Windows 11 with its paging files, hibernation, and updates occupies 60–80 GB alone, and common programs consume another hundred. With 500 GB, one can manage, if disciplined and having a second drive for data.

The **sensible reference capacity in 2026 is 1 TB** for a general-purpose PC, and **2 TB** for a gaming or creative work PC. Below 1 TB, continuous compromises are made; above 2 TB, the cost increases, and it's worth evaluating whether it's better to add a second drive (even slower and cheaper) rather than doubling the capacity of the primary one.

There's also a little-known technical reason why **larger capacities are often faster**: a 2 TB SSD has more NAND chips than a 500 GB one, and the controller can therefore distribute operations across more channels in parallel. It's typical for the 250 GB or 500 GB model in a line to have significantly lower declared write speeds than the 1 or 2 TB model in the exact same line. Buying the smallest size in a series often means buying the worst version in terms of performance, not just space.

### 6.2 The Fill Percentage

This is one of the most useful and least known facts in the entire chapter: **a full SSD is a slow SSD**, and not by a small margin.

There are three competing reasons.

The first is the **dynamic SLC cache** mentioned above: less free space means less fast cache, and the drive falls back to the native NAND speed sooner.

The second is **garbage collection**. The controller needs free blocks to consolidate data and delete obsolete ones. If there is very little free space, it has to perform acrobatics: read a nearly full block, copy valid pages elsewhere, erase, rewrite. The number of physical writes for each logical write — a parameter called **write amplification** — skyrockets. This means not only slowness but also **accelerated wear**: a drive kept constantly full wears out faster.

The third is **over-provisioning**. Every SSD reserves a portion of NAND from the factory that is not visible to the user (this is why a "1 TB" SSD shows 931 GB: partly it's the difference between decimal TB and binary TiB, partly it's reserved space). This margin is precisely for garbage collection and replacing defective blocks. Leaving free space is equivalent to increasing over-provisioning, and it improves everything.

**The practical rule: do not exceed 80% fill, and try to stay below 75% on the system drive.** A 1 TB SSD should practically be considered a 750 GB usable SSD. This is, incidentally, an excellent argument for buying the next size up: 2 TB used at 80% offers a comfortable 1.6 TB, with intact performance.

### 6.3 Sensible Configurations

Let's look at storage architectures that make sense, by use profile.

**Minimum configuration (office PC, browsing, study).** A single 1 TB NVMe, TLC, with or without DRAM. No second drive. Simple, quiet, efficient. If the budget is truly minimal, even a 1 TB SATA is better than a 256 GB NVMe: **space matters more than marginal speed**.

**Standard configuration (gaming PC, general use).** A 2 TB NVMe, TLC with DRAM, as the sole drive. This is the most elegant solution: nothing to manage, nothing to move, excellent performance everywhere. It's what I would recommend by default to most people.

**Two-tier configuration (gaming with large library, creator).** A 1 TB TLC NVMe with DRAM for the operating system and work applications, plus a second 2–4 TB NVMe (here QLC becomes acceptable, and sometimes it's the rational choice) for the game library and data. The advantage is that heavy and continuous system writes are on the good drive, while the large drive serves predominantly read loads, where QLC performs well.

**Full configuration (professional, local archive).** 1–2 TB NVMe for OS and applications, 2–4 TB NVMe or SATA for active projects, 8–20 TB HDD for archive and local backups. This is the classic three-tier configuration — hot, warm, cold — and it still makes perfect sense. It must be accompanied by an **off-machine backup**: an external drive or a NAS, because an internal HDD does not protect against theft, fire, power surge, or ransomware.

A note on **RAID** (Redundant Array of Independent Disks) in a home environment: RAID 0 (striping, two disks working in parallel) is almost always a bad idea on modern SSDs — it doubles the risk of total data loss for sequential bandwidth that no one uses — and RAID 1 (mirroring) is a high-availability mechanism, **not a backup**: if you accidentally delete a file, or if ransomware encrypts it, it is deleted or encrypted on both disks instantly. RAID protects against hardware failure of a disk, and nothing else.

---

## 7. Reference Brands and Models

### 7.1 Who produces NAND and who assembles

This distinction is key to navigating an otherwise chaotic market, and it's the same logic already encountered in the RAM chapter.

There are **very few NAND memory manufacturers** in the world: Samsung, SK Hynix (which absorbed Intel's NAND division to create **Solidigm**), Kioxia (Toshiba's former memory division), Western Digital/SanDisk (historically in a production joint venture with Kioxia), Micron (consumer brand Crucial), and YMTC in China. All other brands you see on shelves — Kingston, Corsair, Sabrent, Teamgroup, Adata, Silicon Power, Lexar, Patriot, PNY, and dozens of others — **do not produce NAND**. They buy memory chips and controllers on the market, assemble them on a PCB, and put firmware and a label on them.

This is not a defect in itself. Some assemblers make excellent products (the Kingston KC3000 and Sabrent Rockets have been excellent SSDs, built on high-end Phison controllers and good NAND). But it has a precise and dangerous consequence: **the assembler can change the internal components without changing the product name.**

It's the phenomenon known as **component lottery** or *silent revision*: an SSD is launched with TLC NAND and controller X, gets excellent reviews, and six months later the exact same model number is produced with QLC NAND and a different controller, with much worse sustained performance. The buyer reads the enthusiastic launch reviews and receives a different product at home. The most cited case in the consumer sphere is that of the **Kingston NV1/NV2**, openly declared as subject to component variation, with units found on the market in radically different configurations; but the phenomenon has affected, to varying degrees, many brands, including the famous case of the silent revision of the Samsung 970 EVO Plus and the variations on the WD Blue SN550.

The defense is twofold. First: **prefer, at the same price, brands that produce their own NAND** (Samsung, WD/SanDisk, Solidigm, Micron/Crucial as long as available, Kioxia), because the supply chain is vertical and silent revisions are rarer and more documented. Second: **be wary of models whose specifications do not state the type of NAND**. If a datasheet doesn't say whether it's TLC or QLC, whether it has DRAM or not, it's almost always because you wouldn't like the answer, or because the manufacturer wants to keep its options open.

### 7.2 Overview of SSD Brands

**Samsung.** It is the historical reference, for a simple reason: it produces NAND, controllers, and DRAM in-house. The **970 EVO/Pro** (PCIe 3.0) series were the gold standard for years; the **980** series introduced an annoying ambiguity (the plain "980" is a Gen3 **DRAM-less**, while the "980 Pro" is a Gen4 with DRAM: almost identical names, products in different categories — a textbook case of misleading nomenclature). The **990 PRO** series is today a reference Gen4, fast, efficient, with excellent consistency under load; the **990 EVO Plus** variant is cheaper and uses a hybrid configuration of PCIe lanes. Samsung tends to cost more than average: you pay for the brand, but also for real consistency. Historical note: the first 990 PRO and 980 PRO units had health degradation issues resolved via firmware update — a reminder that **updating SSD firmware** (with official tools: Samsung Magician, WD Dashboard, Crucial Storage Executive) is a good habit, not an operation for power users.

**Western Digital / SanDisk.** The other big vertical name. The **WD Black SN850X** is probably the most recommended Gen4 SSD with DRAM: excellent performance, great thermal management, competitive price, and in many configurations it is the rational choice for a system drive. The **WD Black SN770** is the champion of the DRAM-less category: it uses HMB intelligently, has TLC NAND, and is surprisingly close to SSDs with DRAM in real-world use, at a lower price. It is the exception that proves that "DRAM-less" does not automatically mean "poor quality," as long as the rest is done well. The **SN850X** also comes in a heatsink version and a PS5-compatible version. The **WD Blue** line (SN580 and similar) is the honest entry-level. The group recently separated its flash activities under the **SanDisk** brand, so one can expect to see the same products under transitioning names *[variable data]*.

**Crucial (Micron).** Historically the champion of price-to-performance. The **P3** and **P3 Plus** are inexpensive QLC drives (good as secondary drives, not recommended as primary); the **P5 Plus** was an excellent Gen4 with DRAM; the **T500** is an excellent and very competitive Gen4 with DRAM; the **T700** and **T705** were among the first top-tier Gen5 drives, very fast and very hot, requiring substantial heatsinks. **[volatile data]** As mentioned at the beginning, Micron has announced its exit from the consumer market with the Crucial brand: these products remain technically valid but should no longer be considered a line with a future, and availability must be checked on a case-by-case basis. Anyone buying residual Crucial stock today gets a technical bargain but must account for less guaranteed support and replacement over time.

**Kingston.** An assembler, not a NAND manufacturer. The **KC3000** was an excellent Gen4 (Phison E18 controller, TLC NAND, DRAM) and the **Fury Renegade** is its gaming counterpart. At the opposite extreme, the **NV2** series (and previously the NV1) is the textbook example of the component lottery: same name, variable components, unpredictable performance. It's not an "bad" product in absolute terms, but it's a product where **you cannot know what you are buying**, which in 2026 is a sufficient reason to stay away if alternatives at the same price exist.

**Solidigm.** This is Intel's former NAND division, now under SK Hynix. It produces its own NAND. It has become particularly interesting in the niche of **very high-capacity, low-cost SSDs per TB**: the **P44 Pro** series is an excellent Gen4 with DRAM (technically a close relative of the SK Hynix Platinum P41), while the high-capacity QLC series (P41 Plus, and lines from 4 TB upwards) are among the most sensible when a lot of space is needed at a low price for predominantly read workloads.

**Seagate.** Known primarily for hard drives, it has a good line of **FireCuda** SSDs (530, 540), based on Phison controllers and TLC NAND, with very high declared TBW and a long warranty. These are valid products, often positioned at premium prices.

**Sabrent.** An American assembler that has built a solid reputation with its **Rocket** line (Rocket 4 Plus, Rocket 5), based on top-tier Phison controllers. It is often the first to market with new generations. Good products, but the rule always applies: check the revision and actual components.

**SK Hynix.** Less common in Europe but technically excellent: the **Platinum P41** and the subsequent **P51** are drives with proprietary NAND and controllers, among the most efficient on the market (low power consumption, low heat, excellent performance). If found at a good price, it is a very solid choice.

### 7.3 SSD Orientation Table (indicative)

| Tier | Reference Models | Features | Recommended Use |
|---|---|---|---|
| Entry / Second Drive | Crucial P3 Plus, WD Blue SN580, Solidigm P41 Plus | Gen3/Gen4, DRAM-less, often QLC | Secondary storage, game libraries |
| Quality Entry | **WD Black SN770**, Kingston NV3 (with reservations) | Gen4, DRAM-less HMB, TLC | System drive on a tight budget |
| Mid-range (the sweet spot) | **WD Black SN850X**, Samsung 990 EVO Plus, Crucial T500, SK Hynix P41/Solidigm P44 Pro | Gen4, DRAM, TLC | **System drive: the recommended choice** |
| High-end Gen4 | Samsung 990 PRO, Seagate FireCuda 530/540 | Max Gen4, DRAM, TLC | Workstations, for those who want the best without Gen5 |
| Gen5 | Crucial T705, Samsung 9100 PRO, Sabrent Rocket 5, Corsair MP700 Pro | Gen5, DRAM, TLC, mandatory heatsink | Only for massive professional transfer workloads |

*Specific models and their availability change rapidly; this table should be used to understand categories, not as a definitive shopping list.* **[volatile data]**

### 7.4 Hard Drives: Seagate, WD, Toshiba, and the SMR Trap

In the hard drive market, there are **only three manufacturers** left in the world: **Seagate**, **Western Digital** (with WD and HGST brands), and **Toshiba**. Everything you buy, under any label, comes from them.

WD historically uses a color code for its lines, which is useful to know:

| WD Line | Color | Purpose |
|---|---|---|
| WD Blue | Blue | General desktop, light use |
| WD Black | Black | Performance desktop, 7,200 RPM, large cache |
| WD Red / Red Plus / Red Pro | Red | NAS (caution: the plain "Red" is SMR!) |
| WD Purple | Purple | Video surveillance, continuous writing |
| WD Gold | Gold | Enterprise, datacenter |

Seagate has a similar structure: **BarraCuda** (desktop), **IronWolf** and **IronWolf Pro** (NAS), **SkyHawk** (surveillance), **Exos** (enterprise). Toshiba uses alphanumeric designations (**N300** series for NAS, **X300** for performance desktop, **MG** for enterprise).

And now, the most important trap in this section.

**CMR versus SMR.** These are two ways of writing magnetic tracks on the platter.

**CMR** (Conventional Magnetic Recording — sometimes called PMR) writes tracks side-by-side and not overlapping. Each track can be rewritten independently of the others. This is the "normal" behavior expected from a drive.

**SMR** (Shingled Magnetic Recording) exploits a physical asymmetry: the **write** head is wider than the **read** head. If tracks are written partially overlapping, like roof shingles, more can be packed onto the same platter, gaining 10–25% capacity with the same hardware. Reading works perfectly, because the narrower read head can read the uncovered portion of the track.

The problem is **rewriting**. Since tracks overlap, a single track cannot be rewritten without destroying adjacent ones: the entire "band" of shingled tracks must be read, modified in memory, and then rewritten entirely. The drive masks this phenomenon with a CMR cache zone, but when the cache runs out — i.e., under prolonged random writing — performance **collapses**, and not by a little: it drops to **10–20 MB/s**, with latencies of seconds. The drive appears broken.

Where does it become a disaster? In a **NAS with RAID**. When a failed drive is replaced, the array must **rebuild** the data onto the new drive: this is a heavy, continuous write load, lasting hours or days. An SMR drive, under these conditions, slows down so much that the RAID controller can consider it unresponsive and **eject it from the array**, ruining the rebuild. In an already degraded array, with only one redundancy drive remaining, this can mean **total data loss**.

What makes the situation even more serious is that, in 2020, WD, Seagate, and Toshiba were discovered selling SMR drives **without declaring it**, including them in lines explicitly intended for NAS (particularly some 2–6 TB WD Red drives). This led to class actions, public apologies, and a clarification of product lines: since then, WD has created "Red **Plus**" to indicate CMR, leaving the plain "Red" for SMR. But the lesson remains: **labels are not enough; the specific model must be verified**.

**Operational rule: for a NAS, for a RAID, or for any use involving significant writes, only CMR should be purchased.** Before buying a hard drive, search for the exact model number (e.g., WD60EFPX, ST8000VN004) along with the word "CMR" or "SMR": the three manufacturers now publish official lists, and enthusiast communities maintain updated ones. SMR only makes sense for write-once, read-many archival storage, typically in external USB drives intended for backing up large archives that are rarely rewritten.

One last note: some ultra-high capacity enterprise hard drives are **helium-filled** instead of air-filled. Helium, being less dense, reduces turbulence around the platters, allowing more platters to be stacked (9, 10, 11) and reducing power consumption and temperatures. These are excellent, hermetically sealed drives, but expensive and often noisy (7,200 RPM enterprise models are not designed to sit under a living room desk).

### 7.5 The selection process, in order

Summarizing the method for choosing storage in a logical sequence, proceed as follows:

1.  **Usage.** What should this drive do? Operating system? Game library? Archive? Backup? Everything else follows from this.
2.  **Interface.** Operating system and applications → NVMe, always. Secondary data storage → NVMe if there are free slots, SATA if not. Mass archive → HDD.
3.  **Capacity.** Estimate actual occupancy, add margin for the future, and **divide by 0.8** to adhere to the fill rule. Need 1 TB of stuff? Buy 1.5–2 TB.
4.  **NAND and DRAM Type.** System drive → **TLC**, preferably **with DRAM**. Secondary drive predominantly for reading → QLC acceptable.
5.  **TBW and Warranty.** Verify that the TBW is consistent with the expected load (for consumer use, it almost always is) and prefer 5-year warranties.
6.  **Brand.** Given identical specifications, prefer manufacturers who produce their own NAND and do not make silent revisions.
7.  **Price.** Only at this point look at the price, and compare the **cost per gigabyte** among the remaining candidates. Price is the last filter, not the first, because a cheap drive that fails on points 4 and 6 is not a bargain: it's a postponed problem.

---

## 8. Common buyer mistakes

Here, we explicitly list the most common errors seen — many already anticipated, but worth listing because each costs money, time, or data.

**Buying a QLC DRAM-less SSD as a primary drive.** This is the number one mistake, and it's marketing-driven: the box says "PCIe 4.0, up to 5,000 MB/s," the price is great, it seems like a bargain. Then you discover that those 5,000 MB/s only last as long as the SLC cache holds out, and that when the drive fills up — which is after a year of normal use — write speeds drop below 200 MB/s and the system becomes sluggish under load. **The system drive is the last place to save 20 euros.**

**Mounting the NVMe in the wrong slot and disabling SATA ports.** Already thoroughly discussed: the motherboard manual has the lane sharing table, and it should be read **before** buying, not after the drives have disappeared from the BIOS.

**Paying for a Gen5 SSD without needing it.** A Gen5 costs significantly more than a Gen4 of the same capacity, runs much hotter, requires a bulky heatsink, and in consumer use — including gaming — offers a perceived improvement ranging from "none" to "imperceptible." That price difference, spent instead on **capacity** (2 TB Gen4 instead of 1 TB Gen5) yields a real and daily benefit. Gen5 makes sense for those who habitually move tens or hundreds of gigabytes between fast drives, and for few others.

**Buying an M.2 SATA believing you're buying an NVMe.** Same slot, different protocol, one-tenth the performance. Check that "NVMe" and "PCIe" appear in the technical specifications, and be wary of drives with two notches.

**Getting an SMR HDD for a NAS.** Concrete risk of total data loss during a RAID rebuild. Verify the exact model against official CMR/SMR lists.

**Buying the smallest size in a series.** The 250 or 500 GB model of a line is often slower in writing than the 1 or 2 TB model of the same line, because it has fewer chips to parallelize across. You pay little and get little, in two senses.

**Filling the drive to 95% and then complaining that the PC is slow.** The remedy is free: free up space, or buy more capacity.

**Defragmenting an SSD.** Useless and harmful. Modern Windows knows this and, when it "optimizes" an SSD, it actually sends TRIM commands — which is a completely different and legitimate thing. Do not install third-party defragmentation utilities on an SSD.

**Believing that RAID 1 is a backup.** It is not. It protects against a single drive failure. It does not protect against accidental deletions, ransomware, power surges, theft, or fire.

**Ignoring firmware updates.** Several serious reliability issues on branded SSDs have been resolved by firmware updates. It's worth installing the manufacturer's official utility and checking once in a while.

**Never checking S.M.A.R.T. status.** **S.M.A.R.T.** (Self-Monitoring, Analysis and Reporting Technology) is the self-diagnosis system integrated into all drives: it exposes counters such as power-on hours, terabytes written, reallocated sectors, and remaining life percentage. Free tools like CrystalDiskInfo (Windows) or `smartctl` (Linux) allow you to read them in seconds. A quick look now and then, and especially **before buying used**, avoids unpleasant surprises: a used SSD with 80% of its life consumed is not a bargain at any price.

**Trusting a single storage medium.** It's worth repeating one last time: SSDs die without warning, and recovery is expensive, if not impossible. Any storage configuration without a backup strategy is, simply put, incomplete.

---

## 9. Operational Summary — Storage Decision Checklist

**Step 1 — Define the role of each drive.**
- [ ] Operating system and applications → **NVMe SSD**, no exceptions.
- [ ] Games and active projects → NVMe (or SATA if no slots available).
- [ ] Archive and local backup → **High-capacity CMR HDD**.
- [ ] Remote/external backup → planned? (3-2-1 rule)

**Step 2 — Check motherboard compatibility.**
- [ ] How many M.2 slots are there and what PCIe generation do they support?
- [ ] Open the manual, read the **lane sharing** table: which M.2 slot disables which SATA ports or reduces the GPU slot?
- [ ] Is the chosen slot for the system drive **directly connected to the CPU**?
- [ ] Does the motherboard already include an **M.2 heatsink**? (If yes, do not buy an SSD with an integrated heatsink.)
- [ ] Is the format **2280**? (Check mounting holes if using different formats.)

**Step 3 — Choose SSD specifications.**
- [ ] Is it **NVMe** and not M.2 SATA? (Look for "NVMe" and "PCIe" in the technical specifications; be wary of drives with two notches.)
- [ ] Is the NAND **TLC**? (QLC only allowed for secondary drives with predominantly read operations.)
- [ ] Does it have **DRAM cache**? (If DRAM-less, is it a reputable model with HMB like the SN770?)
- [ ] Is the **TBW** consistent with the expected workload? (For consumer use, almost always yes.)
- [ ] Is the **warranty** 5 years?
- [ ] Are there **sustained write** tests in reviews? Where does the speed drop?
- [ ] Is the PCIe generation adequate: **Gen4 is the sweet spot**; Gen5 only if the workload truly justifies it.

**Step 4 — Size the capacity.**
- [ ] Estimate actual expected usage.
- [ ] Add margin and divide by **0.8** (do not exceed 80% full).
- [ ] Minimum 1 TB for general use, **2 TB recommended** for gaming or creative work.
- [ ] Verify that the chosen capacity is not a depowered version of the series.

**Step 5 — Choose the brand.**
- [ ] Given similar specifications, prefer a vertical NAND manufacturer (Samsung, WD/SanDisk, SK Hynix/Solidigm, Kioxia, Micron).
- [ ] Verify that the model is not known for **silent revisions** of components.
- [ ] Check that the technical specifications explicitly state NAND type and presence of DRAM.

**Step 6 — If buying an HDD.**
- [ ] Verify it is **CMR** (mandatory for NAS and RAID), checking the exact model number.
- [ ] Choose the right line for the use (NAS / surveillance / desktop / enterprise).
- [ ] Consider noise and vibrations if the PC is in a quiet environment.

**Step 7 — After installation.**
- [ ] Are all drives visible in the BIOS/UEFI? (If not → suspect lane sharing.)
- [ ] Is the NVMe drive recognized at the correct PCIe generation? (Verifiable with CrystalDiskInfo or manufacturer utilities.)
- [ ] Is **TRIM** active? (On modern Windows and Linux, it is by default.)
- [ ] Firmware updated with the official utility?
- [ ] Temperatures under load checked? (Above 70 °C sustained on the controller, better dissipation is needed.)
- [ ] **Backup configured.** No storage configuration is complete without this point.

**Quick tips by budget tier** *(indicative and currently very unstable prices — [volatile data])*

| Tier | Recommended configuration | Logic |
|---|---|---|
| **Entry** | 1× 1TB Gen4 TLC NVMe, even quality DRAM-less (e.g., WD Black SN770) | A single, good drive. Better a decent 1TB than a "premium" 500GB or a poor 2TB. |
| **Mid** | 1× 2TB Gen4 TLC NVMe with DRAM (e.g., WD Black SN850X, Samsung 990 EVO Plus, Crucial T500) | **The right choice for most people.** Simple, fast, capacious. |
| **High-end** | 1× 2TB Gen4/Gen5 NVMe with DRAM for OS + 1× 4TB NVMe (even QLC) for libraries + 1× 8–20TB CMR HDD for archive | Three tiers: hot, warm, cold. Gen5 only if the workload justifies it. |

---

**Final note.** If I had to summarize the entire chapter in one sentence, it would be this: *the quality of an SSD is measured by TLC, DRAM, and sustained write performance, not by the big number printed on the box.* Those who internalize this principle will buy well even without knowing anything else; those who ignore it will continue to pay for sequential bandwidth they will never use, and wonder why their new computer seems bogged down after a year.

---

[← Previous](04-consumer-gpu.html) · [All chapters](./) · [Next →](06-power-supply.html)
