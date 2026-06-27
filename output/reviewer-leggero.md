## Findings

- UI crash pianificato contraddice gli acceptance criteria dell'issue. `entry-point-map.md:80-84` e `prompt-patch-limitato.md:39-43` dichiarano che il crash di `TicketCard (RangeError su new Date(undefined))` è `"accettato per questo slice"`. L'issue `issue-create-ticket.md:73` (criterio #6) richiede: `"Nessuna variazione visiva rispetto allo stato attuale dell'app: layout, stili e flussi esistenti restano invariati"`. Un crash JavaScript dell'intera lista ticket non è "nessuna variazione" — è una regressione bloccante. Il piano accetta un danno funzionale che l'issue vieta esplicitamente.
- status: "open" assente dal contract di risposta ma aggiunto internamente. Il piano `entry-point-map.md:72` prevede di salvare `status: "open"` nell'array in memoria per compatibilità col filtro GET esistente, ma di rispondere con solo `{id, title, description, priority}` come da contract. Non c'è violazione formale del contract di risposta, ma la divergenza tra dato salvato e dato esposto non è documentata nel contract sketch né nella issue — un futuro consumer della GET troverà status nei ticket ma non saprà da dove viene.

## Verifiche Mancanti

- Test di regressione multi-ticket L05 non coperto. L'issue `issue-create-ticket.md:82` richiede esplicitamente: `"Creo 3 ticket validi con valori diversi e verifico che compaiano tutti nell'elenco, nell'ordine atteso"`. Né la verifica in `entry-point-map.md:94-112` né quella in `prompt-patch-limitato.md:94-113` includono questo caso. Il piano verifica solo 1 POST + GET, non l'accumulo ordinato di più ticket.
- Nessuna verifica di sopravvivenza UI dopo POST. Il piano di verifica copre solo l'API (POST e GET), mai cosa effettivamente appare a schermo dopo la creazione di un ticket. Considerando il rischio RangeError noto, la verifica manuale dovrebbe includere un controllo esplicito: `"dopo POST valida, l'app non crasha e l'elenco ticket è navigabile"`. Assente.
- `manual-test-plan.md`, `handoff-pr-description.md`, `review-note.md` non compilati. Richiesti da `docs/istruzioni-l08.md` step 9, 10, 12. Il gate L08 è incompleto senza questi output.

## Decisione

- Fermarsi e chiedere contesto — La contraddizione tra "crash accettato" e l'acceptance criterion #6 dell'issue non può essere risolta dal piano senza una decisione esplicita. 
Le opzioni sono: 
a. mitigare subito il crash in TicketCard.jsx con un fallback es. `ticket.updatedAt ?? new Date().toISOString())` autorizzando una modifica minima al frontend 
b. emendare l'acceptance criterion L05 per dichiarare il degrado temporaneo. Senza una di queste due scelte, procedere con la patch produrrebbe un'app che crasha, violando i criteri di accettazione dichiarati.