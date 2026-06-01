# CInfo.mqh

Libreria MQL5 per la gestione avanzata di informazioni di trading, analisi del mercato e statistiche operative.

---

# Funzionalità principali

Questa libreria permette di:

- Analizzare posizioni aperte e ordini pendenti
- Recuperare informazioni su deal, posizioni e ordini
- Calcolare risultati giornalieri e storici
- Gestire statistiche del giorno precedente
- Analizzare candele e prezzo (massimi/minimi)
- Rilevare nuove candele
- Calcolare valore pip

---

# Strutture dati

## DealInfo

Contiene informazioni sull’ultimo deal trovato.

| Campo | Tipo | Descrizione |
|------|------|-------------|
| ticket | ulong | ID deal |
| symbol | string | Simbolo |
| tipo_entrata | ENUM_DEAL_ENTRY | Tipo entrata |
| tipo_deal | ENUM_DEAL_TYPE | Tipo deal |
| time | datetime | Tempo esecuzione |
| magic | long | Magic number |
| volume | double | Volume |
| price | double | Prezzo |
| profit | double | Profitto |
| fee | double | Fee |
| commission | double | Commissione |
| swap | double | Swap |
| take | double | Take profit |
| stop | double | Stop loss |

---

## PositionInfo

| Campo | Tipo | Descrizione |
|------|------|-------------|
| ticket | ulong | ID posizione |
| symbol | string | Simbolo |
| time | datetime | Apertura posizione |
| tipo | ENUM_POSITION_TYPE | Tipo posizione |
| magic | long | Magic number |
| volume | double | Volume |
| price_open | double | Prezzo apertura |
| stop | double | Stop loss |
| take | double | Take profit |
| swap | double | Swap |
| profit | double | Profitto corrente |

---

## OrderInfo

| Campo | Tipo | Descrizione |
|------|------|-------------|
| ticket | ulong | ID ordine |
| symbol | string | Simbolo |
| time_inserimento | datetime | Apertura ordine |
| time_cancellazione | datetime | Scadenza/cancellazione |
| tipo | ENUM_ORDER_TYPE | Tipo ordine |
| magic | long | Magic number |
| volume | double | Volume |
| stop | double | Stop loss |
| take | double | Take profit |

---

## DailyStats

| Campo | Tipo | Descrizione |
|------|------|-------------|
| risultato | double | Profitto netto giornaliero |
| drawdown | double | Somma perdite giornaliere |
| num_trade | int | Numero trade chiusi |

---

# Inizializzazione

```cpp
#include <CInfo.mqh>

CInfo info;
```

---

# Metodi disponibili

# 1. Candlestick e Prezzo

---

## NuovaCandela()

Ritorna `true` se è iniziata una nuova candela.

```cpp
if(info.NuovaCandela())
{
   Print("Nuova candela rilevata");
}
```

---

## Pips()

Ritorna il valore di 1 pip del simbolo corrente.

```cpp
double pip = info.Pips();
```

---

## Massimo(candele_totali, candela_inizio)

Ritorna il massimo delle ultime N candele.

```cpp
double high = info.Massimo(20, 1);
```

---

## Minimo(candele_totali, candela_inizio)

Ritorna il minimo delle ultime N candele.

```cpp
double low = info.Minimo(20, 1);
```

---

# 2. Posizioni

---

## CiSonoPosizioni(magic, symbol)

Controlla se esistono posizioni aperte per magic e simbolo.

```cpp
bool exists = info.CiSonoPosizioni(12345, _Symbol);
```

---

## CiSonoPosizioni(magic, symbol, tipo)

Controlla se esistono posizioni di un tipo specifico.

```cpp
bool buyOnly = info.CiSonoPosizioni(
   12345,
   _Symbol,
   POSITION_TYPE_BUY
);
```

---

## InfoUltimaPosizione(symbol, magic)

Restituisce l’ultima posizione aperta.

```cpp
PositionInfo pos =
   info.InfoUltimaPosizione(_Symbol, 12345);

Print(pos.profit);
```

---

## RisultatoPosizioniAperte(magic, symbol)

Profitto/perdita delle posizioni aperte.

```cpp
double p = info.RisultatoPosizioniAperte(12345, _Symbol);
```

---

# 3. Ordini

---

## CiSonoOrdini(magic, symbol)

Controlla ordini pendenti.

```cpp
if(info.CiSonoOrdini(12345, _Symbol))
{
   Print("Ordini presenti");
}
```

---

## CiSonoOrdini(magic, symbol, tipo)

Controlla ordini di un tipo specifico.

```cpp
bool buyLimit =
   info.CiSonoOrdini(
      12345,
      _Symbol,
      ORDER_TYPE_BUY_LIMIT
   );
```

---

## InfoUltimoOrdine(symbol, magic)

Restituisce l’ultimo ordine pendente.

```cpp
OrderInfo order =
   info.InfoUltimoOrdine(_Symbol, 12345);
```

---

# 4. Deal e storico

---

## InfoUltimoDeal(symbol, magic)

Restituisce l’ultimo deal.

```cpp
DealInfo deal =
   info.InfoUltimoDeal(_Symbol, 12345);
```

---

## RisultatoDealsGiornalieri(magic, symbol)

Profitto netto dei deal chiusi oggi.

```cpp
double daily =
   info.RisultatoDealsGiornalieri(12345, _Symbol);
```

---

## RisultatoGiornaliero(magic, symbol)

Somma di posizioni aperte + deal chiusi.

```cpp
double total =
   info.RisultatoGiornaliero(12345, _Symbol);
```

---

## RisultatoGiornoPrecedente(magic, symbol)

Statistiche del giorno precedente.

```cpp
DailyStats stats =
   info.RisultatoGiornoPrecedente(12345, _Symbol);

Print(stats.risultato);
Print(stats.drawdown);
Print(stats.num_trade);
```

---

# 5. Utility interne

---

## Ask() / Bid()

Prezzi correnti (uso interno o avanzato).

---

# Quick Example

```cpp
#include <CInfo.mqh>

CInfo info;

void OnTick()
{
   if(info.NuovaCandela())
   {
      double result =
         info.RisultatoGiornaliero(12345, _Symbol);

      Print("Risultato: ", result);
   }

   if(info.CiSonoPosizioni(12345, _Symbol))
   {
      Print("Posizione attiva");
   }
}
```

---

# Note importanti

- Tutti i calcoli sono basati su `magic number` + `symbol`
- Le funzioni storico usano `HistorySelect`
- Le funzioni giornaliere si basano su:
  - inizio giornata = 00:00 server time
- Le performance dipendono dalla quantità di history disponibile

---

# Obiettivo della libreria

Ridurre la complessità nella gestione di:

- posizioni
- ordini
- storico
- performance

per costruire Expert Advisor più puliti e modulari.

---

# Licenza

Uso libero per progetti personali e commerciali.

Vietata la redistribuzione o rivendita della libreria senza autorizzazione.
