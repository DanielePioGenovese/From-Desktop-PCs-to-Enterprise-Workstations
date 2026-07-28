---
title: "Capitolo 9 - Assemblaggio PC, passo dopo passo"
parent: "Edizione italiana"
nav_order: 9
---

<details open markdown="block">
  <summary>Indice del capitolo</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## Introduzione: perché questo capitolo è diverso da tutti gli altri

Fino a questo punto, il libro ha lavorato in astratto. Abbiamo studiato la CPU come architettura, la RAM come gerarchia di latenze, la GPU come motore di calcolo parallelo, l'alimentatore come convertitore di energia. Erano oggetti di conoscenza. Da questo capitolo in poi, diventano oggetti fisici: hanno un peso, hanno degli spigoli, hanno dei pin che si piegano, hanno dei connettori che entrano in un solo verso e che si fondono se li inserisci male. La differenza tra sapere cos'è un socket LGA e riuscire a chiudere una levetta di ritenzione senza tremare è la stessa che c'è tra leggere un manuale di volo e volare.

Va detto subito, per alleviare l'ansia: **assemblare un PC è meccanicamente facile**. Non richiede saldature, non richiede misurazioni con il multimetro, non richiede esperienza. È un gioco di incastri pensato apposta perché non si possano fare troppi errori, con connettori polarizzati (cioè, sagomati per entrare in un solo verso) e tacche di allineamento ovunque. Un adulto attento che segue le istruzioni finirà il lavoro in due o tre ore la prima volta, e in quaranta minuti la quinta volta.

Ma — ed ecco il punto — la facilità meccanica coesiste con **poche azioni irreversibili e costose**. Un pin della CPU piegato può significare una scheda madre da 300 euro da buttare. Un connettore ARGB 5V inserito in un header 12V distrugge la striscia LED e, a volte, il controller sulla scheda madre. Un cavo di alimentazione 12V-2×6 non completamente inserito può fondere il connettore della GPU sotto carico. Non c'è una lunga coda di errori medi: c'è un enorme insieme di errori innocui e una minuscola manciata di errori letali. L'intero capitolo è costruito attorno a questa distinzione. Ogni passaggio ti dirà: di cosa hai bisogno, come farlo fisicamente, **quanta forza applicare** (è la variabile che nessuno ti spiega mai), cosa non fare per nessun motivo e come verificare di averlo fatto correttamente prima di proseguire.

Un avvertimento finale sui dati: i nomi dei socket, i modelli dei connettori e gli acronimi dei chipset qui menzionati sono quelli in circolazione al momento della stesura. Sono la parte del libro che invecchia più rapidamente. Le **procedure fisiche** — leve, tacche, impostazioni di coppia, ordine dei passaggi — sono, tuttavia, straordinariamente stabili: chiunque abbia assemblato un PC nel 2015 riconoscerà ogni gesto di un assemblaggio del 2026. Quando i dati saranno volatili, lo indicherò esplicitamente.

---

## 0. Preparazione: il novanta per cento del lavoro è fatto prima di aprire le scatole

### 0.1 Gli attrezzi: pochi, ma quelli giusti

L'attrezzo di cui hai veramente bisogno è **uno**: un cacciavite **Phillips PH2**, magnetico, con un'asta lunga almeno 10-12 centimetri.

Chiariamo l'acronimo, perché è il primo esempio di come funziona questo lavoro. "Phillips" è il nome del tipo di testa della vite — la croce con i lati leggermente svasati — e **PH2** è la sua dimensione. PH0 e PH1 sono più piccoli, PH3 è più grande. Praticamente tutte le viti di un PC moderno (viti della scheda madre, viti dell'alimentatore, viti del pannello, viti delle staffe PCIe, viti delle ventole) usano la testa PH2. Le uniche eccezioni sono le micro-viti per gli SSD M.2, che richiedono un PH1 o addirittura un PH0, e che generalmente vengono fornite con il loro cacciavite nella scatola della scheda madre.

La ragione del **magnetico** merita una riga, perché è il punto che spaventa di più i principianti: no, un cacciavite magnetizzato **non danneggia** i componenti del PC. La paura nasce da un'analogia sbagliata con i vecchi hard disk meccanici e i floppy disk, e in ogni caso, il campo di un cacciavite magnetizzato è molto debole e si estende solo per pochi millimetri. Al contrario, il magnetismo ti salva la giornata: le viti si attaccano alla punta e non cadono all'interno del case, in quello spazio irraggiungibile sotto la scheda madre da cui possono essere recuperate solo capovolgendo tutto e scuotendo. Una vite persa in un case chiuso che viene poi acceso è, questo sì, una potenziale fonte di cortocircuito.

Intorno al cacciavite, poco altro è necessario:

**Fascette per cavi.** Ne avrai bisogno di una decina. Quelle in nylon usa e getta vanno benissimo (da tagliare con tronchesi, mai strappare); se prevedi di lavorare frequentemente all'interno del PC, le **fascette in velcro** sono preferibili, in quanto riutilizzabili e spesso incluse con l'alimentatore o il case.

**Una luce.** Il retro di un case, l'area degli header sul bordo inferiore della scheda madre, l'interno di un alloggiamento per hard disk: sono tutti luoghi bui. Una lampada da scrivania regolabile o una lampada frontale trasforma un'operazione goffa in una chiara. Usare la luce del telefono, tenuta con la mano sinistra mentre la mano destra cerca di inserire un connettore F_PANEL da tre millimetri, è una ricetta per la frustrazione.

**Una superficie ampia, piana, stabile e non tessile.** Un tavolo da pranzo sgombro è perfetto. Ti serve spazio per il case sdraiato, per la scatola della scheda madre (che ti farà da banco di lavoro, ci arriviamo), per i componenti in attesa di installazione e per le scatole vuote, che vanno conservate: contengono gli accessori, sono utili per eventuali resi, e la scatola della scheda madre è la superficie ideale per il pre-assemblaggio.

**Tronchesine o forbici** per le fascette, e opzionalmente un **contenitore per le viti** — una ciotola, un portauova, un tappo di barattolo. Le viti dei PC sono di almeno cinque tipi diversi, e mescolarle è un modo lento per perdere venti minuti.

Cosa **non** ti serve, nonostante quello che potresti sentirti dire: pinze (se ti servono le pinze, stai sbagliando qualcosa), pasta termica se il dissipatore ce l'ha già applicata, un tappetino antistatico professionale, un tester.

### 0.2 Elettricità Statica (ESD): la Paura Giusta, nella Giusta Misura

**ESD** sta per *ElectroStatic Discharge*, scarica elettrostatica. È il fenomeno per cui due materiali che sfregano tra loro si scambiano elettroni, e uno dei due si carica; quando quel corpo carico tocca un conduttore a potenziale diverso, la carica si scarica repentinamente, con una differenza di potenziale che può facilmente superare i **mille volt** e arrivare a decine di migliaia. È la scintilla che prendi toccando la maniglia della macchina d'inverno, ed è lo stesso fenomeno che ti fa "scoppiettare" quando ti togli un maglione di lana al buio.

Il punto delicato è questo: la soglia a cui **tu** percepisci una scossa è intorno ai 2.000-3.000 volt, ma i transistor moderni, con isolanti di gate spessi pochi nanometri, possono essere danneggiati da scariche di **poche centinaia di volt**. Questo significa che c'è un ampio intervallo in cui puoi distruggere un componente **senza accorgertene**. Peggio: il danno da ESD è spesso *latente*. Non uccide il chip subito; lo indebolisce. Il PC funziona, magari per settimane, e poi comincia a dare errori casuali, crash inspiegabili, corruzione di dati. È il tipo di guasto più difficile da diagnosticare.

Detto questo, va anche detto che le probabilità sono a tuo favore: i componenti moderni hanno diodi di protezione ESD sui loro ingressi, e milioni di persone assemblano PC ogni anno senza precauzioni e senza conseguenze. La strategia razionale non è la paranoia; è la **routine**. Poche semplici regole, sempre le stesse:

1. **Lavora su una superficie rigida, non su tappeti, moquette o divani.** I tessuti sono la fonte primaria di cariche statiche. Se il pavimento è in moquette, lavora su un tavolo e non strisciare i piedi.
2. **Scarica te stesso prima di toccare i componenti** toccando una grande superficie metallica appena verniciata — tipicamente lo **chassis del case del PC** — e ripeti questa azione ogni volta che ti sei alzato in piedi, hai camminato, o ti sei tolto o messo una felpa.
3. Se vuoi la soluzione tecnicamente corretta, usa un **cinturino antistatico da polso**: una cinghia conduttiva al polso, collegata tramite un cavo (che contiene una resistenza da 1 MΩ per la tua sicurezza) a una messa a terra. La messa a terra più comoda è lo chassis del case del PC. È bene sapere una cosa che quasi nessuno spiega: il cinturino da polso **non "scarica" i componenti**; serve a mantenere **te e il case allo stesso potenziale**, in modo che non ci sia mai un salto di tensione tra la tua mano e la scheda. Il PC non ha bisogno di essere collegato alla presa di corrente (anzi, non dovrebbe esserlo, ne parleremo tra un attimo). L'equipotenzialità è sufficiente.
4. **Conserva i componenti nelle loro buste antistatiche** (quelle grigie, metalliche che formano una gabbia di Faraday) finché non li usi. E non posizionare mai una scheda madre *sopra* la sua busta antistatica: la superficie esterna è leggermente conduttiva e può creare percorsi indesiderati. Posizionala **sopra la scatola di cartone**, che è l'isolante ideale.
5. **Maneggia le schede dai bordi.** Mai dai contatti dorati, mai dai chip.

Una nota che sfata un mito comune: **non** lasciare il PC "collegato con l'interruttore spento per avere la messa a terra". Questo era un consiglio degli anni Novanta, quando gli alimentatori ATX non sempre avevano tensioni attive. Oggi, un alimentatore collegato alla rete elettrica mantiene la linea **5 V standby (+5VSB)** attiva anche quando il PC è spento: se lavori all'interno del case con l'alimentatore collegato, ci sono parti sotto tensione. La regola moderna è: **cavo di alimentazione scollegato dalla parete, sempre, quando le mani sono all'interno del case.**

### 0.3 Il controllo finale di compatibilità: l'ultima uscita prima dell'autostrada

Aprire una scatola significa, in molti negozi, rinunciare al diritto di reso "per ripensamento" (il diritto di recesso rimane, ma un prodotto aperto e assemblato può essere contestato). Quindi, **prima di rompere il primo sigillo**, fai un controllo finale. È noioso e ti salverà una volta su cinque.

Prendi la scatola della scheda madre — è il fulcro di tutte le compatibilità — e controlla questi sei punti, uno per uno:

| # | Cosa controllare | Come | Sintomo se errato |
|---|---|---|---|
| 1 | **Socket** | Il socket stampato sulla scatola della mobo (es. AM5, LGA1851, LGA1700) deve corrispondere *esattamente* al socket della CPU | La CPU non si inserisce fisicamente |
| 2 | **Supporto BIOS CPU** | Sul sito del produttore della mobo, sezione "CPU Support List", trovare il *modello esatto* della propria CPU e leggere la versione minima del BIOS richiesta | La CPU è compatibile con il socket ma il PC non fa il POST: è necessario un aggiornamento del BIOS |
| 3 | **RAM: tipo e QVL** | DDR4 e DDR5 **non** sono interscambiabili (posizione notch diversa, voltaggio diverso, pinout diverso). Controllare anche la QVL (*Qualified Vendor List*, l'elenco dei kit testati) | RAM che non si inserisce, o si inserisce ma non tiene la frequenza dichiarata |
| 4 | **Spazio dissipatore** | Altezza massima dissipatore consentita dal case (in mm) ≥ altezza del proprio dissipatore. Per gli AIO: il case supporta un radiatore di quella lunghezza *in quella posizione*? | Il pannello laterale non si chiude |
| 5 | **Lunghezza GPU** | Lunghezza massima GPU consentita dal case ≥ lunghezza della propria GPU (nota: i cavi di alimentazione occupano altri 3-4 cm se escono lateralmente) | La GPU non si inserisce, o urta i bay degli hard disk |
| 6 | **PSU: watt e connettori** | Potenza sufficiente (con margine) **e** presenza fisica dei connettori richiesti: quanti PCIe 8-pin richiede la GPU? Serve un 12V-2×6? Quanti EPS richiede la mobo? | Non si riesce ad alimentare tutto |

Il punto 2 merita un paragrafo a sé, in quanto è la trappola più insidiosa nel moderno PC building. Un socket ha una vita utile che abbraccia più generazioni di CPU: AM5, ad esempio, ospita CPU rilasciate a distanza di anni. Una scheda madre prodotta nel 2023 e rimasta in magazzino potrebbe avere un firmware che semplicemente **non riconosce** una CPU rilasciata nel 2025: il socket corrisponde, la CPU si installa, ma il PC non si avvia perché il BIOS non sa come inizializzarla. La soluzione esiste ed è ottima, ma va pianificata **in anticipo**: si chiama **BIOS Flashback** (nomi commerciali: *BIOS Flashback*, *Q-Flash Plus*, *Flash BIOS Button*), una funzionalità presente sulle schede madri di fascia media e alta che permette di aggiornare il BIOS **senza CPU, RAM o GPU installate**, semplicemente collegando l'alimentazione, inserendo una chiavetta USB formattata FAT32 con il file del BIOS rinominato come richiesto, e premendo un piccolo pulsante sul retro. Se la vostra CPU è nuova e la vostra scheda madre potrebbe essere di vecchio stock, **verificate che la scheda abbia il Flashback**: è la differenza tra un'ora di attesa e una settimana persa a cercare di farsi prestare una vecchia CPU.

### 0.4 La Strategia: Pre-assemblaggio "a banco"

Ecco il consiglio che distingue chi ha assemblato dieci PC da chi sta assemblando il primo, e che nessuna guida illustrata sottolinea abbastanza: **non installare prima la scheda madre nel case.**

Il metodo corretto è il **pre-assemblaggio a banco** (detto anche *bench build* o *breadboarding*). Si posiziona la scheda madre **sopra la sua scatola di cartone** — non sulla busta antistatica, come detto — e su quella superficie comoda, ben illuminata e facilmente accessibile, si installano: **CPU, RAM, SSD M.2 e (se ad aria) il dissipatore**. Solo dopo si prende il blocco così assemblato e lo si cala nel case.

Ci sono tre solide ragioni per questo.

Il primo è **ergonomico**: installare una CPU dentro un case significa lavorare in verticale, con poca luce, e il rischio che il componente scivoli. Installarla su un tavolo significa lavorare in orizzontale, con la gravità che assiste invece di ostacolare, permettendo di vedere chiaramente i pin e le tacche. Lo stesso vale per la RAM, che va spinta con decisione: farlo con la scheda madre già avvitata nel case significa flettere il PCB (il *Printed Circuit Board*, il supporto in fibra di vetro su cui è costruita la scheda) contro i suoi punti di fissaggio; farlo su una superficie rigida significa che la forza viene assorbita dal tavolo.

Il secondo è **diagnostico**, ed è il più importante. Il pre-assemblaggio consente il **test POST fuori dal case**: installa la CPU, il dissipatore, un singolo stick di RAM, la GPU e l'alimentatore, collega un monitor e prova ad accenderlo cortocircuitando momentaneamente i due pin del Power Switch con la punta di un cacciavite (o usando il pulsante di accensione integrato, se la scheda ne ha uno). Se compare la schermata del BIOS, sei certo che i quattro componenti più costosi e difficili da sostituire **funzionano e sono compatibili tra loro**. Se il PC non si avvia, hai un sistema nudo con cinque cavi, dove isolare il problema è banale. Confronta questo con lo scenario alternativo: installi tutto nel case, con quaranta cavi, ventole, hard disk, e non si avvia. Ora il colpevole potrebbe essere qualsiasi cosa, e per indagare, devi smontare tutto. Il test POST richiede dieci minuti e li ripaga dieci volte nello scenario peggiore.

Il terzo è **pratico**: alcuni case rendono impossibile montare il backplate di certi dissipatori una volta avvitata la scheda madre, se il ritaglio posteriore del *motherboard tray* non è allineato con il socket. Meglio scoprirlo prima.

L'unica eccezione alla regola è il **radiatore di un AIO** (*All-In-One*, il sistema di raffreddamento a liquido chiuso e pre-assemblato). Il waterblock viene montato sulla CPU sul banco, ma il radiatore, che è collegato ad esso da tubi corti e rigidi, deve essere montato **dopo** che la scheda madre è nel case. Ne parleremo in dettaglio nei punti 4 e 8.

---

## 1. Installazione della CPU sulla scheda madre

È il primo e più temuto passo. Facciamolo bene.

### 1.1 Le due filosofie: LGA e PGA

Storicamente, ci sono stati due modi per collegare una CPU a una scheda madre.

**PGA** (*Pin Grid Array*): i pin — centinaia di sottili aghi metallici — sono **sulla CPU**, e il socket sulla scheda madre è una matrice di fori. Questo è lo schema usato da AMD fino al socket AM4 incluso. Il rischio è interamente sul processore: se lo fai cadere o lo posizioni in modo errato, pieghi i suoi pin, e i pin di una CPU possono (con pazienza, una lente d'ingrandimento e la punta di una lametta da barba) anche essere **raddrizzati**.

**LGA** (*Land Grid Array*): i pin si trovano **sul socket della scheda madre** — un migliaio o più di aghi elastici, sottilissimi, montati su una piastrina — e la CPU ha solo dei pad di contatto piatti e dorati (*lands*, appunto). È lo schema usato da Intel da vent'anni e, dal 2022, anche da AMD con il socket **AM5**. Il rischio si è spostato: la CPU è quasi indistruttibile, ma il socket della scheda madre è **estremamente fragile**. Un pin LGA piegato è largo qualche decina di micron; raddrizzarlo richiede un microscopio, mano ferma e un'incredibile fortuna, e nella maggior parte dei casi la scheda madre è da buttare — e le garanzie **non coprono** i danni meccanici al socket.

Conseguenza operativa, valida per **tutte** le piattaforme attuali (Intel LGA e AMD AM5):

> **MAI, per nessun motivo, toccare i pin del socket. Non con le dita, non con un cacciavite, non con una spazzola, non con un panno.** Se cade qualcosa nel socket, non "spazzolatelo via": soffiate delicatamente, o usate aria compressa da lontano.

### 1.2 La Procedura, Passo per Passo

Il socket è coperto da un **coperchietto di plastica** (solitamente nero) protettivo. **Non toglietelo a mano.** Sui socket moderni si stacca da solo quando si chiude la levetta con la CPU dentro, oppure va rimosso solo dopo aver aperto il meccanismo. Strapparlo via prematuramente è uno dei modi più comuni per danneggiare i pin. (Conservatelo: se un giorno doveste mandare la scheda madre in RMA — *Return Merchandise Authorization*, la procedura di reso in garanzia — va riattaccato, e alcuni produttori rifiutano i resi senza il coperchietto.)

**Passo 1 — Aprire il meccanismo di ritenzione.** Su **AM5** e **Intel LGA**, il principio è lo stesso: una levetta metallica laterale, tenuta da un gancio. Premete la levetta **verso il basso e leggermente di lato** per sganciarla, quindi sollevatela fino in fondo (circa 90-100 gradi). Vedrete la piastra di ritenzione (*load plate*) sopra il socket sollevarsi. La levetta oppone una notevole resistenza in questa fase: è normale, è una molla precaricata.

**Passo 2 — Prendere la CPU.** Prendetela **dai bordi laterali**, con pollice e indice, come fareste con un CD. Mai dalle facce.

**Passo 3 — Allineare.** Questo è il passo cruciale, e va fatto **guardando**, non armeggiando. Ci sono due sistemi di allineamento ridondanti:
- Un **triangolo dorato** stampato su un angolo del package della CPU deve corrispondere a un **triangolino** (o una freccia, o un angolo smussato) stampato su un angolo del socket. Su AM5, il triangolo è ben visibile; su Intel LGA, lo troverete serigrafato sulla cornice.
- Delle **tacche** (mezze lune) sui lati corti del package si inseriscono in apposite **linguette** sul socket. Sono intenzionalmente asimmetriche: se la CPU è ruotata, non si appoggerà piatta.

**Passo 4 — Appoggiare, non premere.** Questa è la frase da ricordare: **la CPU si appoggia, non si inserisce**. Tenetela orizzontale, portatela sopra il socket e abbassatela **dritta dall'alto**, senza inclinarla, senza farla scivolare lateralmente sui pin. Deve **cadere nella sua sede con un movimento di un millimetro** e rimanere lì ferma. La forza da applicare in questo passo è **zero**. Se dovete premere, è sbagliato: sollevatela dritta e riprovate.

**Come verificare di aver fatto correttamente:** con un dito, date un leggerissimo colpetto laterale alla CPU. Dovrebbe avere un micropigiama (una frazione di millimetro) e tornare in posizione. Se non si muove *per niente*, potrebbe essere incastrata storta; se dondola vistosamente, non è in sede. Poi guardate il profilo **da sopra e di lato**: la CPU deve essere perfettamente parallela al piano del socket, senza alcun angolo sollevato.

**Fase 5 — Chiudere.** Abbassate la piastra di carico e riportate la levetta in basso, re-ingaggiandola sotto il gancio. **Qui, la resistenza è forte, e questo spaventa tutti.** La forza necessaria per chiudere un moderno socket LGA è nell'ordine di **50-70 newton** (l'equivalente di cinque-sette chilogrammi di spinta): serve a premere la CPU contro oltre un migliaio di pin elastici, assicurando un contatto affidabile su ognuno. **È normale sentire una resistenza significativa e anche un piccolo scricchiolio.** Quello che *non* è normale è un **blocco duro**: se la levetta non scende con pressione progressiva e decisa, **fermatevi**, riaprite e ricontrollate l'allineamento. La resistenza è elastica e continua; l'ostacolo è duro e improvviso. Imparare a distinguere queste due sensazioni è, letteralmente, l'abilità chiave per questo passaggio.

Il cappuccio di plastica, sui socket Intel, salterà via da solo alla chiusura. Su AM5, va rimosso durante il processo secondo manuale.

### 1.3 Errori comuni

**Pin piegato.** Questo deriva quasi sempre da due azioni: far scivolare la CPU sul socket invece di appoggiarla, o forzare la levetta con la CPU storta. Se, dopo un errore, il PC non si avvia, o si avvia ma non vede metà della RAM (i pin dei canali di memoria passano dal socket!), sospettate un pin piegato. Ispezionatelo con una torcia radente e una lente d'ingrandimento: i pin devono formare file perfettamente regolari, come un campo di grano pettinato. Una fila che "ondeggia" indica un pin fuori posto.

**CPU montata storta ("a 90 gradi").** Impossibile su una piattaforma sana, perché la CPU non è quadrata nei suoi ancoraggi — ma è possibile forzandola. Se avete anche solo un dubbio, riaprite e guardate il piccolo triangolo.

**Toccare la superficie dorata (IHS).** L'**IHS** (*Integrated Heat Spreader*, il coperchio metallico che vedete sopra la CPU) va tenuto pulito: le impronte digitali lasciano unto, che è un pessimo conduttore termico. Se lo toccate, pulitelo con un panno in microfibra e alcool isopropilico.

**Non conservare la cover del socket.** Vedi sopra: può invalidare la garanzia.

---

## 2. Installazione della RAM

Questo sembra il passaggio più banale, ma è in realtà la causa numero uno di "il mio nuovo PC non si accende".

### 2.1 Perché non tutti gli slot sono uguali: dual channel

La memoria di sistema comunica con la CPU tramite l'**IMC** (Integrated Memory Controller, il controller di memoria che ora risiede **dentro** la CPU, non più nel chipset). L'IMC delle moderne CPU consumer è **dual channel**: espone due canali indipendenti alla RAM, ognuno con il proprio bus. Se popolate un solo canale, dimezzate la banda disponibile. L'effetto è drammatico dove la banda conta — nelle **iGPU** (GPU integrate, la grafica integrata nella CPU, che usa la RAM di sistema come memoria video) può significare **il doppio dei frame**; nel gaming con GPU dedicata, parliamo comunque di percentuali a doppia cifra nei casi peggiori.

Ora: una scheda madre ATX standard ha **quattro** slot DIMM, ma ci sono **due** canali. Ogni canale ha due slot. La domanda diventa: se ho due banchi, dove li metto?

La risposta, quasi universalmente, è: **negli slot 2 e 4 a partire dalla CPU**, cioè quelli etichettati **A2 e B2** (o **DIMM_A2 / DIMM_B2**, o su alcune schede DDR5 con nomenclatura diversa). Sono i due slot **più lontani** dalla CPU tra quelli disponibili, ed è controintuitivo: si penserebbe che più vicino sia meglio.

La ragione è l'**integrità del segnale**. Le tracce che trasportano il bus di memoria dalla CPU agli slot sono linee ad altissima frequenza; uno slot vuoto lasciato "prima" di uno popolato crea uno **stub**, una sezione non terminata della linea che genera riflessioni del segnale. La topologia a *daisy-chain* usata dalla stragrande maggioranza delle schede consumer è progettata e ottimizzata affinché il segnale sia più pulito quando vengono popolati gli **slot terminali**. Su piattaforme AM5 e DDR5 in generale, dove le frequenze sono molto alte e il controller è già sotto stress, questo non è un dettaglio accademico: installare due banchi negli slot sbagliati può significare **non riuscire ad avviare il profilo EXPO/XMP**, o non fare il POST del tutto.

> **La regola d'oro: apri il manuale della tua scheda madre alla pagina "Configurazione della memoria" e leggi la tabella. Trenta secondi ti separano dalla certezza.** La tabella ti dice esattamente, per 1, 2 e 4 moduli, quali slot popolare. Non tirare a indovinare, non affidarti alla memoria: alcuni modelli (rari, ma esistono) richiedono A1/B1.

Una nota che vale il prezzo del capitolo: **due banchi sono meglio di quattro**. Con quattro moduli, l'IMC deve pilotare il doppio dei carichi elettrici, e la frequenza massima stabile **cala**, spesso in modo significativo (su DDR5, un kit che supporta 6000 MT/s con 2 moduli potrebbe faticare sopra i 4800 con 4 moduli). Se ti servono 32 GB, compra **2×16**, non 4×8. E soprattutto: **non mescolare kit diversi**. Due kit 2×16 comprati in momenti diversi, anche se identici sull'etichetta, potrebbero avere chip di produttori diversi e non funzionare insieme alla frequenza nominale. Un kit è testato *nel suo insieme*: se un giorno vuoi 64 GB, compra un nuovo kit 2×32.

### 2.2 La procedura

**Passo 1 — Apri le clip.** Le clip sono le levette di plastica alle estremità dello slot. Alcune schede hanno clip su **entrambi** i lati, mentre altre (sempre più comuni, per fare spazio alla GPU) hanno una clip su un solo **lato**, con l'altra estremità fissa. Guarda e apri quelle presenti, spingendole verso l'esterno finché non scattano.

**Passo 2 — Allinea la tacca.** Il modulo DIMM ha una **tacca** (un'indentazione) sul bordo dei contatti, **non centrata**: non è al centro. Lo slot ha una linguetta corrispondente. Ruota il modulo finché la tacca non si allinea. **Questa tacca è il motivo per cui non puoi installare DDR4 su una scheda DDR5**: è in una posizione diversa proprio per rendere gli errori fisicamente impossibili (le due generazioni hanno tensioni e pinout incompatibili, e un inserimento forzato distruggerebbe qualcosa).

**Passo 3 — Premi.** Questo è l'altro punto in cui devi superare la paura. Posiziona il modulo nello slot, assicurati che sia **perfettamente verticale e allineato**, quindi premi **con i pollici sulle due estremità superiori del modulo, una alla volta o insieme**, con **forza ferma e progressiva**. La forza necessaria è **notevole**: stiamo parlando di una spinta paragonabile a quella che useresti per chiudere una valigia piena. Non è un gesto delicato.

Il modulo scende, e le clip — spinte dalla forma del PCB — **si chiudono da sole con un CLICK udibile e distinto**. Se le clip sono su entrambi i lati, dovresti sentire **due click**. Da qui l'espressione che sentirai ovunque: *"premi fino al doppio click."*

**Come controllare di aver fatto correttamente:** guarda il modulo **di lato, all'altezza dello slot**, e verifica che la **linea dorata dei contatti sia completamente scomparsa** all'interno dello slot, **uniformemente per tutta la sua lunghezza**. Poi prova a tirare delicatamente il modulo verso l'alto: non dovrebbe muoversi di un millimetro. Infine, controlla che le clip siano **verticali e chiuse**, impegnate nelle tacche laterali del modulo.

### 2.3 L'errore che colpisce tutti: il modulo semi-inserito

È statisticamente **la causa numero uno** di "PC nuovo che non fa niente": il modulo è inserito, sembra dentro, la clip da un lato si è chiusa, ma **dall'altro lato è rimasto sollevato di un millimetro**. Il contatto elettrico è parziale o assente, il POST fallisce, il PC si accende (le ventole girano!) ma non dà segnale video, e il LED diagnostico **DRAM** rimane acceso.

Perché succede così spesso? Perché la forza necessaria è maggiore di quanto l'istinto suggerisca, e perché premendo con un solo pollice al centro il modulo tende a incastrarsi da un lato e rimanere alto dall'altro. **Premi alle estremità. Ascolta i click. Guarda i contatti.**

---

## 3. Installazione SSD M.2

### 3.1 Cos'è e quale slot scegliere

Un **SSD M.2** è un'unità a stato solido su una piccola scheda a circuito rettangolare (tipicamente 22 × 80 mm, da cui la designazione **2280**) che si collega direttamente a uno slot sulla scheda madre, senza cavi. Nella quasi totalità dei casi moderni, si tratta di unità **NVMe** (*Non-Volatile Memory Express*, il protocollo specificamente progettato per la memoria flash) che comunicano tramite il bus **PCIe** (*Peripheral Component Interconnect Express*, il bus seriale basato su lane che collega CPU e periferiche).

E qui sta la scelta importante: **non tutti gli slot M.2 su una scheda madre sono uguali.**

Una scheda madre moderna ha tipicamente 2, 3 o 4 slot M.2. Di questi, **solo uno** — quasi sempre quello **più vicino alla CPU**, tra il socket e il primo slot PCIe x16 — è collegato **direttamente alle lane PCIe della CPU**. Gli altri passano attraverso il **chipset**, il chip che funge da hub di espansione ed è collegato alla CPU tramite un link condiviso (DMI su Intel, un link PCIe dedicato su AMD).

Ci sono due conseguenze pratiche. La prima è la **larghezza di banda**: lo slot dalla CPU offre generalmente l'ultima generazione PCIe e x4 completo (quattro lane); gli slot dal chipset possono essere di una generazione più vecchia, e in ogni caso condividono la larghezza di banda del link chipset↔CPU con tutto il resto (USB, SATA, rete, schede di espansione). La seconda è la **latenza**: passare attraverso il chipset aggiunge un passaggio.

> **Regola:** Il tuo SSD primario — quello con il sistema operativo — va nello **slot M.2 primario, quello collegato alla CPU**. Il manuale lo indica esplicitamente, spesso chiamandolo *M2_1*, *M2A_CPU*, o simili, e specifica sempre quali lane utilizza.

C'è una seconda ragione per leggere il manuale: la **condivisione delle risorse**. Su molte schede, popolare un certo slot M.2 **disabilita** le porte SATA, o riduce lo slot PCIe x16 secondario da x4 a x2. Questi sono normali compromessi (le linee sono una risorsa finita), ma dovrebbero essere noti in anticipo, non scoperti dopo aver installato tre unità che non compaiono.

### 3.2 La procedura

**Fase 1 — Rimuovere il dissipatore M.2 della scheda madre.** Le schede madri moderne coprono gli slot M.2 con una piastra metallica (a volte un vero e proprio dissipatore alettato). Si svita con una o due piccole viti (PH1). Rimuoverlo e metterlo da parte con la sua vite.

**Fase 2 — Rimuovere la pellicola dal pad termico.** Questo è il passaggio che **quasi tutti dimenticano**, e vale la pena sottolinearlo: sotto il dissipatore c'è un **pad termico** (un cuscinetto di materiale gommoso conduttivo), e sopra il pad c'è una **pellicola protettiva trasparente**. **Deve essere rimossa.** Se la si lascia, si è inserito un isolante tra il chip e il dissipatore, e il vostro SSD subirà *thermal throttling* (riduzione automatica delle prestazioni a causa di temperatura eccessiva) sotto carico. La pellicola è spesso invisibile a uno sguardo casuale: passare un'unghia su un angolo per trovarne il bordo.

**Fase 3 — Regolare il distanziatore.** Lo slot M.2 accetta diverse lunghezze (2242, 2260, 2280, 22110: le ultime due cifre sono la lunghezza in millimetri). Il punto di montaggio deve essere spostato nel foro corrispondente alla lunghezza del vostro SSD — quasi sempre 2280, che di solito è l'impostazione predefinita. Molte schede recenti hanno sostituito la vite con una **clip a scatto** (nomi commerciali: *M.2 Q-Latch*, *EZ Latch*), che si ruota o si preme senza attrezzi: molto più comoda.

**Fase 4 — Inserire a 30 gradi.** L'SSD **non** entra dritto. Deve essere presentato allo slot con un'**angolazione di circa 20-30 gradi**, spinto finché i contatti non sono completamente inseriti (la tacca sui contatti, come per la RAM, assicura l'orientamento corretto), e **poi abbassato** in piano. La forza è modesta: sentirete il modulo scivolare e fermarsi. Quando lo rilasciate, l'SSD si solleverà di qualche grado — questo è normale, è la molla dei contatti.

**Fase 5 — Fissare.** Tenere l'SSD in piano con un dito e avvitare la micro-vite (o chiudere la clip). **Non stringere troppo**: si sta avvitando una vite M2 su un PCB sottile. "A mano più un soffio" è più che sufficiente; stringere di più rovinerà la filettatura o creperà l'SSD.

**Fase 6 — Rimettere il dissipatore** (con la pellicola rimossa!) e avvitarlo.

**Come controllare:** L'SSD dovrebbe essere piatto, senza gioco. Ma il vero controllo avviene al primo avvio, nel BIOS: se l'unità non compare nell'elenco dei dispositivi di archiviazione, spegnere e ricontrollare l'inserimento.

---

## 4. Installazione del dissipatore

Ecco la parte più delicata e più incompresa dell'intero assemblaggio: la pasta termica. Chiariamo prima il principio, poi le due procedure.

### 4.1 Il principio: perché esiste la pasta termica (e dovrebbe SEMPRE essere applicata)

L'IHS della CPU e la base del dissipatore (la *cold plate*) appaiono perfettamente lisce. Non lo sono. Al microscopio, entrambe le superfici sono catene montuose: rugosità di qualche micron, più eventuali deformazioni macroscopiche (molti IHS sono leggermente concavi o convessi). Se le appoggi una sull'altra, il contatto metallo-metallo avviene solo su una **piccola frazione della superficie** — il resto sono microscopiche sacche d'aria. E l'aria è uno dei **più efficaci isolanti termici** che si conoscano (è il motivo per cui funzionano i piumini d'oca e i doppi vetri).

La **pasta termica** (*TIM*, *Thermal Interface Material*) serve esclusivamente a **riempire quelle sacche d'aria**. Non è fatta per "condurre meglio del metallo" — anzi, la pasta conduce il calore **molto peggio** del rame. Serve per essere migliore **dell'aria che sostituisce**. Da questo discende immediatamente la regola d'oro dell'applicazione:

> **La pasta termica va applicata nella quantità minima sufficiente a riempire gli interstizi. Di più non è meglio: è peggio**, perché uno strato spesso di pasta è uno strato spesso di materiale mediocre che *allontana* i due metalli.

E, altrettanto importante:

> **La pasta termica va SEMPRE applicata tra la CPU e la cold plate. Sempre. Aria o liquido non fa differenza.** Questa è una confusione che sento costantemente: la gente crede che nei sistemi a liquido "ci pensi il liquido". No. Il liquido lavora *all'interno* del waterblock; l'interfaccia tra l'IHS della CPU e il fondo del waterblock è **identica** a quella di un dissipatore ad aria, e ha esattamente la stessa necessità di pasta.

**Quanta, e come.** Il metodo più affidabile e universale è il **chicco centrale**: una goccia al centro dell'IHS, del diametro di **un chicco di riso / un pisellino** (4-5 mm). La pressione di montaggio la sparge radialmente e uniformemente da sola, espellendo l'aria verso i bordi — che è esattamente quello che si vuole. Spalmare a mano con una spatolina sembra più "professionale" ma introduce facilmente bolle d'aria; la X diagonale e i cinque puntini sono variazioni accettabili, utili su IHS molto allungati (come quelli rettangolari di certe piattaforme HEDT o LGA1700, dove alcuni preferiscono una **sottile linea** lungo l'asse maggiore). Per i dettagli su conduttività, paste a metallo liquido, pump-out e longevità, si rimanda al capitolo sul raffreddamento; qui, il focus è sull'azione in sé.

Due avvertenze finali: **non spalmare col dito** (olio della pelle), e **non montare e smontare "per controllare"**. Se sollevi il dissipatore dopo averlo appoggiato, l'unico modo corretto di procedere è **pulire tutto con alcol isopropilico e riapplicare da capo**: il film si è rotto e ha inglobato aria.

Ultimo punto: **molti dissipatori arrivano con la pasta preapplicata** sulla cold plate (uno strato grigio, spesso con un pattern serigrafato). In quel caso, **non applicare altra pasta**. E, soprattutto, **controlla se c'è una pellicola di plastica sopra**: ci arriveremo, perché è l'errore più catastrofico di tutto il capitolo.

### 4.2 CASO A — Dissipatore ad Aria

**Fase 1 — Il backplate.** La maggior parte dei dissipatori ad aria di qualità non usa il sistema di ritenzione di serie della scheda madre, ma uno proprio: una placca metallica o plastica (il **backplate**) che va posizionata sul **retro** della scheda madre, dietro il socket, e serve a distribuire la forte pressione di montaggio su un'ampia area, impedendo alla PCB di flettersi. È esattamente per questo che il pre-assemblaggio su banchetto è comodo: la scheda è libera, la giri, metti il backplate, poi la rigiri.
Sulle piattaforme AMD AM4/AM5, il backplate **è spesso già presente** sulla scheda madre e va **riutilizzato** (a volte va rimosso solo il *retention module*, la cornice di plastica nera attorno al socket). Leggi il manuale del *cooler*, che ti indicherà quale strada seguire.

**Fase 2 — Gli standoff.** Sopra la scheda madre, in corrispondenza dei quattro fori attorno al socket, si avvitano quattro perni filettati che sporgono dal backplate. Vanno stretti **a mano, fino a battuta, senza forzare**. Determinano l'altezza — e quindi la pressione — del dissipatore.

**Fase 3 — La pasta.** Come sopra: una quantità a chicco di pisello al centro dell'IHS pulito. (Se il dissipatore ha già la pasta preapplicata, salta questo passaggio.)

**Fase 4 — Posizionare il dissipatore.** Abbassalo **dritto, senza strisciare**, allineando le staffe sopra gli standoff. Non "atterrarlo" ad angolo.

**Fase 5 — Stringere a croce, in modo incrementale.** Questo è il passaggio tecnico. Le viti (di solito quattro, a volte due lunghe barre) **non vanno strette una alla volta fino in fondo**. Stringi **in diagonale** — 1, 3, 2, 4 — e a **mezzi giri**: due giri sulla prima, due sulla terza (quella opposta), due sulla seconda, due sulla quarta, e ripeti. La ragione è che il dissipatore deve scendere **parallelo** all'IHS: se stringi la vite 1 fino in fondo prima di toccare la 3, inclini la piastra fredda, e la pasta viene schiacciata da un lato e non dall'altro, con conseguente scarso contatto (e, nei casi peggiori, flessione della PCB).
Stringi **finché le viti vanno a battuta e iniziano a girare a vuoto** o finché la molla è completamente compressa. La maggior parte dei kit moderni ha **viti prigioniere con molle precaricate**, progettate per rendere *impossibile* stringere troppo: quando la vite non avanza più, hai finito. **Non applicare ulteriore coppia "per sicurezza".**

**Fase 6 — Collegare la ventola a CPU_FAN.** Il connettore a 4 pin del dissipatore va nell'header etichettato **CPU_FAN** (non CPU_OPT, non SYS_FAN). Ragione: la scheda madre **monitora quell'header**, e molti BIOS **rifiutano di avviare il sistema** (o mostrano un "CPU Fan Error") se non rilevano un segnale di rotazione lì. Il connettore ha una linguetta di guida su un lato: entra solo in un modo, e se hai un dissipatore con due ventole, il secondo cavo va in **CPU_OPT**.

### 4.3 CASO B — AIO (raffreddamento a liquido)

Un **AIO** è un circuito chiuso pre-assemblato: un **waterblock** con **pompa** integrata che si monta sulla CPU, due **tubi**, un **radiatore** con le sue ventole. Non si riempie, non si spurgava, non si apre.

L'ordine dei passaggi qui è **diverso**, e sbagliarlo significa lavorare in condizioni impossibili:

**Fase 1 — Montare le staffe del socket sul waterblock**, *prima di tutto*, sul tavolo. Ogni AIO include kit di staffe diverse per AMD e Intel: scegli quelle giuste, e falle scorrere/avvitale sul blocco.

**Fase 2 — Backplate e standoff sulla scheda madre**, come per il raffreddamento ad aria.

**Fase 3 — LA PELLICOLA.** Stop. Guarda la **piastra fredda** dell'AIO, la piastra di rame o nichel sul fondo del waterblock. Nella stragrande maggioranza dei casi, il produttore ha applicato la pasta termica in fabbrica **e l'ha coperta con una pellicola di plastica trasparente o colorata**.

> **RIMUOVI LA PELLICOLA.** Questo non è un dettaglio: è l'errore più costoso e più comune di questo capitolo. Una piastra fredda montata con la pellicola significa **zero trasferimento termico**: la CPU raggiunge la sua temperatura di sicurezza (**T-junction max**, tipicamente 95-100 °C) in **pochi secondi** dopo l'accensione, entra in throttling estremo e poi si spegne. Il PC "si spegne da solo dopo dieci secondi" è quasi sempre questo. Non danneggia la CPU (le protezioni funzionano), ma ti costa un pomeriggio.

Se la piastra fredda è **nuda** (senza pasta pre-applicata), allora applica tu stesso la quantità a forma di pisello sull'IHS della CPU, come per il raffreddamento ad aria.

**Fase 4 — Fissa il waterblock.** Posizionalo dritto e stringi i dadi/viti **a croce e in modo incrementale**, esattamente come per il raffreddamento ad aria, fino a quando non è ben saldo.

**Fase 5 — Il radiatore: NON ORA.** Il radiatore rimane appeso ai suoi tubi, appoggiato sul tavolo. **Dovrebbe essere montato nel case dopo aver installato la scheda madre** (punto 8). Tentare di montare il radiatore e la scheda madre insieme, fuori dal case, significa dover calare un oggetto rigido e pesante vincolato da due tubi corti nel case: è il modo migliore per strappare qualcosa. Nel frattempo, **appoggia delicatamente il radiatore** e non piegare i tubi ad angoli acuti.

**Fase 6 — Connessioni elettriche.** Un AIO generalmente ha due (o tre) cavi:
- il cavo della **pompa**, che va in un header dedicato: **AIO_PUMP** se esiste, altrimenti **CPU_FAN** (l'header della pompa fornisce sempre il 100% della tensione, senza una curva di regolazione: è quello che vuoi, una pompa dovrebbe sempre funzionare al massimo o a una velocità fissa);
- il cavo (o i cavi) per le **ventole del radiatore**, che vanno in **CPU_FAN** e/o **CPU_OPT**, o — se il kit ha uno **splitter** o un hub — in un singolo header. Se colleghi la pompa a AIO_PUMP e le ventole a CPU_FAN, il BIOS è contento e puoi costruire una curva sensata.
- opzionalmente un cavo **ARGB/USB** per l'illuminazione e il monitoraggio (attenzione con ARGB: vedi punto 10).

---

## 5. Preparazione del case

Il blocco scheda madre + CPU + RAM + M.2 + dissipatore ad aria è pronto. Prima di calarlo, prepara il nido.

**Fase 1 — Apri tutto.** Rimuovi **entrambi** i pannelli laterali (quello in vetro/metallo e quello posteriore, dietro il *motherboard tray*: è qui che farai passare i cavi) e, se possibile, i pannelli anteriore e superiore, per accedere alle staffe delle ventole. Metti le viti dei pannelli in un contenitore separato.

**Fase 2 — Controlla i distanziali.** Questo passaggio è spesso trascurato ed è potenzialmente **distruttivo**. I **distanziali** del case sono piccoli pilastri esagonali filettati, avvitati nel *motherboard tray*, su cui poggerà la scheda madre. Hanno **due funzioni**: sollevare la scheda di pochi millimetri dal metallo, per evitare che i giunti di saldatura sul retro tocchino il telaio e **vadano in cortocircuito**; e definire i punti di ancoraggio.

I case vengono forniti con distanziali **pre-montati per il formato ATX**. Ma ci sono tre formati (e li elencheremo, perché la scelta è tua):

| Formato | Dimensioni tipiche | Fori di montaggio tipici | Slot PCIe | Slot RAM |
|---|---|---|---|---|
| **ATX** | 305 × 244 mm | 9 | fino a 7 | 4 |
| **Micro-ATX (mATX)** | 244 × 244 mm | 6-8 | fino a 4 | 2-4 |
| **Mini-ITX** | 170 × 170 mm | 4 | 1 | 2 |

> **Il controllo critico:** appoggiare (senza avvitare) la scheda madre nel case e controllare che **sotto ogni foro della scheda ci sia un distanziale, e — cosa più importante — che NON ci sia un distanziale dove la scheda NON ha un foro.** Un distanziale "orfano" che tocca il retro della scheda madre è un **cortocircuito**: il PC non si accenderà al primo avvio, e nel peggiore dei casi la scheda verrà danneggiata. È un errore classico di chi monta una scheda Micro-ATX in un case pensato per ATX. I distanziali in eccesso vanno svitati (a mano o con una chiave a bussola da 5 mm, spesso inclusa).

Molti case hanno un **distanziale centrale rialzato e non filettato**: serve da appoggio e per il centraggio, non per le viti. Va benissimo che ci sia.

**Passo 3 — Lo I/O shield.** Lo **I/O shield** è la placca metallica rettangolare che copre le porte posteriori della scheda madre. Nelle schede madri odierne di fascia media e alta è **integrato** (già montato sulla scheda, spesso con una copertura in plastica sopra i VRM): in quel caso non dovete fare nulla e potete saltare questo passo. Se la vostra scheda ha uno I/O shield **separato** — una placca metallica nella scatola — va installato **ora**, perché dopo non potrete più farlo.

Si installa **dall'interno del case verso l'esterno**: lo si appoggia nell'apertura rettangolare posteriore, si controlla l'**orientamento** (le etichette delle porte devono essere leggibili dall'esterno, e la forma deve corrispondere alle porte della vostra scheda: guardate la scheda madre mentre lo fate) e si preme sui **quattro angoli** finché non scatta in posizione con un rumore metallico. È necessaria una notevole **forza**, e la lamiera ha bordi taglienti: fate attenzione alle dita. Uno I/O shield installato al contrario è uno dei ricordi più amari del PC building, perché ci si accorge solo dopo che la scheda madre è già avvitata.
Una piccola avvertenza: alcuni I/O shield hanno delle **linguette metalliche elastiche** che sporgono verso l'interno. Devono premere **sopra** le porte (fanno da messa a terra), non **dentro** di esse: quando calate la scheda madre, controllate che nessuna linguetta sia entrata in una presa USB o Ethernet.

**Passo 4 — Pre-instradare i cavi frontali.** I cavi del pannello frontale (interruttore di accensione, USB, audio) e i cavi delle ventole del case preinstallate sono **già lì**, penzolanti. Prima di installare la scheda madre, instradateli attraverso i **gommini più vicini** alla loro destinazione, in modo che siano già posizionati correttamente. Farlo dopo significa dover infilare la mano tra la scheda madre e lo chassis.

---

## 6. Installazione dell'alimentatore (PSU)

L'**alimentatore** (PSU - Power Supply Unit) si trova quasi sempre nella **parte inferiore** nei case moderni, in un vano separato (shroud), e questa è una buona cosa: aspira aria fresca dal basso e la espelle direttamente all'esterno, senza riscaldare il resto del sistema.

### 6.1 Orientamento della ventola: la domanda che tutti sbagliano

La ventola dell'alimentatore può essere rivolta **verso il basso** o **verso l'alto (all'interno del case)**. La regola è semplice ma va compresa:

- Se il case ha **piedini alti e una griglia filtrata sotto l'alimentatore** (praticamente tutti i case moderni), la ventola dovrebbe essere rivolta **verso il basso**. L'alimentatore aspira **aria fresca dall'esterno**, la usa per raffreddarsi ed espelle l'aria calda dal retro. Questo è un circuito completamente indipendente dal flusso d'aria del case: eccellente.
- Se il case poggia direttamente sul pavimento senza flusso d'aria, o il fondo è **chiuso** (vecchi case, case desktop con l'alimentatore in alto), allora la ventola dovrebbe essere rivolta **verso l'interno**: aspirerà aria calda dal case. Questo è subottimale, ma è meglio che non ricevere aria affatto. Una ventola rivolta verso un fondo chiuso **soffocherà** l'alimentatore, e un alimentatore surriscaldato invecchierà rapidamente o attiverà la protezione termica.

Se lo posizioni su un tappeto, i piedini non sono sufficienti: metti una tavola sotto il case.

### 6.2 La Procedura

**Passo 1 — Collega i cavi modulari PRIMA di inserire l'alimentatore.** Se il tuo PSU è **modulare** (cavi staccabili) o **semi-modulare** (24-pin e EPS fissi, il resto staccabile), questo è il momento di attaccare i cavi al **lato dell'alimentatore**, mentre lo tieni in mano e le porte sono accessibili. Una volta avvitato al fondo del case, dietro la copertura, quelle porte diventano scomode.
Di quali cavi hai bisogno? Fai un inventario: **1× ATX 24-pin** (sempre), **1 o 2× EPS 8-pin** (per la CPU; controlla quanti ne richiede la tua scheda madre), cavi **PCIe** per la GPU (quanti 8-pin? o un **12V-2×6**?), **1× SATA power** se hai SSD o hard disk da 2.5", e qualsiasi cavo per ventole/hub.

> **AVVISO CRITICO, e questo non è un'esagerazione: i cavi modulari NON sono intercambiabili tra alimentatori diversi, anche dello stesso produttore.** Il connettore lato PSU può essere fisicamente identico ma avere una **piedinatura completamente diversa**. Usare un cavo di un PSU su un altro è uno dei pochissimi modi per **distruggere istantaneamente tutti i componenti collegati**, inviando 12V a una linea che si aspetta 3.3V o mettendo la massa dove dovrebbe esserci tensione. **Usa solo i cavi forniti nella scatola con quell'alimentatore.** Se li hai persi, possono essere acquistati dal produttore, per quello specifico modello.

**Passo 2 — Inserisci il PSU** nel suo alloggiamento (a volte dal retro, a volte dall'interno; alcuni case hanno una staffa rimovibile che si avvita prima al PSU e poi al case).

**Passo 3 — Avvita** le quattro viti sul retro del case. Queste sono viti a passo grosso (UNC 6-32): non forzarle.

**Passo 4 — Fai passare i cavi** dietro il vassoio della scheda madre, verso le loro destinazioni. Non collegarli ancora: la scheda madre va inserita per prima.

---

## 7. Installazione della Scheda Madre nel Case

**Passo 1 — Abbassa la scheda.** Tienila per i bordi (o per il dissipatore della CPU, se è un dissipatore ad aria, che ora è attaccato) e abbassala nel case con un movimento leggermente **obliquo**: prima le porte posteriori verso lo scudo I/O, poi la scheda piatta.

**Passo 2 — Allinea con lo scudo I/O.** La scheda dovrebbe essere **spinta verso il retro del case** finché le porte non sporgono attraverso i fori dello scudo I/O. Questo è il momento di controllare che nessuna linguetta metallica sia finita all'interno di una porta. Quando la scheda è in posizione, i **fori per i suoi punti di montaggio si allineano perfettamente con i distanziali**: se un foro è disallineato di due millimetri, non forzarlo — la scheda non è spinta abbastanza indietro, o un distanziale è nel posto sbagliato.

**Fase 3 — Avvitare.** Posiziona **la prima vite in un angolo, senza serrarla**, solo per tenere la posizione. Poi le altre. Poi torna indietro e stringile tutte, **lavorando a croce** (mai in cerchio) per evitare di mettere in tensione il PCB.

**Quanta forza?** Poca. La vite deve **appena fare contatto**: appena senti metallo toccare metallo, **fermati**. Stringere una vite della scheda madre "con forza" significa deformare il PCB attorno al foro e, a lungo andare, crepare le tracce di rame. Una scheda madre avvitata correttamente **non flette** se ci premi un dito sopra, ma le viti non sono strette come i dadi delle ruote di un'auto.

**Controllo:** conta le viti. Devono essere tante quanti sono i distanziali. Un distanziale senza vite è un punto di supporto non assicurato, e quando premerai la RAM o la GPU, la scheda fletterà in quel punto.

---

## 8. Installazione del radiatore AIO (se presente)

Ora che la scheda madre è dentro, il radiatore trova la sua casa.

### 8.1 Dove metterlo, e la regola dell'aria nella pompa

Le due posizioni sensate in un case ATX standard sono:

**Top exhaust (in alto, che espelle aria) — la scelta consigliata.** Il radiatore si trova sul tetto del case e spinge l'aria calda verso l'alto/fuori. Vantaggi: non riscalda il resto del sistema; la GPU riceve aria fresca dal davanti; l'aria calda va dove la fisica vuole già mandarla. Svantaggio: il liquido nel radiatore riceve aria già leggermente riscaldata dai componenti, quindi le temperature della CPU sono di qualche grado più alte rispetto alla configurazione front intake.

**Front intake (davanti, che immette aria) — massime prestazioni per la CPU.** Il radiatore si trova davanti e aspira aria fresca dall'esterno. La CPU ottiene le migliori temperature possibili, ma **tutta l'aria che entra nel case è stata preriscaldata dal radiatore**, e la GPU, i VRM (Voltage Regulator Module, gli stadi di alimentazione della CPU) e gli SSD ne pagano il prezzo.

C'è però un vincolo che **scavalca tutto**, ed è idraulico. La **pompa** dell'AIO deve essere **nel punto più basso del circuito**, o almeno **mai nel punto più alto**. Il motivo: in ogni AIO, col tempo, si accumula una piccola quantità d'aria (permeazione attraverso i tubi, aria residua di fabbrica). Quell'aria **galleggia** e va nel punto più alto del circuito. Se il punto più alto è il **radiatore**, l'aria si raccoglie lì e non fa danni. Se il punto più alto è la **pompa**, l'aria entra nella girante, la pompa gira "a secco" — e inizia a fare un rumore inconfondibile, un gorgoglio/ronzio ciclico — e **perde efficienza, fino a grippare**.

> **Regola pratica: i tubi del radiatore devono uscire dal LATO INFERIORE del radiatore** (o almeno il waterblock deve essere sotto il livello della parte superiore del radiatore). In una configurazione top, monta il radiatore con i tubi verso il retro/basso. In una configurazione frontale, monta il radiatore **in alto**, con i **tubi in basso**, mai con i tubi in alto sopra la pompa.

Una posizione da **evitare**: il radiatore montato **in alto con i tubi che puntano verso l'alto** e la pompa più in alto del radiatore stesso — una situazione tipica in alcuni case molto compatti. E il montaggio **posteriore** (120 mm sul retro) è adatto solo per AIO piccoli.

### 8.2 La procedura

**Fase 1 — Montare le ventole sul radiatore**, decidendo la direzione del flusso d'aria (le ventole hanno delle frecce stampate sull'alloggiamento: una indica la direzione dell'aria, l'altra la rotazione). In configurazione top exhaust, le ventole spingono l'aria **attraverso il radiatore verso l'esterno**; possono essere sotto (per spingere) o sopra (per tirare) — sotto è più comune per motivi di spazio.

**Fase 2 — Avvitare l'assemblaggio radiatore+ventole al case.** Le viti che attraversano il radiatore sono **lunghe e pericolose**: se sono troppo lunghe, la punta **perforerà le alette e il canale del liquido**, e l'AIO è morto. Usare **solo le viti fornite nel kit**, e distinguere tra quelle "corte" (radiatore avvitato direttamente al case) e quelle "lunghe" (radiatore + ventole insieme). In caso di dubbio, avvitare **a mano, contando i giri**, e fermarsi non appena incontrano resistenza.

**Fase 3 — Collegare le ventole** (a CPU_FAN/CPU_OPT o all'hub del kit) e assicurarsi che i tubi non premano contro la RAM o il dissipatore VRM.

---

## 9. Installazione della GPU

### 9.1 Quale slot, e perché non è banale

La scheda madre ha uno, due, a volte tre slot **PCIe x16** — fisicamente identici, lunghi. Sembrano intercambiabili. **Non lo sono.**

La GPU va nel **primo slot PCIe x16, quello più vicino alla CPU**. Ed ecco perché, che è la spiegazione che manca in nove guide su dieci.

Il bus **PCIe** opera su **lane**: ogni lane è una coppia differenziale punto-punto. Una CPU consumer moderna espone un numero **fisso e limitato** di lane PCIe — tipicamente **20-28** utilizzabili — di cui **16 sono riservate per la connessione grafica** e 4 (o 8) per l'SSD NVMe primario. Il resto della connettività (porte USB, SATA, rete, slot di espansione secondari) è gestito dal **chipset**, che si collega alla CPU tramite un unico link condiviso.

Questo significa che, su una scheda madre consumer:
- il **primo slot x16** è **elettricamente x16** ed è cablato **direttamente alla CPU**;
- gli slot x16 successivi sono **fisicamente x16** (la lunghezza è la stessa), ma elettricamente sono **x4** o **x1**, e spesso passano attraverso il **chipset**.

Mettere una GPU moderna in uno slot x4 dal chipset significa **strangolarla**: la banda passante è drasticamente ridotta e la latenza aumenta, con perdite di prestazioni che possono raggiungere percentuali a doppia cifra nei giochi (e molto di più nei carichi di calcolo che trasferiscono continuamente dati tra RAM e VRAM). Questo non è un dettaglio per puristi: è il tipo di errore che ti fa comprare una GPU da 800 euro e ottenere le prestazioni di una da 500 euro.

Il manuale della scheda madre lo dichiara sempre, con annotazioni come **"PCIEX16_1: x16 mode (CPU)"** e **"PCIEX16_2: x4 mode (Chipset)"**. Leggetelo.

**Eccezioni**, che esistono e sono legittime:
- **Spazio per radiatore AIO o dissipatore.** Su alcuni case compatti, il primo slot è troppo vicino a qualcosa. In tal caso, uno slot x8 dalla CPU (se disponibile, su schede che supportano lo splitting x8/x8) è meglio di una GPU che non entra.
- Schede **Workstation/HEDT** (*High-End Desktop*, es. Threadripper): qui le lane della CPU sono 48, 64 o più, e tutti gli slot sono x16 elettrici. La regola non si applica.
- **Flusso d'aria.** Alcuni preferiscono il secondo slot per dare a una GPU spessa un po' di respiro. È un compromesso: qualche grado in meno, banda passante dimezzata. Quasi mai ne vale la pena.

### 9.2 La procedura

**Fase 1 — Rimuovere le staffe posteriori.** Contate quanti **slot di espansione** occupa la vostra GPU (le GPU moderne sono spesse 2, 2.5 o 3 slot) e rimuovete le corrispondenti **staffe metalliche** dal retro del case, svitandole (**non piegatele fino a romperle**: nei case economici sono a strappo, e una volta rotte non tornano più). Rimuovete quelle **corrette**: se la GPU va nel primo slot x16 e occupa 3 slot, dovrete rimuovere la staffa corrispondente e le due sottostanti.

**Fase 2 — Aprire la clip dello slot.** Alla fine dello slot PCIe, verso l'interno della scheda madre, c'è una **clip di ritenzione** in plastica. Deve essere abbassata/spostata di lato **prima** di inserire la GPU. Questo è un dettaglio che spesso viene trascurato, e forzare la GPU con la clip chiusa può rompere lo slot.

**Fase 3 — Inserire.** Allineate il **connettore dorato** della GPU con lo slot (la tacca sul connettore, come sempre, assicura l'orientamento corretto) e la **staffa** con i fori sul retro del case. Premete **dritti verso il basso, con forza uniforme su entrambe le estremità** della scheda (una sopra il connettore, una sull'estremità libera). La GPU scivola dentro e la clip **scatta in posizione con un CLICK**.
Verifica: guardate il connettore di lato — i contatti dorati devono essere **completamente nascosti** nello slot, e la scheda deve essere **parallela** alla scheda madre.

**Fase 4 — Avvitare la staffa** al case (una o due viti). Non stringere eccessivamente.

### 9.3 Alimentazione GPU: il paragrafo che può salvare la vostra casa

Le GPU consumano più di quanto lo slot PCIe possa fornire (lo slot fornisce un massimo di 75 W), quindi prelevano energia direttamente dall'alimentatore. Esistono due famiglie di connettori.

**Famiglia classica: PCIe 6+2 pin (comunemente "8-pin").** Ogni connettore a 8 pin è certificato per **150 W**; quello a 6 pin per 75 W. Una GPU può averne uno, due o tre.

> **La regola del daisy-chain.** Molti alimentatori forniscono cavi PCIe con **due connettori sullo stesso cavo** (uno all'estremità e uno "a metà cavo", chiamato *piggyback* o *daisy-chain*). Questi sono comodi ma **condividono gli stessi conduttori**. Su una GPU di fascia bassa (un solo ingresso di alimentazione, o due ingressi ma consumo energetico modesto), questo va perfettamente bene. Su una GPU potente — tipicamente **250 W e oltre**, o ogni volta che la GPU richiede **due o più connettori** — **utilizzate cavi separati, uno per ogni ingresso della GPU, partendo da diverse uscite dell'alimentatore.** Un singolo cavo che trasporta 300 W su conduttori progettati per 150 W si surriscalderà, si degraderà e, in casi documentati, **si scioglierà**.

**Nuova famiglia: 12V-2×6 (evoluzione del 12VHPWR).** Questo è il connettore ad alta densità introdotto con lo standard **PCIe 5.0 / ATX 3.0**: dodici pin di alimentazione (sei per 12V, sei per la massa) più quattro pin di segnale (sideband), in grado di trasportare fino a **600 W** da un singolo cavo. Il "2×6" è la revisione che ha accorciato i pin di segnale rispetto all'originale 12VHPWR: se il connettore non è completamente inserito, i pin più corti perdono prima il contatto di rilevamento, quindi l'alimentatore interrompe l'erogazione a 0 W invece di consentire comunque fino a 150 W attraverso una connessione allentata — una correzione di sicurezza/rilevamento, non una barriera meccanica all'inserimento parziale.

> **AVVISO DI SICUREZZA, il più grave di questo libro: il connettore 12V-2×6 va inserito FINO IN FONDO, finché non scatta la clip, e va verificato visivamente.**
>
> La ragione è fisica e inesorabile. Un connettore inserito **parzialmente** (anche di un solo millimetro) o **piegato con un angolo troppo acuto** subito dopo l'uscita ha una **resistenza di contatto elevata** su alcuni pin. La potenza dissipata in un contatto è **P = R × I²**: con correnti nell'ordine di 40-50 ampere, anche pochi milliohm di resistenza in eccesso producono decine di watt concentrati su un piccolo pezzo di plastica e ottone. Il risultato documentato, con abbondanti foto online, è la **fusione del connettore** — lato GPU, lato PSU, o entrambi — danneggiando la scheda video.
>
> **Le regole:** (1) inserire **fino allo scatto**, verificando che l'**area dorata** dei terminali **non sia più visibile**; (2) **non piegare il cavo** entro **almeno 35 mm** dal connettore (è la distanza minima specificata dallo standard) — se il pannello laterale del case preme sul cavo forzando una piega acuta immediata, avete un problema di design e serve un cavo angolato o un adattatore a 90° certificato; (3) usare il **cavo nativo dell'alimentatore** se è ATX 3.x, o l'adattatore fornito con la GPU, mai adattatori generici di dubbia provenienza; (4) **ricontrollare** il connettore dopo aver chiuso il case e dopo ogni spostamento del PC.

**Ultimo passo — Il supporto anti-sag.** Le GPU moderne possono pesare **fino a 2 chilogrammi** e sono sostenute da due punti: lo slot PCIe e la staffa. La conseguenza è il **sag**: la scheda si inclina verso il basso, flettendo permanentemente lo slot e il PCB della GPU. Se guardate la GPU montata **di lato** e vedete che l'estremità libera scende visibilmente rispetto allo slot, aggiungete un **supporto** (un piedistallo regolabile che si appoggia sul fondo del case, o una staffa retrattile: molte GPU e case ne includono uno). Non è solo estetica: una GPU pesante lasciata in flessione per due anni può sviluppare micro-fratture nelle saldature.

---

## 10. Cablaggio completo, uno per uno

Siamo alla parte che spaventa la gente perché "ci sono cinquanta cavi". In realtà, i tipi sono nove, e ognuno ha un solo posto dove andare. Procediamo in ordine, dall'alto verso il basso.

### 10.1 ATX 24-pin (alimentazione scheda madre)

Il connettore più grande, da **24 pin** (spesso in due pezzi separabili, 20+4). Va nell'unico posto dove può andare: il connettore lungo sul **bordo destro** della scheda madre. È polarizzato (i pin hanno forme diverse) e ha una **clip laterale** che deve **scattare in posizione**.
**Forza:** considerevole. Il 24-pin richiede una spinta decisa, e i principianti spesso lo lasciano a metà. Spingete finché la clip non scatta in posizione. Verifica: provate a tirarlo; non deve uscire.

### 10.2 EPS 8-pin (alimentazione CPU) — il cavo più confuso di tutti

L'**EPS** è un connettore da **8 pin** che alimenta i VRM della CPU, e si trova in **alto a sinistra sulla scheda madre**, vicino al socket, spesso etichettato **CPU_PWR1**, **ATX_12V**, o **EPS12V**.

> **Il rischio: EPS 8-pin e PCIe 8-pin sembrano identici.** Hanno lo stesso numero di pin, la stessa forma generale, lo stesso colore (nero). Ma la **piedinatura è diversa** (le posizioni 12 V e terra non corrispondono) e **la forma dei singoli pin è diversa**: alcuni sono quadrati, altri smussati, proprio per prevenire errori. **Non forzare mai un connettore a 8 pin.** Se non scorre agevolmente, hai il cavo sbagliato. Un PCIe forzato in un socket EPS può **distruggere la scheda madre**.
>
> **Come distinguerli sempre:** I cavi dell'alimentatore sono **etichettati** — cerca **"CPU"** o **"EPS"** sul connettore stesso, rispetto a **"PCIe"** o **"VGA"**. Se le etichette mancano, guarda la separazione: PCIe è **6+2** (si divide in un blocco da 6 e uno da 2); EPS è **4+4** (si divide a metà). Questo è il metodo infallibile.

Le schede madri di fascia alta hanno **due** connettori EPS (8+8, o 8+4). Con le CPU consumer, **solo uno è quasi sempre sufficiente** per il normale funzionamento; il secondo è per overclock estremo e correnti molto elevate. Se ne hai due e l'alimentatore ha i cavi, collegali entrambi: non fa male.

**Avvertenza sull'accessibilità:** L'EPS si trova nell'angolo più scomodo del case, spesso già coperto dal dissipatore. **Collegalo prima** di montare il radiatore superiore, o anche prima di installare la scheda madre se il case è angusto.

### 10.3 PCIe / 12V-2×6 (alimentazione GPU)

Già trattato nella sezione 9.3. Riassumo la regola: **niente daisy-chain su GPU potenti; 12V-2×6 fino in fondo, senza curve strette entro 35 mm.**

### 10.4 Alimentazione SATA e dati SATA

Se hai un SSD da 2.5" o un disco rigido meccanico, hai bisogno di **due** cavi: uno **SATA power** (connettore piatto a L, largo, proveniente dall'alimentatore, con più spine sullo stesso cavo — qui il daisy-chaining va benissimo, il consumo energetico è minimo) e uno **SATA data** (piccolo, a forma di L, che va dall'unità a una porta **SATA** sulla scheda madre). Entrambi hanno una clip. Il cavo dati SATA è spesso incluso con la scheda madre.
Se hai solo unità M.2, **non hai bisogno di nulla di tutto questo**.

### 10.5 Pannello frontale (F_PANEL) — il puzzle dei pin

Questo è il set più odiato: **quattro o cinque cavi sottili, con connettori a due pin**, provenienti dalla parte anteriore del case, che vanno in un blocco di **9-10 pin** sul bordo **in basso a destra** della scheda madre, etichettato **F_PANEL**, **PANEL1**, **JFP1**, o simili.

I cavi sono:

| Cavo | Funzione | Polarità |
|---|---|---|
| **POWER SW** (o PWR_SW) | Il pulsante di accensione: chiude un contatto momentaneo | **Indifferente** (è un interruttore) |
| **RESET SW** | Il pulsante di reset | **Indifferente** |
| **POWER LED** (a volte in due pezzi, + e −) | Il LED di accensione | **Importa**: il polo positivo (filo colorato) va sul pin + |
| **HDD LED** (o H.D.D. LED) | Il LED di attività del disco | **Importa** |
| **SPEAKER** (se presente) | Il buzzer che emette i bip diagnostici | Indifferente in pratica |

**Il punto chiave:** **non esiste uno standard universale per l'ordine dei pin.** Ogni produttore di schede madri utilizza un layout leggermente diverso. **L'unica fonte di verità è il diagramma nel manuale della scheda madre**, che mostra il blocco pin per pin. Aprilo, tienilo aperto e collegali uno per uno con l'aiuto di una torcia. Questa è l'unica parte dell'assemblaggio in cui non puoi affidarti all'intuizione.

Buone notizie: **fare un errore qui non romperà nulla**. Se inverti un LED, quel LED non si accenderà (e devi solo capovolgere il connettore). Se scambi alimentazione e reset, il pulsante di accensione eseguirà un reset. Nessun danno permanente. Alcune schede madri (specialmente ASUS) includono un adattatore **Q-Connector**: un blocco dove inserire comodamente i cavi **all'esterno** del case, e poi il tutto si collega alla scheda. Se c'è, usalo.

**Se il tuo case ha un pulsante di accensione ma non riesci a capire quale sia POWER SW:** ricorda che, per accendere un PC, basta **cortocircuitare i due pin POWER SW per un istante** con la punta di un cacciavite. Questo è esattamente ciò che fa il pulsante. Utile per test POST al banco.

### 10.6 Connettori dati del pannello frontale

**Connettore USB 3.0 / USB 3.2 Gen 1:** un connettore **blu, largo, a 19/20 pin**, con una tacca di guida. Si inserisce nell'apposito connettore sulla scheda madre. **Attenzione:** è rigido e i pin sono sottili; si inserisce solo in un modo ma deve essere inserito dritto. Questo è il connettore che più spesso viene **danneggiato piegando i pin**, perché si trova sul bordo della scheda e in una posizione scomoda. Prenditi il tuo tempo.

**Connettore USB-C (USB 3.2 Gen 2 / Gen 2×2):** un piccolo connettore rettangolare, tipo "E-key", spesso etichettato **USB 20G** o **USB-C**. Si inserisce facilmente in un solo modo. Se la tua scheda madre **non ha** questo connettore, la porta USB-C frontale del case **non funzionerà**: questa è una verifica di compatibilità da effettuare in fase di acquisto.

**Connettore USB 2.0:** 9 pin (uno mancante funge da chiave). Utilizzato per il cavo di controllo di molti AIO, per gli hub RGB, per alcuni lettori di schede.

**HD Audio (o AAFP / F_AUDIO):** un connettore a 9 pin, molto simile a USB 2.0 (**ma NON la stessa cosa**: guarda l'etichetta, "HD AUDIO"). Si inserisce nel connettore audio, che si trova quasi sempre nell'**angolo in basso a sinistra** della scheda madre, il più lontano possibile dai circuiti digitali (per ridurre le interferenze). Questo è il cavo per i jack frontali di cuffie/microfono.

### 10.7 Ventole: PWM vs DC

Ogni ventola ha un connettore a **3 o 4 pin**, con una linguetta di guida che ne assicura l'orientamento, e si collega a uno dei connettori **SYS_FAN** / **CHA_FAN** sulla scheda madre.

La differenza tra 3 e 4 pin è concettualmente importante:

| | **3 pin (DC)** | **4 pin (PWM)** |
|---|---|---|
| Pin | GND, 12V, Sense (tachimetro) | GND, 12V, Sense, **PWM** |
| Come viene regolata la velocità | Variando la **tensione** (da 12V in giù) | Mantenendo 12V fisso e inviando un **segnale di controllo** a duty cycle variabile |
| Regolazione a bassa velocità | Imprecisa; al di sotto di una certa tensione, la ventola **si ferma e non riparte** | Precisa e stabile anche a bassi RPM |
| Compatibilità | Una ventola a 3 pin **funziona** su un connettore a 4 pin, ma solo se il connettore è impostato in **modalità DC** nel BIOS | Una ventola a 4 pin su un connettore a 3 pin **girerà sempre al 100%** |

**Conseguenza pratica:** se hai ventole a 3 pin che girano sempre a piena velocità e fanno rumore, vai nel BIOS, trova la sezione delle ventole (Q-Fan, Smart Fan, Fan Control) e imposta quel connettore su **DC** invece di **PWM/Auto**. Questa è la soluzione a un problema che frustra molte persone.

Se hai più ventole rispetto alle intestazioni disponibili, usa uno **splitter** (un cavo che si sdoppia; il segnale tachimetrico proviene da una sola ventola) o un **hub PWM** alimentato direttamente dall'alimentatore con un connettore di alimentazione SATA (obbligatorio se colleghi più di 3-4 ventole: un'intestazione della scheda madre tipicamente fornisce **1 A / 12 W**, e superare questo limite può bruciare il circuito di controllo).

### 10.8 ARGB e RGB: il paragrafo salva-hardware

È qui che le cose si distruggono davvero, e la causa è che i due connettori sembrano simili.

| | **RGB 12 V (4 pin)** | **ARGB 5 V (3 pin)** |
|---|---|---|
| Voltaggio | **12 volt** | **5 volt** |
| Pin | 4 (12V, G, R, B) | 3 (5V, Data, **vuoto**, GND) — ha uno spazio vuoto! |
| Cosa può fare | L'intera striscia cambia colore **insieme** | Ogni LED è **indirizzabile individualmente** (effetti "arcobaleno") |
| Etichetta sulla mobo | RGB_HEADER, JRGB, 12V G R B | ARGB_HEADER, JRAINBOW, ADD_HEADER, **5V D G** |

> **NON COLLEGARE MAI un dispositivo ARGB da 5V a un'intestazione RGB da 12V.** I connettori sono **fisicamente simili**, e su alcune intestazioni (che hanno 4 pin), è **possibile inserire accidentalmente** un connettore a 3 pin. Il risultato è che il dispositivo riceve **più del doppio della tensione per cui è progettato**: la striscia LED o la ventola **si brucia istantaneamente**, e con una certa probabilità, si porta dietro il **controller LED della scheda madre**, che non è riparabile.
>
> **Come proteggersi:** l'intestazione ARGB a 3 pin ha una caratteristica inconfondibile: ci sono **4 posizioni ma un pin è MANCANTE** (c'è un buco). Il connettore ARGB ha un corrispondente foro cieco. Conta i pin, **leggi la serigrafia sulla scheda** (dice sempre "12V" o "5V" accanto all'intestazione), e in caso di dubbio, **non collegare nulla e consulta la pagina del manuale**. Questo è un caso in cui trenta secondi di lettura valgono cento euro di componenti.

### 10.9 Gestione dei cavi

Non è (solo) estetica. Un fascio di cavi che attraversa il case davanti al dissipatore o alla GPU **ostruisce il flusso d'aria**, aumenta le temperature e rende impossibile qualsiasi futura manutenzione.

Ci sono tre principi:
1. **Tutto ciò che può andare dietro il vassoio della scheda madre, va dietro.** I case moderni hanno un vano posteriore dedicato e passacavi in gomma in punti strategici. Il cavo esce dal passacavo più vicino alla sua destinazione, percorre pochi centimetri a vista e si collega.
2. **Raggruppa e fissa con fascette**, senza stringerle così tanto da schiacciare i cavi. Fissali ai punti di ancoraggio del case (occhielli, fessure).
3. **Non ostruire l'aspirazione delle ventole o lo scarico del dissipatore.** Nessun cavo dovrebbe passare *davanti* alla ventola anteriore o sopra il dissipatore della CPU.

Se il pannello posteriore non si chiude, hai troppa roba dietro: ridistribuisci, non forzare.

---

## 11. Primo Avvio

### 11.1 Lista di controllo pre-accensione

Prima di premere il pulsante, fermati. Cinque minuti qui valgono un'ora dopo. Rivedi ogni punto:

1. **La leva del socket della CPU è chiusa e bloccata?**
2. **Il dissipatore è fissato saldamente?** Prova a muoverlo: non dovrebbe ruotare o oscillare. (E: **hai rimosso la pellicola dalla piastra fredda?**)
3. **La ventola/pompa del dissipatore è collegata a CPU_FAN / AIO_PUMP?**
4. **La RAM è negli slot corretti (A2/B2) e TUTTI i fermi sono chiusi?**
5. **La GPU è nel primo slot x16, il fermo è scattato, la staffa è avvitata?**
6. **Tutti i cavi di alimentazione sono collegati?** 24-pin, EPS 8-pin (**non dimenticarlo: è di gran lunga l'errore più comune**), PCIe/12V-2×6 per la GPU, alimentazione SATA.
7. **Il 12V-2×6 è inserito FINO IN FONDO?** Guardalo.
8. **Non ci sono viti, fascette o attrezzi all'interno del case?** Capovolgi il case e scuotilo delicatamente: se senti rumori, cercali.
9. **Nessun distanziatore orfano sotto la scheda madre?**
10. **L'interruttore sul retro dell'alimentatore è impostato su "O"?** Tienilo spento finché non hai collegato il cavo di alimentazione.

### 11.2 Collegamento del Monitor: Alla GPU, Non alla Scheda Madre

Sembra ovvio e **una persona su tre** commette questo errore.

Se hai una **GPU dedicata**, il cavo del monitor (DisplayPort o HDMI) va nelle **porte della GPU**, che sono quelle **orizzontali, in basso, sulla staffa della scheda video**. Le porte video **sulla scheda madre** (verticali, in alto, nel cluster I/O posteriore) appartengono alla **iGPU** della CPU e, per impostazione predefinita, **sono disabilitate quando è presente una GPU dedicata**.

Sintomo dell'errore: il PC si accende, le ventole girano, i LED si illuminano, ma **il monitor dice "Nessun Segnale"**. Panico ingiustificato. Sposta il cavo.

(Corollario: se la tua CPU **non ha** grafica integrata — molti modelli AMD serie X e alcuni modelli Intel "F" — le porte video della scheda madre non funzionano mai, e una GPU dedicata è **assolutamente necessaria**.)

### 11.3 Il POST e Come Leggere le Diagnostiche

Accendilo: interruttore dell'alimentatore su "I", poi il pulsante di accensione del case.

Il **POST** (*Power-On Self-Test*) è la sequenza di autodiagnostica che il firmware esegue prima di cedere il controllo al sistema operativo. Inizializza, in ordine: **CPU → memoria → dispositivi PCIe/grafici → dispositivi di avvio**. Se qualcosa fallisce, il POST **si ferma lì**, e ti dice dove.

Come te lo dice dipende dalla scheda madre:

**LED di diagnostica (EZ Debug LED / Q-LED).** Quattro LED sul bordo della scheda madre, etichettati **CPU**, **DRAM**, **VGA**, **BOOT**. Durante il POST, si illuminano in sequenza e si spengono man mano che ogni fase viene superata. **Il LED che rimane acceso indica la fase in cui il sistema si è bloccato**:

| LED acceso fisso | Significato | Cosa controllare, in ordine |
|---|---|---|
| **CPU** | CPU non è stata inizializzata | **EPS 8-pin collegato?** Poi: CPU correttamente inserita? BIOS aggiornato (CPU troppo nuova)? Pin del socket intatti? |
| **DRAM** | La memoria non risponde | RAM inserita fino al doppio click? Slot corretti (A2/B2)? Prova **un solo modulo**, in A2. Cancella CMOS. |
| **VGA** | Grafica non rilevata | GPU inserita fino al click? **Cavi PCIe collegati alla GPU?** Prova l'altro slot. |
| **BOOT** | Tutto OK, ma nessun disco avviabile trovato | **Normale** se non hai ancora installato il sistema operativo! Altrimenti: SSD rilevato nel BIOS? Chiavetta USB di installazione collegata? |

Una nota di realismo che evita molti attacchi di panico: alla **prima accensione** con RAM DDR5, il sistema può impiegare **da 30 secondi a diversi minuti** per fare il POST, con il LED DRAM acceso e schermo nero, e magari uno o due riavvii automatici. Questo è il **memory training**: il controller sta calibrando decine di parametri elettrici per ogni modulo, e ne memorizza il risultato. **Questo è normale, e succede solo la prima volta (e dopo ogni cambio RAM o clear CMOS).** Non spegnete tutto dopo venti secondi: **aspettate almeno cinque minuti** prima di dichiarare il fallimento.

**Display Q-Code / POST code.** Le schede madri di fascia alta hanno un piccolo display **esadecimale a due cifre** che mostra il codice della fase corrente. Il codice su cui **si blocca** identifica il problema con precisione chirurgica; la tabella completa è **nell'appendice del manuale**. Alcuni codici ricorrenti (variano tra produttori — consultare sempre il proprio manuale): i codici della serie **0d/19/53/55** indicano tipicamente problemi di memoria; **d6/d7** indicano assenza di dispositivo grafico; **A0/A2/AA** indicano che il POST è riuscito e il sistema sta procedendo al boot. Se il display mostra un **00** o **FF** fisso, in genere la CPU non sta eseguendo nulla: sospettare alimentazione CPU o CPU non riconosciuta.

**Beep code (buzzer).** Se avete collegato l'**altoparlantino**, un **singolo beep breve** significa "POST superato, tutto bene". Sequenze lunghe/ripetute indicano errori (tipicamente: beep ripetuti = RAM; un lungo e due brevi = grafica). Vale la pena collegarlo: non costa nulla e dà informazioni.

### 11.4 Se non si avvia: la procedura sistematica

La regola d'oro della diagnostica: **cambiare una variabile alla volta, e iniziare dalla più semplice.** La tentazione di smontare tutto e rimontare a caso è forte e porta solo a confusione.

**Livello 0 — Le assurdità (davvero, controllatele).** L'interruttore dell'alimentatore è su ON? Il cavo è attaccato alla presa? La presa funziona? Il connettore del pulsante di accensione (POWER SW) è sui pin corretti? (Se il PC non dà **nessun** segno di vita — nemmeno un LED, nemmeno un accenno di ventola — il colpevole è quasi sempre qui, o è l'alimentatore.)

**Livello 1 — Reseating.** Spegnete, scollegate, **tenete premuto il pulsante di accensione per 10 secondi** (scarica i condensatori). Poi:
- **RAM:** Togliete tutti i moduli e reinseritene **solo uno**, nello slot **A2** (il secondo dalla CPU). Provate. Se non parte, provate lo stesso modulo in **B2**. Se non parte, provate **l'altro modulo** in A2. Questa matrice di quattro tentativi distingue un modulo difettoso da uno slot difettoso da un errore di inserimento, e risolve la stragrande maggioranza dei casi.
- **GPU:** Reinserite, e ricontrollate i cavi di alimentazione.
- **EPS 8-pin:** Ricontrollatelo. Seriamente. È l'errore più comune.

**Livello 2 — Cancella CMOS.** Il **CMOS** è la memoria (alimentata dalla batteria a bottone CR2033) dove il BIOS memorizza le impostazioni. Un'impostazione impossibile (tipicamente: un profilo XMP/EXPO troppo aggressivo che la tua CPU non può gestire) può impedire il POST. *Cancellare il CMOS* ripristina tutto alle impostazioni di fabbrica. Questo può essere fatto in tre modi, in ordine di comodità:
1. Premere il **pulsante "Clear CMOS"** o **"BIOS Reset"** sul pannello posteriore (le schede madri di fascia media/alta ce l'hanno);
2. Cortocircuitare i due **pin "CLR_CMOS" / "JBAT1"** sulla scheda madre per 5-10 secondi (con un cacciavite o il ponticello incluso), **con il PC spento e scollegato dalla corrente**;
3. **Rimuovere la batteria CR2032** per un paio di minuti, sempre con il PC spento e scollegato.
Dopo un clear CMOS, tutte le impostazioni (XMP, ordine di avvio, curve delle ventole) tornano ai valori predefiniti e devono essere riconfigurate. E l'addestramento della memoria riparte da zero: **sii paziente al successivo primo avvio.**

**Livello 3 — Breadboarding.** Se nulla ha funzionato, si torna al banco. **Rimuovi la scheda madre dal case** (sì, tutto) e riassemblala nuda sulla sua scatola di cartone, con **il minimo indispensabile**: CPU, dissipatore, **un** modulo RAM, GPU, alimentatore, monitor. Niente hard disk, niente ventole del case, niente USB frontali, niente strisce LED. Accendi cortocircuitando i pin POWER SW.
Se **si avvia così**, il colpevole era nel case: quasi sempre un **distanziatore orfano** che causa un corto, o un cavo del pannello frontale mal collegato, o una periferica difettosa. Riassembla **un pezzo alla volta**, testando ogni volta.
Se **non si avvia così**, il colpevole è tra i cinque componenti rimanenti, e a quel punto sono necessari componenti di ricambio per le sostituzioni (la parte in cui un amico con un PC diventa prezioso).

---

## 12. Configurazione Iniziale

Il PC fa il POST e visualizza qualcosa. Non hai finito: hai terminato la **parte meccanica**. Ora arriva la parte in cui il computer diventa *tuo*, e in cui recuperi prestazioni che hai già pagato ma che, con le impostazioni di fabbrica, **non stai usando**.

### 12.1 Entrare nel BIOS/UEFI

Alla prima accensione, premi ripetutamente **CANC** — o **F2** su alcune schede madri — non appena il PC si avvia. Entrerai nell'**UEFI** (*Unified Extensible Firmware Interface*, il moderno successore del BIOS; i due termini sono ormai usati come sinonimi), l'interfaccia di configurazione del firmware.

**Primo controllo: c'è tutto?** La schermata iniziale (*EZ Mode*) ti mostra un riepilogo. Verifica:
- La **CPU** è riconosciuta con il nome corretto;
- La **quantità totale di RAM** corrisponde a quella che hai installato (se hai 32 GB e ne vede solo 16, un modulo non fa contatto: spegni e reinserisci);
- Tutti gli **SSD/hard disk** sono elencati;
- La **velocità delle ventole** è diversa da zero per la CPU;
- La **temperatura della CPU** è ragionevole (a riposo, sotto i 50-60 °C in un sistema ben assemblato; se vedi 80-90 °C a riposo, **spegni immediatamente**: il dissipatore non fa contatto, e la prima cosa da controllare è **la pellicola**).

### 12.2 Attivare XMP / EXPO: cinque secondi, dieci percento di prestazioni

Questa è l'unica azione con il miglior rapporto sforzo-beneficio nell'intero processo.

I moduli RAM, secondo le specifiche **JEDEC** (l'ente che standardizza i semiconduttori), partono sempre a una **frequenza base conservativa** — molto più bassa di quella per cui hai pagato. Il kit da 6000 MT/s che hai acquistato, una volta installato, funziona a 4800 MT/s con timing rilassati. **Le prestazioni per cui hai pagato ci sono, ma sono disattivate.**

Per attivarlo, c'è un profilo memorizzato nel chip **SPD** del modulo, che il BIOS può caricare con un click:
- **XMP** (*Extreme Memory Profile*) è il nome del profilo su piattaforme **Intel**;
- **EXPO** (*EXtended Profiles for Overclocking*) è l'equivalente **AMD**;
- alcune schede lo chiamano genericamente **D.O.C.P.** o **A-XMP**.

Sono concettualmente la stessa cosa: un set predefinito di frequenza, timings e voltaggi, testato dal produttore delle memorie. **Trovate l'opzione (è in bella vista in EZ Mode) e attivatela.** Il PC si riavvierà ed effettuerà nuovamente il memory training (di nuovo: **pazienza, può richiedere minuti**).

**Tecnicamente è un overclock**, quindi: se dopo l'attivazione il sistema è instabile (crash, schermate blu, no POST), non è un difetto — è il vostro specifico IMC che non regge quel profilo. Soluzioni: provate il profilo XMP/EXPO **secondario** (spesso più conservativo), oppure riducete manualmente la frequenza di uno step. E se non fa più POST: **clear CMOS**, e si ricomincia.

### 12.3 Aggiornamento del BIOS

Se il PC funziona, è **consigliabile** aggiornare il BIOS all'ultima versione stabile: le revisioni successive al lancio di una piattaforma migliorano sostanzialmente la compatibilità e la stabilità delle memorie, correggono bug e talvolta aumentano le prestazioni.

**La procedura corretta** (i nomi commerciali variano: *EZ Flash*, *M-Flash*, *Q-Flash*):
1. Sul **sito del produttore della vostra scheda madre**, nella pagina **del vostro esatto modello** (attenzione alle varianti: una "B650 Gaming X" non è una "B650 Gaming X AX"), scaricate l'ultimo file BIOS stabile;
2. Copiatelo su una **chiavetta USB formattata in FAT32**, nella directory radice;
3. Entrate nel BIOS, aprite l'utility di aggiornamento **integrata nel BIOS stesso** (mai, mai usare utility di aggiornamento da Windows: sono la causa storica di BIOS "brickati"), selezionate il file;
4. **NON SPEGNETE, NON TOCCATE NULLA** per tutta la durata. Un'interruzione di corrente durante il flash può rendere la scheda madre inutilizzabile (a meno che non abbia un dual BIOS o Flashback).

Dopo l'aggiornamento, le impostazioni verranno resettate: **rifate XMP/EXPO e curve delle ventole.**

### 12.4 Curve delle ventole e Resizable BAR

**Curve delle ventole.** Nella sezione dedicata del BIOS (Q-Fan Control, Smart Fan, Fan Xpert), potete definire una **curva** per ogni header che lega la velocità (in % o RPM) a una temperatura di riferimento. Il default è quasi sempre troppo aggressivo (rumoroso) o troppo pigro. Una curva sensata per la CPU: silenziosa fino a ~50 °C, aumento graduale tra 50 e 75 °C, massimo oltre 80 °C. Importante: impostate un **tempo di risposta** (*step up/down time*, *hysteresis*) di qualche secondo, per evitare che la ventola "pompi" su e giù ad ogni picco di temperatura momentaneo — il fenomeno che rende un PC fastidioso anche quando è silenzioso in media. E ricordate: header su **DC** per ventole a 3 pin, **PWM** per ventole a 4 pin.

**Resizable BAR** (Re-Size BAR / Smart Access Memory su AMD). È una funzionalità PCIe che permette alla CPU di accedere all'**intera VRAM** della GPU in una volta sola, invece che a finestre da 256 MB alla volta. Il beneficio nei giochi va da nullo a **oltre il 10%** a seconda del titolo. Va abilitato **nel BIOS** (spesso servono due impostazioni: **Above 4G Decoding** su *Enabled* e **Re-Size BAR Support** su *Auto/Enabled*). È gratis: abilitatelo.

### 12.5 Sistema operativo e installazione dei driver

**Preparazione dei media:** Su un altro PC, scarica lo strumento ufficiale per la creazione dei media (per Windows, il *Media Creation Tool* dal sito Microsoft; per Linux, la ISO della distribuzione scritta su una chiavetta USB con uno strumento come Rufus o balenaEtcher). Ti servirà una chiavetta USB di almeno 8 GB, che verrà **formattata**.

**Boot:** Inserisci la chiavetta USB in una **porta USB posteriore** (più affidabile delle porte frontali durante l'installazione), riavvia e seleziona la chiavetta USB dal **Boot Menu** (solitamente **F8**, **F11** o **F12** — la scheda madre te lo indica all'avvio) o impostala come primo dispositivo di avvio nel BIOS.

**Durante l'installazione**, quando ti chiede dove installare, seleziona il tuo **NVMe SSD** (identificabile dalla dimensione). Se ci sono partizioni preesistenti, eliminale e usa lo spazio non allocato.

**Dopo l'installazione, l'ordine dei driver è importante:**
1. **Driver del chipset** — prima, sempre. Scaricali dal sito della **scheda madre** (o direttamente da AMD/Intel, spesso più aggiornati). Insegnano al sistema operativo come comunicare con la piattaforma: gestione energetica, scheduling dei core, controller.
2. **Driver della GPU** — direttamente dal sito **NVIDIA / AMD / Intel**, **non** dal disco incluso nella scatola (è vecchio di mesi).
3. **Driver di rete, audio e altri** — Windows Update ne installa molti automaticamente; per il resto, controlla il sito della scheda madre.

### 12.6 Test: controllo temperature e stabilità

Non dare per scontato che un PC sia a posto solo perché si accende. Un PC che si accende può comunque avere un dissipatore montato male, una curva delle ventole sbagliata o un profilo di memoria instabile. I test sono la prova.

**Gli strumenti (tutti gratuiti):**
- **HWiNFO64** (in modalità *Sensors only*): la finestra della verità. Mostra temperature, frequenze, voltaggi e consumi di ogni componente, con valori **minimi/massimi/medi**. Tienilo aperto durante ogni test.
- **Cinebench** (R23 o versioni successive): un benchmark che carica **tutti i core della CPU al 100%** per 10 minuti. È il test più immediato: ti dà un punteggio (confrontabile online con CPU identiche alla tua, per capire se la tua sta performando come dovrebbe) e porta la CPU alla sua temperatura di picco realistica.
- **OCCT** o **Prime95**: stress test più severi. Utili per verificare la stabilità di un profilo XMP/EXPO (il test *Memory* di OCCT, per un'ora, è la validazione standard) e la tenuta dell'alimentatore.
- **FurMark** o **3DMark**: carico della GPU.
- **MemTest86** (avviato da chiavetta USB, fuori dal sistema operativo): il test definitivo della RAM. Un passaggio completo, senza errori, certifica che la tua memoria è a posto.
- **CrystalDiskMark** o **CrystalDiskInfo**: velocità e salute degli SSD.

**Cosa cercare, e cosa è normale:**

| Componente | A riposo | Sotto pieno carico | Allarme |
|---|---|---|---|
| **CPU** | ~30-50 °C | Fino a 85-95 °C è **normale** su molte CPU moderne, che sono progettate per operare al loro limite termico e regolarsi | Se raggiunge il limite (95-100 °C) **entro pochi secondi** dall'inizio del test → **dissipatore montato male / pellicola non rimossa** |
| **GPU** | ~30-45 °C | 65-80 °C tipici (**VRAM/hotspot** possono essere più alti) | > 85-90 °C sostenuti, o ventole costantemente al 100% |
| **NVMe SSD** | 35-50 °C | Fino a ~70 °C in scrittura sostenuta | Throttling visibile (cali di velocità) → dissipatore/pellicola |
| **Stabilità** | — | Nessun crash, nessun errore, nessun riavvio | Anche **un singolo** errore in MemTest o un crash in OCCT = problema reale, quasi sempre XMP/EXPO |

Una precisazione importante, perché genera ansia inutile: **una CPU moderna che raggiunge i 90 °C sotto Cinebench non è "difettosa" né "montata male"**. Le CPU odierne utilizzano algoritmi di boost opportunistici che spingono frequenza e voltaggio **fino a saturare il budget termico disponibile**: se il dissipatore è buono, la CPU va più veloce e rimane comunque calda. Quello che conta non è il numero assoluto, ma **quanto tempo ci mette ad arrivarci** (secondi = male, minuti = normale), **se ci rimane senza calare**, e **che frequenza mantiene**. Un test di 10 minuti che finisce a 88 °C con frequenze piene è un sistema sano. Un test che arriva a 100 °C in cinque secondi e cala alle frequenze base è un sistema con un problema di montaggio.

---

## Riepilogo Operativo — la checklist di assemblaggio

Tieni questo aperto accanto al tuo tavolo. Non passare al passo successivo finché il precedente non è verificato.

**PRIMA DI APRIRE LE SCATOLE**
- [ ] Cacciavite **PH2 magnetico**, fascette, luce, superficie ampia non tessile
- [ ] Controllo finale compatibilità: **socket** / **BIOS che supporta la CPU** (c'è il **Flashback**?) / **RAM DDR corretta** / **ingombro dissipatore** / **lunghezza GPU** / **PSU: wattaggio + connettori**
- [ ] Cavo di alimentazione **staccato dal muro** ad ogni fase del lavoro
- [ ] Scaricarsi toccando lo chassis del case (o braccialetto antistatico) e ripetere dopo ogni pausa

**PRE-ASSEMBLAGGIO A BANCO (mobo sulla sua SCATOLA, non sul sacchetto antistatico)**
- [ ] **CPU:** levetta aperta → allineare triangolo → **appoggiata**, non premuta (forza = zero) → levetta chiusa (forza = alta, resistenza elastica **sì**, stop netto **no**)
- [ ] **RAM:** slot **A2/B2** (**leggere il manuale**) → allineare tacca → premere alle estremità fino al **doppio click** → contatti dorati **scomparsi**
- [ ] **M.2:** slot **primario (CPU)** → **PELLICOLA THERMAL PAD RIMOSSA** → inserito a 30° → vite senza forzare
- [ ] **Dissipatore:** backplate + standoff → **PASTA TERMICA sempre** (puntino a pisello al centro) → **AIO: RIMUOVERE PELLICOLA da cold plate** → stringere **a croce, gradualmente**, fino a battuta → ventola/pompa su **CPU_FAN / AIO_PUMP**
- [ ] *(Consigliato)* **Test POST a banco** prima di mettere tutto nel case

**CASE**
- [ ] Standoff: **uno sotto ogni foro**, **nessuno standoff orfano** (rischio corto)
- [ ] **I/O shield** montato nell'orientamento corretto (se non integrato)
- [ ] Cavi frontali pre-instradati attraverso i gommini

**PSU**
- [ ] Ventola **verso il basso** se c'è filtro grigliato, verso l'interno se il fondo è chiuso
- [ ] **Solo cavi originali di QUEL modello di PSU** (mai cavi di altri PSU!)
- [ ] Cavi modulari collegati **prima** di inserirlo nel case

**MOBO E RADIATORE**
- [ ] Mobo allineata con I/O shield, avvitata **a croce**, **senza stringere troppo**
- [ ] Radiatore AIO: **scarico in alto** (o immissione frontale) con **tubi in basso** — **la pompa non deve mai essere il punto più alto**
- [ ] Usare solo viti del kit (viti troppo lunghe **bucano il radiatore**)

**GPU**
- [ ] **Primo slot x16** (l'unico elettricamente x16 dalla CPU) — controllare manuale
- [ ] Staffe rimosse, clip aperta, inserita fino al **click**, avvitata
- [ ] **NO daisy-chaining** sui cavi PCIe per GPU potenti
- [ ] **12V-2×6 inserito FINO IN FONDO**, nessuna piega entro **35 mm** — **ricontrollare a case chiuso**
- [ ] Supporto anti-sag se la scheda flette

**CABLAGGIO**
- [ ] **24-pin** (il fermo scatta) — **EPS 8-pin** (in alto a sinistra: **è "4+4", PCIe è "6+2"**) — **PCIe/12V-2×6** — **SATA power**
- [ ] **F_PANEL** secondo lo **schema del manuale** (una connessione sbagliata non rompe nulla, ma non si avvia)
- [ ] Header **USB 3.0** (pin fragili!), **USB-C**, **USB 2.0**, **HD Audio** (non confondere con USB 2.0)
- [ ] Ventole: **CPU_FAN** obbligatoria; 3 pin = **DC**, 4 pin = **PWM** (impostare nel BIOS)
- [ ] **ARGB 5V (3 pin, con un buco) ≠ RGB 12V (4 pin). MAI mescolarli: brucerà tutto.**
- [ ] Cavi dietro il vassoio, fascette, **nessun cavo davanti a ventole o dissipatore**

**PRIMO AVVIO**
- [ ] Lista di controllo pre-accensione (viti perse? attrezzi all'interno? EPS collegato?)
- [ ] **Monitor collegato alla GPU, NON alla scheda madre**
- [ ] POST: interpretare i **LED CPU / DRAM / VGA / BOOT** o il **Q-Code**
- [ ] **DDR5: il primo POST può richiedere minuti (memory training). ASPETTARE.**
- [ ] Se non si avvia: **una variabile alla volta** → reinserire la RAM → **solo un modulo in A2** → **cancellare il CMOS** → **breadboarding**

**CONFIGURAZIONE**
- [ ] BIOS: tutto rilevato? (CPU, **RAM totale corretta**, drive, ventole, **temperature sane**)
- [ ] **Abilitare XMP / EXPO** (è performance che hai già pagato)
- [ ] Aggiornare il BIOS (**dall'utility interna, mai da Windows**) → rifare XMP e ventole
- [ ] **Resizable BAR** + **Above 4G Decoding** = Abilitato
- [ ] Curve delle ventole con isteresi; header DC/PWM corretti
- [ ] OS da USB → **driver del chipset PRIMA**, poi **driver della GPU dal sito del produttore**
- [ ] Test: **Cinebench + HWiNFO** (temperature CPU), **OCCT/MemTest86** (stabilità XMP), **FurMark/3DMark** (GPU)
- [ ] Temperature di picco elevate ≠ guasto. **Temperature di picco raggiunte in POCHI SECONDI = dissipatore montato male.**

---

*Nota sui dati: le designazioni dei socket, dei chipset e dei modelli di connettori menzionati riflettono lo stato del mercato al momento della stesura e sono la parte più volatile di questo capitolo; le procedure fisiche, le forze e gli ordini di assemblaggio, tuttavia, sono sostanzialmente stabili da oltre un decennio. Controlla sempre il manuale della tua specifica scheda madre: è l'unica fonte autorevole per il layout di slot, header e pin.*

---

[← Precedente](08-raffreddamento.html) · [Tutti i capitoli](./) · [Successivo →](10-cpu-workstation-hedt.html)
