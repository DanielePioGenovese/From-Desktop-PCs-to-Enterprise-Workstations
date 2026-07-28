---
title: "Chapter 7 - The Case and Ventilation"
parent: "English Edition"
nav_order: 7
---

<details open markdown="block">
  <summary>Chapter contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## Foreword: The Component Everyone Underestimates

There's an implicit hierarchy in the mind of anyone building their first computer. At the top are the CPU (Central Processing Unit) and the GPU (Graphics Processing Unit), because these are the parts discussed in reviews and featured in benchmarks. Then comes RAM (Random Access Memory, volatile working memory) and storage, because at least they have a number that grows and can be compared. Then the motherboard, chosen for its socket and price. Then the power supply, often chosen poorly. And finally, as an afterthought, almost an aesthetic accessory, the case: the "cabinet," the "chassis," that box of sheet metal and glass that holds everything together.

This hierarchy is wrong, and for a very concrete reason: the case is the only component that affects **all others simultaneously**. It doesn't calculate anything, doesn't produce frames, doesn't store data, but it determines the temperature at which the CPU and GPU operate, the noise the system produces in the room, the amount of dust that accumulates on the heatsinks, the useful life of the components, and — something only discovered once assembly begins — how painful or pleasant the assembly and maintenance experience will be.

A wrong case won't make your computer explode. It does something more insidious: it silently slows it down. A GPU that operates at 68 °C in a well-ventilated chassis, maintaining its maximum boost, can reach 84 °C inside an "aquarium" enclosed by three glass panels. At that point, the boost algorithm—which is dynamic and temperature-sensitive—lowers frequencies to stay within thermal limits. The result is a 5-10% performance loss that the user doesn't see because they never saw the "before." They simply bought an 800 euro graphics card and are only using 730 euros worth of its performance.

This chapter covers the case and everything surrounding it—formats, compatibility, airflow, fans, filters, brands—with the same level of detail reserved for "noble" components. By the end, you will have the tools to open a chassis's technical specifications on an e-commerce site and understand, in ninety seconds, whether it's suitable for your configuration or if it's a trap.

> **Note on volatile data.** Models, prices, and availability of cases and fans change rapidly and vary greatly across markets. Every euro figure reported in this chapter is indicative of the Italian market and should be verified at the time of purchase. Data marked **[volatile data]** are most subject to aging.

---

## 1. The role of the case

### 1.1 Mechanical and electrical protection

The oldest and most obvious function of the case is that of a structural container. PC components are fragile in a non-intuitive way: the motherboard is a fiberglass and copper sandwich with hundreds of solder joints that crack if the PCB (Printed Circuit Board) is flexed; a large air cooler can weigh 1.5 kg and, if the system is transported vertically in a car, exerts enormous leverage on the four socket mounting points; a modern triple-slot graphics card weighs up to 2 kg and is suspended from a PCIe (Peripheral Component Interconnect Express, the high-speed expansion bus) connector and two screws on the rear bracket.

The case provides the rigid frame that absorbs these stresses. It does so through the **motherboard tray**, the vertical metal sheet to which the motherboard is screwed via **standoffs**: small threaded cylinders that raise the PCB a few millimeters from the sheet, preventing electrical contact between the copper traces on the back of the board and the metal of the chassis. It's good to remember this immediately, because it's one of the classic beginner's mistakes: **mounting the motherboard without standoffs, or leaving a standoff in a position where the board doesn't have a hole, causes a short circuit** which, in the best case, prevents power-on and, in the worst case, permanently damages the board.

Then there's a less visible electromagnetic function. A PC is a source of EMI (Electromagnetic Interference): inside the case, there are signals switching billions of times per second, and a closed, grounded metal enclosure acts as an imperfect but effective **Faraday cage**, reducing both emissions to the outside and susceptibility to external disturbances. This is why cases have expansion slots closed by metal brackets and why there are regulations (FCC in the United States, CE marking in Europe) that chassis must comply with. In practice, for the home user, this translates into a simple rule: **the brackets for unused PCIe slots must be reinstalled**, not because fluid physics dictates it, but because they serve to close the shell and — secondarily — to prevent air from entering or exiting through unintended paths.

### 1.2 Thermal Management: The True Function

This is the core of the chapter. From a thermodynamic perspective, a computer is an **electric energy to heat converter**. Practically 100% of the power drawn from the outlet (minus a negligible amount that exits as LED light and Wi-Fi radio waves) eventually ends up as heat in the room's air. A system that draws 500 W from the outlet is, from an environmental heating perspective, indistinguishable from a 500 W electric heater.

The problem is not the heat itself: it's **power density**. The CPU dissipates its TDP (Thermal Design Power, the thermal power designed, i.e., the heat the cooling system must be able to dissipate under sustained conditions) from a surface of a few square centimeters; the GPU does the same from a die the size of a postage stamp. The CPU cooler and the graphics card cooler are tasked with taking that concentrated heat and transferring it to the air passing through them. But the air passing through them **is the air inside the case**.

This is the central idea, and it bears repeating because it's the source of 90% of errors: the CPU cooler and the GPU cooler do not cool with room air. They cool with case air. The case's job is to make the case air as similar as possible to room air, meaning it should be continuously replaced before it heats up. If the case doesn't exchange air, a condition of **recirculation** occurs: the GPU expels air at 55 °C, that air finds no way out, is re-aspirated by the GPU's own fans and the CPU cooler, and the system stabilizes at a much higher thermal equilibrium. The coolers still work perfectly — they are simply working with incoming air at 45 °C instead of 24 °C, and those 21 degrees of difference are almost entirely transferred to the chip temperatures.

The parameter describing this phenomenon is called **ambient-to-case delta T**: the difference between the temperature of the air inside the case (measurable with sensors, or estimable from motherboard temperature sensors) and the room temperature. In a well-ventilated case under load, this delta is 3-6 °C. In a closed and poorly configured case, it can exceed 15-20 °C. **Every degree of delta T is an extra degree on the CPU and GPU**, added to everything else.

### 1.3 Acoustics

The second axis on which the case works is noise. Here there is a fundamental compromise that needs to be understood immediately, because no marketing will ever explicitly state it: **airflow and silence are in tension**. PC noise has three origins:

1. **Aerodynamic noise** from fans, generated by air turbulence passing through blades, grilles, and heatsink fins;
2. **Mechanical noise** from fan bearings, liquid cooling pumps, and mechanical hard drives;
3. **Structural vibrations** transmitted by the frame, which acts as a sound box if the metal sheets are thin.

A traditional "silent" case — think of historical models from be quiet! or the Fractal Design Define — addresses the problem with **insulation**: side and front panels internally lined with sound-absorbing material (bituminous or polyurethane foam), a closed front with a solid door, and reduced openings. This works on the noise that escapes, but it worsens airflow, so the fans have to spin faster to compensate, and faster fans make more noise. The net gain exists, but it is much smaller than commonly believed.

The modern school — which has dominated the market from 2019-2020 onwards — has inverted the reasoning: **a very open (mesh) case with large, slow fans is preferred**. The reasoning is that aerodynamic noise grows in a strongly non-linear way with rotational speed (as a first approximation, acoustic power scales as the fifth power of the peripheral speed of the blades: doubling the RPM means a noise increase of about 15 dB, which is "much more than double" to human perception). If the case offers little resistance, the fans can spin at 700-900 RPM instead of 1400-1600 to move the same amount of air, and the net result is **cooler AND quieter**, even if the residual noise is less "shielded". This is currently the winning strategy for the vast majority of users, and independent tests consistently confirm it.

There are still edge cases where insulation makes sense: very low-power systems (an office PC with a 65W CPU and no dedicated graphics card) placed in very quiet rooms, where the residual noise of low-speed fans is still perceptible and the acoustic barrier makes a difference. But a gaming system with a 300W GPU cannot be made silent by enclosing it: it can only be made hot.

### 1.4 Aesthetics

It must be said honestly: for a huge segment of the market, the case is **the only component that is visible**, and it is therefore legitimate for aesthetics to weigh in on the choice. The point is not to deny this need, but to order it correctly relative to the others. The practical rule I propose, and which I will return to in the selection procedure, is: **aesthetics is a selection criterion within the set of cases that work, not a criterion that defines the set.** First, filter by compatibility and airflow, then choose the most aesthetically pleasing among those remaining. The set is quite broad: in 2025-2026, there are dozens of beautiful *and* well-ventilated cases, so it is no longer necessary to compromise. Those who choose the "aquarium" display case before looking at temperatures are simply reversing the order of factors, and they will pay for it in degrees.

### 1.5 Assembly and Maintenance Ergonomics

The last role, one that is only appreciated after the second or third assembly: a good case allows for easy assembly. The features that make a difference are concrete and verifiable in video reviews:

- **space behind the motherboard tray** for cable management (from the scarce 20 mm of economic cases to the 30-35 mm of well-designed models);
- **tool-less side panels**, snap-on or with magnetic closure;
- **rolled edges** on the sheet metal, which prevent cutting fingers — a detail that immediately distinguishes a well-crafted chassis from one built on a budget;
- **reusable expansion slot covers**, not tear-off: some economic cases have PCIe slot covers fixed with a metal bridge to be broken, and once broken, they cannot be reattached;
- **removable dust filters without disassembling the PC**, ideally from the front or top, not from underneath requiring the case to be lifted;
- **removable drive trays** and access to the rear without having to lay the machine down.

None of this appears in a specifications table, and all of it determines whether working on the computer again in two years will be an afternoon task or a nightmare.

---

## 2. Form Factors: Chassis and Motherboard

### 2.1 Motherboard Form Factors, Reviewed from a Case Perspective

The motherboard form factor — covered in the dedicated chapter — is the primary constraint that determines the case, because the chassis must have the standoffs in the correct positions and the physical space to contain it. There are four form factors relevant to the consumer market, defined by the **ATX** standard (Advanced Technology eXtended, the standard introduced by Intel in 1995 that governs dimensions, mounting holes, and connector placement):

| Form Factor | Dimensions (mm) | Expansion Slots | Typical Use |
|---|---|---|---|
| E-ATX ("Extended ATX") | 305 × 330 (nominal, but variable) | 7 | Workstations, HEDT, high-end boards |
| ATX | 305 × 244 | 7 | Reference standard, full desktop |
| Micro-ATX (mATX) | 244 × 244 | 4 | Compact budget, offices |
| Mini-ITX (ITX) | 170 × 170 | 1 | SFF (Small Form Factor) systems |

An important clarification and source of real trouble: **"E-ATX" is not a rigorous standard**. Formally, it indicates 305 × 330 mm, but manufacturers also use the label for boards measuring 305 × 277 mm or 305 × 267 mm (the so-called "EE-ATX" or simply enlarged ATX formats). A case declared "E-ATX compatible up to 280 mm" will accept a 277 mm board but not a 330 mm one. **Always check the actual width of the board in millimeters**, not the acronym. This is one of the most costly mistakes because the problem is only discovered when everything is already in hand.

Compatibility is **descending**: a case that accepts ATX also accepts mATX and ITX, because the standoff holes are a subset. The reverse is not true. Mounting an ITX in an ATX case is perfectly legitimate (there will be a lot of empty space, which is not a thermal defect, if anything the opposite), but it is a waste of volume and money.

### 2.2 Case Form Factors

The commercial names of chassis are less standardized than one might think. There is no official definition of "mid tower." A convention is used, and the most honest parameter for comparing chassis is **volume in liters**, which many manufacturers and specialized websites now declare.

| Category | Typical Height | Typical Volume | Max Mobo | Typical Fans | Notes |
|---|---|---|---|---|---|
| Full tower | 500-650 mm | 60-100+ L | E-ATX / SSI-EEB | 8-12 | Custom loop, many drives, workstation |
| Mid tower | 420-500 mm | 40-60 L | ATX (often "narrow" E-ATX) | 5-9 | **The reference format** |
| Mini tower / mATX | 350-420 mm | 25-40 L | mATX | 3-5 | Space/cost compromise |
| SFF (Small Form Factor) | variable | 8-25 L | Mini-ITX | 1-4 | Extreme compactness, many compromises |
| Cube / "dual chamber" | variable | 30-60 L | ITX → E-ATX | variable | Dual-chamber layout, aesthetics |

**Full tower.** Makes sense in three cases: custom liquid cooling (with multiple 360/420 mm radiators mounted simultaneously), a large number of mechanical drives (home NAS, video archives), or true E-ATX workstation motherboards. Outside of these scenarios, it's wasted space and money: a full tower is not automatically cooler than a good mid tower, because volume itself doesn't cool — **airflow** cools. A full tower with three fans is worse than a mid tower with six.

**Mid tower.** This format solves 90% of use cases. It accepts ATX boards, 360 mm radiators, 340-400 mm long graphics cards, and 160-175 mm tall air coolers. This is where manufacturers' research and development is concentrated, where there is the most competition, and thus the best quality/price ratio. **If you don't have an explicit and articulable reason to leave this category, stay in it.**

**Mini tower / mATX.** Makes sense when the constraint is desk space or budget. An mATX motherboard costs less than an equivalent ATX, and the four expansion slots are more than sufficient for anyone without particular needs (in 2026, expansion slots are almost exclusively for the GPU: audio, network, and USB are integrated). The compromise is thermal: less space for fans, often only 2-3 positions, and tighter clearances.

**SFF (Small Form Factor).** This is a discipline in itself, almost a sport. Below 15 liters, every millimeter is contested: the graphics card must be short or "2.5 slot," the CPU cooler is often a low-profile 47-67 mm tall, the power supply is an **SFX** or **SFX-L** (compact format, 100 × 63.5 mm versus 150 × 86 mm for standard ATX) which costs 40-60% more than an ATX of equal power, cables must be shortened or replaced with custom sets. The result can be splendid — a 12-liter system with full desktop performance — but the journey is expensive, requires in-depth research, and does not forgive planning errors. **It is the wrong format for a first build.** Industry reference chassis include the Fractal Design Terra and Ridge, the Cooler Master NR200P (the most accessible and "forgiving"), the Lian Li A4-H2O and Q58, and the boutique scene (SSUPD, Formd, Dan Cases) **[volatile data]**.

### 2.3 Which format for which need

- **Office PC / HTPC / browsing**: mATX or ITX. Low heat, low space, low cost.
- **Entry/mid-range gaming PC (single GPU, air cooler or AIO 240)**: mid-tower mesh. There is no rational alternative.
- **High-end gaming PC (top-tier GPU, AIO 360, many fans)**: quality mid-tower or dual-chamber like Lian Li O11. Note: dual-chamber cases often **lack front intake** and must be configured with air intake from the bottom and/or side, as we will see.
- **AI/ML or rendering Workstation (multiple GPUs, many PCIe lanes, lots of RAM)**: full tower, and here the constraint becomes **the width to accommodate two 3-slot graphics cards with air space in between**, in addition to the PSU capacity. It must be clearly stated that two flagship GPUs side-by-side in a consumer case are a thermally problematic configuration: the upper card draws hot air from the lower card. Solutions include blower models (rare in consumer), liquid cooling, or chassis with increased slot spacing.
- **NAS / home server**: specific chassis with cages for 6-12 3.5" drives, medium-flow fans in front of the cages, or rack formats if you have a cabinet.

---

## 3. Compatibility and internal space: the checklist that saves your purchase

This is the most important operational section of the chapter. All the numbers listed here are published in the case specifications; the problem is that almost no one reads them **before** ordering. Take a sheet (or a file) and fill in these fields for your configuration.

### 3.1 Maximum GPU length

This is the measurement, in millimeters, from the rear bracket to the front end of the graphics card. The case declares a "**GPU clearance**" or "max VGA length". **Operational rule: take the actual length of the specific model you buy — not the chip, the model.** An ASUS RTX 5070 can be 305 mm long, and the same RTX 5070 from another manufacturer can be 240 mm. The length is a characteristic of the cooler, not the GPU.

Beware of three pitfalls:

1. **The value declared by the case is often "with front fans not installed"**. Many chassis declare two numbers: for example, "400 mm, 360 mm with front radiator". If you mount a radiator or thick fans in front, you lose 25-55 mm.
2. **Power cables.** The **12V-2×6** connector (the evolution of 12VHPWR, the 16-pin connector of modern high-power cards) exits from the top side of the card and requires a minimum bending radius: the standard requires not to bend the cable within 35 mm of the connector. If the side panel is 15 mm from the card, the cable will be crushed — and this is exactly the mechanism that caused connector melting incidents. Some cards have the connector rotated or positioned differently precisely for this reason. **Check the width clearance, not just the length.**
3. **Slot thickness.** Modern cards occupy 2, 2.5, 3, or even 4 slots in height. A case with 7 PCIe slots physically accommodates any card, but if you have an expansion card (a sound card, a 10 Gbit adapter, a capture card), you might find that the GPU covers its slot.

### 3.2 Maximum CPU cooler height

Measurement in millimeters from the motherboard surface to the top of the cooler. Typical values:

| Cooler Category | Height | Required Case |
|---|---|---|
| Low-profile (Noctua NH-L9, Thermalright AXP) | 37-67 mm | SFF, HTPC |
| Compact single tower | 120-140 mm | mATX, mid tower |
| Standard single tower (e.g., Peerless Assassin, NH-U12A) | 155-160 mm | mid tower (almost all) |
| Dual tower (Noctua NH-D15, Thermalright FA/PA) | 160-168 mm | wide mid tower |
| "Large" dual tower | 170-175 mm | explicitly verify |

The rule: **leave at least 5 mm of clearance** compared to the value declared by the case. Manufacturing tolerances, gasket thickness, stand-off height, and the bulging of the glass side panel can eat up those few millimeters. A cooler that touches the side panel not only won't close: it transmits vibrations to the glass and turns the entire side of the case into a speaker.

### 3.3 Radiators: dimensions, positions, thicknesses

The **radiator** is the heat exchanger of a liquid cooling system (**AIO**, All-In-One, closed and pre-filled systems, or custom loops). It is measured by the number and size of the fans it accommodates:

| Abbreviation | Fans | Approximate Length | Approximate Cooling Capacity |
|---|---|---|---|
| 120 | 1 × 120 mm | ~155 mm | poor, not recommended |
| 240 | 2 × 120 mm | ~275 mm | CPUs up to ~180 W |
| 280 | 2 × 140 mm | ~315 mm | ~15-20% better than a 240 |
| 360 | 3 × 120 mm | ~395 mm | high-end CPUs (up to 250-300 W) |
| 420 | 3 × 140 mm | ~455 mm | maximum consumer, large cases |

Possible **mounting positions** and their implications:

- **Top (upper), exhaust.** This is the canonical position for an AIO. Hot air rises, the radiator expels it upwards, the pump remains lower than the highest point of the circuit (important: **the pump should never be the highest point**, otherwise the air that inevitably accumulates in the circuit collects in the pump and produces the classic gurgling sound and premature wear). The compromise: the radiator slightly heats the air that... exits, so no problem; however, it takes up space from the VRMs (Voltage Regulator Module, the CPU power delivery stage on the motherboard) and can interfere with the VRM heatsinks themselves or with tall RAM modules. **Check the "CPU cooler to top" clearance of the case and the radiator + fan thickness** (typically 27 + 25 = 52 mm, but 38 and 45 mm thick radiators exist).
- **Front (front), intake.** Maximum performance for the CPU, because the radiator receives fresh air from outside. The cost is that all the air entering the case has been preheated by the radiator (by 3-8 °C under load), and this penalizes the GPU. It is a legitimate choice if the priority is the CPU. It reduces the available GPU length.
- **Side / bottom.** Typical of dual-chamber cases. To be evaluated on a case-by-case basis.

One last honest note: **for the vast majority of consumer CPUs, a good air cooler for 40-60 euros (Thermalright Peerless Assassin, Phantom Spirit, DeepCool AK620) is within 2-4 °C of a 130 euro AIO 360**, it doesn't have a pump that can fail, it doesn't have liquid that can evaporate over time, and it doesn't have a single point of failure. An AIO makes sense for aesthetics, to free up space around the socket, for truly extreme CPUs, or when the case height does not allow for a tower. Don't buy it thinking it's automatically superior.

### 3.4 Drive Bays

We distinguish three families:

- **3.5" (mechanical HDDs).** These require a drive cage with trays, ideally cushioned (anti-vibration grommets). The trend in recent years is to reduce their number: many modern cases offer 2, some "airflow-first" only 1, some SFF zero. If you have an archive of mechanical drives, **count them first**.
- **2.5" (SATA SSDs and old laptop HDDs).** These take up little space and mount on trays behind the motherboard tray or on the PSU shroud. Typically 2-4 positions.
- **M.2 (NVMe SSDs).** These do not concern the case: they mount directly on the motherboard. This is why modern cases have fewer and fewer bays — most new systems have no physical drives other than NVMe.

It's worth noting that front 3.5" drive cages, when present, **are an obstacle to airflow**: they are placed exactly behind the intake fans. Modern cases make them removable precisely for this reason. If you don't need them, remove them.

### 3.5 Power Supply: position, length, shroud

Virtually all modern cases mount the power supply unit (**PSU**) **at the bottom**, with its fan facing downwards and a dust filter underneath. This thermally isolates the PSU from the rest of the system: it draws cool air from beneath the case and exhausts directly out the back, without exchanging heat with the internal air. This is a huge improvement over 2000s cases, where the top-mounted PSU drew hot air from the CPU.

Two practical implications:

1. **The case must have sufficiently tall feet** (at least 20-25 mm) for the PSU to breathe. On a long-pile carpet, even 25 mm might not be enough: the carpet obstructs it and the PSU heats up. **Do not place the PC on a carpet.**
2. **The maximum PSU length** is stated by the case (typically 160-200 mm). Standard ATX PSUs are 140-160 mm deep; high-power ones (1200 W+) can reach 180-200 mm. If the case states 180 mm "without front drive cage" and you want the cage, the actual number decreases.

The **PSU shroud** (or "basement cover") is the horizontal sheet metal cover that hides the power supply and cables. It's aesthetic but also functional: it creates a separate compartment for the tangle of cables. Some cases have a **perforated** shroud, which allows fans to be mounted above it; others have a solid one, which can be a problem in a specific case: if you have a low front intake fan and the shroud separates it from the rest, that air won't reach the GPU.

### 3.6 Cable management

The space behind the motherboard tray is where all the cables run: the 24-pin ATX, the 8-pin EPS CPU cables that need to go up to the top left, the PCIe/12V-2×6 GPU cables, SATA, fan cables, and front panel cables.

- **Space behind the tray**: under 20 mm is tight, 25 mm is acceptable, 30+ mm is comfortable. This is where budget cases cut corners.
- **Rubber grommets**: rubber rings in the tray holes that hide edges and hold cables in place. Their absence is not a functional defect, but an aesthetic and practical one.
- **Channels and Velcro straps**: well-designed modern cases (Lian Li, Fractal, Phanteks) integrate channels with snap-on covers and pre-mounted Velcro straps. They make a real difference in assembly time.
- Tidy cable management **does not dramatically improve temperatures** in a modern case (most cables are behind the tray, out of the airflow), but a bundle of cables left hanging in front of the intake fans certainly worsens them. And most importantly: the PC will need to be reopened, and finding it organized is a blessing.

### 3.7 Vertical GPU mounting and PCIe risers

Vertical mounting — the graphics card rotated 90° and facing the glass panel — is purely aesthetic and requires two things: a vertical bracket (sometimes included, often bought separately) and a **PCIe riser cable**, which is a flexible extension that carries the signal from the motherboard slot to the rotated graphics card.

There are two serious caveats, and they should be taken seriously:

**1. The PCIe generation of the riser must match.** Risers are sold as "PCIe 3.0", "PCIe 4.0", or "PCIe 5.0". The PCIe bus doubles its signaling frequency with each generation, and signal integrity over a flexible cable becomes progressively more difficult. **A PCIe 3.0 riser used with a PCIe 4.0/5.0 card causes transmission errors**, which manifest as crashes, artifacts, black screens, or — more insidiously — as an automatic downgrade of the link to a lower speed that costs you performance without telling you. Always check in BIOS/UEFI or with GPU-Z that the link is negotiated at the expected generation and width (x16). A quality PCIe 5.0 riser costs 60-100 euros **[volatile data]**; if the budget doesn't allow it, do not mount vertically.

**2. Vertical mounting suffocates the card.** Rotated against the side panel, the GPU is 15-30 mm from the glass, and its fans draw air from there. In many cases, this means +8/+15 °C compared to horizontal mounting. Cases designed for vertical mounting (some Lian Li, Hyte Y60/Y70) leave more space or use the side panel as an air intake. **If your case is not designed for this, vertical mounting is a thermal downgrade paid at a high price.**

---

## 4. Airflow: the serious discussion

### 4.1 Mesh vs. glass: how much does it really matter

The most frequent question and the one with the clearest answer. The **front panel** is the point from which, in most layouts, fresh air enters. If the front is a tempered glass or solid acrylic panel, air does not pass **through** it: it must squeeze into the side (typically 10-20 mm on each side) or top gaps. Intake fans, which are designed to work with low static pressure, encounter an obstacle and see their actual flow rate plummet: anechoic chamber tests show flow reductions in the order of 40-70% compared to the same fan in free air.

The effect on components, in comparative tests between the mesh variant and the glass variant **of the exact same case** (an experiment performed multiple times by specialized press, for example on the old NZXT H510 vs H510 Flow, or on the Corsair 4000D vs 4000D Airflow), is typically:

- **GPU: 8 to 15 °C higher** with a closed front panel, resulting in a drop in sustained boost;
- **CPU: 3 to 8 °C higher**, less dramatic because the CPU cooler is higher up and closer to the rear exhaust fan;
- **Noise: equal or higher** with a closed front panel, because the fans have to spin faster and because air being choked in the gaps generates turbulence.

The operational conclusion is brutal and admits no nuances: **the front panel must be mesh, or at least have a significant and direct open surface in front of the fans.** Glass on the front is a design flaw disguised as an aesthetic feature. The **side** glass, however, is almost always harmless, because in the vast majority of layouts, the side is not a primary airflow path.

A small technical note to avoid naivety: not all "mesh" is created equal. A grid with small, dense round holes and a lot of material between holes can have an **open area** of 40%, while a well-designed honeycomb mesh can reach 65-75%. And perforated panels with decorative "tight-knit" patterns for aesthetics are often worse than they seem. If a review mentions "restrictive mesh," it's a warning.

### 4.2 Positive, Negative, Neutral Pressure

Let's define precisely. In a steady state, the mass of air entering the case must equal the mass exiting — this is conservation of mass, there's no choice. What changes is **where** it exits.

- **Positive pressure**: The airflow of intake fans is greater than that of exhaust fans. The excess air exits through gaps, bracket holes, and crevices. The case is "inflated."
- **Negative pressure**: Exhaust airflow exceeds intake. The missing air is sucked in through every gap, every unfiltered hole, the back of the case, the PCIe bracket bay.
- **Neutral pressure**: Essentially in equilibrium.

Why do we care? **For dust.** In positive pressure, all incoming air passes through the intake fans, and these are the only point where **dust filters** exist. Filtered air enters, and the air exiting through the gaps pushes out dust particles. The case remains reasonably clean, and dust accumulates on the filters, where it can be removed in thirty seconds.

In negative pressure, air enters everywhere, and "everywhere" is unfiltered. Dust settles on heatsinks, fins, cards — where cleaning is much more laborious.

**Recommendation: slight positive pressure.** In practice, this means having an intake airflow 10-30% higher than the exhaust. Be careful not to confuse the number of fans with airflow, however: three intake fans behind a dense filter can move less air than two exhaust fans in open air. The filter is resistance, and resistance reduces airflow.

It must also be said honestly, because it's treated like a religion online: **the thermal difference between the three configurations is 1-3 °C**, which is almost irrelevant. It's maintenance, not temperature, that's the serious argument for positive pressure. A well-ventilated negative pressure case is cooler than a poorly-ventilated positive pressure one. The priority remains the **volume of air exchanged**, not the sign of the differential.

### 4.3 Airflow Path

Two principles guide the layout:

**Principle 1: Hot air rises.** Due to natural convection, heated air expands, decreases in density, and rises. This effect, by itself, is weak compared to forced ventilation, but it gives the correct direction: **intake low and front, exhaust high and rear** means working with physics rather than against it.

**Principle 2: The path must be short and direct.** Fresh air must reach the heatsinks (GPU at the bottom, CPU in the middle-top) without mixing with already used hot air.

The canonical layout, valid for 90% of mid towers:

```
                    ┌── OUT ──┐  ┌── OUT ──┐
      ┌─────────────┴─────────┴──┴─────────┴──────┐
      │  TOP fans (exhaust)                       │
      │                                    ┌──────┤
   ─► │  ┌────┐   [ CPU Cooler ]           │ REAR │ ──►  OUT
 IN   │  │FRON│         ═══════►           │ (out)│
   ─► │  │TAL │                            └──────┤
 IN   │  │mesh│    [ GRAPHICS CARD ]             │
   ─► │  └────┘    ▼▼▼ GPU fans ▼▼▼            │
 IN   │  ══════════════════════════════════       │
      │  ┌──── PSU shroud ──────────────────┐     │
      │  │  [ POWER SUPPLY ]                │     │
      └──┴─────────▲───────────────────────┴──────┘
                   │  IN (air from below, filtered)
```

- **Front intake (2-3 fans)**: fresh air directly hitting the graphics card, which is the component that dissipates the most heat.
- **Bottom intake (optional, 1-2 fans)**: if the case allows, fans on the bottom in front of the GPU are extremely effective, as they blow fresh air directly into the graphics card fans. It's one of the most underrated and most effective configurations overall. Requires high feet and a filter.
- **Rear exhaust (1 fan, 120 or 140 mm)**: immediately behind the CPU cooler, expelling the air that has just passed through it before it recirculates.
- **Top exhaust (1-3 fans)**: collects rising hot air. If you mount an AIO radiator, this is its natural location.

Some counterintuitive but verified notes:

- **Do not put intake fans at the top.** They fight against convection and recycle hot air from the AIO.
- **The glass side panel is not a thermal problem** as long as the front is open. Those who want a glass side can have it without guilt.
- **In a dual-chamber case like the Lian Li O11**, the front is often closed, and the air intakes are **the bottom and the side**. These cases work very well, but **only if you configure them as designed**: intake from bottom + side, exhaust from top + rear. Those who mount all fans at the top for exhaust and no air intake create a vacuum and get terrible temperatures.

### 4.4 Typical configurations by number of fans

| Fans | Configuration | Notes |
|---|---|---|
| 2 | 1 front IN + 1 rear OUT | The bare minimum. Only acceptable for low-power systems (< 250 W total). |
| 3 | 2 front IN + 1 rear OUT | Sensible basic configuration. Slight positive pressure. Most common in entry-level cases. |
| 4 | 3 front IN + 1 rear OUT | Excellent compromise. Still positive pressure. Standard for mid-range "airflow" cases. |
| 5 | 3 front IN + 1 rear OUT + 1 top OUT | Balanced. Good for high-end air-cooled systems. |
| 6 | 3 front IN + 1 rear OUT + 2 top OUT | Balanced/neutral. Typical configuration with a 240/280 AIO at the top. |
| 7+ | 3 front IN + 2/3 bottom IN + 1 rear OUT + 3 top OUT | Enthusiast configuration. Marked positive pressure, excellent GPU temperatures. |

**Marginal returns decrease rapidly.** The jump from 2 to 4 fans is worth many degrees. The jump from 6 to 9 is worth 1-2 °C and adds noise. The fourth fan is an excellent investment; the ninth is a decoration.

### 4.5 Dust filters

A filter is a fine mesh (nylon or steel) placed in front of air intakes. It does two things: it traps dust and **resists airflow**. Resistance costs flow, typically 10-20% with a clean filter and much more with a dirty filter.

Where they are really needed:

- **Under the PSU**: mandatory, and must be **removable from the back or side**, not from underneath (otherwise you have to lift the PC every time).
- **Front (in front of intake fans)**: yes, and it's the main defense.
- **Bottom (if there are intake fans)**: yes.
- **Top (if fans are exhausting)**: **no, or only magnetic and removable**. A filter on an exhaust surface filters nothing — air exits — and only adds resistance. It only serves to prevent dust from settling inside **when the PC is off**. If your case has a magnetic filter on top and the PC is almost always on, you can remove it and gain a few percentage points of exhaust.

**Maintenance**: filters should be cleaned every 1-3 months depending on the environment (case on the floor + pets + smoke = every month). Compressed air or a simple rinse, with the precaution of **drying them completely** before reassembling.

---

## 5. Case fans

### 5.1 Sizes: 120, 140, 200 mm

A fan is, physically, a propeller that moves a volume of air. For the same **airflow** (volume of air per second), a larger fan can rotate more slowly, because each rotation moves more air. And since aerodynamic noise depends strongly on the **peripheral speed of the blades**, rotating more slowly means making less noise.

This is why, **for the same airflow, a 140 mm fan is quieter than a 120 mm fan, and a 200 mm fan is quieter than both.** It's a simple and reliable principle.

However, there are practical counter-arguments:

- **140mm fans have fewer mounting positions.** Many cases accept 3 x 120mm but only 2 x 140mm on the front, and in that case, **3 x 120mm move more air than 2 x 140mm**, negating the advantage.
- **200mm fans have a very limited model selection**, rotate slowly (600-800 RPM), have **very low static pressure**, and are therefore unusable on radiators or behind dense filters. They are excellent for free intake in cases designed for them, but they are a niche.
- **140mm radiators** (280, 420) are slightly more efficient than their 120mm counterparts (240, 360) for the same length, but are supported by fewer cases.

**Rule of thumb: use 140mm fans when the case accepts the same number as 120mm fans; otherwise, use 120mm fans and be happy.**

### 5.2 Specifications: reading them without being misled

Every fan has a spec sheet with four key numbers. They should be read together, never in isolation.

**CFM (Cubic Feet per Minute).** This is the **airflow**, meaning how much air the fan moves *under zero resistance conditions*, in free air. Typical values: 50-80 CFM for a 120mm fan, 60-100 for a 140mm fan. In Europe, it can also be expressed in m³/h (1 CFM ≈ 1.7 m³/h). **The stated number is the theoretical maximum and you will never see it in reality**, because there is always resistance inside the case.

**Static pressure (mmH₂O, millimeters of water column).** This is the fan's ability to **push air against resistance**. Typical values: 1.0-1.5 mmH₂O for an "airflow" fan, 2.0-3.5 mmH₂O for a high-speed "static pressure" fan. This is the parameter that matters when the fan needs to push air through:
- the dense fins of a **radiator**;
- the fins of an **air cooler**;
- a dense **dust filter**;
- a restrictive front grille.

**When to prioritize one or the other**: a fan in **free intake** or **free exhaust** (in front of a hole, essentially) benefits from airflow. A fan on a **radiator or cooler** benefits from static pressure. A fan in front of a filter is somewhere in between. In the reality of 2026, the best "hybrid" fans (Arctic P12/P14, Noctua NF-A12x25, Phanteks T30) are good in both regimes, and the distinction is less dramatic than it used to be — however, it is still true that a fan explicitly declared "high airflow" with thin, wide blades (like Noctua NF-S12, or the old "airflow" Corsair AF) performs poorly on a radiator.

**RPM (Revolutions Per Minute).** The rotational speed. Together with the diameter, it determines the peripheral speed and thus the noise. A fan with a 200-2000 RPM range is much more versatile than one with an 800-1200 range: the former can be made almost inaudible at low loads and pushed to maximum under stress.

**dBA (decibel A-weighted).** Noise, measured with a weighting that reflects the sensitivity of the human ear. **This is the least reliable number on the spec sheet**, because measurement conditions (distance, anechoic chamber, load) are not standardized among manufacturers, and because dBA does not capture the **quality** of the noise. A 30 dBA fan with a low-frequency hum is more tolerable than a 28 dBA fan with a high-pitched whine or bearing tick. **Stated dBA values are only useful for comparing fans from the same manufacturer.** For cross-brand comparisons, trust independent measurements that normalize noise (e.g., "at 35 dBA, how many CFM does it move?", which is the correct way to compare).

One last pitfall: **PQ** (pressure-flow) curves. A serious manufacturer publishes a graph showing how much airflow the fan delivers at each level of backpressure. This is the only complete data. Noctua, Arctic, and Phanteks do this; many others do not.

### 5.3 PWM (4 pin) vs. DC (3 pin)

The connector for system fans is **standardized**:

| Pin | Function |
|---|---|
| 1 | GND (Ground) |
| 2 | +12 V |
| 3 | Sense / Tachometer (reads RPM) |
| 4 | **PWM** (control signal) |

A **3-pin fan** is regulated by **DC (Direct Current)**: the motherboard lowers the supply voltage, from 12 V downwards, and the fan slows down. This is simple, but has a limitation: below a certain voltage (typically 5-6 V), the motor does not have enough torque to start or maintain rotation, so the minimum speed is relatively high (often not dropping below 40-50% of nominal speed).

A **4-pin fan** uses **PWM** (Pulse Width Modulation): the voltage always remains at 12 V, but a square wave signal (25 kHz for the Intel standard) rapidly switches the motor's drive circuit on and off. By varying the **duty cycle** (the percentage of time the signal is high), the speed is controlled. The advantage is enormous: **starting torque remains full**, so it can go down to very low speeds (10-20%, i.e., 200-300 RPM) with reliable startup, and control is much more precise and linear.

**Recommendation: buy PWM fans.** The extra cost is only a few euros and is money well spent. If your motherboard has a 4-pin header (all modern ones do), you can still set it to DC mode to drive 3-pin fans — most BIOS/UEFI allow this, and some automatically detect the type.

**Control from BIOS/UEFI.** Every modern motherboard has a section (Q-Fan on ASUS, Smart Fan on Gigabyte/MSI, Fan Xpert, etc.) where you define a **fan curve**: a function that links fan speed to a reference temperature. Three non-trivial tips:

1. **Choose the right temperature source.** If you tie your case fans to the **CPU package** temperature, you'll have fans that constantly speed up and slow down, because the temperature of a modern CPU fluctuates by 20 °C in half a second with every micro-load. The result is the infamous **fan surging**, the PC's "panting," and it's much more annoying than a constant noise. Instead, tie them to the **motherboard / VRM / chipset** temperature (a large thermal mass that changes slowly) or, if the BIOS allows, to the GPU.
2. **Use "smoothing" / "step up-down time"**: almost all BIOSes allow you to set a delay (in seconds) before the fan reacts to a change. Set it to 3-6 seconds. This eliminates surging almost completely.
3. **Don't aim for zero-RPM on the case.** Some fans stop completely below a certain threshold. This is fine for the GPU; for case fans, a permanent minimum flow at 400-500 RPM is inaudible and keeps the air moving.

**Hubs and daisy-chains.** Motherboards typically have 4-7 fan headers, and a well-populated case can have 8-9. Solutions:

- **Passive hub / splitter**: a splitter that connects multiple fans to a single header. Caution: **each header has a current limit**, typically 1 A (12 W). A PWM fan consumes 0.1-0.3 A, so 3-4 fans per header is the prudent limit. Also, only **one** fan can report the tachometer signal; the others are ignored (so the BIOS will show only one RPM value).
- **Active hub**: powered directly from the PSU (with a SATA or Molex connector) and only receives the PWM signal from the motherboard. It can handle 6-10 fans without problems. This is the correct solution for a system with many fans, and many mid/high-end cases include one.
- **Daisy-chain**: some fan series (Lian Li Uni Fan, Corsair iCUE Link, Phanteks D30) physically clip to each other and pass power and data through contacts, eliminating almost all cables. This is a very elegant solution for cable management and is one of the reasons these products are expensive. The downside: **they tie you to a proprietary ecosystem**, with a dedicated controller and dedicated software.

### 5.4 Bearings

The bearing is what supports the rotating shaft. It determines **mechanical noise, lifespan, and allowed mounting orientation**.

| Type | Description | Typical Lifespan (MTBF) | Noise | Notes |
|---|---|---|---|---|
| **Sleeve** | Oil-lubricated cylindrical bushing | 20,000-30,000 h | Quiet when new | Lubricant leaks if mounted **horizontally**; wears out and becomes noisy. Inexpensive. |
| **Ball bearing** | Caged balls | 50,000-75,000 h | Louder (humming/ticking) | Durable, tolerates heat and any orientation. Used in server environments. |
| **FDB** (Fluid Dynamic Bearing) | Sleeve with hydrodynamic pressure oil film and sealing | 60,000-150,000 h | Very quiet | **The best compromise.** De facto standard in quality fans. Commercial names: SSO2 (Noctua), Rifle, Hydro, HDB. |
| **Maglev / Magnetic levitation** | Shaft supported by magnetic field, almost no friction | 100,000+ h | Extremely quiet | Expensive, heavy marketing, real but marginal benefits compared to a good FDB. |

**Purchase rule: look for FDB (or commercial equivalents) and ignore everything else.** Sleeve bearings are only acceptable in pre-installed fans in budget cases and only if mounted vertically. Maglev is a luxury.

One last honest observation: stated MTBF (Mean Time Between Failures) durations—150,000 hours is 17 years—are statistical extrapolations from accelerated tests, not promises. They are for comparison, not for planning.

### 5.5 Fan Brands

| Brand | Reference Models | Indicative Price (each) | Positioning |
|---|---|---|---|
| **Arctic** | P12 / P14 (PWM PST), P12 Max | 6-13 € | **The absolute king of price/performance.** Performance 3-5% from the top, one-fifth the price. Spartan aesthetics, slightly stiff cable. If budget matters, this is the answer. |
| **Noctua** | NF-A12x25 (the reference), NF-A14, NF-P12 redux | 20-35 € | The technical gold standard. Minimal tolerances (0.5 mm blade clearance), excellent static pressure and airflow, exemplary quietness, 6-year warranty. Divisive brown-beige color (black "chromax" line exists, which costs more). |
| **Phanteks** | T30-120 | 25-35 € | 30 mm thick fan, three speed modes, off-the-charts performance on radiators. Non-standard footprint. |
| **be quiet!** | Silent Wings 4 / Pro 4 | 20-30 € | Focused on silence. Excellent, a step below Noctua in static pressure. |
| **Lian Li** | Uni Fan SL/TL/SL-Infinity | 20-30 € (kit of 3: 60-90 €) | Daisy-chain, spectacular RGB, good performance. Closed ecosystem. |
| **Corsair** | RS/QX, iCUE Link LX/RX | 20-35 € | Good, but the value is in the iCUE software and the Link ecosystem. Expensive. |
| **Thermalright** | TL-C12 / TL-B12 | 5-9 € | Very cheap Chinese alternative, surprisingly good. Variable build quality. |

**[volatile data: prices change frequently and models are refreshed every 18-24 months.]**

**When it makes sense to spend.** The jump from a generic pre-installed fan to an Arctic P12 is worth a lot: better temperatures, lower noise, 8 euros. The jump from an Arctic P12 to a Noctua NF-A12x25 is worth an extra 25 euros for **2-4 °C and a couple of dB**. It's worth it if: (a) the PC is half a meter from your head in a quiet room; (b) the fan is on a radiator or cooler, where Noctua's superiority in static pressure is expressed; (c) you have already optimized everything else. **It's not worth it** if you still have a gasping GPU in a closed case: fix the case first.

---

## 6. Case Brands

There is no "absolute best": there are companies with different philosophies and levels of refinement. Here's the overview, with the caveat that **specific models age quickly** and need to be verified **[volatile data]**.

**Lian Li.** Historically a manufacturer of premium aluminum, today the strongest name in the enthusiast segment thanks to the **O11** family (O11 Dynamic, O11 Dynamic EVO, EVO XL, and derivatives). These are dual-chamber cases: the main chamber houses the motherboard and GPU, while the side chamber conceals the PSU and cables. They offer enormous radiator support (up to three 360mm radiators). Excellent construction, superb ergonomics. **Note: the front panel is closed.** They should be configured with intake from the bottom and side. The O11 Vision and the "aquarium" versions with three glass panels require even more care. The **Lancool** line (203/216/207) is also excellent; it's their airflow-first offering at much more contained prices and often provides the best value for money in the entire market.

**Fractal Design.** The Swedish company with the best reputation for design and finish. Three lines to know: **Meshify** (airflow-first, angular mesh front, the archetype of a well-ventilated case), **North** (Scandinavian aesthetic with a walnut wood front — and, notably, with a real mesh behind the wood, making it beautiful *and* functional), **Torrent** (the extreme airflow case, with two enormous 180mm front fans, reference thermal performance, and a very particular layout). Also, the **Define** line for those who still want sound dampening. Construction, tolerances, and accessories are always top-notch. Mid-to-high prices.

**be quiet!** German company. Historically focused on silence (the **Silent Base**, **Dark Base** lines), but with a decisive shift towards airflow with the **Pure Base 500DX / 500FX** and **Shadow Base** lines. Included fans are of excellent quality (their fans are a real strong point). Solid construction, sober and "adult" aesthetic.

**Corsair.** Wide range of products, from the **4000D Airflow** (for years the default choice for an affordable and well-ventilated mid-tower) to the **iCUE** series with its integrated RGB ecosystem and sensors. Powerful but heavy iCUE software. Solid quality, medium prices. The "Airflow" version of each model is the one to buy; the plain version with a closed front panel is not.

**NZXT.** Very recognizable minimalist aesthetic (the **H** line). Historically, they got airflow wrong (the H510 with its closed front panel is a textbook example of this error) and then corrected it with the **Flow** versions. Good construction, prices a bit high for what they offer, excellent internal ergonomics. If you buy NZXT, buy a Flow model.

**Phanteks.** Highly regarded by enthusiasts. **Eclipse** lines (P400/P500 and derivatives) and **NV** (dual-chamber glass cases). Excellent internal engineering, many intelligent solutions for cable management, often generous fan configurations.

**Cooler Master.** Very wide range, variable quality. The **NR200 / NR200P** is the benchmark in accessible SFF. The older **MasterBox** series was the budget choice for years. Check model by model: qualitative consistency is not their strong suit.

**Montech, DeepCool, Antec, Thermaltake.** The aggressive budget segment. **Montech** in particular (AIR, King, XR series) has earned a notable reputation by offering cases with 3-6 ARGB fans included at prices ranging from 60-90 euros, with honest construction. **DeepCool** (CH and Morpheus series) is similar. They are the rational choice for those on a tight budget: **you accept thinner sheet metal and less refined finishing in exchange for excellent airflow and included fans.** This is a much smarter compromise than buying a "premium" closed-off case.

### 6.1 What distinguishes a well-designed case

Beyond the brand, here are the concrete signs of quality, which you can verify in a video review:

1. **Sheet metal thickness** (0.8-1.0 mm in well-made cases, 0.55-0.6 mm in budget ones). Thin sheet metal flexes, vibrates, and resonates.
2. **Rolled edges**, no sharp cuts.
3. **Tolerances**: panels fit together, screws go in without forcing, glass doesn't "rub."
4. **Assembly ergonomics**: cable channels, pre-installed Velcro straps, tie-down points, included fan hub, removable PSU bracket, removable filters without disassembling anything.
5. **Fan provision**: how many are included and of what quality. A 90-euro case with 4 decent PWM fans costs less than an 80-euro case with zero fans (to which 40 euros for Arctic P12s must be added).
6. **Front I/O**: number and type of USB ports. In 2026, demand at least **one USB-C** (ideally USB 3.2 Gen 2 at 10 Gbps or higher) and verify that the motherboard has the corresponding internal header — it's a classic mistake to buy a case with front USB-C and a cheap motherboard that doesn't have the header to connect it.
7. **Documentation and spare parts**: serious manufacturers sell spare parts (screws, filters, panels, feet). Fractal, Lian Li, be quiet! do.

---

## 7. How to choose: the procedure

Follow this order. **The order is the point:** reversing it is the cause of almost all bad purchases.

**Step 1 — Motherboard format.** You have already chosen the motherboard (or at least its format). This defines the minimum: ITX → everything; mATX → mid/mini tower; ATX → mid tower or larger; E-ATX → check the **width in millimeters**, not the acronym.

**Step 2 — Clearance.** Write down the three numbers of your configuration and compare them with the case's technical specifications, with a margin:
- **GPU** length (specific model) + 15 mm margin for cables;
- **CPU cooler** height + 5 mm;
- **radiator** size and thickness (if AIO) and its position;
- **PSU** length;
- number of **3.5" drives**.

**Step 3 — Airflow.** The front **must** be mesh or otherwise have a direct airflow path. If it isn't, the case is excluded — unless it's a dual-chamber designed to draw air from the bottom and side, in which case it's fine but you need to configure it correctly. Check how many fan positions there are and where.

**Step 4 — Quality and features.** How many fans are included? What type (PWM or 3-pin)? Is there a hub? What is the thickness of the sheet metal? How much space is behind the tray? **Calculate the real total cost**: case price + necessary additional fans.

**Step 5 — Aesthetics.** Now, and only now, choose the one you like best from those that survived steps 1-4.

**Step 6 — Budget.** If your preferred choice exceeds the budget, go back to step 5 and pick the second one. Do not go back to step 3.

### 7.1 Typical buyer mistakes

**1. The "aquarium" case.** Three glass panels, RGB lighting, zero air vents. It looks beautiful in photos and bakes components. If you still want one, know that you're paying 10-15 °C on the GPU, and at least configure it with robust exhaust and intake fans from the bottom, if possible.

**2. Buying the case before the GPU.** The graphics card is the bulkiest and most variable component. Choose it first, or at least decide on the tier and get a case with generous clearance (350+ mm).

**3. Trusting the chip's designation instead of the model.** "Will an RTX 5080 fit?" is not a question with a single answer. The right question is: "Will this specific RTX 5080, which is 336 mm long and 3.2 slots thick, fit?"

**4. Cases without included fans.** Some cases, especially "premium minimalist" ones, are sold with zero or only one fan. The price seems competitive until you add €40-70 for fans. **Count the total cost.**

**5. Ignoring panel noise.** A thin side panel that vibrates at a certain frequency can make an otherwise silent system unbearable. This defect doesn't appear in any spec sheet: you only discover it in reviews.

**6. Putting all fans on exhaust.** "The more air goes out, the cooler it is" is false reasoning. The case becomes depressurized, air enters from every crevice (unfiltered), and the actual airflow drops because the fans are working against enormous resistance. You need a path: **air enters from somewhere, exits from somewhere else.**

**7. Mounting fans backward.** Every fan has a direction. Airflow goes **from the side with the motor support grille towards the side with the blades** — or, more simply: look at the **arrows printed on the fan frame** (one indicates airflow, the other rotation direction). In the absence of arrows: air exits from the side where the four/seven struts supporting the hub are located.

**8. The PC on the floor on carpet.** Obstructs the PSU's air intake and turns the bottom into a vacuum cleaner. At least a hard surface, preferably raised.

**9. Choosing SFF as a first build.** This leads to an expensive dead end.

**10. Neglecting the USB-C header.** Verify that the motherboard has the internal "USB 3.2 Gen 2 Type-C" connector (20-1 pin) if the case has a front USB-C. Otherwise, that port will remain a decorative hole.

### 7.2 Budget-tier recommendations

**[Volatile data: prices and models. Verify before purchase.]**

**Entry (€50-90).** The goal is to maximize airflow and included fans, accepting thin sheet metal and simple finishes. Look at **Montech** (AIR / XR series), **DeepCool** (CH series), **Cooler Master** (MasterBox), **Lian Li Lancool 207/216** if the price drops into this range. Look for: mesh front, 3-4 included fans (ideally PWM), GPU support ≥ 330 mm, cooler ≥ 160 mm. Do not look for: glass on three sides, elaborate RGB.

**Mid (€90-160).** This is the tier with the best value-for-money in the entire market. **Fractal Design North / Pop Air / Meshify**, **Lian Li Lancool 216/207**, **Corsair 4000D Airflow**, **be quiet! Pure Base 500DX**, **Phanteks Eclipse**, **NZXT H7 Flow**. Expect: decent sheet metal, excellent ergonomics, 3-4 quality fans included, 360 AIO support, neat cable management, front USB-C. **If you have no special needs, your case is here.**

**High-end (160-300+ €).** You pay for build quality, aluminum, modularity, fan ecosystems, multi-radiator support. **Lian Li O11 Dynamic EVO / EVO XL**, **Fractal Design Torrent** (the temperature champion) or **Define/Meshify 2 XL**, **be quiet! Dark Base**, **Hyte Y70**. Buy for a specific reason: custom loop, dual 360mm radiator, well-designed vertical GPU, strong aesthetic requirement. **Do not buy it expecting drastically better temperatures than a good 120 euro case: the difference exists but is small.**

**Note on SFF.** In this format, there is no sensible entry-level. The serious entry ticket starts from the Cooler Master NR200P (~100 €) and rises quickly, but it must be added to the premium for an SFX power supply (+40-70 €) and often a low-profile cooler and a short GPU. **The real cost of an SFF is 150-300 € higher than an equivalent mid-tower.**

---

## 8. Operational Summary — Case Selection Checklist

Print it, or copy it to a file, and fill it out before ordering.

### A. Dimensional Constraints (mandatory: if one fails, the case is excluded)

- [ ] **Motherboard format**: my mobo is ______ (ITX / mATX / ATX / E-ATX of ___ mm width). Does the case support it? **Yes / No**
- [ ] **GPU length**: my specific model is ______ mm long. The case states ______ mm **in the configuration I will use** (with front radiator/fans installed). Margin ≥ 15 mm? **Yes / No**
- [ ] **GPU thickness**: ______ slots. Does the case have enough free slots below? **Yes / No**
- [ ] **Width clearance for the 12V-2×6 cable** (if the GPU uses it): is the side panel ≥ 30 mm from the connector? **Yes / No**
- [ ] **CPU cooler height**: mine is ______ mm high. The case states ______ mm. Margin ≥ 5 mm? **Yes / No**
- [ ] **Radiator** (if AIO): size ______ (240/280/360/420), radiator + fan thickness ______ mm. Does the case support it in the ______ position (top/front)? Does it interfere with RAM or VRM heatsinks? **Yes / No**
- [ ] **PSU length**: mine is ______ mm deep. The case states ______ mm (with the cages I intend to keep). **OK / KO**
- [ ] **3.5" drives**: I need ______. The case accepts ______. **OK / KO**

### B. Airflow (mandatory)

- [ ] Is the **front mesh** or does it have a direct airflow path in front of the fans? If it's closed, is it a dual-chamber designed to draw air from the bottom/side? **Yes / No — if No, discard.**
- [ ] How many **fan positions** are there? Front ___ / Top ___ / Rear ___ / Bottom ___ / Side ___
- [ ] Can I achieve at least the minimum configuration **3 IN (front) + 1 OUT (rear)**? **Yes / No**
- [ ] Are there **dust filters** on all air intakes, and are they **removable without disassembling the PC**? **Yes / No**
- [ ] Are the **feet** ≥ 20 mm high for PSU air intake? **Yes / No**

### C. Quality and Features

- [ ] **How many fans are included?** ______ — what type? (PWM 4-pin / DC 3-pin) — what quality?
- [ ] **Real total cost** = case price (______ €) + additional fans (______ €) = ______ €
- [ ] Is a **fan hub** included? **Yes / No** — Does my mobo have enough headers? ______
- [ ] Is the **space behind the motherboard tray** ≥ 25 mm? **Yes / No**
- [ ] Are there **rubber grommets, cable channels, Velcro straps**? **Yes / No**
- [ ] Do reviews report **thin sheet metal, vibrations, poor tolerances, sharp edges**? **Yes / No**
- [ ] Does the **front I/O** have a **USB-C**? And does my motherboard have the corresponding **internal header**? **Yes / No**

### D. Configuration, after purchase

- [ ] **Front/bottom fans as intake**, **rear/top fans as exhaust**. Direction arrows checked on each fan.
- [ ] **Slight positive pressure**: intake airflow ≥ exhaust airflow.
- [ ] Fan curves in BIOS/UEFI **tied to a slow source** (mobo/VRM/GPU), **not to the CPU package**, with 3-6 second smoothing.
- [ ] **3.5" cages removed** if not needed, to free up front airflow.
- [ ] **Unused PCIe slot brackets reinstalled** in their place.
- [ ] **Top filter** removed if the PC is almost always on and the top is all exhaust (optional).
- [ ] **Standoffs** checked: one for each motherboard hole, **no extras**.
- [ ] Validation test: after 20 minutes of combined CPU+GPU stress test, GPU temperature is **below 80 °C** and the CPU is not thermally throttling. If not, the problem is the case or fan configuration, not the cooler.
- [ ] **Maintenance reminder**: clean filters every **1-3 months**.

### E. The three golden rules, if you forget everything else

1. **The front must breathe.** Mesh, not glass.
2. **Measure the GPU before buying the case.** The specific model, in millimeters.
3. **Four good fans in a 100 euro case beat two fans in a 250 euro case.** The volume of air exchanged is what cools; everything else is secondary.

---

*End of chapter. The next chapter — the display peripheral (the monitor) — concludes the consumer part before moving on to professional and workstation hardware.*

---

[← Previous](06-power-supply.html) · [All chapters](./) · [Next →](08-cooling.html)
