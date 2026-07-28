---
title: "Capitolo 1 - La CPU nei PC Desktop Consumer"
parent: "Edizione italiana"
nav_order: 1
---

<details open markdown="block">
  <summary>Indice del capitolo</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

> **Nota sugli aggiornamenti dei dati.** Questo capitolo è aggiornato a **Luglio 2026**. I concetti architetturali e i protocolli (PCIe, socket, cache, IPC) sono stabili nel tempo; ciò che cambia rapidamente sono i **prezzi**, le **ultime generazioni disponibili** e la **disponibilità**. Nel 2026, in particolare, si registra una condizione di mercato anomala: una **grave carenza di memoria DDR5**, con i prezzi delle RAM aumentati di 5-8 volte rispetto alla normalità. Questo distorce pesantemente il "costo totale della piattaforma" di cui parleremo, al punto che spesso *un kit di RAM veloci costa più della CPU stessa*. Tutti i prezzi indicati vanno quindi presi come ordine di grandezza indicativo e verificati al momento dell'acquisto.

---

## 1. Cosa fa la CPU

### 1.1 Definizione e Ruolo nel Sistema

L'acronimo **CPU** sta per *Central Processing Unit*. È il componente che esegue le istruzioni dei programmi: è, letteralmente, il "cervello" che coordina e controlla il resto del computer. Tutto ciò che il PC fa — aprire un file, calcolare una formula, muovere il cursore, decidere cosa inviare alla scheda video — passa in qualche forma attraverso la CPU, che ne orchestra il flusso.

Per capire *come* funziona una CPU, bisogna partire dal ciclo fondamentale che essa ripete miliardi di volte al secondo, chiamato **fetch-decode-execute**. Nella fase di *fetch*, la CPU recupera dalla memoria la prossima istruzione da eseguire; nella fase di *decode*, la interpreta, capendo quale operazione sia e quali dati coinvolga; nella fase di *execute*, la esegue effettivamente, ad esempio sommando due numeri o scrivendo un valore in memoria. Una volta completata l'esecuzione, il risultato viene scritto (fase di *write-back*) e il ciclo ricomincia con l'istruzione successiva. Questo processo, banale se descritto a parole, è ciò che accade miliardi di volte al secondo all'interno di ogni core, ed è il motivo per cui la frequenza ("GHz") ha un impatto così diretto sulle prestazioni: più cicli al secondo, più istruzioni elaborate.

La CPU non lavora da sola: comunica costantemente con gli altri componenti del sistema, e capire questa comunicazione è fondamentale per fare acquisti sensati. Con la **RAM** (*Random Access Memory*, la memoria di lavoro volatile), la CPU scambia i dati che sta usando in quel momento: la RAM è veloce ma si svuota allo spegnimento, e la CPU ne dipende in modo critico perché è lì che tiene i dati "caldi" del programma in esecuzione. Con lo **storage** (l'unità di archiviazione, tipicamente oggi un SSD NVMe), la CPU interagisce per leggere e scrivere dati permanenti, ma lo storage è enormemente più lento della RAM, quindi i dati vengono caricati dallo storage alla RAM prima di essere elaborati. Con la **GPU** (*Graphics Processing Unit*, l'unità di elaborazione grafica, ovvero la scheda video), la CPU coopera nei carichi grafici: la CPU prepara la scena, calcola la logica di gioco, la fisica, l'intelligenza artificiale dei nemici e le istruzioni di disegno, poi "consegna" questo lavoro alla GPU che lo trasforma in pixel sullo schermo. In un videogioco, quindi, CPU e GPU si passano continuamente il testimone, e se una delle due è troppo lenta rispetto all'altra, si verifica il fenomeno del *bottleneck*, di cui parleremo nella sezione dedicata alla scelta.

### 1.2 CPU vs GPU: Due Paradigmi di Calcolo (Seriale vs. Parallelo)

Una delle confusioni più comuni per chi si avvicina all'hardware è pensare che CPU e GPU siano "la stessa cosa, una più potente dell'altra". In realtà, sono due filosofie di calcolo profondamente diverse, e questa differenza spiega perché siano necessarie entrambe.

La CPU è ottimizzata per il **calcolo seriale complesso**: ha pochi core molto sofisticati (tipicamente da 4 a 24 nei desktop consumer), ciascuno capace di eseguire un'ampia varietà di operazioni e di prendere decisioni complesse rapidamente, gestendo salti condizionali, eventi inattesi e dipendenze tra istruzioni. È come avere pochi ingegneri brillantissimi, ciascuno capace di risolvere problemi difficili e diversi, ma uno alla volta con grande cura.

La GPU, d'altra parte, è ottimizzata per il **calcolo massivo parallelo**: ha migliaia di unità di calcolo molto più semplici, ciascuna individualmente non molto intelligente, ma tutte insieme capaci di eseguire la stessa operazione su enormi quantità di dati simultaneamente. È come avere un esercito di migliaia di operai, ciascuno capace solo di svolgere un compito semplice, ma che insieme possono dipingere un'enorme parete in pochi secondi perché lavorano tutti in parallelo. Questo è esattamente ciò che serve per la grafica (calcolare il colore di milioni di pixel simultaneamente) e, non a caso, anche per l'addestramento di reti neurali di intelligenza artificiale, che è calcolo parallelo su matrici.

La conseguenza pratica è chiara: i compiti *sequenziali* e ricchi di decisioni (logica di gioco, compilazione di codice, apertura di un foglio di calcolo) girano meglio sulla CPU; i compiti *massivamente paralleli e ripetitivi* (rendering 3D, calcolo grafico, deep learning) girano molto meglio sulla GPU. Nessuno dei due sostituisce l'altro, ed è per questo che un PC bilanciato ha bisogno di entrambi adeguatamente dimensionati.

---

## 2. Architettura Interna della CPU

### 2.1 Core Fisici: Cosa Sono e Perché Contano

Un **core** è un'unità di elaborazione completa e indipendente all'interno della CPU: è, in pratica, un piccolo processore autonomo, capace di eseguire il proprio ciclo fetch-decode-execute in modo indipendente. Una CPU con 8 core contiene 8 di questi motori di calcolo affiancati sullo stesso pezzo di silicio.

Il numero di core conta perché determina quante attività *veramente indipendenti* la CPU può svolgere simultaneamente. Con un singolo core, il computer può eseguire un'istruzione alla volta (e simula il multitasking passando rapidamente tra i programmi); con più core, può eseguire veramente più flussi di lavoro in parallelo. Attenzione, però, a un errore comune: *non tutti i programmi sanno usare più core*. Un software si dice "multi-threaded" quando è scritto per dividere il proprio lavoro in porzioni parallele; molti giochi, soprattutto quelli più vecchi, e molte applicazioni di uso quotidiano, rimangono però prevalentemente "single-threaded", ovvero utilizzano efficacemente solo uno o due core. Per questo, per il gaming, "più core" oltre una certa soglia (oggi 6-8 core sono più che sufficienti per la stragrande maggioranza dei titoli) offre rendimenti decrescenti, mentre per il rendering video, la compilazione, la virtualizzazione e il calcolo scientifico, i core aggiuntivi sono preziosissimi.

### 2.2 Thread e SMT/Hyper-Threading: Differenza Tra Core e Thread

Un **thread** è un singolo flusso di istruzioni ordinato. La confusione tra "core" e "thread" nasce da una tecnologia chiamata **SMT** (Simultaneous Multi-Threading), che Intel commercializza con il nome **Hyper-Threading**. Grazie all'SMT, un singolo core fisico può gestire *due thread contemporaneamente*, presentandosi al sistema operativo come se fossero due core "logici".

Il trucco funziona perché un core, mentre esegue un thread, ha spesso delle unità interne inattive: magari sta aspettando dati dalla memoria, e nel frattempo alcuni dei suoi circuiti computazionali restano oziosi. L'SMT riempie quei "buchi" con il lavoro di un secondo thread, sfruttando meglio le risorse esistenti. Il guadagno tipico è nell'ordine del 15-30% di throughput in più in carichi di lavoro ben parallelizzati: non raddoppia le prestazioni (due thread su un core non equivalgono a due core), ma è un aumento di efficienza "gratuito".

Quando i thread aiutano e quando no? Aiutano nei carichi di lavoro pesantemente multi-threaded e paralleli (rendering, compressione, virtualizzazione, editing pesante), dove mantenere le unità del core costantemente occupate offre un vantaggio concreto. Aiutano poco o per nulla in molti giochi e carichi di lavoro single-thread, dove ciò che conta è la velocità del singolo thread. In alcuni casi estremi, in passato, l'SMT poteva persino ridurre marginalmente le prestazioni nei giochi a causa della contesa di risorse, ed è uno dei motivi per cui a volte se ne consigliava la disattivazione per specifiche competizioni e-sports. Un fatto notevole e recente: **Intel, con l'architettura Arrow Lake (Core Ultra serie 2, dal 2024), ha abbandonato l'Hyper-Threading sui desktop**, concentrandosi invece su un maggior numero di core efficienti. AMD, d'altro canto, mantiene l'SMT su tutta la sua gamma Ryzen. Di conseguenza, oggi, contare i "thread" per confrontare direttamente Intel e AMD è diventato fuorviante: bisogna guardare all'architettura complessiva e ai benchmark.

### 2.3 Frequenza Base vs. Frequenza Boost

La **frequenza di clock** misura quanti cicli al secondo il core completa, espressa in gigahertz (GHz, miliardi di cicli al secondo). Le CPU moderne non hanno una sola frequenza ma almeno due valori dichiarati. La **frequenza base** è quella garantita in condizioni di carico prolungato, entro i limiti standard di consumo energetico e temperatura: è il "minimo sindacale" che la CPU mantiene sempre. La **frequenza boost** (o *turbo*) è il massimo che la CPU raggiunge opportunisticamente quando ha margine termico ed energetico, per accelerare i picchi di lavoro.

È fondamentale distinguere tra due tipi di boost. Il **single-core boost** è la frequenza massima raggiungibile quando è impegnato un solo core (o pochissimi): è alta perché il calore è concentrato in un unico punto e c'è ampio margine, ed è il valore prominentemente pubblicizzato sulla confezione. L'**all-core boost**, d'altra parte, è la frequenza sostenuta quando *tutti* i core lavorano insieme: è sempre inferiore al picco single-core, perché il calore prodotto da tutti i core simultaneamente è molto maggiore e deve essere dissipato. Questa distinzione ha importanti conseguenze pratiche: per un gioco, che spesso sollecita pochi core, il single-core boost è importante; per un rendering che carica tutti i core al 100%, contano l'all-core boost e la capacità del dissipatore di mantenerlo. Un errore comune è lasciarsi affascinare dal "5.7 GHz" scritto sulla confezione senza capire che questo valore si applica solo a un core, per pochi istanti, e non sotto il carico di lavoro previsto.

### 2.4 Cache L1, L2, L3 e il Caso della 3D V-Cache

La **cache** è una memoria molto piccola ma molto veloce, integrata all'interno della CPU, che memorizza i dati e le istruzioni usati frequentemente per evitare di doverli richiedere ogni volta alla RAM, che è molto più lenta. La differenza di velocità è enorme: accedere alla cache costa alla CPU pochi cicli, accedere alla RAM ne costa centinaia. Ogni volta che il dato cercato è già in cache (una situazione chiamata *cache hit*), la CPU risparmia un'enorme attesa; quando non lo è (*cache miss*), deve andarlo a prendere dalla RAM, sprecando tempo prezioso. Ottimizzare la cache è quindi uno dei modi più efficaci per aumentare le prestazioni reali.

Le cache sono organizzate in livelli, in ordine crescente di dimensione e decrescente di velocità. La **cache L1** è la più piccola (poche decine di kilobyte per core) e la più veloce, privata a ogni core. La **cache L2** è più grande (da centinaia di kilobyte a pochi megabyte per core) e leggermente più lenta, anch'essa tipicamente privata al core. La **cache L3** è la più grande (da diversi megabyte fino a oltre 100 MB) ed è normalmente *condivisa* tra tutti i core: agisce come una grande riserva comune, riducendo gli accessi alla RAM per l'intero processore.

| Livello di Cache | Dimensione Tipica | Velocità | Scopo |
|---|---|---|---|
| L1 | 32–80 KB per core | Massima | Privata al core |
| L2 | 512 KB – 3 MB per core | Alta | Privata al core |
| L3 | 16 – 128 MB e oltre | Media | Condivisa tra i core |

La ragione per cui una cache L3 di grandi dimensioni è così importante è esemplificata in modo più eclatante dalla tecnologia **3D V-Cache** di AMD. In sostanza, AMD "impila" verticalmente un blocco aggiuntivo di memoria cache SRAM *sopra* (o, nelle versioni più recenti, sotto) il die di calcolo, aumentando la cache L3 a valori enormi — per esempio, 96 MB su un Ryzen 7 con 3D V-Cache. Questo si è dimostrato un enorme vantaggio, specialmente nel gaming, perché molti giochi hanno set di dati che, se tutti rientrano nella cache, evitano continui e molto costosi viaggi alla RAM, con guadagni nel frame rate che possono essere percentuali a doppia cifra. Questo è il motivo per cui i modelli AMD con il suffisso **X3D** (come il famoso Ryzen 7 9800X3D e il più recente 9850X3D del 2026) sono considerati tra le migliori CPU da gaming di sempre. Va notato, tuttavia, che la 3D V-Cache non aiuta *ogni* gioco allo stesso modo, e il beneficio tende a diminuire all'aumentare della risoluzione (a 4K, il collo di bottiglia si sposta sulla GPU). Inoltre, storicamente, le prime generazioni X3D dovevano ridurre leggermente la loro frequenza per gestire il calore della cache impilata, penalizzando i carichi di lavoro di produttività; con le generazioni più recenti, questo compromesso è stato notevolmente mitigato.

### 2.5 IPC: perché la sola frequenza non basta

**IPC** sta per *Instructions Per Clock*: misura quanto lavoro utile un core può compiere in un singolo ciclo. È il concetto che sfata il mito che "più GHz = più veloce". La formula intuitiva per le prestazioni di un core è infatti *prestazioni ≈ IPC × frequenza*: due fattori moltiplicati, non solo uno.

Questo significa che una CPU moderna a 4.5 GHz può facilmente battere una CPU di dieci anni fa a 5.0 GHz, perché la prima esegue molte più istruzioni per ciclo grazie a un'architettura più efficiente (migliore predizione di diramazioni, più unità di esecuzione, cache più grandi, pipeline ottimizzate). L'IPC è la ragione per cui non è possibile confrontare i GHz tra generazioni o tra marchi diversi: questi numeri significano cose diverse a seconda dell'architettura sottostante. Ogni nuova generazione di CPU vanta tipicamente un "+X% IPC" rispetto alla precedente, ed è quel guadagno — non i GHz — a fare la vera differenza. La lezione pratica per l'acquirente è chiara: non confrontare mai le CPU per GHz, ma per benchmark, che catturano il prodotto reale IPC × frequenza.

### 2.6 TDP e consumo reale: PL1/PL2 e PPT

**TDP** (*Thermal Design Power*) è un valore espresso in watt che, originariamente, indicava quanto calore il dissipatore deve essere in grado di dissipare affinché la CPU operi entro le specifiche. Nella pratica moderna, è diventato una cifra ambigua e spesso fuorviante, perché **non coincide con il consumo massimo effettivo della CPU** sotto carico pesante.

Intel gestisce il consumo energetico attraverso due limiti chiamati **PL1** e **PL2** (*Power Limit 1* e *Power Limit 2*). PL1 è il limite di potenza sostenuta a lungo termine e corrisponde approssimativamente al TDP nominale. PL2 è il limite di picco molto più alto che la CPU può assorbire per brevi intervalli (governati da un timer chiamato *Tau*) durante il boost. Questo è il motivo per cui una CPU con "TDP 125 W" può effettivamente assorbire 250 W o più al picco: quel numero sulla scatola descrive il regime sostenuto, non il picco. Molte schede madri, inoltre, impostano di default limiti "sbloccati" che permettono alla CPU di consumare quanto vuole, alterando ulteriormente il quadro.

AMD utilizza una metrica diversa e più onesta chiamata **PPT** (*Package Power Tracking*), che indica il tetto di potenza effettivo che l'intero package della CPU può assorbire dal socket. Il PPT è tipicamente circa 1,35 volte il TDP nominale: una CPU AMD con "TDP 105 W" ha generalmente un PPT intorno ai 142 W, che rappresenta il reale consumo massimo a pieno carico.

La lezione pratica è duplice. Primo: il TDP non è sufficiente per dimensionare l'alimentatore o il dissipatore; è necessario guardare il reale consumo di picco (PL2 o PPT). Secondo, e cruciale per l'acquirente: comprare una CPU potente e poi abbinarla a un dissipatore inadeguato significa buttare via le prestazioni, perché la CPU, non riuscendo a dissipare il calore, riduce automaticamente la sua frequenza (un fenomeno chiamato *thermal throttling*) e non mantiene mai il boost dichiarato.

### 2.7 Architetture ibride Intel: P-cores ed E-cores

A partire dalla dodicesima generazione, Intel ha introdotto un'architettura **ibrida**, che mescola due tipi di core sullo stesso processore. I **P-cores** (*Performance cores*) sono core grandi, potenti e avidi di energia, progettati per carichi di lavoro pesanti e sensibili alla latenza, come il thread principale di un gioco. Gli **E-cores** (*Efficient cores*) sono core più piccoli e frugali, progettati per gestire in modo efficiente molti task in background e carichi di lavoro altamente paralleli, occupando poco spazio e poca energia: molti possono essere inseriti nello spazio di un P-core.

L'idea è di affidare al sistema operativo (tramite una componente hardware chiamata *Thread Director* che suggerisce come distribuire il lavoro) il compito di assegnare i task giusti ai core giusti: task critici ai P-cores, task secondari e massivi agli E-cores. Il risultato, quando funziona bene, è un eccellente equilibrio tra prestazioni di picco ed efficienza multi-thread. Esistono tuttavia delle implicazioni pratiche. La prima è che il numero di core Intel va letto con attenzione: un "Core Ultra 9 285K" con "24 core" ha in realtà 8 P-cores e 16 E-cores, con caratteristiche prestazionali molto diverse. La seconda è che, agli inizi, alcuni software datati (in particolare certi sistemi anti-cheat di giochi e vecchi programmi) si confondevano nell'assegnazione dei thread, portando a cali di prestazioni; il problema è stato in gran parte risolto con aggiornamenti di Windows e driver, ma è utile saperlo. AMD, dal canto suo, adotta un approccio diverso: nei desktop consumer, utilizza tutti core identici "full-size", eventualmente combinando più gruppi di core (i cosiddetti CCD, i chiplet di calcolo), il che semplifica lo scheduling ma introduce altre sottigliezze nei modelli dual-chiplet.

### 2.8 iGPU integrata: quando serve e quando no

Molte CPU integrano una **iGPU** (*integrated GPU*): una piccola scheda video integrata nel processore stesso. Non ha la potenza di una scheda video dedicata, ma è sufficiente per visualizzare il desktop, riprodurre video, navigare e giocare a giochi leggeri. La sua presenza o assenza è direttamente codificata nei suffissi commerciali, ed è fonte di costosi errori di acquisto.

Per **Intel**, il suffisso **F** (ad esempio, "Core Ultra 5 245KF") indica una CPU *senza iGPU funzionante*: costa qualche decina di euro in meno, ma richiede tassativamente una scheda video dedicata per funzionare, altrimenti non verrà visualizzato nulla a schermo. Per **AMD**, il ragionamento è inverso e più insidioso: la maggior parte delle CPU Ryzen desktop "normali" ha solo una iGPU minimale, appena sufficiente per il desktop; i modelli pensati davvero per giocare senza scheda video dedicata sono le **APU** contraddistinte dal suffisso **G** (come il Ryzen 7 8700G), che integrano una grafica Radeon molto più potente, capace di far girare dignitosamente diversi giochi a 1080p con dettagli ridotti.

Quando serve una iGPU (o una APU)? Serve per un ufficio, un media center, un PC secondario, o un PC economico da usare in attesa di comprare una scheda video, e funge da "rete di sicurezza" per diagnosticare guasti della GPU dedicata. Non serve, ed è inutile pagarla, se comunque verrà installata una potente scheda video dedicata per giocare: in quel caso, scegliere un modello con suffisso F (Intel) può far risparmiare qualche soldo. L'errore classico, di cui parleremo alla fine, è comprare una CPU "F" pensando di risparmiare e poi accorgersi di non avere alcuna scheda video per accendere il PC.

---

## 3. Il socket: compatibilità tra CPU e scheda madre

### 3.1 Cos'è fisicamente un socket

Il **socket** è il connettore fisico sulla scheda madre dove si inserisce la CPU: è l'interfaccia meccanica ed elettrica che collega i mille e più contatti del processore alle tracce sulla scheda. Il socket determina rigidamente *quali* CPU possono essere montate su una data scheda madre: una CPU può funzionare solo su un socket per cui è stata progettata, punto. È la prima e più assoluta regola di compatibilità dell'intero sistema.

Esistono due principali filosofie costruttive. Nelle **LGA** (*Land Grid Array*), i pin — cioè i contatti metallici — sono *sul socket della scheda madre*, mentre la CPU ha solo dei pad piatti (le *lands*) che vi si appoggiano. Nelle **PGA** (*Pin Grid Array*), è l'opposto: i pin sono *sulla CPU*, e il socket della scheda madre ha i fori corrispondenti. La differenza ha conseguenze pratiche sulla fragilità: in PGA, se si piega un pin, si danneggia la costosa CPU; in LGA, un pin piegato è sul socket della scheda madre (comunque difficile da riparare, ma spesso è la mobo ad essere a rischio). Intel usa da anni il formato LGA; AMD ha usato a lungo PGA sulla piattaforma AM4 ma è passata a LGA con la nuova piattaforma AM5.

### 3.2 Socket AMD attuali e recenti: AM4 e AM5

AMD ha costruito un'enorme reputazione sulla **longevità delle piattaforme**, intendendo con ciò la sua capacità di supportare molte generazioni di CPU sullo stesso socket, permettendo upgrade senza cambiare la scheda madre. Il socket **AM4** (formato PGA) ne è l'esempio principe: introdotto nel 2016, ha supportato un'impressionante gamma di generazioni Ryzen, dai primi Ryzen 1000 fino ai Ryzen 5000 (e AMD ha continuato a lanciare nuovi modelli su di esso anche anni dopo, celebrandone i "10 anni" nel 2026 con edizioni speciali). Chi ha comprato una buona scheda madre AM4 nel 2017 ha potuto, anni dopo, installare una CPU molto più potente con un semplice aggiornamento firmware.

Il socket **AM5** (formato LGA, dal 2022) è l'attuale piattaforma di AMD per le serie Ryzen 7000 e 9000 (e i loro modelli X3D). Introduce il supporto obbligatorio per la memoria **DDR5** e il **PCIe 5.0**. Anche qui, AMD ha promesso e confermato una lunga longevità: **il supporto AM5 è garantito almeno fino al 2029**, il che rende una scheda madre AM5 acquistata oggi un investimento ragionevolmente a prova di futuro, in grado di ospitare anche le prossime generazioni. Vale la pena notare che le CPU di prossima generazione basate sull'architettura Zen 6 (la famiglia "Ryzen 10000", nome in codice "Olympic Ridge") sono state, secondo le indiscrezioni di metà 2026, **posticipate al 2027**, e rimarranno comunque sul socket AM5: chi acquista AM5 oggi sarà molto probabilmente aggiornabile anche a quella generazione.

### 3.3 Socket Intel attuali e recenti: LGA1700 e LGA1851

Intel ha storicamente avuto una politica opposta a quella di AMD: **cambia i socket molto più frequentemente**, tipicamente ogni una o due generazioni, costringendo spesso chi aggiorna la propria CPU a cambiare anche la scheda madre. Il numero nel nome del socket (tutti in formato LGA) indica il numero di contatti. Il socket **LGA1700** ha ospitato le generazioni Intel dalla dodicesima alla quattordicesima (Core di 12a, 13a e 14a gen), con supporto sia per DDR4 che per DDR5 a seconda della scheda madre.

Il socket attuale è **LGA1851**, introdotto con la serie **Core Ultra 2 "Arrow Lake"** (2024) e con il loro aggiornamento **"Arrow Lake Refresh" / Core Ultra 200S Plus** a marzo 2026 (modelli come il 270K Plus e il 250K Plus). LGA1851 supporta esclusivamente DDR5. Qui, tuttavia, entra in gioco il classico avvertimento sulla politica di Intel: **LGA1851 è già a fine vita**. I chipset della serie 800 sono l'ultima generazione per questo socket, e Intel introdurrà un nuovo socket (denominato **LGA1954**) per la prossima generazione "Nova Lake" nella seconda metà del 2026. In pratica, acquistare una piattaforma Intel LGA1851 oggi significa acquisire, con ogni probabilità, un "vicolo cieco" in termini di futuri aggiornamenti della CPU: si potranno cambiare CPU solo all'interno della famiglia attuale. Questo è un fattore decisivo nella scelta tra Intel e AMD per chi pianifica aggiornamenti a distanza di anni.

| Socket | Produttore | Tipo | Generazioni CPU | Memoria | PCIe (dalla CPU) | Stato (Lug. 2026) |
|---|---|---|---|---|---|---|
| AM4 | AMD | PGA | Ryzen 1000–5000 | DDR4 | 4.0 | Legacy, ma ancora venduto (segmento budget) |
| AM5 | AMD | LGA | Ryzen 7000–9000 (e futuri) | DDR5 | 5.0 | **Attuale**, supportato almeno fino al 2029 |
| LGA1700 | Intel | LGA | Core 12a–14a gen | DDR4/DDR5 | 5.0 | Precedente, ancora disponibile |
| LGA1851 | Intel | LGA | Core Ultra 200S / 200S Plus | DDR5 | 5.0 | **Attuale**, ma a fine ciclo (in arrivo LGA1954) |

### 3.4 Come verificare la compatibilità: socket + chipset + BIOS

La verifica della compatibilità richiede il controllo di *tre* livelli, non solo del socket. Il primo livello è, ovviamente, il **socket**: CPU e scheda madre devono avere lo stesso socket. Il secondo livello è il **chipset**, che è il "controllore" sulla scheda madre che gestisce le connessioni periferiche (porte, linee PCIe del chipset, funzioni di overclock): anche con lo stesso socket, non tutte le combinazioni chipset/CPU sono valide o convenienti. Su AMD, ad esempio, i chipset di fascia alta (serie X, come X670E o X870E) abilitano più funzionalità e l'overclocking, mentre quelli di fascia media (serie B) sono più economici ma limitati; su Intel, esiste una distinzione simile, con i chipset "Z" (es. Z890) che sbloccano l'overclocking e i chipset "B" più economici che non lo fanno. Il terzo livello, spesso dimenticato, è la **versione del BIOS/UEFI** (il firmware della scheda madre): una scheda madre rilasciata prima di una certa CPU potrebbe non riconoscerla finché il suo firmware non viene aggiornato. È un errore molto comune acquistare una scheda madre che è "compatibile sulla carta" e trovarla non responsiva perché ha un BIOS troppo vecchio per la CPU appena acquistata.

Il modo corretto di procedere è consultare la **lista di supporto CPU** (l'elenco ufficiale delle CPU supportate) per quel modello specifico sul sito web del produttore della scheda madre, che indica la versione minima del BIOS richiesta per ogni CPU. È un controllo di due minuti che fa risparmiare giorni di frustrazione.

### 3.5 Retrocompatibilità e aggiornamenti BIOS senza CPU (flashback)

Il problema del "BIOS troppo vecchio" ha una soluzione elegante offerta da molte schede madri di fascia media e alta: il **BIOS Flashback** (chiamato anche *Q-Flash Plus* o simili a seconda del marchio). Questa funzione permette di **aggiornare il BIOS senza avere CPU, RAM o scheda grafica installate**: basta scaricare il file del BIOS su una chiavetta USB, inserirla in una porta specifica, premere un pulsante dedicato sulla scheda, e la scheda si aggiorna da sola usando solo l'alimentazione. È preziosissimo nel caso classico: ho comprato una nuova CPU e una scheda madre più vecchia che non la riconosce; senza flashback, sarei bloccato, perché per aggiornare il BIOS avrei bisogno di una CPU già supportata da installare temporaneamente. Con il flashback, risolvo tutto in dieci minuti. Chiunque acquisti una nuova piattaforma o pianifichi aggiornamenti dovrebbe considerare la presenza di questa funzionalità un requisito quasi indispensabile nella scelta di una scheda madre.

---

## 4. PCI Express: il trattamento completo

Questa è la sezione più densa del capitolo, perché il **PCIe** è il "sistema nervoso" che collega la CPU alla scheda grafica, agli SSD veloci e ad altre espansioni, ed è fonte di enormi incomprensioni.

### 4.1 Cos'è il PCIe: un bus seriale punto-punto

**PCIe** sta per *Peripheral Component Interconnect Express*: è lo standard di interconnessione ad alta velocità che permette a CPU e periferiche di scambiare dati. La sua caratteristica architettonica fondamentale è che è un bus **seriale** e **punto-punto**. "Seriale" significa che i dati viaggiano in fila su percorsi stretti ma molto veloci, invece che in parallelo su molti fili come negli standard più vecchi: paradossalmente, a queste frequenze, inviare dati in serie su pochi canali molto veloci è più efficiente e affidabile che distribuirli su molti fili paralleli che rischiano di "desincronizzarsi". "Punto-punto" significa che ogni dispositivo ha la sua connessione dedicata con l'host, senza doverla condividere e senza contendere la larghezza di banda con altri, come avveniva con i bus condivisi del passato.

### 4.2 Corsie (Lanes): cosa significano x1, x4, x8, x16

L'unità base del PCIe è la **corsia** (lane). Una corsia è una singola connessione seriale bidirezionale, fisicamente composta da due coppie di fili: una coppia per la trasmissione e una per la ricezione, in modo che i dati possano fluire in entrambe le direzioni simultaneamente (*full duplex*). Le corsie possono essere aggregate per aumentare la larghezza di banda: le designazioni **x1, x4, x8, x16** indicano quante corsie sono raggruppate per una data connessione. Uno slot "x16" utilizza 16 corsie in parallelo e offre quindi sedici volte la larghezza di banda di una singola corsia; uno slot "x4" ne usa quattro, e così via.

Questo ha una duplice natura, *fisica* ed *elettrica*, che causa molta confusione. Uno slot può essere **fisicamente** lungo come un x16 (cioè, avere lo spazio meccanico per 16 corsie) ma essere **elettricamente** collegato solo a x4 o x8, il che significa che ha effettivamente un numero inferiore di corsie cablate. Questa è una situazione molto comune sulle schede madri economiche: il secondo slot lungo, che sembra un x16, in realtà funziona a x4. Il dispositivo si adatta e funziona comunque, ma alla larghezza di banda ridotta delle corsie effettivamente presenti. Per questo motivo, quando si leggono le specifiche tecniche di una scheda madre, bisogna sempre distinguere la lunghezza fisica dello slot dal numero effettivo di corsie elettriche (spesso indicato con notazioni come "x16 (x8 electrical)").

### 4.3 Generazioni: PCIe 3.0, 4.0, 5.0 e un breve accenno al 6.0

Ogni nuova generazione di PCIe **raddoppia** la larghezza di banda per corsia rispetto alla precedente, a parità di numero di corsie. Questo raddoppio generazionale è la chiave per capire tutto il resto. La seguente tabella riassume la larghezza di banda unidirezionale approssimativa per configurazione (i valori "utili" effettivi sono leggermente inferiori a causa dell'overhead di codifica, ma l'ordine di grandezza e i rapporti sono corretti):

| Generazione | Larghezza di banda per corsia (~) | x4 (NVMe tipico) | x16 (GPU tipica) | Diffusione (Lug. 2026) |
|---|---|---|---|---|
| PCIe 3.0 | ~1 GB/s | ~4 GB/s | ~16 GB/s | Legacy, ancora comune sui chipset |
| PCIe 4.0 | ~2 GB/s | ~8 GB/s | ~32 GB/s | Standard consolidato, ottimo rapporto q/p |
| PCIe 5.0 | ~4 GB/s | ~16 GB/s | ~64 GB/s | Top consumer attuale (GPU e SSD di fascia alta) |
| PCIe 6.0 | ~8 GB/s | ~32 GB/s | ~128 GB/s | Emergente, attualmente in ambienti server/datacenter |

Il PCIe 6.0, che raddoppia ancora, è tecnicamente definito e sta arrivando nel mondo enterprise, ma sui desktop consumer a metà 2026 non è ancora rilevante: le CPU e le schede madri consumer arrivano al PCIe 5.0. Ne parleremo più in dettaglio nel capitolo sulle workstation e i server, dove la larghezza di banda estrema conta davvero.

### 4.4 Compatibilità all'indietro tra generazioni e slot

Una delle grandi virtù di PCIe è la **piena compatibilità all'indietro e in avanti**. Un dispositivo di generazione più recente inserito in uno slot di generazione precedente (ad esempio, una GPU PCIe 5.0 in uno slot PCIe 4.0) funziona senza problemi, semplicemente negoziando la velocità della generazione più bassa tra le due. Viceversa, un dispositivo più vecchio in uno slot più recente funziona alla sua velocità. Inoltre, un dispositivo con meno linee si adatta a uno slot più lungo: una scheda x4 funziona in uno slot x16 (utilizzando solo le linee di cui ha bisogno). Tuttavia, l'inverso meccanico non funziona: una scheda x16 non si adatta fisicamente a uno slot x1 più corto, a meno che lo slot non sia "aperto" sul retro. Il messaggio pratico è rassicurante: PCIe "si arrangia" quasi sempre, adattandosi al minimo comune denominatore, e non c'è rischio di danni mescolando le generazioni; al massimo, si perde un po' di banda.

### 4.5 Chi fornisce le linee: CPU vs. chipset, e perché è importante

Questo è il punto più sottile e importante per fare scelte informate. Le linee PCIe in un sistema provengono da **due diverse fonti**, con caratteristiche molto diverse. Alcune linee sono fornite **direttamente dalla CPU**: queste sono le più veloci e a bassa latenza, connesse direttamente al processore senza intermediari, e sono riservate ai dispositivi più esigenti in termini di banda, tipicamente lo slot della scheda grafica principale (x16) e uno o due slot NVMe primari. Altre linee sono fornite dal **chipset** della scheda madre, che è connesso alla CPU tramite un collegamento a banda limitata (una "tubazione" chiamata *DMI* su Intel o un collegamento dedicato su AMD).

Il punto cruciale è che **tutte le linee del chipset condividono quella singola tubazione verso la CPU**. Se molti dispositivi connessi al chipset vengono caricati simultaneamente (SSD secondari, schede di rete, USB veloci), possono saturare il collegamento e contendersi la banda, cosa che non accade sulle linee dirette della CPU. Per questo una regola d'oro nell'assemblaggio è: **la scheda grafica e l'SSD NVMe principale vanno negli slot direttamente connessi alla CPU**, non negli slot del chipset. Il manuale della scheda madre indica sempre quali slot sono "CPU-attached" e quali "chipset-attached"; ignorare questa distinzione può portare, ad esempio, a installare un SSD veloce in uno slot che ne dimezza le prestazioni.

### 4.6 Quante linee hanno le CPU consumer rispetto a HEDT e server

Le moderne CPU consumer forniscono un numero **limitato** di linee PCIe dirette: tipicamente **da 20 a 28 linee** (ad esempio, 16 per la GPU, 4–8 per gli SSD NVMe e alcune per la connessione al chipset). Questo potrebbe sembrare poco, e in effetti lo è: è una scelta di posizionamento, poiché AMD e Intel riservano un'abbondanza di linee per piattaforme di fascia più alta e più costose. Le piattaforme **HEDT** (*High-End Desktop*) e **server** — come AMD Threadripper ed EPYC, o Intel Xeon — offrono un numero enormemente maggiore di linee, nell'ordine di **64, 128 o più**. Questa abbondanza è ciò che permette a una workstation di montare simultaneamente più GPU, decine di SSD, schede di acquisizione, schede di rete da 100 gigabit e altro ancora, il tutto a piena banda e senza contese. Questo è un argomento che approfondiremo nel capitolo dedicato alle workstation; per ora, basti dire che *il numero di linee è uno dei muri invisibili che separano il mondo consumer da quello professionale*, ed è spesso la vera ragione per cui certi utenti "estremi" hanno bisogno di aggiornare la propria piattaforma.

### 4.7 Biforcazione: cos'è e quando è necessaria

La **biforcazione** è la capacità di **dividere un singolo slot x16 in più connessioni indipendenti più piccole**, ad esempio, in due x8, o quattro x4. La CPU ha un certo numero di lane fisiche; la biforcazione permette di riallocarle in modo diverso dal raggruppamento predefinito. Il caso d'uso tipico per il consumatore è installare una scheda di espansione che ospita più SSD NVMe (una "scheda quad M.2") in un singolo slot x16, chiedendo alla scheda madre di dividere quelle 16 lane in quattro gruppi x4, uno per ogni SSD. È utile anche per chi vuole due schede grafiche a x8/x8 invece di una sola a x16. La biforcazione non è obbligatoria, e non tutte le schede madri la supportano (va verificata nel BIOS e nelle specifiche): è una funzionalità da controllare *prima* dell'acquisto se si ha in mente uno di questi scenari. Per l'utente medio, che installa una GPU e uno o due SSD nei rispettivi slot, non è un fattore rilevante.

### 4.8 Impatto pratico: GPU su x8 vs x16, NVMe Gen4 vs Gen5

Concludiamo con le domande pratiche più frequenti. La prima: **una scheda grafica su x8 perde prestazioni rispetto a x16?** La risposta, sorprendente per molti, è: quasi sempre no, o molto poco. Le schede grafiche moderne, anche di fascia alta, raramente saturano la banda di uno slot x16 di generazione recente; se lo slot è di una generazione moderna (PCIe 4.0 o 5.0), farlo funzionare a x8 di quella generazione lascia comunque una banda enorme, e la perdita effettiva di frame rate è tipicamente nell'ordine di pochi punti percentuali, spesso impercettibile. Diventa più significativa solo su generazioni molto vecchie o con schede al limite della loro banda. Questo rassicura chi, per installare più dispositivi, si ritrova la GPU a funzionare a x8.

La seconda: **vale la pena un SSD NVMe PCIe 5.0 (Gen5) rispetto a un Gen4?** Sulla carta, il Gen5 raddoppia la banda sequenziale, e nei benchmark che coinvolgono trasferimenti enormi, la differenza è notevole. Nell'uso quotidiano reale, tuttavia, il vantaggio è molto meno percepibile di quanto i numeri suggeriscano, perché la maggior parte delle operazioni comuni (avvio del sistema, apertura programmi, caricamento giochi) dipendono più dalle prestazioni casuali su piccoli file che dalla banda sequenziale di picco. Inoltre, gli SSD Gen5 tendono a costare di più, scaldano di più e richiedono dissipatori più seri. Per l'utente tipico, un buon Gen4 offre attualmente il miglior rapporto qualità-prezzo; il Gen5 ha senso per specifici carichi di lavoro professionali di editing video con file giganteschi o per chi semplicemente vuole il massimo assoluto a prescindere.

---

## 5. Nomenclatura Commerciale: Come Leggere il Nome di una CPU

Saper "decodificare" il nome di una CPU è un'abilità pratica che previene molti errori. Vediamo i due produttori.

### 5.1 Intel: Da Core i a Core Ultra

Per quasi quindici anni, Intel ha usato lo schema **Core i3 / i5 / i7 / i9**, dove il numero indica il livello di prestazioni crescente: i3 entry, i5 fascia media, i7 fascia alta, i9 enthusiast. Il modello si leggeva così: dopo la designazione veniva la **generazione** (ad esempio, "14" in "i7-14700K" indicava la quattordicesima generazione), seguita dal numero di identificazione del modello e da un **suffisso** letterale.

Dal 2023–2024, Intel ha cambiato il suo branding: è stata introdotta la nomenclatura **Core Ultra**, con i livelli **Core Ultra 5 / 7 / 9** (e "Core" senza Ultra per i livelli inferiori), abbandonando il vecchio "i". Sui desktop, questa nuova famiglia è la serie **Core Ultra 200S "Arrow Lake"** (ad esempio, "Core Ultra 9 285K"), con il refresh del 2026 identificato dal suffisso **"Plus"** (ad esempio, "Core Ultra 7 270K Plus"). La logica di lettura rimane simile: livello (5/7/9), poi il numero di modello, poi i suffissi.

I **suffissi Intel** sono la parte che più spesso causa confusione. Ecco il loro significato:

| Suffisso Intel | Significato |
|---|---|
| **K** | Moltiplicatore sbloccato: **overclockabile**. Frequenze più alte, richiede chipset "Z" e buon raffreddamento. |
| **KF** | Come K (sbloccato) ma **senza iGPU**: richiede una scheda grafica dedicata. |
| **F** | **Senza iGPU funzionante**: richiede una scheda grafica dedicata. Costa un po' meno. |
| **T** | Versione a **basso consumo** (TDP ridotto), per sistemi compatti/silenziosi; prestazioni inferiori. |
| **(nessun suffisso)** | Modello standard, con iGPU, non overclockabile ma efficiente. |
| **S** | Storicamente indicava versioni "special edition"; oggi "S" identifica principalmente la *serie desktop* (200**S**). |

Un "Core Ultra 5 245KF" si legge quindi come: fascia media (5), modello 245, **K**=overclockabile, **F**=senza grafica integrata (richiede una GPU dedicata).

### 5.2 AMD: La Logica Ryzen

AMD utilizza lo schema **Ryzen 3 / 5 / 7 / 9**, con lo stesso principio di livelli crescenti: Ryzen 3 entry-level, Ryzen 5 fascia media, Ryzen 7 fascia alta, Ryzen 9 enthusiast. Dopo il livello segue un numero a **quattro cifre** dove la prima cifra indica la **serie/generazione**: 5000, 7000, 9000. Attenzione a una trappola: la serie 8000 per desktop (APU 8700G, ecc.) è, a livello architetturale, un po' fuori sequenza rispetto alle 7000/9000, ed è dedicata principalmente alle APU con grafica potente. Anche nel caso di AMD, i suffissi finali sono cruciali:

| Suffisso AMD | Significato |
|---|---|
| **X** | Frequenze più alte e boost migliore rispetto al modello base; tutti i Ryzen desktop sono comunque overclockabili. |
| **X3D** | Dotato di **3D V-Cache**: enorme cache L3, il top per il **gaming** (es. 9800X3D, 9850X3D). |
| **G** | **APU**: grafica integrata Radeon potente, gioca a 1080p senza scheda grafica dedicata (es. 8700G). |
| **GE** | Come G ma a **basso consumo** (versione efficiente delle APU). |
| **F** | **Nessuna grafica integrata** (raro nei desktop AMD; es. 8700F), richiede una GPU dedicata. |
| **(nessun suffisso)** | Modello standard "non-X", frequenze leggermente inferiori ma spesso ottimo rapporto prezzo/prestazioni. |

Un "Ryzen 7 9800X3D" si legge quindi come: fascia alta (7), serie 9000, **X3D**=con 3D V-Cache progettata per il gaming. Nota importante e a volte controintuitiva: nei desktop AMD, *tutte* le CPU hanno il moltiplicatore sbloccato e sono in linea di principio overclockabili (a differenza di Intel, dove serve una "K"); la "X" indica solo un migliore binning e boost out of the box, non lo "sblocco".

### 5.3 Threadripper e Xeon: solo un accenno

Oltre alle linee consumer, esistono linee professionali: **Ryzen Threadripper** (AMD) e **Xeon** (Intel) sono processori HEDT e server con molti core e molte linee PCIe, socket dedicati, supporto per grandi quantità di RAM (spesso con correzione errori ECC) e prezzi di tutt'altra entità. Non sono pensati per il gaming o l'uso domestico, ma per workstation professionali, calcolo e datacenter. Li tratteremo ampiamente nel capitolo dedicato alle workstation; qui basti sapere che esistono e rappresentano un mondo a parte rispetto a quanto descritto in questo capitolo.

---

## 6. Confronto tra marchi: Intel vs. AMD

Va premesso che "chi è meglio" cambia costantemente ad ogni generazione, e le seguenti considerazioni riflettono la situazione a metà 2026: andranno riverificate al momento dell'acquisto.

### 6.1 Punti di forza attuali

Nel **gaming puro**, AMD ha attualmente un vantaggio piuttosto netto grazie ai modelli **X3D** con 3D V-Cache, che sono considerati le CPU da gaming più veloci sul mercato (il Ryzen 7 9800X3D e il più recente 9850X3D del 2026 dominano le classifiche di frame rate). Intel, dopo un debutto deludente di Arrow Lake nel gaming, ha recuperato terreno con il refresh "200S Plus" nel 2026 e con ottimizzazioni software (come lo strumento di ottimizzazione del codice di gioco introdotto con il refresh), ma nel gaming di fascia alta AMD rimane il punto di riferimento.

Nella **produttività multi-thread** (rendering, compilazione, calcolo), i due marchi sono paragonabili a seconda del modello e del prezzo: i Ryzen 9 ad alto numero di core e le CPU Intel con abbondanza di E-core sono entrambi molto validi, ed è consigliabile guardare benchmark specifici per il proprio software. Sul fronte dell'**efficienza energetica**, Intel ha fatto notevoli progressi con Arrow Lake, che consuma e scalda molto meno rispetto alle disastrose generazioni precedenti (le CPU Core di 13a/14a gen erano note per gli alti consumi e i problemi di degrado); AMD rimane molto competitiva, e i suoi modelli X3D in particolare offrono prestazioni gaming elevatissime a consumi contenuti.

Sul **rapporto prezzo/prestazioni**, il refresh 2026 di Intel ha rimesso in gioco modelli molto aggressivi: il Core Ultra 5 250K Plus a circa $199 e il 270K Plus a circa $299 sono stati accolti come ottimi affari per la produttività. AMD risponde con la sua lineup consolidata e, soprattutto, con il valore della piattaforma a lungo termine, di cui parleremo subito.

### 6.2 Piattaforma: costo totale e longevità

Il confronto non va fatto solo sulla CPU, ma sul **costo totale della piattaforma**, che include CPU + scheda madre + RAM. Qui, il tema della **longevità del socket** gioca un ruolo determinante. Acquistare AMD **AM5** oggi significa acquistare una piattaforma con supporto garantito almeno fino al 2029: una scheda madre AM5 potrà ospitare anche le future CPU (Zen 6/Ryzen 10000, attese nel 2027), permettendo un futuro upgrade della sola CPU senza cambiare scheda madre e RAM. Acquistare Intel **LGA1851** oggi, invece, significa acquistare un socket già a fine vita, che verrà sostituito da LGA1954 nella seconda metà del 2026: i futuri upgrade della CPU saranno limitati all'attuale famiglia, e per passare di generazione sarà necessario un cambio di scheda madre. Questo è, per molti, il singolo argomento più forte a favore di AMD per chi ragiona a lungo termine.

Tuttavia, c'è un enorme fattore contingente da considerare nel 2026: la già citata **crisi dei prezzi delle memorie DDR5**. Con prezzi delle RAM che sono multipli del normale, il "costo della piattaforma" è attualmente dominato dalla memoria più che dalla CPU o dalla scheda madre. In alcuni casi, un buon kit di RAM veloce costa più della CPU stessa. Questo consiglia, in questo particolare momento storico, di non sovradimensionare la RAM oltre il necessario e di dare un peso ancora maggiore alla scelta di una piattaforma longeva, in modo da diluire nel tempo un investimento che oggi è particolarmente oneroso.

### 6.3 Casi d'Uso

Per il **gaming puro**, specialmente ad alti frame rate e a 1080p/1440p, la scelta più sensata oggi è una CPU AMD **X3D** (spesso anche un modello di generazione precedente rappresenta un buon affare). Per lo **streaming e gaming insieme**, dove servono core extra per l'encoding oltre che per il gioco, sono adatte sia le CPU Ryzen 7/9 che le Intel con molti E-core, a patto di avere un buon numero di thread. Per **editing e rendering**, contano i core e i thread: si cerca il miglior punteggio multi-thread per euro, e qui entrambi i marchi hanno proposte valide a seconda del budget. Per la **virtualizzazione**, contano core, thread e la quantità di RAM supportata (e, se possibile, il supporto ECC, più facile da trovare su AMD consumer): sono preferibili CPU multi-core. Per una **build economica**, oggi ci sono ottime opzioni da entrambi i lati, incluse le APU "G" di AMD che permettono di partire senza scheda grafica, e Intel di fascia bassa con un buon rapporto q/p; qui la scelta dipende molto dai prezzi locali attuali.

---

## 7. Come Valutare e Scegliere una CPU

### 7.1 Benchmark: Cosa Guardare e Cosa Non Fidarsi

I **benchmark** sono test standardizzati che misurano le prestazioni. Vanno interpretati con attenzione, scegliendo quelli rilevanti per il *vostro* specifico utilizzo. **Cinebench** misura le prestazioni di rendering ed è un buon indicatore per carichi di lavoro produttivi multi-threaded, fornendo punteggi sia single-core che multi-core. **Geekbench** è un benchmark sintetico più generale, utile per una rapida panoramica ma meno rappresentativo di carichi di lavoro specifici del mondo reale. Per il gaming, il test più significativo sono i **benchmark di gaming a 1080p** con una scheda grafica molto potente: a bassa risoluzione, la GPU non è il collo di bottiglia, quindi le differenze misurate riflettono davvero la potenza della CPU. Potrebbe sembrare controintuitivo testare le CPU da gaming a 1080p invece che a 4K, ma è proprio così che si isola il contributo della CPU: a 4K, molte CPU diverse producono lo stesso frame rate perché la GPU è il fattore limitante.

Di cosa non fidarsi? Non fidarsi dei **benchmark forniti dai produttori** senza verifica indipendente, poiché sono scelti per evidenziare i loro punti di forza. Non fidarsi di un **singolo numero sintetico** come metrica universale: una CPU potrebbe brillare in un test e deludere nel software che usate realmente. Non fidarsi dei **GHz** come già ampiamente spiegato. L'approccio giusto è consultare recensioni indipendenti che testano le applicazioni e i giochi a cui siete interessati, guardando le medie su più titoli e i valori di *frame time* (la consistenza, non solo la media), che riflettono meglio la fluidità reale rispetto al frame rate medio.

### 7.2 Bilanciamento CPU/GPU: Evitare i Colli di Bottiglia

Un **collo di bottiglia** si verifica quando un componente è troppo lento rispetto a un altro e ne limita le prestazioni: la catena è forte solo quanto il suo anello più debole. In un PC da gaming, un **collo di bottiglia della CPU** significa che la scheda grafica potrebbe produrre più fotogrammi, ma la CPU non le fornisce dati abbastanza velocemente (tipico a frame rate elevati e bassa risoluzione, o con CPU deboli e GPU potenti); un **collo di bottiglia della GPU** è l'opposto, con la CPU in attesa della scheda grafica (tipico ad alte risoluzioni). Un certo grado di collo di bottiglia della GPU è in realtà desiderabile nel gaming ad alta risoluzione, poiché significa che si sta utilizzando appieno la scheda grafica.

Evitare un collo di bottiglia significa **bilanciare** CPU e GPU in base all'uso previsto. Un errore comune è spendere quasi l'intero budget per la GPU, abbinandola a una CPU economica (o viceversa): il componente costoso sarà limitato dall'altro. La regola pratica è dimensionare i due componenti allo stesso livello di "tier", considerando la risoluzione a cui si giocherà: a 1080p con refresh rate elevati, è necessaria una CPU potente; a 4K, si può optare per una CPU leggermente più modesta, concentrando il budget sulla GPU.

### 7.3 Overclocking: Cosa Serve e Se Conviene Ancora

L'**overclocking** (OC) è la pratica di far funzionare la CPU a frequenze superiori a quelle di fabbrica per estrarre maggiori prestazioni. Per farlo su Intel, è necessaria una CPU con suffisso **K** (moltiplicatore sbloccato) e una scheda madre con chipset **Z**; su AMD, tutte le CPU desktop sono sbloccate, ma è comunque necessario un chipset adeguato (generalmente serie B o X) e, in entrambi i casi, un **raffreddamento robusto** e un buon alimentatore, perché l'OC aumenta il consumo energetico e il calore.

Conviene ancora? Molto meno che in passato. Le CPU moderne eseguono già un "auto-overclocking" intelligente e aggressivo tramite algoritmi di boost, spingendosi al limite del margine termico disponibile: il guadagno manuale residuo è spesso modesto e comporta un costo in termini di consumo energetico e calore significativamente maggiori, oltre a rischi per la stabilità. Oggi, ha più senso investire in un buon dissipatore (che permette alla CPU di *sostenere* i suoi boost automatici più a lungo) e, sul fronte della memoria, attivare i profili **XMP/EXPO** della RAM (che sono un "overclock certificato" della memoria, semplice e sicuro) piuttosto che dedicarsi all'overclocking manuale della CPU. Per la stragrande maggioranza degli utenti, l'overclocking estremo è diventato un hobby per appassionati piuttosto che una necessità.

### 7.4 Raccomandazioni per fasce di prezzo

I prezzi seguenti sono ordini di grandezza indicativi per la sola CPU (mercato europeo, luglio 2026) e **devono essere verificati**, specialmente nell'attuale contesto di elevata volatilità. Ricordate inoltre di aggiungere sempre scheda madre e RAM per il costo reale della piattaforma.

| Livello | Budget CPU (indicativo) | Caso d'uso | Approccio consigliato |
|---|---|---|---|
| **Entry** | ~100–150 € | Ufficio, studio, gaming leggero, PC secondario | CPU recente 6-core; considerare APU AMD "G" per iniziare senza GPU dedicata; chipset economico (B), dissipatore stock spesso sufficiente. |
| **Mid** | ~200–350 € | Gaming solido, produttività quotidiana, streaming leggero | 6–8 core performanti; per il gaming, un AMD X3D è spesso il miglior investimento; scheda madre B di buona qualità, dissipatore ad aria decente. |
| **High-end** | 400 €+ | Gaming di alto livello, editing/rendering pesanti, workstation leggera | Modelli top (X3D per gaming, molti core per produttività); scheda madre di fascia alta se servono espansioni; raffreddamento a liquido o ad aria di alto livello. |

Un consiglio trasversale: per il *gaming*, spesso il miglior impiego del denaro è prendere una CPU X3D di fascia medio-alta e concentrare il resto del budget sulla scheda grafica, perché la GPU la fa da padrona nei frame rate ad alte risoluzioni. Per la *produttività*, al contrario, investire nei core della CPU rende di più.

---

## 8. Errori comuni dell'acquirente

Concludiamo con gli errori che, in pratica, rovinano il maggior numero di build e fanno sprecare più denaro.

Il primo, quasi un classico, è **acquistare una CPU con suffisso "F" (o "KF") senza avere una scheda grafica dedicata**. Attratti dal piccolo risparmio, ci si ritrova con un PC che non dà segnale video, perché la CPU è sprovvista di grafica integrata. Se non è prevista una GPU dedicata, questo suffisso va assolutamente evitato.

Il secondo è l'**errore di compatibilità socket o BIOS**: acquistare una CPU e una scheda madre con socket diversi, oppure una scheda madre "compatibile sulla carta" ma con un BIOS troppo vecchio per riconoscere la CPU, senza la funzione di flashback per aggiornarlo a freddo. Si evita controllando il socket, il chipset e la lista ufficiale di supporto CPU della scheda madre, con la versione minima del BIOS richiesta.

Il terzo è **abbinare una CPU potente a un dissipatore inadeguato** (o affidarsi al dissipatore "stock" incluso quando non è all'altezza). Il risultato è il thermal throttling: la CPU si surriscalda, taglia le frequenze e non mantiene mai il boost per cui si è pagato. Le CPU di fascia alta, specialmente in overclock o sotto pieno carico, richiedono un raffreddamento serio; risparmiare qui significa buttare via prestazioni pagate altrove.

Il quarto è **trascurare la RAM in relazione all'IMC**. L'**IMC** (*Integrated Memory Controller*) è la parte della CPU che comunica con la RAM, e le prestazioni del sistema dipendono pesantemente dall'accoppiata CPU-memoria. Installare RAM lenta, in single channel (un solo modulo invece di due, perdendo il *dual channel* che raddoppia la banda), o dimenticare di attivare il profilo **XMP/EXPO** nel BIOS (lasciando quindi la RAM a frequenze base molto basse) sono errori che strozzano una CPU altrimenti capace. Le architetture AMD, in particolare, sono storicamente sensibili alla velocità e latenza della RAM. Nel 2026, però, va aggiunta la controfaccia: con i prezzi delle DDR5 alle stelle, esagerare con RAM ultra-veloci e ultra-capienti può costare più della CPU stessa; il giusto equilibrio è puntare a un buon kit dual-channel a una frequenza sensata, attivare il suo profilo XMP/EXPO e non sovradimensionare inutilmente.

Il quinto errore è lo **squilibrio CPU/GPU** già discusso: spendere tutto su un componente e strozzarlo con l'altro. Il sesto, più sottile, è **pagare per linee PCIe o funzionalità che non verranno usate** (schede madri costosissime comprate per semplice gaming) o, al contrario, **installare l'SSD o la GPU veloci negli slot sbagliati** (slot del chipset invece di quelli direttamente collegati alla CPU), perdendo banda senza accorgersene. Infine, un errore di prospettiva: **inseguire a tutti i costi l'ultima generazione**, quando spesso il modello della generazione precedente, ora scontato, offre il 90% delle prestazioni a una frazione del prezzo — una considerazione particolarmente valida oggi, con le nuove generazioni AMD posticipate al 2027 e l'attuale socket Intel già a fine vita.

---

## Riepilogo Operativo — Checklist Decisionale CPU

Prima dell'acquisto, rispondi a queste domande in ordine:

1. **Definisci l'uso primario.** Gaming puro, produttività/rendering, uso misto, o ufficio/budget? Questa domanda guida tutto il resto (per il gaming, guarda AMD X3D; per la produttività, conta i core).

2. **Stabilisci il budget per l'intera piattaforma, non solo per la CPU.** Somma CPU + scheda madre + RAM. Nel 2026, controlla attentamente il prezzo delle DDR5, che può essere la voce più significativa.

3. **Scegli la piattaforma pensando alla longevità.** AM5 (AMD) è supportato almeno fino al 2029 e ospiterà CPU future; LGA1851 (Intel) è a fine ciclo. Se prevedi futuri upgrade solo della CPU, questo pesa molto.

4. **Verifica la compatibilità su tre livelli:** socket identico, chipset adeguato all'uso (Z/X per overclock, B per risparmiare), e versione del BIOS supportata (controlla la lista ufficiale di supporto CPU della scheda madre). Preferisci schede madri con BIOS Flashback.

5. **Controlla il suffisso della CPU.** Eviti "F/KF" se non hai una GPU dedicata? Ti serve una APU "G" per partire senza scheda grafica? Vuoi una "X3D" per il gaming?

6. **Dimensiona la soluzione di raffreddamento per il consumo energetico reale (PL2/PPT), non il TDP.** Accoppia la CPU scelta con un dissipatore adeguato, o perderai prestazioni a causa del throttling.

7. **Bilancia CPU e GPU** in base alla risoluzione di gioco per evitare colli di bottiglia. Non concentrare tutto il budget su un singolo componente.

8. **Pianifica RAM e PCIe correttamente:** RAM in dual channel con profilo XMP/EXPO attivo, senza sovradimensionare nel 2026; SSD NVMe principale e GPU negli slot direttamente collegati alla CPU, non al chipset.

9. **Affidati a benchmark indipendenti** per i carichi che userai realmente (gaming 1080p per isolare la CPU, Cinebench per la produttività), non ai GHz o alle slide dei produttori.

10. **Considera le generazioni precedenti scontate.** Spesso offrono gran parte delle prestazioni a molto meno, specialmente in una fase di mercato come quella attuale.

---

*Nota finale: i riferimenti a modelli, socket e prezzi riflettono lo stato del mercato a luglio 2026 (Arrow Lake Refresh "200S Plus", Ryzen 9850X3D, AM5 fino al 2029, Intel LGA1954/Nova Lake socket in arrivo, Zen 6/Ryzen 10000 attesi nel 2027, crisi dei prezzi DDR5). Controlla sempre disponibilità e listini aggiornati prima dell'acquisto.*

---

[Tutti i capitoli](./) · [Successivo →](02-scheda-madre.html)
