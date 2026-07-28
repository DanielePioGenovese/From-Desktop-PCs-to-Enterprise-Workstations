---
title: "Capitolo 11 - GPU Nvidia per l'AI: Dalla Workstation al Datacenter"
parent: "Edizione italiana"
nav_order: 11
---

<details open markdown="block">
  <summary>Indice del capitolo</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## Nota metodologica preliminare

Questo capitolo tratta un settore in cui i dati invecchiano più velocemente di quanto un libro possa essere stampato. Tutte le informazioni riportate sono aggiornate a metà 2026, ma tre categorie di dati devono essere considerate **per loro natura volatili** e sempre verificate alla fonte prima di prendere una decisione di acquisto:

1.  **Prezzi.** Le GPU per datacenter non hanno un listino pubblico stabile: vengono acquistate tramite OEM (Original Equipment Manufacturers, ovvero produttori di server come Dell, Supermicro, HPE, Lenovo) o integratori di sistemi, con prezzi che dipendono dal volume, dal contratto e dal momento storico. Le cifre che troverete qui sono ordini di grandezza documentati dalla stampa di settore, non listini prezzi.
2.  **Disponibilità.** Nel 2024–2026, la domanda ha costantemente superato l'offerta: un prodotto "annunciato" può avere un tempo di attesa reale di 6–12 mesi.
3.  **L'ultima generazione.** Nvidia ha adottato una cadenza architetturale annuale. Ciò che è "l'ultima" al momento della stesura sarà la penultima entro dodici mesi.

Contrassegnerò esplicitamente con **[DATI VOLATILI]** ogni punto in cui questo problema è particolarmente acuto.

---

## 1. La gerarchia completa di Nvidia

### 1.1 Perché non esiste "la GPU Nvidia", ma tre mondi separati

Chi si avvicina all'hardware per intelligenza artificiale quasi sempre commette lo stesso errore concettuale: pensa che esista una scala continua di prodotti, dalla scheda grafica economica alla scheda grafica costosa, e che l'unica differenza sia "quanto è veloce". Non è così. Nvidia produce tre linee di prodotti che condividono l'architettura di base ma sono **prodotti diversi, con mercati diversi, accordi di licenza diversi e vincoli fisici diversi**. Confonderli porta ad acquisti sbagliati che costano decine di migliaia di euro.

Innanzitutto, definiamo il termine **GPU**: *Graphics Processing Unit*. Nasce negli anni '90 come acceleratore per il rendering 3D nei videogiochi, ovvero per un compito molto specifico: applicare la stessa operazione matematica (una moltiplicazione di matrici, una trasformazione di coordinate, un calcolo di illuminazione) a milioni di elementi indipendenti, tutti in una volta. Questa è la definizione di **calcolo massivo parallelo**. Il fatto storicamente decisivo è che l'addestramento delle reti neurali richiede esattamente la stessa cosa: moltiplicare matrici enormi, ripetute miliardi di volte. La GPU non è stata "adattata" per l'IA; si è trovata nel posto giusto al momento giusto, e Nvidia ha avuto la lungimiranza di costruire un ecosistema software su di essa (CUDA, di cui parleremo nella sezione 7) dieci anni prima che fosse veramente necessario.

Le tre famiglie sono:

**GeForce (consumer).** Schede da gioco: RTX 4090, RTX 5090, RTX 5080 e le loro sorelle minori. Vendute al dettaglio, installate in PC desktop, con dissipatori ad aria e ventole. L'acronimo **RTX** = *Ray Tracing Texel eXtreme*, introdotto con la generazione Turing (2018) per indicare la presenza di unità hardware dedicate al ray tracing. Ai fini dell'IA, il ray tracing è irrilevante; ciò che conta è che queste stesse schede hanno anche i **Tensor Cores** (unità dedicate alla moltiplicazione di matrici a bassa precisione), ed è per questo che una GeForce è una macchina AI perfettamente valida — entro limiti molto precisi.

**RTX PRO / workstation professionale (ex Quadro).** La linea storicamente chiamata *Quadro*, poi rinominata prima "RTX A" (Ampere: A4000, A5000, A6000), poi "RTX Ada" (Ada Lovelace: RTX 5000 Ada, RTX 6000 Ada), e dal 2025 **RTX PRO Blackwell** (RTX PRO 4000/5000/6000 Blackwell). Queste schede sono destinate a workstation professionali: CAD, rendering, simulazione, visualizzazione medica e, sempre più spesso, sviluppo locale di IA. Costano 3-5 volte una GeForce con silicio equivalente.

**Datacenter (ex Tesla).** A100, H100, H200, B100, B200, GB200 e la nuova generazione Rubin. Non hanno uscite video (nella maggior parte dei casi), non hanno ventole nella variante SXM e non possono essere acquistate su Amazon. Sono componenti server, progettati per funzionare al 100% del carico per anni.

### 1.2 Le sette differenze che giustificano tre linee

Vediamole una per una, perché ognuna ha conseguenze pratiche.

**(1) VRAM.** *Video RAM*: la memoria montata sulla scheda, fisicamente separata dalla RAM di sistema. È **il vincolo numero uno** nell'IA. Una RTX 5090 ha 32 GB; una RTX PRO 6000 Blackwell ha 96 GB; una H200 ha 141 GB; una B200 ha 192 GB. La differenza non è "più veloce/più lenta": è **puoi eseguire il modello o no**. Un modello che non rientra nella VRAM semplicemente non si avvierà, o funzionerà a velocità ridicole appoggiandosi alla RAM di sistema.

**(2) Tipo di memoria.** Le schede consumer utilizzano **GDDR** (*Graphics Double Data Rate*, attualmente GDDR7): chip di memoria saldati attorno alla GPU sul PCB. Le schede datacenter utilizzano **HBM** (*High Bandwidth Memory*): stack di die di memoria impilati verticalmente e collegati alla GPU tramite un *interposer* di silicio, con un bus molto ampio (migliaia di bit contro le centinaia per GDDR). HBM costa enormemente di più ma offre una larghezza di banda 3-5 volte superiore. Vedremo nella sezione 8 perché la larghezza di banda è quasi altrettanto importante quanto la capacità.

**(3) ECC.** *Error Correcting Code*: memoria in grado di rilevare e correggere errori a singolo bit. In un PC da gaming, un bit flip dovuto a un raggio cosmico o a una cella di memoria marginale produce un artefatto grafico per un frame, e nessuno se ne accorge. In una sessione di training di tre settimane su 1.000 GPU, produce un NaN (*Not a Number*) che corrompe i gradienti e spreca giorni di calcolo. Le schede Datacenter e RTX PRO hanno ECC; le schede GeForce no (o ne hanno forme parziali e non certificate).

**(4) Driver Certificati.** Nvidia distribuisce driver *Game Ready* per le schede GeForce (ottimizzati per i giochi appena rilasciati, aggiornati ogni due settimane) e driver **enterprise/production branch** per le schede professionali e datacenter: raramente aggiornati, testati per mesi, garantiti stabili con software specifici certificati e con supporto a lungo termine. Se il vostro cluster deve funzionare per due anni senza sorprese, questa differenza vale denaro.

**(5) Fattore di forma e raffreddamento.** Una RTX 5090 è larga tre slot e ha tre ventole che soffiano aria all'interno del case. Metterne quattro in un server è impossibile: si soffocherebbero a vicenda. Le schede datacenter sono disponibili in due formati: **PCIe** (una scheda a doppio slot, *passiva*, cioè senza ventole proprie: si affida al fortissimo flusso d'aria generato dalle ventole dello chassis del server) e **SXM** (un modulo proprietario, senza connettore PCIe, che si avvita direttamente su una scheda madre dedicata e consente TDP e interconnessioni molto più elevate). Torneremo su SXM nella sezione 3.

**(6) Licenza d'uso per Datacenter.** Questo è il punto che quasi nessuno conosce e che causa i problemi peggiori. L'**EULA** (*End User License Agreement*) per i driver Nvidia GeForce proibisce esplicitamente il *datacenter deployment* delle schede GeForce, con un'eccezione storica per il mining di blockchain e per l'uso accademico/di ricerca. In pratica: **non è possibile costruire legalmente un servizio cloud commerciale basato su RTX 4090/5090**. È possibile usarle nella propria azienda, sulla propria workstation, per il proprio prodotto interno; non è possibile affittarle a terzi come capacità di calcolo, né installarle in un datacenter come infrastruttura di servizio. È una clausola contrattuale, non un blocco tecnico: la scheda funziona perfettamente. Ma se siete una startup che vuole vendere inferenza, questo è un rischio legale reale. **[DATI VOLATILI: il testo dell'EULA è cambiato più volte; verificarlo sempre alla data di acquisto.]**

**(7) NVLink.** L'interconnessione ad alta velocità tra GPU. Presente sulle schede datacenter, **assente sull'intera linea consumer RTX 40 e RTX 50** e sulla maggior parte delle schede RTX PRO più recenti. Questa singola scelta di prodotto è ciò che impedisce di costruire l'equivalente economico di un nodo datacenter con schede consumer. È l'argomento della sezione 4, ed è probabilmente il concetto più importante dell'intero capitolo.

### 1.3 Come leggere le nomenclature

Le tre linee hanno tre logiche di denominazione completamente diverse, e questo di per sé è una fonte di confusione.

| Linea | Schema | Esempio | Come leggere |
|---|---|---|---|
| GeForce | RTX + [generazione][livello] | RTX **50**90 | "50" = serie 50 (Blackwell consumer); "90" = livello più alto. 80 = alto, 70 = medio, 60 = entry |
| Workstation | RTX PRO + [numero livello] + [architettura] | RTX PRO 6000 **Blackwell** | 6000 = top-tier; 5000/4000/2000 = livelli inferiori. L'architettura è nel nome per distinguerla da precedenti modelli omonimi (RTX 6000 Ada ≠ RTX PRO 6000 Blackwell!) |
| Datacenter | [Lettera architettura] + [numero] | **H**100, **B**200 | A = Ampere, H = Hopper, B = Blackwell, R = Rubin. Il numero aumenta con il posizionamento. Suffissi: **G**B200 = con CPU **G**race; NVL = configurazione NVLink; il numero dopo NVL indica la dimensione del dominio |

**Classica trappola di nomenclatura:** esiste una "RTX 6000 Ada" e una "RTX PRO 6000 Blackwell". Sono due schede diverse, distanti due generazioni, con 48 GB e 96 GB rispettivamente. Vengono spesso confuse nelle liste dei rivenditori. Leggere sempre l'architettura, mai solo il numero.

---

## 2. Architetture: Cosa cambia veramente tra le generazioni

### 2.1 Il concetto di "architettura" e "processo produttivo"

Due termini vanno nettamente separati.

L'**architettura** è il design logico del chip: quante unità di calcolo, come sono organizzate, che tipi di operazioni possono eseguire, come è strutturata la gerarchia della cache, come è connessa la memoria. Nvidia nomina ogni architettura con il nome di uno scienziato: Pascal (2016), Volta (2017), Turing (2018), Ampere (2020), Hopper (2022), Ada Lovelace (2022, solo consumer/pro), Blackwell (2024), Rubin (2026).

Il **processo produttivo** (o *nodo*) è la tecnologia di fonderia con cui il chip viene fisicamente fabbricato: "4 nanometri", "3 nanometri". Attenzione: questi numeri **non corrispondono più a nessuna dimensione fisica reale** da almeno un decennio. Sono nomi commerciali. Il "4N" di TSMC usato per Hopper e Blackwell è un derivato del nodo a 5 nm ottimizzato per Nvidia. Ciò che conta veramente è la **densità di transistor** e l'efficienza energetica per operazione. Un nodo più avanzato permette di mettere più transistor nella stessa area e consuma meno per operazione — motivo per cui l'industria continua a pagare cifre esorbitanti per accedervi.

### 2.2 Ampere (2020) — A100

Ampere ha introdotto, sul fronte datacenter, tre cose che oggi diamo per scontate.

La prima è la **terza generazione di Tensor Core con supporto TF32**. Un **Tensor Core** è un'unità di calcolo che non esegue una moltiplicazione alla volta, ma esegue in un colpo solo una piccola moltiplicazione matrice-matrice con accumulo (tipicamente 4x4 o simile), che è l'operazione elementare di ogni rete neurale. **TF32** (*TensorFloat-32*) è un formato numerico intelligente: ha lo stesso range dinamico (gli 8 bit di esponente) del classico FP32 ma solo 10 bit di mantissa come FP16. Risultato: i modelli scritti in FP32 giravano più veloci **senza modificare una riga di codice**, perché il Tensor Core convertiva silenziosamente. Fu un enorme acceleratore di adozione.

La seconda è la **memoria HBM2e** con bandwidth attorno ai 2 TB/s nella versione da 80 GB — in confronto, una GPU consumer di quegli anni stava sotto 1 TB/s.

La terza è **MIG** (*Multi-Instance GPU*): la capacità di dividere una singola A100 in fino a sette GPU logiche hardware-isolate, ognuna con la sua fetta di memoria, cache e unità di calcolo. Questo è utile nel cloud, dove un cliente che fa inferenza su un modello piccolo non deve occupare (e pagare) un'intera GPU.

L'A100 esiste nelle versioni da 40 GB e 80 GB, PCIe e SXM4. **[DATI VOLATILI]** Nel 2026, è tecnicamente obsoleto ma commercialmente molto attivo nel mercato dell'usato e sui cloud a basso costo, a prezzi che sono una frazione dell'originale. Per molti carichi di lavoro di inferenza e per il fine-tuning di modelli medi, è ancora l'opzione con il miglior rapporto prezzo/prestazioni sul mercato secondario.

### 2.3 Hopper (2022) — H100 e H200

Hopper è la generazione che ha coinciso con l'esplosione degli LLM (*Large Language Models*) ed è, di fatto, il cavallo di battaglia su cui sono stati addestrati la maggior parte dei modelli famosi.

Innovazioni chiave:

**Il Transformer Engine e il formato FP8.** Il Transformer Engine è un blocco hardware+software che analizza dinamicamente, strato per

**FP4 e il Transformer Engine di seconda generazione.** Se FP8 dimezzava i bit rispetto a FP16, Blackwell li dimezza ancora con **FP4** (nello specifico il formato proprietario **NVFP4**, che usa fattori di scala a livello di blocco per contenere la perdita di precisione). Quattro bit per numero significano **sedici possibili valori in totale**. Sembra assurdo, e per il training lo è quasi sempre; ma per l'**inferenza**, dove il modello è già addestrato e deve solo essere eseguito, la ricerca ha dimostrato che con le giuste tecniche di quantizzazione il degrado di qualità è minimale. Il guadagno è duplice: raddoppio del throughput e dimezzamento della memoria occupata dai pesi.

**Le "sottogenerazioni": B100, B200 e GB200.** Qui un punto di confusione va chiarito. B100 e B200 **usano lo stesso identico silicio**. La differenza è la configurazione: TDP (*Thermal Design Power*, la potenza termica che il sistema di raffreddamento deve dissipare, usata come proxy per il consumo) e frequenze.

- **B100**: ~700 W. Progettato per essere un *drop-in replacement* in rack progettati per H100, che erano già dimensionati per 700 W. Nessuna modifica infrastrutturale, prestazioni superiori. Ha avuto una vita commerciale breve e un ruolo marginale: Nvidia ha spinto tutti verso B200.
- **B200**: ~1000–1200 W a seconda della configurazione. Stesso chip, sbloccato. Prestazioni significativamente più elevate, ma richiede nuovi rack e raffreddamento.
- **GB200**: **non è una GPU**. È un *superchip*: **una CPU Grace + due GPU B200** su un singolo modulo, connesse da **NVLink-C2C** (*Chip-to-Chip*) a 900 GB/s. Non è un semplice "pacchetto commerciale": la connessione CPU-GPU via NVLink-C2C, invece che via PCIe, è circa sette volte più veloce ed è **cache-coherent**, il che significa che CPU e GPU vedono lo stesso spazio di memoria senza copie esplicite. Questo cambia il modo in cui il codice viene scritto: pesi o KV-cache possono essere *offloadati* alla memoria LPDDR della CPU (centinaia di GB) senza pagare il prezzo devastante di un trasferimento PCIe.

**Blackwell Ultra (B300 / GB300).** Come H200 era per H100, B300 è per B200: stessa architettura, memoria HBM3e aumentata (fino a ~288 GB per package) e ottimizzazioni per l'inferenza a bassa precisione, con circa 208 miliardi di transistor. Questa è la "metà generazione" che copre il 2025–2026.

### 2.5 Rubin (2026) — la generazione attuale

**[DATI VOLATILI — questa sezione descrive un prodotto in fase di lancio: verificare tutto.]**

Rubin è l'architettura che succede a Blackwell. Nvidia ha annunciato la sua piena produzione all'inizio del 2026, con disponibilità per partner e fornitori di cloud nella seconda metà dell'anno. I punti chiave, come pubblicamente noti a metà 2026:

- **Rubin GPU (spesso indicata come R100/R200)**: nodo TSMC 3nm, design **dual-die** come Blackwell ma con circa **336 miliardi di transistor** (~1.6× Blackwell).
- **Memoria HBM4**: circa **288 GB** per package con una larghezza di banda dichiarata fino a circa **22 TB/s** — quasi il triplo di Blackwell. Il salto deriva principalmente dal raddoppio della larghezza del bus per stack.
- **Compute**: nell'ordine di **50 PFLOPS in NVFP4** per package (rispetto a ~20 per Blackwell).
- **Vera CPU**: sostituisce Grace. CPU ARM personalizzata con 88 core "Olympus", fino a 1.5 TB di LPDDR5X, collegata alle GPU tramite NVLink-C2C a 1.8 TB/s.
- **NVLink 6**: circa 3.6 TB/s per GPU, il doppio della generazione Blackwell.
- **Sistema**: il rack di riferimento è il **VR200 NVL72** (72 package GPU), con un consumo energetico nell'ordine di 190–230 kW per rack — rispetto a ~120–130 kW per il GB200 NVL72. Il raffreddamento a liquido è **obbligatorio**: non esistono configurazioni raffreddate ad aria.

La roadmap dichiarata prosegue con **Rubin Ultra** (2027, rack "Kyber" a ~600 kW) e **Feynman** (2028). Il messaggio strategico è chiaro e deve essere compreso da chiunque pianifichi infrastrutture: **Nvidia ha adottato una cadenza annuale in cui non solo il chip ma l'intero involucro fisico cambia** — alimentazione, raffreddamento, rete. Chiunque costruisca un data center oggi lo sta costruendo per hardware che, entro due cicli, non sarà più compatibile.

### 2.6 Tabella riassuntiva generazionale

| | Ampere (A100) | Hopper (H100) | Hopper (H200) | Blackwell (B200) | Blackwell Ultra (B300) | Rubin (R100) |
|---|---|---|---|---|---|---|
| Anno | 2020 | 2022 | 2023–24 | 2024–25 | 2025–26 | 2026 (ramp) |
| Nodo | TSMC 7N | TSMC 4N | TSMC 4N | TSMC 4NP | TSMC 4NP | TSMC 3nm |
| Die | singolo | singolo | singolo | **dual-die (NV-HBI)** | dual-die | dual-die |
| Transistor | 54 mld | 80 mld | 80 mld | 208 mld | ~208 mld | ~336 mld |
| Memoria | HBM2e 40/80 GB | HBM3 80 GB | HBM3e 141 GB | HBM3e 192 GB | HBM3e ~288 GB | **HBM4 ~288 GB** |
| Larghezza di banda | ~2.0 TB/s | ~3.35 TB/s | ~4.8 TB/s | ~8 TB/s | ~8 TB/s | **~22 TB/s** |
| Precisione minima | FP16/BF16 | **FP8** | FP8 | **FP4 (NVFP4)** | FP4 | FP4/FP6 |
| NVLink | 4a gen, 600 GB/s | 4a gen, 900 GB/s | 900 GB/s | 5a gen, 1.8 TB/s | 1.8 TB/s | **6a gen, 3.6 TB/s** |
| TDP (SXM) | 400 W | 700 W | 700 W | ~1000–1200 W | ~1400 W | ~1800–2300 W |

*Tutti i valori sono indicativi e si riferiscono alle varianti SXM di fascia alta. Le versioni PCIe hanno sempre TDP e larghezza di banda NVLink inferiori.*

---

## 3. Schede per data center, una per una

### 3.1 Premessa: SXM contro PCIe

Prima di esaminare i singoli prodotti, è importante comprendere questa distinzione, poiché è la prima domanda che qualsiasi venditore vi porrà.

**PCIe** (*Peripheral Component Interconnect Express*) è il bus standard con cui qualsiasi scheda si collega alla scheda madre di qualsiasi computer. Una GPU per data center in formato PCIe assomiglia a una scheda video molto lunga, a doppio slot, **senza ventole**: è *passiva* e si affida al forte flusso d'aria che le ventole del server spingono attraverso lo chassis. Vantaggi: si monta in un server standard, costa meno e può essere acquistata una alla volta. Svantaggi: il **TDP è limitato** (tipicamente 300–600 W, perché il connettore e lo slot non possono gestire di più) e, in modo critico, **l'interconnessione tra le GPU è limitata** — al massimo un singolo bridge NVLink tra due schede, o nulla del tutto nelle generazioni recenti.

**SXM** è un formato proprietario Nvidia. Il modulo non ha un connettore PCIe: si avvita su una **baseboard** dedicata (tipicamente HGX, la scheda a 4 o 8 GPU che Nvidia vende agli OEM). La baseboard fornisce alimentazione (fino a 700–1400 W per modulo) e linee NVLink agli NVSwitch. Vantaggi: TDP molto più elevato (quindi frequenze più alte, quindi maggiori prestazioni dallo stesso silicio), e **NVLink a piena larghezza di banda tra tutte le GPU nel nodo**. Svantaggi: può essere acquistato solo come sistema completo a 4 o 8 GPU, è molto costoso e richiede un server appositamente progettato.

**La regola pratica:** se stai eseguendo un *training* distribuito su più GPU, hai bisogno di SXM. Se stai eseguendo *inferenza* con modelli che rientrano in una singola GPU, PCIe è più che sufficiente — e molto più economico.

### 3.2 A100 (40 GB / 80 GB)

- **VRAM:** 40 GB o 80 GB HBM2e
- **Larghezza di banda:** ~1.55 TB/s (40 GB) / ~2.0 TB/s (80 GB SXM)
- **TDP:** 250–300 W (PCIe) / 400 W (SXM4)
- **Fattore di forma:** PCIe e SXM4
- **Prestazioni indicative:** ~312 TFLOPS in FP16 con sparsità, ~19.5 TFLOPS FP32
- **Prezzo [DATI VOLATILI]:** fuori produzione da tempo; sul mercato dell'usato e ricondizionato, si possono trovare a una frazione del prezzo originale (che era di circa $10.000–$15.000 per la 80 GB). Sui cloud, è tra le opzioni più economiche disponibili.
- **Casi d'uso:** inferenza di modelli fino a ~30–70 miliardi di parametri quantizzati, fine-tuning, ricerca accademica, carichi di lavoro HPC classici (simulazione, CFD). **Non** ha FP8: per l'inferenza LLM moderna, che si basa su FP8/FP4, questa è una seria limitazione architetturale.
- **Chi dovrebbe acquistarla:** chi ha un budget limitato, un carico di lavoro noto e l'esperienza per gestire hardware usato. È l'"opzione usata sicura" nel settore.

### 3.3 H100

- **VRAM:** 80 GB HBM3 (esiste anche una H100 NVL con 94 GB, una variante a doppia scheda)
- **Larghezza di banda:** ~3.35 TB/s (SXM5) / ~2.0 TB/s (PCIe)
- **TDP:** 700 W (SXM5) / 350 W (PCIe)
- **Prestazioni:** ~1.979 TFLOPS FP8 con sparsità (SXM5). La versione PCIe ha prestazioni **circa il 20–30% inferiori** a causa del TDP dimezzato e della minore larghezza di banda.
- **Prezzo [DATI VOLATILI]:** l'ordine di grandezza riportato pubblicamente è di $25.000–$40.000 per scheda; un nodo HGX H100 a 8 GPU è nella fascia $250.000–$400.000.
- **Casi d'uso:** è stata *la* GPU del boom degli LLM. Training di modelli fino a decine di miliardi di parametri (in cluster), inferenza ad alta produttività, fine-tuning serio.

**Errore comune:** acquistare la H100 PCIe pensando che sia "una H100 leggermente più economica". Non lo è: è una GPU significativamente più lenta, con NVLink limitato o assente, che può avere prestazioni dimezzate in un carico di lavoro multi-GPU. Se il tuo carico di lavoro è multi-GPU, la PCIe è quasi sempre un falso risparmio.

### 3.4 H200

- **VRAM:** 141 GB HBM3e
- **Larghezza di banda:** ~4.8 TB/s
- **TDP:** 700 W (SXM5), lo stesso del suo predecessore
- **Chip di calcolo:** **identico all'H100**. Nessun aumento di TFLOPS.
- **Prezzo [DATI VOLATILI]:** superiore all'H100, nell'ordine di $30.000–$40.000.
- **Casi d'uso:** inferenza LLM. Qui il guadagno è drammatico — spesso 1.5–2x rispetto all'H100 sullo stesso modello — **non perché calcola di più, ma perché l'inferenza autoregressiva è limitata dalla larghezza di banda della memoria**. Ogni token generato richiede la rilettura di tutti i pesi del modello dalla VRAM. Più larghezza di banda = più token al secondo, punto.

Questo è il momento di affermare chiaramente qualcosa che ricorrerà in tutto il capitolo: **nell'inferenza LLM, la larghezza di banda della memoria è più importante dei TFLOPS.** Chiunque confronti le GPU guardando solo i TFLOPS sta guardando la metrica sbagliata.

### 3.5 B100, B200, GB200

- **B100:** ~192 GB HBM3e, ~8 TB/s, 700 W. Compatibile con l'infrastruttura H100. Prodotto di transizione, non ampiamente adottato.
- **B200:** ~192 GB HBM3e, ~8 TB/s, ~1000–1200 W. Circa 20 PFLOPS in FP4 denso. Questa è la vera GPU Blackwell per datacenter, disponibile su schede madri HGX B200 a 8 GPU.
- **GB200:** 1 CPU Grace + 2 GPU B200 su un superchip. ~384 GB di HBM3e (2×192) più fino a 480 GB di LPDDR5X sulla Grace. Consumo energetico del modulo intorno ai 2700 W. **Richiede raffreddamento a liquido.**
- **Prezzo [DATI VOLATILI]:** una singola B200 è nell'ordine di $30.000–$40.000; un sistema GB200 NVL72 completo è stato stimato dalla stampa specializzata a circa $3 milioni.

### 3.6 GB200 NVL72: quando l'unità di calcolo diventa il rack

Questo è il concetto più importante di questa sezione e segna la fine di un'era.

Fino a Hopper, l'unità di calcolo era **il nodo**: un server con 8 GPU connesse tramite NVLink, e i nodi stessi connessi tramite rete (InfiniBand). Il divario di larghezza di banda tra "all'interno del nodo" (900 GB/s) e "all'esterno del nodo" (50–100 GB/s) era di un ordine di grandezza, e l'intera teoria dell'addestramento distribuito era costruita attorno a questa disparità.

Il **GB200 NVL72** riscrive la regola. È un rack che contiene **36 superchip GB200**, il che significa **72 GPU B200 + 36 CPU Grace**, **tutti connessi da un singolo dominio NVLink** tramite 9 vassoi NVSwitch. Le 72 GPU vedono uno spazio di memoria condiviso di circa 13,5 TB di HBM3e, con una larghezza di banda NVLink aggregata nell'ordine di 130 TB/s. Dal punto di vista software, **l'intero rack si comporta come una gigantesca GPU**.

Perché è importante? Perché un modello con centinaia di miliardi o trilioni di parametri, che in precedenza doveva essere suddiviso tra molti nodi pagando la penalità di rete ad ogni livello, ora vive all'interno di un singolo dominio ad altissima larghezza di banda. Per i modelli **MoE** (*Mixture of Experts*) (architetture in cui ogni token attiva solo un sottoinsieme di "esperti" e quindi genera molto traffico di routing tra le GPU), il guadagno è enorme.

Il prezzo da pagare è fisico: ~120–130 kW per rack, raffreddamento a liquido obbligatorio e un'infrastruttura elettrica che la maggior parte dei datacenter esistenti nel mondo **semplicemente non possiede**. Un rack tradizionale consuma 5–15 kW.

### 3.7 Grace: perché ARM nel datacenter

**Grace** è la CPU progettata da Nvidia sull'architettura **ARM** (specificamente core Neoverse V2), con 72 core, memoria **LPDDR5X** saldata sul package (fino a 480 GB, ~500 GB/s di larghezza di banda) e — questo è il punto chiave — una connessione **NVLink-C2C** alla GPU a 900 GB/s, **cache-coherent**.

Perché ARM e non x86? Tre ragioni, in ordine di importanza reale:

1. **La connessione.** Nvidia non poteva inserire NVLink-C2C in un processore Intel o AMD: non controlla quei progetti. Per avere un percorso CPU-GPU sette volte più veloce del PCIe, ha dovuto creare la propria CPU. ARM le ha concesso una licenza architetturale su cui basarsi. **La CPU Grace esiste principalmente per essere il socket giusto accanto alla GPU**, non perché sia una CPU rivoluzionaria di per sé.
2. **Efficienza energetica.** In un rack da 130 kW, ogni watt speso dalla CPU è un watt sottratto alle GPU. I core ARM con memoria LPDDR (memoria a basso consumo, come nei telefoni, saldata e non espandibile) offrono un eccellente rapporto prestazioni-per-watt per il ruolo che devono svolgere qui: alimentare i dati alle GPU, gestire l'I/O, orchestrare.
3. **Controllo verticale.** Nvidia vuole vendere il sistema completo, non solo un componente. Grace fa parte di questa strategia.

Con Rubin, Grace viene sostituita da **Vera**, con core ARM interamente personalizzati.

**Implicazione pratica per lo sviluppatore:** un sistema Grace è una macchina **aarch64**, non x86_64. Il tuo container Docker, il tuo wheel Python compilato, il tuo binario proprietario devono esistere per ARM. Nell'ecosistema AI mainstream (PyTorch, CUDA, container NGC di Nvidia) questo problema è ormai risolto. Nel software aziendale legacy, spesso no. **È il primo controllo da fare prima dell'acquisto.**

---

## 4. NVLink e Interconnessioni

### 4.1 Perché PCIe non è sufficiente

Immagina di addestrare un modello su 8 GPU con **parallelismo dei dati**: ogni GPU riceve una fetta diversa dei dati, calcola i suoi gradienti (le derivate della funzione di perdita rispetto a ciascun parametro), e poi tutte devono **fare la media dei gradienti tra loro** prima di aggiornare i pesi. Questa operazione si chiama **all-reduce** e deve essere eseguita **ad ogni singolo passo di addestramento**, cioè decine di migliaia di volte.

Quanti dati vengono scambiati? Tanti quanti i parametri del modello, moltiplicati per la dimensione in byte di ciascuno. Per un modello da 7 miliardi di parametri in BF16, sono 14 GB, e all-reduce ne sposta circa il doppio.

Ora facciamo i conti. **PCIe 5.0 x16** offre circa **64 GB/s** per direzione. **NVLink 4** offre **900 GB/s**. Il rapporto è di 14 a 1. In uno scenario di addestramento reale, questo significa che con PCIe, le GPU trascorrono una frazione enorme del tempo **inattive, in attesa dei dati dai loro compagni**. L'utilizzo effettivo crolla, e otto GPU si comportano come tre o quattro.

Poi c'è la **latenza**, che è un problema distinto dalla larghezza di banda: PCIe passa attraverso il *root complex* della CPU, con overhead di protocollo. NVLink è una connessione diretta punto-punto tra GPU, con una latenza di un ordine di grandezza inferiore. Nei modelli **tensor-parallel** (dove un singolo strato è diviso tra più GPU e ogni GPU deve scambiare attivazioni **all'interno del forward pass**, decine di volte per token), la latenza è più critica della larghezza di banda. Con PCIe, il parallelismo tensoriale è semplicemente **impraticabile**.

### 4.2 NVLink: Generazioni

**NVLink** è l'interconnessione proprietaria di Nvidia introdotta nel 2016 con Pascal. Non è un bus: è un insieme di **collegamenti seriali punto-punto** (chiamati *bricks* o *links*) che possono essere aggregati. Ogni generazione ha aumentato la velocità per link e il numero di link per GPU.

| Generazione | Anno | Architettura | Larghezza di banda bidirezionale per GPU |
|---|---|---|---|
| NVLink 1 | 2016 | Pascal (P100) | 160 GB/s |
| NVLink 2 | 2017 | Volta (V100) | 300 GB/s |
| NVLink 3 | 2020 | Ampere (A100) | 600 GB/s |
| NVLink 4 | 2022 | Hopper (H100/H200) | 900 GB/s |
| NVLink 5 | 2024 | Blackwell (B200/GB200) | 1.800 GB/s (1.8 TB/s) |
| NVLink 6 | 2026 | Rubin | ~3.600 GB/s (3.6 TB/s) **[DATI VOLATILI]** |

**NVSwitch.** Connettere 8 GPU "all-to-all" direttamente richiederebbe 28 collegamenti fisici, e non è scalabile. L'NVSwitch è un **chip switch** dedicato: ogni GPU si connette allo switch, e lo switch instrada il traffico. Il risultato è che **ogni GPU parla con ogni altra GPU a piena larghezza di banda**, simultaneamente, senza colli di bottiglia. Nel GB200 NVL72, gli NVSwitch si spostano fuori dal server e diventano vassoi dedicati nel rack, estendendo il dominio NVLink a 72 GPU. Questo è esattamente il salto che ha reso possibile "il rack come unità di calcolo".

### 4.3 La rimozione di NVLink dalle schede consumer e professionali

Questo paragrafo è probabilmente **il più importante del capitolo per chiunque stia costruendo una workstation**.

- La RTX 3090 (Ampere) aveva NVLink: due schede potevano essere accoppiate con un bridge.
- **La RTX 4090 (Ada) e la RTX 5090 (Blackwell consumer) NON hanno NVLink.** Nessun bridge, nessuna opzione.
- Anche nella linea professionale, il supporto si è drasticamente ridotto: la RTX 6000 Ada non aveva NVLink; le schede RTX PRO Blackwell nemmeno.

**Le implicazioni concrete:**

1. **Non è possibile sommare la VRAM.** Due RTX 5090 non creano una GPU da 64 GB. Creano due GPU separate da 32 GB. Un modello che non entra in 32 GB non entra, punto — a meno di dividerlo manualmente su PCIe, con le penalità discusse sopra.
2. **Il parallelismo tensoriale è fuori discussione.** Si può fare *parallelismo di pipeline* (mettere i layer 1–20 sulla GPU A e 21–40 sulla GPU B, con un solo scambio al confine): questo funziona anche su PCIe perché il traffico è molto inferiore. Ma la velocità sarà mediocre.
3. **Il multi-GPU consumer è principalmente per carichi di lavoro indipendenti:** quattro lavori diversi su quattro GPU, quattro modelli in inferenza, quattro esperimenti in parallelo. Questo funziona perfettamente. L'addestramento distribuito di un singolo modello grande, no.

Non è una limitazione tecnica: è una **decisione commerciale**. Nvidia non vuole che le persone costruiscano nodi di addestramento economici con schede da gioco. Saperlo vi eviterà di spendere 10.000 € per una workstation che non farà ciò che sperate.

### 4.4 Oltre il nodo: InfiniBand ed Ethernet/RoCE

Quando si va oltre le GPU di un singolo nodo (o di un singolo rack NVL72), è necessario passare alla **rete**.

**InfiniBand** è una tecnologia di rete progettata per l'HPC (*High Performance Computing*), acquisita da Nvidia con Mellanox. La sua caratteristica distintiva è il **RDMA** (*Remote Direct Memory Access*): una GPU può scrivere direttamente nella memoria di una GPU su un altro server **senza coinvolgere le CPU o i sistemi operativi**. Il risultato è una latenza molto bassa (~1 microsecondo) e un controllo della congestione affidabile. Le generazioni attuali sono NDR (400 Gb/s) e XDR (800 Gb/s). È la scelta classica per i grandi cluster di addestramento.

**Ethernet con RoCE** (*RDMA over Converged Ethernet*) porta RDMA all'Ethernet standard. Vantaggi: costo inferiore, utilizza l'esperienza e le attrezzature aziendali esistenti, evita il vendor lock-in. Svantaggi: Ethernet è intrinsecamente *lossy* (può perdere pacchetti in caso di congestione) e l'IA richiede la configurazione di meccanismi di rete lossless (PFC, ECN) che sono notoriamente difficili da mettere a punto. Nvidia vende la propria piattaforma Ethernet ottimizzata per l'IA (**Spectrum-X**) proprio per colmare questa lacuna.

**Regola generale:** al di sotto di ~64 GPU, la rete è raramente il principale collo di bottiglia e l'Ethernet va bene. Al di sopra di migliaia di GPU, la qualità dell'interconnessione determina la scalabilità dell'intero investimento, e ogni punto percentuale di efficienza vale milioni.

---

## 5. Workstation e Sistemi Pre-assemblati

### 5.1 La Linea DGX

**DGX** è la linea di sistemi "chiavi in mano" di Nvidia: server completi, progettati, assemblati e supportati da Nvidia, con stack software preinstallato. Non si acquistano GPU: si acquista **un sistema garantito funzionante**, con supporto enterprise, e si risparmiano mesi di sforzi di integrazione.

- **DGX H100:** 8× H100 SXM5 (640 GB HBM3), 2 CPU Intel Xeon, ~10 kW. **[DATI VOLATILI]** Prezzo di listino storicamente riportato intorno a $300.000–$400.000.
- **DGX B200:** 8× B200 (1.440 GB HBM3e), raffreddato ad aria (l'ultimo DGX a permetterlo), ~14,3 kW. Ordine di grandezza: $500.000.
- **DGX GB200 / GB200 NVL72:** il rack completo descritto nella sezione 3.6. Milioni di dollari.
- **DGX SuperPOD:** l'aggregazione di molte unità DGX in un supercomputer chiavi in mano, con networking, storage e software di gestione. Si parla di decine di milioni.

**A chi è destinata la linea DGX?** Organizzazioni in cui il costo del tempo di ingegneria supera il premio hardware: laboratori di ricerca finanziati, aziende Fortune 500, fornitori di cloud. Se stai calcolando quanto risparmieresti acquistando i componenti separatamente, **non sei il cliente DGX** — e va benissimo così.

### 5.2 DGX Spark (precedentemente Project DIGITS)

Questo prodotto merita attenzione perché è il primo serio tentativo di Nvidia di portare l'architettura dei datacenter sul desktop a un prezzo non assurdo, e perché è **profondamente incompreso**.

**Cos'è:** una scatola delle dimensioni di un piccolo Mac Mini, basata sul superchip **GB10 Grace Blackwell**: una CPU ARM (20 core, in collaborazione con MediaTek) e una GPU Blackwell in un unico package, con **128 GB di memoria LPDDR5X unificata** condivisa tra CPU e GPU. Circa **1 PFLOP di calcolo FP4**. Consumo energetico di circa 240 W: alimentato da una presa domestica standard. **[DATI VOLATILI]** Prezzo di lancio intorno a $3.000–$4.000.

**A cosa serve realmente.** La cosa straordinaria è la **capacità di memoria**: 128 GB accessibili alla GPU significano poter caricare modelli che non entrerebbero mai su nessuna GPU consumer. Nvidia parla di modelli fino a ~200 miliardi di parametri (quantizzati) su una singola unità, e fino a ~400 miliardi collegandone due. Per uno sviluppatore che ha bisogno di **prototipare, sperimentare, testare pipeline, eseguire inferenze locali su modelli di grandi dimensioni**, è un oggetto impareggiabile a quel prezzo.

**Il limite, e va detto con brutale chiarezza: la larghezza di banda della memoria.** L'LPDDR5X del GB10 offre nell'ordine di **270 GB/s**. Si confronti con i ~1.800 GB/s di una RTX 5090 o i 4.800 GB/s di un H200. È **6–18 volte più lento**. E abbiamo già stabilito che **l'inferenza LLM è limitata dalla larghezza di banda della memoria**. Conseguenza pratica: sulla DGX Spark, un modello grande **ci sta**, ma genera token lentamente. Non è una macchina da produzione, non è una macchina da benchmark di throughput.

**Il posizionamento corretto è questo:** la DGX Spark è una **macchina di sviluppo**. Serve a scrivere e validare codice che poi girerà su una vera DGX o in cloud, con lo **stesso identico stack software e la stessa architettura ARM+Blackwell**. Il valore è nella fedeltà dell'ambiente, non nella velocità. Chi la compra aspettandosi una GPU da inferenza veloce rimarrà deluso, e la colpa è di un marketing ambiguo, non del prodotto.

### 5.3 DGX Station (GB300)

**[DATI VOLATILI]** La DGX Station di nuova generazione è una workstation desktop (formato tower) basata sul superchip **GB300 Grace Blackwell Ultra**, con circa **784 GB di memoria coerente totale** (HBM3e sulla GPU + LPDDR5X sulla CPU), NIC ConnectX ad alta velocità integrata e alimentazione da presa a muro standard. È l'anello mancante tra la DGX Spark e il rack: una macchina da datacenter **vera**, con vera banda HBM, in un formato che sta a fianco di una scrivania. Prezzo previsto: nell'ordine di decine di migliaia di dollari, lontano dalla Spark e ben al di sotto di un rack.

### 5.4 L'alternativa: costruirsi la propria workstation

Ha senso? Facciamo un confronto onesto.

| Configurazione | VRAM Totale | Banda per GPU | NVLink | Costo Indicativo [VOLATILE] | Adatta per |
|---|---|---|---|---|---|
| 2× RTX 5090 | 2×32 GB (non sommabili) | ~1.8 TB/s | ❌ | ~€7.000–9.000 | Inferenza modelli medi, LoRA, Stable Diffusion, job paralleli |
| 4× RTX 5090 | 4×32 GB | ~1.8 TB/s | ❌ | ~€15.000–20.000 | Come sopra ×2; potenza e raffreddamento diventano un problema serio |
| 2× RTX PRO 6000 Blackwell | 2×96 GB | ~1.8 TB/s | ❌ | ~€20.000–25.000 | Inferenza modelli grandi; fine-tuning serio; uso commerciale legittimo |
| DGX Spark | 128 GB unificati | ~0.27 TB/s | — | ~€4.000 | Sviluppo, prototipazione, modelli enormi ma lenti |
| 1× H100 PCIe (usata) | 80 GB | ~2.0 TB/s | limitato | ~€20.000–25.000 | Inferenza professionale, uso datacenter legittimo |
| HGX 8× H100 Node | 640 GB | ~3.35 TB/s | ✅ 900 GB/s | ~€250.000–400.000 | Vero training distribuito |

**Nota fondamentale sulla potenza domestica.** Quattro RTX 5090 da sole assorbono 2.300 W di picco, più CPU, ventole e perdite dell'alimentatore: si superano agevolmente i 3.000 W. Una presa domestica standard italiana eroga 3.000–3.300 W **per tutto l'appartamento**. Questo non è un dettaglio: è un vincolo che chiude fisicamente la discussione. Oltre due schede di fascia alta, serve una linea dedicata e una seria considerazione per l'estrazione del calore da una stanza in cui si stanno scaricando l'equivalente termico di tre stufette.

---

## 6. GPU consumer per AI: quando bastano

### 6.1 RTX 4090 e RTX 5090

**RTX 4090** (Ada, 2022): 24 GB GDDR6X, ~1.008 GB/s di banda, 450 W.
**RTX 5090** (Blackwell, 2025): 32 GB GDDR7, ~1.792 GB/s di banda, 575 W. **[DATI VOLATILI: MSRP ~ $2.000, prezzo di mercato storicamente molto più alto.]**

Il salto della 5090 è significativo per l'AI **soprattutto per la banda** (+78%) e per il supporto nativo FP4, non tanto per i 32 GB — che sono ancora pochi.

**C

CUDA è un linguaggio (un'estensione di C/C++), un compilatore, un runtime, un modello di programmazione. Ma **il vero vantaggio competitivo non è CUDA in sé: è ciò che è cresciuto al di sopra di esso.**

- **cuDNN** (*CUDA Deep Neural Network library*): Kernel ottimizzati a mano, per ogni architettura GPU, per convoluzioni, normalizzazioni, funzioni di attivazione. Ogni framework di deep learning al mondo si affida ad esso.
- **cuBLAS**: Algebra lineare (moltiplicazioni di matrici) alla massima performance ottenibile dal silicio.
- **NCCL** (*NVIDIA Collective Communications Library*): Primitive di comunicazione multi-GPU (all-reduce, all-gather, broadcast) ottimizzate per NVLink e InfiniBand. Senza NCCL, l'addestramento distribuito dovrebbe essere scritto a mano.
- **TensorRT** e **TensorRT-LLM**: Compilatori di inferenza che prendono un modello addestrato e lo trasformano in un motore ottimizzato per una specifica GPU (fusione di kernel, quantizzazione, selezione automatica dei migliori algoritmi). Il guadagno rispetto all'esecuzione ingenua è spesso 2-5×.
- **Triton Inference Server**, **NeMo**, container **NGC**: L'intera pila di produzione.

### 7.2 Cosa significa in pratica quando si sceglie l'hardware

Significa che quando si confronta una GPU Nvidia con un'alternativa (AMD Instinct MI300X/MI355X, Intel Gaudi, Google TPU, acceleratori di startup) **non si stanno confrontando due chip. Si stanno confrontando due ecosistemi.** E il confronto deve essere fatto onestamente su tre livelli:

**Livello 1 — Funziona?** Su Nvidia: `pip install torch`, il codice gira, fatto. Sulle alternative: dipende dalla maturità dello stack (ROCm di AMD è migliorato enormemente ma rimane più fragile), dalla versione, dal modello.

**Livello 2 — Funziona velocemente?** L'ottimizzazione di basso livello (kernel fusi, attenzione ottimizzata come FlashAttention, quantizzazione) esce prima e meglio su CUDA. Un nuovo modello su Nvidia gira ottimizzato dal primo giorno; sulle alternative, spesso mesi dopo, o mai.

**Livello 3 — Funziona quando si rompe?** Quando un addestramento si blocca alle tre del mattino, la probabilità che qualcuno abbia già visto quell'errore e lo abbia risolto su Stack Overflow, GitHub o un forum è enormemente più alta su CUDA. **Questo è il vero vantaggio competitivo**, ed è fatto di persone, non di silicio.

**La conseguenza pratica per l'acquisto:** il costo totale di proprietà di un'alternativa più economica include il tempo dei vostri ingegneri. Se un ingegnere costa 400€/giorno e l'alternativa vi fa perdere venti giorni di integrazione, avete bruciato 8.000€ — spesso più del risparmio. Le alternative hanno senso in due condizioni: **su larga scala** (dove il risparmio unitario si moltiplica per migliaia di unità e ci si può permettere un team dedicato) o **per un carico di lavoro ristretto e stabile** (un singolo modello, in inferenza, che si valida una volta e non si tocca più). Nel mezzo, Nvidia vince per ragioni che non hanno nulla a che fare con i TFLOPS.

---

## 8. Come valutare una GPU per l'AI: criteri espliciti

### 8.1 Criterio #1: VRAM

**Sempre, in ogni caso, prima di ogni altra cosa.** Una GPU super-veloce con poca memoria è inutile; una GPU lenta con abbastanza memoria porta comunque a termine il lavoro.

Il calcolo deve essere fatto sommando **tre voci**:

**(a) Pesi del modello.** Numero di parametri × byte per parametro:
- FP32 (32 bit) = 4 byte → un modello da 7B occupa 28 GB
- FP16/BF16 (16 bit) = 2 byte → 14 GB
- FP8 (8 bit) = 1 byte → 7 GB
- INT4/FP4 (4 bit) = 0.5 byte → 3.5 GB

**(b) Stato dell'ottimizzatore, se si sta addestrando.** Con Adam (l'ottimizzatore standard), sono necessari **due stati aggiuntivi per parametro** (momentum e varianza) più i gradienti. Regola pratica: **l'addestramento in mixed precision costa circa 16–20 byte per parametro**, che è **8–10 volte** il costo della sola inferenza in FP16. Un modello da 7B che occupa 14 GB per l'inferenza richiede **oltre 100 GB** per il fine-tuning completo. Questo è il motivo per cui esiste LoRA.

**(c) Attivazioni e KV-cache.** Le attivazioni sono i risultati intermedi di ogni strato e crescono **linearmente con la dimensione del batch**. La **KV-cache** (*Key-Value cache*) è la memoria che, durante la generazione di testo, memorizza le chiavi e i valori di attenzione già calcolati per evitare di ricalcolarli per ogni token: cresce **linearmente con la lunghezza del contesto e il numero di richieste concorrenti**. In un servizio di inferenza con contesti lunghi e molti utenti, **la KV-cache può superare le dimensioni del modello stesso**. Questo è l'elemento che tutti dimenticano di calcolare, ed è ciò che fa esplodere i server di produzione.

### 8.2 Criterio #2: Larghezza di banda della memoria

Come ripetutamente affermato: **l'inferenza autoregressiva di LLM è memory-bound**. Per generare **ogni singolo token**, la GPU deve leggere **tutti i pesi del modello** dalla VRAM. Il limite superiore teorico di token al secondo è quindi, in prima approssimazione:

> **token/s ≈ larghezza di banda della memoria (GB/s) ÷ dimensione del modello in memoria (GB)**

Esempio: un modello da 14 GB su una scheda con 1.800 GB/s produce un limite massimo di circa 128 token/s (in pratica, si raggiunge il 60–80% di questo valore). La stessa scheda con il doppio della larghezza di banda produrrebbe il doppio dei token. **I TFLOPS non compaiono in questa formula.** Questo è il motivo per cui H200 batte H100 pur avendo lo stesso chip.

Nota: la formula si applica alla **generazione** (la fase di *decode*). La fase di **prefill** (elaborazione del prompt di input, che è parallelizzabile su tutti i token insieme) è invece **compute-bound**: lì, i TFLOPS contano molto. Un carico di lavoro con prompt molto lunghi e risposte brevi ha un profilo completamente diverso da un chatbot conversazionale.

### 8.3 Criterio #3: Precisione numerica

Questo deve essere ben compreso, perché i produttori dichiarano i TFLOPS nella precisione che li fa apparire al meglio.

| Formato | Bit | Struttura | Uso tipico |
|---|---|---|---|
| **FP64** | 64 | 1 segno, 11 esponente, 52 mantissa | Simulazione scientifica. **Irrilevante per l'IA** — ma le GPU per datacenter ce l'hanno e le GPU consumer no |
| **FP32** | 32 | 1+8+23 | Precisione "completa" storica per il deep learning |
| **TF32** | 19 usati (in contenitore a 32 bit) | 1+8+10 | Formato Nvidia: range FP32, mantissa FP16. Accelerazione trasparente |
| **FP16** | 16 | 1+5+10 | Classica mixed precision. Range dinamico ristretto → rischio di overflow/underflow, richiede loss scaling |
| **BF16** | 16 | 1+**8**+7 | *Brain Float*: stesso range di FP32, meno mantissa. **Più robusto di FP16 nell'addestramento**, ora lo standard de facto |
| **FP8** | 8 | E4M3 o E5M2 | Inferenza e (con cautela) addestramento su Hopper/Blackwell. Raddoppia il throughput e dimezza la memoria |
| **FP4 / NVFP4** | 4 | block scaling | Solo inferenza, su Blackwell e successivi. Raddoppia di nuovo. Richiede una quantizzazione accurata |

**La regola d'oro quando si leggono le schede tecniche:** un numero TFLOPS senza precisione specificata **non significa nulla**. E se leggete "con sparsity", sappiate che questo numero è il **doppio** del numero reale in condizioni normali: presuppone una struttura di sparsity 2:4 nei pesi che la maggior parte dei modelli reali non ha. Nvidia lo dichiara sempre, in una piccola nota. **Dividete per due.**

### 8.4 Criterio #4: Interconnessione

Se il carico di lavoro è **multi-GPU su un singolo modello**, l'interconnessione non è un dettaglio: è la differenza tra funzionare e non funzionare. Rivedere la sezione 4. Se il carico di lavoro è **multi-GPU con lavori indipendenti**, l'interconnessione è irrilevante e potete ignorarla completamente.

### 8.5 Criterio #5: TDP, Potenza e Raffreddamento

Il **TDP** è la potenza di progetto termico. Deve essere moltiplicato per il numero di schede e aggiunto al resto del sistema; poi deve essere aggiunto un margine del 20-30% per i picchi transitori (le GPU moderne hanno picchi istantanei molto più alti del TDP nominale, e gli alimentatori sottodimensionati si spengono bruscamente). Poi deve essere risolto il problema di **dove va a finire quel calore**: una workstation con due 5090 sotto carico riscalda una stanza in un modo che nessuno che non l'abbia sperimentato può immaginare. E infine, deve essere calcolato il **costo dell'energia**: 2 kW × 8 ore/giorno × 250 giorni × €0,30/kWh ≈ **€1.200/anno** solo per l'elettricità, senza contare l'aria condizionata.

### 8.6 Criterio #6: Costo per GB di VRAM

Una metrica grezza ma utile per un confronto iniziale. **[TUTTI I DATI VOLATILI]**

| Scheda | VRAM | Prezzo Indicativo | €/GB |
|---|---|---|---|
| RTX 5090 | 32 GB | ~€2.500 | ~€78/GB |
| RTX PRO 6000 Blackwell | 96 GB | ~€9.000 | ~€94/GB |
| DGX Spark | 128 GB | ~€4.000 | ~€31/GB (ma 6× meno banda!) |
| H100 PCIe | 80 GB | ~€25.000 | ~€310/GB |
| H200 SXM | 141 GB | ~€35.000 | ~€250/GB |

Questa tabella dice due cose. Primo: **la DGX Spark è imbattibile per euro/GB, e terribile per euro/(GB/s)** — a conferma che è una macchina di sviluppo, non di produzione. Secondo: **le schede per datacenter costano 3-4 volte di più per GB rispetto alle schede professionali**, e si paga per ECC, HBM, NVLink, licenze e supporto. Se non si usa nessuna di queste quattro cose, **si paga per niente**.

### 8.7 Criterio #7: Comprare o Noleggiare?

Questa domanda dovrebbe essere posta prima di ogni altra, e la risposta dipende da un solo numero: **il fattore di utilizzo**.

Una GPU cloud costa (ordine di grandezza, **[VOLATILE]**) $2–4/ora per una H100. In un anno di uso continuo, sono circa $25.000 — che è **il prezzo di acquisto della scheda**. Quindi:

- **Utilizzo inferiore al 30-40%:** Noleggio. Punto. Comprare hardware che sta inattivo è bruciare capitale.
- **Utilizzo superiore al 60-70%, per almeno 18-24 mesi:** Comprare è conveniente, a condizione di poter gestire l'infrastruttura (e il costo include spazio, elettricità, raffreddamento, rete, manutenzione e il tempo di un amministratore di sistema).
- **Picchi imprevedibili:** Ibrido — hardware locale per la base, cloud per i picchi.
- **Dati sensibili / vincoli normativi (sanità, difesa, GDPR rigoroso):** L'hardware locale può essere obbligatorio indipendentemente dalla convenienza economica.

Un errore molto comune nelle piccole imprese: comprare una workstation da €20.000 per un progetto che dura due settimane al mese. Sarebbe costato €800 nel cloud.

### 8.8 Tabella decisionale: carico di lavoro → GPU consigliata

| Carico di lavoro | VRAM minima | Raccomandazione | Alternativa economica |
|---|---|---|---|
| Apprendimento, corsi, tutorial | 8–12 GB | RTX 4060 Ti 16GB / RTX 5070 | Google Colab gratuito |
| Stable Diffusion / generazione di immagini | 12–24 GB | RTX 4090 / 5090 | RTX 4070 Ti Super 16GB |
| Inferenza LLM 7–14B quantizzata | 8–16 GB | RTX 4090 / 5090 | Mac con Apple Silicon (memoria unificata) |
| Inferenza LLM 30–70B quantizzata | 40–80 GB | **RTX PRO 6000 Blackwell (96GB)** | 2× RTX 5090 con parallelismo di pipeline; DGX Spark (lento) |
| Fine-tuning LoRA/QLoRA su 7–13B | 24–32 GB | RTX 5090 | RTX 4090 usata |
| Fine-tuning LoRA su 70B | 80+ GB | RTX PRO 6000 / H100 | Cloud a ore |
| Fine-tuning completo 7B | 100+ GB | H100 / H200 (o multi-GPU) | Cloud, sempre |
| Inferenza in produzione, throughput elevato | dipende | **H200** (la larghezza di banda è tutto) | H100 se il budget è limitato |
| Addestramento di un modello grande da zero | migliaia di GB | HGX B200 / GB200 NVL72 | Cloud. Sempre e solo cloud, a meno che non siate un hyperscaler |
| Sviluppo e prototipazione su modelli enormi | 128+ GB | **DGX Spark** | Cloud a ore |
| Visione artificiale, YOLO, ASR, embedding | 8–24 GB | RTX 4090 / 5090 | Qualsiasi RTX recente 12–16 GB |
| Servizio di inferenza commerciale per terzi | dipende | Qualsiasi **datacenter o RTX PRO** | Nessuna: le GeForce sono escluse dall'EULA |

---

## 9. Errori tipici dell'acquirente

Li elenchiamo qui perché sono i più costosi e si ripresentano con impressionante regolarità.

**1. Acquistare due GPU pensando di poter sommare la VRAM.** Non succede. Senza NVLink (e le schede consumer non ce l'hanno), due schede da 32 GB non fanno 64 GB per un singolo modello.

**2. Guardare i TFLOPS invece della VRAM e della larghezza di banda.** I TFLOPS sono il numero che il marketing vuole che tu guardi. Per l'inferenza LLM, sono quasi irrilevanti.

**3. Non rendersi conto che il numero dichiarato è "con sparsità".** Dimezzalo.

**4. Confondere H100 PCIe e H100 SXM.** Stesso nome, prestazioni molto diverse.

**5. Dimenticare la KV-cache nel dimensionamento.** Il modello si adatta, il servizio va in produzione, e al decimo utente concorrente, il server esplode.

**6. Acquistare hardware per un utilizzo del 15%.** Il cloud esisteva.

**7. Ignorare l'EULA sul datacenter.** Costruire un prodotto commerciale su GeForce è un rischio legale che nessun investitore serio accetterà durante la due diligence.

**8. Sottovalutare potenza e calore.** Il progetto di workstation a 4 GPU quasi sempre muore al contatore elettrico, non sul budget.

**9. Acquistare il DGX Spark aspettandosi velocità.** È una macchina di sviluppo. La larghezza di banda è di 270 GB/s. Rileggi la sezione 5.2.

**10. Non considerare l'architettura ARM.** Se acquisti un sistema Grace/Vera, verifica che il tuo stack esista per aarch64 **prima** di firmare.

**11. Acquistare "l'ultima generazione" per principio.** Se il tuo carico di lavoro è l'inferenza su modelli medi, una A100 usata o una H100 di seconda mano ti daranno un rapporto prezzo/prestazioni che nessuna nuova scheda può avvicinare.

---

## Riepilogo Operativo — Lista di Controllo per la Scelta di una GPU AI

Segui l'ordine. Non saltare i passaggi: ognuno può chiudere la decisione da solo.

**Passaggio 1 — Definisci il carico di lavoro, per iscritto.**
- [ ] Solo addestramento, fine-tuning o inferenza?
- [ ] Quale modello, quanti parametri, a quale precisione?
- [ ] Quale lunghezza del contesto e quante richieste concorrenti al picco?
- [ ] È un progetto una tantum o un servizio continuo?

**Fase 2 — Calcolare la VRAM necessaria.**
- [ ] Pesi = parametri × byte/parametro (4 / 2 / 1 / 0.5)
- [ ] Se in addestramento: × 8–10 per ottimizzatore e gradienti (o usare LoRA/QLoRA)
- [ ] Aggiungere attivazioni (∝ dimensione del batch) e KV-cache (∝ contesto × concorrenza)
- [ ] Aggiungere un **margine del 20%**
- [ ] → Questo numero è il **filtro eliminatorio**. Tutto ciò che è inferiore è escluso.

**Fase 3 — Decidere se acquistare o noleggiare.**
- [ ] Utilizzo previsto inferiore al 40% → **cloud**, fine della discussione
- [ ] Vincoli di privacy/regolamentari che proibiscono il cloud → **on-premises**, a qualsiasi costo
- [ ] Utilizzo superiore al 60% per più di 18 mesi → valutare l'acquisto, includendo elettricità, spazio, raffreddamento e ore-uomo nel TCO

**Fase 4 — Verificare se è necessaria una configurazione multi-GPU su un singolo modello.**
- [ ] Se **sì** (parallelismo tensoriale, addestramento distribuito) → è necessario **NVLink** → si è nel mondo SXM/datacenter. Le schede consumer sono escluse.
- [ ] Se **no** (lavori indipendenti, un modello per GPU) → si può rimanere su PCIe e schede consumer/pro, risparmiando un ordine di grandezza.

**Fase 5 — Verificare il vincolo di licenza.**
- [ ] Il calcolo sarà venduto o noleggiato a terzi? → **GeForce esclusa**. È necessaria una RTX PRO o datacenter.
- [ ] Uso interno o ricerca? → GeForce consentita.

**Fase 6 — Verificare la larghezza di banda, non solo la capacità.**
- [ ] Il carico di lavoro è inferenza generativa? → la **larghezza di banda della memoria** è il predittore delle prestazioni. Stima: token/s ≈ larghezza di banda ÷ dimensione del modello.
- [ ] Il carico di lavoro è prefill lungo o addestramento? → allora i **TFLOPS** (alla giusta precisione!) contano davvero.

**Fase 7 — Verificare la compatibilità del sistema.**
- [ ] Alimentatore: TDP totale + 30% di margine
- [ ] Sufficienti linee PCIe CPU/chipset per il numero di schede (x16 o almeno x8 per scheda)
- [ ] Spazio fisico nel case e flusso d'aria effettivo
- [ ] Linea elettrica: sopra i 2 kW continui, in Italia è necessaria una seria considerazione del contatore
- [ ] Architettura della CPU: se Grace/Vera, il tuo software esiste per **aarch64**?

**Fase 8 — Scegliere per fascia di prezzo.**
- [ ] **Entry (< €3.000):** RTX 5070 Ti / 5080, o RTX 4090 usata. Per apprendimento, visione artificiale, LLM piccoli. In alternativa: **nessun hardware e €500 di cloud.**
- [ ] **Mid (€3.000–12.000):** RTX 5090 (una o due), o DGX Spark se il vincolo è la capacità e non la velocità. Copre il 90% delle reali esigenze di un professionista o di una PMI.
- [ ] **High-end (€15.000–30.000):** RTX PRO 6000 Blackwell (una o due, per 96/192 GB), o una H100/H200 PCIe se sono necessarie licenza datacenter e larghezza di banda HBM. Questa è la fascia in cui una PMI può fare seria AI in-house.
- [ ] **Datacenter (€100.000 +):** nodo HGX H200/B200. A questo punto, si ha un team dedicato e un ufficio acquisti, e questo capitolo serve solo a porre le domande giuste al proprio fornitore.

**Fase 9 — Prima di firmare.**
- [ ] Hai controllato i prezzi **oggi**, e non su questa pagina?
- [ ] Hai controllato i tempi di consegna effettivi (non quelli dichiarati)?
- [ ] Hai testato il tuo carico di lavoro esatto su quella GPU **nel cloud, per un'ora**, prima di acquistarla? Questo è l'unico consiglio con il miglior rapporto valore/costo in tutto il capitolo: **€20 di cloud possono farti risparmiare €20.000 di errore.**

---

*Nota finale sulla volatilità dei dati: architetture, prezzi e disponibilità in questo settore cambiano su base trimestrale. Le architetture, i principi di dimensionamento e i criteri di selezione descritti in questo capitolo rimangono validi; i numeri specifici devono essere riverificati alla data della decisione.*

---

[← Precedente](10-cpu-workstation-hedt.html) · [Tutti i capitoli](./) · [Successivo →](12-amd-instinct-vs-nvidia.html)
