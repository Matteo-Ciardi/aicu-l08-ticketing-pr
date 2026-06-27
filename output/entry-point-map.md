# Mappa Dei Punti Di Intervento (Entry-Point Map) - Create Ticket

## Prima Di Compilare

Una mappa dei punti di intervento (entry-point map) e' una mappa dei file o delle aree in cui la modifica potrebbe entrare.

Serve a distinguere:

- file suggeriti dall'AI;
- file trovati nel repo;
- file davvero collegati al task;
- file da non toccare.

L'output atteso e' una lista di candidati con evidenza, non una lista di nomi plausibili.

Non segnare un file come `ammesso` se non lo hai aperto o letto.

## Task

```txt
Serve creare ticket dal supporto.
```

## Input Usati

- Issue L05 importata: `lavoro-precedente/issue-create-ticket.md`
- Contract sketch L06 importato: `lavoro-precedente/contract-sketch-create-ticket.md`
- Data sketch L06 importato: `lavoro-precedente/data-sketch-create-ticket.md`

## Regola

Un file suggerito dall'AI non e' ancora un file candidato verificato.

Diventa candidato solo se:

- esiste;
- e' stato aperto o letto;
- ha un ruolo collegato al task;
- hai scritto l'evidenza minima.

## File Candidati

| File / area | Suggerito da | Evidenza verificata | Stato |
| --- | --- | --- | --- |
| server/index.js | repo | Righe 26-31: stub `POST /api/tickets` con 501 "primo slice da implementare nel Lab 08". Il contract sketch definisce payload valido, risposta di successo ed errori (`TITLE_REQUIRED`, `DESCRIPTION_REQUIRED`, `PRIORITY_INVALID`). L'issue L05 richiede validazione campi e salvataggio in memoria. | ammesso |
| server/data/tickets.js | repo | Esporta array `tickets[]` con 3 fixture e `allowedPriorities` = `["Alta", "Media", "Bassa"]`. Il data sketch classifica `id` come generato, `title/description/priority` come accettati. Il nuovo ticket va inserito in questo array. Discrepanza: l'issue L05 usa valori `{low, medium, high}`, la codebase usa italiano. | ammesso |
| src/api.js | repo | Espone solo `fetchOpenTickets()` per GET `/api/tickets`. Manca una funzione `createTicket()` per la POST, richiesta dal contract sketch. | dubbio |
| src/App.jsx | repo | Gestisce stato `tickets[]`, `status`, `error`. L'issue L05 richiede che il nuovo ticket compaia nell'elenco pre-esistente. Se lo slice e' solo backend, nessuna modifica necessaria. | dubbio |
| src/components/TicketCard.jsx | repo | Renderizza `ticket.id`, `ticket.priority`, `ticket.title`, `ticket.customer`, `ticket.updatedAt`. Il contract sketch prevede risposta con solo `{id, title, description, priority}` -- `customer` e `updatedAt` non sono nel contratto. | dubbio |
| src/components/TicketList.jsx | repo | Riceve `tickets[]` prop e mappa su `<TicketCard>`. L'issue L05 richiede che il nuovo ticket compaia nell'elenco. Il componente non richiede modifiche: funziona con qualsiasi array. Solo verifica. | ammesso |
| src/styles.css | repo | Stili per `.ticket-card`, `.ticket-card__priority--alta/media/bassa`. Nessuno stile per form. Se lo slice e' backend-only, nessun ruolo. | vietato |
| src/main.jsx | repo | Boilerplate: monta `<StrictMode><App /></StrictMode>`. Nessun ruolo collegato al create ticket. | vietato |
| index.html | repo | Shell HTML statica con `<div id="root">`. Nessun ruolo collegato al create ticket. | vietato |

## File Ammessi Per Il Primo Slice

- server/index.js
- server/data/tickets.js
- src/components/TicketList.jsx

## File Vietati O Fuori Scope

- src/styles.css - Non necessario per slice backend-only; nessuno stile form da aggiungere
- src/main.jsx - Boilerplate React senza ruolo nel task
- index.html - Shell statica senza ruolo nel task

## Primo Slice Proposto

```txt
Implementare il backend minimo per `POST /api/tickets`: il server riceve `{title, description, priority}`,
valida i campi (title e description non vuoti, priority in {validi}), genera un `id`, salva il ticket
nell'array in memoria e risponde con `{id, title, description, priority}`.
Nessuna modifica frontend. Il nuovo ticket e' verificabile via `GET /api/tickets` e appare nell'elenco
esistente senza toccare UI.
```

## Perche' Questo Slice E' Piccolo

- Toglie solo il codice `501` e scrive il corpo della POST con validazione
- Non tocca frontend, CSS, componenti React
- Usa l'array in-memory gia' esistente in `server/data/tickets.js`
- Non introduce autenticazione, persistenza, migration, dashboard

## Verifica Manuale Proposta

```txt
POST /api/tickets con {"title":"Test","description":"Descrizione test","priority":"low"}
→ atteso 200 con {"id":"<generato>","title":"Test","description":"Descrizione test","priority":"low"}

POST /api/tickets con {"title":"","description":"ok","priority":"low"}
→ atteso 400 con {"error":"TITLE_REQUIRED"}

POST /api/tickets con {"title":"Test","description":"","priority":"low"}
→ atteso 400 con {"error":"DESCRIPTION_REQUIRED"}

POST /api/tickets con {"title":"Test","description":"ok","priority":"urgentissimo"}
→ atteso 400 con {"error":"PRIORITY_INVALID"}

GET /api/tickets → l'array contiene il nuovo ticket creato
```
