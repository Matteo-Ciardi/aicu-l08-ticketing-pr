# Contratto Iniziale (Contract Sketch) - Create Ticket

## Prima Di Compilare

Un contratto iniziale (contract sketch) e' una descrizione leggera di input, output e risposte attese.

Serve a rendere verificabile `create ticket` prima di chiedere codice all'AI.

Compila solo la superficie minima:

- cosa entra;
- cosa esce;
- cosa viene rifiutato;
- quale errore ti aspetti.

Non trasformarlo in una specifica API completa, uno schema di un database o un piano di una patch.

Quando compili, ogni esempio deve rispondere (almeno) a tre domande:

- perche' questo input e' valido o invalido?
- quale risposta mi aspetto?
- quale parte della issue o del fuori scope giustifica la scelta?

## Request

```txt
Serve creare ticket dal supporto.
```

## Boundary Map

| Superficie | Cosa riguarda | Nota |
| --- | --- | --- |
| UI | campi di testo: titolo, descrizione, priorita', da compilare da parte dell'utente | [nota] |
| API / azione | una chiamata POST verso il server che inserira' il nuovo ticket in memoria  | [nota] |
| Dati | salviamo il titolo,la descrizione e la priorita' | [nota] |
| Verifica | provo a fare un ticket e controllo se i campi vengono salvati correttamente e il ticket viene inviato | [nota] |

## Action

Per questo slice, `create ticket` significa:

```txt
il ticket viene creato ed e' visualizzabile
```

## Payload Valido

```json
{
    "title": "Ticket di prova",
    "description": "Ticket di prova per test sistema",
    "priority": "low"
}
```

Perche' e' valido:

- Il payload e' il minimo indispensabile e contiene i campi corretti
- La priority appartiene all'insieme {low, medium, high}

## Risposta Attesa Di Successo

```json
{
    "id": "<generato>",
    "title": "Ticket di prova",
    "description": "Ticket di prova per test sistema",
    "priority": "low"
}
```

Campi attesi:

- `id` — generato (il sistema crea un identificativo univoco)
- `title` — confermato (echo dell'input)
- `description` — confermato (echo dell'input)
- `priority` — confermato (echo dell'input)

## Payload Invalido 1

```json
{
    "title": "",
    "description": "Descrizione valida",
    "priority": "low"
}
```

Motivo del rifiuto:

```txt
Il campo 'title' e' obbligatorio. Un ticket senza titolo non ha significato e viola
il criterio L05: "L'invio con campo vuoto non crea un ticket".
```

Risposta attesa:

```json
{"error": "TITLE_REQUIRED"}
```

## Payload Invalido 2

```json
{
    "title": "Ticket di prova",
    "description": "Descrizione valida",
    "priority": "urgentissimo"
}
```

Motivo del rifiuto:

```txt
Il campo 'priority' deve appartenere all'insieme {low, medium, high}.
"urgentissimo" non e' un valore ammesso dal contratto.
```

Risposta attesa:

```json
{"error": "PRIORITY_INVALID"}
```

## Payload Invalido 3

```json
{
    "title": "Ticket di prova",
    "description": "",
    "priority": "low"
}
```

Motivo del rifiuto:

```txt
Il campo 'description' e' obbligatorio. Un ticket senza descrizione viola
il criterio L05: "L'invio con campo vuoto non crea un ticket".
```

Risposta attesa:

```json
{"error": "DESCRIPTION_REQUIRED"}
```

## Error Model Minimo

| Caso | Motivo | Risposta attesa |
| --- | --- | --- |
| `title` mancante o vuoto | `title` e' il campo minimo indispensabile; senza titolo il ticket non ha significato e viola l'acceptance criterion L05 | `{"error": "TITLE_REQUIRED"}` |
| `description` mancante o vuoto | `description` e' obbligatorio e, se vuoto, viola l'acceptance criterion L05 | `{"error": "DESCRIPTION_REQUIRED"}` |
| `priority` fuori da {low, medium, high} | `priority` deve appartenere a {low, medium, high}; valori esterni rendono il ticket non classificabile | `{"error": "PRIORITY_INVALID"}` |

## Non-Goals Confermati

- Non aggiungere autenticazione o autorizzazione
- Non introdurre campi extra oltre a `title`, `description`, `priority` (es. assegnatario, categoria, allegati)
- Non implementare modifica o eliminazione di ticket esistenti
- Non introdurre persistenza complessa (database, backend dedicato)
- Non aggiungere notifiche
- Non modificare UI/UX esistente
- Non creare pagine o componenti UI aggiuntivi
- `stato` e `area` sono rimandati a uno slice successivo; non bloccano il primo "create ticket"


