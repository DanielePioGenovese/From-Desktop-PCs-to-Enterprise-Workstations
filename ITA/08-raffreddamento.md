---
title: "Capitolo 8 - Il raffreddamento"
parent: "Edizione italiana"
nav_order: 8
---

<details open markdown="block">
  <summary>Indice del capitolo</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## 8.1 Perché serve il raffreddamento: la fisica prima del marketing

Ogni componente elettronico attivo in un computer è, dal punto di vista termodinamico, un convertitore di energia elettrica in energia termica. Questa affermazione suona brutale ma è letteralmente vera: un processore non "consuma" corrente per produrre computazione e poi dissipa un po' di calore come effetto collaterale. Un processore assorbe potenza elettrica e la restituisce interamente all'ambiente sotto forma di calore. La computazione — l'informazione, il risultato dell'operazione — non porta via energia. Se una CPU (Central Processing Unit) assorbe 200 watt dalla linea a 12 volt della scheda madre, quei 200 watt diventano 200 watt termici da dissipare. Non 180, non 150: duecento. Il sistema di raffreddamento non è quindi un accessorio che "aiuta" il processore: è l'unica via d'uscita per l'energia che vi entra, e la sua efficacia determina direttamente la frequenza operativa, la stabilità e la durata del componente.

La ragione per cui questo calore è un problema risiede nella fisica del silicio. Un MOSFET (Metal-Oxide-Semiconductor Field-Effect Transistor, il blocco elementare di ogni circuito digitale moderno) commuta tra uno stato di acceso e uno di spento; ad ogni commutazione, dissipa energia per caricare e scaricare le capacità parassite del circuito, e inoltre perde continuamente energia a causa delle correnti di leakage che fluiscono attraverso il dispositivo anche quando dovrebbe essere spento. La corrente di leakage aumenta in modo fortemente non lineare con la temperatura. È qui che inizia il circolo vizioso termico: un chip caldo dissipa più potenza a parità di lavoro, e dissipando più potenza, si scalda ancora di più. Contemporaneamente, la mobilità dei portatori di carica nel silicio diminuisce all'aumentare della temperatura, il che significa che i transistor commutano più lentamente, e per mantenere la stessa frequenza sarebbe necessaria più tensione — il che a sua volta produce più calore. Da qui il fatto osservabile, verificabile su qualsiasi banco di prova, che una CPU ben raffreddata consuma *meno* watt della stessa unità mal raffreddata, pur svolgendo esattamente lo stesso lavoro.

La seconda ragione per cui il calore deve essere controllato è la degradazione a lungo termine. Fenomeni come l'elettromigrazione (la migrazione fisica di atomi metallici nelle tracce di interconnessione, spinta dal flusso di elettroni) e il NBTI (Negative Bias Temperature Instability, uno spostamento progressivo della tensione di soglia dei transistor) hanno una dipendenza esponenziale dalla temperatura, tipicamente descritta da un'equazione di Arrhenius. In termini pratici: un chip che opera a 90 °C invecchia significativamente più velocemente di uno che opera a 70 °C. Questo non significa che una CPU a 90 °C si romperà domani — i produttori dimensionano i limiti proprio per garantire la vita utile attesa anche al limite — ma significa che il margine termico è un margine di longevità e di silenziosità, non solo di prestazioni.

### 8.1.1 Throttling: il freno d'emergenza

Poiché il chip non può fisicamente impedire a sé stesso di scaldarsi, i produttori hanno integrato nel silicio sensori di temperatura distribuiti (DTS, Digital Thermal Sensor) e logiche di protezione. Quando la temperatura del die raggiunge una soglia chiamata **T<sub>jmax</sub>** (maximum junction temperature, ovvero la massima temperatura consentita alla giunzione del transistor), il processore riduce automaticamente frequenza e voltaggio per rientrare nei limiti. Questo fenomeno si chiama **thermal throttling**. Se, nonostante il throttling, la temperatura continua a salire fino a una seconda soglia critica, interviene **THERMTRIP**, un segnale hardware che spegne immediatamente la macchina per prevenire danni permanenti.

Il throttling non è un malfunzionamento: è il sistema che lavora come previsto. Ma è anche un segno inequivocabile che il sistema di raffreddamento è sottodimensionato per il carico. L'utente lo percepisce come improvvisi cali di frame rate nei giochi, tempi di rendering che peggiorano man mano che il lavoro procede (i primi minuti sono veloci, poi rallenta), e benchmark che restituiscono punteggi inferiori alla seconda esecuzione rispetto alla prima.

È importante capire che i processori moderni non hanno più una frequenza "di targa" garantita in ogni condizione. Dal 2015 circa, sia Intel che AMD hanno adottato algoritmi di boost opportunistici — Intel Turbo Boost / Thermal Velocity Boost, AMD Precision Boost 2 e PBO (Precision Boost Overdrive) — che aumentano la frequenza finché non viene raggiunto *uno qualsiasi* dei limiti: limite di corrente, limite di potenza, limite di temperatura. Il primo limite raggiunto è quello che governa. Di conseguenza, il dissipatore non si limita a "impedire il throttling": il dissipatore *determina la frequenza operativa*. Un Ryzen 9 o un Core i9 con un dissipatore mediocre non si romperà; semplicemente, andrà più lento e sarà più rumoroso. Il dissipatore, in questa architettura, è a tutti gli effetti una componente prestazionale.

### 8.1.2 TDP, PL1/PL2, PPT: decodificare i numeri di potenza

Qui dobbiamo essere chirurgici, perché il marketing ha reso questi acronimi quasi inutili se presi alla lettera.

**TDP** sta per *Thermal Design Power*. Storicamente, era la potenza termica, espressa in watt, che il sistema di raffreddamento doveva essere in grado di dissipare affinché il processore operasse alle sue specifiche nominali. Il problema è che nessuno dei due maggiori produttori oggi usa TDP come sinonimo di "consumo massimo".

Per **Intel**, la nomenclatura moderna distingue:
- **PBP** (Processor Base Power), che ha sostituito la vecchia denominazione TDP: è la potenza dissipata quando il processore opera alla sua *frequenza base* con tutti i core attivi. È un valore conservativo e, nell'uso reale, quasi mai rappresentativo.
- **MTP** (Maximum Turbo Power): la potenza massima in condizioni di boost. È questo il dato che conta per il dimensionamento del dissipatore.
- **PL1** e **PL2** (Power Limit 1 e Power Limit 2): PL1 è il limite di potenza sostenuta a lungo termine, PL2 è il limite di picco consentito per un intervallo di tempo chiamato **Tau**. Nelle configurazioni desktop di fascia alta, molti produttori di schede madri impostano di default PL1 = PL2 e Tau infinito, il che significa che il processore opera *sempre* al suo limite di picco. È proprio per questo che un processore dichiarato "125 W" può assorbire 250 W in un rendering, senza che nulla si rompa.

Per **AMD**, sulle piattaforme AM4 e AM5, l'acronimo chiave è **PPT** (Package Power Tracking), il limite di potenza complessiva assorbita dal package. La relazione approssimativa storicamente usata è PPT ≈ TDP × 1,35: un processore dichiarato 105 W ha un PPT attorno ai 142 W, uno dichiarato 170 W arriva a circa 230 W. AMD affianca al PPT altri due limiti, **TDC** (Thermal Design Current, corrente sostenuta erogabile dal VRM in condizioni termiche stabilizzate) ed **EDC** (Electrical Design Current, corrente di picco istantanea); il VRM (Voltage Regulator Module, il modulo di regolazione della tensione della scheda madre, trattato nel capitolo sulle schede madri) è il circuito che eroga fisicamente questa corrente.

La conseguenza pratica è una sola, e andrebbe scolpita nella pietra: **non si sceglie un dissipatore guardando il TDP della CPU; lo si sceglie guardando il consumo reale sotto carico pesante** (MTP per Intel, PPT per AMD, o ancora meglio, le misurazioni indipendenti nelle recensioni). Un utente che compra un dissipatore "adatto fino a 150 W" per un processore "con TDP di 125 W" che in Cinebench assorbe 250 W ha fatto un errore di dimensionamento del 60%.

### 8.1.3 Il concetto che conta davvero: la resistenza termica

Se c'è una grandezza che vale la pena imparare, è questa. Un sistema di raffreddamento può essere descritto da una **resistenza termica**, misurata in gradi Celsius per watt (°C/W). Essa lega la potenza dissipata alla differenza di temperatura tra il chip e l'aria ambiente:

> ΔT = P × R<sub>th</sub>
> oppure: T<sub>CPU</sub> = T<sub>ambiente</sub> + (Potenza in watt × Resistenza termica in °C/W)

Un buon dissipatore a torre a doppia ventola ha una resistenza termica complessiva (dal die all'aria) nell'ordine di 0,10–0,15 °C/W a velocità media delle ventole. Facciamo i conti: con 200 W dissipati e una R<sub>th</sub> di 0,13 °C/W, la differenza termica è di 26 °C. Se l'aria all'interno del case è a 35 °C (non 22, perché all'interno del case fa più caldo che nella stanza), la CPU si assesterà attorno ai 61 °C. Con lo stesso dissipatore e un processore da 280 W, la differenza diventa 36 °C, arrivando a 71 °C. Con un dissipatore economico a 0,25 °C/W e 280 W, la differenza sarebbe di 70 °C, portando a 105 °C, il che significa throttling permanente.

Questo modello, per quanto semplificato, spiega quasi tutto ciò che si osserva nella realtà: spiega perché raddoppiare la superficie del dissipatore non dimezza le temperature (perché la resistenza termica totale è la somma di più resistenze in serie, e quella della pasta termica e dell'IHS non cambia), spiega perché la temperatura ambiente conta uno a uno (ogni grado in più nella stanza è un grado in più sulla CPU), e spiega perché il confronto tra dissipatori va sempre fatto a parità di potenza dissipata e temperatura ambiente.

La catena delle resistenze termiche, dall'hot spot all'aria, è la seguente: die → TIM interno (il materiale che unisce il die all'IHS, saldatura o pasta) → **IHS** (Integrated Heat Spreader, il coperchio metallico saldato sopra il die che vediamo esternamente) → pasta termica esterna → base del dissipatore → heatpipes → alette → aria. Il progettista può migliorare solo gli ultimi anelli; i primi sono decisi da Intel e AMD. Per questo motivo, oltre un certo punto, aggiungere massa e ventole porta a rendimenti decrescenti: si sta ottimizzando un anello della catena mentre il collo di bottiglia è altrove.

### 8.1.4 Temperature target: cosa è normale e cosa no

Uno degli argomenti con più disinformazione. Stabiliamo alcuni punti di riferimento, notando che questi sono valori tipici al momento della scrittura e che i limiti esatti dovrebbero sempre essere verificati nelle specifiche tecniche del proprio modello specifico, poiché cambiano di generazione in generazione.

| Componente | Inattivo (desktop, nessun carico) | Carico di gioco | Carico pesante prolungato (rendering, compilazione) | Limite di intervento (T<sub>jmax</sub>) |
|---|---|---|---|---|
| CPU desktop Intel Core (12a–14a gen, Core Ultra) | 30–45 °C | 55–75 °C | 75–95 °C | ~100 °C |
| CPU AMD Ryzen 5000 (AM4) | 35–45 °C | 60–75 °C | 75–90 °C | ~90 °C |
| CPU AMD Ryzen 7000/9000 (AM5) | 35–50 °C | 60–80 °C | 85–95 °C | ~95 °C (serie 7000 X3D inferiore, ~89 °C; 9800X3D di nuovo a ~95 °C) |
| GPU (die/core, recenti GeForce e Radeon) | 30–45 °C | 65–80 °C | 70–85 °C | ~90 °C core |
| GPU — memoria GDDR6/GDDR6X (T<sub>mem</sub>) | — | 70–95 °C | fino a 100–105 °C | ~105 °C (throttling) |
| GPU — hotspot / junction | — | 75–95 °C | 85–100 °C | ~105–110 °C |

*Nota sui dati volatili: i valori di T<sub>jmax</sub> e le soglie di throttling cambiano con le generazioni e talvolta con gli aggiornamenti del microcodice. I dati in tabella sono indicativi e validi al meglio delle conoscenze disponibili; i dati autorevoli sono quelli forniti dal produttore per la specifica SKU.*

Ci sono due idee sbagliate ricorrenti da sfatare immediatamente.

**Prima idea sbagliata: "la mia CPU è a 90 °C, sta per rompersi."** No. I processori AMD Ryzen serie 7000 e 9000, in particolare, sono progettati per *utilizzare* tutto il budget termico disponibile: l'algoritmo di boost alza la frequenza finché non raggiunge i 95 °C, e poi rimane lì. Un Ryzen 9 che opera costantemente a 95 °C sotto pieno carico con un buon dissipatore non è un sistema malato; è un sistema che fa esattamente ciò per cui è stato progettato. La differenza tra un dissipatore mediocre e uno eccellente, su questi chip, non si riflette nella temperatura (che è sempre 95 °C) ma nella *frequenza sostenuta* e nel *punteggio del benchmark*. Chiunque valuti il proprio dissipatore guardando solo il termometro, su queste piattaforme, sta guardando lo strumento sbagliato.

**Seconda idea sbagliata: "la temperatura inattiva è ciò che conta."** Anche no. La temperatura a riposo è la meno informativa di tutte, perché dipende in modo anomalo dai transienti di boost del sistema operativo (basta un servizio in background che si risveglia per far schizzare la lettura di 15 °C per mezzo secondo) e perché a potenze molto basse, la resistenza termica del sistema è quasi irrilevante. Un dissipatore viene valutato sotto carico costante e prolungato, con almeno 10-15 minuti di stress test, misurando *simultaneamente* temperatura, consumo energetico e frequenza. Senza tutti e tre i numeri, il confronto è privo di significato.

Un terzo elemento deve essere introdotto: la **temperatura dell'aria in ingresso**. Nessun dissipatore ad aria o AIO può portare la CPU al di sotto della temperatura dell'aria che vi entra; questo è un limite termodinamico, non un difetto. Se l'aria all'interno del case è a 40 °C perché il flusso d'aria è mal progettato, il dissipatore parte da 40 °C e aggiunge il suo ΔT. Questo è il motivo per cui il capitolo sul case e la ventilazione e questo capitolo sono, in realtà, un unico capitolo diviso in due: **un eccellente dissipatore in un case soffocato si comporta come un dissipatore mediocre in un case ben ventilato.**

---

## 8.2 Raffreddamento ad aria

### 8.2.1 Descrizione e funzionamento: come funziona realmente una torre

Un dissipatore ad aria moderno è composto da quattro elementi funzionali, e vale la pena capirli uno ad uno perché la qualità del prodotto sta tutta nei dettagli di come sono realizzati.

**La base (cold plate).** È la piastra, quasi sempre in rame (a volte nichelata per estetica e resistenza all'ossidazione), che poggia sull'IHS della CPU. Il suo compito è raccogliere il calore da una zona piccola e concentrata. Due filosofie costruttive si contendono questo campo: la **base solida** (un blocco di rame lavorato, in cui vengono inserite e saldate le heatpipe) e il **direct-touch** (le heatpipe sono appiattite e toccano direttamente l'IHS, senza piastra intermedia). Il direct-touch elimina un'interfaccia termica ma lascia scanalature di alluminio o rame tra i tubi, con una distribuzione del calore meno uniforme; la base solida aggiunge un'interfaccia ma distribuisce meglio il calore. In pratica, entrambe le soluzioni ben eseguite danno risultati equivalenti, mentre entrambe le soluzioni mal eseguite (base concava, saldatura scadente, finitura grezza) danno risultati pessimi. Un dettaglio spesso trascurato è la **planarità e convessità** della base: molti produttori realizzano la base leggermente convessa per compensare la deformazione dell'IHS sotto la pressione del sistema di montaggio.

**Le heatpipe.** Sono il cuore tecnologico e la ragione per cui una torre da 1 kg dissipa più calore di un blocco di rame massiccio da 3 kg. Una heatpipe è un tubo di rame sigillato, parzialmente sottovuoto, e contenente una piccola quantità di fluido di lavoro (tipicamente acqua). All'interno della parete, c'è una struttura capillare (**wick**): polvere di rame sinterizzata, rete metallica, o scanalature longitudinali. Il funzionamento è un ciclo chiuso di cambiamento di fase. All'estremità calda (a contatto con la CPU), il fluido evapora, assorbendo il **calore latente di vaporizzazione** — una quantità enorme di energia rispetto a quella necessaria per scaldare lo stesso fluido di pochi gradi. Il vapore, che occupa un volume molto maggiore, si espande e si muove molto velocemente verso l'estremità fredda (immersa nelle alette), dove condensa, rilasciando il calore latente. Il liquido condensato viene poi riportato indietro dall'azione capillare del wick. Dato che il vuoto parziale abbassa il punto di ebollizione dell'acqua a poche decine di gradi, il ciclo funziona anche a normali temperature operative.

Il risultato è che una heatpipe ha una **conduttività termica effettiva** ordini di grandezza superiore a quella del rame solido (parliamo di migliaia di W/m·K equivalenti, contro circa 400 W/m·K per il rame). Questo spiega perché: (a) i dissipatori seri hanno 6, 7 o 8 heatpipe e non una sola; (b) il numero di heatpipe è un indicatore grezzo ma reale della capacità di trasporto; (c) esiste un limite oltre il quale la heatpipe "si prosciuga": se la potenza è troppo alta, il liquido evapora più velocemente di quanto il wick riesca a riportarlo indietro, e la conduttività crolla improvvisamente. Questa è una delle ragioni per cui i dissipatori ad aria hanno un tetto di potenza abbastanza chiaro.

Un corollario poco noto: **l'orientamento conta**, anche se meno di quanto si tema. Un buon heatpipe a wick sinterizzato funziona in qualsiasi posizione, anche contro gravità, ma nella posizione "peggiore" (evaporatore in alto, condensatore in basso, con la gravità che si oppone al ritorno del liquido) può perdere qualche punto percentuale di efficienza. Nei case standard con scheda madre verticale, la torre lavora in orizzontale, che è una posizione neutra; nei case con scheda madre orizzontale (alcuni HTPC, alcuni bench case), è consigliabile controllare le istruzioni del produttore.

**Il pacco alettato.** È la superficie di scambio termico con l'aria: decine di sottili alette in alluminio (raramente rame, più efficiente ma molto più pesante e costoso), infilate sugli heatpipe. Qui, il parametro chiave è la **densità delle alette**, spesso espressa in FPI (Fins Per Inch). Alette dense offrono più superficie ma creano più resistenza al flusso d'aria: richiedono ventole ad alta **pressione statica** e rendono male con ventole lente. Alette sparse offrono meno superficie ma respirano bene anche a basse velocità: sono la scelta per i dissipatori orientati al silenzio. Non esiste una singola impostazione "migliore": esiste un accoppiamento coerente tra densità delle alette e curva della ventola. Un ottimo dissipatore ha un pacco alettato *progettato insieme* alla sua ventola.

Il modo in cui le alette sono attaccate agli heatpipe è un differenziatore di qualità sottovalutato: nei buoni prodotti, sono **saldate** o comunque unite con un legame metallurgico; nei prodotti economici, sono solo pressate meccanicamente, con un'interfaccia aria-metallo che introduce resistenza termica.

**La ventola.** Fornisce il flusso d'aria che allontana il calore dalle alette. I due parametri fondamentali sono la **portata d'aria** (CFM, Cubic Feet per Minute — o m³/h) e la **pressione statica** (mm H₂O, millimetri di colonna d'acqua). La portata d'aria indica quanta aria muove in assenza di ostacoli; la pressione statica indica quanto bene riesce a spingere l'aria *attraverso* un ostacolo. Un pacco alettato è un ostacolo. Discuteremo questo in dettaglio nella sezione 8.7, poiché l'argomento è comune sia al raffreddamento ad aria che a liquido.

### 8.2.2 Formati: Come leggere le dimensioni

I dissipatori ad aria si dividono in tre famiglie principali, distinte dalla geometria del flusso.

**Low profile.** Il pacco alettato è orizzontale, sopra il socket, e la ventola soffia verso il basso (**top-flow**). Le altezze tipiche vanno da 25 a 70 mm. Sono stati progettati per case Mini-ITX, HTPC, sistemi office compatti e workstation slim. Un vantaggio collaterale è che il flusso verso il basso raffredda anche i VRM della scheda madre e i moduli RAM, cosa che un dissipatore a torre non fa. Lo svantaggio è che la superficie disponibile è piccola e la ventola, essendo sottile e piccola, deve girare velocemente: questi dissipatori sono rumorosi in proporzione alla potenza dissipata. Riferimenti tipici: la serie **Noctua NH-L9** (circa 37 mm, adatta a CPU fino a circa 65 W), il **Noctua NH-L12S** (circa 70 mm, molto più capace), la famiglia **Thermalright AXP90** e **AXP120**, i dissipatori **ID-Cooling IS** per Mini-ITX.

**Single tower.** Un singolo pacco alettato verticale, con una ventola davanti (configurazione push) e talvolta una seconda dietro (push-pull). Altezze tipiche 120–160 mm. Questo è il formato dominante nella fascia media: un buon compromesso tra ingombro, prestazioni e prezzo. Riferimenti: **Thermalright Assassin X120 Refined**, **Arctic Freezer 36**, **DeepCool AK400**, **be quiet! Pure Rock 2**, **Noctua NH-U12S / NH-U12A**

In termini di posizionamento del marchio, si possono tracciare alcune linee chiare. **Thermalright** ha stravolto il mercato negli ultimi anni offrendo prestazioni di fascia alta a prezzi di fascia bassa; il compromesso è una minore qualità percepita dei materiali e dei kit di montaggio, e un supporto post-vendita meno strutturato in Europa. **Noctua** rappresenta l'estremo opposto: prezzi elevati, ma sistemi di montaggio eccellenti (SecuFirm2), alcune delle migliori ventole sul mercato (NF-A e NF-P), documentazione impeccabile, una lunga garanzia e — un dettaglio che vale il suo peso in oro — **kit di adattamento gratuiti o quasi gratuiti per i nuovi socket**, il che significa che un dissipatore Noctua può sopravvivere a più generazioni di piattaforme. **be quiet!** punta sul silenzio, con soluzioni acusticamente raffinate e un'estetica nero opaco molto sobria. **DeepCool** e **Arctic** occupano la fascia media con prodotti solidi e prezzi competitivi; Arctic, in particolare, è aggressiva sul prezzo e generosa con la sua garanzia.

*Nota di contesto commerciale, potenzialmente volatile: la disponibilità di alcuni marchi può variare a causa di ragioni normative o di distribuzione regionale. Prima di ordinare, verificare disponibilità e supporto nel proprio mercato.*

### 8.2.4 Pro e contro del raffreddamento ad aria, senza retorica

**Pro.** L'affidabilità è il punto decisivo: un dissipatore ad aria non ha parti soggette a usura eccetto il cuscinetto della ventola, che si sostituisce in due minuti e costa 15-20 euro. Non c'è liquido, non ci sono guarnizioni, nulla che possa evaporare, gelificare o perdere. La vita utile pratica è quella del cuscinetto, e con buoni cuscinetti (rifle bearing, FDB — Fluid Dynamic Bearing, SSO2 di Noctua) parliamo di 100.000-150.000 ore MTTF (Mean Time To Failure). In concreto: un buon dissipatore ad aria può accompagnare tre o quattro build successive. Il costo è imbattibile: 40 euro oggi comprano prestazioni che dieci anni fa richiedevano 100 euro. La manutenzione è zero, esclusa la periodica rimozione della polvere. E il **rumore**: a parità di prestazioni, una torre ben progettata è quasi sempre più silenziosa di un AIO, perché non ha la pompa — che è una fonte di rumore continuo, spesso a frequenze fastidiose (ronzio a bassa frequenza, gorgoglii).

**Contro.** L'ingombro è il tallone d'Achille. Un dissipatore a doppia torre alto 165 mm e pesante 1,5 kg crea tre categorie di problemi: (1) potrebbe non entrare nel case; (2) potrebbe interferire con i moduli RAM, specialmente quelli con dissipatori alti; (3) potrebbe interferire con il primo slot PCIe (Peripheral Component Interconnect Express, il bus di espansione di cui si è parlato nel capitolo sulla scheda madre) o con i dissipatori degli SSD M.2. Il peso, inoltre, esercita una coppia sulla scheda madre: durante il trasporto, è consigliabile rimuovere i dissipatori più pesanti o trasportare il case in orizzontale. Infine, l'estetica: una torre nera copre metà della scheda madre e nasconde la RAM; per chi costruisce un sistema con fianco in vetro e illuminazione, è un sacrificio. Va detto onestamente che è un sacrificio *estetico*, non tecnico.

### 8.2.5 Compatibilità: le tre misure da controllare sempre

È qui che si annidano la maggior parte degli errori di acquisto. Prima di comprare un dissipatore ad aria, tre controlli sono obbligatori.

**1. Il socket.** Il kit del dissipatore deve includere il sistema di montaggio per il socket della vostra scheda madre. Socket rilevanti oggi: **LGA1700** (Intel 12a/13a/14a generazione), **LGA1851** (Intel Core Ultra 200 series desktop), **LGA1200** (10a/11a gen, legacy), **AM4** (Ryzen 1000–5000), **AM5** (Ryzen 7000/8000/9000). Due note pratiche estremamente preziose: AM5 ha mantenuto lo **stesso schema di fori di montaggio di AM4**, quindi quasi tutti i dissipatori AM4 sono meccanicamente compatibili con AM5. E LGA1851 ha mantenuto lo schema di fori 78 × 78 mm di LGA1700, quindi anche lì la compatibilità è ampia — ma **è comunque obbligatorio controllare il sito web del produttore del dissipatore**, perché l'altezza dell'ILM (Independent Loading Mechanism, il meccanismo di ritenzione del processore) e lo spessore dell'IHS possono cambiare, e alcuni kit richiedono comunque un backplate diverso.

**2. L'altezza massima del dissipatore consentita dal case.** Ogni case specifica "CPU cooler clearance" o "max CPU cooler height", tipicamente 155–175 mm nei mid-tower. Questo dovrebbe essere confrontato con l'altezza dichiarata del dissipatore. **Non lasciare margini di 1–2 mm**: le tolleranze di fabbricazione, gli spessori dei backplate e la posizione del socket sulla scheda madre introducono variazioni. Se il case dichiara 165 mm e il dissipatore misura 165 mm, è un azzardo. Se il case dichiara 170 e il dissipatore 165, siete al sicuro.

**3. La distanza dalla RAM.** Questo è il vero killer silenzioso. Nei dissipatori a torre, la ventola anteriore sporge verso gli slot di memoria. Il produttore di solito dichiara due valori: l'altezza massima dei moduli RAM con la ventola in posizione standard (spesso 32–35 mm), e l'altezza consentita alzando la ventola (a costo di aumentare l'altezza complessiva del dissipatore, ricadendo al punto 2). I moduli di memoria "nudi" (senza dissipatori, o con dissipatori a basso profilo) misurano circa 31–33 mm; molti moduli gaming con dissipatori prominenti e RGB (Red-Green-Blue, illuminazione LED indirizzabile) raggiungono i 42–50 mm. Un Noctua NH-D15 con RAM alta 48 mm semplicemente non ci starà, o vi costringerà ad alzare la ventola oltre i limiti del case, o a rimuoverla del tutto, perdendo prestazioni. **La regola pratica è: se avete già scelto un grande dissipatore a torre, acquistate RAM a basso profilo.**

Un quarto controllo, meno frequente ma reale: alcuni dissipatori a torre molto larghi possono coprire parzialmente il primo slot PCIe x16 (dove va la scheda grafica) o i dissipatori M.2 sopra il primo slot. Questo problema si verifica spesso con i case Mini-ITX e le schede mATX compatte. Tutti i produttori seri pubblicano liste di compatibilità e persino modelli 3D scaricabili; usarli non è essere pedanti, è buona pratica.

### 8.2.6 Errori tipici dell'acquirente

Il primo e più costoso: **dimensionare il dissipatore in base al TDP dichiarato invece del consumo energetico effettivo**. Ne abbiamo discusso in 8.1.2; è l'errore che porta ad acquistare un dissipatore da 40 euro per un Core i7 K che assorbe 250W sotto carico.

Il secondo: **ignorare la distanza dalla RAM**, acquistando prima una memoria "bella" e poi scoprendo che il dissipatore non ci sta.

Il terzo: **credere che il numero di heatpipe da solo determini le prestazioni**. Sei heatpipe ben saldate a una fitta pila di alette e ben ventilate battono otto heatpipe mal fatte. Il numero è un'indicazione, non una prova.

Il quarto: **usare il dissipatore stock su una CPU di fascia alta**. I dissipatori inclusi nella confezione (AMD Wraith Stealth/Spire, Intel Laminar coolers) sono progettati per il minimo indispensabile, ovvero per far funzionare la CPU senza guasti. Su un Ryzen 5 o un Core i5 non-K sono accettabili; su qualsiasi cosa al di sopra, sono un collo di bottiglia. Va anche notato che, alla data di scrittura, **la maggior parte dei processori di fascia alta viene venduta senza alcun dissipatore incluso**: non è un'opzione, è un componente obbligatorio da mettere in conto.

Il quinto: **non montare correttamente la piastra posteriore (backplate)**, o stringere le viti in modo non uniforme. Torneremo sulla stretta nella sezione dedicata alla pasta termica.

Il sesto, specifico di Intel: **ignorare il problema della flessione dell'ILM su LGA1700**. Il meccanismo di ritenzione di questo socket esercita una pressione che tende a piegare leggermente l'IHS del processore, creando una concavità al centro e riducendo il contatto con la base del dissipatore. Questo fenomeno costa tipicamente 3–8 °C. Esistono dei **contact frame** aftermarket (Thermalright, Thermal Grizzly, e in alcuni casi kit dei produttori di schede madri) che sostituiscono l'ILM originale e distribuiscono la pressione in modo più uniforme. È una modifica da 15–20 euro che recupera diversi gradi, ma richiede la rimozione del meccanismo originale — un'operazione reversibile che va fatta con attenzione. *Verificare l'applicabilità al proprio socket e generazione, in quanto il problema è specifico e non universale.*

### 8.2.7 Raccomandazioni per fascia di prezzo (aria)

**Entry (CPU fino a ~100W reali: Ryzen 5 non-X, Core i5 non-K, APU).** Una singola torre da 30–40 euro è più che sufficiente. Arctic Freezer 36, Thermalright Assassin X120, DeepCool AK400. In molti casi, il dissipatore incluso nella scatola è sufficiente, ma spendere 30 euro compra un enorme guadagno acustico: è probabilmente il miglior rapporto qualità-prezzo dell'intero PC.

**Mid (CPU fino a ~180–220W reali: Ryzen 7, Ryzen 9 non overcloccati, Core i5 K, Core i7).** Doppia torre economica: **Thermalright Peerless Assassin 120 SE o Phantom Spirit 120 SE**. Sono, senza esagerare, il consiglio più solido che si possa dare a chiunque nel 2025–2026: prestazioni entro 2–4 °C dai modelli di punta a un terzo del prezzo. Alternativa di maggiore qualità costruttiva: DeepCool AK620.

**High-end (CPU oltre 250W: Ryzen 9 in PBO, Core i9 K, workstation).** Qui l'aria raggiunge il suo limite fisico. Le opzioni serie sono Noctua NH-D15 G2, be quiet! Dark Rock Elite, DeepCool Assassin IV. Questi vanno scelti sapendo che su un Core i9 top di gamma sotto pieno carico, anche il miglior dissipatore ad aria lascerà la CPU parzialmente in throttling: questo non è un difetto del dissipatore, è che la CPU dissipa più calore di quanto l'aria possa portare via in quello spazio. La scelta consapevole a questo punto è: accettare qualche punto percentuale in meno di prestazioni in cambio di affidabilità e silenzio (aria), oppure passare al raffreddamento a liquido.

---

## 8.3 Raffreddamento a Liquido AIO

### 8.3.1 Descrizione e Funzione

**AIO** sta per *All-In-One*: un sistema di raffreddamento a liquido pre-assemblato, pre-riempito e sigillato in fabbrica. L'utente si limita a montarlo; non viene aperto, ricaricato o manutenuto.

Il circuito è concettualmente semplice. Un **waterblock** (o cold plate) poggia sull'IHS della CPU: è un blocco di rame la cui superficie inferiore è liscia (a contatto con la pasta termica) e la cui superficie superiore è intagliata in una fitta griglia di micro-alette, attraverso le quali il liquido è costretto a passare. Il liquido assorbe calore, viene spinto da una **pompa** (nella stragrande maggioranza degli AIO, la pompa è integrata nel blocco stesso, sopra il waterblock; in alcuni modelli, è integrata nel radiatore) attraverso un **tubo** flessibile al **radiatore**, che è uno scambiatore di calore liquido-aria: canali piatti attraverso cui scorre il liquido, attraversati da una pila di alette di alluminio. Una o più **ventole** soffiano aria attraverso il radiatore, che dissipa il calore nell'ambiente. Il liquido raffreddato ritorna al waterblock attraverso il secondo tubo. È un circuito chiuso.

Il punto che quasi tutti fraintendono è **cosa fa effettivamente il liquido**. Il liquido non "raffredda" nel senso che non produce freddo: è semplicemente un *mezzo di trasporto del calore* molto efficace perché l'acqua ha una capacità termica specifica molto elevata (circa 4,18 kJ/kg·K) e perché una pompa può muoverla continuamente. In un dissipatore ad aria, il calore deve essere trasportato dalla base alle alette tramite heatpipe, e le alette devono essere fisicamente lì, sopra il socket, in uno spazio limitato. In un AIO, il calore viene *portato altrove*, in un punto del case dove c'è spazio per una superficie di scambio molto più grande e più ventole. **Il vantaggio di un AIO non è il liquido: è che il liquido permette di posizionare la superficie di scambio dove c'è spazio.**

C'è un secondo vantaggio, meno ovvio ma prezioso: la **massa termica**. Il liquido nel circuito (circa 150-400 ml) agisce come un volano termico: assorbe brevi picchi di potenza senza che la temperatura salga immediatamente. Nei carichi a raffica (gaming, dove la CPU alterna picchi e pause), questo si traduce in temperature di picco più basse e ventole che accelerano più lentamente, con un comportamento acustico più stabile. Questo è uno dei motivi per cui un AIO "sembra" più silenzioso nel gaming anche quando non è sotto carico costante.

### 8.3.2 Dimensioni del radiatore: quale per quale CPU

La capacità di dissipazione di un AIO è determinata quasi interamente dalla **superficie del radiatore** e dal flusso d'aria attraverso di essa. La pompa e il waterblock contano molto meno di quanto suggerisca il marketing: le differenze tra pompe di buona qualità sono nell'ordine di 1-2 °C, mentre tra un radiatore da 240 mm e uno da 360 mm ci sono 5-10 °C.

Le dimensioni sono espresse dalla lunghezza approssimativa: **120** (una ventola da 120 mm), **240** (due ventole da 120 mm), **280** (due ventole da 140 mm), **360** (tre ventole da 120 mm), **420** (tre ventole da 140 mm). Esistono anche 140, 320 e 480 in mercati di nicchia. Nota: un 280 ha una superficie *maggiore* di un 240 (le ventole da 140 mm hanno circa il 36% in più di area frontale), ed è spesso paragonabile a un 360 pur essendo più corto, il che lo rende una scelta intelligente nei case che lo supportano — ma i case compatibili con radiatori da 280 mm sono meno numerosi.

Una **spessore** del radiatore che conta: lo standard è 27 mm, ma alcuni produttori (Arctic con la serie Liquid Freezer, in particolare) usano radiatori da 38 mm, che offrono più massa e superficie a parità di lunghezza. Il rovescio della medaglia: maggiore spessore richiede più pressione statica dalle ventole e può causare problemi di spazio con lo stack di alette VRM sulla scheda madre o con la RAM, se montato sopra un case stretto. **Controllare sempre lo spessore massimo supportato dal case, sommando radiatore + ventole.**

| Radiatore | Superficie Relativa | CPU adatte (consumo energetico reale sotto carico) | Note |
|---|---|---|---|
| 120 mm | 1× | fino a ~100 W | Spesso peggio di una torre da 40€ allo stesso prezzo. Sconsigliato a meno che lo spazio non sia limitato |
| 240 mm | 2× | fino a ~180 W | Fascia media. Adatto per Ryzen 7, Core i5 K/i7 |
| 280 mm | ~2.7× | fino a ~230 W | Ottimo compromesso, se il case lo accetta |
| 360 mm | 3× | fino a ~280 W | Lo standard de facto per l'alta gamma |
| 420 mm | ~4× | oltre 300 W | Per Core i9 di fascia alta, Ryzen 9 in PBO, Threadripper. Case grandi obbligatori |

**Scomoda verità da dire ad alta voce:** un AIO economico da 120 o 240 mm è quasi sempre una scelta peggiore di un dissipatore a doppia torre da 40€, sotto ogni aspetto — prestazioni, rumore, affidabilità, prezzo. Ha senso solo se lo spazio impedisce fisicamente una torre (case sottili, alcuni ITX) o se l'estetica è la priorità. Il raffreddamento a liquido diventa oggettivamente superiore all'aria **da 280 mm in su, e su CPU che dissipano molto calore**.

### 8.3.3 Posizionamento del radiatore e la regola dell'aria nella pompa

Questo è il paragrafo che, se ignorato, porta ad AIO rumorosi e AIO prematuramente morti. Va capito, non memorizzato.

**Il fatto fisico.** Un AIO, per quanto sigillato, non è ermetico indefinitamente. I tubi di gomma sono *permeabili* al vapore acqueo: molecola per molecola, anno dopo anno, una piccola quantità di liquido evapora attraverso la parete del tubo. Inoltre, alcune reazioni chimiche interne (e il fatto che il riempimento non è mai al 100% durante la produzione) generano una piccola quantità di gas. Il risultato è che **in ogni AIO, prima o poi, si forma una bolla d'aria**. È inevitabile e anticipato dai progettisti.

**La conseguenza.** L'aria, essendo meno densa del liquido, si raccoglie sempre nel **punto più alto del circuito**. Se il punto più alto del circuito è la pompa, la pompa aspira aria invece di liquido. Questo produce tre effetti, in ordine di gravità crescente: (1) un rumore evidente e fastidioso, un gorgoglio o un ronzio ("gurgling"); (2) una perdita di efficienza di pompaggio, perché l'aria non trasporta calore; (3) **cavitazione a secco** e usura accelerata del cuscinetto e della girante, perché la pompa funziona senza il liquido che la lubrifica e la raffredda. Una pompa che funziona a secco per settimane morirà.

**La regola pratica.** Il punto più alto del circuito **non deve mai essere la pompa** (cioè il blocco sulla CPU). Da questo, seguono le configurazioni corrette:

- **Radiatore sulla parte superiore del case** (in alto), con i tubi che escono verso il basso o lateralmente: configurazione ideale. Il punto più alto è la parte superiore del radiatore, che è progettata per contenere l'aria (spesso il canale superiore del radiatore funge da piccola camera d'aria). Il liquido raggiunge sempre la pompa pieno.
- **Radiatore sulla parte anteriore del case, con i tubi in basso**: configurazione corretta. Se il radiatore è nella parte anteriore e i tubi escono dalla *parte inferiore* del radiatore, l'aria si raccoglie nella parte superiore del radiatore, lontano dalla pompa. Questa è la configurazione da scegliere quando si monta un AIO nella parte anteriore.
- **Radiatore sulla parte anteriore con i tubi in alto**: configurazione **sbagliata e da evitare**, perché è qui che l'aria dal radiatore può migrare nei tubi e finire nella pompa. È l'errore di installazione più comune ed è ciò che genera la famosa lamentela "il mio AIO fa un rumore di gorgoglio".
- **Radiatore nella parte inferiore del case**: il peggiore in assoluto. Il punto più basso del circuito diventa il radiatore, e il punto più alto diventa la pompa. Tutta l'aria nel circuito finisce nella pompa. Non farlo mai, anche se il case lo consente meccanicamente.

Se la geometria del case impone un montaggio non ideale, c'è un palliativo: dopo l'assemblaggio, **inclinare il case in varie posizioni per un paio di minuti con il sistema acceso**, per far migrare la bolla d'aria verso il radiatore. Questa non è una soluzione permanente ma spesso risolve il rumore iniziale.

**Una nota finale sull'orientamento:** alcuni produttori (Arctic, Corsair, Lian Li) offrono blocchi con loghi o display ruotabili, in modo che l'estetica non imponga un montaggio termicamente scorretto. Se il tuo AIO non lo consente e desideri comunque un orientamento preciso dei tubi, è meglio scegliere un altro modello piuttosto che compromettere la fisica per un logo dritto.

### 8.3.4 Direzione dell'aria: radiatore come immissione o espulsione?

Una domanda posta continuamente e quasi sempre a cui si risponde in modo errato. Le due opzioni:

**Radiatore come immissione**: le ventole soffiano aria fresca *dall'esterno* attraverso il radiatore, all'interno del case. Vantaggio: il radiatore riceve l'aria più fredda disponibile (temperatura ambiente), quindi la CPU è più fresca — tipicamente 3-6 °C in meno. Svantaggio: l'aria che entra nel case dopo essere passata attraverso il radiatore è preriscaldata, quindi GPU, VRM, SSD e RAM funzionano qualche grado più caldi.

**Radiatore come espulsione**: le ventole aspirano aria dal case attraverso il radiatore e la espellono. Vantaggio: il resto del case respira aria fresca. Svantaggio: il radiatore riceve aria già riscaldata dalla GPU, quindi la CPU è più calda.

Non esiste una risposta universale; esiste un criterio: **dare priorità al componente più critico**. Se hai un Core i9 in un sistema di rendering e una GPU modesta, ha senso posizionare il radiatore come immissione nella parte anteriore. Se hai una GPU di fascia alta da 400 W e una CPU media in un sistema di gioco, ha senso posizionare il radiatore come espulsione in alto e lasciare che la parte anteriore porti aria fresca alla GPU. La configurazione più comune e bilanciata nei case moderni è: **radiatore in alto come espulsione, ventole anteriori come immissione**. Con un avvertimento: se il radiatore è un'immissione dalla parte anteriore, deve essere posizionato un filtro antipolvere davanti ad esso, perché un radiatore intasato di polvere perde drasticamente efficienza, e pulirlo è molto più difficile che pulire una pila di alette di un dissipatore a torre.

### 8.3.5 Marche e Modelli

*Panoramica basata sulle migliori conoscenze disponibili; il segmento AIO è in rapida evoluzione e le revisioni cambiano spesso, quindi controlla sempre le recensioni per la revisione specifica in vendita.*

**Arctic Liquid Freezer (serie II e III).** Il benchmark de facto per il rapporto prezzo/prestazioni, e per diverse revisioni, anche il benchmark di prestazioni assolute. Caratteristiche distintive: radiatore da 38 mm (più spesso dello standard), **una ventola VRM da 40 mm integrata nel blocco** che raffredda i regolatori di tensione della scheda madre (cosa che nessun altro AIO fa e che gli AIO tradizionali, a differenza dei dissipatori a basso profilo, rimuovono), pompa PWM regolabile, prezzi aggressivi. Contro: estetica spartana, cavo singolo un po' scomodo, lo spessore del radiatore può causare problemi di spazio in alcuni casi.

**Corsair (iCUE Link Titan, serie H).** Ecosistema molto raffinato, software esteso, alta qualità costruttiva, prezzi elevati. Il sistema iCUE Link riduce il cablaggio con un singolo connettore a margherita, che è un vero vantaggio pratico ma ti blocca nell'ecosistema.

**Lian Li Galahad II.** Prestazioni molto competitive, estetica di fascia alta, illuminazione raffinata, prezzi medio-alti. Una buona alternativa a Corsair per chi desidera l'estetica senza il blocco software.

**NZXT Kraken (ed Elite).** Il punto di forza è l'ampio display LCD personalizzabile sul blocco; le prestazioni termiche sono buone ma raramente di prim'ordine, e si paga un premio per l'estetica e il software (CAM).

**DeepCool (serie LT, LE, Mystique).** Ottimo rapporto qualità-prezzo, buone prestazioni, estetica raffinata. *Nota: la disponibilità di questo marchio può variare a seconda del mercato; verifica la disponibilità locale.*

**be quiet! (Silent Loop, Pure Loop).** Focalizzati sul funzionamento silenzioso, con pompe particolarmente discrete. Le prestazioni sono raramente di prim'ordine, ma acusticamente eccellenti.

Tecnologicamente, vale la pena notare che per molti anni, gran parte degli AIO sul mercato, indipendentemente dal marchio, utilizzava pompe prodotte da **Asetek** su licenza (o da CoolIT, l'altro grande OEM). Ciò significa che il "cuore" di AIO apparentemente diversi era spesso identico, e le vere differenze risiedevano nel radiatore, nelle ventole, nei tubi e nel software. Questo è un motivo aggiuntivo per non pagare un premio eccessivo per il marchio e per concentrarsi sui dati misurati: dimensioni del radiatore, prestazioni certificate da recensioni indipendenti e rumore misurato allo stesso livello di dissipazione.

### 8.3.6 Pro e Contro degli AIO

**Pro.** Per le CPU di fascia alta, un AIO da 360/420 mm supera il raffreddamento ad aria: non di poco, in alcuni casi di 8-12 °C sotto carico pesante, con conseguente maggiore frequenza sostenuta. Libera spazio intorno al socket: nessun conflitto con la RAM (qualsiasi memoria può essere installata, anche con i dissipatori più alti), nessun conflitto con il primo slot PCIe, nessun peso appeso alla scheda madre. Consente build compatte con CPU potenti, poiché il radiatore va ovunque ci sia spazio. E, francamente, è esteticamente più pulito: la scheda madre rimane visibile, l'illuminazione è visibile e i sistemi con pannello laterale in vetro hanno senso.

**Contro.** Innanzitutto, il costo: un AIO da 360mm decente parte da 90-110 euro e sale rapidamente. La **pompa è un singolo punto di guasto**: se si ferma, la temperatura della CPU sale verticalmente in pochi secondi e il sistema va in throttling o si spegne; a differenza di una ventola, non c'è degradazione graduale e nessuna ridondanza. Quando si installa un AIO, è buona norma collegare la pompa al connettore **AIO_PUMP** o **CPU_FAN** della scheda madre e impostare l'allarme di velocità zero, in modo che il BIOS blocchi l'avvio se la pompa non è in funzione. La **durata** è limitata: è tipicamente stimata in **5-7 anni**, vincolata dall'evaporazione del liquido attraverso i tubi, dall'usura del cuscinetto della pompa e dal potenziale accumulo di detriti nei microcanali del waterblock. Un dissipatore ad aria, nello stesso periodo, necessita solo di una nuova ventola da 20 euro. Infine, il rumore: la pompa aggiunge una fonte acustica continua che non esiste nel raffreddamento ad aria, e non tutte le pompe sono silenziose a basse velocità.

### 8.3.7 Errori tipici dell'acquirente (AIO)

Acquistare un **AIO da 240mm economico invece di un dissipatore a torre da 40 euro**, credendo che "liquido = migliore". Non è così: si paga di più per prestazioni uguali o inferiori, con una pompa aggiuntiva che può rompersi.

**Montare il radiatore nella parte inferiore del case** o **nella parte anteriore con i tubi in alto**, per motivi di cablaggio o estetici. Vedere 8.3.3.

**Non controllare lo spazio disponibile**: radiatore + ventole possono superare i 55-65 mm di spessore. Sulla parte superiore di molti case, questo interferisce con i dissipatori VRM della scheda madre o con la RAM. La specifica del case di solito indica un "clearance" massimo per la parte superiore; deve essere rispettato.

**Collegare la pompa a un connettore generico per ventole con una curva PWM aggressiva.** La pompa non è una ventola: farla funzionare al 40% per "silenziare" riduce il flusso e le prestazioni. La maggior parte delle pompe dovrebbe essere mantenuta a una velocità fissa ed elevata (spesso 100%); alcune moderne accettano PWM, e il produttore specifica come. Leggere il manuale.

**Trascurare il periodo di rodaggio.** Nei primi giorni, un AIO può produrre un leggero gorgoglio che si attenua man mano che le bolle d'aria si depositano. Se il rumore persiste dopo una settimana, l'installazione è errata.

### 8.3.8 Raccomandazioni di fascia economica (AIO a liquido)

**Entry.** Nessuno. Se il budget è basso, prendete un dissipatore a torre. Un AIO economico è un cattivo affare.

**Mid (CPU fino a ~200W, si desidera estetica o si hanno vincoli di RAM alta).** Un buon marchio da 240 o 280mm. Arctic Liquid Freezer III 240/280 è la scelta razionale.

**High-end (CPU oltre 250W).** Un 360mm di fascia alta, o un 420mm se il case lo consente. Arctic Liquid Freezer III 360/420, Corsair, Lian Li Galahad II 360. Per un Core i9 o un Threadripper, qui il raffreddamento a liquido non è una scelta estetica ma una necessità tecnica.

---

## 8.4 Il Custom Loop: per completezza, e per capire perché quasi nessuno dovrebbe farlo

Un **custom loop** è un circuito di raffreddamento a liquido assemblato dall'utente componente per componente. Nasce dal desiderio di superare i limiti degli AIO (radiatori più grandi, radiatori multipli e soprattutto la possibilità di raffreddare a liquido anche la GPU), e dalla passione per l'oggetto stesso.

### 8.4.1 I Componenti

**Pompa e Serbatoio.** La pompa muove il liquido; il serbatoio è un contenitore che facilita il riempimento, raccoglie l'aria e fornisce un margine di liquido. Nella pratica moderna, pompe e serbatoi sono quasi sempre acquistati come un **combo** integrato. Le due piattaforme di pompe dominanti sono la **D5** e la **DDC**, entrambe derivate da design industriali (rispettivamente Laing/Xylem e Laing). La **D5** ha un'alta portata e una pressione di prevalenza media (la capacità di superare la resistenza del circuito), è più grande, molto silenziosa e affidabile. La **DDC** ha una pressione di prevalenza più alta e una portata inferiore, è più compatta ma funziona più calda ed è tipicamente più rumorosa. In un loop lungo, con più radiatori e blocchi molto restrittivi, la DDC ha un vantaggio; nella maggior parte dei loop domestici, la D5 è la scelta sensata.

**Waterblock CPU.** Un blocco di rame nichelato con una base a microcanali (larghezza dei canali dell'ordine dei decimi di millimetro), una parte superiore in acrilico o acetale e raccordi filettati. Le differenze di prestazioni tra buoni waterblock CPU sono modeste (2–4 °C); la qualità del montaggio e la pressione uniforme sono più importanti.

**Waterblock GPU — questo deve essere spiegato bene, perché è qui che le persone si mettono nei guai.**

Una scheda grafica standard viene venduta con un dissipatore ad aria proprietario, avvitato al **PCB** (Printed Circuit Board, la scheda circuitale della scheda) e collegato ai componenti caldi tramite pasta termica sulla GPU e **thermal pad** (fogli di materiale conduttivo morbido) su memoria e VRM. Per raffreddare a liquido la GPU, è necessario:

1. **Smontare completamente il dissipatore di serie**, il che significa rimuovere la piastra posteriore, svitare la piastra, scollegare i cavi delle ventole e separare il pacco alettato dal PCB — un'operazione che richiede cura perché il dissipatore aderisce al chip tramite pasta termica indurita.
2. **Pulire perfettamente il PCB** da residui di pasta e pad.
3. **Installare un waterblock full-cover**, ovvero una piastra che copre non solo la GPU ma anche la memoria GDDR e i VRM. Ecco il punto cruciale: **il waterblock è specifico per quel PCB**, non per quel modello di GPU. Una GeForce di una certa serie prodotta da diversi produttori ha PCB diversi, con componenti posizionati in modo diverso. Un waterblock progettato per il PCB "reference" (il design di riferimento del produttore del chip) **non è compatibile** con una scheda con PCB personalizzato. Esistono elenchi di compatibilità dettagliati (EK, Alphacool e Bykski li pubblicano) e dovrebbero essere consultati **prima** di acquistare la scheda grafica, non dopo.
4. **Applicare thermal pad dello spessore corretto** alla memoria e ai VRM: qui il margine di errore è piccolo. Un pad troppo sottile non farà contatto (e la memoria si brucerà o andrà in throttling); un pad troppo spesso impedirà al blocco di appoggiarsi sul die della GPU (e la GPU andrà in throttling). Gli spessori sono misurati in decimi di millimetro, e il kit del waterblock li fornisce pretagliati e dimensionati — usate quelli, non improvvisate.

**Sulla garanzia.** Questo è un argomento delicato e va esposto con precisione. In molti paesi extra-UE i produttori dichiarano che la rimozione del dissipatore invalida la garanzia, punto. Nell'Unione Europea la situazione è più sfumata: la **garanzia legale di conformità** (due anni, prevista dalla direttiva UE recepita nel diritto italiano dal Codice del Consumo) copre i difetti di conformità presenti al momento della consegna, e il venditore non può escluderla del tutto per il semplice fatto che il prodotto sia stato aperto. Tuttavia, il venditore **non è tenuto a coprire danni causati dall'utente**, e l'onere di dimostrare che il guasto non è dovuto alla modifica ricade, dopo i primi mesi, sul consumatore. In pratica: smontare il dissipatore di una GPU da 1.500 euro è un rischio che si assume l'utente, e in caso di guasto la discussione con il venditore sarà lunga e l'esito incerto. Alcuni produttori (storicamente pochi, e le policy cambiano nel tempo — *verificare caso per caso, il dato è volatile*) dichiarano esplicitamente di tollerare la sostituzione del dissipatore. **Il consiglio onesto è: non fatelo su una scheda che non potete permettervi di ricomprare.**

**Radiatori.** I custom loop usano radiatori a moduli standard da 120/140 mm, in lunghezze 240/280/360/420/480, e in tre spessori tipici: **30 mm** (slim), **45 mm** (standard, il più comune), **60 mm** (thick, per ventole ad alta pressione statica). La regola empirica per il dimensionamento, cauta ma affidabile: **circa 120 mm di radiatore ogni 150–200 W da dissipare**, con ventole a velocità moderata. Un loop con CPU (250 W) e GPU (400 W) richiede quindi almeno un 360 + un 240, ed è più comodo con due 360.

**Fittings.** Sono i connettori che uniscono tubi e componenti. Lo standard universale è la filettatura **G1/4"**. I tipi principali sono i **compression fittings** (con ghiera, per soft tubing: il tubo viene spinto sul barb e si avvita una ghiera che lo comprime — sicuri e reversibili) e i fittings per **hard tubing** (con O-ring che sigillano contro la parete esterna del tubo). Esistono poi fittings angolati, valvole di scarico (indispensabili, e troppo spesso omesse), passaparete e adattatori vari.

**Tubing.** Due mondi: il **soft tubing** (tubo flessibile, in PVC o silicone, tipicamente 10/13 mm o 10/16 mm — diametro interno/esterno) è flessibile, perdona gli errori, si taglia con le forbici, ed è la scelta razionale per i principianti. L'**hard tubing** (tubo rigido, in PETG, acrilico o metallo) va misurato, tagliato, piegato a caldo con appositi inserti, sbavato e lucidato: il risultato estetico è superiore, i tempi di realizzazione si moltiplicano, e ogni errore costa un pezzo di tubo. È artigianato, e come tale va affrontato.

**Liquido.** Acqua distillata (mai acqua di rubinetto: i sali minerali depositano e conducono) con additivi biocidi e anticorrosione, oppure un **premix** commerciale già pronto (EK CryoFuel, Mayhems, Aquacomputer). Il liquido colorato opaco (pastel) è bello e problematico: tende a sedimentare e a intasare i microcanali; i liquidi trasparenti o leggermente colorati sono molto più affidabili. **Regola chimica aurea: mai mescolare rame e alluminio nello stesso circuito.** Il contatto tra due metalli diversi in presenza di un elettrolita innesca la **corrosione galvanica**: l'alluminio (meno nobile) si corrode rapidamente, i detriti intasano i canali, il loop muore. Dato che quasi tutti i waterblock sono in rame e alcuni radiatori economici sono in alluminio, questo è un errore reale e frequente. Verificare i materiali di ogni singolo componente.

### 8.4.2 Marche

**EK Water Blocks** è storicamente il marchio più popolare, con il più ampio catalogo di waterblock per GPU e un ecosistema completo; *va notato che la stabilità dell'azienda ha affrontato difficoltà negli ultimi anni, con impatti su consegne e supporto — un dato volatile da verificare al momento dell'acquisto.* **Alphacool** offre un eccellente rapporto qualità-prezzo, radiatori tutti in rame e una gamma molto ampia. **Watercool** (marchio tedesco, serie Heatkiller) è il riferimento per la qualità costruttiva assoluta, a prezzi elevati. **Corsair Hydro X** è la soluzione "chiavi in mano" per chi rimane nell'ecosistema Corsair, ben fatta ma costosa. **Bykski** e **Barrow** sono marchi cinesi che offrono componenti a una frazione del prezzo occidentale, con qualità variabile ma spesso sorprendentemente buona: sono il punto di ingresso economico ai custom loop, a patto di verificare attentamente la compatibilità.

### 8.4.3 Costi, manutenzione e a chi conviene davvero

**Costi.** Un loop decente solo CPU parte da 350–450 euro. Un loop CPU + GPU costa realisticamente tra 700 e 1.200 euro, e sale facilmente. Questo va confrontato con un AIO da 360 mm a 130 euro che, per la CPU, offre l'85–90% del beneficio.

**Manutenzione.** Non è opzionale. Il liquido va **sostituito circa ogni 12 mesi** (alcuni si spingono a 18–24 con premix di qualità), il circuito va flussato, e filtri e trappole vanno controllati. Ogni intervento richiede lo svuotamento del loop, il che significa mezza giornata di lavoro. E c'è il rischio sempre presente di una **perdita**: un raccordo non stretto bene, un O-ring danneggiato, un tubo rigido tagliato male. Una perdita su una scheda madre alimentata distrugge componenti da centinaia o migliaia di euro. Per questo, il **leak test** — riempire il loop e metterlo in pressione (o alimentare solo la pompa con un jumper sul connettore 24 pin, tenendo il resto del sistema spento) per almeno 12–24 ore prima di accendere il PC — non è una precauzione consigliata, è una procedura obbligatoria.

**A chi conviene?** Con brutale onestà: **quasi a nessuno, da un punto di vista razionale**. Le prestazioni della CPU di un custom loop superano solo di poco quelle di un buon AIO da 360. Il vero vantaggio tecnico è il raffreddamento della GPU, che può abbassare le temperature di 25–30 °C, eliminare il rumore delle ventole dalla scheda e consentire overclock significativi. Ma è un vantaggio che costa 800 euro, dieci ore di lavoro, un rischio per la garanzia e una manutenzione annuale.

Il custom loop è adatto a tre categorie: chi cerca il **silenzio assoluto** in un sistema molto potente (un loop grande con ventole a 500 RPM è praticamente inudibile, e nessuna altra tecnologia lo permette); chi realizza sistemi **estremi** con più GPU o overclock aggressivi; e chi lo fa **per il piacere di farlo**, come hobby, esattamente come restaurare una moto d'epoca. Quest'ultima è una motivazione perfettamente legittima, purché dichiarata come tale e non mascherata da razionalità tecnica.

---

## 8.5 Aria vs. Liquido: La Decisione Finale

| Criterio | Aria (Dual Tower) | AIO 240–280 | AIO 360–420 | Custom Loop |
|---|---|---|---|---|
| Prestazioni su CPU ≤ 150 W | Eccellente | Equivalente | Eccessivo | Eccessivo |
| Prestazioni su CPU 200–250 W | Buono | Buono | Molto Buono | Molto Buono |
| Prestazioni su CPU > 280 W | Insufficiente (throttling) | Insufficiente | Molto Buono | Eccellente |
| Rumore a parità di dissipazione | Eccellente (no pompa) | Decente | Buono | Eccellente (a bassa velocità) |
| Costo | 30–150 € | 80–130 € | 110–220 € | 400–1.200+ € |
| Affidabilità / Punti di guasto | Solo ventola | Pompa | Pompa | Pompa, raccordi, perdite |
| Durata prevista | 10+ anni | 5–7 anni | 5–7 anni | Indefinita, con manutenzione |
| Manutenzione | Spolverata | Nessuna | Nessuna | Annuale, obbligatoria |
| Spazio intorno al socket | Alto (conflitti RAM) | Nessuno | Nessuno | Nessuno |
| Estetica | Ingombrante | Pulita | Pulita | Massima |
| Raffredda anche GPU | No | No | No | Sì |
| Complessità di assemblaggio | Bassa | Bassa/Media | Media | Molto Alta |

**Raccomandazioni inequivocabili per fascia di CPU:**

- **CPU Entry (fino a 100 W effettivi)**: Single tower economico. Nessun motivo per il liquido.
- **CPU Mid (100–200 W)**: Dual tower economico (Peerless Assassin e simili). L'AIO ha senso solo per vincoli di ingombro RAM elevati o estetica.
- **CPU High (200–280 W)**: Dual tower di fascia alta *oppure* AIO 280/360. Entrambe le scelte sono difendibili; l'aria vince su affidabilità e costo, il liquido su temperature e spazio.
- **CPU Flagship (oltre 280 W: Core i9 di fascia alta, Ryzen 9 in PBO, HEDT/Threadripper)**: AIO 360/420 obbligatorio, o custom loop. L'aria qui non basta, e insistere significa lasciare prestazioni sul tavolo.

Un'ultima osservazione, che vale come principio generale per questo capitolo: **è quasi sempre più intelligente spendere di più sulla CPU e meno sul dissipatore, che il contrario**. Il dissipatore migliora le prestazioni di pochi punti percentuali; una CPU superiore le migliora di decine di punti percentuali. Un budget mal allocato è quello in cui si mette un AIO da 250 euro su un processore da 250 euro.

---

## 8.6 Pasta Termica: Una Discussione Approfondita

### 8.6.1 A cosa serve, e perché non è "un lubrificante"

Se si osservassero al microscopio la superficie superiore dell'IHS della CPU e quella inferiore della base del dissipatore — entrambe apparentemente lisce e specchiate — si vedrebbero due paesaggi montuosi. Nessun processo meccanico produce una superficie perfettamente piana: rimangono micro-asperità, ondulazioni, e a volte una leggera concavità o convessità dovuta alla produzione o alla pressione di montaggio.

Quando queste due superfici vengono pressate l'una contro l'altra, il contatto metallo-metallo **effettivo** avviene solo su una frazione minima dell'area apparente: tipicamente **meno dell'1–2%**. Tutto il resto è aria intrappolata in micro-cavità. E l'aria è un cattivo conduttore termico: circa **0,026 W/m·K**, rispetto a circa 400 W/m·K per il rame. Uno strato d'aria spesso pochi micrometri, che si estende su oltre il 98% dell'area di contatto, costituisce una barriera termica devastante.

Il compito del **TIM** (Thermal Interface Material — questo è il nome tecnico corretto per "pasta termica") è quello di **espellere l'aria e riempire quelle micro-cavità con un materiale che conduca il calore molto meglio dell'aria**. Ecco il punto che deve essere compreso una volta per tutte: la pasta termica **non è un buon conduttore in termini assoluti** — un'ottima pasta ha 8–13 W/m·K, che è trenta o quaranta volte peggio del rame. La pasta è un **compromesso necessario**: è molto peggio del metallo, ma centinaia di volte meglio dell'aria che sostituisce.

Da questa osservazione deriva immediatamente la regola operativa più importante dell'intera sezione: **lo strato di pasta deve essere il più sottile possibile**. La pasta non deve creare spessore: deve solo riempire gli spazi. Ogni micrometro extra di pasta tra i due metalli aggiunge resistenza termica. Chiunque spalmi un millimetro di pasta "per sicurezza" sta letteralmente costruendo un isolante.

### 8.6.2 Tipi di TIM

**Paste tradizionali a base di silicone.** Si tratta di una matrice polimerica (silicone o simile) caricata con particelle conduttive: ossidi metallici (zinco, alluminio), ceramiche, nitruro di boro, particelle metalliche elettricamente non conduttive. Sono **elettricamente non conduttive** (con alcune eccezioni per carichi metallici elevati — leggere il datasheet), il che le rende sicure: una piccola quantità che trabocca sul socket non causerà cortocircuiti. La conduttività dichiarata è tipicamente tra 5 e 14 W/m·K.

Una nota metodologica molto importante: **i valori di conduttività dichiarati dai produttori non sono confrontabili tra diverse marche**, perché non esiste uno standard di misurazione universalmente adottato e ogni produttore utilizza il metodo che lo favorisce. Una pasta che dichiara 13 W/m·K non è necessariamente migliore di una che dichiara 8,5 W/m·K. **Gli unici dati affidabili sono i confronti empirici in test comparativi indipendenti**, dove la differenza tra buone paste risulta essere di 1–3 °C — quasi irrilevante rispetto alla scelta del dissipatore. Chi spende 60 euro per un dissipatore mediocre e poi cerca una pasta miracolosa sta ottimizzando la variabile sbagliata.

Riferimenti concreti:
- **Arctic MX-4** e **MX-6**: economiche, molto facili da applicare (viscosità perfetta), non conduttive, prestazioni eccellenti, longevità dichiarata di anni. Sono la scelta predefinita per il 90% degli utenti e sono difficili da battere per rapporto prezzo/prestazioni.
- **Noctua NT-H1** e **NT-H2**: eccellenti, molto stabili nel tempo, facili da applicare e pulire. Un po' più costose. La NT-H2 è la versione migliorata.
- **Thermal Grizzly Kryonaut**: storicamente tra le migliori in termini di prestazioni pure, con un serio avvertimento: soffre di **pump-out** (vedi sotto) e si degrada relativamente in fretta con i cicli termici, specialmente sopra gli 80 °C. È buona per i benchmark e per chi riapplica spesso la pasta; è una scelta discutibile per un sistema "monta e dimentica". Esistono varianti (Kryonaut Extreme) e formulazioni più stabili nella stessa famiglia.

La **pompa-via** merita una spiegazione, perché è il principale meccanismo di degrado. Ogni volta che il sistema si scalda e si raffredda, i metalli si espandono e si contraggono in misura diversa; questo produce un micro-movimento tra l'IHS e la base del dissipatore che, ciclo dopo ciclo, **"pompa" la pasta fuori dall'interfaccia**, verso i bordi. Col tempo, al centro (dove il calore è maggiore) restano dei vuoti. Il risultato è un peggioramento graduale delle temperature nell'arco di mesi o anni. Le paste più fluide e con matrici meno stabili sono più suscettibili al fenomeno.

**Metallo liquido.** È una lega di gallio, indio e stagno (a volte genericamente chiamata galinstan), liquida a temperatura ambiente. La sua conduttività è su un altro pianeta: **circa 70–80 W/m·K**, quasi dieci volte quella di un'ottima pasta. Il guadagno reale sull'IHS è tipicamente di **5–12 °C**, e su un die nudo (CPU delidded, laptop, console) può essere anche maggiore. Il riferimento commerciale è **Thermal Grizzly Conductonaut** (ed Extreme).

I rischi, però, sono seri e vanno elencati senza indorare la pillola:
1. **È elettricamente conduttivo.** Una goccia che cade su un socket LGA, su un componente SMD della scheda madre, o sui contatti della CPU può causare un cortocircuito e distruggere l'hardware. In posizione verticale, la CPU è verticale: il metallo liquido, se in eccesso, può gocciolare.
2. **Corrode e amalgama l'alluminio.** Il gallio dissolve letteralmente l'alluminio: crea un amalgama fragile e distrugge il metallo. **Non usarlo mai su una base di alluminio**, né vicino a componenti in alluminio. Va usato solo su rame nichelato o superfici compatibili.
3. **Col tempo può essere "assorbito" nel rame** (diffusione), macchiando permanentemente l'IHS e la base, e perdendo efficacia.
4. È **difficile da applicare**: va steso in uno strato sottilissimo con un cotton fioc, sia sull'IHS che sulla base, e l'area circostante va schermata con nastro isolante o conformal coating.

**In sintesi: il metallo liquido non è per l'utente medio.** Ha senso su una CPU delidded, su un laptop che throttla, su un die nudo. In un normale build desktop, il rapporto rischio/beneficio non lo giustifica.

**PTM7950 e materiali a cambiamento di fase.** Questa è l'innovazione che ha cambiato le carte in tavola negli ultimi anni. Il **PTM7950** (Honeywell) è un **PCM** (Phase Change Material): a temperatura ambiente, si presenta come un sottile foglio semi-solido, non appiccicoso, che può essere tagliato e applicato. Al primo riscaldamento (transizione intorno ai 45 °C), si ammorbidisce, "bagna" perfettamente le due superfici riempiendo le micro-cavità, per poi ri-solidificarsi parzialmente al raffreddamento. Questo ciclo si ripete ad ogni accensione.

Il vantaggio decisivo non è la sua conduttività di picco (che è paragonabile a una buona pasta, intorno agli 8 W/m·K) ma **l'assenza quasi totale di pompa-via**: dato che il materiale si ri-solidifica, non viene espulso dai cicli termici. Il risultato è una stabilità eccezionale a lungo termine: dove la pasta tradizionale degrada di 5–10 °C in tre anni, il PTM7950 resta sostanzialmente invariato. È diventato **lo standard de facto per GPU e laptop**, proprio le applicazioni dove il repaste è difficile e i cicli termici sono aggressivi. Il piccolo prezzo da pagare: nei primi minuti da freddo, prima che il materiale abbia raggiunto la temperatura di transizione, le prestazioni sono leggermente peggiori. Questo è irrilevante nell'uso reale.

*Nota su un rischio pratico: essendo un prodotto molto ricercato, il PTM7950 è ampiamente contraffatto sui marketplace. Acquistare da venditori affidabili.*

**Pad in grafite.** Fogli di grafite riutilizzabili (il riferimento è **Innovation Cooling Graphite**), con alta conduttività nel piano ma modesta attraverso lo spessore. Non seccano mai, non pompano fuori e possono essere rimossi e reinstallati. Le prestazioni sono tipicamente 2–4 °C peggiori di una buona pasta. Sono una soluzione "monta e dimentica" per chi smonta frequentemente, o per chi vuole zero manutenzione. Una nicchia legittima.

**Thermal pad (per memorie e VRM).** Questi sono diversi e servono a un altro scopo: riempire **gap** di vario spessore (da 0.5 a 3 mm) tra un componente e il dissipatore. Hanno bassa conduttività (1–15 W/m·K) ma sono comprimibili e mantengono il contatto. Si usano su VRAM, VRM e chip SSD. **Non si usano sotto un IHS al posto della pasta**: lì il gap è di micrometri, e serve un materiale che si assottigli, non un pad.

### 8.6.3 Come applicare: quantità e metodi

**La quantità.** La regola tradizionale è "un chicco di riso" o "un pisello". Sono approssimazioni utili ma vanno calibrate sulla dimensione dell'IHS:
- **AM4** (IHS piccolo, quadrato con smussi): una quantità equivalente a un chicco di riso grande o un pisello piccolo, al centro.
- **AM5** (IHS con caratteristiche aperture sui lati): un pisello al centro. Alcuni preferiscono cinque puntini perché la superficie utilizzabile è di forma "ottagonale".
- **LGA1700 / LGA1851** (IHS rettangolare e allungato): un singolo puntino al centro tende a non raggiungere le estremità corte. Meglio una **linea sottile lungo l'asse maggiore**, o una **X**, o cinque puntini.

**I metodi.**
- **Puntino centrale (pea method)**: Una singola goccia viene posta al centro e si lascia che la pressione di montaggio la sparga. È il metodo raccomandato dalla maggior parte dei produttori, funziona bene, non introduce bolle e non richiede abilità. **Per la maggior parte degli utenti, questo è il metodo giusto.**
- **X / croce / cinque puntini**: Distribuisce meglio su IHS grandi o rettangolari. Ottimo su LGA1700.
- **Linea**: Due o tre linee parallele all'asse lungo dell'IHS, per socket rettangolari.
- **Spalmatura**: La pasta viene spalmata con una spatola o una tessera plastificata prima di posizionare il dissipatore. Dà il massimo controllo sullo spessore, ma **rischia di intrappolare microbolle d'aria** se fatto in modo scorretto, ed è il metodo con il maggior margine di errore. Ha senso quando si usa metallo liquido (obbligatorio) o su un die nudo (dove la superficie è piccola e la pressione di montaggio è bassa).

**Pressione di montaggio.** Non è un dettaglio minore: è ciò che espelle la pasta in eccesso e riduce lo spessore residuo. **Le viti del dissipatore vanno serrate a croce, un poco alla volta, alternando**, esattamente come i dadi di una ruota dell'auto: mai serrare completamente una vite e poi passare alla successiva, perché questo inclinerà la base e creerà un contatto non uniforme. Serrare fino al raggiungimento del finecorsa meccanico (i buoni sistemi, come il SecuFirm2 di Noctua, hanno un finecorsa che impedisce il serraggio eccessivo).

**Non sollevare e riposizionare il dissipatore** dopo averlo montato: se viene rimosso, la pasta è compromessa (si sono formate bolle e vuoti) e **deve essere rifatta da capo**, pulendo entrambe le superfici. È tentante "controllare" dopo il montaggio: non fatelo, o se lo fate, siate pronti a pulire e riapplicare.

### 8.6.4 Errori comuni

**Troppa pasta.** L'errore più comune. Un eccesso non "isola" come vorrebbe la leggenda (perché la pressione ne espelle la maggior parte), ma sborda ai lati, sporca il socket e, nel caso di paste ad alto carico metallico, può causare problemi. Soprattutto, se è troppa e la pressione non è sufficiente a espellerla, lascia uno strato spesso e quindi resistivo.

**Troppa poca pasta.** Un errore più insidioso perché invisibile: aree dell'IHS restano senza contatto, dove l'aria fa da isolante. Ne conseguono temperature elevate e spesso asimmetriche (un core molto più caldo degli altri).

**Bolle d'aria.** Nascono da una stesura malfatta, o dal sollevare e riposizionare il dissipatore.

**Non pulire la vecchia pasta.** Pasta indurita mescolata a pasta nuova dà scarsi risultati. La pulizia si fa con **alcool isopropilico al 99%** (non alcool rosa denaturato, che lascia residui) e un panno che non lasci pelucchi (microfibra o carta da laboratorio; la carta da cucina lascia fibre). Pulire **entrambe** le superfici: IHS e base del dissipatore.

**Dimenticare di togliere la pellicola protettiva dal dissipatore nuovo.** Questo merita una riga in grassetto a sé, perché è un errore reale, frequente, che produce sintomi drammatici (throttling immediato, temperature di 100 °C entro pochi secondi dall'accensione). **Quasi tutti i dissipatori nuovi hanno la pasta termica preapplicata sulla base, e con una pellicola di plastica trasparente o colorata a protezione.** La pellicola va **rimossa** prima del montaggio. Il fatto che sia trasparente e ben aderente la rende facile da non notare. Se un PC nuovo va in throttling istantaneamente, questa è la prima cosa da controllare.

**Applicare la pasta E usare quella preapplicata insieme.** No: o si usa la pasta preapplicata (che è di discreta qualità su quasi tutti i dissipatori di marca) *oppure* la si rimuove completamente con alcool isopropilico e si applica la propria. Mai sovrapporle.

### 8.6.5 Ogni quanto sostituirla, e repasting GPU

**Sulla CPU.** Con una pasta di qualità (MX-4, NT-H2) e un uso normale, un intervallo di **3–5 anni** è ragionevole. Con una pasta soggetta a pump-out (come la Kryonaut) e carichi termici pesanti, si può scendere a **1–2 anni**. Il segnale che è ora di intervenire è un peggioramento progressivo delle temperature a parità di carico e ambiente: se il PC che due anni fa stava a 75 °C in rendering ora sta a 88 °C, e la polvere è già stata rimossa, la pasta è la principale indiziata.

**Sulla GPU.** Qui la sostituzione dà i risultati più spettacolari, ed è anche la più delicata. Le schede grafiche sono assemblate in fabbrica con paste di qualità variabile e, soprattutto, sono soggette a cicli termici molto violenti. Su una GPU di 3–5 anni, non è raro guadagnare **10–20 °C** con un repaste, e anche di più sull'hotspot (il punto più caldo del die, misurato separatamente sulle schede recenti: se il delta tra temperatura media del core e hotspot supera i 20–25 °C, è un forte indizio di pasta degradata o montaggio non uniforme).

La procedura richiede: smontaggio di backplate e plate, scollegamento dei cavi delle ventole, **fotografare tutto prima di smontare** (posizione e spessore dei thermal pad!), pulizia di GPU e base, applicazione della nuova pasta (**PTM7950 è la scelta ideale qui**, proprio per la sua resistenza al pump-out), **sostituzione dei thermal pad solo se necessario e con spessore identico** — ed è qui la trappola: un pad da 1.5 mm sostituito con uno da 2.0 mm impedisce al plate di appoggiarsi sul die, e la GPU va in throttling severo. Serrare le viti **a croce e progressivamente**, seguendo la numerazione spesso stampata sul backplate.

Per quanto riguarda la garanzia, valgono le stesse considerazioni fatte per il waterblock GPU nella sezione 8.4: aprire una scheda in garanzia è un rischio che va valutato consapevolmente.

### 8.6.6 Raccomandazioni per tier (pasta termica)

**Entry / uso normale**: Arctic MX-4. Economica, ottima, semplice, sicura. Fine della storia.
**Mid / per chi vuole il massimo senza rischi**: Noctua NT-H2 o Arctic MX-6.
**High-end / GPU e portatili**: PTM7950, per la stabilità a lungo termine.
**Overclock estremo, bare die, CPU deliddate**: metallo liquido, con tutte le dovute precauzioni e piena consapevolezza dei rischi.

---

## 8.7 Ventole sui radiatori (e sui dissipatori)

### 8.7.1 Airflow vs. pressione statica

Una ventola è definita da una **curva caratteristica** che lega portata d'aria a pressione: a pressione zero (nessuna ostruzione), la portata è massima; all'aumentare dell'ostruzione e della pressione richiesta, la portata diminuisce, fino al punto di **massima pressione statica** (mm H₂O), dove la ventola gira ma non sposta più aria.

Le ventole sono progettate privilegiando un estremo o l'altro della curva:
- Una **ventola da airflow** ha pale larghe, spaziate, poche e un motore piccolo: sposta molta aria in spazio libero, ma "affoga" davanti a un ostacolo. È la ventola giusta per un pannello frontale libero o per l'estrazione posteriore non ostruita.
- Una **ventola da pressione statica** ha pale numerose, ravvicinate, con un profilo più aggressivo e un hub più grande: sposta meno aria libera ma spinge molto meglio attraverso una resistenza. **È la ventola obbligatoria per un radiatore o una fitta pila di alette.**

Montare una ventola da airflow su un radiatore da 38 mm con alette fitte significa buttare via una parte significativa delle prestazioni del radiatore. È un errore comune, spesso fatto per ragioni estetiche (la "bella" ventola RGB è quasi sempre una ventola da airflow).

Nella pratica moderna, molte ventole di qualità sono ibride e lavorano bene in entrambi i regimi (Arctic P12/P14, Noctua NF-A12x25, Phanteks T30, be quiet! Silent Wings): il modo più affidabile per scegliere è leggere le **specifiche di pressione statica** (un valore superiore a 2.0–2.5 mm H₂O è indicativo di una ventola adatta ai radiatori) e le recensioni con misurazioni a parità di rumore.

### 8.7.2 Push, pull, push-pull

Sono i tre modi di disporre le ventole rispetto al radiatore.

**Push**: le ventole sono davanti al radiatore rispetto alla direzione del flusso, e **spingono** aria al suo interno. È la configurazione più comune e la più efficiente per una singola ventola, perché l'aria arriva al radiatore già in pressione.

**Pull**: le ventole sono dietro al radiatore e ne **estraggono** l'aria. A livello prestazionale è leggermente inferiore al push, perché la ventola pesca anche dai bordi e il flusso è meno uniforme. Ha però un vantaggio pratico: **il radiatore resta accessibile e visibile** e la polvere si deposita sulla faccia esterna del radiatore invece che sulle ventole. In alcuni casi è l'unica configurazione che ci sta fisicamente.

**Push-pull**: ventole su entrambi i lati, che spingono da un lato ed estraggono dall'altro. Il beneficio è reale ma modesto: tipicamente **2–5 °C** su un radiatore spesso e a fitte alette, meno di 2 °C su un radiatore sottile. Il costo è doppio in ventole, doppio in potenziale rumore (anche se, a parità di dissipazione, tutte le ventole possono essere tenute a regimi inferiori, il che *riduce* il rumore complessivo), e doppio in spessore occupato — un radiatore da 38 mm in push-pull con ventole da 25 mm occupa **88 mm**, che non stanno in molti case.

**Consiglio operativo:** il push-pull vale la pena su radiatori spessi (38 mm e oltre), su radiatori a elevata densità di alette, e quando l'obiettivo è il silenzio (perché quattro ventole a 800 RPM fanno meno rumore di due a 1.400 RPM a parità di dissipazione). Su un radiatore standard da 27 mm, in un sistema normale, il guadagno non giustifica la spesa.

### 8.7.3 Orientamento: capire da che parte soffia una ventola

Una domanda banale, e a cui si risponde costantemente in modo errato. Ci sono due regole, e sono infallibili:
1. **L'aria entra dal lato con l'adesivo/mozzo col logo... no.** Attenzione: l'aria entra dal lato *aperto* (dove si vedono solo le pale e la cornice) ed esce dal lato dove si vedono i **raggi di supporto** (le quattro o cinque braccia che reggono il motore) e il retro del mozzo. In altre parole: **la ventola soffia verso il lato dove si vede la "griglia" strutturale.**
2. Praticamente tutte le ventole hanno **due piccole frecce stampate sulla cornice** (una indica la direzione del flusso, l'altra la direzione di rotazione). Guardate quelle e fidatevi.

Se si sbaglia, il sintomo è chiaro: temperature elevate e un flusso d'aria che, mettendoci la mano vicino, non va dove dovrebbe.

### 8.7.4 Controllo: PWM, DC e curve

Le ventole si collegano alla scheda madre con connettori **3-pin (DC)** o **4-pin (PWM)**. Nel controllo **DC** (Direct Current), la scheda madre varia la tensione di alimentazione (da 12 V in giù) per rallentare la ventola; sotto una certa tensione (tipicamente 4–5 V), la ventola non parte affatto. Nel controllo **PWM** (Pulse Width Modulation), l'alimentazione resta a 12 V, e un quarto filo porta un segnale che accende e spegne rapidamente il motore, variando il **duty cycle** (la percentuale di tempo in cui è acceso). Il PWM permette un controllo più fine e velocità minime inferiori (spesso 200–300 RPM), ed è la scelta corretta per un sistema silenzioso.

La **curva della ventola** è impostata nel BIOS/UEFI o tramite software: associa una velocità a ogni temperatura. Due consigli concreti che valgono più di tanta costosa ferramenta:
- **Collegare le curve delle ventole del case alla temperatura della CPU è un errore comune**, perché la CPU ha transitori di boost molto violenti (da 40 a 80 °C in mezzo secondo, poi giù) che fanno "pompare" le ventole in modo fastidioso. È meglio collegarle a un sensore più lento (la temperatura della scheda madre, o la temperatura del liquido se l'AIO la espone) o, se non possibile, impostare una generosa **isteresi / tempo di salita e discesa** (3-5 secondi) per smorzare le oscillazioni.
- **La pompa dell'AIO non va trattata come una ventola**: si collega al connettore AIO_PUMP e segue le istruzioni del produttore, che nella maggior parte dei casi sono "100% costante" o una curva molto piatta.

---

## 8.8 Riepilogo operativo — checklist decisionale per il raffreddamento

Da usare in ordine, prima di qualsiasi acquisto.

**1. Quantificare il carico termico effettivo.**
☐ Cercare il consumo energetico effettivo sotto carico pesante della propria CPU in recensioni indipendenti (non il TDP dichiarato: cercare MTP per Intel, PPT per AMD, o meglio ancora, misurazioni in Cinebench o Prime99).
☐ Aggiungere un margine del 15-20% se si prevede di usare PBO / overclock / limiti di potenza sbloccati.

**2. Decidere la tecnologia.**
☐ Sotto ~100 W → single tower economico. Fatto.
☐ 100–200 W → dual tower economico (Thermalright Peerless/Phantom, Arctic Freezer 36). Questa è la scelta predefinita per la stragrande maggioranza degli utenti.
☐ 200–280 W → dual tower di fascia alta **o** AIO 280/360. Scegliere aria se si privilegia affidabilità, costo, zero manutenzione. Scegliere liquido se si ha RAM alta, si desidera un'estetica pulita, o se il proprio case non ospita torri grandi.
☐ Oltre 280 W → AIO 360/420. Sotto questa soglia, si lasceranno prestazioni sul tavolo.
☐ Custom loop → solo se si vuole raffreddare anche la GPU, si cerca il silenzio assoluto, o lo si fa per hobby. Non è mai la scelta economicamente razionale.

**3. Verificare la compatibilità (tutte e tre, senza saltarne alcuna).**
☐ **Socket**: Il kit di montaggio include il proprio socket (LGA1700 / LGA1851 / AM4 / AM5)? Verificato sul sito del produttore del dissipatore, non sulla pagina prodotto del venditore.
☐ **Altezza (aria)**: L'altezza del dissipatore è inferiore all'"altezza massima dissipatore CPU" del case, con almeno 3-5 mm di spazio?
☐ **RAM (aria)**: L'altezza dei moduli di memoria è compatibile con lo spazio dichiarato? Se si ha già una torre grande, si è scelta RAM a basso profilo?
☐ **Radiatore (liquido)**: Il case supporta quella lunghezza *in quella posizione*? Lo spessore (radiatore + ventole) rientra nello spazio dichiarato, senza toccare VRM o RAM?
☐ Nessun conflitto con il primo slot PCIe o i dissipatori M.2?

**4. Pianificare l'installazione.**
☐ (Liquido) Il radiatore è in **alto**, o sul **frontale con i tubi in basso**. **Mai** in basso. **Mai** frontale con tubi in alto.
☐ (Liquido) La pompa **non è il punto più alto del loop**.
☐ (Liquido) La pompa è collegata al connettore AIO_PUMP con allarme velocità zero attivo.
☐ Le ventole sul radiatore sono a **pressione statica** (≥ ~2 mm H₂O), non ventole a puro flusso d'aria.
☐ La direzione delle ventole è verificata con le frecce sul telaio.
☐ (Intel LGA1700) Si è considerato un **contact frame** per compensare la deformazione dell'ILM? Vale 3-8 °C per 15-20 €.

**5. Pasta termica.**
☐ **Hai rimosso la pellicola protettiva** dalla base del nuovo dissipatore? (Sì, ricontrolla.)
☐ Se riusi un dissipatore: entrambe le superfici pulite con **alcool isopropilico al 99%** e un panno che non lascia pelucchi.
☐ Quantità: un pisello al centro (AM4/AM5) o una linea/X (LGA1700/1851). Poca, non tanta.
☐ Viti serrate **a croce, progressivamente**, mai una alla volta fino in fondo.
☐ Dissipatore appoggiato **una volta sola**: se lo sollevi, pulisci e riapplica.
☐ Pasta scelta: MX-4 (default), NT-H2 (premium), PTM7950 (GPU/laptop, longevità), metallo liquido (solo se sai esattamente cosa stai facendo).

**6. Controllo post-installazione.**
☐ Stress test di almeno 15 minuti (Cinebench in loop, Prime95, OCCT) monitorando **simultaneamente** temperatura, frequenza sostenuta e consumo.
☐ Temperature entro i range della tabella in 8.1.4 — ricordando che 90–95 °C su un Ryzen 9 a pieno carico non è un difetto, è il comportamento previsto.
☐ Nessun rumore di gorgoglio dalla pompa dopo i primi giorni di rodaggio.
☐ Curva ventole impostata con adeguata isteresi, per evitare "pompaggio" acustico.

**7. Manutenzione programmata.**
☐ Rimozione polvere da fin stack / radiatore ogni 6–12 mesi.
☐ Repaste CPU ogni 3–5 anni (o prima, se le temperature peggiorano progressivamente a parità di carico).
☐ Repaste GPU dopo 3–5 anni, valutando PTM7950 e fotografando i pad prima dello smontaggio.
☐ (AIO) Prevedere la sostituzione dopo 5–7 anni.
☐ (Custom loop) Cambio liquido e flush ogni 12 mesi. Test di tenuta obbligatorio dopo ogni intervento.

---

*Prossimo capitolo: l'alimentatore e la distribuzione della potenza — ovvero, l'unico componente il cui guasto può portarsi dietro tutti gli altri.*

---

[← Precedente](07-case-ventilazione.html) · [Tutti i capitoli](./) · [Successivo →](09-assemblaggio-pc.html)
