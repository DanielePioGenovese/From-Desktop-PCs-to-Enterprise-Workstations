---
title: "Chapter 10 - CPUs for AI Workstation and HEDT"
parent: "English Edition"
nav_order: 10
---

<details open markdown="block">
  <summary>Chapter contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## Introduction: why this chapter exists

In previous chapters, we built, piece by piece, the mental model of the consumer PC: a CPU at the center, a GPU attached to the main slot, two RAM modules, one or two NVMe SSDs, a power supply sized with a reasonable margin. In that world, the dominant question is "how many frames per second do I get?" or "how long does it take to compile?", and the answer almost always revolves around clock frequency and core count.

When the goal becomes an **AI/ML workstation** — that is, a machine that trains neural networks, performs fine-tuning of language models, does local inference on quantized LLMs, processes video streams with detection models, or simply serves as a test bench for pipelines that will then run in datacenters — that mental model no longer holds. Not because it's wrong, but because **the question changes**. The question is no longer "how fast is the CPU," but "how much material can I simultaneously move between memory, drives, network, and accelerators, without anything waiting."

This is the central point of the entire chapter, and it's worth stating brutally: **in an AI workstation, the CPU's main purpose is to not be a bottleneck for everything else**. Heavy computation is done by GPUs. The CPU must ensure three things: (1) enough communication lanes (PCIe lanes) for GPUs and SSDs to communicate at full speed, (2) enough memory bandwidth and enough RAM for data to reach GPUs smoothly, (3) enough cores so that data preparation (image decoding, augmentation, tokenization) doesn't leave GPUs idle.

From these three needs arises an entire category of products — **HEDT** and **workstation/server** — that the consumer market does not know and that has completely different rules, nomenclature, prices, and pitfalls. HEDT stands for *High-End DeskTop*: it is a historical segment, created for those who needed more than a desktop but did not want (or could not) manage a server. Today, HEDT essentially means AMD Threadripper and, in part, Intel Xeon W.

The chapter proceeds as follows: first, we understand *why* a consumer CPU is not enough (and, equally important, *when it is perfectly sufficient*), then we delve into Threadripper platforms, then server platforms (EPYC and Xeon), then we explore PCIe as an infrastructure, then workstation motherboards, then sizing based on workload, and finally, typical errors. We conclude with an operational summary, which is effectively a purchase checklist.

A methodological note before we begin. **Numerical data in this sector ages very quickly**: prices, availability, generations, and even the compatibility matrix between CPU and chipset change with every firmware update. Whenever data is volatile, I will explicitly mark it with the tag *[volatile data]*. Architectures and principles, however, remain valid for much longer: these are what are truly worth learning.

---

## 1. Why a Consumer CPU Is Not Enough

### 1.1 The Shift in Perspective: The CPU as a Dispatch Node

In a consumer PC, the CPU is the protagonist: it executes application code, and everything else revolves around it. In an AI workstation, the protagonist is the accelerator (the GPU), and the CPU takes on what we might call an *infrastructural* role. It is the traffic dispatch node: it reads data from disk, decodes it, transforms it, packages it, and sends it to the GPU; then it collects the results, writes them, synchronizes them across multiple GPUs, and sends them over the network.

If even one of these steps is bottlenecked, the GPU — which costs as much as the rest of the system combined — remains idle. The critical parameter in an AI workstation is called **GPU utilization**: the percentage of time the accelerator cores are actually computing. A well-designed workstation keeps GPUs above 90–95% utilization during training. An unbalanced workstation — consumer CPU with four GPUs attached to reduced lanes, dataset on a single SSD, little RAM — can easily drop to 40–50%, which means **half of the hardware's economic value is wasted**.

Let's look, one by one, at the four resources that consumer systems fail to provide.

### 1.2 PCIe Lanes: The True Currency of the Workstation

**PCIe** stands for *Peripheral Component Interconnect Express*: it is the bus (i.e., the connection system) through which the CPU communicates with high-speed peripherals — graphics cards, NVMe SSDs, network cards, dedicated accelerators, storage controllers.

PCIe is a **serial and point-to-point** bus: it is not a shared road where everyone crowds (like the old PCI or ISA), but a set of dedicated connections. The elementary unit is called a **lane**. A lane is, physically, a pair of differential connections: one for transmitting, one for receiving. This means PCIe is **full-duplex**: it can transmit and receive simultaneously at full nominal speed in both directions.

Lanes are aggregated: a slot can be x1, x4, x8, x16. The number indicates how many lanes are wired to that slot. More lanes mean more bandwidth: bandwidth grows linearly with the number of lanes.

The second variable is the **generation** (or *revision*) of PCIe, which determines the speed of each lane. Here is the overview, which is worth memorizing because we will use it constantly:

| Generation | Indicative Year | Bandwidth per Lane (per direction) | x4 Bandwidth | x8 Bandwidth | x16 Bandwidth |
|---|---|---|---|---|---|
| PCIe 3.0 | 2010 | ~0.985 GB/s | ~3.94 GB/s | ~7.88 GB/s | ~15.75 GB/s |
| PCIe 4.0 | 2017 | ~1.97 GB/s | ~7.88 GB/s | ~15.75 GB/s | ~31.5 GB/s |
| PCIe 5.0 | 2019 | ~3.94 GB/s | ~15.75 GB/s | ~31.5 GB/s | ~63 GB/s |
| PCIe 6.0 | 2022 (spec) | ~7.88 GB/s | ~31.5 GB/s | ~63 GB/s | ~126 GB/s |

To be read as follows: **each generation doubles the bandwidth per lane**, for the same number of lanes. Consequently, a PCIe 5.0 x8 slot has the same bandwidth as a PCIe 4.0 x16 slot. This equivalence is very important and will come up repeatedly.

*[volatile data]* PCIe 6.0 exists as a specification and is being adopted in the datacenter world, but on workstation platforms available today, the reference standard is PCIe 5.0. PCIe 7.0 has already been ratified as a specification but does not yet have real products on the workstation market.

#### Why Lanes Are the Real Bottleneck

A CPU has a **finite** number of PCIe lanes, because each lane requires physical pins on the package, transceivers, dedicated silicon, and power consumption. It is an expensive resource, and manufacturers ration it to segment the market.

Let's count what a serious AI workstation needs:

- **One x16 PCIe 5.0 GPU**: 16 lanes. If there are four GPUs, **64 lanes** are needed just for the accelerators.
- **NVMe SSDs**: Each NVMe (*Non-Volatile Memory express*, the protocol by which SSDs communicate directly on the PCIe bus) typically occupies 4 lanes. An AI workstation often has 3–4: one for the operating system, one or two for the "hot" dataset, one for checkpoints. That's **12–16 lanes**.
- **Network card**: A 10 GbE NIC (*Network Interface Card*) generally occupies 4 PCIe 3.0/4.0 lanes; a 25/100 GbE one occupies 8–16 lanes. Let's say **4–16 lanes**.
- **HBA/RAID controller** for mass storage (datasets of tens of TB): **8 lanes**.
- **Capture cards, dedicated accelerators, acquisition cards**: variable.

Realistically summing up for a 4-GPU workstation: 64 + 16 + 8 + 8 = **96 lanes**. A consumer CPU has about twenty. The gap is a factor of four or five, and no software optimization can bridge it.

#### How Many Lanes Do Consumer CPUs Really Have

Here, one must pay attention to a detail that confuses many buyers: the lanes **of the CPU** and the lanes **of the chipset** are not the same thing.

The **chipset** (in AMD, it's also called *PCH*, *Platform Controller Hub*, originally an Intel term) is a separate chip on the motherboard that expands connectivity. But the chipset is connected to the CPU through a link that is itself limited — typically x4 PCIe 4.0. Everything connected to the chipset (secondary slots, some NVMe ports, USB, SATA, integrated LAN) **shares that single x4 link**. It's like having a villa with ten rooms but only one meter-wide corridor to reach them all.

For this reason, when designing a workstation, **only the CPU's native lanes matter**, those directly connected. Chipset lanes are fine for slow peripherals (audio, USB, gigabit network, archive SSDs) and should be avoided for high-performance GPUs and NVMe drives.

Here is the consumer overview *[indicative data, verify for specific models]*:

- **AMD AM5 (Ryzen 7000/8000/9000)**: 28 total PCIe lanes from the CPU, of which **24 are actually usable** (16 for the GPU + 4 for one NVMe + 4 for a second NVMe), plus 4 lanes dedicated to the chipset connection. Versions with powerful integrated graphics ("G" series) have even fewer.
- **Intel LGA1700 (12th–14th gen)**: **20 lanes** from the CPU (16 PCIe 5.0 for the GPU + 4 PCIe 4.0 for an NVMe), plus the DMI connection to the chipset.
- **Intel LGA1851 (Core Ultra 200 series "Arrow Lake")**: **24 lanes** from the CPU (16 PCIe 5.0 + 4 PCIe 5.0 + 4 PCIe 4.0), plus DMI.

*[volatile data]* These numbers change with each new platform; the principle (consumer = ~20–28 lanes, and no more) has been stable for over a decade.

### 1.3 Comparative table: lanes by platform segment

| Platform | Segment | PCIe Lanes from CPU | Generation | Notes |
|---|---|---|---|---|
| Intel LGA1700 (Core 12th/13th/14th gen) | Consumer | 20 | 5.0 (16) + 4.0 (4) | Single GPU at x16 |
| Intel LGA1851 (Core Ultra 200S) | Consumer | 24 | 5.0 (20) + 4.0 (4) | One GPU + 2 NVMe |
| AMD AM5 (Ryzen 7000/9000) | Consumer | 24 usable (+4 chipset) | 5.0 | One GPU + 2 NVMe |
| AMD Threadripper 7000/9000 (TRX50) | HEDT | **48 PCIe 5.0** (+ additional 4.0 lanes; AMD states up to ~92 platform lanes) | 5.0 / 4.0 | 2 GPUs at full x16 + NVMe |
| AMD Threadripper PRO 7000WX/9000WX (WRX90) | Workstation | **128 PCIe 5.0** | 5.0 | Up to 7 x16 slots |
| Intel Xeon W-2400/2500 (W790) | Entry Workstation | 64 | 5.0 | 2 GPUs at x16 + storage |
| Intel Xeon W-3400/3500 (W790) | Workstation | 112 | 5.0 | 4+ GPUs |
| AMD EPYC 9004/9005 (SP5) | Server | **128 per socket** | 5.0 | 160 total lanes in dual-socket |
| Intel Xeon 6 (6700P/6900P) | Server | 88 / 96 per socket | 5.0 | Depends on SKU |

*[volatile data: always check the datasheet for the precise SKU, as variants with different counts exist within the same family]*

Practical reading of this table: the jump from consumer to HEDT **doubles or triples** the lanes; the jump from HEDT to professional workstation **doubles them again**. And this is why — not for the number of cores — one moves up in platform.

### 1.4 Memory channels: why RAM bandwidth matters

The **memory controller** is the circuit that manages communication between the CPU and RAM. For about fifteen years, it has been integrated into the CPU itself, and is called the **IMC** (*Integrated Memory Controller*). Previously, it was on the chipset (the infamous "northbridge"), and its integration into the CPU was one of the major performance breakthroughs of the 2000s.

The IMC communicates with the RAM through one or more **channels**. A channel is an independent path to the memory modules: more channels mean more parallel paths, and thus more aggregated bandwidth.

Let's do the math. A DDR5 (*Double Data Rate 5*, the fifth generation of SDRAM memory) module at 5600 MT/s (*Mega Transfers per second*, i.e., millions of transfers per second) provides approximately **44.8 GB/s per channel** (5600 × 8 bytes). From this:

| Configuration | Channels | Theoretical Bandwidth (DDR5-5600) | Platforms |
|---|---|---|---|
| Dual channel | 2 | ~90 GB/s | Consumer AM5, LGA1700/1851 |
| Quad channel | 4 | ~180 GB/s | Threadripper non-PRO (TRX50), Xeon W-2400/2500 |
| Octa channel | 8 | ~358 GB/s | Threadripper PRO (WRX90), Xeon W-3400/3500 |
| 12-channel | 12 | ~537 GB/s | AMD EPYC 9004/9005, Intel Xeon 6 (6900P) |

*[note: officially supported frequencies vary; server platforms typically use registered DDR5 at 4800–6400 MT/s depending on the number of modules per channel. Volatile data.]*

#### Why RAM Bandwidth Matters in an AI Workstation

There are three reasons, all concrete.

**First: data loading.** During training, the flow is as follows: raw data (JPEG images, audio files, texts) is read from disk, decoded into RAM, transformed (resizing, normalization, augmentation), packaged into tensors, and finally copied to the GPU's memory via PCIe. All these intermediate steps reside in system RAM. With four GPUs requesting, say, 3–4 GB/s of data each, and considering that each byte is touched multiple times (reading, decoding, transforming, copying to *pinned memory*), RAM is traversed at tens of GB/s. A dual channel at a theoretical 90 GB/s — which in practice translates to 60–70 GB/s real-world — becomes a bottleneck.

**Second: CPU inference.** When running an LLM (*Large Language Model*) directly on the CPU — with `llama.cpp`, with ONNX Runtime backend, with oneDNN — the **token-by-token generation** phase is almost entirely *memory-bound*, meaning limited by memory bandwidth, not computational power. The reason is that to generate each single token, **all model weights** must be read from RAM. A 70 billion parameter model quantized to 4 bits occupies about 40 GB. To generate 10 tokens per second, 400 GB/s must be read. On a dual-channel platform with 70 GB/s real-world, this results in about 1.5–2 tokens/s: unusable for interactive use. On a 12-channel platform with 400+ GB/s, this results in 8–10 tokens/s: slow but usable. **This is why there is a real niche of people who buy used EPYC for LLM inference on CPU: not for the cores, but for the bandwidth.**

**Third: classic preprocessing.** Pandas/polars pipelines on tabular datasets of tens of GB, join operations, groupby, feature engineering: all operations that move mountains of bytes and scale with memory bandwidth more than with clock frequency.

### 1.5 RAM Capacity: The Real Limits and Registered Modules

Beyond bandwidth, there's **capacity**. And here the consumer limit is clear.

A consumer platform has **four DIMM slots** (on two channels: two modules per channel). The practical limit depends on the maximum capacity of available UDIMM modules:

*[volatile data]* On AM5 and LGA1851, with 48 GB UDIMM modules, up to 192 GB is achieved; with the arrival of 64 GB modules and related AGESA/BIOS updates, 256 GB is reached. Beyond that, it's not possible, and already with 4 modules, operating frequencies drop because the IMC struggles to drive two modules per channel at high speed.

This is the critical point: **on a consumer platform, filling all four slots degrades RAM frequency**. A DDR5-6000 kit with two modules runs at 6000; the same four modules often drop to 3600–4400 MT/s. You pay for capacity in exchange for bandwidth — exactly the compromise you don't want to make in a workstation.

#### UDIMM, RDIMM, LRDIMM

To overcome this limit, the professional world uses different modules.

- **UDIMM** (*Unbuffered DIMM*): This is the consumer module. Address and command signals from the IMC arrive **directly** at all memory chips on the module. Simple, inexpensive, low latency—but the electrical load on the controller increases with the number of chips, which limits how many modules can be placed per channel and at what frequency.

- **RDIMM** (*Registered DIMM*): There is an additional chip on the module, the **RCD** (*Registering Clock Driver*), which acts as a buffer for address and command signals. The IMC drives only one load (the register) instead of dozens of chips. Cost: one additional clock cycle of latency. Benefit: much larger modules can be installed (64, 96, 128 GB and more) and more modules per channel while maintaining high frequencies. **Threadripper PRO, EPYC, and Xeon W/Scalable platforms require RDIMM: they do not accept UDIMM.**

- **LRDIMM** (*Load-Reduced DIMM*): This buffers data lines as well, not just addresses and commands. It allows for extreme capacities (up to 256 GB per module) at the cost of additional latency. Used in servers with enormous memory requirements.

- **MRDIMM** (*Multiplexed Rank DIMM*): The recent evolution, which multiplexes two ranks to double the effective bandwidth. Supported on the latest server platforms. *[volatile data, evolving ecosystem]*

#### Capacity Limits per Platform

| Platform | Typical DIMM Slots | Memory Type | Indicative Max Capacity |
|---|---|---|---|
| Consumer AM5 / LGA1851 | 4 | UDIMM (optional unofficial ECC) | 192–256 GB *[volatile]* |
| Threadripper 7000/9000 (TRX50) | 4 | RDIMM ECC (4 channels) | ~1 TB |
| Threadripper PRO (WRX90) | 8 | RDIMM ECC (8 channels) | ~2 TB |
| Xeon W-3400/3500 (W790) | 8 | RDIMM ECC (8 channels) | ~2 TB |
| EPYC 9004/9005 (SP5) | 12 or 24 | RDIMM ECC (12 channels) | 6 TB and beyond per socket |

*[volatile data: maximum capacities increase with each new RDIMM module on the market. Check the motherboard's QVL — Qualified Vendor List.]*

The jump is an order of magnitude. And it's truly needed: loading an entire image dataset into RAM to eliminate disk I/O, holding a huge graph in memory, performing CPU inference on a 405-billion-parameter model, running simulations—these are all cases where 256 GB is insufficient.

### 1.6 ECC: What it is and why it's non-negotiable in professional environments

**ECC** stands for *Error-Correcting Code*.

The problem it solves is physical. A DRAM memory cell stores a bit as an electrical charge in a microscopic capacitor. That charge can be altered: by a cosmic ray (a high-energy neutron passing through silicon), by alpha particles emitted from radioactive impurities in package materials, by electromagnetic interference, or by thermal or power instabilities. The result is a **soft error**: a bit that flips, from 0 to 1 or vice versa, without anything being physically broken.

The frequency is low but not negligible. Field studies (the most cited being Google's on tens of thousands of servers) indicate orders of magnitude of **thousands of correctable errors per gigabyte per year** under real conditions. On a desktop with 32 GB accessed eight hours a day, an occasional error: annoying, maybe a crash, you restart. On a workstation with **512 GB running 24/7 for three weeks** during training, the probability of at least one bit flip becomes **high**.

#### How ECC works

The classic implementation is called **SECDED**: *Single Error Correction, Double Error Detection*. For every 64 bits of useful data, 8 bits of control code (an extended Hamming code) are added, meaning the module has extra chips: an ECC module typically has 9 or 18 chips instead of 8 or 16. When the CPU reads those 72 bits, it recalculates the code: if a single bit is wrong, the system **understands which one** and corrects it on the fly, transparently, logging the event. If two bits are wrong, the system **detects** the error but cannot correct it: it generates a machine exception and generally halts the system, because continuing would mean working with corrupted data.

This is the key point: **without ECC, a bit flip is silent**. The computer doesn't know the data is wrong. It just keeps going.

#### Why it's essential in AI/ML

Let's imagine the concrete consequences of a silent bit flip in an AI workstation:

- During a 72-hour training session, a bit flips in the exponent of a floating-point number representing a weight. The weight changes from 0.003 to 3×10³⁰. The gradient explodes, the loss becomes NaN (*Not a Number*), and the problem is noticed — in the best case — hours later. **Three days of GPU wasted.**
- Worse: the bit flip occurs at a point that doesn't cause anything to explode, but subtly corrupts a batch of data or a weight. The model still converges, but it is **subtly wrong**, and no one will ever notice. The scientific result or product is compromised in a non-reproducible and non-diagnosable way.
- A bit flip in a checkpoint written to disk corrupts the file, and it's only noticed weeks later when trying to resume training.

In a professional context — publishable research, models going into production, calculations on which decisions are made — **silent non-reproducibility is unacceptable**. ECC costs 10–15% more for modules and about one percentage point in performance. It's the cheapest insurance for the system.

#### Beware of two common confusions

**Confusion 1: DDR5's "on-die ECC" is not system ECC.** All DDR5 modules, even the cheapest gaming ones, have a form of error correction *internal to the chip* (on-die ECC), introduced because cell densities have become so high that internal errors would otherwise be unsustainable. But this correction only protects the cell; **it does not protect the path between the chip and the CPU**, does not report errors to the operating system, and provides no end-to-end guarantee. Seeing "DDR5 with on-die ECC" on the packaging of a gaming kit and thinking you have an ECC machine is a very common and very costly mistake.

**Confusion 2: ECC support on a consumer platform.** Some AM5 CPUs technically support **UDIMM ECC** modules, and some motherboards (especially ASRock and some ASUS Pro) expose it in the BIOS. This is real support but **not officially validated** for professional use, with limited module availability and no guarantees. It's not a shortcut to avoid the workstation platform: it's a "better than nothing" for a homelab.

### 1.7 What if consumer is enough?

Before proceeding, it's honest to say something that the rest of the chapter might make you forget: **for many real-world use cases, a high-end consumer platform is the right choice.**

If the project is: a single GPU (even an RTX 5090 or an RTX PRO 6000), 128 GB of RAM, two NVMe drives, fine-tuning with LoRA, local inference, prototype development — then a Ryzen 9 9950X or a Core Ultra 9 285K on a consumer platform **does exactly what's needed**, costs a quarter, has higher single-thread frequencies (useful for the non-parallelized Python code), and doesn't require exotic cooling.

Spending 3,000 euros on a Threadripper PRO CPU + motherboard to drive a single GPU is, as we'll see in the errors section, the most frequent and most expensive mistake in this sector. **You upgrade platforms when a precise boundary is crossed, not for prestige.** That boundary is: **two or more GPUs at x16 electrical**, or the need for more than 256 GB of RAM, or the requirement for certified ECC.

---

## 2. AMD Threadripper: The Complete Overview

### 2.1 What Threadripper is and where it fits

**Threadripper** is AMD's line launched in 2017 to fill the gap between Ryzen (consumer) and EPYC (server). Technically, it's not a separate project: it's **repackaged EPYC silicon** on a dedicated package and socket, with higher frequencies, fewer memory channels (in non-PRO versions), and a motherboard ecosystem designed for desktop rather than rack.

The positioning is precise:

- **Ryzen** (AM5): 1 GPU, 2 RAM channels, ~24 lanes. Maximum frequencies, minimum latencies, low price.
- **Threadripper** (sTR5/TRX50): 2 GPUs at x16, 4 RDIMM RAM channels, 48 PCIe 5.0 lanes. The "extreme desktop."
- **Threadripper PRO** (sTR5/WRX90): 4–7 GPUs, 8 RDIMM RAM channels, 128 PCIe 5.0 lanes. The true workstation.
- **EPYC** (SP5): 12 channels, 128 lanes per socket, dual socket, but lower frequencies and no "desktop" support.

It's important to understand immediately: **PRO and non-PRO share the same physical socket (sTR5) but not the same platforms**. This is the number one source of confusion.

### 2.2 Threadripper non-PRO: The 7000X and 9000X Series

The Zen 4-based generation (codename *Storm Peak*, launched in late 2023) includes:

| Model | Cores / Threads | Indicative Max Boost | L3 Cache | TDP |
|---|---|---|---|---|
| Threadripper 7960X | 24 / 48 | ~5.3 GHz | 128 MB | 350 W |
| Threadripper 7970X | 32 / 64 | ~5.3 GHz | 128 MB | 350 W |
| Threadripper 7980X | 64 / 128 | ~5.1 GHz | 256 MB | 350 W |

The next generation, based on **Zen 5** (codename *Shimada Peak*, 9000 series, available from 2025), maintains the same structure:

| Model | Cores / Threads | Notes |
|---|---|---|
| Threadripper 9960X | 24 / 48 | Direct successor to 7960X |
| Threadripper 9970X | 32 / 64 | Successor to 7970X |
| Threadripper 9980X | 64 / 128 | Successor to 7980X |

*[volatile data: frequencies, prices, and availability change; the core/lane structure is stable]*

Platform characteristics of non-PROs on TRX50 chipset:

- **Memory**: **4-channel** DDR5 RDIMM ECC, 4 DIMM slots. Important note: even non-PRO Threadrippers **require registered RDIMM**, not consumer UDIMM. You cannot reuse a gaming kit.
- **PCIe**: **48 PCIe 5.0 lanes** from the CPU usable on the TRX50 platform, plus additional PCIe 4.0 lanes from the chipset and CPU. AMD commercially advertises a total platform "up to 92 lanes" by summing everything (CPU + chipset, mixed generations). *[Caution: this is a marketing number. What matters for GPUs are the 48 native PCIe 5.0 lanes.]*
- **TDP**: **350 W** (*Thermal Design Power*, the thermal design power that the cooling system must be able to dissipate under sustained conditions; actual peak consumption may exceed it).

**What it's used for**: two GPUs at full x16 PCIe 5.0, plus 2–3 NVMe at x4, plus a NIC. This is the typical configuration for a serious individual researcher's AI workstation, or a small team.

### 2.3 Threadripper PRO: The 7000WX and 9000WX Series

This is a different category. The PRO line is AMD's professional workstation, and the difference is not in the core count (which also increases) but **in the platform**.

| Model | Cores / Threads | L3 Cache | TDP |
|---|---|---|---|
| Threadripper PRO 7955WX | 16 / 32 | 64 MB | 350 W |
| Threadripper PRO 7965WX | 24 / 48 | 128 MB | 350 W |
| Threadripper PRO 7975WX | 32 / 64 | 128 MB | 350 W |
| Threadripper PRO 7985WX | 64 / 128 | 256 MB | 350 W |
| Threadripper PRO 7995WX | **96 / 192** | 384 MB | 350 W |

The Zen 5 generation (9000WX series) replicates the structure, with the **9995WX** at 96 cores at the top, flanked by 9985WX (64c), 9975WX (32c), 9965WX (24c), 9955WX (16c). *[volatile data: check updated SKU list]*

PRO platform features on **WRX90** chipset:

- **Memory**: **8-channel** DDR5 RDIMM ECC, 8 DIMM slots, up to ~2 TB.
- **PCIe**: **128 native PCIe 5.0 lanes**. This is the number that justifies the entire existence of the line.
- **Professional features**: AMD PRO Manageability (enterprise-level remote management), AMD PRO Security (Memory Guard, memory encryption), ISV (*Independent Software Vendor*: certification that professional software such as CAD, simulators, and rendering suites are tested on that platform) guarantees and validation.

### 2.4 The Comparison That Really Matters

Let's summarize, because this is **the most important table in the entire chapter**:

| Feature | Threadripper (non-PRO) on TRX50 | Threadripper PRO on WRX90 |
|---|---|---|
| Socket | sTR5 | sTR5 (**same physical socket**) |
| Chipset | TRX50 | WRX90 |
| Native PCIe 5.0 Lanes | **48** | **128** |
| RAM Channels | **4** | **8** |
| DIMM Slots | 4 | 8 |
| Max RAM | ~1 TB | ~2 TB |
| RAM Type | RDIMM ECC | RDIMM ECC |
| Max Cores | 64 | **96** |
| GPUs at x16 electrical | **2** | **up to 7** |
| Remote Management (IPMI/BMC) | rarely | typical |
| Platform Cost (CPU+MB) | High | Very High |

A subtle point that causes a lot of confusion: **a Threadripper PRO CPU can be mounted on a TRX50 motherboard**, because the socket is the same. But in that case, it **operates with the limitations of the TRX50**: 4 memory channels instead of 8, and a reduced number of exposed lanes (TRX50 boards do not have the wiring for 128 lanes). The opposite is **not true**: a non-PRO CPU **does not work** on a WRX90 motherboard. WRX90 only accepts PRO.

This opens up an interesting practical scenario: **buying a PRO CPU on a TRX50 board** only makes sense if you want access to a higher core count (e.g., the 96-core 7995WX) without needing 128 lanes. It's a niche case — but it exists, for example, for rendering or massive compilation workloads with a single GPU.

### 2.5 Decoding the nomenclature

Let's take **7995WX** and break it down:

- **7**: generation. The 7000 series corresponds to Zen 4; the 9000 series to Zen 5. *(Note: AMD skipped the 8000 series on Threadripper, aligning with Ryzen numbering.)*
- **99**: tier within the generation. Higher = more cores. 95 → 96 cores, 85 → 64 cores, 75 → 32 cores, 65 → 24 cores, 55 → 16 cores. The two-digit logic is "position in the stack," not a mathematical formula.
- **5**: segment/revision digit, practically constant in the line.
- **WX**: **the suffix is the most important thing of all**. *WX* = *WorkstationeXtreme*, i.e., **PRO**: 8 channels, 128 lanes, WRX90.
- **X** (without W): non-PRO. 4 channels, 48 lanes, TRX50.

So: **7970X ≠ 7975WX**. The first has 32 cores, 4 channels, 48 lanes. The second has 32 cores, 8 channels, 128 lanes. **Same cores, completely different platforms, very different price.** Those who don't know the meaning of "WX" are highly likely to buy the wrong product.

### 2.6 sTR5 Socket, TRX50 and WRX90 Chipsets

The **sTR5** socket is an LGA (*Land Grid Array*: the pins are on the socket, not on the CPU) with **4844 pins**. It's physically enormous — a rectangle of about 76 × 58 mm — and this has practical consequences:

- **Mounting**: the CPU is inserted into an orange plastic *carrier* that slides into a metal rail; the lid closes with **three Torx screws** to be tightened **in numbered order (1, 2, 3)** and with **controlled torque**. AMD provides a torque screwdriver in the box. This is not a whim: tightening out of order or with incorrect torque on such a large surface can cause missed contacts or permanent damage to the package.
- **Cooling**: the IHS (*Integrated Heat Spreader*, the metal lid of the CPU) is much larger than consumer ones. **An AM5 or LGA1700 cooler will not cover the surface** and does not have the correct mounting. Specific sTR5 coolers are required.

The **chipset**, as we have seen, is the chip on the motherboard that expands connectivity. The choice between TRX50 and WRX90 **is the real architectural decision**, and it must be made **before** choosing the CPU:

| Question | If the answer is... | Platform |
|---|---|---|
| How many GPUs at electrical x16? | 1 or 2 | **TRX50** |
| How many GPUs at electrical x16? | 3 or more | **WRX90** |
| Do you need more than ~500 GB of RAM? | Yes | **WRX90** |
| Do you need maximum memory bandwidth (CPU inference)? | Yes | **WRX90** (8 channels) |
| Do you need remote IPMI/BMC management? | Yes | **WRX90** (or server board) |
| Is budget the main constraint? | Yes | **TRX50** |

### 2.7 Why Threadripper PRO is *the* platform for AI workstations

There is only one reason, and it lies in the 128 lanes. Let's count on an **ASUS Pro WS WRX90E-SAGE SE** board, which is the reference in this category:

- **7 PCIe 5.0 x16 slots** — all **true electrical x16**, all simultaneously. 7 × 16 = **112 lanes**.
- Approximately 16 lanes remain for NVMe (often 4 M.2 slots, some via bifurcation) and for the rest.

Seven full x16 slots mean you can install **four dual-slot GPUs with adequate space between them** (using slots 1, 3, 5, 7), or **seven single-slot cards**. No other desktop-derived platform offers this. It is literally the reason why multi-GPU workstations exist under the desk instead of in racks.

### 2.8 Brand and Price Range Comparison (Threadripper)

*[All prices are indicative and volatile; use them only as orders of magnitude.]*

| Configuration | Indicative CPU | Motherboard | CPU+MB Cost Range |
|---|---|---|---|
| HEDT entry | TR 7960X / 9960X (24c) | TRX50 (ASUS TRX50-SAGE, ASRock TRX50 WS) | ~2,000–2,800 € |
| HEDT high | TR 7970X / 9970X (32c) | TRX50 | ~3,000–4,000 € |
| Workstation entry PRO | TR PRO 7955WX (16c) | WRX90 (ASUS WRX90E-SAGE SE) | ~3,500–4,500 € |
| Workstation medium PRO | TR PRO 7975WX (32c) | WRX90 | ~5,500–7,000 € |
| Workstation top PRO | TR PRO 7995WX (96c) | WRX90 | ~12,000–15,000 € |

The point to note: **the 16-core 7955WX is the "budget" PRO CPU**, and for many AI workstations, **it is the right choice**, because you buy it for the lanes and memory channels, not for the cores. Sixteen Zen 4 cores are more than enough to handle data loading for four GPUs. Paying three times as much for 96 cores that will remain at 15% utilization is wasted money — unless the workload is also CPU-intensive (rendering, simulation, compilation).

---

## 3. AMD EPYC and Intel Xeon: When to Move to Server

### 3.1 AMD EPYC (Genoa, Bergamo, Turin)

**EPYC** is AMD's server line. Recent generations, on **SP5** socket (LGA6096):

- **EPYC 9004 "Genoa"** (Zen 4, 2022): up to **96 cores** per socket.
- **EPYC 9004 "Bergamo"** (Zen 4c, density-optimized cores): up to **128 cores**.
- **EPYC "Genoa-X"**: with **3D V-Cache**, L3 cache up to over 1 GB. Useful for cache-sensitive workloads (CFD simulation, EDA).
- **EPYC 9005 "Turin"** (Zen 5, 2024): up to **128 Zen 5 cores**, or **192 cores** in Zen 5c variants.

SP5 platform features:

- **128 PCIe 5.0 lanes per socket.** In a **dual-socket** configuration, some lanes are used for inter-processor interconnection (the **Infinity Fabric** inter-socket link), so the total usable is not 256 but typically **160 lanes** (configurable: more lanes can be sacrificed for a wider inter-socket link, or fewer lanes for more free ones).
- **12 DDR5 RDIMM channels per socket** → in dual socket, **24 channels**, with theoretical aggregated bandwidth over **1 TB/s**. This is a number no desktop platform can approach.
- **Huge RAM capacity**: several TB per socket.
- **Lower frequencies** compared to Threadripper (typically 2.0–3.5 GHz base, boost up to ~4.x GHz only on low core count SKUs), because the thermal budget is divided among many more cores and because servers optimize for efficiency, not peak single-thread.

**The EPYC compromise in a word: you gain in parallelism, bandwidth, and connectivity; you lose in single-thread performance.** And this matters, because a lot of orchestration Python code (the training loop, PyTorch's DataLoader before it distributes to workers, non-vectorized preprocessing code) is **single-thread**. An EPYC workstation clocked at 2.4 GHz can be *slower* than a Ryzen 9 in all the "glue" parts of the code.

### 3.2 Intel Xeon W and Xeon Scalable

Intel divides the professional world into two:

**Xeon W** is the workstation line, conceptually equivalent to Threadripper PRO. On **LGA4677** socket and **W790** chipset:

- **Xeon W-2400** (Sapphire Rapids, up to 24 cores): **4 channels** DDR5, **64 PCIe 5.0 lanes**.
- **Xeon W-3400** (up to 56 cores): **8 channels** DDR5, **112 PCIe 5.0 lanes**.
- **Xeon W-2500 / W-3500**: the generation refresh, with slightly higher core counts (up to ~60 cores on the W-3500 line) and the same platform features. *[volatile data: check current SKU list]*

**Xeon Scalable / Xeon 6** is the server line, equivalent to EPYC:

- **Xeon 6 "Granite Rapids"** (P-core, high performance per core): 6900P series up to 128 cores, **12 channels** DDR5/MRDIMM, ~96 PCIe 5.0 lanes; 6700P series with 8 channels and ~88 lanes.
- **Xeon 6 "Sierra Forest"** (E-core, optimized for density and efficiency): very high core counts (up to 288 E-cores), but **E-cores without AVX-512 and without AMX**, thus less suitable for CPU inference.

*[volatile data: Intel's roadmap is rapidly evolving]*

#### Intel's Countermove: AMX

There's an Intel ace up its sleeve worth knowing about, because it's specifically relevant to AI: **AMX** (*Advanced Matrix Extensions*), introduced with Sapphire Rapids. It's an instruction set that adds two-dimensional **"tile" registers** and a dedicated matrix multiplication unit to the CPU. In practice, it puts a small tensor core inside each CPU core.

The result is that CPU inference of quantized models (INT8, BF16) on Xeon with AMX can be **3 to 8 times faster** than on the same CPU without AMX, at the same clock and core count, when the software supports it (oneDNN, OpenVINO, PyTorch with Intel backend). **This is the strongest argument in favor of Intel in the AI on CPU domain**, and should be kept in mind by those doing CPU-only or hybrid inference.

### 3.3 Why AMD Dominates in Core Count and Lanes Today

It's useful to understand the structural *why*, not just observe the fact.

The main reason is **architectural**, and it's called **chiplet**. Since 2017, AMD has been building its server CPUs by assembling multiple small dies:

- **CCDs** (*Core Complex Dies*): small dies that contain only the cores and cache, manufactured on the most advanced production node available (5 nm, then 4 nm, then 3 nm).
- An **IOD** (*I/O Die*): a larger die that contains the memory controllers, PCIe controllers, and interconnect infrastructure, manufactured on a cheaper, more mature node (where I/O transistors don't benefit from extreme miniaturization anyway).

The advantage is economic and statistical. **Production yield** (the percentage of functional dies on a wafer) drops exponentially with die area, because the larger it is, the more likely a random defect is to strike it. By producing **eight or twelve small dies** instead of **one giant monolithic die**, AMD achieves much higher yields, can select the best dies, and can scale the number of cores simply by **adding CCDs to the package**.

Intel used monolithic dies for years, and when it wanted to increase core count, it encountered increasingly worse yields and rising costs. It too has moved to a tile-based approach (with Sapphire Rapids and successors), but with a delay of several years, exacerbated by known difficulties in transitioning to advanced production nodes (the famous stagnation at 10 nm).

The same applies to PCIe lanes: the PCIe logic is in the IOD, a large and "cheap" die, and AMD could afford to put a lot of it there. 128 native lanes versus Intel's ~96 is not a coincidence: it's a direct consequence of the packaging choice.

**Honest Conclusion**: AMD currently has a structural advantage in cores, lanes, and memory channels. Intel maintains advantages in specific integrated accelerators (AMX), optimized software ecosystems (OpenVINO, oneAPI), certain low-latency SKUs, and sometimes in the price of used platforms. *[Volatile situation: the competitive advantage between the two can change rapidly.]*

### 3.4 Server or HEDT? The Decision for a Researcher or Small Team

Here are the concrete criteria, beyond marketing.

**Choose HEDT (Threadripper / Threadripper PRO) if you:**
- Work under a desk, in an office, and need **silence** (a 2U server with 15,000 rpm fans is unsustainable in a work environment).
- Want **high frequencies** because part of the workload is single-threaded.
- Have **1–4 GPUs**.
- Want to be able to **assemble everything yourself** with retail components.
- Need ISV certifications for professional software.

**Choose server (EPYC / Xeon Scalable) if you:**
- Need **more than 4 GPUs** or dual-socket.
- Need **extreme memory bandwidth** (CPU inference on huge models, HPC).
- Already have a **rack**, with three-phase power, cooling, and isolated noise.
- Need **multi-terabyte RAM**.
- Want to **maximize price/performance ratio on the used market**: decommissioned datacenter servers (EPYC Rome/Milan, first and second-generation Xeon Scalable) can be found at fractions of their original price, and for those who need lanes and RAM more than clock speed, they can be excellent deals. This is a path taken by many homelabbers and small labs. *[The used server market is extremely volatile in pricing.]*

**A practical observation worth its weight in gold**: if you need *a lot* of power *occasionally*, the economically sound answer is often **not to buy anything** and rent cloud instances (or on-demand GPUs from specialized providers). A local workstation is justified when usage is **continuous** (months of training, daily use), when **data cannot leave** the organization (privacy, GDPR, industrial secret), or when a rapid **iterative cycle** matters more than peak power.

---

## 4. PCIe in Workstations: In-depth

### 4.1 Review and Extension

We have already defined lanes and generations. Now let's add three distinctions that can be ignored in the consumer world but are decisive in the workstation world.

**Distinction 1: physical slot vs. electrical slot.** A slot can be **physically** x16 long (i.e., the connector has the length to accommodate an x16 card) but only 4 or 8 lanes are **electrically** wired. This is very common, and motherboards indicate it in their documentation with notations like "PCIe 5.0 x16 (x8 mode)" or "x16 slot, x4 electrical". A GPU inserted into a physical x16 but electrical x4 slot **will work**, but with a quarter of the bandwidth.

**Golden Rule**: in the motherboard manual, **always** look for the "PCIe lane distribution table" or "block diagram". It is the only document that tells the truth. The commercial description on the website does not.

**Distinction 2: shared lanes.** On many motherboards, populating a certain slot **disables or reduces** another slot or an M.2 slot. The manual contains a table like "if PCIe_2 slot is used, M.2_3 slot is disabled." On workstation platforms with 128 native lanes, these shared configurations are much less frequent — and this is precisely one of the benefits you pay for.

**Distinction 3: downward negotiation.** PCIe automatically negotiates both width (number of active lanes) and speed (generation). A disturbed signal, a low-quality riser, or a dirty contact can cause the link to drop to x8 instead of x16, or to Gen3 instead of Gen5, **without any visible error**: the system works, just slower. On Linux, this can be checked with `lspci -vv` looking for `LnkCap` (capability) and `LnkSta` (current status) lines; on NVIDIA, also with `nvidia-smi -q | grep -i pcie`. **This check should always be performed after every assembly.**

### 4.2 Why 2–4 GPUs need true x16 electrical lanes

Here we need to be precise, because there is a lot of misinformation circulating in both directions.

**When x16 is NOT necessary.** If the workload is: a single GPU, the dataset fits in VRAM or is loaded once, and training is purely GPU-bound — then PCIe bandwidth matters little. Known tests on a single GPU show differences of 2–5% between x8 and x16. In this case, spending on a workstation platform just to have x16 is irrational.

**When x16 is indispensable.** The picture changes completely in three scenarios:

**Scenario A: multi-GPU distributed training.** When using *data parallelism* (each GPU has a copy of the model and processes a different batch), at each training step the GPUs must **synchronize gradients**. The operation is called **all-reduce**: each GPU must receive the sum of gradients from all others. The data volume is equal to the model size, **at every single step**. For a 1 billion parameter model in FP16, that's 2 GB per GPU per step. With 4 GPUs and one step every 100 ms, **tens of GB/s are continuously moved across the PCIe bus**. At x8 Gen4 (15 GB/s) this becomes **the dominant bottleneck**: the GPU computes in 60 ms and then waits 150 ms for the gradients to arrive. Scaling efficiency plummets: four GPUs perform like two and a half.

**Scenario B: model parallelism / pipeline parallelism.** When the model is too large for one GPU and is split between multiple GPUs, **activations** must pass from one GPU to another at each forward and backward pass. Traffic is continuous and sensitive to latency. Here, PCIe bandwidth is even more critical.

**Scenario C: offloading and streaming.** Techniques like ZeRO-Offload or offloading part of the weights to system RAM (used when VRAM is insufficient) continuously transfer weights back and forth over PCIe. Bandwidth is everything.

**Conclusion**: if you have **one** GPU, x8 is perfectly fine. If you have **two or more and use them together**, every lane counts.

### 4.3 PCIe switches (PLX / Broadcom): what they are and when to use them

A **PCIe switch** is a chip that does what a network switch does for Ethernet: it takes a certain number of incoming lanes (*upstream*, towards the CPU) and expands them into a larger number of outgoing lanes (*downstream*, towards peripherals), dynamically routing packets.

The most well-known chips are those originally produced by **PLX Technology**, a company later acquired by **Avago/Broadcom**. Hence, by habit, "PLX" is used to refer to any PCIe switch. The historical family is **PEX 87xx/88xx** (Gen3/Gen4); recent Gen5 generations are marketed by Broadcom under names like **Atlas 3** / **PEX 89xxx**.

**How it works in practice.** Imagine a switch with 16 upstream lanes to the CPU and 32 downstream lanes to two GPUs (x16 each). The two GPUs each see an x16 link and are happy. But towards the CPU, there is only one shared x16.

**The real, and often misunderstood, benefit**: the switch **does not create bandwidth out of thin air**. If both GPUs want to communicate with the CPU simultaneously at full speed, they contend for the 16 upstream lanes. **The benefit is that the two GPUs can communicate with each other, through the switch, at full x16 speed, without ever disturbing the CPU.** This local **peer-to-peer** communication is exactly what is needed in multi-GPU training. A switch transforms four GPUs connected at x4 each into a cluster where the GPUs exchange gradients at full bandwidth among themselves, using the uplink to the CPU only to load data.

**When a switch makes sense:**
- On consumer or entry-workstation motherboards, to run 4 GPUs with an insufficient number of CPU lanes.
- On server motherboards designed for GPU density (the classic 8-GPU baseboards).
- On expansion cards (backplanes) that expand an x16 slot into multiple slots.

**When it does NOT make sense:**
- If you already have 128 native lanes (Threadripper PRO, EPYC). **Native lanes are always better**: lower latency, no contention point, no cost, no additional power consumption.
- PCIe switches are expensive (hundreds of dollars per chip), consume 10–25 W, and add latency. It's technology to be used out of necessity, not by choice.

### 4.4 Bifurcation: splitting an x16 slot

**Bifurcation** is the ability of the CPU and BIOS to **split a single x16 slot into multiple independent links**: typically x8+x8, x8+x4+x4, or **x4+x4+x4+x4**.

It's important to understand that **bifurcation is passive**. There is no additional chip: the CPU is simply instructed to treat those 16 lanes as four separate 4-lane links. The CPU's PCIe chip natively supports this configuration; the BIOS exposes the option (usually in the "PCIe Bifurcation" or "PCIe Slot Configuration" menu).

**The main application in AI workstations**: **quad M.2 cards**. These are passive PCIe x16 expansion cards, without any controller on board, that expose four M.2 slots. Each NVMe SSD uses 4 lanes, and the total is exactly 16. They cost a few tens of euros (the PCB, connectors, and a heatsink) and allow you to add **four full-speed NVMe SSDs** using a single slot.

Common examples: ASUS Hyper M.2 X16, Gigabyte AORUS Gen4 AIC, ASRock Hyper Quad M.2. Some workstation motherboards already include dedicated connectors (SlimSAS, MCIO) that expose lanes in a similar way.

**Beware of the classic trap**: if the BIOS **does not support bifurcation** on that slot, a passive quad M.2 card will show **only one SSD** (the first one), or none at all. This is not a defect of the card: it is the platform that cannot split the lanes. There are also **active** quad M.2 cards, which contain a PCIe switch and work everywhere — but cost 5–10 times as much. **Before buying a passive quad M.2 card, check the motherboard manual to ensure the slot supports x4x4x4x4 mode.** Threadripper/PRO and workstation platforms almost always support it; consumer platforms often do not, or only on one slot.

### 4.5 P2P (peer-to-peer) over PCIe vs NVLink

**P2P** (peer-to-peer) is the ability of two GPUs to **exchange data directly**, without routing data through system RAM. Without P2P, a copy from GPU0 to GPU1 requires: GPU0 → system RAM → GPU1. With P2P: GPU0 → GPU1, directly on the PCIe bus. The savings are in bandwidth (one copy instead of two), latency, and CPU utilization.

This capability is the basis of collective communication libraries like **NCCL** (*NVIDIA Collective Communications Library*), which is what PyTorch and every distributed training framework use under the hood for all-reduce of gradients.

**The problem**: NVIDIA has **disabled P2P on GeForce GPUs** starting from recent generations (from RTX 30 onwards, support has been progressively removed; on RTX 40 and 50, P2P via official drivers **is not available**). This is a commercial segmentation choice, not a technical one: the silicon could do it. The consequence is that on a workstation with four RTX 5090s, NCCL is forced to use the path through system RAM, with significant costs in bandwidth and latency.

*(A well-known community curiosity: there is a modified driver, developed by the tinygrad team, that re-enables P2P on RTX 4090s. It works, but it is unsupported, fragile with respect to updates, and unsuitable for a production environment.)*

**NVLink** is NVIDIA's proprietary alternative: a **dedicated** interconnect, separate from PCIe, with much higher bandwidth (on the order of hundreds of GB/s per link, compared to 63 GB/s for an x16 Gen5) and much lower latency. Historically available via a physical "bridge" between two cards on Quadro/RTX A-series, and via NVSwitch in server configurations (SXM).

**The current situation, to be well understood** *[volatile data]*: NVIDIA has **removed the NVLink connector from professional PCIe format cards** starting with the Ada Lovelace generation (RTX 6000 Ada and later do not have the bridge). Today, "true" NVLink is reserved for GPUs in **SXM format** (modules mounted directly on the baseboard in server systems like DGX/HGX) and rack-scale platforms.

**Practical consequence**, and this is the message that brings this section into the CPU chapter: **on a workstation with PCIe format GPUs — which is what the vast majority of us will build — inter-GPU communication goes through PCIe. Period.** There is no NVLink to save a configuration with insufficient lanes. And this is precisely why the CPU's PCIe lanes are not a specification detail: **they are the cluster's interconnect**. It is the ultimate technical reason why Threadripper PRO exists.

*(We will delve into NVLink, NVSwitch, SXM formats, and multi-GPU topologies in the chapter dedicated to GPUs.)*

---

## 5. Choosing the Workstation Motherboard

The motherboard, in this segment, is not an accessory: it is **the document that defines what the machine will be able to do**. Here are the criteria, in order of importance.

### 5.1 Number of Physical AND Electrical x16 Slots

Already covered, but it bears repeating because it is the number one criterion. Look in the manual (not on the commercial website) for the **block diagram** and verify how many lanes are actually wired to each slot and in which generation. A board with "5 PCIe x16 slots" that actually has two electrical x16, one x8, and two x4 is a different product from one with five true x16 slots.

### 5.2 Slot Spacing (the Most Underestimated Criterion)

This is, statistically, **the most frequent mistake by far** for those building their first multi-GPU workstation.

Modern high-end GPUs occupy **2, 2.5, or even 3 slots in thickness** due to oversized coolers. A custom RTX 5090 can be a 3.5-slot card. If the motherboard has x16 slots spaced by **two slots**, and the GPU occupies **three**, **the second GPU simply won't physically fit**. This is not a matter of performance: it's a matter of geometry.

And even when they do fit, there are two further problems:
- **Thermal Suffocation**: two adjacent GPUs with open-fan coolers will have the intake of the second one a millimeter from the backplate of the first. Temperatures rise by 15–25 °C, and the GPUs go into *thermal throttling* (automatic frequency reduction to limit temperature). Performance plummets, and noise explodes.
- **Weight and Flex**: a modern GPU weighs 2 kg. Four GPUs mounted cantilevered will flex the motherboard and the GPUs' own PCBs. Support brackets are needed.

**How to solve this:**
1. Choose motherboards designed for multiple GPUs: reference WRX90 boards have **7 slots with calculated spacing** specifically to accommodate 4 dual/triple-slot cards in alternating slots (1, 3, 5, 7).
2. Prefer **blower-style** GPUs (turbine, with air expelled out of the case) or **2-slot** format — these are the formats of professional cards (RTX PRO/A-series are designed to be stacked).
3. Use quality **PCIe risers** (certified for the correct generation: a Gen3 riser on a Gen5 link causes silent degradation) to move GPUs to ventilated positions, with a **mining/open-frame case** or a large workstation case.

**Practical rule**: before buying, **measure**. Take the thickness of the GPU (in millimeters, not "in slots") and the distance between the slots on the motherboard. A standard PCIe slot has a **20.32 mm** (0.8 inch) pitch.

### 5.3 RDIMM Channels and Slots

Verify **how many DIMM slots** there are and **how they are organized into channels**. A WRX90 board with 8 DIMM slots on 8 channels gives one module per channel: maximum frequency, ideal configuration. Some boards have more slots per channel (2 DIMMs per channel, "2DPC"): they allow for more capacity but often at reduced frequency.

**Critical point that ruins many builds**: on 8-channel platforms, **populating only 4 slots halves memory bandwidth**. If you buy a WRX90 and put in 4x 32GB modules instead of 8x 16GB modules, you have the same capacity (128GB) but **half the bandwidth**. You paid for 8 channels and are only using 4. **Always fill all channels.** This single rule is perhaps the most valuable advice in the entire chapter, because the error is invisible: the system works, everything seems fine, and it simply runs at half speed.

### 5.4 Network: 10G Ethernet and beyond

High-end workstation motherboards typically integrate **dual 10 GbE** (10 gigabits per second). Why it matters:

- **Datasets on NAS/network storage**: A 10 TB dataset won't fit on a local SSD. It resides on a storage server. With 1 GbE (~110 MB/s real), loading is impossible. With 10 GbE (~1.1 GB/s real), it becomes feasible.
- **Distributed training across machines**: If you have two workstations and want to distribute training, the network becomes the interconnect, and it's already very slow compared to PCIe. 10 GbE is the minimum; for serious work, 25/100 GbE or **InfiniBand** (a very low-latency networking technology dominant in HPC, which supports **RDMA** — *Remote Direct Memory Access* — i.e., the ability to write directly into another machine's memory without involving the CPU) is needed.

If the motherboard doesn't have integrated 10 GbE, remember that a 10G NIC occupies one PCIe slot and 4 lanes. It must be counted in the lane budget.

### 5.5 IPMI / BMC: remote management

**BMC** stands for *Baseboard Management Controller*: it is an **autonomous microcontroller** soldered onto the motherboard (the most common is the ASPEED AST2500/AST2600), with its own CPU, its own RAM, its own firmware, and **its own Ethernet port**. It works **even when the machine is off**, as long as the power supply is connected to the electrical grid.

**IPMI** (*Intelligent Platform Management Interface*) is the standard protocol for communication.

What it allows you to do, from a browser, from anywhere in the world:
- **Turn on, turn off, and restart** the machine.
- View the **console video** as if you were sitting in front of the monitor — **including the BIOS and POST** (*Power-On Self-Test*, the diagnostic sequence at startup). This function is called **KVM over IP** (*Keyboard, Video, Mouse over IP*).
- **Mount a remote ISO image** as if it were a USB stick inserted into the machine: you can therefore **reinstall the operating system remotely**.
- Read **all sensors**: temperatures, voltages, fan speeds, power consumption.
- Consult **hardware logs** (SEL, *System Event Log*), including **corrected ECC events**.

**Why it's crucial in an AI workstation**: because an AI workstation is a machine you leave on for days, often in another room, in the basement, or in a small rack. When the training gets stuck at three in the morning and the machine doesn't respond via SSH, without BMC you have to get up, go there physically, and connect a monitor. With BMC, you open the browser and see the kernel panic screen. **The first time it saves you, the BMC has paid for itself.**

BMC is almost always present on **server** boards (Supermicro, ASRock Rack, Gigabyte, Tyan) and on some high-end workstations (some WRX90). It is not present on consumer boards or most "desktop" TRX50 boards.

### 5.6 Reference Brands and Models

*[Models and availability volatile]*

| Brand | Positioning | Typical Models | Notes |
|---|---|---|---|
| **ASUS Pro WS** | "Desktop-friendly" workstation | Pro WS WRX90E-SAGE SE, Pro WS TRX50-SAGE WIFI, Pro WS W790E-SAGE SE | Industry benchmark for multi-GPU workstations. The WRX90E-SAGE SE with 7 full x16 slots is effectively the standard. |
| **Supermicro** | Server and server-grade workstations | M13SWA-TF (sTR5), H13/X13 series | Server quality and reliability, BMC/IPMI as standard, but spartan BIOS and support oriented towards businesses, not individuals. |
| **ASRock Rack** | Servers, excellent value for money | WRX90D8-2T, TRX50D8-2L2T | Server form factor (SSI-EEB or proprietary), IPMI, often more affordable. Pay attention to case form factor. |
| **Gigabyte (Server)** | Servers and barebones | MZ73/MZ33 (EPYC), TRX50 AERO D | Good range, from complete barebones to single boards. |
| **Tyan** | Servers, HPC niche | Various | Less common in retail in Europe. |

**Practical advice**: If you are building a workstation to keep in the office and want a "desktop" experience (usable BIOS, quiet fans, standard ATX/E-ATX form factor, consumer RMA support), go for **ASUS Pro WS**. If you are building something that will live in a rack or a technical room and want BMC and 24/7 reliability, go for **Supermicro** or **ASRock Rack**, but be prepared for a more "industrial" experience.

### 5.7 Power Supply: Multiple EPS and Server-Grade PSUs

An aspect that first-time builders systematically underestimate.

**The EPS12V connector** (often simply called "EPS" or "CPU power") is the **8-pin** connector that supplies 12 volts to the CPU's **VRM** section. **VRM** stands for *Voltage Regulator Module*: it is the circuit on the motherboard that converts the 12 V from the power supply into the much lower voltage (approximately 1.0–1.3 V) and very high current (hundreds of amperes) that the CPU requires. The VRM is made of **phases** (parallel conversion stages): more phases mean more current can be delivered with less thermal stress on each.

A Threadripper CPU with a 350 W TDP, during transients, can draw well over 400 W. With a VRM efficiency of around 90%, the power supply must provide more than 450 W **on the CPU rail alone**. A single 8-pin EPS connector is specified for approximately **300 W**. **This is not enough.** This is why sTR5 motherboards have **two (or even three) 8-pin EPS connectors**, and they **must all be connected**. Connecting only one will, at best, result in power-on with warnings and limited performance; at worst, instability under load or overheating of the connectors.

The overall power budget of a multi-GPU workstation:

| Component | Indicative Consumption |
|---|---|
| Threadripper PRO CPU (350 W TDP) | 400–450 W peak |
| High-end GPUs (e.g., 450–600 W TDP each) | 4 × 500 = **2,000 W** |
| RAM (8 RDIMM modules) | ~50 W |
| NVMe (4) | ~40 W |
| Fans, pumps, motherboard | ~100 W |
| **Total Peak** | **~2,600 W** |

With a 20% margin for transients (modern GPUs have instantaneous peaks — *transient spikes* — that can exceed nominal consumption by 50–100% for a few milliseconds, and a power supply that doesn't absorb them will trigger OCP/OPP protection and shut down the machine), this reaches **3,000 W or more**.

Here we encounter two obstacles:

**Wall 1: The electrical outlet.** In Italy, a standard household outlet on a 16 A, 230 V circuit theoretically provides ~3,680 W, but household circuits and standard supply contracts (3 kW or 4.5 kW) cannot handle a 3 kW machine **plus everything else in the house**. A dedicated circuit is needed, and often an increase in contractual power. In 110 V countries (USA), the problem is even more severe: a 15 A, 120 V outlet provides 1,800 W theoretical, ~1,440 W in continuous use – **which is why 4-GPU workstations in the USA almost always require two separate circuits or a 240 V outlet**.

**Wall 2: The power supply.** Consumer ATX power supplies typically reach 1,600–2,000 W *[volatile]*. To go beyond this, the options are:
- **Dual ATX power supply** synchronized with an "add2psu" (an adapter that turns on the second PSU when the first one turns on, by bridging the PS_ON signal). It works, is common in mining and homelab environments, but must be done carefully: **never power the same card from two different PSUs** (for example: PCIe slot from PSU 1 and additional GPU connectors from PSU 2), because the grounds can have different potentials and damage the hardware.
- **Server-grade power supplies** (Delta, Murata, HP/Dell "Common Slot" power supplies) with breakout boards, often 240 V input, which can reach 2,400–3,000 W in a single module. This is the professional route, but requires attention to noise (the fans of these power supplies are designed for a rack, not an office).
- **Reduce consumption via software**: with `nvidia-smi -pl <watt>` you can set a power limit per GPU. Reducing a GPU from 500 W to 350 W (−30%) typically costs only 5–10% in performance, because the power/performance curve is highly non-linear at the high end. **On a 4-GPU workstation, this single command line can reduce consumption from 2,000 to 1,400 W practically for free.** This is a standard technique in serious labs, not a poor man's compromise.

### 5.8 sTR5 socket cooling

A note that deserves its own space, because it's a beginner's mistake with immediate consequences.

The IHS of an sTR5 CPU is **much larger** than that of an AM5 CPU. A consumer cooler, even the best one, has a base designed to cover ~40×40 mm. On sTR5, it would leave much of the surface uncovered, and – even worse – with Threadripper, the dies (the CCDs) are **distributed across the entire package area**, not concentrated in the center. A cooler that only covers the center leaves the peripheral CCDs to cook.

**Specific solutions are needed for sTR5:**
- **Air cooling**: Noctua NH-U14S TR5-SP6 (and variants), IceGiant ProSiphon. These are coolers with enormous bases and a large number of distributed heatpipes.
- **AIO liquid cooling** (*All-In-One*, the closed pump+radiator system): SilverStone XE360-TR5, Arctic Liquid Freezer for TR5 *[volatile availability]*. **Crucial**: the *cold plate* (the cold plate in contact with the CPU) must be **full-coverage size** for sTR5. An AIO with a consumer cold plate, even mounted with adapters, **will not work**.
- **Custom loop**: with a dedicated sTR5 waterblock.

And, I repeat because it's important: mounting requires the **torque screwdriver** supplied with the CPU and **tightening in the numbered order** stamped on the socket. It is not a formality.

---

## 6. How to size the CPU for the AI workload

Let's get to the practical question: **which CPU should I actually buy?** The answer depends on *what* the machine does.

### 6.1 GPU-bound Training: The CPU is for Data Loading

In typical computer vision training or models with large datasets, the cycle is:

1. A **worker** (a separate process, managed in PyTorch by `DataLoader` with `num_workers`) reads a file from disk.
2. It **decodes** it (a JPEG needs to be decompressed: this is a CPU-intensive operation, and for a high-resolution image, it can take milliseconds).
3. It applies **transformations** (resize, crop, flip, color jitter, normalization): more CPU work.
4. It packages the result into a tensor and copies it to **pinned memory** (non-pageable, "locked" memory, from which the GPU's DMA can read directly).
5. The tensor is transferred to the GPU via PCIe.

If the workers don't keep up, the GPU waits. This is **the** most common bottleneck in unbalanced workstations and is easily diagnosable: `nvidia-smi dmon` showing GPU utilization fluctuating between 30% and 100% instead of staying flat at 95% is the classic symptom.

**The rule of thumb**, shared in the community and consistent with reference system configurations (NVIDIA DGX systems historically have about 8 physical cores per GPU):

> **4–8 physical cores per GPU**, depending on the intensity of the preprocessing pipeline.

- **Light pipeline** (already preprocessed data, binary tensors, `.npy` or Arrow/WebDataset format, no decoding): **2–4 cores per GPU** are sufficient.
- **Medium pipeline** (standard JPEGs, classic augmentations): **4–6 cores per GPU**.
- **Heavy pipeline** (very high-resolution images, video, complex augmentations, audio decoding): **8+ cores per GPU**, or — a better solution — **move preprocessing to the GPU** with libraries like NVIDIA DALI (which performs JPEG decoding in hardware, on the GPU's own NVDEC/nvJPEG decoders) or with accelerated video decoding.

**Important corollary**: if your problem is data loading, the *best* answer is often not to buy more cores, but to **preprocess the dataset once** and save it in a ready-to-use format (tensors, TFRecord, WebDataset shard). It's free and shifts the bottleneck to the disk, where an NVMe handles it effortlessly.

### 6.2 When Cores Really Matter

There are workloads where the CPU **is** the computation, not just support:

**CPU Inference.** As discussed, this is dominated by memory bandwidth during the generation phase. But in the **prefill** phase (processing the initial prompt, which is a large matrix multiplication), cores matter a lot, and vector instructions (**AVX-512**, **AMX** on Intel) make an enormous difference. A CPU with AMX can perform prefill in a fraction of the time. **If CPU inference is central to your workflow, seriously consider Intel Xeon with AMX or AMD platforms with many memory channels.**

**Classic Preprocessing and Feature Engineering.** Pandas/Polars/DuckDB pipelines on datasets of tens or hundreds of GB. Here, cores scale linearly (if the code is parallelized), and memory bandwidth matters greatly. This is the use case where a 32 or 64-core Threadripper truly shines.

**Tokenization.** Tokenizing a corpus of hundreds of GB of text to train a language model is a **massively parallel and CPU-bound** operation. With Hugging Face's `tokenizers` library (written in Rust, which releases the GIL), 64 cores can do in one hour what 8 cores do in eight hours.

**Compilation.** If you work on CUDA code, Triton kernels, compiling PyTorch from source, or compiling models with TensorRT: `make -j64` on a Threadripper is a revelation. Compilation is perhaps the workload that scales best with cores.

**Simulation and classical processing.** CFD, scientific computing, 3D rendering to generate synthetic datasets, combinatorial optimization: all CPU-bound.

### 6.3 Typical Configurations by Budget Range

Here is the operational core. *[Indicative, volatile prices, referring to the European market. Always verify.]*

#### ENTRY Tier — Single-GPU Workstation (~€2,500–€5,000)

**The most important message: here the consumer WINS.**

| Component | Choice |
|---|---|
| CPU | AMD Ryzen 9 9950X (16c) or Intel Core Ultra 9 285K |
| Motherboard | High-end X870E / Z890 |
| RAM | 64–128 GB DDR5 (2 modules to avoid frequency degradation; 4 modules if capacity is needed) |
| GPU | 1 × RTX 5090 (32 GB) or RTX PRO 4500/5000 |
| Storage | 2 × NVMe Gen4/Gen5 (1 TB system + 2–4 TB dataset) |
| PSU | 1,000–1,200 W |
| Notes | No certified ECC. If ECC is needed, the only alternative is to upgrade the platform. |

**Who it's for**: those doing fine-tuning with LoRA/QLoRA, local inference, prototyping, computer vision on medium datasets, courses, and individual research. **This covers 70–80% of real-world cases.** Do not upgrade the platform if you don't have a reason.

#### MID Tier — 2 GPU Workstation (~€7,000–€12,000)

| Component | Choice |
|---|---|
| CPU | **AMD Threadripper 7960X / 9960X** (24 cores) |
| Motherboard | TRX50 (ASUS Pro WS TRX50-SAGE WIFI, ASRock TRX50 WS) |
| RAM | 128–256 GB DDR5 RDIMM ECC, **4 modules on 4 channels** |
| GPU | 2 × RTX 5090 or 2 × RTX PRO 6000 (at **full x16 PCIe 5.0**) |
| Storage | 2–4 × NVMe Gen5 |
| PSU | 1,600 W+ (or dual PSU) |
| Cooling | AIO 360 mm specific sTR5 |

**Who it's for**: the serious researcher, small teams, those who need ECC and two GPUs that truly communicate. **24 cores are more than enough**: 12 per GPU, well above the rule of thumb.

#### HIGH-END Tier — 4+ GPU Workstation (~€20,000–€50,000)

| Component | Choice |
|---|---|
| CPU | **AMD Threadripper PRO 7955WX / 7965WX / 9955WX** (16–24 cores) — **or 7975WX/7995WX if the workload is also CPU-bound** |
| Motherboard | **WRX90** (ASUS Pro WS WRX90E-SAGE SE, ASRock Rack WRX90D8-2T) |
| RAM | 256 GB–1 TB DDR5 RDIMM ECC, **8 modules on 8 channels — fill all channels!** |
| GPU | 4 × RTX PRO 6000 Blackwell (96 GB each) or 4 × RTX 5090 |
| Storage | 4+ × NVMe Gen5 (one for the system, an array for datasets) |
| Network | Integrated 10/25 GbE or dedicated NIC |
| PSU | 2 × 1,600 W, or server-grade 3,000 W PSU on a dedicated circuit |
| Case | Large workstation chassis or open-frame |
| Notes | **Check slot spacing against the thickness of the chosen GPUs.** |

**Pay attention to the central point**: in the high-end segment, **the PRO CPU is purchased for its 128 lanes and 8 channels, not for its cores**. A **16-core 7955WX** costs a fraction of the 7995WX and provides **the same 128 lanes and 8 channels**. Sixteen cores for four GPUs = 4 cores per GPU: at the low end of the rule of thumb, but sufficient with a well-optimized pipeline (preprocessed dataset, DALI). If preprocessing is heavy, move up to the **7965WX (24c)** or **7975WX (32c)**. The **7995WX (96c)** only makes sense **if the workload is genuinely CPU-intensive** (rendering, simulation, massive compilation) — otherwise, it's ~€9,500–10,000+ of inactive silicon.

#### EXTREME Tier — Server (€50,000+)

Here, we move from workstations to servers: **EPYC 9004/9005** or **Xeon 6**, dual socket, 8 GPUs on the baseboard, rack, three-phase power, direct liquid cooling. This is a different category, with different suppliers (Supermicro, Dell, HPE, Lenovo, Gigabyte) and different purchasing logic (you buy a system, you don't assemble it). It deserves a chapter of its own.

### 6.4 The three-line sizing rule

If I had to condense all of section 6:

1. **Count the GPUs.** 1 → consumer. 2 → Threadripper. 3+ → Threadripper PRO. 8+ → server.
2. **Count the necessary lanes.** GPU × 16 + NVMe × 4 + NIC. If the total exceeds the CPU's lanes, upgrade the platform.
3. **Count the cores.** 4–8 per GPU. If the result is less than the cores of the CPU you need for the lanes, **do not pay for extra cores**: get the cheapest SKU for that platform.

---

## 7. Common mistakes

We collect them here, in order of frequency and severity, because each one costs real money.

### Mistake 1 — Buying Threadripper PRO for a single GPU

**The mistake.** "I want a serious AI workstation, so I'll get a Threadripper PRO." Then only one GPU is installed.

**Why it's a mistake.** The PRO platform costs €3,000–€5,000 more (CPU + motherboard + RDIMM RAM) and provides 128 lanes, of which you'll use 20. In addition, the single-thread frequencies of a Threadripper are **lower** than those of a Ryzen 9 of the same generation, and your orchestration Python code — which is single-thread — will run **slower**. You spent more to go slower in the part you use every day.

**What to do instead.** Get a Ryzen 9 9950X or a Core Ultra 9, invest the difference in **a better GPU** or **more RAM**. A single RTX PRO 6000 with 96 GB of VRAM allows you to train models that four 32 GB RTX 5090s cannot contain without complex partitioning techniques.

### Mistake 2 — Ignoring slot spacing

**The mistake.** Buying four triple-slot custom GPUs and a motherboard with slots spaced by two.

**Why it's a mistake.** The GPUs **won't fit**. Or they'll fit but thermally choke each other and throttle. The cost is: returning the GPUs (if possible), or buying risers and a different case, or living with 20–30% reduced performance.

**What to do instead.** **Measure in millimeters before buying.** Check the slot layout in the motherboard manual. Prefer **blower** or professional 2-slot GPUs for dense configurations. The standard pitch of a PCIe slot is 20.32 mm.

### Mistake 3 — Wrong RAM

This mistake has three variants, all common.

**Variant A: UDIMM on an RDIMM platform.** Buying a nice DDR5 gaming kit only to find out that the TRX50/WRX90 board **doesn't even POST**. Threadripper (even non-PRO), Threadripper PRO, EPYC, and Xeon W **require ECC RDIMMs**. It's not an option, it's a requirement.

**Variant B: Not filling all channels.** Buying 4x 64 GB modules (256 GB total) for an 8-channel WRX90. It works, you have the capacity you wanted — **and half the memory bandwidth**. You paid for an 8-channel platform and are only using 4. **Always buy a number of modules equal to the number of channels** (or a multiple).

**Variant C: No ECC where it's needed.** Training for three days on a machine without ECC and never understanding why the loss occasionally goes to NaN.

### Error 4 — Underestimating power supply

**The error.** Connecting only one EPS connector on a board that has two. Buying a 1,000 W power supply for a machine that consumes 2,000 W. Ignoring transient GPU peaks. Plugging everything into a household outlet on a 3 kW contract.

**Why it's an error.** The symptoms are the worst possible: **random shutdowns under load**, occurring after hours of training, with no logs, no reproducible pattern. Weeks are spent blaming software, drivers, the kernel. It's the power supply going into protection.

**What to do instead.** Balance the power budget with a 25–30% margin. Connect **all** EPS connectors. Seriously consider **power limiting** GPUs (`nvidia-smi -pl`): the cost/benefit ratio is unbeatable. Check the electrical circuit of the room.

### Error 5 — Underestimating sTR5 cooling

**The error.** Using an AM5 cooler with an adapter, or an AIO with a consumer cold plate.

**Why it's an error.** The sTR5 package is huge and the CCDs are distributed. A small cold plate cools the center and leaves the peripheral dies at throttling temperatures. Result: a €5,000 CPU running at 60% of its frequency.

**What to do instead.** **sTR5 specific** cooler, with a full-coverage base. Tighten with a **torque screwdriver**, in the **numbered order**.

### Error 6 — Trusting the commercial description of slots

**The error.** Buying a motherboard because it "has 4 PCIe x16 slots" and finding out that two are x4 electrical and connected to the chipset.

**What to do instead.** Download the **PDF manual** and look for the **block diagram** and the **PCIe lane distribution table**. If the manufacturer doesn't publish them, it's a red flag.

### Error 7 — Overestimating core needs, underestimating RAM needs

**The error.** Spending on a 96-core and putting in 128 GB of RAM.

**Why it's an error.** The 96 cores will remain at 10% during training. The 128 GB will run out quickly, because the rule of thumb is **system RAM ≥ 1.5–2× total VRAM** (to hold the dataset in cache, worker buffers, pinned memory, and any offloading). With 4x 48 GB GPUs (192 GB VRAM), 128 GB of RAM is **insufficient**: at least 384 GB are needed.

**What to do instead.** Cheaper CPU SKU of the right platform + more RAM.

### Error 8 — Buying hardware when the cloud was needed

**The error.** Spending €30,000 on a workstation that will be used at full load two weeks a year.

**What to do instead.** Make an honest assessment of the hours/GPUs needed per year and compare it with the cost of cloud instances or on-demand GPU providers. A local workstation is justified for **continuous use**, **sensitive data that cannot leave the premises**, or **rapid daily iteration**. Not for prestige.

---

## Operational Summary: AI Workstation CPU Decision Checklist

To be used **in this order**. Each step depends on the previous one.

### PHASE 1 — Define the Workload (before looking at a single product)

- [ ] **How many GPUs** will I install, today and realistically within 2 years? (This is the determining question.)
- [ ] Is the workload **GPU-bound** (training/inference on GPU) or is there a significant **CPU-bound** component (heavy preprocessing, tokenization, compilation, simulation, CPU inference)?
- [ ] Will the GPUs need to **communicate with each other** (distributed training) or will they work independently (e.g., parallel inference on different requests)? If they communicate, lanes are critical; if not, much less so.
- [ ] How much **total VRAM** will I have? → System RAM should be **1.5–2 times** that amount.
- [ ] Is the **dataset** on local disk or on the network? → If on the network, 10 GbE or more is needed.
- [ ] Do I really need local hardware, or is the **cloud** more economical for my usage profile?

### PHASE 2 — Calculate Necessary PCIe Lanes

- [ ] GPU × 16 = ______ lanes
- [ ] NVMe × 4 = ______ lanes
- [ ] NIC (4 for 10G, 8–16 for 25/100G) = ______ lanes
- [ ] Other cards (HBA, capture, accelerators) = ______ lanes
- [ ] **TOTAL lanes needed = ______**

### PHASE 3 — Choose the Platform Based on the Total

| Lanes Needed | GPUs | Platform |
|---|---|---|
| ≤ 24 | 1 | **Consumer** (Ryzen 9 / Core Ultra) — and it's perfectly fine |
| 25–48 | 2 | **Threadripper non-PRO on TRX50** |
| 49–128 | 3–7 | **Threadripper PRO on WRX90** |
| > 128 | 8+ | **EPYC / Xeon Scalable, server** |

- [ ] Is **certified ECC** needed? → If yes, Threadripper minimum (RDIMM ECC mandatory).
- [ ] Is **more than 256 GB of RAM** needed? → If yes, Threadripper minimum.
- [ ] Is **maximum memory bandwidth** needed (CPU inference on large LLMs)? → 8 channels (WRX90) or 12 channels (EPYC).
- [ ] Is **remote management** needed? → Motherboard with BMC/IPMI.

### PHASE 4 — Choose the CPU SKU within the Platform

- [ ] Calculate necessary cores: **4–8 physical cores per GPU**.
- [ ] Add cores if the workload has a real CPU-bound component (tokenization, compilation, simulation, heavy preprocessing not transferable to GPU).
- [ ] **If the result is less than the cores of the cheapest SKU on that platform, take the cheapest SKU.** Don't pay for cores that will remain idle. (Example: 4 GPUs + light pipeline → **7955WX/9955WX with 16 cores** on WRX90, not the 7995WX.)
- [ ] Check the **suffix**: **WX = PRO** (8 channels, 128 lanes). **X without W = non-PRO** (4 channels, 48 lanes). **Do not confuse them.**
- [ ] If CPU inference is central → consider **Intel Xeon with AMX**.

### PHASE 5 — Choose the Motherboard

- [ ] Download the **PDF manual** and find the **block diagram** / **PCIe lane distribution table**. Do not trust the product page.
- [ ] Count the **ELECTRICAL x16** slots (not just physical) and their PCIe generation.
- [ ] **Measure the slot spacing** (standard pitch: 20.32 mm per slot) and compare it with the **thickness in mm** of the GPUs you will buy.
- [ ] Verify the number of **DIMM slots** and their channel organization. Confirm that you will buy **one module per channel**.
- [ ] Verify **bifurcation** support (x4x4x4x4) if you plan to use passive quad M.2 cards.
- [ ] Verify the presence of integrated **10 GbE** (or account for lanes for a NIC).
- [ ] Verify the presence of **BMC/IPMI** if the machine will not be within reach.
- [ ] Count the required **EPS 8-pin** connectors and verify that your power supply has all of them.
- [ ] Verify the **form factor** (E-ATX, SSI-EEB, proprietary) and compatibility with the chosen case.

### PHASE 6 — Memory

- [ ] Type: **RDIMM ECC** on all Threadripper/PRO/EPYC/Xeon W platforms. No exceptions.
- [ ] Number of modules = **number of channels** (4 on TRX50, 8 on WRX90, 12 on EPYC). **Do not leave channels empty.**
- [ ] Total capacity ≥ **1.5–2 × total VRAM**.
- [ ] Check the motherboard's **QVL** (Qualified Vendor List) before buying modules.

### PHASE 7 — Power and cooling

- [ ] Power budget: CPU + (GPU × number) + RAM + storage + fans, **+25–30% margin for transients**.
- [ ] Verify that the **room's electrical circuit** can handle it (in Italy: dedicated contract and circuit for machines above ~2 kW).
- [ ] Consider **power limiting** for GPUs (`nvidia-smi -pl`): −30% consumption for −5/10% performance.
- [ ] Connect **all** EPS connectors on the motherboard.
- [ ] **sTR5-specific cooler** with full-coverage base. **Never use AM5/LGA1700 adapters.**
- [ ] Mount the CPU with the **supplied torque screwdriver**, in the **numbered order** stamped on the socket.

### PHASE 8 — Post-assembly verification (never skip it)

- [ ] `lspci -vv` → compare **LnkCap** (capability) with **LnkSta** (actual state) for each GPU and NVMe. If a link is negotiated at x8 instead of x16, or at Gen3 instead of Gen5, **there is a hardware problem to solve now**, not in three months.
- [ ] `nvidia-smi -q | grep -i pcie` → verify GPU link width and generation.
- [ ] Verify in BIOS/OS that **ECC is active and reports events** (on Linux: `edac-util`, `dmidecode -t memory`, the BMC's SEL log).
- [ ] Run a **combined stress test** (CPU + all GPUs at 100% simultaneously) for at least an hour and monitor temperatures, clocks (to verify absence of throttling), and power stability.
- [ ] Verify **real memory bandwidth** with a benchmark (STREAM, Intel's `mlc`): if it's half of what's expected, you've populated the channels incorrectly.
- [ ] Run a **real training** and observe **GPU utilization**: if it's not stably above 90%, you have a bottleneck to diagnose (almost always: data loading, or PCIe link negotiated downwards).

---

*A final note on data volatility: prices, availability, CPU generations, and compatibility matrices change rapidly in this industry. All cited models, prices, and SKU counts should be verified against official manufacturer documentation at the time of purchase. Architectural principles—lanes as currency, channels as bandwidth, ECC as insurance, spacing as a physical constraint—are stable, however, and will remain valid even as product names change.*

---

*In the next chapter, we will discuss GPUs for AI: architectures, VRAM, tensor cores, NVLink and NVSwitch, SXM versus PCIe form factors, and how to choose the right accelerator for your workload.*

---

[← Previous](09-pc-assembly.html) · [All chapters](./) · [Next →](11-nvidia-gpu-ai.html)
