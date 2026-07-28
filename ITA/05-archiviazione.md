---
title: "Capitolo 5 - Archiviazione"
parent: "Edizione italiana"
nav_order: 5
---

<details open markdown="block">
  <summary>Indice del capitolo</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## 1. Panoramica: Cos'è l'archiviazione e perché è diversa dalla memoria

Ogni volta che accendi un computer, succede qualcosa che diamo per scontato: la macchina "ricorda" se stessa. Trova il sistema operativo, i programmi installati, i documenti scritti il giorno prima, le foto di dieci anni fa. Questa capacità di ricordare oltre lo spegnimento è il compito dello **storage**, ovvero la memoria di massa, e per capire veramente cosa fa, bisogna prima capire cosa la RAM *non* fa.

La RAM (Random Access Memory) è **volatile**: conserva i dati solo finché è alimentata. Nel momento esatto in cui togliamo l'alimentazione, il contenuto delle celle DRAM svanisce, perché ogni bit è memorizzato come una minuscola carica elettrica in un condensatore che si scarica in millisecondi se non viene continuamente "rinfrescato". La RAM è estremamente veloce e completamente amnesica. Lo storage è l'esatto opposto: è **non volatile**, cioè conserva le informazioni anche senza alimentazione, per mesi o anni, ma paga questa persistenza con latenze che, rispetto alla RAM, sono astronomiche. Per dare una scala mentale: un accesso alla RAM costa circa 80-100 nanosecondi; un accesso a un buon SSD NVMe costa 50.000-80.000 nanosecondi (cioè 50-80 microsecondi); un accesso a un hard disk meccanico costa 8.000.000-15.000.000 nanosecondi (8-15 millisecondi). Tra RAM e hard disk ci sono cinque ordini di grandezza: è la differenza tra prendere un libro dalla propria scrivania e ordinarlo per posta.

Il computer è quindi costruito come una **gerarchia di memoria**, dove ogni livello diminuisce in velocità e aumenta in capacità e persistenza: registri della CPU → cache L1/L2/L3 → RAM → storage → (eventualmente) archiviazione remota o backup offline. Lo storage è il gradino dove la piramide diventa permanente. Tutto quello che vogliamo ritrovare domani deve, prima o poi, atterrare lì.

Il ruolo dello storage nel sistema è duplice. Da un lato, è l'**archivio**: contiene il sistema operativo, gli eseguibili e i dati utente. Dall'altro lato, è il **collo di bottiglia della reattività percepita**. Questo secondo punto è quello che gli utenti sottovalutano di più. Quando un PC viene acceso, quando un programma pesante viene aperto, quando un videogioco carica un livello, o quando un progetto di sviluppo compila migliaia di file sorgente, ciò che determina i secondi di attesa non è quasi mai la potenza bruta della CPU: è la velocità con cui i dati viaggiano dallo storage alla RAM. Un processore di fascia alta accoppiato a un disco meccanico dà una sensazione di lentezza che nessuna quantità di core può compensare, perché la CPU passa la maggior parte del tempo in attesa (in gergo, in *I/O wait*, input/output wait). Per questo, nella scala di priorità di un assemblaggio, il passaggio da HDD a SSD è storicamente l'upgrade con il miglior rapporto costo-miglioramento percepito di sempre nella storia dei personal computer.

### 1.1 L'evoluzione: HDD → SATA SSD → NVMe SSD

La storia dello storage consumer negli ultimi vent'anni è la storia di due rivoluzioni successive, ed è utile ripercorrerla perché spiega perché il mercato odierno ha la forma che ha, con i suoi retaggi e le sue insidie.

**Fase Uno: Il Regno dell'HDD (fino al ~2010).** Per decenni, l'unico storage di massa economicamente sensato era l'hard disk drive magnetico, un dispositivo elettromeccanico con parti in movimento. Tutta l'architettura software dei PC — il modo in cui i sistemi operativi ordinano le richieste di lettura, il concetto stesso di "deframmentazione", le strategie di caching — era progettata attorno alle limitazioni fisiche di un braccio meccanico che si muove su un piatto rotante.

**Fase Due: L'Arrivo degli SSD SATA (2008–2015).** Gli SSD (Solid State Drive) sostituiscono le parti in movimento con la memoria flash. Ma i primi SSD consumer emersero in un mondo dove l'interfaccia standard era **SATA** (Serial ATA, Serial Advanced Technology Attachment), un'interfaccia progettata *per gli hard disk*. Gli SSD entrarono così nel PC travestiti da hard disk: stesso cavo, stesso connettore, stesso fattore di forma da 2.5 pollici, stesso protocollo software (AHCI, di cui parleremo). Il risultato fu comunque una rivoluzione percepita — i tempi di avvio crollarono da un minuto a dieci secondi — ma fu una mezza rivoluzione, perché l'SSD parlava una lingua pensata per un dispositivo meccanico. Il limite di banda del SATA III, circa 550 MB/s reali, fu saturato quasi subito dalle prime generazioni di SSD decenti, e da lì in poi tutti gli SSD SATA del mondo — dal più economico al più costoso — riportarono più o meno gli stessi numeri sequenziali. Il collo di bottiglia si era spostato dal drive all'interfaccia.

**Fase Tre: NVMe e PCIe (2015-oggi).** La soluzione è rimuovere l'interfaccia legacy e connettere la memoria flash direttamente al bus più veloce disponibile nel sistema, il **PCIe** (Peripheral Component Interconnect Express), lo stesso bus a cui è connessa la scheda grafica. Un nuovo protocollo, **NVMe** (Non-Volatile Memory Express), è definito sopra PCIe, progettato da zero per la memoria flash: parallelismo massivo, code multiple, latenze ridotte, nessun retaggio meccanico. Il risultato, nel giro di pochi anni, è un salto di banda da 550 MB/s a 3.500 MB/s (PCIe 3.0), poi 7.400 MB/s (PCIe 4.0), poi 14.000 MB/s (PCIe 5.0), con latenze che diminuiscono di conseguenza.

Oggi, nel 2026, il panorama consumer è questo: gli SSD NVMe sono lo standard de facto per i drive di sistema, gli SSD SATA sopravvivono come storage secondario economico e nelle macchine più vecchie, e gli HDD si sono ritirati in una nicchia precisa ma ancora molto importante — l'archiviazione ad alta capacità e basso costo per terabyte. Nessuna di queste tre tecnologie è "morta". Sono semplicemente specializzate.

> **[dati volatili]** Dalla fine del 2025, il mercato dello storage sta vivendo una fase anomala di forti rincari. La domanda di memoria generata dalle infrastrutture di intelligenza artificiale ha dirottato la capacità produttiva di NAND e DRAM verso il segmento enterprise, con conseguenti aumenti di prezzo percentuali a doppia cifra per gli SSD consumer e ridotta disponibilità per alcune linee di prodotto. Nello stesso periodo, Micron ha annunciato l'uscita dal mercato consumer con il marchio Crucial, per concentrarsi su datacenter e clienti OEM: i modelli Crucial citati in questo capitolo rimangono validi come riferimento tecnico e potrebbero essere ancora disponibili come scorte residue, ma non devono più essere considerati una linea di prodotti in evoluzione. Tutti i prezzi indicati di seguito devono quindi essere presi come ordini di grandezza da verificare al momento dell'acquisto, non come valori affidabili.

---

## 2. HDD vs. SSD: Due Architetture Fisiche Differenti

### 2.1 Come Funziona un Hard Disk

Un **HDD** (Hard Disk Drive) è, letteralmente, un giradischi di precisione sigillato in una scatola di alluminio. All'interno, ci sono uno o più **piatti**, dischi rigidi di alluminio o vetro rivestiti con un sottilissimo strato di materiale ferromagnetico. I piatti sono montati su un singolo perno collegato a un motore che li fa ruotare a velocità costante: tipicamente 5.400 giri al minuto (RPM) nei modelli per archiviazione e laptop, 7.200 RPM nei modelli desktop e NAS, fino a 10.000 o 15.000 RPM nei drive enterprise più vecchi e ormai obsoleti.

Le informazioni sono memorizzate orientando la magnetizzazione di minuscole regioni del rivestimento: una direzione è "0", l'altra è "1". Le **testine**, montate all'estremità di bracci meccanici che si muovono radialmente sulla superficie dei piatti, come il braccio di un giradischi, leggono e scrivono questi orientamenti. Ogni superficie di ogni piatto ha la sua testina, e tutti i bracci si muovono insieme, all'unisono. Le testine non toccano mai il piatto: "volano" a poche decine di nanometri dalla superficie, sostenute dal cuscino d'aria generato dalla rotazione. Se una testina tocca il piatto in movimento — a causa di un urto, per esempio — si verifica un famigerato *head crash*, che raschia via il rivestimento magnetico e distrugge i dati in quell'area.

I dati sono organizzati in cerchi concentrici chiamati **tracce**, divise in **settori** (storicamente 512 byte, ora 4096 byte fisici nello standard *Advanced Format*). L'insieme delle tracce che si trovano alla stessa distanza dal centro su tutti i piatti forma un **cilindro**.

La lentezza dell'HDD deriva direttamente da questa architettura. Per leggere i dati, il disco deve eseguire due movimenti fisici in sequenza:

1.  **Tempo di ricerca (Seek time)**: il braccio deve spostare la testina sulla traccia corretta. Questo costa tipicamente 8-12 millisecondi su un drive desktop.
2.  **Latenza rotazionale (Rotational latency)**: una volta sulla traccia corretta, si deve attendere che il settore desiderato passi sotto la testina a causa della rotazione del piatto. In media, si attende mezza rotazione. A 7.200 RPM, una rotazione impiega circa 8,3 ms, quindi la latenza media è di circa 4,2 ms; a 5.400 RPM, sale a circa 5,6 ms.

Sommando, un accesso "a freddo" a una posizione casuale del disco costa nell'ordine di **10 millisecondi**. Ed ecco il punto cruciale: quel tempo è **fisico**, dettato dalla meccanica, e non può essere ridotto da alcun miglioramento elettronico. Un moderno HDD da 24 TB legge dati sequenziali a 280 MB/s, una velocità tutt'altro che trascurabile — è la metà di un SSD SATA. Ma se gli chiediamo di leggere quattromila piccoli file sparsi sul disco, come fa un sistema operativo all'avvio, deve pagare 10 ms per ciascuno: il throughput scende a valori nell'ordine di **1-2 MB/s**, ed è per questo che un PC con un hard disk sembra impantanato nella melassa. Non è lento a leggere: è lento a *cercare*.

Le prestazioni di accesso casuale sono misurate in **IOPS** (Input/Output Operations Per Second). Un HDD da 7.200 RPM esegue 75-120 IOPS. Un SSD NVMe di fascia media ne esegue **un milione**. Il rapporto è di circa 1 a 10.000, e nessun numero in questo capitolo è più importante di questo.

### 2.2 Come funziona un SSD

Un **SSD** (Solid State Drive) non ha parti in movimento. È un piccolo computer specializzato, composto da tre elementi fondamentali.

Il primo è la **memoria flash NAND**, i chip che contengono fisicamente i dati. La NAND memorizza le informazioni intrappolando elettroni in una struttura isolata chiamata *floating gate* (o, in implementazioni più moderne, una *charge trap*) all'interno di una cella a transistor. La presenza o assenza di carica — o, più precisamente, il *livello* di carica — determina il valore memorizzato. Poiché l'isolante trattiene gli elettroni anche senza alimentazione, la memoria è non volatile. Questa è una proprietà notevole ma non eterna: un SSD lasciato scollegato per anni perde lentamente la carica e, alla fine, i dati. Per un uso normale, questo è irrilevante; per l'archiviazione a lungo termine in un cassetto, non lo è (torneremo su questo).

Le celle sono organizzate in **pagine** (tipicamente 4-16 KB), e le pagine in **blocchi** (centinaia di pagine, per qualche megabyte). Qui risiede l'asimmetria fondamentale del flash, che spiega quasi tutto ciò che di strano fa un SSD: **i dati vengono letti e scritti per pagine, ma cancellati solo per blocchi interi**. Non si può sovrascrivere una pagina già scritta: bisogna prima cancellare l'intero blocco che la contiene. Di conseguenza, quando il sistema operativo "modifica" un file, l'SSD in realtà scrive la nuova versione in una pagina libera altrove, marca quella vecchia come obsoleta, e prima o poi dovrà fare pulizia consolidando i dati validi e cancellando i blocchi ormai pieni di spazzatura. Questa pulizia si chiama **garbage collection**, e insieme al comando **TRIM** (con cui il sistema operativo comunica all'SSD quali blocchi appartengono a file cancellati e quindi non devono più essere conservati) è ciò che permette all'unità di mantenere prestazioni decenti nel tempo.

Il secondo elemento è il **controller**, il processore dell'SSD. Gestisce tutto: la traduzione tra gli indirizzi logici visti dal sistema operativo e le effettive posizioni fisiche nella NAND (una tabella chiamata *mapping table*, gestita dal firmware **FTL** (Flash Translation Layer)), la garbage collection, la correzione degli errori tramite **ECC** (Error Correcting Code), e il **wear leveling**, ovvero la distribuzione uniforme delle scritture su tutte le celle in modo che nessuna si usuri prima delle altre. Il controller è, non meno della NAND, ciò che determina la qualità di un SSD: due unità con gli stessi chip di memoria ma controller diversi possono comportarsi in modo radicalmente diverso sotto carico.

Il terzo elemento — presente solo nei modelli di fascia medio-alta — è la **cache DRAM**: un chip DRAM (spesso 1 GB per 1 TB di capacità) che il controller usa per tenere la mapping table in memoria veloce. Ne parleremo in dettaglio nella sezione 5, perché è uno dei più importanti discriminanti tra un buon SSD e uno mediocre.

Ne consegue che l'accesso ai dati su un SSD non richiede alcun movimento fisico: il controller consulta la tabella, identifica il chip e la pagina, e li interroga elettricamente. La latenza è nell'ordine delle decine di microsecondi ed è **sostanzialmente identica** sia che il dato sia "all'inizio" o "alla fine" del disco, e sia che si stia leggendo un file grande o mille file piccoli. È la scomparsa del costo di seek, non una maggiore banda passante, la vera ragione per cui gli SSD hanno cambiato l'esperienza d'uso del computer. Da questo deriva anche il fatto che **defragmentare un SSD è inutile** e persino dannoso, perché la frammentazione è un problema solo per chi deve muovere una testina di lettura/scrittura, e le scritture inutili consumano le celle.

### 2.3 Quando l'HDD ha ancora senso

Detto tutto questo, sarebbe un errore concludere che l'hard disk sia obsoleto. L'HDD ha ancora un robusto vantaggio e un vantaggio secondario.

Il robusto vantaggio è il **costo per terabyte**. Un hard disk da 20 TB oggi costa, come ordine di grandezza, meno di un terzo di quanto costerebbe la stessa capacità in SSD *(dato variabile, e reso più favorevole all'HDD dall'aumento in corso dei prezzi delle NAND)*. Per una libreria di 40 TB di video, backup e archivi, la differenza non è un dettaglio: è la differenza tra un progetto fattibile e uno irrealizzabile.

Il vantaggio secondario è la **conservazione dei dati a lungo termine senza alimentazione**. Un disco rigido correttamente conservato mantiene i dati per anni senza essere alimentato; un SSD, che si basa su cariche elettriche intrappolate in un isolante, tende a degradarsi più rapidamente se lasciato scollegato per periodi molto lunghi, specialmente se le celle sono già usurate e se le temperature di conservazione sono elevate. Per un backup "a freddo" da mettere in un cassetto e dimenticare, un HDD è ancora la scelta più sensata — a condizione, ovviamente, che venga collegato e controllato periodicamente, e che non ci si affidi mai a una singola copia.

Gli scenari in cui l'HDD è ancora la risposta giusta sono quindi:

- **Archiviazione a freddo**: collezioni multimediali, archivi fotografici, materiale raramente acceduto e consultato sequenzialmente.
- **Backup**: unità di destinazione per backup periodici, dove la capacità conta più della latenza.
- **NAS domestici e piccoli server** (Network Attached Storage): qui, gli HDD regnano, sia per il costo che perché il collo di bottiglia è spesso la rete stessa. Una rete gigabit trasferisce al massimo circa 110 MB/s: un HDD la satura senza sforzo, e mettere costosi SSD dietro una tale connessione non porta alcun beneficio in termini di larghezza di banda (porta, tuttavia, un beneficio in latenza e IOPS, il che conta se il NAS ospita macchine virtuali o database, non se ospita film).
- **Videosorveglianza**: registrazione continua e sequenziale di flussi video 24/7, per i quali esistono unità specifiche.

Ciò che l'HDD **non** dovrebbe più fare, nel 2026, è ospitare il sistema operativo. Su questo non c'è dibattito né margine di budget: anche il PC più economico deve avviarsi da un SSD.

### 2.4 Tabella riassuntiva HDD / SATA SSD / NVMe SSD

| Caratteristica | HDD 7.200 RPM | SATA SSD | NVMe PCIe 4.0 SSD |
|---|---|---|---|
| Lettura Sequenziale | 150–280 MB/s | ~550 MB/s | 5.000–7.400 MB/s |
| Scrittura Sequenziale | 150–280 MB/s | ~520 MB/s | 4.000–7.000 MB/s |
| Lettura Casuale 4K IOPS | 75–120 | 80.000–100.000 | 600.000–1.500.000 |
| Latenza Tipica | 8–15 ms | ~100 µs | 40–80 µs |
| Parti in Movimento | Sì | No | No |
| Resistenza agli Urti | Bassa | Alta | Alta |
| Livello di Rumore | Udibile | Silenzioso | Silenzioso |
| Consumo Energetico Tipico | 5–9 W | 2–4 W | 4–9 W (picchi) |
| Capacità Massima Consumer | 24–30 TB | 8 TB | 8 TB (raro), 4 TB comune |
| Costo per TB | Più basso | Intermedio | Più alto |
| Uso Ideale | Archivio, backup, NAS | Archiviazione secondaria, upgrade PC vecchio | OS, giochi, lavoro |

---

## 3. SATA vs. NVMe: Interfaccia e Protocollo

Qui è fondamentale una distinzione che molti acquirenti confondono. Un'unità di archiviazione è caratterizzata da **due cose diverse**:

- l'**interfaccia fisica ed elettrica**: i fili su cui viaggiano i bit (SATA, PCIe);
- il **protocollo logico**: il linguaggio con cui il sistema operativo e l'unità comunicano su quei fili (AHCI, NVMe).

Storicamente, le due cose andavano di pari passo — SATA usava AHCI — e così si è diffusa l'abitudine di trattarle come sinonimi. Ma, come vedremo con M.2, oggi non lo sono affatto, ed è proprio lì che risiedono gli errori di acquisto.

### 3.1 SATA III e AHCI

**SATA** (Serial ATA) è l'interfaccia introdotta nei primi anni 2000 per sostituire la vecchia ATA parallela con i suoi cavi a nastro. Nella sua terza revisione, **SATA III** (o SATA 6 Gb/s), offre una larghezza di banda grezza di 6 gigabit al secondo su un singolo canale seriale. Sei gigabit sembrano molti, ma la codifica di linea utilizzata (8b/10b: per ogni 8 bit di dati, ne vengono trasmessi 10, per ragioni di sincronizzazione) occupa il 20% della larghezza di banda, e l'overhead del protocollo si mangia il resto. Il risultato pratico è un tetto di circa **550–560 MB/s** in lettura sequenziale. Questo numero è **il muro**: qualsiasi SSD SATA, oggi come dieci anni fa, si ferma lì. Non esiste un SSD SATA "più veloce" in sequenziale, perché il limite non è nell'unità ma nel canale.

Fisicamente, SATA utilizza **due cavi separati**: un sottile cavo dati a sette contatti che va dall'unità a una porta SATA sulla scheda madre, e un connettore di alimentazione a quindici contatti che proviene dall'alimentatore. Questo è un dettaglio banale ma è la causa principale di panico per i costruttori inesperti che collegano solo il cavo dati e non capiscono perché l'unità non venga rilevata.

Logicamente, SATA utilizza **AHCI** (Advanced Host Controller Interface), un protocollo progettato nell'era degli hard disk. AHCI ha una caratteristica che oggi sembra grottesca: gestisce **una sola coda di comandi**, con una profondità massima di **32 comandi** (una funzionalità nota come NCQ, Native Command Queuing). La ragione storica è ovvia: che senso aveva inviare mille richieste simultanee a un dispositivo che ha una sola testina di lettura/scrittura e può servirne una alla volta? La singola coda serviva a riordinare le richieste per minimizzare i movimenti del braccio, non per sfruttare un parallelismo che non esisteva. Ma la memoria flash è intrinsecamente parallela: un SSD ha decine di chip NAND collegati al controller su più canali indipendenti, e potrebbe servire molte richieste simultaneamente. Costringerlo in AHCI significa strozzarlo. Inoltre, AHCI richiede alla CPU di eseguire compiti di gestione (accessi ai registri, gestione degli interrupt) che introducono una latenza software non trascurabile quando le operazioni al secondo raggiungono le centinaia di migliaia.

### 3.2 NVMe

**NVMe** (Non-Volatile Memory Express) è il protocollo nato per risolvere esattamente questi problemi. Funziona **direttamente sul bus PCIe**, bypassando completamente il controller SATA della scheda madre, e a livello logico, è costruito attorno a tre idee.

La prima è il **parallelismo massivo**: NVMe supporta fino a **65.535 code**, ciascuna profonda fino a **65.535 comandi**. Questi sono numeri iperbolici che nessun uso consumer si avvicina nemmeno lontanamente, ma il punto non è il numero: è che il protocollo non è più un collo di bottiglia. Un SSD può ricevere migliaia di richieste in sospeso e riordinarle internamente per distribuirle sui suoi canali NAND.

La seconda è l'**affinità di core**: ogni core della CPU può avere la propria coda dedicata, senza dover contendere i lock con altri core. Questo elimina un punto di serializzazione che, ad alte frequenze di I/O, era diventato dominante.

La terza è il **percorso software ridotto**: NVMe richiede molti meno accessi ai registri per comando rispetto ad AHCI, supporta gli interrupt MSI-X e riduce drasticamente l'overhead per operazione.

Il risultato combinato è che un SSD NVMe non è solo "più veloce in larghezza di banda": è **più veloce in latenza** e, soprattutto, gestisce molto meglio i carichi con code profonde, cioè quando arrivano molte richieste insieme.

### 3.3 Confronto reale delle prestazioni: dove conta e dove no

Ora arriviamo alla parte che merita la massima onestà, perché è qui che il marketing mente per omissione.

I numeri stampati sulla scatola sono quelli **sequenziali**: "fino a 7.400 MB/s in lettura". Questi si ottengono leggendo un enorme flusso continuo, con code profonde, in condizioni ideali, su un'unità vuota e fredda. Corrispondono all'uso nel mondo reale in pochissimi casi: copiare un file gigantesco da un NVMe veloce a un altro NVMe veloce, lavorare con flussi video non compressi ad altissima risoluzione, caricare enormi set di dati in memoria.

Il carico di lavoro che **domina l'uso quotidiano**, tuttavia, è completamente diverso: **piccole letture e scritture casuali** (le famose "random 4K"), spesso con **poche richieste in sospeso alla volta** (bassa queue depth, QD1–QD4). Aprire un'applicazione, avviare il sistema operativo, caricare le texture di un gioco, compilare un progetto, aprire un progetto in un IDE: queste sono tutte sequenze di migliaia di piccole letture sparse.

Ed ecco la scomoda verità: nelle **random 4K a bassa queue depth**, la differenza tra un buon SSD SATA e un eccellente NVMe PCIe 5.0 non è dieci volte, come i numeri sequenziali potrebbero suggerire. È tipicamente **due o tre volte** – significativa, ma non miracolosa. E la differenza tra un NVMe PCIe 4.0 di fascia media e un PCIe 5.0 di fascia alta, sotto lo stesso carico, è spesso **solo di pochi punti percentuali**, completamente invisibile a occhio nudo.

Per riassumere brutalmente, ma utilmente:

| Transizione | Miglioramento percepito |
|---|---|
| HDD → SSD SATA | **Enorme.** Il PC sembra un computer diverso. |
| SSD SATA → NVMe PCIe 3.0/4.0 | **Modesto ma reale.** Tempi di avvio e caricamento leggermente più veloci, copie di file molto più veloci, molto più margine di manovra sotto carico pesante. |
| NVMe Gen4 → NVMe Gen5 | **Quasi trascurabile per l'utente medio.** Misurabile nei benchmark, invisibile nell'uso, eccetto per specifici carichi di lavoro professionali. |
| SSD QLC senza DRAM → SSD TLC con DRAM | **Evidente quando conta** (scritture prolungate, disco pieno, multitasking pesante). |

Quest'ultimo punto merita enfasi perché è il fulcro del capitolo: **la classe di un SSD conta più della sua generazione PCIe**. Un buon Gen3 con DRAM e NAND TLC supererà, nell'uso reale, un economico Gen4 senza DRAM con NAND QLC quasi ogni volta che il carico di lavoro diventa serio, nonostante quest'ultimo abbia il doppio dei numeri sequenziali sulla scatola.

La tecnologia **DirectStorage** su Windows e i suoi equivalenti su console meritano una menzione a parte. Questa tecnologia consente ai giochi di caricare asset compressi dall'SSD direttamente nella memoria della GPU, riducendo la necessità di passare attraverso la CPU. Sulla carta, è la tecnologia che giustificherebbe finalmente NVMe più veloci nel gaming. In pratica, l'adozione è stata molto più lenta del previsto e i benefici misurati sui pochi titoli che la implementano rimangono modesti su sistemi con CPU decenti. È un motivo per non comprare un HDD o un SATA per i giochi; **non** è un motivo per pagare un Gen5.

---

## 4. Fattore di forma, slot M.2 e compatibilità fisica

### 4.1 Formati fisici

Le unità di archiviazione consumer oggi sono disponibili in tre forme fisiche principali.

Il formato da **3,5 pollici** è per gli hard disk desktop: un "mattone" di alluminio di circa 147 × 102 × 26 mm, che si avvita in un alloggiamento del case, alimentato tramite SATA power e collegato tramite SATA data. È l'unico formato in cui si trovano capacità enormi (12, 16, 20, 24 TB).

Il formato da **2,5 pollici** è per gli SSD SATA e gli hard disk dei portatili: circa 100 × 70 mm, con uno spessore di 7 mm (a volte 9,5 mm sugli HDD). Anche qui, due cavi SATA. È il formato per gli upgrade classici su macchine di qualche anno fa.

Il formato **M.2** è una schedina piatta che si inserisce direttamente in uno slot sulla scheda madre e si fissa con una vite (o, sulle schede recenti, con un fermo a scatto senza attrezzi). Non ha cavi: alimentazione e dati passano attraverso lo slot. È il formato dominante per gli SSD moderni ed è anche la principale fonte di confusione, per il motivo che segue.

### 4.2 La trappola numero uno: M.2 SATA vs. M.2 NVMe

**Lo slot M.2 è un fattore di forma fisico, non un protocollo.** Esistono SSD M.2 che, pur avendo una forma identica, parlano **SATA** e non NVMe: internamente, hanno un controller SATA, sono limitati ai soliti 550 MB/s e usano il protocollo AHCI. Sono apparsi negli anni di transizione e si trovano ancora nei marketplace e nelle offerte "troppo belle per essere vere".

Il risultato è che un acquirente inesperto potrebbe acquistare un "SSD M.2", installarlo e ritrovarsi con le prestazioni di un disco SATA di dieci anni fa, senza capirne il motivo. Peggio: molte schede madri hanno slot M.2 che supportano **solo NVMe** e non SATA. In quel caso, l'unità M.2 SATA semplicemente non viene rilevata, e l'utente passa un pomeriggio a cercare guasti inesistenti.

La regola pratica: **quando si acquista un SSD M.2, bisogna leggere esplicitamente "NVMe" e "PCIe" nella descrizione.** Se non c'è scritto, si presume che non lo sia. E quando si sceglie una scheda madre, si dovrebbe leggere il manuale per vedere cosa supporta ogni slot M.2, perché su molte schede, lo slot primario è solo NVMe, mentre uno secondario accetta entrambi.

### 4.3 M.2 Keys

Il connettore M.2 ha delle **tacche** (*keys*) che impediscono di inserirlo in slot incompatibili. Ce ne sono tre che sono rilevanti per lo storage.

La **B key** ha la tacca in una posizione che, elettricamente, corrisponde a un massimo di **due linee PCIe** più segnali SATA. Di per sé, è ormai rara negli SSD moderni.

La **M key** ha la tacca in un'altra posizione e corrisponde a **quattro linee PCIe**. È quella che usano gli SSD NVMe veloci: se un'unità ha una sola tacca, ed è a destra guardando il connettore, è una M key.

La **B+M key** ha **due tacche** ed è quindi meccanicamente compatibile sia con slot B che M. Le unità B+M sono per lo più M.2 SATA o NVMe limitati a due linee PCIe, perché la doppia tacca implica il minimo comune denominatore elettrico. **Un SSD con due tacche non è mai un NVMe x4 di fascia alta.** Questo è un indizio visivo molto utile: se vedete due tacche nella foto del prodotto, state guardando un'unità lenta o comunque limitata.

Va aggiunto, per completezza, che il fattore di forma M.2 non ospita solo SSD: schede Wi-Fi (solitamente A o E key, più corte), moduli 5G e altro. Questi slot sono fisicamente simili ma elettricamente diversi, e le chiavi servono proprio a evitare di inserire la cosa sbagliata nel posto sbagliato.

### 4.4 Dimensioni: 2280 e altri

La designazione numerica di un M.2 codifica larghezza e lunghezza in millimetri: **2280** significa 22 mm di larghezza e 80 mm di lunghezza. È di gran lunga il formato più comune e, salvo casi particolari, è quello che dovreste acquistare.

| Designazione | Dimensioni | Uso Tipico |
|---|---|---|
| 2230 | 22 × 30 mm | Console portatili (Steam Deck, ROG Ally), ultrabook |
| 2242 | 22 × 42 mm | Laptop compatti, alcuni mini-PC |
| 2260 | 22 × 60 mm | Raro, alcuni laptop |
| **2280** | **22 × 80 mm** | **Standard desktop e laptop: 95% del mercato** |
| 22110 | 22 × 110 mm | Enterprise/workstation, spesso con PLP |

Il formato **22110** (110 mm di lunghezza) si trova quasi esclusivamente in ambito enterprise, dove lo spazio extra serve ad alloggiare i condensatori del **PLP** (Power Loss Protection): una batteria di condensatori che, in caso di improvvisa interruzione di corrente, fornisce l'energia necessaria a scrivere sulla NAND i dati che erano ancora nella cache volatile, prevenendo la corruzione. È una caratteristica preziosa nei server e praticamente assente nei prodotti consumer, dove al massimo si trova un PLP "parziale" che protegge solo i metadati.

Attenzione: gli slot delle schede madri desktop hanno solitamente i fori di montaggio per il 2280 e talvolta per lunghezze inferiori, ma quasi mai per il 22110. Se si vuole riutilizzare un SSD enterprise da 110 mm, occorre verificare che la scheda madre possa fisicamente ospitarlo.

### 4.5 Generazioni PCIe negli SSD

Ogni generazione **PCIe** raddoppia la banda passante per linea. Un SSD NVMe consumer usa quasi sempre **quattro linee** (x4).

| Generazione | Banda per Linea | Banda x4 (Teorica) | Velocità Sequenziale Reale Tipica |
|---|---|---|---|
| PCIe 3.0 | ~985 MB/s | ~3.9 GB/s | 3.000–3.500 MB/s |
| PCIe 4.0 | ~1.97 GB/s | ~7.9 GB/s | 5.000–7.400 MB/s |
| PCIe 5.0 | ~3.94 GB/s | ~15.8 GB/s | 10.000–14.900 MB/s |
| PCIe 6.0 | ~7.88 GB/s | ~31.5 GB/s | Non ancora in ambito consumer *[dati volatili]* |

Il PCIe è **retrocompatibile in entrambe le direzioni**: un SSD Gen4 inserito in uno slot Gen3 funzionerà, limitato alla velocità Gen3; un SSD Gen3 in uno slot Gen5 funzionerà, alla velocità Gen3. Nulla si rompe, nulla si perde se non banda che non sarebbe stata comunque utilizzata. Questo è importante da sapere perché permette di acquistare oggi un drive Gen4 anche su una piattaforma Gen3, in ottica di un futuro cambio di scheda madre.

**Chi ha davvero bisogno del Gen5?** La risposta onesta è: pochissimi utenti consumer. Benefici concreti si vedono in scenari di trasferimento di file molto grandi tra due drive Gen5, nell'editing video ad altissima risoluzione con codec poco compressi, in carichi di lavoro professionali di analisi dati dove si leggono decine di gigabyte in streaming, e in alcuni carichi di lavoro AI (caricamento pesi modello, dataset). Per il gaming, la navigazione, la produttività d'ufficio, la programmazione, la differenza rispetto a un buon Gen4 è nel margine di errore.

Il Gen5 ha anche due svantaggi concreti. Il primo è il **prezzo**, sensibilmente più alto a parità di capacità. Il secondo è il **calore**: i controller Gen5 di prima e seconda generazione consumano molto (si sono visti picchi di 10–12 W contro i 6–8 W di un Gen4), scaldano moltissimo e richiedono **assolutamente** un dissipatore serio, talvolta ingombrante al punto da interferire con la scheda grafica o il dissipatore della CPU. Alcuni modelli Gen5 con controller più efficienti hanno migliorato la situazione, ma il problema rimane.

### 4.6 Dissipatori e Thermal Throttling

Il **thermal throttling** è la riduzione automatica delle prestazioni che un dispositivo applica a sé stesso quando supera una certa temperatura, per prevenire danni. Negli SSD NVMe, questo riguarda principalmente il **controller**, che è il componente che scalda davvero.

C'è una sottigliezza contro-intuitiva: mentre il controller vuole stare fresco, la **NAND performa meglio quando è calda** (le celle scrivono più facilmente ad alte temperature) ma soffre nel *mantenere* i dati quando è calda e spenta. Per questo motivo, i sensori e le soglie sono calibrate principalmente per il controller.

Quando serve un dissipatore?

- Un SSD **Gen3** non ne ha praticamente mai bisogno in un case con un minimo di airflow.
- Un SSD **Gen4** ne beneficia sotto carichi prolungati; molte schede madri di fascia medio-alta ne includono uno integrato, più che sufficiente. Raramente è necessario acquistarne uno aftermarket.
- Un SSD **Gen5** ne ha **sempre** bisogno. Senza adeguata dissipazione, andrà in throttling nel giro di pochi secondi di scrittura sostenuta e finirà per essere più lento di un Gen4 ben raffreddato: uno degli spettacoli più tristi dell'informatica moderna.

Due avvertenze pratiche. Primo: se la scheda madre include un proprio dissipatore M.2, **non c'è bisogno** di comprare un SSD con dissipatore preinstallato — anzi, i due andranno in conflitto e uno dovrà essere rimosso. Secondo: se l'SSD è destinato a una **PlayStation 5**, il dissipatore è obbligatorio ma deve rispettare i limiti di altezza imposti dallo slot (circa 11.25 mm totali), quindi bisogna acquistare modelli esplicitamente dichiarati compatibili, non un dissipatore desktop qualsiasi.

Infine, un dettaglio sull'installazione: i dissipatori aftermarket richiedono la rimozione dell'etichetta dell'SSD? **No.** L'etichetta di molti SSD contiene uno strato di rame o alluminio che aiuta nella dissipazione, e rimuoverla può, in alcuni casi, invalidare la garanzia. Il thermal pad va applicato sopra l'etichetta.

### 4.7 Lane sharing: quando un SSD disabilita le porte SATA

Qui il capitolo storage si intreccia con il capitolo schede madri, e va letto con attenzione perché è la fonte degli errori di assemblaggio più insidiosi.

Le **lane PCIe** sono una risorsa finita. La CPU ne fornisce un certo numero (tipicamente 20–28 utilizzabili sulle moderne piattaforme consumer: 16 per la scheda grafica, 4 o 8 per uno o due slot M.2 diretti, più il link al chipset). Il **chipset** (il "southbridge", il chip di supporto sulla scheda madre) ne fornisce altre, ma tutte queste devono passare attraverso il collegamento tra chipset e CPU, che è esso stesso limitato e condiviso con USB, rete, audio e il resto.

Dato che le lane non bastano mai, i progettisti di schede madri le **multiplexano**: certe risorse sono alternative tra loro. Configurazioni tipiche che si trovano nei manuali sono:

- Popolando il secondo o terzo slot M.2 si **disabilitano due o quattro porte SATA** (perché quelle lane erano condivise).
- Popolando un certo slot M.2 si **riduce lo slot PCIe della scheda grafica da x16 a x8** (impatto sulle prestazioni della GPU: nella maggior parte dei casi trascurabile su Gen4/Gen5, ma non su Gen3).
- Popolando un secondo slot PCIe si **disabilita** un M.2.

La conseguenza pratica: installi il nuovo NVMe nello slot sbagliato, e di colpo il vecchio SSD SATA o l'hard disk di archivio **scompare dal BIOS**. Il PC funziona, ma metà dello storage è sparito. Chi non ha familiarità con il fenomeno passa ore a dare la colpa ai cavi, all'alimentatore o al drive stesso.

La regola: **prima di acquistare, apri il PDF del manuale della scheda madre e cerca la tabella "M.2 and SATA configuration" (o simile)**. C'è sempre, di solito in una pagina che nessuno legge, e chiarisce esattamente quale slot condivide cosa. Un secondo criterio pratico: lo slot M.2 **direttamente connesso alla CPU** (di solito il primo, immediatamente sotto lo slot della scheda video) è il più veloce e tipicamente **non** ruba porte SATA; è lì che va il disco di sistema.

---

## 5. Tecnologia NAND e componenti chiave

### 5.1 SLC, MLC, TLC, QLC: quanti bit per cella

Abbiamo detto che una cella NAND memorizza informazioni come un livello di carica elettrica intrappolata. La domanda cruciale è: **quanti livelli distinti possiamo distinguere in una cella?** Perché più livelli distinguiamo, più bit possiamo memorizzare nella stessa cella fisica, e quindi meno costa ogni gigabyte.

- **SLC** (Single-Level Cell): 2 livelli, **1 bit** per cella.
- **MLC** (Multi-Level Cell): 4 livelli, **2 bit** per cella.
- **TLC** (Triple-Level Cell): 8 livelli, **3 bit** per cella.
- **QLC** (Quad-Level Cell): 16 livelli, **4 bit** per cella.
- **PLC** (Penta-Level Cell): 32 livelli, 5 bit — annunciata da anni, non ancora un prodotto consumer significativo.

Il guadagno di densità è ovvio. Il costo, meno. Immaginate di dover distinguere il livello dell'acqua in un bicchiere: se dovete solo dire "vuoto o pieno" (SLC), potete sbagliare di parecchio ed essere comunque nel giusto. Se dovete distinguere sedici livelli diversi (QLC) nello stesso bicchiere, ogni goccia in più o in meno conta, la misurazione richiede più tempo, e la minima evaporazione (cioè perdita di carica dovuta all'usura o al tempo) vi fa scivolare nel livello sbagliato.

Ci sono tre importanti conseguenze:

1.  **Durata.** Ogni ciclo di scrittura/cancellazione danneggia leggermente l'isolante della cella. Più livelli devono essere distinti, meno degrado può essere tollerato prima che i livelli diventino ambigui. Da qui l'ordine di grandezza dei cicli tollerabili: SLC ~100.000, MLC ~10.000, TLC ~1.000–3.000, QLC ~300–1.000.
2.  **Velocità di scrittura.** La scrittura richiede di portare la carica al livello esatto, con successive verifiche. Più livelli significano una programmazione della cella più lenta. La scrittura QLC "nativa" (cioè non assistita da cache) è drammaticamente lenta: si vedono valori nell'ordine di **80–150 MB/s**, che è peggio di un hard disk.
3.  **Prezzo.** Nella direzione opposta: QLC è la più economica per gigabyte, ed è per questo che esiste.

| Tipo | Bit/cella | Cicli P/E indicativi | Velocità di scrittura nativa | Uso tipico oggi |
|---|---|---|---|---|
| SLC | 1 | ~100.000 | Molto alta | Solo enterprise, cache |
| MLC | 2 | ~10.000 | Alta | Praticamente scomparsa dal consumer |
| TLC | 3 | ~1.000–3.000 | Media | **Lo standard di qualità consumer** |
| QLC | 4 | ~300–1.000 | Bassa | SSD budget ad alta capacità |

La conclusione operativa è chiara: **per il disco di sistema, acquistate TLC.** La QLC ha il suo posto — un grande disco secondario da 4 TB destinato a ospitare una libreria di giochi che viene letta molto e scritta poco è un uso perfettamente legittimo della QLC, e in quello scenario il risparmio è reale. Ma un disco QLC come unità principale di un PC che scrive continuamente (aggiornamenti, cache del browser, file temporanei, compilazioni, editing video) è una scelta che si paga in termini di prestazioni e longevità.

Una nota tecnica va aggiunta per completezza: oggi, tutto il NAND consumer è **NAND 3D** (o V-NAND, nella terminologia Samsung), ovvero le celle non sono più disposte su un unico piano ma impilate in torri verticali di decine o centinaia di strati (si è passati da 32 strati a oltre 200-300 strati nelle generazioni recenti). Questo ha permesso di aumentare la densità senza rimpicciolire ulteriormente le celle, il che paradossalmente ha *migliorato* l'affidabilità rispetto ai vecchi NAND planari con nodi minuscoli. È per questo che i moderni TLC sono più affidabili degli MLC di dieci anni fa.

### 5.2 Cache DRAM contro DRAM-less e HMB

Un controller SSD deve mantenere la **tabella di mappatura**: la tabella che associa ogni indirizzo logico (quello che vede il sistema operativo) alla sua effettiva posizione fisica nel NAND. Questa tabella è grande: come regola generale, circa **1 MB di tabella per ogni GB di capacità**, quindi un SSD da 1 TB ha una tabella di mappatura di circa 1 GB.

Dove viene memorizzata?

Un SSD **con cache DRAM** monta un chip DRAM dedicato (tipicamente 1 GB per 1 TB) e vi memorizza l'intera tabella. Ogni volta che il controller deve tradurre un indirizzo, consulta la DRAM: latenza nell'ordine dei nanosecondi. Immediato.

Un SSD **DRAM-less** non ha quel chip. La tabella vive nel NAND stesso e viene consultata da lì. Consultare il NAND per *trovare* dove sono i dati, prima di leggere i dati, significa fare due accessi al NAND invece di uno: la latenza raddoppia, o peggio.

Per mitigare il problema, è stato introdotto **HMB** (Host Memory Buffer): una funzionalità del protocollo NVMe che consente all'SSD di **prendere in prestito una piccola porzione di RAM di sistema** (tipicamente 32-64 MB, in alcuni casi qualche centinaio) per memorizzare parte della tabella di mappatura. Funziona, ed è per questo che i migliori SSD DRAM-less moderni (WD SN770, per esempio) sono sorprendentemente buoni. Ma ha tre limitazioni: (a) 64 MB non sono 1 GB, quindi solo la parte "calda" della tabella può essere messa in cache, e su un disco usato in modo disorganizzato, i miss aumentano; (b) l'accesso alla RAM di sistema passa comunque attraverso il bus PCIe, quindi è più lento della DRAM onboard; (c) HMB richiede il supporto del sistema operativo — è presente nei moderni Windows e Linux, ma in particolari ambienti o vecchie installazioni, potrebbe non esserlo, e in quel caso, l'SSD torna a prestazioni molto scarse.

Quando la differenza diventa evidente? Non nei benchmark sequenziali, dove un'unità DRAM-less può brillare. È evidente nei **carichi di lavoro con molte richieste casuali su un disco pieno e frammentato**, che è esattamente la condizione di un disco di sistema dopo due anni di utilizzo. È evidente nella latenza sotto carico misto. È evidente nella consistenza delle prestazioni.

**Regola pratica: per il disco di sistema, è preferibile un SSD con cache DRAM. Se il budget non lo consente, scegliere almeno un SSD DRAM-less affidabile con HMB (SN770, non qualche sconosciuto), e in ogni caso con NAND TLC.** La combinazione da evitare come disco primario è **DRAM-less + QLC**: è il fondo del barile, ed è esattamente ciò che molti "affari" da 1 TB offrono a prezzi stracciati.

### 5.3 Cache SLC dinamica e crollo della velocità

C'è un fenomeno che confonde molti utenti: copiano un file da 200 GB su un nuovo SSD, vedono 5.000 MB/s per i primi trenta secondi, poi improvvisamente la velocità **scende** a 900 MB/s, o 200, o 90. L'SSD è difettoso? No. Funziona esattamente come progettato.

Il meccanismo è la **cache SLC dinamica** (*pseudo-SLC cache*, o SLC caching). Il controller prende una porzione della NAND TLC o QLC e la fa operare **in modalità SLC**, ovvero scrive un solo bit per cella invece di tre o quattro. In modalità SLC, la scrittura è molto veloce perché non c'è bisogno di posizionare precisamente la carica fra otto o sedici livelli, ma solo di distinguere fra "carico" e "non carico". Tutte le scritture in arrivo vengono quindi riversate in quest'area veloce. Poi, nei momenti di inattività, il controller **rielabora** i dati in background, riscrivendoli in modalità nativa TLC/QLC e liberando la cache.

Il problema è che la cache SLC è **dinamica**: la sua dimensione dipende dallo spazio libero. Un SSD da 2 TB vuoto può avere una cache SLC di 200–600 GB; lo stesso SSD pieno all'85% potrebbe averne solo 20–30 GB, o quasi nulla. E quando la cache si esaurisce, si scrive **direttamente** sulla NAND nativa, alla sua velocità reale. Per una buona TLC, questo significa 900–1.500 MB/s (ancora buono), ma per una QLC significa **80–150 MB/s**: peggio di un hard disk, per ore, con il sistema apparentemente bloccato.

Da questo, seguono tre lezioni operative:

1. **I test di scrittura sostenuta contano più dei numeri di picco.** Le recensioni serie mostrano un grafico della velocità di scrittura su un trasferimento di centinaia di gigabyte: si vede subito dove il pavimento cede e a quale altezza.
2. **Non riempire l'SSD.** Ne parleremo nella prossima sezione, ma è qui che si vede l'effetto più drammatico.
3. **La QLC dovrebbe essere evitata per carichi di scrittura pesanti e continui.** Un utente che scarica, edita video, sposta archivi di grandi dimensioni, con una QLC quasi piena avrà un'esperienza terribile.

### 5.4 TBW e MTBF: come interpretare la resistenza dichiarata

Due acronimi compaiono nelle specifiche e vengono regolarmente fraintesi.

**TBW** (Terabytes Written) è la quantità totale di dati che il produttore garantisce possano essere scritti sull'unità entro il periodo di garanzia. Un SSD da 1 TB di fascia media dichiara tipicamente 600 TBW; uno di fascia alta 1.200 TBW; una QLC economica potrebbe fermarsi a 200–400 TBW. Il TBW aumenta proporzionalmente con la capacità, perché più celle ci sono, più scritture possono essere distribuite.

La domanda che tutti si pongono è: **è tanto o poco?** Facciamo due conti. Un utente domestico normale scrive tra 10 e 30 GB al giorno sul disco di sistema (aggiornamenti, cache, documenti, download). Prendiamo il caso peggiore, 30 GB al giorno: sono circa 11 TB all'anno. Con 600 TBW dichiarati, l'unità durerebbe **oltre cinquant'anni**. Anche uno sviluppatore che compila continuamente, o un videomaker che scrive 200 GB al giorno, rimane nell'ordine di **otto-dieci anni**.

La conclusione, che va detta chiaramente perché contrasta con un'ansia diffusa, è che **per l'utente consumer, il TBW non è quasi mai il fattore limitante**. Un SSD moderno, nella stragrande maggioranza dei casi, diventa obsoleto o viene sostituito molto prima che le sue celle si usurino. Le vere eccezioni sono i carichi di lavoro enterprise (database con scritture continue, cache di server, nodi di storage) e alcuni carichi di lavoro professionali estremi. Se rientrate in quelle categorie, il TBW è il primo numero da guardare — e probabilmente avete bisogno di un'unità enterprise, non consumer.

Va ricordato che il TBW ha anche un ruolo **contrattuale**: la garanzia scade al raggiungimento del TBW *o* del numero di anni dichiarato, a seconda di quale evento si verifichi per primo. È un limite legale ancor prima che fisico: una volta superato il TBW, l'unità di solito continua a funzionare normalmente.

Un indicatore correlato è il **DWPD** (Drive Writes Per Day), più comunemente usato in ambito enterprise: indica quante volte al giorno l'intera capacità del disco può essere riscritta per l'intera durata della garanzia. Un SSD consumer si aggira intorno a 0,3 DWPD; uno enterprise "write intensive" può raggiungere 3 o 10 DWPD.

**MTBF** (Mean Time Between Failures) è un numero che va interpretato con grande cautela, perché trae in inganno quasi tutti. Un SSD dichiara tipicamente 1.500.000 ore di MTBF. Divise per 8.760 ore in un anno, fanno **171 anni**. Nessuno crede seriamente che un SSD durerà 171 anni, e in effetti non è quello che il numero significa.

MTBF è una **misura statistica di popolazione**, non di longevità individuale. A grandi linee, significa: se metto in funzione 1.500.000 unità per un'ora ciascuna, mi aspetto un guasto. Oppure: se ne metto in funzione 1.000 per un anno, mi aspetto circa 5-6 guasti (8.760.000 ore-unità / 1.500.000 ≈ 5,8). Questo dato è utile a chi gestisce flotte di migliaia di dischi per dimensionare i ricambi. Non **dice assolutamente nulla** su quanto durerà *il vostro* disco, e non tiene conto dell'usura da invecchiamento, perché è misurato durante la fase di vita "utile" del prodotto, escludendo la mortalità infantile e l'usura finale.

Il consiglio: guardate il TBW e gli **anni di garanzia** (5 anni è lo standard per una buona qualità; 3 anni indica un prodotto entry-level). Ignorate l'MTBF come criterio d'acquisto.

Un'ultima cosa, che è la più importante di tutta questa sezione: **nessun numero di durabilità è un sostituto del backup**. Gli SSD, a differenza degli hard disk, tendono a guastarsi **improvvisamente e totalmente**, spesso per un problema al controller o al firmware, senza i rumori e i settori danneggiati progressivi che davano avvisaglie sugli HDD. Un SSD morente spesso diventa un mattone illeggibile, e il recupero dati professionale da NAND è enormemente più difficile e costoso che da un piatto magnetico. La **regola del 3-2-1** (tre copie dei dati, su due supporti diversi, di cui uno off-site) non è una raccomandazione per paranoici: è il minimo indispensabile per chiunque tenga ai propri dati.

---

## 6. Capacità e strategia di configurazione

### 6.1 Quanta capacità

La domanda "di quanto spazio ho bisogno?" ha una risposta che è cambiata rapidamente negli ultimi anni, principalmente per una ragione: **le dimensioni dei giochi sono esplose**. I moderni titoli AAA occupano regolarmente 100-150 GB, e alcuni superano i 200 GB con i pacchetti texture ad alta risoluzione. Un solo di questi giochi consuma un decimo di un drive da 1 TB.

Il minimo assoluto accettabile per un drive di sistema oggi è **500 GB**, e va detto che è già stretto: Windows 11 con i suoi file di paging, ibernazione e aggiornamenti occupa da solo 60-80 GB, e i programmi comuni ne consumano un'altra centinaia. Con 500 GB ci si può arrangiare, se disciplinati e avendo un secondo drive per i dati.

La **capacità di riferimento sensata nel 2026 è 1 TB** per un PC generico, e **2 TB** per un PC da gaming o per lavori creativi. Sotto 1 TB si fanno continui compromessi; sopra 2 TB il costo aumenta, e vale la pena valutare se sia meglio aggiungere un secondo drive (anche più lento ed economico) piuttosto che raddoppiare la capacità di quello primario.

Esiste anche una ragione tecnica poco nota per cui **le capacità maggiori sono spesso più veloci**: un SSD da 2 TB ha più chip NAND di uno da 500 GB, e il controller può quindi distribuire le operazioni su più canali in parallelo. È tipico che il modello da 250 GB o 500 GB di una linea abbia velocità di scrittura dichiarate significativamente inferiori rispetto al modello da 1 o 2 TB della stessa identica linea. Acquistare la dimensione più piccola di una serie significa spesso acquistare la versione peggiore in termini di prestazioni, non solo di spazio.

### 6.2 La percentuale di riempimento

Questo è uno dei fatti più utili e meno conosciuti dell'intero capitolo: **un SSD pieno è un SSD lento**, e non di poco.

Ci sono tre ragioni concorrenti.

La prima è la **cache SLC dinamica** menzionata sopra: meno spazio libero significa meno cache veloce, e l'unità torna alla velocità nativa della NAND prima.

La seconda è la **garbage collection**. Il controller ha bisogno di blocchi liberi per consolidare i dati ed eliminare quelli obsoleti. Se c'è pochissimo spazio libero, deve eseguire acrobazie: leggere un blocco quasi pieno, copiare le pagine valide altrove, cancellare, riscrivere. Il numero di scritture fisiche per ogni scrittura logica — un parametro chiamato **write amplification** — sale alle stelle. Questo significa non solo lentezza ma anche **usura accelerata**: un'unità mantenuta costantemente piena si usura più velocemente.

La terza è l'**over-provisioning**. Ogni SSD riserva una porzione di NAND dalla fabbrica che non è visibile all'utente (questo è il motivo per cui un SSD da "1 TB" mostra 931 GB: in parte è la differenza tra TB decimale e TiB binario, in parte è spazio riservato). Questo margine serve proprio per la garbage collection e la sostituzione dei blocchi difettosi. Lasciare spazio libero equivale ad aumentare l'over-provisioning, e migliora tutto.

**La regola pratica: non superare l'80% di riempimento, e cercare di rimanere sotto il 75% sull'unità di sistema.** Un SSD da 1 TB dovrebbe essere praticamente considerato un SSD utilizzabile da 750 GB. Questo è, incidentalmente, un ottimo argomento per acquistare la dimensione successiva: 2 TB utilizzati all'80% offrono un comodo 1,6 TB, con prestazioni intatte.

### 6.3 Configurazioni sensate

Vediamo le architetture di archiviazione che hanno senso, per profilo d'uso.

**Configurazione minima (PC da ufficio, navigazione, studio).** Un singolo NVMe da 1 TB, TLC, con o senza DRAM. Nessun secondo disco. Semplice, silenzioso, efficiente. Se il budget è davvero minimo, anche un SATA da 1 TB è meglio di un NVMe da 256 GB: **lo spazio conta più della velocità marginale**.

**Configurazione standard (PC da gaming, uso generale).** Un NVMe da 2 TB, TLC con DRAM, come unico disco. Questa è la soluzione più elegante: niente da gestire, niente da spostare, prestazioni eccellenti ovunque. È ciò che consiglierei di default alla maggior parte delle persone.

**Configurazione a due livelli (gaming con ampia libreria, creator).** Un NVMe TLC da 1 TB con DRAM per il sistema operativo e le applicazioni di lavoro, più un secondo NVMe da 2–4 TB (qui QLC diventa accettabile, e a volte è la scelta razionale) per la libreria di giochi e i dati. Il vantaggio è che le scritture di sistema pesanti e continue avvengono sul disco buono, mentre il disco grande serve prevalentemente carichi di lettura, dove QLC si comporta bene.

**Configurazione completa (professionale, archivio locale).** NVMe da 1–2 TB per sistema operativo e applicazioni, NVMe o SATA da 2–4 TB per progetti attivi, HDD da 8–20 TB per archivio e backup locali. Questa è la classica configurazione a tre livelli — hot, warm, cold — e ha ancora perfettamente senso. Deve essere accompagnata da un **backup off-machine**: un'unità esterna o un NAS, perché un HDD interno non protegge contro furto, incendio, sbalzi di tensione o ransomware.

Una nota sul **RAID** (Redundant Array of Independent Disks) in ambiente domestico: il RAID 0 (striping, due dischi che lavorano in parallelo) è quasi sempre una cattiva idea sui moderni SSD — raddoppia il rischio di perdita totale dei dati per una banda sequenziale che nessuno usa — e il RAID 1 (mirroring) è un meccanismo di alta disponibilità, **non un backup**: se si cancella accidentalmente un file, o se il ransomware lo cripta, viene cancellato o criptato su entrambi i dischi istantaneamente. Il RAID protegge dal guasto hardware di un disco, e nient'altro.

---

## 7. Marche e Modelli di Riferimento

### 7.1 Chi produce NAND e chi assembla

Questa distinzione è fondamentale per orientarsi in un mercato altrimenti caotico, ed è la stessa logica già incontrata nel capitolo sulla RAM.

Esistono **pochissimi produttori di memorie NAND** al mondo: Samsung, SK Hynix (che ha assorbito la divisione NAND di Intel per creare **Solidigm**), Kioxia (ex divisione memorie di Toshiba), Western Digital/SanDisk (storicamente in joint venture produttiva con Kioxia), Micron (marchio consumer Crucial) e YMTC in Cina. Tutti gli altri marchi che si vedono sugli scaffali — Kingston, Corsair, Sabrent, Teamgroup, Adata, Silicon Power, Lexar, Patriot, PNY e decine di altri — **non producono NAND**. Acquistano chip di memoria e controller sul mercato, li assemblano su un PCB e ci mettono sopra un firmware e un'etichetta.

Questo non è un difetto in sé. Alcuni assemblatori realizzano prodotti eccellenti (i Kingston KC3000 e i Sabrent Rocket sono stati SSD eccellenti, costruiti su controller Phison di fascia alta e buone NAND). Ma ha una conseguenza precisa e pericolosa: **l'assemblatore può cambiare i componenti interni senza cambiare il nome del prodotto.**

È il fenomeno noto come **lotteria dei componenti** o *revisione silenziosa*: un SSD viene lanciato con NAND TLC e controller X, ottiene recensioni eccellenti, e sei mesi dopo lo stesso identico numero di modello viene prodotto con NAND QLC e un controller diverso, con prestazioni sostenute molto peggiori. L'acquirente legge le entusiastiche recensioni di lancio e riceve a casa un prodotto diverso. Il caso più citato in ambito consumer è quello dei **Kingston NV1/NV2**, dichiaratamente soggetti a variazione di componenti, con unità trovate sul mercato in configurazioni radicalmente diverse; ma il fenomeno ha colpito, in misura variabile, molti marchi, incluso il famoso caso della revisione silenziosa del Samsung 970 EVO Plus e le variazioni sul WD Blue SN550.

La difesa è duplice. Primo: **preferire, a parità di prezzo, i marchi che producono le proprie NAND** (Samsung, WD/SanDisk, Solidigm, Micron/Crucial finché disponibili, Kioxia), perché la filiera è verticale e le revisioni silenziose sono più rare e documentate. Secondo: **diffidare dei modelli le cui specifiche non dichiarano il tipo di NAND**. Se una scheda tecnica non dice se è TLC o QLC, se ha DRAM o meno, è quasi sempre perché la risposta non piacerebbe, o perché il produttore vuole tenersi aperte le opzioni.

### 7.2 Panoramica dei Marchi di SSD

**Samsung.** È il riferimento storico, per una semplice ragione: produce NAND, controller e DRAM in-house. Le serie **970 EVO/Pro** (PCIe 3.0) sono state per anni il gold standard; la serie **980** ha introdotto una fastidiosa ambiguità (il semplice "980" è un Gen3 **DRAM-less**, mentre il "980 Pro" è un Gen4 con DRAM: nomi quasi identici, prodotti in categorie diverse — un caso da manuale di nomenclatura fuorviante). La serie **990 PRO** è oggi un riferimento Gen4, veloce, efficiente, con ottima consistenza sotto carico; la variante **990 EVO Plus** è più economica e usa una configurazione ibrida di linee PCIe. Samsung tende a costare più della media: si paga il brand, ma anche una reale consistenza. Nota storica: le prime unità 990 PRO e 980 PRO hanno avuto problemi di degrado della salute risolti via aggiornamento firmware — un promemoria che **aggiornare il firmware degli SSD** (con gli strumenti ufficiali: Samsung Magician, WD Dashboard, Crucial Storage Executive) è una buona abitudine, non un'operazione da power user.

**Western Digital / SanDisk.** L'altro grande nome verticale. Il **WD Black SN850X** è probabilmente l'SSD Gen4 con DRAM più consigliato: prestazioni eccellenti, ottima gestione termica, prezzo competitivo, e in molte configurazioni è la scelta razionale per un drive di sistema. Il **WD Black SN770** è il campione della categoria DRAM-less: usa l'HMB in modo intelligente, ha NAND TLC, ed è sorprendentemente vicino agli SSD con DRAM nell'uso reale, a un prezzo inferiore. È l'eccezione che dimostra che "DRAM-less" non significa automaticamente "scarsa qualità", a patto che il resto sia fatto bene. L'**SN850X** è disponibile anche in versione con dissipatore e in versione compatibile con PS5. La linea **WD Blue** (SN580 e simili) è l'onesto entry-level. Il gruppo ha recentemente separato le sue attività flash sotto il marchio **SanDisk**, quindi ci si può aspettare di vedere gli stessi prodotti sotto nomi in transizione *[variable data]*.

**Crucial (Micron).** Storicamente il campione del rapporto prezzo-prestazioni. I **P3** e **P3 Plus** sono drive QLC economici (buoni come drive secondari, sconsigliati come primari); il **P5 Plus** è stato un eccellente Gen4 con DRAM; il **T500** è un ottimo e molto competitivo Gen4 con DRAM; i **T700** e **T705** sono stati tra i primi drive Gen5 di fascia alta, molto veloci e molto caldi, richiedendo dissipatori sostanziosi. **[volatile data]** Come accennato all'inizio, Micron ha annunciato la sua uscita dal mercato consumer con il marchio Crucial: questi prodotti rimangono tecnicamente validi ma non dovrebbero più essere considerati una linea con un futuro, e la disponibilità deve essere verificata caso per caso. Chi acquista oggi scorte residue Crucial fa un affare tecnico ma deve tenere conto di un supporto e una sostituzione meno garantiti nel tempo.

**Kingston.** Un assemblatore, non un produttore di NAND. Il **KC3000** è stato un eccellente Gen4 (controller Phison E18, NAND TLC, DRAM) e il **Fury Renegade** è la sua controparte gaming. All'estremo opposto, la serie **NV2** (e in precedenza l'NV1) è l'esempio da manuale della lotteria dei componenti: stesso nome, componenti variabili, prestazioni imprevedibili. Non è un prodotto "cattivo" in termini assoluti, ma è un prodotto in cui **non si può sapere cosa si sta comprando**, il che nel 2026 è una ragione sufficiente per starne alla larga se esistono alternative allo stesso prezzo.

**Solidigm.** È l'ex divisione NAND di Intel, ora sotto SK Hynix. Produce NAND proprie. È diventata particolarmente interessante nella nicchia degli **SSD a capacità molto elevata e basso costo per TB**: la serie **P44 Pro** è un ottimo Gen4 con DRAM (tecnicamente un parente stretto dell'SK Hynix Platinum P41), mentre le serie QLC ad alta capacità (P41 Plus, e le linee da 4 TB in su) sono tra le più sensate quando serve tanto spazio a basso prezzo per carichi prevalentemente in lettura.

**Seagate.** Nota principalmente per gli hard disk, ha una buona linea di SSD **FireCuda** (530, 540), basati su controller Phison e NAND TLC, con TBW dichiarati molto elevati e garanzia lunga. Sono prodotti validi, spesso posizionati a prezzi premium.

**Sabrent.** Assemblatore americano che si è costruito un'ottima reputazione con la sua linea **Rocket** (Rocket 4 Plus, Rocket 5), basata su controller Phison di fascia alta. Spesso è il primo a commercializzare le nuove generazioni. Buoni prodotti, ma vale sempre la regola: verificare la revisione e i componenti effettivi.

**SK Hynix.** Meno diffusa in Europa ma tecnicamente eccellente: il **Platinum P41** e il successivo **P51** sono drive con NAND e controller proprietari, tra i più efficienti sul mercato (bassi consumi, poco calore, ottime prestazioni). Se trovati a buon prezzo, è una scelta molto solida.

### 7.3 Tabella Orientativa SSD

| Livello | Modelli di Riferimento | Caratteristiche | Uso Consigliato |
|---|---|---|---|
| Entry / Secondo Drive | Crucial P3 Plus, WD Blue SN580, Solidigm P41 Plus | Gen3/Gen4, DRAM-less, spesso QLC | Archiviazione secondaria, librerie di giochi |
| Entry di Qualità | **WD Black SN770**, Kingston NV3 (con riserve) | Gen4, DRAM-less HMB, TLC | Drive di sistema con budget limitato |
| Fascia Media (il "sweet spot") | **WD Black SN850X**, Samsung 990 EVO Plus, Crucial T500, SK Hynix P41/Solidigm P44 Pro | Gen4, DRAM, TLC | **Drive di sistema: la scelta consigliata** |
| Fascia Alta Gen4 | Samsung 990 PRO, Seagate FireCuda 530/540 | Max Gen4, DRAM, TLC | Workstation, per chi vuole il massimo senza il Gen5 |
| Gen5 | Crucial T705, Samsung 9100 PRO, Sabrent Rocket 5, Corsair MP700 Pro | Gen5, DRAM, TLC, dissipatore obbligatorio | Solo per carichi di trasferimento professionali massivi |

*I modelli specifici e la loro disponibilità cambiano rapidamente; questa tabella va usata per capire le categorie, non come lista della spesa definitiva.* **[dati volatili]**

### 7.4 Hard Disk: Seagate, WD, Toshiba e la Trappola SMR

Nel mercato degli hard disk, sono rimasti **solo tre produttori** al mondo: **Seagate**, **Western Digital** (con i marchi WD e HGST) e **Toshiba**. Tutto ciò che comprate, sotto qualsiasi etichetta, proviene da loro.

WD usa storicamente un codice colore per le sue linee, utile da conoscere:

| Linea WD | Colore | Scopo |
|---|---|---|
| WD Blue | Blu | Desktop generico, uso leggero |
| WD Black | Nero | Desktop performance, 7.200 RPM, cache grande |
| WD Red / Red Plus / Red Pro | Rosso | NAS (attenzione: il "Red" liscio è SMR!) |
| WD Purple | Viola | Videosorveglianza, scrittura continua |
| WD Gold | Oro | Enterprise, datacenter |

Seagate ha una struttura simile: **BarraCuda** (desktop), **IronWolf** e **IronWolf Pro** (NAS), **SkyHawk** (sorveglianza), **Exos** (enterprise). Toshiba usa designazioni alfanumeriche (serie **N300** per NAS, **X300** per desktop performance, **MG** per enterprise).

E ora, la trappola più importante di questa sezione.

**CMR contro SMR.** Questi sono due modi di scrivere le tracce magnetiche sul piatto.

**CMR** (Conventional Magnetic Recording — a volte chiamato PMR) scrive le tracce affiancate e non sovrapposte. Ogni traccia può essere riscritta indipendentemente dalle altre. Questo è il comportamento "normale" atteso da un drive.

**SMR** (Shingled Magnetic Recording) sfrutta un'asimmetria fisica: la testina di **scrittura** è più larga di quella di **lettura**. Se le tracce vengono scritte parzialmente sovrapposte, come le tegole di un tetto, se ne possono impacchettare di più sullo stesso piatto, guadagnando il 10–25% di capacità a parità di hardware. La lettura funziona perfettamente, perché la testina di lettura più stretta può leggere la porzione scoperta della traccia.

Il problema è la **riscrittura**. Poiché le tracce si sovrappongono, una singola traccia non può essere riscritta senza distruggere quelle adiacenti: l'intera "banda" di tracce shingled deve essere letta, modificata in memoria, e poi riscritta per intero. Il drive maschera questo fenomeno con una zona di cache CMR, ma quando la cache si esaurisce — cioè, sotto scrittura casuale prolungata — le prestazioni **crollano**, e non di poco: scendono a **10–20 MB/s**, con latenze di secondi. Il drive sembra rotto.

Dove diventa un disastro? In un **NAS con RAID**. Quando un drive guasto viene sostituito, l'array deve **ricostruire** i dati sul nuovo drive: questo è un carico di scrittura pesante e continuo, che dura ore o giorni. Un drive SMR, in queste condizioni, rallenta così tanto che il controller RAID può considerarlo non responsivo ed **espellerlo dall'array**, rovinando la ricostruzione. In un array già degradato, con un solo drive di ridondanza rimanente, questo può significare la **perdita totale dei dati**.

Ciò che rende la situazione ancora più grave è che, nel 2020, WD, Seagate e Toshiba sono stati scoperti a vendere drive SMR **senza dichiararlo**, includendoli in linee esplicitamente destinate ai NAS (in particolare alcuni WD Red da 2–6 TB). Questo ha portato a class action, scuse pubbliche e una chiarificazione delle linee di prodotto: da allora, WD ha creato "Red **Plus**" per indicare i CMR, lasciando il semplice "Red" per gli SMR. Ma la lezione rimane: **le etichette non bastano; bisogna verificare il modello specifico**.

**Regola operativa: per un NAS, per un RAID, o per qualsiasi uso che preveda scritture significative, si deve acquistare solo CMR.** Prima di acquistare un hard drive, cercate il numero di modello esatto (es. WD60EFPX, ST8000VN004) insieme alla parola "CMR" o "SMR": i tre produttori pubblicano ora liste ufficiali, e le comunità di appassionati ne mantengono di aggiornate. L'SMR ha senso solo per l'archiviazione write-once, read-many, tipicamente in drive USB esterni destinati al backup di grandi archivi che vengono raramente riscritti.

Un'ultima nota: alcuni hard drive enterprise ad altissima capacità sono **riempiti di elio** invece che di aria. L'elio, essendo meno denso, riduce la turbolenza attorno ai piatti, permettendo di impilare più piatti (9, 10, 11) e riducendo il consumo energetico e le temperature. Si tratta di drive eccellenti, sigillati ermeticamente, ma costosi e spesso rumorosi (i modelli enterprise a 7.200 RPM non sono progettati per stare sotto la scrivania del salotto).

### 7.5 Il processo di selezione, in ordine

Riassumendo il metodo per scegliere lo storage in una sequenza logica, si procede come segue:

1.  **Utilizzo.** Cosa dovrebbe fare questa unità? Sistema operativo? Libreria di giochi? Archivio? Backup? Tutto il resto ne consegue.
2.  **Interfaccia.** Sistema operativo e applicazioni → NVMe, sempre. Archiviazione dati secondaria → NVMe se ci sono slot liberi, SATA altrimenti. Archivio di massa → HDD.
3.  **Capacità.** Stima l'occupazione effettiva, aggiungi un margine per il futuro e **dividi per 0.8** per aderire alla regola del riempimento. Hai bisogno di 1 TB di roba? Compra 1.5–2 TB.
4.  **Tipo di NAND e DRAM.** Unità di sistema → **TLC**, preferibilmente **con DRAM**. Unità secondaria prevalentemente per la lettura → QLC accettabile.
5.  **TBW e Garanzia.** Verifica che il TBW sia coerente con il carico previsto (per uso consumer, lo è quasi sempre) e preferisci garanzie di 5 anni.
6.  **Marca.** A parità di specifiche, preferisci i produttori che producono le proprie NAND e non fanno revisioni silenziose.
7.  **Prezzo.** Solo a questo punto guarda il prezzo e confronta il **costo per gigabyte** tra i candidati rimanenti. Il prezzo è l'ultimo filtro, non il primo, perché un'unità economica che fallisce ai punti 4 e 6 non è un affare: è un problema rimandato.

---

## 8. Errori comuni dell'acquirente

Qui elenchiamo esplicitamente gli errori più comuni riscontrati — molti già anticipati, ma vale la pena elencarli perché ognuno costa denaro, tempo o dati.

**Acquistare un SSD QLC DRAM-less come unità primaria.** Questo è l'errore numero uno, ed è guidato dal marketing: la scatola dice "PCIe 4.0, fino a 5.000 MB/s", il prezzo è ottimo, sembra un affare. Poi scopri che quei 5.000 MB/s durano solo finché la cache SLC regge, e che quando l'unità si riempie — il che avviene dopo un anno di uso normale — le velocità di scrittura scendono sotto i 200 MB/s e il sistema diventa lento sotto carico. **L'unità di sistema è l'ultimo posto dove risparmiare 20 euro.**

**Montare l'NVMe nello slot sbagliato e disabilitare le porte SATA.** Già ampiamente discusso: il manuale della scheda madre ha la tabella di condivisione delle linee, e dovrebbe essere letto **prima** di acquistare, non dopo che le unità sono scomparse dal BIOS.

**Pagare per un SSD Gen5 senza averne bisogno.** Un Gen5 costa significativamente di più di un Gen4 della stessa capacità, scalda molto di più, richiede un dissipatore ingombrante, e nell'uso consumer — incluso il gaming — offre un miglioramento percepito che va da "nessuno" a "impercettibile". Quella differenza di prezzo, spesa invece in **capacità** (2 TB Gen4 invece di 1 TB Gen5) produce un beneficio reale e quotidiano. Il Gen5 ha senso per chi sposta abitualmente decine o centinaia di gigabyte tra unità veloci, e per pochi altri.

**Acquistare un M.2 SATA credendo di acquistare un NVMe.** Stesso slot, protocollo diverso, un decimo delle prestazioni. Controlla che "NVMe" e "PCIe" appaiano nelle specifiche tecniche, e diffida delle unità con due tacche.

**Prendere un HDD SMR per un NAS.** Rischio concreto di perdita totale dei dati durante una ricostruzione RAID. Verifica il modello esatto rispetto agli elenchi ufficiali CMR/SMR.

**Acquistare la dimensione più piccola di una serie.** Il modello da 250 o 500 GB di una linea è spesso più lento in scrittura rispetto al modello da 1 o 2 TB della stessa linea, perché ha meno chip da parallelizzare. Paghi poco e ottieni poco, in due sensi.

**Riempire l'unità al 95% e poi lamentarsi che il PC è lento.** Il rimedio è gratuito: libera spazio, o acquista più capacità.

**Deframmentare un SSD.** Inutile e dannoso. I Windows moderni lo sanno e, quando "ottimizzano" un SSD, in realtà inviano comandi TRIM — che è una cosa completamente diversa e legittima. Non installare utility di deframmentazione di terze parti su un SSD.

**Credere che RAID 1 sia un backup.** Non lo è. Protegge da un singolo guasto del disco. Non protegge da cancellazioni accidentali, ransomware, sbalzi di tensione, furti o incendi.

**Ignorare gli aggiornamenti firmware.** Diversi seri problemi di affidabilità su SSD di marca sono stati risolti tramite aggiornamenti firmware. Vale la pena installare l'utility ufficiale del produttore e controllare di tanto in tanto.

**Non controllare mai lo stato S.M.A.R.T.** **S.M.A.R.T.** (Self-Monitoring, Analysis and Reporting Technology) è il sistema di autodiagnosi integrato in tutti i dischi: espone contatori come le ore di accensione, i terabyte scritti, i settori riallocati e la percentuale di vita rimanente. Strumenti gratuiti come CrystalDiskInfo (Windows) o `smartctl` (Linux) consentono di leggerli in pochi secondi. Un rapido sguardo di tanto in tanto, e specialmente **prima di acquistare usato**, evita spiacevoli sorprese: un SSD usato con l'80% della sua vita consumata non è un affare a nessun prezzo.

**Fidarsi di un singolo supporto di archiviazione.** Vale la pena ripeterlo un'ultima volta: gli SSD muoiono senza preavviso e il recupero è costoso, se non impossibile. Qualsiasi configurazione di archiviazione senza una strategia di backup è, semplicemente, incompleta.

---

## 9. Riepilogo Operativo — Lista di Controllo per la Scelta dello Storage

**Passo 1 — Definire il ruolo di ogni unità.**
- [ ] Sistema operativo e applicazioni → **NVMe SSD**, senza eccezioni.
- [ ] Giochi e progetti attivi → NVMe (o SATA se non ci sono slot disponibili).
- [ ] Archivio e backup locale → **HDD CMR ad alta capacità**.
- [ ] Backup remoto/esterno → pianificato? (regola 3-2-1)

**Passo 2 — Verificare la compatibilità della scheda madre.**
- [ ] Quanti slot M.2 ci sono e quale generazione PCIe supportano?
- [ ] Aprire il manuale, leggere la tabella di **lane sharing**: quale slot M.2 disabilita quali porte SATA o riduce lo slot GPU?
- [ ] Lo slot scelto per il disco di sistema è **direttamente collegato alla CPU**?
- [ ] La scheda madre include già un **dissipatore M.2**? (Se sì, non acquistare un SSD con dissipatore integrato.)
- [ ] Il formato è **2280**? (Controllare i fori di montaggio se si usano formati diversi.)

**Passo 3 — Scegliere le specifiche dell'SSD.**
- [ ] È **NVMe** e non M.2 SATA? (Cercare "NVMe" e "PCIe" nelle specifiche tecniche; diffidare dei dischi con due tacche.)
- [ ] La NAND è **TLC**? (QLC consentito solo per dischi secondari con operazioni prevalentemente di lettura.)
- [ ] Ha **cache DRAM**? (Se senza DRAM, è un modello affidabile con HMB come l'SN770?)
- [ ] Il **TBW** è coerente con il carico di lavoro previsto? (Per uso consumer, quasi sempre sì.)
- [ ] La **garanzia** è di 5 anni?
- [ ] Ci sono test di **scrittura sostenuta** nelle recensioni? Dove cala la velocità?
- [ ] La generazione PCIe è adeguata: **Gen4 è il punto ottimale**; Gen5 solo se il carico di lavoro lo giustifica davvero.

**Passo 4 — Dimensionare la capacità.**
- [ ] Stimare l'utilizzo effettivo previsto.
- [ ] Aggiungere un margine e dividere per **0.8** (non superare l'80% di riempimento).
- [ ] Minimo 1 TB per uso generale, **2 TB consigliati** per giochi o lavoro creativo.
- [ ] Verificare che la capacità scelta non sia una versione depotenziata della serie.

**Fase 5 — Scegliere la marca.**
- [ ] A parità di specifiche, preferire un produttore di NAND verticali (Samsung, WD/SanDisk, SK Hynix/Solidigm, Kioxia, Micron).
- [ ] Verificare che il modello non sia noto per **revisioni silenziose** dei componenti.
- [ ] Controllare che le specifiche tecniche dichiarino esplicitamente il tipo di NAND e la presenza di DRAM.

**Fase 6 — Se si acquista un HDD.**
- [ ] Verificare che sia **CMR** (obbligatorio per NAS e RAID), controllando il numero di modello esatto.
- [ ] Scegliere la linea giusta per l'utilizzo (NAS / sorveglianza / desktop / enterprise).
- [ ] Considerare rumore e vibrazioni se il PC si trova in un ambiente silenzioso.

**Fase 7 — Dopo l'installazione.**
- [ ] Tutti i drive sono visibili nel BIOS/UEFI? (Se no → sospettare lane sharing.)
- [ ] Il drive NVMe è riconosciuto alla corretta generazione PCIe? (Verificabile con CrystalDiskInfo o utility del produttore.)
- [ ] Il **TRIM** è attivo? (Su Windows e Linux moderni, lo è di default.)
- [ ] Firmware aggiornato con l'utility ufficiale?
- [ ] Temperature sotto carico controllate? (Sopra i 70 °C sostenuti sul controller, serve migliore dissipazione.)
- [ ] **Backup configurato.** Nessuna configurazione di storage è completa senza questo punto.

**Consigli rapidi per fascia di budget** *(prezzi indicativi e attualmente molto instabili — [dati volatili])*

| Fascia | Configurazione consigliata | Logica |
|---|---|---|
| **Entry** | 1× 1TB Gen4 TLC NVMe, anche DRAM-less di qualità (es. WD Black SN770) | Un singolo, buon drive. Meglio un discreto 1TB che un "premium" 500GB o un pessimo 2TB. |
| **Mid** | 1× 2TB Gen4 TLC NVMe con DRAM (es. WD Black SN850X, Samsung 990 EVO Plus, Crucial T500) | **La scelta giusta per la maggior parte delle persone.** Semplice, veloce, capiente. |
| **High-end** | 1× 2TB Gen4/Gen5 NVMe con DRAM per OS + 1× 4TB NVMe (anche QLC) per librerie + 1× 8–20TB CMR HDD per archivio | Tre livelli: hot, warm, cold. Gen5 solo se il carico di lavoro lo giustifica. |

---

**Nota finale.** Se dovessi riassumere l'intero capitolo in una frase, sarebbe questa: *la qualità di un SSD si misura da TLC, DRAM e prestazioni di scrittura sostenute, non dal numerone stampato sulla scatola.* Chi interiorizza questo principio comprerà bene anche senza sapere altro; chi lo ignora continuerà a pagare per banda sequenziale che non userà mai, e si chiederà perché il suo nuovo computer sembra impantanato dopo un anno.

---

[← Precedente](04-gpu-consumer.html) · [Tutti i capitoli](./) · [Successivo →](06-alimentatore-psu.html)
