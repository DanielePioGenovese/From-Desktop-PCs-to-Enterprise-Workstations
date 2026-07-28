---
title: "Chapter 2 - The Motherboard"
parent: "English Edition"
nav_order: 2
---

<details open markdown="block">
  <summary>Chapter contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

> **Note on data updates.** The architectural information in this chapter (chipset function, socket hierarchy, VRM principles, form factor, PCIe and USB standards) is stable and changes slowly. **Prices**, however, are extremely volatile and vary by country, currency, availability, and promotions: any figure indicated here should be understood as an **indicative order of magnitude** and should be verified at the time of purchase. The list of the **latest generations** (in particular AMD's 800 series chipsets and the Intel LGA1851 platform) is also what was available at the time of writing: when you encounter a point where data may have already changed, you will find it explicitly marked with the phrase **[VERIFY]**.

---

## 1. What the motherboard does

### 1.1 Definition and role as a hub

The **motherboard** (often abbreviated as *mobo*) is the large printed circuit board that forms the backbone of every computer. If we were to use a metaphor, it is not the "brain" (that's the CPU) nor the "muscles" (the GPU): it is rather the **nervous and circulatory system combined**, i.e., the physical and electrical infrastructure on which all other components are mounted and through which they communicate with each other and receive power.

Before going into detail, it is useful to clarify an acronym that will appear frequently: **PCB** (*Printed Circuit Board*) is the rigid sheet, usually green or black, composed of multiple layers of fiberglass alternated with very thin copper traces. These traces are the microscopic "wires" that carry signals and current from one end of the board to the other. A quality motherboard uses more copper layers (often 4 to 8, sometimes more in premium models) and a greater copper thickness, which improves the integrity of high-frequency signals and the ability to carry current without overheating.

The role of the motherboard is that of a **central hub**: it connects the CPU, RAM, graphics card (GPU), other expansion cards, storage devices (SSDs and hard drives), power supply unit (PSU), and all external peripherals (keyboard, mouse, monitor, network, audio). None of these components "speak" directly to each other: everything passes through the motherboard, which acts as a switchboard and a road network. The quality and characteristics of the motherboard therefore determine not so much the raw power of the system (which is provided by the CPU and GPU), but rather **how much and what you can connect, at what speed, with what stability, and with what possibilities for future expansion**.

### 1.2 Main Components on the PCB

Looking at a motherboard from above, several distinct areas can be recognized. We will describe them here as an introduction, and then delve deeper into them in dedicated sections.

The **CPU socket** is the receptacle, generally in the upper-center of the board, where the processor is installed. It is a rectangle full of tiny contacts; its shape, number of pins, and fastening mechanism determine which CPUs are compatible. The socket is the absolute first compatibility constraint: a CPU designed for one socket will not physically fit into a different socket.

The **RAM slots** (also called *DIMM slots*, where **DIMM** stands for *Dual In-line Memory Module*, the format of memory modules for desktops) are the long vertical slots next to the socket, typically two or four. Memory modules are inserted here.

The **PCIe slots** (*Peripheral Component Interconnect Express*) are the horizontal slots in the lower part of the board. This is where the graphics card and other expansion cards (sound cards, network cards, capture cards, additional controllers) are installed. They come in different lengths depending on the number of available electrical "lanes".

The **chipset** is a small microchip, often hidden under a metal heatsink in the lower half of the board. It is the "co-processor" that manages communications with peripherals and provides additional connectivity. It is so important that we will dedicate the longest section of the chapter to it.

The **VRM** (*Voltage Regulator Module*) is the row of electronic components, often covered by a robust heatsink, that surrounds the CPU socket on one or two sides. Its task is to transform the power supplied by the PSU into a clean and stable voltage suitable for the CPU.

The **M.2 slots** are small horizontal slots, usually covered by metal plates, where modern "gum stick" format SSDs are installed. The **SATA ports** (*Serial ATA*) are the "L"-shaped connectors for connecting traditional 2.5 or 3.5-inch hard drives and SSDs.

Finally, there are the **power connectors** (the large 24-pin connector for the motherboard and one or two 8-pin connectors, called *EPS*, for the CPU), the **rear I/O panel** (the set of ports that protrude from the back of the case), and a multitude of **internal headers**, which are small pin headers where fans, RGB lighting, front USB ports of the case, and power buttons are connected. All these elements will be revisited in detail later.

---

## 2. The Chipset

### 2.1 What the Chipset Is and What It Manages

The **chipset** is, historically, the set of logic circuits that govern the flow of data between the CPU and the rest of the system. In computers from twenty to thirty years ago, the chipset was divided into two distinct physical parts: a **Northbridge**, close to the CPU, which managed fast connections (memory and graphics card), and a **Southbridge**, further away, which managed slow connections (disks, USB, audio, network). With technological evolution, the Northbridge has been progressively **integrated into the CPU itself**: today, the memory controller (**IMC**, *Integrated Memory Controller*, the circuit that drives RAM) and the controller for the main PCIe lanes for the graphics card live directly in the processor. What we call "chipset" in modern motherboards is, therefore, essentially the heir to the old Southbridge: a single chip dedicated to secondary connectivity.

What does the chipset of a current motherboard actually manage? Fundamentally, it **provides additional connectivity lanes** compared to those the CPU offers alone. The CPU, for cost and physical space reasons, exposes a limited number of PCIe lanes (typically 16 lanes reserved for the graphics card plus 4 or 8 lanes for one or two NVMe SSDs) and few other resources. Everything else — the numerous USB ports, SATA ports for disks, the network card, PCIe lanes for secondary slots, often the audio chip and Wi-Fi — is managed or enabled by the chipset. In practice, the chipset is a **connectivity multiplier and distributor**: it takes a single fast connection from the CPU and "expands" it into dozens of ports and slots.

This means that the choice of chipset directly determines **how many peripherals you can connect and with what advanced capabilities** (overclocking, USB4, PCIe 5.0), while it **does not** determine the system's computing power. Two boards with different chipsets but the same CPU and RAM offer, with the same configuration, almost identical performance in normal workloads: the difference lies in features and expandability, not in "frames per second."

### 2.2 Communication between CPU and chipset

The chipset must constantly communicate with the CPU, and this communication occurs over a dedicated high-speed link. It is important to understand this because that link is a **potential bottleneck**: all traffic from peripherals managed by the chipset (disks, USB, secondary slots) must pass through it to reach the CPU.

In **Intel**, this link is called **DMI** (*Direct Media Interface*). DMI is, technically, a bus derived from PCIe technology. In recent platforms, DMI 4.0 is used with a variable number of lanes depending on the chipset's tier: entry-level chipsets use a narrower DMI (for example, the equivalent of 4 PCIe 4.0 lanes), while high-end chipsets use a wider DMI (the equivalent of 8 PCIe 4.0 lanes), which doubles the available bandwidth between the chipset and the CPU. This explains why a high-end chipset not only offers more ports but also allows them to work together without saturation.

In **AMD** systems, on the AM5 platform, the connection between the CPU and the chipset is simply a **4-lane PCIe 4.0 link**. An important architectural detail concerns AMD's "dual" chipsets: in X670/X670E and X870E motherboards, AMD uses **two identical chips** (codename Promontory 21) connected **in cascade** (daisy chain), meaning one attached to the other. The first chip communicates with the CPU, the second communicates with the first. This doubles the total number of available ports and lanes but introduces a small increase in latency for devices connected to the second chip, and still maintains that 4-lane link as the overall bottleneck to the CPU. This is the compromise AMD chose to offer extensive connectivity by reusing a single, high-volume manufactured chip.

### 2.3 The AMD chipset hierarchy (AM5 socket)

The **AM5** socket is AMD's current desktop platform, introduced with the Ryzen 7000 series processors and also used by subsequent series (8000 with integrated graphics and 9000). One of AMD's most appreciated features is **socket longevity**: AM5 has been declared supported for several years, which generally allows for future CPU upgrades while keeping the same motherboard (after a BIOS update, which we will discuss). All AM5 chipsets use **DDR5** memory (there is no DDR4 variant on AM5) and provide PCIe 5.0 from the CPU side.

Before reading the table, three fundamental interpretative keys. The letter **"E"** at the end of the name (as in B650**E** or X670**E**) stands for *Extreme* and indicates that **PCIe 5.0 lanes are guaranteed for both the graphics card and at least one SSD**; in versions without "E", PCIe 5.0 for the graphics card is often optional or absent, while for storage it may be present. The **800 series** (B850, X870, X870E), newer than the 600 series, is distinguished primarily by the wider availability of **USB4** (a very high-speed port, up to 40 Gbps, compatible with Thunderbolt): it is **mandatory on X870/X870E**, while on B850 it remains optional but common on most boards. Finally, on AM5, **CPU overclocking is enabled almost everywhere except on the entry-level A620 chipset**, and this is one of the most significant historical differences compared to Intel.

| AMD Chipset (AM5) | Tier | CPU Overclock | PCIe 5.0 GPU | PCIe 5.0 storage | USB4 | Notes |
|---|---|---|---|---|---|---|
| **A620** | Entry | No | No (GPU to PCIe 4.0) | No/limited | No | Fewer lanes and ports; ideal for budget builds. On some boards, limits on top CPU TDP. |
| **B650** | Mainstream | Yes | Optional (often PCIe 4.0) | Yes (at least 1 M.2) | Optional/rare | The "sensible" chipset for most users. |
| **B650E** | Mainstream+ | Yes | Yes (guaranteed) | Yes | Optional | Like B650 but with PCIe 5.0 also for the GPU. |
| **X670** | High (dual chip) | Yes | Optional | Yes | Optional | Many more USB/SATA ports and PCIe lanes. |
| **X670E** | High (dual chip) | Yes | Yes | Yes | Optional | Maximum connectivity for the 600 series, full PCIe 5.0. |
| **B850** | Mainstream+ (800 series) | Yes | Optional | Yes | Optional but frequent | Update of B650E; USB4 more widespread. **[VERIFY]** availability/price |
| **X870** | High (800 series) | Yes | Frequent/guaranteed on many boards | Yes | **Mandatory** | Single chip but with USB4 as standard. **[VERIFY]** |
| **X870E** | Top (800 series, dual chip) | Yes | Yes | Yes | **Mandatory** | The ultimate in AM5 expandability. **[VERIFY]** |

> **[VERIFY]** The exact PCIe 5.0 guarantees on the video card for X870 (non-E) vary from model to model: some boards implement it, others do not. If there is a strict requirement (for example, a PCIe 5.0 GPU or SSD to be fully utilized), always check the technical specifications of the **individual model**, not just the chipset name.

A recurring practical clarification: the difference between a B650 and an X670E **does not concern how fast your game will be**. It concerns how many NVMe SSDs you can install, how many fast USB ports you will have, whether you can use two video cards or expansion cards simultaneously at full bandwidth, and how much margin you will have for extreme overclocking. For a normal single-video-card gaming PC, a good B650/B650E is more than sufficient.

### 2.4 The Intel Chipset Hierarchy (LGA1700 and LGA1851)

Intel has recently undergone a socket change, so it's necessary to distinguish between two platforms.

The **LGA1700** socket hosts 12th, 13th, and 14th generation processors (code names Alder Lake, Raptor Lake, and Raptor Lake Refresh). It is a "mature" platform, widely diffused and often available at convenient prices. A historical peculiarity of LGA1700 is that, depending on the board, it can use either **DDR4 or DDR5** memory (never both on the same board): this makes it interesting for those who want to reuse existing DDR4 RAM and save money, with a small performance penalty compared to DDR5.

The **LGA1851** socket is the most recent platform, introduced with **Core Ultra 200S series** processors (code name Arrow Lake). It supports **only DDR5** and introduces a different organization of lanes (the CPU offers PCIe 5.0 for the video card and for storage, with improved native support for Thunderbolt/USB4). Pay attention to a clear constraint: **LGA1851 is not backward compatible with LGA1700 CPUs and vice versa**; the number (1700 vs 1851) precisely indicates the number of contacts on the socket.

Intel's golden rule on overclocking is diametrically opposed to AMD's: **only "Z" series chipsets (Z690, Z790, Z890) allow CPU overclocking**. All other Intel chipsets allow at most memory overclocking (via Intel's **XMP**, *Extreme Memory Profile*, profiles — the equivalent of AMD's EXPO), and on this point there has been an evolution: while historical entry-level chipsets like H610 do not even enable XMP, more recent mid-range chipsets (B760, B860) do.

| Intel Chipset | Platform | Tier | CPU OC | Memory OC (XMP) | DMI to CPU | Notes |
|---|---|---|---|---|---|---|
| **H610** | LGA1700 | Entry | No | No | x4 | Often 2 RAM slots; few USB/SATA; no PCIe 5.0 from chipset. |
| **B660** | LGA1700 | Mainstream | No | Yes | x4 | Good balance point 12th/13th gen. |
| **B760** | LGA1700 | Mainstream | No | Yes | x4 | B660 refresh, better connectivity out of the box. |
| **H670** | LGA1700 | Mid-range | No | Yes | x8 | More lanes than B660, no CPU OC. |
| **H770** | LGA1700 | Mid-range | No | Yes | x8 | H670 refresh, more fast USB. |
| **Z690** | LGA1700 | High-end | Yes | Yes | x8 | Full OC, maximum lanes (12th/13th gen). |
| **Z790** | LGA1700 | High-end | Yes | Yes | x8 | Z690 refresh, more USB 3.2 Gen2x2. |
| **B860** | LGA1851 | Mainstream | No | Yes | x8 **[VERIFY]** | Mainstream Arrow Lake; USB4/Thunderbolt more common. **[VERIFY]** |
| **Z890** | LGA1851 | Top | Yes | Yes | x8 | Flagship Arrow Lake: full OC, many PCIe 4.0 lanes from chipset, native Thunderbolt 4/USB4. |

> **[VERIFY]** The precise details of Intel's 800 series (B860, and the entry H810 not listed but existing) and the exact width of their DMI may differ from "traditional" values; check the specific model's datasheet, especially for the number of M.2 slots and USB4/Thunderbolt support.

### 2.5 Decision table: which chipset for which user

This table pragmatically summarizes the pairing between user type and recommended chipset. It is intentionally simplified: it serves as a guide, not a replacement for checking individual models.

| User Profile | Recommended AMD (AM5) | Recommended Intel |
|---|---|---|
| Office/multimedia, minimal budget, no OC | A620 | H610 / B660 |
| Entry-mid gaming, single GPU, 1–2 SSDs | B650 | B760 |
| Mid-high gaming/creator, overclocking, 2+ fast SSDs | B650E / B850 | Z790 / Z890 |
| Workstation, maximum connectivity, many drives, USB4 | X670E / X870E | Z890 |
| Those who want to reuse DDR4 RAM and spend little | *(not available: AM5 is DDR5 only)* | LGA1700 with DDR4 board |

### 2.6 Overclocking: the net summary

Summarizing the logic of overclocking, because it is one of the first choices that influences the chipset: on **AMD AM5, CPU overclocking is allowed on all chipsets except the A620**, so even an inexpensive B650 allows pushing an unlocked processor. On **Intel, CPU overclocking strictly requires a "Z" chipset** (Z690, Z790, Z890); with any other chipset, the CPU runs at its factory frequencies (including automatic turbo modes, which are not considered manual overclocking). **Memory** overclocking, however, is more permissive: almost all modern chipsets allow it via EXPO (AMD) or XMP (Intel), with the exception of the cheapest Intel chipsets like H610. Since on many platforms the biggest performance gain, for the same cost, comes precisely from activating the high-speed memory profile, checking that the chipset allows it is often more important than being able to overclock the CPU.

---

## 3. The VRM (power delivery section)

### 3.1 What is the VRM and why does it exist

The **VRM** (*Voltage Regulator Module*) is the circuit that converts the **12 volts** supplied by the power supply into the much lower and more precise voltage required by the CPU, typically between about **1.0 and 1.4 volts**. This may seem like a trivial task, but it is not at all: a modern high-performance CPU can draw **over 200-250 watts** at full load, and since power is the product of voltage and current, at low voltage this means enormous currents, on the order of **150-250 amperes**. The VRM must supply this current stably, cleanly, and reactively: if the CPU goes from idle to maximum load in a few microseconds, the VRM must follow that demand without causing the voltage to collapse or overload, otherwise the system becomes unstable (crashes, reboots) or the CPU reduces its frequency to protect itself (*throttling*).

### 3.2 Power Phases

To handle such high currents, the VRM is divided into multiple **phases**. A power phase is a small, complete circuit essentially composed of several elements. There are **MOSFETs** (*Metal-Oxide-Semiconductor Field-Effect Transistor*), electronic switches that turn on and off thousands of times per second; modern VRMs often use integrated components called **DrMOS** or *power stages*, which enclose MOSFETs and their driving logic in a single package, improving efficiency and temperature. Then there's the **choke** (or inductor), that small square block that stores energy and "smoothes" the current. There are **capacitors**, which stabilize the voltage by filtering out disturbances. And finally, there's a **PWM controller** (*Pulse Width Modulation*), the "orchestra conductor" that coordinates the staggered switching of the various phases.

The advantage of having multiple phases is twofold. First, the total current is **distributed**: if ten phases share 200 amperes, each handles only 20 amperes, remaining cool and reliable. Second, by having the phases work in a **staggered** manner over time, the resulting voltage is more continuous and cleaner. However, be aware of a common myth: **the number of phases is not in itself a measure of quality**. A motherboard advertised with "16 phases" but built with cheap components and inadequate dissipation can perform worse than one with "8 phases" but high-current components and a good heatsink. It should also be known that many motherboards use **doublers**, circuits that make one phase appear as two: the board claims "12+2 phases" but the controller actually drives half that number. This is not necessarily a defect, but it is why simply counting phases is an unreliable marketing metric.

### 3.3 How to recognize an adequate VRM

Since the nameplate data is ambiguous, the best way to evaluate a VRM is to **cross-reference three signals**. The first is the **presence and mass of the heatsinks**: a serious VRM is covered by robust aluminum blocks (sometimes connected by a *heatpipe*, a small tube that transfers heat) with a large surface area and fins; a "naked" VRM, with exposed chips, is almost always an indicator of an inexpensive board intended for low-power CPUs. The second signal is the **amperage rating of the individual phases**, a data point that technical reviewers often report (for example, "50A or 70A power stage"): phases of 60-90A each are an excellent sign. The third, and in practice the most reliable for non-engineers, is to **read specialized technical reviews**, which directly measure VRM temperatures under prolonged load with a demanding CPU. If a review shows that the VRM of a certain board stays below 70-80 °C with the CPU you intend to use at maximum load, that board is adequate; if it exceeds 100-110 °C or throttles, it is not.

The practical rule is simple: **the more powerful the CPU and the more you intend to overclock it, the more the VRM matters**. For an entry-level CPU in an office PC, almost any VRM will do. For a high-end, multi-core CPU pushed to its limits, a weak VRM becomes the limiting factor for the entire system and can even reduce its lifespan due to excessive heat.

---

## 4. The Form Factor

### 4.1 What the form factor is and why it matters

The **form factor** is the dimensional standard of the motherboard: it defines the physical measurements, the position of the mounting holes, and the general layout of the components. It is a crucial parameter because it must be **consistent with the case**: a case declares which form factors it accepts, and it is not possible to mount a board larger than what the case allows. The form factor also influences expandability (number of available slots), ease of cooling, and, in part, the price.

### 4.2 The four main form factors

**ATX** (*Advanced Technology eXtended*) is the reference standard in the desktop world, with dimensions of **305 × 244 mm** (approximately 30.5 × 24.4 cm). It is the "full" format: it typically offers four RAM slots, multiple PCIe slots, ample space for the VRM and heatsinks, and maximum connectivity. It is the most balanced choice for most fixed builds because it does not impose compromises on expandability.

**Micro-ATX** (often abbreviated as *mATX* or *µATX*) measures **244 × 244 mm**, meaning it is a square that is shorter downwards compared to ATX. It usually retains four RAM slots but reduces the number of PCIe slots (typically one or two usable). It is the format with the **best price/performance ratio**: mATX boards often cost less than equivalent ATX boards while offering everything needed for a normal PC with a single graphics card. It is an excellent choice for those who want to save money without sacrificing four RAM slots.

The **Mini-ITX** measures just **170 × 170 mm**: it is the compact format par excellence, designed for small and portable PCs. However, it imposes significant compromises: it has **only one PCIe slot** (meaning only one video card, no additional cards) and, above all, **only two RAM slots**. For the same features, Mini-ITX boards also tend to cost **more** than ATX boards, because concentrating everything into such a small space is engineeringly expensive. It is chosen for its form factor, not for saving money.

The **E-ATX** (*Extended ATX*) is the largest format, with a width of up to **305 × 330 mm** (exact measurements vary slightly between manufacturers, and this is a point to check carefully against the case specifications). It is reserved for workstations and high-end systems that require enormous VRMs, many slots, and extreme connectivity. It requires large and specifically compatible cases, and comes at a high cost.

| Form factor | Dimensions (mm) | Typical PCIe slots | Typical RAM slots | Pros | Cons |
|---|---|---|---|---|---|
| **E-ATX** | up to 305 × 330 | Many | 4 (sometimes 8 on HEDT) | Maximum expandability and VRM | Expensive, requires large and compatible cases |
| **ATX** | 305 × 244 | 3–4 | 4 | Ideal balance, widespread standard | Larger footprint than mATX |
| **Micro-ATX** | 244 × 244 | 1–2 | 4 | Excellent price, compact but spacious | Fewer PCIe slots |
| **Mini-ITX** | 170 × 170 | 1 | 2 | Extreme compactness, portability | Limited expandability, high price, limited RAM |

### 4.3 Practical implications

The choice of form factor is intertwined with the choice of case and the goals of the build. For a "normal" gaming or work PC, **ATX or Micro-ATX** cover the vast majority of needs; Micro-ATX in particular is often the smartest choice in terms of value for money. **Mini-ITX** makes sense when compactness is a priority (a living room PC, one to take to events), accepting its limitations and premium price. **E-ATX** is only appropriate for professional configurations with very high expandability, where many lanes, many drives, and an oversized VRM are truly needed. A common mistake is to buy an elegant but small case and then discover that the desired ATX board doesn't fit, or that there's no space for the CPU cooler: **the form factor must be decided together with the case, not separately**.

---

## 5. Slots and ports

### 5.1 PCIe slots: number, generation, and lane allocation

As anticipated, **PCIe** slots house the video card and other expansion cards. To understand them, two concepts must be introduced: **generation** and **lanes**.

**Generation** (PCIe 3.0, 4.0, 5.0) determines the bandwidth per lane: each generation doubles the previous one. A PCIe 3.0 lane offers about 1 GB/s, a 4.0 about 2 GB/s, a 5.0 about 4 GB/s. **Lanes** are the parallel electrical pathways: an "x16" slot has sixteen lanes, an "x4" has four, and so on. Total bandwidth is the product of lanes and bandwidth per lane: an **x16 slot in PCIe 4.0** therefore offers about 32 GB/s, an **x16 in PCIe 5.0** about 64 GB/s.

A point that confuses many beginners is the difference between the **physical length** and **electrical lanes** of a slot. A slot can be physically as long as an x16 (to accommodate a graphics card, which requires the long connector) but only be electrically wired as x4 or x1. On technical specifications, this is written, for example, as "PCIe x16 (in x4 mode)": this means that the graphics card physically fits, but only receives a quarter of the bandwidth. This is an important detail when you want to use multiple slots simultaneously.

The most important distinction, however, is between **slots connected directly to the CPU** and **slots connected to the chipset**. The **first x16 slot**, intended for the main graphics card, is almost always connected **directly to the CPU**: it is the fastest, with full bandwidth and minimal latency, and it is the one you should use for the GPU. Secondary slots, on the other hand, are generally connected **to the chipset**, which means their bandwidth shares the DMI/PCIe link to the CPU we discussed: if you use them intensively along with other chipset peripherals, they may contend for bandwidth. For a normal single-graphics-card PC, this is completely irrelevant; it only becomes relevant in configurations with multiple high-bandwidth expansion cards.

### 5.2 M.2 slots and lane sharing

**M.2** slots house modern SSDs. An NVMe SSD (*Non-Volatile Memory Express*, the fast protocol used by high-performance M.2 SSDs) typically uses **four PCIe lanes** to achieve its high speeds. Motherboards offer one to four (or more, on top models) M.2 slots, of different generations: the first M.2 slot is often connected directly to the CPU and can be PCIe 5.0 or 4.0, while the others are connected to the chipset and may be of a lower generation.

Here, a concept as important as it is underestimated comes into play: **lane sharing**. Since the total PCIe lanes are a finite resource, manufacturers often **share** the same lanes among multiple connectors, with the rule that **they cannot all be used simultaneously**. The classic case: a certain M.2 slot **shares lanes with some SATA ports**, so installing an SSD in that slot **automatically disables two or four SATA ports**. Another case: a second M.2 slot shares lanes with a secondary PCIe slot, so populating one reduces or disables the other. Furthermore: on some boards, populating all M.2 slots causes the graphics card slot to drop from x16 to x8.

This is one of the **most costly mistakes** made by inexperienced buyers: they buy a board with "four M.2 slots and six SATA ports" convinced they can use them all, and then discover that filling the M.2s turns off half the SATA ports, or that adding a drive removes lanes elsewhere. The solution is to **always read the board's manual (or the "detailed specifications" section) before purchasing**, identifying the lane sharing table, which lists exactly which connectors are mutually exclusive. On platforms with few lanes (e.g., entry-level chipsets), lane sharing is more stringent; on high-end chipsets, with many more lanes from the chipset, it is less frequent but still possible.

### 5.3 RAM slots: 2 vs 4

The number of RAM slots has two implications. The first concerns **dual channel**: modern desktop platforms achieve full memory bandwidth by using **two channels**, which means installing modules **in pairs**. With four slots, you can start with two modules and add two more later; with two slots (typical of Mini-ITX), you are already "full" if you want dual channel, and to increase capacity you will have to **replace** the modules rather than add them. The second implication concerns **upgrading**: four slots offer more future flexibility, while two slots force you to discard existing modules to increase RAM.

A technical detail often overlooked: **filling all four slots makes it more difficult to achieve very high memory frequencies**, because the integrated memory controller (IMC) in the CPU has to drive more modules and the signal is more complex to manage. For this reason, those aiming for maximum RAM speeds often prefer **two high-capacity modules** rather than four lower-capacity ones, even if they have four slots.

### 5.4 SATA Ports

**SATA** ports are used to connect mechanical hard drives and "traditional" 2.5-inch SSDs. They are still useful for large, inexpensive storage (large hard drives use SATA). The number of SATA ports varies from four to eight depending on the motherboard; as already seen, some of these ports can be shared with M.2 slots via lane sharing. Those who plan to connect many mechanical drives (for example, for a small home server or multimedia storage) must carefully count the SATA ports **actually usable simultaneously with the M.2 SSDs** they intend to install.

### 5.5 The Rear I/O Panel

The **I/O panel** (Input/Output) is the row of ports that protrudes from the back of the case. It is one of the most tangible differences between price ranges, and it is good to decipher its acronyms.

**USB** ports follow a confusing nomenclature due to continuous renamings. **USB 2.0** are slow (480 Mbps) and only suitable for keyboards, mice, and undemanding peripherals. **USB 3.2 Gen 1** (formerly called USB 3.0 or USB 3.1 Gen 1) offer 5 Gbps; **USB 3.2 Gen 2** offer 10 Gbps; **USB 3.2 Gen 2x2** reach 20 Gbps using two lanes. At the top are **USB4** and **Thunderbolt**, which reach **40 Gbps** and support advanced functions such as connecting monitors and high-speed external enclosures; USB4 and Thunderbolt 4 are largely interoperable. The **Type-C connector** is the now standard reversible physical form for faster ports, while the classic rectangular **Type-A** remains common for intermediate speeds.

On the panel you will also find **video** outputs (HDMI, DisplayPort): these are only needed **if you use the integrated graphics (iGPU)** of the CPU, i.e., without a dedicated graphics card; if you have a graphics card, you will connect the monitor to it and the motherboard's outputs will remain unused. Then there is **networking**: modern motherboards offer at least one **2.5G** (2.5 Gbps) Ethernet port, while high-end models may have **10G** (10 Gbps); **Wi-Fi** with its antenna is also often present. The **audio** section includes analog jacks and, on better motherboards, an optical output and higher quality audio chips.

Two very practical functions, found mostly in mid-range motherboards and above, deserve a mention. **BIOS Flashback** (also called *Q-Flash Plus*, *Flash BIOS Button* depending on the manufacturer) is a button that allows you to **update the BIOS without a CPU, RAM, or graphics card installed**: it is essential when you buy a "new old stock" motherboard with a BIOS too outdated to recognize your new CPU. **Clear CMOS** is a button (or a jumper) that **resets the BIOS settings** to their factory defaults: indispensable for restoring startup after a failed overclock.

### 5.6 Internal Headers

**Headers** are pin connectors on the PCB where internal cables are connected. The main ones are the **front USB headers**, which power the case's USB ports (Type-A and Type-C on the front); **ARGB and RGB headers** (*Addressable RGB*, individually addressable LED lighting), for connecting fans and light strips; **fan connectors** (usually 4-pin PWM, which allow speed control); and the header dedicated to the **AIO pump** (*All-In-One*, the pre-assembled liquid cooling system), which provides stable and full power to the pump. Counting the available headers is important for those with many fans or an elaborate lighting system: an inexpensive board might offer too few, forcing the purchase of additional *splitters* or *hubs*.

---

## 6. Nomenclature and Brands

### 6.1 Why Names Matter

Manufacturers organize their boards into **lines** (or *series*), which represent increasing price and feature tiers. Learning to read these lines allows you to immediately place a board in the correct tier without having to analyze every specification. Be careful, however: **the same line can contain models of different prices** depending on the chipset (a "Strix B650" costs less than a "Strix X670E"), so the line indicates *relative positioning*, not an absolute price.

### 6.2 ASUS

ASUS structures its range quite linearly. The **Prime** line is the entry/mainstream, essential and affordable. **TUF Gaming** is the mid-range, with an emphasis on robustness and reliable components, offering an excellent quality/price compromise. **ROG Strix** (*Republic of Gamers*) is the mid-to-high range, with more robust VRMs, more connectivity, and refined aesthetics. At the top are **ROG Maximus** (for Intel platforms) and **ROG Crosshair** (for AMD platforms), the flagship boards designed for extreme overclocking and maximum features. Finally, there is the **ProArt** line, aimed at content creators, with a sober aesthetic and connectivity (Thunderbolt, fast networking) designed for workstations.

### 6.3 MSI

MSI uses four-letter acronyms in ascending order of tier. **PRO** is the entry/business line, sober and functional. **MAG** (*MSI Arsenal Gaming*) is the mid-range gaming tier, where very popular models like the "Tomahawk" fall. **MPG** (*MSI Performance Gaming*) is the mid-to-high range. **MEG** (*MSI Enthusiast Gaming*) is the top, with the flagship boards. The mnemonic rule is that, as you go up, the second letter tells the progression: from PRO to Arsenal, Performance, Enthusiast.

### 6.4 Gigabyte

Gigabyte separates the entry-level from the gaming lines. **UD** (*Ultra Durable*) is the essential entry-level. Boards simply labeled **Gaming** occupy the mid-range. The premium brand is **Aorus**, structured in increasing levels: **Aorus Elite** (mid-range), **Aorus Pro** (upper mid-range), **Aorus Master** (high-end), and **Aorus Xtreme** (flagship). There is also the **Aero** line for content creators. Moving up the Aorus scale, VRMs, cooling, connectivity, and build quality all improve.

### 6.5 ASRock

ASRock has historically offered aggressive pricing. Entry-level boards carry essential names or the designation **Pro**. **Steel Legend** is the mid-range with a distinctive aesthetic. **Phantom Gaming** (often abbreviated as *PG*) is the mid-range gaming line, while names like **Riptide**, **Lightning**, or **Nova** identify specific variants. At the top is **Taichi**, the flagship line recognizable by its gear-themed aesthetic, with a complete feature set and robust VRMs.

### 6.6 Price Ranges and Brand Reputation

As a general guideline (with the caveat that **prices are volatile** and should always be checked), entry-level lines are in the most economical price bracket, mid-range lines in the intermediate, and flagship lines in the high-end, with top-tier boards potentially costing as much as a mid-range graphics card. The most important practical advice is: **don't pay for a tier you won't use**. A flagship board only makes sense if you truly utilize its features (extreme overclocking, exotic connectivity, many drives); otherwise, that money is better invested in the CPU, GPU, or RAM.

Regarding **brand reputation**, three factors matter more than aesthetics. The first is the **quality and usability of the BIOS/UEFI** (the firmware interface used to configure the board): a well-made, stable, and feature-rich BIOS makes life much easier, especially for overclocking and memory management. The second is the quality of support and the **RMA** (*Return Merchandise Authorization*) procedure, which varies in speed and efficiency by region. The third is the **actual quality of components** (VRMs, heatsinks, network, audio) beyond marketing. Since all major brands (ASUS, MSI, Gigabyte, ASRock) produce both excellent boards and mediocre models, the golden rule is **not to trust the brand alone but to evaluate each specific model through independent technical reviews**.

---

## 7. How to Choose the Ideal Motherboard

### 7.1 The Step-by-Step Procedure

Choosing a motherboard never starts with the motherboard itself: it starts with the **CPU**, and proceeds in a cascading manner. This is the correct method, ordered by priority.

The first step is to **decide on the CPU**, because the CPU determines everything else. The **socket** immediately follows from the CPU (AM5 for recent Ryzens, LGA1700 or LGA1851 for Intel depending on the generation): this is a rigid and non-negotiable constraint. Once the socket is chosen, you move on to the **chipset**, evaluating which functions are truly needed: overclocking yes or no, how many lines and ports, PCIe 5.0 or USB4 necessary or superfluous. Then the **form factor** is defined in coordination with the case (ATX, Micro-ATX, Mini-ITX): this choice must be made together with the case, not afterwards. At this point, the **necessary connectivity** is checked by comparing how many USB ports, how many M.2 slots, how many SATA ports, and what network are needed for your actual use. Only then is the **VRM** checked, sizing it to the chosen CPU (the more powerful and overclocked the CPU, the more robust the VRM must be). Finally, the **budget** is applied, choosing from the models that meet the requirements the one with the best quality/price ratio, without paying for unnecessary features.

### 7.2 Compatibility Checks

Once a candidate is identified, its compatibility must be checked on five fronts, because an error here can render the system unusable.

Compatibility with the **CPU** is not limited to the socket: you need to check that the board supports that specific CPU **with the installed BIOS version**. Boards sold before the release of a new CPU might have a BIOS that is too old to recognize it, making an update necessary via the **BIOS Flashback** function (which, as seen, also works without a compatible CPU). Manufacturers publish a **supported CPU list** for each board: this should always be consulted when pairing a very new CPU with a board that might have been in storage for some time.

Compatibility with **RAM** is verified on the **QVL** (*Qualified Vendor List*, the list of memory modules tested and certified by the manufacturer for that board) and on the supported memory speeds. The QVL is not an absolute constraint (modules not listed often work), but choosing one present in the list reduces the risk of stability problems, especially at high frequencies.

Compatibility with the **GPU** mainly concerns physical space (*clearance*): modern video cards are large and heavy, and you need to ensure that, once installed, the GPU does not cover M.2 slots or SATA ports that are needed, and that there is space in the case. In compact form factors, this aspect becomes critical.

Compatibility with the **case** is the form factor consistency already discussed: the case must accept the dimensions of the board. Compatibility with the **cooler** (CPU heatsink) concerns the bulk: very tall air coolers or large liquid radiators must find space without interfering with the VRM heatsinks, tall RAM modules, or the case panel.

### 7.3 Typical Buyer Mistakes

Some errors occur with such frequency that they warrant an explicit warning. The first is **paying for a top-tier chipset without using its features**: buying an X670E or a Z890 and then installing a mid-range CPU, a single graphics card, and a single SSD means throwing away money that would have yielded much more in CPU, GPU, or RAM. The second, already discussed, is **ignoring M.2 and SATA lane sharing**, ending up with ports that disable each other after purchase. The third is **pairing a weak VRM with a top-tier CPU**: an inexpensive board with an undersized VRM will limit a powerful CPU and can overheat under prolonged load. To these are added more trivial but frequent errors: forgetting to check BIOS support for a new CPU, choosing a form factor incompatible with the case, underestimating the size of the cooler, and being guided solely by the number of VRM phases or aesthetics instead of technical reviews.

---

## Operational Summary — Motherboard Decision Checklist

Use this checklist in order, from top to bottom. Each point must be checked before moving to the next.

**1. CPU and socket (rigid constraint).** Have I already chosen the CPU? The socket derives from it: AM5 (recent Ryzens, DDR5 only), LGA1700 (Intel 12th–14th gen, DDR4 or DDR5) or LGA1851 (Intel Core Ultra 200S, DDR5 only). The board must have exactly that socket.

**2. BIOS Support.** Does the board support my specific CPU with the factory BIOS? If the CPU is very recent, does the board have **BIOS Flashback** to update it without a compatible CPU? Have I checked the supported CPU list?

**3. Chipset and actual features.** Do I need **CPU overclocking**? If yes: for Intel, a **Z** chipset is needed; for AMD, any chipset except A620 is sufficient. Do I need **RAM overclocking** (EXPO/XMP)? Almost all allow it, except Intel H610. Do I really need **PCIe 5.0** (GPU/SSD) or **USB4**, or am I paying for features I won't use?

**4. Form factor and case.** Have I chosen the format (ATX / Micro-ATX / Mini-ITX / E-ATX) **together with the case**? Does the case accept these dimensions? Remember that Mini-ITX only has 2 RAM slots and 1 PCIe slot, and that Micro-ATX often offers the best value for money.

**5. Connectivity and lane sharing.** Have I counted how many **M.2 slots**, how many **SATA ports**, and how many **USB ports** I need? Have I read the **lane sharing** table to verify that the M.2 and SATA I want to use **do not disable each other**? Do I have enough **RAM slots** (2 vs 4) for future upgrades I anticipate? Is the **network** (2.5G/10G/Wi-Fi) adequate?

**6. VRM.** Is the chosen CPU powerful and/or will I overclock it? If yes, does the board have a **robust VRM** (substantial heatsinks, high-current phases) confirmed by **technical reviews** (temperatures under prolonged load)? Remember that the number of phases alone is not an indicator of quality.

**7. Physical compatibility.** Does the **GPU** fit and not cover ports/slots I need? Does the **CPU cooler** (tall air or liquid radiator) find space without interfering with VRM, RAM, and case? Is the chosen **RAM** on the QVL or otherwise compatible with the declared speeds?

**8. Brand and model.** Have I placed the board in the correct tier by reading the line (Prime/TUF/Strix/Maximus-Crosshair for ASUS; PRO/MAG/MPG/MEG for MSI; UD/Gaming/Aorus for Gigabyte; Pro/Steel Legend/Phantom Gaming/Taichi for ASRock)? Have I evaluated the **individual model** with independent reviews, instead of trusting only the brand or aesthetics?

**9. Final budget.** Among the models that pass all the previous points, am I choosing the one with the **best value for money**, avoiding paying for a chipset or a tier whose features I will not use? Would that money be better spent elsewhere (CPU/GPU/RAM)?

**Final golden rule.** The motherboard does not increase "fps": it enables, connects, and powers. Buy the **cheapest board that meets all your real requirements** with a VRM adequate for the CPU — no more, no less.

---

*End of chapter. Architectural data is updated to the best of available knowledge at the time of writing; prices, availability, and details of the latest chipsets (AMD 800 series, Intel LGA1851 platform) marked with **[VERIFY]** must be confirmed on the official technical specifications of individual models at the time of purchase.*

---

[← Previous](01-cpu-desktop-pcs.html) · [All chapters](./) · [Next →](03-ram-memory.html)
