---
title: "Chapter 4 - The Graphics Card (Consumer GPU)"
parent: "English Edition"
nav_order: 4
---

<details open markdown="block">
  <summary>Chapter contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

> **Note on data and reference period.** This chapter is updated as of mid-2026. This is an anomalous time for the graphics card market: since the last quarter of 2025, there has been a severe **memory shortage** (DRAM and GDDR), caused by data center demand for artificial intelligence, which has pushed list prices far above **MSRPs** (*Manufacturer's Suggested Retail Price*). For this reason, whenever I quote a price in this chapter, I refer to it as "list price/MSRP" and warn that the actual retail price, especially in Italy and Europe (where VAT and logistics are added), can be significantly higher and change week by week. Data on generations, models, and prices should therefore be verified at the time of purchase: they are the part of this chapter that ages most quickly. The technical principles, however, remain valid over time.

---

## 1. What the GPU does

### Description and function

The **GPU** (*Graphics Processing Unit*) is the computer component specialized in **massive parallel computing**. To understand what this means, it's helpful to contrast it with the **CPU** (*Central Processing Unit*). The CPU is designed to execute a few very complex instructions in rapid succession: it has a relatively low number of "cores" (processing units), each very powerful and flexible, suitable for tasks that require continuous and unpredictable logical decisions, such as running the operating system or program logic. The GPU follows the opposite philosophy: it contains thousands of small, simple cores, each individually less powerful, but capable of working all together at the same instant on the same type of operation. This is the ideal architecture when the same calculation needs to be applied to millions of distinct data points simultaneously.

The historical use case that gave birth to the GPU is **3D rendering**, which is the transformation of a mathematically described three-dimensional scene (vertices, triangles, textures, lights) into a two-dimensional pixel image to be displayed on a screen. A 1920×1080 pixel monitor contains just over two million pixels; at 3840×2160 (so-called 4K), it exceeds eight million. If each frame needs to be recalculated sixty, one hundred twenty, or more times per second, the number of operations per second becomes astronomical. No CPU could handle it: an architecture that calculates millions of pixels in parallel is needed, and that is exactly what the GPU does.

Over time, however, it was realized that this same parallel computing engine is also useful outside of gaming. Today, the GPU is used for **video encoding and decoding** (compressing and decompressing video streams, fundamental for streaming and editing), for **scientific computing**, and especially for **local artificial intelligence**: training and especially executing (performing *inference*) neural networks, from image generation models like Stable Diffusion to large language models (**LLM**) run on one's own PC. The reason is the same as for rendering: neural networks boil down to enormous matrix multiplications, which is the same calculation repeated millions of times on different data. It is this convergence between graphics and AI that has made the GPU the most strategic — and most contested — component in the entire hardware sector.

### The Physical Components of a Graphics Card

When we speak of "graphics card" in the strict sense, we mean the entire product: a printed circuit board with various components on it, which is inserted into the computer. The actual graphics chip is only one part. Let's look at them one by one.

The **GPU die** is the actual silicon chip: the graphics processor. This is where the computing cores (which NVIDIA calls *CUDA cores* and *Tensor cores*, AMD *Stream Processors* and *AI Accelerators*), the units dedicated to ray tracing, and the memory controllers reside. It is the heart of the product and determines its raw computing power.

**VRAM** (*Video RAM*) is the dedicated memory of the card, physically separate from the system RAM. It contains textures, 3D models, rendering buffers, and — in the case of AI — the weights of the neural model. It is such a central element that we dedicate the entire next section to it.

The **VRM** (*Voltage Regulator Module*) is the power delivery system of the card. The GPU receives a 12-volt supply from the power supply, but the chip operates at much lower voltages (approximately 1 volt) and very high amperages. The VRM converts and stabilizes this energy, distributing it cleanly to the die. A good quality VRM, with many power "phases," allows for higher clocks, lower temperatures, and longer lifespan; a cheap VRM is often the real limit of low-end cards and poorer versions.

The **PCB** (*Printed Circuit Board*) is the green (or black) board on which everything is mounted. Its quality, the number of copper layers, and the layout of the traces influence electrical stability and overclocking capability.

The **dissipation system** dissipates the heat produced by the die and the VRMs. It is typically composed of a *vapor chamber* or *heat pipes* that transport heat to a stack of aluminum fins, cooled by one or more fans. In more powerful cards, the dissipation is the most voluminous and heaviest part of the product, and this explains why two cards with the same chip can have very different dimensions, noise levels, and prices.

### Typical errors at this level

The most common beginner's mistake is to confuse the **GPU die** (the chip) with the **video card** (the complete product). This leads to a second misunderstanding: thinking that two cards with the same name (for example, two "RTX 5070s") are identical. They are not: they share the chip, but the third-party manufacturer that assembles them chooses the cooler, the VRM, the factory clocks, and the power supply. This is the difference between *reference* versions and *custom* versions, which we will discuss extensively in section 6.

---

## 2. VRAM and memory bus

### What VRAM is and why it's different from system RAM

**VRAM** is the very high-speed memory soldered directly onto the video card, around the GPU die. It serves to store all the data that the graphics chip needs to work on at any given moment: textures (the "skins" applied to 3D models), scene geometry, various intermediate rendering buffers, and, in AI applications, the neural network weights. The difference compared to system RAM (DDR4 or DDR5 modules inserted into the motherboard and used by the CPU) is twofold. Firstly, VRAM is enormously faster in terms of bandwidth: a mid-range video card moves hundreds of gigabytes per second, while a system RAM kit stops at a few tens. Secondly, VRAM is *dedicated* and local: it is a few millimeters from the chip that uses it, avoiding the bottleneck of the **PCIe** bus (*Peripheral Component Interconnect Express*, the channel that connects the video card to the rest of the computer). If the GPU had to read textures from system RAM by passing through PCIe every time, performance would plummet.

The generations of VRAM encountered today are mainly three. **GDDR6** (*Graphics Double Data Rate 6*) is the mature and economical standard, still used today by AMD across the entire RX 9000 series and by NVIDIA on the lower tiers of past generations. **GDDR6X** is a faster variant developed by NVIDIA with Micron, appearing on high-end RTX 30 and 40 series. **GDDR7** is the most recent generation, adopted by NVIDIA across the entire RTX 50 series: it offers greater bandwidth for the same bus width and is more efficient. As of this writing, AMD on the RX 9000 series (RDNA 4) has chosen to stick with GDDR6, relying on wider buses and internal cache to compensate — a choice that also has the advantage of lower costs, not insignificant during the memory crisis.

### The memory bus and the bandwidth formula

The **memory bus** is the width of the channel connecting the GPU die to the VRAM, measured in bits. Typical values are 128, 192, 256, and 384 bits (with extreme cases like the 512 bits of the RTX 5090). It should be imagined as the width of a highway: more lanes (more bits) allow more data to pass through simultaneously. By itself, however, the bus says nothing: the speed at which each lane travels, i.e., the effective frequency of the memory chips, also matters.

The overarching metric is **bandwidth** (memory bandwidth), which is how many gigabytes per second the GPU can exchange with its VRAM. The conceptual formula is:

> **Bandwidth (GB/s) = (effective memory speed in Gbps × bus width in bits) ÷ 8**

We divide by 8 because one byte consists of 8 bits. Let's take a concrete example: a card with GDDR6 memory at 20 Gbps on a 256-bit bus has a bandwidth of (20 × 256) ÷ 8 = **640 GB/s**. A card with the same type of memory but a 128-bit bus stops at (20 × 128) ÷ 8 = **320 GB/s**, which is half. This is why the bus is as important a factor as the amount of VRAM.

The following table shows how, for the same memory speed, the bus width determines the final bandwidth. The values are indicative and serve to establish orders of magnitude.

| Bus Width | 18 Gbps Memory (GDDR6) | 20 Gbps Memory (GDDR6) | 28 Gbps Memory (GDDR7) |
|:---:|:---:|:---:|:---:|
| 128 bit | 288 GB/s | 320 GB/s | 448 GB/s |
| 192 bit | 432 GB/s | 480 GB/s | 672 GB/s |
| 256 bit | 576 GB/s | 640 GB/s | 896 GB/s |
| 384 bit | 864 GB/s | 960 GB/s | 1344 GB/s |

It's immediately clear why GDDR7 is interesting: it allows for high bandwidths even with narrower buses, which enables manufacturers to save on the number of chips and PCB complexity. And it's also why you can't compare the bandwidth of two cards by only looking at the bus bits without considering the memory type.

### How much VRAM do you really need

The amount of VRAM required depends on three intertwined factors: the **resolution** at which you game or work, the **quality of textures** and effects, and the presence or absence of demanding features like **ray tracing** (realistic physical calculation of lights, shadows, and reflections). A general rule applies: the higher the resolution, the more high-definition textures must reside in memory simultaneously, thus more VRAM is needed. Added to this is the fact that ray tracing requires additional data structures in memory, and some *frame generation* technologies (AI-driven generation of intermediate frames) consume additional VRAM.

As a practical reference, valid in mid-2026 and destined to grow over time as games become more demanding:

| Usage Scenario | Minimum Recommended VRAM | Comfortable VRAM |
|:---|:---:|:---:|
| Gaming 1080p, medium-high details | 8 GB | 12 GB |
| Gaming 1440p, high details + ray tracing | 12 GB | 16 GB |
| Gaming 4K, maximum details + ray tracing | 16 GB | 20–24 GB |
| Stable Diffusion / image generation | 8–12 GB | 16 GB+ |
| Quantized local LLMs (7–8 billion parameters) | 8–12 GB | 16 GB |
| Medium-sized local LLMs (13–34 billion) | 24 GB | 24–48 GB |

In the realm of **local AI**, VRAM is even more critical than in gaming, and for a clear reason: a neural model must fit *entirely* into the graphics card's memory to run at full speed. If the model doesn't fit, it either won't start at all, or it will be "split" between VRAM and system RAM, leading to a performance collapse. For this reason, for those working with AI, the amount of VRAM often becomes the number one selection criterion, even more so than the raw power of the chip. A 7–8 billion parameter language model quantized to 4-bit occupies approximately 5–6 GB and fits comfortably on a 12 GB card; a 70 billion model, however, requires tens of gigabytes and remains the preserve of professional cards or multi-GPU configurations.

### The case of "crippled" GPUs on the bus

There is a recurring commercial trap that this chapter must make explicit: the card with **lots of VRAM but a narrow bus**. A manufacturer, to make a low-end model appealing, might equip it with 16 GB of memory — a number that looks great on the box — but connect it with only a 128-bit bus. The result is that the card has space to accommodate abundant textures, but the insufficient bandwidth does not allow it to *move them* fast enough. It's like having a huge warehouse with only one narrow door: the goods are there, but they can't enter and exit at the required speed.

This scenario is particularly disappointing in two cases: at high resolutions, where bandwidth is the limiting factor, and in games with continuous texture streaming. The inexperienced buyer purchases "16 GB" convinced they've made a future-proof deal, only to discover that a card with "only" 12 GB but a wider bus outperforms it in real-world performance. The lesson is clear: **VRAM should never be evaluated in isolation, but always in conjunction with the bus and memory type**, meaning by looking at the overall bandwidth. A good habit is to always check three numbers together — capacity (GB), bus width (bit), and memory type (GDDR6/6X/7) — before being swayed by capacity alone.

---

## 3. NVIDIA Nomenclature: How to Read the Name

### The Name Structure

NVIDIA sells its consumer cards under the **GeForce RTX** brand. The acronym **RTX** (*Ray Tracing eXtension*) indicates, from the 20 series onwards, the presence of dedicated hardware units for ray tracing and AI calculations; the old acronym **GTX** identified cards without such units. The actual name is a four-digit number that follows a very regular pattern, such as **RTX [series][tier]**.

Let's take the example of the **RTX 4070**. The first two digits, "40", indicate the **generation** (in this case, the 40 series, Ada Lovelace architecture). The next two digits, "70", indicate the **tier** within that generation. So "RTX 4070" reads as "tier 70 card of the fourth RTX generation". By the same criterion, an RTX 5080 is the tier 80 of the fifth generation, an RTX 3060 is the tier 60 of the third generation, and so on. Once this pattern is understood, the name of any NVIDIA card becomes immediately readable.

### The Series (Generations)

Each generation corresponds to a different **architecture**, meaning an internal chip design. The three most relevant for today's buyers are:

The **30 series (Ampere)**, released in 2020, is now old but still very popular in the used market. It introduced the second generation of RT cores and the first truly viable DLSS.

The **40 series (Ada Lovelace)**, released in late 2022, brought a great leap in efficiency and DLSS 3 technologies with frame generation. It is no longer in production, so it is mainly found used or as residual stock, but it remains interesting in terms of price/performance ratio precisely because it was built with less expensive memory than current ones.

The **50 series (Blackwell)**, the current consumer generation at the time of writing, announced at CES in January 2025 and launched in the following months. It introduces the fourth generation of RT cores, the fifth generation of Tensor cores, GDDR7 memory across the entire range, and DLSS 4 technologies with **Multi-Frame Generation** (generating multiple intermediate frames instead of just one).

> **Warning — evolving data.** In mid-2026, there is persistent talk of a mid-generation refresh called **RTX 50 SUPER** (with more VRAM: rumors suggest 18 GB on the 5070, 24 GB on the 5070 Ti and 5080, and a 12 GB 5060). At the time of writing, however, these models **have not yet been launched** and their debut has been postponed several times precisely due to memory shortages, with some sources moving it to 2027 or considering it uncertain. They should therefore be treated as unconfirmed rumors, to be verified at the time of purchase.

### The Tiers

The last two digits encode the positioning and, with it, what can be expected in terms of performance and price. The scheme, valid as a general rule across generations, is as follows:

The **50 tier** is the absolute entry-level, designed for light 1080p gaming and multimedia use. The **60 tier** is the large volume of the market: the "popular" card for high-quality 1080p and for 1440p with some compromises. The **70 tier** is the heart of the mid-range segment, dedicated to high-refresh 1440p and 4K with the help of upscaling. The **80 tier** is the high-end, designed for 4K gaming with high details. The **90 tier** is the enthusiast, the absolute top of the consumer range, with maximum VRAM and power, oriented towards demanding 4K, content creation, and AI — and with prices that cross into professional territory.

### The Suffixes: Ti and SUPER

In addition to the base number, NVIDIA uses two suffixes to create intermediate variants. The suffix **Ti** (historically *Titanium*) indicates an enhanced version of the tier it is attached to: an RTX 4070 Ti is positioned between the 4070 and the 4080, and is faster than the plain 4070. The suffix **SUPER** plays a similar role, typical of mid-generation updates: it indicates an improved revision of an existing model, often with more cores, more VRAM, or more bandwidth. In some cases, both coexist (for example, "RTX 4070 Ti SUPER"). The practical rule to keep in mind is that, within the same generation, the ascending order of performance tends to be: base model → SUPER → Ti → Ti SUPER, then moving up in numerical tier.

### Summary Table of Recent NVIDIA Generations

The table shows the main models, with VRAM, bus, and MSRP at launch. **Please note that, due to memory shortages, actual prices in mid-2026 are often much higher than the indicated MSRPs**, especially in the high-end tiers.

| Model | Gen. / Architecture | VRAM | Mem. Type | Bus | MSRP at Launch (USD) |
|:---|:---|:---:|:---:|:---:|:---:|
| RTX 3060 | 30 / Ampere | 12 GB | GDDR6 | 192 bit | ~329 |
| RTX 3070 | 30 / Ampere | 8 GB | GDDR6 | 256 bit | ~499 |
| RTX 3080 | 30 / Ampere | 10 GB | GDDR6X | 320 bit | ~699 |
| RTX 4060 | 40 / Ada | 8 GB | GDDR6 | 128 bit | ~299 |
| RTX 4070 | 40 / Ada | 12 GB | GDDR6X | 192 bit | ~549 |
| RTX 4070 Ti SUPER | 40 / Ada | 16 GB | GDDR6X | 256 bit | ~799 |
| RTX 4080 SUPER | 40 / Ada | 16 GB | GDDR6X | 256 bit | ~999 |
| RTX 4090 | 40 / Ada | 24 GB | GDDR6X | 384 bit | ~1599 |
| RTX 5060 | 50 / Blackwell | 8 GB | GDDR7 | 128 bit | ~299 |
| RTX 5060 Ti | 50 / Blackwell | 8 / 16 GB | GDDR7 | 128 bit | ~379–429 |
| RTX 5070 | 50 / Blackwell | 12 GB | GDDR7 | 192 bit | ~549 |
| RTX 5070 Ti | 50 / Blackwell | 16 GB | GDDR7 | 256 bit | ~749 |
| RTX 5080 | 50 / Blackwell | 16 GB | GDDR7 | 256 bit | ~999 |
| RTX 5090 | 50 / Blackwell | 32 GB | GDDR7 | 512 bit | ~1999 |

### Typical NVIDIA Naming Errors

The most common mistake is comparing two cards solely by tier, ignoring the generation: a newer generation RTX 4070 can outperform or match an older, higher-tier RTX 3080, thanks to more efficient architecture and new upscaling technologies. The second mistake concerns the RTX 5060 Ti, sold in two versions, 8 GB and 16 GB, under the same name: the 8 GB version is penalized in modern games and at higher resolutions, and for a few more euros, the 16 GB version is almost always the better choice. The third mistake is being misled by the 90-tier into thinking it's "double" the 80: 90-tier cards offer superior performance, but with a price/performance ratio that significantly worsens as you go up, because at that tier, you also pay for exclusivity and abundant VRAM, which is more useful for professionals than gamers.

---

## 4. AMD Naming Convention: How to Read the Name

### The Name Structure

AMD sells its consumer graphics cards under the **Radeon RX** brand. The naming scheme resembles NVIDIA's: **RX [series][tier]**. Let's take the **RX 7800 XT**. The first two digits, "78", should be read as "7000 series, 800 tier": the 7000 series is the generation, and 800 is its positioning within that generation. The "XT" suffix indicates, as we will see, the more powerful variant of that model. As with NVIDIA, the tier increases with the number: an RX 7600 is entry-level, an RX 7900 is high-end.

### The Series (Generations)

There are three relevant AMD generations today, all based on the **RDNA** (*Radeon DNA*) architecture:

The **RX 6000 series (RDNA 2)**, from 2020, is now dated but still available on the used market, offering a good price/performance ratio in pure rasterization, but with weaker ray tracing compared to contemporary NVIDIA counterparts.

The **RX 7000 series (RDNA 3)**, from late 2022, introduced a *chiplet* design (the chip divided into multiple separate dies) and FSR 3 technology with frame generation. It still includes the high-end models RX 7900 XT and XTX, as the subsequent generation abandoned the *enthusiast* segment.

The **RX 9000 series (RDNA 4)**, the current generation at the time of writing, launched in March 2025. The jump in numbering (from 7000 to 9000, skipping 8000) aligns the GPU branding with that of Ryzen processors. RDNA 4 significantly improved ray tracing (third-generation accelerators) and introduced **FSR 4**, the first version of AMD's upscaling based on machine learning. It's important to note that this generation **does not have an enthusiast-tier model**: AMD chose to focus on the *mid-range* segment, with the RX 9070 XT at the top of the lineup.

### The suffixes: XT, XTX, GRE

AMD uses suffixes to distinguish power variants with the same number. The model **without a suffix** is the base version. The **XT** suffix indicates a more powerful version, with more compute units or higher clocks: the RX 9070 XT is faster than the plain RX 9070. The **XTX** suffix, which appeared on the 7000 series, indicates the maximum variant, a step above the XT (as in the RX 7900 XTX compared to the 7900 XT). The **GRE** suffix (*Golden Rabbit Edition*, a name initially created for the Chinese market for the Year of the Rabbit) identifies a slightly depowered version with a narrower bus, positioned between two models of the same series to cover an intermediate price range; the RX 9070 GRE, for example, has 12 GB on a 192-bit bus compared to the 16 GB on a 256-bit bus of the plain 9070, and in 2026 it was made globally available after an initial China-exclusive debut.

### Approximate AMD ↔ NVIDIA equivalence table

This table matches models by **price range and indicative performance**, not by exact technical equivalence. Relative performance varies from game to game and, more importantly, changes depending on whether pure rasterization (where AMD is competitive) or ray tracing (where NVIDIA maintains an advantage) is considered. It should therefore be read as a compass, not as a law.

| Tier / Indicative Price | AMD (RDNA 4) | NVIDIA (Blackwell) |
|:---|:---|:---|
| Entry (~$300) | RX 9060 XT 8 GB | RTX 5060 |
| Entry-mid (~$350) | RX 9060 XT 16 GB | RTX 5060 Ti 16 GB |
| Mid (~$550) | RX 9070 | RTX 5070 |
| Mid-high (~$600) | RX 9070 XT | (between 5070 and 5070 Ti) |
| High (~$750) | — (no model) | RTX 5070 Ti |
| High / enthusiast ($900+) | — (no model) | RTX 5080 |
| Absolute enthusiast ($2000+) | — (no model) | RTX 5090 |

The most eloquent data in the table is the row of dashes: in the current generation, AMD **does not compete at all** in the high-end and enthusiast segments, having left them uncovered. Anyone looking for a card beyond the power of the RX 9070 XT today effectively turns only to NVIDIA.

### Typical errors in AMD nomenclature

A recurring error is confusing XT and XTX, or assuming that the GRE suffix indicates something better when it actually indicates a reduced variant. A second error, mirroring the one seen for NVIDIA, concerns the RX 9060 XT sold in 8 GB and 16 GB versions: the 8 GB version is less advisable for modern games, and the price difference with the 16 GB is often small. A third error is assuming that AMD is always cheaper: this is true in many segments, but market conditions in 2026 have made prices volatile even for AMD, although Radeon models have held their MSRP better than their NVIDIA counterparts, thanks to memory supplies secured in advance.

---

## 5. NVIDIA vs AMD: Full Comparison

This is the ultimate decision-making section. I compare the two manufacturers on the planes that truly matter, so that the reader can understand *when* one is convenient and *when* the other, without bias.

### Pure Rasterization: Price/Performance

**Rasterization** is the traditional technique for generating images, without the realistic physical calculation of light. It still determines most of the performance in games. In this area, **AMD is historically very competitive**, often offering more frames per second per euro spent than NVIDIA in the same range. In the current generation, the RX 9070 XT offers rasterization performance close to that of the more expensive RTX 5070 Ti, at a lower list price: it is the classic example of AMD's advantage in price/performance ratio in pure gaming.

### Ray Tracing: NVIDIA Advantage, RDNA 4 Recovery

**Ray tracing** simulates the physical behavior of light to achieve realistic reflections, shadows, and lighting. It is much heavier to compute than rasterization and requires dedicated hardware units (NVIDIA's *RT cores* and AMD's *Ray Accelerators*). Historically, **NVIDIA has had a marked advantage** in this field: its cards lose less performance when ray tracing is activated. With RDNA 4, however, **AMD has recovered a lot of ground**, significantly closing the gap thanks to third-generation accelerators. The NVIDIA advantage remains, especially in games with *path tracing* (the most extreme and realistic form of ray tracing), but the gap is no longer as abysmal as in previous generations.

### Upscaling: DLSS vs FSR vs XeSS

**Upscaling** is a technology that allows a game to be rendered at a lower resolution (e.g., 1080p) and then enlarged to a higher resolution (e.g., 4K) using intelligent algorithms, gaining frames with minimal quality loss. It has become a central competitive factor, as much as raw power. The three main technologies are:

NVIDIA's **DLSS** (*Deep Learning Super Sampling*) is considered the benchmark for quality and diffusion. It is based on neural networks executed on Tensor cores and is exclusive to NVIDIA cards. DLSS 4, exclusive to the 50 series, introduces **Multi-Frame Generation**, capable of generating more intermediate frames via AI to multiply the frame rate; however, it should be remembered that frame generation adds latency and does not replace "true" performance.

AMD's **FSR** (*FidelityFX Super Resolution*) is the answer to DLSS. Up to version 3, it was *vendor-agnostic*, meaning it also worked on NVIDIA and Intel cards, but with lower quality. With **FSR 4**, AMD has moved to a machine learning-based approach that has greatly improved quality, bringing it closer to DLSS, but at the cost of limiting it to RDNA 4 cards equipped with the necessary AI accelerators.

Intel's **XeSS** (*Xe Super Sampling*) is the third option, created for Intel Arc cards but also usable in a reduced form on other brands. It offers good quality and is the "third wheel" in the sector.

### Encoder: NVENC vs AMF

The **encoder** is the hardware unit that compresses video in real-time, essential for **streaming** (Twitch, YouTube) and for exporting in **video editing**. NVIDIA has **NVENC** (*NVIDIA Encoder*), universally considered the best for quality and stability, so much so that it is a recurring reason why streamers and videomakers choose NVIDIA. AMD uses **AMF** (*Advanced Media Framework*): historically inferior, it has improved in recent generations, but NVENC maintains a recognized advantage, especially in high-efficiency encoding (HEVC and AV1) at low bitrates.

### CUDA vs ROCm: why NVIDIA dominates for AI and productivity

This is perhaps the most important point for a reader who, in addition to gaming, is looking at **artificial intelligence and productivity**, and it is the basis on which the entire section dedicated to workstations will rest. **CUDA** (*Compute Unified Device Architecture*) is NVIDIA's proprietary software platform for general-purpose computing on GPUs. It is not just an API: it is an entire ecosystem, mature for over fifteen years, on which almost all AI software is built — from PyTorch to TensorFlow, from image generation frameworks to tools for local LLMs. The AMD counterpart is called **ROCm** (*Radeon Open Compute*): it is open and continuously improving, but it remains less mature, less widespread, and with more fragmented software support. The practical result is clear: **for AI, accelerated creative productivity, and scientific computing, NVIDIA dominates** not so much because of the hardware but because of the software. Those who buy a card thinking of using it to train or run models, edit videos with AI effects, or work with professional applications, will in most cases find far fewer frictions with NVIDIA. This is a consideration that, for certain usage profiles, completely overturns AMD's price/performance advantage in pure gaming.

### Drivers, power consumption, prices

On the **driver** front, both brands today offer stable software, but NVIDIA has historically enjoyed a reputation for greater reliability at the launch of new games, while AMD has sometimes suffered from problems in the first few weeks after a card's release, which were then resolved with updates. In terms of **power consumption**, the two brands are equivalent in their respective segments, with model-by-model fluctuations. Regarding **prices**, as already mentioned, AMD tends to offer more for the same money in rasterization, while NVIDIA charges for the ecosystem (DLSS, NVENC, CUDA); in the context of 2026, moreover, real prices are governed more by memory availability than by official price lists.

### The third player: Intel Arc

**Intel Arc**, Intel's line of graphics cards, should be mentioned as a third player. The **Battlemage** series (cards like the Arc B580 12 GB) has gained an excellent reputation in the entry-level segment, offering more VRAM than the competition at aggressive prices and quality AV1 encoders. Arc's historical limitations are its drivers, which are less mature, especially in older games, and an AI software ecosystem that lags behind CUDA. Furthermore, in mid-2026, the future of Intel's gaming line appears uncertain: some rumors suggest a downsizing of high-end models, diverted towards the professional market. For the budget-conscious buyer, however, an entry-level Arc card remains one of the best VRAM/price options today, provided they accept some compromises on drivers.

---

## 6. AIB partners and custom versions

### Founders Edition / reference vs custom

When NVIDIA and AMD design a card, they also produce an "official" version: NVIDIA calls it the **Founders Edition** (FE), AMD calls it the **reference** version. These are the cards with the chip manufacturer's reference design. Alongside these, there is a large number of **AIB partners** (*Add-In Board partners*, third-party manufacturers who buy the chips and assemble their own cards): ASUS, MSI, Gigabyte, Sapphire, PowerColor, and many others. Each produces its own **custom** versions, with different coolers, PCBs, VRMs, clocks, and aesthetics. The same RTX 5070, therefore, exists in dozens of physically different variants, all with the same chip but with build quality, temperatures, noise, and price that can vary considerably.

### NVIDIA partners and their ranges

Each AIB partner organizes its cards into product lines ordered by increasing quality. Knowing these commercial names is essential to avoid paying too much or too little. The main NVIDIA partner brands, with their respective ranges from low to high, are:

**ASUS** offers the **Dual** line (entry, simple dual-fan cooler), the **TUF Gaming** (mid-range robust, excellent quality/price balance), and the **ROG Strix** (high-end, top-notch cooling and VRMs, high prices). **MSI** offers the **Ventus** (entry), the **Gaming / Gaming Trio** (mid-range, with a triple-fan cooler), and the **Suprim** (top-of-the-range, premium build). **Gigabyte** has the **Windforce / Gaming** line (entry and mid-range) and the **Aorus** (high-end). The picture is completed by brands like **Zotac** (often aggressive on price, with very compact models), **Palit** and **Gainward** (good quality/price ratio, generally cheaper), **PNY** (widespread and reliable, no frills), and **INNO3D**.

### AMD partners and "reference brands"

The AMD landscape has some peculiarities. **Sapphire** is considered the quintessential reference AMD partner — the equivalent of what the best premium partners are for NVIDIA — with the **Pulse** line (mid-range, excellent balance) and the **Nitro+** (top-of-the-line, highly regarded). **PowerColor** is the other big historical name associated with AMD, with the **Fighter** (entry), **Hellhound** (mid-range, quiet and well-balanced), and **Red Devil** (high-end) lines. Completing the picture are **XFX**, a historical exclusive AMD partner, and **ASRock**, which more recently entered the graphics card market with good results. Sapphire, PowerColor, and XFX are often referred to as AMD's "reference brands" because they work almost exclusively with Radeon, unlike ASUS, MSI, and Gigabyte, which produce for both brands.

### How much is it really worth paying more?

The practical question is: is it worth spending more for a premium version? The difference between a base card and a top-of-the-line card focuses on four aspects. The **cooler**, larger and with more heat pipes, keeps temperatures lower. The **factory clocks**, slightly higher on premium versions ("OC" acronyms indicate a factory *overclock*), typically lead to modest performance gains, in the order of a few percentage points. The **noise**, often lower on premium versions at the same temperature, thanks to better fans and more refined curves. The **aesthetics**, with RGB lighting and more refined finishes. The reasonable practical rule is this: **mid-range versions (TUF, Pulse, Hellhound, Gaming) offer the best compromise** and are the ones to recommend in the vast majority of cases; top-of-the-line versions (ROG Strix, Suprim, Nitro+, Red Devil, Aorus) only make sense for those seeking absolute silence, extreme overclocking, or aesthetics, and are willing to pay a premium that rarely translates into significantly superior performance. Beware, however, of the cheaper and more spartan versions of each brand on high-end cards: an undersized cooler on a powerful chip leads to high temperatures, throttling (automatic performance reduction to protect against heat), and noise.

### Typical errors with custom versions

The most expensive mistake is paying the premium for a top version thinking you'll get a performance leap that isn't actually there: between the base version and the premium version of the *same* card, the frame rate difference is almost always negligible. The opposite mistake is buying the cheapest version of a powerful card to save money, ending up with inadequate dissipation and noisy fans. A third mistake is being guided only by RGB and aesthetics, ignoring the parameters that really matter: temperatures, noise, and VRM quality.

---

## 7. Physical size and compatibility

### Length, thickness, and case clearance

A video card is a bulky item, and one of the most frustrating beginner mistakes is buying one without checking if it fits into your **case**. Two main dimensions need to be checked. The **length**, expressed in millimeters, must be less than the maximum *clearance* (available space) declared by the case manufacturer: high-end cards can exceed 320–350 mm, and not all cases accommodate them. The **thickness**, measured in "slots," indicates how many expansion slots the card occupies on the back of the case: cards are divided into **2-slot**, **2.5-slot**, **3-slot**, and even **4-slot** models. The larger the cooler — meaning the more powerful or premium the card — the more slots it occupies, and the greater the risk that it will cover other motherboard connectors or not fit into a compact case. The operating rule is to note the desired card's length and thickness, compare them with the case's specifications, and leave a few millimeters of margin for cables.

### Power Connectors: 8-pin PCIe vs 12VHPWR / 12V-2x6

The video card receives power from the **power supply unit** (PSU) through dedicated connectors. Historically, **8-pin PCIe** connectors have been used, of which a card may require one, two, or three depending on its power consumption. With the RTX 40 series, NVIDIA introduced a new high-density single connector called **12VHPWR** (*12-pin High Power*), later revised into an improved version called **12V-2x6**, capable of delivering up to 600 watts through a single cable. However, this connector has a well-known **problematic history**: on very high-end cards (especially RTX 4090 and 5090), there have been cases of connector overheating and melting, often linked to imperfect insertion or uneven current distribution among the pins. Practical countermeasures include firmly inserting the connector all the way, avoiding tight bends of the cable right behind the plug, and preferring native PSU cables over the included multi-8-pin **adapters**, which are bulkier and considered less reliable. Anyone purchasing a high-end card with this connector should ideally equip themselves with a modern **ATX 3.0 / ATX 3.1** power supply with a native 12V-2x6 cable.

### PSU Requirements by GPU Tier

Each card has a typical power consumption, often indicated as **TDP** (*Thermal Design Power*, the thermal design power, used as an approximation of electrical consumption under load) or as TBP (*Total Board Power*, the consumption of the entire card). The power supply must have sufficient power not only for the card but for the entire system, with a safety margin. The following table provides conservative recommendations for the recommended PSU wattage based on the GPU tier, considering a complete system with a comparable CPU.

| GPU Tier (examples) | Typical GPU Consumption | Recommended PSU (complete system) |
|:---|:---:|:---:|
| Entry (RTX 5060 / RX 9060 XT) | 130–180 W | 550–650 W |
| Mid (RTX 5070 / RX 9070) | 200–250 W | 650–750 W |
| Mid-high (RTX 5070 Ti / RX 9070 XT) | 260–320 W | 750–850 W |
| High (RTX 5080) | ~360 W | 850 W |
| Enthusiast (RTX 5090) | ~575 W | 1000–1200 W |

The practical advice is not to skimp on the power supply and not to get one that is just barely sufficient: a 30-40% margin above the estimated peak consumption improves efficiency, reduces PSU fan noise, and extends the component's life. It is also preferable to choose units from reliable brands with **80 Plus** certification (Gold or higher), because a poor power supply is one of the most insidious causes of instability and, in the worst cases, hardware damage.

### GPU Sag and Supports

Modern graphics cards are heavy, and over time they tend to bend downwards under their own weight: this phenomenon is called **GPU sag**. Besides being unsightly, significant sag puts strain on the PCIe slot and the connector. The solution is an **anti-sag support** (a small prop, often included with premium cards or available separately) that supports the free end of the card. It is an inexpensive accessory and recommended for all mid-range and high-end cards, especially in configurations with vertical cases or with the card mounted horizontally.

---

## 8. Which Slot to Install It In

### The First PCIe x16 Slot Connected to the CPU

The motherboard has multiple **PCIe** expansion slots, but they are not all the same, and the graphics card must be installed in the correct one. The correct slot is the **first PCIe x16 slot directly connected to the CPU**. The "x16" designation indicates the number of physical and electrical *lanes* (data paths) available: sixteen lanes are the maximum for a consumer graphics card and ensure full bandwidth. The fact that it is connected *directly to the CPU*, and not to the chipset, is crucial: it means that data travels between the graphics card and the processor via the shortest and fastest route, without intermediate steps that add latency. This slot is almost always the first from the top, closest to the CPU socket, and is typically metal-reinforced precisely to support the weight of the graphics card.

### What Happens in Secondary Slots

**Secondary slots**, lower down on the motherboard, have inferior characteristics. Even when they are physically as long as an x16 slot, electrically they often offer only 4 lanes (**x4**) and — a decisive detail — are connected to the **chipset** rather than directly to the CPU. This means two penalties combined: less bandwidth (a quarter of the lanes) and a longer, shared path to the processor, passing through the connection between the chipset and the CPU, where traffic from other devices also travels. Installing a powerful graphics card in a secondary slot can therefore limit its performance, negligibly for gaming at normal resolutions but noticeably in AI and intense data transfer scenarios. Secondary slots are designed for other expansion cards (network cards, video capture cards, additional controllers), not for the main graphics card. The mistake to avoid, typical of those assembling for the first time or not checking the motherboard's instructions, is to insert the graphics card into the first physical x16 slot they find without verifying that it is the one connected to the CPU: the correct slot is always clearly indicated in the motherboard manual.

---

## 9. How to Choose

### Start with the Monitor: Resolution and Refresh

The correct starting point for choosing a video card **is not the budget or the brand, but the monitor**. The display's resolution and refresh rate (measured in hertz) define how much power is truly needed. Buying an enthusiast card for a 1080p 60 Hz monitor is a waste: the card would produce many more frames than the monitor can display. Conversely, installing an entry-level card on a high-refresh 4K monitor leads to disappointing frame rates. The logic is: first, establish the goal (for example, "1440p at 144 Hz with high details"), then choose the card that achieves it, with a small margin for future games. As a general guide: for 1080p at high refresh, a 60-series card is sufficient; for 1440p at high refresh, a 70-series card is ideal; for 4K with high details, at least a 70 Ti / 80-series card or equivalent top-tier AMD cards are needed.

### The Budget: The GPU as the Component to Allocate the Largest Share To

In a PC primarily designed for **gaming**, the video card is the component that most influences perceived performance, and it is therefore correct to allocate the largest share of the budget to it — typically between 35% and 45% of the total configuration cost. This does not mean buying the most expensive card possible by sacrificing everything else: it means balancing. A sound rule is that the video card and the processor must be in a coherent range with each other, because excessive imbalance creates a **bottleneck**, a situation where a component that is too weak slows down the other, preventing it from performing optimally.

### Common Buyer Mistakes

Let's conclude with the most frequent pitfalls, which negatively summarize everything we've seen. The first is a **top GPU with an entry-level CPU**: pairing a very powerful card with an inexpensive processor that cannot "feed" it data fast enough, wasting a good part of the card's potential, especially at lower resolutions. The second is **insufficient VRAM for the intended use**: buying a card with too little memory for the resolution or AI workload you intend to tackle, resulting in performance drops and stuttering as soon as games or models exceed the available capacity. The third is a **case that is too small**: falling in love with a card without checking its length and thickness against the internal space of the cabinet, and discovering at the last minute that it doesn't fit. The fourth is an **undersized PSU**: pairing a barely sufficient or low-quality power supply with a demanding card, causing sudden shutdowns under load or hard-to-diagnose instability. The fifth, more subtle, is **chasing VRAM quantity alone** while ignoring the bus and memory type, as already discussed in section 2. Each of these errors stems from the same fundamental flaw: evaluating a component in isolation, forgetting that a video card lives within a system and must be chosen in harmony with everything else — monitor, CPU, case, and power supply.

---

## Operational Summary — Video Card Decision Checklist

This checklist condenses the entire chapter into a sequence of decisions to be made in the correct order, from the first to the last step.

1. **Define your goal based on the monitor.** Establish the resolution and refresh rate of your display (or the display you intend to purchase): 1080p, 1440p, or 4K, and at how many hertz. This, not the budget, is the true starting point.

2. **Identify your usage profile.** Gaming only? Also streaming and video editing? Artificial intelligence, image generation, or local LLMs? If you fall into the latter two categories, the software ecosystem (NVIDIA's CUDA) and the amount of VRAM weigh more than pure price/performance.

3. **Choose the tier consistent with your goal.** 60/9060 series for 1080p, 70/9070 series for 1440p, 70 Ti-80 series for 4K. Do not overspec for your monitor or underspec for your target.

4. **Check VRAM along with bus and memory type.** Always check three numbers together: capacity in GB, bus width in bits, memory type (GDDR6/6X/7). Be wary of cards with a lot of VRAM but a narrow bus. For AI, aim for as much VRAM as possible within your price range.

5. **Decide between NVIDIA and AMD based on your profile.** Gaming only in rasterization and tight budget → AMD is often better value. Heavy ray tracing, quality streaming, AI, and productivity → NVIDIA is the more solid choice. Absolute minimum budget → also consider Intel Arc.

6. **Choose the right custom version.** Prefer mid-range lines (TUF, Pulse, Hellhound, Gaming, Windforce). Only upgrade to top-tier (ROG Strix, Nitro+, Suprim, Red Devil) if you need absolute silence, overclocking, or aesthetics, knowing that the performance gain is minimal.

7. **Check physical compatibility.** Measure the length and thickness (number of slots) of the card and compare them with your case's clearance, leaving room for cables.

8. **Size the power supply with margin.** Choose a reliable brand PSU, 80 Plus Gold certified or higher, with a 30–40% margin over the estimated peak system power consumption. For cards with a 12V-2x6 connector, prefer an ATX 3.0/3.1 power supply with a native cable.

9. **Verify balance with the CPU.** Ensure that the processor is in a consistent tier with the graphics card to avoid bottlenecks. Better two balanced components than one excellent and one mediocre.

10. **Install in the first PCIe x16 slot connected to the CPU.** This is indicated in the motherboard manual, usually the first from the top and reinforced. Add an anti-sag bracket if the card is heavy.

11. **Check prices at the time of purchase.** Given the ongoing memory shortage in 2026, ignore theoretical MSRPs and check actual retail prices, comparing multiple retailers. If your budget is limited, also consider a well-maintained previous-generation card, which during a memory crisis can offer a better price/performance ratio than new.

---

*Final note on data currency: models, VRAM, prices, and availability in this chapter reflect the situation in mid-2026, a period marked by a memory shortage that makes prices particularly volatile. The conceptual sections (GPU operation, bandwidth formula, selection criteria, compatibility) remain valid over time; model and price tables should be re-verified at the time of purchase.*

---

[← Previous](03-ram-memory.html) · [All chapters](./) · [Next →](05-storage.html)
