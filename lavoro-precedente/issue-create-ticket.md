# Issue: Create ticket with validation

## Request

```txt
Serve creare ticket dal supporto.
```
## Fatti (Facts)

- L'applicazione è una semplice webapp di ticketing.
- Esiste già un componente "ticket" usato per la visualizzazione nell'elenco (verificato nel codice esistente).

## Assunzioni (Assumptions)

- Il flusso di creazione deve riutilizzare il componente ticket esistente, modificandone solo le informazioni al suo interno.

## Domande Aperte (Questions)

- L'elenco in cui appare il nuovo ticket è già presente nell'app o va mostrato in un punto nuovo?
- Il campo di input per il testo del problema è già presente nell'app o va aggiunto?
- Quali campi compongono il ticket (solo testo libero, oppure anche titolo/identificativo/altro)?
  → Deciso: `title`, `description`, `priority`.
- Dove persiste il ticket (in memoria, localStorage, backend già esistente)?
  → Deciso: persistenza in memoria (array/struttura in-memory), nessun backend dedicato.
- Il campo testo è obbligatorio? Ha vincoli di lunghezza minima/massima?
  → Deciso: `title` e `description` obbligatori e non vuoti; `priority` obbligatoria e appartenente a {low, medium, high}.
- Chi visualizza l'elenco dei ticket (solo l'operatore che lo crea, o anche altri ruoli)?

## Decisione (Decision)

Per questo slice, "creare ticket" significa:

```txt
Un operatore compila il form di creazione ticket inserendo `title`, `description` e selezionando `priority` tra {low, medium, high}. All'invio, il sistema valida i campi, salva il ticket in memoria con un `id` generato e lo mostra nell'elenco esistente.
```
## Fuori Scope / Non-Obiettivi (Non-Goals)

- Non creare pagine aggiuntive
- Non modificare UI/UX
- Non toccare altro codice che non sia legato alla modifica richiesta
- Non introdurre autenticazione o autorizzazione
- Non aggiungere campi extra al ticket (es. assegnatario, categoria, allegati)
- Non aggiungere funzionalità di modifica o eliminazione di un ticket esistente
- Non introdurre un nuovo backend / database / servizio di persistenza complesso
- Non aggiungere notifiche

## Campi Rimandati

- `stato` — non necessario al primo slice; da decidere se generato di default o selezionabile in L07/L08
- `area` — puo' servire per categorizzazione futura; non richiesto per "create ticket"

## Formato Errore Minimo

Tutte le risposte di errore restituiscono un JSON con struttura:

```json
{ "error": "<CODICE>" }
```

| Codice | Significato |
| --- | --- |
| `TITLE_REQUIRED` | `title` mancante o vuoto |
| `DESCRIPTION_REQUIRED` | `description` mancante o vuoto |
| `PRIORITY_INVALID` | `priority` non in {low, medium, high} |

## Criteri Di Accettazione (Acceptance Criteria)

1. L'operatore compila `title`, `description`, `priority` e, all'invio, un nuovo ticket compare nell'elenco pre-esistente con `id` generato.
2. I campi `title`, `description`, `priority` mostrati nel nuovo ticket corrispondono esattamente ai valori inseriti.
3. L'invio con `title` vuoto o `description` vuoto non crea un ticket e restituisce errore.
4. L'invio con `priority` non appartenente a {low, medium, high} non crea un ticket e restituisce errore.
5. Nessuna nuova pagina o nuovo componente UI viene introdotto; il componente ticket esistente è riutilizzato.
6. Nessuna variazione visiva rispetto allo stato attuale dell'app: layout, stili e flussi esistenti restano invariati.

## Piano Di Verifica Manuale (Manual Test Plan)

- Apro l'app, identifico i campi `title`, `description`, `priority` e l'elenco ticket esistente.
- Compilo `title="Test"`, `description="Descrizione test"`, `priority="low"`, invio: verifico ticket in elenco con i tre valori e un `id` generato.
- Invio con `title=""`: verifico errore `{"error":"TITLE_REQUIRED"}` e nessun ticket creato.
- Invio con `description=""`: verifico errore `{"error":"DESCRIPTION_REQUIRED"}` e nessun ticket creato.
- Invio con `priority="urgentissimo"`: verifico errore `{"error":"PRIORITY_INVALID"}` e nessun ticket creato.
- Creo 3 ticket validi con valori diversi e verifico che compaiano tutti nell'elenco, nell'ordine atteso.
- Confronto visivo con screenshot pre-modifica: nessuna differenza di layout/stile.
- Navigazione nell'app: apertura elenco, apertura dettaglio (se presente), eventuali altri flussi esistenti funzionano come prima (nessuna regressione).
- Confronto visivo con uno screenshot/dump dello stato pre-modifica: nessuna differenza di layout/stile.