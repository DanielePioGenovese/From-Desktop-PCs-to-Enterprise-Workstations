---
title: "Capitolo 6 - L'alimentatore (PSU)"
parent: "Edizione italiana"
nav_order: 6
---

<details open markdown="block">
  <summary>Indice del capitolo</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## Premessa: il componente invisibile

Di tutte le parti che compongono un personal computer, l'alimentatore è quella che riceve meno attenzioni e, statisticamente, causa più danni. È un oggetto che non compare nei benchmark, non produce frame per secondo, non ha un punteggio Cinebench, non è esposto in nessuna vetrinetta illuminata a RGB (o meglio: alcuni produttori ci hanno provato, con risultati discutibili). Sta in fondo al case, spesso in un vano separato, coperto da una paratia metallica, e l'utente medio se ne dimentica il giorno stesso in cui chiude il pannello laterale.

Eppure, ogni singolo elettrone che alimenta CPU, GPU, RAM, SSD e ventole passa attraverso di esso. L'alimentatore — in inglese, *Power Supply Unit*, da cui l'acronimo **PSU** che useremo d'ora in poi — è l'unico componente che, guastandosi male, può trascinare con sé il resto del sistema. Una scheda grafica che si rompe, si rompe da sola. Una PSU che si rompe male può portare con sé la scheda madre, la CPU, la memoria e i dischi in una frazione di secondo, perché è l'unica cosa nel case fisicamente connessa a un muro che eroga 230 volt di corrente alternata.

Questo capitolo tratta la PSU come merita: partendo da cosa fa fisicamente, passando a come dimensionarla, come leggere un'etichetta, come distinguere un buon prodotto da un cattivo prodotto mascherato da buono, e terminando con una procedura di acquisto ripetibile. Come per i capitoli precedenti, i dati che possono diventare obsoleti (prezzi, modelli sul mercato, generazioni di schede grafiche) sono esplicitamente contrassegnati con il tag **[dati volatili]**: l'hardware si muove velocemente, i principi fisici molto meno.

---

## 1. Cosa fa la PSU

### 1.1 Conversione AC→DC

La presa elettrica di casa, in Italia e in tutta Europa, eroga corrente alternata (**AC**) a una tensione nominale di 230 volt e una frequenza di 50 hertz. "Alternata" significa che la tensione non è costante: oscilla sinusoidalmente, passando cinquanta volte al secondo da un valore di picco positivo a un valore di picco negativo. Questo è ottimo per trasportare energia su lunghe distanze — motivo per cui l'intera rete elettrica globale funziona così — ma è totalmente inutilizzabile da un circuito digitale.

I componenti del computer vogliono corrente continua (**DC**): una tensione stabile e costante che non oscilla. Un transistor all'interno di una CPU non ha idea di cosa fare con un'onda sinusoidale a 50 Hz. Vuole 12 volt fissi, o 5, o 3.3, e li vuole stabili entro pochi punti percentuali, sempre, senza interruzioni.

La PSU è la macchina che esegue questa traduzione. Il processo, semplificando enormemente una catena che in realtà ha decine di stadi, funziona così. La corrente alternata entra e incontra per prima cosa un **filtro EMI** (*ElectroMagnetic Interference*): un gruppo di condensatori e induttori il cui compito è duplice: impedire che i disturbi dalla rete elettrica entrino nell'alimentatore e — altrettanto importante e spesso dimenticato — impedire che i disturbi generati dall'alimentatore stesso (che è un circuito che commuta decine di migliaia di volte al secondo) tornino alla rete e disturbino altri apparecchi. Negli alimentatori economici, questo stadio è il primo a essere sacrificato: vengono rimossi componenti, lasciati pad vuoti sul circuito stampato, e il risultato è un dispositivo che, per legge, non dovrebbe nemmeno poter recare la marcatura CE.

Dopo aver superato il filtro, l'onda sinusoidale incontra un **ponte raddrizzatore**, un gruppo di quattro diodi che "ribalta" le semionde negative, rendendole positive. A questo punto abbiamo una tensione sempre positiva ma che pulsa violentemente: non è ancora corrente continua, è una specie di gobba di cammello ripetuta un centinaio di volte al secondo. Interviene quindi lo stadio di **PFC** (*Power Factor Correction*), che negli alimentatori moderni è quasi sempre **attivo** e non passivo. Il compito del PFC è far sì che la corrente prelevata dalla rete sia in fase con la tensione e abbia una forma d'onda il più possibile sinusoidale: senza di esso, un alimentatore preleverebbe corrente a impulsi stretti e violenti, inquinando la rete e costringendo il fornitore di energia a dimensionare tutto per una potenza apparente ben maggiore di quella effettivamente consumata. Un PFC attivo ben progettato porta il fattore di potenza sopra 0.95; è anche lo stadio che carica il grosso condensatore elettrolitico primario, quello grande e cilindrico che è subito visibile aprendo un alimentatore e che tipicamente opera attorno ai 400 volt in continua.

Da quei 400 volt in continua, dobbiamo scendere ai 12 volt richiesti dal computer, e questo deve avvenire con un completo isolamento galvanico tra la rete elettrica e il PC — altrimenti toccare il case significherebbe toccare la rete elettrica. Questo compito spetta allo **stadio di switching primario**, che trita i 400 V continui in un'onda quadra ad alta frequenza (decine o centinaia di kilohertz), la invia attraverso un trasformatore e ne raccoglie una versione a bassa tensione sull'altro lato. Lavorare ad alta frequenza è il trucco che permette agli alimentatori moderni di essere piccoli: un trasformatore che lavorasse a 50 Hz per erogare 850 watt sarebbe un mattone di diversi chilogrammi, mentre uno che lavora a 100 kHz sta nel palmo di una mano. Le topologie usate hanno nomi che il lettore incontrerà nelle recensioni tecniche — half-bridge, full-bridge, e soprattutto **LLC risonante**, oggi lo standard de facto nella fascia media e alta perché permette ai transistor di commutare quando la corrente che li attraversa è prossima allo zero, riducendo drasticamente le perdite.

Infine, sul lato secondario, la corrente a bassa tensione ma ancora alternata viene raddrizzata. Negli alimentatori di qualità, questo avviene con la **rettifica sincrona**: invece di diodi (che dissipano una tensione di soglia, circa 0.4-0.7 V, moltiplicata per decine di ampere, cioè decine di watt sprecati in calore), si usano MOSFET pilotati attivamente, che si comportano come interruttori quasi ideali. Questa è una delle ragioni principali per cui un alimentatore Gold o Platinum è più efficiente di uno Bronze.

### 1.2 I rail: 12V, 5V, 3.3V (e i fantasmi del passato)

Il termine **rail** indica una linea di alimentazione a una specifica tensione. Un alimentatore ATX moderno fornisce al computer le seguenti tensioni:

| Rail | Tensione | Uso Corrente | Percentuale del Totale |
|---|---|---|---|
| +12V | 12 volt | CPU (tramite VRM), GPU, ventole, motori hard disk, praticamente tutto | 90-99% |
| +5V | 5 volt | Elettronica di controllo dischi SATA, porte USB, alcuni circuiti della scheda madre | Pochi watt |
| +3.3V | 3.3 volt | Slot DIMM (tramite regolatori), slot M.2 e PCIe (parzialmente), logiche varie | Pochi watt |
| +5VSB | 5 volt standby | Mantiene il sistema "vivo" a PC spento: Wake-on-LAN, pulsante di accensione, ricarica USB | 1-3 A |
| -12V | -12 volt | Retaggio storico delle porte seriali RS-232. Praticamente inutilizzato oggi | 0.3 A o meno |

La domanda naturale è: perché 12V domina in modo così schiacciante? La risposta sta nella legge di Ohm e nella potenza. La potenza è la tensione moltiplicata per la corrente (P = V × I); a parità di potenza, maggiore è la tensione, minore è la corrente. Ed è la corrente che costa: le perdite in un conduttore sono proporzionali al *quadrato* della corrente (P_persa = R × I²). Trasportare 600 watt a 12 volt richiede 50 ampere; trasportarli a 5 volt ne richiederebbe 120, con perdite quasi sei volte maggiori e cavi molto più spessi. 12 volt è il compromesso storico tra "abbastanza alto da limitare la corrente" e "abbastanza basso da rimanere in tensione di sicurezza extra-bassa".

C'è, tuttavia, una seconda ragione, più moderna e più importante. Nessun componente del PC utilizza effettivamente 12 volt come tali. La CPU oggi opera a circa 1.0-1.3 volt; la GPU è nello stesso intervallo; la memoria DDR5 opera a 1.1 volt. Quei 12 volt vengono ulteriormente convertiti *in loco* dai **VRM** (*Voltage Regulator Modules*, i moduli di regolazione della tensione di cui abbiamo parlato ampiamente nel capitolo della scheda madre) presenti sulla scheda madre e sulla scheda grafica. Il VRM è un convertitore DC-DC multifase che prende l'ingresso a 12 volt e lo riduce alla tensione richiesta dal chip, con una precisione e una velocità di risposta che nessun cavo lungo mezzo metro potrebbe garantire. In altre parole: l'alimentatore non alimenta la CPU. L'alimentatore alimenta il VRM, che alimenta la CPU. L'alimentatore fornisce potenza "grezza" a 12 volt il più vicino possibile al punto di utilizzo, e la conversione fine avviene lì.

Questa architettura ha una conseguenza pratica su come gli alimentatori sono costruiti internamente. Negli alimentatori più vecchi, progettati negli anni 2000, le tre tensioni principali erano tutte generate dal trasformatore con avvolgimenti separati e "regolate in gruppo" (**group regulation**): il circuito di controllo guardava una media ponderata di 12V e 5V e cercava di mantenerla entro i limiti. Il difetto è evidente: se il carico è fortemente sbilanciato — e nel 2026 lo è sempre, perché i 12V assorbono il 95% della potenza mentre i 5V e i 3.3V sono quasi scarichi — le tensioni divergono. I 12V tendono a scendere sotto carico, i 3.3V tendono a salire, e in casi patologici, escono dalla tolleranza ATX ±5%. La soluzione moderna, adottata da qualsiasi alimentatore decente, è **DC-DC**: il trasformatore genera *solo* 12 volt, e i 5V e i 3.3V sono derivati da essi tramite piccoli convertitori buck posti su schede dedicate. Il risultato è una regolazione della tensione che rimane entro l'1-2% in qualsiasi condizione di carico. Se leggete in una recensione tecnica che un alimentatore "utilizza una piattaforma group-regulated", state leggendo — nel 2026 — che si tratta di un design obsoleto, quasi sempre appartenente alla fascia di prezzo più bassa.

Un ultimo concetto: **single rail** contro **multi rail**. Alcuni alimentatori dividono virtualmente il loro 12V in diverse "rail" separate, ognuna con la propria protezione da sovracorrente. Queste non sono quasi mai rail fisicamente distinte (c'è un solo trasformatore): si tratta di una suddivisione fatta a valle, con sensori di corrente separati sui vari gruppi di cavi. Il vantaggio teorico è la sicurezza: se un cavo va in corto, la protezione per quel singolo ramo scatta a una soglia bassa, invece di aspettare che l'intero alimentatore raggiunga la sua soglia globale. Lo svantaggio è che con schede grafiche molto esigenti in termini di potenza, c'è il rischio di far scattare la protezione di una singola rail pur rimanendo all'interno della potenza totale. In pratica: entrambe le architetture sono valide se ben implementate, un single rail ben protetto va perfettamente bene per un PC casalingo, e questa è una discussione che negli anni 2010 ha consumato molta più energia nei forum che nei casi reali.

### 1.3 Perché è il componente su cui NON si risparmia

Arriviamo al punto che rende l'intero capitolo degno di nota. Un alimentatore scadente non è "un alimentatore leggermente peggiore". È un dispositivo che può fallire in modi qualitativamente diversi da qualsiasi altro componente.

Il primo rischio è l'eccessivo **ripple**. Il ripple è la tensione AC residua che rimane sull'uscita DC: invece di essere una linea piatta a 12 volt, la tensione oscilla continuamente sopra e sotto quel valore. Lo standard ATX impone che il ripple non superi i 120 millivolt picco-picco sulla rail da 12V e 50 mV sulle rail da 5V e 3.3V. Un buon alimentatore rimane ben al di sotto di questo — 20-40 mV sui 12V è tipico per un prodotto di fascia alta. Un alimentatore scadente può raggiungere 150-200 mV o più, specialmente sotto carico pesante. Le conseguenze non sono né immediate né spettacolari: il PC funziona, ma i VRM sulla scheda madre e sulla scheda grafica ricevono una tensione "sporca", devono lavorare di più per pulirla, i condensatori a valle si scaldano e invecchiano più rapidamente, e in casi estremi si verificano instabilità apparentemente inspiegabili — crash sotto carico, errori di memoria, riavvii casuali — che l'utente passa mesi ad attribuire alla RAM, ai driver video o a Windows. Il ripple è un killer lento e silenzioso, e non compare in nessuna specifica pubblicizzata.

Il secondo rischio è la **regolazione fuori tolleranza**. Se sotto carico transitorio i 12V scendono a 11.2 volt, il VRM della scheda grafica può andare in modalità di protezione o il sistema potrebbe semplicemente spegnersi. Il sintomo classico — "il PC si riavvia solo quando gioco" — è, nella stragrande maggioranza dei casi, un alimentatore inadeguato, non una GPU difettosa.

Il terzo rischio è catastrofico: la **mancanza di protezioni funzionali**. Un alimentatore ben progettato ha una serie di circuiti di monitoraggio (che esamineremo in dettaglio nella sezione 6) che, in caso di anomalia, spengono tutto in millisecondi. Un alimentatore economico spesso ha queste protezioni solo sulla carta: il circuito integrato di supervisione potrebbe essere un modello che le supporta, ma i componenti passivi che le abilitano non sono stati installati per risparmiare pochi centesimi. In quel caso, un guasto interno può inviare tensioni errate ai componenti — 12 volt sulla linea da 5V, per esempio — portando alla distruzione istantanea e irreversibile di tutto ciò che è collegato. Questo non è un rischio teorico: i laboratori indipendenti che testano alimentatori economici distruggendoli deliberatamente producono regolarmente questo tipo di risultato, e nel mondo reale, ci sono infinite discussioni di utenti che hanno perso la scheda madre, la CPU e le unità a causa di un PSU da trenta euro acquistato su un marketplace.

Il quarto rischio è il più banale e il più concreto: l'**incendio**. Un alimentatore funziona con un ingresso di 230 volt, con condensatori caricati a 400 volt, e dissipa decine di watt sotto forma di calore. I componenti che separano il primario dal secondario, i filtri, i fusibili e le distanze di isolamento sulla scheda a circuito stampato sono elementi di sicurezza. Negli alimentatori privi di certificazioni reali — e un marchio CE apposto unilateralmente da un produttore asiatico non è una certificazione, è un'autodichiarazione — questi elementi sono i primi a fallire.

Infine, c'è una considerazione economica che dovrebbe convincere anche coloro che non sono influenzati dagli argomenti tecnici. L'alimentatore è il componente con la vita utile più lunga nell'intero sistema. Una CPU

Un numero illustra il punto. Una scheda con un TBP di 350 watt può mostrare picchi di 600-700 watt su una finestra di misurazione di un decimo di millisecondo. Nessuno strumento domestico li vede: un misuratore da presa aggiorna la sua lettura una o due volte al secondo, e su quella scala, il picco è invisibile, completamente perso nella media. Ma l'alimentatore lo vede assolutamente, e — cosa cruciale — anche il suo circuito di protezione da sovracorrente lo vede. Se l'**OCP** (*Over Current Protection*) è impostato troppo strettamente e la sua risposta è veloce, l'alimentatore interpreta il picco come un guasto e si spegne. Questo ha portato al fenomeno, diventato famoso con le schede NVIDIA RTX serie 30 nel 2020-2021 **[dati volatili]**, di alimentatori da 750 watt perfettamente onesti che spegnevano PC con schede il cui consumo medio era ben al di sotto di quella soglia.

Questo è precisamente il problema che lo standard ATX 3.0 (sezione 5) è stato creato per risolvere. Ed è per questo che la regola pratica corretta è: **prendere il consumo di picco realistico calcolato in precedenza e aggiungere un margine del 30-40%**. Non perché il sistema consumi effettivamente il 40% in più, ma perché quel margine assorbe i transitori senza far scattare le protezioni, e perché posiziona il punto di funzionamento tipico dell'alimentatore nella zona in cui è più efficiente e silenzioso.

### 2.3 Tabelle Indicative e Calcolatori

I calcolatori online — quelli seri, come il calcolatore di be quiet!, di Seasonic, o il classico di Cooler Master **[dato volatile: gli strumenti cambiano URL e vengono dismessi]** — sono utili per un riscontro, non come oracolo. Vanno usati con la consapevolezza che tendono a essere conservativi (il produttore ha interesse a vendervi un alimentatore più grande) e che spesso si basano sui TDP nominali piuttosto che sul consumo effettivo. Il calcolatore di OuterVision è storicamente il più dettagliato perché permette di specificare overclock e carichi personalizzati.

La seguente tabella offre un punto di partenza per configurazioni tipiche nel 2025-2026. I valori di TBP per le schede sono quelli dichiarati al lancio del prodotto e andrebbero verificati **[dato volatile: la gamma delle schede video cambia ogni 18-24 mesi]**.

| Tipo di Configurazione | GPU (TBP Indicativo) | CPU (Picco) | Picco Stimato Sistema | PSU Raccomandato |
|---|---|---|---|---|
| Ufficio / HTPC, grafica integrata | — | 65-90 W | ~150 W | 400-450 W |
| Gaming entry (es. RTX 5060 / RX 9060 XT) | ~150-180 W | ~120 W | ~350 W | 550-600 W |
| Gaming medio (es. RTX 5070 / RX 9070) | ~250 W | ~150 W | ~450 W | 650-750 W |
| Medio-alto (es. RTX 5070 Ti / RX 9070 XT) | ~300 W | ~180 W | ~530 W | 750-850 W |
| Fascia alta (es. RTX 5080) | ~360 W | ~200 W | ~600 W | 850-1000 W |
| Enthusiast (es. RTX 5090) | ~575 W | ~250 W | ~900 W | 1000-1200 W |
| Workstation / dual GPU | 2× 300-575 W | 250-350 W | 1200-1600 W | 1600 W+ (e verifica impianto) |

Riguardo all'ultima riga, una nota specifica per chi vive in Italia: il contratto residenziale standard prevede 3 kW di potenza impegnata, con una tolleranza fino a circa 3,3 kW prima che scatti il limitatore. Un sistema con due schede video di fascia alta, a pieno carico, può avvicinarsi pericolosamente a metà di quel budget da solo, e va considerato assieme a tutto il resto della casa. Non è un problema per il 99% delle configurazioni, ma è un vincolo reale per chi costruisce macchine da inferenza o rendering.

### 2.4 Sovradimensionamento: pro e contro

Il principale **pro** è l'efficienza. La curva di efficienza di un alimentatore non è piatta: è bassa a carichi molto leggeri (sotto il 10-15%), sale rapidamente, raggiunge il picco attorno al 40-60% del carico nominale, e scende leggermente man mano che si avvicina al 100%. Dimensionare l'alimentatore in modo che il consumo tipico del sistema — non il picco, ma il consumo *tipico*, cioè quello che si ha giocando o lavorando — cada in quella finestra centrale significa operare nel punto di massima efficienza.

Il secondo pro è la rumorosità, ed è quello che si nota di più nella pratica quotidiana. Praticamente tutti gli alimentatori decenti oggi hanno una modalità **zero-RPM** o semi-passiva: sotto una certa soglia di carico (tipicamente il 30-40%), la ventola rimane spenta, e l'alimentatore si raffredda per convezione. Un alimentatore da 850 W in un sistema che tipicamente assorbe 300 W sarà assolutamente silenzioso per la maggior parte del tempo. Lo stesso sistema con un alimentatore da 550 W avrà la ventola costantemente in funzione.

Il terzo pro è la longevità: un alimentatore che opera costantemente al 90% del suo carico nominale lavora con condensatori più caldi, e la vita di un condensatore elettrolitico si dimezza circa per ogni 10 °C di aumento della temperatura. Il quarto è la possibilità di aggiornare la scheda grafica fra tre anni senza dover cambiare anche l'alimentatore.

Ci sono due **contro**. Il primo è ovvio: il costo. Il secondo è il calo di efficienza a carichi molto bassi. Un sistema che consuma 60 watt in idle, alimentato da un'unità da 1200 W, sta operando al 5% del carico, ben al di sotto del punto in cui la certificazione 80 PLUS misura qualcosa. Le unità moderne di alta qualità si comportano comunque bene lì — questo è precisamente ciò che misura la certificazione Cybenetics, come vedremo — ma le unità economiche vacillano. Il consiglio pratico è di non esagerare: installare un'unità da 1000 W su un sistema che consuma 300 W di picco non porta benefici, costa di più e non migliora nulla. Il punto ottimale è come descritto sopra: picco realistico + 30-40%.

---

## 3. La Certificazione 80 PLUS

### 3.1 Cosa misura realmente (e cosa no)

**80 PLUS** è un programma di certificazione volontario, nato nel 2004 negli Stati Uniti, che misura **una sola cosa**: l'efficienza della conversione da AC a DC, espressa come rapporto tra la potenza erogata in uscita e la potenza prelevata dalla presa a muro. Un alimentatore che eroga 500 watt al PC prelevandone 555 dalla presa ha un'efficienza del 90%: i 55 watt di differenza sono stati convertiti in calore all'interno del case.

È fondamentale capire cosa 80 PLUS **non** misura, perché la confusione su questo punto è la fonte della maggior parte degli acquisti errati:

Non misura il ripple. Non misura la regolazione della tensione. Non misura la risposta ai transitori. Non misura la presenza o la calibrazione delle protezioni. Non misura la qualità dei condensatori. Non misura la rumorosità della ventola. Non misura l'*hold-up time* (il tempo, minimo 17 millisecondi secondo lo standard ATX, per cui l'alimentatore deve continuare a erogare tensioni valide dopo un'interruzione di corrente, per dare tempo al sistema di gestire uno spegnimento ordinato). Non misura la durata.

Un'etichetta 80 PLUS Gold certifica che l'alimentatore è efficiente. Non dice assolutamente nulla sul fatto che sia *buono*. Questi sono due assi ortogonali, e sul mercato ci sono alimentatori Gold mal costruiti e alimentatori Bronze molto ben costruiti. Ci torneremo tra un attimo.

Va aggiunta una nota storica per aiutare a calibrare la fiducia: per anni, il programma è stato gestito da un'organizzazione (Clearesult/Ecova) che testava un solo campione fornito dal produttore, senza controlli a campione sulla produzione di serie. Vari test indipendenti hanno trovato prodotti sul mercato che non raggiungevano l'efficienza dichiarata sulle loro etichette. Non è un programma fraudolento, ma è un programma con capacità di controllo limitate.

### 3.2 Livelli e Percentuali

Le soglie di efficienza sono definite a tre (o quattro) punti di carico. Un punto molto importante, quasi sempre ignorato: **le soglie variano a seconda della tensione di rete**. La tabella "americana" a 115 volt, quella che circola nei forum, non è quella che si applica in Italia. Noi operiamo a 230 volt, e per la stessa potenza in uscita, la corrente in ingresso è la metà, quindi le perdite sono minori e i requisiti sono più stringenti. La tabella corretta per l'Europa è quella "230V EU internal".

**Efficienza Minima Richiesta — 230V EU (la tabella che conta in Italia)**

| Livello | Carico 10% | Carico 20% | Carico 50% | Carico 100% |
|---|---|---|---|---|
| 80 PLUS (White) | — | 82% | 85% | 82% |
| 80 PLUS Bronze | — | 85% | 88% | 85% |
| 80 PLUS Silver | — | 87% | 90% | 87% |
| 80 PLUS Gold | — | 90% | 92% | 89% |
| 80 PLUS Platinum | — | 92% | 94% | 90% |
| 80 PLUS Titanium | 90% | 94% | 96% | 91% |

**Efficienza minima richiesta — 115V (per confronto, non applicabile alla rete italiana)**

| Livello | 10% | 20% | 50% | 100% |
|---|---|---|---|---|
| 80 PLUS (White) | — | 80% | 80% | 80% |
| Bronze | — | 82% | 85% | 82% |
| Silver | — | 85% | 88% | 85% |
| Gold | — | 87% | 90% | 87% |
| Platinum | — | 90% | 92% | 89% |
| Titanium | 90% | 92% | 94% | 90% |

Si noti che Titanium è l'unico livello che impone un requisito al 10% di carico. Questa è una scelta significativa: riconosce che un PC moderno trascorre la maggior parte della sua vita in idle o quasi idle, e che l'efficienza a basso carico ha un impatto reale sulle bollette elettriche e sul calore.

Quanto vale, in termini monetari, salire di livello? Facciamo un calcolo onesto. Supponiamo un PC che consuma in media 300 watt in uscita, usato quattro ore al giorno per 365 giorni. Con un alimentatore Bronze all'88% di efficienza, assorbe 341 W dalla presa; con un Gold al 92%, assorbe 326 W. La differenza è di 15 watt, per 1460 ore all'anno, che sono circa 22 kWh. A un prezzo dell'energia di 0,25 €/kWh **[dato volatile]**, stiamo parlando di circa 5,50 euro all'anno. Il sovrapprezzo di un Gold rispetto a un Bronze si ripaga in tre o quattro anni, non in sei mesi. La conclusione corretta non è "il Gold non vale la pena": è che **non si compra un Gold per l'efficienza in sé**, lo si compra perché i Gold e i livelli superiori concentrano i migliori progetti, con componenti migliori e garanzie più lunghe. L'efficienza è un indicatore correlato alla qualità, non la ragione d'acquisto.

### 3.3 Cybenetics: La Certificazione che Misura Ciò che Conta

**Cybenetics** è un ente di certificazione indipendente, fondato da Aris Mpitziopoulos (uno dei più rigorosi revisori tecnici del settore), creato proprio per colmare le lacune di 80 PLUS. Rilascia due distinte certificazioni.

La prima è **ETA**, che misura l'efficienza. La differenza metodologica rispetto a 80 PLUS è sostanziale: invece di misurare in tre punti fissi, Cybenetics calcola una **media ponderata sull'intero intervallo di carico**, dal 10% al 100%, includendo esplicitamente i carichi leggeri; misura anche l'efficienza del rail di standby a 5 volt e il consumo di "potenza vampiro" (cioè, con il PC spento ma l'alimentatore collegato alla rete). I livelli vanno da ETA-A+++ (il più alto) a scendere attraverso A++, A+, A, B, C, D, E. Un'unità Cybenetics ETA-A corrisponde all'incirca a un 80 PLUS Gold/Platinum, ma i dati sono molto più rappresentativi del comportamento nel mondo reale.

La seconda, e per molti utenti la più preziosa, è **LAMBDA**: misura il **rumore** in una camera anecoica sull'intero intervallo di carico, con livelli che vanno da LAMBDA-A++ (media inferiore a 15 dB(A), il che significa un alimentatore praticamente inudibile) fino a LAMBDA-E. È l'unica certificazione al mondo che quantifica il rumore dell'alimentatore con un metodo ripetibile, e per chi costruisce un PC silenzioso, vale più di qualsiasi altro dato.

Cybenetics pubblica anche **rapporti di prova completi** sul proprio sito web: ripple, regolazione della tensione, risposta ai transitori, tempo di hold-up, efficienza a ogni carico e verifica delle protezioni. Si tratta di documenti PDF gratuiti, lunghi venti o trenta pagine, che ti dicono tutto ciò che c'è da sapere su un alimentatore. Consultare il database Cybenetics prima di un acquisto è, oggi, l'azione più utile che un acquirente possa intraprendere.

### 3.4 Perché un Gold scadente può essere peggio di un Bronze eccellente

Ora possiamo chiudere il cerchio con un esempio concreto e illustrativo. Immaginiamo due alimentatori da 650 watt.

Il primo è un Gold di un marchio meno conosciuto, costruito da un OEM di terzo livello. Raggiunge il 90% di efficienza — lo ha fatto usando componenti a basso costo ma con una topologia efficiente, il che è perfettamente possibile — ma ha condensatori cinesi da 85 °C sul secondario, un ripple di 90 mV sul 12V (entro le specifiche ATX, ma al limite), una protezione OCP mal calibrata che interviene sui transitori della GPU, una ventola con cuscinetto a manicotto rumorosa senza modalità zero-RPM e una garanzia di tre anni.

Il secondo è un Bronze di Seasonic o be quiet!, costruito su una piattaforma matura. Raggiunge l'88% di efficienza. Ha condensatori giapponesi da 105 °C, un ripple di 30 mV, protezioni correttamente calibrate e verificate, una ventola FDB con zero-RPM e una garanzia di cinque o sette anni.

Il secondo alimentatore è, per ogni metrica che conta — stabilità, silenziosità, longevità, sicurezza — un prodotto migliore. Consuma 15 watt in più a 650. La lezione, ripetuta perché è la più importante del capitolo: **80 PLUS è un filtro grossolano, non un criterio di selezione.** Serve a escludere la spazzatura (non comprare mai sotto il Bronze, e praticamente mai sotto il Gold nel 2026 perché non c'è motivo), non a scegliere tra due candidati.

---

## 4. Modularità

### 4.1 Tre architetture

**Non modulare**: Tutti i cavi escono dall'alimentatore, saldati o crimpati in modo permanente. Sono tutti lì, sempre, inclusi i quattro connettori SATA che non userai mai e i due connettori Molex che nessuno usa dal 2012. Ciò che non viene utilizzato deve essere nascosto da qualche parte nel case — tipicamente in un vano per hard disk vuoto o dietro il pannello posteriore, dove ostruisce il flusso d'aria e complica l'assemblaggio. Il vantaggio è il costo: a parità di piattaforma, un PSU non modulare costa 10-20 euro in meno di uno completamente modulare, perché elimina i connettori, il PCB dei connettori e le resistenze di contatto associate. È una scelta ragionevole solo nella fascia di budget assoluto o in build dove la gestione dei cavi non è importante.

**Semi-modulare**: I cavi che sono *sempre* necessari, in qualsiasi configurazione — il cavo a 24 pin della scheda madre e il cavo EPS a 8 pin della CPU — sono fissi; tutto il resto (PCIe, SATA, Molex) è staccabile. Questo è un compromesso intelligente ed è la scelta dominante nella fascia media. Il risparmio rispetto al full modulare è modesto (5-15 euro), e lo svantaggio pratico è quasi nullo, tranne in casi molto piccoli dove anche instradare due cavi fissi della lunghezza sbagliata è un fastidio.

**Completamente modulare**: Ogni singolo cavo, incluso il 24-pin, è staccabile. I vantaggi sono tre. Il primo è la gestione dei cavi: si collegano solo i cavi necessari. Il secondo, meno ovvio e molto apprezzato da chi ha costruito PC in case compatti, è che si può **installare l'alimentatore nudo nel case e poi collegare i cavi**, invece di dover infilare nel vano un blocco di metallo con un metro e mezzo di spaghetti penzolanti. Il terzo è la possibilità di usare cavi personalizzati o "sleeved", per ragioni estetiche o per avere lunghezze su misura.

### 4.2 L'avvertimento più importante di questo capitolo: i cavi non sono intercambiabili

Questo andrebbe scritto in grassetto e letto due volte, perché è l'errore che distrugge più hardware legato all'alimentazione.

**I cavi modulari NON sono intercambiabili tra marche diverse, e spesso nemmeno tra modelli diversi dello stesso produttore.**

Le regole operative che ne conseguono sono tre, e non ammettono eccezioni. Primo: **usare solo i cavi forniti nella scatola dell'alimentatore che si sta usando.** Secondo: quando si sostituisce un alimentatore, **sostituire anche tutti i cavi**, anche quelli che "sembrano uguali" e anche se il nuovo alimentatore è della stessa marca del vecchio. Terzo: se si acquistano cavi di terze parti (CableMod, Corsair Premium Kit, e simili), **verificare che siano dichiarati compatibili con quello specifico modello**, e non con quella marca genericamente. I produttori seri di cavi custom hanno configuratori che chiedono il modello esatto dell'alimentatore proprio per questo.

Una nota su un ambito adiacente: nel 2023, CableMod ha dovuto ritirare dal mercato i suoi adattatori angolati per il connettore 12VHPWR, dopo un numero significativo di casi di surriscaldamento e fusione **[dato volatile: episodio storico, ma il principio resta]**. La morale non è che i cavi di terze parti siano sempre pericolosi, ma che non ci si improvvisa sul percorso delle alte correnti, e che un adattatore in più significa due contatti elettrici in più dove possono formarsi resistenze parassite.

---

## 5. Standard e Connettori

### 5.1 ATX 2.x versus ATX 3.0/3.1

Lo standard **ATX** (*Advanced Technology eXtended*), introdotto da Intel nel 1995, definisce, tra le altre cose, la "Power Supply Design Guide": il documento che stabilisce quali tensioni un alimentatore deve fornire, con quali tolleranze, con quali connettori, e con quale comportamento in condizioni anomale. La revisione **ATX 2.x**, nelle sue varie sottovarianti, ha governato il mercato per quasi vent'anni.

La revisione **ATX 3.0**, pubblicata da Intel nel 2022, nasce da un problema ben preciso: i transitori delle moderne schede video di cui abbiamo discusso nella sezione 2.2. La novità centrale è che ATX 3.0 impone requisiti espliciti sulla capacità di sostenere **escursioni di potenza** ben oltre la potenza nominale per intervalli brevissimi, senza che intervengano le protezioni e senza che le tensioni vadano fuori tolleranza. In termini pratici, un alimentatore ATX 3.0 deve reggere escursioni fino a circa il **200% della sua potenza nominale** per durate nell'ordine dei 100 microsecondi, con requisiti scalari per durate più lunghe, e deve tollerare che una singola scheda video connessa al connettore 12 volt ad alta potenza richieda fino a **tre volte** la sua potenza nominale per un istante. È un requisito severo, che di fatto costringe il progettista a sovradimensionare i componenti di potenza e, soprattutto, a **tarare le protezioni con una risposta temporizzata** invece che istantanea: la protezione deve distinguere tra un picco legittimo di 80 microsecondi e un vero cortocircuito.

Il secondo grande contributo di ATX 3.0 è l'introduzione del connettore a 16 pin **12VHPWR** (*12 Volt High Power*), progettato per veicolare fino a 600 watt su un singolo cavo.

La revisione **ATX 3.1**, del 2024, non rivoluziona nulla ma corregge il problema che il 12VHPWR aveva rivelato sul campo. Sostituisce quel connettore con la variante **12V-2x6** (detta anche H++), rende obbligatorio che l'alimentatore sia in grado di erogare la potenza dichiarata attraverso di esso, e stringe alcuni requisiti sui transitori. Nel 2026, un nuovo alimentatore di fascia media o alta dovrebbe essere ATX 3.1 **[volatile data: lo standard evolve]**.

Il **12VHPWR / 12V-2x6** è il connettore a 16 pin: dodici pin di potenza (sei 12V e sei di massa) e quattro pin laterali di segnale, i **sense pin**. I sense pin sono la parte concettualmente interessante: due di essi (Sense0 e Sense1) formano un codice a due bit che l'alimentatore usa per comunicare alla scheda grafica **quanta potenza è autorizzata a prelevare** attraverso quel cavo — 150, 300, 450 o 600 watt. Una scheda che rileva un codice da 150 W limiterà di conseguenza il suo consumo. Il connettore, nella sua prima incarnazione 12VHPWR, ha avuto una storia travagliata: numerosi casi documentati di fusione dell'alloggiamento, prima con le RTX 4090 nel 2022-2023 e successivamente con le RTX 5090 nel 2025 **[volatile data]**. Le cause individuate sono state essenzialmente due: inserzioni incomplete (il connettore non veniva spinto fino in fondo, i contatti toccavano solo parzialmente, la resistenza di contatto aumentava, il calore fondeva la plastica) e squilibri di corrente tra i sei conduttori da 12 volt, con un singolo filo costretto a portare corrente ben oltre la sua capacità. La revisione **12V-2x6** affronta la prima causa in modo elegante: **accorcia i sense pin e allunga i contatti di potenza**, in modo che se il connettore non è completamente inserito, i sense pin non fanno contatto, e la scheda — non ricevendo autorizzazione — si limita a pochi watt invece di tentare di prelevarne 600 attraverso contatti parziali. Non risolve la seconda causa, che è un problema di progettazione lato scheda grafica (bilanciamento delle correnti), ma è un sostanziale miglioramento della sicurezza.

La regola operativa per il 12V-2x6 è chiara: **usare il cavo di alimentazione nativo, inserirlo fino allo scatto, controllare che non ci siano spazi visibili tra il connettore e il corpo della scheda, non piegare bruscamente il cavo nei primi 3-4 centimetri dal connettore e non usare l'adattatore a quattro connettori PCIe incluso nella scatola della scheda grafica se si può evitare.** Questi adattatori funzionano, ma introducono contatti aggiuntivi e sono la configurazione in cui si sono verificati la maggior parte degli incidenti.

I connettori di alimentazione **SATA** (15 pin, piatti, con l'inconfondibile forma a L) alimentano dischi meccanici e SSD da 2.5". Trasportano 12V, 5V e 3.3V. I connettori **Molex** a 4 pin sono un artefatto degli anni ottanta, sopravvissuti per alimentare vecchi accessori, hub per ventole e pompe: trasportano 12V e 5V, sono scomodi da inserire ed estrarre, e entro il 2026 il loro uso sarà residuale.

### 5.3 "PCIe Gen5 ready": cosa significa (e cosa non significa)

Questo è un termine di marketing, non uno standard formale, e deve essere decodificato. Sulle scatole degli alimentatori, di solito significa due cose insieme: che l'unità è conforme ad ATX 3.0 o 3.1 (gestendo quindi i transitori secondo le specifiche) e che ha almeno un **connettore 12V-2x6 nativo** cablato direttamente all'alimentatore, senza adattatori.

Ciò che *non* significa è altrettanto importante: non ha nulla a che fare con la versione dello slot PCI Express della scheda madre. Un alimentatore non "sa" se lo slot è Gen4 o Gen5 e non gli importa: fornisce 12 volt, punto. Il termine si riferisce al connettore di alimentazione (a volte impropriamente chiamato "connettore di alimentazione PCIe 5.0") e alla sua capacità di sostenere picchi di potenza. Un alimentatore non "Gen5 ready" alimenterà perfettamente una scheda grafica PCIe 5.0 che utilizza connettori tradizionali a 8 pin.

---

## 6. Qualità Interna

### 6.1 Gli OEM: Chi costruisce davvero il tuo alimentatore

Questa è la sezione che separa l'acquirente informato da tutti gli altri, e il concetto da afferrare è che **la stragrande maggioranza dei marchi che vendono alimentatori non li produce**. Corsair non ha fabbriche di alimentatori. Nemmeno be quiet!, né NZXT, né Cooler Master, né MSI, né — con alcune sfumature — EVGA. Quello che fanno è progettare o commissionare un prodotto, definirne le specifiche, testarlo e apporvi il proprio marchio, mentre la progettazione elettronica dettagliata e la produzione fisica sono affidate a un **OEM** (*Original Equipment Manufacturer*).

Gli OEM significativi nel mercato consumer sono relativamente pochi. **Seasonic** è l'unico che è contemporaneamente un OEM di alto livello e un marchio che vende con il proprio nome; produce anche per altri (storicamente per NZXT, Antec e altri). **Super Flower** è taiwanese, ha un'ottima reputazione ed è la piattaforma dietro molte unità EVGA di fascia alta e il marchio Leadex. **CWT** (*Channel Well Technology*) è enorme e produce per Corsair e molti altri; la sua qualità dipende interamente dal livello della piattaforma, che va da molto buona a mediocre. **FSP** (*Fortron Source Power*) è un altro gigante, con un catalogo che va dall'economico all'eccellente, e produce anche con il proprio marchio. **HEC**, **Andyson**, **Sirfa/High Power**, **Solytech**, **Great Wall**, **Gospower**, **Enhance**, **Delta** completano il panorama con livelli di qualità molto diversi. Nella fascia bassa del mercato, produttori come **Sama** o vari OEM cinesi minori forniscono le piattaforme per prodotti senza marchio.

La conseguenza diretta e controintuitiva di questa struttura è la seguente: **lo stesso marchio può vendere contemporaneamente alimentatori eccellenti e alimentatori pessimi**, perché prodotti da OEM diversi su piattaforme diverse. La serie di fascia alta di un marchio potrebbe essere costruita da Seasonic, e la serie economica dello stesso marchio da un OEM di terz'ordine, con la stessa etichetta e lo stesso logo. **Comprare "un Corsair" o "un Cooler Master" non significa nulla. Bisogna comprare un modello specifico.**

Questo, incidentalmente, è esattamente il motivo per cui esistono e sono così utili le *community tier list* (sezione 7.2): esse elencano i *modelli*, non i marchi.

### 6.2 Protezioni

Un alimentatore ben progettato include un circuito supervisore integrato che monitora continuamente le uscite e interviene spegnendo tutto in caso di anomalia. Le protezioni che devono essere presenti — e la cui presenza è dichiarata nelle specifiche tecniche, ma la cui *corretta calibrazione* è verificata solo in test indipendenti — sono le seguenti.

**OVP** (*Over Voltage Protection*) interviene se una tensione di uscita sale al di sopra di una soglia preimpostata (tipicamente intorno ai 13.5-14 volt sul rail da 12V). Questa protezione salva letteralmente il computer: senza di essa, un guasto nel circuito di regolazione può inviare tensioni distruttive ai componenti.

**UVP** (*Under Voltage Protection*) fa l'opposto: spegne l'alimentatore se una tensione scende al di sotto di una soglia (intorno ai 10-10.5 volt sui 12V). Questo impedisce ai componenti di operare in condizioni di *brownout*, che possono causare corruzione dei dati sui drive.

**OCP** (*Over Current Protection*) monitora la corrente erogata su ogni rail (o su ogni rail virtuale, negli alimentatori multi-rail) e interviene se supera la soglia. È la protezione più delicata da calibrare, per i motivi visti riguardo ai transitori: troppo stretta e l'alimentatore spegne il PC ogni volta che la scheda video ha un picco, troppo lasca e non protegge.

**OPP** (*Over Power Protection*) guarda la potenza *totale* erogata, non la corrente di un singolo rail, e interviene se il sistema richiede più di quanto l'alimentatore possa fornire. È la rete di sicurezza globale.

**SCP** (*Short Circuit Protection*) è la più fondamentale: rileva un cortocircuito netto tra un rail e la massa e spegne immediatamente. Deve essere sempre presente, senza eccezioni.

**OTP** (*Over Temperature Protection*) usa un sensore interno, solitamente sul dissipatore secondario, e spegne l'alimentatore se la temperatura interna supera una soglia critica. È l'unica protezione che spesso *manca* anche in unità altrimenti decenti, ed è quella che conta di più se l'alimentatore è montato con la ventola verso il basso in un case con poco spazio, o se una ventola si guasta.

Infine, ci sono protezioni "ancillari" ma non banali: **SIP** (*Surge and Inrush Protection*) protegge contro i picchi di corrente all'avvio e i transitori di rete, e **NLO** (*No Load Operation*) permette all'alimentatore di funzionare in sicurezza senza un carico collegato, utile durante i test.

Il punto pratico: quasi tutti i datasheet dichiarano tutte queste sigle. La dichiarazione non costa nulla. Quello che conta è che siano *implementate* e *calibrate*, e questo si può scoprire solo leggendo un test indipendente (Cybenetics, HWBusters, TechPowerUp, Tom's Hardware) dove il recensore cerca deliberatamente di attivarle.

### 6.3 Condensatori, ventole, materiali

I **condensatori**, assieme alla ventola, sono l'elemento che invecchia. Il condensatore elettrolitico primario (quello grande, sui 400 V) e i condensatori secondari lavorano in ambiente caldo e devono mantenere la propria capacità per anni. Il discriminante classico è la provenienza: i condensatori giapponesi — i marchi da cercare nelle recensioni sono **Nippon Chemi-Con**, **Rubycon**, **Nichicon**, **Panasonic** — hanno tolleranze migliori, minore resistenza serie equivalente e, soprattutto, una vita utile dichiarata molto più lunga rispetto ai condensatori taiwanesi o cinesi di fascia bassa (Teapo, CapXon, Elite, Su'scon). L'altro parametro è la **temperatura nominale**: i condensatori da **105 °C** sono lo standard di qualità, mentre quelli da 85 °C sono un segno di risparmio. Nel secondario, gli alimentatori moderni di qualità usano diffusamente i **condensatori a polimeri solidi**, che non hanno elettrolita liquido, non si seccano e tollerano meglio il calore.

La **ventola** merita attenzione perché è il componente che si sente. Il tipo di cuscinetto determina rumorosità e durata: un **cuscinetto a manicotto** (sleeve bearing) è il più economico, diventa più rumoroso con l'età e ha vita ridotta se montato in orizzontale; un **rifle bearing** è un miglioramento dello sleeve bearing, ed è migliore; un **ball bearing** dura a lungo ma è più rumoroso; un **FDB** (Fluid Dynamic Bearing) è ormai lo standard di qualità: silenzioso, con vita utile dichiarata di decine di migliaia di ore e insensibile all'orientamento di montaggio. Le ventole a **levitazione magnetica** (maglev) sono un'ulteriore evoluzione, presente nel segmento premium.

Merita una menzione la **modalità Zero-RPM** (o semi-fanless, o "hybrid mode"). Si tratta della funzione per cui la ventola rimane completamente ferma sotto una certa soglia di carico e di temperatura. Il vantaggio è ovvio: un alimentatore che non gira è un alimentatore che non fa rumore e non aspira polvere. Lo svantaggio potenziale è che, rimanendo fermo a lungo, l'alimentatore accumula calore, e quando la ventola si accende si può percepire un fastidioso ciclo on/off se l'isteresi è mal progettata. Le unità di qualità gestiscono questo con un'ampia isteresi. Molti alimentatori hanno un interruttore fisico sul retro per disabilitare la modalità zero-RPM: se il vostro alimentatore opera in un case caldo o vicino al suo limite, disabilitarla è una scelta prudente.

### 6.4 La garanzia come indicatore

Ecco un criterio semplice e quantitativo, che non richiede di leggere una sola recensione tecnica: **la lunghezza della garanzia è il miglior indicatore singolo della fiducia del produttore nel proprio prodotto**. Un produttore che offre dieci o dodici anni sta dichiarando, con un impegno contrattuale che gli costa soldi veri, che si aspetta che quell'unità funzioni per un decennio. Nessuno regala dodici anni di garanzia su un prodotto costruito con condensatori scadenti.

| Garanzia | Cosa suggerisce | Range tipico |
|---|---|---|
| 2-3 anni | Prodotto economico, componenti basilari | Entry / da evitare per build serie |
| 5 anni | Prodotto decente, fascia media | Mid, accettabile |
| 7 anni | Buon prodotto, componenti di qualità | Mid-high |
| 10 anni | Prodotto di alta qualità, condensatori giapponesi | High-end |
| 12 anni | Top del mercato consumer | Flagship |

Prestate attenzione a un dettaglio non banale: in Italia e in Europa vige una garanzia legale di conformità di due anni verso il venditore, che è diversa dalla **garanzia commerciale del produttore**. Quest'ultima è quella che conta qui, ed è offerta direttamente dal produttore, spesso richiedendo la registrazione del prodotto. Conservate la fattura e, se il produttore lo richiede, registrate l'alimentatore al momento dell'acquisto.

---

## 7. Marche e linee di riferimento

Questa sezione va letta con una avvertenza: **le gamme dei produttori cambiano ogni 18-24 mesi**, e un modello eccellente può essere rimpiazzato da un successore costruito da un OEM diverso e di qualità inferiore, pur mantenendo lo stesso nome commerciale. Quanto segue è quindi una mappa delle *tendenze storiche* e della *reputazione delle linee*, non un listino prezzi. **[dato volatile: verificare sempre il modello esatto su una tier list aggiornata prima dell'acquisto]**

### 7.1 Marche principali

**Seasonic** è il punto di riferimento assoluto per reputazione, essendo sia OEM che brand. La linea **Prime** (nelle varianti Gold, Platinum, Titanium, e più recentemente le versioni PX/TX con connettore 12V-2x6) rappresenta l'apice del mercato consumer, con dodici anni di garanzia. La linea **Focus** (GX, PX) è la fascia medio-alta, offrendo un eccellente rapporto qualità/prezzo, con dieci anni di garanzia. Le linee più economiche di Seasonic (Core, S12) sono più ordinarie e non godono della stessa reputazione: anche con Seasonic, il nome sulla scatola non basta.

**Corsair** ha una gamma ampia e stratificata, ed è l'esempio perfetto del perché sia necessario guardare il modello. La linea **CX** è la fascia economica, storicamente costruita da CWT su piattaforme basilari: accettabile ma non entusiasmante, con garanzia più breve. La linea **RM**, e soprattutto **RMx**, è il cuore della loro offerta: unità Gold, silenziose, ben costruite, con dieci anni di garanzia; sono tra le più frequentemente consigliate per un build di fascia media-alta. Le linee **HX** e **HXi** sono un gradino superiore (Platinum, monitoraggio digitale nella variante "i"), e **AX/AXi** rappresenta l'apice (Titanium). La linea **SF** copre il formato SFX per case compatti ed è considerata eccellente.

**be quiet!** è un marchio tedesco che, come suggerisce il nome, si concentra sul silenzio. La linea **Pure Power** è l'entry-mid: onesta, funzionale, senza fuochi d'artificio. La linea **Straight Power** è la fascia medio-alta ed è molto apprezzata. Le linee **Dark Power** e **Dark Power Pro** sono il top di gamma: prestazioni eccellenti, silenzio quasi assoluto, prezzo elevato. La forza trasversale del marchio risiede nelle ventole, che sono di produzione propria e tra le migliori del settore.

**EVGA** ha una storia peculiare: le sue unità di fascia alta, costruite su piattaforme Super Flower (le serie **G**, **P**, **T**, e in particolare le SuperNOVA G2/G3/G5/G6/G7 e P2/T2), sono state per anni tra le più consigliate. Le sue unità economiche (serie **N1**, **W1**, **BQ**, **BR**) erano costruite da OEM di livello inferiore e vanno dal mediocre al decisamente da evitare. Va notato che EVGA ha annunciato l'uscita dal mercato delle schede grafiche nel 2022 e ha progressivamente ridotto la sua presenza sul mercato **[dato volatile: verificare la disponibilità attuale dei prodotti e il supporto post-vendita]**.

**Cooler Master** è un brand con una gamma molto eterogenea. La linea **V** (V Gold, V Platinum, V SFX) è di ottima qualità. Le linee **MWE** sono entry-level, e la loro qualità è variata significativamente tra le revisioni: alcune sono accettabili, altre no. Questo è un caso da manuale in cui il nome della serie non basta, e va conosciuta la revisione esatta.

Anche **Thermaltake** ha una gamma altrettanto eterogenea. La linea di fascia medio-alta **Toughpower GF/PF/GT** è generalmente buona; le linee economiche (Smart, Litepower) sono da evitare per qualsiasi sistema con una scheda grafica seria.

**NZXT** ha adottato negli anni un approccio semplice: la serie **C** (C750, C850, C1000 Gold e Platinum) è costruita su piattaforme Seasonic e gode di un'ottima reputazione. È un esempio di brand che, non avendo una fascia economica di ripiego, ha una qualità costantemente omogenea.

**MSI** è entrata nel mercato degli alimentatori relativamente tardi. La linea **MPG A-GF** (e quelle successive) è di buona qualità. Alcune linee economiche **MAG** hanno ricevuto in passato recensioni molto critiche, con casi di unità che fallivano i test di sicurezza **[volatile data]**. È un altro brand che va valutato modello per modello.

Ci sono poi brand che hanno guadagnato terreno di recente nel mercato di fascia alta, come **ASUS** con le linee ROG Thor e Loki, **Super Flower** che vende anche con il proprio marchio (Leadex), **XPG/ADATA**, **Lian Li**, **Phanteks**, **FSP** con il proprio marchio, e **Antec** (la cui linea Signature è molto apprezzata). E dall'altra parte, c'è l'intero universo di marchi che non andrebbero nemmeno presi in considerazione: le unità da 30-40 euro con wattaggi fantasiosi stampati sulla scatola, vendute sui marketplace generici, spesso con nomi che cambiano ogni sei mesi. Il criterio è brutale ma efficace: **se non riuscite a trovare una singola recensione tecnica indipendente di quel modello, non compratelo.**

### 7.2 Tier List della Community

Dato che nessun acquirente può leggere una recensione tecnica per ognuno dei trecento modelli in commercio, la community ha prodotto uno strumento eccezionalmente utile: le **PSU tier list**. Storicamente, la più nota è quella mantenuta dal **Cultists Network** (la successora della vecchia tier list di LinusTechTips, che è stata ritirata) **[volatile data: le tier list cambiano manutentori e posizioni nel tempo; cercate la versione attuale]**.

Il funzionamento è semplice: gli alimentatori vengono catalogati per modello esatto e wattaggio, e assegnati a un tier (Tier A, B, C, D... o schemi simili) in base ai test indipendenti disponibili, ai dati di reso in garanzia e alla piattaforma OEM. La regola pratica è: **per un sistema con scheda grafica dedicata, non scendere sotto i tier medio-alti; per un sistema con scheda di fascia alta, rimanere nei tier più alti.** Le liste indicano anche esplicitamente i modelli pericolosi, quelli da non comprare a nessun prezzo.

Altre risorse da conoscere sono il **database Cybenetics** (che offre i report completi menzionati), le recensioni di **HWBusters**, **TechPowerUp**, **Tom's Hardware**, e i test distruttivi di **GamersNexus**. Sono tutti gratuiti, e coprono collettivamente quasi tutti i modelli degni di considerazione.

---

## 8. Come scegliere, passo dopo passo

### 8.1 La Procedura

**Primo passo: calcolare il wattaggio.** Sommare il TBP della GPU, il consumo di picco della CPU (PL2 o PPT), e 80-100 watt per tutto il resto. Aggiungere un margine del 30-40%. Arrotondare per eccesso al successivo incremento commerciale (gli incrementi tipici sono 550, 650, 750, 850, 1000, 1200, 1600 W).

**Secondo passo: decidere lo standard.** Se la scheda grafica usa un connettore a 16 pin, esigere ATX 3.0 o 3.1 con **12V-2x6 nativo**. Se usa connettori PCIe a 8 pin, ATX 3.x è comunque preferibile ma non un requisito assoluto.

**Terzo passo: fissare la soglia di efficienza.** Nel 2026, con l'attuale disponibilità e prezzi **[dati volatili]**, non c'è motivo di scendere sotto l'**80 PLUS Gold** per un sistema con scheda grafica dedicata. Il Bronze è accettabile solo in un PC da ufficio a basso consumo. Platinum e Titanium hanno senso per chi tiene il PC acceso molte ore al giorno o cerca la massima silenziosità.

**Quarto passo — quello che nessuno fa e che fa tutta la differenza: verificare il modello specifico.** Cercare il modello esatto (non la serie, il *modello con il suo wattaggio*) su una tier list aggiornata e, se possibile, leggere il report Cybenetics o una recensione tecnica. Cinque minuti di ricerca a questo punto valgono più di tutti i passi precedenti messi insieme.

**Quinto passo: scegliere la modularità.** Full modular se il budget lo permette e il case è compatto o ha una finestra. Semi-modular come ragionevole compromesso. Non-modular solo per build economiche in case grandi.

**Sesto passo: controllare la garanzia.** Sotto i cinque anni, in una build seria, è una bandiera rossa. Dieci anni è quello che ci si aspetta da un prodotto di fascia medio-alta.

**Settimo passo: verificare i dettagli fisici.** La lunghezza dell'alimentatore (le unità da 1000W e oltre possono essere più lunghe di 160-180mm e potrebbero non entrare in tutti i case), il fattore di forma (ATX standard contro **SFX** o **SFX-L** per case Mini-ITX), il numero di connettori PCIe e SATA effettivamente disponibili, e la lunghezza dei cavi (nei case con PSU montato in basso e connettore EPS montato in alto, un cavo CPU corto è un problema serio: sono necessari almeno 60-65cm, e in molti case full tower è consigliabile un cavo di estensione).

### 8.2 Raccomandazioni basate sul budget

**[dati volatili: i prezzi indicati sono ordini di grandezza per il mercato italiano e cambiano frequentemente]**

Nella **fascia entry-level** (circa 60-90 euro, per sistemi da 450-650W), l'obiettivo è comprare un prodotto decente senza inseguire il meglio assoluto. Questa fascia include linee come Corsair RM (wattaggi minori), be quiet! Pure Power, Seasonic Focus GX nei wattaggi inferiori, MSI MPG A-GF. Il consiglio: puntare a un'unità Gold semi-modulare da 650W di un marchio conosciuto, con almeno cinque anni di garanzia. Questo è già sufficiente per una scheda grafica di fascia media, e si può aggiornare la GPU in seguito senza cambiare tutto.

Nella **fascia media** (circa 100-150 euro, per sistemi da 750-850W), il mercato è affollato e la scelta è ampia. Corsair RMx, be quiet! Straight Power, Seasonic Focus GX/PX, NZXT serie C, Cooler Master V Gold, ASUS TUF Gaming: sono tutti prodotti competenti. Il consiglio: **850W Gold, full modular, ATX 3.1 con 12V-2x6 nativo, dieci anni di garanzia**. Questa configurazione copre il 90% delle build gaming serie entro il 2026, con spazio per un futuro aggiornamento della scheda grafica.

Nella **fascia alta** (circa 180-350 euro, per sistemi da 1000-1200W e oltre), si sta comprando qualcosa che deve gestire una scheda grafica di fascia alta con transienti brutali, e si sta comprando silenziosità e longevità. Seasonic Prime PX/TX, be quiet! Dark Power Pro, Corsair HX/AX, ASUS ROG Thor. Il consiglio: **1000-1200W Platinum o Titanium, full modular, ATX 3.1, dodici anni di garanzia se possibile**. Se la macchina è una workstation che gira ore al giorno sotto carico, il Titanium ripaga davvero.

Una nota per chi assembla in **SFF** (*Small Form Factor*, case compatti): i formati **SFX** e **SFX-L** hanno un mercato più piccolo e prezzi più alti a parità di potenza erogata. Corsair SF, Cooler Master V SFX, Lian Li SP, FSP Dagger sono i riferimenti usuali. Non usate mai un adattatore SFX→ATX come scusa per infilare un'unità economica in un case piccolo: le unità SFX di qualità hanno una densità di potenza e un dimensionamento termico superiori, progettati per quel volume, e quelle economiche in quel formato sono particolarmente pericolose.

### 8.3 Errori comuni, spiegati

**L'alimentatore no-name da 30 euro.** Questo è l'errore cardinale. Un alimentatore da 30 euro pubblicizzato come "700W" non eroga 700 watt: tipicamente ne eroga 300-400 reali prima di andare in protezione o guastarsi, ha ripple fuori specifica, protezioni assenti o non funzionanti, condensatori di bassa qualità, e nei test distruttivi non è raro che si guasti prendendo fuoco. La logica dell'acquirente — "ho speso 1500 euro in componenti, risparmio sull'alimentatore" — è esattamente l'opposto della razionalità economica. Regola: l'alimentatore dovrebbe rappresentare tra il 7% e il 12% del budget totale del PC.

**Tagliare troppo corto con il wattaggio.** Comprare un PSU da 650W per un sistema che consuma 620W di picco è tecnicamente possibile ma praticamente sbagliato: l'alimentatore opererà al 95% del carico, con la ventola sempre al massimo, i condensatori al loro limite termico, e zero margine per i transienti della GPU. Il primo spegnimento improvviso sotto carico avverrà entro poche settimane.

**Fidarsi solo dell'etichetta 80 PLUS.** Ne abbiamo discusso a lungo. L'efficienza non è qualità.

**Riutilizzare i cavi modulari del vecchio alimentatore.** L'errore più distruttivo. Vale la pena rileggere la sezione 4.2. Quando si cambia alimentatore, si cambiano *tutti* i cavi.

**Daisy-chaining su schede grafiche potenti.** Molti alimentatori forniscono cavi PCIe con **due connettori sullo stesso cavo** (uno 6+2 a metà e un altro 6+2 in fondo). La ragione è economica: serve un solo connettore lato PSU per alimentare due connettori lato GPU. Il problema è che quei due connettori condividono gli stessi tre conduttori da 12 volt del cavo, e un conduttore da 18 AWG è dimensionato ragionevolmente per 7-8 ampere. Due connettori PCIe a 8 pin dovrebbero portare fino a 300 watt combinati, che sono 25 ampere a 12 volt, distribuiti su soli tre fili invece di sei: questo porta a oltre 8 ampere per filo, con riscaldamento del conduttore e caduta di tensione. Per una scheda che consuma poco (150-180 W), non succede nulla. Per una scheda che consuma 300 W o più, il daisy-chaining è una cattiva idea, e le raccomandazioni dei produttori sono unanimi: **un cavo PCIe fisicamente separato per ogni connettore sulla scheda grafica**, prelevato da un'uscita diversa dell'alimentatore. Se l'alimentatore non ha abbastanza uscite PCIe per farlo, è un segno che è sottodimensionato per quella scheda.

**Usare l'adattatore incluso con la GPU invece del cavo nativo.** Gli adattatori da 3× o 4× PCIe a 12VHPWR funzionano, ma aggiungono un punto di contatto e un punto di cedimento meccanico su un percorso che porta fino a 600 watt. Se l'alimentatore ha un cavo 12V-2x6 nativo, usatelo. Se non lo ha, questo è un buon argomento per cambiare l'alimentatore.

**Ignorare le specifiche tecniche dell'etichetta.** Su un alimentatore serio, l'etichetta laterale mostra la distribuzione della potenza tra i rail. Il numero da cercare è la **potenza combinata sul rail da 12V**: su un'unità onesta da 850W, solo il 12V dovrebbe essere in grado di erogare 830-840W (cioè circa 70 ampere). Se un alimentatore da "700W" dichiara solo 400-500W sul 12V, con il resto della potenza attribuito a 5V e 3.3V (che non useranno mai più di 30-50W), quell'alimentatore è, funzionalmente, un'unità da 450-550W venduta come 700W. Questo è un trucco di marketing tipico delle unità economiche e delle generazioni più vecchie, e leggere l'etichetta lo smaschera in dieci secondi.

**Acquistare un alimentatore usato.** I condensatori invecchiano, la ventola si usura, la garanzia non è trasferibile e non si conoscono le condizioni in cui ha operato. È l'unico componente del PC per il quale il mercato dell'usato è categoricamente sconsigliabile.

**Dimenticare la protezione a monte.** Un alimentatore, per quanto buono, non è un protettore di sovratensioni per la rete elettrica. In aree con una rete elettrica instabile — e in molte parti d'Italia lo è — una **ciabatta con protezione da sovratensioni** costa venti euro, e un **UPS** (Uninterruptible Power Supply) con AVR costa circa cento euro e protegge da cali di tensione e interruzioni. Per una build da duemila euro, è un'assicurazione ragionevole. Attenzione a un dettaglio tecnico: gli alimentatori con PFC attivo possono avere problemi con gli UPS economici che emettono un'onda quadra approssimata durante un blackout; per un PC moderno, è necessario un UPS con uscita a **onda sinusoidale pura**.

---

## Riepilogo Operativo — Lista di Controllo per la Scelta dell'Alimentatore

**A. Dimensionamento**
1. Recuperare il **TBP della GPU** dal sito del produttore (non dalla scatola del rivenditore).
2. Recuperare il **consumo di picco della CPU** (PL2 per Intel, PPT per AMD), non il TDP nominale.
3. Aggiungere **80-100W** per scheda madre, RAM, unità, ventole, pompa.
4. Sommare e aggiungere un **margine del 30-40%** per i transitori e per operare alla massima efficienza.
5. Arrotondare alla dimensione commerciale successiva. Verificare con un calcolatore online come controllo incrociato, non come oracolo.

**B. Standard e Connettori**
6. Scheda grafica con **connettore a 16 pin**? → Richiedere **ATX 3.1 con 12V-2x6 nativo**. Nessun adattatore.
7. Contare i **connettori PCIe/12V-2x6 effettivamente disponibili**: uno per ogni connettore della scheda grafica, su cavi separati.
8. Verificare che ci siano abbastanza **connettori SATA** per le unità previste e un **EPS 8+8** se la scheda madre lo richiede.

**C. Qualità**
9. Certificazione minima: **80 PLUS Gold** per qualsiasi sistema con GPU dedicata (tabella EU 230V, non 115V).
10. Cercare il **modello esatto** su una tier list aggiornata (Cultists Network o equivalente). Se il modello non compare da nessuna parte, non acquistarlo.
11. Se disponibile, leggere il **rapporto Cybenetics**: controllare ripple, regolazione, risposta ai transitori, protezioni e il livello **LAMBDA** se il silenzio è importante.
12. Verificare la presenza (e nei test, la funzionalità) di **OVP, UVP, OCP, OPP, SCP, OTP**.
13. Verificare che l'etichetta dichiari una **potenza sul rail da 12V pari ad almeno il 95% della potenza nominale totale**.

**D. Ergonomia e Fisica**
14. Scegliere la **modularità**: full modular se il budget e il case lo giustificano.
15. Controllare la **lunghezza dell'unità** rispetto allo spazio disponibile nel case e il **fattore di forma** (ATX / SFX / SFX-L).
16. Controllare la **lunghezza dei cavi**, specialmente l'EPS della CPU nei case grandi.
17. Preferire ventola **FDB** con **modalità zero-RPM**.

**E. Garanzia e Protezione**
18. **Minimo 5 anni**; 10 anni è lo standard atteso nella fascia medio-alta. Registrare il prodotto se richiesto.
19. Fornire **protezione a monte**: ciabatta con protezione da sovratensioni o, meglio, un **UPS a onda sinusoidale pura**.

**F. Cose da Non Fare, Mai**
20. Non riutilizzare mai i **cavi modulari** di un altro alimentatore, nemmeno della stessa marca.
21. Non usare mai il **collegamento a margherita** (daisy-chaining) dei connettori PCIe su schede grafiche oltre i ~200 W.
22. Non comprare mai un alimentatore **senza marca** o senza recensioni tecniche indipendenti.
23. Non comprare mai un alimentatore **usato**.
24. Non inserire mai il connettore a 16 pin **parzialmente**: deve fare click, senza spazi visibili, e il cavo non deve essere piegato bruscamente nei primi centimetri.

---

*Nota finale sui dati: i modelli, i prezzi, le fasce di produttori e le generazioni di schede grafiche citati in questo capitolo riflettono lo stato del mercato al momento della scrittura e sono intrinsecamente soggetti a rapida obsolescenza. I principi fisici e di progettazione — la conversione AC/DC, il predominio del rail da 12V, il ruolo dei transitori, la distinzione tra efficienza e qualità, la non intercambiabilità dei cavi modulari — non cambiano.*

---

[← Precedente](05-archiviazione.html) · [Tutti i capitoli](./) · [Successivo →](07-case-ventilazione.html)
