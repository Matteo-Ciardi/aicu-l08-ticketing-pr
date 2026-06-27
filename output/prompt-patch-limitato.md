# Prompt Patch Limitato

## Prima Di Compilare

Un prompt patch limitato e' l'istruzione operativa che userai nel lab per autorizzare solo il primo slice.

Serve a dire al builder:

- quale task affrontare;
- quali file puo' toccare;
- cosa resta fuori scope;
- quale verifica proporre;
- quando fermarsi.

Non usarlo per chiedere la feature completa o per autorizzare file non verificati.

Usa questo prompt nel lab L08, dopo il gate umano.

```txt
Dato questo task:
"Serve creare ticket dal supporto."

Usa questi input:
- issue: lavoro-precedente/issue-create-ticket.md
- contract sketch: lavoro-precedente/contract-sketch-create-ticket.md
- data sketch: lavoro-precedente/data-sketch-create-ticket.md
- mappa dei punti di intervento: output/entry-point-map.md

Applica solo il primo slice approvato:
Implementare il backend minimo per `POST /api/tickets`: il server riceve `{title, description, priority}`,
valida i campi (title e description non vuoti, priority in {low, medium, high}), genera un `id`, salva il ticket
nell'array in memoria e risponde con `{id, title, description, priority}`.
Nessuna modifica frontend. Il nuovo ticket e' verificabile via `GET /api/tickets` e appare nell'elenco
esistente senza toccare UI.

File o aree ammesse:
- server/index.js
- server/data/tickets.js

File o aree vietate:
- src/styles.css
- src/main.jsx
- index.html
- src/api.js
- src/App.jsx
- src/components/TicketCard.jsx
- src/components/TicketList.jsx

Non aggiungere:
- auth;
- allegati;
- notifiche;
- owner avanzato;
- dashboard;
- migration;
- redesign;
- refactor generale;
- campi extra oltre a title, description, priority (es. assegnatario, categoria, stato, area);
- pagine o componenti UI aggiuntivi;
- persistenza complessa (database, backend dedicato).

Prima di modificare, conferma:
- task;
- file che toccherai;
- cosa resta fuori scope;
- verifica manuale proposta.

Applica solo la patch minima approvata.
Fermati se servono file o decisioni fuori piano.
```

## Gate Umano Prima Della Patch

Prima di autorizzare modifiche, controlla che la risposta AI dica chiaramente:

- quali file tocchera';
- quali file non tocchera';
- quale verifica manuale propone;
- quando si fermera'.

Se manca uno di questi punti, chiedi correzione prima della patch.

## Verifica Attesa

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

Confronto visivo con screenshot pre-modifica: nessuna differenza di layout/stile.
Navigazione nell'app: elenco, dettaglio e flussi esistenti funzionano come prima.
```

## Note

- Non chiedere feature completa.
- Non chiedere di "sistemare tutto".
- Non autorizzare file non verificati.
