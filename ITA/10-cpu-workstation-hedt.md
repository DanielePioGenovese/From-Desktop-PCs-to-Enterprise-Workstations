---
title: "Capitolo 10 - CPU per Workstation AI e HEDT"
parent: "Edizione italiana"
nav_order: 10
---

<details open markdown="block">
  <summary>Indice del capitolo</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## Introduzione: perché esiste questo capitolo

Nei capitoli precedenti abbiamo costruito, pezzo per pezzo, il modello mentale del PC consumer: una CPU al centro, una GPU attaccata allo slot principale, due moduli RAM, uno o due SSD NVMe, un alimentatore dimensionato con un ragionevole margine. In quel mondo, la domanda dominante è "quanti frame al secondo ottengo?" o "quanto tempo ci vuole per compilare?", e la risposta quasi sempre ruota attorno alla frequenza di clock e al numero di core.

Quando l'obiettivo diventa una **workstation AI/ML** — cioè, una macchina che addestra reti neurali, esegue il fine-tuning di modelli linguistici, fa inferenza locale su LLM quantizzati, processa stream video con modelli di rilevamento, o semplicemente serve da banco di prova per pipeline che poi gireranno nei datacenter — quel modello mentale non regge più. Non perché sia sbagliato, ma perché **la domanda cambia**. La domanda non è più "quanto è veloce la CPU", ma "quanto materiale posso spostare contemporaneamente tra memoria, drive, rete e acceleratori, senza che nulla aspetti".

Questo è il punto centrale dell'intero capitolo, ed è bene affermarlo brutalmente: **in una workstation AI, lo scopo principale della CPU è non essere un collo di bottiglia per tutto il resto**. Il calcolo pesante è fatto dalle GPU. La CPU deve garantire tre cose: (1) abbastanza corsie di comunicazione (PCIe lanes) affinché GPU e SSD comunichino a piena velocità, (2) abbastanza banda di memoria e abbastanza RAM affinché i dati raggiungano le GPU senza intoppi, (3) abbastanza core affinché la preparazione dei dati (decodifica immagini, augmentation, tokenization) non lasci le GPU inattive.

Da queste tre esigenze nasce un'intera categoria di prodotti — **HEDT** e **workstation/server** — che il mercato consumer non conosce e che ha regole, nomenclature, prezzi e insidie completamente diverse. HEDT sta per *High-End DeskTop*: è un segmento storico, nato per chi aveva bisogno di più di un desktop ma non voleva (o non poteva) gestire un server. Oggi, HEDT significa essenzialmente AMD Threadripper e, in parte, Intel Xeon W.

Il capitolo procede come segue: prima, capiamo *perché* una CPU consumer non è sufficiente (e, altrettanto importante, *quando è perfettamente sufficiente*), poi approfondiamo le piattaforme Threadripper, poi le piattaforme server (EPYC e Xeon), poi esploriamo il PCIe come infrastruttura, poi le schede madri per workstation, poi il dimensionamento in base al carico di lavoro, e infine, gli errori tipici. Concludiamo con un riassunto operativo, che è di fatto una checklist per l'acquisto.

Una nota metodologica prima di iniziare. **I dati numerici in questo settore invecchiano molto rapidamente**: prezzi, disponibilità, generazioni, e persino la matrice di compatibilità tra CPU e chipset cambiano ad ogni aggiornamento firmware. Ogni volta che i dati sono volatili, lo indicherò esplicitamente con il tag *[dati volatili]*. Architetture e principi, tuttavia, rimangono validi molto più a lungo: sono questi che vale la pena imparare davvero.

---

## 1. Perché una CPU Consumer non è Sufficiente

### 1.1 Il Cambiamento di Prospettiva: La CPU come Nodo di Smistamento

In un PC consumer, la CPU è la protagonista: esegue il codice applicativo, e tutto il resto le ruota attorno. In una workstation AI, la protagonista è l'acceleratore (la GPU), e la CPU assume quello che potremmo definire un ruolo *infrastrutturale*. È il nodo di smistamento del traffico: legge i dati dal disco, li decodifica, li trasforma, li impacchetta e li invia alla GPU; poi raccoglie i risultati, li scrive, li sincronizza tra più GPU e li invia in rete.

Se anche uno solo di questi passaggi è un collo di bottiglia, la GPU — che costa quanto il resto del sistema messo insieme — rimane inattiva. Il parametro critico in una workstation AI si chiama **GPU utilization**: la percentuale di tempo in cui i core dell'acceleratore stanno effettivamente calcolando. Una workstation ben progettata mantiene le GPU sopra il 90-95% di utilizzo durante l'addestramento. Una workstation sbilanciata — CPU consumer con quattro GPU attaccate a corsie ridotte, dataset su un singolo SSD, poca RAM — può facilmente scendere al 40-50%, il che significa **metà del valore economico dell'hardware sprecato**.

Vediamo, una per una, le quattro risorse che i sistemi consumer non riescono a fornire.

### 1.2 PCIe Lanes: La Vera Moneta della Workstation

**PCIe** sta per *Peripheral Component Interconnect Express*: è il bus (cioè il sistema di connessione) attraverso il quale la CPU comunica con le periferiche ad alta velocità — schede grafiche, SSD NVMe, schede di rete, acceleratori dedicati, controller di storage.

PCIe è un bus **seriale e punto-punto**: non è una strada condivisa dove tutti si accalcano (come il vecchio PCI o ISA), ma un insieme di connessioni dedicate. L'unità elementare è chiamata **lane**. Una lane è, fisicamente, una coppia di connessioni differenziali: una per la trasmissione, una per la ricezione. Questo significa che PCIe è **full-duplex**: può trasmettere e ricevere simultaneamente alla piena velocità nominale in entrambe le direzioni.

Le lane sono aggregate: uno slot può essere x1, x4, x8, x16. Il numero indica quante lane sono cablate a quello slot. Più lane significano più larghezza di banda: la larghezza di banda cresce linearmente con il numero di lane.

La seconda variabile è la **generazione** (o *revisione*) di PCIe, che determina la velocità di ciascuna lane. Ecco la panoramica, che vale la pena memorizzare perché la useremo costantemente:

| Generazione | Anno Indicativo | Larghezza di Banda per Lane (per direzione) | Larghezza di Banda x4 | Larghezza di Banda x8 | Larghezza di Banda x16 |
|---|---|---|---|---|---|
| PCIe 3.0 | 2010 | ~0.985 GB/s | ~3.94 GB/s | ~7.88 GB/s | ~15.75 GB/s |
| PCIe 4.0 | 2017 | ~1.97 GB/s | ~7.88 GB/s | ~15.75 GB/s | ~31.5 GB/s |
| PCIe 5.0 | 2019 | ~3.94 GB/s | ~15.75 GB/s | ~31.5 GB/s | ~63 GB/s |
| PCIe 6.0 | 2022 (spec) | ~7.88 GB/s | ~31.5 GB/s | ~63 GB/s | ~126 GB/s |

Da leggere come segue: **ogni generazione raddoppia la larghezza di banda per lane**, a parità di numero di lane. Di conseguenza, uno slot PCIe 5.0 x8 ha la stessa larghezza di banda di uno slot PCIe 4.0 x16. Questa equivalenza è molto importante e si presenterà ripetutamente.

*[dati volatili]* PCIe 6.0 esiste come specifica ed è in fase di adozione nel mondo dei datacenter, ma sulle piattaforme workstation disponibili oggi, lo standard di riferimento è PCIe 5.0. PCIe 7.0 è già stato ratificato come specifica ma non ha ancora prodotti reali sul mercato delle workstation.

#### Perché le Lane Sono il Vero Collo di Bottiglia

Una CPU ha un numero **finito** di lane PCIe, perché ogni lane richiede pin fisici sul package, transceiver, silicio dedicato e consumo energetico. È una risorsa costosa, e i produttori la razionano per segmentare il mercato.

Contiamo ciò di cui ha bisogno una seria workstation AI:

- **Una GPU PCIe 5.0 x16**: 16 lane. Se ci sono quattro GPU, sono necessarie **64 lane** solo per gli acceleratori.
- **SSD NVMe**: Ogni NVMe (*Non-Volatile Memory express*, il protocollo con cui gli SSD comunicano direttamente sul bus PCIe) occupa tipicamente 4 lane. Una workstation AI ne ha spesso 3-4: una per il sistema operativo, una o due per il dataset "caldo", una per i checkpoint. Sono **12-16 lane**.
- **Scheda di rete**: Una NIC (*Network Interface Card*) da 10 GbE occupa generalmente 4 lane PCIe 3.0/4.0; una da 25/100 GbE occupa 8-16 lane. Diciamo **4-16 lane**.
- **Controller HBA/RAID** per l'archiviazione di massa (dataset di decine di TB): **8 lane**.
- **Schede di acquisizione, acceleratori dedicati, schede di acquisizione**: variabile.

Realisticamente, per una workstation a 4 GPU: 64 + 16 + 8 + 8 = **96 lane**. Una CPU consumer ne ha circa venti. Il divario è di un fattore quattro o cinque, e nessuna ottimizzazione software può colmarlo.

#### Quante Lane Hanno Veramente le CPU Consumer

Qui, bisogna prestare attenzione a un dettaglio che confonde molti acquirenti: le lane **della CPU** e le lane **del chipset** non sono la stessa cosa.

Il **chipset** (in AMD, è anche chiamato *PCH*, *Platform Controller Hub*, originariamente un termine Intel) è un chip separato sulla scheda madre che espande la connettività. Ma il chipset è collegato alla CPU tramite un link che è esso stesso limitato — tipicamente x4 PCIe 4.0. Tutto ciò che è collegato al chipset (slot secondari, alcune porte NVMe, USB, SATA, LAN integrata) **condivide quel singolo link x4**. È come avere una villa con dieci stanze ma un solo corridoio largo un metro per raggiungerle tutte.

Per questo motivo, quando si progetta una workstation, **contano solo le linee native della CPU**, quelle direttamente collegate. Le linee del chipset vanno bene per periferiche lente (audio, USB, rete gigabit, SSD di archivio) e dovrebbero essere evitate per GPU e unità NVMe ad alte prestazioni.

Ecco la panoramica consumer *[dati indicativi, verificare per modelli specifici]*:

- **AMD AM5 (Ryzen 7000/8000/9000)**: 28 linee PCIe totali dalla CPU, di cui **24 effettivamente utilizzabili** (16 per la GPU + 4 per un NVMe + 4 per un secondo NVMe), più 4 linee dedicate alla connessione del chipset. Le versioni con grafica integrata potente (serie "G") ne hanno ancora meno.
- **Intel LGA1700 (12a–14a gen)**: **20 linee** dalla CPU (16 PCIe 5.0 per la GPU + 4 PCIe 4.0 per un NVMe), più la connessione DMI al chipset.
- **Intel LGA1851 (Core Ultra 200 series "Arrow Lake")**: **24 linee** dalla CPU (16 PCIe 5.0 + 4 PCIe 5.0 + 4 PCIe 4.0), più DMI.

*[dati volatili]* Questi numeri cambiano ad ogni nuova piattaforma; il principio (consumer = ~20–28 linee, e non di più) è stabile da oltre un decennio.

### 1.3 Tabella comparativa: linee per segmento di piattaforma

| Piattaforma | Segmento | Linee PCIe dalla CPU | Generazione | Note |
|---|---|---|---|---|
| Intel LGA1700 (Core 12a/13a/14a gen) | Consumer | 20 | 5.0 (16) + 4.0 (4) | Singola GPU a x16 |
| Intel LGA1851 (Core Ultra 200S) | Consumer | 24 | 5.0 (20) + 4.0 (4) | Una GPU + 2 NVMe |
| AMD AM5 (Ryzen 7000/9000) | Consumer | 24 utilizzabili (+4 chipset) | 5.0 | Una GPU + 2 NVMe |
| AMD Threadripper 7000/9000 (TRX50) | HEDT | **48 PCIe 5.0** (+ ulteriori linee 4.0; AMD dichiara fino a ~92 linee di piattaforma) | 5.0 / 4.0 | 2 GPU a x16 complete + NVMe |
| AMD Threadripper PRO 7000WX/9000WX (WRX90) | Workstation | **128 PCIe 5.0** | 5.0 | Fino a 7 slot x16 |
| Intel Xeon W-2400/2500 (W790) | Workstation Entry | 64 | 5.0 | 2 GPU a x16 + storage |
| Intel Xeon W-3400/3500 (W790) | Workstation | 112 | 5.0 | 4+ GPU |
| AMD EPYC 9004/9005 (SP5) | Server | **128 per socket** | 5.0 | 160 linee totali in dual-socket |
| Intel Xeon 6 (6700P/6900P) | Server | 88 / 96 per socket | 5.0 | Dipende dalla SKU |

*[dati volatili: controllare sempre il datasheet per la SKU precisa, poiché esistono varianti con conteggi diversi all'interno della stessa famiglia]*

Lettura pratica di questa tabella: il salto da consumer a HEDT **raddoppia o triplica** le linee; il salto da HEDT a workstation professionale **le raddoppia di nuovo**. Ed è per questo — non per il numero di core — che si sale di piattaforma.

### 1.4 Canali di memoria: perché la larghezza di banda della RAM è importante

Il **controller di memoria** è il circuito che gestisce la comunicazione tra la CPU e la RAM. Da circa quindici anni, è integrato nella CPU stessa, e viene chiamato **IMC** (*Integrated Memory Controller*). In precedenza, era sul chipset (il famigerato "northbridge"), e la sua integrazione nella CPU è stata una delle maggiori innovazioni prestazionali degli anni 2000.

L'IMC comunica con la RAM attraverso uno o più **canali**. Un canale è un percorso indipendente verso i moduli di memoria: più canali significano più percorsi paralleli e quindi una maggiore larghezza di banda aggregata.

Facciamo i calcoli. Un modulo DDR5 (*Double Data Rate 5*, la quinta generazione di memoria SDRAM) a 5600 MT/s (*Mega Transfers per second*, ovvero milioni di trasferimenti al secondo) fornisce circa **44,8 GB/s per canale** (5600 × 8 byte). Da ciò:

| Configurazione | Canali | Larghezza di banda teorica (DDR5-5600) | Piattaforme |
|---|---|---|---|
| Dual channel | 2 | ~90 GB/s | Consumer AM5, LGA1700/1851 |
| Quad channel | 4 | ~180 GB/s | Threadripper non-PRO (TRX50), Xeon W-2400/2500 |
| Octa channel | 8 | ~358 GB/s | Threadripper PRO (WRX90), Xeon W-3400/3500 |
| 12-channel | 12 | ~537 GB/s | AMD EPYC 9004/9005, Intel Xeon 6 (6900P) |

*[nota: le frequenze ufficialmente supportate variano; le piattaforme server tipicamente usano DDR5 registrata a 4800–6400 MT/s a seconda del numero di moduli per canale. Dati volatili.]*

#### Perché la larghezza di banda della RAM è importante in una workstation AI

Ci sono tre ragioni, tutte concrete.

**Primo: caricamento dei dati.** Durante l'addestramento, il flusso è il seguente: i dati grezzi (immagini JPEG, file audio, testi) vengono letti dal disco, decodificati nella RAM, trasformati (ridimensionamento, normalizzazione, aumento), impacchettati in tensori e infine copiati nella memoria della GPU tramite PCIe. Tutti questi passaggi intermedi risiedono nella RAM di sistema. Con quattro GPU che richiedono, diciamo, 3–4 GB/s di dati ciascuna, e considerando che ogni byte viene toccato più volte (lettura, decodifica, trasformazione, copia in *pinned memory*), la RAM viene attraversata a decine di GB/s. Un dual channel a 90 GB/s teorici — che in pratica si traduce in 60–70 GB/s reali — diventa un collo di bottiglia.

**Secondo: inferenza CPU.** Quando si esegue un LLM (*Large Language Model*) direttamente sulla CPU — con `llama.cpp`, con backend ONNX Runtime, con oneDNN — la fase di **generazione token-by-token** è quasi interamente *memory-bound*, ovvero limitata dalla larghezza di banda della memoria, non dalla potenza computazionale. La ragione è che per generare ogni singolo token, **tutti i pesi del modello** devono essere letti dalla RAM. Un modello da 70 miliardi di parametri quantizzato a 4 bit occupa circa 40 GB. Per generare 10 token al secondo, devono essere letti 400 GB/s. Su una piattaforma dual-channel con 70 GB/s reali, ciò si traduce in circa 1,5–2 token/s: inutilizzabile per un uso interattivo. Su una piattaforma a 12 canali con oltre 400 GB/s, ciò si traduce in 8–10 token/s: lento ma utilizzabile. **Questo è il motivo per cui esiste una vera nicchia di persone che acquistano EPYC usati per l'inferenza LLM su CPU: non per i core, ma per la larghezza di banda.**

**Terzo: pre-elaborazione classica.** Pipeline Pandas/polars su dataset tabulari di decine di GB, operazioni di join, groupby, feature engineering: tutte operazioni che spostano montagne di byte e scalano con la larghezza di banda della memoria più che con la frequenza di clock.

### 1.5 Capacità della RAM: i limiti reali e i moduli registrati

Oltre alla larghezza di banda, c'è la **capacità**. E qui il limite consumer è chiaro.

Una piattaforma consumer ha **quattro slot DIMM** (su due canali: due moduli per canale). Il limite pratico dipende dalla capacità massima dei moduli UDIMM disponibili:

*[dati volatili]* Su AM5 e LGA1851, con moduli UDIMM da 48 GB, si raggiungono 192 GB; con l'arrivo dei moduli da 64 GB e relativi aggiornamenti AGESA/BIOS, si raggiungono 256 GB. Oltre non è possibile, e già con 4 moduli le frequenze operative calano perché l'IMC fatica a pilotare due moduli per canale ad alta velocità.

Questo è il punto critico: **su una piattaforma consumer, riempire tutti e quattro gli slot degrada la frequenza della RAM**. Un kit DDR5-6000 con due moduli funziona a 6000; gli stessi quattro moduli spesso scendono a 3600–4400 MT/s. Si paga la capacità in cambio di banda — esattamente il compromesso che non si vuole fare in una workstation.

#### UDIMM, RDIMM, LRDIMM

Per superare questo limite, il mondo professionale usa moduli diversi.

- **UDIMM** (*Unbuffered DIMM*): È il modulo consumer. I segnali di indirizzo e comando dall'IMC arrivano **direttamente** a tutti i chip di memoria sul modulo. Semplice, economico, bassa latenza — ma il carico elettrico sul controller aumenta con il numero di chip, il che limita quanti moduli possono essere posti per canale e a quale frequenza.

- **RDIMM** (*Registered DIMM*): C'è un chip aggiuntivo sul modulo, il **RCD** (*Registering Clock Driver*), che funge da buffer per i segnali di indirizzo e comando. L'IMC pilota un solo carico (il register) invece di decine di chip. Costo: un ciclo di clock aggiuntivo di latenza. Beneficio: si possono installare moduli molto più grandi (64, 96, 128 GB e oltre) e più moduli per canale mantenendo frequenze elevate. **Le piattaforme Threadripper PRO, EPYC e Xeon W/Scalable richiedono RDIMM: non accettano UDIMM.**

- **LRDIMM** (*Load-Reduced DIMM*): Questo bufferizza anche le linee dati, non solo indirizzi e comandi. Permette capacità estreme (fino a 256 GB per modulo) a costo di latenza aggiuntiva. Usato in server con enormi requisiti di memoria.

- **MRDIMM** (*Multiplexed Rank DIMM*): La recente evoluzione, che multiplexa due rank per raddoppiare la banda effettiva. Supportato sulle ultime piattaforme server. *[dati volatili, ecosistema in evoluzione]*

#### Limiti di Capacità per Piattaforma

| Piattaforma | Slot DIMM tipici | Tipo di memoria | Capacità massima indicativa |
|---|---|---|---|
| Consumer AM5 / LGA1851 | 4 | UDIMM (ECC non ufficiale opzionale) | 192–256 GB *[volatile]* |
| Threadripper 7000/9000 (TRX50) | 4 | RDIMM ECC (4 canali) | ~1 TB |
| Threadripper PRO (WRX90) | 8 | RDIMM ECC (8 canali) | ~2 TB |
| Xeon W-3400/3500 (W790) | 8 | RDIMM ECC (8 canali) | ~2 TB |
| EPYC 9004/9005 (SP5) | 12 o 24 | RDIMM ECC (12 canali) | 6 TB e oltre per socket |

*[dati volatili: le capacità massime aumentano con ogni nuovo modulo RDIMM sul mercato. Controllare la QVL — Qualified Vendor List — della scheda madre.]*

Il salto è di un ordine di grandezza. Ed è davvero necessario: caricare un intero dataset di immagini in RAM per eliminare l'I/O su disco, tenere un grafo enorme in memoria, fare inferenza CPU su un modello da 405 miliardi di parametri, eseguire simulazioni — sono tutti casi in cui 256 GB sono insufficienti.

### 1.6 ECC: Cos'è e perché è irrinunciabile in ambito professionale

**ECC** sta per *Error-Correcting Code*.

Il problema che risolve è fisico. Una cella di memoria DRAM memorizza un bit come una carica elettrica in un condensatore microscopico. Tale carica può essere alterata: da un raggio cosmico (un neutrone ad alta energia che attraversa il silicio), da particelle alfa emesse da impurità radioattive nei materiali del package, da interferenze elettromagnetiche o da instabilità termiche o di alimentazione. Il risultato è un **soft error**: un bit che si inverte, da 0 a 1 o viceversa, senza che nulla sia fisicamente rotto.

La frequenza è bassa ma non trascurabile. Studi sul campo (il più citato è quello di Google su decine di migliaia di server) indicano ordini di grandezza di **migliaia di errori correggibili per gigabyte all'anno** in condizioni reali. Su un desktop con 32 GB accessi otto ore al giorno, un errore occasionale: fastidioso, magari un crash, si riavvia. Su una workstation con **512 GB in funzione 24 ore su 24, 7 giorni su 7 per tre settimane** durante l'addestramento, la probabilità di almeno un'inversione di bit diventa **alta**.

#### Come funziona l'ECC

L'implementazione classica si chiama **SECDED**: *Single Error Correction, Double Error Detection*. Per ogni 64 bit di dati utili, vengono aggiunti 8 bit di codice di controllo (un codice Hamming esteso), il che significa che il modulo ha chip extra: un modulo ECC tipicamente ha 9 o 18 chip invece di 8 o 16. Quando la CPU legge quei 72 bit, ricalcola il codice: se un singolo bit è sbagliato, il sistema **capisce quale** e lo corregge al volo, in modo trasparente, registrando l'evento. Se due bit sono sbagliati, il sistema **rileva** l'errore ma non può correggerlo: genera un'eccezione della macchina e generalmente arresta il sistema, perché continuare significherebbe lavorare con dati corrotti.

Questo è il punto chiave: **senza ECC, un'inversione di bit è silenziosa**. Il computer non sa che i dati sono sbagliati. Continua semplicemente a funzionare.

#### Perché è essenziale in AI/ML

Immaginiamo le conseguenze concrete di un'inversione di bit silenziosa in una workstation AI:

- Durante una sessione di addestramento di 72 ore, un bit si inverte nell'esponente di un numero in virgola mobile che rappresenta un peso. Il peso cambia da 0.003 a 3×10³⁰. Il gradiente esplode, la perdita diventa NaN (*Not a Number*), e il problema viene notato — nel migliore dei casi — ore dopo. **Tre giorni di GPU sprecati.**
- Peggio: l'inversione di bit si verifica in un punto che non causa alcuna esplosione, ma corrompe sottilmente un batch di dati o un peso. Il modello converge comunque, ma è **sottilmente sbagliato**, e nessuno lo noterà mai. Il risultato scientifico o il prodotto sono compromessi in modo non riproducibile e non diagnosticabile.
- Un'inversione di bit in un checkpoint scritto su disco corrompe il file, e viene notato solo settimane dopo quando si tenta di riprendere l'addestramento.

In un contesto professionale — ricerca pubblicabile, modelli che vanno in produzione, calcoli su cui vengono prese decisioni — la **non riproducibilità silenziosa è inaccettabile**. L'ECC costa il 10-15% in più per i moduli e circa un punto percentuale in termini di prestazioni. È l'assicurazione più economica per il sistema.

#### Attenzione a due confusioni comuni

**Confusione 1: l'"on-die ECC" di DDR5 non è ECC di sistema.** Tutti i moduli DDR5, anche i più economici da gaming, hanno una forma di correzione degli errori *interna al chip* (on-die ECC), introdotta perché le densità delle celle sono diventate così elevate che gli errori interni sarebbero altrimenti insostenibili. Ma questa correzione protegge solo la cella; **non protegge il percorso tra il chip e la CPU**, non segnala errori al sistema operativo e non fornisce alcuna garanzia end-to-end. Vedere "DDR5 con on-die ECC" sulla confezione di un kit da gaming e pensare di avere una macchina ECC è un errore molto comune e molto costoso.

**Confusione 2: Supporto ECC su una piattaforma consumer.** Alcune CPU AM5 supportano tecnicamente moduli **UDIMM ECC**, e alcune schede madri (specialmente ASRock e alcune ASUS Pro) lo espongono nel BIOS. Questo è un supporto reale ma **non ufficialmente validato** per uso professionale, con disponibilità limitata di moduli e nessuna garanzia. Non è una scorciatoia per evitare la piattaforma workstation: è un "meglio di niente" per un homelab.

### 1.7 E se il consumer fosse sufficiente?

Prima di procedere, è onesto dire una cosa che il resto del capitolo potrebbe farvi dimenticare: **per molti casi d'uso reali, una piattaforma consumer di fascia alta è la scelta giusta.**

Se il progetto è: una singola GPU (anche una RTX 5090 o una RTX PRO 6000), 128 GB di RAM, due unità NVMe, fine-tuning con LoRA, inferenza locale, sviluppo di prototipi — allora un Ryzen 9 9950X o un Core Ultra 9 285K su una piattaforma consumer **fa esattamente ciò che serve**, costa un quarto, ha frequenze single-thread più elevate (utili per il codice Python non parallelizzato) e non richiede raffreddamenti esotici.

Spendere 3.000 euro per una CPU Threadripper PRO + scheda madre per pilotare una singola GPU è, come vedremo nella sezione errori, l'errore più frequente e più costoso in questo settore. **Si aggiornano le piattaforme quando si supera un confine preciso, non per prestigio.** Quel confine è: **due o più GPU a x16 elettrico**, o la necessità di più di 256 GB di RAM, o il requisito di ECC certificato.

---

## 2. AMD Threadripper: La Panoramica Completa

### 2.1 Cos'è Threadripper e dove si colloca

**Threadripper** è la linea di AMD lanciata nel 2017 per colmare il divario tra Ryzen (consumer) ed EPYC (server). Tecnicamente, non è un progetto separato: è **silicio EPYC riconfezionato** su un package e socket dedicati, con frequenze più elevate, meno canali di memoria (nelle versioni non-PRO) e un ecosistema di schede madri progettato per il desktop anziché per il rack.

Il posizionamento è preciso:

- **Ryzen** (AM5): 1 GPU, 2 canali RAM, ~24 lane. Frequenze massime, latenze minime, prezzo basso.
- **Threadripper** (sTR5/TRX50): 2 GPU a x16, 4 canali RAM RDIMM, 48 lane PCIe 5.0. Il "desktop estremo".
- **Threadripper PRO** (sTR5/WRX90): 4–7 GPU, 8 canali RAM RDIMM, 128 lane PCIe 5.0. La vera workstation.
- **EPYC** (SP5): 12 canali, 128 lane per socket, dual socket, ma frequenze più basse e nessun supporto "desktop".

È importante capire subito: **PRO e non-PRO condividono lo stesso socket fisico (sTR5) ma non le stesse piattaforme**. Questa è la principale fonte di confusione.

### 2.2 Threadripper non-PRO: Le Serie 7000X e 9000X

La generazione basata su Zen 4 (nome in codice *Storm Peak*, lanciata a fine 2023) include:

| Modello | Core / Thread | Boost Max Indicativo | Cache L3 | TDP |
|---|---|---|---|---|
| Threadripper 7960X | 24 / 48 | ~5.3 GHz | 128 MB | 350 W |
| Threadripper 7970X | 32 / 64 | ~5.3 GHz | 128 MB | 350 W |
| Threadripper 7980X | 64 / 128 | ~5.1 GHz | 256 MB | 350 W |

La prossima generazione, basata su **Zen 5** (nome in codice *Shimada Peak*, serie 9000, disponibile dal 2025), mantiene la stessa struttura:

| Modello | Core / Thread | Note |
|---|---|---|
| Threadripper 9960X | 24 / 48 | Successore diretto del 7960X |
| Threadripper 9970X | 32 / 64 | Successore del 7970X |
| Threadripper 9980X | 64 / 128 | Successore del 7980X |

*[dati volatili: frequenze, prezzi e disponibilità cambiano; la struttura core/lane è stabile]*

Caratteristiche della piattaforma dei non-PRO su chipset TRX50:

- **Memoria**: **4 canali** DDR5 RDIMM ECC, 4 slot DIMM. Nota importante: anche i Threadripper non-PRO **richiedono RDIMM registrate**, non UDIMM consumer. Non è possibile riutilizzare un kit da gaming.
- **PCIe**: **48 lane PCIe 5.0** dalla CPU utilizzabili sulla piattaforma TRX50, più ulteriori lane PCIe 4.0 dal chipset e dalla CPU. AMD pubblicizza commercialmente un totale di piattaforma "fino a 92 lane" sommando tutto (CPU + chipset, generazioni miste). *[Attenzione: questo è un numero di marketing. Ciò che conta per le GPU sono le 48 lane PCIe 5.0 native.]*
- **TDP**: **350 W** (*Thermal Design Power*, la potenza di dissipazione termica che il sistema di raffreddamento deve essere in grado di gestire in condizioni sostenute; il consumo di picco effettivo può superarlo).

**A cosa serve**: due GPU a pieno x16 PCIe 5.0, più 2-3 NVMe a x4, più una NIC. Questa è la configurazione tipica per una workstation AI di un ricercatore individuale serio, o di un piccolo team.

### 2.3 Threadripper PRO: Le serie 7000WX e 9000WX

Questa è una categoria diversa. La linea PRO è la workstation professionale di AMD, e la differenza non è nel numero di core (che pure aumenta) ma **nella piattaforma**.

| Modello | Core / Thread | Cache L3 | TDP |
|---|---|---|---|
| Threadripper PRO 7955WX | 16 / 32 | 64 MB | 350 W |
| Threadripper PRO 7965WX | 24 / 48 | 128 MB | 350 W |
| Threadripper PRO 7975WX | 32 / 64 | 128 MB | 350 W |
| Threadripper PRO 7985WX | 64 / 128 | 256 MB | 350 W |
| Threadripper PRO 7995WX | **96 / 192** | 384 MB | 350 W |

La generazione Zen 5 (serie 9000WX) replica la struttura, con il **9995WX** a 96 core al vertice, affiancato da 9985WX (64c), 9975WX (32c), 9965WX (24c), 9955WX (16c). *[dati volatili: verificare l'elenco SKU aggiornato]*

Caratteristiche della piattaforma PRO su chipset **WRX90**:

- **Memoria**: **8 canali** DDR5 RDIMM ECC, 8 slot DIMM, fino a ~2 TB.
- **PCIe**: **128 lane PCIe 5.0 native**. Questo è il numero che giustifica l'intera esistenza della linea.
- **Funzionalità professionali**: AMD PRO Manageability (gestione remota a livello enterprise), AMD PRO Security (Memory Guard, crittografia della memoria), garanzie e validazione ISV (*Independent Software Vendor*: certificazione che software professionali come CAD, simulatori e suite di rendering sono testati su quella piattaforma).

### 2.4 Il Confronto Che Conta Davvero

Ricapitoliamo, perché questa è **la tabella più importante dell'intero capitolo**:

| Caratteristica | Threadripper (non-PRO) su TRX50 | Threadripper PRO su WRX90 |
|---|---|---|
| Socket | sTR5 | sTR5 (**stesso socket fisico**) |
| Chipset | TRX50 | WRX90 |
| Linee PCIe 5.0 native | **48** | **128** |
| Canali RAM | **4** | **8** |
| Slot DIMM | 4 | 8 |
| RAM massima | ~1 TB | ~2 TB |
| Tipo di RAM | RDIMM ECC | RDIMM ECC |
| Core massimi | 64 | **96** |
| GPU a x16 elettrico | **2** | **fino a 7** |
| Gestione remota (IPMI/BMC) | raramente | tipica |
| Costo piattaforma (CPU+MB) | Alto | Molto alto |

Un punto sottile che genera molta confusione: **una CPU Threadripper PRO può essere montata su una scheda madre TRX50**, perché il socket è lo stesso. Ma in quel caso, **opera con le limitazioni del TRX50**: 4 canali di memoria anziché 8, e un numero ridotto di linee esposte (le schede TRX50 non hanno il cablaggio per 128 linee). Il contrario **non è vero**: una CPU non-PRO **non funziona** su una scheda madre WRX90. WRX90 accetta solo PRO.

Questo apre un interessante scenario pratico: **acquistare una CPU PRO su una scheda TRX50** ha senso solo se si desidera accedere a un numero di core più elevato (ad esempio, il 7995WX a 96 core) senza la necessità di 128 linee. È un caso di nicchia — ma esiste, ad esempio, per carichi di lavoro di rendering o compilazione massiva con una singola GPU.

### 2.5 Decodifica della nomenclatura

Prendiamo il **7995WX** e analizziamolo:

- **7**: generazione. La serie 7000 corrisponde a Zen 4; la serie 9000 a Zen 5. *(Nota: AMD ha saltato la serie 8000 su Threadripper, allineandosi alla numerazione Ryzen.)*
- **99**: livello all'interno della generazione. Più alto = più core. 95 → 96 core, 85 → 64 core, 75 → 32 core, 65 → 24 core, 55 → 16 core. La logica a due cifre è "posizione nello stack", non una formula matematica.
- **5**: cifra di segmento/revisione, praticamente costante nella linea.
- **WX**: **il suffisso è la cosa più importante di tutte**. *WX* = *WorkstationeXtreme*, cioè **PRO**: 8 canali, 128 linee, WRX90.
- **X** (senza W): non-PRO. 4 canali, 48 linee, TRX50.

Quindi: **7970X ≠ 7975WX**. Il primo ha 32 core, 4 canali, 48 linee. Il secondo ha 32 core, 8 canali, 128 linee. **Stessi core, piattaforme completamente diverse, prezzo molto diverso.** Chi non conosce il significato di "WX" è molto probabile che acquisti il prodotto sbagliato.

### 2.6 Socket sTR5, Chipset TRX50 e WRX90

Il socket **sTR5** è un LGA (*Land Grid Array*: i pin sono sul socket, non sulla CPU) con **4844 pin**. È fisicamente enorme — un rettangolo di circa 76 × 58 mm — e questo ha conseguenze pratiche:

- **Montaggio**: la CPU viene inserita in un *carrier* di plastica arancione che scorre in una guida metallica; il coperchio si chiude con **tre viti Torx** da stringere **in ordine numerato (1, 2, 3)** e con **coppia controllata**. AMD fornisce un cacciavite dinamometrico nella confezione. Questo non è un capriccio: stringere fuori ordine o con coppia errata su una superficie così grande può causare contatti mancanti o danni permanenti al package.
- **Raffreddamento**: l'IHS (*Integrated Heat Spreader*, il coperchio metallico della CPU) è molto più grande di quelli consumer. **Un dissipatore AM5 o LGA1700 non coprirà la superficie** e non ha il montaggio corretto. Sono necessari dissipatori sTR5 specifici.

Il **chipset**, come abbiamo visto, è il chip sulla scheda madre che espande la connettività. La scelta tra TRX50 e WRX90 **è la vera decisione architetturale**, e deve essere presa **prima** di scegliere la CPU:

| Domanda | Se la risposta è... | Piattaforma |
|---|---|---|
| Quante GPU a x16 elettrico? | 1 o 2 | **TRX50** |
| Quante GPU a x16 elettrico? | 3 o più | **WRX90** |
| Hai bisogno di più di ~500 GB di RAM? | Sì | **WRX90** |
| Hai bisogno della massima larghezza di banda della memoria (inferenza CPU)? | Sì | **WRX90** (8 canali) |
| Hai bisogno di gestione remota IPMI/BMC? | Sì | **WRX90** (o scheda server) |
| Il budget è il vincolo principale? | Sì | **TRX50** |

### 2.7 Perché Threadripper PRO è *la* piattaforma per le workstation AI

C'è solo un motivo, e risiede nelle 128 linee. Contiamo su una scheda **ASUS Pro WS WRX90E-SAGE SE**, che è il riferimento in questa categoria:

- **7 slot PCIe 5.0 x16** — tutti **veri x16 elettrici**, tutti contemporaneamente. 7 × 16 = **112 linee**.
- Circa 16 linee rimangono per NVMe (spesso 4 slot M.2, alcuni tramite biforcazione) e per il resto.

Sette slot x16 completi significano che puoi installare **quattro GPU a doppio slot con spazio adeguato tra di esse** (usando gli slot 1, 3, 5, 7), o **sette schede a singolo slot**. Nessun'altra piattaforma derivata da desktop offre questo. È letteralmente il motivo per cui le workstation multi-GPU esistono sotto la scrivania invece che nei rack.

### 2.8 Confronto tra marchi e fasce di prezzo (Threadripper)

*[Tutti i prezzi sono indicativi e volatili; usali solo come ordini di grandezza.]*

| Configurazione | CPU indicativa | Scheda madre | Fascia di costo CPU+MB |
|---|---|---|---|
| HEDT entry | TR 7960X / 9960X (24c) | TRX50 (ASUS TRX50-SAGE, ASRock TRX50 WS) | ~2.000–2.800 € |
| HEDT high | TR 7970X / 9970X (32c) | TRX50 | ~3.000–4.000 € |
| Workstation entry PRO | TR PRO 7955WX (16c) | WRX90 (ASUS WRX90E-SAGE SE) | ~3.500–4.500 € |
| Workstation medium PRO | TR PRO 7975WX (32c) | WRX90 | ~5.500–7.000 € |
| Workstation top PRO | TR PRO 7995WX (96c) | WRX90 | ~12.000–15.000 € |

Il punto da notare: **la CPU PRO da 16 core 7955WX è la "budget"**, e per molte workstation AI, **è la scelta giusta**, perché la si acquista per le linee e i canali di memoria, non per i core. Sedici core Zen 4 sono più che sufficienti per gestire il caricamento dei dati per quattro GPU. Pagare tre volte tanto per 96 core che rimarranno al 15% di utilizzo è denaro sprecato — a meno che il carico di lavoro non sia anche intensivo per la CPU (rendering, simulazione, compilazione).

---

## 3. AMD EPYC e Intel Xeon: quando passare al server

### 3.1 AMD EPYC (Genoa, Bergamo, Turin)

**EPYC** è la linea di server di AMD. Le generazioni recenti, su socket **SP5** (LGA6096):

- **EPYC 9004 "Genoa"** (Zen 4, 2022): fino a **96 core** per socket.
- **EPYC 9004 "Bergamo"** (Zen 4c, core ottimizzati per la densità): fino a **128 core**.
- **EPYC "Genoa-X"**: con **3D V-Cache**, cache L3 fino a oltre 1 GB. Utile per carichi di lavoro sensibili alla cache (simulazione CFD, EDA).
- **EPYC 9005 "Turin"** (Zen 5, 2024): fino a **128 core Zen 5**, o **192 core** nelle varianti Zen 5c.

Caratteristiche della piattaforma SP5:

- **128 linee PCIe 5.0 per socket.** In una configurazione **dual-socket**, alcune linee sono utilizzate per l'interconnessione tra processori (il collegamento inter-socket **Infinity Fabric**), quindi il totale utilizzabile non è 256 ma tipicamente **160 linee** (configurabile: più linee possono essere sacrificate per un collegamento inter-socket più ampio, o meno linee per averne di più libere).
- **12 canali DDR5 RDIMM per socket** → in dual socket, **24 canali**, con una larghezza di banda aggregata teorica di oltre **1 TB/s**. Questo è un numero a cui nessuna piattaforma desktop può avvicinarsi.
- **Enorme capacità RAM**: diversi TB per socket.
- **Frequenze più basse** rispetto a Threadripper (tipicamente 2.0–3.5 GHz base, boost fino a ~4.x GHz solo su SKU con pochi core), perché il budget termico è diviso tra molti più core e perché i server ottimizzano per l'efficienza, non per le prestazioni single-thread di picco.

**Il compromesso EPYC in una parola: si guadagna in parallelismo, larghezza di banda e connettività; si perde in prestazioni single-thread.** E questo è importante, perché molto codice Python di orchestrazione (il ciclo di training, il DataLoader di PyTorch prima che distribuisca ai worker, il codice di pre-elaborazione non vettorizzato) è **single-thread**. Una workstation EPYC con clock a 2.4 GHz può essere *più lenta* di un Ryzen 9 in tutte le parti "collanti" del codice.

### 3.2 Intel Xeon W e Xeon Scalable

Intel divide il mondo professionale in due:

**Xeon W** è la linea workstation, concettualmente equivalente a Threadripper PRO. Su socket **LGA4677** e chipset **W790**:

- **Xeon W-2400** (Sapphire Rapids, fino a 24 core): **4 canali** DDR5, **64 linee PCIe 5.0**.
- **Xeon W-3400** (fino a 56 core): **8 canali** DDR5, **112 linee PCIe 5.0**.
- **Xeon W-2500 / W-3500**: l'aggiornamento generazionale, con un numero di core leggermente superiore (fino a ~60 core sulla linea W-3500) e le stesse caratteristiche della piattaforma. *[dati volatili: controllare l'elenco SKU attuale]*

**Xeon Scalable / Xeon 6** è la linea server, equivalente a EPYC:

- **Xeon 6 "Granite Rapids"** (P-core, alte prestazioni per core): serie 6900P fino a 128 core, **12 canali** DDR5/MRDIMM, ~96 linee PCIe 5.0; serie 6700P con 8 canali e ~88 linee.
- **Xeon 6 "Sierra Forest"** (E-core, ottimizzato per densità ed efficienza): conteggi di core molto elevati (fino a 288 E-core), ma **E-core senza AVX-512 e senza AMX**, quindi meno adatti per l'inferenza CPU.

*[dati volatili: la roadmap di Intel è in rapida evoluzione]*

#### La contromossa di Intel: AMX

C'è un asso nella manica di Intel che vale la pena conoscere, perché è specificamente rilevante per l'IA: **AMX** (*Advanced Matrix Extensions*), introdotto con Sapphire Rapids. È un set di istruzioni che aggiunge **registri "tile" bidimensionali** e un'unità dedicata alla moltiplicazione di matrici alla CPU. In pratica, inserisce un piccolo core tensoriale all'interno di ogni core della CPU.

Il risultato è che l'inferenza CPU di modelli quantizzati (INT8, BF16) su Xeon con AMX può essere **da 3 a 8 volte più veloce** rispetto alla stessa CPU senza AMX, alla stessa frequenza e numero di core, quando il software lo supporta (oneDNN, OpenVINO, PyTorch con backend Intel). **Questo è l'argomento più forte a favore di Intel nel campo dell'IA su CPU**, e dovrebbe essere tenuto presente da coloro che fanno inferenza solo su CPU o ibrida.

### 3.3 Perché AMD domina oggi nel conteggio dei core e nelle linee

È utile capire il *perché* strutturale, non solo osservare il fatto.

La ragione principale è **architetturale**, e si chiama **chiplet**. Dal 2017, AMD ha costruito le sue CPU server assemblando più piccoli die:

- **CCD** (*Core Complex Dies*): piccoli die che contengono solo i core e la cache, prodotti sul nodo di produzione più avanzato disponibile (5 nm, poi 4 nm, poi 3 nm).
- Un **IOD** (*I/O Die*): un die più grande che contiene i controller di memoria, i controller PCIe e l'infrastruttura di interconnessione, prodotto su un nodo più economico e maturo (dove i transistor di I/O non beneficiano comunque di un'estrema miniaturizzazione).

Il vantaggio è economico e statistico. La **resa di produzione** (la percentuale di die funzionanti su un wafer) diminuisce esponenzialmente con l'area del die, perché più è grande, più è probabile che un difetto casuale lo colpisca. Producendo **otto o dodici piccoli die** invece di **un unico die monolitico gigante**, AMD ottiene rese molto più elevate, può selezionare i die migliori e può scalare il numero di core semplicemente **aggiungendo CCD al package**.

Intel ha utilizzato die monolitici per anni, e quando ha voluto aumentare il numero di core, ha incontrato rese sempre peggiori e costi crescenti. Anche essa è passata a un approccio basato su tile (con Sapphire Rapids e successori), ma con un ritardo di diversi anni, esacerbato da note difficoltà nella transizione a nodi di produzione avanzati (la famosa stagnazione a 10 nm).

Lo stesso vale per le linee PCIe: la logica PCIe è nell'IOD, un die grande ed "economico", e AMD ha potuto permettersi di metterne molta lì. 128 linee native contro le ~96 di Intel non è una coincidenza: è una diretta conseguenza della scelta di packaging.

**Conclusione onesta**: AMD ha attualmente un vantaggio strutturale in core, linee e canali di memoria. Intel mantiene vantaggi in acceleratori integrati specifici (AMX), ecosistemi software ottimizzati (OpenVINO, oneAPI), alcune SKU a bassa latenza e talvolta nel prezzo delle piattaforme usate. *[Situazione volatile: il vantaggio competitivo tra i due può cambiare rapidamente.]*

### 3.4 Server o HEDT? La decisione per un ricercatore o un piccolo team

Ecco i criteri concreti, al di là del marketing.

**Scegli HEDT (Threadripper / Threadripper PRO) se:**
- Lavori sotto una scrivania, in un ufficio, e hai bisogno di **silenzio** (un server 2U con ventole da 15.000 rpm è insostenibile in un ambiente di lavoro).
- Desideri **alte frequenze** perché parte del carico di lavoro è single-threaded.
- Hai **1-4 GPU**.
- Vuoi essere in grado di **assemblare tutto da solo** con componenti al dettaglio.
- Hai bisogno di certificazioni ISV per software professionali.

**Scegli server (EPYC / Xeon Scalable) se:**
- Hai bisogno di **più di 4 GPU** o dual-socket.
- Hai bisogno di **larghezza di banda di memoria estrema** (inferenza CPU su modelli enormi, HPC).
- Hai già un **rack**, con alimentazione trifase, raffreddamento e rumore isolato.
- Hai bisogno di **RAM multi-terabyte**.
- Vuoi **massimizzare il rapporto prezzo/prestazioni sul mercato dell'usato**: i server datacenter dismessi (EPYC Rome/Milan, Xeon Scalable di prima e seconda generazione) possono essere trovati a frazioni del loro prezzo originale, e per coloro che hanno bisogno di linee e RAM più che di velocità di clock, possono essere ottimi affari. Questo è un percorso intrapreso da molti homelabber e piccoli laboratori. *[Il mercato dei server usati è estremamente volatile nei prezzi.]*

**Un'osservazione pratica che vale oro**: se hai bisogno di *molta* potenza *occasionalmente*, la risposta economicamente sensata è spesso **non comprare nulla** e noleggiare istanze cloud (o GPU on-demand da fornitori specializzati). Una workstation locale si giustifica quando l'utilizzo è **continuo** (mesi di training, uso quotidiano), quando i **dati non possono lasciare** l'organizzazione (privacy, GDPR, segreto industriale), o quando un rapido **ciclo iterativo** conta più della potenza di picco.

---

## 4. PCIe nelle Workstation: Approfondimento

### 4.1 Ripasso ed Estensione

Abbiamo già definito le lane e le generazioni. Aggiungiamo ora tre distinzioni che nel mondo consumer possono essere ignorate, ma nel mondo workstation sono decisive.

**Distinzione 1: slot fisico vs. slot elettrico.** Uno slot può essere **fisicamente** lungo x16 (cioè il connettore ha la lunghezza per ospitare una scheda x16) ma solo 4 o 8 lane sono cablate **elettricamente**. Questo è molto comune, e le schede madri lo indicano nella loro documentazione con notazioni come "PCIe 5.0 x16 (x8 mode)" o "x16 slot, x4 electrical". Una GPU inserita in uno slot fisico x16 ma elettrico x4 **funzionerà**, ma con un quarto della banda.

**Regola d'oro**: nel manuale della scheda madre, cerca **sempre** la "PCIe lane distribution table" o "block diagram". È l'unico documento che dice la verità. La descrizione commerciale sul sito web no.

**Distinzione 2: lane condivise.** Su molte schede madri, popolare un certo slot **disabilita o riduce** un altro slot o uno slot M.2. Il manuale contiene una tabella tipo "if PCIe_2 slot is used, M.2_3 slot is disabled." Sulle piattaforme workstation con 128 lane native, queste configurazioni condivise sono molto meno frequenti — ed è proprio uno dei benefici per cui si paga.

**Distinzione 3: negoziazione al ribasso.** PCIe negozia automaticamente sia la larghezza (numero di lane attive) che la velocità (generazione). Un segnale disturbato, un riser di bassa qualità, o un contatto sporco possono far sì che il link scenda a x8 invece di x16, o a Gen3 invece di Gen5, **senza alcun errore visibile**: il sistema funziona, solo più lentamente. Su Linux, questo si può controllare con `lspci -vv` cercando le righe `LnkCap` (capability) e `LnkSta` (current status); su NVIDIA, anche con `nvidia-smi -q | grep -i pcie`. **Questo controllo dovrebbe essere sempre eseguito dopo ogni assemblaggio.**

### 4.2 Perché 2-4 GPU hanno bisogno di vere lane elettriche x16

Qui dobbiamo essere precisi, perché c'è molta disinformazione che circola in entrambe le direzioni.

**Quando x16 NON è necessario.** Se il carico di lavoro è: una singola GPU, il dataset entra in VRAM o viene caricato una volta, e il training è puramente GPU-bound — allora la banda PCIe conta poco. Test noti su singola GPU mostrano differenze del 2-5% tra x8 e x16. In questo caso, spendere per una piattaforma workstation solo per avere x16 è irrazionale.

**Quando x16 è indispensabile.** Il quadro cambia completamente in tre scenari:

**Scenario A: addestramento distribuito multi-GPU.** Quando si utilizza il *data parallelism* (ogni GPU ha una copia del modello ed elabora un batch diverso), ad ogni passo di addestramento le GPU devono **sincronizzare i gradienti**. L'operazione si chiama **all-reduce**: ogni GPU deve ricevere la somma dei gradienti da tutte le altre. Il volume di dati è pari alla dimensione del modello, **ad ogni singolo passo**. Per un modello da 1 miliardo di parametri in FP16, sono 2 GB per GPU per passo. Con 4 GPU e un passo ogni 100 ms, **decine di GB/s vengono continuamente spostati attraverso il bus PCIe**. A x8 Gen4 (15 GB/s) questo diventa **il collo di bottiglia dominante**: la GPU calcola in 60 ms e poi attende 150 ms che arrivino i gradienti. L'efficienza di scaling crolla: quattro GPU performano come due e mezzo.

**Scenario B: model parallelism / pipeline parallelism.** Quando il modello è troppo grande per una GPU e viene splittato tra più GPU, le **attivazioni** devono passare da una GPU all'altra ad ogni forward e backward pass. Il traffico è continuo e sensibile alla latenza. Qui, la banda PCIe è ancora più critica.

**Scenario C: offloading e streaming.** Tecniche come ZeRO-Offload o lo scaricamento di parte dei pesi nella RAM di sistema (usato quando la VRAM è insufficiente) trasferiscono continuamente pesi avanti e indietro via PCIe. La banda è tutto.

**Conclusione**: se hai **una** GPU, x8 va benissimo. Se ne hai **due o più e le usi insieme**, ogni lane conta.

### 4.3 Switch PCIe (PLX / Broadcom): cosa sono e quando usarli

Uno **switch PCIe** è un chip che fa quello che fa uno switch di rete per Ethernet: prende un certo numero di lane in ingresso (*upstream*, verso la CPU) e le espande in un numero maggiore di lane in uscita (*downstream*, verso le periferiche), instradando dinamicamente i pacchetti.

I chip più noti sono quelli originariamente prodotti da **PLX Technology**, azienda poi acquisita da **Avago/Broadcom**. Per abitudine, quindi, si usa "PLX" per riferirsi a qualsiasi switch PCIe. La famiglia storica è **PEX 87xx/88xx** (Gen3/Gen4); le recenti generazioni Gen5 sono commercializzate da Broadcom con nomi come **Atlas 3** / **PEX 89xxx**.

**Come funziona in pratica.** Immagina uno switch con 16 lane upstream verso la CPU e 32 lane downstream verso due GPU (x16 ciascuna). Le due GPU vedono ciascuna un link x16 e sono contente. Ma verso la CPU, c'è solo un x16 condiviso.

**Il vero, e spesso incompreso, beneficio**: lo switch **non crea banda dal nulla**. Se entrambe le GPU vogliono comunicare con la CPU simultaneamente a piena velocità, si contendono le 16 lane upstream. **Il beneficio è che le due GPU possono comunicare tra loro, attraverso lo switch, a piena velocità x16, senza mai disturbare la CPU.** Questa comunicazione locale **peer-to-peer** è esattamente ciò che serve nell'addestramento multi-GPU. Uno switch trasforma quattro GPU collegate a x4 ciascuna in un cluster dove le GPU si scambiano i gradienti a piena banda tra loro, usando l'uplink verso la CPU solo per caricare i dati.

**Quando uno switch ha senso:**
- Su schede madri consumer o entry-workstation, per far funzionare 4 GPU con un numero insufficiente di lane della CPU.
- Su schede madri server progettate per la densità di GPU (le classiche baseboard da 8 GPU).
- Su schede di espansione (backplane) che espandono uno slot x16 in più slot.

**Quando NON ha senso:**
- Se si dispone già di 128 lane native (Threadripper PRO, EPYC). Le **lane native sono sempre migliori**: latenza inferiore, nessun punto di contesa, nessun costo, nessun consumo energetico aggiuntivo.
- Gli switch PCIe sono costosi (centinaia di dollari per chip), consumano 10-25 W e aggiungono latenza. È una tecnologia da usare per necessità, non per scelta.

### 4.4 Biforcazione: dividere uno slot x16

La **biforcazione** è la capacità della CPU e del BIOS di **dividere un singolo slot x16 in più link indipendenti**: tipicamente x8+x8, x8+x4+x4, o **x4+x4+x4+x4**.

È importante capire che la **biforcazione è passiva**. Non c'è un chip aggiuntivo: la CPU viene semplicemente istruita a trattare quelle 16 lane come quattro link separati da 4 lane. Il chip PCIe della CPU supporta nativamente questa configurazione; il BIOS espone l'opzione (solitamente nel menu "PCIe Bifurcation" o "PCIe Slot Configuration").

**L'applicazione principale nelle workstation AI**: **schede quad M.2**. Si tratta di schede di espansione PCIe x16 passive, senza alcun controller a bordo, che espongono quattro slot M.2. Ogni SSD NVMe utilizza 4 lane, e il totale è esattamente 16. Costano poche decine di euro (il PCB, i connettori e un dissipatore) e permettono di aggiungere **quattro SSD NVMe a piena velocità** utilizzando un singolo slot.

Esempi comuni: ASUS Hyper M.2 X16, Gigabyte AORUS Gen4 AIC, ASRock Hyper Quad M.2. Alcune schede madri per workstation includono già connettori dedicati (SlimSAS, MCIO) che espongono le lane in modo simile.

**Attenzione alla classica trappola**: se il BIOS **non supporta la biforcazione** su quello slot, una scheda quad M.2 passiva mostrerà **solo un SSD** (il primo), o nessuno. Questo non è un difetto della scheda: è la piattaforma che non può dividere le lane. Esistono anche schede quad M.2 **attive**, che contengono uno switch PCIe e funzionano ovunque — ma costano 5-10 volte tanto. **Prima di acquistare una scheda quad M.2 passiva, controllate il manuale della scheda madre per assicurarvi che lo slot supporti la modalità x4x4x4x4.** Le piattaforme Threadripper/PRO e workstation quasi sempre la supportano; le piattaforme consumer spesso no, o solo su un singolo slot.

### 4.5 P2P (peer-to-peer) su PCIe vs NVLink

Il **P2P** (peer-to-peer) è la capacità di due GPU di **scambiare dati direttamente**, senza instradare i dati attraverso la RAM di sistema. Senza P2P, una copia da GPU0 a GPU1 richiede: GPU0 → RAM di sistema → GPU1. Con P2P: GPU0 → GPU1, direttamente sul bus PCIe. I risparmi sono in larghezza di banda (una copia invece di due), latenza e utilizzo della CPU.

Questa capacità è la base delle librerie di comunicazione collettiva come **NCCL** (*NVIDIA Collective Communications Library*), che è ciò che PyTorch e ogni framework di training distribuito usano sotto il cofano per l'all-reduce dei gradienti.

**Il problema**: NVIDIA ha **disabilitato il P2P sulle GPU GeForce** a partire dalle generazioni recenti (dalla RTX 30 in poi, il supporto è stato progressivamente rimosso; sulle RTX 40 e 50, il P2P tramite driver ufficiali **non è disponibile**). Questa è una scelta di segmentazione commerciale, non tecnica: il silicio potrebbe farlo. La conseguenza è che su una workstation con quattro RTX 5090, NCCL è costretto a usare il percorso attraverso la RAM di sistema, con costi significativi in larghezza di banda e latenza.

*(Una curiosità ben nota nella comunità: esiste un driver modificato, sviluppato dal team tinygrad, che riabilita il P2P sulle RTX 4090. Funziona, ma non è supportato, è fragile rispetto agli aggiornamenti e non adatto a un ambiente di produzione.)*

**NVLink** è l'alternativa proprietaria di NVIDIA: un interconnettore **dedicato**, separato dal PCIe, con una larghezza di banda molto più elevata (nell'ordine di centinaia di GB/s per link, rispetto ai 63 GB/s di un x16 Gen5) e una latenza molto più bassa. Storicamente disponibile tramite un "bridge" fisico tra due schede su Quadro/RTX serie A, e tramite NVSwitch nelle configurazioni server (SXM).

**La situazione attuale, da ben comprendere** *[dato volatile]*: NVIDIA ha **rimosso il connettore NVLink dalle schede professionali in formato PCIe** a partire dalla generazione Ada Lovelace (le RTX 6000 Ada e successive non hanno il bridge). Oggi, il "vero" NVLink è riservato alle GPU in **formato SXM** (moduli montati direttamente sulla baseboard in sistemi server come DGX/HGX) e alle piattaforme rack-scale.

**Conseguenza pratica**, ed è il messaggio che porta questa sezione nel capitolo CPU: **su una workstation con GPU in formato PCIe — che è ciò che la stragrande maggioranza di noi costruirà — la comunicazione inter-GPU passa attraverso il PCIe. Punto.** Non c'è NVLink a salvare una configurazione con corsie insufficienti. Ed è precisamente per questo che le corsie PCIe della CPU non sono un dettaglio della specifica: **sono l'interconnessione del cluster**. È la ragione tecnica ultima per cui esiste Threadripper PRO.

*(Approfondiremo NVLink, NVSwitch, i formati SXM e le topologie multi-GPU nel capitolo dedicato alle GPU.)*

---

## 5. Scelta della Scheda Madre della Workstation

La scheda madre, in questo segmento, non è un accessorio: è **il documento che definisce cosa la macchina sarà in grado di fare**. Ecco i criteri, in ordine di importanza.

### 5.1 Numero di Slot x16 Fisici E Elettrici

Già trattato, ma vale la pena ripeterlo perché è il criterio numero uno. Cercate nel manuale (non sul sito commerciale) il **diagramma a blocchi** e verificate quante corsie sono effettivamente cablate a ciascuno slot e in quale generazione. Una scheda con "5 slot PCIe x16" che in realtà ha due x16 elettrici, un x8 e due x4 è un prodotto diverso da uno con cinque veri slot x16.

### 5.2 Spaziatura degli Slot (il Criterio Più Sottovalutato)

Questo è, statisticamente, **l'errore più frequente in assoluto** per chi costruisce la sua prima workstation multi-GPU.

Le moderne GPU di fascia alta occupano **2, 2.5 o anche 3 slot di spessore** a causa dei dissipatori sovradimensionati. Una RTX 5090 custom può essere una scheda da 3.5 slot. Se la scheda madre ha slot x16 distanziati di **due slot**, e la GPU ne occupa **tre**, **la seconda GPU semplicemente non ci starà fisicamente**. Non è una questione di prestazioni: è una questione di geometria.

E anche quando ci stanno, ci sono due ulteriori problemi:
- **Soffocamento Termico**: due GPU adiacenti con dissipatori a ventola aperta avranno l'aspirazione della seconda a un millimetro dal backplate della prima. Le temperature salgono di 15–25 °C, e le GPU vanno in *thermal throttling* (riduzione automatica della frequenza per limitare la temperatura). Le prestazioni crollano, e il rumore esplode.
- **Peso e Flessione**: una GPU moderna pesa 2 kg. Quattro GPU montate a sbalzo fletteranno la scheda madre e i PCB delle GPU stesse. Sono necessarie staffe di supporto.

**Come risolvere il problema:**
1. Scegliere schede madri progettate per più GPU: le schede WRX90 di riferimento hanno **7 slot con spaziatura calcolata** specificamente per ospitare 4 schede dual/triple-slot in slot alternati (1, 3, 5, 7).
2. Preferire GPU **blower-style** (a turbina, con aria espulsa fuori dal case) o in formato **2-slot** — questi sono i formati delle schede professionali (le serie RTX PRO/A sono progettate per essere impilate).
3. Utilizzare **riser PCIe** di qualità (certificati per la generazione corretta: un riser Gen3 su un link Gen5 causa un degrado silenzioso) per spostare le GPU in posizioni ventilate, con un **case da mining/open-frame** o un case workstation di grandi dimensioni.

**Regola pratica**: prima di acquistare, **misurare**. Prendere lo spessore della GPU (in millimetri, non "in slot") e la distanza tra gli slot sulla scheda madre. Uno slot PCIe standard ha un passo di **20.32 mm** (0.8 pollici).

### 5.3 Canali e Slot RDIMM

Verificare **quanti slot DIMM** ci sono e **come sono organizzati in canali**. Una scheda WRX90 con 8 slot DIMM su 8 canali offre un modulo per canale: massima frequenza, configurazione ideale. Alcune schede hanno più slot per canale (2 DIMM per canale, "2DPC"): consentono maggiore capacità ma spesso a frequenza ridotta.

**Punto critico che rovina molte build**: su piattaforme a 8 canali, **popolare solo 4 slot dimezza la larghezza di banda della memoria**. Se acquisti una WRX90 e inserisci 4 moduli da 32GB invece di 8 moduli da 16GB, hai la stessa capacità (128GB) ma **metà della larghezza di banda**. Hai pagato per 8 canali e ne stai usando solo 4. **Riempire sempre tutti i canali.** Questa singola regola è forse il consiglio più prezioso dell'intero capitolo, perché l'errore è invisibile: il sistema funziona, tutto sembra a posto, e semplicemente gira a metà velocità.

### 5.4 Rete: 10G Ethernet e oltre

Le schede madri per workstation di fascia alta integrano tipicamente **doppio 10 GbE** (10 gigabit al secondo). Perché è importante:

- **Dataset su NAS/storage di rete**: Un dataset da 10 TB non entra su un SSD locale. Risiede su un server di storage. Con 1 GbE (~110 MB/s reali), il caricamento è impossibile. Con 10 GbE (~1.1 GB/s reali), diventa fattibile.
- **Addestramento distribuito tra macchine**: Se hai due workstation e vuoi distribuire l'addestramento, la rete diventa l'interconnessione, ed è già molto lenta rispetto al PCIe. 10 GbE è il minimo; per lavori seri, sono necessari 25/100 GbE o **InfiniBand** (una tecnologia di rete a bassissima latenza dominante nell'HPC, che supporta **RDMA** — *Remote Direct Memory Access* — cioè la capacità di scrivere direttamente nella memoria di un'altra macchina senza coinvolgere la CPU).

Se la scheda madre non ha 10 GbE integrato, ricorda che una NIC 10G occupa uno slot PCIe e 4 lane. Deve essere conteggiata nel budget delle lane.

### 5.5 IPMI / BMC: gestione remota

**BMC** sta per *Baseboard Management Controller*: è un **microcontrollore autonomo** saldato sulla scheda madre (il più comune è l'ASPEED AST2500/AST2600), con la sua CPU, la sua RAM, il suo firmware e **la sua porta Ethernet**. Funziona **anche quando la macchina è spenta**, purché l'alimentatore sia collegato alla rete elettrica.

**IPMI** (*Intelligent Platform Management Interface*) è il protocollo standard per la comunicazione.

Cosa ti permette di fare, da un browser, da qualsiasi parte del mondo:
- **Accendere, spegnere e riavviare** la macchina.
- Visualizzare il **video della console** come se fossi seduto davanti al monitor — **inclusi il BIOS e il POST** (*Power-On Self-Test*, la sequenza diagnostica all'avvio). Questa funzione si chiama **KVM over IP** (*Keyboard, Video, Mouse over IP*).
- **Montare un'immagine ISO remota** come se fosse una chiavetta USB inserita nella macchina: puoi quindi **reinstallare il sistema operativo da remoto**.
- Leggere **tutti i sensori**: temperature, voltaggi, velocità delle ventole, consumo energetico.
- Consultare i **log hardware** (SEL, *System Event Log*), inclusi gli **eventi ECC corretti**.

**Perché è cruciale in una workstation AI**: perché una workstation AI è una macchina che lasci accesa per giorni, spesso in un'altra stanza, in cantina o in un piccolo rack. Quando il training si blocca alle tre del mattino e la macchina non risponde via SSH, senza BMC devi alzarti, andare fisicamente lì e collegare un monitor. Con BMC, apri il browser e vedi la schermata di kernel panic. **La prima volta che ti salva, il BMC si è ripagato.**

Il BMC è quasi sempre presente sulle schede **server** (Supermicro, ASRock Rack, Gigabyte, Tyan) e su alcune workstation di fascia alta (alcune WRX90). Non è presente sulle schede consumer o sulla maggior parte delle schede TRX50 "desktop".

### 5.6 Marche e Modelli di Riferimento

*[Modelli e disponibilità volatili]*

| Marca | Posizionamento | Modelli Tipici | Note |
|---|---|---|---|
| **ASUS Pro WS** | Workstation "desktop-friendly" | Pro WS WRX90E-SAGE SE, Pro WS TRX50-SAGE WIFI, Pro WS W790E-SAGE SE | Punto di riferimento del settore per workstation multi-GPU. La WRX90E-SAGE SE con 7 slot x16 completi è di fatto lo standard. |
| **Supermicro** | Server e workstation di livello server | M13SWA-TF (sTR5), serie H13/X13 | Qualità e affidabilità server, BMC/IPMI di serie, ma BIOS spartano e supporto orientato alle aziende, non ai singoli. |
| **ASRock Rack** | Server, ottimo rapporto qualità-prezzo | WRX90D8-2T, TRX50D8-2L2T | Form factor server (SSI-EEB o proprietario), IPMI, spesso più convenienti. Attenzione al form factor del case. |
| **Gigabyte (Server)** | Server e barebone | MZ73/MZ33 (EPYC), TRX50 AERO D | Buona gamma, dai barebone completi alle singole schede. |
| **Tyan** | Server, nicchia HPC | Vari | Meno comune nella vendita al dettaglio in Europa. |

**Consiglio pratico**: Se stai costruendo una workstation da tenere in ufficio e vuoi un'esperienza "desktop" (BIOS usabile, ventole silenziose, form factor ATX/E-ATX standard, supporto RMA consumer), scegli **ASUS Pro WS**. Se stai costruendo qualcosa che vivrà in un rack o in una sala tecnica e vuoi BMC e affidabilità 24/7, scegli **Supermicro** o **ASRock Rack**, ma preparati a un'esperienza più "industriale".

### 5.7 Alimentazione: EPS Multipli e PSU Server-Grade

Un aspetto che i costruttori alle prime armi sottovalutano sistematicamente.

Il **connettore EPS12V** (spesso chiamato semplicemente "EPS" o "CPU power") è il connettore a **8 pin** che fornisce 12 volt alla sezione **VRM** della CPU. **VRM** sta per *Voltage Regulator Module*: è il circuito sulla scheda madre che converte i 12 V dall'alimentatore nella tensione molto più bassa (circa 1.0–1.3 V) e nella corrente molto alta (centinaia di ampere) che la CPU richiede. Il VRM è composto da **fasi** (stadi di conversione paralleli): più fasi significano che può essere erogata più corrente con meno stress termico su ciascuna.

Una CPU Threadripper con un TDP di 350 W, durante i transitori, può assorbire ben oltre 400 W. Con un'efficienza VRM di circa il 90%, l'alimentatore deve fornire più di 450 W **solo sul rail della CPU**. Un singolo connettore EPS a 8 pin è specificato per circa **300 W**. **Questo non è sufficiente.** Questo è il motivo per cui le schede madri sTR5 hanno **due (o anche tre) connettori EPS a 8 pin**, e **devono essere tutti collegati**. Collegarne solo uno, nel migliore dei casi, comporterà l'accensione con avvisi e prestazioni limitate; nel peggiore dei casi, instabilità sotto carico o surriscaldamento dei connettori.

Il budget energetico complessivo di una workstation multi-GPU:

| Componente | Consumo Indicativo |
|---|---|
| CPU Threadripper PRO (350 W TDP) | 400–450 W di picco |
| GPU di fascia alta (es. 450–600 W TDP ciascuna) | 4 × 500 = **2.000 W** |
| RAM (8 moduli RDIMM) | ~50 W |
| NVMe (4) | ~40 W |
| Ventole, pompe, scheda madre | ~100 W |
| **Picco Totale** | **~2.600 W** |

Con un margine del 20% per i transitori (le GPU moderne hanno picchi istantanei — *transient spikes* — che possono superare il consumo nominale del 50-100% per pochi millisecondi, e un alimentatore che non li assorbe farà scattare la protezione OCP/OPP e spegnerà la macchina), si raggiungono **3.000 W o più**.

Qui incontriamo due ostacoli:

**Muro 1: La presa elettrica.** In Italia, una presa domestica standard su un circuito da 16 A, 230 V fornisce teoricamente ~3.680 W, ma i circuiti domestici e i contratti di fornitura standard (3 kW o 4.5 kW) non possono gestire una macchina da 3 kW **più tutto il resto della casa**. È necessario un circuito dedicato, e spesso un aumento della potenza contrattuale. Nei paesi a 110 V (USA), il problema è ancora più grave: una presa da 15 A, 120 V fornisce 1.800 W teorici, ~1.440 W in uso continuo – **motivo per cui le workstation a 4 GPU negli USA richiedono quasi sempre due circuiti separati o una presa da 240 V**.

**Muro 2: L'alimentatore.** Gli alimentatori ATX consumer tipicamente raggiungono 1.600–2.000 W *[volatile]*. Per andare oltre, le opzioni sono:
- **Doppio alimentatore ATX** sincronizzato con un "add2psu" (un adattatore che accende il secondo PSU quando si accende il primo, ponticellando il segnale PS_ON). Funziona, è comune negli ambienti di mining e homelab, ma deve essere fatto con attenzione: **non alimentare mai la stessa scheda da due PSU diversi** (ad esempio: slot PCIe da PSU 1 e connettori aggiuntivi GPU da PSU 2), perché i ground possono avere potenziali diversi e danneggiare l'hardware.
- **Alimentatori di livello server** (Delta, Murata, alimentatori HP/Dell "Common Slot") con breakout board, spesso con ingresso a 240 V, che possono raggiungere 2.400–3.000 W in un singolo modulo. Questa è la strada professionale, ma richiede attenzione al rumore (le ventole di questi alimentatori sono progettate per un rack, non per un ufficio).
- **Ridurre il consumo via software**: con `nvidia-smi -pl <watt>` è possibile impostare un limite di potenza per GPU. Ridurre una GPU da 500 W a 350 W (−30%) costa tipicamente solo il 5–10% in termini di prestazioni, perché la curva potenza/prestazioni è altamente non lineare nella fascia alta. **Su una workstation a 4 GPU, questa singola riga di comando può ridurre il consumo da 2.000 a 1.400 W praticamente a costo zero.** Questa è una tecnica standard nei laboratori seri, non un compromesso da poveri.

### 5.8 Raffreddamento del socket sTR5

Una nota che merita un suo spazio, perché è un errore da principiante con conseguenze immediate.

L'IHS di una CPU sTR5 è **molto più grande** di quello di una CPU AM5. Un dissipatore consumer, anche il migliore, ha una base progettata per coprire circa 40×40 mm. Su sTR5, lascerebbe gran parte della superficie scoperta, e – peggio ancora – con Threadripper, i die (i CCD) sono **distribuiti su tutta l'area del package**, non concentrati al centro. Un dissipatore che copre solo il centro lascia i CCD periferici a "cuocere".

**Sono necessarie soluzioni specifiche per sTR5:**
- **Raffreddamento ad aria**: Noctua NH-U14S TR5-SP6 (e varianti), IceGiant ProSiphon. Si tratta di dissipatori con basi enormi e un gran numero di heatpipe distribuite.
- **Raffreddamento a liquido AIO** (*All-In-One*, il sistema chiuso pompa+radiatore): SilverStone XE360-TR5, Arctic Liquid Freezer per TR5 *[disponibilità volatile]*. **Cruciale**: il *cold plate* (la piastra fredda a contatto con la CPU) deve essere **full-coverage size** per sTR5. Un AIO con un cold plate consumer, anche montato con adattatori, **non funzionerà**.
- **Custom loop**: con un waterblock dedicato sTR5.

E, lo ripeto perché è importante: il montaggio richiede il **cacciavite dinamometrico** fornito con la CPU e il **serraggio nell'ordine numerato** stampato sul socket. Non è una formalità.

---

## 6. Come dimensionare la CPU per il carico di lavoro AI

Arriviamo alla domanda pratica: **quale CPU devo comprare?** La risposta dipende da *cosa* fa la macchina.

### 6.1 Training GPU-bound: la CPU serve per il Data Loading

Nel tipico training di computer vision o di modelli con dataset ampi, il ciclo è:

1. Un **worker** (un processo separato, gestito in PyTorch da `DataLoader` con `num_workers`) legge un file dal disco.
2. Lo **decodifica** (un JPEG va decompresso: è un'operazione CPU-intensive, e per un'immagine ad alta risoluzione può richiedere millisecondi).
3. Applica le **trasformazioni** (resize, crop, flip, color jitter, normalization): altro lavoro per la CPU.
4. Impacchetta il risultato in un tensore e lo copia in **pinned memory** (memoria non paginabile, "bloccata", da cui il DMA della GPU può leggere direttamente).
5. Il tensore viene trasferito alla GPU via PCIe.

Se i worker non tengono il passo, la GPU aspetta. Questo è **il** bottleneck più comune nelle workstation sbilanciate ed è facilmente diagnosticabile: `nvidia-smi dmon` che mostra l'utilizzo della GPU fluttuare tra 30% e 100% invece di rimanere piatto al 95% è il sintomo classico.

**La regola del pollice**, condivisa nella comunità e consistente con le configurazioni dei sistemi di riferimento (i sistemi NVIDIA DGX storicamente hanno circa 8 core fisici per GPU):

> **4–8 core fisici per GPU**, a seconda dell'intensità della pipeline di preprocessing.

- **Pipeline leggera** (dati già pre-processati, tensori binari, formato `.npy` o Arrow/WebDataset, nessuna decodifica): bastano **2–4 core per GPU**.
- **Pipeline media** (JPEG standard, classiche augmentations): **4–6 core per GPU**.
- **Pipeline pesante** (immagini ad altissima risoluzione, video, augmentations complesse, decodifica audio): **8+ core per GPU**, oppure — soluzione migliore — **spostare il preprocessing sulla GPU** con librerie come NVIDIA DALI (che esegue la decodifica JPEG in hardware, sui decoder NVDEC/nvJPEG della GPU stessa) o con la decodifica video accelerata.

**Corollario importante**: se il tuo problema è il caricamento dei dati, la risposta *migliore* spesso non è comprare più core, ma **pre-elaborare il dataset una volta** e salvarlo in un formato pronto all'uso (tensori, TFRecord, shard WebDataset). È gratuito e sposta il collo di bottiglia sul disco, dove un NVMe lo gestisce senza sforzo.

### 6.2 Quando i Core Contano Davvero

Ci sono carichi di lavoro in cui la CPU **è** il calcolo, non solo il supporto:

**Inferenza CPU.** Come discusso, questa è dominata dalla larghezza di banda della memoria durante la fase di generazione. Ma nella fase di **prefill** (elaborazione del prompt iniziale, che è una grande moltiplicazione di matrici), i core contano molto, e le istruzioni vettoriali (**AVX-512**, **AMX** su Intel) fanno un'enorme differenza. Una CPU con AMX può eseguire il prefill in una frazione del tempo. **Se l'inferenza CPU è centrale per il tuo flusso di lavoro, considera seriamente Intel Xeon con AMX o piattaforme AMD con molti canali di memoria.**

**Pre-elaborazione Classica e Feature Engineering.** Pipeline Pandas/Polars/DuckDB su dataset di decine o centinaia di GB. Qui, i core scalano linearmente (se il codice è parallelizzato), e la larghezza di banda della memoria conta molto. Questo è il caso d'uso in cui un Threadripper a 32 o 64 core brilla davvero.

**Tokenizzazione.** Tokenizzare un corpus di centinaia di GB di testo per addestrare un modello linguistico è un'operazione **massivamente parallela e CPU-bound**. Con la libreria `tokenizers` di Hugging Face (scritta in Rust, che rilascia il GIL), 64 core possono fare in un'ora ciò che 8 core fanno in otto ore.

**Compilazione.** Se lavori su codice CUDA, kernel Triton, compilando PyTorch da sorgente, o compilando modelli con TensorRT: `make -j64` su un Threadripper è una rivelazione. La compilazione è forse il carico di lavoro che scala meglio con i core.

**Simulazione e elaborazione classica.** CFD, calcolo scientifico, rendering 3D per generare dataset sintetici, ottimizzazione combinatoria: tutti CPU-bound.

### 6.3 Configurazioni Tipiche per Fascia di Budget

Ecco il nocciolo operativo. *[Prezzi indicativi, volatili, riferiti al mercato europeo. Verificare sempre.]*

#### Livello ENTRY — Workstation Single-GPU (~€2.500–€5.000)

**Il messaggio più importante: qui il consumatore VINCE.**

| Componente | Scelta |
|---|---|
| CPU | AMD Ryzen 9 9950X (16c) o Intel Core Ultra 9 285K |
| Scheda Madre | X870E / Z890 di fascia alta |
| RAM | 64–128 GB DDR5 (2 moduli per evitare degrado di frequenza; 4 moduli se è necessaria la capacità) |
| GPU | 1 × RTX 5090 (32 GB) o RTX PRO 4500/5000 |
| Archiviazione | 2 × NVMe Gen4/Gen5 (1 TB sistema + 2–4 TB dataset) |
| PSU | 1.000–1.200 W |
| Note | No ECC certificato. Se l'ECC è necessario, l'unica alternativa è aggiornare la piattaforma. |

**Per chi è**: chi fa fine-tuning con LoRA/QLoRA, inferenza locale, prototipazione, computer vision su dataset medi, corsi e ricerca individuale. **Questo copre il 70–80% dei casi reali.** Non aggiornare la piattaforma se non hai un motivo.

#### Livello MID — Workstation 2 GPU (~€7.000–€12.000)

| Componente | Scelta |
|---|---|
| CPU | **AMD Threadripper 7960X / 9960X** (24 core) |
| Scheda Madre | TRX50 (ASUS Pro WS TRX50-SAGE WIFI, ASRock TRX50 WS) |
| RAM | 128–256 GB DDR5 RDIMM ECC, **4 moduli su 4 canali** |
| GPU | 2 × RTX 5090 o 2 × RTX PRO 6000 (a **full x16 PCIe 5.0**) |
| Archiviazione | 2–4 × NVMe Gen5 |
| PSU | 1.600 W+ (o doppia PSU) |
| Raffreddamento | AIO 360 mm specifico sTR5 |

**A chi è rivolto**: ricercatori seri, piccoli team, coloro che necessitano di ECC e due GPU che comunicano veramente. **24 core sono più che sufficienti**: 12 per GPU, ben al di sopra della regola pratica.

#### Livello HIGH-END — Workstation con 4+ GPU (~€20.000–€50.000)

| Componente | Scelta |
|---|---|
| CPU | **AMD Threadripper PRO 7955WX / 7965WX / 9955WX** (16–24 core) — **o 7975WX/7995WX se il carico di lavoro è anche CPU-bound** |
| Scheda madre | **WRX90** (ASUS Pro WS WRX90E-SAGE SE, ASRock Rack WRX90D8-2T) |
| RAM | 256 GB–1 TB DDR5 RDIMM ECC, **8 moduli su 8 canali — riempire tutti i canali!** |
| GPU | 4 × RTX PRO 6000 Blackwell (96 GB ciascuna) o 4 × RTX 5090 |
| Archiviazione | 4+ × NVMe Gen5 (uno per il sistema, un array per i dataset) |
| Rete | 10/25 GbE integrata o NIC dedicata |
| PSU | 2 × 1.600 W, o PSU server-grade da 3.000 W su circuito dedicato |
| Case | Chassis workstation grande o open-frame |
| Note | **Verificare la spaziatura degli slot rispetto allo spessore delle GPU scelte.** |

**Attenzione al punto centrale**: nel segmento high-end, **la CPU PRO si acquista per le sue 128 lane e 8 canali, non per i suoi core**. Un **7955WX da 16 core** costa una frazione del 7995WX e fornisce **le stesse 128 lane e 8 canali**. Sedici core per quattro GPU = 4 core per GPU: al limite inferiore della regola pratica, ma sufficiente con una pipeline ben ottimizzata (dataset pre-processato, DALI). Se il pre-processing è pesante, passare al **7965WX (24c)** o **7975WX (32c)**. Il **7995WX (96c)** ha senso solo **se il carico di lavoro è genuinamente CPU-intensive** (rendering, simulazione, compilazione massiva) — altrimenti, sono ~€9.500–10.000+ di silicio inattivo.

#### Livello EXTREME — Server (€50.000+)

Qui, passiamo dalle workstation ai server: **EPYC 9004/9005** o **Xeon 6**, dual socket, 8 GPU sulla scheda madre, rack, alimentazione trifase, raffreddamento a liquido diretto. Questa è una categoria diversa, con fornitori diversi (Supermicro, Dell, HPE, Lenovo, Gigabyte) e logiche di acquisto diverse (si compra un sistema, non lo si assembla). Merita un capitolo a sé.

### 6.4 La regola di dimensionamento in tre righe

Se dovessi condensare tutta la sezione 6:

1. **Contare le GPU.** 1 → consumer. 2 → Threadripper. 3+ → Threadripper PRO. 8+ → server.
2. **Contare le lane necessarie.** GPU × 16 + NVMe × 4 + NIC. Se il totale supera le lane della CPU, aggiornare la piattaforma.
3. **Contare i core.** 4–8 per GPU. Se il risultato è inferiore ai core della CPU di cui hai bisogno per le lane, **non pagare per core extra**: prendi la SKU più economica per quella piattaforma.

---

## 7. Errori comuni

Li raccogliamo qui, in ordine di frequenza e gravità, perché ognuno costa denaro reale.

### Errore 1 — Acquistare Threadripper PRO per una singola GPU

**L'errore.** "Voglio una workstation AI seria, quindi prenderò un Threadripper PRO." Poi viene installata una sola GPU.

**Perché è un errore.** La piattaforma PRO costa €3.000–€5.000 in più (CPU + scheda madre + RAM RDIMM) e fornisce 128 lane, di cui ne userai 20. Inoltre, le frequenze single-thread di un Threadripper sono **inferiori** a quelle di un Ryzen 9 della stessa generazione, e il tuo codice Python di orchestrazione — che è single-thread — girerà **più lentamente**. Hai speso di più per andare più lento nella parte che usi ogni giorno.

**Cosa fare invece.** Prendi un Ryzen 9 9950X o un Core Ultra 9, investi la differenza in **una GPU migliore** o **più RAM**. Una singola RTX PRO 6000 con 96 GB di VRAM ti permette di addestrare modelli che quattro RTX 5090 da 32 GB non possono contenere senza complesse tecniche di partizionamento.

### Errore 2 — Ignorare la spaziatura degli slot

**L'errore.** Acquistare quattro GPU custom a triplo slot e una scheda madre con slot distanziati di due.

**Perché è un errore.** Le GPU **non ci staranno**. Oppure ci staranno ma si soffocheranno termicamente a vicenda e andranno in throttling. Il costo è: restituire le GPU (se possibile), o acquistare riser e un case diverso, o vivere con una riduzione delle prestazioni del 20-30%.

**Cosa fare invece.** **Misura in millimetri prima di acquistare.** Controlla il layout degli slot nel manuale della scheda madre. Preferisci GPU **blower** o professionali a 2 slot per configurazioni dense. Il passo standard di uno slot PCIe è di 20,32 mm.

### Errore 3 — RAM sbagliata

Questo errore ha tre varianti, tutte comuni.

**Variante A: UDIMM su una piattaforma RDIMM.** Acquistare un bel kit DDR5 da gaming solo per scoprire che la scheda TRX50/WRX90 **non fa nemmeno il POST**. Threadripper (anche non-PRO), Threadripper PRO, EPYC e Xeon W **richiedono ECC RDIMM**. Non è un'opzione, è un requisito.

**Variante B: Non riempire tutti i canali.** Acquistare 4 moduli da 64 GB (256 GB totali) per una WRX90 a 8 canali. Funziona, hai la capacità che volevi — **e metà della larghezza di banda della memoria**. Hai pagato per una piattaforma a 8 canali e ne stai usando solo 4. **Acquista sempre un numero di moduli pari al numero di canali** (o un multiplo).

**Variante C: Niente ECC dove è necessario.** Addestrare per tre giorni su una macchina senza ECC e non capire mai perché la loss occasionalmente va a NaN.

### Errore 4 — Sottovalutare l'alimentazione

**L'errore.** Collegare un solo connettore EPS su una scheda che ne ha due. Acquistare un alimentatore da 1.000 W per una macchina che consuma 2.000 W. Ignorare i picchi transitori della GPU. Collegare tutto a una presa domestica con un contratto da 3 kW.

**Perché è un errore.** I sintomi sono i peggiori possibili: **spegnimenti casuali sotto carico**, che si verificano dopo ore di addestramento, senza log, senza un pattern riproducibile. Si passano settimane a incolpare il software, i driver, il kernel. È l'alimentatore che va in protezione.

**Cosa fare invece.** Bilancia il budget di potenza con un margine del 25-30%. Collega **tutti** i connettori EPS. Considera seriamente il **power limiting** delle GPU (`nvidia-smi -pl`): il rapporto costo/beneficio è imbattibile. Controlla l'impianto elettrico della stanza.

### Errore 5 — Sottovalutare il raffreddamento sTR5

**L'errore.** Usare un dissipatore AM5 con un adattatore, o un AIO con una piastra fredda consumer.

**Perché è un errore.** Il package sTR5 è enorme e i CCD sono distribuiti. Una piccola piastra fredda raffredda il centro e lascia i die periferici a temperature di throttling. Risultato: una CPU da 5.000 € che funziona al 60% della sua frequenza.

**Cosa fare invece.** Dissipatore **specifico per sTR5**, con una base a copertura totale. Stringere con un **cacciavite dinamometrico**, nell'**ordine numerato**.

### Errore 6 — Fidarsi della descrizione commerciale degli slot

**L'errore.** Acquistare una scheda madre perché "ha 4 slot PCIe x16" e scoprire che due sono x4 elettrici e collegati al chipset.

**Cosa fare invece.** Scarica il **manuale PDF** e cerca il **diagramma a blocchi** e la **tabella di distribuzione delle linee PCIe**. Se il produttore non li pubblica, è un campanello d'allarme.

### Errore 7 — Sopravvalutare le esigenze di core, sottovalutare le esigenze di RAM

**L'errore.** Spesa per un 96-core e installazione di 128 GB di RAM.

**Perché è un errore.** I 96 core rimarranno al 10% durante l'addestramento. I 128 GB si esauriranno rapidamente, perché la regola generale è **RAM di sistema ≥ 1,5–2× VRAM totale** (per contenere il dataset in cache, i buffer dei worker, la memoria bloccata e qualsiasi offloading). Con 4 GPU da 48 GB (192 GB VRAM), 128 GB di RAM sono **insufficienti**: sono necessari almeno 384 GB.

**Cosa fare invece.** SKU della CPU più economica della piattaforma giusta + più RAM.

### Errore 8 — Acquistare hardware quando era necessario il cloud

**L'errore.** Spesa di 30.000 € per una workstation che verrà utilizzata a pieno carico due settimane all'anno.

**Cosa fare invece.** Effettuare una valutazione onesta delle ore/GPU necessarie all'anno e confrontarla con il costo delle istanze cloud o dei fornitori di GPU on-demand. Una workstation locale è giustificata per **uso continuo**, **dati sensibili che non possono lasciare la sede**, o **rapida iterazione quotidiana**. Non per prestigio.

---

## Riepilogo Operativo: Lista di Controllo per la Decisione della CPU della Workstation AI

Da utilizzare **in quest'ordine**. Ogni passaggio dipende dal precedente.

### FASE 1 — Definire il Carico di Lavoro (prima di guardare un singolo prodotto)

- [ ] **Quante GPU** installerò, oggi e realisticamente entro 2 anni? (Questa è la domanda determinante.)
- [ ] Il carico di lavoro è **GPU-bound** (addestramento/inferenza su GPU) o c'è una componente significativa **CPU-bound** (pre-elaborazione pesante, tokenizzazione, compilazione, simulazione, inferenza CPU)?
- [ ] Le GPU dovranno **comunicare tra loro** (addestramento distribuito) o lavoreranno indipendentemente (es. inferenza parallela su richieste diverse)? Se comunicano, le corsie sono critiche; se no, molto meno.
- [ ] Quanta **VRAM totale** avrò? → La RAM di sistema dovrebbe essere **1,5–2 volte** tale quantità.
- [ ] Il **dataset** è su disco locale o sulla rete? → Se sulla rete, è necessaria una 10 GbE o superiore.
- [ ] Ho davvero bisogno di hardware locale, o il **cloud** è più economico per il mio profilo di utilizzo?

### FASE 2 — Calcolare le Corsie PCIe Necessarie

- [ ] GPU × 16 = ______ corsie
- [ ] NVMe × 4 = ______ corsie
- [ ] NIC (4 per 10G, 8–16 per 25/100G) = ______ corsie
- [ ] Altre schede (HBA, acquisizione, acceleratori) = ______ corsie
- [ ] **Corsie TOTALI necessarie = ______**

### FASE 3 — Scegliere la Piattaforma in Base al Totale

| Corsie Necessarie | GPU | Piattaforma |
|---|---|---|
| ≤ 24 | 1 | **Consumer** (Ryzen 9 / Core Ultra) — ed è perfettamente adeguata |
| 25–48 | 2 | **Threadripper non-PRO su TRX50** |
| 49–128 | 3–7 | **Threadripper PRO su WRX90** |
| > 128 | 8+ | **EPYC / Xeon Scalable, server** |

- [ ] È necessario **ECC certificato**? → Se sì, Threadripper minimo (RDIMM ECC obbligatorio).
- [ ] È necessaria **più di 256 GB di RAM**? → Se sì, Threadripper minimo.
- [ ] È necessaria la **massima larghezza di banda della memoria** (inferenza CPU su LLM di grandi dimensioni)? → 8 canali (WRX90) o 12 canali (EPYC).
- [ ] È necessaria la **gestione remota**? → Scheda madre con BMC/IPMI.

### FASE 4 — Scegliere la SKU della CPU all'interno della Piattaforma

- [ ] Calcolare i core necessari: **4–8 core fisici per GPU**.
- [ ] Aggiungere core se il carico di lavoro ha una componente reale legata alla CPU (tokenizzazione, compilazione, simulazione, pre-elaborazione pesante non trasferibile alla GPU).
- [ ] **Se il risultato è inferiore ai core della SKU più economica su quella piattaforma, prendere la SKU più economica.** Non pagare per core che rimarranno inattivi. (Esempio: 4 GPU + pipeline leggera → **7955WX/9955WX con 16 core** su WRX90, non il 7995WX.)
- [ ] Controllare il **suffisso**: **WX = PRO** (8 canali, 128 linee). **X senza W = non-PRO** (4 canali, 48 linee). **Non confonderli.**
- [ ] Se l'inferenza della CPU è centrale → considerare **Intel Xeon con AMX**.

### FASE 5 — Scegliere la scheda madre

- [ ] Scaricare il **manuale PDF** e trovare il **diagramma a blocchi** / **tabella di distribuzione delle linee PCIe**. Non fidarsi della pagina del prodotto.
- [ ] Contare gli slot **ELETTRICI x16** (non solo fisici) e la loro generazione PCIe.
- [ ] **Misurare la spaziatura degli slot** (passo standard: 20.32 mm per slot) e confrontarla con lo **spessore in mm** delle GPU che si acquisteranno.
- [ ] Verificare il numero di **slot DIMM** e la loro organizzazione dei canali. Confermare che si acquisterà **un modulo per canale**.
- [ ] Verificare il supporto alla **biforcazione** (x4x4x4x4) se si prevede di utilizzare schede M.2 quad passive.
- [ ] Verificare la presenza di **10 GbE** integrato (o tenere conto delle linee per una NIC).
- [ ] Verificare la presenza di **BMC/IPMI** se la macchina non sarà a portata di mano.
- [ ] Contare i connettori **EPS 8-pin** richiesti e verificare che l'alimentatore li abbia tutti.
- [ ] Verificare il **fattore di forma** (E-ATX, SSI-EEB, proprietario) e la compatibilità con il case scelto.

### FASE 6 — Memoria

- [ ] Tipo: **RDIMM ECC** su tutte le piattaforme Threadripper/PRO/EPYC/Xeon W. Nessuna eccezione.
- [ ] Numero di moduli = **numero di canali** (4 su TRX50, 8 su WRX90, 12 su EPYC). **Non lasciare canali vuoti.**
- [ ] Capacità totale ≥ **1.5–2 × VRAM totale**.
- [ ] Controllare la **QVL** (Qualified Vendor List) della scheda madre prima di acquistare i moduli.

### FASE 7 — Alimentazione e raffreddamento

- [ ] Budget di alimentazione: CPU + (GPU × numero) + RAM + storage + ventole, **+25–30% di margine per i transitori**.
- [ ] Verificare che l'**impianto elettrico della stanza** possa gestirlo (in Italia: contratto e circuito dedicati per macchine sopra i ~2 kW).
- [ ] Considerare il **power limiting** per le GPU (`nvidia-smi -pl`): −30% di consumo per −5/10% di prestazioni.
- [ ] Collegare **tutti** i connettori EPS sulla scheda madre.
- [ ] **Dissipatore specifico per sTR5** con base a copertura totale. **Non usare mai adattatori AM5/LGA1700.**
- [ ] Montare la CPU con il **cacciavite dinamometrico in dotazione**, nell'**ordine numerato** stampato sul socket.

### FASE 8 — Verifica post-assemblaggio (non saltarla mai)

- [ ] `lspci -vv` → confrontare **LnkCap** (capacità) con **LnkSta** (stato attuale) per ogni GPU e NVMe. Se un link è negoziato a x8 invece di x16, o a Gen3 invece di Gen5, **c'è un problema hardware da risolvere subito**, non tra tre mesi.
- [ ] `nvidia-smi -q | grep -i pcie` → verificare larghezza e generazione del link GPU.
- [ ] Verificare in BIOS/OS che l'**ECC sia attivo e riporti eventi** (su Linux: `edac-util`, `dmidecode -t memory`, il log SEL del BMC).
- [ ] Eseguire uno **stress test combinato** (CPU + tutte le GPU al 100% simultaneamente) per almeno un'ora e monitorare temperature, clock (per verificare l'assenza di throttling) e stabilità dell'alimentazione.
- [ ] Verificare la **reale banda passante della memoria** con un benchmark (STREAM, `mlc` di Intel): se è la metà di quanto previsto, i canali sono stati popolati in modo errato.
- [ ] Eseguire un **training reale** e osservare l'**utilizzo della GPU**: se non è stabilmente superiore al 90%, c'è un collo di bottiglia da diagnosticare (quasi sempre: caricamento dei dati o link PCIe negoziato al ribasso).

---

*Una nota finale sulla volatilità dei dati: prezzi, disponibilità, generazioni di CPU e matrici di compatibilità cambiano rapidamente in questo settore. Tutti i modelli, i prezzi e i conteggi SKU citati devono essere verificati rispetto alla documentazione ufficiale del produttore al momento dell'acquisto. I principi architetturali — corsie come valuta, canali come banda passante, ECC come assicurazione, spaziatura come vincolo fisico — sono stabili, tuttavia, e rimarranno validi anche al cambiare dei nomi dei prodotti.*

---

*Nel prossimo capitolo, discuteremo le GPU per l'AI: architetture, VRAM, tensor core, NVLink e NVSwitch, fattori di forma SXM versus PCIe e come scegliere l'acceleratore giusto per il proprio carico di lavoro.*

---

[← Precedente](09-assemblaggio-pc.html) · [Tutti i capitoli](./) · [Successivo →](11-gpu-nvidia-ai.html)
