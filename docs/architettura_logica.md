# 📖 Architettura Logica Completa del Simulatore

## 🎯 Obiettivo della Demo

Trasformare un titolare di studio dentistico da una visione **"il medico costa il 35%"** a una visione **"il medico costa il 35% NETTO di 272€, che sono 95€, quindi il mio margine è solo 20€"**.

---

## 📊 Il Flusso dei Dati

```
┌─────────────────────────────────────────────────────────────┐
│                    INPUT LAYER (Tabelle Base)               │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  [Listino Studio]           [Materiali + BOM]               │
│   ├─ ID_Prestazione         ├─ ID_Materiale                │
│   ├─ Nome                   ├─ Costo_Unitario              │
│   ├─ Prezzo_Listino         ├─ Quantità per prestazione    │
│   ├─ Costo_Lab              │                               │
│   └─ Tempo_Standard          │                               │
│                              │                               │
│  [Medici]                   [Parametri Studio]             │
│   ├─ ID_Medico               ├─ Costo_Orario_Poltrona      │
│   ├─ Nome                     ├─ Sconto_Medio_Paziente      │
│   ├─ Tipo_Accordo (% o €)    ├─ Commissione_Finanziamento  │
│   └─ Valore                   └─ Saturazione_Poltrona       │
│                                                               │
└─────────────────────────────────────────────────────────────┘
                                ↓
                    ┌───────────────────────┐
                    │  POWER QUERY ENGINE   │
                    │  (Incrocia i dati)    │
                    └───────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────┐
│           CALCOLO LAYER (Formule Matematiche)               │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  Per ogni (Prestazione, Medico):                             │
│                                                               │
│  1. Prezzo_Reale = Prezzo_Listino × (1 - Sconto%)          │
│  2. Costo_Materiali = Σ (Quantità × Costo_Unitario)        │
│  3. Costo_Poltrona = (Tempo_Standard / 60) × Costo/ora      │
│  4. Imponibile = Prezzo_Listino - Costo_Lab                 │
│  5. Compenso_Medico = Imponibile × % (oppure € fisso)       │
│  6. Costo_Totale = Lab + Materiali + Poltrona + Medico      │
│  7. MARGINE_NETTO = Prezzo_Reale - Costo_Totale            │
│  8. MARGINE % = (Margine / Prezzo_Reale) × 100              │
│                                                               │
└─────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────┐
│         OUTPUT LAYER (Tabella Master Margini)               │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  Righe: 12 Prestazioni × 3 Medici = 36 combinazioni        │
│  Colonne: ID | Nome | Prezzo | Costi | Margine € | Margine %│
│                                                               │
│  Questa tabella alimenta il Simulatore Interattivo          │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔢 Esempio di Calcolo Passo-Passo

### **Scenario: Endodonzia Molare (ID=2) eseguita da Dr. Rossi (ID=1)**

#### INPUT
```
Prestazione:     Endodonzia Molare
Prezzo_Listino:  350 €
Costo_Lab:       50 €
Tempo_Standard:  90 minuti

Materiali BOM:
  - Guttaperca (x5)              = 5 × 6€   = 30 €
  - Cemento Endodontico (x1)     = 1 × 15€  = 15 €
  - Lima Endodontica (x4)        = 4 × 4€   = 16 €
  - Diga di Gomma (x1)           = 1 × 2€   = 2 €
  - Disinfettante (x10 ml)       = 10 × 0.5€ = 5 €
  TOTALE MATERIALI = 68 €

Medico:         Dr. Rossi
Tipo_Accordo:   Percentuale
Valore:         35%

Parametri:
  Costo_Orario_Poltrona = 28 €/ora
  Sconto_Paziente = 8%
```

#### CALCOLI

```
Step 1: Prezzo Reale Incassato
  Prezzo_Reale = 350 × (1 - 0.08) = 350 × 0.92 = 322 €

Step 2: Imponibile Compenso Medico
  Imponibile = 350 - 50 = 300 €

Step 3: Compenso Medico
  Compenso = 300 × 0.35 = 105 €

Step 4: Costo Poltrona
  Costo_Poltrona = (90 / 60) × 28 = 1.5 × 28 = 42 €

Step 5: Costo Totale Studio
  Costo_Totale = 50 + 68 + 42 + 105 = 265 €

Step 6: Margine Netto (€)
  Margine = 322 - 265 = 57 €

Step 7: Margine Netto (%)
  Margine % = (57 / 322) × 100 = 17.7%
```

#### OUTPUT
```
┌────────────────────────────────────────┐
│ Endodonzia Molare - Dr. Rossi (35%)    │
├────────────────────────────────────────┤
│ Prezzo Listino:        350 €           │
│ Sconto Paziente:       -28 € (-8%)     │
│ Prezzo Reale:          322 €           │
├────────────────────────────────────────┤
│ COSTI:                                 │
│   Laboratorio:         50 €            │
│   Materiali:           68 €            │
│   Poltrona (90 min):   42 €            │
│   Compenso Medico:     105 €           │
│ ─────────────────────────────────────  │
│   COSTO TOTALE:        265 €           │
├────────────────────────────────────────┤
│ MARGINE NETTO:         57 € (17.7%) 🟡 │
│                                        │
│ Interpretazione: Lo studio guadagna    │
│ 57€ netti. Se il medico fosse più      │
│ veloce (60 min), il margine salirebbe  │
│ a 70€. Se facesse il 42%, calerebbe    │
│ a 40€.                                 │
└────────────────────────────────────────┘
```

---

## 🎮 Le 3 Leve del Simulatore (Come Impattano il Margine)

### **Leva 1: Tempo di Esecuzione (Minuti)**

**Cosa fa**: Modifica il **Costo_Poltrona**

```
Formula: Costo_Poltrona = (Tempo / 60) × 28

Esempio:
  Tempo = 60 min  → Costo = 28 €  → Margine = 70 €
  Tempo = 90 min  → Costo = 42 €  → Margine = 57 €
  Tempo = 120 min → Costo = 56 €  → Margine = 43 €

Impatto relativo: ALTO ⭐⭐⭐
Perché: La poltrona è un costo fisso/ora. Se il medico è lento,
        questo costo erode il margine proporzionalmente.
```

---

### **Leva 2: Sconto Paziente / Commissione Incasso (%)**

**Cosa fa**: Riduce il **Prezzo_Reale**

```
Formula: Prezzo_Reale = Prezzo_Listino × (1 - Sconto%)

Esempio (sempre Endodonzia 350€):
  Sconto =  0% → Prezzo_Reale = 350 €  → Margine = 85 €
  Sconto =  8% → Prezzo_Reale = 322 €  → Margine = 57 €
  Sconto = 15% → Prezzo_Reale = 297.50€ → Margine = 32,50 €

Impatto relativo: MEDIO ⭐⭐
Perché: Riduce il "top line" ma non tutti i costi calano
        (es. il compenso medico è calcolato sull'imponibile,
        non sul prezzo reale). Per questo è CRITICO negoziare
        il compenso medico se fai sconti.
```

---

### **Leva 3: Saturazione Poltrona (Ore/Settimana)**

**Cosa fa**: Modifica come il **Costo_Orario_Poltrona** si distribuisce

```
Logica: Il costo della poltrona (28€/ora) è FISSO, indipendentemente
        da quante ore la usi. Se la usi solo 10 ore/settimana,
        ogni paziente deve coprire una fetta più grande di quel costo.

Formula Alternativa (se vuoi amortizzare il costo):
  Costo_Orario_Reale = Costo_Poltrona_Settimanale / Ore_Reali

Esempio:
  Costo_Settimanale_Fisso = 28€/ora × 40 ore = 1.120€/settimana
  
  Se il medico lavora 40 ore/settimana:
    Costo_Orario_Effettivo = 1.120 / 40 = 28€/ora  (già incluso)
  
  Se il medico lavora solo 20 ore/settimana:
    Costo_Orario_Effettivo = 1.120 / 20 = 56€/ora  (RADDOPPIA!)
    → Ogni prestazione diventa più cara per lo studio
    → Margine crolla

Impatto relativo: BASSO ⭐
Perché: È un fattore di lungo termine. Non cambia la singola
        prestazione, ma influenza il "break-even" mensile/annuale.
        Nel simulatore la inseriamo come scenario planning.
```

---

## 📈 Matrice di Sensibilità (Tornado Chart)

Questo grafico mostra l'impatto relativo di ogni leva sul margine:

```
Leva 1: Tempo Esecuzione       ████████████████████
        ± 20 min = ± 18€ margine

Leva 2: Sconto Paziente        ██████████████
        ± 5% = ± 12€ margine

Leva 3: Saturazione Poltrona   ████████
        ± 10 ore/sett = ± 8€ margine

^ Impatto sul Margine Netto (€)
```

**Interpretazione per il Titolare**:
- Se vuoi massimizzare il margine, **fai più forte leva sul Tempo** (assume medici veloci)
- Proteggiti dagli **Sconti Paziente** (non farli senza ridurre il compenso medico)
- La **Saturazione** conta solo se il medico è assunto a tempo pieno

---

## 🔄 Modalità di Utilizzo del Simulatore

### **Modalità 1: DIRECT (Scenario Corrente)**

```
INPUT:
  Prestazione:      Endodonzia Molare
  Medico:           Dr. Rossi
  Sconto Paziente:  8%
  Tempo Medio:      90 min
  
→ PRESS CALCOLA

OUTPUT:
  Margine Netto: 57€ (17.7%) 🟡
  
INSIGHT: Il margine è borderline. Se il medico
         è lento, potrebbe andare in perdita.
```

### **Modalità 2: REVERSE (Target Margine)**

```
INPUT:
  Margine Target:   25% (es. 88€ su 350€)
  Prestazione:      Endodonzia Molare
  Tempo Medio:      90 min
  
→ PRESS CALCOLA

OUTPUT:
  Compenso Massimo Medico: 28%
  
  Oppure (se è fisso):
  Compenso Massimo Medico: 84€ a seduta
  
INSIGHT: Il titolare sa che non può offrire
         più del 28% a questo medico per mantenere
         il margine del 25%.
```

---

## 📋 Checklist di Configurazione

Prima di portare la demo dal titolare, assicurati che:

- [ ] Listino prezzi è aggiornato (puoi differenziare per materiale?)
- [ ] Materiali: Hai inserito TUTTI i costi variabili (anche i monouso piccoli)
- [ ] Medici: Hai i contratti reali (% o fisso?)
- [ ] Costo Poltrona: Include tutto (affitto, utenze, ammortamento, ASO)?
- [ ] Sconto Paziente: È realistico? (0% = nessuno sconto, 15% = massimo)
- [ ] Test: Hai provato il simulatore con 2-3 prestazioni reali?

---

## 🚀 Prossimi Passi Tecnici

1. **Importa i CSV nel tuo dataset reale**
   - Sostituisci le prestazioni demo con le tue 20-30 prestazioni più comuni
   - Aggiungi i TUOI materiali con i TUOI costi (non gli approssimativi)
   - Aggiungi i tuoi 3-5 medici con i loro accordi

2. **Testa Power Query**
   - Verifica che il join tra Listino e BOM funzioni
   - Controlla che i calcoli siano corretti (ricalcola 2-3 prestazioni a mano)

3. **Crea il Simulatore Interattivo**
   - Foglio separato per l'input
   - Menu a tendina per prestazioni + medici
   - 3 slider per le leve
   - Tabella output con colori
   - Grafico Tornado

4. **Presentazione al Titolare**
   - Simulazione 1: "Caso attuale" (che margine faccio davvero oggi?)
   - Simulazione 2: "Nuovo medico" (posso permettermi questo contratto?)
   - Simulazione 3: "Scenario stress" (cosa succede se faccio sconti?)

---

## 📞 Troubleshooting

**P: Power Query non trova i file CSV**
R: Controlla il path (usa il percorso completo, es. `C:\Users\Diego\Desktop\01_dataset_base.csv`)

**P: I numeri non tornano**
R: Ricalcola manualmente uno scenario e controlla dove il calcolo diverge dalla formula

**P: Il Tornado non mostra nulla**
R: Assicurati che il grafico sia configurato su data sensitivity (Excel chiamalo "Scenario Analysis" o simile)

**P: Il titolare non capisce il margine**
R: Mostragli questo calcolo: "Su una prestazione da 350€, lui incassa 350€, ma gli costa 265€. Quindi guadagna 85€. Se gli faccio il 10% di sconto, incassa 315€ e il guadagno crolla a 50€."

---

**Fine Architettura. Pronto per il step 3: il Simulatore Interattivo!** 🚀
