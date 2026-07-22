# 🦷 Studio Dentistico - Simulatore di Margini

**Un prototipo funzionante per calcolare margini netti e negoziare compensi medici in tempo reale.**

---

## 📋 Panoramica del Progetto

Questo simulatore è costruito per titolari di studi dentistici che vogliono:

✅ Capire il **vero margine netto** di ogni prestazione (non solo il prezzo di listino)  
✅ **Negoziare contratti** sapendo esattamente quanto può costare un medico  
✅ **Simulare scenari** diversi (es. "Se il medico è lento, quanto perde lo studio?")  
✅ **Identificare leve di profittabilità** (tempo, sconti, saturazione poltrona)

---

## 🏗️ Architettura

### Le 5 Tabelle di Base

```
[Listino Studio]      → Prezzi di vendita per prestazione
[Anagrafica Materiali] → Costi unitari dei materiali
[Distinta Base]       → Quale materiale + tempo per ogni prestazione
[Medici]              → Accordi di compenso (% o cifra fissa)
[Parametri Studio]    → Costi fissi (poltrona/ora, sconto medio, etc.)

                    ↓ POWER QUERY ↓

[TABELLA MASTER MARGINI] → Tutti i calcoli per riga
```

### Formula del Margine Netto (€)

```
Prezzo Reale = Prezzo Listino × (1 - Sconto %)
Costo Lab = [Costo Laboratorio da Listino] oppure calcolato da BOM
Costo Materiali = Somma(Quantità × Costo Unitario)
Costo Poltrona = (Costo Orario / 60) × Minuti Prestazione
Compenso Medico = Imponibile × % Medico (oppure Importo Fisso)

MARGINE NETTO (€) = Prezzo Reale - (Costo Lab + Costo Materiali + Costo Poltrona + Compenso Medico)
MARGINE NETTO (%) = (Margine Netto € / Prezzo Reale) × 100
```

---

## 🎮 Le 3 Leve del Simulatore

| Leva | Cosa Controlla | Impatto | Input |
|------|---|---|---|
| **Leva 1: Tempo Esecuzione** | Velocità del medico (min) | Costo poltrona ↑/↓ | Slider 30-120 min |
| **Leva 2: Sconto/Incasso** | Sconti paziente + commissioni finanziamento (%) | Prezzo reale ↓ | Slider 0-15% |
| **Leva 3: Saturazione Poltrona** | Ore settimanali garantite dal medico | Costo fisso diluito | Slider 10-40 ore/sett |

---

## 📁 File del Progetto

### 1. **`01_dataset_base.xlsx`**
Il punto di partenza. Contiene 5 fogli:
- `Listino Studio` - 12 prestazioni dentistiche comuni
- `Anagrafica Materiali` - 15 materiali con costi
- `Distinta Base` - BOM per ogni prestazione
- `Medici` - 3 medici con accordi diversi
- `Parametri Studio` - Costi fissi

**👉 AZIONE**: Importa questi dati in Power Query, oppure mantienili come lookup se preferisci Excel puro.

### 2. **`02_power_query_queries.txt`**
Le query M di Power Query per:
- Unire Listino Studio + Distinta Base + Materiali
- Calcolare costo totale per prestazione
- Generare la tabella master con margini

**👉 AZIONE**: Copia-incolla queste query in Power Query Editor.

### 3. **`03_simulatore_margini.xlsx`**
**La demo interattiva.** Contiene:
- **Input Panel**: 3 slider/menu a tendina per le leve
- **Output Panel**: Numeri calcolati (Prezzo, Costo, Margine €, Margine %)
- **Grafico Tornado**: Mostra l'impatto di ogni variabile
- **Tabella Colori**: Semaforo rosso/giallo/verde per il margine

---

## 🚀 Come Usarlo

### Fase 1: Setup Dati (5 minuti)
1. Apri `01_dataset_base.xlsx`
2. Sostituisci il listino con i TUOI prezzi
3. Aggiungi i materiali che usi davvero
4. Inserisci i tuoi 2-3 medici e loro accordi

### Fase 2: Configura Power Query (10 minuti)
1. In Excel, vai a **Dati > Nuova Query > Da File**
2. Carica le tabelle di lookup
3. Incolla le query da `02_power_query_queries.txt`

### Fase 3: Usa il Simulatore (Al volo)
1. Apri `03_simulatore_margini.xlsx`
2. Seleziona una prestazione dal menu a tendina
3. Muovi i 3 slider per simulare scenari
4. Guarda il margine aggiornarsi in tempo reale
5. Usa il grafico tornado per capire quale leva impatta di più

---

## 📊 Cosa Vede il Titolare nella Demo

### Scenario 1: "Endodonzia Molare - Medico Giovane"
```
Prezzo Listino:         500 €
Sconto Paziente:        -10%
Prezzo Reale Incassato: 450 €

Costo Laboratorio:      80 €
Costo Materiali:        35 €
Costo Poltrona:         42 € (90 min × 28€/ora)
Compenso Medico:        115,50 € (35% su imponibile)

COSTO TOTALE:           272,50 €
───────────────────────
MARGINE NETTO:          177,50 € ✅ (39,4%)
```

### Scenario 2: "Stesso Medico, Ma Lavora Lento (120 min)"
```
[Solo il costo poltrona cambia: 56 € invece di 42 €]

MARGINE NETTO:          163,50 € 🟡 (36,3%)
```

### Scenario 3: "Medico Più Esperto, Chiede il 40%"
```
Compenso Medico:        148 € (40% su imponibile)

MARGINE NETTO:          145,50 € 🔴 (32,3%)
[Se il titolare vuole mantenere 39%, deve negoziare il tempo]
```

---

## 🎯 Il Valore per il Cliente (Titolare)

| Domanda del Titolare | Come il Simulatore Risponde |
|---|---|
| *"Se il medico fa il 35%, quanto guadagno?"* | ✅ Risposta immediata con tutti i costi |
| *"Posso permettermi il 40% se è veloce?"* | ✅ Simula il tempo minore e mostra il margine |
| *"Se faccio il 15% di sconto per chiudere il preventivo, come me lo recupero?"* | ✅ Mostra che serve ridurre il compenso medico O aumentare la velocità |
| *"Quanto costa realmente una seduta di 20 min vs una di 60 min?"* | ✅ Calcola il costo poltrona per seduta |

---

## 📈 Grafico Tornado - Cosa Mostra

Un grafico che ordina per impatto:

```
Leva 1 (Tempo)        ████████████████████
Leva 2 (Sconto)       ████████████
Leva 3 (Saturazione)  ███████
```

**Interpretazione**: Se il titolare muove il tempo di ±10 minuti, il margine cambia di 42€. Se muove lo sconto di ±5%, il margine cambia di 22€. La saturazione è la meno impattante.

---

## 🔧 Tecnologie Usate

- **Excel + Power Query** (Niente dipendenze software strane)
- **Formule Excel Standard** (VLOOKUP, SUMIF, IF condizionali)
- **Formattazione Condizionale** (Semafori automatici)
- **Grafici Nativi di Excel** (Tornado, Sensitivity)

---

## 📝 Prossimi Passi

- [ ] Importa i tuoi dati reali nel `01_dataset_base.xlsx`
- [ ] Configura Power Query sul tuo PC
- [ ] Testa il simulatore con 2-3 contratti medici reali
- [ ] Se funziona, porta la demo dal titolare
- [ ] Raccogli feedback: quali altre leve servirebbero?

---

## 💡 Estensioni Future

Se funziona, possiamo aggiungere:
- **Analisi Mensile/Annuale**: Stima fatturato basata su volume sedute
- **Break-Even Analysis**: A che margine il medico "costa più che guadagna"?
- **Competitor Pricing**: Confronto con studi vicini
- **Scenario Savepdf**: Salva le configurazioni negoziate con i medici

---

## 📞 Domande?

Se qualcosa non è chiaro, leggi `docs/architettura_logica.md` per diagrammi e spiegazioni più dettagliate.

**Buona simulazione! 🚀**
