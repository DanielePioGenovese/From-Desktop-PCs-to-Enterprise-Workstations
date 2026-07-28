---
title: "Capitolo 2 - La Scheda Madre"
parent: "Edizione italiana"
nav_order: 2
---

<details open markdown="block">
  <summary>Indice del capitolo</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

> **Nota sugli aggiornamenti dei dati.** Le informazioni architetturali contenute in questo capitolo (funzione del chipset, gerarchia dei socket, principi dei VRM, fattore di forma, standard PCIe e USB) sono stabili e cambiano lentamente. I **prezzi**, tuttavia, sono estremamente volatili e variano in base a paese, valuta, disponibilità e promozioni: qualsiasi cifra qui indicata è da intendersi come un **ordine di grandezza indicativo** e andrà verificata al momento dell'acquisto. Anche l'elenco delle **ultime generazioni** (in particolare i chipset serie 800 di AMD e la piattaforma Intel LGA1851) è quello disponibile al momento della stesura: quando incontrerete un punto in cui i dati potrebbero essere già cambiati, lo troverete esplicitamente contrassegnato con la dicitura **[VERIFICARE]**.

---

## 1. Cosa fa la scheda madre

### 1.1 Definizione e ruolo di hub

La **scheda madre** (spesso abbreviata in *mobo*) è la grande scheda a circuito stampato che costituisce la spina dorsale di ogni computer. Se volessimo usare una metafora, non è il "cervello" (quello è la CPU) né i "muscoli" (la GPU): è piuttosto il **sistema nervoso e circolatorio combinato**, ovvero l'infrastruttura fisica ed elettrica su cui tutti gli altri componenti sono montati e attraverso la quale comunicano tra loro e ricevono alimentazione.

Prima di entrare nel dettaglio, è utile chiarire una sigla che comparirà frequentemente: **PCB** (*Printed Circuit Board*) è la lastra rigida, solitamente verde o nera, composta da più strati di fibra di vetro alternati a sottilissime tracce di rame. Queste tracce sono i "fili" microscopici che trasportano segnali e corrente da un capo all'altro della scheda. Una scheda madre di qualità utilizza più strati di rame (spesso da 4 a 8, a volte di più nei modelli premium) e uno spessore maggiore del rame, il che migliora l'integrità dei segnali ad alta frequenza e la capacità di trasportare corrente senza surriscaldarsi.

Il ruolo della scheda madre è quello di **hub centrale**: essa connette la CPU, la RAM, la scheda grafica (GPU), le altre schede di espansione, i dispositivi di archiviazione (SSD e hard disk), l'alimentatore (PSU) e tutte le periferiche esterne (tastiera, mouse, monitor, rete, audio). Nessuno di questi componenti "parla" direttamente con gli altri: tutto passa attraverso la scheda madre, che funge da centralino e da rete stradale. La qualità e le caratteristiche della scheda madre determinano quindi non tanto la potenza bruta del sistema (che è fornita da CPU e GPU), quanto piuttosto **quanto e cosa si può connettere, a che velocità, con quale stabilità e con quali possibilità di espansione futura**.

### 1.2 Componenti principali sul PCB

Osservando una scheda madre dall'alto, si possono riconoscere diverse aree distinte. Le descriveremo qui in via introduttiva, per poi approfondirle in sezioni dedicate.

Il **socket della CPU** è l'alloggiamento, generalmente nella parte superiore-centrale della scheda, dove viene installato il processore. È un rettangolo pieno di minuscoli contatti; la sua forma, il numero di pin e il meccanismo di fissaggio determinano quali CPU sono compatibili. Il socket è il primo vincolo di compatibilità in assoluto: una CPU progettata per un socket non si adatterà fisicamente a un socket diverso.

Gli **slot RAM** (chiamati anche *slot DIMM*, dove **DIMM** sta per *Dual In-line Memory Module*, il formato dei moduli di memoria per desktop) sono gli slot lunghi e verticali accanto al socket, tipicamente due o quattro. Qui vengono inseriti i moduli di memoria.

Gli **slot PCIe** (*Peripheral Component Interconnect Express*) sono gli slot orizzontali nella parte inferiore della scheda. È qui che vengono installate la scheda grafica e altre schede di espansione (schede audio, schede di rete, schede di acquisizione, controller aggiuntivi). Sono disponibili in diverse lunghezze a seconda del numero di "lane" elettriche disponibili.

Il **chipset** è un piccolo microchip, spesso nascosto sotto un dissipatore di calore metallico nella metà inferiore della scheda. È il "co-processore" che gestisce le comunicazioni con le periferiche e fornisce connettività aggiuntiva. È così importante che gli dedicheremo la sezione più lunga del capitolo.

Il **VRM** (*Voltage Regulator Module*) è la fila di componenti elettronici, spesso coperta da un robusto dissipatore di calore, che circonda il socket della CPU su uno o due lati. Il suo compito è trasformare l'energia fornita dall'alimentatore in una tensione pulita e stabile adatta alla CPU.

Gli **slot M.2** sono piccoli slot orizzontali, solitamente coperti da piastre metalliche, dove vengono installati i moderni SSD in formato "gum stick". Le **porte SATA** (*Serial ATA*) sono i connettori a forma di "L" per il collegamento di hard disk e SSD tradizionali da 2.5 o 3.5 pollici.

Infine, ci sono i **connettori di alimentazione** (il grande connettore a 24 pin per la scheda madre e uno o due connettori a 8 pin, chiamati *EPS*, per la CPU), il **pannello I/O posteriore** (l'insieme di porte che sporgono dal retro del case) e una moltitudine di **header interni**, che sono piccoli connettori a pin dove vengono collegati ventole, illuminazione RGB, porte USB frontali del case e pulsanti di accensione. Tutti questi elementi verranno ripresi in dettaglio in seguito.

---

## 2. Il Chipset

### 2.1 Cos'è il Chipset e Cosa Gestisce

Il **chipset** è, storicamente, l'insieme dei circuiti logici che governano il flusso di dati tra la CPU e il resto del sistema. Nei computer di venti-trent'anni fa, il chipset era diviso in due parti fisiche distinte: un **Northbridge**, vicino alla CPU, che gestiva le connessioni veloci (memoria e scheda grafica), e un **Southbridge**, più lontano, che gestiva le connessioni lente (dischi, USB, audio, rete). Con l'evoluzione tecnologica, il Northbridge è stato progressivamente **integrato nella CPU stessa**: oggi, il controller di memoria (**IMC**, *Integrated Memory Controller*, il circuito che pilota la RAM) e il controller per le principali lane PCIe per la scheda grafica vivono direttamente nel processore. Quello che chiamiamo "chipset" nelle moderne schede madri è, quindi, essenzialmente l'erede del vecchio Southbridge: un singolo chip dedicato alla connettività secondaria.

Cosa gestisce effettivamente il chipset di una scheda madre attuale? Fondamentalmente, **fornisce lane di connettività aggiuntive** rispetto a quelle che la CPU offre da sola. La CPU, per ragioni di costo e spazio fisico, espone un numero limitato di lane PCIe (tipicamente 16 lane riservate alla scheda grafica più 4 o 8 lane per uno o due SSD NVMe) e poche altre risorse. Tutto il resto — le numerose porte USB, le porte SATA per i dischi, la scheda di rete, le lane PCIe per gli slot secondari, spesso il chip audio e il Wi-Fi — è gestito o abilitato dal chipset. In pratica, il chipset è un **moltiplicatore e distributore di connettività**: prende una singola connessione veloce dalla CPU e la "espande" in decine di porte e slot.

Ciò significa che la scelta del chipset determina direttamente **quante periferiche si possono collegare e con quali capacità avanzate** (overclocking, USB4, PCIe 5.0), mentre **non** determina la potenza di calcolo del sistema. Due schede con chipset diversi ma stessa CPU e RAM offrono, a parità di configurazione, prestazioni quasi identiche nei carichi di lavoro normali: la differenza sta nelle funzionalità e nell'espandibilità, non nei "fotogrammi al secondo".

### 2.2 Comunicazione tra CPU e chipset

Il chipset deve comunicare costantemente con la CPU, e questa comunicazione avviene tramite un collegamento dedicato ad alta velocità. È importante capire questo perché tale collegamento è un **potenziale collo di bottiglia**: tutto il traffico proveniente dalle periferiche gestite dal chipset (dischi, USB, slot secondari) deve passare attraverso di esso per raggiungere la CPU.

In **Intel**, questo collegamento è chiamato **DMI** (*Direct Media Interface*). Il DMI è, tecnicamente, un bus derivato dalla tecnologia PCIe. Nelle piattaforme recenti, viene utilizzato il DMI 4.0 con un numero variabile di linee a seconda del livello del chipset: i chipset di fascia bassa utilizzano un DMI più stretto (ad esempio, l'equivalente di 4 linee PCIe 4.0), mentre i chipset di fascia alta utilizzano un DMI più ampio (l'equivalente di 8 linee PCIe 4.0), che raddoppia la larghezza di banda disponibile tra il chipset e la CPU. Questo spiega perché un chipset di fascia alta non solo offre più porte, ma permette anche che queste lavorino insieme senza saturazione.

Nei sistemi **AMD**, sulla piattaforma AM5, la connessione tra la CPU e il chipset è semplicemente un **collegamento PCIe 4.0 a 4 linee**. Un dettaglio architettonico importante riguarda i chipset "dual" di AMD: nelle schede madri X670/X670E e X870E, AMD utilizza **due chip identici** (nome in codice Promontory 21) collegati **in cascata** (daisy chain), ovvero uno attaccato all'altro. Il primo chip comunica con la CPU, il secondo comunica con il primo. Questo raddoppia il numero totale di porte e linee disponibili ma introduce un piccolo aumento di latenza per i dispositivi collegati al secondo chip, e mantiene comunque quel collegamento a 4 linee come collo di bottiglia complessivo verso la CPU. Questo è il compromesso che AMD ha scelto per offrire un'ampia connettività riutilizzando un singolo chip prodotto in grandi volumi.

### 2.3 La gerarchia dei chipset AMD (socket AM5)

Il socket **AM5** è l'attuale piattaforma desktop di AMD, introdotta con i processori della serie Ryzen 7000 e utilizzata anche dalle serie successive (8000 con grafica integrata e 9000). Una delle caratteristiche più apprezzate di AMD è la **longevità del socket**: AM5 è stato dichiarato supportato per diversi anni, il che generalmente consente futuri aggiornamenti della CPU mantenendo la stessa scheda madre (dopo un aggiornamento del BIOS, di cui parleremo). Tutti i chipset AM5 utilizzano memoria **DDR5** (non esiste una variante DDR4 su AM5) e forniscono PCIe 5.0 dal lato CPU.

Prima di leggere la tabella, tre fondamentali chiavi interpretative. La lettera "**E**" alla fine del nome (come in B650**E** o X670**E**) sta per *Extreme* e indica che le **lane PCIe 5.0 sono garantite sia per la scheda grafica che per almeno un SSD**; nelle versioni senza "E", la PCIe 5.0 per la scheda grafica è spesso opzionale o assente, mentre per lo storage può essere presente. La **serie 800** (B850, X870, X870E), più recente della serie 600, si distingue principalmente per la maggiore disponibilità di **USB4** (una porta ad altissima velocità, fino a 40 Gbps, compatibile con Thunderbolt): è **obbligatoria su X870/X870E**, mentre su B850 rimane opzionale ma comune sulla maggior parte delle schede. Infine, su AM5, **l'overclock della CPU è abilitato quasi ovunque tranne che sul chipset entry-level A620**, e questa è una delle differenze storiche più significative rispetto a Intel.

| Chipset AMD (AM5) | Livello | Overclock CPU | PCIe 5.0 GPU | PCIe 5.0 storage | USB4 | Note |
|---|---|---|---|---|---|---|
| **A620** | Entry | No | No (GPU a PCIe 4.0) | No/limitato | No | Meno lane e porte; ideale per build economiche. Su alcune schede, limiti sul TDP delle CPU top. |
| **B650** | Mainstream | Sì | Opzionale (spesso PCIe 4.0) | Sì (almeno 1 M.2) | Opzionale/raro | Il chipset "sensato" per la maggior parte degli utenti. |
| **B650E** | Mainstream+ | Sì | Sì (garantito) | Sì | Opzionale | Come B650 ma con PCIe 5.0 anche per la GPU. |
| **X670** | High (dual chip) | Sì | Opzionale | Sì | Opzionale | Molte più porte USB/SATA e lane PCIe. |
| **X670E** | High (dual chip) | Sì | Sì | Sì | Opzionale | Massima connettività per la serie 600, PCIe 5.0 completo. |
| **B850** | Mainstream+ (serie 800) | Sì | Opzionale | Sì | Opzionale ma frequente | Aggiornamento di B650E; USB4 più diffuso. **[VERIFICARE]** disponibilità/prezzo |
| **X870** | High (serie 800) | Sì | Frequente/garantito su molte schede | Sì | **Obbligatorio** | Single chip ma con USB4 di serie. **[VERIFICARE]** |
| **X870E** | Top (serie 800, dual chip) | Sì | Sì | Sì | **Obbligatorio** | Il massimo dell'espandibilità AM5. **[VERIFICARE]** |

> **[VERIFICARE]** Le esatte garanzie PCIe 5.0 sulla scheda video per X870 (non-E) variano da modello a modello: alcune schede lo implementano, altre no. Se c'è un requisito stringente (ad esempio, una GPU o un SSD PCIe 5.0 da sfruttare appieno), controllare sempre le specifiche tecniche del **singolo modello**, non solo il nome del chipset.

Una ricorrente precisazione pratica: la differenza tra un B650 e un X670E **non riguarda quanto sarà veloce il vostro gioco**. Riguarda quanti SSD NVMe potrete installare, quante porte USB veloci avrete, se potrete usare due schede video o schede di espansione contemporaneamente a piena banda, e quanto margine avrete per l'overclock estremo. Per un normale PC da gioco con una singola scheda video, un buon B650/B650E è più che sufficiente.

### 2.4 La gerarchia dei chipset Intel (LGA1700 e LGA1851)

Intel ha recentemente subito un cambio di socket, quindi è necessario distinguere tra due piattaforme.

Il socket **LGA1700** ospita i processori di 12a, 13a e 14a generazione (nomi in codice Alder Lake, Raptor Lake e Raptor Lake Refresh). È una piattaforma "matura", ampiamente diffusa e spesso disponibile a prezzi convenienti. Una peculiarità storica di LGA1700 è che, a seconda della scheda, può utilizzare memoria **DDR4 o DDR5** (mai entrambe sulla stessa scheda): questo la rende interessante per chi vuole riutilizzare RAM DDR4 esistente e risparmiare, con una piccola penalità prestazionale rispetto alla DDR5.

Il socket **LGA1851** è la piattaforma più recente, introdotta con i processori della **serie Core Ultra 200S** (nome in codice Arrow Lake). Supporta **solo DDR5** e introduce una diversa organizzazione delle linee (la CPU offre PCIe 5.0 per la scheda video e per lo storage, con un supporto nativo migliorato per Thunderbolt/USB4). Attenzione a un vincolo chiaro: **LGA1851 non è retrocompatibile con le CPU LGA1700 e viceversa**; il numero (1700 vs 1851) indica proprio il numero di contatti sul socket.

La regola d'oro di Intel sull'overclocking è diametralmente opposta a quella di AMD: **solo i chipset della serie "Z" (Z690, Z790, Z890) consentono l'overclock della CPU**. Tutti gli altri chipset Intel consentono al massimo l'overclock della memoria (tramite i profili **XMP** di Intel, *Extreme Memory Profile* — l'equivalente degli EXPO di AMD), e su questo punto c'è stata un'evoluzione: mentre i chipset entry-level storici come l'H610 non abilitano nemmeno l'XMP, i chipset di fascia media più recenti (B760, B860) lo fanno.

| Chipset Intel | Piattaforma | Livello | OC CPU | OC Memoria (XMP) | DMI alla CPU | Note |
|---|---|---|---|---|---|---|
| **H610** | LGA1700 | Entry | No | No | x4 | Spesso 2 slot RAM; poche USB/SATA; no PCIe 5.0 dal chipset. |
| **B660** | LGA1700 | Mainstream | No | Sì | x4 | Ottimo punto di equilibrio 12a/13a gen. |
| **B760** | LGA1700 | Mainstream | No | Sì | x4 | Refresh B660, migliore connettività out of the box. |
| **H670** | LGA1700 | Mid-range | No | Sì | x8 | Più linee rispetto a B660, no OC CPU. |
| **H770** | LGA1700 | Mid-range | No | Sì | x8 | Refresh H670, più USB veloci. |
| **Z690** | LGA1700 | High-end | Sì | Sì | x8 | OC completo, massimo numero di linee (12a/13a gen). |
| **Z790** | LGA1700 | High-end | Sì | Sì | x8 | Refresh Z690, più USB 3.2 Gen2x2. |
| **B860** | LGA1851 | Mainstream | No | Sì | x8 **[VERIFICARE]** | Mainstream Arrow Lake; USB4/Thunderbolt più comuni. **[VERIFICARE]** |
| **Z890** | LGA1851 | Top | Sì | Sì | x8 | Flagship Arrow Lake: OC completo, molte linee PCIe 4.0 dal chipset, Thunderbolt 4/USB4 nativo. |

> **[VERIFICARE]** I dettagli precisi della serie 800 di Intel (B860, e l'entry H810 non elencato ma esistente) e l'esatta larghezza del loro DMI potrebbero differire dai valori "tradizionali"; controllare la scheda tecnica del modello specifico, specialmente per il numero di slot M.2 e il supporto USB4/Thunderbolt.

### 2.5 Tabella decisionale: quale chipset per quale utente

Questa tabella riassume pragmaticamente l'abbinamento tra tipo di utente e chipset consigliato. È intenzionalmente semplificata: serve da guida, non da sostituto per la verifica dei singoli modelli.

| Profilo utente | AMD consigliato (AM5) | Intel consigliato |
|---|---|---|
| Ufficio/multimedia, budget minimo, no OC | A620 | H610 / B660 |
| Gaming entry-mid, GPU singola, 1–2 SSD | B650 | B760 |
| Gaming/creator mid-high, overclocking, 2+ SSD veloci | B650E / B850 | Z790 / Z890 |
| Workstation, massima connettività, molti drive, USB4 | X670E / X870E | Z890 |
| Chi vuole riutilizzare RAM DDR4 e spendere poco | *(non disponibile: AM5 è solo DDR5)* | LGA1700 con scheda DDR4 |

### 2.6 Overclocking: il riassunto

Riassumendo la logica dell'overclocking, perché è una delle prime scelte che influenza il chipset: su **AMD AM5, l'overclock della CPU è permesso su tutti i chipset tranne l'A620**, quindi anche un economico B650 permette di spingere un processore sbloccato. Su **Intel, l'overclock della CPU richiede tassativamente un chipset "Z"** (Z690, Z790, Z890); con qualsiasi altro chipset, la CPU gira alle sue frequenze di fabbrica (inclusi i turbo automatici, che non sono considerati overclock manuale). L'overclock della **memoria**, invece, è più permissivo: quasi tutti i chipset moderni lo permettono tramite EXPO (AMD) o XMP (Intel), con l'eccezione dei chipset Intel più economici come l'H610. Dato che su molte piattaforme il maggiore guadagno prestazionale, a parità di costo, viene proprio dall'attivazione del profilo di memoria ad alta velocità, controllare che il chipset lo permetta è spesso più importante che poter overcloccare la CPU.

---

## 3. Il VRM (sezione di alimentazione)

### 3.1 Cos'è il VRM e perché esiste

Il **VRM** (*Voltage Regulator Module*) è il circuito che converte i **12 volt** forniti dall'alimentatore nella tensione molto più bassa e precisa richiesta dalla CPU, tipicamente tra circa **1.0 e 1.4 volt**. Questo può sembrare un compito banale, ma non lo è affatto: una CPU moderna ad alte prestazioni può assorbire **oltre 200-250 watt** a pieno carico, e poiché la potenza è il prodotto di tensione e corrente, a bassa tensione questo significa correnti enormi, nell'ordine di **150-250 ampere**. Il VRM deve fornire questa corrente in modo stabile, pulito e reattivo: se la CPU passa da idle a massimo carico in pochi microsecondi, il VRM deve seguire quella richiesta senza far crollare la tensione o sovraccaricarla, altrimenti il sistema diventa instabile (crash, riavvii) o la CPU riduce la sua frequenza per proteggersi (*throttling*).

### 3.2 Fasi di alimentazione

Per gestire correnti così elevate, il VRM è diviso in più **fasi**. Una fase di alimentazione è un piccolo circuito completo essenzialmente composto da diversi elementi. Ci sono i **MOSFET** (*Metal-Oxide-Semiconductor Field-Effect Transistor*), interruttori elettronici che si accendono e spengono migliaia di volte al secondo; i VRM moderni spesso usano componenti integrati chiamati **DrMOS** o *power stages*, che racchiudono MOSFET e la loro logica di pilotaggio in un unico package, migliorando efficienza e temperatura. Poi c'è l'**induttore** (o *choke*), quel piccolo blocco quadrato che immagazzina energia e "liscia" la corrente. Ci sono i **condensatori**, che stabilizzano la tensione filtrando i disturbi. E infine, c'è un **controller PWM** (*Pulse Width Modulation*), il "direttore d'orchestra" che coordina l'accensione scaglionata delle varie fasi.

Il vantaggio di avere più fasi è duplice. In primo luogo, la corrente totale è **distribuita**: se dieci fasi condividono 200 ampere, ciascuna gestisce solo 20 ampere, rimanendo fresca e affidabile. In secondo luogo, facendo lavorare le fasi in modo **sfalsato** nel tempo, la tensione risultante è più continua e pulita. Tuttavia, attenzione a un mito comune: **il numero di fasi non è di per sé una misura di qualità**. Una scheda madre pubblicizzata con "16 fasi" ma costruita con componenti economici e dissipazione inadeguata può avere prestazioni peggiori di una con "8 fasi" ma componenti ad alta corrente e un buon dissipatore. Va anche detto che molte schede madri utilizzano **doubler**, circuiti che fanno apparire una fase come due: la scheda dichiara "12+2 fasi" ma il controller ne pilota in realtà la metà. Questo non è necessariamente un difetto, ma è il motivo per cui il semplice conteggio delle fasi è una metrica di marketing inaffidabile.

### 3.3 Come riconoscere un VRM adeguato

Dato che i dati di targa sono ambigui, il modo migliore per valutare un VRM è **incrociare tre segnali**. Il primo è la **presenza e la massa dei dissipatori**: un VRM serio è coperto da robusti blocchi di alluminio (a volte collegati da una *heatpipe*, un piccolo tubo che trasferisce il calore) con un'ampia superficie e alette; un VRM "nudo", con chip esposti, è quasi sempre un indicatore di una scheda economica destinata a CPU a bassa potenza. Il secondo segnale è il **rating in ampere delle singole fasi**, un dato che i recensori tecnici spesso riportano (ad esempio, "power stage da 50A o 70A"): fasi da 60-90A ciascuna sono un ottimo segno. Il terzo, e in pratica il più affidabile per i non ingegneri, è **leggere recensioni tecniche specializzate**, che misurano direttamente le temperature del VRM sotto carico prolungato con una CPU impegnativa. Se una recensione mostra che il VRM di una certa scheda rimane sotto i 70-80 °C con la CPU che si intende utilizzare al massimo carico, quella scheda è adeguata; se supera i 100-110 °C o va in throttling, non lo è.

La regola pratica è semplice: **più la CPU è potente e più si intende overcloccarla, più il VRM è importante**. Per una CPU entry-level in un PC da ufficio, quasi ogni VRM andrà bene. Per una CPU multi-core di fascia alta spinta al limite, un VRM debole diventa il fattore limitante per l'intero sistema e può persino ridurne la durata a causa del calore eccessivo.

---

## 4. Il Form Factor

### 4.1 Cos'è il form factor e perché è importante

Il **form factor** è lo standard dimensionale della scheda madre: definisce le misure fisiche, la posizione dei fori di montaggio e il layout generale dei componenti. È un parametro cruciale perché deve essere **coerente con il case**: un case dichiara quali form factor accetta, e non è possibile montare una scheda più grande di quanto il case consenta. Il form factor influenza anche l'espandibilità (numero di slot disponibili), la facilità di raffreddamento e, in parte, il prezzo.

### 4.2 I quattro principali form factor

**ATX** (*Advanced Technology eXtended*) è lo standard di riferimento nel mondo desktop, con dimensioni di **305 × 244 mm** (circa 30.5 × 24.4 cm). È il formato "completo": offre tipicamente quattro slot RAM, molteplici slot PCIe, ampio spazio per il VRM e i dissipatori, e massima connettività. È la scelta più equilibrata per la maggior parte delle build fisse perché non impone compromessi sull'espandibilità.

**Micro-ATX** (spesso abbreviato in *mATX* o *µATX*) misura **244 × 244 mm**, quindi è un quadrato più corto verso il basso rispetto all'ATX. Di solito mantiene quattro slot RAM ma riduce il numero di slot PCIe (tipicamente uno o due utilizzabili). È il formato con il **miglior rapporto prezzo/prestazioni**: le schede mATX costano spesso meno delle equivalenti ATX pur offrendo tutto il necessario per un PC normale con una singola scheda grafica. È un'ottima scelta per chi vuole risparmiare senza rinunciare a quattro slot RAM.

Il **Mini-ITX** misura appena **170 × 170 mm**: è il formato compatto per eccellenza, pensato per PC piccoli e portatili. Tuttavia, impone compromessi significativi: ha **un solo slot PCIe** (quindi una sola scheda video, niente schede aggiuntive) e, soprattutto, **solo due slot RAM**. A parità di funzionalità, le schede Mini-ITX tendono anche a costare **di più** delle schede ATX, perché concentrare tutto in uno spazio così piccolo è ingegneristicamente costoso. Si sceglie per il fattore di forma, non per risparmiare.

L'**E-ATX** (*Extended ATX*) è il formato più grande, con una larghezza che arriva fino a **305 × 330 mm** (le misure esatte variano leggermente tra i produttori, ed è un punto da verificare attentamente con le specifiche del case). È riservato a workstation e sistemi di fascia altissima che richiedono VRM enormi, molti slot e connettività estrema. Richiede case grandi e specificamente compatibili, e ha un costo elevato.

| Fattore di forma | Dimensioni (mm) | Slot PCIe tipici | Slot RAM tipici | Pro | Contro |
|---|---|---|---|---|---|
| **E-ATX** | fino a 305 × 330 | Molti | 4 (a volte 8 su HEDT) | Massima espandibilità e VRM | Costoso, richiede case grandi e compatibili |
| **ATX** | 305 × 244 | 3–4 | 4 | Equilibrio ideale, standard diffuso | Ingombro maggiore rispetto a mATX |
| **Micro-ATX** | 244 × 244 | 1–2 | 4 | Ottimo prezzo, compatto ma spazioso | Meno slot PCIe |
| **Mini-ITX** | 170 × 170 | 1 | 2 | Compattezza estrema, portabilità | Espandibilità limitata, prezzo elevato, RAM limitata |

### 4.3 Implicazioni pratiche

La scelta del fattore di forma si intreccia con la scelta del case e con gli obiettivi della build. Per un PC "normale" da gaming o lavoro, **ATX o Micro-ATX** coprono la stragrande maggioranza delle esigenze; Micro-ATX in particolare è spesso la scelta più intelligente in termini di rapporto qualità-prezzo. **Mini-ITX** ha senso quando la compattezza è una priorità (un PC da salotto, uno da portare agli eventi), accettandone le limitazioni e il prezzo premium. **E-ATX** è appropriato solo per configurazioni professionali ad altissima espandibilità, dove servono davvero molte lane, molti drive e un VRM sovradimensionato. Un errore comune è comprare un case elegante ma piccolo e poi scoprire che la scheda ATX desiderata non ci sta, o che non c'è spazio per il dissipatore della CPU: **il fattore di forma va deciso assieme al case, non separatamente**.

---

## 5. Slot e porte

### 5.1 Slot PCIe: numero, generazione e allocazione delle lane

Come anticipato, gli slot **PCIe** ospitano la scheda video e le altre schede di espansione. Per comprenderli, vanno introdotti due concetti: **generazione** e **lane**.

**Generazione** (PCIe 3.0, 4.0, 5.0) determina la larghezza di banda per corsia: ogni generazione raddoppia la precedente. Una corsia PCIe 3.0 offre circa 1 GB/s, una 4.0 circa 2 GB/s, una 5.0 circa 4 GB/s. Le **corsie** sono i percorsi elettrici paralleli: uno slot "x16" ha sedici corsie, un "x4" ne ha quattro, e così via. La larghezza di banda totale è il prodotto delle corsie e della larghezza di banda per corsia: uno **slot x16 in PCIe 4.0** offre quindi circa 32 GB/s, un **x16 in PCIe 5.0** circa 64 GB/s.

Un punto che confonde molti principianti è la differenza tra la **lunghezza fisica** e le **corsie elettriche** di uno slot. Uno slot può essere fisicamente lungo quanto un x16 (per ospitare una scheda grafica, che richiede il connettore lungo) ma essere cablato elettricamente solo come x4 o x1. Sulle specifiche tecniche, questo è scritto, per esempio, come "PCIe x16 (in modalità x4)": ciò significa che la scheda grafica si adatta fisicamente, ma riceve solo un quarto della larghezza di banda. Questo è un dettaglio importante quando si vogliono usare più slot contemporaneamente.

La distinzione più importante, tuttavia, è tra gli **slot collegati direttamente alla CPU** e gli **slot collegati al chipset**. Il **primo slot x16**, destinato alla scheda grafica principale, è quasi sempre collegato **direttamente alla CPU**: è il più veloce, con piena larghezza di banda e latenza minima, ed è quello che si dovrebbe usare per la GPU. Gli slot secondari, d'altra parte, sono generalmente collegati **al chipset**, il che significa che la loro larghezza di banda condivide il link DMI/PCIe verso la CPU di cui abbiamo discusso: se li si usa intensivamente insieme ad altre periferiche del chipset, potrebbero contendersi la larghezza di banda. Per un normale PC con una singola scheda grafica, questo è completamente irrilevante; diventa rilevante solo in configurazioni con più schede di espansione ad alta larghezza di banda.

### 5.2 Slot M.2 e condivisione delle corsie

Gli slot **M.2** ospitano gli SSD moderni. Un SSD NVMe (*Non-Volatile Memory Express*, il protocollo veloce usato dagli SSD M.2 ad alte prestazioni) tipicamente usa **quattro corsie PCIe** per raggiungere le sue alte velocità. Le schede madri offrono da uno a quattro (o più, sui modelli top) slot M.2, di diverse generazioni: il primo slot M.2 è spesso collegato direttamente alla CPU e può essere PCIe 5.0 o 4.0, mentre gli altri sono collegati al chipset e possono essere di una generazione inferiore.

Qui entra in gioco un concetto tanto importante quanto sottovalutato: la **condivisione delle corsie**. Poiché le corsie PCIe totali sono una risorsa finita, i produttori spesso **condividono** le stesse corsie tra più connettori, con la regola che **non possono essere tutti usati contemporaneamente**. Il caso classico: un certo slot M.2 **condivide le corsie con alcune porte SATA**, quindi l'installazione di un SSD in quello slot **disabilita automaticamente due o quattro porte SATA**. Un altro caso: un secondo slot M.2 condivide le corsie con uno slot PCIe secondario, quindi popolare uno riduce o disabilita l'altro. Inoltre: su alcune schede, popolare tutti gli slot M.2 fa sì che lo slot della scheda grafica scenda da x16 a x8.

Questo è uno degli **errori più costosi** commessi dagli acquirenti inesperti: comprano una scheda con "quattro slot M.2 e sei porte SATA" convinti di poterli usare tutti, per poi scoprire che riempire gli M.2 disattiva metà delle porte SATA, o che l'aggiunta di un'unità rimuove corsie altrove. La soluzione è **leggere sempre il manuale della scheda (o la sezione "specifiche dettagliate") prima dell'acquisto**, identificando la tabella di condivisione delle corsie, che elenca esattamente quali connettori sono mutuamente esclusivi. Su piattaforme con poche corsie (ad esempio, chipset di fascia bassa), la condivisione delle corsie è più stringente; su chipset di fascia alta, con molte più corsie dal chipset, è meno frequente ma comunque possibile.

### 5.3 Slot RAM: 2 vs 4

Il numero di slot RAM ha due implicazioni. La prima riguarda il **dual channel**: le moderne piattaforme desktop raggiungono la piena larghezza di banda della memoria utilizzando **due canali**, il che significa installare i moduli **a coppie**. Con quattro slot, si può iniziare con due moduli e aggiungerne altri due in seguito; con due slot (tipici dei Mini-ITX), si è già "pieni" se si vuole il dual channel, e per aumentare la capacità si dovrà **sostituire** i moduli anziché aggiungerli. La seconda implicazione riguarda l'**aggiornamento**: quattro slot offrono maggiore flessibilità futura, mentre due slot costringono a scartare i moduli esistenti per aumentare la RAM.

Un dettaglio tecnico spesso trascurato: **riempire tutti e quattro gli slot rende più difficile raggiungere frequenze di memoria molto elevate**, perché il controller di memoria integrato (IMC) nella CPU deve gestire più moduli e il segnale è più complesso da gestire. Per questo motivo, chi punta alla massima velocità della RAM spesso preferisce **due moduli ad alta capacità** piuttosto che quattro di capacità inferiore, anche se dispone di quattro slot.

### 5.4 Porte SATA

Le porte **SATA** sono utilizzate per collegare dischi rigidi meccanici e SSD "tradizionali" da 2,5 pollici. Sono ancora utili per l'archiviazione di grandi dimensioni e a basso costo (i dischi rigidi di grandi dimensioni utilizzano SATA). Il numero di porte SATA varia da quattro a otto a seconda della scheda madre; come già visto, alcune di queste porte possono essere condivise con gli slot M.2 tramite la condivisione delle corsie. Chi prevede di collegare molti dischi meccanici (ad esempio, per un piccolo server domestico o per l'archiviazione multimediale) deve contare attentamente le porte SATA **effettivamente utilizzabili contemporaneamente con gli SSD M.2** che intende installare.

### 5.5 Il pannello I/O posteriore

Il **pannello I/O** (Input/Output) è la fila di porte che sporge dal retro del case. È una delle differenze più tangibili tra le fasce di prezzo, ed è bene decifrarne gli acronimi.

Le porte **USB** seguono una nomenclatura confusa a causa di continui rinominazioni. Le **USB 2.0** sono lente (480 Mbps) e adatte solo per tastiere, mouse e periferiche poco esigenti. Le **USB 3.2 Gen 1** (precedentemente chiamate USB 3.0 o USB 3.1 Gen 1) offrono 5 Gbps; le **USB 3.2 Gen 2** offrono 10 Gbps; le **USB 3.2 Gen 2x2** raggiungono i 20 Gbps utilizzando due corsie. Al vertice ci sono **USB4** e **Thunderbolt**, che raggiungono i **40 Gbps** e supportano funzioni avanzate come la connessione di monitor e enclosure esterne ad alta velocità; USB4 e Thunderbolt 4 sono ampiamente interoperabili. Il **connettore Type-C** è la forma fisica reversibile ormai standard per le porte più veloci, mentre il classico **Type-A** rettangolare rimane comune per le velocità intermedie.

Sul pannello troverete anche le uscite **video** (HDMI, DisplayPort): queste servono solo **se usate la grafica integrata (iGPU)** della CPU, cioè senza una scheda grafica dedicata; se avete una scheda grafica, collegherete il monitor ad essa e le uscite della scheda madre rimarranno inutilizzate. Poi c'è la **rete**: le schede madri moderne offrono almeno una porta Ethernet **2.5G** (2.5 Gbps), mentre i modelli di fascia alta possono avere **10G** (10 Gbps); spesso è presente anche il **Wi-Fi** con la sua antenna. La sezione **audio** include jack analogici e, sulle schede madri migliori, un'uscita ottica e chip audio di qualità superiore.

Due funzioni molto pratiche, presenti per lo più nelle schede madri di fascia media e superiore, meritano una menzione. Il **BIOS Flashback** (chiamato anche *Q-Flash Plus*, *Flash BIOS Button* a seconda del produttore) è un pulsante che permette di **aggiornare il BIOS senza CPU, RAM o scheda grafica installate**: è essenziale quando si acquista una scheda madre "new old stock" con un BIOS troppo obsoleto per riconoscere la nuova CPU. Il **Clear CMOS** è un pulsante (o un jumper) che **resetta le impostazioni del BIOS** ai valori di fabbrica: indispensabile per ripristinare l'avvio dopo un overclock fallito.

### 5.6 Header Interni

Gli **header** sono connettori a pin presenti sul PCB a cui vengono collegati i cavi interni. I principali sono gli **header USB frontali**, che alimentano le porte USB del case (Type-A e Type-C sul frontale); gli **header ARGB e RGB** (*Addressable RGB*, illuminazione LED indirizzabile individualmente), per collegare ventole e strisce luminose; i **connettori per ventole** (solitamente PWM a 4 pin, che permettono il controllo della velocità); e l'header dedicato alla **pompa AIO** (*All-In-One*, il sistema di raffreddamento a liquido preassemblato), che fornisce alimentazione stabile e completa alla pompa. Contare gli header disponibili è importante per chi ha molte ventole o un sistema di illuminazione elaborato: una scheda economica potrebbe offrirne troppo pochi, costringendo all'acquisto di *splitter* o *hub* aggiuntivi.

---

## 6. Nomenclatura e Marchi

### 6.1 Perché i Nomi Contano

I produttori organizzano le loro schede in **linee** (o *serie*), che rappresentano livelli crescenti di prezzo e funzionalità. Imparare a leggere queste linee permette di collocare immediatamente una scheda nel giusto livello senza dover analizzare ogni specifica. Attenzione, però: **la stessa linea può contenere modelli di prezzo diverso** a seconda del chipset (una "Strix B650" costa meno di una "Strix X670E"), quindi la linea indica un *posizionamento relativo*, non un prezzo assoluto.

### 6.2 ASUS

ASUS struttura la sua gamma in modo piuttosto lineare. La linea **Prime** è l'entry/mainstream, essenziale ed economica. **TUF Gaming** è la fascia media, con un'enfasi sulla robustezza e componenti affidabili, offrendo un ottimo compromesso qualità/prezzo. **ROG Strix** (*Republic of Gamers*) è la fascia medio-alta, con VRM più robusti, maggiore connettività e un'estetica più curata. Al vertice ci sono **ROG Maximus** (per piattaforme Intel) e **ROG Crosshair** (per piattaforme AMD), le schede ammiraglie progettate per l'overclock estremo e il massimo delle funzionalità. Infine, c'è la linea **ProArt**, rivolta ai creatori di contenuti, con un'estetica sobria e connettività (Thunderbolt, rete veloce) pensata per le workstation.

### 6.3 MSI

MSI utilizza acronimi di quattro lettere in ordine crescente di livello. **PRO** è la linea entry/business, sobria e funzionale. **MAG** (*MSI Arsenal Gaming*) è il livello gaming di fascia media, dove rientrano modelli molto popolari come il "Tomahawk". **MPG** (*MSI Performance Gaming*) è la fascia medio-alta. **MEG** (*MSI Enthusiast Gaming*) è il top, con le schede ammiraglie. La regola mnemonica è che, salendo, la seconda lettera indica la progressione: da PRO ad Arsenal, Performance, Enthusiast.

### 6.4 Gigabyte

Gigabyte separa le linee entry-level da quelle gaming. **UD** (*Ultra Durable*) è l'entry-level essenziale. Le schede etichettate semplicemente **Gaming** occupano la fascia media. Il brand premium è **Aorus**, strutturato su livelli crescenti: **Aorus Elite** (fascia media), **Aorus Pro** (fascia medio-alta), **Aorus Master** (fascia alta) e **Aorus Xtreme** (ammiraglia). Esiste anche la linea **Aero** per i content creator. Salendo nella scala Aorus, migliorano VRM, raffreddamento, connettività e qualità costruttiva.

### 6.5 ASRock

ASRock ha storicamente offerto prezzi aggressivi. Le schede entry-level portano nomi essenziali o la designazione **Pro**. **Steel Legend** è la fascia media con un'estetica distintiva. **Phantom Gaming** (spesso abbreviato in *PG*) è la linea gaming di fascia media, mentre nomi come **Riptide**, **Lightning** o **Nova** identificano varianti specifiche. Al top si trova **Taichi**, la linea ammiraglia riconoscibile per l'estetica a ingranaggi, con un set di funzionalità completo e VRM robusti.

### 6.6 Fasce di Prezzo e Reputazione dei Brand

Come linea guida generale (con l'avvertenza che i **prezzi sono volatili** e vanno sempre verificati), le linee entry-level si collocano nella fascia di prezzo più economica, le linee di fascia media nell'intermedia, e le linee ammiraglie nell'alta, con le schede top di gamma che possono costare quanto una scheda grafica di fascia media. Il consiglio pratico più importante è: **non pagare per un livello che non userai**. Una scheda ammiraglia ha senso solo se si utilizzano realmente le sue funzionalità (overclock estremo, connettività esotica, molti drive); altrimenti, quel denaro è meglio investito nella CPU, GPU o RAM.

Per quanto riguarda la **reputazione dei brand**, tre fattori contano più dell'estetica. Il primo è la **qualità e usabilità del BIOS/UEFI** (l'interfaccia firmware usata per configurare la scheda): un BIOS ben fatto, stabile e ricco di funzionalità semplifica molto la vita, specialmente per l'overclock e la gestione della memoria. Il secondo è la qualità del supporto e la procedura di **RMA** (*Return Merchandise Authorization*), che varia in velocità ed efficienza a seconda della regione. Il terzo è la **qualità effettiva dei componenti** (VRM, dissipatori, rete, audio) al di là del marketing. Poiché tutti i maggiori brand (ASUS, MSI, Gigabyte, ASRock) producono sia schede eccellenti che modelli mediocri, la regola d'oro è **non fidarsi solo del brand, ma valutare ogni singolo modello specifico tramite recensioni tecniche indipendenti**.

---

## 7. Come Scegliere la Scheda Madre Ideale

### 7.1 La Procedura Passo-Passo

La scelta di una scheda madre non inizia mai dalla scheda madre stessa: inizia dalla **CPU**, e procede a cascata. Questo è il metodo corretto, ordinato per priorità.

Il primo passo è **decidere la CPU**, perché la CPU determina tutto il resto. Dalla CPU discende immediatamente il **socket** (AM5 per i Ryzen recenti, LGA1700 o LGA1851 per Intel a seconda della generazione): questo è un vincolo rigido e non negoziabile. Scelto il socket, si passa al **chipset**, valutando quali funzioni servono davvero: overclock sì o no, quante linee e porte, PCIe 5.0 o USB4 necessari o superflui. Poi si definisce il **form factor** in coordinamento con il case (ATX, Micro-ATX, Mini-ITX): questa scelta va fatta assieme al case, non dopo. A questo punto si verifica la **connettività necessaria** confrontando quante porte USB, quanti slot M.2, quante porte SATA e che rete servono per il proprio uso effettivo. Solo allora si verifica il **VRM**, dimensionandolo alla CPU scelta (più la CPU è potente e overcloccata, più il VRM deve essere robusto). Infine, si applica il **budget**, scegliendo fra i modelli che rispettano i requisiti quello con il miglior rapporto qualità/prezzo, senza pagare per funzionalità inutili.

### 7.2 Verifiche di compatibilità

Una volta individuata una candidata, va verificata la sua compatibilità su cinque fronti, perché un errore qui può rendere il sistema inutilizzabile.

La compatibilità con la **CPU** non si limita al socket: occorre verificare che la scheda supporti quella specifica CPU **con la versione del BIOS installata**. Schede vendute prima dell'uscita di una nuova CPU potrebbero avere un BIOS troppo vecchio per riconoscerla, rendendo necessario un aggiornamento tramite la funzione **BIOS Flashback** (che, come visto, funziona anche senza una CPU compatibile). I produttori pubblicano una **lista di CPU supportate** per ogni scheda: va sempre consultata quando si accoppia una CPU molto nuova con una scheda che potrebbe essere stata in magazzino per qualche tempo.

La compatibilità con la **RAM** si verifica sulla **QVL** (*Qualified Vendor List*, la lista dei moduli di memoria testati e certificati dal produttore per quella scheda) e sulle velocità di memoria supportate. La QVL non è un vincolo assoluto (spesso moduli non elencati funzionano), ma scegliere uno presente in lista riduce il rischio di problemi di stabilità, specialmente ad alte frequenze.

La compatibilità con la **GPU** riguarda principalmente lo spazio fisico (*clearance*): le schede video moderne sono grandi e pesanti, e occorre assicurarsi che, una volta installata, la GPU non copra slot M.2 o porte SATA che servono, e che ci sia spazio nel case. Nei form factor compatti, questo aspetto diventa critico.

La compatibilità con il **case** è la coerenza del form factor già discussa: il case deve accettare le dimensioni della scheda. La compatibilità con il **cooler** (dissipatore della CPU) riguarda l'ingombro: dissipatori ad aria molto alti o radiatori liquidi grandi devono trovare spazio senza interferire con i dissipatori del VRM, moduli RAM alti o il pannello del case.

### 7.3 Errori tipici dell'acquirente

Alcuni errori si verificano con una tale frequenza da giustificare un avvertimento esplicito. Il primo è **pagare per un chipset di fascia alta senza usarne le funzionalità**: comprare una X670E o una Z890 e poi installare una CPU di fascia media, una singola scheda grafica e un singolo SSD significa buttare via soldi che avrebbero fruttato molto di più in CPU, GPU o RAM. Il secondo, già discusso, è **ignorare la condivisione delle linee M.2 e SATA**, ritrovandosi con porte che si disabilitano a vicenda dopo l'acquisto. Il terzo è **accoppiare un VRM debole con una CPU di fascia alta**: una scheda economica con un VRM sottodimensionato limiterà una CPU potente e può surriscaldarsi sotto carico prolungato. A questi si aggiungono errori più banali ma frequenti: dimenticare di controllare il supporto BIOS per una nuova CPU, scegliere un fattore di forma incompatibile con il case, sottovalutare le dimensioni del dissipatore ed essere guidati unicamente dal numero di fasi VRM o dall'estetica invece che dalle recensioni tecniche.

---

## Riepilogo Operativo — Lista di Controllo per la Scelta della Scheda Madre

Usa questa lista di controllo in ordine, dall'alto verso il basso. Ogni punto deve essere verificato prima di passare al successivo.

**1. CPU e socket (vincolo rigido).** Ho già scelto la CPU? Da essa deriva il socket: AM5 (Ryzens recenti, solo DDR5), LGA1700 (Intel 12a–14a gen, DDR4 o DDR5) o LGA1851 (Intel Core Ultra 200S, solo DDR5). La scheda deve avere esattamente quel socket.

**2. Supporto BIOS.** La scheda supporta la mia specifica CPU con il BIOS di fabbrica? Se la CPU è molto recente, la scheda ha il **BIOS Flashback** per aggiornarlo senza una CPU compatibile? Ho controllato la lista delle CPU supportate?

**3. Chipset e funzionalità effettive.** Ho bisogno di **overclock della CPU**? Se sì: per Intel serve un chipset **Z**; per AMD, qualsiasi chipset tranne A620 è sufficiente. Ho bisogno di **overclock della RAM** (EXPO/XMP)? Quasi tutte lo permettono, tranne Intel H610. Ho davvero bisogno di **PCIe 5.0** (GPU/SSD) o **USB4**, o sto pagando per funzionalità che non userò?

**4. Fattore di forma e case.** Ho scelto il formato (ATX / Micro-ATX / Mini-ITX / E-ATX) **insieme al case**? Il case accetta queste dimensioni? Ricorda che Mini-ITX ha solo 2 slot RAM e 1 slot PCIe, e che Micro-ATX offre spesso il miglior rapporto qualità-prezzo.

**5. Connettività e condivisione linee.** Ho contato quanti **slot M.2**, quante **porte SATA** e quante **porte USB** mi servono? Ho letto la tabella di **condivisione delle linee** per verificare che gli M.2 e SATA che voglio usare **non si disabilitino a vicenda**? Ho abbastanza **slot RAM** (2 vs 4) per futuri upgrade che prevedo? La **rete** (2.5G/10G/Wi-Fi) è adeguata?

**6. VRM.** La CPU scelta è potente e/o la overclockerò? Se sì, la scheda ha un **VRM robusto** (dissipatori sostanziosi, fasi ad alta corrente) confermato da **recensioni tecniche** (temperature sotto carico prolungato)? Ricorda che il solo numero di fasi non è indicatore di qualità.

**7. Compatibilità fisica.** La **GPU** ci sta e non copre porte/slot che mi servono? Il **dissipatore della CPU** (ad aria alto o radiatore a liquido) trova spazio senza interferire con VRM, RAM e case? La **RAM** scelta è nella QVL o comunque compatibile con le velocità dichiarate?

**8. Marca e modello.** Ho inquadrato la scheda nel tier corretto leggendo la riga (Prime/TUF/Strix/Maximus-Crosshair per ASUS; PRO/MAG/MPG/MEG per MSI; UD/Gaming/Aorus per Gigabyte; Pro/Steel Legend/Phantom Gaming/Taichi per ASRock)? Ho valutato il **singolo modello** con recensioni indipendenti, invece di fidarmi solo della marca o dell'estetica?

**9. Budget finale.** Tra i modelli che superano tutti i punti precedenti, sto scegliendo quello con il **miglior rapporto qualità-prezzo**, evitando di pagare per un chipset o un tier le cui funzionalità non userò? Quei soldi sarebbero spesi meglio altrove (CPU/GPU/RAM)?

**Regola d'oro finale.** La scheda madre non aumenta gli "fps": abilita, connette e alimenta. Compra la **scheda più economica che soddisfa tutti i tuoi requisiti reali** con un VRM adeguato alla CPU — né più, né meno.

---

*Fine capitolo. I dati architetturali sono aggiornati al meglio delle conoscenze disponibili al momento della stesura; prezzi, disponibilità e dettagli degli ultimi chipset (AMD serie 800, Intel piattaforma LGA1851) contrassegnati con **[VERIFICARE]** vanno confermati sulle specifiche tecniche ufficiali dei singoli modelli al momento dell'acquisto.*

---

[← Precedente](01-cpu-desktop-consumer.html) · [Tutti i capitoli](./) · [Successivo →](03-memoria-ram.html)
