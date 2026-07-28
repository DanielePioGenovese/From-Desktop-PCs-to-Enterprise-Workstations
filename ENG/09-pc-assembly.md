---
title: "Chapter 9 - PC Assembly, Step by Step"
parent: "English Edition"
nav_order: 9
---

<details open markdown="block">
  <summary>Chapter contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## Introduction: why this chapter is different from all the others

Up to this point, the book has worked in the abstract. We have studied the CPU as an architecture, RAM as a hierarchy of latencies, the GPU as a parallel computing engine, the power supply as an energy converter. They were objects of knowledge. From this chapter onwards, they become physical objects: they have weight, they have edges, they have pins that bend, they have connectors that only fit one way and that melt if you insert them incorrectly. The difference between knowing what an LGA socket is and being able to close a retention lever without trembling is the same as between reading a flight manual and flying.

It must be said immediately, to relieve anxiety: **assembling a PC is mechanically easy**. It does not require soldering, it does not require multimeter measurements, it does not require experience. It is a game of interlocking parts designed specifically so that you cannot make too many mistakes, with polarized connectors (i.e., shaped to fit only one way) and alignment notches everywhere. An attentive adult following the instructions will finish the job in two or three hours the first time, and in forty minutes the fifth time.

But — and here's the point — mechanical ease coexists with **a few irreversible and costly actions**. A bent socket pin can mean a 300-euro motherboard to throw away. A 5V ARGB connector inserted into a 12V header destroys the LED strip and, sometimes, the controller on the motherboard. A 12V-2×6 power cable not fully inserted can melt the GPU connector under load. There isn't a long tail of medium errors: there's a huge set of harmless errors, and a tiny handful of lethal errors. This entire chapter is built around that distinction. Each step will tell you: what you need, how to do it physically, **how much force to apply** (it's the variable no one ever explains to you), what not to do for any reason, and how to check that you've done it correctly before moving on.

A final warning about the data: the socket names, connector models, and chipset acronyms mentioned here are those in circulation at the time of writing. They are the part of the book that ages fastest. The **physical procedures** — levers, notches, torque settings, order of steps — are, however, extraordinarily stable: anyone who assembled a PC in 2015 will recognize every gesture of a 2026 assembly. When data is volatile, I will explicitly indicate it.

---

## 0. Preparation: ninety percent of the work is done before opening the boxes

### 0.1 The tools: few, but the right ones

The tool you really need is **one**: a **Phillips PH2** screwdriver, magnetic, with a shaft at least 10-12 centimeters long.

Let's clarify the acronym, because it's the first example of how this job works. "Phillips" is the name of the screw head type — the cross with slightly flared sides — and **PH2** is its size. PH0 and PH1 are smaller, PH3 is larger. Practically all screws in a modern PC (motherboard screws, power supply screws, panel screws, PCIe bracket screws, fan screws) use the PH2 head. The only exceptions are the micro-screws for M.2 SSDs, which require a PH1 or even a PH0, and which generally come with their own screwdriver in the motherboard box.

The reason for **magnetic** deserves a line, because it's the point that beginners are most scared of: no, a magnetized screwdriver **does not damage** PC components. The fear arises from a wrong analogy with old mechanical hard drives and floppy disks, and in any case, the field of a magnetized screwdriver is very weak and only extends a few millimeters. On the contrary, magnetism saves your day: screws stick to the tip and don't fall inside the case, into that unreachable space under the motherboard from which they can only be retrieved by turning everything upside down and shaking. A lost screw in a closed case that is then powered on is, this is true, a potential source of short circuit.

Around the screwdriver, little else is needed:

**Cable ties.** You'll need about ten. Disposable nylon ones are perfectly fine (to be cut with wire cutters, never torn); if you plan to frequently work inside the PC, **Velcro cable ties** are preferable, as they are reusable and often included with the power supply or case.

**A light.** The back of a case, the header area on the bottom edge of the motherboard, the inside of a hard drive bay: these are all dark places. An adjustable desk lamp or a headlamp transforms a fumbling operation into a clear one. Using your phone's light, held with your left hand while your right hand tries to insert a three-millimeter F_PANEL connector, is a recipe for frustration.

**A large, flat, stable, non-textile surface.** A clear dining table is perfect. You need space for the case lying down, for the motherboard box (which will serve as your workbench, we'll get to that), for components awaiting installation, and for empty boxes, which should be kept: they store accessories, are useful for potential returns, and the motherboard box is the ideal surface for pre-assembly.

**Wire cutters or scissors** for the cable ties, and optionally a **container for screws** — a bowl, an egg carton, a jar lid. PC screws come in at least five different types, and mixing them up is a slow way to lose twenty minutes.

What you **don't** need, despite what you might be told: pliers (if you need pliers, you're doing something wrong), thermal paste if the cooler already has it applied, a professional anti-static mat, a tester.

### 0.2 Static Electricity (ESD): The Right Fear, in the Right Measure

**ESD** stands for *ElectroStatic Discharge*. It is the phenomenon where two materials rubbing against each other exchange electrons, and one of them becomes charged; when that charged body touches a conductor at a different potential, the charge discharges suddenly, with a potential difference that can easily exceed **one thousand volts** and reach tens of thousands. It's the spark you get when touching a car door handle in winter, and it's the same phenomenon that makes you "crack" when you take off a wool sweater in the dark.

The delicate point is this: the threshold at which **you** perceive a shock is around 2,000-3,000 volts, but modern transistors, with gate insulators only a few nanometers thick, can be damaged by discharges of **a few hundred volts**. This means there is a wide range where you can destroy a component **without noticing anything**. Worse: ESD damage is often *latent*. It doesn't kill the chip immediately; it weakens it. The PC works, perhaps for weeks, and then starts giving random errors, inexplicable crashes, data corruption. It is the most difficult type of fault to diagnose.

That said, it must also be stated that the odds are in your favor: modern components have ESD protection diodes on their inputs, and millions of people assemble PCs every year without precautions and without consequences. The rational strategy is not paranoia; it is **routine**. A few simple rules, always the same:

1. **Work on a hard surface, not on carpets, rugs, or sofas.** Textiles are the primary source of static charges. If the floor is carpeted, work on a table and do not drag your feet.
2. **Discharge yourself before touching components** by touching a large, freshly painted metal surface — typically the **PC case chassis** — and repeat this action every time you have stood up, walked, or taken off or put on a sweatshirt.
3. If you want the technically correct solution, use an **anti-static wrist strap**: a conductive strap on your wrist, connected by a cable (which contains a 1 MΩ resistor for your safety) to a ground. The most convenient ground is the PC case chassis. It's good to know something almost no one explains: the wrist strap **does not "discharge" components**; it serves to keep **you and the case at the same potential**, so that there is never a voltage jump between your hand and the board. The PC does not need to be plugged into the power outlet (in fact, it should not be, we'll discuss that in a moment). Equipotentiality is sufficient.
4. **Keep components in their anti-static bags** (the gray, metallic ones that form a Faraday cage) until you use them. And never place a motherboard *on top of* its anti-static bag: the outer surface is slightly conductive and can create unwanted paths. Place it **on top of the cardboard box**, which is the ideal insulator.
5. **Handle boards by their edges.** Never by the gold contacts, never by the chips.

A note that debunks a common myth: **do not** leave the PC "plugged in with the switch off to have grounding." This was advice from the nineties, when ATX power supplies did not always have live voltages. Today, a power supply connected to the mains keeps the **5 V standby (+5VSB)** line alive even when the PC is off: if you work inside the case with the power supply connected, there are parts under voltage. The modern rule is: **power cable unplugged from the wall, always, when your hands are inside the case.**

### 0.3 The final compatibility check: the last exit before the highway

Opening a box means, in many stores, giving up the right to return "for second thoughts" (the right of withdrawal remains, but an opened and assembled product can be contested). So, **before breaking the first seal**, do a final check. It's tedious and will save you one out of five times.

Take the motherboard box — it's the hub of all compatibilities — and check these six points, one by one:

| # | What to check | How | Symptom if wrong |
|---|---|---|---|
| 1 | **Socket** | The socket printed on the mobo box (e.g., AM5, LGA1851, LGA1700) must match the CPU's socket *exactly* | The CPU does not physically fit |
| 2 | **CPU BIOS support** | On the mobo manufacturer's website, "CPU Support List" section, find the *exact model* of your CPU and read the minimum BIOS version required | The CPU is compatible with the socket but the PC does not POST: a BIOS update is needed |
| 3 | **RAM: type and QVL** | DDR4 and DDR5 are **not** interchangeable (different notch position, different voltage, different pinout). Also check the QVL (*Qualified Vendor List*, the list of tested kits) | RAM that doesn't fit, or fits but doesn't hold the declared frequency |
| 4 | **Cooler clearance** | Maximum cooler height allowed by the case (in mm) ≥ your cooler's height. For AIOs: does the case support a radiator of that length *in that position*? | The side panel does not close |
| 5 | **GPU length** | Maximum GPU length allowed by the case ≥ your GPU's length (note: power cables take up another 3-4 cm if they exit sideways) | The GPU does not fit, or hits the hard drive bays |
| 6 | **PSU: watts and connectors** | Sufficient power (with margin) **and** physical presence of required connectors: how many 8-pin PCIe does the GPU need? Is a 12V-2×6 needed? How many EPS does the mobo need? | You cannot power everything |

Point 2 deserves its own paragraph, as it's the most insidious trap in modern PC building. A socket has a lifespan spanning multiple CPU generations: AM5, for example, hosts CPUs released years apart. A motherboard manufactured in 2023 and left in storage might have firmware that simply **doesn't recognize** a CPU released in 2025: the socket matches, the CPU installs, but the PC won't boot because the BIOS doesn't know how to initialize it. The solution exists and is excellent, but it must be planned **in advance**: it's called **BIOS Flashback** (commercial names: *BIOS Flashback*, *Q-Flash Plus*, *Flash BIOS Button*), a feature found on mid-range and high-end motherboards that allows you to update the BIOS **without a CPU, RAM, or GPU installed**, simply by connecting the power supply, inserting a FAT32-formatted USB drive with the BIOS file renamed as required, and pressing a small button on the back. If your CPU is new and your motherboard might be old stock, **verify that the board has Flashback**: it's the difference between an hour of waiting and a week lost trying to borrow an old CPU.

### 0.4 The Strategy: "Bench" Pre-assembly

Here's the advice that distinguishes someone who has built ten PCs from someone building their first, and that no illustrated guide emphasizes enough: **do not install the motherboard in the case first.**

The correct method is **bench pre-assembly** (also known as *bench build* or *breadboarding*). You place the motherboard **on top of its cardboard box** — not on the anti-static bag, as mentioned — and on that comfortable, well-lit, easily accessible surface, you install: **CPU, RAM, M.2 SSD, and (if air-cooled) the cooler**. Only then do you take the assembled block and lower it into the case.

There are three solid reasons for this.

The first is **ergonomic**: installing a CPU inside a case means working vertically, with poor lighting, and the risk of the component slipping. Installing it on a table means working horizontally, with gravity assisting instead of hindering, allowing you to clearly see the pins and notches. The same applies to RAM, which needs to be pushed firmly: doing so with the motherboard already screwed into the case means flexing the PCB (the *Printed Circuit Board*, the fiberglass support on which the board is built) against its mounting points; doing it on a rigid surface means the force is absorbed by the table.

The second is **diagnostic**, and it's the most important. Pre-assembly allows for **out-of-case POST testing**: install the CPU, cooler, a single stick of RAM, the GPU, and the power supply, connect a monitor, and try to power it on by momentarily shorting the two Power Switch pins with the tip of a screwdriver (or using the integrated power button, if the board has one). If the BIOS screen appears, you are certain that the four most expensive and difficult-to-replace components **are working and compatible with each other**. If the PC doesn't boot, you have a bare system with five cables, where isolating the problem is trivial. Compare this to the alternative scenario: you install everything in the case, with forty cables, fans, hard drives, and it doesn't boot. Now the culprit could be anything, and to investigate, you have to disassemble everything. The POST test takes ten minutes and repays them tenfold in the worst-case scenario.

The third is **practical**: some cases make it impossible to mount the backplate of certain coolers once the motherboard is screwed in, if the rear cutout of the *motherboard tray* is not aligned with the socket. Better to find out beforehand.

The only exception to the rule is the **radiator of an AIO** (*All-In-One*, the closed and pre-assembled liquid cooling system). The waterblock is mounted on the CPU on the bench, but the radiator, which is connected to it by short and rigid tubes, must be mounted **after** the motherboard is in the case. We will discuss this in detail in points 4 and 8.

---

## 1. Installing the CPU on the Motherboard

It's the first and most feared step. Let's do it right.

### 1.1 The Two Philosophies: LGA and PGA

Historically, there have been two ways to connect a CPU to a motherboard.

**PGA** (*Pin Grid Array*): the pins — hundreds of thin metal needles — are **on the CPU**, and the socket on the motherboard is a matrix of holes. This is the scheme used by AMD up to and including the AM4 socket. The risk is entirely on the processor: if you drop it or place it incorrectly, you bend its pins, and the pins of a CPU can (with patience, a magnifying glass, and the tip of a razor blade) even be **straightened**.

**LGA** (*Land Grid Array*): the pins are **on the motherboard socket** — a thousand or more elastic, very thin needles, mounted on a plate — and the CPU only has flat, gold-plated contact pads (*lands*, precisely). This is the scheme used by Intel for twenty years and, since 2022, also by AMD with the **AM5** socket. The risk has shifted: the CPU is almost indestructible, but the motherboard socket is **extremely fragile**. A bent LGA pin is a few tens of microns wide; straightening it requires a microscope, a steady hand, and incredible luck, and in most cases, the motherboard needs to be replaced — and warranties **do not cover** mechanical damage to the socket.

Operational consequence, valid for **all** current platforms (Intel LGA and AMD AM5):

> **NEVER, for any reason, touch the socket pins. Not with your fingers, not with a screwdriver, not with a brush, not with a cloth.** If something falls into the socket, do not "brush it away": blow gently, or use compressed air from a distance.

### 1.2 The Procedure, Step by Step

The socket is covered by a protective **plastic cap** (usually black). **Do not remove it by hand.** On modern sockets, it detaches by itself when you close the lever with the CPU inside, or it should only be removed after opening the mechanism. Tearing it off prematurely is one of the most common ways to damage the pins. (Keep it: if one day you need to send the motherboard for RMA — *Return Merchandise Authorization*, the warranty return procedure — it must be reattached, and some manufacturers refuse returns without the cap.)

**Step 1 — Open the retention mechanism.** On **AM5** and **Intel LGA**, the principle is the same: a side metal lever, held by a hook. Press the lever **down and slightly sideways** to release it from the hook, then lift it all the way up (about 90-100 degrees). You will see the retention plate (the *load plate*) above the socket rise. The lever offers considerable resistance at this stage: this is normal, it is a preloaded spring.

**Step 2 — Pick up the CPU.** Pick it up **by the side edges**, with your thumb and index finger, like you would a CD. Never by the faces.

**Step 3 — Align.** This is the crucial step, and it must be done by **looking**, not by fumbling. There are two redundant alignment systems:
- A **golden triangle** printed on one corner of the CPU package must match a **small triangle** (or an arrow, or a chamfered corner) printed on one corner of the socket. On AM5, the triangle is clearly visible; on Intel LGA, you'll find it silk-screened on the frame.
- **Notches** (half-moons) on the short sides of the package fit into corresponding **tabs** on the socket. They are intentionally asymmetrical: if the CPU is rotated, it won't sit flat.

**Step 4 — Place, don't press.** This is the phrase to remember: **the CPU is placed, not inserted**. Keep it horizontal, bring it over the socket, and lower it **straight down from above**, without tilting it, without letting it slide sideways onto the pins. It must **drop into its housing with a one-millimeter movement** and stay there firmly. The force to apply in this step is **zero**. If you have to press, it's wrong: lift it straight up and try again.

**How to check if you've done it correctly:** with a finger, give the CPU a very light tap on the side. It should have a micro-play (a fraction of a millimeter) and return to position. If it doesn't move *at all*, it might be stuck crooked; if it wobbles noticeably, it's not in its seat. Then look at the profile **from above and from the side**: the CPU must be perfectly parallel to the socket plane, with no raised corner.

**Step 5 — Close.** Lower the load plate and bring the lever back down, re-engaging it under the hook. **Here, the resistance is strong, and this scares everyone.** The force required to close a modern LGA socket is on the order of **50-70 newtons** (the equivalent of five to seven kilograms of thrust): it serves to press the CPU against over a thousand elastic pins, ensuring reliable contact on each one. **It is normal to feel significant resistance and even a small creak.** What is *not* normal is a **hard stop**: if the lever does not descend with progressive and firm pressure, **stop**, reopen, and recheck the alignment. The resistance is elastic and continuous; the obstacle is hard and sudden. Learning to distinguish these two sensations is, literally, the key skill for this step.

The plastic cap, on Intel sockets, will pop off by itself upon closing. On AM5, it should be removed during the process according to the manual.

### 1.3 Common Errors

**Bent pin.** This almost always results from two actions: sliding the CPU onto the socket instead of placing it, or forcing the lever with the CPU crooked. If, after an error, the PC doesn't start, or starts but doesn't see half of the RAM (the memory channel pins pass through the socket!), suspect a bent pin. Inspect it with a grazing flashlight and a magnifying glass: the pins must form perfectly regular rows, like a combed field of wheat. A row that "wobbles" indicates a misplaced pin.

**CPU mounted crooked ("at 90 degrees").** Impossible on a healthy platform, because the CPU is not square in its anchors — but it is possible by forcing it. If you even have a doubt, reopen and look at the small triangle.

**Touching the golden surface (IHS).** The **IHS** (*Integrated Heat Spreader*, the metal lid you see on top of the CPU) must be kept clean: fingerprints leave grease, which is a poor thermal conductor. If you touch it, clean it with a microfiber cloth and isopropyl alcohol.

**Do not keep the socket cover.** See above: it can void the warranty.

---

## 2. Installing RAM

This seems like the most trivial step, but it is actually the number one cause of "my new PC won't turn on."

### 2.1 Why not all slots are equal: dual channel

System memory communicates with the CPU through the **IMC** (Integrated Memory Controller, the memory controller that now resides **inside** the CPU, no longer in the chipset). The IMC of modern consumer CPUs is **dual channel**: it exposes two independent channels to the RAM, each with its own bus. If you populate only one channel, you halve the available bandwidth. The effect is dramatic where bandwidth matters — in **iGPUs** (integrated GPUs, the graphics integrated into the CPU, which uses system RAM as video memory) it can mean **double the frames**; in gaming with a dedicated GPU, we are still talking about double-digit percentages in the worst cases.

Now: a standard ATX motherboard has **four** DIMM slots, but there are **two** channels. Each channel has two slots. The question becomes: if I have two sticks, where do I put them?

The answer, almost universally, is: **in slots 2 and 4 counting from the CPU**, i.e., those labeled **A2 and B2** (or **DIMM_A2 / DIMM_B2**, or on some DDR5 boards with different nomenclature). These are the two slots **furthest** from the CPU among those available, and it's counterintuitive: one would think that closer is better.

The reason is **signal integrity**. The traces that carry the memory bus from the CPU to the slots are very high-frequency lines; an empty slot left "before" a populated one creates a **stub**, an unterminated section of the line that generates signal reflections. The *daisy-chain* topology used by the vast majority of consumer boards is designed and optimized for the signal to be cleaner when the **terminal slots** are populated. On AM5 and DDR5 platforms in general, where frequencies are very high and the controller is already under stress, this is not an academic detail: installing two sticks in the wrong slots can mean **not being able to start the EXPO/XMP profile**, or not POSTing at all.

> **The golden rule: open your motherboard manual to the "Memory Configuration" page and read the table. Thirty seconds stand between you and certainty.** The table tells you exactly, for 1, 2, and 4 modules, which slots to populate. Don't guess, don't rely on memory: some models (rare, but they exist) require A1/B1.

A note worth the price of the chapter: **two sticks are better than four**. With four modules, the IMC has to drive twice the electrical loads, and the maximum stable frequency **drops**, often significantly (on DDR5, a kit that supports 6000 MT/s with 2 modules may struggle above 4800 with 4 modules). If you need 32 GB, buy **2×16**, not 4×8. And most importantly: **do not mix different kits**. Two 2×16 kits bought at different times, even if identical on the label, may have chips from different manufacturers and not work together at the nominal frequency. A kit is tested *as a whole*: if one day you want 64 GB, buy a new 2×32 kit.

### 2.2 The procedure

**Step 1 — Open the clips.** The clips are the plastic levers at the ends of the slot. Some boards have clips on **both** sides, while others (increasingly common, to make room for the GPU) have a clip on only **one side**, with the other end fixed. Look at and open the ones that are present, pushing them outwards until they snap open.

**Step 2 — Align the notch.** The DIMM module has a **notch** (an indentation) on the edge of the contacts, **off-center**: it's not in the middle. The slot has a corresponding tab. Rotate the module until the notch aligns. **This notch is why you cannot install DDR4 on a DDR5 board**: it's in a different position specifically to make errors physically impossible (the two generations have incompatible voltages and pinouts, and forced insertion would destroy something).

**Step 3 — Press.** This is the other point where you need to overcome fear. Place the module in the slot, ensure it is **perfectly vertical and aligned**, then press **with your thumbs on the two upper ends of the module, one at a time or together**, with **firm and progressive force**. The necessary force is **considerable**: we're talking about a push comparable to what you would use to close a full suitcase. It is not a delicate gesture.

The module slides down, and the clips — pushed by the shape of the PCB — **close by themselves with an audible and distinct CLICK**. If the clips are on both sides, you should hear **two clicks**. Hence the expression you'll hear everywhere: *"press until the double click."*

**How to check if you've done it correctly:** look at the module **from the side, at the height of the slot**, and check that the **golden line of contacts has completely disappeared** inside the slot, **uniformly along its entire length**. Then try gently pulling the module upwards: it should not move a millimeter. Finally, check that the clips are **vertical and closed**, engaged in the side notches of the module.

### 2.3 The error that affects everyone: the half-inserted module

It is statistically **the number one cause** of "new PC that does nothing": the module is inserted, it seems to be in, the clip on one side has closed, but **on the other side, it has remained raised by a millimeter**. The electrical contact is partial or absent, the POST fails, the PC turns on (the fans spin!) but gives no video signal, and the **DRAM** diagnostic LED remains lit.

Why does this happen so often? Because the necessary force is greater than what instinct suggests, and because pressing with only one thumb in the center causes the module to tend to get stuck on one side and remain high on the other. **Press at the ends. Listen for the clicks. Look at the contacts.**

---

## 3. M.2 SSD Installation

### 3.1 What it is and which slot to choose

An **M.2 SSD** is a solid-state drive on a small rectangular circuit board (typically 22 × 80 mm, hence the designation **2280**) that plugs directly into a slot on the motherboard, without cables. In almost all modern cases, we are talking about **NVMe** (*Non-Volatile Memory Express*, the protocol specifically designed for flash memory) units that communicate over the **PCIe** (*Peripheral Component Interconnect Express*, the serial lane-based bus that connects CPU and peripherals) bus.

And here lies the important choice: **not all M.2 slots on a motherboard are equal.**

A modern motherboard typically has 2, 3, or 4 M.2 slots. Of these, **only one** — almost always the one **closest to the CPU**, between the socket and the first PCIe x16 slot — is connected **directly to the CPU's PCIe lanes**. The others go through the **chipset**, the chip that acts as an expansion hub and is connected to the CPU via a shared link (DMI on Intel, a dedicated PCIe link on AMD).

There are two practical consequences. The first is **bandwidth**: the slot from the CPU generally offers the latest PCIe generation and full x4 (four lanes); the slots from the chipset may be an older generation, and in any case, they share the chipset↔CPU link bandwidth with everything else (USB, SATA, network, expansion cards). The second is **latency**: going through the chipset adds a hop.

> **Rule:** Your primary SSD — the one with the operating system — goes into the **primary M.2 slot, the one connected to the CPU**. The manual explicitly indicates this, often calling it *M2_1*, *M2A_CPU*, or similar, and always specifies which lanes it uses.

There's a second reason to read the manual: **resource sharing**. On many boards, populating a certain M.2 slot **disables** SATA ports, or reduces the secondary PCIe x16 slot from x4 to x2. These are normal compromises (lanes are a finite resource), but they should be known beforehand, not discovered after installing three drives that don't show up.

### 3.2 The procedure

**Step 1 — Remove the motherboard's M.2 heatsink.** Modern motherboards cover M.2 slots with a metal plate (sometimes a true finned heatsink). It's unscrewed with one or two small screws (PH1). Remove it and set it aside with its screw.

**Step 2 — Remove the film from the thermal pad.** This is the step that **almost everyone forgets**, and it's worth emphasizing: under the heatsink there is a **thermal pad** (a cushion of conductive rubbery material), and on top of the pad there is a protective **transparent plastic film**. **It must be removed.** If you leave it on, you've inserted an insulator between the chip and the heatsink, and your SSD will experience *thermal throttling* (automatic performance reduction due to excessive temperature) under load. The film is often invisible to a casual glance: run a fingernail over a corner to find its edge.

**Step 3 — Adjust the standoff.** The M.2 slot accepts different lengths (2242, 2260, 2280, 22110: the last two digits are the length in millimeters). The mounting point must be moved to the hole corresponding to the length of your SSD — almost always 2280, which is usually the default. Many recent boards have replaced the screw with a **snap-on clip** (commercial names: *M.2 Q-Latch*, *EZ Latch*), which is rotated or pressed without tools: much more convenient.

**Step 4 — Insert at 30 degrees.** The SSD does **not** go in straight. It should be presented to the slot at an **angle of about 20-30 degrees**, pushed in until the contacts are fully inserted (the notch on the contacts, like for RAM, ensures the correct orientation), and **then lowered** flat. The force is modest: you'll feel the module slide and stop. When you release it, the SSD will lift itself a few degrees — this is normal, it's the spring of the contacts.

**Step 5 — Secure.** Hold the SSD flat with one finger and screw in the micro-screw (or close the clip). **Do not overtighten**: you are tightening an M2 screw on a thin PCB. "Finger-tight plus a whisper" is more than enough; tightening more will strip the thread or crack the SSD.

**Step 6 — Reattach the heatsink** (with the film removed!) and screw it in.

**How to check:** The SSD should be flat, with no play. But the real check comes at the first boot, in the BIOS: if the drive does not appear in the list of storage devices, power off and recheck the insertion.

---

## 4. Cooler installation

Here is the most delicate and most misunderstood part of the entire assembly: thermal paste. Let's first clarify the principle, then the two procedures.

### 4.1 The principle: why thermal paste exists (and should ALWAYS be applied)

The CPU's IHS and the cooler's base (the *cold plate*) appear perfectly smooth. They are not. Under a microscope, both surfaces are mountain ranges: roughness of a few microns, plus any macroscopic deformations (many IHS are slightly concave or convex). If you place them one on top of the other, actual metal-to-metal contact occurs only on a **small fraction of the surface** — the rest are microscopic air pockets. And air is one of the **most effective thermal insulators** you know (it's why down comforters and double glazing work).

**Thermal paste** (*TIM*, *Thermal Interface Material*) serves exclusively to **fill those air pockets**. It is not meant to "conduct better than metal" — in fact, paste conducts heat **much worse** than copper. It serves to be better **than the air it replaces**. From this immediately follows the golden rule of application:

> **Thermal paste should be applied in the minimum amount sufficient to fill the gaps. More is not better: it's worse**, because a thick layer of paste is a thick layer of mediocre material that *moves* the two metals *apart*.

And, equally important:

> **Thermal paste should ALWAYS be applied between the CPU and the cold plate. Always. Air or liquid makes no difference.** This is a confusion I hear constantly: people believe that in liquid systems "the liquid takes care of it." No. The liquid works *inside* the waterblock; the interface between the CPU's IHS and the bottom of the waterblock is **identical** to that of an air cooler, and has exactly the same need for paste.

**How much, and how.** The most reliable and universal method is the **central pea**: a drop in the center of the IHS, with a diameter of **a grain of rice / a small pea** (4-5 mm). The mounting pressure spreads it radially and uniformly by itself, expelling air towards the edges — which is exactly what you want. Applying by hand with a spatula seems more "professional" but easily introduces air bubbles; the diagonal X and the five dots are acceptable variations, useful on very elongated IHSs (like the rectangular ones of certain HEDT platforms or LGA1700, where some prefer a **thin line** along the major axis). For details on conductivity, liquid metal pastes, pump-out, and longevity, refer to the cooling chapter; here, the focus is on the action itself.

Two final warnings: **do not spread with your finger** (skin oil), and **do not assemble and disassemble "to check"**. If you lift the heatsink after placing it, the only correct way to proceed is to **clean everything with isopropyl alcohol and reapply from scratch**: the film has broken and incorporated air.

Last point: **many heatsinks come with pre-applied paste** on the cold plate (a gray layer, often with a silkscreened pattern). In that case, **do not apply more paste**. And, above all, **check if there is a plastic film on top**: we'll get to this, because it's the most catastrophic mistake in the entire chapter.

### 4.2 CASE A — Air Cooler

**Step 1 — The backplate.** Most quality air coolers do not use the motherboard's stock retention system, but their own: a metal or plastic plate (the **backplate**) that must be positioned on the **back** of the motherboard, behind the socket, and serves to distribute the strong mounting pressure over a wide area, preventing the PCB from flexing. This is exactly why pre-assembly on a test bench is convenient: the board is free, you flip it, put on the backplate, then flip it back.
On AMD AM4/AM5 platforms, the backplate **is often already present** on the motherboard and must be **reused** (sometimes only the *retention module*, the black plastic frame around the socket, needs to be removed). Read the *cooler*'s manual, which will tell you which path to follow.

**Step 2 — The standoffs.** Above the motherboard, corresponding to the four holes around the socket, four threaded posts that protrude from the backplate are screwed in. They should be tightened **by hand, until snug, without forcing**. They determine the height — and thus the pressure — of the cooler.

**Step 3 — The paste.** As above: pea-sized amount in the center of the clean IHS. (If the cooler already has factory paste, skip this step.)

**Step 4 — Placing the cooler.** Lower it **straight down, without dragging**, aligning the brackets over the standoffs. Do not "land" it at an angle.

**Step 5 — Tighten in a cross pattern, incrementally.** This is the technical step. The screws (usually four, sometimes two long bars) **should not be tightened one at a time all the way**. Tighten **diagonally** — 1, 3, 2, 4 — and in **partial turns**: two turns on the first, two on the third (the opposite), two on the second, two on the fourth, and repeat. The reason is that the cooler must descend **parallel** to the IHS: if you tighten screw 1 all the way before touching 3, you tilt the cold plate, and the paste is squeezed on one side and not the other, resulting in poor contact (and, in the worst cases, PCB flexing).
Tighten **until the screws bottom out and start to spin freely** or until the spring is fully compressed. Most modern kits have **captive screws with preloaded springs**, designed to make it *impossible* to overtighten: when the screw no longer advances, you are done. **Do not apply extra torque "just in case."**

**Step 6 — Connect the fan to CPU_FAN.** The 4-pin connector from the cooler goes into the header labeled **CPU_FAN** (not CPU_OPT, not SYS_FAN). Reason: the motherboard **monitors that header**, and many BIOSes **refuse to boot the system** (or show a "CPU Fan Error") if they don't detect a rotation signal there. The connector has a guiding tab on one side: it only goes in one way, and if you have a cooler with two fans, the second cable goes into **CPU_OPT**.

### 4.3 CASE B — AIO (liquid cooling)

An **AIO** is a pre-assembled closed loop: a **waterblock** with an integrated **pump** that mounts on the CPU, two **tubes**, a **radiator** with its fans. It is not filled, bled, or opened.

The order of the steps here is **different**, and getting it wrong means working in impossible conditions:

**Step 1 — Mount the socket brackets on the waterblock**, *first of all*, on the table. Each AIO includes different bracket kits for AMD and Intel: choose the right ones, and slide/screw them onto the block.

**Step 2 — Backplate and standoffs on the motherboard**, as for air cooling.

**Step 3 — THE FILM.** Stop. Look at the **cold plate** of the AIO, the copper or nickel plate on the bottom of the waterblock. In the vast majority of cases, the manufacturer has applied thermal paste at the factory **and covered it with a transparent or colored plastic film**.

> **REMOVE THE FILM.** This is not a detail: it is the most expensive and most common mistake in this chapter. A cold plate mounted with the film on it means **zero thermal transfer**: the CPU reaches its safety temperature (**T-junction max**, typically 95-100 °C) in **a few seconds** after power-on, goes into extreme throttling, and then shuts down. The PC "shuts down by itself after ten seconds" is almost always this. It doesn't damage the CPU (the protections work), but it costs you an afternoon.

If the cold plate is **bare** (no pre-applied paste), then apply the pea-sized amount yourself on the CPU's IHS, as for air cooling.

**Step 4 — Secure the waterblock.** Place it straight and tighten the nuts/screws **crosswise and incrementally**, exactly as for air cooling, until snug.

**Step 5 — The radiator: NOT NOW.** The radiator remains hanging from its tubes, resting on the table. **It should be mounted in the case after installing the motherboard** (point 8). Attempting to mount the radiator and motherboard together, outside the case, means having to lower a rigid, heavy object constrained by two short tubes into the case: it's the best way to tear something. In the meantime, **gently rest the radiator** and do not bend the tubes at sharp angles.

**Step 6 — Electrical connections.** An AIO generally has two (or three) cables:
- the **pump** cable, which goes into a dedicated header: **AIO_PUMP** if it exists, otherwise **CPU_FAN** (the pump header always provides 100% voltage, without a regulation curve: that's what you want, a pump should always run at maximum or at a fixed speed);
- the cable (or cables) for the **radiator fans**, which go into **CPU_FAN** and/or **CPU_OPT**, or — if the kit has a **splitter** or a hub — into a single header. If you connect the pump to AIO_PUMP and the fans to CPU_FAN, the BIOS is happy and you can build a sensible curve.
- optionally an **ARGB/USB** cable for lighting and monitoring (be careful with ARGB: see point 10).

---

## 5. Case preparation

The motherboard + CPU + RAM + M.2 + air cooler block is ready. Before lowering it in, prepare the nest.

**Step 1 — Open everything.** Remove **both** side panels (the glass/metal one and the rear one, behind the *motherboard tray*: this is where you'll route cables) and, if possible, the front and top panels, to access the fan brackets. Put the panel screws in a separate container.

**Step 2 — Check the standoffs.** This step is often overlooked and is potentially **destructive**. The case's **standoffs** are small threaded hexagonal pillars, screwed into the motherboard tray, on which the motherboard will rest. They have **two functions**: to lift the board a few millimeters from the metal, to prevent the solder joints on the back from touching the frame and **short-circuiting**; and to define the anchoring points.

Cases come with standoffs **pre-mounted for the ATX format**. But there are three formats (and we'll list them, because the choice is yours):

| Format | Typical Dimensions | Typical Mounting Holes | PCIe Slots | RAM Slots |
|---|---|---|---|---|
| **ATX** | 305 × 244 mm | 9 | up to 7 | 4 |
| **Micro-ATX (mATX)** | 244 × 244 mm | 6-8 | up to 4 | 2-4 |
| **Mini-ITX** | 170 × 170 mm | 4 | 1 | 2 |

> **The critical check:** Place (without screwing) the motherboard in the case and check that **under every hole on the board there is a standoff, and — most importantly — that there is NO standoff where the board does NOT have a hole.** An "orphan" standoff touching the back of the motherboard is a **short circuit**: the PC won't start on the first boot, and in the worst case, the board will be damaged. This is a classic mistake made by those who install a Micro-ATX board in a case designed for ATX. Excess standoffs should be unscrewed (by hand or with a 5 mm socket wrench, often included).

Many cases have a central **raised, non-threaded standoff**: it serves as support and for centering, not for screws. It's perfectly fine for it to be there.

**Step 3 — The I/O shield.** The **I/O shield** is the rectangular metal plate that covers the rear ports of the motherboard. On today's mid-range and high-end motherboards, it is **integrated** (already mounted on the board, often with a plastic cover over the VRMs): in that case, you don't need to do anything and can skip this step. If your board has a **separate** I/O shield — a metal plate in the box — it must be installed **now**, because you won't be able to do it later.

It is installed **from the inside of the case outwards**: place it in the rectangular opening at the back, check the **orientation** (the port labels must be readable from the outside, and the shape must match your board's ports: look at the motherboard while you do this) and press on the **four corners** until it clicks into place with a metallic sound. Considerable **force** is required, and the metal sheet has sharp edges: be careful with your fingers. An I/O shield installed backward is one of the most bitter memories of PC building, because you only realize it after the motherboard is already screwed in.
A small warning: some I/O shields have **elastic metal tabs** that protrude inwards. They must press **on top** of the ports (they act as grounding), not **inside** them: when you lower the motherboard, check that no tab has entered a USB or Ethernet socket.

**Step 4 — Pre-route front cables.** The front panel cables (power switch, USB, audio) and the cables for the pre-installed case fans are **already there**, dangling. Before installing the motherboard, route them through the **nearest grommets** to their destination, so they are already positioned correctly. Doing it later means having to thread your hand between the motherboard and the chassis.

---

## 6. Installing the PSU (Power Supply Unit)

The **PSU** (Power Supply Unit) is almost always located at the **bottom** in modern cases, in a separate compartment (shroud), and this is a good thing: it draws cool air from below and expels it directly outside, without heating up the rest of the system.

### 6.1 Fan Orientation: The Question Everyone Gets Wrong

The power supply fan can face **downwards** or **upwards (into the case)**. The rule is simple but needs to be understood:

- If the case has **tall feet and a filtered grille under the power supply** (practically all modern cases), the fan should face **downwards**. The power supply draws **cool air from outside**, uses it to cool itself, and expels it out the back. This is a completely independent circuit from the case's airflow: excellent.
- If the case rests directly on the floor without airflow, or the bottom is **closed** (old cases, desktop cases with the power supply at the top), then the fan should face **inwards**: it will draw hot air from the case. This is suboptimal, but it's better than not getting any air at all. A fan facing a closed bottom will **suffocate** the power supply, and an overheating power supply will age quickly or trigger thermal protection.

If you place it on carpet, the feet are not enough: put a board under the case.

### 6.2 The Procedure

**Step 1 — Connect modular cables BEFORE inserting the power supply.** If your PSU is **modular** (detachable cables) or **semi-modular** (fixed 24-pin and EPS, the rest detachable), this is the time to attach the cables to the **power supply side**, while you have it in your hand and the ports are accessible. Once it's screwed into the bottom of the case, behind the shroud, those ports become inconvenient.
Which cables do you need? Take inventory: **1× ATX 24-pin** (always), **1 or 2× EPS 8-pin** (for the CPU; check how many your motherboard requires), **PCIe** cables for the GPU (how many 8-pin? or a **12V-2×6**?), **1× SATA power** if you have 2.5" SSDs or hard drives, and any cables for fans/hubs.

> **CRITICAL WARNING, and this is not an exaggeration: modular cables are NOT interchangeable between different power supplies, even from the same manufacturer.** The PSU-side connector may be physically identical but have a **completely different pinout**. Using a cable from one PSU on another is one of the very few ways to **instantly destroy all connected components**, sending 12V to a line expecting 3.3V or putting ground where there should be voltage. **Only use the cables that came in the box with that power supply.** If you've lost them, they can be purchased from the manufacturer, for that specific model.

**Step 2 — Insert the PSU** into its bay (sometimes from the rear, sometimes from the inside; some cases have a removable bracket that screws to the PSU first and then to the case).

**Step 3 — Screw in** the four screws on the back of the case. These are coarse-thread screws (UNC 6-32): do not force them.

**Step 4 — Route the cables** behind the motherboard tray, towards their destinations. Do not connect them yet: the motherboard goes in first.

---

## 7. Installing the Motherboard in the Case

**Step 1 — Lower the board.** Hold it by the edges (or by the CPU cooler, if it's an air cooler, which is now attached) and lower it into the case with a slightly **oblique motion**: first the rear ports towards the I/O shield, then the board flat.

**Step 2 — Align with the I/O shield.** The board should be **pushed towards the back of the case** until the ports protrude through the I/O shield holes. This is when you check that no metal tabs have ended up inside a port. When the board is in position, the **holes for its mounting points align perfectly with the standoffs**: if a hole is misaligned by two millimeters, do not force it — the board is not pushed back enough, or a standoff is in the wrong place.

**Step 3 — Screw it in.** Place **the first screw in a corner, without tightening it**, just to hold the position. Then the others. Then go back and tighten all of them, **working in a cross pattern** (never in a circle) to avoid putting tension on the PCB.

**How much force?** Little. The screw should **just make contact**: as soon as you feel metal touching metal, **stop**. Tightening a motherboard screw "with force" means deforming the PCB around the hole and, in the long run, cracking the copper traces. A properly screwed-in motherboard **does not flex** if you press a finger on it, but the screws are not as tight as wheel lug nuts.

**Check:** count the screws. There should be as many as there are standoffs. A standoff without a screw is an unsecured support point, and when you press the RAM or GPU, the board will flex there.

---

## 8. Installing the AIO Radiator (if present)

Now that the motherboard is in, the radiator finds its home.

### 8.1 Where to put it, and the rule of air in the pump

The two sensible positions in a standard ATX case are:

**Top exhaust (at the top, expelling air) — the recommended choice.** The radiator sits on the roof of the case and pushes hot air upwards/out. Advantages: it doesn't heat up the rest of the system; the GPU receives fresh air from the front; hot air goes where physics already wants to send it. Disadvantage: the liquid in the radiator receives air already slightly warmed by the components, so CPU temperatures are a few degrees higher compared to the front intake configuration.

**Front intake (at the front, drawing in air) — maximum performance for the CPU.** The radiator sits at the front and draws fresh air from the outside. The CPU gets the best possible temperatures, but **all the air entering the case has been preheated by the radiator**, and the GPU, VRMs (Voltage Regulator Module, the CPU power stages), and SSDs pay the price.

However, there's a constraint that **overrides everything**, and it's hydraulic. The AIO **pump** must be **at the lowest point of the circuit**, or at least **never at the highest point**. The reason: in every AIO, over time, a small amount of air accumulates (permeation through the tubes, residual factory air). That air **floats** and goes to the highest point of the circuit. If the highest point is the **radiator**, the air collects there and does no harm. If the highest point is the **pump**, the air enters the impeller, the pump spins "dry" — and starts making an unmistakable noise, a cyclic gurgle/hum — and **loses efficiency, eventually seizing**.

> **Practical rule: the radiator tubes must exit from the BOTTOM SIDE of the radiator** (or at least the waterblock must be below the level of the top part of the radiator). In a top configuration, mount the radiator with the tubes towards the back/bottom. In a front configuration, mount the radiator **high**, with the **tubes at the bottom**, never with the tubes at the top above the pump.

A position to **avoid**: the radiator mounted **at the top with the tubes pointing upwards** and the pump higher than the radiator itself — a typical situation in some very compact cases. And **rear** mounting (120 mm at the back) is only suitable for small AIOs.

### 8.2 The procedure

**Step 1 — Mount the fans on the radiator**, deciding the direction of the airflow (fans have arrows stamped on the housing: one indicates the air direction, the other the rotation). In top exhaust, the fans push air **through the radiator outwards**; they can be underneath (to push) or on top (to pull) — underneath is more common for space reasons.

**Step 2 — Screw the radiator+fan assembly to the case.** The screws that go through the radiator are **long and dangerous**: if they are too long, the tip will **puncture the fins and the liquid channel**, and the AIO is dead. Use **only the screws provided in the kit**, and distinguish between "short" ones (radiator screwed directly to the case) and "long" ones (radiator + fans together). When in doubt, screw **by hand, counting the turns**, and stop as soon as they meet resistance.

**Step 3 — Connect the fans** (to CPU_FAN/CPU_OPT or the kit's hub) and ensure that the tubes do not press against the RAM or the VRM heatsink.

---

## 9. GPU Installation

### 9.1 Which slot, and why it's not trivial

The motherboard has one, two, sometimes three **PCIe x16** slots — physically identical, long. They seem interchangeable. **They are not.**

The GPU goes into the **first PCIe x16 slot, the one closest to the CPU**. And here's why, which is the explanation missing in nine out of ten guides.

The **PCIe** bus operates on **lanes**: each lane is a point-to-point differential pair. A modern consumer CPU exposes a **fixed and limited** number of PCIe lanes — typically **20-28** usable — of which **16 are reserved for the graphics connection** and 4 (or 8) for the primary NVMe SSD. The rest of the connectivity (USB ports, SATA, network, secondary expansion slots) is managed by the **chipset**, which connects to the CPU via a single shared link.

This means that, on a consumer motherboard:
- the **first x16 slot** is **electrically x16** and is wired **directly to the CPU**;
- subsequent x16 slots are **physically x16** (the length is the same), but electrically they are **x4** or **x1**, and often pass through the **chipset**.

Putting a modern GPU into an x4 slot from the chipset means **strangling it**: bandwidth is drastically reduced and latency increases, with performance losses that can reach double-digit percentages in games (and much more in compute loads that continuously transfer data between RAM and VRAM). This is not a detail for purists: it's the kind of mistake that makes you buy an 800 euro GPU and get the performance of a 500 euro one.

The motherboard manual always states this, with notations like **"PCIEX16_1: x16 mode (CPU)"** and **"PCIEX16_2: x4 mode (Chipset)"**. Read it.

**Exceptions**, which exist and are legitimate:
- **AIO radiator or cooler clearance.** On some compact cases, the first slot is too close to something. In that case, an x8 slot from the CPU (if available, on boards that support x8/x8 splitting) is better than a GPU that doesn't fit.
- **Workstation/HEDT** (*High-End Desktop*, e.g., Threadripper) boards: here the CPU lanes are 48, 64 or more, and all slots are x16 electrical. The rule does not apply.
- **Airflow.** Some prefer the second slot to give a thick GPU some breathing room. It's a compromise: a few degrees less, halved bandwidth. Almost never worth it.

### 9.2 The procedure

**Step 1 — Remove the rear brackets.** Count how many **expansion slots** your GPU occupies (modern GPUs are 2, 2.5, or 3 slots thick) and remove the corresponding **metal brackets** from the back of the case, unscrewing them (**do not bend them until they break**: in cheap cases they are tear-off, and once broken they don't go back). Remove the **correct** ones: if the GPU goes into the first x16 slot and occupies 3 slots, you need to remove the corresponding bracket and the two below it.

**Step 2 — Open the slot clip.** At the end of the PCIe slot, towards the inside of the motherboard, there is a plastic **retention clip**. It must be lowered/pushed aside **before** inserting the GPU. This is a detail that is often missed, and forcing the GPU with the clip closed can break the slot.

**Step 3 — Insert.** Align the GPU's **gold connector** with the slot (the notch on the connector, as always, ensures the correct orientation) and the **bracket** with the holes on the back of the case. Press **straight down, with uniform force on both ends** of the card (one above the connector, one on the free end). The GPU slides in and the clip **snaps into place with a CLICK**.
Verification: look at the connector from the side — the gold contacts must be **completely hidden** in the slot, and the card must be **parallel** to the motherboard.

**Step 4 — Screw the bracket** to the case (one or two screws). Do not overtighten.

### 9.3 GPU power: the paragraph that can save your house

GPUs consume more than the PCIe slot can provide (the slot provides a maximum of 75 W), so they draw power directly from the power supply. There are two families of connectors.

**Classic family: PCIe 6+2 pin (commonly "8-pin").** Each 8-pin connector is certified for **150 W**; the 6-pin for 75 W. A GPU can have one, two, or three of these.

> **The daisy-chain rule.** Many power supplies provide PCIe cables with **two connectors on the same cable** (one at the end and one "mid-cable," called a *piggyback* or *daisy-chain*). These are convenient but **share the same conductors**. On a low-end GPU (only one power input, or two inputs but modest power consumption), this is perfectly fine. On a powerful GPU — typically **250 W and up**, or whenever the GPU requires **two or more connectors** — **use separate cables, one for each GPU input, starting from different power supply outlets.** A single cable carrying 300 W on conductors designed for 150 W will heat up, degrade, and in documented cases, **melt**.

**New family: 12V-2×6 (evolution of 12VHPWR).** This is the high-density connector introduced with the **PCIe 5.0 / ATX 3.0** standard: twelve power pins (six for 12V, six for ground) plus four signal pins (sideband), capable of carrying up to **600 W** from a single cable. The "2×6" is the revision that shortened the signal pins compared to the original 12VHPWR: if the connector isn't fully seated, the shorter pins lose sense contact first, so the power supply cuts delivery to 0 W instead of still allowing up to 150 W through a loose connection — a safety/detection fix, not a mechanical barrier to partial insertion.

> **SAFETY WARNING, the most serious in this book: the 12V-2×6 connector must be inserted ALL THE WAY IN, until the clip clicks, and must be visually verified.**
>
> The reason is physical and unforgiving. A connector inserted **partially** (even by a single millimeter) or **bent at too sharp an angle** immediately after exiting has **high contact resistance** on some pins. The power dissipated in a contact is **P = R × I²**: with currents in the order of 40-50 amperes, even a few milliohms of excess resistance produce tens of watts concentrated on a small piece of plastic and brass. The documented result, with abundant photos online, is the **melting of the connector** — on the GPU side, PSU side, or both — damaging the video card.
>
> **The rules:** (1) insert **until it clicks**, verifying that the **golden area** of the terminals is **no longer visible**; (2) **do not bend the cable** within **at least 35 mm** of the connector (this is the minimum distance specified by the standard) — if the side panel of the case presses on the cable forcing an immediate sharp bend, you have a design problem and need an angled cable or a certified 90° adapter; (3) use the **native power supply cable** if it is ATX 3.x, or the adapter provided with the GPU, never generic adapters of dubious origin; (4) **recheck** the connector after closing the case and after any movement of the PC.

**Final step — The anti-sag support.** Modern GPUs can weigh **up to 2 kilograms** and are supported by two points: the PCIe slot and the bracket. The consequence is **sag**: the card tilts downwards, permanently flexing the slot and the GPU's PCB. If you look at the mounted GPU **from the side** and see that the free end visibly drops compared to the slot, add a **support** (an adjustable stand resting on the bottom of the case, or a retractable bracket: many GPUs and cases include one). This is not just aesthetics: a heavy GPU left in flexion for two years can develop micro-cracks in the solder joints.

---

## 10. Complete Cabling, One by One

We're at the part that scares people because "there are fifty cables." In reality, there are nine types, and each has only one place to go. Let's proceed in order, from top to bottom.

### 10.1 ATX 24-pin (motherboard power)

The largest connector, **24-pin** (often in two separable pieces, 20+4). It goes in the only place it can go: the long connector on the **right edge** of the motherboard. It is polarized (the pins have different shapes) and has a **side clip** that must **snap into place**.
**Force:** considerable. The 24-pin requires a firm push, and beginners often leave it halfway. Push until the clip clicks into place. Verification: try to pull it; it should not come out.

### 10.2 EPS 8-pin (CPU power) — the most confusing cable of all

The **EPS** is an **8-pin** connector that powers the CPU's VRMs, and it is located **top left on the motherboard**, near the socket, often labeled **CPU_PWR1**, **ATX_12V**, or **EPS12V**.

> **The risk: The EPS 8-pin and PCIe 8-pin look identical.** They have the same number of pins, the same general shape, the same color (black). But the **pinout is different** (the 12 V and ground positions do not match) and **the shape of the individual pins is different**: some are square, others are beveled, precisely to prevent errors. **Never force an 8-pin connector.** If it doesn't slide in smoothly, you have the wrong cable. A PCIe forced into an EPS socket can **destroy the motherboard**.
>
> **How to always distinguish them:** Power supply cables are **labeled** — look for **"CPU"** or **"EPS"** on the connector itself, versus **"PCIe"** or **"VGA"**. If the labels are missing, look at the separation: PCIe is **6+2** (it splits into a 6-block and a 2-block); EPS is **4+4** (it splits in half). This is the foolproof method.

High-end motherboards have **two** EPS connectors (8+8, or 8+4). With consumer CPUs, **only one is almost always sufficient** for normal operation; the second is for extreme overclocking and very high currents. If you have two and the power supply has the cables, connect both: it doesn't hurt.

**Accessibility warning:** The EPS is in the most awkward corner of the case, often already covered by the cooler. **Connect it before** mounting the top radiator, or even before installing the motherboard if the case is cramped.

### 10.3 PCIe / 12V-2×6 (GPU power)

Already covered in section 9.3. I'll summarize the rule: **no daisy-chain on powerful GPUs; 12V-2×6 all the way, without tight bends within 35 mm.**

### 10.4 SATA power and SATA data

If you have a 2.5" SSD or a mechanical hard drive, you need **two** cables: one **SATA power** (flat L-shaped connector, wide, coming from the power supply, with multiple plugs on the same cable — here daisy-chaining is perfectly fine, power consumption is minimal) and one **SATA data** (small, L-shaped, going from the drive to a **SATA** port on the motherboard). Both have a clip. The SATA data cable is often included with the motherboard.
If you only have M.2 drives, **you don't need any of this**.

### 10.5 Front panel (F_PANEL) — the pin puzzle

This is the most hated set: **four or five thin cables, with two-pin connectors**, coming from the front of the case, which go into a block of **9-10 pins** on the **bottom right** edge of the motherboard, labeled **F_PANEL**, **PANEL1**, **JFP1**, or similar.

The cables are:

| Cable | Function | Polarity |
|---|---|---|
| **POWER SW** (or PWR_SW) | The power button: closes a momentary contact | **Indifferent** (it's a switch) |
| **RESET SW** | The reset button | **Indifferent** |
| **POWER LED** (sometimes in two pieces, + and −) | The power LED | **Matters**: the positive pole (colored wire) goes on the + pin |
| **HDD LED** (or H.D.D. LED) | The disk activity LED | **Matters** |
| **SPEAKER** (if present) | The buzzer that emits diagnostic beeps | Indifferent in practice |

**The key point:** **there is no universal standard for pin order.** Each motherboard manufacturer uses a slightly different layout. **The only source of truth is the diagram in the motherboard manual**, which shows the block pin by pin. Open it, keep it open, and connect them one by one with the help of a flashlight. This is the only part of the assembly where you cannot rely on intuition.

Good news: **making a mistake here won't break anything**. If you reverse an LED, that LED won't light up (and you just need to flip the connector). If you swap power and reset, the power button will perform a reset. No permanent damage. Some motherboards (especially ASUS) include a **Q-Connector** adapter: a block where you conveniently insert the cables **outside** the case, and then the whole thing plugs into the board. If it's there, use it.

**If your case has a power button but you can't figure out which one is POWER SW:** remember that, to turn on a PC, you just need to **short-circuit the two POWER SW pins for an instant** with the tip of a screwdriver. This is exactly what the button does. Useful for bench POST testing.

### 10.6 Front Panel Data Headers

**USB 3.0 / USB 3.2 Gen 1 header:** a **blue, wide, 19/20-pin** connector, with a guide notch. It goes into the corresponding header on the motherboard. **Caution:** it's rigid and the pins are thin; it only goes in one way but must be inserted straight. This is the connector that is most often **damaged by bending pins**, because it's on the edge of the board and awkward. Take your time.

**USB-C header (USB 3.2 Gen 2 / Gen 2×2):** a small, rectangular, "E-key" type connector, often labeled **USB 20G** or **USB-C**. It easily goes in one way. If your motherboard **does not have** this header, the front USB-C port on the case **will not work**: this is a compatibility check to perform during purchase.

**USB 2.0 header:** 9 pins (one missing acts as a key). Used for the control cable of many AIOs, for RGB hubs, for some card readers.

**HD Audio (or AAFP / F_AUDIO):** a 9-pin connector, very similar to USB 2.0 (**but NOT the same thing**: look at the label, "HD AUDIO"). It goes into the audio header, which is almost always in the **bottom left corner** of the motherboard, as far as possible from digital circuits (to reduce interference). This is the cable for the front headphone/microphone jacks.

### 10.7 Fans: PWM vs DC

Each fan has a **3 or 4-pin** connector, with a guide tab that ensures its orientation, and plugs into one of the **SYS_FAN** / **CHA_FAN** headers on the motherboard.

The difference between 3 and 4 pins is conceptually important:

| | **3 pin (DC)** | **4 pin (PWM)** |
|---|---|---|
| Pin | GND, 12V, Sense (tachometer) | GND, 12V, Sense, **PWM** |
| How speed is regulated | By varying the **voltage** (from 12V downwards) | By keeping 12V fixed and sending a variable duty cycle **control signal** |
| Low-speed regulation | Imprecise; below a certain voltage, the fan **stops and does not restart** | Precise and stable even at low RPMs |
| Compatibility | A 3-pin fan **works** on a 4-pin header, but only if the header is set to **DC mode** in the BIOS | A 4-pin fan on a 3-pin header will **always run at 100%** |

**Practical consequence:** if you have 3-pin fans that are always running at full speed and making noise, go into the BIOS, find the fan section (Q-Fan, Smart Fan, Fan Control), and set that header to **DC** instead of **PWM/Auto**. This is the solution to a problem that frustrates many people.

If you have more fans than available headers, use a **splitter** (a cable that splits; the tachometric signal comes from only one fan) or a **PWM hub** powered directly from the PSU with a SATA power connector (mandatory if you connect more than 3-4 fans: a motherboard header typically provides **1 A / 12 W**, and exceeding this can burn out the control circuit).

### 10.8 ARGB and RGB: the hardware-saving paragraph

This is where things really get destroyed, and the cause is that the two connectors look similar.

| | **RGB 12 V (4 pin)** | **ARGB 5 V (3 pin)** |
|---|---|---|
| Voltage | **12 volts** | **5 volts** |
| Pin | 4 (12V, G, R, B) | 3 (5V, Data, **empty**, GND) — it has an empty space! |
| What it can do | The entire strip changes color **together** | Each LED is **individually addressable** ("rainbow" effects) |
| Label on the mobo | RGB_HEADER, JRGB, 12V G R B | ARGB_HEADER, JRAINBOW, ADD_HEADER, **5V D G** |

> **NEVER CONNECT a 5V ARGB device to a 12V RGB header.** The connectors are **physically similar**, and on some headers (which have 4 pins), it is **possible to accidentally insert** a 3-pin connector. The result is that the device receives **more than double the voltage it is designed for**: the LED strip or fan **burns out instantly**, and with a certain probability, it takes the **motherboard's LED controller** with it, which is not repairable.
>
> **How to protect yourself:** the 3-pin ARGB header has an unmistakable characteristic: there are **4 positions but one pin is MISSING** (there's a hole). The ARGB connector has a corresponding blanking hole. Count the pins, **read the silkscreen on the board** (it always says "12V" or "5V" next to the header), and if in doubt, **do not connect anything and consult the manual page**. This is a case where thirty seconds of reading are worth a hundred euros of components.

### 10.9 Cable management

It's not (just) aesthetics. A bundle of cables running through the case in front of the cooler or GPU **obstructs airflow**, raises temperatures, and makes any future maintenance impossible.

There are three principles:
1. **Anything that can go behind the motherboard tray, goes behind.** Modern cases have a dedicated rear compartment and rubber grommets at strategic points. The cable exits the grommet closest to its destination, travels a few centimeters in view, and connects.
2. **Group and secure with zip ties**, without tightening them so much that they crush the cables. Secure them to the case's anchor points (eyelets, slots).
3. **Do not obstruct fan intake or cooler exhaust.** No cable should pass *in front* of the front fan or over the CPU cooler.

If the rear panel doesn't close, you have too much stuff behind it: redistribute, don't force it.

---

## 11. First Boot

### 11.1 Pre-Power-On Checklist

Before pressing the button, stop. Five minutes here are worth an hour later. Review each point:

1. **Is the CPU socket lever closed and latched?**
2. **Is the cooler securely fastened?** Try to move it: it should not rotate or wobble. (And: **did you remove the film from the cold plate?**)
3. **Is the cooler's fan/pump connected to CPU_FAN / AIO_PUMP?**
4. **Is the RAM in the correct slots (A2/B2) and are ALL clips closed?**
5. **Is the GPU in the first x16 slot, is the clip snapped shut, is the bracket screwed in?**
6. **Are all power cables connected?** 24-pin, EPS 8-pin (**don't forget it: it's the most common mistake by far**), PCIe/12V-2×6 for the GPU, SATA power.
7. **Is the 12V-2×6 inserted ALL THE WAY?** Look at it.
8. **Are there no screws, zip ties, or tools inside the case?** Turn the case upside down and shake it gently: if you hear rattling, search for them.
9. **No orphaned standoffs under the motherboard?**
10. **Is the switch on the back of the power supply set to "O"?** Keep it off until you've connected the power cable.

### 11.2 Connecting the Monitor: To the GPU, Not the Motherboard

It seems obvious and **one out of three people** make this mistake.

If you have a **dedicated GPU**, the monitor cable (DisplayPort or HDMI) goes into the **GPU's ports**, which are the **horizontal ones, at the bottom, on the video card's bracket**. The video ports **on the motherboard** (vertical, at the top, in the rear I/O cluster) belong to the CPU's **iGPU** and, by default, **are disabled when a dedicated GPU is present**.

Symptom of the error: the PC turns on, fans spin, LEDs light up, but **the monitor says "No Signal"**. Unjustified panic. Move the cable.

(Corollary: if your CPU **does not have** integrated graphics — many AMD X-series models and some Intel "F" models — the motherboard's video ports never work, and a dedicated GPU is **absolutely necessary**.)

### 11.3 The POST and How to Read Diagnostics

Turn it on: PSU switch to "I", then the case's power button.

The **POST** (*Power-On Self-Test*) is the self-diagnostic sequence that the firmware performs before handing control to the operating system. It initializes, in order: **CPU → memory → PCIe/graphics devices → boot devices**. If something fails, the POST **stops there**, and tells you where.

How it tells you depends on the motherboard:

**Diagnostic LEDs (EZ Debug LED / Q-LED).** Four LEDs on the edge of the motherboard, labeled **CPU**, **DRAM**, **VGA**, **BOOT**. During POST, they light up in sequence and turn off as each phase passes. **The LED that remains lit indicates the phase where the system got stuck**:

| LED lit solid | Meaning | What to check, in order |
|---|---|---|
| **CPU** | CPU has not been initialized | **EPS 8-pin connected?** Then: CPU properly seated? BIOS updated (CPU too new)? Socket pins intact? |
| **DRAM** | Memory is not responding | RAM inserted until double click? Correct slots (A2/B2)? Try **only one module**, in A2. Clear CMOS. |
| **VGA** | Graphics not detected | GPU inserted until click? **PCIe cables connected to GPU?** Try the other slot. |
| **BOOT** | All OK, but no bootable disk found | **Normal** if you haven't installed the OS yet! Otherwise: SSD detected in BIOS? Installation USB stick connected? |

A note of realism that avoids many panic attacks: on **first power-on** with DDR5 RAM, the system can take **from 30 seconds to several minutes** to POST, with the DRAM LED lit and a black screen, and perhaps one or two automatic reboots. This is **memory training**: the controller is calibrating dozens of electrical parameters for each module, and stores the result. **This is normal, and only happens the first time (and after every RAM change or clear CMOS).** Do not turn everything off after twenty seconds: **wait at least five minutes** before declaring failure.

**Q-Code / POST code display.** High-end motherboards have a small **two-digit hexadecimal** display that shows the code of the current phase. The code **it gets stuck on** identifies the problem with surgical precision; the complete table is **in the appendix of the manual**. Some recurring codes (vary between manufacturers — always check your manual): codes in the **0d/19/53/55** series typically indicate memory problems; **d6/d7** indicate absence of a graphics device; **A0/A2/AA** indicate that POST was successful and the system is proceeding to boot. If the display shows a solid **00** or **FF**, generally the CPU is not executing anything: suspect CPU power or unrecognized CPU.

**Beep code (buzzer).** If you have connected the **speaker**, a **single, short beep** means "POST passed, all good". Long/repeated sequences indicate errors (typically: repeated beeps = RAM; one long and two short = graphics). It's worth connecting it: it costs nothing and gives you information.

### 11.4 If it doesn't start: the systematic procedure

The golden rule of diagnostics: **change one variable at a time, and start with the simplest.** The temptation to dismantle everything and reassemble randomly is strong and only leads to confusion.

**Level 0 — The absurdities (really, check them).** Is the power supply switch on? Is the cable plugged into the outlet? Does the outlet work? Is the power button connector (POWER SW) on the correct pins? (If the PC shows **no** signs of life — not even an LED, not even a fan twitch — the culprit is almost always here, or it's the power supply.)

**Level 1 — Reseating.** Turn off, unplug, **hold the power button for 10 seconds** (discharges capacitors). Then:
- **RAM:** Remove all modules and reinsert **only one**, into slot **A2** (the second from the CPU). Test. If it doesn't start, try the same module in **B2**. If it doesn't start, try **the other module** in A2. This four-attempt matrix distinguishes a faulty module from a faulty slot from an insertion error, and resolves the vast majority of cases.
- **GPU:** Reseat, and recheck power cables.
- **EPS 8-pin:** Recheck it. Seriously. It's the most common error.

**Level 2 — Clear CMOS.** The **CMOS** is the memory (powered by the CR2033 coin cell battery) where the BIOS stores settings. An impossible setting (typically: an overly aggressive XMP/EXPO profile that your CPU can't handle) can prevent POST. *Clear CMOS* resets everything to factory defaults. This can be done in three ways, in order of convenience:
1. Press the **"Clear CMOS"** or **"BIOS Reset" button** on the rear panel (mid/high-end motherboards have this);
2. Short-circuit the two **"CLR_CMOS" / "JBAT1" pins** on the motherboard for 5-10 seconds (with a screwdriver or the included jumper), **with the PC off and unplugged**;
3. **Remove the CR2032 battery** for a couple of minutes, again with the PC off and unplugged.
After a clear CMOS, all settings (XMP, boot order, fan curves) return to default and must be reconfigured. And memory training restarts from scratch: **be patient on the subsequent first boot.**

**Level 3 — Breadboarding.** If nothing has worked, it's back to the bench. **Remove the motherboard from the case** (yes, everything) and reassemble it bare on its cardboard box, with **the bare minimum**: CPU, cooler, **one** RAM module, GPU, power supply, monitor. No hard drives, no case fans, no front USB, no LED strips. Power on by short-circuiting the POWER SW pins.
If it **starts this way**, the culprit was in the case: almost always an **orphan standoff** causing a short, or a poorly connected front panel cable, or a faulty peripheral. Reassemble **one piece at a time**, testing each time.
If it **doesn't start this way**, the culprit is among the five remaining components, and at that point, spare components are needed for substitutions (the part where a friend with a PC becomes invaluable).

---

## 12. Initial Configuration

The PC POSTs and displays something. You're not done: you've finished the **mechanical part**. Now comes the part where the computer becomes *yours*, and where you reclaim performance you've already paid for but, at factory settings, **are not using**.

### 12.1 Entering the BIOS/UEFI

On first power-on, repeatedly press **DEL** — or **F2** on some motherboards — as soon as the PC starts. You will enter the **UEFI** (*Unified Extensible Firmware Interface*, the modern successor to BIOS; the two terms are now used synonymously), the firmware configuration interface.

**First check: is everything there?** The initial screen (*EZ Mode*) shows you a summary. Verify:
- The **CPU** is recognized with the correct name;
- The **total amount of RAM** matches what you have installed (if you have 32 GB and it only sees 16, one module is not making contact: power off and re-seat);
- All **SSDs/hard drives** are listed;
- The **fan speed** is non-zero for the CPU;
- The **CPU temperature** is reasonable (at idle, below 50-60 °C on a well-assembled system; if you see 80-90 °C at rest, **power off immediately**: the cooler is not making contact, and the first thing to check is **the film**).

### 12.2 Activating XMP / EXPO: five seconds, ten percent performance

This is the single action with the best effort-to-benefit ratio in the entire process.

RAM modules, according to **JEDEC** specifications (the body that standardizes semiconductors), always start at a **conservative base frequency** — much lower than what you paid for. The 6000 MT/s kit you bought, once installed, runs at 4800 MT/s with relaxed timings. **The performance you paid for is there, but it's off.**

To turn it on, there is a profile stored in the module's **SPD** chip, which the BIOS can load with a click:
- **XMP** (*Extreme Memory Profile*) is the name of the profile on **Intel** platforms;
- **EXPO** (*EXtended Profiles for Overclocking*) is the **AMD** equivalent;
- some boards generically call it **D.O.C.P.** or **A-XMP**.

They are conceptually the same thing: a predefined set of frequency, timings, and voltages, tested by the memory manufacturer. **Find the option (it's prominently displayed in EZ Mode) and activate it.** The PC will restart and perform memory training again (again: **patience, it can take minutes**).

**Technically it's an overclock**, so: if after activation the system is unstable (crashes, blue screens, no POST), it's not a fault — it's your specific IMC that can't handle that profile. Solutions: try the **secondary** XMP/EXPO profile (often more conservative), or manually reduce the frequency by one step. And if it no longer POSTs: **clear CMOS**, and start over.

### 12.3 Updating the BIOS

If the PC is working, it is **advisable** to update the BIOS to the latest stable version: revisions after a platform's launch substantially improve memory compatibility and stability, fix bugs, and sometimes increase performance.

**The correct procedure** (commercial names vary: *EZ Flash*, *M-Flash*, *Q-Flash*):
1. On the **website of your motherboard manufacturer**, on the page **for your exact model** (pay attention to variants: a "B650 Gaming X" is not a "B650 Gaming X AX"), download the latest stable BIOS file;
2. Copy it to a **USB stick formatted in FAT32**, in the root directory;
3. Enter the BIOS, open the update utility **integrated into the BIOS itself** (never, ever use update utilities from within Windows: they are the historical cause of "bricked" BIOSes), select the file;
4. **DO NOT POWER OFF, DO NOT TOUCH ANYTHING** for the entire duration. A power interruption during the flash can render the motherboard unusable (unless it has a dual BIOS or Flashback).

After the update, settings will be reset: **re-do XMP/EXPO and fan curves.**

### 12.4 Fan Curves and Resizable BAR

**Fan curves.** In the dedicated BIOS section (Q-Fan Control, Smart Fan, Fan Xpert), you can define a **curve** for each header that links the speed (in % or RPM) to a reference temperature. The default is almost always too aggressive (noisy) or too sluggish. A sensible curve for the CPU: silent up to ~50 °C, gradual increase between 50 and 75 °C, maximum beyond 80 °C. Important: set a **response time** (*step up/down time*, *hysteresis*) of a few seconds, to prevent the fan from "pumping" up and down with every momentary temperature spike — the phenomenon that makes a PC annoying even when it's quiet on average. And remember: header on **DC** for 3-pin fans, **PWM** for 4-pin fans.

**Resizable BAR** (Re-Size BAR / Smart Access Memory on AMD). This is a PCIe feature that allows the CPU to access the **entire VRAM** of the GPU at once, instead of 256 MB windows at a time. The benefit in games ranges from none to **over 10%** depending on the title. It must be enabled **in the BIOS** (often two settings are needed: **Above 4G Decoding** to *Enabled* and **Re-Size BAR Support** to *Auto/Enabled*). It's free: enable it.

### 12.5 Operating system and driver installation

**Media preparation:** On another PC, download the official media creation tool (for Windows, the *Media Creation Tool* from the Microsoft website; for Linux, the distribution's ISO written to a USB stick with a tool like Rufus or balenaEtcher). You need a USB stick of at least 8 GB, which will be **formatted**.

**Boot:** Insert the USB stick into a **rear USB port** (more reliable than front ports during installation), restart, and select the USB stick from the **Boot Menu** (usually **F8**, **F11**, or **F12** — the board tells you at startup) or set it as the first boot device in the BIOS.

**During installation**, when it asks where to install, select your **NVMe SSD** (identifiable by size). If there are pre-existing partitions, delete them and use the unallocated space.

**After installation, driver order matters:**
1. **Chipset drivers** — first, always. Download them from the **motherboard** website (or directly from AMD/Intel, often more up-to-date). They teach the operating system how to communicate with the platform: power management, core scheduling, controllers.
2. **GPU drivers** — directly from the **NVIDIA / AMD / Intel** website, **not** from the disc included in the box (it's months old).
3. **Network, audio, and other drivers** — Windows Update installs many of them automatically; for the rest, check the motherboard website.

### 12.6 Testing: checking temperatures and stability

Don't assume a PC is good just because it turns on. A PC that turns on can still have a poorly mounted cooler, a wrong fan curve, or an unstable memory profile. Testing is the proof.

**The tools (all free):**
- **HWiNFO64** (in *Sensors only* mode): the window of truth. Shows temperatures, frequencies, voltages, and power consumption of each component, with **minimum/maximum/average** values. Keep it open during every test.
- **Cinebench** (R23 or later versions): a benchmark that loads **all CPU cores to 100%** for 10 minutes. It's the most immediate test: it gives you a score (comparable online with identical CPUs to yours, to understand if yours is performing as it should) and brings the CPU to its realistic peak temperature.
- **OCCT** or **Prime95**: more severe stress tests. Useful for verifying the stability of an XMP/EXPO profile (OCCT's *Memory* test, for an hour, is the standard validation) and the PSU's endurance.
- **FurMark** or **3DMark**: GPU load.
- **MemTest86** (booted from a USB stick, outside the operating system): the definitive RAM test. A complete pass, without errors, certifies that your memory is fine.
- **CrystalDiskMark** or **CrystalDiskInfo**: SSD speed and health.

**What to look for, and what is normal:**

| Component | At idle | Under full load | Alarm |
|---|---|---|---|
| **CPU** | ~30-50 °C | Up to 85-95 °C is **normal** on many modern CPUs, which are designed to operate at their thermal limit and regulate themselves | If it reaches the limit (95-100 °C) **within a few seconds** of starting the test → **cooler poorly mounted / film not removed** |
| **GPU** | ~30-45 °C | 65-80 °C typical (**VRAM/hotspot** can be higher) | > 85-90 °C sustained, or constant 100% fan speed |
| **NVMe SSD** | 35-50 °C | Up to ~70 °C in sustained writing | Visible throttling (speed drops) → heatsink/film |
| **Stability** | — | No crashes, no errors, no reboots | Even **a single** error in MemTest or a crash in OCCT = real problem, almost always XMP/EXPO |

An important clarification, because it generates unnecessary anxiety: **a modern CPU that hits 90 °C under Cinebench is not "faulty" nor "poorly mounted"**. Today's CPUs use opportunistic boost algorithms that push frequency and voltage **until they saturate the available thermal budget**: if the cooler is good, the CPU runs faster and still remains hot. What matters is not the absolute number, but **how long it takes to get there** (seconds = bad, minutes = normal), **if it stays there without dropping**, and **what frequency it maintains**. A 10-minute test that ends at 88 °C with full frequencies is a healthy system. A test that reaches 100 °C in five seconds and drops to base frequencies is a system with a mounting problem.

---

## Operational Summary — the assembly checklist

Keep this open next to your table. Do not move to the next step until the previous one is verified.

**BEFORE OPENING THE BOXES**
- [ ] **PH2 magnetic** screwdriver, zip ties, light, large non-textile surface
- [ ] Final compatibility check: **socket** / **BIOS that supports the CPU** (is **Flashback** available?) / **correct DDR RAM** / **cooler clearance** / **GPU length** / **PSU: wattage + connectors**
- [ ] Power cable **unplugged from the wall** at every stage of the work
- [ ] Discharge yourself by touching the case chassis (or anti-static wristband) and repeat after each break

**BENCH PRE-ASSEMBLY (mobo on its BOX, not on the anti-static bag)**
- [ ] **CPU:** lever open → align triangle → **placed**, not pressed (force = zero) → lever closed (force = high, elastic resistance **yes**, hard stop **no**)
- [ ] **RAM:** slots **A2/B2** (**read the manual**) → align notch → press at ends until **double click** → golden contacts **disappeared**
- [ ] **M.2:** **primary (CPU)** slot → **THERMAL PAD FILM REMOVED** → inserted at 30° → screw without forcing
- [ ] **Cooler:** backplate + standoff → **THERMAL PASTE always** (pea-sized dot in center) → **AIO: REMOVE FILM from cold plate** → tighten **crosswise, incrementally**, until snug → fan/pump on **CPU_FAN / AIO_PUMP**
- [ ] *(Recommended)* **Bench POST test** before putting everything in the case

**CASE**
- [ ] Standoffs: **one under each hole**, **no orphaned standoffs** (short circuit risk)
- [ ] **I/O shield** mounted in the correct orientation (if not integrated)
- [ ] Front cables pre-routed through grommets

**PSU**
- [ ] Fan **facing down** if there's a grilled filter, facing inwards if the bottom is closed
- [ ] **Only original cables for THAT PSU model** (never cables from other PSUs!)
- [ ] Modular cables connected **before** inserting it into the case

**MOBO AND RADIATOR**
- [ ] Mobo aligned with I/O shield, screwed in **crosswise**, **without overtightening**
- [ ] AIO Radiator: **top exhaust** (or front intake) with **tubes at the bottom** — **the pump should never be the highest point**
- [ ] Only use screws from the kit (screws that are too long will **puncture the radiator**)

**GPU**
- [ ] **First x16 slot** (the only electrically x16 from the CPU) — check manual
- [ ] Brackets removed, clip open, inserted until **click**, screwed in
- [ ] **NO daisy-chaining** on PCIe cables for powerful GPUs
- [ ] **12V-2×6 inserted ALL THE WAY IN**, no bends within **35 mm** — **recheck with case closed**
- [ ] Anti-sag support if the card sags

**CABLING**
- [ ] **24-pin** (clip clicks) — **EPS 8-pin** (top left: **it's "4+4", PCIe is "6+2"**) — **PCIe/12V-2×6** — **SATA power**
- [ ] **F_PANEL** according to the **manual's diagram** (wrong connection won't break anything, but it won't start)
- [ ] Headers **USB 3.0** (fragile pins!), **USB-C**, **USB 2.0**, **HD Audio** (do not confuse with USB 2.0)
- [ ] Fans: **CPU_FAN** mandatory; 3 pin = **DC**, 4 pin = **PWM** (set in BIOS)
- [ ] **ARGB 5V (3 pin, with a gap) ≠ RGB 12V (4 pin). NEVER mix them: everything will burn.**
- [ ] Cables behind the tray, zip ties, **no cables in front of fans or heatsink**

**FIRST BOOT**
- [ ] Pre-power-on checklist (lost screws? tools inside? EPS connected?)
- [ ] **Monitor connected to the GPU, NOT the motherboard**
- [ ] POST: interpret **CPU / DRAM / VGA / BOOT LEDs** or **Q-Code**
- [ ] **DDR5: the first POST may take minutes (memory training). WAIT.**
- [ ] If it doesn't start: **one variable at a time** → re-seat RAM → **only one module in A2** → **clear CMOS** → **breadboarding**

**CONFIGURATION**
- [ ] BIOS: everything detected? (CPU, **correct total RAM**, drives, fans, **healthy temperatures**)
- [ ] **Enable XMP / EXPO** (it's performance you've already paid for)
- [ ] Update BIOS (**from internal utility, never from Windows**) → re-do XMP and fans
- [ ] **Resizable BAR** + **Above 4G Decoding** = Enabled
- [ ] Fan curves with hysteresis; correct DC/PWM headers
- [ ] OS from USB → **chipset drivers FIRST**, then **GPU drivers from manufacturer's website**
- [ ] Testing: **Cinebench + HWiNFO** (CPU temperatures), **OCCT/MemTest86** (XMP stability), **FurMark/3DMark** (GPU)
- [ ] High peak temperatures ≠ fault. **Peak temperatures reached in a FEW SECONDS = poorly mounted cooler.**

---

*Note on data: the socket designations, chipsets, and connector models mentioned reflect the market status at the time of writing and are the most volatile part of this chapter; the physical procedures, forces, and assembly orders, however, have been substantially stable for over a decade. Always check your specific motherboard manual: it is the only authoritative source for slot, header, and pin layout.*

---

[← Previous](08-cooling.html) · [All chapters](./) · [Next →](10-cpu-workstation-hedt.html)
