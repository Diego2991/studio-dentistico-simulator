# 📖 Guida: Come Importare i Tuoi Dati Reali

## 🎯 Obiettivo
Sostituire i dati demo con i tuoi dati reali (listino prezzi, materiali, medici, costi) in modo che il simulatore calcoli i margini veri del tuo studio.

---

## 📋 STEP 1: Raccogli i Tuoi Dati

Prima di iniziare, prepara questi 5 file (anche in formato Excel o Word, li convertirai in CSV):

### **A) LISTINO_TUO_STUDIO.csv**
Le prestazioni che offri, con prezzi e costi.

```
ID_Prestazione,Nome_Prestazione,Prezzo_Listino,Costo_Lab_Default,Tempo_Standard_Min
1,Igiene Dentale,85,0,30
2,Endodonzia Molare,380,55,90
3,Corona Zirconio,850,220,120
...
```

**Come compilarlo:**
- **ID_Prestazione**: Numero progressivo (1, 2, 3...)
- **Nome_Prestazione**: Nome esatto della prestazione nel tuo listino
- **Prezzo_Listino**: Prezzo che chiedi al paziente (senza sconto)
- **Costo_Lab_Default**: Se hai laboratori esterni, quanto paghi? (Se non ce ne sono, metti 0)
- **Tempo_Standard_Min**: Tempo medio in minuti per quella prestazione (chiedi ai tuoi medici!)

**Dove trovare i dati:**
- Prendi il tuo listino attuale (PDF o Excel)
- Chiedi ai medici "quanto tempo in media per questa prestazione?"
- Se lavori con laboratori esterni, chiedi loro i costi

---

### **B) MATERIALI_TUOI.csv**
Tutti i materiali che usi, con costi unitari.

```
ID_Materiale,Nome_Materiale,Categoria,Costo_Unitario_EUR,Unita_Misura
M001,Composito A2,Restauro,9.50,fiala
M002,Resina Fluida,Restauro,6.00,fiala
M003,Corona Ceramica Lab,Protesi,165,pezzo
...
```

**Come compilarlo:**
- **ID_Materiale**: Codice progressivo (M001, M002...)
- **Nome_Materiale**: Nome del materiale nel tuo fornitore
- **Categoria**: Restauro / Protesi / Endodonzia / Implantologia / Consumibili / etc
- **Costo_Unitario_EUR**: Prezzo che paghi per una singola unità (chiedi al fornitore!)
- **Unita_Misura**: fiala / pezzo / tubo / ml / etc

**Dove trovare i dati:**
- Guarda le fatture dei tuoi fornitori (Zhermack, Ivoclar, etc)
- Calcola il costo unitario (fattura totale / numero unità)
- **Includi ANCHE i monouso piccoli** (diga gomma, disinfettante, etc) - non trascurare nulla!

**⚠️ IMPORTANTE:**
Se un materiale costa meno di 1€, mettilo comunque. Somme anche piccoli costi hanno un impatto sul margine totale.

---

### **C) DISTINTA_BASE_TUA.csv**
Per ogni prestazione, quali materiali usi e quanti.

```
ID_Prestazione,ID_Materiale,Quantita
1,M001,1
1,M002,1
1,M003,0.5
2,M004,5
2,M005,1
...
```

**Come compilarlo:**
- **ID_Prestazione**: Riferito al file A (es. 1 = Igiene)
- **ID_Materiale**: Riferito al file B (es. M001 = Composito A2)
- **Quantita**: Quante unità di quel materiale usi per quella prestazione
  - Se usi mezzo pezzo, scrivi 0.5
  - Se usi un intero tubo, scrivi 1
  - Se usi 5 ml di disinfettante, scrivi 5

**Come compilarlo:**
- Prendi una ricetta di laboratorio per ogni prestazione
- Chiedi ai tuoi medici "per fare una Corona Zirconio, quanti materiali usi?"
- Fai una prova pratica e conta!

---

### **D) MEDICI_TUOI.csv**
Anagrafica e accordi economici con i tuoi medici.

```
ID_Medico,Nome_Medico,Specializzazione,Tipo_Accordo,Valore_Accordo,Descrizione
1,Dr. Rossi,Generico,Percentuale,0.38,Senior, accordo 38% sul compensabile
2,Dr. Bianchi,Implantologia,Percentuale,0.45,Junior specialista, 45%
3,Dr. Verdi,Generico,Fisso,2000,2000€ al mese fisso
...
```

**Come compilarlo:**
- **ID_Medico**: Numero progressivo
- **Nome_Medico**: Nome (o nickname privato)
- **Specializzazione**: La sua specialità (o "Generico" se fa un po' di tutto)
- **Tipo_Accordo**: "Percentuale" o "Fisso"
  - Percentuale: % sul compensabile (es. 35%)
  - Fisso: € al mese o € a seduta
- **Valore_Accordo**: 
  - Se Percentuale: scrivi 0.35 (per il 35%) o 0.42 (per il 42%)
  - Se Fisso: scrivi 2000 (per 2000€) o 180 (per 180€ a seduta)
- **Descrizione**: Breve nota (opzionale)

**⚠️ ATTENZIONE:**
Se il medico ha un accordo complesso (es. fisso + % su alcuni servizi), semplifica:
- Scegli la modalità principale
- Oppure crea 2 righe per lo stesso medico con diverse prestazioni

---

### **E) PARAMETRI_STUDIO_TUOI.csv**
Costi fissi e politiche dello studio.

```
Parametro,Valore,Unita_Misura,Note
Costo_Orario_Poltrona,32,EUR/ora,Affitto + utenze + ammortamento
Sconto_Medio_Paziente,0.10,%,10% di sconto medio
Commissione_Finanziamento,0.05,%,5% commissione su finanziamenti
Saturazione_Poltrona_Ore_Settimana,35,ore,Ore medie di utilizzo/settimana
Margine_Minimo_Target,0.25,%,Target minimo di margine netto
```

**Come compilarlo:**
- **Costo_Orario_Poltrona**: 
  - Somma: affitto poltrona + utenze (luce, gas) + ammortamento attrezzature + ASO
  - Dividi per ore annuali di utilizzo
  - Se studi a 30€/ora, metti 30
- **Sconto_Medio_Paziente**: 
  - Quanta % di sconto dai in media? (0% se nessuno, 10% se dai 10%, etc)
- **Commissione_Finanziamento**: 
  - Se i pazienti pagano con finanziamento, quanto paghi di commissione?
- **Saturazione_Poltrona_Ore_Settimana**: 
  - Quante ore in media lavora il medico per settimana?
- **Margine_Minimo_Target**: 
  - Quale margine netto % consideri accettabile? (es. 25%)

---

## 🔧 STEP 2: Converti i Dati in CSV

Se i tuoi dati sono in Excel:

1. Apri il file Excel
2. Seleziona il foglio che vuoi salvare
3. **File > Salva con nome > Formato: CSV (Delimitato da virgola)**
4. Salva con nome il file (es. `LISTINO_TUO_STUDIO.csv`)

**Controlla il file CSV:**
- Apri il CSV con Blocco note
- Verifica che le righe siano separate da `,`
- Esempio: `1,Igiene Dentale,85,0,30`

---

## 📥 STEP 3: Importa i Dati nel Simulatore

### **3A: Copia i dati nel foglio DATA**

1. Apri il file `03_simulatore_margini.xlsx`
2. Vai al foglio **"DATA"**
3. Seleziona il range **A1:N1** (l'intestazione)
4. Apri il file `04_DATA_MASTER_MARGINI.csv` (quello che hai scaricato dal repo)
5. Seleziona tutte le righe del CSV
6. Copia e incolla nel foglio DATA, sostituendo i dati demo

**Alternativa (più facile):**
1. In Excel: **Dati > Da testo/CSV**
2. Seleziona il file `04_DATA_MASTER_MARGINI.csv`
3. Configura il delimitatore (virgola)
4. Incolla in un foglio temporaneo
5. Copia le colonne che ti interessano

### **3B: Aggiorna le formule**

Quando hai i nuovi dati, verifica che le formule siano corrette:

**Colonna J (Costo Poltrona):**
```
=I2/60*28
```
Cambia il `28` con il tuo **Costo_Orario_Poltrona** da E.

**Colonna L (Compenso Medico):**
```
=IF(G2="Percentuale", K2*H2, H2)
```
Questa formula è OK, non toccarla.

**Colonna N (Margine €):**
```
=B2-M2
```
Verifica che il calcolo sia: Prezzo - Costo Totale

**Colonna O (Margine %):**
```
=N2/B2*100
```
Verifica che sia: (Margine € / Prezzo) × 100

---

## 🎮 STEP 4: Testa il Simulatore

Una volta importati i dati, testa il simulatore:

1. Vai al foglio **"SIMULATORE"**
2. Nel dropdown "Prestazione", seleziona una delle TUE prestazioni
3. Nel dropdown "Medico", seleziona uno dei TUOI medici
4. Verifica che i numeri cambino correttamente
5. Sposta i 3 slider e vedi come cambia il margine

**Test specifico:**
- Seleziona la prestazione che fai più spesso
- Con il medico che lavora più ore
- Controlla se il margine è realistico
- Se è negativo o molto basso, qualcosa non quadra nei dati

---

## 🐛 Troubleshooting

### **P: I numeri nel simulatore non cambiano quando cambio la prestazione**
**R:** Il dropdown non è configurato correttamente. Va a:
- Foglio SIMULATORE, cella B4
- Dati > Convalida dati
- Verifica che l'origine sia: `=DATA.$B$2:$B$13` (o il range giusto)

### **P: Il margine è negativo per tutte le prestazioni**
**R:** Probabilmente i costi sono troppo alti. Controlla:
- Il Costo_Orario_Poltrona è realistico?
- I costi dei materiali sono corretti?
- Il compenso medico è troppo alto?

### **P: Una prestazione specifica mostra numeri strani**
**R:** Ricalcola manualmente:
- Prezzo listino: X
- Costi totali: Y
- Margine dovrebbe essere: X - Y
- Se non torna, c'è un errore nei dati di input

---

## 📊 STEP 5: Usa il Simulatore per le Decisioni

Una volta che il simulatore ha i TUOI dati, puoi usarlo per:

### **Scenario 1: Valutazione Nuovo Medico**
- "Se assumo questo medico al 40%, quali prestazioni rimangono profittevoli?"
- Testa il simulatore con diversi compensi

### **Scenario 2: Negoziazione Prezzi**
- "Se sconto il 15% a un paziente, quale medico posso permettermi ancora?"
- Sposta il slider dello sconto

### **Scenario 3: Ottimizzazione Tempi**
- "Se il medico è più veloce (60 min invece di 90), il margine migliora?"
- Sposta il slider del tempo

### **Scenario 4: Break-even Analysis**
- "A quale sconto minimo rimango in break-even?"
- Sposta lo slider finché Margine € non diventa 0

---

## ✅ Checklist Finale

Prima di presentare il simulatore al tuo commercialista o al tuo medico:

- [ ] Tutti i prezzi del listino sono aggiornati
- [ ] Tutti i materiali sono inclusi (anche i monouso piccoli)
- [ ] I costi dei materiali sono reali (fatture attuali)
- [ ] I tempi di esecuzione sono realistici (chiesti ai medici)
- [ ] Gli accordi con i medici sono corretti (% esatta o € fisso)
- [ ] Ho testato 3-5 prestazioni e i numeri hanno senso
- [ ] I margini sono coerenti con le mie aspettative
- [ ] Ho provato a muovere i 3 slider e tutto funziona

---

## 🚀 Prossimi Passi

1. **Raccogli i dati** (2-3 ore di lavoro)
2. **Importa nel simulatore** (30 minuti)
3. **Testa con 5 prestazioni reali** (1 ora)
4. **Usa il simulatore per le decisioni** (ongoing)
5. **Aggiorna i dati ogni trimestre** (se cambiano costi o accordi)

---

**Domande? Errori? Contattami e aggiusto!** 🚀
