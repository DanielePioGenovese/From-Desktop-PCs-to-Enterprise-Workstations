---
title: Errata
nav_order: 3
---

# Errata

Known corrections to *Hardware for Artificial Intelligence*.
This page covers both editions, English and Italian.

**Current version: v1.0 — no open errors at this time.**

If you find one, [open a report](https://github.com/DanielePioGenovese/From-Desktop-PCs-to-Enterprise-Workstations/issues/new/choose):
it is the most useful thing you can do for whoever reads after you. See
[how to report](CONTRIBUTING.md).

---

## Open errors

*None at the moment.* Confirmed reports appear here as soon as they are verified, and are
corrected in the text at the following release.

---

## Fixed before publication (v1.0)

Before release, every verifiable technical claim was checked: an automated pass flagged
**77 doubtful points**, each of which was then re-checked by hand against sources.
**73 were false positives** — largely cases where the automated checker simply did not
know about 2025/2026 hardware.

The **4 real errors** that emerged were corrected in the text before publication. They are
listed here for transparency: if you are holding a copy downloaded before the public
release, these are the passages not to trust.

### 1. Chapter 2 — USB4 on AMD 800-series chipsets

The text described USB4 as mandatory across the whole 800 series, contradicting its own
table a few lines further down.

**Corrected to:** mandatory on **X870/X870E**; on **B850** optional, but present on most
boards.

### 2. Chapter 9 — why the 12V-2×6 sense pins are shorter

The text said the shortening was meant to make partial insertion physically harder. It is
not: pin length offers no mechanical resistance.

**Corrected to:** it is a **detection** mechanism. If the connector is not fully seated,
the shorter pins lose sense contact first and the power supply cuts delivery to **0 W**,
instead of continuing to feed up to 150 W through a loose connection as could happen with
the original 12VHPWR.

### 3. Chapter 10 — Threadripper PRO 7995WX price

Listed at ~€7,000, understating it by 35-50%.

**Corrected to:** ~**€9,500-10,000+** (US MSRP $9,999; real EU retail €9,500-10,800).

### 4. Chapter 8 — Tjmax of Ryzen X3D CPUs

The text generalised that X3D parts run at a lower Tjmax. That holds for the 7000 series,
not for the whole line.

**Corrected to:** **7000-series** X3D parts do run cooler (~89 °C), but the **9800X3D is
back to ~95 °C**.

---

## What is not an error

Two things that get reported often and are in fact deliberate:

**The `[VERIFY]` / `[VERIFICARE]` markers** in chapter 2 are not forgotten notes. They flag
the points where data on the most recent chipsets was still moving at the time of writing
and should be reconfirmed against the official specifications of the individual model. The
chapter introduction explains this.

**Prices and product generations** refer to **July 2026** — including that year's DDR5
pricing anomaly, with RAM up 5-8× on normal levels. That they have since changed is not an
error in the book: the architectural concepts remain valid, the price lists do not. Check
them at the time of purchase.

---

## Credits

Anyone who reports a confirmed technical error is credited here, unless the report says
otherwise.
