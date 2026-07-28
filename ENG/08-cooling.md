---
title: "Chapter 8 - Cooling"
parent: "English Edition"
nav_order: 8
---

<details open markdown="block">
  <summary>Chapter contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## 8.1 Why cooling is needed: physics before marketing

Every active electronic component in a computer is, from a thermodynamic point of view, an electrical energy converter into thermal energy. This statement sounds brutal but is literally true: a processor does not "consume" current to produce computation and then dissipate some heat as a side effect. A processor absorbs electrical power and returns it entirely to the environment in the form of heat. Computation — information, the result of the operation — does not take away energy. If a CPU (Central Processing Unit) draws 200 watts from the motherboard's 12-volt line, those 200 watts become 200 thermal watts to dissipate. Not 180, not 150: two hundred. The cooling system is therefore not an accessory that "helps" the processor: it is the only way out for the energy that enters it, and its effectiveness directly determines the operating frequency, stability, and lifespan of the component.

The reason this heat is a problem lies in the physics of silicon. A MOSFET (Metal-Oxide-Semiconductor Field-Effect Transistor, the elementary building block of every modern digital circuit) switches between an on and an off state; with each switch, it dissipates energy to charge and discharge the parasitic capacitances of the circuit, and it also continuously loses energy due to leakage currents that flow through the device even when it should be off. Leakage current increases in a strongly non-linear way with temperature. This is where the thermal vicious cycle begins: a hot chip dissipates more power for the same work, and by dissipating more power, it gets even hotter. Simultaneously, the mobility of charge carriers in silicon decreases as temperature increases, meaning transistors switch more slowly, and to maintain the same frequency, more voltage would be needed — which in turn produces more heat. Hence the observable fact, verifiable on any test bench, that a well-cooled CPU consumes *fewer* watts than the same poorly cooled unit, even while performing the exact same work.

The second reason heat must be controlled is long-term degradation. Phenomena such as electromigration (the physical migration of metal atoms in interconnect traces, driven by electron flow) and NBTI (Negative Bias Temperature Instability, a progressive shift in the threshold voltage of transistors) have an exponential dependence on temperature, typically described by an Arrhenius equation. In practical terms: a chip operating at 90 °C ages significantly faster than one operating at 70 °C. This does not mean that a CPU at 90 °C will break tomorrow — manufacturers dimension limits precisely to guarantee the expected useful life even at the limit — but it does mean that the thermal margin is a margin of longevity and quietness, not just performance.

### 8.1.1 Throttling: The Emergency Brake

Since the chip cannot physically prevent itself from heating up, manufacturers have integrated distributed temperature sensors (DTS, Digital Thermal Sensor) and protection logic into the silicon. When the die temperature reaches a threshold called **T<sub>jmax</sub>** (maximum junction temperature, i.e., the maximum allowed temperature at the transistor junction), the processor automatically reduces frequency and voltage to stay within limits. This phenomenon is called **thermal throttling**. If, despite throttling, the temperature continues to rise to a second critical threshold, **THERMTRIP** intervenes, a hardware signal that immediately shuts down the machine to prevent permanent damage.

Throttling is not a malfunction: it is the system working as intended. But it is also an unequivocal sign that the cooling system is undersized for the load. Users perceive this as sudden frame rate drops in games, rendering times that worsen as work progresses (the first few minutes are fast, then it slows down), and benchmarks that return lower scores on the second run compared to the first.

It's important to understand that modern processors no longer have a "nameplate" frequency guaranteed under all conditions. Since around 2015, both Intel and AMD have adopted opportunistic boost algorithms—Intel Turbo Boost / Thermal Velocity Boost, AMD Precision Boost 2 and PBO (Precision Boost Overdrive)—that increase frequency until *any one* of the limits is reached: current limit, power limit, temperature limit. The first limit hit is the one that governs. Consequently, the cooler doesn't just "prevent throttling": the cooler *determines the operating frequency*. A Ryzen 9 or Core i9 with a mediocre cooler won't break; it will simply run slower and be noisier. The cooler, in this architecture, is effectively a performance component.

### 8.1.2 TDP, PL1/PL2, PPT: decoding power numbers

Here we need to be surgical, because marketing has made these acronyms almost useless if taken literally.

**TDP** stands for *Thermal Design Power*. Historically, it was the thermal power, expressed in watts, that the cooling system had to be able to dissipate for the processor to operate at its nominal specifications. The problem is that neither of the two major manufacturers today uses TDP as a synonym for "maximum consumption."

For **Intel**, modern nomenclature distinguishes:
- **PBP** (Processor Base Power), which replaced the old TDP designation: this is the power dissipated when the processor operates at its *base frequency* with all cores active. It is a conservative value and, in real-world use, almost never representative.
- **MTP** (Maximum Turbo Power): the maximum power under boost conditions. This is the figure that matters for sizing the cooler.
- **PL1** and **PL2** (Power Limit 1 and Power Limit 2): PL1 is the long-term sustained power limit, PL2 is the peak limit allowed for a time interval called **Tau**. In high-end desktop configurations, many motherboard manufacturers set PL1 = PL2 and infinite Tau by default, meaning the processor *always* operates at its peak limit. This is precisely why a processor declared "125 W" can draw 250 W in a rendering, without anything being broken.

For **AMD**, on AM4 and AM5 platforms, the key acronym is **PPT** (Package Power Tracking), the overall power limit absorbed by the package. The approximate relationship historically used is PPT ≈ TDP × 1.35: a processor declared 105 W has a PPT around 142 W, one declared 170 W reaches about 230 W. AMD complements PPT with two other limits, **TDC** (Thermal Design Current, sustained current deliverable by the VRM under stabilized thermal conditions) and **EDC** (Electrical Design Current, instantaneous peak current); the VRM (Voltage Regulator Module, the motherboard's voltage regulation module, discussed in the chapter on motherboards) is the circuit that physically delivers this current.

The practical consequence is only one, and it should be etched in stone: **you don't choose a cooler by looking at the CPU's TDP; you choose it by looking at the real power consumption under heavy load** (MTP for Intel, PPT for AMD, or even better, independent measurements in reviews). A user who buys a cooler "suitable up to 150 W" for a processor "with a 125 W TDP" that draws 250 W in Cinebench has made a sizing error of 60%.

### 8.1.3 The concept that truly matters: thermal resistance

If there's one quantity worth learning, it's this one. A cooling system can be described by a **thermal resistance**, measured in degrees Celsius per watt (°C/W). It links the dissipated power to the temperature difference between the chip and the ambient air:

> ΔT = P × R<sub>th</sub>
> or: T<sub>CPU</sub> = T<sub>ambient</sub> + (Power in watts × Thermal resistance in °C/W)

A good dual-fan tower cooler has an overall thermal resistance (from die to air) in the order of 0.10–0.15 °C/W at medium fan speed. Let's do the math: with 200 W dissipated and an R<sub>th</sub> of 0.13 °C/W, the thermal difference is 26 °C. If the air inside the case is 35 °C (not 22, because it's warmer inside the case than in the room), the CPU will settle around 61 °C. With the same cooler and a 280 W processor, the difference becomes 36 °C, reaching 71 °C. With an inexpensive cooler at 0.25 °C/W and 280 W, the difference would be 70 °C, leading to 105 °C, which means permanent throttling.

This model, simplified as it is, explains almost everything observed in reality: it explains why doubling the cooler's surface area doesn't halve temperatures (because the total thermal resistance is the sum of multiple resistances in series, and that of the thermal paste and IHS doesn't change), it explains why ambient temperature counts one-to-one (every extra degree in the room is an extra degree on the CPU), and it explains why comparing coolers should always be done with the same dissipated power and ambient temperature.

The chain of thermal resistances, from the hot spot to the air, is as follows: die → internal TIM (the material joining the die to the IHS, solder or paste) → **IHS** (Integrated Heat Spreader, the metal lid soldered over the die that we see externally) → external thermal paste → cooler base → heatpipes → fins → air. The designer can only improve the latter links; the former are decided by Intel and AMD. This is why beyond a certain point, adding mass and fans yields diminishing returns: you are optimizing one link in the chain while the bottleneck is elsewhere.

### 8.1.4 Target Temperatures: What's Normal and What's Not

One of the topics with the most misinformation. Let's establish some benchmarks, noting that these are typical values at the time of writing and that exact limits should always be checked in the technical specifications of your specific model, as they change from generation to generation.

| Component | Idle (desktop, no load) | Gaming Load | Prolonged Heavy Load (rendering, compilation) | Intervention Limit (T<sub>jmax</sub>) |
|---|---|---|---|---|
| Intel Core desktop CPU (12th–14th gen, Core Ultra) | 30–45 °C | 55–75 °C | 75–95 °C | ~100 °C |
| AMD Ryzen 5000 CPU (AM4) | 35–45 °C | 60–75 °C | 75–90 °C | ~90 °C |
| AMD Ryzen 7000/9000 CPU (AM5) | 35–50 °C | 60–80 °C | 85–95 °C | ~95 °C (7000-series X3D lower, ~89 °C; 9800X3D back to ~95 °C) |
| GPU (die/core, recent GeForce and Radeon) | 30–45 °C | 65–80 °C | 70–85 °C | ~90 °C core |
| GPU — GDDR6/GDDR6X memory (T<sub>mem</sub>) | — | 70–95 °C | up to 100–105 °C | ~105 °C (throttling) |
| GPU — hotspot / junction | — | 75–95 °C | 85–100 °C | ~105–110 °C |

*Note on volatile data: T<sub>jmax</sub> values and throttling thresholds change with generations and sometimes with microcode updates. The figures in the table are indicative and valid to the best of available knowledge; the authoritative data is that provided by the manufacturer for the specific SKU.*

There are two recurring misconceptions to dispel immediately.

**First misconception: "my CPU is at 90 °C, it's about to break."** No. AMD Ryzen 7000 and 9000 series processors, in particular, are designed to *use* all available thermal budget: the boost algorithm raises the frequency until it reaches 95 °C, and then stays there. A Ryzen 9 that consistently operates at 95 °C under full load with a good cooler is not a sick system; it's a system doing exactly what it was designed to do. The difference between a mediocre cooler and an excellent one, on these chips, is not reflected in the temperature (which is always 95 °C) but in the *sustained frequency* and the *benchmark score*. Anyone evaluating their cooler by only looking at the thermometer, on these platforms, is looking at the wrong instrument.

**Second misconception: "idle temperature is what matters."** Also no. Resting temperature is the least informative of all, because it depends abnormally on OS boost transients (a background service waking up is enough to make the reading spike by 15 °C for half a second) and because at very low power, the thermal resistance of the system is almost irrelevant. A cooler is evaluated under constant and prolonged load, with at least 10–15 minutes of stress testing, measuring *simultaneously* temperature, power consumption, and frequency. Without all three numbers, the comparison is meaningless.

A third element must be introduced: the **air intake temperature**. No air cooler or AIO can bring the CPU below the temperature of the air entering it; this is a thermodynamic limit, not a defect. If the air inside the case is at 40 °C because the airflow is poorly designed, the cooler starts at 40 °C and adds its ΔT. This is why the chapter on case and ventilation and this chapter are, in reality, a single chapter divided into two: **an excellent cooler in a suffocated case performs like a mediocre cooler in a well-ventilated case.**

---

## 8.2 Air Cooling

### 8.2.1 Description and function: how a tower really works

A modern air cooler is composed of four functional elements, and it's worth understanding them one by one because the product's quality lies entirely in the details of how they are made.

**The base (cold plate).** This is the plate, almost always made of copper (sometimes nickel-plated for aesthetics and oxidation resistance), that rests on the CPU's IHS. Its task is to collect heat from a small, concentrated area. Two construction philosophies compete in this field: the **solid base** (a machined copper block, into which the heatpipes are inserted and soldered) and **direct-touch** (the heatpipes are flattened and directly touch the IHS, without an intermediate plate). Direct-touch eliminates a thermal interface but leaves aluminum or copper grooves between the tubes, resulting in less uniform heat distribution; the solid base adds an interface but distributes heat better. In practice, both well-executed solutions yield equivalent results, while both poorly executed solutions (concave base, poor soldering, rough finish) yield terrible results. A detail often overlooked is the **flatness and convexity** of the base: many manufacturers make the base slightly convex to compensate for the deformation of the IHS under the pressure of the mounting system.

**The heatpipes.** These are the technological core and the reason why a 1 kg tower dissipates more heat than a 3 kg solid copper block. A heatpipe is a sealed copper tube, partially vacuumed, and containing a small amount of working fluid (typically water). Inside the wall, there is a capillary structure (**wick**): sintered copper powder, metallic mesh, or longitudinal grooves. The operation is a closed cycle of phase change. At the hot end (in contact with the CPU), the fluid evaporates, absorbing the **latent heat of vaporization** — an enormous amount of energy compared to that needed to heat the same fluid by a few degrees. The vapor, which occupies a much larger volume, expands and moves very quickly towards the cold end (immersed in the fins), where it condenses, releasing the latent heat. The condensed liquid is then returned by the capillary action of the wick. Since the partial vacuum lowers the boiling point of water to a few tens of degrees, the cycle works even at normal operating temperatures.

The result is that a heatpipe has an **effective thermal conductivity** orders of magnitude higher than that of solid copper (we are talking about thousands of equivalent W/m·K, compared to about 400 W/m·K for copper). This explains why: (a) serious coolers have 6, 7, or 8 heatpipes and not just one; (b) the number of heatpipes is a crude but real indicator of transport capacity; (c) there is a limit beyond which the heatpipe "dries out": if the power is too high, the liquid evaporates faster than the wick can return it, and the conductivity suddenly collapses. This is one of the reasons why air coolers have a fairly clear power ceiling.

A little-known corollary: **orientation matters**, though less than feared. A good quality sintered wick heatpipe works in any position, including against gravity, but in the "worst" position (evaporator at the top, condenser at the bottom, with gravity opposing liquid return) it can lose a few percentage points of efficiency. In standard cases with a vertical motherboard, the tower works horizontally, which is a neutral position; in cases with a horizontal motherboard (some HTPCs, some bench cases), it's advisable to check the manufacturer's instructions.

**The fin stack.** This is the heat exchange surface with the air: dozens of thin aluminum fins (rarely copper, which is more efficient but much heavier and more expensive), threaded onto the heatpipes. Here, the key parameter is **fin density**, often expressed in FPI (Fins Per Inch). Dense fins offer more surface area but create more resistance to airflow: they require high **static pressure** fans and perform poorly with slow fans. Sparse fins offer less surface area but breathe well even at low speeds: they are the choice for silence-oriented coolers. There is no single "best" setting: there is a coherent pairing between fin density and fan curve. An excellent cooler has a fin stack *designed together* with its fan.

The way the fins are attached to the heatpipes is an underestimated quality differentiator: in good products, they are **soldered** or otherwise joined with a metallurgical bond; in economic products, they are only mechanically pressed, with an air-to-metal interface that introduces thermal resistance.

**The fan.** Provides the airflow that carries heat away from the fins. The two fundamental parameters are **airflow** (CFM, Cubic Feet per Minute — or m³/h) and **static pressure** (mm H₂O, millimeters of water column). Airflow indicates how much air it moves in the absence of obstacles; static pressure indicates how well it can push air *through* an obstacle. A fin stack is an obstacle. We will discuss this in detail in section 8.7, as the topic is common to both air and liquid cooling.

### 8.2.2 Formats: How to read the sizes

Air coolers are divided into three main families, distinguished by flow geometry.

**Low profile.** The fin stack is horizontal, above the socket, and the fan blows downwards (**top-flow**). Typical heights range from 25 to 70 mm. They were designed for Mini-ITX cases, HTPCs, compact office systems, and slim workstations. A collateral advantage is that the downward flow also cools the motherboard VRMs and RAM modules, which a tower cooler does not. The disadvantage is that the available surface area is small, and the fan, being thin and small, must spin fast: these coolers are noisy in proportion to the power dissipated. Typical references: the **Noctua NH-L9** series (approx. 37 mm, suitable for CPUs up to approx. 65 W), the **Noctua NH-L12S** (approx. 70 mm, much more capable), the **Thermalright AXP90** and **AXP120** family, the **ID-Cooling IS** coolers for Mini-ITX.

**Single tower.** A single vertical fin stack, with one fan in front (push configuration) and sometimes a second behind (push-pull). Typical heights 120–160 mm. This is the dominant format in the mid-range: a good compromise between footprint, performance, and price. References: **Thermalright Assassin X120 Refined**, **Arctic Freezer 36**, **DeepCool AK400**, **be quiet! Pure Rock 2**, **Noctua NH-U12S / NH-U12A

In terms of brand positioning, some clear lines can be drawn. **Thermalright** has overturned the market in recent years by offering high-end performance at low-end prices; the trade-off is a lower perceived quality of materials and mounting kits, and less structured after-sales support in Europe. **Noctua** represents the opposite extreme: high prices, but excellent mounting systems (SecuFirm2), some of the best fans on the market (NF-A and NF-P), impeccable documentation, a long warranty, and — a detail worth its weight in gold — **free or nearly free adaptation kits for new sockets**, which means a Noctua cooler can survive multiple platform generations. **be quiet!** builds on silence, with acoustically refined solutions and a very understated matte black aesthetic. **DeepCool** and **Arctic** occupy the middle ground with solid products and competitive prices; Arctic, in particular, is aggressive on price and generous with its warranty.

*Commercial context note, potentially volatile: the availability of some brands may vary due to regulatory or regional distribution reasons. Before ordering, verify availability and support in your market.*

### 8.2.4 Pros and cons of air cooling, without rhetoric

**Pros.** Reliability is the decisive point: an air cooler has no wear-prone parts except the fan bearing, which can be replaced in two minutes and costs 15–20 euros. There's no liquid, no gaskets, nothing that can evaporate, gel, or leak. The practical lifespan is that of the bearing, and with good bearings (rifle bearing, FDB — Fluid Dynamic Bearing, Noctua's SSO2) we're talking about 100,000–150,000 hours MTTF (Mean Time To Failure). In concrete terms: a good air cooler can accompany three or four successive builds. The cost is unbeatable: 40 euros today buys performance that required 100 euros ten years ago. Maintenance is zero, excluding periodic dust removal. And **noise**: for the same performance, a well-designed tower is almost always quieter than an AIO, because it doesn't have a pump — which is a source of continuous noise, often at annoying frequencies (low-frequency hum, gurgling).

**Cons.** Bulk is the Achilles' heel. A dual-tower cooler 165 mm high and weighing 1.5 kg creates three categories of problems: (1) it might not fit in the case; (2) it might interfere with RAM modules, especially those with tall heat spreaders; (3) it might interfere with the first PCIe (Peripheral Component Interconnect Express, the expansion bus discussed in the motherboard chapter) slot or with M.2 SSD heatsinks. The weight, moreover, exerts torque on the motherboard: during transport, it's advisable to remove heavier coolers or transport the case horizontally. Finally, aesthetics: a black tower covers half the motherboard and hides the RAM; for those building a glass-sided system with lighting, it's a sacrifice. It must be honestly said that it's an *aesthetic* sacrifice, not a technical one.

### 8.2.5 Compatibility: the three measurements to always check

This is where most purchasing errors occur. Before buying an air cooler, three checks are mandatory.

**1. The socket.** The cooler kit must include the mounting system for your motherboard's socket. Relevant sockets today: **LGA1700** (Intel 12th/13th/14th generation), **LGA1851** (Intel Core Ultra 200 series desktop), **LGA1200** (10th/11th gen, legacy), **AM4** (Ryzen 1000–5000), **AM5** (Ryzen 7000/8000/9000). Two extremely valuable practical notes: AM5 has maintained the **same mounting hole pattern as AM4**, so almost all AM4 coolers are mechanically compatible with AM5. And LGA1851 has maintained the 78 × 78 mm hole pattern of LGA1700, so compatibility there is also broad — but **it is still mandatory to check the cooler manufacturer's website**, because the height of the ILM (Independent Loading Mechanism, the processor retention mechanism) and the thickness of the IHS can change, and some kits still require a different backplate.

**2. The maximum cooler height allowed by the case.** Every case specifies "CPU cooler clearance" or "max CPU cooler height," typically 155–175 mm in mid-towers. This should be compared with the cooler's stated height. **Do not leave 1–2 mm margins**: manufacturing tolerances, backplate thicknesses, and socket position on the motherboard introduce variations. If the case states 165 mm and the cooler measures 165 mm, it's a gamble. If the case states 170 and the cooler 165, you're safe.

**3. RAM clearance.** This is the real silent killer. In tower coolers, the front fan protrudes towards the memory slots. The manufacturer usually states two values: the maximum height of RAM modules with the fan in the standard position (often 32–35 mm), and the allowed height by raising the fan (at the cost of increasing the overall cooler height, falling back to point 2). "Bare" memory modules (without heatsinks, or with low-profile heatsinks) measure approximately 31–33 mm; many gaming modules with prominent heatsinks and RGB (Red-Green-Blue, addressable LED lighting) reach 42–50 mm. A Noctua NH-D15 with 48 mm tall RAM simply won't fit, or forces you to raise the fan beyond the case's limits, or remove it entirely, losing performance. **The practical rule is: if you've already chosen a large tower cooler, buy low-profile RAM.**

A fourth, less frequent but real check: some very wide tower coolers can partially cover the first PCIe x16 slot (where the graphics card goes) or the M.2 heatsinks above the first slot. This problem often occurs with Mini-ITX cases and compact mATX boards. All serious manufacturers publish compatibility lists and even downloadable 3D models; using them is not being pedantic, it's good practice.

### 8.2.6 Typical buyer mistakes

The first and most expensive: **sizing the cooler based on the stated TDP instead of actual power consumption**. We discussed this in 8.1.2; it's the mistake that leads to buying a 40-euro cooler for a Core i7 K that draws 250W under load.

The second: **ignoring RAM clearance**, buying "nice" memory first and then discovering the cooler doesn't fit.

The third: **believing that the number of heatpipes alone determines performance**. Six well-soldered heatpipes to a dense fin stack and well-ventilated beat eight poorly made heatpipes. The number is an indication, not proof.

The fourth: **using the stock cooler on a high-end CPU**. The coolers included in the box (AMD Wraith Stealth/Spire, Intel Laminar coolers) are designed for the bare minimum, i.e., to make the CPU function without failure. On a Ryzen 5 or a non-K Core i5, they are acceptable; on anything above that, they are a bottleneck. It should also be noted that, as of the writing date, **most high-end processors are sold without any cooler included**: it's not an option, it's a mandatory component to budget for.

The fifth: **not mounting the backplate correctly**, or tightening the screws unevenly. We will return to tightening in the section dedicated to thermal paste.

The sixth, specific to Intel: **ignoring the ILM bending issue on LGA1700**. The retention mechanism of this socket exerts pressure that tends to slightly bend the processor's IHS, creating a concavity in the center and reducing contact with the cooler base. This phenomenon typically costs 3–8 °C. There are aftermarket **contact frames** (Thermalright, Thermal Grizzly, and in some cases motherboard manufacturer kits) that replace the original ILM and distribute pressure more evenly. It's a 15–20 euro modification that recovers several degrees, but it requires removing the original mechanism — a reversible operation that must be done carefully. *Verify applicability to your socket and generation, as the problem is specific and not universal.*

### 8.2.7 Budget-tier recommendations (air)

**Entry (CPUs up to ~100W real: Ryzen 5 non-X, Core i5 non-K, APUs).** A single tower for 30–40 euros is more than sufficient. Arctic Freezer 36, Thermalright Assassin X120, DeepCool AK400. In many cases, the cooler included in the box is enough, but spending 30 euros buys a huge acoustic gain: it's probably the best value for money in the entire PC.

**Mid (CPUs up to ~180–220W real: Ryzen 7, non-overclocked Ryzen 9, Core i5 K, Core i7).** Economical dual tower: **Thermalright Peerless Assassin 120 SE or Phantom Spirit 120 SE**. They are, without exaggeration, the most solid advice one can give anyone in 2025–2026: performance within 2–4 °C of top-tier models at a third of the price. Higher build quality alternative: DeepCool AK620.

**High-end (CPUs over 250W: Ryzen 9 in PBO, Core i9 K, workstations).** Here air reaches its physical limit. Serious options are Noctua NH-D15 G2, be quiet! Dark Rock Elite, DeepCool Assassin IV. These should be chosen knowing that on a top-end Core i9 under full load, even the best air cooler will leave the CPU partially throttling: this is not a defect of the cooler, it's that the CPU dissipates more heat than air can carry away in that space. The conscious choice at this point is: accept a few percentage points less performance in exchange for reliability and silence (air), or switch to liquid cooling.

---

## 8.3 AIO Liquid Cooling

### 8.3.1 Description and Function

**AIO** stands for *All-In-One*: a pre-assembled, pre-filled, and factory-sealed liquid cooling system. The user simply mounts it; it is not opened, refilled, or maintained.

The circuit is conceptually simple. A **waterblock** (or cold plate) rests on the CPU's IHS: it's a copper block whose bottom surface is smooth (in contact with thermal paste) and whose top surface is carved into a dense grid of micro-fins, through which the liquid is forced to pass. The liquid absorbs heat, is pushed by a **pump** (in the vast majority of AIOs, the pump is integrated into the block itself, above the waterblock; in some models, it's integrated into the radiator) through a flexible **tube** to the **radiator**, which is a liquid-to-air heat exchanger: flat channels through which the liquid flows, crossed by a stack of aluminum fins. One or more **fans** blow air through the radiator, which dissipates heat into the environment. The cooled liquid returns to the waterblock through the second tube. It's a closed circuit.

The point that almost everyone misunderstands is **what the liquid actually does**. The liquid doesn't "cool" in the sense that it doesn't produce cold: it's simply a very effective *means of heat transport* because water has a very high specific heat capacity (approximately 4.18 kJ/kg·K) and because a pump can move it continuously. In an air cooler, heat must be transported from the base to the fins via heatpipes, and the fins must physically be there, above the socket, in a limited space. In an AIO, heat is *carried elsewhere*, to a point in the case where there's space for a much larger exchange surface and more fans. **The advantage of an AIO is not the liquid: it's that the liquid allows the exchange surface to be placed where there's room.**

There's a second, less obvious but valuable advantage: **thermal mass**. The liquid in the circuit (approximately 150 to 400 ml) acts as a thermal flywheel: it absorbs short power peaks without the temperature immediately rising. In burst loads (gaming, where the CPU alternates between peaks and pauses), this translates to lower peak temperatures and fans that spin up more slowly, resulting in more stable acoustic behavior. This is one of the reasons why an AIO "seems" quieter in gaming even when it isn't under constant load.

### 8.3.2 Radiator sizes: which for which CPU

The dissipation capacity of an AIO is determined almost entirely by the **radiator surface area** and the airflow through it. The pump and waterblock matter much less than marketing suggests: differences between good quality pumps are in the order of 1–2 °C, while between a 240 mm radiator and a 360 mm one there are 5–10 °C.

Sizes are expressed by approximate length: **120** (one 120 mm fan), **240** (two 120 mm fans), **280** (two 140 mm fans), **360** (three 120 mm fans), **420** (three 140 mm fans). 140, 320, and 480 also exist in niche markets. Note: a 280 has a *larger* surface area than a 240 (140 mm fans have approximately 36% more frontal area), and is often comparable to a 360 despite being shorter, which makes it a clever choice in cases that support it — but cases compatible with 280 mm radiators are less numerous.

A radiator **thickness** that matters: the standard is 27 mm, but some manufacturers (Arctic with the Liquid Freezer series, in particular) use 38 mm radiators, which offer more mass and surface area for the same length. The downside: greater thickness requires more static pressure from the fans and can cause clearance issues with the VRM fin stack on the motherboard or with RAM, if mounted on top of a narrow case. **Always check the maximum thickness supported by the case, summing radiator + fans.**

| Radiator | Relative Surface Area | Suitable CPUs (real power consumption under load) | Notes |
|---|---|---|---|
| 120 mm | 1× | up to ~100 W | Often worse than a €40 tower at the same price. Not recommended unless space is constrained |
| 240 mm | 2× | up to ~180 W | Mid-range. Suitable for Ryzen 7, Core i5 K/i7 |
| 280 mm | ~2.7× | up to ~230 W | Excellent compromise, if the case accepts it |
| 360 mm | 3× | up to ~280 W | The de facto standard for high-end |
| 420 mm | ~4× | over 300 W | For top-tier Core i9, Ryzen 9 in PBO, Threadripper. Large cases mandatory |

**Uncomfortable truth to say out loud:** an economical 120 or 240 mm AIO is almost always a worse choice than a €40 dual-tower cooler, in every respect — performance, noise, reliability, price. It only makes sense if space physically prevents a tower (slim cases, some ITX) or if aesthetics are the priority. Liquid cooling objectively becomes superior to air **from 280 mm upwards, and on CPUs that dissipate a lot of heat**.

### 8.3.3 Radiator placement and the rule of air in the pump

This is the paragraph that, if ignored, leads to noisy AIOs and prematurely dead AIOs. It must be understood, not memorized.

**The physical fact.** An AIO, however sealed, is not hermetic indefinitely. Rubber tubes are *permeable* to water vapor: molecule by molecule, year after year, a small amount of liquid evaporates through the tube wall. Furthermore, some internal chemical reactions (and the fact that the fill is never 100% during production) generate a small amount of gas. The result is that **in every AIO, sooner or later, an air bubble forms**. It is inevitable and anticipated by designers.

**The consequence.** Air, being less dense than liquid, always collects at the **highest point in the circuit**. If the highest point in the circuit is the pump, the pump draws air instead of liquid. This produces three effects, in order of increasing severity: (1) an obvious and annoying noise, a gurgle or hum ("gurgling"); (2) a loss of pumping efficiency, because air does not transport heat; (3) **dry cavitation** and accelerated wear of the bearing and impeller, because the pump runs without the liquid that lubricates and cools it. A pump that runs dry for weeks will die.

**The practical rule.** The highest point in the circuit **must never be the pump** (i.e., the block on the CPU). From this, the correct configurations follow:

- **Radiator on top of the case** (at the top), with tubes exiting downwards or sideways: ideal configuration. The highest point is the top of the radiator, which is designed to contain air (often the top channel of the radiator acts as a small air chamber). The liquid always reaches the pump full.
- **Radiator on the front of the case, with tubes at the bottom**: correct configuration. If the radiator is at the front and the tubes exit from the *bottom* of the radiator, air collects in the upper part of the radiator, away from the pump. This is the configuration to choose when mounting an AIO at the front.
- **Radiator on the front with tubes at the top**: **wrong and to be avoided** configuration, because this is where air from the radiator can migrate into the tubes and end up in the pump. It is the most common installation error and is what generates the famous complaint "my AIO makes a gurgling noise."
- **Radiator at the bottom of the case**: the absolute worst. The lowest point of the circuit becomes the radiator, and the highest point becomes the pump. All the air in the circuit ends up in the pump. Never do this, even if the case mechanically allows it.

If the case geometry forces a non-ideal mounting, there is a palliative: after assembly, **tilt the case into various positions for a couple of minutes with the system on**, to make the air bubble migrate towards the radiator. This is not a permanent solution but often resolves the initial noise.

**A final note on orientation:** some manufacturers (Arctic, Corsair, Lian Li) offer blocks with rotatable logos or displays, so aesthetics do not force a thermally incorrect mounting. If your AIO does not allow this and you still want a precise tube orientation, it is better to choose another model than to compromise physics for a straight logo.

### 8.3.4 Air direction: radiator as intake or exhaust?

A question asked continuously and almost always answered incorrectly. The two options:

**Radiator as intake**: fans blow fresh air *from the outside* through the radiator, into the case. Advantage: the radiator receives the coldest air available (ambient temperature), so the CPU is cooler — typically 3–6 °C less. Disadvantage: the air entering the case after passing through the radiator is preheated, so the GPU, VRM, SSD, and RAM run a few degrees warmer.

**Radiator as exhaust**: fans draw air from the case through the radiator and expel it. Advantage: the rest of the case breathes fresh air. Disadvantage: the radiator receives air already heated by the GPU, so the CPU is warmer.

There is no universal answer; there is a criterion: **prioritize the most critical component**. If you have a Core i9 in a rendering system and a modest GPU, it makes sense to put the radiator as intake at the front. If you have a high-end 400 W GPU and an average CPU in a gaming system, it makes sense to put the radiator as exhaust on top and let the front bring fresh air to the GPU. The most common and balanced configuration in modern cases is: **radiator on top as exhaust, front fans as intake**. With a caveat: if the radiator is an intake from the front, a dust filter must be placed in front of it, because a radiator clogged with dust dramatically loses efficiency, and cleaning it is much more difficult than cleaning a fin stack of a tower cooler.

### 8.3.5 Brands and Models

*Overview based on the best available knowledge; the AIO segment is rapidly evolving, and revisions often change, so always check reviews for the specific revision on sale.*

**Arctic Liquid Freezer (II and III series).** The de facto benchmark for price/performance ratio, and for several revisions, also the absolute performance benchmark. Distinctive features: 38mm radiator (thicker than standard), **a 40mm VRM fan integrated into the block** that cools the motherboard's voltage regulators (something no other AIO does and that traditional AIOs, unlike low-profile coolers, remove), adjustable PWM pump, aggressive pricing. Cons: spartan aesthetics, somewhat inconvenient single cable, radiator thickness can cause clearance issues in some cases.

**Corsair (iCUE Link Titan, H-series).** Very refined ecosystem, extensive software, high build quality, high prices. The iCUE Link system reduces cabling with a single daisy-chain connector, which is a real practical advantage but locks you into the ecosystem.

**Lian Li Galahad II.** Very competitive performance, high-end aesthetics, refined lighting, medium-to-high prices. A good alternative to Corsair for those who want aesthetics without software lock-in.

**NZXT Kraken (and Elite).** The strong point is the large, customizable LCD display on the block; thermal performance is good but rarely top-tier, and you pay a premium for aesthetics and software (CAM).

**DeepCool (LT, LE, Mystique series).** Solid value for money, good performance, refined aesthetics. *Note: availability of this brand may vary by market; check local availability.*

**be quiet! (Silent Loop, Pure Loop).** Focused on quiet operation, with particularly discreet pumps. Performance is rarely top-tier, but acoustically excellent.

Technologically, it's worth noting that for many years, a large portion of AIOs on the market, regardless of brand, used pumps manufactured by **Asetek** under license (or by CoolIT, the other major OEM). This means that the "heart" of seemingly different AIOs was often identical, and the real differences lay in the radiator, fans, tubes, and software. This is an additional reason not to pay an excessive premium for the brand, and to focus on measured data: radiator size, performance certified by independent reviews, and noise measured at the same dissipation level.

### 8.3.6 Pros and Cons of AIOs

**Pros.** For high-end CPUs, a 360/420mm AIO outperforms air cooling: not by a small margin, in some cases by 8–12 °C under heavy load, resulting in higher sustained frequencies. It frees up space around the socket: no conflicts with RAM (any memory can be installed, even with the tallest heatsinks), no conflicts with the first PCIe slot, no weight hanging from the motherboard. It allows for compact builds with powerful CPUs, as the radiator goes wherever there's space. And, frankly, it's aesthetically cleaner: the motherboard remains visible, lighting is visible, and side-glass systems make sense.

**Cons.** First and foremost, the cost: a decent 360mm AIO starts at 90–110 euros and quickly goes up. The **pump is a single point of failure**: if it stops, the CPU temperature rises vertically within seconds, and the system throttles or shuts down; unlike a fan, there is no gradual degradation and no redundancy. When installing an AIO, it's good practice to connect the pump to the motherboard's **AIO_PUMP** or **CPU_FAN** connector and set the zero-speed alarm, so the BIOS blocks startup if the pump isn't running. The **lifespan** is limited: it's typically estimated at **5–7 years**, constrained by liquid evaporation through the tubes, wear on the pump bearing, and potential debris accumulation in the waterblock's microchannels. An air cooler, over the same period, only needs a new 20-euro fan. Finally, noise: the pump adds a continuous acoustic source that doesn't exist in air cooling, and not all pumps are silent at low speeds.

### 8.3.7 Typical Buyer Mistakes (AIO)

Buying a **cheap 240mm AIO instead of a 40-euro tower cooler**, believing that "liquid = better." It's not: you pay more for equal or inferior performance, with an extra pump that can break.

**Mounting the radiator at the bottom of the case** or **at the front with tubes up**, for cabling or aesthetic reasons. See 8.3.3.

**Not checking available space**: radiator + fans can exceed 55–65 mm in thickness. On the top of many cases, this interferes with the motherboard's VRM heatsinks or RAM. The case specification usually states a maximum "clearance" for the top; it must be respected.

**Connecting the pump to a generic fan connector with an aggressive PWM curve.** The pump is not a fan: running it at 40% to "silence" it reduces flow and performance. Most pumps should be kept at a fixed, high speed (often 100%); some modern ones accept PWM, and the manufacturer specifies how. Read the manual.

**Neglecting the break-in period.** In the first few days, an AIO may produce a slight gurgling noise that subsides as air bubbles settle. If the noise persists after a week, the installation is incorrect.

### 8.3.8 Budget-Tier Recommendations (AIO Liquid)

**Entry.** None. If the budget is low, get a tower cooler. A cheap AIO is a bad deal.

**Mid (CPUs up to ~200W, desire aesthetics or have tall RAM constraints).** A good brand 240 or 280mm. Arctic Liquid Freezer III 240/280 is the rational choice.

**High-end (CPUs over 250W).** A high-end 360mm, or a 420mm if the case allows. Arctic Liquid Freezer III 360/420, Corsair, Lian Li Galahad II 360. For a Core i9 or a Threadripper, this is where liquid cooling is not an aesthetic choice but a technical necessity.

---

## 8.4 The Custom Loop: For Completeness, and to Understand Why Almost No One Should Do It

A **custom loop** is a liquid cooling circuit assembled by the user component by component. It arises from the desire to overcome the limitations of AIOs (larger radiators, multiple radiators, and especially the ability to liquid cool the GPU as well), and from a passion for the object itself.

### 8.4.1 The Components

**Pump and Reservoir.** The pump moves the liquid; the reservoir is a tank that facilitates filling, collects air, and provides a liquid margin. In modern practice, pumps and reservoirs are almost always purchased as an integrated **combo**. The two dominant pump platforms are the **D5** and the **DDC**, both derived from industrial designs (Laing/Xylem and Laing, respectively). The **D5** has high flow rate and medium head pressure (the ability to overcome circuit resistance), is larger, and very quiet and reliable. The **DDC** has higher head pressure and lower flow rate, is more compact but runs hotter and is typically noisier. In a long loop, with multiple radiators and very restrictive blocks, the DDC has an advantage; in the majority of home loops, the D5 is the sensible choice.

**CPU Waterblock.** A nickel-plated copper block with a microchannel base (channel width on the order of tenths of a millimeter), an acrylic or acetal top, and threaded fittings. Performance differences between good CPU waterblocks are modest (2–4 °C); mounting quality and uniform pressure are more important.

**GPU Waterblock — this needs to be explained well, because this is where people get into trouble.**

A stock graphics card is sold with a proprietary air cooler, screwed to the **PCB** (Printed Circuit Board, the card's circuit board) and connected to hot components via thermal paste on the GPU and **thermal pads** (sheets of soft conductive material) on memory and VRMs. To liquid cool the GPU, you must:

1. **Completely disassemble the stock cooler**, which means removing the backplate, unscrewing the plate, disconnecting the fan cables, and separating the fin stack from the PCB — an operation that requires care because the cooler adheres to the chip via hardened thermal paste.
2. **Perfectly clean the PCB** of paste and pad residue.
3. **Install a full-cover waterblock**, meaning a plate that covers not only the GPU but also the GDDR memory and VRM. Here's the crucial point: **the waterblock is specific to that PCB**, not to that GPU model. A GeForce of a certain series produced by different manufacturers has different PCBs, with components positioned differently. A waterblock designed for the "reference" PCB (the chip manufacturer's reference design) **is not compatible** with a custom PCB card. Detailed compatibility lists exist (EK, Alphacool, and Bykski publish them) and should be consulted **before** buying the graphics card, not after.
4. **Apply thermal pads of the correct thickness** to memory and VRMs: here the margin of error is small. A pad that is too thin will not make contact (and the memory will burn out or throttle); a pad that is too thick will prevent the block from resting on the GPU die (and the GPU will throttle). Thicknesses are measured in tenths of a millimeter, and the waterblock kit provides them pre-cut and sized — use those, do not improvise.

**On warranty.** This is a delicate topic and needs to be stated precisely. In many non-EU countries, manufacturers declare that removing the heatsink voids the warranty, full stop. In the European Union, the situation is more nuanced: the **legal guarantee of conformity** (two years, provided for by the EU directive transposed into Italian law by the Consumer Code) covers defects of conformity present at the time of delivery, and the seller cannot exclude it entirely simply because the product has been opened. However, the seller **is not obliged to cover damage caused by the user**, and the burden of proving that the fault is not due to the modification falls, after the first few months, on the consumer. In practice: disassembling the heatsink of a 1,500 euro GPU is a risk taken by the user, and in the event of a fault, the discussion with the seller will be long and the outcome uncertain. Some manufacturers (historically few, and policies change over time — *verify case by case, the data is volatile*) explicitly state that they tolerate heatsink replacement. **The honest recommendation is: do not do it on a card you cannot afford to replace.**

**Radiators.** Custom loops use standard 120/140 mm module radiators, in lengths 240/280/360/420/480, and in three typical thicknesses: **30 mm** (slim), **45 mm** (standard, most common), **60 mm** (thick, for high static pressure fans). The empirical rule for sizing, cautious but reliable: **approximately 120 mm of radiator for every 150–200 W to dissipate**, with fans at moderate speed. A loop with a CPU (250 W) and GPU (400 W) therefore requires at least a 360 + a 240, and is more comfortable with two 360s.

**Fittings.** These are the connectors that link tubes and components. The universal standard is **G1/4"** threading. The main types are **compression fittings** (with a nut, for soft tubing: the tube is pushed onto the barb and a nut is screwed on to compress it — secure and reversible) and fittings for **hard tubing** (with O-rings that seal against the outer wall of the tube). There are also angled fittings, drain valves (essential, and too often omitted), bulkhead pass-throughs, and various adapters.

**Tubing.** Two worlds: **soft tubing** (flexible tubing, in PVC or silicone, typically 10/13 mm or 10/16 mm — inner/outer diameter) is flexible, forgiving of errors, cut with scissors, and is the rational choice for beginners. **Hard tubing** (rigid tubing, in PETG, acrylic, or metal) must be measured, cut, heat-bent with special inserts, deburred, and polished: the aesthetic result is superior, the production time multiplies, and every error costs a piece of tubing. It is craftsmanship, and should be approached as such.

**Liquid.** Distilled water (never tap water: mineral salts deposit and conduct) with biocide and anti-corrosion additives, or a ready-made commercial **premix** (EK CryoFuel, Mayhems, Aquacomputer). Opaque colored liquid (pastel) is beautiful and problematic: it tends to sediment and clog microchannels; clear or slightly tinted liquids are far more reliable. **Golden chemical rule: never mix copper and aluminum in the same circuit.** Contact between two different metals in the presence of an electrolyte triggers **galvanic corrosion**: aluminum (less noble) corrodes rapidly, debris clogs the channels, the loop dies. Since almost all water blocks are made of copper and some inexpensive radiators are made of aluminum, this is a real and frequent mistake. Verify the materials of each individual component.

### 8.4.2 Brands

**EK Water Blocks** is historically the most popular brand, with the widest catalog of GPU water blocks and a complete ecosystem; *it should be noted that the company's stability has faced difficulties in recent years, impacting deliveries and support — a volatile data point that should be verified at the time of purchase.* **Alphacool** offers excellent value for money, all-copper radiators, and a very wide range. **Watercool** (German brand, Heatkiller series) is the benchmark for absolute build quality, at high prices. **Corsair Hydro X** is the "turnkey" solution for those who stay within the Corsair ecosystem, well-made but expensive. **Bykski** and **Barrow** are Chinese brands that offer components at a fraction of the Western price, with variable but often surprisingly good quality: they are the affordable entry point to custom loops, provided you carefully check compatibility.

### 8.4.3 Costs, maintenance, and for whom it's truly worthwhile

**Costs.** A decent CPU-only loop starts from 350–450 euros. A CPU + GPU loop realistically costs between 700 and 1,200 euros, and easily goes higher. This should be compared with a 360 mm AIO at 130 euros which, for the CPU, offers 85–90% of the benefit.

**Maintenance.** It's not optional. The liquid must be **replaced approximately every 12 months** (some extend to 18–24 with quality premixes), the circuit must be flushed, and filters and traps must be checked. Each intervention requires draining the loop, which means half a day of work. And there's the ever-present risk of a **leak**: a fitting not tightened properly, a damaged O-ring, a poorly cut rigid tube. A leak on a powered motherboard destroys components worth hundreds or thousands of euros. For this reason, the **leak test** — filling the loop and pressurizing it (or powering only the pump with a jumper on the 24-pin connector, keeping the rest of the system off) for at least 12–24 hours before turning on the PC — is not a recommended precaution, it is a mandatory procedure.

**For whom is it worthwhile?** With brutal honesty: **almost no one, from a rational point of view**. The CPU performance of a custom loop only slightly surpasses that of a good 360 AIO. The real technical advantage is GPU cooling, which can lower temperatures by 25–30 °C, eliminate fan noise from the card, and allow significant overclocks. But it's an advantage that costs 800 euros, ten hours of work, a warranty risk, and annual maintenance.

The custom loop is suitable for three categories: those seeking **absolute silence** in a very powerful system (a large loop with fans at 500 RPM is practically inaudible, and no other technology allows this); those building **extreme** systems with multiple GPUs or aggressive overclocks; and those who do it **for the pleasure of doing it**, as a hobby, exactly like restoring a vintage motorcycle. The latter is a perfectly legitimate motivation, as long as it is declared as such and not disguised as technical rationality.

---

## 8.5 Air vs. Liquid: The Final Decision

| Criterion | Air (Dual Tower) | AIO 240–280 | AIO 360–420 | Custom Loop |
|---|---|---|---|---|
| Performance on CPU ≤ 150 W | Excellent | Equivalent | Excessive | Excessive |
| Performance on CPU 200–250 W | Good | Good | Very Good | Very Good |
| Performance on CPU > 280 W | Insufficient (throttling) | Insufficient | Very Good | Excellent |
| Noise at same dissipation | Excellent (no pump) | Decent | Good | Excellent (at low speed) |
| Cost | €30–150 | €80–130 | €110–220 | €400–1,200+ |
| Reliability / Failure Points | Only fan | Pump | Pump | Pump, fittings, leaks |
| Expected Lifespan | 10+ years | 5–7 years | 5–7 years | Indefinite, with maintenance |
| Maintenance | Dusting | None | None | Annual, mandatory |
| Space around socket | High (RAM conflicts) | None | None | None |
| Aesthetics | Bulky | Clean | Clean | Maximum |
| Also cools GPU | No | No | No | Yes |
| Assembly Complexity | Low | Low/Medium | Medium | Very High |

**Unambiguous recommendations by CPU tier:**

- **Entry CPU (up to 100 W actual)**: Inexpensive single tower. No reason for liquid.
- **Mid CPU (100–200 W)**: Inexpensive dual tower (Peerless Assassin and similar). AIO only makes sense for high RAM clearance constraints or aesthetics.
- **High CPU (200–280 W)**: High-end dual tower *or* AIO 280/360. Both choices are defensible; air wins on reliability and cost, liquid on temperatures and space.
- **Flagship CPU (over 280 W: top-tier Core i9, Ryzen 9 in PBO, HEDT/Threadripper)**: AIO 360/420 mandatory, or custom loop. Air is not enough here, and insisting means leaving performance on the table.

A final observation, which serves as a general principle for this chapter: **it is almost always smarter to spend more on the CPU and less on the cooler, than the other way around**. The cooler improves performance by a few percentage points; a superior CPU improves it by tens of percentage points. A poorly allocated budget is one where someone puts a 250 euro AIO on a 250 euro processor.

---

## 8.6 Thermal Paste: A Comprehensive Discussion

### 8.6.1 What it's for, and why it's not "a lubricant"

If one were to observe the top surface of the CPU's IHS and the bottom surface of the cooler's base under a microscope — both seemingly smooth and mirrored — one would see two mountainous landscapes. No mechanical process produces a perfectly flat surface: there remain micro-roughness, undulations, and sometimes a slight concavity or convexity due to manufacturing or mounting pressure.

When these two surfaces are pressed against each other, **actual** metal-to-metal contact occurs only on a minimal fraction of the apparent area: typically **less than 1–2%**. All the rest is air trapped in micro-cavities. And air is a poor thermal conductor: approximately **0.026 W/m·K**, compared to about 400 W/m·K for copper. A layer of air a few micrometers thick, extending over 98% of the contact area, constitutes a devastating thermal barrier.

The task of the **TIM** (Thermal Interface Material — this is the correct technical name for "thermal paste") is to **expel the air and fill those micro-cavities with a material that conducts heat much better than air**. Here's the point that needs to be understood once and for all: thermal paste **is not a good conductor in absolute terms** — an excellent paste has 8–13 W/m·K, which is thirty or forty times worse than copper. Paste is a **necessary compromise**: it is much worse than metal, but hundreds of times better than the air it replaces.

From this observation immediately follows the most important operational rule of the entire section: **the paste layer must be as thin as possible**. The paste should not create thickness: it should only fill the gaps. Every extra micrometer of paste between the two metals adds thermal resistance. Anyone who spreads a millimeter of paste "to be sure" is literally building an insulator.

### 8.6.2 Types of TIMs

**Traditional silicone-based pastes.** These are a polymer matrix (silicone or similar) loaded with conductive particles: metal oxides (zinc, aluminum), ceramics, boron nitride, electrically non-conductive metal particles. They are **electrically non-conductive** (with some exceptions for high metallic loads — read the datasheet), which makes them safe: a small amount overflowing onto the socket will not cause short circuits. Stated conductivity is typically between 5 and 14 W/m·K.

A very important methodological note: **conductivity values declared by manufacturers are not comparable between different brands**, because there is no universally adopted measurement standard and each manufacturer uses the method that favors them. A paste that claims 13 W/m·K is not necessarily better than one that claims 8.5 W/m·K. **The only reliable data is empirical comparison in independent comparative tests**, where the difference between good pastes turns out to be 1–3 °C — almost irrelevant compared to the choice of cooler. Anyone who spends 60 euros on a mediocre cooler and then looks for miraculous paste is optimizing the wrong variable.

Concrete references:
- **Arctic MX-4** and **MX-6**: inexpensive, very easy to apply (perfect viscosity), non-conductive, excellent performance, declared longevity of years. They are the default choice for 90% of users, and are hard to beat on price/performance ratio.
- **Noctua NT-H1** and **NT-H2**: excellent, very stable over time, easy to apply and clean. A bit more expensive. The NT-H2 is the improved version.
- **Thermal Grizzly Kryonaut**: historically among the best in pure performance, with a serious caveat: it suffers from **pump-out** (see below) and degrades relatively quickly with thermal cycles, especially above 80 °C. It's good for benchmarks and for those who reapply paste often; it's a questionable choice for a "mount and forget" system. Variants (Kryonaut Extreme) and more stable formulations exist in the same family.

**Pump-out** deserves an explanation, because it is the main degradation mechanism. Every time the system heats up and cools down, metals expand and contract to different degrees; this produces a micro-movement between the IHS and the heatsink base which, cycle after cycle, **"pumps" the paste out of the interface**, towards the edges. Over time, voids remain in the center (where heat is highest). The result is a gradual worsening of temperatures over months or years. More fluid pastes with less stable matrices are more susceptible to the phenomenon.

**Liquid metal.** It is an alloy of gallium, indium, and tin (sometimes generically called galinstan), liquid at room temperature. Its conductivity is on another planet: **about 70–80 W/m·K**, almost ten times that of an excellent paste. The real gain on the IHS is typically **5–12 °C**, and on a bare die (delidded CPU, laptop, console) it can be even higher. The commercial reference is **Thermal Grizzly Conductonaut** (and Extreme).

The risks, however, are serious and must be listed without sugarcoating:
1. **It is electrically conductive.** A drop falling on an LGA socket, on an SMD component of the motherboard, or on the CPU contacts can cause a short circuit and destroy hardware. In a vertical position, the CPU is vertical: liquid metal, if in excess, can drip.
2. **It corrodes and amalgamates aluminum.** Gallium literally dissolves aluminum: it creates a fragile amalgam and destroys the metal. **Never use it on an aluminum base**, nor near aluminum components. It should only be used on nickel-plated copper or compatible surfaces.
3. **Over time it can be "absorbed" into copper** (diffusion), permanently staining the IHS and the base, and losing effectiveness.
4. It is **difficult to apply**: it must be spread in a very thin layer with a cotton swab, both on the IHS and on the base, and the surrounding area must be shielded with insulating tape or conformal coating.

**In summary: liquid metal is not for the average user.** It makes sense on a delidded CPU, on a laptop that throttles, on a bare die. In a normal desktop build, the risk/benefit ratio does not justify it.

**PTM7950 and phase change materials.** This is the innovation that has changed the game in recent years. **PTM7950** (Honeywell) is a **PCM** (Phase Change Material): at room temperature, it appears as a thin, semi-solid, non-sticky sheet that can be cut and applied. Upon initial heating (transition around 45 °C), it softens, perfectly "wets" the two surfaces by filling micro-cavities, and then partially re-solidifies upon cooling. This cycle repeats with each power-on.

The decisive advantage is not its peak conductivity (which is comparable to a good paste, around 8 W/m·K) but **the almost total absence of pump-out**: since the material re-solidifies, it is not expelled by thermal cycles. The result is exceptional long-term stability: where traditional paste degrades by 5–10 °C in three years, PTM7950 remains substantially unchanged. It has become **the de facto standard for GPUs and laptops**, precisely the applications where repasting is difficult and thermal cycles are aggressive. The small price to pay: in the first few minutes from cold, before the material has reached its transition temperature, performance is slightly worse. This is irrelevant in real-world use.

*Note on a practical risk: as a highly sought-after product, PTM7950 is widely counterfeited on marketplaces. Purchase from reliable sellers.*

**Graphite pads.** Reusable graphite sheets (the reference is **Innovation Cooling Graphite**), with high conductivity in the plane but modest through the thickness. They never dry out, do not pump out, and can be removed and reinstalled. Performance is typically 2–4 °C worse than a good paste. They are a "mount and forget" solution for those who frequently disassemble, or for those who want zero maintenance. A legitimate niche.

**Thermal pads (for memory and VRMs).** These are different and serve another purpose: to fill **gaps** of varying thickness (from 0.5 to 3 mm) between a component and the heatsink. They have low conductivity (1–15 W/m·K) but are compressible and maintain contact. They are used on VRAM, VRMs, and SSD chips. **They are not used under an IHS instead of paste**: there, the gap is micrometers, and a material that thins out is needed, not a pad.

### 8.6.3 How to apply: quantity and methods

**The quantity.** The traditional rule is "a grain of rice" or "a pea." These are useful approximations but should be calibrated to the size of the IHS:
- **AM4** (small, square IHS with chamfers): an amount equivalent to a large grain of rice or a small pea, in the center.
- **AM5** (IHS with characteristic openings on the sides): a pea in the center. Some prefer five dots because the usable surface is "octagonal" in shape.
- **LGA1700 / LGA1851** (rectangular and elongated IHS): a single dot in the center tends not to reach the short ends. A **thin line along the major axis**, or an **X**, or five dots is better.

**The methods.**
- **Central dot (pea method)**: A single drop is placed in the center and mounting pressure is allowed to spread it. This is the method recommended by most manufacturers, it works well, does not introduce bubbles, and requires no skill. **For most users, this is the right method.**
- **X / cross / five dots**: Distributes better on large or rectangular IHSs. Excellent on LGA1700.
- **Line**: Two or three lines parallel to the long axis of the IHS, for rectangular sockets.
- **Spreading**: The paste is spread with a spatula or a plastic-coated card before placing the cooler. It gives maximum control over thickness, but **risks trapping micro-air bubbles** if done incorrectly, and is the method with the most margin for error. It makes sense when using liquid metal (mandatory) or on a bare die (where the surface is small and mounting pressure is low).

**Mounting pressure.** This is not a minor detail: it's what expels excess paste and reduces residual thickness. **The cooler screws should be tightened in a cross pattern, a little at a time, alternating**, exactly like car wheel lug nuts: never fully tighten one screw and then move to the next, as this will tilt the base and create uneven contact. Tighten until the mechanical stop is reached (good systems, like Noctua's SecuFirm2, have a stop that prevents overtightening).

**Do not lift and re-seat the cooler** after mounting it: if it is removed, the paste is compromised (bubbles and voids have formed) and **must be redone from scratch**, cleaning both surfaces. It's tempting to "check" after mounting: don't do it, or if you do, be prepared to clean and reapply.

### 8.6.4 Common errors

**Too much paste.** The most common mistake. An excess does not "insulate" as much as legend would have it (because pressure expels most of it), but it spills over the sides, soils the socket, and in the case of high metallic load pastes, can cause problems. Above all, if there's too much and the pressure isn't enough to expel it, it leaves a thick and thus resistive layer.

**Too little paste.** A more insidious error because it's invisible: areas of the IHS remain without contact, where air acts as an insulator. This results in high and often asymmetrical temperatures (one core much hotter than the others).

**Air bubbles.** Arise from poorly done spreading, or from lifting and re-seating the cooler.

**Not cleaning old paste.** Hardened paste mixed with new paste gives poor results. Cleaning is done with **99% isopropyl alcohol** (not pink denatured alcohol, which leaves residues) and a lint-free cloth (microfiber or laboratory paper; kitchen paper leaves fibers). Clean **both** surfaces: IHS and cooler base.

**Forgetting to remove the protective film from the new cooler.** This deserves its own bolded line, because it is a real, frequent error that produces dramatic symptoms (immediate throttling, temperatures of 100 °C within seconds of power-on). **Almost all new coolers come with thermal paste pre-applied to the base, and with a transparent or colored plastic film for protection.** The film must be **removed** before mounting. The fact that it is transparent and well-adhered makes it easy to overlook. If a new PC throttles instantly, this is the first thing to check.

**Apply paste AND use the pre-applied one together.** No: either use the pre-applied paste (which is of decent quality on almost all branded coolers) *or* remove it completely with isopropyl alcohol and apply your own. Never layer them.

### 8.6.5 How often to replace it, and GPU repasting

**On the CPU.** With a quality paste (MX-4, NT-H2) and normal use, an interval of **3–5 years** is reasonable. With a paste prone to pump-out (like Kryonaut) and heavy thermal loads, it can drop to **1–2 years**. The sign that it's time to intervene is a progressive worsening of temperatures under the same load and environment: if the PC that was at 75 °C in rendering two years ago is now at 88 °C, and dust has already been removed, the paste is the main suspect.

**On the GPU.** This is where replacement yields the most spectacular results, and it's also the most delicate. Graphics cards are assembled in the factory with varying quality pastes and, above all, are subject to very violent thermal cycles. On a 3–5 year old GPU, it's not uncommon to gain **10–20 °C** with a repaste, and even more on the hotspot (the hottest point of the die, measured separately on recent cards: if the delta between average core temperature and hotspot exceeds 20–25 °C, it's a strong indication of degraded paste or uneven mounting).

The procedure requires: disassembling the backplate and plate, disconnecting the fan cables, **photographing everything before disassembling** (position and thickness of thermal pads!), cleaning the GPU and base, applying the new paste (**PTM7950 is the ideal choice here**, precisely because of its resistance to pump-out), **replacing thermal pads only if necessary and with identical thickness** — and this is the trap: a 1.5 mm pad replaced with a 2.0 mm one prevents the plate from resting on the die, and the GPU goes into severe throttling. Tighten the screws **crosswise and progressively**, following the numbering often printed on the backplate.

Regarding warranty, the same considerations apply as for the GPU waterblock in section 8.4: opening a card under warranty is a risk that must be consciously evaluated.

### 8.6.6 Recommendations by tier (thermal paste)

**Entry / normal use**: Arctic MX-4. Economical, excellent, simple, safe. End of story.
**Mid / for those who want the best without risks**: Noctua NT-H2 or Arctic MX-6.
**High-end / GPU and laptops**: PTM7950, for long-term stability.
**Extreme overclocking, bare die, delidded CPU**: liquid metal, with all due precautions and full awareness of the risks.

---

## 8.7 Fans on radiators (and coolers)

### 8.7.1 Airflow vs. static pressure

A fan is defined by a **characteristic curve** that links airflow to pressure: at zero pressure (no obstruction), airflow is maximum; as the obstruction increases and the required pressure rises, airflow decreases, up to the point of **maximum static pressure** (mm H₂O), where the fan spins but no longer moves air.

Fans are designed by prioritizing one end of the curve or the other:
- An **airflow fan** has wide, spaced, few blades and a small motor: it moves a lot of air in free space, but "drowns" in front of an obstacle. This is the right fan for a free front panel or for unobstructed rear extraction.
- A **static pressure fan** has numerous, closely spaced blades with a more aggressive profile and a larger hub: it moves less free air but pushes much better through resistance. **This is the mandatory fan for a radiator or a dense fin stack.**

Mounting an airflow fan on a 38 mm radiator with dense fins means throwing away a significant part of the radiator's performance. This is a common mistake, often made for aesthetic reasons (the "beautiful" RGB fan is almost always an airflow fan).

In modern practice, many quality fans are hybrid and work well in both regimes (Arctic P12/P14, Noctua NF-A12x25, Phanteks T30, be quiet! Silent Wings): the most reliable way to choose is to read the **static pressure specifications** (a value above 2.0–2.5 mm H₂O is indicative of a fan suitable for radiators) and reviews with measurements at the same noise level.

### 8.7.2 Push, pull, push-pull

These are the three ways to arrange fans relative to the radiator.

**Push**: the fans are in front of the radiator relative to the flow direction, and **push** air into it. This is the most common configuration and the most efficient for a single fan, because the air reaches the radiator already under pressure.

**Pull**: the fans are behind the radiator and **pull** air through it. Performance-wise, it is slightly inferior to push, because the fan also draws from the edges and the flow is less uniform. However, it has a practical advantage: **the radiator remains accessible and visible** and dust settles on the outer face of the radiator instead of on the fans. In some cases, it is the only configuration that physically fits.

**Push-pull**: fans on both sides, pushing from one side and pulling from the other. The benefit is real but modest: typically **2–5 °C** on a thick radiator with dense fins, less than 2 °C on a thin radiator. The cost is double in fans, double in potential noise (although, for equal dissipation, all fans can be kept at lower RPMs, which *reduces* overall noise), and double in occupied thickness — a 38 mm radiator in push-pull with 25 mm fans occupies **88 mm**, which doesn't fit in many cases.

**Operational advice:** push-pull is worthwhile on thick radiators (38 mm and above), on radiators with high fin density, and when the goal is silence (because four fans at 800 RPM make less noise than two at 1,400 RPM for the same dissipation). On a standard 27 mm radiator, in a normal system, the gain does not justify the expense.

### 8.7.3 Orientation: understanding which way a fan blows

A trivial question, and one that's constantly answered incorrectly. There are two rules, and they are infallible:
1. **Air enters from the side with the sticker/hub with the logo... no.** Attention: air enters from the *open* side (where you only see the blades and the frame) and exits from the side where you see the **support spokes** (the four or five arms holding the motor) and the back of the hub. In other words: **the fan blows towards the side where you see the structural "grille."**
2. Practically all fans have **two small arrows stamped on the frame** (one indicates the direction of flow, the other the direction of rotation). Look at those and trust them.

If you make a mistake, the symptom is clear: high temperatures and an airflow that, when you put your hand near it, doesn't go where it should.

### 8.7.4 Control: PWM, DC, and curves

Fans connect to the motherboard with **3-pin (DC)** or **4-pin (PWM)** connectors. In **DC** (Direct Current) control, the motherboard varies the supply voltage (from 12 V downwards) to slow down the fan; below a certain voltage (typically 4–5 V), the fan doesn't start at all. In **PWM** (Pulse Width Modulation) control, the power supply remains at 12 V, and a fourth wire carries a signal that rapidly switches the motor on and off, varying the **duty cycle** (the percentage of time it's on). PWM allows for finer control and lower minimum speeds (often 200–300 RPM), and is the correct choice for a silent system.

The **fan curve** is set in the BIOS/UEFI or via software: it associates a speed with each temperature. Two concrete tips that are worth more than much expensive hardware:
- **Linking case fan curves to CPU temperature is a common mistake**, because the CPU has very violent boost transients (from 40 to 80 °C in half a second, then down) that make the fans "pump" annoyingly. It's better to link them to a slower sensor (the motherboard temperature, or the liquid temperature if the AIO exposes it) or, if not possible, set a generous **hysteresis / step-up and step-down time** (3–5 seconds) to dampen oscillations.
- **The AIO pump should not be treated like a fan**: it connects to the AIO_PUMP connector and follows the manufacturer's instructions, which in most cases is "100% constant" or a very flat curve.

---

## 8.8 Operational summary — cooling decision checklist

To be used in order, before any purchase.

**1. Quantify the actual thermal load.**
☐ Look for the actual power consumption under heavy load of your CPU in independent reviews (not the declared TDP: look for MTP for Intel, PPT for AMD, or better yet, measurements in Cinebench or Prime95).
☐ Add a 15–20% margin if you plan to use PBO / overclock / unlocked power limits.

**2. Decide on the technology.**
☐ Below ~100 W → inexpensive single tower. Done.
☐ 100–200 W → inexpensive dual tower (Thermalright Peerless/Phantom, Arctic Freezer 36). This is the default choice for the vast majority of users.
☐ 200–280 W → high-end dual tower **or** AIO 280/360. Choose air if you prioritize reliability, cost, zero maintenance. Choose liquid if you have tall RAM, want a clean aesthetic, or if your case doesn't accommodate large towers.
☐ Over 280 W → AIO 360/420. Below this threshold, you will leave performance on the table.
☐ Custom loop → only if you also want to cool the GPU, seek absolute silence, or do it as a hobby. It is never the economically rational choice.

**3. Check compatibility (all three, without skipping any).**
☐ **Socket**: Does the mounting kit include your socket (LGA1700 / LGA1851 / AM4 / AM5)? Verified on the cooler manufacturer's website, not the seller's product page.
☐ **Height (air)**: Is the cooler's height less than the case's "max CPU cooler height," with at least 3–5 mm of clearance?
☐ **RAM (air)**: Is the height of your memory modules compatible with the declared clearance? If you already have a large tower, did you choose low-profile RAM?
☐ **Radiator (liquid)**: Does the case support that length *in that position*? Does the thickness (radiator + fans) fit within the declared clearance, without touching the VRM or RAM?
☐ No conflict with the first PCIe slot or M.2 heatsinks?

**4. Plan the installation.**
☐ (Liquid) The radiator is on the **top**, or on the **front with the tubes at the bottom**. **Never** on the bottom. **Never** front with tubes at the top.
☐ (Liquid) The pump is **not the highest point in the loop**.
☐ (Liquid) The pump is connected to the AIO_PUMP connector with zero speed alarm active.
☐ The fans on the radiator are **static pressure** (≥ ~2 mm H₂O), not pure airflow fans.
☐ Fan direction is verified with the arrows on the frame.
☐ (Intel LGA1700) Have you considered a **contact frame** to compensate for ILM deformation? It's worth 3–8 °C for 15–20 €.

**5. Thermal paste.**
☐ **Have you removed the protective film** from the base of the new cooler? (Yes, check again.)
☐ If reusing a cooler: both surfaces cleaned with **99% isopropyl alcohol** and a lint-free cloth.
☐ Quantity: a pea in the center (AM4/AM5) or a line/X (LGA1700/1851). Little, not a lot.
☐ Screws tightened **in a cross pattern, progressively**, never one at a time all the way.
☐ Cooler placed **only once**: if you lift it, clean and reapply.
☐ Paste chosen: MX-4 (default), NT-H2 (premium), PTM7950 (GPU/laptop, longevity), liquid metal (only if you know exactly what you are doing).

**6. Post-installation check.**
☐ Stress test of at least 15 minutes (Cinebench in loop, Prime95, OCCT) monitoring **simultaneously** temperature, sustained frequency, and power consumption.
☐ Temperatures within the ranges of the table in 8.1.4 — remembering that 90–95 °C on a Ryzen 9 under full load is not a fault, it is the designed behavior.
☐ No gurgling noise from the pump after the first few days of break-in.
☐ Fan curve set with adequate hysteresis, to avoid acoustic "pumping."

**7. Scheduled maintenance.**
☐ Dust removal from fin stack / radiator every 6–12 months.
☐ CPU repaste every 3–5 years (or sooner, if temperatures progressively worsen under the same load).
☐ GPU repaste after 3–5 years, considering PTM7950 and photographing pads before disassembly.
☐ (AIO) Plan for replacement after 5–7 years.
☐ (Custom loop) Liquid change and flush every 12 months. Leak test mandatory after each intervention.

---

*Next chapter: the power supply and power distribution — that is, the only component whose failure can take out all the others.*

---

[← Previous](07-case-ventilation.html) · [All chapters](./) · [Next →](09-pc-assembly.html)
