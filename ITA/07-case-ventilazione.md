---
title: "Capitolo 7 - Il Case e la Ventilazione"
parent: "Edizione italiana"
nav_order: 7
---

<details open markdown="block">
  <summary>Indice del capitolo</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## Premessa: Il Componente Che Tutti Sottovalutano

Esiste una gerarchia implicita nella mente di chiunque assembla il suo primo computer. In cima ci sono la CPU (Central Processing Unit) e la GPU (Graphics Processing Unit), perché sono le parti di cui si parla nelle recensioni e che compaiono nei benchmark. Poi viene la RAM (Random Access Memory, memoria di lavoro volatile) e lo storage, perché almeno hanno un numero che cresce e si può confrontare. Poi la scheda madre, scelta per il suo socket e il prezzo. Poi l'alimentatore, spesso scelto male. E infine, come un ripensamento, quasi un accessorio estetico, il case: il "mobiletto", lo "chassis", quella scatola di lamiera e vetro che tiene tutto insieme.

Questa gerarchia è sbagliata, e per una ragione molto concreta: il case è l'unico componente che influenza **tutti gli altri contemporaneamente**. Non calcola nulla, non produce frame, non immagazzina dati, ma determina la temperatura a cui operano CPU e GPU, il rumore che il sistema produce nella stanza, la quantità di polvere che si accumula sui dissipatori, la vita utile dei componenti, e — cosa che si scopre solo quando si inizia l'assemblaggio — quanto sarà dolorosa o piacevole l'esperienza di montaggio e manutenzione.

Un case sbagliato non farà esplodere il vostro computer. Fa qualcosa di più insidioso: lo rallenta silenziosamente. Una GPU che in uno chassis ben ventilato opera a 68 °C, mantenendo il suo boost massimo, può raggiungere gli 84 °C all'interno di un "acquario" chiuso da tre pannelli di vetro. A quel punto, l'algoritmo di boost — che è dinamico e sensibile alla temperatura — abbassa le frequenze per rientrare nei limiti termici. Il risultato è un 5-10% di prestazioni in meno che l'utente non vede perché non ha mai visto il "prima". Ha semplicemente comprato una scheda grafica da 800 euro e ne sta usando solo 730 euro di prestazioni.

Questo capitolo copre il case e tutto ciò che lo circonda — formati, compatibilità, flusso d'aria, ventole, filtri, marche — con lo stesso livello di dettaglio riservato ai componenti "nobili". Alla fine, avrete gli strumenti per aprire le specifiche tecniche di uno chassis su un sito di e-commerce e capire, in novanta secondi, se è adatto alla vostra configurazione o se è una trappola.

> **Nota sui dati volatili.** Modelli, prezzi e disponibilità di case e ventole cambiano rapidamente e variano molto tra i mercati. Ogni cifra in euro riportata in questo capitolo è indicativa del mercato italiano e andrebbe verificata al momento dell'acquisto. I dati marcati **[dati volatili]** sono i più soggetti a invecchiamento.

---

## 1. Il ruolo del case

### 1.1 Protezione meccanica ed elettrica

La funzione più antica e ovvia del case è quella di contenitore strutturale. I componenti del PC sono fragili in modo non intuitivo: la scheda madre è un sandwich di fibra di vetro e rame con centinaia di saldature che si crepano se il PCB (Printed Circuit Board) viene flesso; un dissipatore ad aria di grandi dimensioni può pesare 1,5 kg e, se il sistema viene trasportato in verticale in auto, esercita un'enorme leva sui quattro punti di montaggio del socket; una moderna scheda grafica a triplo slot pesa fino a 2 kg ed è sospesa a un connettore PCIe (Peripheral Component Interconnect Express, il bus di espansione ad alta velocità) e a due viti sulla staffa posteriore.

Il case fornisce il telaio rigido che assorbe queste sollecitazioni. Lo fa tramite il **vassoio della scheda madre** (motherboard tray), la lamiera verticale a cui la scheda madre è avvitata tramite gli **standoff**: piccoli cilindri filettati che sollevano il PCB di qualche millimetro dalla lamiera, impedendo il contatto elettrico tra le tracce di rame sul retro della scheda e il metallo dello chassis. È bene ricordarlo subito, perché è uno dei classici errori da principiante: **montare la scheda madre senza standoff, o lasciare uno standoff in una posizione in cui la scheda non ha un foro, provoca un cortocircuito** che, nel migliore dei casi, impedisce l'accensione e, nel peggiore, danneggia permanentemente la scheda.

C'è poi una funzione elettromagnetica meno visibile. Un PC è una sorgente di EMI (Electromagnetic Interference): all'interno del case ci sono segnali che commutano miliardi di volte al secondo, e un contenitore metallico chiuso e messo a terra agisce come una **gabbia di Faraday** imperfetta ma efficace, riducendo sia le emissioni verso l'esterno sia la suscettibilità a disturbi esterni. Per questo i case hanno gli slot di espansione chiusi da staffe metalliche e per questo esistono normative (FCC negli Stati Uniti, marcatura CE in Europa) a cui gli chassis devono conformarsi. In pratica, per l'utente domestico, questo si traduce in una semplice regola: **le staffe degli slot PCIe inutilizzati vanno reinstallate**, non perché lo detti la fluidodinamica, ma perché servono a chiudere il guscio e — secondariamente — a impedire all'aria di entrare o uscire da percorsi non voluti.

### 1.2 Gestione termica: la vera funzione

Questo è il cuore del capitolo. Da un punto di vista termodinamico, un computer è un **convertitore di energia elettrica in calore**. Praticamente il 100% della potenza prelevata dalla presa (meno una quantità trascurabile che esce come luce LED e onde radio Wi-Fi) finisce per diventare calore nell'aria della stanza. Un sistema che preleva 500 W dalla presa è, dal punto di vista del riscaldamento ambientale, indistinguibile da una stufetta elettrica da 500 W.

Il problema non è il calore in sé: è la **densità di potenza**. La CPU dissipa il suo TDP (Thermal Design Power, la potenza termica di progetto, cioè il calore che il sistema di raffreddamento deve essere in grado di dissipare in condizioni sostenute) da una superficie di pochi centimetri quadrati; la GPU fa lo stesso da un die grande come un francobollo. Il dissipatore della CPU e quello della scheda grafica hanno il compito di prendere quel calore concentrato e trasferirlo all'aria che li attraversa. Ma l'aria che li attraversa **è l'aria all'interno del case**.

Questa è l'idea centrale, e merita di essere ripetuta perché è la fonte del 90% degli errori: il dissipatore della CPU e quello della GPU non raffreddano con l'aria della stanza. Raffreddano con l'aria del case. Il compito del case è rendere l'aria del case il più simile possibile all'aria della stanza, il che significa che dovrebbe essere continuamente sostituita prima che si riscaldi. Se il case non scambia aria, si verifica una condizione di **ricircolo**: la GPU espelle aria a 55 °C, quell'aria non trova via d'uscita, viene ri-aspirata dalle ventole della GPU stessa e dal dissipatore della CPU, e il sistema si stabilizza a un equilibrio termico molto più elevato. I dissipatori funzionano ancora perfettamente — semplicemente lavorano con aria in ingresso a 45 °C invece di 24 °C, e quei 21 gradi di differenza sono quasi interamente trasferiti alle temperature dei chip.

Il parametro che descrive questo fenomeno si chiama **delta T ambiente-case**: la differenza tra la temperatura dell'aria all'interno del case (misurabile con sensori, o stimabile dai sensori di temperatura della scheda madre) e la temperatura ambiente. In un case ben ventilato sotto carico, questo delta è di 3-6 °C. In un case chiuso e mal configurato, può superare i 15-20 °C. **Ogni grado di delta T è un grado in più su CPU e GPU**, aggiunto a tutto il resto.

### 1.3 Acustica

Il secondo asse su cui lavora il case è il rumore. Qui c'è un compromesso fondamentale che va capito subito, perché nessun marketing lo dichiarerà mai esplicitamente: **flusso d'aria e silenzio sono in tensione**. Il rumore del PC ha tre origini:

1. **Rumore aerodinamico** delle ventole, generato dalla turbolenza dell'aria che attraversa pale, griglie e alette del dissipatore;
2. **Rumore meccanico** dei cuscinetti delle ventole, delle pompe dei sistemi di raffreddamento a liquido e degli hard disk meccanici;
3. **Vibrazioni strutturali** trasmesse dal telaio, che funge da cassa di risonanza se le lamiere sono sottili.

Un case "silenzioso" tradizionale — si pensi ai modelli storici di be quiet! o al Fractal Design Define — affronta il problema con l'**isolamento**: pannelli laterali e frontali rivestiti internamente con materiale fonoassorbente (bituminoso o schiuma poliuretanica), un frontale chiuso con uno sportello solido e aperture ridotte. Questo agisce sul rumore che fuoriesce, ma peggiora il flusso d'aria, quindi le ventole devono girare più velocemente per compensare, e ventole più veloci fanno più rumore. Il guadagno netto esiste, ma è molto più piccolo di quanto comunemente si creda.

La scuola moderna — che ha dominato il mercato dal 2019-2020 in poi — ha invertito il ragionamento: **si preferisce un case molto aperto (mesh) con ventole grandi e lente**. Il ragionamento è che il rumore aerodinamico cresce in modo fortemente non lineare con la velocità di rotazione (in prima approssimazione, la potenza acustica scala come la quinta potenza della velocità periferica delle pale: raddoppiare gli RPM significa un aumento del rumore di circa 15 dB, che è "molto più del doppio" per la percezione umana). Se il case offre poca resistenza, le ventole possono girare a 700-900 RPM invece di 1400-1600 per spostare la stessa quantità d'aria, e il risultato netto è **più fresco E più silenzioso**, anche se il rumore residuo è meno "schermato". Questa è attualmente la strategia vincente per la stragrande maggioranza degli utenti, e i test indipendenti lo confermano costantemente.

Esistono ancora casi limite in cui l'isolamento ha senso: sistemi a bassissima potenza (un PC da ufficio con una CPU da 65W e nessuna scheda grafica dedicata) collocati in stanze molto silenziose, dove il rumore residuo delle ventole a bassa velocità è ancora percepibile e la barriera acustica fa la differenza. Ma un sistema da gaming con una GPU da 300W non può essere reso silenzioso racchiudendolo: può solo essere reso caldo.

### 1.4 Estetica

Va detto onestamente: per un'enorme fetta del mercato, il case è **l'unico componente visibile**, ed è quindi legittimo che l'estetica pesi sulla scelta. Il punto non è negare questa esigenza, ma ordinarla correttamente rispetto alle altre. La regola pratica che propongo, e su cui tornerò nella procedura di selezione, è: **l'estetica è un criterio di selezione all'interno dell'insieme dei case che funzionano, non un criterio che definisce l'insieme.** Per prima cosa, filtra per compatibilità e flusso d'aria, poi scegli il più esteticamente gradevole tra quelli rimasti. L'insieme è piuttosto ampio: nel 2025-2026, ci sono decine di case belli *e* ben ventilati, quindi non è più necessario scendere a compromessi. Chi sceglie il case "acquario" prima di guardare le temperature sta semplicemente invertendo l'ordine dei fattori, e lo pagherà in gradi.

### 1.5 Ergonomia di Assemblaggio e Manutenzione

L'ultimo ruolo, quello che si apprezza solo dopo il secondo o terzo assemblaggio: un buon case permette un assemblaggio facile. Le caratteristiche che fanno la differenza sono concrete e verificabili nelle recensioni video:

- **spazio dietro il vassoio della scheda madre** per la gestione dei cavi (dai miseri 20 mm dei case economici ai 30-35 mm dei modelli ben progettati);
- **pannelli laterali tool-less**, a scatto o con chiusura magnetica;
- **bordi arrotolati** sulla lamiera, che impediscono di tagliarsi le dita — un dettaglio che distingue immediatamente uno chassis ben fatto da uno costruito al risparmio;
- **coperture degli slot di espansione riutilizzabili**, non a strappo: alcuni case economici hanno le coperture degli slot PCIe fissate con un ponte metallico da rompere, e una volta rotte non possono essere riattaccate;
- **filtri antipolvere rimovibili senza smontare il PC**, idealmente dalla parte anteriore o superiore, non da sotto richiedendo di sollevare il case;
- **vassoi per unità rimovibili** e accesso alla parte posteriore senza dover coricare la macchina.

Niente di tutto questo appare in una tabella delle specifiche, e tutto ciò determina se lavorare di nuovo sul computer tra due anni sarà un compito pomeridiano o un incubo.

---

## 2. Fattori di Forma: Chassis e Scheda Madre

### 2.1 Fattori di Forma della Scheda Madre, Riveduti da una Prospettiva del Case

Il fattore di forma della scheda madre — trattato nel capitolo dedicato — è il vincolo primario che determina il case, perché lo chassis deve avere i distanziatori nelle posizioni corrette e lo spazio fisico per contenerla. Esistono quattro fattori di forma rilevanti per il mercato consumer, definiti dallo standard **ATX** (Advanced Technology eXtended, lo standard introdotto da Intel nel 1995 che governa dimensioni, fori di montaggio e posizionamento dei connettori):

| Fattore di Forma | Dimensioni (mm) | Slot di Espansione | Uso Tipico |
|---|---|---|---|
| E-ATX ("Extended ATX") | 305 × 330 (nominale, ma variabile) | 7 | Workstation, HEDT, schede di fascia alta |
| ATX | 305 × 244 | 7 | Standard di riferimento, desktop completo |
| Micro-ATX (mATX) | 244 × 244 | 4 | Budget compatto, uffici |
| Mini-ITX (ITX) | 170 × 170 | 1 | Sistemi SFF (Small Form Factor) |

Un'importante precisazione e fonte di veri guai: **"E-ATX" non è uno standard rigoroso**. Formalmente, indica 305 × 330 mm, ma i produttori usano l'etichetta anche per schede che misurano 305 × 277 mm o 305 × 267 mm (i cosiddetti formati "EE-ATX" o semplicemente ATX allargati). Un case dichiarato "compatibile E-ATX fino a 280 mm" accetterà una scheda da 277 mm ma non una da 330 mm. **Controllate sempre la larghezza effettiva della scheda in millimetri**, non la sigla. Questo è uno degli errori più costosi perché il problema si scopre solo quando si ha già tutto in mano.

La compatibilità è **discendente**: un case che accetta ATX accetta anche mATX e ITX, perché i fori per i distanziatori sono un sottoinsieme. Il contrario non è vero. Montare un ITX in un case ATX è perfettamente legittimo (ci sarà molto spazio vuoto, il che non è un difetto termico, semmai il contrario), ma è uno spreco di volume e denaro.

### 2.2 Fattori di Forma dei Case

I nomi commerciali degli chassis sono meno standardizzati di quanto si possa pensare. Non esiste una definizione ufficiale di "mid tower". Si usa una convenzione, e il parametro più onesto per confrontare gli chassis è il **volume in litri**, che molti produttori e siti specializzati ormai dichiarano.

| Categoria | Altezza Tipica | Volume Tipico | Mobo Max | Ventole Tipiche | Note |
|---|---|---|---|---|---|
| Full tower | 500-650 mm | 60-100+ L | E-ATX / SSI-EEB | 8-12 | Custom loop, molti drive, workstation |
| Mid tower | 420-500 mm | 40-60 L | ATX (spesso E-ATX "stretto") | 5-9 | **Il formato di riferimento** |
| Mini tower / mATX | 350-420 mm | 25-40 L | mATX | 3-5 | Compromesso spazio/costo |
| SFF (Small Form Factor) | variabile | 8-25 L | Mini-ITX | 1-4 | Compattezza estrema, molti compromessi |
| Cube / "dual chamber" | variabile | 30-60 L | ITX → E-ATX | variabile | Layout a doppia camera, estetica |

**Full tower.** Ha senso in tre casi: raffreddamento a liquido custom (con più radiatori da 360/420 mm montati contemporaneamente), un gran numero di drive meccanici (NAS casalingo, archivi video), o vere schede madri workstation E-ATX. Al di fuori di questi scenari, è spazio e denaro sprecati: un full tower non è automaticamente più fresco di un buon mid tower, perché il volume di per sé non raffredda — raffredda l'**airflow**. Un full tower con tre ventole è peggio di un mid tower con sei.

**Mid tower.** Questo formato risolve il 90% dei casi d'uso. Accetta schede madri ATX, radiatori da 360 mm, schede grafiche lunghe 340-400 mm e dissipatori ad aria alti 160-175 mm. È qui che si concentra la ricerca e sviluppo dei produttori, dove c'è la maggiore concorrenza, e quindi il miglior rapporto qualità/prezzo. **Se non hai una ragione esplicita e articolabile per uscire da questa categoria, restaci.**

**Mini tower / mATX.** Ha senso quando il vincolo è lo spazio sulla scrivania o il budget. Una scheda madre mATX costa meno di una ATX equivalente, e i quattro slot di espansione sono più che sufficienti per chiunque non abbia esigenze particolari (nel 2026, gli slot di espansione sono quasi esclusivamente per la GPU: audio, rete e USB sono integrati). Il compromesso è termico: meno spazio per le ventole, spesso solo 2-3 posizioni, e spazi più ristretti.

**SFF (Small Form Factor).** Questa è una disciplina a sé stante, quasi uno sport. Sotto i 15 litri, ogni millimetro è conteso: la scheda grafica deve essere corta o "2.5 slot", il dissipatore della CPU è spesso un low-profile alto 47-67 mm, l'alimentatore è un **SFX** o **SFX-L** (formato compatto, 100 × 63.5 mm contro 150 × 86 mm per l'ATX standard) che costa il 40-60% in più di un ATX di pari potenza, i cavi devono essere accorciati o sostituiti con set personalizzati. Il risultato può essere splendido — un sistema da 12 litri con prestazioni desktop complete — ma il percorso è costoso, richiede una ricerca approfondita e non perdona errori di pianificazione. **È il formato sbagliato per una prima build.** I telai di riferimento del settore includono il Fractal Design Terra e Ridge, il Cooler Master NR200P (il più accessibile e "indulgente"), il Lian Li A4-H2O e Q58, e la scena boutique (SSUPD, Formd, Dan Cases) **[dati volatili]**.

### 2.3 Quale formato per quale esigenza

- **PC da ufficio / HTPC / navigazione**: mATX o ITX. Poco calore, poco spazio, basso costo.
- **PC da gaming entry/mid-range (GPU singola, dissipatore ad aria o AIO 240)**: mid-tower mesh. Non esiste alternativa razionale.
- **PC da gaming high-end (GPU top-tier, AIO 360, molte ventole)**: mid-tower di qualità o dual-chamber come Lian Li O11. Nota: i case dual-chamber spesso **mancano di aspirazione frontale** e devono essere configurati con aspirazione dell'aria dal basso e/o dal lato, come vedremo.
- **Workstation AI/ML o rendering (GPU multiple, molte linee PCIe, molta RAM)**: full tower, e qui il vincolo diventa **la larghezza per ospitare due schede grafiche a 3 slot con spazio d'aria in mezzo**, oltre alla capacità dell'alimentatore. Va detto chiaramente che due GPU flagship affiancate in un case consumer sono una configurazione termicamente problematica: la scheda superiore aspira aria calda dalla scheda inferiore. Le soluzioni includono modelli blower (rari nel consumer), raffreddamento a liquido o chassis con spaziatura degli slot aumentata.
- **NAS / server domestico**: chassis specifici con gabbie per 6-12 dischi da 3.5", ventole a medio flusso davanti alle gabbie, o formati rack se si dispone di un armadio.

---

## 3. Compatibilità e spazio interno: la checklist che ti salva l'acquisto

Questa è la sezione operativa più importante del capitolo. Tutti i numeri qui elencati sono pubblicati nelle specifiche del case; il problema è che quasi nessuno li legge **prima** di ordinare. Prendi un foglio (o un file) e compila questi campi per la tua configurazione.

### 3.1 Lunghezza massima della GPU

Questa è la misura, in millimetri, dalla staffa posteriore all'estremità anteriore della scheda grafica. Il case dichiara una "**GPU clearance**" o "max VGA length". **Regola operativa: prendi la lunghezza effettiva del modello specifico che acquisti — non il chip, il modello.** Una ASUS RTX 5070 può essere lunga 305 mm, e la stessa RTX 5070 di un altro produttore può essere 240 mm. La lunghezza è una caratteristica del dissipatore, non della GPU.

Attenzione a tre insidie:

1. **Il valore dichiarato dal case è spesso "senza ventole frontali installate"**. Molti chassis dichiarano due numeri: ad esempio, "400 mm, 360 mm con radiatore frontale". Se monti un radiatore o ventole spesse nella parte anteriore, perdi 25-55 mm.
2. **Cavi di alimentazione.** Il connettore **12V-2×6** (l'evoluzione del 12VHPWR, il connettore a 16 pin delle moderne schede ad alta potenza) esce dal lato superiore della scheda e richiede un raggio di curvatura minimo: lo standard impone di non piegare il cavo entro 35 mm dal connettore. Se il pannello laterale è a 15 mm dalla scheda, il cavo verrà schiacciato — ed è esattamente questo il meccanismo che ha causato gli incidenti di fusione dei connettori. Alcune schede hanno il connettore ruotato o posizionato diversamente proprio per questo motivo. **Verifica l'ingombro in larghezza, non solo in lunghezza.**
3. **Spessore slot.** Le schede moderne occupano 2, 2.5, 3 o anche 4 slot in altezza. Un case con 7 slot PCIe accoglie fisicamente qualsiasi scheda, ma se hai una scheda di espansione (una scheda audio, un adattatore 10 Gbit, una scheda di acquisizione), potresti scoprire che la GPU copre il suo slot.

### 3.2 Altezza massima dissipatore CPU

Misura in millimetri dalla superficie della scheda madre alla parte superiore del dissipatore. Valori tipici:

| Categoria Dissipatore | Altezza | Case Richiesto |
|---|---|---|
| Low-profile (Noctua NH-L9, Thermalright AXP) | 37-67 mm | SFF, HTPC |
| Compact single tower | 120-140 mm | mATX, mid tower |
| Standard single tower (es. Peerless Assassin, NH-U12A) | 155-160 mm | mid tower (quasi tutti) |
| Dual tower (Noctua NH-D15, Thermalright FA/PA) | 160-168 mm | wide mid tower |
| "Large" dual tower | 170-175 mm | verificare esplicitamente |

La regola: **lascia almeno 5 mm di spazio** rispetto al valore dichiarato dal case. Tolleranze di fabbricazione, spessore delle guarnizioni, altezza degli stand-off e la bombatura del pannello laterale in vetro possono mangiarsi quei pochi millimetri. Un dissipatore che tocca il pannello laterale non solo non si chiuderà: trasmette vibrazioni al vetro e trasforma l'intero lato del case in un altoparlante.

### 3.3 Radiatori: dimensioni, posizioni, spessori

Il **radiatore** è lo scambiatore di calore di un sistema di raffreddamento a liquido (**AIO**, All-In-One, sistemi chiusi e pre-riempiti, o custom loop). Si misura in base al numero e alla dimensione delle ventole che ospita:

| Abbreviazione | Ventole | Lunghezza Approssimativa | Capacità di Raffreddamento Approssimativa |
|---|---|---|---|
| 120 | 1 × 120 mm | ~155 mm | scarsa, sconsigliata |
| 240 | 2 × 120 mm | ~275 mm | CPU fino a ~180 W |
| 280 | 2 × 140 mm | ~315 mm | ~15-20% migliore di un 240 |
| 360 | 3 × 120 mm | ~395 mm | CPU di fascia alta (fino a 250-300 W) |
| 420 | 3 × 140 mm | ~455 mm | massimo consumer, case grandi |

**Posizioni di montaggio** possibili e le loro implicazioni:

- **Superiore (alto), scarico.** Questa è la posizione canonica per un AIO. L'aria calda sale, il radiatore la espelle verso l'alto, la pompa rimane più in basso del punto più alto del circuito (importante: **la pompa non deve mai essere il punto più alto**, altrimenti l'aria che inevitabilmente si accumula nel circuito si raccoglie nella pompa e produce il classico gorgoglio e usura prematura). Il compromesso: il radiatore scalda leggermente l'aria che... esce, quindi nessun problema; però occupa spazio alle VRM (Voltage Regulator Module, lo stadio di alimentazione della CPU sulla scheda madre) e può interferire con i dissipatori stessi delle VRM o con moduli RAM alti. **Verificare il "CPU cooler to top" clearance del case e lo spessore radiatore + ventola** (tipicamente 27 + 25 = 52 mm, ma esistono radiatori da 38 e 45 mm di spessore).
- **Frontale (fronte), immissione.** Massime prestazioni per la CPU, perché il radiatore riceve aria fresca dall'esterno. Il costo è che tutta l'aria che entra nel case è stata preriscaldata dal radiatore (di 3-8 °C sotto carico), e questo penalizza la GPU. È una scelta legittima se la priorità è la CPU. Riduce la lunghezza GPU disponibile.
- **Laterale / inferiore.** Tipico dei case dual-chamber. Da valutare caso per caso.

Un'ultima nota onesta: **per la stragrande maggioranza delle CPU consumer, un buon dissipatore ad aria da 40-60 euro (Thermalright Peerless Assassin, Phantom Spirit, DeepCool AK620) è a 2-4 °C da un AIO 360 da 130 euro**, non ha una pompa che può guastarsi, non ha liquido che può evaporare nel tempo, e non ha un singolo punto di fallimento. Un AIO ha senso per estetica, per liberare spazio attorno al socket, per CPU veramente estreme, o quando l'altezza del case non permette una torre. Non compratelo pensando che sia automaticamente superiore.

### 3.4 Alloggiamenti per drive

Distinguiamo tre famiglie:

- **3.5" (HDD meccanici).** Richiedono una gabbia per drive con vassoi, idealmente ammortizzati (gommini antivibrazione). La tendenza negli ultimi anni è di ridurne il numero: molti case moderni ne offrono 2, alcuni "airflow-first" solo 1, alcuni SFF zero. Se avete un archivio di drive meccanici, **contateli prima**.
- **2.5" (SSD SATA e vecchi HDD da laptop).** Occupano poco spazio e si montano su vassoi dietro il vassoio della scheda madre o sullo shroud dell'alimentatore. Tipicamente 2-4 posizioni.
- **M.2 (SSD NVMe).** Questi non riguardano il case: si montano direttamente sulla scheda madre. Per questo i case moderni hanno sempre meno alloggiamenti — la maggior parte dei nuovi sistemi non ha drive fisici oltre agli NVMe.

Vale la pena notare che le gabbie per drive da 3.5" frontali, quando presenti, **sono un ostacolo all'airflow**: sono posizionate esattamente dietro le ventole di immissione. I case moderni le rendono rimovibili proprio per questo motivo. Se non vi servono, rimuovetele.

### 3.5 Alimentatore: posizione, lunghezza, shroud

Praticamente tutti i case moderni montano l'unità di alimentazione (**PSU**) **in basso**, con la sua ventola rivolta verso il basso e un filtro antipolvere sotto. Questo isola termicamente la PSU dal resto del sistema: aspira aria fresca da sotto il case e la espelle direttamente sul retro, senza scambiare calore con l'aria interna. Questo è un enorme miglioramento rispetto ai case degli anni 2000, dove la PSU montata in alto aspirava aria calda dalla CPU.

Due implicazioni pratiche:

1. **Il case deve avere piedini sufficientemente alti** (almeno 20-25 mm) perché l'alimentatore possa respirare. Su una moquette a pelo lungo, anche 25 mm potrebbero non bastare: la moquette ostruisce e l'alimentatore si scalda. **Non appoggiare il PC su una moquette.**
2. **La lunghezza massima dell'alimentatore** è dichiarata dal case (tipicamente 160-200 mm). Gli alimentatori ATX standard sono profondi 140-160 mm; quelli ad alta potenza (1200 W+) possono arrivare a 180-200 mm. Se il case dichiara 180 mm "senza gabbia dischi frontale" e voi volete la gabbia, il numero effettivo diminuisce.

Lo **shroud dell'alimentatore** (o "basement cover") è la copertura orizzontale in lamiera che nasconde l'alimentatore e i cavi. È estetica ma anche funzionale: crea un compartimento separato per l'intrico di cavi. Alcuni case hanno uno shroud **perforato**, che permette di montare ventole sopra di esso; altri ne hanno uno solido, il che può essere un problema in un caso specifico: se avete una ventola frontale in immissione bassa e lo shroud la separa dal resto, quell'aria non raggiungerà la GPU.

### 3.6 Gestione dei cavi (cable management)

Lo spazio dietro il vassoio della scheda madre è dove passano tutti i cavi: il 24-pin ATX, gli 8-pin EPS della CPU che devono salire in alto a sinistra, i cavi PCIe/12V-2×6 della GPU, SATA, cavi delle ventole e cavi del pannello frontale.

- **Spazio dietro il vassoio**: sotto i 20 mm è stretto, 25 mm è accettabile, 30+ mm è comodo. Qui è dove i case economici tagliano gli angoli.
- **Gommini passacavo (rubber grommets)**: anelli di gomma nei fori del vassoio che nascondono i bordi e tengono fermi i cavi. La loro assenza non è un difetto funzionale, ma estetico e pratico.
- **Canali e fascette in velcro**: i case moderni ben progettati (Lian Li, Fractal, Phanteks) integrano canali con coperture a scatto e fascette in velcro pre-montate. Fanno una vera differenza nel tempo di assemblaggio.
- Una gestione ordinata dei cavi **non migliora drasticamente le temperature** in un case moderno (la maggior parte dei cavi è dietro il vassoio, fuori dal flusso d'aria), ma un fascio di cavi lasciato penzolare davanti alle ventole in immissione le peggiora sicuramente. E soprattutto: il PC dovrà essere riaperto, e trovarlo organizzato è una benedizione.

### 3.7 Montaggio verticale della GPU e riser PCIe

Il montaggio verticale — la scheda grafica ruotata di 90° e rivolta verso il pannello in vetro — è puramente estetico e richiede due cose: una staffa verticale (a volte inclusa, spesso acquistata separatamente) e un **cavo riser PCIe**, che è una prolunga flessibile che porta il segnale dallo slot della scheda madre alla scheda grafica ruotata.

Ci sono due avvertenze serie, e vanno prese sul serio:

**1. La generazione PCIe del riser deve corrispondere.** I riser sono venduti come "PCIe 3.0", "PCIe 4.0" o "PCIe 5.0". Il bus PCIe raddoppia la sua frequenza di segnalazione ad ogni generazione, e l'integrità del segnale su un cavo flessibile diventa progressivamente più difficile. **Un riser PCIe 3.0 usato con una scheda PCIe 4.0/5.0 causa errori di trasmissione**, che si manifestano come crash, artefatti, schermate nere, o — più insidiosamente — come un downgrade automatico del link a una velocità inferiore che vi costa prestazioni senza dirvelo. Controllate sempre nel BIOS/UEFI o con GPU-Z che il link sia negoziato alla generazione e larghezza (x16) attese. Un riser PCIe 5.0 di qualità costa 60-100 euro **[dato volatile]**; se il budget non lo permette, non montate in verticale.

**2. Il montaggio verticale soffoca la scheda.** Ruotata contro il pannello laterale, la GPU si trova a 15-30 mm dal vetro e le sue ventole aspirano aria da lì. In molti casi, questo significa +8/+15 °C rispetto al montaggio orizzontale. I case progettati per il montaggio verticale (alcuni Lian Li, Hyte Y60/Y70) lasciano più spazio o usano il pannello laterale come presa d'aria. **Se il vostro case non è progettato per questo, il montaggio verticale è un peggioramento termico pagato a caro prezzo.**

---

## 4. Airflow: la discussione seria

### 4.1 Mesh vs. vetro: quanto conta davvero

La domanda più frequente e quella con la risposta più chiara. Il **pannello frontale** è il punto da cui, nella maggior parte dei layout, entra aria fresca. Se il frontale è un pannello in vetro temperato o acrilico solido, l'aria non passa **attraverso** di esso: deve infilarsi nelle fessure laterali (tipicamente 10-20 mm per lato) o superiori. Le ventole in immissione, che sono progettate per lavorare con bassa pressione statica, incontrano un ostacolo e vedono la loro portata effettiva crollare: test in camera anecoica mostrano riduzioni di flusso nell'ordine del 40-70% rispetto alla stessa ventola in aria libera.

L'effetto sui componenti, in test comparativi tra la variante mesh e la variante vetro **dello stesso identico case** (un esperimento eseguito più volte dalla stampa specializzata, ad esempio sui vecchi NZXT H510 vs H510 Flow, o sui Corsair 4000D vs 4000D Airflow), è tipicamente:

- **GPU: 8-15 °C più alta** con un pannello frontale chiuso, con conseguente calo del boost sostenuto;
- **CPU: 3-8 °C più alta**, meno drammatico perché il dissipatore della CPU è più in alto e più vicino alla ventola di scarico posteriore;
- **Rumore: uguale o superiore** con un pannello frontale chiuso, perché le ventole devono girare più velocemente e perché l'aria strozzata nelle fessure genera turbolenza.

La conclusione operativa è brutale e non ammette sfumature: **il pannello frontale deve essere in mesh, o almeno avere una superficie aperta significativa e diretta davanti alle ventole.** Il vetro sul frontale è un difetto di progettazione mascherato da caratteristica estetica. Il vetro **laterale**, invece, è quasi sempre innocuo, perché nella stragrande maggioranza dei layout, il lato non è un percorso primario di airflow.

Una piccola nota tecnica per evitare ingenuità: non tutte le "mesh" sono uguali. Una griglia con fori rotondi piccoli e densi e molto materiale tra i fori può avere un'**area aperta** del 40%, mentre una mesh a nido d'ape ben progettata può raggiungere il 65-75%. E i pannelli perforati con motivi decorativi "a maglie strette" per l'estetica sono spesso peggio di quanto sembri. Se una recensione menziona "mesh restrittiva", è un avvertimento.

### 4.2 Pressione Positiva, Negativa, Neutra

Definiamo con precisione. In uno stato stazionario, la massa d'aria che entra nel case deve essere uguale alla massa che esce — questa è la conservazione della massa, non c'è scelta. Ciò che cambia è **da dove** esce.

- **Pressione positiva**: Il flusso d'aria delle ventole in immissione è maggiore di quello delle ventole in estrazione. L'aria in eccesso esce attraverso fessure, fori delle staffe e interstizi. Il case è "gonfiato".
- **Pressione negativa**: Il flusso d'aria in estrazione supera quello in immissione. L'aria mancante viene aspirata attraverso ogni fessura, ogni foro non filtrato, il retro del case, il vano delle staffe PCIe.
- **Pressione neutra**: Essenzialmente in equilibrio.

Perché ci interessa? **Per la polvere.** In pressione positiva, tutta l'aria in ingresso passa attraverso le ventole di aspirazione, e queste sono l'unico punto dove esistono i **filtri antipolvere**. Entra aria filtrata, e l'aria che esce dalle fessure spinge fuori le particelle di polvere. Il case rimane ragionevolmente pulito, e la polvere si accumula sui filtri, dove può essere rimossa in trenta secondi.

In pressione negativa, l'aria entra dappertutto, e "dappertutto" è non filtrato. La polvere si deposita su dissipatori, alette, schede — dove la pulizia è molto più laboriosa.

**Raccomandazione: leggera pressione positiva.** In pratica, questo significa avere un flusso d'aria in ingresso superiore del 10-30% rispetto a quello in uscita. Attenzione a non confondere però il numero di ventole con il flusso d'aria: tre ventole in ingresso dietro un filtro denso possono muovere meno aria di due ventole in uscita in aria libera. Il filtro è resistenza, e la resistenza riduce il flusso d'aria.

Va anche detto onestamente, perché online è trattata come una religione: **la differenza termica tra le tre configurazioni è di 1-3 °C**, quasi irrilevante. È la manutenzione, non la temperatura, l'argomento serio per la pressione positiva. Un case in pressione negativa ben ventilato è più fresco di uno in pressione positiva mal ventilato. La priorità rimane il **volume d'aria scambiato**, non il segno del differenziale.

### 4.3 Percorso del Flusso d'Aria

Due principi guidano la disposizione:

**Principio 1: L'aria calda sale.** Per convezione naturale, l'aria riscaldata si espande, diminuisce di densità e sale. Questo effetto, da solo, è debole rispetto alla ventilazione forzata, ma dà la direzione corretta: **ingresso basso e frontale, uscita alta e posteriore** significa lavorare con la fisica piuttosto che contro di essa.

**Principio 2: Il percorso deve essere breve e diretto.** L'aria fresca deve raggiungere i dissipatori (GPU in basso, CPU a metà-superiore) senza mescolarsi con aria calda già utilizzata.

Il layout canonico, valido per il 90% dei mid tower:

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

- **Ingresso frontale (2-3 ventole)**: aria fresca che colpisce direttamente la scheda grafica, che è il componente che dissipa più calore.
- **Ingresso inferiore (opzionale, 1-2 ventole)**: se il case lo permette, le ventole sul fondo davanti alla GPU sono estremamente efficaci, poiché soffiano aria fresca direttamente nelle ventole della scheda grafica. È una delle configurazioni più sottovalutate e più efficaci in assoluto. Richiede piedini alti e un filtro.
- **Uscita posteriore (1 ventola, 120 o 140 mm)**: immediatamente dietro il dissipatore della CPU, espellendo l'aria che vi è appena passata prima che ricircoli.
- **Uscita superiore (1-3 ventole)**: raccoglie l'aria calda che sale. Se si monta un radiatore AIO, questa è la sua posizione naturale.

Alcune note controintuitive ma verificate:

- **Non posizionare ventole di aspirazione in alto.** Contrastano la convezione e riciclano aria calda dall'AIO.
- **Il pannello laterale in vetro non è un problema termico** purché il frontale sia aperto. Chi vuole un lato in vetro può averlo senza sensi di colpa.
- **In un case a doppia camera come il Lian Li O11**, il frontale è spesso chiuso, e le prese d'aria sono **il fondo e il lato**. Questi case funzionano molto bene, ma **solo se li si configura come progettato**: aspirazione dal basso + lato, scarico dall'alto + posteriore. Chi monta tutte le ventole in alto per lo scarico e nessuna presa d'aria crea un vuoto e ottiene temperature pessime.

### 4.4 Configurazioni tipiche per numero di ventole

| Ventole | Configurazione | Note |
|---|---|---|
| 2 | 1 frontale IN + 1 posteriore OUT | Il minimo indispensabile. Accettabile solo per sistemi a bassa potenza (< 250 W totali). |
| 3 | 2 frontali IN + 1 posteriore OUT | Configurazione base sensata. Leggera pressione positiva. La più comune nei case entry-level. |
| 4 | 3 frontali IN + 1 posteriore OUT | Ottimo compromesso. Ancora pressione positiva. Standard per case "airflow" di fascia media. |
| 5 | 3 frontali IN + 1 posteriore OUT + 1 superiore OUT | Bilanciata. Buona per sistemi high-end raffreddati ad aria. |
| 6 | 3 frontali IN + 1 posteriore OUT + 2 superiori OUT | Bilanciata/neutra. Configurazione tipica con un AIO da 240/280 in alto. |
| 7+ | 3 frontali IN + 2/3 inferiori IN + 1 posteriore OUT + 3 superiori OUT | Configurazione da entusiasti. Pressione positiva marcata, ottime temperature GPU. |

**I rendimenti marginali diminuiscono rapidamente.** Il salto da 2 a 4 ventole vale molti gradi. Il salto da 6 a 9 vale 1-2 °C e aggiunge rumore. La quarta ventola è un ottimo investimento; la nona è una decorazione.

### 4.5 Filtri antipolvere

Un filtro è una rete fine (nylon o acciaio) posta davanti alle prese d'aria. Fa due cose: intrappola la polvere e **resiste al flusso d'aria**. La resistenza costa flusso, tipicamente 10-20% con un filtro pulito e molto di più con un filtro sporco.

Dove sono realmente necessari:

- **Sotto l'alimentatore**: obbligatorio, e deve essere **rimovibile da dietro o di lato**, non da sotto (altrimenti bisogna sollevare il PC ogni volta).
- **Frontale (davanti alle ventole di aspirazione)**: sì, ed è la difesa principale.
- **Inferiore (se ci sono ventole di aspirazione)**: sì.
- **Superiore (se le ventole sono in estrazione)**: **no, o solo magnetico e rimovibile**. Un filtro su una superficie di estrazione non filtra nulla — l'aria esce — e aggiunge solo resistenza. Serve solo a impedire che la polvere si depositi all'interno **quando il PC è spento**. Se il vostro case ha un filtro magnetico in alto e il PC è quasi sempre acceso, potete toglierlo e guadagnare qualche punto percentuale di estrazione.

**Manutenzione**: i filtri vanno puliti ogni 1-3 mesi a seconda dell'ambiente (case a terra + animali domestici + fumo = ogni mese). Aria compressa o un semplice risciacquo, con la precauzione di **asciugarli completamente** prima di rimontarli.

---

## 5. Ventole del case

### 5.1 Dimensioni: 120, 140, 200 mm

Una ventola è, fisicamente, un'elica che muove un volume d'aria. A parità di **flusso d'aria** (volume d'aria al secondo), una ventola più grande può ruotare più lentamente, perché ogni rotazione sposta più aria. E poiché il rumore aerodinamico dipende fortemente dalla **velocità periferica delle pale**, ruotare più lentamente significa fare meno rumore.

Ecco perché, **a parità di flusso d'aria, una ventola da 140 mm è più silenziosa di una da 120 mm, e una da 200 mm è più silenziosa di entrambe.** È un principio semplice e affidabile.

Tuttavia, ci sono contro-argomenti pratici:

- Le **ventole da 140mm hanno meno posizioni di montaggio**. Molti case accettano 3 x 120mm ma solo 2 x 140mm sul frontale, e in quel caso, **3 x 120mm spostano più aria di 2 x 140mm**, annullando il vantaggio.
- Le **ventole da 200mm hanno una selezione di modelli molto limitata**, ruotano lentamente (600-800 RPM), hanno una **pressione statica molto bassa**, e sono quindi inutilizzabili su radiatori o dietro filtri densi. Sono eccellenti per l'immissione libera in case progettati per esse, ma sono una nicchia.
- I **radiatori da 140mm** (280, 420) sono leggermente più efficienti delle loro controparti da 120mm (240, 360) a parità di lunghezza, ma sono supportati da meno case.

**Regola generale: usa ventole da 140mm quando il case ne accetta lo stesso numero delle ventole da 120mm; altrimenti, usa ventole da 120mm e sii felice.**

### 5.2 Specifiche: leggerle senza farsi ingannare

Ogni ventola ha una scheda tecnica con quattro numeri chiave. Devono essere letti insieme, mai isolatamente.

**CFM (Cubic Feet per Minute).** Questo è il **flusso d'aria**, ovvero quanta aria sposta la ventola *in condizioni di resistenza zero*, in aria libera. Valori tipici: 50-80 CFM per una ventola da 120mm, 60-100 per una ventola da 140mm. In Europa, può essere espresso anche in m³/h (1 CFM ≈ 1.7 m³/h). **Il numero dichiarato è il massimo teorico e non lo vedrai mai nella realtà**, perché c'è sempre resistenza all'interno del case.

**Pressione statica (mmH₂O, millimetri di colonna d'acqua).** Questa è la capacità della ventola di **spingere l'aria contro la resistenza**. Valori tipici: 1.0-1.5 mmH₂O per una ventola "airflow", 2.0-3.5 mmH₂O per una ventola "static pressure" ad alta velocità. Questo è il parametro che conta quando la ventola deve spingere l'aria attraverso:
- le fitte alette di un **radiatore**;
- le alette di un **dissipatore ad aria**;
- un **filtro antipolvere** denso;
- una griglia frontale restrittiva.

**Quando dare priorità all'uno o all'altro**: una ventola in **immissione libera** o **scarico libero** (davanti a un buco, essenzialmente) beneficia del flusso d'aria. Una ventola su un **radiatore o dissipatore** beneficia della pressione statica. Una ventola davanti a un filtro è una via di mezzo. Nella realtà del 2026, le migliori ventole "ibride" (Arctic P12/P14, Noctua NF-A12x25, Phanteks T30) sono buone in entrambi i regimi, e la distinzione è meno drammatica di quanto non fosse in passato — tuttavia, è ancora vero che una ventola esplicitamente dichiarata "high airflow" con pale sottili e larghe (come Noctua NF-S12, o le vecchie Corsair AF "airflow") si comporta male su un radiatore.

**RPM (Revolutions Per Minute).** La velocità di rotazione. Insieme al diametro, determina la velocità periferica e quindi il rumore. Una ventola con un range di 200-2000 RPM è molto più versatile di una con un range di 800-1200: la prima può essere resa quasi inudibile a bassi carichi e spinta al massimo sotto stress.

**dBA (decibel A-weighted).** Rumore, misurato con una ponderazione che riflette la sensibilità dell'orecchio umano. **Questo è il numero meno affidabile sulla scheda tecnica**, perché le condizioni di misurazione (distanza, camera anecoica, carico) non sono standardizzate tra i produttori, e perché il dBA non cattura la **qualità** del rumore. Una ventola da 30 dBA con un ronzio a bassa frequenza è più tollerabile di una ventola da 28 dBA con un fischio acuto o un ticchettio del cuscinetto. **I valori dBA dichiarati sono utili solo per confrontare ventole dello stesso produttore.** Per confronti tra marche diverse, fidati di misurazioni indipendenti che normalizzano il rumore (ad esempio, "a 35 dBA, quanti CFM sposta?", che è il modo corretto di confrontare).

Un'ultima insidia: le curve **PQ** (pressione-flusso). Un produttore serio pubblica un grafico che mostra quanta aria eroga la ventola a ogni livello di contropressione. Questo è l'unico dato completo. Noctua, Arctic e Phanteks lo fanno; molti altri no.

### 5.3 PWM (4 pin) vs. DC (3 pin)

Il connettore per le ventole di sistema è **standardizzato**:

| Pin | Funzione |
|---|---|
| 1 | GND (Massa) |
| 2 | +12 V |
| 3 | Sense / Tachometer (legge gli RPM) |
| 4 | **PWM** (segnale di controllo) |

Una **ventola a 3 pin** è regolata in **DC (Direct Current)**: la scheda madre abbassa la tensione di alimentazione, da 12 V in giù, e la ventola rallenta. Questo è semplice, ma ha una limitazione: sotto una certa tensione (tipicamente 5-6 V), il motore non ha abbastanza coppia per avviarsi o mantenere la rotazione, quindi la velocità minima è relativamente alta (spesso non scende sotto il 40-50% della velocità nominale).

Una **ventola a 4 pin** usa la **PWM** (Pulse Width Modulation): la tensione rimane sempre a 12 V, ma un segnale a onda quadra (25 kHz per lo standard Intel) accende e spegne rapidamente il circuito di pilotaggio del motore. Variando il **duty cycle** (la percentuale di tempo in cui il segnale è alto), si controlla la velocità. Il vantaggio è enorme: la **coppia di spunto rimane piena**, quindi può scendere a velocità molto basse (10-20%, cioè 200-300 RPM) con avvio affidabile, e il controllo è molto più preciso e lineare.

**Raccomandazione: comprare ventole PWM.** Il costo aggiuntivo è di pochi euro ed è denaro ben speso. Se la vostra scheda madre ha un header a 4 pin (tutte quelle moderne lo hanno), potete comunque impostarlo in modalità DC per pilotare ventole a 3 pin — la maggior parte dei BIOS/UEFI lo permette, e alcuni rilevano automaticamente il tipo.

**Controllo da BIOS/UEFI.** Ogni scheda madre moderna ha una sezione (Q-Fan su ASUS, Smart Fan su Gigabyte/MSI, Fan Xpert, ecc.) dove si definisce una **curva della ventola**: una funzione che lega la velocità della ventola a una temperatura di riferimento. Tre consigli non banali:

1. **Scegliere la giusta sorgente di temperatura.** Se si collegano le ventole del case alla temperatura del **package della CPU**, si avranno ventole che accelerano e rallentano costantemente, perché la temperatura di una CPU moderna fluttua di 20 °C in mezzo secondo a ogni micro-carico. Il risultato è il famigerato **fan surging**, il "rantolo" del PC, ed è molto più fastidioso di un rumore costante. Invece, collegarle alla temperatura della **scheda madre / VRM / chipset** (una grande massa termica che cambia lentamente) o, se il BIOS lo permette, alla GPU.
2. **Usare lo "smoothing" / "step up-down time"**: quasi tutti i BIOS permettono di impostare un ritardo (in secondi) prima che la ventola reagisca a un cambiamento. Impostatelo a 3-6 secondi. Questo elimina quasi completamente il surging.
3. **Non puntare allo zero-RPM sul case.** Alcune ventole si fermano completamente sotto una certa soglia. Questo va bene per la GPU; per le ventole del case, un flusso minimo permanente a 400-500 RPM è inudibile e mantiene l'aria in movimento.

**Hub e daisy-chain.** Le schede madri hanno tipicamente 4-7 header per ventole, e un case ben popolato può averne 8-9. Soluzioni:

- **Hub / splitter passivo**: uno splitter che collega più ventole a un singolo connettore. Attenzione: **ogni connettore ha un limite di corrente**, tipicamente 1 A (12 W). Una ventola PWM consuma 0,1-0,3 A, quindi 3-4 ventole per connettore è il limite prudente. Inoltre, solo **una** ventola può riportare il segnale del tachimetro; le altre vengono ignorate (quindi il BIOS mostrerà un solo valore di RPM).
- **Hub attivo**: alimentato direttamente dall'alimentatore (con un connettore SATA o Molex) e riceve solo il segnale PWM dalla scheda madre. Può gestire 6-10 ventole senza problemi. Questa è la soluzione corretta per un sistema con molte ventole, e molti case di fascia media/alta ne includono uno.
- **Daisy-chain**: alcune serie di ventole (Lian Li Uni Fan, Corsair iCUE Link, Phanteks D30) si agganciano fisicamente l'una all'altra e trasmettono alimentazione e dati tramite contatti, eliminando quasi tutti i cavi. Questa è una soluzione molto elegante per la gestione dei cavi ed è una delle ragioni per cui questi prodotti sono costosi. Lo svantaggio: **ti legano a un ecosistema proprietario**, con un controller dedicato e un software dedicato.

### 5.4 Cuscinetti

Il cuscinetto è ciò che supporta l'albero rotante. Determina **il rumore meccanico, la durata e l'orientamento di montaggio consentito**.

| Tipo | Descrizione | Durata tipica (MTBF) | Rumore | Note |
|---|---|---|---|---|
| **A strisciamento** (Sleeve) | Boccola cilindrica lubrificata a olio | 20.000-30.000 h | Silenzioso da nuovo | Il lubrificante fuoriesce se montato **orizzontalmente**; si usura e diventa rumoroso. Economico. |
| **A sfera** (Ball bearing) | Sfere in gabbia | 50.000-75.000 h | Più rumoroso (ronzio/ticchettio) | Durevole, tollera il calore e qualsiasi orientamento. Utilizzato in ambienti server. |
| **FDB** (Fluid Dynamic Bearing) | A strisciamento con film d'olio a pressione idrodinamica e tenuta | 60.000-150.000 h | Molto silenzioso | **Il miglior compromesso.** Standard de facto nelle ventole di qualità. Nomi commerciali: SSO2 (Noctua), Rifle, Hydro, HDB. |
| **Maglev / Levitazione magnetica** | Albero supportato da campo magnetico, quasi senza attrito | 100.000+ h | Estremamente silenzioso | Costoso, marketing pesante, benefici reali ma marginali rispetto a un buon FDB. |

**Regola d'acquisto: cerca FDB (o equivalenti commerciali) e ignora tutto il resto.** I cuscinetti a strisciamento sono accettabili solo nelle ventole preinstallate in case economici e solo se montati verticalmente. Maglev è un lusso.

Un'ultima osservazione onesta: le durate MTBF (Mean Time Between Failures) dichiarate—150.000 ore sono 17 anni—sono estrapolazioni statistiche da test accelerati, non promesse. Servono per confronto, non per pianificazione.

### 5.5 Marche di ventole

| Marchio | Modelli di riferimento | Prezzo indicativo (ciascuno) | Posizionamento |
|---|---|---|---|
| **Arctic** | P12 / P14 (PWM PST), P12 Max | 6-13 € | **Il re assoluto del rapporto qualità/prezzo.** Prestazioni 3-5% inferiori ai top, un quinto del prezzo. Estetica spartana, cavo leggermente rigido. Se il budget conta, questa è la risposta. |
| **Noctua** | NF-A12x25 (il riferimento), NF-A14, NF-P12 redux | 20-35 € | Lo standard tecnico aureo. Tolleranze minime (0.5 mm di distanza dalla pala), eccellente pressione statica e flusso d'aria, silenziosità esemplare, 6 anni di garanzia. Colore marrone-beige divisivo (esiste la linea "chromax" nera, che costa di più). |
| **Phanteks** | T30-120 | 25-35 € | Ventola spessa 30 mm, tre modalità di velocità, prestazioni fuori scala sui radiatori. Ingombro non standard. |
| **be quiet!** | Silent Wings 4 / Pro 4 | 20-30 € | Focalizzate sul silenzio. Eccellenti, un gradino sotto Noctua nella pressione statica. |
| **Lian Li** | Uni Fan SL/TL/SL-Infinity | 20-30 € (kit da 3: 60-90 €) | Daisy-chain, RGB spettacolare, buone prestazioni. Ecosistema chiuso. |
| **Corsair** | RS/QX, iCUE Link LX/RX | 20-35 € | Buone, ma il valore è nel software iCUE e nell'ecosistema Link. Costose. |
| **Thermalright** | TL-C12 / TL-B12 | 5-9 € | Alternativa cinese molto economica, sorprendentemente buona. Qualità costruttiva variabile. |

**[dati volatili: i prezzi cambiano frequentemente e i modelli vengono aggiornati ogni 18-24 mesi.]**

**Quando ha senso spendere.** Il salto da una ventola generica preinstallata a una Arctic P12 vale molto: migliori temperature, minor rumore, 8 euro. Il salto da una Arctic P12 a una Noctua NF-A12x25 vale 25 euro in più per **2-4 °C e un paio di dB**. Ne vale la pena se: (a) il PC è a mezzo metro dalla testa in una stanza silenziosa; (b) la ventola è su un radiatore o un dissipatore, dove si esprime la superiorità di Noctua nella pressione statica; (c) si è già ottimizzato tutto il resto. **Non ne vale la pena** se si ha ancora una GPU asfittica in un case chiuso: prima si sistema il case.

---

## 6. Marchi di Case

Non esiste un "migliore assoluto": ci sono aziende con filosofie e livelli di raffinatezza diversi. Ecco la panoramica, con l'avvertenza che **i modelli specifici invecchiano rapidamente** e vanno verificati **[dati volatili]**.

**Lian Li.** Storicamente produttore di alluminio premium, oggi il nome più forte nel segmento enthusiast grazie alla famiglia **O11** (O11 Dynamic, O11 Dynamic EVO, EVO XL e derivati). Si tratta di case a doppia camera: la camera principale ospita la scheda madre e la GPU, mentre la camera laterale nasconde l'alimentatore e i cavi. Offrono un enorme supporto per i radiatori (fino a tre radiatori da 360mm). Ottima costruzione, ergonomia superba. **Nota: il pannello frontale è chiuso.** Vanno configurati con immissione dal basso e dal lato. L'O11 Vision e le versioni "acquario" con tre pannelli in vetro richiedono ancora più cura. Ottima anche la linea **Lancool** (203/216/207); è la loro offerta "airflow-first" a prezzi molto più contenuti e spesso offre il miglior rapporto qualità-prezzo dell'intero mercato.

**Fractal Design.** L'azienda svedese con la migliore reputazione per design e finiture. Tre linee da conoscere: **Meshify** (airflow-first, frontale in mesh angolare, archetipo del case ben ventilato), **North** (estetica scandinava con frontale in legno di noce — e, notevole, con una vera mesh dietro il legno, rendendolo bello *e* funzionale), **Torrent** (il case da airflow estremo, con due enormi ventole frontali da 180mm, prestazioni termiche di riferimento, e un layout molto particolare). Inoltre, la linea **Define** per chi vuole ancora la insonorizzazione. Costruzione, tolleranze e accessori sono sempre di prim'ordine. Prezzi medio-alti.

**be quiet!** Azienda tedesca. Storicamente focalizzata sul silenzio (le linee **Silent Base**, **Dark Base**), ma con un deciso spostamento verso l'airflow con le linee **Pure Base 500DX / 500FX** e **Shadow Base**. Le ventole incluse sono di ottima qualità (le loro ventole sono un vero punto di forza). Costruzione solida, estetica sobria e "adulta".

**Corsair.** Ampia gamma di prodotti, dal **4000D Airflow** (per anni la scelta predefinita per un mid-tower economico e ben ventilato) alla serie **iCUE** con il suo ecosistema RGB e sensori integrati. Software iCUE potente ma pesante. Qualità solida, prezzi medi. La versione "Airflow" di ogni modello è quella da comprare; la versione semplice con pannello frontale chiuso no.

**NZXT.** Estetica minimalista molto riconoscibile (la linea **H**). Storicamente, hanno sbagliato l'airflow (l'H510 con il suo pannello frontale chiuso è un esempio da manuale di questo errore) e poi lo hanno corretto con le versioni **Flow**. Buona costruzione, prezzi un po' alti per quello che offrono, ottima ergonomia interna. Se comprate NZXT, comprate un modello Flow.

**Phanteks.** Molto apprezzata dagli appassionati. Linee **Eclipse** (P400/P500 e derivati) e **NV** (case in vetro a doppia camera). Ottima ingegnerizzazione interna, molte soluzioni intelligenti per il cable management, spesso configurazioni di ventole generose.

**Cooler Master.** Gamma molto ampia, qualità variabile. L'**NR200 / NR200P** è il punto di riferimento negli SFF accessibili. La vecchia serie **MasterBox** è stata per anni la scelta economica. Verificare modello per modello: la coerenza qualitativa non è il loro forte.

**Montech, DeepCool, Antec, Thermaltale.** Il segmento budget aggressivo. **Montech** in particolare (serie AIR, King, XR) si è guadagnata una notevole reputazione offrendo case con 3-6 ventole ARGB incluse a prezzi che vanno dai 60-90 euro, con una costruzione onesta. **DeepCool** (serie CH e Morpheus) è simile. Sono la scelta razionale per chi ha un budget limitato: **si accetta lamiera più sottile e finiture meno raffinate in cambio di un ottimo airflow e ventole incluse.** Questo è un compromesso molto più intelligente che comprare un case "premium" chiuso.

### 6.1 Cosa distingue un case ben progettato

Al di là del marchio, ecco i segni concreti di qualità, che potete verificare in una video recensione:

1. **Spessore della lamiera** (0,8-1,0 mm nei casi ben fatti, 0,55-0,6 mm in quelli economici). La lamiera sottile flette, vibra e risuona.
2. **Bordi arrotolati**, senza tagli netti.
3. **Tolleranze**: i pannelli si incastrano, le viti entrano senza forzare, il vetro non "sfrega".
4. **Ergonomia di montaggio**: canali per i cavi, fascette in velcro preinstallate, punti di ancoraggio, fan hub incluso, staffa PSU rimovibile, filtri rimovibili senza smontare nulla.
5. **Dotazione ventole**: quante sono incluse e di che qualità. Un case da 90 euro con 4 ventole PWM decenti costa meno di un case da 80 euro con zero ventole (a cui vanno aggiunti 40 euro di Arctic P12).
6. **Front I/O**: numero e tipo di porte USB. Nel 2026, pretendete almeno **una USB-C** (idealmente USB 3.2 Gen 2 a 10 Gbps o superiore) e verificate che la scheda madre abbia l'header interno corrispondente — è un classico errore comprare un case con USB-C frontale e una scheda madre economica che non ha l'header per collegarla.
7. **Documentazione e ricambi**: i produttori seri vendono ricambi (viti, filtri, pannelli, piedini). Fractal, Lian Li, be quiet! lo fanno.

---

## 7. Come scegliere: la procedura

Seguite quest'ordine. **L'ordine è il punto:** invertirlo è la causa di quasi tutti i cattivi acquisti.

**Passo 1 — Formato scheda madre.** Avete già scelto la scheda madre (o almeno il suo formato). Questo definisce il minimo: ITX → tutto; mATX → mid/mini tower; ATX → mid tower o più grande; E-ATX → controllate la **larghezza in millimetri**, non la sigla.

**Passo 2 — Spazi di ingombro (clearance).** Segnatevi i tre numeri della vostra configurazione e confrontateli con le specifiche tecniche del case, con un margine:
- lunghezza **GPU** (modello specifico) + 15 mm di margine per i cavi;
- altezza **dissipatore CPU** + 5 mm;
- dimensioni e spessore **radiatore** (se AIO) e sua posizione;
- lunghezza **PSU**;
- numero di **drive da 3.5"**.

**Passo 3 — Airflow.** Il frontale **deve** essere a mesh o comunque avere un percorso diretto per l'aria. Se non lo è, il case è escluso — a meno che non sia un dual-chamber pensato per prendere aria dal basso e dal lato, in quel caso va bene ma va configurato correttamente. Controllate quante posizioni per ventole ci sono e dove.

**Passo 4 — Qualità e dotazioni.** Quante ventole sono incluse? Di che tipo (PWM o 3-pin)? C'è un hub? Qual è lo spessore della lamiera? Quanto spazio c'è dietro il tray? **Calcolate il costo totale reale**: prezzo case + ventole aggiuntive necessarie.

**Passo 5 — Estetica.** Ora, e solo ora, scegliete quello che vi piace di più tra quelli che sono sopravvissuti ai passi 1-4.

**Passo 6 — Budget.** Se la vostra scelta preferita supera il budget, tornate al passo 5 e scegliete la seconda. Non tornate al passo 3.

### 7.1 Errori tipici dell'acquirente

**1. Il case "acquario".** Tre pannelli di vetro, illuminazione RGB, zero prese d'aria. Fa un figurone in foto e cuoce i componenti. Se proprio lo volete, sappiate che state pagando 10-15 °C sulla GPU, e almeno configuratelo con robuste ventole in estrazione e in immissione dal basso, se possibile.

**2. Comprare il case prima della GPU.** La scheda grafica è il componente più ingombrante e variabile. Sceglietela prima, o almeno decidete il tier e prendete un case con clearance generosa (350+ mm).

**3. Fidarsi della designazione del chip invece che del modello.** "Ci sta una RTX 5080?" non è una domanda con una sola risposta. La domanda giusta è: "Ci sta questa specifica RTX 5080, che è lunga 336 mm e spessa 3.2 slot?".

**4. Case senza ventole incluse.** Alcuni case, specialmente quelli "minimalisti premium", sono venduti con zero o una sola ventola. Il prezzo sembra competitivo finché non aggiungi 40-70 € di ventole. **Fai il conto del costo totale.**

**5. Ignorare la rumorosità dei pannelli.** Un pannello laterale sottile che vibra a una certa frequenza può rendere insopportabile un sistema altrimenti silenzioso. Questo difetto non compare in nessuna scheda tecnica: lo scopri solo nelle recensioni.

**6. Mettere tutte le ventole in estrazione.** "Più aria esce, più è fresco" è un ragionamento fallace. Il case va in depressione, l'aria entra da ogni fessura (non filtrata) e il flusso d'aria effettivo cala perché le ventole lavorano contro un'enorme resistenza. Serve un percorso: **l'aria entra da qualche parte, esce da qualche altra.**

**7. Montare le ventole al contrario.** Ogni ventola ha una direzione. Il flusso d'aria va **dal lato con la griglia di supporto del motore verso il lato con le pale** — o, più semplicemente: guarda le **frecce stampate sul telaio della ventola** (una indica il flusso d'aria, l'altra la direzione di rotazione). In assenza di frecce: l'aria esce dal lato dove si trovano i quattro/sette montanti che sorreggono l'hub.

**8. Il PC per terra su tappeto.** Ostruisce la presa d'aria dell'alimentatore e trasforma il fondo in un aspirapolvere. Almeno una superficie rigida, preferibilmente rialzata.

**9. Scegliere SFF come prima build.** Porta a un costoso vicolo cieco.

**10. Trascurare l'header USB-C.** Verifica che la scheda madre abbia il connettore interno "USB 3.2 Gen 2 Type-C" (20-1 pin) se il case ha una USB-C frontale. Altrimenti, quella porta resterà un buco decorativo.

### 7.2 Raccomandazioni per fascia di prezzo

**[Dati volatili: prezzi e modelli. Verificare prima dell'acquisto.]**

**Entry (50-90 €).** L'obiettivo è massimizzare il flusso d'aria e le ventole incluse, accettando lamierino sottile e finiture semplici. Guarda **Montech** (serie AIR / XR), **DeepCool** (serie CH), **Cooler Master** (MasterBox), **Lian Li Lancool 207/216** se il prezzo scende in questa fascia. Cerca: frontale mesh, 3-4 ventole incluse (idealmente PWM), supporto GPU ≥ 330 mm, cooler ≥ 160 mm. Non cercare: vetro su tre lati, RGB elaborato.

**Mid (90-160 €).** Questa è la fascia con il miglior rapporto qualità-prezzo dell'intero mercato. **Fractal Design North / Pop Air / Meshify**, **Lian Li Lancool 216/207**, **Corsair 4000D Airflow**, **be quiet! Pure Base 500DX**, **Phanteks Eclipse**, **NZXT H7 Flow**. Aspettati: lamierino dignitoso, ottima ergonomia, 3-4 ventole di qualità incluse, supporto AIO da 360, cable management curato, USB-C frontale. **Se non hai esigenze particolari, il tuo case è qui.**

**High-end (160-300+ €).** Si paga la qualità costruttiva, l'alluminio, la modularità, gli ecosistemi di ventole, il supporto multi-radiatore. **Lian Li O11 Dynamic EVO / EVO XL**, **Fractal Design Torrent** (il campione delle temperature) o **Define/Meshify 2 XL**, **be quiet! Dark Base**, **Hyte Y70**. Compra per un motivo specifico: custom loop, doppio radiatore da 360mm, GPU verticale ben progettata, forte esigenza estetica. **Non comprarlo aspettandoti temperature drasticamente migliori di un buon case da 120 euro: la differenza c'è ma è piccola.**

**Nota sugli SFF.** In questo formato, non esiste un entry-level sensato. Il biglietto d'ingresso serio parte dal Cooler Master NR200P (~100 €) e sale rapidamente, ma va aggiunto al premium per un alimentatore SFX (+40-70 €) e spesso un cooler low-profile e una GPU corta. **Il costo reale di un SFF è 150-300 € più alto di un mid-tower equivalente.**

---

## 8. Riepilogo Operativo — Lista di Controllo per la Scelta del Case

Stampalo, o copialo in un file, e compilalo prima di ordinare.

### A. Vincoli Dimensionali (obbligatorio: se uno fallisce, il case è escluso)

- [ ] **Formato scheda madre**: la mia mobo è ______ (ITX / mATX / ATX / E-ATX di ___ mm di larghezza). Il case la supporta? **Sì / No**
- [ ] **Lunghezza GPU**: il mio modello specifico è lungo ______ mm. Il case dichiara ______ mm **nella configurazione che userò** (con radiatore/ventole frontali installate). Margine ≥ 15 mm? **Sì / No**
- [ ] **Spessore GPU**: ______ slot. Il case ha abbastanza slot liberi sotto? **Sì / No**
- [ ] **Spazio per il cavo 12V-2×6** (se la GPU lo usa): il pannello laterale è a ≥ 30 mm dal connettore? **Sì / No**
- [ ] **Altezza dissipatore CPU**: il mio è alto ______ mm. Il case dichiara ______ mm. Margine ≥ 5 mm? **Sì / No**
- [ ] **Radiatore** (se AIO): dimensione ______ (240/280/360/420), spessore radiatore + ventola ______ mm. Il case lo supporta nella posizione ______ (superiore/frontale)? Interferisce con RAM o dissipatori VRM? **Sì / No**
- [ ] **Lunghezza PSU**: il mio è profondo ______ mm. Il case dichiara ______ mm (con le gabbie che intendo mantenere). **OK / KO**
- [ ] **Drive da 3.5"**: me ne servono ______. Il case ne accetta ______. **OK / KO**

### B. Flusso d'aria (obbligatorio)

- [ ] Il **frontale è a mesh** o ha un percorso diretto per il flusso d'aria davanti alle ventole? Se è chiuso, è un design a doppia camera progettato per aspirare aria dal basso/lato? **Sì / No — se No, scartare.**
- [ ] Quante **posizioni per ventole** ci sono? Frontali ___ / Superiori ___ / Posteriori ___ / Inferiori ___ / Laterali ___
- [ ] Posso ottenere almeno la configurazione minima **3 IN (frontali) + 1 OUT (posteriore)**? **Sì / No**
- [ ] Ci sono **filtri antipolvere** su tutte le prese d'aria, e sono **rimovibili senza smontare il PC**? **Sì / No**
- [ ] I **piedini** sono alti ≥ 20 mm per l'aspirazione dell'aria della PSU? **Sì / No**

### C. Qualità e Caratteristiche

- [ ] **Quante ventole sono incluse?** ______ — di che tipo? (PWM 4-pin / DC 3-pin) — di che qualità?
- [ ] **Costo totale reale** = prezzo case (______ €) + ventole aggiuntive (______ €) = ______ €
- [ ] È incluso un **hub per ventole**? **Sì / No** — La mia mobo ha abbastanza connettori? ______
- [ ] Lo **spazio dietro il vassoio della scheda madre** è ≥ 25 mm? **Sì / No**
- [ ] Ci sono **gommini passacavo, canali per cavi, fascette in velcro**? **Sì / No**
- [ ] Le recensioni riportano **lamiera sottile, vibrazioni, tolleranze scadenti, bordi taglienti**? **Sì / No**
- [ ] Il **pannello I/O frontale** ha una **USB-C**? E la mia scheda madre ha l'apposito **header interno**? **Sì / No**

### D. Configurazione, dopo l'acquisto

- [ ] **Ventole frontali/inferiori come immissione**, **ventole posteriori/superiori come estrazione**. Frecce di direzione controllate su ogni ventola.
- [ ] **Leggera pressione positiva**: flusso d'aria in ingresso ≥ flusso d'aria in uscita.
- [ ] Curve delle ventole nel BIOS/UEFI **legate a una sorgente lenta** (mobo/VRM/GPU), **non al package della CPU**, con smussatura di 3-6 secondi.
- [ ] **Gabbie da 3.5" rimosse** se non necessarie, per liberare il flusso d'aria frontale.
- [ ] **Staffette degli slot PCIe inutilizzati reinstallate** al loro posto.
- [ ] **Filtro superiore** rimosso se il PC è quasi sempre acceso e la parte superiore è tutta in estrazione (opzionale).
- [ ] **Standoff** controllati: uno per ogni foro della scheda madre, **nessun extra**.
- [ ] Test di validazione: dopo 20 minuti di stress test combinato CPU+GPU, la temperatura della GPU è **inferiore a 80 °C** e la CPU non va in throttling termico. Se no, il problema è il case o la configurazione delle ventole, non il dissipatore.
- [ ] **Promemoria manutenzione**: pulire i filtri ogni **1-3 mesi**.

### E. Le tre regole d'oro, se dimenticate tutto il resto

1.  **Il frontale deve respirare.** Mesh, non vetro.
2.  **Misurate la GPU prima di comprare il case.** Il modello specifico, in millimetri.
3.  **Quattro buone ventole in un case da 100 euro battono due ventole in un case da 250 euro.** Il volume di aria scambiata è ciò che raffredda; tutto il resto è secondario.

---

*Fine capitolo. Il prossimo capitolo — la periferica di visualizzazione (il monitor) — conclude la parte consumer prima di passare all'hardware professionale e workstation.*

---

[← Precedente](06-alimentatore-psu.html) · [Tutti i capitoli](./) · [Successivo →](08-raffreddamento.html)
