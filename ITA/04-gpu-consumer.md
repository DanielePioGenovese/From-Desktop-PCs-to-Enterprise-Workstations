---
title: "Capitolo 4 - La Scheda Grafica (GPU Consumer)"
parent: "Edizione italiana"
nav_order: 4
---

<details open markdown="block">
  <summary>Indice del capitolo</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

> **Nota su dati e periodo di riferimento.** Questo capitolo è aggiornato a metà 2026. Questo è un momento anomalo per il mercato delle schede grafiche: dall'ultimo trimestre del 2025 si è verificata una grave **carenza di memoria** (DRAM e GDDR), causata dalla domanda dei data center per l'intelligenza artificiale, che ha spinto i prezzi di listino ben al di sopra degli **MSRP** (*Manufacturer's Suggested Retail Price*). Per questo motivo, ogni volta che in questo capitolo citerò un prezzo, mi riferirò ad esso come "prezzo di listino/MSRP" e avvertirò che il prezzo effettivo al dettaglio, specialmente in Italia ed Europa (dove si aggiungono IVA e logistica), può essere significativamente più alto e cambiare di settimana in settimana. I dati su generazioni, modelli e prezzi vanno quindi verificati al momento dell'acquisto: sono la parte di questo capitolo che invecchia più rapidamente. I principi tecnici, invece, rimangono validi nel tempo.

---

## 1. Cosa fa la GPU

### Descrizione e funzione

La **GPU** (*Graphics Processing Unit*) è il componente del computer specializzato nel **calcolo massivo parallelo**. Per capire cosa significhi, è utile contrapporla alla **CPU** (*Central Processing Unit*). La CPU è progettata per eseguire poche istruzioni molto complesse in rapida successione: ha un numero relativamente basso di "core" (unità di elaborazione), ognuno molto potente e flessibile, adatto a compiti che richiedono continue e imprevedibili decisioni logiche, come l'esecuzione del sistema operativo o la logica di un programma. La GPU segue la filosofia opposta: contiene migliaia di core piccoli e semplici, ognuno singolarmente meno potente, ma capaci di lavorare tutti insieme nello stesso istante sullo stesso tipo di operazione. Questa è l'architettura ideale quando lo stesso calcolo deve essere applicato contemporaneamente a milioni di punti dati distinti.

Il caso d'uso storico che ha dato origine alla GPU è il **rendering 3D**, ovvero la trasformazione di una scena tridimensionale descritta matematicamente (vertici, triangoli, texture, luci) in un'immagine bidimensionale di pixel da visualizzare su uno schermo. Un monitor da 1920×1080 pixel contiene poco più di due milioni di pixel; a 3840×2160 (il cosiddetto 4K), si superano gli otto milioni. Se ogni fotogramma deve essere ricalcolato sessanta, centoventi o più volte al secondo, il numero di operazioni al secondo diventa astronomico. Nessuna CPU potrebbe gestirlo: serve un'architettura che calcoli milioni di pixel in parallelo, ed è esattamente ciò che fa la GPU.

Tuttavia, con il tempo ci si è resi conto che questo stesso motore di calcolo parallelo è utile anche al di fuori del gaming. Oggi la GPU è usata per la **codifica e decodifica video** (comprimere e decomprimere flussi video, fondamentale per lo streaming e l'editing), per il **calcolo scientifico**, e soprattutto per l'**intelligenza artificiale locale**: addestrare e soprattutto eseguire (fare *inference*) reti neurali, dai modelli di generazione di immagini come Stable Diffusion ai modelli linguistici di grandi dimensioni (**LLM**) eseguiti sul proprio PC. La ragione è la stessa del rendering: le reti neurali si riducono a enormi moltiplicazioni di matrici, che è lo stesso calcolo ripetuto milioni di volte su dati diversi. È questa convergenza tra grafica e IA che ha reso la GPU il componente più strategico — e più conteso — dell'intero settore hardware.

### I Componenti Fisici di una Scheda Grafica

Quando parliamo di "scheda grafica" in senso stretto, intendiamo l'intero prodotto: una scheda a circuito stampato con vari componenti su di essa, che viene inserita nel computer. Il chip grafico vero e proprio è solo una parte. Vediamoli uno per uno.

Il **die della GPU** è il chip di silicio vero e proprio: il processore grafico. È qui che risiedono i core di calcolo (che NVIDIA chiama *CUDA core* e *Tensor core*, AMD *Stream Processors* e *AI Accelerators*), le unità dedicate al ray tracing e i controller di memoria. È il cuore del prodotto e ne determina la potenza di calcolo grezza.

La **VRAM** (*Video RAM*) è la memoria dedicata della scheda, fisicamente separata dalla RAM di sistema. Contiene texture, modelli 3D, buffer di rendering e — nel caso dell'IA — i pesi del modello neurale. È un elemento così centrale che le dedichiamo l'intera prossima sezione.

Il **VRM** (*Voltage Regulator Module*) è il sistema di alimentazione della scheda. La GPU riceve un'alimentazione a 12 volt dall'alimentatore, ma il chip opera a tensioni molto più basse (circa 1 volt) e amperaggi molto elevati. Il VRM converte e stabilizza questa energia, distribuendola in modo pulito al die. Un VRM di buona qualità, con molte "fasi" di alimentazione, consente clock più elevati, temperature più basse e una maggiore durata; un VRM economico è spesso il vero limite delle schede di fascia bassa e delle versioni più povere.

Il **PCB** (*Printed Circuit Board*) è la scheda verde (o nera) su cui è montato tutto. La sua qualità, il numero di strati di rame e il layout delle tracce influenzano la stabilità elettrica e la capacità di overclock.

Il **sistema di dissipazione** dissipa il calore prodotto dal die e dai VRM. È tipicamente composto da una *vapor chamber* o *heat pipes* che trasportano il calore a una pila di alette di alluminio, raffreddate da una o più ventole. Nelle schede più potenti, la dissipazione è la parte più voluminosa e pesante del prodotto, e questo spiega perché due schede con lo stesso chip possono avere dimensioni, livelli di rumore e prezzi molto diversi.

### Errori tipici a questo livello

L'errore più comune del principiante è confondere la **GPU die** (il chip) con la **scheda video** (il prodotto completo). Questo porta a un secondo fraintendimento: pensare che due schede con lo stesso nome (ad esempio, due "RTX 5070") siano identiche. Non lo sono: condividono il chip, ma il produttore di terze parti che le assembla sceglie il dissipatore, i VRM, le frequenze di fabbrica e l'alimentazione. Questa è la differenza tra versioni *reference* e versioni *custom*, di cui parleremo ampiamente nella sezione 6.

---

## 2. VRAM e bus di memoria

### Cos'è la VRAM e perché è diversa dalla RAM di sistema

La **VRAM** è la memoria ad altissima velocità saldata direttamente sulla scheda video, attorno alla GPU die. Serve a immagazzinare tutti i dati su cui il chip grafico deve lavorare in un dato momento: le texture (le "pelli" applicate ai modelli 3D), la geometria della scena, i vari buffer di rendering intermedi e, nelle applicazioni AI, i pesi della rete neurale. La differenza rispetto alla RAM di sistema (moduli DDR4 o DDR5 inseriti nella scheda madre e usati dalla CPU) è duplice. In primo luogo, la VRAM è enormemente più veloce in termini di banda: una scheda video di fascia media muove centinaia di gigabyte al secondo, mentre un kit di RAM di sistema si ferma a poche decine. In secondo luogo, la VRAM è *dedicata* e locale: si trova a pochi millimetri dal chip che la usa, evitando il collo di bottiglia del bus **PCIe** (*Peripheral Component Interconnect Express*, il canale che collega la scheda video al resto del computer). Se la GPU dovesse leggere le texture dalla RAM di sistema passando ogni volta per il PCIe, le prestazioni crollerebbero.

Le generazioni di VRAM che si incontrano oggi sono principalmente tre. La **GDDR6** (*Graphics Double Data Rate 6*) è lo standard maturo ed economico, usato ancora oggi da AMD su tutta la serie RX 9000 e da NVIDIA sui tier inferiori delle passate generazioni. La **GDDR6X** è una variante più veloce sviluppata da NVIDIA con Micron, presente sulle RTX serie 30 e 40 di fascia alta. La **GDDR7** è la generazione più recente, adottata da NVIDIA su tutta la serie RTX 50: offre maggiore banda a parità di ampiezza del bus ed è più efficiente. Al momento della stesura, AMD sulla serie RX 9000 (RDNA 4) ha scelto di rimanere con la GDDR6, affidandosi a bus più ampi e a cache interne per compensare — una scelta che ha anche il vantaggio di costi inferiori, non indifferente durante la crisi delle memorie.

### Il bus di memoria e la formula della banda

Il **bus di memoria** è l'ampiezza del canale che collega la GPU die alla VRAM, misurata in bit. Valori tipici sono 128, 192, 256 e 384 bit (con casi estremi come i 512 bit della RTX 5090). Va immaginato come la larghezza di un'autostrada: più corsie (più bit) permettono di far passare più dati contemporaneamente. Da solo, però, il bus non dice nulla: conta anche la velocità a cui viaggia ogni corsia, ovvero la frequenza effettiva dei chip di memoria.

La metrica che riassume il tutto è la **banda** (memory bandwidth), ovvero quanti gigabyte al secondo la GPU può scambiare con la sua VRAM. La formula concettuale è:

> **Banda (GB/s) = (velocità effettiva della memoria in Gbps × ampiezza del bus in bit) ÷ 8**

Dividiamo per 8 perché un byte è composto da 8 bit. Prendiamo un esempio concreto: una scheda con memoria GDDR6 a 20 Gbps su un bus a 256 bit ha una larghezza di banda di (20 × 256) ÷ 8 = **640 GB/s**. Una scheda con lo stesso tipo di memoria ma un bus a 128 bit si ferma a (20 × 128) ÷ 8 = **320 GB/s**, che è la metà. Ecco perché il bus è un fattore importante quanto la quantità di VRAM.

La seguente tabella mostra come, a parità di velocità di memoria, la larghezza del bus determini la larghezza di banda finale. I valori sono indicativi e servono a stabilire ordini di grandezza.

| Larghezza Bus | Memoria 18 Gbps (GDDR6) | Memoria 20 Gbps (GDDR6) | Memoria 28 Gbps (GDDR7) |
|:---:|:---:|:---:|:---:|
| 128 bit | 288 GB/s | 320 GB/s | 448 GB/s |
| 192 bit | 432 GB/s | 480 GB/s | 672 GB/s |
| 256 bit | 576 GB/s | 640 GB/s | 896 GB/s |
| 384 bit | 864 GB/s | 960 GB/s | 1344 GB/s |

È subito chiaro perché la GDDR7 sia interessante: permette di ottenere elevate larghezze di banda anche con bus più stretti, il che consente ai produttori di risparmiare sul numero di chip e sulla complessità del PCB. Ed è anche il motivo per cui non si può confrontare la larghezza di banda di due schede guardando solo i bit del bus senza considerare il tipo di memoria.

### Quanta VRAM ti serve davvero

La quantità di VRAM necessaria dipende da tre fattori interconnessi: la **risoluzione** a cui si gioca o si lavora, la **qualità delle texture** e degli effetti, e la presenza o assenza di funzionalità esigenti come il **ray tracing** (calcolo fisico realistico di luci, ombre e riflessi). Vale una regola generale: maggiore è la risoluzione, più texture ad alta definizione devono risiedere contemporaneamente in memoria, quindi più VRAM è necessaria. A ciò si aggiunge il fatto che il ray tracing richiede strutture dati aggiuntive in memoria, e alcune tecnologie di *frame generation* (generazione di frame intermedi guidata dall'AI) consumano VRAM aggiuntiva.

Come riferimento pratico, valido a metà 2026 e destinato a crescere nel tempo man mano che i giochi diventeranno più esigenti:

| Scenario d'uso | VRAM Minima Raccomandata | VRAM Confortevole |
|:---|:---:|:---:|
| Gaming 1080p, dettagli medio-alti | 8 GB | 12 GB |
| Gaming 1440p, dettagli alti + ray tracing | 12 GB | 16 GB |
| Gaming 4K, dettagli massimi + ray tracing | 16 GB | 20–24 GB |
| Stable Diffusion / generazione immagini | 8–12 GB | 16 GB+ |
| LLM locali quantizzati (7–8 miliardi di parametri) | 8–12 GB | 16 GB |
| LLM locali di medie dimensioni (13–34 miliardi) | 24 GB | 24–48 GB |

Nel campo dell'**AI locale**, la VRAM è ancora più critica che nel gaming, e per una ragione chiara: un modello neurale deve stare *interamente* nella memoria della scheda grafica per funzionare a piena velocità. Se il modello non ci sta, o non si avvia affatto, o viene "spalmato" tra VRAM e RAM di sistema, portando a un crollo delle prestazioni. Per questo motivo, per chi lavora con l'AI, la quantità di VRAM diventa spesso il criterio di selezione numero uno, ancora più della potenza bruta del chip. Un modello linguistico da 7–8 miliardi di parametri quantizzato a 4 bit occupa circa 5–6 GB e sta comodamente su una scheda da 12 GB; un modello da 70 miliardi, invece, richiede decine di gigabyte e rimane appannaggio di schede professionali o configurazioni multi-GPU.

### Il caso delle GPU "castrate" sul bus

C'è una ricorrente trappola commerciale che questo capitolo deve esplicitare: la scheda con **tanta VRAM ma un bus stretto**. Un produttore, per rendere appetibile un modello di fascia bassa, potrebbe equipaggiarlo con 16 GB di memoria — un numero che fa un'ottima figura sulla scatola — ma collegarla con un bus di soli 128 bit. Il risultato è che la scheda ha lo spazio per ospitare texture abbondanti, ma l'insufficiente banda passante non le permette di *muoverle* abbastanza velocemente. È come avere un enorme magazzino con una sola porta stretta: la merce c'è, ma non può entrare e uscire alla velocità richiesta.

Questo scenario è particolarmente deludente in due casi: ad alte risoluzioni, dove la banda passante è il fattore limitante, e nei giochi con streaming continuo di texture. L'acquirente inesperto compra "16 GB" convinto di aver fatto un affare a prova di futuro, solo per scoprire che una scheda con "soli" 12 GB ma un bus più largo la surclassa nelle prestazioni reali. La lezione è chiara: **la VRAM non va mai valutata in isolamento, ma sempre in congiunzione con il bus e il tipo di memoria**, ovvero guardando alla banda passante complessiva. Una buona abitudine è controllare sempre tre numeri insieme — capacità (GB), ampiezza del bus (bit) e tipo di memoria (GDDR6/6X/7) — prima di farsi influenzare dalla sola capacità.

---

## 3. Nomenclatura NVIDIA: Come Leggere il Nome

### La Struttura del Nome

NVIDIA vende le sue schede consumer sotto il marchio **GeForce RTX**. L'acronimo **RTX** (*Ray Tracing eXtension*) indica, dalla serie 20 in poi, la presenza di unità hardware dedicate al ray tracing e ai calcoli AI; il vecchio acronimo **GTX** identificava schede prive di tali unità. Il nome vero e proprio è un numero a quattro cifre che segue uno schema molto regolare, del tipo **RTX [serie][tier]**.

Prendiamo l'esempio della **RTX 4070**. Le prime due cifre, "40", indicano la **generazione** (in questo caso, la serie 40, architettura Ada Lovelace). Le successive due cifre, "70", indicano il **tier** all'interno di quella generazione. Quindi "RTX 4070" si legge come "scheda di tier 70 della quarta generazione RTX". Con lo stesso criterio, una RTX 5080 è il tier 80 della quinta generazione, una RTX 3060 è il tier 60 della terza generazione, e così via. Una volta compreso questo schema, il nome di qualsiasi scheda NVIDIA diventa immediatamente leggibile.

### Le Serie (Generazioni)

Ogni generazione corrisponde a un'**architettura** diversa, ovvero a un design interno del chip. Le tre più rilevanti per gli acquirenti odierni sono:

La **serie 30 (Ampere)**, rilasciata nel 2020, è ormai datata ma ancora molto popolare nel mercato dell'usato. Ha introdotto la seconda generazione di RT core e il primo DLSS veramente utilizzabile.

La **serie 40 (Ada Lovelace)**, rilasciata a fine 2022, ha portato un grande salto di efficienza e le tecnologie DLSS 3 con frame generation. Non è più in produzione, quindi si trova principalmente usata o come scorte residue, ma rimane interessante nel rapporto prezzo/prestazioni proprio perché è stata costruita con memorie meno costose di quelle attuali.

La **serie 50 (Blackwell)**, l'attuale generazione consumer al momento della stesura, annunciata al CES di gennaio 2025 e lanciata nei mesi successivi. Introduce la quarta generazione di RT core, la quinta generazione di Tensor core, memorie GDDR7 su tutta la gamma e le tecnologie DLSS 4 con **Multi-Frame Generation** (generazione di più frame intermedi invece di uno solo).

> **Attenzione — dati in evoluzione.** A metà 2026, si parla con insistenza di un aggiornamento di metà generazione chiamato **RTX 50 SUPER** (con più VRAM: le voci suggeriscono 18 GB sulla 5070, 24 GB sulla 5070 Ti e 5080, e una 5060 da 12 GB). Al momento della stesura, tuttavia, questi modelli **non sono ancora stati lanciati** e il loro debutto è stato posticipato più volte proprio a causa della carenza di memoria, con alcune fonti che lo spostano al 2027 o lo considerano incerto. Dovrebbero quindi essere trattati come voci non confermate, da verificare al momento dell'acquisto.

### I Livelli (Tier)

Le ultime due cifre codificano il posizionamento e, con esso, ciò che ci si può aspettare in termini di prestazioni e prezzo. Lo schema, valido come regola generale tra le generazioni, è il seguente:

Il **livello 50** è l'assoluto entry-level, progettato per il gaming leggero a 1080p e l'uso multimediale. Il **livello 60** rappresenta il grande volume del mercato: la scheda "popolare" per il 1080p di alta qualità e per il 1440p con alcuni compromessi. Il **livello 70** è il cuore del segmento di fascia media, dedicato al 1440p ad alto refresh e al 4K con l'aiuto dell'upscaling. Il **livello 80** è la fascia alta, progettato per il gaming 4K con dettagli elevati. Il **livello 90** è l'entusiasta, il top assoluto della gamma consumer, con massima VRAM e potenza, orientato al 4K esigente, alla creazione di contenuti e all'IA — e con prezzi che sconfinano nel territorio professionale.

### I Suffissi: Ti e SUPER

Oltre al numero base, NVIDIA utilizza due suffissi per creare varianti intermedie. Il suffisso **Ti** (storicamente *Titanium*) indica una versione potenziata del livello a cui è associato: una RTX 4070 Ti si posiziona tra la 4070 e la 4080, ed è più veloce della semplice 4070. Il suffisso **SUPER** svolge un ruolo simile, tipico degli aggiornamenti di metà generazione: indica una revisione migliorata di un modello esistente, spesso con più core, più VRAM o più larghezza di banda. In alcuni casi, entrambi coesistono (ad esempio, "RTX 4070 Ti SUPER"). La regola pratica da tenere a mente è che, all'interno della stessa generazione, l'ordine crescente di prestazioni tende ad essere: modello base → SUPER → Ti → Ti SUPER, per poi salire di livello numerico.

### Tabella Riassuntiva delle Recenti Generazioni NVIDIA

La tabella mostra i modelli principali, con VRAM, bus e MSRP al lancio. **Si prega di notare che, a causa della carenza di memoria, i prezzi effettivi a metà 2026 sono spesso molto più alti degli MSRP indicati**, specialmente nei livelli di fascia alta.

| Modello | Gen. / Architettura | VRAM | Tipo Mem. | Bus | MSRP al Lancio (USD) |
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

### Errori Comuni di Nomenclatura NVIDIA

L'errore più comune è confrontare due schede basandosi solo sul tier, ignorando la generazione: una RTX 4070 di nuova generazione può superare o eguagliare una RTX 3080 più vecchia e di tier superiore, grazie a un'architettura più efficiente e a nuove tecnologie di upscaling. Il secondo errore riguarda la RTX 5060 Ti, venduta in due versioni, 8 GB e 16 GB, con lo stesso nome: la versione da 8 GB è penalizzata nei giochi moderni e a risoluzioni più elevate, e per pochi euro in più, la versione da 16 GB è quasi sempre la scelta migliore. Il terzo errore è farsi ingannare dal tier 90 pensando che sia "il doppio" dell'80: le schede di tier 90 offrono prestazioni superiori, ma con un rapporto prezzo/prestazioni che peggiora significativamente man mano che si sale, perché a quel tier si paga anche l'esclusività e l'abbondante VRAM, più utile per i professionisti che per i giocatori.

---

## 4. Convenzione di Naming AMD: Come Leggere il Nome

### La Struttura del Nome

AMD vende le sue schede grafiche consumer sotto il marchio **Radeon RX**. Lo schema di denominazione assomiglia a quello di NVIDIA: **RX [serie][tier]**. Prendiamo la **RX 7800 XT**. Le prime due cifre, "78", vanno lette come "serie 7000, tier 800": la serie 7000 è la generazione, e 800 è il suo posizionamento all'interno di quella generazione. Il suffisso "XT" indica, come vedremo, la variante più potente di quel modello. Come per NVIDIA, il tier aumenta con il numero: una RX 7600 è entry-level, una RX 7900 è high-end.

### Le Serie (Generazioni)

Esistono tre generazioni AMD rilevanti oggi, tutte basate sull'architettura **RDNA** (*Radeon DNA*):

La **serie RX 6000 (RDNA 2)**, del 2020, è ormai datata ma ancora disponibile sul mercato dell'usato, offrendo un buon rapporto prezzo/prestazioni nella pura rasterizzazione, ma con un ray tracing più debole rispetto alle controparti NVIDIA contemporanee.

La **serie RX 7000 (RDNA 3)**, di fine 2022, ha introdotto un design a *chiplet* (il chip diviso in più die separati) e la tecnologia FSR 3 con frame generation. Include ancora i modelli di fascia alta RX 7900 XT e XTX, poiché la generazione successiva ha abbandonato il segmento *enthusiast*.

La **serie RX 9000 (RDNA 4)**, l'attuale generazione al momento della scrittura, lanciata a marzo 2025. Il salto nella numerazione (da 7000 a 9000, saltando 8000) allinea il branding delle GPU con quello dei processori Ryzen. RDNA 4 ha migliorato significativamente il ray tracing (acceleratori di terza generazione) e ha introdotto **FSR 4**, la prima versione dell'upscaling di AMD basata sul machine learning. È importante notare che questa generazione **non ha un modello di tier enthusiast**: AMD ha scelto di concentrarsi sul segmento *mid-range*, con la RX 9070 XT al vertice della lineup.

### I suffissi: XT, XTX, GRE

AMD utilizza suffissi per distinguere le varianti di potenza con lo stesso numero. Il modello **senza suffisso** è la versione base. Il suffisso **XT** indica una versione più potente, con più unità di calcolo o clock più elevati: la RX 9070 XT è più veloce della semplice RX 9070. Il suffisso **XTX**, apparso sulla serie 7000, indica la variante massima, un passo sopra la XT (come nella RX 7900 XTX rispetto alla 7900 XT). Il suffisso **GRE** (*Golden Rabbit Edition*, un nome inizialmente creato per il mercato cinese per l'Anno del Coniglio) identifica una versione leggermente depotenziata con un bus più stretto, posizionata tra due modelli della stessa serie per coprire una fascia di prezzo intermedia; la RX 9070 GRE, ad esempio, ha 12 GB su un bus a 192 bit rispetto ai 16 GB su un bus a 256 bit della semplice 9070, e nel 2026 è stata resa disponibile a livello globale dopo un debutto iniziale esclusivo per la Cina.

### Tabella di equivalenza approssimativa AMD ↔ NVIDIA

Questa tabella abbina i modelli per **fascia di prezzo e prestazioni indicative**, non per esatta equivalenza tecnica. Le prestazioni relative variano da gioco a gioco e, cosa più importante, cambiano a seconda che si consideri la pura rasterizzazione (dove AMD è competitiva) o il ray tracing (dove NVIDIA mantiene un vantaggio). Va quindi letta come una bussola, non come una legge.

| Livello / Prezzo indicativo | AMD (RDNA 4) | NVIDIA (Blackwell) |
|:---|:---|:---|
| Entry (~$300) | RX 9060 XT 8 GB | RTX 5060 |
| Entry-mid (~$350) | RX 9060 XT 16 GB | RTX 5060 Ti 16 GB |
| Mid (~$550) | RX 9070 | RTX 5070 |
| Mid-high (~$600) | RX 9070 XT | (tra 5070 e 5070 Ti) |
| High (~$750) | — (nessun modello) | RTX 5070 Ti |
| High / enthusiast ($900+) | — (nessun modello) | RTX 5080 |
| Absolute enthusiast ($2000+) | — (nessun modello) | RTX 5090 |

Il dato più eloquente della tabella è la riga dei trattini: nella generazione attuale, AMD **non compete affatto** nei segmenti di fascia alta e enthusiast, avendoli lasciati scoperti. Chiunque cerchi una scheda oltre la potenza della RX 9070 XT oggi si rivolge effettivamente solo a NVIDIA.

### Errori tipici nella nomenclatura AMD

Un errore ricorrente è confondere XT e XTX, o supporre che il suffisso GRE indichi qualcosa di migliore quando in realtà indica una variante ridotta. Un secondo errore, che rispecchia quello visto per NVIDIA, riguarda la RX 9060 XT venduta nelle versioni da 8 GB e 16 GB: la versione da 8 GB è meno consigliabile per i giochi moderni, e la differenza di prezzo con la 16 GB è spesso esigua. Un terzo errore è supporre che AMD sia sempre più economica: questo è vero in molti segmenti, ma le condizioni di mercato nel 2026 hanno reso i prezzi volatili anche per AMD, sebbene i modelli Radeon abbiano mantenuto meglio il loro MSRP rispetto alle controparti NVIDIA, grazie a forniture di memoria assicurate in anticipo.

---

## 5. NVIDIA vs AMD: Confronto Completo

Questa è la sezione definitiva per prendere decisioni. Confronto i due produttori sui piani che contano davvero, in modo che il lettore possa capire *quando* uno è conveniente e *quando* l'altro, senza pregiudizi.

### Pura Rasterizzazione: Prezzo/Prestazioni

La **rasterizzazione** è la tecnica tradizionale per la generazione di immagini, senza il calcolo fisico realistico della luce. Determina ancora la maggior parte delle prestazioni nei giochi. In quest'ambito, **AMD è storicamente molto competitiva**, offrendo spesso più frame al secondo per euro speso rispetto a NVIDIA nella stessa fascia. Nell'attuale generazione, la RX 9070 XT offre prestazioni in rasterizzazione vicine a quelle della più costosa RTX 5070 Ti, a un prezzo di listino inferiore: è il classico esempio del vantaggio AMD nel rapporto prezzo/prestazioni nel gaming puro.

### Ray Tracing: Vantaggio NVIDIA, Recupero RDNA 4

Il **ray tracing** simula il comportamento fisico della luce per ottenere riflessi, ombre e illuminazione realistici. È molto più pesante da calcolare rispetto alla rasterizzazione e richiede unità hardware dedicate (gli *RT core* di NVIDIA e i *Ray Accelerators* di AMD). Storicamente, **NVIDIA ha avuto un netto vantaggio** in questo campo: le sue schede perdono meno prestazioni quando il ray tracing viene attivato. Con RDNA 4, tuttavia, **AMD ha recuperato molto terreno**, riducendo significativamente il divario grazie agli acceleratori di terza generazione. Il vantaggio NVIDIA rimane, specialmente nei giochi con *path tracing* (la forma più estrema e realistica di ray tracing), ma il divario non è più abissale come nelle generazioni precedenti.

### Upscaling: DLSS vs FSR vs XeSS

L'**upscaling** è una tecnologia che permette di renderizzare un gioco a una risoluzione inferiore (es. 1080p) e poi ingrandirlo a una risoluzione superiore (es. 4K) tramite algoritmi intelligenti, guadagnando frame con una minima perdita di qualità. È diventato un fattore competitivo centrale, tanto quanto la potenza bruta. Le tre principali tecnologie sono:

Il **DLSS** (*Deep Learning Super Sampling*) di NVIDIA è considerato il punto di riferimento per qualità e diffusione. Si basa su reti neurali eseguite su Tensor core ed è esclusivo delle schede NVIDIA. Il DLSS 4, esclusivo della serie 50, introduce la **Multi-Frame Generation**, capace di generare più frame intermedi via AI per moltiplicare il frame rate; va però ricordato che la frame generation aggiunge latenza e non sostituisce le prestazioni "vere".

L'**FSR** (*FidelityFX Super Resolution*) di AMD è la risposta al DLSS. Fino alla versione 3, era *vendor-agnostic*, cioè funzionava anche su schede NVIDIA e Intel, ma con qualità inferiore. Con **FSR 4**, AMD è passata a un approccio basato su machine learning che ha migliorato molto la qualità, avvicinandola al DLSS, ma al costo di limitarlo alle schede RDNA 4 dotate degli acceleratori AI necessari.

Lo **XeSS** (*Xe Super Sampling*) di Intel è la terza opzione, creata per le schede Intel Arc ma utilizzabile in forma ridotta anche su altre marche. Offre una buona qualità ed è la "terza incomoda" del settore.

### Encoder: NVENC vs AMF

L'**encoder** è l'unità hardware che comprime video in tempo reale, essenziale per lo **streaming** (Twitch, YouTube) e per l'esportazione in **video editing**. NVIDIA dispone di **NVENC** (*NVIDIA Encoder*), universalmente considerato il migliore per qualità e stabilità, tanto da essere un motivo ricorrente per cui streamer e videomaker scelgono NVIDIA. AMD utilizza **AMF** (*Advanced Media Framework*): storicamente inferiore, è migliorato nelle ultime generazioni, ma NVENC mantiene un riconosciuto vantaggio, specialmente nella codifica ad alta efficienza (HEVC e AV1) a bassi bitrate.

### CUDA vs ROCm: perché NVIDIA domina per AI e produttività

Questo è forse il punto più importante per un lettore che, oltre al gaming, guarda all'**intelligenza artificiale e alla produttività**, ed è la base su cui poggerà l'intera sezione dedicata alle workstation. **CUDA** (*Compute Unified Device Architecture*) è la piattaforma software proprietaria di NVIDIA per il calcolo generico su GPU. Non è solo un'API: è un intero ecosistema, maturo da oltre quindici anni, su cui è costruito quasi tutto il software di IA — da PyTorch a TensorFlow, dai framework di generazione di immagini agli strumenti per LLM locali. La controparte AMD si chiama **ROCm** (*Radeon Open Compute*): è open e in continuo miglioramento, ma rimane meno matura, meno diffusa e con un supporto software più frammentato. Il risultato pratico è chiaro: **per l'IA, la produttività creativa accelerata e il calcolo scientifico, NVIDIA domina** non tanto per l'hardware quanto per il software. Chi acquista una scheda pensando di usarla per addestrare o eseguire modelli, editare video con effetti AI o lavorare con applicazioni professionali, nella maggior parte dei casi troverà molte meno frizioni con NVIDIA. Questa è una considerazione che, per certi profili di utilizzo, ribalta completamente il vantaggio prezzo/prestazioni di AMD nel gaming puro.

### Driver, consumi, prezzi

Sul fronte **driver**, entrambi i marchi offrono oggi software stabili, ma NVIDIA ha storicamente goduto di una reputazione di maggiore affidabilità al lancio di nuovi giochi, mentre AMD ha talvolta sofferto di problemi nelle prime settimane dopo l'uscita di una scheda, poi risolti con aggiornamenti. In termini di **consumi**, i due marchi si equivalgono nei rispettivi segmenti, con fluttuazioni modello per modello. Per quanto riguarda i **prezzi**, come già accennato, AMD tende a offrire di più a parità di denaro in rasterizzazione, mentre NVIDIA fa pagare l'ecosistema (DLSS, NVENC, CUDA); nel contesto del 2026, inoltre, i prezzi reali sono governati più dalla disponibilità di memoria che dai listini ufficiali.

### Il terzo incomodo: Intel Arc

Come terzo incomodo, va menzionata **Intel Arc**, la linea di schede grafiche di Intel. La serie **Battlemage** (schede come la Arc B580 12 GB) ha conquistato un'ottima reputazione nel segmento entry-level, offrendo più VRAM della concorrenza a prezzi aggressivi e encoder AV1 di qualità. I limiti storici di Arc sono i driver, meno maturi, specialmente nei giochi più datati, e un ecosistema software per l'IA che arranca rispetto a CUDA. Inoltre, a metà 2026, il futuro della linea gaming di Intel appare incerto: alcune voci suggeriscono un ridimensionamento dei modelli di fascia alta, dirottati verso il mercato professionale. Per l'acquirente attento al budget, tuttavia, una scheda Arc entry-level rimane una delle migliori opzioni VRAM/prezzo oggi, a patto di accettare qualche compromesso sui driver.

---

## 6. Partner AIB e versioni custom

### Founders Edition / reference vs custom

Quando NVIDIA e AMD progettano una scheda, ne producono anche una versione "ufficiale": NVIDIA la chiama **Founders Edition** (FE), AMD la chiama versione **reference**. Si tratta delle schede con il design di riferimento del produttore del chip. Accanto a queste, esiste un gran numero di **partner AIB** (*Add-In Board partners*, produttori di terze parti che acquistano i chip e assemblano le proprie schede): ASUS, MSI, Gigabyte, Sapphire, PowerColor e molti altri. Ognuno produce le proprie versioni **custom**, con dissipatori, PCB, VRM, clock ed estetica diversi. La stessa RTX 5070, quindi, esiste in decine di varianti fisicamente diverse, tutte con lo stesso chip ma con qualità costruttiva, temperature, rumorosità e prezzo che possono variare considerevolmente.

### Partner NVIDIA e le loro gamme

Ogni partner AIB organizza le proprie schede in linee di prodotti ordinate per qualità crescente. Conoscere questi nomi commerciali è essenziale per evitare di pagare troppo o troppo poco. I principali marchi partner NVIDIA, con le rispettive gamme dal basso all'alto, sono:

**ASUS** offre la linea **Dual** (entry, semplice dissipatore a doppia ventola), la **TUF Gaming** (fascia media robusta, ottimo equilibrio qualità/prezzo) e la **ROG Strix** (fascia alta, raffreddamento e VRM di prim'ordine, prezzi elevati). **MSI** offre la **Ventus** (entry), la **Gaming / Gaming Trio** (fascia media, con dissipatore a tripla ventola) e la **Suprim** (top di gamma, costruzione premium). **Gigabyte** ha la linea **Windforce / Gaming** (entry e fascia media) e la **Aorus** (fascia alta). Il quadro è completato da marchi come **Zotac** (spesso aggressiva sul prezzo, con modelli molto compatti), **Palit** e **Gainward** (buon rapporto qualità/prezzo, generalmente più economiche), **PNY** (diffusa e affidabile, senza fronzoli) e **INNO3D**.

### Partner AMD e "marchi di riferimento"

Il panorama AMD presenta alcune peculiarità. **Sapphire** è considerata il partner AMD di riferimento per eccellenza — l'equivalente di ciò che i migliori partner premium sono per NVIDIA — con la linea **Pulse** (fascia media, ottimo equilibrio) e la **Nitro+** (top di gamma, molto apprezzata). **PowerColor** è l'altro grande nome storico associato ad AMD, con le linee **Fighter** (entry), **Hellhound** (fascia media, silenziosa e ben bilanciata) e **Red Devil** (fascia alta). Completano il quadro **XFX**, partner storico esclusivo AMD, e **ASRock**, che più recentemente è entrata nel mercato delle schede grafiche con buoni risultati. Sapphire, PowerColor e XFX sono spesso definite "marchi di riferimento" di AMD perché lavorano quasi esclusivamente con Radeon, a differenza di ASUS, MSI e Gigabyte, che producono per entrambi i marchi.

### Quanto vale davvero la pena pagare di più?

La domanda pratica è: vale la pena spendere di più per una versione premium? La differenza tra una scheda base e una top di gamma si concentra su quattro aspetti. Il **dissipatore**, più grande e con più heat pipe, mantiene le temperature più basse. Le **frequenze di fabbrica**, leggermente più alte nelle versioni premium (le sigle "OC" indicano un *overclock* di fabbrica), portano tipicamente a modesti guadagni prestazionali, nell'ordine di pochi punti percentuali. La **rumorosità**, spesso inferiore nelle versioni premium a parità di temperatura, grazie a ventole migliori e curve più raffinate. L'**estetica**, con illuminazione RGB e finiture più curate. La ragionevole regola pratica è questa: **le versioni di fascia media (TUF, Pulse, Hellhound, Gaming) offrono il miglior compromesso** e sono quelle da consigliare nella stragrande maggioranza dei casi; le versioni top di gamma (ROG Strix, Suprim, Nitro+, Red Devil, Aorus) hanno senso solo per chi cerca il silenzio assoluto, l'overclock estremo o l'estetica, ed è disposto a pagare un sovrapprezzo che raramente si traduce in prestazioni significativamente superiori. Attenzione, però, alle versioni più economiche e spartane di ogni brand sulle schede di fascia alta: un dissipatore sottodimensionato su un chip potente porta a temperature elevate, throttling (riduzione automatica delle prestazioni per protezione dal calore) e rumore.

### Errori tipici con le versioni custom

L'errore più costoso è pagare il sovrapprezzo per una versione top pensando di ottenere un salto prestazionale che in realtà non c'è: tra la versione base e la versione premium della *stessa* scheda, la differenza di frame rate è quasi sempre trascurabile. L'errore opposto è comprare la versione più economica di una scheda potente per risparmiare, ritrovandosi con una dissipazione inadeguata e ventole rumorose. Un terzo errore è farsi guidare solo da RGB ed estetica, ignorando i parametri che contano davvero: temperature, rumorosità e qualità dei VRM.

---

## 7. Dimensioni fisiche e compatibilità

### Lunghezza, spessore e spazio nel case

Una scheda video è un componente ingombrante, e uno degli errori da principiante più frustranti è acquistarne una senza verificare che entri nel proprio **case**. Due dimensioni principali vanno controllate. La **lunghezza**, espressa in millimetri, deve essere inferiore al massimo *clearance* (spazio disponibile) dichiarato dal produttore del case: le schede di fascia alta possono superare i 320–350 mm, e non tutti i case le ospitano. Lo **spessore**, misurato in "slot", indica quanti slot di espansione la scheda occupa sul retro del case: le schede si dividono in modelli da **2-slot**, **2.5-slot**, **3-slot** e persino **4-slot**. Più grande è il dissipatore — e quindi più potente o premium è la scheda — più slot occupa, e maggiore è il rischio che vada a coprire altri connettori della scheda madre o che non entri in un case compatto. La regola operativa è annotare lunghezza e spessore della scheda desiderata, confrontarli con le specifiche del case e lasciare qualche millimetro di margine per i cavi.

### Connettori di alimentazione: 8-pin PCIe vs 12VHPWR / 12V-2x6

La scheda video riceve alimentazione dall'**alimentatore** (PSU) tramite connettori dedicati. Storicamente sono stati usati connettori **PCIe a 8 pin**, di cui una scheda ne può richiedere uno, due o tre a seconda del suo consumo. Con la serie RTX 40, NVIDIA ha introdotto un nuovo connettore singolo ad alta densità chiamato **12VHPWR** (*12-pin High Power*), successivamente rivisto in una versione migliorata chiamata **12V-2x6**, capace di erogare fino a 600 watt tramite un singolo cavo. Tuttavia, questo connettore ha una **storia problematica** ben nota: su schede di fascia molto alta (specialmente RTX 4090 e 5090) si sono verificati casi di surriscaldamento e fusione del connettore, spesso legati a un inserimento imperfetto o a una distribuzione non uniforme della corrente tra i pin. Le contromisure pratiche includono l'inserimento fermo e completo del connettore, l'evitare pieghe strette del cavo subito dietro la spina e il preferire i cavi nativi dell'alimentatore rispetto agli **adattatori** multi-8-pin inclusi, che sono più ingombranti e considerati meno affidabili. Chi acquista una scheda di fascia alta con questo connettore dovrebbe idealmente dotarsi di un alimentatore moderno **ATX 3.0 / ATX 3.1** con cavo 12V-2x6 nativo.

### Requisiti PSU per Fascia GPU

Ogni scheda ha un consumo tipico, spesso indicato come **TDP** (*Thermal Design Power*, la potenza di progetto termico, usata come approssimazione del consumo elettrico sotto carico) o come TBP (*Total Board Power*, il consumo dell'intera scheda). L'alimentatore deve avere potenza sufficiente non solo per la scheda ma per l'intero sistema, con un margine di sicurezza. La seguente tabella fornisce raccomandazioni conservative per il wattaggio consigliato del PSU in base alla fascia della GPU, considerando un sistema completo con una CPU comparabile.

| Fascia GPU (esempi) | Consumo tipico GPU | PSU consigliato (sistema completo) |
|:---|:---:|:---:|
| Entry (RTX 5060 / RX 9060 XT) | 130–180 W | 550–650 W |
| Mid (RTX 5070 / RX 9070) | 200–250 W | 650–750 W |
| Mid-high (RTX 5070 Ti / RX 9070 XT) | 260–320 W | 750–850 W |
| High (RTX 5080) | ~360 W | 850 W |
| Enthusiast (RTX 5090) | ~575 W | 1000–1200 W |

Il consiglio pratico è di non lesinare sull'alimentatore e di non prenderne uno appena sufficiente: un margine del 30-40% sopra il consumo di picco stimato migliora l'efficienza, riduce la rumorosità della ventola del PSU e allunga la vita del componente. È anche preferibile scegliere unità di marchi affidabili con certificazione **80 Plus** (Gold o superiore), perché un alimentatore scadente è una delle cause più insidiose di instabilità e, nei casi peggiori, di danni all'hardware.

### GPU Sag e Supporti

Le schede grafiche moderne sono pesanti e, col tempo, tendono a piegarsi verso il basso sotto il loro stesso peso: questo fenomeno è chiamato **GPU sag**. Oltre a essere antiestetico, un sag significativo sollecita lo slot PCIe e il connettore. La soluzione è un **supporto anti-sag** (un piccolo puntello, spesso incluso con schede premium o disponibile separatamente) che sostiene l'estremità libera della scheda. È un accessorio economico e consigliato per tutte le schede di fascia media e alta, specialmente in configurazioni con case verticali o con la scheda montata orizzontalmente.

---

## 8. In quale Slot installarla

### Il Primo Slot PCIe x16 Collegato alla CPU

La scheda madre ha diversi slot di espansione **PCIe**, ma non sono tutti uguali e la scheda grafica deve essere installata in quello corretto. Lo slot corretto è il **primo slot PCIe x16 direttamente collegato alla CPU**. La designazione "x16" indica il numero di *lane* (percorsi dati) fisiche ed elettriche disponibili: sedici lane sono il massimo per una scheda grafica consumer e assicurano la piena larghezza di banda. Il fatto che sia collegato *direttamente alla CPU*, e non al chipset, è cruciale: significa che i dati viaggiano tra la scheda grafica e il processore tramite il percorso più breve e veloce, senza passaggi intermedi che aggiungono latenza. Questo slot è quasi sempre il primo dall'alto, il più vicino al socket della CPU, ed è tipicamente rinforzato in metallo proprio per sostenere il peso della scheda grafica.

### Cosa Succede negli Slot Secondari

Gli **slot secondari**, più in basso sulla scheda madre, hanno caratteristiche inferiori. Anche quando sono fisicamente lunghi come uno slot x16, elettricamente offrono spesso solo 4 lane (**x4**) e — dettaglio decisivo — sono collegati al **chipset** anziché direttamente alla CPU. Questo significa due penalità combinate: minore larghezza di banda (un quarto delle lane) e un percorso più lungo e condiviso verso il processore, passando attraverso la connessione tra chipset e CPU, dove transita anche il traffico di altri dispositivi. Installare una scheda grafica potente in uno slot secondario può quindi limitarne le prestazioni, in modo trascurabile per il gaming a risoluzioni normali ma evidente in scenari di AI e trasferimento dati intenso. Gli slot secondari sono pensati per altre schede di espansione (schede di rete, schede di acquisizione video, controller aggiuntivi), non per la scheda grafica principale. L'errore da evitare, tipico di chi assembla per la prima volta o non consulta le istruzioni della scheda madre, è inserire la scheda grafica nel primo slot fisico x16 che si trova senza verificare che sia quello collegato alla CPU: lo slot corretto è sempre indicato chiaramente nel manuale della scheda madre.

---

## 9. Come Scegliere

### Partire dal Monitor: Risoluzione e Refresh

Il punto di partenza corretto per la scelta di una scheda video **non è il budget o il brand, ma il monitor**. La risoluzione e la frequenza di aggiornamento (misurata in hertz) del display definiscono quanta potenza sia realmente necessaria. Comprare una scheda enthusiast per un monitor 1080p 60 Hz è uno spreco: la scheda produrrebbe molti più fotogrammi di quanti il monitor possa visualizzare. Viceversa, installare una scheda entry-level su un monitor 4K ad alto refresh porta a frame rate deludenti. La logica è: prima si stabilisce l'obiettivo (ad esempio, "1440p a 144 Hz con dettagli alti"), poi si sceglie la scheda che lo raggiunge, con un piccolo margine per i giochi futuri. Come guida generale: per il 1080p ad alto refresh, una scheda serie 60 è sufficiente; per il 1440p ad alto refresh, una scheda serie 70 è l'ideale; per il 4K con dettagli alti, sono necessarie almeno schede serie 70 Ti / 80 o equivalenti schede AMD di fascia alta.

### Il Budget: La GPU Come Componente Cui Destinare la Quota Maggiore

In un PC pensato principalmente per il **gaming**, la scheda video è il componente che più influenza le prestazioni percepite, ed è quindi corretto destinarle la fetta più grande del budget — tipicamente tra il 35% e il 45% del costo totale della configurazione. Questo non significa comprare la scheda più costosa possibile sacrificando tutto il resto: significa bilanciare. Una sana regola è che scheda video e processore debbano essere in un range coerente tra loro, perché uno squilibrio eccessivo crea un **collo di bottiglia**, una situazione in cui un componente troppo debole rallenta l'altro, impedendogli di esprimere il suo potenziale ottimale.

### Errori comuni dell'acquirente

Concludiamo con le insidie più frequenti, che riassumono in negativo tutto quanto abbiamo visto. La prima è una **GPU top con una CPU entry-level**: abbinare una scheda potentissima a un processore economico che non riesce a "nutrirla" di dati abbastanza velocemente, sprecando buona parte del potenziale della scheda, specialmente a risoluzioni più basse. La seconda è **VRAM insufficiente per l'uso previsto**: acquistare una scheda con poca memoria per la risoluzione o il carico di lavoro AI che si intende affrontare, con conseguenti cali di prestazioni e stuttering non appena giochi o modelli superano la capacità disponibile. La terza è un **case troppo piccolo**: innamorarsi di una scheda senza verificarne lunghezza e spessore rispetto allo spazio interno del cabinet, e scoprire all'ultimo che non ci sta. La quarta è un **PSU sottodimensionato**: abbinare un alimentatore appena sufficiente o di bassa qualità a una scheda esigente, causando spegnimenti improvvisi sotto carico o instabilità difficili da diagnosticare. La quinta, più sottile, è **inseguire solo la quantità di VRAM** ignorando bus e tipo di memoria, come già discusso nella sezione 2. Ognuno di questi errori deriva dallo stesso difetto fondamentale: valutare un componente in isolamento, dimenticando che una scheda video vive all'interno di un sistema e va scelta in armonia con tutto il resto — monitor, CPU, case e alimentatore.

---

## Riepilogo Operativo — Checklist Decisionale Scheda Video

Questa checklist condensa l'intero capitolo in una sequenza di decisioni da prendere nel giusto ordine, dal primo all'ultimo passo.

1.  **Definisci il tuo obiettivo in base al monitor.** Stabilisci risoluzione e refresh rate del tuo display (o del display che intendi acquistare): 1080p, 1440p, o 4K, e a quanti hertz. Questo, non il budget, è il vero punto di partenza.

2.  **Identifica il tuo profilo d'uso.** Solo gaming? Anche streaming e video editing? Intelligenza artificiale, generazione di immagini o LLM locali? Se ricadi nelle ultime due categorie, l'ecosistema software (CUDA di NVIDIA) e la quantità di VRAM pesano più del puro prezzo/prestazioni.

3.  **Scegli il tier coerente con il tuo obiettivo.** Serie 60/9060 per 1080p, serie 70/9070 per 1440p, serie 70 Ti-80 per 4K. Non sovradimensionare per il tuo monitor o sottodimensionare per il tuo target.

4.  **Verifica la VRAM insieme a bus e tipo di memoria.** Controlla sempre tre numeri insieme: capacità in GB, larghezza del bus in bit, tipo di memoria (GDDR6/6X/7). Diffida di schede con tanta VRAM ma un bus stretto. Per l'AI, punta a quanta più VRAM possibile nel tuo range di prezzo.

5.  **Decidi tra NVIDIA e AMD in base al tuo profilo.** Solo gaming in rasterizzazione e budget risicato → AMD è spesso un valore migliore. Ray tracing pesante, streaming di qualità, AI e produttività → NVIDIA è la scelta più solida. Budget minimo assoluto → considera anche Intel Arc.

6. **Scegli la versione custom giusta.** Preferisci le linee di fascia media (TUF, Pulse, Hellhound, Gaming, Windforce). Passa alle top di gamma (ROG Strix, Nitro+, Suprim, Red Devil) solo se hai bisogno di silenzio assoluto, overclock o estetica, sapendo che il guadagno prestazionale è minimo.

7. **Controlla la compatibilità fisica.** Misura la lunghezza e lo spessore (numero di slot) della scheda e confrontali con lo spazio disponibile nel tuo case, lasciando spazio per i cavi.

8. **Dimensiona l'alimentatore con margine.** Scegli un PSU di marca affidabile, certificato 80 Plus Gold o superiore, con un margine del 30-40% rispetto al consumo di picco stimato del sistema. Per schede con connettore 12V-2x6, preferisci un alimentatore ATX 3.0/3.1 con cavo nativo.

9. **Verifica l'equilibrio con la CPU.** Assicurati che il processore sia di una fascia coerente con la scheda grafica per evitare colli di bottiglia. Meglio due componenti bilanciati che uno eccellente e uno mediocre.

10. **Installa nello slot PCIe x16 primario collegato alla CPU.** Questo è indicato nel manuale della scheda madre, solitamente il primo dall'alto e rinforzato. Aggiungi una staffa anti-sag se la scheda è pesante.

11. **Controlla i prezzi al momento dell'acquisto.** Data la carenza di memoria in corso nel 2026, ignora gli MSRP teorici e controlla i prezzi al dettaglio effettivi, confrontando più rivenditori. Se il tuo budget è limitato, considera anche una scheda di generazione precedente ben mantenuta, che durante una crisi di memoria può offrire un rapporto prezzo/prestazioni migliore rispetto alle nuove.

---

*Nota finale sull'attualità dei dati: modelli, VRAM, prezzi e disponibilità in questo capitolo riflettono la situazione a metà 2026, un periodo caratterizzato da una carenza di memoria che rende i prezzi particolarmente volatili. Le sezioni concettuali (funzionamento GPU, formula della banda, criteri di selezione, compatibilità) rimangono valide nel tempo; le tabelle di modelli e prezzi andranno riverificate al momento dell'acquisto.*

---

[← Precedente](03-memoria-ram.html) · [Tutti i capitoli](./) · [Successivo →](05-archiviazione.html)
