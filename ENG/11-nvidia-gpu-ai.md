---
title: "Chapter 11 - Nvidia GPUs for AI: From Workstation to Datacenter"
parent: "English Edition"
nav_order: 11
---

<details open markdown="block">
  <summary>Chapter contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## Preliminary methodological note

This chapter deals with an industry where data ages faster than a book can be printed. All information reported is current as of mid-2026, but three categories of data should be considered **volatile by nature** and always verified at the source before making a purchasing decision:

1. **Prices.** Datacenter GPUs do not have a stable public price list: they are purchased through OEMs (Original Equipment Manufacturers, i.e., server manufacturers like Dell, Supermicro, HPE, Lenovo) or system integrators, with prices depending on volume, contract, and historical moment. The figures you will find here are orders of magnitude documented by industry press, not price lists.
2. **Availability.** In 2024–2026, demand consistently outstripped supply: an "announced" product can have a real waiting time of 6–12 months.
3. **The latest generation.** Nvidia has adopted an annual architectural cadence. What is "the latest" at the time of writing will be second-to-last within twelve months.

I will explicitly mark with **[VOLATILE DATA]** every point where this issue is particularly acute.

---

## 1. The complete Nvidia hierarchy

### 1.1 Why there isn't "the Nvidia GPU," but three separate worlds

Those new to artificial intelligence hardware almost always make the same conceptual mistake: they think there is a continuous scale of products, from the cheap graphics card to the expensive graphics card, and that the only difference is "how fast it is." This is not the case. Nvidia produces three product lines that share the basic architecture but are **different products, with different markets, different licensing agreements, and different physical constraints**. Confusing them leads to incorrect purchases costing tens of thousands of euros.

First, let's define the term **GPU**: *Graphics Processing Unit*. It originated in the 1990s as an accelerator for 3D rendering in video games, meaning for a very specific task: applying the same mathematical operation (a matrix multiplication, a coordinate transformation, a lighting calculation) to millions of independent elements, all at once. This is the definition of **massive parallel computing**. The historically decisive fact is that training neural networks requires exactly the same thing: multiplying enormous matrices, repeated billions of times. The GPU was not "adapted" for AI; it happened to be in the right place at the right time, and Nvidia had the foresight to build a software ecosystem on top of it (CUDA, which we will discuss in section 7) ten years before it was truly needed.

The three families are:

**GeForce (consumer).** Gaming cards: RTX 4090, RTX 5090, RTX 5080, and their smaller siblings. Sold retail, installed in desktop PCs, with air coolers and fans. The acronym **RTX** = *Ray Tracing Texel eXtreme*, introduced with the Turing generation (2018) to indicate the presence of hardware units dedicated to ray tracing. For AI purposes, ray tracing is irrelevant; what matters is that these same cards also have **Tensor Cores** (units dedicated to low-precision matrix multiplication), and this is why a GeForce is a perfectly valid AI machine — within very precise limits.

**RTX PRO / professional workstation (formerly Quadro).** The line historically called *Quadro*, then renamed first "RTX A" (Ampere: A4000, A5000, A6000), then "RTX Ada" (Ada Lovelace: RTX 5000 Ada, RTX 6000 Ada), and from 2025 **RTX PRO Blackwell** (RTX PRO 4000/5000/6000 Blackwell). These cards are intended for professional workstations: CAD, rendering, simulation, medical visualization, and increasingly, local AI development. They cost 3–5 times a GeForce with equivalent silicon.

**Datacenter (formerly Tesla).** A100, H100, H200, B100, B200, GB200, and the new Rubin generation. They do not have video outputs (in most cases), do not have fans in the SXM variant, and cannot be bought on Amazon. They are server components, designed to run at 100% load for years.

### 1.2 The seven differences that justify three lines

Let's look at them one by one, because each has practical consequences.

**(1) VRAM.** *Video RAM*: the memory mounted on the card, physically separate from system RAM. It is **the number one constraint** in AI. An RTX 5090 has 32 GB; an RTX PRO 6000 Blackwell has 96 GB; an H200 has 141 GB; a B200 has 192 GB. The difference is not "faster/slower": it is **you can run the model or not**. A model that doesn't fit into VRAM simply won't start, or will run at ridiculous speeds by relying on system RAM.

**(2) Memory type.** Consumer cards use **GDDR** (*Graphics Double Data Rate*, currently GDDR7): memory chips soldered around the GPU on the PCB. Datacenter cards use **HBM** (*High Bandwidth Memory*): stacks of memory dies vertically stacked and connected to the GPU via a silicon *interposer*, with a very wide bus (thousands of bits versus hundreds for GDDR). HBM costs enormously more but offers 3–5 times higher bandwidth. We will see in section 8 why bandwidth is almost as important as capacity.

**(3) ECC.** *Error Correcting Code*: memory capable of detecting and correcting single-bit errors. In a gaming PC, a bit flip due to a cosmic ray or a marginal memory cell produces a graphical artifact for one frame, and no one notices. In a three-week training run on 1,000 GPUs, it produces a NaN (*Not a Number*) that corrupts gradients and wastes days of computation. Datacenter and RTX PRO cards have ECC; GeForce cards do not (or have partial and uncertified forms).

**(4) Certified Drivers.** Nvidia distributes *Game Ready* drivers for GeForce cards (optimized for newly released games, updated every two weeks) and **enterprise/production branch** drivers for professional and datacenter cards: rarely updated, tested for months, guaranteed stable with specific certified software, and with long-term support. If your cluster needs to run for two years without surprises, this difference is worth money.

**(5) Form factor and cooling.** An RTX 5090 is three slots wide and has three fans that blow air into the case. Putting four of them in a server is impossible: they would suffocate each other. Datacenter cards come in two formats: **PCIe** (a double-slot card, *passive*, meaning without its own fans: it relies on the very strong airflow generated by the server chassis fans) and **SXM** (a proprietary module, without a PCIe connector, that screws directly onto a dedicated motherboard and allows for much higher TDP and interconnections). We will return to SXM in section 3.

**(6) Datacenter Use License.** This is the point that almost no one knows about and that causes the worst problems. The **EULA** (*End User License Agreement*) for Nvidia GeForce drivers explicitly prohibits *datacenter deployment* of GeForce cards, with a historical exception for blockchain mining and for academic/research use. In practice: **you cannot legally build a commercial cloud service based on RTX 4090/5090**. You can use them in your company, on your workstation, for your internal product; you cannot rent them to third parties as computing capacity, nor install them in a datacenter as service infrastructure. It is a contractual clause, not a technical block: the card works perfectly fine. But if you are a startup that wants to sell inference, this is a real legal risk. **[VOLATILE DATA: the EULA text has changed multiple times; always verify it as of the date of purchase.]**

**(7) NVLink.** The high-speed interconnection between GPUs. Present on datacenter cards, **absent on the entire consumer RTX 40 and RTX 50 line** and on most recent RTX PRO cards. This single product choice is what prevents building the economic equivalent of a datacenter node with consumer cards. It is the subject of section 4, and is probably the most important concept in the entire chapter.

### 1.3 How to read the nomenclatures

The three lines have three completely different naming logics, and this in itself is a source of confusion.

| Line | Schema | Example | How to read |
|---|---|---|---|
| GeForce | RTX + [generation][tier] | RTX **50**90 | "50" = 50 series (Blackwell consumer); "90" = highest tier. 80 = high, 70 = mid, 60 = entry |
| Workstation | RTX PRO + [tier number] + [architecture] | RTX PRO 6000 **Blackwell** | 6000 = top-tier; 5000/4000/2000 = lower tiers. The architecture is in the name to distinguish it from previous eponymous models (RTX 6000 Ada ≠ RTX PRO 6000 Blackwell!) |
| Datacenter | [Architecture letter] + [number] | **H**100, **B**200 | A = Ampere, H = Hopper, B = Blackwell, R = Rubin. The number increases with positioning. Suffixes: **G**B200 = with **G**race CPU; NVL = NVLink configuration; the number after NVL indicates the domain size |

**Classic nomenclature trap:** there is an "RTX 6000 Ada" and an "RTX PRO 6000 Blackwell". These are two different cards, two generations apart, with 48 GB and 96 GB respectively. They are often confused in reseller listings. Always read the architecture, never just the number.

---

## 2. Architectures: What truly changes between generations

### 2.1 The concept of "architecture" and "manufacturing process"

Two terms must be clearly separated.

**Architecture** is the logical design of the chip: how many computing units, how they are organized, what types of operations they can perform, how the cache hierarchy is structured, how memory is connected. Nvidia names each architecture after a scientist: Pascal (2016), Volta (2017), Turing (2018), Ampere (2020), Hopper (2022), Ada Lovelace (2022, consumer/pro only), Blackwell (2024), Rubin (2026).

The **manufacturing process** (or *node*) is the foundry technology with which the chip is physically manufactured: "4 nanometers", "3 nanometers". Caution: these numbers **no longer correspond to any real physical dimension** for at least a decade. They are commercial names. TSMC's "4N" used for Hopper and Blackwell is a derivative of the 5 nm node optimized for Nvidia. What truly matters is **transistor density** and energy efficiency per operation. A more advanced node allows more transistors to be placed in the same area and consumes less per operation — which is why the industry continues to pay exorbitant amounts to access it.

### 2.2 Ampere (2020) — A100

Ampere introduced, on the datacenter front, three things we now take for granted.

The first is the **third-generation Tensor Core with TF32 support**. A **Tensor Core** is a computing unit that doesn't perform one multiplication at a time, but executes a small matrix-matrix multiplication with accumulation (typically 4x4 or similar) in one go, which is the elementary operation of every neural network. **TF32** (*TensorFloat-32*) is a clever numerical format: it has the same dynamic range (the 8 exponent bits) as classic FP32 but only 10 mantissa bits like FP16. Result: models written in FP32 ran faster **without modifying a single line of code**, because the Tensor Core silently converted. It was a huge adoption accelerator.

The second is **HBM2e memory** with bandwidth around 2 TB/s in the 80 GB version — in comparison, a consumer GPU of those years was under 1 TB/s.

The third is **MIG** (*Multi-Instance GPU*): the ability to divide a single A100 into up to seven hardware-isolated logical GPUs, each with its own slice of memory, cache, and compute units. This is useful in the cloud, where a customer performing inference on a small model doesn't have to occupy (and pay for) an entire GPU.

The A100 exists in 40 GB and 80 GB, PCIe and SXM4. **[VOLATILE DATA]** In 2026, it is technically obsolete but commercially very active in the used market and on low-cost clouds, at prices that are a fraction of the original. For many inference workloads and for fine-tuning medium models, it is still the option with the best price/performance ratio on the secondary market.

### 2.3 Hopper (2022) — H100 and H200

Hopper is the generation that coincided with the explosion of LLMs (*Large Language Models*) and is, in fact, the workhorse on which most famous models have been trained.

Key innovations:

**The Transformer Engine and the FP8 format.** The Transformer Engine is a hardware+software block that dynamically analyzes, layer by layer, which tensors can be represented in **FP8** (8-bit floating point) without degrading model quality, and converts them on the fly while keeping the rest in FP16/BF16. Going from 16 to 8 bits **doubles theoretical throughput and halves the memory occupied by tensors**. It is the single most important leap of this generation.

**HBM3 memory** (80 GB, approximately 3.35 TB/s on SXM5) and, with the H200 revision, **HBM3e**: 141 GB and approximately 4.8 TB/s. Note: H200 **does not change the compute chip**. It is the same silicon as H100 with larger and faster memory. This is a pattern that Nvidia will repeat, and it is crucial to understand: **a higher number does not imply a more powerful chip, it often only implies more memory**. For LLM inference workloads — which are *memory-bound*, meaning limited by memory bandwidth and not by compute power — H200 is still significantly superior to H100, even with the same TFLOPS.

**Fourth-generation NVLink** at 900 GB/s per GPU and third-generation NVSwitch (see section 4).

**Confidential Computing:** the ability to perform computations on encrypted data in a hardware enclave, relevant for healthcare and finance.

### 2.4 Blackwell (2024–2025) — the architectural leap

Blackwell deserves extended discussion because it introduces a paradigm shift in the physical construction of the chip.

**The problem: the reticle limit.** In photolithography, the circuit image is projected onto the wafer through a mask. The maximum area that the machinery can expose in one go is called a **reticle**, and it is approximately 800 mm². A chip cannot be larger than that. Hopper was already practically at the limit (814 mm²). Nvidia therefore had two paths: wait for a better node (slow, uncertain) or **stop making single chips**.

**The solution: dual-die.** Blackwell is composed of **two separate dies**, each close to the reticle limit, mounted side-by-side on the same package and connected by an interconnect called **NV-HBI** (*NVIDIA High Bandwidth Interface*), which offers approximately **10 TB/s** of bandwidth between the two dies. This is an enormous figure: ten terabytes per second. The consequence is that, from the perspective of software, drivers, and programmers, **the two dies appear as a single coherent GPU**: a single memory space, a single CUDA device. It's not disguised multi-GPU; it's a single chip, stitched together in silicon. This is the true technical content of the "Blackwell leap." In total, approximately 208 billion transistors on TSMC 4NP node.

**FP4 and the second-generation Transformer Engine.** If FP8 halved the bits compared to FP16, Blackwell halves them again with **FP4** (specifically the proprietary **NVFP4** format, which uses block-level scaling factors to contain precision loss). Four bits per number means **sixteen possible values in total**. This seems absurd, and for training, it almost always is; but for **inference**, where the model is already trained and only needs to be executed, research has shown that with the right quantization techniques, the quality degradation is minimal. The gain is twofold: doubling of throughput and halving of memory occupied by weights.

**The "sub-generational": B100, B200, and GB200.** Here, a confusing point needs to be clarified. B100 and B200 **use the exact same silicon**. The difference is the configuration: TDP (*Thermal Design Power*, the thermal power that the cooling system must dissipate, used as a proxy for consumption) and frequencies.

- **B100**: ~700 W. Designed to be a *drop-in replacement* in racks designed for H100, which were already sized for 700 W. No infrastructure modifications, superior performance. It had a short commercial life and a marginal role: Nvidia pushed everyone towards B200.
- **B200**: ~1000–1200 W depending on the configuration. Same chip, unlocked. Significantly higher performance, but requires new racks and cooling.
- **GB200**: **is not a GPU**. It is a *superchip*: **one Grace CPU + two B200 GPUs** on a single module, connected by **NVLink-C2C** (*Chip-to-Chip*) at 900 GB/s. It is not a simple "commercial package": the CPU-GPU connection via NVLink-C2C, instead of via PCIe, is about seven times faster and is **cache-coherent**, meaning that the CPU and GPU see the same memory space without explicit copies. This changes how code is written: weights or KV-cache can be *offloaded* to the CPU's LPDDR memory (hundreds of GB) without paying the devastating price of a PCIe transfer.

**Blackwell Ultra (B300 / GB300).** As H200 was to H100, B300 is to B200: same architecture, increased HBM3e memory (up to ~288 GB per package), and optimizations for low-precision inference, with approximately 208 billion transistors. This is the "mid-generation" covering 2025–2026.

### 2.5 Rubin (2026) — the current generation

**[VOLATILE DATA — this section describes a product in ramp-up: verify everything.]**

Rubin is the architecture succeeding Blackwell. Nvidia announced its full production in early 2026, with availability to partners and cloud providers in the second half of the year. The key points, as publicly known in mid-2026:

- **Rubin GPU (often referred to as R100/R200)**: TSMC 3nm node, **dual-die** design like Blackwell but with approximately **336 billion transistors** (~1.6× Blackwell).
- **HBM4 Memory**: approximately **288 GB** per package with a declared bandwidth of up to approximately **22 TB/s** — almost triple that of Blackwell. The leap primarily comes from doubling the bus width per stack.
- **Compute**: in the order of **50 PFLOPS in NVFP4** per package (compared to ~20 for Blackwell).
- **Vera CPU**: replaces Grace. Custom ARM CPU with 88 "Olympus" cores, up to 1.5 TB of LPDDR5X, connected to GPUs via NVLink-C2C at 1.8 TB/s.
- **NVLink 6**: approximately 3.6 TB/s per GPU, double the Blackwell generation.
- **System**: the reference rack is the **VR200 NVL72** (72 GPU packages), with power consumption in the order of 190–230 kW per rack — compared to ~120–130 kW for the GB200 NVL72. Liquid cooling is **mandatory**: air-cooled configurations do not exist.

The declared roadmap continues with **Rubin Ultra** (2027, "Kyber" rack at ~600 kW) and **Feynman** (2028). The strategic message is clear and must be understood by anyone planning infrastructure: **Nvidia has adopted an annual cadence where not only the chip but the entire physical enclosure changes** — power, cooling, network. Anyone building a data center today is building it for hardware that, within two cycles, will no longer fit.

### 2.6 Generational summary table

| | Ampere (A100) | Hopper (H100) | Hopper (H200) | Blackwell (B200) | Blackwell Ultra (B300) | Rubin (R100) |
|---|---|---|---|---|---|---|
| Year | 2020 | 2022 | 2023–24 | 2024–25 | 2025–26 | 2026 (ramp) |
| Node | TSMC 7N | TSMC 4N | TSMC 4N | TSMC 4NP | TSMC 4NP | TSMC 3nm |
| Die | single | single | single | **dual-die (NV-HBI)** | dual-die | dual-die |
| Transistors | 54 bln | 80 bln | 80 bln | 208 bln | ~208 bln | ~336 bln |
| Memory | HBM2e 40/80 GB | HBM3 80 GB | HBM3e 141 GB | HBM3e 192 GB | HBM3e ~288 GB | **HBM4 ~288 GB** |
| Bandwidth | ~2.0 TB/s | ~3.35 TB/s | ~4.8 TB/s | ~8 TB/s | ~8 TB/s | **~22 TB/s** |
| Minimum Precision | FP16/BF16 | **FP8** | FP8 | **FP4 (NVFP4)** | FP4 | FP4/FP6 |
| NVLink | 4th gen, 600 GB/s | 4th gen, 900 GB/s | 900 GB/s | 5th gen, 1.8 TB/s | 1.8 TB/s | **6th gen, 3.6 TB/s** |
| TDP (SXM) | 400 W | 700 W | 700 W | ~1000–1200 W | ~1400 W | ~1800–2300 W |

*All values are indicative and refer to top-tier SXM variants. PCIe versions always have lower TDP and NVLink bandwidth.*

---

## 3. Datacenter cards, one by one

### 3.1 Premise: SXM versus PCIe

Before looking at individual products, it's important to understand this distinction, as it's the first question any salesperson will ask you.

**PCIe** (*Peripheral Component Interconnect Express*) is the standard bus by which any card connects to the motherboard of any computer. A datacenter GPU in PCIe format looks like a very long, dual-slot video card, **without fans**: it is *passive*, and relies on the strong airflow that the server fans push through the chassis. Advantages: it mounts in a standard server, costs less, and can be bought one at a time. Disadvantages: **TDP is limited** (typically 300–600 W, because the connector and slot cannot handle more) and, critically, **interconnection between GPUs is limited** — at most a single NVLink bridge between two cards, or nothing at all on recent generations.

**SXM** is a proprietary Nvidia format. The module does not have a PCIe connector: it bolts onto a dedicated **baseboard** (typically HGX, the 4 or 8 GPU board that Nvidia sells to OEMs). The baseboard provides power (up to 700–1400 W per module) and NVLink lines to the NVSwitches. Advantages: much higher TDP (thus higher frequencies, thus more performance from the same silicon), and **full-bandwidth NVLink between all GPUs in the node**. Disadvantages: it can only be purchased as a complete 4 or 8 GPU system, it is very expensive, and it requires a specially designed server.

**The practical rule:** if you are doing distributed *training* on multiple GPUs, you need SXM. If you are doing *inference* with models that fit on a single GPU, PCIe is more than enough — and much cheaper.

### 3.2 A100 (40 GB / 80 GB)

- **VRAM:** 40 GB or 80 GB HBM2e
- **Bandwidth:** ~1.55 TB/s (40 GB) / ~2.0 TB/s (80 GB SXM)
- **TDP:** 250–300 W (PCIe) / 400 W (SXM4)
- **Form factor:** PCIe and SXM4
- **Indicative performance:** ~312 TFLOPS in FP16 with sparsity, ~19.5 TFLOPS FP32
- **Price [VOLATILE DATA]:** long out of production; on the used and refurbished market, they can be found at a fraction of the original price (which was around $10,000–$15,000 for the 80 GB). On clouds, it is among the cheapest options available.
- **Use cases:** inference of models up to ~30–70 billion quantized parameters, fine-tuning, academic research, classic HPC workloads (simulation, CFD). It does **not** have FP8: for modern LLM inference, which relies on FP8/FP4, this is a serious architectural limitation.
- **Who should buy it:** those with a tight budget, a known workload, and the expertise to manage used hardware. It is the "safe used option" in the industry.

### 3.3 H100

- **VRAM:** 80 GB HBM3 (there is also an H100 NVL with 94 GB, a dual-board variant)
- **Bandwidth:** ~3.35 TB/s (SXM5) / ~2.0 TB/s (PCIe)
- **TDP:** 700 W (SXM5) / 350 W (PCIe)
- **Performance:** ~1,979 TFLOPS FP8 with sparsity (SXM5). The PCIe version performs **about 20–30% less** due to halved TDP and lower bandwidth.
- **Price [VOLATILE DATA]:** the publicly reported order of magnitude is $25,000–$40,000 per card; an HGX H100 8-GPU node is in the $250,000–$400,000 range.
- **Use cases:** it was *the* GPU of the LLM boom. Training models up to tens of billions of parameters (in clusters), high-throughput inference, serious fine-tuning.

**Common mistake:** buying the H100 PCIe thinking it's "a slightly cheaper H100". It's not: it's a significantly slower GPU, with limited or no NVLink, which can perform half as well in a multi-GPU workload. If your workload is multi-GPU, the PCIe is almost always a false economy.

### 3.4 H200

- **VRAM:** 141 GB HBM3e
- **Bandwidth:** ~4.8 TB/s
- **TDP:** 700 W (SXM5), same as its predecessor
- **Compute chip:** **identical to H100**. No increase in TFLOPS.
- **Price [VOLATILE DATA]:** above the H100, in the order of $30,000–$40,000.
- **Use cases:** LLM inference. Here the gain is dramatic — often 1.5–2x compared to H100 on the same model — **not because it computes more, but because autoregressive inference is limited by memory bandwidth**. Each generated token requires re-reading all model weights from VRAM. More bandwidth = more tokens per second, period.

This is the moment to state something clearly that will recur throughout the chapter: **in LLM inference, memory bandwidth matters more than TFLOPS.** Anyone comparing GPUs by only looking at TFLOPS is looking at the wrong metric.

### 3.5 B100, B200, GB200

- **B100:** ~192 GB HBM3e, ~8 TB/s, 700 W. Compatible with H100 infrastructure. Transitional product, not widely adopted.
- **B200:** ~192 GB HBM3e, ~8 TB/s, ~1000–1200 W. Approximately 20 PFLOPS in FP4 dense. This is the true Blackwell datacenter GPU, available in 8-GPU HGX B200 baseboards.
- **GB200:** 1 Grace CPU + 2 B200 GPUs on a superchip. ~384 GB of HBM3e (2×192) plus up to 480 GB of LPDDR5X on the Grace. Module power consumption around 2700 W. **Requires liquid cooling.**
- **Price [VOLATILE DATA]:** a single B200 is in the range of $30,000–$40,000; a complete GB200 NVL72 system has been estimated by the specialized press at around $3 million.

### 3.6 GB200 NVL72: when the compute unit becomes the rack

This is the most important concept in this section, and it marks the end of an era.

Up until Hopper, the compute unit was **the node**: a server with 8 GPUs connected by NVLink, and the nodes themselves connected by network (InfiniBand). The bandwidth gap between "inside the node" (900 GB/s) and "outside the node" (50–100 GB/s) was an order of magnitude, and the entire theory of distributed training was built around this disparity.

The **GB200 NVL72** rewrites the rule. It is a rack that contains **36 GB200 superchips**, meaning **72 B200 GPUs + 36 Grace CPUs**, **all connected by a single NVLink domain** through 9 NVSwitch trays. The 72 GPUs see a shared memory space of approximately 13.5 TB of HBM3e, with an aggregated NVLink bandwidth in the order of 130 TB/s. From a software perspective, **the entire rack behaves like a gigantic GPU**.

Why is this important? Because a model with hundreds of billions or trillions of parameters, which previously had to be split across many nodes paying the network penalty at each layer, now lives within a single, ultra-high-bandwidth domain. For **MoE** (*Mixture of Experts*) models (architectures where each token activates only a subset of "experts" and thus generate a lot of routing traffic between GPUs), the gain is enormous.

The price to pay is physical: ~120–130 kW per rack, mandatory liquid cooling, and an electrical infrastructure that most existing datacenters in the world **simply do not have**. A traditional rack consumes 5–15 kW.

### 3.7 Grace: why ARM in the datacenter

**Grace** is the CPU designed by Nvidia on the **ARM** architecture (specifically Neoverse V2 cores), with 72 cores, **LPDDR5X** memory soldered on the package (up to 480 GB, ~500 GB/s bandwidth) and — this is the key point — an **NVLink-C2C** connection to the GPU at 900 GB/s, **cache-coherent**.

Why ARM and not x86? Three reasons, in order of real importance:

1. **The connection.** Nvidia couldn't put NVLink-C2C into an Intel or AMD processor: it doesn't control those designs. To have a CPU–GPU path seven times faster than PCIe, it had to make its own CPU. ARM gave it an architecture license to build upon. **The Grace CPU exists primarily to be the right socket next to the GPU**, not because it is a revolutionary CPU in itself.
2. **Power efficiency.** In a 130 kW rack, every watt spent by the CPU is a watt taken away from the GPUs. ARM cores with LPDDR memory (low-power memory, like in phones, soldered and not expandable) offer an excellent performance-per-watt ratio for the role they need to play here: feeding data to the GPUs, managing I/O, orchestrating.
3. **Vertical control.** Nvidia wants to sell the complete system, not just a component. Grace is part of this strategy.

With Rubin, Grace is replaced by **Vera**, with entirely custom ARM cores.

**Practical implication for the developer:** a Grace system is an **aarch64** machine, not x86_64. Your Docker container, your compiled Python wheel, your proprietary binary must exist for ARM. In the mainstream AI ecosystem (PyTorch, CUDA, Nvidia's NGC containers) this is now resolved. In legacy enterprise software, often not. **It's the first check to make before buying.**

---

## 4. NVLink and Interconnections

### 4.1 Why PCIe is not enough

Imagine training a model on 8 GPUs with **data parallelism**: each GPU receives a different slice of the data, calculates its gradients (the derivatives of the loss function with respect to each parameter), and then all of them must **average the gradients among themselves** before updating the weights. This operation is called **all-reduce** and must be performed **at every single training step**, i.e., tens of thousands of times.

How much data is exchanged? As much as the model's parameters, multiplied by the byte size of each. For a 7 billion parameter model in BF16, that's 14 GB, and all-reduce moves about twice that.

Now let's do the math. **PCIe 5.0 x16** offers about **64 GB/s** per direction. **NVLink 4** offers **900 GB/s**. The ratio is 14 to 1. In a real training scenario, this means that with PCIe, GPUs spend a huge fraction of the time **idle, waiting for data from their companions**. Actual utilization plummets, and eight GPUs perform like three or four.

Then there's **latency**, which is a distinct problem from bandwidth: PCIe goes through the CPU's *root complex*, with protocol overhead. NVLink is a direct point-to-point connection between GPUs, with an order of magnitude lower latency. In **tensor-parallel** models (where a single layer is split across multiple GPUs and each GPU must exchange activations **within the forward pass**, dozens of times per token), latency is more critical than bandwidth. With PCIe, tensor parallelism is simply **impractical**.

### 4.2 NVLink: Generations

**NVLink** is Nvidia's proprietary interconnect introduced in 2016 with Pascal. It is not a bus: it is a set of **point-to-point serial links** (called *bricks* or *links*) that can be aggregated. Each generation has increased the speed per link and the number of links per GPU.

| Generation | Year | Architecture | Bidirectional Bandwidth per GPU |
|---|---|---|---|
| NVLink 1 | 2016 | Pascal (P100) | 160 GB/s |
| NVLink 2 | 2017 | Volta (V100) | 300 GB/s |
| NVLink 3 | 2020 | Ampere (A100) | 600 GB/s |
| NVLink 4 | 2022 | Hopper (H100/H200) | 900 GB/s |
| NVLink 5 | 2024 | Blackwell (B200/GB200) | 1,800 GB/s (1.8 TB/s) |
| NVLink 6 | 2026 | Rubin | ~3,600 GB/s (3.6 TB/s) **[VOLATILE DATA]** |

**NVSwitch.** Connecting 8 GPUs "all-to-all" directly would require 28 physical links, and it doesn't scale. The NVSwitch is a dedicated **switch chip**: each GPU connects to the switch, and the switch routes the traffic. The result is that **each GPU talks to every other GPU at full bandwidth**, simultaneously, without bottlenecks. In the GB200 NVL72, NVSwitches move out of the server and become dedicated trays in the rack, extending the NVLink domain to 72 GPUs. This is exactly the leap that made "the rack as a compute unit" possible.

### 4.3 The removal of NVLink from consumer and professional cards

This paragraph is probably **the most important in the chapter for anyone building a workstation**.

- RTX 3090 (Ampere) had NVLink: two cards could be paired with a bridge.
- **RTX 4090 (Ada) and RTX 5090 (Blackwell consumer) do NOT have NVLink.** No bridge, no option.
- Even in the professional line, support has drastically reduced: the RTX 6000 Ada did not have NVLink; the RTX PRO Blackwell cards don't either.

**The concrete implications:**

1. **You cannot sum VRAM.** Two RTX 5090s do not make a 64 GB GPU. They make two separate 32 GB GPUs. A model that doesn't fit into 32 GB doesn't fit, period — unless you manually split it over PCIe, with the penalties discussed above.
2. **Tensor parallelism is out of the question.** You can do *pipeline parallelism* (put layers 1–20 on GPU A and 21–40 on GPU B, with only one exchange at the boundary): this also works on PCIe because the traffic is much lower. But the speed will be mediocre.
3. **Consumer multi-GPU is mainly for independent workloads:** four different jobs on four GPUs, four models in inference, four experiments in parallel. This works perfectly. Distributed training of a single large model, no.

It's not a technical limitation: it's a **commercial decision**. Nvidia doesn't want people building cheap training nodes with gaming cards. Knowing this will prevent you from spending €10,000 on a workstation that won't do what you hope.

### 4.4 Beyond the node: InfiniBand and Ethernet/RoCE

When you go beyond the GPUs of a single node (or a single NVL72 rack), you need to move to the **network**.

**InfiniBand** is a networking technology designed for HPC (*High Performance Computing*), acquired by Nvidia with Mellanox. Its distinctive feature is **RDMA** (*Remote Direct Memory Access*): a GPU can write directly into the memory of a GPU on another server **without involving the CPUs or operating systems**. The result is very low latency (~1 microsecond) and reliable congestion control. Current generations are NDR (400 Gb/s) and XDR (800 Gb/s). It is the classic choice for large training clusters.

**Ethernet with RoCE** (*RDMA over Converged Ethernet*) brings RDMA to standard Ethernet. Advantages: lower cost, utilizes existing company expertise and equipment, avoids vendor lock-in. Disadvantages: Ethernet is inherently *lossy* (can drop packets under congestion), and AI requires configuring lossless networking mechanisms (PFC, ECN) which are notoriously difficult to fine-tune. Nvidia sells its own AI-optimized Ethernet platform (**Spectrum-X**) precisely to bridge this gap.

**Rule of thumb:** below ~64 GPUs, the network is rarely the main bottleneck, and Ethernet is fine. Above thousands of GPUs, the quality of the interconnection determines the scalability of the entire investment, and every percentage point of efficiency is worth millions.

---

## 5. Workstations and Pre-built Systems

### 5.1 The DGX Line

**DGX** is Nvidia's line of "turnkey" systems: complete servers, designed, assembled, and supported by Nvidia, with pre-installed software stack. You're not buying GPUs: you're buying **a guaranteed working system**, with enterprise support, and saving months of integration effort.

- **DGX H100:** 8× H100 SXM5 (640 GB HBM3), 2 Intel Xeon CPUs, ~10 kW. **[VOLATILE DATA]** Historically reported list price around $300,000–$400,000.
- **DGX B200:** 8× B200 (1,440 GB HBM3e), air-cooled (the last DGX to allow this), ~14.3 kW. Order of magnitude: $500,000.
- **DGX GB200 / GB200 NVL72:** the complete rack described in section 3.6. Millions of dollars.
- **DGX SuperPOD:** the aggregation of many DGX units into a turnkey supercomputer, with networking, storage, and management software. We're talking tens of millions.

**Who is the DGX line for?** Organizations where the cost of engineering time outweighs the hardware premium: funded research labs, Fortune 500 companies, cloud providers. If you're calculating how much you'd save by buying components separately, **you are not the DGX customer** — and that's perfectly fine.

### 5.2 DGX Spark (formerly Project DIGITS)

This product deserves attention because it's Nvidia's first serious attempt to bring datacenter architecture to the desktop at a non-absurd price, and because it is **profoundly misunderstood**.

**What it is:** a box the size of a small Mac Mini, based on the **GB10 Grace Blackwell** superchip: an ARM CPU (20 cores, in collaboration with MediaTek) and a Blackwell GPU on a single package, with **128 GB of unified LPDDR5X memory** shared between CPU and GPU. Approximately **1 PFLOP of FP4 compute**. Power consumption around 240 W: powered by a standard household outlet. **[VOLATILE DATA]** Launch price around $3,000–$4,000.

**What it's really for.** The extraordinary thing is the **memory capacity**: 128 GB accessible to the GPU means being able to load models that would never fit on any consumer GPU. Nvidia talks about models up to ~200 billion parameters (quantized) on a single unit, and up to ~400 billion by connecting two. For a developer who needs to **prototype, experiment, test pipelines, perform local inference on large models**, it is an unparalleled object at that price.

**The limit, and it must be said with brutal clarity: memory bandwidth.** The GB10's LPDDR5X offers in the order of **270 GB/s**. Compare that to the ~1,800 GB/s of an RTX 5090 or the 4,800 GB/s of an H200. It is **6–18 times slower**. And we have already established that **LLM inference is limited by memory bandwidth**. Practical consequence: on the DGX Spark, a large model **fits**, but generates tokens slowly. It is not a production machine, it is not a throughput benchmark machine.

**The correct positioning is this:** the DGX Spark is a **development machine**. It is used to write and validate code that will then run on a real DGX or in the cloud, with the **exact same software stack and the same ARM+Blackwell architecture**. The value is in the fidelity of the environment, not in speed. Anyone who buys it expecting a fast inference GPU will be disappointed, and the fault lies with ambiguous marketing, not the product.

### 5.3 DGX Station (GB300)

**[VOLATILE DATA]** The new-generation DGX Station is a desktop workstation (tower format) based on the **GB300 Grace Blackwell Ultra** superchip, with approximately **784 GB of total coherent memory** (HBM3e on the GPU + LPDDR5X on the CPU), integrated high-speed ConnectX NIC, and standard wall outlet power. It is the missing link between the DGX Spark and the rack: a **true** datacenter machine, with true HBM bandwidth, in a format that sits next to a desk. Expected price: in the order of tens of thousands of dollars, far from the Spark and well below a rack.

### 5.4 The alternative: building your own workstation

Does it make sense? Let's make an honest comparison.

| Configuration | Total VRAM | Bandwidth per GPU | NVLink | Indicative Cost [VOLATILE] | Suitable for |
|---|---|---|---|---|---|
| 2× RTX 5090 | 2×32 GB (not summable) | ~1.8 TB/s | ❌ | ~€7,000–9,000 | Inference of medium models, LoRA, Stable Diffusion, parallel jobs |
| 4× RTX 5090 | 4×32 GB | ~1.8 TB/s | ❌ | ~€15,000–20,000 | As above ×2; power and cooling become a serious problem |
| 2× RTX PRO 6000 Blackwell | 2×96 GB | ~1.8 TB/s | ❌ | ~€20,000–25,000 | Large model inference; serious fine-tuning; legitimate commercial use |
| DGX Spark | 128 GB unified | ~0.27 TB/s | — | ~€4,000 | Development, prototyping, huge but slow models |
| 1× H100 PCIe (used) | 80 GB | ~2.0 TB/s | limited | ~€20,000–25,000 | Professional inference, legitimate datacenter use |
| HGX 8× H100 Node | 640 GB | ~3.35 TB/s | ✅ 900 GB/s | ~€250,000–400,000 | True distributed training |

**Fundamental note on domestic power.** Four RTX 5090s alone draw 2,300 W peak, plus CPU, fans, and power supply losses: easily exceeding 3,000 W. A standard Italian domestic socket provides 3,000–3,300 W **for the entire apartment**. This is not a detail: it is a constraint that physically closes the discussion. Beyond two high-end cards, a dedicated line and serious consideration for heat extraction from a room where you are dumping the thermal equivalent of three space heaters are needed.

---

## 6. Consumer GPUs for AI: when they are enough

### 6.1 RTX 4090 and RTX 5090

**RTX 4090** (Ada, 2022): 24 GB GDDR6X, ~1,008 GB/s bandwidth, 450 W.
**RTX 5090** (Blackwell, 2025): 32 GB GDDR7, ~1,792 GB/s bandwidth, 575 W. **[VOLATILE DATA: MSRP ~ $2,000, street price historically much higher.]**

The leap of the 5090 is significant for AI **especially for bandwidth** (+78%) and for native FP4 support, not so much for the 32 GB — which is still little.

**What you actually do with it, concretely:**

- **Inference of quantized LLMs.** A 7–14 billion parameter model in 4-bit occupies 4–9 GB and flies. A quantized 32B barely fits into 32 GB. A 70B in 4-bit requires ~40 GB: **it doesn't fit**, and this is the number one frustration for 5090 owners.
- **LoRA / QLoRA fine-tuning.** **LoRA** (*Low-Rank Adaptation*) is a technique that freezes the weights of the original model and trains only small additional low-rank matrices, reducing trainable parameters by orders of magnitude. **QLoRA** adds 4-bit quantization of the base model. Together, they allow fine-tuning of 7–13B models on a single consumer card. This is the use case where a 4090/5090 truly shines, and it's why there's an entire ecosystem of fine-tuned models built by people with a single gaming card.
- **Image/video generation** (Stable Diffusion, Flux, and similar): excellent. These models are compute-bound rather than memory-bound and fit comfortably in 24–32 GB.
- **Computer vision, YOLO, ASR, embedding, training small models from scratch:** perfect.

**The wall:** 32 GB. There's no way around it. No NVLink. No expansion. When the model doesn't fit, the card — however powerful — is useless for that task.

### 6.2 RTX PRO 6000 Blackwell

**[VOLATILE DATA]** The RTX PRO 6000 Blackwell offers **96 GB of GDDR7 with ECC**, bandwidth around 1.8 TB/s, TDP of 600 W in the workstation variant (there is also a 300 W *Max-Q* version and a passive *Server Edition* for racks). Indicative price: $8,000–$10,000.

**When does it justify the price?** The calculation is straightforward. It costs about 4–5 times an RTX 5090 and offers **3 times the VRAM** for the same bandwidth. If you only considered TFLOPS per euro, it would be a bad deal. But:

1. **96 GB allows models to fit that 32 GB cannot.** A 70B in 4-bit fits comfortably. The leap is not quantitative, it is **binary**: you can or you cannot.
2. **It has ECC**, and for fine-tuning that lasts days, it matters.
3. **It does not have the EULA restriction on the datacenter**: you can use it in a commercial service.
4. **In a workstation, two RTX PRO 6000 = 192 GB of VRAM** (in separate cards, without NVLink, but with practicable pipeline parallelism) at a cost of about €20,000, compared to much higher figures for equivalent datacenter hardware in capacity.

It is, today, **the rational choice for the vast majority of small to medium-sized companies that want to do AI seriously locally**. It is not cheap; it is the least expensive among those that truly solve the problem.

### 6.3 The three limits to always keep in mind

1. **No NVLink.** GPUs do not sum memory. Let's repeat this because it is the most expensive mistake in the industry.
2. **VRAM is a wall, not a ladder.** There is no gradual degradation: there is "it works" and "OutOfMemoryError".
3. **The datacenter license.** If your business model involves selling access to computation, GeForce cards are out of the question.

---

## 7. The software moat: CUDA

### 7.1 What it is and why it is Nvidia's real product

**CUDA** (*Compute Unified Device Architecture*) is the parallel computing platform introduced by Nvidia in 2007. At the time, it seemed like a strange gamble: why invest heavily to allow researchers to use video cards for general-purpose mathematics, when the market was video games? The answer came fifteen years later.

CUDA is a language (an extension of C/C++), a compiler, a runtime, a programming model. But **the real moat is not CUDA itself: it's what has grown on top of it.**

- **cuDNN** (*CUDA Deep Neural Network library*): Hand-optimized kernels, for each GPU architecture, for convolutions, normalizations, activation functions. Every deep learning framework in the world relies on it.
- **cuBLAS**: Linear algebra (matrix multiplications) at the maximum performance obtainable from the silicon.
- **NCCL** (*NVIDIA Collective Communications Library*): Multi-GPU communication primitives (all-reduce, all-gather, broadcast) optimized for NVLink and InfiniBand. Without NCCL, distributed training would have to be written by hand.
- **TensorRT** and **TensorRT-LLM**: Inference compilers that take a trained model and transform it into an optimized engine for a specific GPU (kernel fusion, quantization, automatic selection of the best algorithms). The gain compared to naive execution is often 2–5×.
- **Triton Inference Server**, **NeMo**, **NGC** containers: The entire production stack.

### 7.2 What this means in practice when choosing hardware

It means that when you compare an Nvidia GPU with an alternative (AMD Instinct MI300X/MI355X, Intel Gaudi, Google TPU, startup accelerators) **you are not comparing two chips. You are comparing two ecosystems.** And the comparison must be made honestly on three levels:

**Level 1 — Does it work?** On Nvidia: `pip install torch`, the code runs, done. On alternatives: it depends on the maturity of the stack (AMD's ROCm has improved enormously but remains more fragile), the version, the model.

**Level 2 — Does it work fast?** Low-level optimization (fused kernels, optimized attention like FlashAttention, quantization) comes out first and better on CUDA. A new model on Nvidia runs optimized from day one; on alternatives, often months later, or never.

**Level 3 — Does it work when it breaks?** When a training crashes at three in the morning, the probability that someone has already seen that error and solved it on Stack Overflow, GitHub, or a forum is enormously higher on CUDA. **This is the real moat**, and it's made of people, not silicon.

**The practical consequence for purchasing:** the total cost of ownership of a cheaper alternative includes the time of your engineers. If an engineer costs €400/day and the alternative makes you lose twenty days of integration, you've burned €8,000 — often more than the savings. Alternatives make sense under two conditions: **at large scale** (where the unit saving multiplies by thousands of units and you can afford a dedicated team) or **for a narrow and stable workload** (a single model, in inference, which you validate once and never touch again). In between, Nvidia wins for reasons that have nothing to do with TFLOPS.

---

## 8. How to evaluate a GPU for AI: explicit criteria

### 8.1 Criterion #1: VRAM

**Always, in any case, before anything else.** A super-fast GPU with too little memory is useless; a slow GPU with enough memory still gets the job done.

The calculation must be made by summing **three items**:

**(a) Model weights.** Number of parameters × bytes per parameter:
- FP32 (32 bit) = 4 bytes → a 7B model occupies 28 GB
- FP16/BF16 (16 bit) = 2 bytes → 14 GB
- FP8 (8 bit) = 1 byte → 7 GB
- INT4/FP4 (4 bit) = 0.5 bytes → 3.5 GB

**(b) Optimizer state, if you are training.** With Adam (the standard optimizer), you need **two additional states per parameter** (momentum and variance) plus gradients. Rule of thumb: **mixed precision training costs approximately 16–20 bytes per parameter**, which is **8–10 times** the inference-only cost in FP16. A 7B model that occupies 14 GB for inference requires **over 100 GB** for full fine-tuning. This is why LoRA exists.

**(c) Activations and KV-cache.** Activations are the intermediate results of each layer and grow **linearly with batch size**. The **KV-cache** (*Key-Value cache*) is the memory that, during text generation, stores already computed attention keys and values to avoid recomputing them for each token: it grows **linearly with context length and the number of concurrent requests**. In an inference service with long contexts and many users, **the KV-cache can exceed the size of the model itself**. This is the item everyone forgets to calculate, and it's what makes production servers explode.

### 8.2 Criterion #2: Memory Bandwidth

As repeatedly stated: **autoregressive LLM inference is memory-bound**. To generate **every single token**, the GPU must read **all model weights** from VRAM. The theoretical upper limit of tokens per second is therefore, as a first approximation:

> **tokens/s ≈ memory bandwidth (GB/s) ÷ model size in memory (GB)**

Example: a 14 GB model on a card with 1,800 GB/s yields a ceiling of approximately 128 tokens/s (in practice, 60–80% of this value is achieved). The same card with double the bandwidth would yield double the tokens. **TFLOPS do not appear in this formula.** This is why H200 beats H100 despite having the same chip.

Note: the formula applies to **generation** (the *decode* phase). The **prefill** phase (processing the input prompt, which is parallelizable across all tokens together) is instead **compute-bound**: there, TFLOPS matter a lot. A workload with very long prompts and short responses has a completely different profile from a conversational chatbot.

### 8.3 Criterion #3: Numerical Precision

This needs to be well understood, because manufacturers declare TFLOPS in the precision that makes them look best.

| Format | Bits | Structure | Typical Use |
|---|---|---|---|
| **FP64** | 64 | 1 sign, 11 exponent, 52 mantissa | Scientific simulation. **Irrelevant for AI** — but datacenter GPUs have it and consumer GPUs do not |
| **FP32** | 32 | 1+8+23 | Historical "full" precision for deep learning |
| **TF32** | 19 used (in 32-bit container) | 1+8+10 | Nvidia format: FP32 range, FP16 mantissa. Transparent acceleration |
| **FP16** | 16 | 1+5+10 | Classic mixed precision. Restricted dynamic range → risk of overflow/underflow, requires loss scaling |
| **BF16** | 16 | 1+**8**+7 | *Brain Float*: same range as FP32, less mantissa. **More robust than FP16 in training**, now the de facto standard |
| **FP8** | 8 | E4M3 or E5M2 | Inference and (with caution) training on Hopper/Blackwell. Doubles throughput and halves memory |
| **FP4 / NVFP4** | 4 | block scaling | Inference only, on Blackwell and later. Doubles again. Requires accurate quantization |

**The golden rule when reading spec sheets:** a TFLOPS number without specified precision **means nothing**. And if you read "with sparsity," know that this number is **double** the real number under normal conditions: it assumes a 2:4 sparsity structure in weights that most real models do not have. Nvidia always states this, in a small note. **Divide by two.**

### 8.4 Criterion #4: Interconnect

If the workload is **multi-GPU on a single model**, interconnect is not a detail: it's the difference between working and not working. Review section 4. If the workload is **multi-GPU with independent jobs**, interconnect is irrelevant and you can ignore it completely.

### 8.5 Criterion #5: TDP, Power, and Cooling

**TDP** is the thermal design power. It must be multiplied by the number of cards and added to the rest of the system; then a 20–30% margin must be added for transient peaks (modern GPUs have instantaneous peaks much higher than the nominal TDP, and undersized power supplies shut down abruptly). Then the problem of **where that heat goes** must be solved: a workstation with two 5090s under load heats a room in a way that no one who hasn't experienced it can imagine. And finally, the **cost of energy** must be calculated: 2 kW × 8 hours/day × 250 days × €0.30/kWh ≈ **€1,200/year** just for electricity, not counting air conditioning.

### 8.6 Criterion #6: Cost per GB of VRAM

A crude but useful metric for an initial comparison. **[ALL VOLATILE DATA]**

| Card | VRAM | Indicative Price | €/GB |
|---|---|---|---|
| RTX 5090 | 32 GB | ~€2,500 | ~€78/GB |
| RTX PRO 6000 Blackwell | 96 GB | ~€9,000 | ~€94/GB |
| DGX Spark | 128 GB | ~€4,000 | ~€31/GB (but 6× lower bandwidth!) |
| H100 PCIe | 80 GB | ~€25,000 | ~€310/GB |
| H200 SXM | 141 GB | ~€35,000 | ~€250/GB |

This table says two things. First: **the DGX Spark is unbeatable for euro/GB, and terrible for euro/(GB/s)** — confirming it's a development machine, not a production one. Second: **datacenter cards cost 3–4 times more per GB than professional cards**, and you're paying for ECC, HBM, NVLink, licensing, and support. If you don't use any of those four things, **you're paying for nothing**.

### 8.7 Criterion #7: Buy or Rent?

This question should be asked before any other, and the answer depends on only one number: **the utilization factor**.

A cloud GPU costs (order of magnitude, **[VOLATILE]**) $2–4/hour for an H100. In a year of continuous use, that's ~ $25,000 — which is **the purchase price of the card**. Hence:

- **Usage below 30–40%:** Rent. Period. Buying hardware that sits idle is burning capital.
- **Usage above 60–70%, for at least 18–24 months:** Buying is convenient, provided you can manage the infrastructure (and the cost includes space, electricity, cooling, network, maintenance, and a system administrator's time).
- **Unpredictable peaks:** Hybrid — local hardware for baseline, cloud for peaks.
- **Sensitive data / regulatory constraints (healthcare, defense, strict GDPR):** Local hardware may be mandatory regardless of economic convenience.

A very common mistake in small businesses: buying a €20,000 workstation for a project that runs two weeks a month. It would have cost €800 in the cloud.

### 8.8 Decision table: workload → recommended GPU

| Workload | Minimum VRAM | Recommendation | Economical alternative |
|---|---|---|---|
| Learning, courses, tutorials | 8–12 GB | RTX 4060 Ti 16GB / RTX 5070 | Free Google Colab |
| Stable Diffusion / image generation | 12–24 GB | RTX 4090 / 5090 | RTX 4070 Ti Super 16GB |
| LLM inference 7–14B quantized | 8–16 GB | RTX 4090 / 5090 | Mac with Apple Silicon (unified memory) |
| LLM inference 30–70B quantized | 40–80 GB | **RTX PRO 6000 Blackwell (96GB)** | 2× RTX 5090 with pipeline parallelism; DGX Spark (slow) |
| LoRA/QLoRA fine-tuning on 7–13B | 24–32 GB | RTX 5090 | Used RTX 4090 |
| LoRA fine-tuning on 70B | 80+ GB | RTX PRO 6000 / H100 | Cloud by the hour |
| Full fine-tuning 7B | 100+ GB | H100 / H200 (or multi-GPU) | Cloud, always |
| Production inference, high throughput | depends | **H200** (bandwidth is everything) | H100 if budget is tight |
| Training a large model from scratch | thousands of GB | HGX B200 / GB200 NVL72 | Cloud. Always and only cloud, unless you are a hyperscaler |
| Development and prototyping on huge models | 128+ GB | **DGX Spark** | Cloud by the hour |
| Computer vision, YOLO, ASR, embedding | 8–24 GB | RTX 4090 / 5090 | Any recent RTX 12–16 GB |
| Commercial inference service for third parties | depends | Any **datacenter or RTX PRO** | None: GeForce are excluded by the EULA |

---

## 9. Typical buyer mistakes

We list them here because they are the most costly and recur with impressive regularity.

**1. Buying two GPUs thinking you can sum the VRAM.** It doesn't happen. Without NVLink (and consumer cards don't have it), two 32 GB cards do not make 64 GB for a single model.

**2. Looking at TFLOPS instead of VRAM and bandwidth.** TFLOPS are the number marketing wants you to look at. For LLM inference, they are almost irrelevant.

**3. Not realizing that the stated number is "with sparsity."** Halve it.

**4. Confusing H100 PCIe and H100 SXM.** Same name, very different performance.

**5. Forgetting KV-cache in sizing.** The model fits, the service goes into production, and at the tenth concurrent user, the server explodes.

**6. Buying hardware for 15% utilization.** The cloud existed.

**7. Ignoring the EULA on the datacenter.** Building a commercial product on GeForce is a legal risk that no serious investor will accept during due diligence.

**8. Underestimating power and heat.** The 4-GPU workstation project almost always dies at the electricity meter, not on the budget.

**9. Buying the DGX Spark expecting speed.** It's a development machine. Bandwidth is 270 GB/s. Reread section 5.2.

**10. Not considering the ARM architecture.** If you buy a Grace/Vera system, verify that your stack exists for aarch64 **before** signing.

**11. Buying "the latest generation" on principle.** If your workload is inference on medium models, a used A100 or a second-hand H100 will give you a price/performance ratio that no new card can approach.

---

## Operational Summary — Decision Checklist for Choosing an AI GPU

Follow the order. Do not skip steps: each one can close the decision on its own.

**Step 1 — Define the workload, in writing.**
- [ ] Training, fine-tuning, or inference only?
- [ ] Which model, how many parameters, at what precision?
- [ ] What context length and how many concurrent requests at peak?
- [ ] Is it a one-off project or an ongoing service?

**Step 2 — Calculate the necessary VRAM.**
- [ ] Weights = parameters × bytes/parameter (4 / 2 / 1 / 0.5)
- [ ] If training: × 8–10 for optimizer and gradients (or use LoRA/QLoRA)
- [ ] Add activations (∝ batch size) and KV-cache (∝ context × concurrency)
- [ ] Add a **20% margin**
- [ ] → This number is the **eliminatory filter**. Anything below it is excluded.

**Step 3 — Decide whether to buy or rent.**
- [ ] Expected usage below 40% → **cloud**, end of discussion
- [ ] Privacy/regulatory constraints prohibiting cloud → **on-premises**, at any cost
- [ ] Usage above 60% for 18+ months → evaluate purchase, including electricity, space, cooling, and man-hours in TCO

**Step 4 — Check if you need multi-GPU on a single model.**
- [ ] If **yes** (tensor parallelism, distributed training) → you need **NVLink** → you are in the SXM/datacenter world. Consumer cards are excluded.
- [ ] If **no** (independent jobs, one model per GPU) → you can stick to PCIe and consumer/pro cards, saving an order of magnitude.

**Step 5 — Check the licensing constraint.**
- [ ] Will the computation be sold or rented to third parties? → **GeForce excluded**. RTX PRO or datacenter is needed.
- [ ] Internal use or research? → GeForce allowed.

**Step 6 — Check bandwidth, not just capacity.**
- [ ] Is the workload generative inference? → **memory bandwidth** is your performance predictor. Estimate: tokens/s ≈ bandwidth ÷ model size.
- [ ] Is the workload long prefill or training? → then **TFLOPS** (at the right precision!) really matter.

**Step 7 — Check system compatibility.**
- [ ] Power supply: total TDP + 30% margin
- [ ] Sufficient CPU/chipset PCIe lanes for the number of cards (x16 or at least x8 per card)
- [ ] Physical space in the case and actual airflow
- [ ] Electrical line: above 2 kW continuous, in Italy a serious consideration of the meter is needed
- [ ] CPU architecture: if Grace/Vera, does your software exist for **aarch64**?

**Step 8 — Choose by price range.**
- [ ] **Entry (< €3,000):** RTX 5070 Ti / 5080, or used RTX 4090. For learning, computer vision, small LLMs. Alternatively: **no hardware and €500 of cloud.**
- [ ] **Mid (€3,000–12,000):** RTX 5090 (one or two), or DGX Spark if the constraint is capacity and not speed. Covers 90% of the real needs of a professional or an SME.
- [ ] **High-end (€15,000–30,000):** RTX PRO 6000 Blackwell (one or two, for 96/192 GB), or an H100/H200 PCIe if datacenter license and HBM bandwidth are needed. This is the range where an SME can do serious AI in-house.
- [ ] **Datacenter (€100,000 +):** HGX H200/B200 node. At this point, you have a dedicated team and a purchasing department, and this chapter only serves to ask the right questions to your supplier.

**Step 9 — Before signing.**
- [ ] Have you checked prices **today**, and not on this page?
- [ ] Have you checked actual delivery times (not the declared ones)?
- [ ] Have you tested your exact workload on that GPU **in the cloud, for an hour**, before buying it? This is the single piece of advice with the best value/cost ratio in the entire chapter: **€20 of cloud can save you €20,000 of error.**

---

*Final note on data volatility: architectures, prices, and availability in this sector change on a quarterly basis. The architectures, sizing principles, and selection criteria described in this chapter remain valid; specific numbers must be re-verified as of the decision date.*

---

[← Previous](10-cpu-workstation-hedt.html) · [All chapters](./) · [Next →](12-amd-instinct-vs-nvidia.html)
