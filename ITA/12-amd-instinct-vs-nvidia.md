---
title: "Capitolo 12 - GPU AMD Instinct per l'AI e il confronto Nvidia vs. AMD nel Datacenter"
parent: "Edizione italiana"
nav_order: 12
---

<details open markdown="block">
  <summary>Indice del capitolo</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

> **Nota sui Dati.** Questo capitolo tocca il segmento hardware più volatile dell'intera industria IT. Le architetture si succedono annualmente, i prezzi non hanno un listino pubblico e sono negoziati contratto per contratto, e le roadmap vengono riscritte ogni sei mesi. Tutti i numeri riportati sono aggiornati a metà 2026, e laddove il dato è intrinsecamente instabile (prezzi, disponibilità, generazioni non ancora spedite in volume), lo marco esplicitamente con la dicitura **[DATO VOLATILE]**. Al momento della stesura: MI300X e MI325X sono prodotti maturi e ampiamente disponibili; la serie MI350 (MI350X/MI355X) è in produzione da metà 2025; e la serie MI400 con la piattaforma rack-scale "Helios" è stata svelata al CES di gennaio 2026, con volumi attesi nella seconda metà del 2026. Lato Nvidia, Blackwell (B200/GB200) e Blackwell Ultra (B300/GB300) sono sul campo, mentre Vera Rubin (VR200) è entrata in produzione con spedizioni in volume attese nella seconda metà del 2026. Il metodo di ragionamento, tuttavia – come leggere un datasheet, come valutare un acceleratore, quali errori evitare – rimarrà valido molto più a lungo dei numeri.

---

## 1. La Linea AMD Instinct

### 1.1 Cos'è, in termini elementari

Quando parliamo di "GPU" (Graphics Processing Unit), la nostra mente va subito alla scheda video infilata nello slot PCIe del PC, con le ventole e un'uscita HDMI. Le AMD Instinct non sono quello. Sono processori acceleratori che condividono il DNA architetturale con le schede video – migliaia di unità di calcolo parallele che eseguono la stessa istruzione su molti dati diversi – ma hanno dismesso tutto ciò che serve a disegnare pixel: non hanno uscite video, non hanno unità di rasterizzazione, e (nelle generazioni recenti) non hanno motori di ray tracing. Sono macchine da moltiplicazione matriciale, e nient'altro.

La linea Instinct è stata lanciata nel 2016 come risposta di AMD alla linea Tesla di Nvidia (poi semplicemente ribattezzata "Data Center GPU"). Per molti anni, è stata una risposta debole: hardware onesto, software inutilizzabile, quota di mercato irrilevante. La svolta è arrivata nel 2023-2024, quando la scarsità globale di acceleratori per l'AI generativa ha improvvisamente reso interessante il secondo fornitore – e AMD si è trovata, per la prima volta, con un prodotto (MI300X) che, su una metrica cruciale, la memoria di bordo, batteva il campione del mondo.

**Definizione di due acronimi che useremo continuamente.** *HBM* (High Bandwidth Memory) è DRAM impilata verticalmente in torri di più die, saldata sullo stesso substrato del processore e connessa ad esso con bus molto ampi (migliaia di tracce). È molto costosa, consuma molta energia, ma offre una banda passante di un ordine di grandezza superiore rispetto alle GDDR delle schede da gaming. *TDP* (Thermal Design Power) è la potenza in watt che il sistema di raffreddamento deve essere in grado di dissipare continuamente; AMD nel datacenter usa più spesso il termine *TBP* (Total Board Power), che include l'intera scheda, memoria inclusa. Nel resto del capitolo, li tratterò come sinonimi pratici, perché su questi prodotti quasi sempre coincidono.

### 1.2 Posizionamento: Perché AMD Esiste in Questo Mercato

La posizione di Instinct può essere spiegata con una sola frase: **nessun acquirente razionale vuole un unico fornitore per la voce di spesa più grande del proprio budget.** Nel 2024-2026, un cluster di addestramento AI costa più dell'immobile che lo ospita, e Nvidia detiene una quota di mercato che, a seconda di come vengono contate le TPU di Google, varia tra l'80% e il 90%. Hyperscaler (Microsoft, Meta, Oracle, Amazon), laboratori di frontiera e governi hanno un enorme interesse strategico nell'esistenza di un secondo fornitore credibile: non necessariamente per acquistare in blocco, ma per **avere potere negoziale** ed evitare di essere ostaggi di un listino prezzi e di un programma di consegna decisi da terzi.

AMD sta giocando esattamente questa partita, e la sta giocando su tre leve:

1.  **Più memoria per GPU.** Questa è la leva principale, quella su cui AMD ha costruito la sua intera narrazione dal 2023. Se un modello con 70 miliardi di parametri entra in una singola GPU invece di due, l'architettura del servizio si semplifica, il costo di comunicazione tra le due GPU scompare, e il costo per token servito crolla.
2.  **Prezzo inferiore per unità di memoria e per FLOP.** AMD non può permettersi il premium di Nvidia, perché il software è inferiore: deve scontare. Questo sconto ha un nome preciso — *ecosystem discount* — e lo analizzeremo nella sezione 4.
3.  **Standard aperti.** Dove Nvidia ha NVLink (proprietario), AMD spinge UALink (consorzio); dove Nvidia ha InfiniBand, AMD spinge Ultra Ethernet; dove Nvidia ha CUDA (proprietario), AMD ha ROCm (open source). Questa è solo apparentemente una scelta ideologica: è la classica strategia dello sfidante che cerca di rendere una commodity il terreno su cui il leader ha costruito il suo fossato.

### 1.3 CDNA vs. RDNA: Perché Due Architetture Separate

Fino al 2018, AMD aveva una sola architettura GPU, GCN (Graphics Core Next), utilizzata sia per il gaming che per il calcolo. Era un compromesso e, come tutti i compromessi, non eccelleva in nessun campo: portava silicio dedicato alla grafica nel datacenter che nessuno usava, e portava un'enfasi sul calcolo in doppia precisione nel gaming che nessun videogioco ha mai richiesto.

Nel 2019, AMD ha fatto la scelta strutturale che ancora oggi definisce il suo portfolio: **biforcare l'architettura**.

-   **RDNA** (Radeon DNA) è la linea *consumer/grafica*. Ottimizzata per la latenza, il rendering in tempo reale, il ray tracing e il consumo energetico entro i 350-600 W di una scheda desktop. Utilizza memoria GDDR. Questo è ciò che si trova nelle serie Radeon RX 7000/9000.
-   **CDNA** (Compute DNA) è la linea *datacenter/calcolo*. Ottimizzata per il throughput puro, piena di unità matriciali (i *Matrix Cores*, l'equivalente funzionale dei Tensor Cores di Nvidia), affamata di larghezza di banda della memoria, alimentata da HBM, progettata per essere inserita in uno chassis server raffreddato da aria in ingresso a 40 °C o liquido.

**Perché è la scelta giusta.** Un die di silicio è un budget di area finito. Ogni millimetro quadrato speso in unità di rasterizzazione è un millimetro quadrato non speso in Matrix Cores. Per un cliente che acquista 8.192 acceleratori per addestrare un modello linguistico, ogni transistor dedicato alla grafica è denaro bruciato e watt sprecati. Nvidia fa esattamente la stessa cosa, meno esplicitamente: il die GB100 del datacenter Blackwell e il die GB202 della GeForce RTX 5090 sono chip completamente diversi, con la stessa etichetta commerciale "Blackwell".

**Conseguenza pratica per il lettore.** Una Radeon RX 7900 XTX e una Instinct MI300X non sono "la stessa cosa in due taglie". Sono parenti lontane. Il codice ottimizzato per una non è automaticamente veloce sull'altra, le funzionalità di basso livello differiscono (RDNA ha wavefront a 32 thread, CDNA a 64), e persino il supporto ROCm segue tempistiche diverse. **Errore tipico dell'acquirente: pensare "provo ROCm sulla mia Radeon e poi scalo su Instinct".** Il codice è portabile, ma le performance e i kernel ottimizzati no.

### 1.4 Come leggere la nomenclatura Instinct

L'acronimo è più semplice di quello Nvidia. Prendiamo `MI355X`:

| Elemento | Significato |
|---|---|
| `MI` | "Machine Intelligence." Marchio di famiglia, invariato dal 2016. |
| `3` (prima cifra) | **Generazione/serie.** MI100 → CDNA1, MI200 → CDNA2, MI300 → CDNA3, MI350 → CDNA4, MI400 → CDNA5. |
| `5` (seconda cifra) | **Posizione nella serie.** MI300 è il capostipite, MI325 e MI350 sono i "refresh" incrementali (tipicamente più memoria o memoria più veloce). |
| `5` (terza cifra) | Variante di prodotto all'interno della stessa famiglia (MI350X vs MI355X: stesso silicio, diverso envelope termico e raffreddamento). |
| `X` (suffisso) | Variante di fascia alta, tipicamente **solo GPU**, per carichi di lavoro AI/HPC. |
| `A` (suffisso) | Variante **APU**: core CPU x86 e core GPU con memoria unificata coesistono sullo stesso package (es. MI300A). |

Il suffisso è la parte più confusionaria: `X` = acceleratore puramente GPU; `A` = ibrido CPU+GPU. Nella serie MI400, AMD ha introdotto una terza dimensione di specializzazione, non più solo termica ma **funzionale** (vedi §2.6): MI430X per HPC ad alta precisione, MI440X/MI455X per AI a bassa precisione.

---

## 2. Le schede, una per una

Prima di entrare nel dettaglio, tre definizioni che verranno usate in ogni descrizione di scheda.

**OAM** (OCP Accelerator Module) è il formato fisico standardizzato dall'Open Compute Project per gli acceleratori da datacenter: un modulo rettangolare, senza dissipatore proprio, senza connettori di alimentazione a cavo, che si avvita su una baseboard (UBB, Universal Baseboard) tipicamente capace di ospitare otto di essi. È l'equivalente aperto del formato proprietario SXM di Nvidia. Un modulo OAM **non si può comprare e installare in un PC**: si compra all'interno di un server, punto.

**PCIe** (Peripheral Component Interconnect Express) è il bus di sistema standard. Esistono versioni PCIe delle GPU da datacenter, sotto forma di scheda add-in a doppio slot, ma sono sempre versioni depotenziate (meno watt, meno banda tra GPU) e servono casi di nicchia, non cluster seri.

**FLOPS** (Floating Point Operations Per Second) è la metrica di calcolo grezzo. Va sempre letta in congiunzione con: (a) la **precisione** (FP64, FP32, FP16, BF16, FP8, FP6, FP4 — meno bit, più operazioni al secondo, meno accuratezza numerica); (b) se il numero è **denso** o **sparso** (con la sparsezza strutturata, i produttori raddoppiano il numero dichiarato: due numeri di "PFLOPS" possono differire di 2× solo per questo, ed è il trucco di marketing più comune nel settore).

### 2.1 MI250X (CDNA2, 2021) — il capitolo storico

**Descrizione e funzione.** La MI250X è la scheda che ha dato ad AMD la sua prima vera vittoria simbolica nel supercomputing. Architettura CDNA2, prodotta a 6 nm da TSMC, adotta un design **MCM** (Multi-Chip Module): due die GPU completi — chiamati GCD, Graphics Compute Die — affiancati sullo stesso package e connessi tramite Infinity Fabric.

| Specifiche | Valore |
|---|---|
| Architettura | CDNA2, TSMC 6 nm |
| Configurazione | 2 GCD × 110 CU = 220 Compute Units |
| Memoria | 128 GB HBM2e |
| Larghezza di banda | ~3.2 TB/s |
| Vettore/Matrice FP64 | ~47.9 / ~95.7 TFLOPS |
| Matrice FP16 | ~383 TFLOPS |
| TBP | 500-560 W |
| Fattore di forma | OAM |

**La debolezza architetturale, ed è didatticamente importante.** I due GCD **non erano visti dal software come una singola GPU**: il sistema operativo vedeva due dispositivi distinti. I programmatori dovevano gestire esplicitamente la partizione del lavoro tra le due metà. Questo è esattamente il problema che AMD risolverà con MI300X, dove il design a chiplet diventa trasparente al programmatore. Ricordate questa distinzione: *chiplet visibile* vs *chiplet trasparente* è una delle differenze concettuali più importanti tra CDNA2 e CDNA3.

**Caso d'uso storico: Frontier.** Il supercomputer Frontier presso l'Oak Ridge National Laboratory (USA), operativo dal 2022, è stata la prima macchina al mondo a superare ufficialmente gli exaFLOPS in doppia precisione (10^18 operazioni al secondo in FP64). È costruito con oltre 37.000 MI250X accoppiati a CPU EPYC. Frontier è la prova che AMD era già in grado di realizzare hardware HPC di prima classe *prima* dell'ondata dell'IA: il problema di AMD non è mai stato il silicio, è sempre stato il software (§4).

**Prezzo indicativo.** **[DATI VOLATILI]** Fuori produzione per il nuovo mercato; i moduli si possono trovare sul mercato dell'usato/dismissione per qualche migliaio di euro. **Non compratelo.** 128 GB di HBM2e sembrano molti, ma senza il supporto FP8 e con uno stack software di quella generazione, è un vicolo cieco per l'IA moderna.

### 2.2 MI300X (CDNA3, fine 2023) — la scheda che ha cambiato la conversazione

Questa è la scheda che ha reso AMD rilevante nell'IA, e merita la discussione più lunga.

**Il design a chiplet 3.5D.** L'MI300X è, dal punto di vista dell'ingegneria del packaging, uno dei chip più complessi mai prodotti. Non è un die monolitico: è una torre.

- Alla base ci sono **4 IOD** (I/O Die, prodotti a 6 nm): contengono i controller di memoria, la cache condivisa (256 MB di "Infinity Cache") e il fabric di interconnessione interno.
- Sopra gli IOD, saldati con tecnologia di **hybrid bonding** (connessione diretta rame-rame, senza microbump, che consente una densità di connessione e latenze molto migliori), ci sono **8 XCD** (Accelerator Complex Die, a 5 nm), per un totale di **304 Compute Units attive**.
- Intorno a essi, su un interposer, **8 stack HBM3** per un totale di **192 GB**.
- Tutto questo per circa **153 miliardi di transistor**.

La parola chiave è "3.5D": stacking verticale (3D, XCD sopra IOD) combinato con posizionamento orizzontale affiancato su un interposer (2.5D, stack HBM accanto al complesso logico).

**Perché è importante.** A differenza dell'MI250X, qui i chiplet sono **trasparenti al software**: PyTorch vede *una* GPU con 192 GB. Il costo di questa trasparenza è pagato nell'hardware, dall'Infinity Fabric interno e dalla cache condivisa.

| Specifiche | MI300X |
|---|---|
| Architettura | CDNA3 (XCD da 5 nm + IOD da 6 nm) |
| Compute Units | 304 |
| Memoria | **192 GB HBM3** |
| Larghezza di banda | **5.3 TB/s** |
| FP16/BF16 (denso) | ~1.3 PFLOPS |
| FP8 (denso) | ~2.6 PFLOPS |
| Matrice FP64 | ~163 TFLOPS |
| TBP | 750 W |
| Fattore di forma | OAM (piattaforma a 8 GPU); esiste anche una nicchia MI300X PCIe |

**Il vantaggio della VRAM su H100: cosa significa realmente.** L'H100 SXM di Nvidia, suo diretto contemporaneo, ha 80 GB di HBM3 con 3,35 TB/s. Il MI300X ha **192 GB con 5,3 TB/s**: 2,4 volte la capacità, 1,6 volte la banda. Tradotto in pratica:

- Un modello da 70 miliardi di parametri in FP16 occupa ~140 GB solo per i pesi. Su H100, sono necessarie **due** GPU (con tutte le complicazioni del *tensor parallelism*, cioè lo splitting delle matrici tra GPU, che introduce comunicazione ad ogni layer). Su MI300X, **una** è sufficiente, e rimangono ~50 GB per la KV cache (la key-value cache, che è la memoria del contesto già processato, che cresce linearmente con lunghezza del contesto × numero di richieste concorrenti).
- Una piattaforma 8-MI300X offre **1,5 TB di HBM aggregata** contro i 640 GB di un nodo 8-H100. Per l'inferenza di modelli enormi (un DeepSeek o un Llama 405B in FP8), questa è la differenza tra "ci sta in un nodo" e "devo distribuirlo su due nodi via rete", che è un salto significativo in complessità e latenza.
- **L'inferenza di LLM durante la generazione è *memory-bound*, non *compute-bound*.** Questa frase andrebbe memorizzata. Per generare un token, la GPU deve leggere *tutti* i pesi del modello dalla memoria. Il collo di bottiglia è la banda di memoria, non le unità di calcolo, che rimangono in gran parte inattive. Ne consegue che **la banda HBM predice la velocità di generazione meglio dei TFLOPS di picco**. Ed è proprio questa l'area in cui AMD è competitiva.

**Prezzo indicativo.** **[DATI VOLATILI]** AMD non pubblica listini. Le stime di mercato per il MI300X in volume sono state nell'ordine di **$10.000-$15.000 per modulo** rispetto ai $25.000-$40.000 chiesti per un H100 SXM al picco della scarsità. Sul cloud, il noleggio orario di un MI300X è stato **15-40% inferiore** a quello di un H100 comparabile. Consideratelo un ordine di grandezza, non un prezzo.

**Casi d'uso.** Inferenza LLM ad alto volume; fine-tuning di modelli medio-grandi; HPC misto (forte in FP64). Meno adatto per: ricerca che dipende da custom CUDA kernel di terze parti (§4).

### 2.3 MI300A (l'APU) ed El Capitan

Il MI300A è la variante concettualmente più interessante e commercialmente più di nicchia. Prende il MI300X, rimuove due XCD (lasciando 6 XCD, 228 CU) e al loro posto mette **tre chiplet CCD con 24 core Zen 4** (la stessa CPU di EPYC). Aggiunge **128 GB di HBM3 condivisa in modo coerente tra CPU e GPU**.

**Cosa significa "coherent unified memory" e perché è un grosso problema.** Nel modello classico, la CPU ha la sua RAM e la GPU ha la sua VRAM. Per calcolare sulla GPU, i dati devono essere *copiati* dalla RAM alla VRAM tramite il bus PCIe, il calcolo eseguito, e poi copiati indietro. Nei carichi di lavoro HPC con molte fasi CPU/GPU alternate, questa copia domina il tempo di esecuzione ed è la fonte più comune di codice lento. Nel MI300A, **non c'è copia**: CPU e GPU vedono gli stessi indirizzi fisici sulla stessa HBM, con la coerenza mantenuta dall'hardware. Un'intera classe di ottimizzazioni (e bug) viene eliminata.

**TBP:** ~550 W nominali, fino a 760 W in configurazione boost. **Fattore di forma:** socket/OAM in nodi dedicati.

**El Capitan.** Il supercomputer del Lawrence Livermore National Laboratory, operativo da fine 2024, costruito su MI300A, è diventato il sistema più veloce al mondo nella lista TOP500, superando 1,7 exaFLOPS FP64 (**[DATI VOLATILI]**: le classifiche cambiano ogni sei mesi). È la seconda dimostrazione, dopo Frontier, che nel calcolo scientifico "vero", AMD non è il secondo vendor: è *il* vendor.

**Errore tipico.** Guardare la MI300A e pensare "128 GB unificati, perfetti per l'AI". No: per l'AI pura, la MI300X con 192 GB di memoria solo GPU è quasi sempre la scelta migliore. La MI300A eccelle nei codici HPC misti CPU/GPU (fluidodinamica, simulazione nucleare, meteo), dove la coerenza della memoria è più preziosa della capacità.

### 2.4 MI325X (aggiornamento CDNA3, fine 2024/2025)

Questa non è una nuova architettura: è **lo stesso silicio CDNA3 della MI300X con memoria migliore**. È un classico "aggiornamento di metà ciclo" e dovrebbe essere inteso come tale.

| Specifiche | MI325X | (per confronto) MI300X |
|---|---|---|
| Architettura | CDNA3 | CDNA3 |
| Memoria | **256 GB HBM3e** | 192 GB HBM3 |
| Larghezza di banda | **~6.0 TB/s** | 5.3 TB/s |
| Calcolo (FP8/FP16) | invariato | — |
| TBP | **1.000 W** | 750 W |

**Posizionamento rispetto a H200.** L'H200 è, analogamente, l'H100 con HBM3e: **141 GB a 4.8 TB/s**, 700 W. Il confronto è quindi 256 GB vs 141 GB e 6.0 vs 4.8 TB/s: AMD mantiene il vantaggio di capacità (1.8×) e aggiunge un vantaggio di larghezza di banda (1.25×), pagando con 300 W in più per modulo.

**Il punto chiave.** Il salto MI300X → MI325X è **+33% di memoria e +13% di larghezza di banda con +33% di potenza, senza alcun guadagno computazionale**. Questo è un aggiornamento che ha senso *solo* se il tuo collo di bottiglia è la memoria — cioè, ancora una volta, se stai facendo inferenza LLM. Se stai addestrando, la MI325X ti offre quasi nulla in più rispetto alla MI300X. **Errore tipico: acquistare l'aggiornamento perché il numero è più grande, senza aver profilato il proprio carico di lavoro.**

### 2.5 MI350X / MI355X (CDNA4, metà 2025)

Qui AMD cambia veramente architettura. CDNA4 su un processo a 3 nm, e soprattutto: **supporto nativo per FP6 e FP4**.

**Cosa sono FP8, FP6, FP4 e perché sono la novità.** Questi sono formati di numeri in virgola mobile a bassissima precisione: rispettivamente 8, 6 e 4 bit per numero, rispetto a 16 per FP16/BF16. Meno bit significano contemporaneamente tre cose: (1) il modello occupa meno memoria (un modello da 70B in FP4 pesa ~35 GB invece di 140); (2) meno byte da leggere dalla memoria per token generato, quindi **inferenza più veloce sul percorso limitato dalla memoria**; (3) le unità di matrice completano più operazioni per ciclo. Il costo è una perdita di accuratezza numerica, che può essere mitigata con sofisticate tecniche di quantizzazione (scale a blocchi, calibrazione, formati di "microscaling" come MXFP4/MXFP6). Il punto è che dal 2025 in poi, **la valuta dell'inferenza è FP4**, motivo per cui sia AMD (CDNA4) che Nvidia (Blackwell) hanno costruito silicio dedicato.

| Specifiche | MI350X | MI355X |
|---|---|---|
| Architettura | CDNA4, TSMC 3 nm | CDNA4, TSMC 3 nm |
| Memoria | 288 GB HBM3e | 288 GB HBM3e |
| Larghezza di banda | ~8 TB/s | ~8 TB/s |
| FP8 (denso) | ~5 PFLOPS | ~5 PFLOPS |
| FP4/FP6 (denso) | ~9-10 PFLOPS | ~10 PFLOPS |
| FP64 | ~72 TFLOPS | ~79 TFLOPS |
| TBP | **1.000 W (aria)** | **1.400 W (liquido)** |
| Raffreddamento | raffreddato ad aria | **solo raffreddato a liquido** |

**La differenza MI350X/MI355X è, essenzialmente, il raffreddamento.** Stesso silicio: la MI350X si adatta a un involucro raffreddato ad aria da 1.000 W e si inserisce nei data center esistenti; la MI355X spinge a 1.400 W ma **richiede raffreddamento a liquido** e quindi un data center attrezzato. Chi ha piastre fredde ottiene la MI355X e guadagna frequenze e throughput; chi ha solo aria ottiene la MI350X. **Questa è la prima volta in questo libro che il vincolo della *struttura* — non il chip — determina quale prodotto si può acquistare.** D'ora in poi, sarà sempre così.

**Confronto dichiarato con Blackwell.** AMD posiziona MI355X contro B200/B300 di Nvidia. I 288GB di HBM3e del MI355X corrispondono esattamente ai 288GB del B300 (Blackwell Ultra), e la larghezza di banda è equivalente (~8 TB/s). Il vantaggio di capacità che AMD aveva contro H100 **è svanito**: Nvidia ha risposto. Sui FLOPS FP4, il B300 dichiara ~15 PFLOPS densi contro i ~10 del MI355X: Nvidia è in vantaggio sulla potenza di calcolo grezza. **[DATI VOLATILI]** Nei benchmark MLPerf Inference indipendenti pubblicati nella primavera del 2026, il MI355X ha ottenuto il miglior risultato di sempre per AMD, posizionandosi a pochi punti percentuali dal B200 nei carichi di lavoro di inferenza server — un risultato che sarebbe stato impensabile due anni prima.

**Prezzo indicativo.** **[DATI VOLATILI]** Ordine di grandezza $25.000-$35.000 per modulo in volume, tipicamente venduto solo come piattaforma a 8 GPU. Un nodo completo a 8-MI355X con CPU EPYC, rete e chassis è nell'ordine di **centinaia di migliaia di euro**.

### 2.6 La serie MI400 e la piattaforma Helios (2026)

**[SEZIONE INTERAMENTE VOLATILE — dati dagli annunci del CES 2026, prodotto non ancora spedito in volume al momento della stesura.]**

Questo è il salto generazionale più ambizioso mai tentato da AMD, e cambia la natura stessa del prodotto: **AMD smette di vendere GPU e inizia a vendere rack.**

**La serie MI400 (CDNA 5, TSMC 2 nm)** per la prima volta presenta varianti *funzionalmente* diverse:

| Modello | Obiettivo | Caratteristica distintiva |
|---|---|---|
| **MI455X** | Addestramento e inferenza su larga scala | Il fiore all'occhiello. ~320 miliardi di transistor, **432 GB HBM4**, ~19.6 TB/s, ~40 PFLOPS FP4 / ~20 PFLOPS FP8. Ottimizzato per bassa precisione (FP4/FP8/BF16). |
| **MI440X** | Enterprise on-premise | Progettato per il server "normale" a 8 GPU da collocare in un rack aziendale esistente, non per la scala di rack. |
| **MI430X** | HPC e "AI sovrana" | **Mantiene FP32/FP64 completi.** Questa è la variante per il calcolo scientifico e per gli stati che desiderano i dati all'interno dei propri confini. Destinato a supercomputer come Discovery (Oak Ridge) e Alice Recoque (Francia). |

**La logica della specializzazione deve essere ben compresa**, perché è una tendenza generale nel settore: separando la variante AI (che non necessita di FP64) dalla variante HPC (che ne ha disperatamente bisogno), AMD **elimina la logica ridondante dal die** e migliora la sua efficienza energetica e il costo. Nvidia sta facendo la stessa cosa in un modo diverso (con il Rubin CPX specializzato nel *prefill*). L'era della "GPU generalista che fa tutto bene" sta finendo.

**Helios.** Questo è il rack. Non è un server: è un cabinet a doppia larghezza, integrato, raffreddato a liquido che AMD vende come unità di calcolo indivisibile:

- **72 acceleratori MI455X** + CPU **EPYC "Venice"** (Zen 6) + NIC (Network Interface Card) Pensando "Vulcano" da 800G;
- **31 TB di HBM4 aggregata**, con **1.4 PB/s** di larghezza di banda di memoria totale;
- **~2.9 exaFLOPS FP4** per l'inferenza e **~1.4 exaFLOPS FP8** per l'addestramento per rack;
- i requisiti di consumo energetico e di raffreddamento rendono necessario un datacenter di nuova generazione (nell'ordine di **centinaia di kW per rack**).

**Perché AMD ha dovuto costruire un rack.** Perché Nvidia lo ha fatto per prima (GB200 NVL72) e ha spostato il campo di battaglia. Quando 72 GPU sono cablate in un unico dominio di memoria coerente, il cliente non confronta più "GPU contro GPU": confronta "rack contro rack", e chi non ha un rack non è in gara. La roadmap continua con la **serie MI500 su CDNA 6 e HBM4E annunciata per il 2027**.

---

## 3. L'interconnessione AMD

### 3.1 Il problema, prima della soluzione

Una singola GPU non può addestrare un modello di frontiera. Ne servono migliaia, e devono scambiarsi continuamente dati: in ogni passo di addestramento distribuito, alla fine del calcolo dei gradienti, tutte le GPU devono sommare i propri gradienti con quelli di tutte le altre (un’operazione che si chiama *all-reduce*). Se questo scambio è lento, le GPU restano inattive ad aspettare, e tu stai pagando 40.000 € a scheda per farle riposare.

Da qui la distinzione fondamentale, che vale per entrambi i produttori:

- **Scale-up** (interconnessione "verticale", all'interno del nodo o all'interno del rack): pochi dispositivi, latenza bassissima, banda enorme, memoria condivisa in modo coerente. Questo è il dominio di NVLink e Infinity Fabric.
- **Scale-out** (interconnessione "orizzontale", tra nodi e tra rack): tanti dispositivi, usando una vera e propria rete (InfiniBand o Ethernet). La banda è un ordine di grandezza inferiore, la latenza è più alta.

La regola universale di progettazione: **mantenere il traffico più intenso all'interno del dominio scale-up e uscire in scale-out il meno possibile.**

### 3.2 Infinity Fabric e la piattaforma OAM a 8 GPU

**Infinity Fabric** (IF) è il *fabric* di interconnessione proprietario di AMD, nato nel 2017 con Zen per connettere i *chiplet* della CPU e poi esteso a ogni cosa: *chiplet-to-chiplet* all'interno del *package*, CPU-to-GPU sul *socket*, GPU-to-GPU sulla *baseboard*.

Nella piattaforma Instinct standard — **8 moduli OAM su una UBB** — le 8 GPU sono connesse tra loro da link Infinity Fabric in una topologia **all-to-all**: ogni GPU ha una connessione diretta con ognuna delle altre sette. Questo è architettonicamente elegante e ha un vantaggio pratico: **non serve un chip switch dedicato**. Nvidia, per ottenere lo stesso risultato, usa **NVSwitch**, chip di switching aggiuntivi.

- **Il vantaggio AMD:** meno silicio, meno costi, meno consumo energetico, topologia semplice.
- **La limitazione AMD:** un *all-to-all* non scala. Funziona perfettamente con 8 GPU. Con 72, il numero di connessioni dirette esplode (n·(n-1)/2) e diventa impossibile. **È proprio per questo che Nvidia, con NVSwitch, ha potuto costruire il GB200 NVL72 — 72 GPU in un singolo dominio NVLink — mentre AMD è rimasta ferma a 8 per un'intera generazione.** Se ricordate una sola frase da questo capitolo, ricordate questa: *il vantaggio strutturale di Nvidia nel 2024-2025 non era la GPU, era lo switch.*

### 3.3 NVLink: Cosa AMD deve battere

**NVLink** è l'interconnessione proprietaria di Nvidia. Nella generazione Blackwell, offre ~1.8 TB/s bidirezionali per GPU; nella generazione Rubin, aumenta a ~3.6 TB/s. Combinato con NVSwitch, crea un **dominio di memoria coerente attraverso 72 GPU**: dal punto di vista del programmatore, il rack NVL72 si comporta come una GPU gigante con memoria condivisa. È un profondo fossato tecnologico, e Nvidia lo difende: NVLink è chiuso, non concesso in licenza a terzi.

### 3.4 UALink: lo standard aperto anti-NVLink

**UALink** (Ultra Accelerator Link) è la risposta collettiva dell'industria. È un consorzio che riunisce AMD, Intel, Google, Meta, Microsoft, Broadcom, Cisco, HPE e altri, con un obiettivo dichiarato: **definire uno standard aperto per l'interconnessione acceleratore-acceleratore in scale-up**, in modo che un rack possa contenere acceleratori di diversi fornitori e switch di diversi fornitori, come già avviene con Ethernet nel networking.

**La situazione reale nel 2026, senza retorica.** Gli acceleratori MI400 saranno i primi a supportare UALink insieme a Infinity Fabric. Ma uno standard di interconnessione senza **silicio per switch** è una specifica, non un prodotto: affinché UALink funzioni veramente, sono necessari chip switch, attesi da fornitori come Astera Labs, Broadcom, Enfabrica e altri. Fino a quando non arriveranno in volume, i primi sistemi Helios utilizzeranno **UALink-over-Ethernet** — una soluzione funzionale ma non ciò per cui UALink è stato progettato. **Traduzione per l'acquirente: UALink è una promessa credibile con tempistiche incerte. Non basare un piano di acquisto su di essa oggi.**

**Ultra Ethernet** è il fratello sul lato scale-out: un consorzio parallelo che estende Ethernet con le funzionalità di latenza e affidabilità richieste dai cluster AI, per sostituire InfiniBand (proprietario di Nvidia dopo l'acquisizione di Mellanox). Qui, la posizione di AMD è più forte, perché Ethernet esiste già e le schede di rete (Pensando Pollara 400G, Vulcano 800G) sono prodotti reali.

---

## 4. Il punto debole: il software

Se hai letto le sezioni precedenti pensando "ma allora AMD è competitiva, perché nessuno la compra?", la risposta è tutta qui.

### 4.1 CUDA: capire il fossato prima di capire il ponte

**CUDA** (Compute Unified Device Architecture) non è il "driver di Nvidia". È uno stack completo accumulato in **quasi vent'anni**: un linguaggio, un compilatore, un runtime e soprattutto una **piramide di librerie** (cuBLAS per l'algebra lineare, cuDNN per le reti neurali, NCCL per la comunicazione multi-GPU, TensorRT-LLM per l'inferenza ottimizzata, CUTLASS per i kernel di matrice) su cui poggia *tutto* il software AI del mondo. E sopra a questo: milioni di righe di codice di ricerca, tutorial, risposte di Stack Overflow, tesi di dottorato, container pre-confezionati e un'intera generazione di ingegneri che ha imparato a programmare in questo modo.

Il fossato non è tecnico. È **sociale e cumulativo**. Ed è per questo che non può essere colmato con un chip più veloce.

### 4.2 ROCm: cos'è e dove si trova realmente

**ROCm** (Radeon Open Compute platform) è la risposta di AMD: uno stack open-source, funzionalmente equivalente a CUDA. A partire dal 2026, la linea attuale è la **7.x** (ROCm 7 ha introdotto miglioramenti dichiarati di oltre 3-4× sull'inferenza e l'addestramento rispetto a ROCm 6.0), con rilasci regolari e supporto Linux di prim'ordine.

**Dove ROCm funziona bene, oggi — e questo è cambiato molto negli ultimi 18 mesi:**

- **PyTorch**: Supporto ufficiale e upstream con build pronte all'uso. Nella stragrande maggioranza dei casi, un modello PyTorch in esecuzione su CUDA funziona su ROCm con zero modifiche al codice: `torch.cuda` continua a essere chiamato così anche su AMD (è un alias, per compatibilità).
- **vLLM e SGLang**: I due motori di inferenza LLM più utilizzati in produzione hanno un supporto ROCm ufficiale e maturo. AMD ha investito molto in questo, con kernel proprietari ottimizzati (la libreria **AITER**) che apportano molteplici miglioramenti al throughput nei backend di attenzione rispetto ai percorsi generici. Nel 2026, ROCm è entrato nella CI (Continuous Integration) ufficiale di vLLM: ogni commit viene testato su silicio AMD, il che significa che le regressioni vengono individuate prima di raggiungere l'utente. Questo è un enorme cambiamento di status, da "un porting della comunità" a "una piattaforma di prima classe".
- **Inferenza LLM standard**: Su carichi di lavoro PyTorch + vLLM senza kernel personalizzati, misurazioni indipendenti collocano ROCm su MI300X/MI355X **intorno al 90-95% del throughput della controparte Nvidia** — e con batch grandi, il divario si restringe ulteriormente. **[DATI VOLATILI]**
- **llama.cpp / Ollama**: Funzionano, anche su schede consumer.

**Dove ROCm soffre ancora, ed è onesto dirlo:**

- **Kernel CUDA personalizzati di terze parti.** Questo è **il** problema, quello che causa il fallimento delle migrazioni. Molto codice di ricerca — e non poche librerie di produzione — distribuisce kernel CUDA scritti a mano, a volte con assembly PTX o intrinsics specifici di Nvidia. Quel codice **non si porta automaticamente**. Ci si trova a scegliere tra: cercare un fork compatibile, riscrivere il kernel o aspettare che la comunità lo faccia. Se il vostro progetto dipende da una libreria di nicchia con kernel personalizzati, **la migrazione ad AMD può bloccarsi su un singolo file `.cu`**.
- **TensorRT-LLM e l'ecosistema di ottimizzazione Nvidia** non hanno un equivalente ROCm completo. Con batch molto piccoli (inferenza a bassa latenza, un utente alla volta), dove TensorRT-LLM offre le migliori prestazioni, Nvidia mantiene un significativo vantaggio di throughput.
- **FlashAttention**: Esistono porting ROCm che funzionano, ma tendono a rimanere indietro rispetto alla versione CUDA di settimane o mesi. Se avete bisogno delle ultime funzionalità per l'addestramento, aspettatevi attriti.
- **Documentazione e casi limite.** Molto migliorata, ma la densità di risposte pronte su Internet rimane di un ordine di grandezza inferiore. Quando si ha un errore CUDA oscuro, lo si cerca su Google e si trovano tre persone che lo hanno già risolto nel 2022. Su ROCm, spesso, si trova un problema aperto su GitHub. **Questo si traduce in ore-uomo, e le ore-uomo hanno un costo.**
- **Windows e macOS**: Per un lavoro serio di ML, **solo Linux**. Ubuntu LTS o RHEL. Senza qualificazioni.

### 4.3 HIP: Il Ponte

**HIP** (Heterogeneous-Compute Interface for Portability) è il livello di compatibilità. Sintatticamente è quasi un clone di CUDA: `cudaMalloc` diventa `hipMalloc`, `__global__` rimane `__global__`, i concetti (griglia, blocco, thread, memoria condivisa) sono gli stessi. AMD fornisce **`hipify`**, uno strumento di traduzione automatica da sorgente a sorgente che converte il codice CUDA in codice HIP.

**Come funziona, realisticamente:**

- Per il codice CUDA "idiomatico" che utilizza API standard: **la conversione automatica riesce nella stragrande maggioranza dei casi.**
- Per il codice che utilizza intrinsics architetturali specifici di Nvidia, warp shuffle con ipotesi sulla dimensione del warp (32 su Nvidia, 64 su CDNA!), assembly PTX inline o le ultime API di Tensor Core: **la conversione fallisce, o peggio, riesce ma produce codice corretto ma estremamente lento.**

Il punto sottile, e cruciale: **HIP garantisce la portabilità, non le prestazioni.** Un kernel tradotto meccanicamente funzionerà. Un kernel *veloce* su CDNA deve essere riscritto tenendo conto di CDNA. Chiunque venda la migrazione ad AMD come "basta eseguire hipify" vi sta vendendo un'illusione.

### 4.4 Lo "sconto ecosistema": perché l'hardware AMD costa meno

Ora possiamo chiudere il cerchio economico. Perché una MI300X con 192 GB costa meno di una H100 con 80 GB?

**Perché il prezzo di un acceleratore non è il prezzo del silicio: è il prezzo del silicio più il valore dell'ecosistema, meno il costo del rischio.** AMD deve compensare, nel prezzo, tutto ciò che il cliente dovrà spendere in più:

- ore di ingegneria per portare e ottimizzare il codice;
- rischio di rimanere bloccati su una dipendenza non supportata;
- rischio di consegna, driver, regressioni;
- mercato dell'usato meno liquido e valore residuo inferiore;
- pool di talenti più piccolo (trovare un ingegnere in grado di ottimizzare i kernel HIP è più difficile che trovarne uno in grado di ottimizzare CUDA, e costa di più).

**Questo sconto è reale e dovrebbe essere accettato solo se si dispone dell'organizzazione per farlo.** Uno sconto del 25% sull'hardware è un ottimo affare per un hyperscaler con un team di 40 ingegneri di sistema che ottimizzano i kernel. È un cattivo affare per una PMI con due data scientist, perché quei due passeranno tre mesi a combattere con i driver, e quei tre mesi costano più dello sconto. **Regola: lo sconto ecosistema vale quanto la vostra capacità interna di assorbirlo.**

---

## 5. Nvidia vs. AMD nel datacenter: battono o non battono?

Facciamo un confronto onesto, criterio per criterio, senza pregiudizi.

### 5.1 VRAM per GPU — **AMD vince**, storicamente

Su questo, AMD ha avuto un vantaggio strutturale continuo. 192 GB vs. 80 (MI300X vs. H100), 256 vs. 141 (MI325X vs. H200). **Attenzione, però**: con Blackwell Ultra (B300, 288 GB), Nvidia ha **eguagliato** la MI355X. Il vantaggio si riapre con MI455X (432 GB HBM4) vs. Rubin VR200 (288 GB HBM4). **Il modello storico è chiaro: AMD apre il vantaggio, Nvidia lo chiude nella generazione successiva.** Non date per scontato che durerà.

### 5.2 Larghezza di banda della memoria — **sostanzialmente alla pari, con testa a testa generazionale**

5.3 vs. 3.35 TB/s (MI300X vs. H100: AMD vince). 8 vs. 8 (MI355X vs. B300: parità). 19.6 vs. 22 TB/s (MI455X vs. VR200: **Nvidia vince**, che con HBM4 a oltre 11 Gbps per pin ha spinto la frequenza più in alto). Questa è la metrica più importante per l'inferenza, ed è il fronte più conteso.

### 5.3 Prestazioni di training — **Nvidia vince, e non di poco**

Non tanto per il silicio, ma per: (a) FLOPS di picco più elevati in bassa precisione; (b) NCCL e l'intero stack di comunicazione collettiva, maturo e ottimizzato da anni; (c) il dominio NVLink a 72 GPU, che per l'addestramento di modelli enormi è un vantaggio architetturale, non incrementale; (d) FlashAttention, Transformer Engine, Megatron/NeMo e l'intero toolkit di training su larga scala, nato su CUDA. **Se dovete addestrare un modello all'avanguardia da zero, nel 2026 acquistate Nvidia.** Questa è la conclusione, per quanto scomoda.

### 5.4 Prestazioni di inferenza — **AMD è competitiva, e in alcuni scenari vince**

Poiché l'inferenza durante la generazione è limitata dalla memoria, e la memoria è il territorio di AMD. Più VRAM per GPU elimina interi assi di parallelismo, semplifica la distribuzione e serve un modello di grandi dimensioni su meno GPU. Su PyTorch + vLLM/SGLang, con grandi batch, il divario si riduce a percentuali a una cifra. **Se la tua attività consiste nel servire token in volume, AMD merita seria considerazione.**

### 5.5 TCO e Prezzo — **AMD vince sul prezzo di acquisto; il TCO dipende da te**

Il **TCO** (Total Cost of Ownership) è la somma di: acquisto + energia + raffreddamento + spazio + personale + software + costo del rischio. AMD vince chiaramente sul primo punto. Tipicamente perde sull'energia (TDP più elevati per la stessa generazione) e sul personale (§4.4). Il verdetto **dipende interamente dalla scala e dalla maturità del tuo team**.

### 5.6 Software — **Nvidia vince, chiaramente**

Vedi §4. Non c'è altro da aggiungere. È il motivo per cui questo capitolo non si conclude con "compra AMD".

### 5.7 Disponibilità — **la variabile impazzita**

In tempi di estrema scarsità, la domanda non è "quale è migliore", ma "cosa posso ottenere". Una MI355X consegnata in otto settimane batte una B300 consegnata in dieci mesi, indipendentemente dai benchmark. **[DATI VOLATILI ed estremamente variabili.]** Questo è, storicamente, il motivo principale per cui AMD ha venduto Instinct.

### 5.8 Chi compra AMD, e perché

1. **Hyperscaler.** Microsoft, Meta, Oracle. Hanno i team per assorbire il costo del software, acquistano in volumi che giustificano ottimizzazioni dedicate e hanno un interesse strategico esistenziale a non dipendere da un unico fornitore. Comprano AMD **anche per avere qualcosa da mostrare a Nvidia durante le negoziazioni.** È una leva negoziale, ancora più che tecnologia.
2. **Fattorie di inferenza.** Aziende il cui prodotto è servire token (fornitori di API, motori di inferenza cloud). Carico limitato dalla memoria, stack software standardizzato (vLLM), estrema sensibilità al costo per token. Il profilo ideale per AMD.
3. **HPC e settore pubblico.** Frontier, El Capitan, sistemi exascale europei. AMD è il campione qui, non lo sfidante — forte FP64, memoria unificata e nessun lock-in.
4. **"AI Sovrana."** Stati che vogliono la propria infrastruttura, spesso con un esplicito mandato politico a favore di standard aperti e contro il monopolio.

Chi **non** compra AMD: la startup con sei ingegneri e diciotto mesi di autonomia, che non può permettersi di perdere tre settimane su un kernel. E giustamente.

### 5.9 Tabella Comparativa Finale

**[TUTTI I DATI IN QUESTA TABELLA SONO VOLATILI.]** FLOPS in valori *densi*, non sparsi. Prezzi di mercato indicativi, non prezzi di listino.

| | **H100 SXM** | **H200 SXM** | **B200** | **B300 / GB300** | **VR200 (Rubin)** | **MI300X** | **MI325X** | **MI355X** | **MI455X** |
|---|---|---|---|---|---|---|---|---|---|
| Produttore | Nvidia | Nvidia | Nvidia | Nvidia | Nvidia | AMD | AMD | AMD | AMD |
| Architettura | Hopper | Hopper | Blackwell | Blackwell Ultra | Rubin | CDNA3 | CDNA3 | CDNA4 | CDNA5 |
| Anno | 2022 | 2023 | 2024-25 | 2025-26 | H2 2026 | 2023 | 2024-25 | 2025 | H2 2026 |
| Memoria | 80 GB HBM3 | 141 GB HBM3e | 180-192 GB HBM3e | 288 GB HBM3e | 288 GB **HBM4** | 192 GB HBM3 | 256 GB HBM3e | 288 GB HBM3e | **432 GB HBM4** |
| Larghezza di banda | 3.35 TB/s | 4.8 TB/s | 8 TB/s | 8 TB/s | **~22 TB/s** | 5.3 TB/s | 6.0 TB/s | 8 TB/s | ~19.6 TB/s |
| FP8 (denso) | ~2 PF | ~2 PF | ~4.5 PF | ~5 PF | ~35 PF (NVFP4 train) | ~2.6 PF | ~2.6 PF | ~5 PF | ~20 PF |
| FP4 (denso) | — | — | ~9 PF | ~15 PF | **~50 PF** | — | — | ~10 PF | ~40 PF |
| FP64 | 34 TF | 34 TF | ~40 TF | ridotto | ridotto | 163 TF | 163 TF | 79 TF | (MI430X) |
| TDP/TBP | 700 W | 700 W | 1.000 W | ~1.400 W | ~1.800-2.300 W | 750 W | 1.000 W | 1.400 W | ~2.300+ W |
| Raffreddamento | aria/liquido | aria/liquido | aria/liquido | **liquido** | **solo liquido** | aria | aria | **liquido** | **solo liquido** |
| Scalabilità | NVLink 4 | NVLink 4 | NVLink 5 | NVLink 5 (NVL72) | NVLink 6 (NVL72) | Infinity Fabric (8 GPU) | IF (8) | IF (8) | IF + **UALink** |
| Rack-scale | HGX 8× | HGX 8× | GB200 NVL72 | GB300 NVL72 | VR200 NVL72 | — | — | — | **Helios (72)** |
| Prezzo indicativo | $25-30k | $30-35k | $30-40k | $40k+ | n.d. | $10-15k | $15-25k | $25-35k | ~$30k (stime) |
| Software | CUDA ★★★★★ | ★★★★★ | ★★★★★ | ★★★★★ | ★★★★★ | ROCm ★★★☆☆ | ★★★☆☆ | ★★★★☆ | ★★★★☆ |

**Come leggere questa tabella.** Guarda le ultime tre righe. Prezzo e software raccontano tutta la storia: AMD offre più memoria per dollaro, Nvidia offre meno attrito per dollaro. La scelta è in ultima analisi una scommessa su **quale delle due risorse—denaro o tempo dei tuoi ingegneri—è più scarsa nella tua organizzazione.**

---

## 6. Guida alla Decisione Finale per una Build AI (Parte Workstation)

Concludiamo scendendo dalle nuvole del datacenter al desktop, perché è lì che il 99% dei lettori di questo libro dovrà prendere una decisione reale. I prezzi sono **[DATI VOLATILI]** e nel 2026 sono stati ulteriormente distorti dalla scarsità di memoria: considerali ordini di grandezza.

### 6.1 La Domanda Zero: Comprare o Noleggiare?

Prima di scegliere un livello, poniti questa domanda. **Il cloud è conveniente quando l'utilizzo è basso, variabile o incerto. L'acquisto è conveniente quando l'utilizzo è alto, costante e prevedibile.**

Il calcolo è semplice: prendi il costo orario dell'istanza cloud equivalente (**[DATI VOLATILI]** — approssimativamente, nel 2026, pochi euro all'ora per una GPU di classe H100/H200 su provider specializzati, di più sui grandi cloud generalisti), moltiplicalo per le ore che *effettivamente* utilizzerai al mese, e confrontalo con (prezzo hardware / 36 mesi) + elettricità + il tuo tempo di amministrazione.

Il risultato tipico è che **il punto di pareggio si aggira intorno al 30-50% di utilizzo continuo**. Se la tua GPU rimanesse accesa e carica meno di 8 ore al giorno, il cloud quasi sempre vince. Se la tieni al 90% per due anni, l'acquisto vince chiaramente.

Ma ci sono tre ragioni **non economiche** per comprare che sono spesso decisive: **data privacy** (se i dati dei clienti non possono uscire dai vostri locali — spesso il caso in Italia sotto GDPR — il public cloud è precluso o estremamente complicato); **latenza e disponibilità** (la vostra GPU è vostra, sempre, senza code o quote); e **apprendimento** (assemblare, configurare, rompere e riparare un sistema insegna cose che affittare un'istanza non farà mai).

### 6.2 Livello A — sotto i €3.000: il PC consumer di fascia alta

**Chi siete:** uno sviluppatore, un ricercatore, uno studente. Volete fare il fine-tuning di piccoli modelli, eseguire inferenze locali, sviluppare e prototipare, e magari giocare la sera.

**Cosa comprate:** una singola GPU consumer di fascia alta (RTX 5090 con 32 GB, o una RTX 4090 usata con 24 GB), una CPU consumer (Ryzen 7/9 o Core i7/i9), **64-128 GB di RAM di sistema** — regola empirica: RAM di sistema ≥ 2× VRAM totale, perché è necessaria per il caricamento, la quantizzazione e la pre-elaborazione — e un NVMe da 2 TB.

**Cosa potete fare:** inferenza di modelli fino a ~30 B in quantizzazione a 4 bit; fine-tuning **LoRA** (Low-Rank Adaptation: solo un piccolo insieme di matrici aggiuntive vengono addestrate invece di tutti i pesi, riducendo drasticamente i requisiti di memoria) su modelli fino a 7-13 B; tutta la computer vision che volete; sviluppo di pipeline che poi verranno eseguite altrove.

**Cosa NON potete fare:** addestrare da zero; fine-tuning completo di modelli grandi; servire in produzione con vera concorrenza.

**Errore comune:** comprare due GPU consumer da 16 GB e aspettarsi di ottenere 32 GB. La **VRAM non si somma** se il modello non è progettato per essere partizionato. Per l'inferenza LLM, una singola GPU da 24 GB quasi sempre batte due GPU da 12 GB. **Massimizzate la VRAM di un singolo chip, non il numero di chip.**

### 6.3 Livello B — €5.000-€15.000: La Workstation Threadripper con 2 GPU

**Chi siete:** Un consulente, un piccolo team, una startup pre-seed. Avete bisogno di una macchina che possa gestire carichi di lavoro seri e funzionare per una settimana di fila.

**Cosa comprate:** Un **Threadripper** (non un Ryzen) o uno Xeon W. Perché? Per le **lane PCIe** (le "corsie" del bus: ogni GPU idealmente vuole x16 lane; una CPU consumer offre ~24-28 lane utilizzabili in totale, il che significa che con due GPU si scende a x8 ciascuna e il collo di bottiglia si sposta sul bus quando le GPU devono comunicare). Un Threadripper offre 48-92, un Threadripper PRO fino a 128. Poi: 2 GPU (2× RTX 5090, o 1-2 RTX PRO Blackwell 48-96 GB se il budget lo permette), 128-256 GB di RAM ECC, un alimentatore da 1.600 W, un case full-tower con un serio flusso d'aria.

**Errore comune:** installare due GPU consumer *open-air* (con ventole aperte, cioè la stragrande maggioranza dei modelli da gaming) adiacenti l'una all'altra. **Vedi §6.5: questo è l'errore più costoso e più comune in tutto questo livello.**

### 6.4 Livello C — €15.000-€50.000: La Workstation Professionale

**Chi siete:** Un'azienda che fa AI seria ma senza ancora un datacenter.

**Cosa comprate:** Threadripper PRO o EPYC single-socket, e **2-4 RTX PRO 6000 Blackwell (96 GB ciascuna)**. Quattro di queste vi danno **384 GB di VRAM su una singola macchina**, con raffreddamento a blower specificamente progettato per l'installazione affiancata, ECC, driver certificati e — un dettaglio non trascurabile — una licenza che permette l'uso in datacenter, che le schede GeForce **non hanno** (i termini di licenza di Nvidia proibiscono l'uso delle schede GeForce nei datacenter, con limitate eccezioni: se fornite servizi a clienti, consultate un avvocato).

**L'alternativa specializzata:** Sistemi desktop basati su superchip (DGX Spark, DGX Station e derivati OEM), che offrono grandi quantità di **memoria unificata coerente** (fino a centinaia di GB) su un singolo chip ARM+GPU. Sono

**3. Alimentatore (PSU) — e calcolo dei transitori.**
Sommare i TDP e aggiungere **almeno il 30-40% di margine**. Non per generica prudenza, ma perché le GPU moderne hanno dei **picchi transitori** (spike di microsecondi) che possono raggiungere il doppio del TDP nominale e far scattare la protezione di sovracorrente (OCP) di un alimentatore sottodimensionato — con conseguente riavvio inaspettato, di solito nel bel mezzo di una sessione di training di trenta ore. Due RTX 5090 (2 × 575 W) più un Threadripper (350 W) più il resto richiedono **1.600 W minimo**.
Sopra i ~1.600-2.000 W, si entra nel territorio dei **doppi PSU**: due alimentatori sincronizzati con un "add2psu" o una scheda madre server preconfigurata. Regola non negoziabile: **una singola GPU non deve mai essere alimentata da due PSU diversi** (differenze di potenziale sui riferimenti di massa → danni). Una GPU, un alimentatore.
E, per i connettori: **12VHPWR/12V-2x6 va inserito fino in fondo, con un click, e non va piegato entro 35 mm dal connettore.** I casi di fusione del connettore sono, nella quasi totalità dei casi, casi di inserimento incompleto.

**4. Raffreddamento — blower vs. open-air, l'errore da 3.000 €.**
Questa è la differenza più importante e meno conosciuta di tutta la sezione.
- Una GPU **open-air** (il design a 2-3 ventole di quasi tutte le schede gaming) aspira aria dal basso e la **soffia lateralmente dentro il case**. Progettata per una singola GPU in un case ben ventilato.
- Una GPU **blower** (ventola radiale) aspira aria e la **espelle interamente fuori dal case attraverso la staffa posteriore**. Progettata per essere affiancata ad altre.

Se installate **due GPU open-air adiacenti**, la scheda superiore aspira l'aria calda espulsa da quella inferiore. Il risultato non è un guasto: è il **thermal throttling**, cioè la riduzione automatica delle frequenze per non superare i limiti di temperatura. La scheda superiore girerà stabilmente 15-25 °C più calda e perderà una frazione significativa di prestazioni, **silenziosamente e permanentemente**. Avrete pagato due GPU e ne otterrete una e mezza, senza alcun avviso.

**Soluzioni, in ordine di preferenza:**
- comprare **GPU professionali con dissipatori blower** (le RTX PRO sono progettate esattamente per questo — è uno dei motivi per cui costano di più, e per cui in setup multi-GPU sono spesso più economiche a parità di prestazioni ottenute);
- **raffreddamento a liquido** con blocchi dedicati (blocco su ogni GPU, radiatori generosi): efficace, costoso, e introduce un punto di fallimento idraulico;
- **distanziare fisicamente** le schede con riser, in un case molto grande, con ventole di scarico dedicate;
- **limitare la potenza** (`nvidia-smi -pl`): controintuitivo, ma limitare una GPU all'80% del suo TDP costa tipicamente solo il 5-10% in prestazioni e riduce drasticamente il calore. In setup multi-GPU, è quasi sempre la scelta razionale.

**5. L'impianto elettrico domestico — il limite dei 3 kW in Italia.**
E qui arriviamo al vincolo più concreto e più ignorato, che nessun sito americano vi menzionerà mai.

Il contratto di fornitura elettrica domestica standard in Italia è di **3 kW** (con una tolleranza tipica del 10%, quindi ~3.3 kW prima che scatti il limitatore). Una presa Schuko su un circuito da 16 A, 230 V gestisce nominalmente ~3.6 kW.

Fai i conti: una workstation con **due RTX 5090** a pieno carico assorbe realisticamente **1.400-1.800 W dalla presa** (l'assorbimento a monte è maggiore dell'output a valle perché l'alimentatore ha un'efficienza dell'88-92%). Aggiungi il monitor, e sei a ~1.900 W. **Questo lascia ~1.100 W per tutto il resto della casa.** Il forno elettrico richiede 2.000. La lavatrice durante il riscaldamento, 2.000. L'aria condizionata, 1.000-1.500. L'asciugacapelli, 1.800.

**Conclusione operativa: con un contratto da 3 kW, una workstation multi-GPU di fascia media farà scattare il tuo interruttore ogni volta che qualcuno in casa accende qualcosa.** E non scatterà a mezzogiorno: scatterà alle tre del mattino, nel bel mezzo di un fine-tuning, perché si è acceso lo scaldabagno.

**Cosa fare, in ordine:**
- **Aumentare la potenza contrattuale** a 4,5 o 6 kW. Questo si fa richiedendolo al proprio fornitore, comporta un adeguamento degli oneri fissi in bolletta, e ha senso a partire dalla fascia B.
- **Controllare il circuito**: una workstation da 1.800 W dovrebbe essere su un **circuito dedicato**, non condiviso con la cucina. Se il tuo sistema ha un unico circuito da 16 A per metà dell'appartamento, chiama un elettricista.
- **Considerare un gruppo di continuità (UPS)** con adeguata potenza *reale*: tieni presente che gli UPS sono venduti in VA (volt-ampere), non watt. Un UPS da "1.500 VA" tipicamente eroga ~900-1.000 W reali. Dimensionarlo in base ai watt.
- **Oltre ~3 kW per la macchina, la workstation smette di essere un elettrodomestico**: è necessaria una linea industriale, e a quel punto, hai implicitamente risposto alla domanda "comprare o cloud".

**6. Carico termico nella stanza.**
Ultimo punto, e il più sottovalutato: **tutta la potenza elettrica assorbita da un computer viene convertita in calore.** Non "quasi tutta": tutta. Una workstation da 1.800 W è, da una prospettiva termodinamica, **una stufa da 1.800 W costantemente accesa**. In una stanza di 15 m² senza aria condizionata, in estate, questo è fisicamente insostenibile — per la macchina e per te. Metti in conto un condizionatore d'aria, e ricorda che anche quello consuma dal tuo contratto da 3 kW.

---

## 7. Riepilogo Operativo — Lista di Controllo Decisionale

### A) Scelta tra Nvidia e AMD nel datacenter

1. **Il mio carico di lavoro è di training o inferenza?**
   → *Training all'avanguardia* → **Nvidia**, senza discussioni.
   → *Inferenza di volume* → AMD è in lizza. Procedi.
2. **Il mio stack dipende da kernel CUDA personalizzati, TensorRT-LLM o librerie di nicchia?**
   → *Sì* → **Nvidia**. Lo sconto AMD non compenserà il porting.
   → *No, uso PyTorch standard + vLLM/SGLang* → AMD è una scelta seria. Procedi.
3. **Ho ingegneri di sistema in grado di risolvere autonomamente un problema di driver o un kernel lento?**
   → *No* → **Nvidia**. Lo sconto sull'ecosistema si realizza solo con competenze interne.
   → *Sì* → Procedi.
4. **Il mio collo di bottiglia è la capacità della memoria GPU?**
   → *Sì* (il modello non ci sta, o ci sta a malapena) → **questo è il caso d'uso ideale per AMD.**
5. **Ho fatto un benchmark del MIO modello, con la MIA lunghezza di contesto e il MIO batch?**
   → *No* → **Fermati e fallo.** Nessun numero in questo capitolo sostituisce una misurazione sul tuo carico di lavoro. Quasi tutti i cloud offrono MI300X/MI355X a ore: **spendi 200€ per il noleggio prima di spendere 200.000€ per l'hardware.**
6. **Ho verificato la disponibilità effettiva e i tempi di consegna?**
   → La migliore GPU è quella che arriva.
7. **Ho considerato il potere negoziale?**
   → Un preventivo AMD in mano cambia il preventivo Nvidia. Questa è, per molte aziende, la ragione principale di questo intero capitolo.

### B) Lista di controllo della compatibilità per una workstation multi-GPU

- [ ] **Corsie PCIe**: somma le corsie richieste (GPU + NVMe + rete) e confrontale con le corsie *effettive* della CPU. Leggi il diagramma di allocazione degli slot nel manuale della scheda madre.
- [ ] **Spaziatura fisica**: verifica che ci sia aria tra le GPU. Misurato, non stimato.
- [ ] **Dissipatori**: **blower** (o liquido) se le GPU sono adiacenti. Mai due dissipatori open-air attaccati.
- [ ] **PSU**: somma dei TDP + 30-40% di margine. Cavi 12VHPWR completamente inseriti, non piegati vicino al connettore.
- [ ] **Doppio PSU** (se >1.600 W): una GPU = un alimentatore. Mai a cavallo.
- [ ] **RAM di sistema** ≥ 2× VRAM totale. ECC se la macchina deve funzionare per giorni.
- [ ] **Limite di potenza** impostato all'80% in multi-GPU: perdi 5-10%, guadagni stabilità e silenzio.
- [ ] **Contratto elettrico**: 3 kW **non bastano** oltre la fascia A. Richiedi un aumento a 4.5-6 kW.
- [ ] **Circuito dedicato** per la workstation, verificato da un elettricista.
- [ ] **UPS** dimensionato in **watt**, non in VA.
- [ ] **Aria condizionata della stanza**: la macchina è un riscaldatore. Trattala come tale.
- [ ] **Sistema operativo**: Linux. Ubuntu LTS. Sia su Nvidia che (soprattutto) su AMD.
- [ ] **Piano di uscita**: se tra 18 mesi avrai bisogno di 4× questa potenza, questa macchina è un investimento o un vicolo cieco? Se è un vicolo cieco, considera il cloud.

---

*Fine del capitolo.*

---

[← Precedente](11-gpu-nvidia-ai.html) · [Tutti i capitoli](./)
