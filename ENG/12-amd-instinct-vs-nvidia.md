---
title: "Chapter 12 - AMD Instinct GPUs for AI and the Nvidia vs. AMD Comparison in the Datacenter"
parent: "English Edition"
nav_order: 12
---

<details open markdown="block">
  <summary>Chapter contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

> **Note on Data.** This chapter touches upon the most volatile hardware segment in the entire IT industry. Architectures succeed each other annually, prices have no public list and are negotiated contract by contract, and roadmaps are rewritten every six months. All reported numbers are updated to mid-2026, and where the data is inherently unstable (prices, availability, generations not yet shipped in volume), I explicitly mark it with the phrase **[VOLATILE DATA]**. At the time of writing: MI300X and MI325X are mature, widely available products; the MI350 series (MI350X/MI355X) has been in production since mid-2025; and the MI400 series with the "Helios" rack-scale platform was unveiled at CES in January 2026, with volumes expected in the second half of 2026. On the Nvidia side, Blackwell (B200/GB200) and Blackwell Ultra (B300/GB300) are in the field, while Vera Rubin (VR200) has entered production with volume shipments expected in the second half of 2026. The reasoning method, however—how to read a datasheet, how to evaluate an accelerator, what mistakes to avoid—will remain valid much longer than the numbers.

---

## 1. The AMD Instinct Line

### 1.1 What it is, in elementary terms

When we talk about "GPU" (Graphics Processing Unit), our minds immediately go to the video card plugged into the PC's PCIe slot, with fans and an HDMI output. AMD Instincts are not that. They are accelerator processors that share architectural DNA with video cards—thousands of parallel computing units executing the same instruction on many different data—but have shed everything needed to draw pixels: they have no video outputs, no rasterization units, and (in recent generations) no ray tracing engines. They are matrix multiplication machines, and nothing else.

The Instinct line was launched in 2016 as AMD's answer to Nvidia's Tesla line (later simply rebranded "Data Center GPU"). For many years, it was a weak response: honest hardware, unusable software, irrelevant market share. The turning point came in 2023-2024, when the global scarcity of accelerators for generative AI suddenly made the second vendor interesting—and AMD found itself, for the first time, with a product (MI300X) that, on a crucial metric, onboard memory, beat the world champion.

**Definition of two acronyms we will use continuously.** *HBM* (High Bandwidth Memory) is DRAM stacked vertically in towers of multiple dies, soldered on the same substrate as the processor and connected to it with very wide buses (thousands of traces). It is very expensive, consumes a lot of power, but offers an order of magnitude higher bandwidth than the GDDR in gaming cards. *TDP* (Thermal Design Power) is the power in watts that the cooling system must be able to dissipate continuously; AMD in the datacenter more often uses the term *TBP* (Total Board Power), which includes the entire board, memory included. In the rest of the chapter, I will treat them as practical synonyms, because on these products they almost always coincide.

### 1.2 Positioning: Why AMD Exists in This Market

The positioning of Instinct can be explained with a single sentence: **no rational buyer wants a single supplier for the largest expenditure item in their budget.** In 2024-2026, an AI training cluster costs more than the real estate that houses it, and Nvidia holds a market share that, depending on how Google's TPUs are counted, ranges between 80% and 90%. Hyperscalers (Microsoft, Meta, Oracle, Amazon), frontier labs, and governments have an enormous strategic interest in the existence of a credible second supplier: not necessarily to buy in bulk, but to **have negotiating leverage** and to avoid being held hostage by a price list and delivery schedule decided by third parties.

AMD is playing exactly this game, and it's playing it on three levers:

1. **More memory per GPU.** This is the main lever, the one on which AMD has built its entire narrative since 2023. If a model with 70 billion parameters fits into a single GPU instead of two, the service architecture simplifies, the communication cost between the two GPUs disappears, and the cost per token served plummets.
2. **Lower price per unit of memory and per FLOP.** AMD cannot afford Nvidia's premium, because the software is inferior: it has to discount. This discount has a precise name — *ecosystem discount* — and we will analyze it in section 4.
3. **Open standards.** Where Nvidia has NVLink (proprietary), AMD pushes UALink (consortium); where Nvidia has InfiniBand, AMD pushes Ultra Ethernet; where Nvidia has CUDA (proprietary), AMD has ROCm (open source). This is only seemingly an ideological choice: it is the classic strategy of the challenger seeking to commoditize the ground on which the leader has built its moat.

### 1.3 CDNA vs. RDNA: Why Two Separate Architectures

Until 2018, AMD had only one GPU architecture, GCN (Graphics Core Next), used for both gaming and computation. It was a compromise and, like all compromises, it didn't excel anywhere: it brought graphics-dedicated silicon to the datacenter that no one used, and it brought an emphasis on double-precision computing to gaming that no video game ever required.

In 2019, AMD made the structural choice that still defines its portfolio today: **bifurcating the architecture**.

- **RDNA** (Radeon DNA) is the *consumer/graphics* line. Optimized for latency, real-time rendering, ray tracing, and power consumption within the 350-600 W of a desktop card. It uses GDDR memory. This is what you find in the Radeon RX 7000/9000 series.
- **CDNA** (Compute DNA) is the *datacenter/compute* line. Optimized for pure throughput, filled with matrix units (the *Matrix Cores*, the functional equivalent of Nvidia's Tensor Cores), hungry for memory bandwidth, powered by HBM, designed to fit into a server chassis cooled by 40 °C inlet air or liquid.

**Why it's the right choice.** A silicon die is a finite area budget. Every square millimeter spent on rasterization units is a square millimeter not spent on Matrix Cores. For a customer buying 8,192 accelerators to train a language model, every transistor dedicated to graphics is burned money and wasted watts. Nvidia does exactly the same thing, less explicitly: the GB100 die of Blackwell datacenter and the GB202 die of the GeForce RTX 5090 are completely different chips, with the same commercial label "Blackwell".

**Practical consequence for the reader.** A Radeon RX 7900 XTX and an Instinct MI300X are not "the same thing in two sizes." They are distant relatives. Code optimized for one is not automatically fast on the other, low-level functionalities differ (RDNA has 32-thread wavefronts, CDNA has 64), and even ROCm support follows different schedules. **Typical buyer's mistake: thinking "I'll try ROCm on my Radeon and then scale up to Instinct."** The code can be ported, but the performance and optimized kernels cannot.

### 1.4 How to read Instinct nomenclature

The acronym is simpler than Nvidia's. Let's take `MI355X`:

| Element | Meaning |
|---|---|
| `MI` | "Machine Intelligence." Family brand, unchanged since 2016. |
| `3` (first digit) | **Generation/series.** MI100 → CDNA1, MI200 → CDNA2, MI300 → CDNA3, MI350 → CDNA4, MI400 → CDNA5. |
| `5` (second digit) | **Position in the series.** MI300 is the progenitor, MI325 and MI350 are the incremental "refreshes" (typically more memory or faster memory). |
| `5` (third digit) | Product variant within the same family (MI350X vs MI355X: same silicon, different thermal envelope and cooling). |
| `X` (suffix) | High-end variant, typically **GPU-only**, for AI/HPC workloads. |
| `A` (suffix) | **APU** variant: x86 CPU cores and GPU cores with unified memory coexist on the same package (e.g., MI300A). |

The suffix is the most confusing part: `X` = pure GPU accelerator; `A` = hybrid CPU+GPU. In the MI400 series, AMD introduced a third dimension of specialization, no longer just thermal but **functional** (see §2.6): MI430X for high-precision HPC, MI440X/MI455X for low-precision AI.

---

## 2. The cards, one by one

Before going into detail, three definitions that will be used in each card description.

**OAM** (OCP Accelerator Module) is the standardized physical format from the Open Compute Project for datacenter accelerators: a rectangular module, without its own heatsink, without cable power connectors, which bolts onto a baseboard (UBB, Universal Baseboard) typically capable of housing eight of them. It is the open equivalent of Nvidia's proprietary SXM format. An OAM module **cannot be bought and installed in a PC**: it is bought inside a server, period.

**PCIe** (Peripheral Component Interconnect Express) is the standard system bus. PCIe versions of datacenter GPUs exist, in the form of a dual-slot add-in card, but they are always depowered versions (less watts, less bandwidth between GPUs) and serve niche cases, not serious clusters.

**FLOPS** (Floating Point Operations Per Second) is the raw computation metric. It should always be read in conjunction with: (a) **precision** (FP64, FP32, FP16, BF16, FP8, FP6, FP4 — fewer bits, more operations per second, less numerical accuracy); (b) whether the number is **dense** or **sparse** (with structured sparsity, manufacturers double the declared number: two "PFLOPS" numbers can differ by 2× just because of this, and it is the most common marketing trick in the industry).

### 2.1 MI250X (CDNA2, 2021) — the historical chapter

**Description and function.** The MI250X is the card that gave AMD its first real symbolic victory in supercomputing. CDNA2 architecture, manufactured at 6 nm by TSMC, adopts an **MCM** (Multi-Chip Module) design: two complete GPU dies — called GCD, Graphics Compute Die — placed side-by-side on the same package and connected by Infinity Fabric.

| Specification | Value |
|---|---|
| Architecture | CDNA2, TSMC 6 nm |
| Configuration | 2 GCD × 110 CU = 220 Compute Units |
| Memory | 128 GB HBM2e |
| Bandwidth | ~3.2 TB/s |
| Vector/Matrix FP64 | ~47.9 / ~95.7 TFLOPS |
| Matrix FP16 | ~383 TFLOPS |
| TBP | 500-560 W |
| Form Factor | OAM |

**The architectural weakness, and it's didactically important.** The two GCDs **were not seen by the software as a single GPU**: the operating system saw two distinct devices. Programmers had to explicitly manage the partitioning of work between the two halves. This is exactly the problem AMD will solve with MI300X, where the chiplet design becomes transparent to the programmer. Remember this distinction: *visible chiplet* vs *transparent chiplet* is one of the most important conceptual differences between CDNA2 and CDNA3.

**Historical use case: Frontier.** The Frontier supercomputer at Oak Ridge National Laboratory (USA), operational since 2022, was the first machine in the world to officially exceed exaFLOPS in double precision (10^18 operations per second in FP64). It is built with over 37,000 MI250X paired with EPYC CPUs. Frontier is proof that AMD was already capable of making first-class HPC hardware *before* the AI wave: AMD's problem has never been silicon, it has always been software (§4).

**Indicative price.** **[VOLATILE DATA]** Out of production for the new market; modules can be found on the used/decommissioning market for a few thousand euros. **Do not buy it.** 128 GB of HBM2e seems like a lot, but without FP8 support and with a software stack of that generation, it's a dead end for modern AI.

### 2.2 MI300X (CDNA3, late 2023) — the card that changed the conversation

This is the card that made AMD relevant in AI, and it deserves the longest discussion.

**The 3.5D chiplet design.** The MI300X is, from a packaging engineering perspective, one of the most complex chips ever produced. It is not a monolithic die: it is a tower.

- At the base are **4 IODs** (I/O Die, manufactured at 6 nm): they contain the memory controllers, shared cache (256 MB of "Infinity Cache"), and the internal interconnect fabric.
- Above the IODs, soldered with **hybrid bonding** technology (direct copper-to-copper connection, without microbumps, which allows much better connection density and latencies), are **8 XCDs** (Accelerator Complex Die, at 5 nm), for a total of **304 active Compute Units**.
- Around them, on an interposer, **8 HBM3 stacks** for a total of **192 GB**.
- All this for approximately **153 billion transistors**.

The keyword is "3.5D": vertical stacking (3D, XCDs above IODs) combined with horizontal side-by-side placement on an interposer (2.5D, HBM stacks next to the logic complex).

**Why it matters.** Unlike the MI250X, here the chiplets are **transparent to the software**: PyTorch sees *one* GPU with 192 GB. The cost of this transparency is paid in hardware, by the internal Infinity Fabric and the shared cache.

| Specification | MI300X |
|---|---|
| Architecture | CDNA3 (5 nm XCD + 6 nm IOD) |
| Compute Units | 304 |
| Memory | **192 GB HBM3** |
| Bandwidth | **5.3 TB/s** |
| FP16/BF16 (dense) | ~1.3 PFLOPS |
| FP8 (dense) | ~2.6 PFLOPS |
| Matrix FP64 | ~163 TFLOPS |
| TBP | 750 W |
| Form Factor | OAM (8-GPU platform); a niche MI300X PCIe also exists |

**The VRAM Advantage on H100: What it Really Means.** Nvidia's H100 SXM, its direct contemporary, has 80 GB of HBM3 with 3.35 TB/s. The MI300X has **192 GB with 5.3 TB/s**: 2.4x the capacity, 1.6x the bandwidth. Translated into practice:

- A 70 billion parameter model in FP16 occupies ~140 GB just for the weights. On H100, **two** GPUs are needed (with all the complications of *tensor parallelism*, i.e., splitting matrices between GPUs, which introduces communication at each layer). On MI300X, **one** is enough, and ~50 GB remains for the KV cache (the key-value cache, which is the memory of the already processed context, growing linearly with context length × number of concurrent requests).
- An 8-MI300X platform offers **1.5 TB of aggregated HBM** compared to 640 GB of an 8-H100 node. For inference of enormous models (a DeepSeek or a Llama 405B in FP8), this is the difference between "it fits in one node" and "I have to distribute it across two nodes via the network," which is a significant jump in complexity and latency.
- **LLM inference during generation is *memory-bound*, not *compute-bound*.** This sentence should be memorized. To generate a token, the GPU must read *all* the model weights from memory. The bottleneck is memory bandwidth, not compute units, which remain largely idle. It follows that **HBM bandwidth predicts generation speed better than peak TFLOPS**. And this is precisely the area where AMD is competitive.

**Indicative Price.** **[VOLATILE DATA]** AMD does not publish price lists. Market estimates for the MI300X in volume have been in the order of **$10,000-$15,000 per module** compared to $25,000-$40,000 asked for an H100 SXM at the peak of scarcity. On the cloud, the hourly rental of an MI300X has been **15-40% below** that of a comparable H100. Consider this an order of magnitude, not a price.

**Use Cases.** High-volume LLM inference; fine-tuning of medium-to-large models; mixed HPC (strong FP64). Less suitable for: research that depends on third-party custom CUDA kernels (§4).

### 2.3 MI300A (the APU) and El Capitan

The MI300A is the most conceptually interesting and commercially more niche variant. Take the MI300X, remove two XCDs (leaving 6 XCDs, 228 CUs) and in their place put **three CCD chiplets with 24 Zen 4 cores** (the same CPU as EPYC). Add **128 GB of HBM3 coherently shared between CPU and GPU**.

**What "coherent unified memory" means and why it's a huge deal.** In the classic model, the CPU has its RAM and the GPU has its VRAM. To compute on the GPU, data must be *copied* from RAM to VRAM via the PCIe bus, the computation performed, and then copied back. In HPC workloads with many alternating CPU/GPU phases, this copying dominates execution time and is the most common source of slow code. In the MI300A, **there is no copying**: CPU and GPU see the same physical addresses on the same HBM, with coherence maintained by hardware. An entire class of optimizations (and bugs) is eliminated.

**TBP:** ~550 W nominal, up to 760 W in boosted configuration. **Form factor:** socket/OAM in dedicated nodes.

**El Capitan.** The supercomputer at Lawrence Livermore National Laboratory, operational from late 2024, built on MI300A, has become the fastest system in the world on the TOP500 list, exceeding 1.7 exaFLOPS FP64 (**[VOLATILE DATA]**: rankings change every six months). It is the second demonstration, after Frontier, that in "true" scientific computing, AMD is not the second vendor: it is *the* vendor.

**Typical error.** Looking at the MI300A and thinking "128 GB unified, perfect for AI." No: for pure AI, the MI300X with 192 GB of GPU-only memory is almost always the better choice. The MI300A excels in mixed CPU/GPU HPC codes (fluid dynamics, nuclear simulation, weather), where memory coherence is more valuable than capacity.

### 2.4 MI325X (CDNA3 refresh, late 2024/2025)

This is not a new architecture: it's **the same CDNA3 silicon as the MI300X with better memory**. It's a classic "mid-cycle refresh," and should be understood as such.

| Specification | MI325X | (for comparison) MI300X |
|---|---|---|
| Architecture | CDNA3 | CDNA3 |
| Memory | **256 GB HBM3e** | 192 GB HBM3 |
| Bandwidth | **~6.0 TB/s** | 5.3 TB/s |
| Compute (FP8/FP16) | unchanged | — |
| TBP | **1,000 W** | 750 W |

**Positioning against H200.** The H200 is, similarly, the H100 with HBM3e: **141 GB at 4.8 TB/s**, 700 W. The comparison is therefore 256 GB vs 141 GB and 6.0 vs 4.8 TB/s: AMD maintains the capacity advantage (1.8×) and adds a bandwidth advantage (1.25×), paying for it with 300 W more per module.

**The takeaway.** The MI300X → MI325X jump is **+33% memory and +13% bandwidth at +33% power, with zero compute gain**. This is an upgrade that makes sense *only* if your bottleneck is memory — that is, again, if you are doing LLM inference. If you are training, the MI325X gives you almost nothing more than the MI300X. **Typical error: buying the refresh because the number is bigger, without having profiled your workload.**

### 2.5 MI350X / MI355X (CDNA4, mid-2025)

Here AMD truly changes architecture. CDNA4 on a 3 nm process, and most importantly: **native support for FP6 and FP4**.

**What are FP8, FP6, FP4 and why they are the news.** These are very low-precision floating-point number formats: 8, 6, and 4 bits per number respectively, compared to 16 for FP16/BF16. Fewer bits simultaneously mean three things: (1) the model occupies less memory (a 70B model in FP4 weighs ~35 GB instead of 140); (2) fewer bytes to read from memory per generated token, thus **faster inference on the memory-bound path**; (3) matrix units complete more operations per cycle. The cost is a loss of numerical accuracy, which can be mitigated with sophisticated quantization techniques (block scales, calibration, "microscaling" formats like MXFP4/MXFP6). The point is that from 2025 onwards, **the currency of inference is FP4**, which is why both AMD (CDNA4) and Nvidia (Blackwell) have built dedicated silicon.

| Specification | MI350X | MI355X |
|---|---|---|
| Architecture | CDNA4, TSMC 3 nm | CDNA4, TSMC 3 nm |
| Memory | 288 GB HBM3e | 288 GB HBM3e |
| Bandwidth | ~8 TB/s | ~8 TB/s |
| FP8 (dense) | ~5 PFLOPS | ~5 PFLOPS |
| FP4/FP6 (dense) | ~9-10 PFLOPS | ~10 PFLOPS |
| FP64 | ~72 TFLOPS | ~79 TFLOPS |
| TBP | **1,000 W (air)** | **1,400 W (liquid)** |
| Cooling | air-cooled | **liquid-cooled only** |

**The MI350X/MI355X difference is, essentially, cooling.** Same silicon: the MI350X fits into a 1,000W air-cooled envelope and slides into existing data centers; the MI355X pushes to 1,400W but **requires liquid cooling** and thus an equipped data center. Those with cold plates get the MI355X and gain frequencies and throughput; those with only air get the MI350X. **This is the first time in this book that the *facility* constraint — not the chip — determines which product you can buy.** From now on, it will always be this way.

**Declared comparison with Blackwell.** AMD positions MI355X against Nvidia's B200/B300. The MI355X's 288GB of HBM3e exactly matches the B300's (Blackwell Ultra) 288GB, and bandwidth is equivalent (~8 TB/s). The capacity advantage AMD had against H100 **is gone**: Nvidia has responded. On FP4 FLOPS, the B300 claims ~15 PFLOPS dense versus the MI355X's ~10: Nvidia is ahead on raw compute. **[VOLATILE DATA]** In independent MLPerf Inference benchmarks published in spring 2026, the MI355X achieved AMD's best result ever, placing within a few percentage points of the B200 on server inference workloads — a result that would have been unthinkable two years prior.

**Indicative price.** **[VOLATILE DATA]** Order of magnitude $25,000-$35,000 per module in volume, typically sold only as an 8-GPU platform. A complete 8-MI355X node with EPYC CPUs, network, and chassis is on the scale of **hundreds of thousands of euros**.

### 2.6 The MI400 Series and the Helios Platform (2026)

**[ENTIRELY VOLATILE SECTION — data from CES 2026 announcements, product not yet shipped in volume at time of writing.]**

This is the most ambitious generational leap ever attempted by AMD, and it changes the very nature of the product: **AMD stops selling GPUs and starts selling racks.**

**The MI400 series (CDNA 5, TSMC 2 nm)** for the first time features *functionally* different variants:

| Model | Target | Distinctive Feature |
|---|---|---|
| **MI455X** | Large-scale training + inference | The flagship. ~320 billion transistors, **432 GB HBM4**, ~19.6 TB/s, ~40 PFLOPS FP4 / ~20 PFLOPS FP8. Optimized for low precision (FP4/FP8/BF16). |
| **MI440X** | Enterprise on-premise | Designed for the "normal" 8-GPU server to be placed in an existing enterprise rack, not for rack-scale. |
| **MI430X** | HPC and "sovereign AI" | **Retains full FP32/FP64.** This is the variant for scientific computing and for states that want data within their own borders. Intended for supercomputers like Discovery (Oak Ridge) and Alice Recoque (France). |

**The logic of specialization must be well understood**, because it is a general trend in the industry: by separating the AI variant (which does not need FP64) from the HPC variant (which desperately needs it), AMD **eliminates redundant logic from the die** and improves its power efficiency and cost. Nvidia is doing the same thing in a different way (with the Rubin CPX specialized in *prefill*). The era of the "generalist GPU that does everything well" is ending.

**Helios.** This is the rack. It is not a server: it is a double-width, integrated, liquid-cooled cabinet that AMD sells as an indivisible computing unit:

- **72 MI455X accelerators** + **EPYC "Venice"** (Zen 6) CPU + 800G Pensando "Vulcano" NIC (Network Interface Card);
- **31 TB of aggregated HBM4**, with **1.4 PB/s** of total memory bandwidth;
- **~2.9 exaFLOPS FP4** for inference and **~1.4 exaFLOPS FP8** for training per rack;
- power consumption and cooling requirements necessitate a new-generation datacenter (on the order of **hundreds of kW per rack**).

**Why AMD had to build a rack.** Because Nvidia did it first (GB200 NVL72) and shifted the battlefield. When 72 GPUs are wired into a single coherent memory domain, the customer no longer compares "GPU versus GPU": they compare "rack versus rack," and whoever doesn't have a rack isn't in the race. The roadmap continues with the **MI500 series on CDNA 6 and HBM4E announced for 2027**.

---

## 3. The AMD Interconnect

### 3.1 The Problem, Before the Solution

A single GPU cannot train a frontier model. Thousands are needed, and they must continuously exchange data: in every distributed training step, at the end of gradient computation, all GPUs must sum their gradients with those of all others (an operation called *all-reduce*). If this exchange is slow, the GPUs remain idle waiting, and you are paying €40,000 per card to let them rest.

Hence the fundamental distinction, which applies to both manufacturers:

- **Scale-up** ("vertical" interconnect, within the node or within the rack): few devices, very low latency, enormous bandwidth, coherently shared memory. This is the domain of NVLink and Infinity Fabric.
- **Scale-out** ("horizontal" interconnect, between nodes and between racks): many devices, using a real network (InfiniBand or Ethernet). Bandwidth is an order of magnitude lower, latency is higher.

The universal design rule: **keep the most intense traffic within the scale-up domain and exit to scale-out as little as possible.**

### 3.2 Infinity Fabric and the 8-GPU OAM Platform

**Infinity Fabric** (IF) is AMD's proprietary interconnect fabric, born in 2017 with Zen to connect CPU chiplets and then extended to everything: chiplet-to-chiplet within the package, CPU-to-GPU on the socket, GPU-to-GPU on the baseboard.

In the standard Instinct platform — **8 OAM modules on a UBB** — the 8 GPUs are connected to each other by Infinity Fabric links in an **all-to-all** topology: each GPU has a direct connection to each of the other seven. This is architecturally elegant and has a practical advantage: **a dedicated switch chip is not needed**. Nvidia, to achieve the same result, uses **NVSwitch**, additional switching chips.

- **The AMD advantage:** less silicon, less cost, less power consumption, simple topology.
- **The AMD limitation:** an all-to-all does not scale. It works perfectly with 8 GPUs. With 72, the number of direct connections explodes (n·(n-1)/2) and becomes impossible. **It is precisely for this reason that Nvidia, with NVSwitch, was able to build the GB200 NVL72 — 72 GPUs in a single NVLink domain — while AMD remained stuck at 8 for an entire generation.** If you remember only one sentence from this chapter, remember this: *Nvidia's structural advantage in 2024-2025 was not the GPU, it was the switch.*

### 3.3 NVLink: What AMD Must Beat

**NVLink** is Nvidia's proprietary interconnect. In the Blackwell generation, it offers ~1.8 TB/s bidirectional per GPU; in the Rubin generation, it increases to ~3.6 TB/s. Combined with NVSwitch, it creates a **coherent memory domain across 72 GPUs**: from a programmer's perspective, the NVL72 rack behaves like a giant GPU with shared memory. It's a deep technological moat, and Nvidia defends it: NVLink is closed, not licensed to third parties.

### 3.4 UALink: the open anti-NVLink standard

**UALink** (Ultra Accelerator Link) is the industry's collective response. It's a consortium bringing together AMD, Intel, Google, Meta, Microsoft, Broadcom, Cisco, HPE, and others, with a stated goal: **to define an open standard for scale-up accelerator-to-accelerator interconnection**, so that a rack can contain accelerators from different vendors and switches from different vendors, as is already the case with Ethernet in networking.

**The real situation in 2026, without rhetoric.** MI400 accelerators will be the first to support UALink alongside Infinity Fabric. But an interconnect standard without **switch silicon** is a specification, not a product: for UALink to truly work, switch chips are needed, expected from vendors like Astera Labs, Broadcom, Enfabrica, and others. Until they arrive in volume, the first Helios systems will use **UALink-over-Ethernet** — a functional solution but not what UALink was designed for. **Translation for the buyer: UALink is a credible promise with uncertain timing. Do not base a purchasing plan on it today.**

**Ultra Ethernet** is the sibling on the scale-out side: a parallel consortium that extends Ethernet with the latency and reliability features required by AI clusters, to replace InfiniBand (Nvidia proprietary after the acquisition of Mellanox). Here, AMD's position is stronger, because Ethernet already exists and network cards (Pensando Pollara 400G, Vulcano 800G) are real products.

---

## 4. The weak point: software

If you've read the previous sections thinking "but then AMD is competitive, why isn't anyone buying it?", the answer is all here.

### 4.1 CUDA: understanding the moat before understanding the bridge

**CUDA** (Compute Unified Device Architecture) is not "Nvidia's driver." It's a complete stack accumulated over **nearly twenty years**: a language, a compiler, a runtime, and above all a **pyramid of libraries** (cuBLAS for linear algebra, cuDNN for neural networks, NCCL for multi-GPU communication, TensorRT-LLM for optimized inference, CUTLASS for matrix kernels) on which *all* the world's AI software rests. And above that: millions of lines of research code, tutorials, Stack Overflow answers, doctoral theses, pre-packaged containers, and an entire generation of engineers who learned to program this way.

The moat is not technical. It is **social and cumulative**. And that's why it cannot be filled with a faster chip.

### 4.2 ROCm: what it is and where it really stands

**ROCm** (Radeon Open Compute platform) is AMD's answer: an open-source stack, functionally equivalent to CUDA. As of 2026, the current line is **7.x** (ROCm 7 introduced stated improvements of over 3-4× on inference and training compared to ROCm 6.0), with regular releases and first-class Linux support.

**Where ROCm works well, today — and this has changed a lot in the last 18 months:**

- **PyTorch**: Official, upstream support with ready-to-use builds. In the vast majority of cases, a PyTorch model running on CUDA runs on ROCm with zero code changes: `torch.cuda` continues to be called that way even on AMD (it's an alias, for compatibility).
- **vLLM and SGLang**: The two most widely used LLM inference engines in production have official and mature ROCm support. AMD has invested heavily here, with optimized proprietary kernels (the **AITER** library) that bring multiple throughput improvements in attention backends compared to generic paths. In 2026, ROCm entered vLLM's official CI (Continuous Integration): every commit is tested on AMD silicon, meaning regressions are caught before reaching the user. This is a huge change in status, from being "a community porting" to being "a first-class platform."
- **Standard LLM Inference**: On PyTorch + vLLM workloads without custom kernels, independent measurements place ROCm on MI300X/MI355X **around 90-95% of Nvidia's counterpart throughput** — and with large batches, the gap narrows further. **[VOLATILE DATA]**
- **llama.cpp / Ollama**: They work, even on consumer cards.

**Where ROCm still suffers, and it's honest to say:**

- **Third-party custom CUDA kernels.** This is **the** problem, the one that causes migrations to fail. A lot of research code — and not a few production libraries — ships hand-written CUDA kernels, sometimes with PTX assembly or Nvidia-specific intrinsics. That code **does not port automatically**. You find yourself choosing between: looking for a compatible fork, rewriting the kernel, or waiting for the community to do it. If your project depends on a niche library with custom kernels, **migration to AMD can get stuck on a single `.cu` file**.
- **TensorRT-LLM and the Nvidia optimization ecosystem** do not have a complete ROCm equivalent. At very small batches (low-latency inference, one user at a time), where TensorRT-LLM performs best, Nvidia maintains a significant throughput advantage.
- **FlashAttention**: ROCm ports exist and work, but tend to lag behind the CUDA version by weeks or months. If you need the latest features for training, expect friction.
- **Documentation and edge cases.** Greatly improved, but the density of ready answers on the Internet remains an order of magnitude lower. When you have an obscure CUDA error, you Google it and find three people who already solved it in 2022. On ROCm, often, you find an open issue on GitHub. **This translates into man-hours, and man-hours have a cost.**
- **Windows and macOS**: For serious ML work, **Linux only**. Ubuntu LTS or RHEL. No qualifiers.

### 4.3 HIP: The Bridge

**HIP** (Heterogeneous-Compute Interface for Portability) is the compatibility layer. Syntactically it's almost a clone of CUDA: `cudaMalloc` becomes `hipMalloc`, `__global__` remains `__global__`, the concepts (grid, block, thread, shared memory) are the same. AMD provides **`hipify`**, an automatic source-to-source translation tool that converts CUDA code to HIP code.

**How well it works, realistically:**

- For "idiomatic" CUDA code that uses standard APIs: **automatic conversion succeeds in the vast majority of cases.**
- For code that uses Nvidia-specific architectural intrinsics, warp shuffle with assumptions about warp size (32 on Nvidia, 64 on CDNA!), inline PTX assembly, or the latest Tensor Core APIs: **conversion fails, or worse, succeeds but produces correct yet extremely slow code.**

The subtle, and crucial, point: **HIP guarantees portability, not performance.** A mechanically translated kernel will run. A *fast* kernel on CDNA needs to be rewritten with CDNA in mind. Anyone selling migration to AMD as "just run hipify" is selling you an illusion.

### 4.4 The "ecosystem discount": why AMD hardware costs less

Now we can close the economic loop. Why does an MI300X with 192 GB cost less than an H100 with 80 GB?

**Because the price of an accelerator is not the price of the silicon: it's the price of the silicon plus the value of the ecosystem, minus the cost of risk.** AMD must compensate, in price, for everything the customer will have to spend extra:

- engineering hours to port and optimize code;
- risk of getting stuck on an unsupported dependency;
- risk of delivery, drivers, regressions;
- less liquid used market and lower residual value;
- smaller talent pool (finding an engineer who can optimize HIP kernels is harder than finding one who can optimize CUDA, and costs more).

**This discount is real and should only be taken if you have the organization to do so.** A 25% discount on hardware is an excellent deal for a hyperscaler with a team of 40 system engineers optimizing kernels. It's a bad deal for an SME with two data scientists, because those two will spend three months fighting with drivers, and those three months cost more than the discount. **Rule: the ecosystem discount is worth as much as your internal capacity to absorb it.**

---

## 5. Nvidia vs. AMD in the datacenter: do they beat or not beat?

Let's make an honest, criterion-by-criterion comparison, without bias.

### 5.1 VRAM per GPU — **AMD wins**, historically

On this, AMD has had a continuous structural advantage. 192 GB vs. 80 (MI300X vs. H100), 256 vs. 141 (MI325X vs. H200). **However, beware**: with Blackwell Ultra (B300, 288 GB), Nvidia has **matched** the MI355X. The advantage reopens with MI455X (432 GB HBM4) vs. Rubin VR200 (288 GB HBM4). **The historical pattern is clear: AMD opens the lead, Nvidia closes it in the next generation.** Don't take it for granted that it will last.

### 5.2 Memory bandwidth — **substantially on par, with generational head-to-head**

5.3 vs. 3.35 TB/s (MI300X vs. H100: AMD wins). 8 vs. 8 (MI355X vs. B300: tied). 19.6 vs. 22 TB/s (MI455X vs. VR200: **Nvidia wins**, which with HBM4 at over 11 Gbps per pin has pushed the frequency higher). This is the most important metric for inference, and it is the most contested front.

### 5.3 Training performance — **Nvidia wins, and it's not close**

Not so much for the silicon, but for: (a) higher peak FLOPS in low precision; (b) NCCL and the entire collective communication stack, mature and optimized for years; (c) the 72-GPU NVLink domain, which for training enormous models is an architectural, not incremental, advantage; (d) FlashAttention, Transformer Engine, Megatron/NeMo and the entire large-scale training toolkit, which originated on CUDA. **If you need to train a frontier model from scratch, in 2026 buy Nvidia.** This is the conclusion, however uncomfortable.

### 5.4 Inference Performance — **AMD is competitive, and in some scenarios wins**

Because inference during generation is memory-bound, and memory is AMD's territory. More VRAM per GPU eliminates entire axes of parallelism, simplifies deployment, and serves a large model on fewer GPUs. On PyTorch + vLLM/SGLang, with large batches, the gap narrows to single-digit percentages. **If your business is serving tokens in volume, AMD deserves serious consideration.**

### 5.5 TCO and Price — **AMD wins on purchase price; TCO depends on you**

**TCO** (Total Cost of Ownership) is the sum of: purchase + energy + cooling + space + personnel + software + risk cost. AMD clearly wins on the first item. It typically loses on energy (higher TDPs for the same generation) and personnel (§4.4). The verdict **depends entirely on the scale and maturity of your team**.

### 5.6 Software — **Nvidia wins, clearly**

See §4. There's nothing else to add. It's why this chapter doesn't end with "buy AMD."

### 5.7 Availability — **the wild card**

In times of extreme scarcity, the question isn't "which is better," it's "what can I get." An MI355X delivered in eight weeks beats a B300 delivered in ten months, regardless of benchmarks. **[VOLATILE and extremely variable DATA.]** This is, historically, the primary reason AMD has sold Instinct.

### 5.8 Who buys AMD, and why

1. **Hyperscalers.** Microsoft, Meta, Oracle. They have the teams to absorb the software cost, buy in volumes that justify dedicated optimization, and have an existential strategic interest in not depending on a single vendor. They buy AMD **also to have something to show Nvidia during negotiations.** It's negotiating leverage, even more than technology.
2. **Inference farms.** Companies whose product is serving tokens (API providers, cloud inference engines). Memory-bound load, standardized software stack (vLLM), extreme sensitivity to cost per token. The ideal profile for AMD.
3. **HPC and the public sector.** Frontier, El Capitan, European exascale systems. AMD is the champion here, not the challenger — strong FP64, unified memory, and no lock-in.
4. **"Sovereign AI."** States that want their own infrastructure, often with an explicit political mandate in favor of open standards and against monopoly.

Who does **not** buy AMD: the startup with six engineers and eighteen months of runway, who cannot afford to lose three weeks on a kernel. And rightly so.

### 5.9 Final Comparative Table

**[ALL DATA IN THIS TABLE IS VOLATILE.]** FLOPS in *dense* values, not sparse. Indicative market prices, not list prices.

| | **H100 SXM** | **H200 SXM** | **B200** | **B300 / GB300** | **VR200 (Rubin)** | **MI300X** | **MI325X** | **MI355X** | **MI455X** |
|---|---|---|---|---|---|---|---|---|---|
| Manufacturer | Nvidia | Nvidia | Nvidia | Nvidia | Nvidia | AMD | AMD | AMD | AMD |
| Architecture | Hopper | Hopper | Blackwell | Blackwell Ultra | Rubin | CDNA3 | CDNA3 | CDNA4 | CDNA5 |
| Year | 2022 | 2023 | 2024-25 | 2025-26 | H2 2026 | 2023 | 2024-25 | 2025 | H2 2026 |
| Memory | 80 GB HBM3 | 141 GB HBM3e | 180-192 GB HBM3e | 288 GB HBM3e | 288 GB **HBM4** | 192 GB HBM3 | 256 GB HBM3e | 288 GB HBM3e | **432 GB HBM4** |
| Bandwidth | 3.35 TB/s | 4.8 TB/s | 8 TB/s | 8 TB/s | **~22 TB/s** | 5.3 TB/s | 6.0 TB/s | 8 TB/s | ~19.6 TB/s |
| FP8 (dense) | ~2 PF | ~2 PF | ~4.5 PF | ~5 PF | ~35 PF (NVFP4 train) | ~2.6 PF | ~2.6 PF | ~5 PF | ~20 PF |
| FP4 (dense) | — | — | ~9 PF | ~15 PF | **~50 PF** | — | — | ~10 PF | ~40 PF |
| FP64 | 34 TF | 34 TF | ~40 TF | reduced | reduced | 163 TF | 163 TF | 79 TF | (MI430X) |
| TDP/TBP | 700 W | 700 W | 1,000 W | ~1,400 W | ~1,800-2,300 W | 750 W | 1,000 W | 1,400 W | ~2,300+ W |
| Cooling | air/liquid | air/liquid | air/liquid | **liquid** | **liquid only** | air | air | **liquid** | **liquid only** |
| Scale-up | NVLink 4 | NVLink 4 | NVLink 5 | NVLink 5 (NVL72) | NVLink 6 (NVL72) | Infinity Fabric (8 GPUs) | IF (8) | IF (8) | IF + **UALink** |
| Rack-scale | HGX 8× | HGX 8× | GB200 NVL72 | GB300 NVL72 | VR200 NVL72 | — | — | — | **Helios (72)** |
| Indicative Price | $25-30k | $30-35k | $30-40k | $40k+ | n.d. | $10-15k | $15-25k | $25-35k | ~$30k (estimates) |
| Software | CUDA ★★★★★ | ★★★★★ | ★★★★★ | ★★★★★ | ★★★★★ | ROCm ★★★☆☆ | ★★★☆☆ | ★★★★☆ | ★★★★☆ |

**How to read this table.** Look at the last three rows. Price and software tell the whole story: AMD offers more memory per dollar, Nvidia offers less friction per dollar. The choice is ultimately a bet on **which of the two resources—money or your engineers' time—is scarcer in your organization.**

---

## 6. Final Decision Guide for an AI Build (Workstation Part)

Let's conclude by descending from the clouds of the datacenter to the desktop, because that's where 99% of this book's readers will have to make a real decision. Price ranges are **[VOLATILE DATA]** and in 2026 have been further distorted by memory scarcity: consider them orders of magnitude.

### 6.1 The Zero Question: Buy or Rent?

Before picking a tier, ask this first. **The cloud is convenient when usage is low, variable, or uncertain. Buying is convenient when usage is high, constant, and predictable.**

The calculation is simple: take the hourly cost of the equivalent cloud instance (**[VOLATILE DATA]** — approximately, in 2026, a few euros per hour for an H100/H200-class GPU on specialized providers, more on large generalist clouds), multiply it by the hours you will *actually* use per month, and compare it with (hardware price / 36 months) + electricity + your own administration time.

The typical result is that **the break-even point sits around 30-50% continuous usage**. If your GPU would stay on and loaded less than 8 hours a day, the cloud almost always wins. If you keep it at 90% for two years, buying clearly wins.

But there are three **non-economic** reasons to buy that are often decisive: **data privacy** (if customer data can't leave your premises — often the case in Italy under GDPR — the public cloud is precluded or extremely complicated); **latency and availability** (your GPU is yours, always, without queues or quotas); and **learning** (assembling, configuring, breaking, and repairing a system teaches things renting an instance never will).

### 6.2 Tier A — under €3,000 : the high-end consumer PC

**Who you are:** a developer, a researcher, a student. You want to fine-tune small models, perform local inference, develop and prototype, and maybe play games in the evening.

**What you buy:** a single high-end consumer GPU (RTX 5090 with 32 GB, or a used RTX 4090 with 24 GB), a consumer CPU (Ryzen 7/9 or Core i7/i9), **64-128 GB of system RAM** — rule of thumb: system RAM ≥ 2× total VRAM, because it's needed for loading, quantization, and preprocessing — and a 2 TB NVMe.

**What you can do:** inference of models up to ~30 B in 4-bit quantization; **LoRA** (Low-Rank Adaptation: only a small set of additional matrices are trained instead of all weights, drastically reducing memory requirements) fine-tuning on models up to 7-13 B; all the computer vision you want; development of pipelines that will then run elsewhere.

**What you CANNOT do:** train from scratch; full fine-tuning of large models; serve in production with real concurrency.

**Common mistake:** buying two 16 GB consumer GPUs and expecting to get 32 GB. **VRAM does not stack** if the model is not designed to be partitioned. For LLM inference, a single 24 GB GPU almost always beats two 12 GB GPUs. **Maximize the VRAM of a single chip, not the number of chips.**

### 6.3 Tier B — €5,000-€15,000: The Threadripper Workstation with 2 GPUs

**Who you are:** A consultant, a small team, a pre-seed startup. You need a machine that can handle serious workloads and run for a week straight.

**What you buy:** A **Threadripper** (not a Ryzen) or a Xeon W. Why? For **PCIe lanes** (the "lanes" of the bus: each GPU ideally wants x16 lanes; a consumer CPU offers ~24-28 usable lanes in total, meaning with two GPUs you drop to x8 each and the bottleneck shifts to the bus when GPUs need to communicate). A Threadripper offers 48-92, a Threadripper PRO up to 128. Then: 2 GPUs (2× RTX 5090, or 1-2 RTX PRO Blackwell 48-96 GB if the budget allows), 128-256 GB of ECC RAM, a 1,600 W power supply, a full-tower case with serious airflow.

**Common mistake:** installing two *open-air* consumer GPUs (with open fans, i.e., the vast majority of gaming models) adjacent to each other. **See §6.5: this is the most expensive and most common mistake in this entire tier.**

### 6.4 Tier C — €15,000-€50,000: The Professional Workstation

**Who you are:** A company doing serious AI but without a datacenter (yet).

**What you buy:** Threadripper PRO or single-socket EPYC, and **2-4 RTX PRO 6000 Blackwell (96 GB each)**. Four of these give you **384 GB of VRAM on a single machine**, with blower cooling specifically designed for side-by-side installation, ECC, certified drivers, and — a non-trivial detail — a license that permits datacenter use, which GeForce cards **do not have** (Nvidia's license terms prohibit the use of GeForce cards in datacenters, with limited exceptions: if you provide services to clients, check with a lawyer).

**The specialized alternative:** Desktop systems based on superchips (DGX Spark, DGX Station, and OEM derivatives), which offer large amounts of **coherent unified memory** (up to hundreds of GB) on a single ARM+GPU chip. They are excellent for **loading huge models** that would never fit into a discrete GPU, but their memory bandwidth is much lower than HBM: these are machines for *development and prototyping on large models*, not for high-throughput production. **[VOLATILE DATA]** Choose them if your constraint is "the model doesn't fit," not if it's "the model is slow."

### 6.5 Tier D — over €50,000 or at scale: Datacenters, and the Real Question

Here the question is no longer "which GPU," it's **"buy or rent."**

**The calculation you need to make, and almost no one does.** Take the cost of an 8-GPU datacenter node (order: €250,000-€400,000). Add: high-speed network, storage, racks, UPS, cooling, and — the forgotten item — **energy**. Eight 1,000 W GPUs plus the rest of the node make ~10 kW continuous; at €0.20/kWh, that's ~€17,500/year **[VOLATILE DATA, and in Italy the industrial energy price is higher than the European average]**. Add depreciation over 3-4 years (because the generational cadence is annual and residual value plummets) and a person to administer it.

Then compare it with cloud rental of the same capacity.

**The rule of thumb, which generally applies:**

- **Cloud is convenient** if usage is **less than 50-60%**, if the load is spiky, if you are still exploring what hardware you need, if you need to scale from 8 to 512 GPUs for two weeks and then back to 8, or if you don't have system personnel. The cloud sells you **optionality**, and optionality in a market that changes every twelve months is worth a lot.
- **Purchasing is convenient** if usage consistently exceeds **70-80% 24/7** (at which point the break-even on cloud typically occurs between 12 and 24 months), if you have non-negotiable data residency requirements (healthcare, defense, personal data), if you already have the facility, or if the load is predictable and constant.
- **The middle ground, often the best:** buy a workstation for daily development (which is 90% of man-hours) and rent the cluster for training sessions (which are 90% of GPU-hours).

### 6.6 Compatibility Checklist for a Multi-GPU Workstation

This is the section to reread before clicking "buy." Each point below corresponds to a mistake I've seen made.

**1. PCIe Lanes — count, don't hope.**
Each GPU wants x16. Sum the lanes required by all GPUs **plus** those of the NVMe drives (4 lanes each) **plus** those of the network card. Compare this with what the CPU *actually* offers (not what the chipset "exposes," which are often shared upstream). Check the motherboard manual for the **slot division scheme**: almost always, populating the second x16 slot will downgrade the first to x8. For single-GPU inference, x8 is not dramatic; for multi-GPU training with continuous gradient exchange, it is.

**2. Physical Slot Spacing.**
Modern GPUs occupy 3-4 slots in thickness. A motherboard with two x16 slots "spaced out" on paper may actually leave **no air gap** between the two cards. Measure. If there is no space, solutions with PCIe *risers* (extensions) and vertical mounts exist, but they are a source of signal instability at PCIe 5.0: buy them certified for the correct generation, never generic ones.

**3. Power Supply Unit (PSU) — and transient calculation.**
Sum the TDPs and add **at least 30-40% margin**. Not for general prudence, but because modern GPUs have **transient peaks** (microsecond spikes) that can reach double the nominal TDP and trigger the overcurrent protection (OCP) of an undersized power supply — resulting in an unexpected reboot, usually in the middle of a thirty-hour training session. Two RTX 5090s (2 × 575 W) plus a Threadripper (350 W) plus the rest require **1,600 W minimum**.
Above ~1,600-2,000 W, you enter the territory of **dual PSUs**: two power supplies synchronized with an "add2psu" or a pre-configured server motherboard. Non-negotiable rule: **a single GPU must never be powered by two different PSUs** (potential differences on ground references → damage). One GPU, one power supply.
And, for the connectors: **12VHPWR/12V-2x6 must be inserted all the way, with a click, and must not be bent within 35 mm of the connector.** Cases of connector melting are, in almost all instances, cases of incomplete insertion.

**4. Cooling — blower vs. open-air, the €3,000 mistake.**
This is the most important and least known difference in the entire section.
- An **open-air** GPU (the 2-3 fan design of almost all gaming cards) draws air from below and **blows it sideways into the case**. Designed for a single GPU in a well-ventilated case.
- A **blower** GPU (radial fan) draws air and **expels it entirely out of the case through the rear bracket**. Designed to be placed alongside others.

If you install **two adjacent open-air GPUs**, the upper card draws in the hot air expelled by the lower one. The result is not a failure: it's **thermal throttling**, i.e., the automatic reduction of frequencies to avoid exceeding temperature limits. The upper card will run stably 15-25 °C hotter and lose a significant fraction of performance, **silently and permanently**. You will have paid for two GPUs and get one and a half, without any warning.

**Solutions, in order of preference:**
- buy **professional GPUs with blower coolers** (RTX PROs are designed exactly for this — it's one of the reasons they cost more, and why in multi-GPU setups they are often more economical for the same performance achieved);
- **liquid cooling** with dedicated blocks (block on each GPU, generous radiators): effective, expensive, and introduces a hydraulic point of failure;
- **physically space out** the cards with risers, in a very large case, with dedicated exhaust fans;
- **limit power** (`nvidia-smi -pl`): counterintuitive, but limiting a GPU to 80% of its TDP typically costs only 5-10% in performance and drastically reduces heat. In multi-GPU setups, it's almost always the rational choice.

**5. The domestic electrical circuit — the 3 kW limit in Italy.**
And here we come to the most concrete and most ignored constraint, which no American website will ever mention.

The standard domestic electricity contract in Italy is **3 kW** (with a typical tolerance of 10%, so ~3.3 kW before the limiter trips). A Schuko socket on a 16 A, 230 V circuit nominally handles ~3.6 kW.

Do the math: a workstation with **two RTX 5090s** under full load realistically draws **1,400-1,800 W from the socket** (the upstream draw is higher than the downstream output because the power supply has an 88-92% efficiency). Add the monitor, and you're at ~1,900 W. **That leaves ~1,100 W for everything else in the house.** The electric oven demands 2,000. The washing machine during heating, 2,000. The air conditioner, 1,000-1,500. The hairdryer, 1,800.

**Operational conclusion: with a 3 kW contract, a mid-range multi-GPU workstation will trip your circuit breaker every time someone in the house turns something on.** And it won't trip at noon: it will trip at three in the morning, in the middle of a fine-tuning, because the water heater turned on.

**What to do, in order:**
- **Increase the contractual power** to 4.5 or 6 kW. This is done by requesting it from your provider, it entails an adjustment to the fixed charges on your bill, and it makes sense starting from band B.
- **Check the circuit**: an 1,800 W workstation should be on a **dedicated circuit**, not shared with the kitchen. If your system has a single 16 A outlet circuit for half the apartment, call an electrician.
- **Consider an uninterruptible power supply (UPS)** with adequate *real* power: be aware that UPS units are sold in VA (volt-amperes), not watts. A "1,500 VA" UPS typically delivers ~900-1,000 real W. Size it based on watts.
- **Above ~3 kW for the machine, the workstation stops being an appliance**: an industrial line is needed, and at that point, you have implicitly answered the "buy or cloud" question.

**6. Thermal load in the room.**
Last point, and the most underestimated: **all electrical power absorbed by a computer is converted into heat.** Not "almost all": all. An 1,800 W workstation is, from a thermodynamic perspective, **an 1,800 W heater constantly on**. In a 15 m² room without air conditioning, in summer, this is physically unsustainable — for the machine and for you. Factor in an air conditioner, and remember that it also consumes from your 3 kW contract.

---

## 7. Operational Summary — Decision Checklist

### A) Choosing between Nvidia and AMD in the datacenter

1. **Is my workload training or inference?**
   → *Frontier training* → **Nvidia**, no discussion.
   → *Volume inference* → AMD is in the running. Proceed.
2. **Does my stack depend on custom CUDA kernels, TensorRT-LLM, or niche libraries?**
   → *Yes* → **Nvidia**. The AMD discount won't compensate for porting.
   → *No, I'm on standard PyTorch + vLLM/SGLang* → AMD is a serious choice. Proceed.
3. **Do I have system engineers capable of independently solving a driver issue or a slow kernel?**
   → *No* → **Nvidia**. The ecosystem discount is only realized with internal expertise.
   → *Yes* → Proceed.
4. **Is my bottleneck GPU memory capacity?**
   → *Yes* (the model doesn't fit, or barely fits) → **this is the ideal use case for AMD.**
5. **Have I benchmarked MY model, with MY context length and MY batch?**
   → *No* → **Stop and do it.** No number in this chapter replaces a measurement on your workload. Almost all clouds offer MI300X/MI355X by the hour: **spend €200 on rental before spending €200,000 on hardware.**
6. **Have I verified actual availability and delivery times?**
   → The best GPU is the one that arrives.
7. **Have I considered negotiating leverage?**
   → An AMD quote in hand changes the Nvidia quote. This is, for many companies, the main reason for this entire chapter.

### B) Compatibility Checklist for a Multi-GPU Workstation

- [ ] **PCIe Lanes**: sum the required lanes (GPU + NVMe + network) and compare them with the *actual* lanes of the CPU. Read the slot allocation diagram in the motherboard manual.
- [ ] **Physical Spacing**: verify that there is air between the GPUs. Measured, not estimated.
- [ ] **Coolers**: **blower** (or liquid) if GPUs are adjacent. Never two open-air coolers stuck together.
- [ ] **PSU**: sum of TDPs + 30-40% margin. 12VHPWR cables fully inserted, not bent near the connector.
- [ ] **Dual PSU** (if >1,600 W): one GPU = one power supply. Never straddle.
- [ ] **System RAM** ≥ 2× total VRAM. ECC if the machine needs to run for days.
- [ ] **Power limit** set to 80% in multi-GPU: you lose 5-10%, gain stability and silence.
- [ ] **Electricity contract**: 3 kW is **not enough** beyond band A. Request an increase to 4.5-6 kW.
- [ ] **Dedicated circuit** for the workstation, verified by an electrician.
- [ ] **UPS** sized in **watts**, not in VA.
- [ ] **Room air conditioning**: the machine is a heater. Treat it as such.
- [ ] **Operating system**: Linux. Ubuntu LTS. Both on Nvidia and (especially) on AMD.
- [ ] **Exit plan**: if in 18 months you will need 4× this power, is this machine an investment or a dead end? If it's a dead end, consider the cloud.

---

*End of chapter.*

---

[← Previous](11-nvidia-gpu-ai.html) · [All chapters](./)
