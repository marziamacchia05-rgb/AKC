# Analisi delle razze canine AKC

Analisi esplorativa e clustering delle razze canine riconosciute dall'**American Kennel Club (AKC)**, con tre casi studio che confrontano cani reali con lo standard della loro razza.

Il notebook unisce due fonti pubbliche: i punteggi caratteriali delle razze e i loro dati fisici (peso, altezza, gruppo AKC). Su questi dati studia la distribuzione delle razze, le raggruppa in archetipi con K-Means e mette a confronto alcuni cani reali con il profilo ufficiale della razza.

## Contenuto

| File | Descrizione |
|---|---|
| `AKC_razzecanine.ipynb` | Notebook con tutta l'analisi |
| `grafici/` | Grafici esportati dal notebook (PNG, 300 dpi), creata all'esecuzione |
| `akc_breeds.xlsx` | Dataset unito e pulito, creato all'esecuzione |

## Dati

| Dataset | Contenuto | Fonte |
|---|---|---|
| Breed traits | 16 tratti per razza (14 numerici su scala 1–5, più tipo e lunghezza del pelo) | [TidyTuesday 2022-02-01](https://github.com/rfordatascience/tidytuesday/tree/master/data/2022/2022-02-01) |
| AKC data | Peso e altezza minimi e massimi, gruppo AKC | [tmfilho/akcdata](https://github.com/tmfilho/akcdata) |

Il notebook scarica i dati direttamente da GitHub, quindi non serve scaricare nulla a mano.

**Pulizia applicata:**
- I due dataset scrivono i nomi delle razze in modo diverso, ad esempio `Retrievers (Labrador)` contro `Labrador Retriever`. Prima dell'unione i nomi vengono normalizzati: spazi speciali, accenti, plurali e ordine delle parole. Per i plurali irregolari c'è una tabella di alias (`Pulik` → `Puli`, `Komondorok` → `Komondor`, …). Il risultato è 194 razze collegate su 194.
- Un peso pari a 0 kg viene trattato come dato mancante (succede con il Cane Corso).
- *Plott Hounds* è esclusa dalle analisi perché ha tutti i tratti a 0, fuori dalla scala 1–5.

## Struttura del notebook

0. **Setup**: librerie, palette e funzioni di stile comuni a tutti i grafici.
1. **Caricamento e unione dei dati**: dati caratteriali e fisici uniti in un solo dataset.
2. **Esplorazione**: distribuzione del peso medio e numero di razze per gruppo AKC.
3. **Clustering K-Means** sui 14 tratti caratteriali standardizzati (z-score):
   - scelta di *k* con il metodo del gomito e il punteggio silhouette, da k = 2 a k = 8;
   - **k = 5 archetipi**, descritti con una heatmap del profilo medio di ogni cluster.
4. **Caso studio 1: Beagle reale vs standard AKC.** Un grafico a manubrio (dumbbell) confronta un Beagle di 8 anni con lo standard di razza su stimolazione mentale, energia, abbaio e addestrabilità.
5. **Caso studio 2: Spacky e la mappa sociale.** UMAP proietta in 2D le razze secondo 5 tratti sociali, e Spacky viene collocato sulla mappa senza riaddestrare il modello. Il notebook calcola anche le razze più vicine a Spacky nello spazio originale e confronta il suo profilo con quello AKC del Miniature Pinscher.
6. **Caso studio 3: Simba vs Greyhound AKC.** Un grafico radar a 5 assi (peso, altezza, lunghezza del pelo, perdita del pelo, sbavamento) confronta Simba, un meticcio di galgo, con il Greyhound.

## Risultati principali

- **Peso:** media **21,8 kg**, mediana **19,3 kg**. La distribuzione è asimmetrica a destra: molte razze piccole e medie, poche giganti.
- **Gruppi AKC:** Sporting (32), Hound, Working e Terrier (31 ciascuno), Herding (30), Non-Sporting (20), Toy (19).
- **Archetipi (k = 5):**

  | Cluster | Razze | Tratti distintivi | Esempi |
  |---|---|---|---|
  | 0 | 37 | Poco adatti ai bambini e agli altri cani, perdono poco pelo | Bassotto, Volpino di Pomerania, Chihuahua |
  | 1 | 50 | Energici, giocosi, facili da addestrare, molto bisognosi di stimoli | Labrador, Pastore tedesco, Barboncino |
  | 2 | 33 | Meno affettuosi con la famiglia, poco adattabili | Pastore australiano, Malinois, Akita |
  | 3 | 48 | Socievoli con bambini e altri cani | Golden Retriever, Beagle, Husky |
  | 4 | 26 | Sbavano molto, protettivi, poca energia | Bulldog, Rottweiler, Alano |

- **Spacky:** il suo profilo sociale è lontano da quello AKC del Miniature Pinscher, che risulta la 138ª razza più vicina su 194.

## Come eseguirlo

**Google Colab:** apri il notebook in Colab ed esegui tutte le celle (*Runtime → Esegui tutte*). La prima cella installa `umap-learn`, che Colab non ha di default.

**In locale** (Python 3.10 o successivo):

```bash
pip install pandas numpy matplotlib seaborn scikit-learn umap-learn openpyxl jupyter
jupyter notebook AKC_razzecanine.ipynb
```

Serve una connessione a Internet, perché i dataset vengono scaricati da GitHub all'avvio.

## Stile dei grafici

Tutti i grafici hanno sfondo bianco e dettagli nel colore `#1800ad`, più due sue tinte chiare per la seconda serie e per la griglia. Nella heatmap i valori sotto la media sono in grigio neutro. Le funzioni `stile_assi()` e `salva()` applicano a ogni grafico gli stessi assi e la stessa esportazione nella cartella `grafici/`.

## Note e limiti

- I punteggi AKC (1–5) descrivono lo **standard di razza**, non il singolo cane. I casi studio servono proprio a mostrare quanto un cane reale possa discostarsene.
- I punteggi dei cani reali (Beagle, Spacky, Simba) sono valutazioni soggettive dei proprietari.
- Nel radar di Simba la normalizzazione 0–1 è calcolata **solo tra i due cani**: per ogni variabile chi ha il valore più alto prende 1 e l'altro 0. Il grafico mostra quindi *chi* è più alto su ogni asse, non *di quanto*.
- Molte razze hanno lo stesso profilo sociale: 83 profili distinti su 194 razze. Per questo, nel caso di Spacky, più razze risultano a distanza 0.
- Per i Barboncini (*Poodles*), che l'AKC divide in tre taglie, vengono usati i dati fisici della taglia Standard.

## Crediti

- Dati caratteriali: [TidyTuesday](https://github.com/rfordatascience/tidytuesday), a partire da dati dell'[American Kennel Club](https://www.akc.org/).
- Dati fisici e gruppi: [tmfilho/akcdata](https://github.com/tmfilho/akcdata).
