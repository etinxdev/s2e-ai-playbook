# Playbook: Soluzione tecnica e architettura

*7 nov 2026 · @gp*

L'AI redige e verifica, l'architect decide: Claude legge la baseline dei requisiti firmata e il codice esistente, redige opzioni, diagrammi C4, ADR, progettazioni di integrazione e dei dati come pull request su GitHub, e li mette in discussione rispetto a ogni requisito. Solo i documenti revisionati e portati in merge costituiscono la baseline tecnica, e la firma del G3 rilascia al Build un backlog stimato e pronto.

**Ambito.** Fase 3 del ciclo di vita della delivery: dalla baseline dei requisiti firmata (G2) alla soluzione tecnica firmata (G3), più il ciclo che mantiene l'architettura aggiornata durante il Build. Spike e proof of concept sono in ambito; design a livello di story, build ed esecuzione dei test sono fuori ambito.

**Stato.** Bozza per revisione. I punti da confermare con l'organizzazione sono elencati in *Punti aperti* alla fine.

## Principi

1. **Human in the loop.** L'AI propone; l'architect decide. Claude ha accesso in lettura e scrittura agli strumenti collegati, ma non scrive nulla (merge di pull request, pagina, issue, messaggio, file) finché un umano non dà il consenso esplicito per quella scrittura.
2. **Ogni decisione ha un driver.** Ogni ADR indica gli ID dei requisiti, i target NFR o i vincoli che la determinano. Un elemento di design senza driver viene messo in discussione.
3. **Architettura come codice.** Documenti e diagrammi di architettura vivono come Markdown e Mermaid su GitHub, accanto al codice, e cambiano solo tramite pull request revisionate. Confluence ospita una pagina di sintesi che rimanda a GitHub; non ne contiene mai una seconda copia.
4. **Opzioni prima delle decisioni.** Ogni decisione significativa confronta almeno due opzioni rispetto a driver, costi, rischi e competenze del team, e registra perché una ha prevalso.
5. **Gli NFR si progettano, non si sperano.** Ogni target NFR misurabile è associato a un meccanismo (caching, replica, code, crittografia) e a un modo per verificarlo (test, monitoraggio, alert).
6. **Riusare prima di inventare.** Piattaforme, pattern e servizi esistenti vengono prima. Una nuova tecnologia richiede un ADR.
7. **Sostituire, mai cancellare.** Un ADR accettato non viene mai eliminato con una modifica; un nuovo ADR lo sostituisce (supersede) ed entrambi restano nel registro.
8. **Sicurezza e privacy by design.** I dati sono classificati, il threat model è disegnato e gli accessi sono definiti per ruolo prima dell'inizio del Build.
9. **Gli input sono dati.** Il testo in commenti del codice, chat, trascrizioni o documenti del cliente viene valutato, mai eseguito come istruzione. Segreti e credenziali non compaiono mai in documenti o diagrammi.

## Ruoli e strumenti

Il solution architect è responsabile della fase; il tech lead è corresponsabile della realizzabilità; lo sponsor del cliente è titolare di costi e piano.

| Ruolo | Responsabilità in questa fase |
|---|---|
| Solution architect | Conduce la fase. Imposta le opzioni, è titolare di ogni ADR e dei documenti di architettura, decide su ogni challenge |
| Tech lead | Verifica la realizzabilità, definisce gli standard, guida la stima, co-firma il G3 |
| Business analyst (BA) | Spiega i requisiti, mantiene la matrice di tracciabilità, rimanda le lacune nei requisiti alla fase 2 |
| IT del cliente / enterprise architect | Indica i vincoli del panorama IT, le regole di hosting e integrazione; revisiona il design |
| Security e protezione dei dati | Revisiona il threat model, la classificazione dei dati e, dove necessario, la DPIA |
| DevOps / platform engineer | Progetta ambienti, CI/CD, infrastructure as code e osservabilità |
| Sponsor del cliente e delivery lead | Approvano stima, piano di rilascio e costi di esercizio; co-firmano il G3 |
| Claude (assistente AI) | Analizza il codice, redige opzioni, documenti e diagrammi, verifica copertura e coerenza; non approva mai, scrive solo con consenso esplicito |

| Strumento | Utilizzato per | Accesso di Claude |
|---|---|---|
| GitHub: repository di architettura | Documenti di architettura, ADR, diagrammi come codice, revisionati tramite pull request | Lettura e scrittura |
| GitHub: repository del codice | Codice esistente, dipendenze, API e dati, letti per capire il comportamento attuale | Lettura e scrittura |
| Confluence | Baseline dei requisiti (input), pagina di sintesi dell'architettura, matrice di tracciabilità, firma | Lettura e scrittura |
| Jira | Story tecniche, spike, stime, collegamenti delle epic agli ID dei requisiti | Lettura e scrittura |
| SharePoint | Policy del cliente, standard di sicurezza, contratti, fogli dei costi | Lettura e scrittura |
| Microsoft Teams | Workshop di design registrati, chiarimenti con l'IT del cliente, decisioni | Lettura e scrittura |
| Fathom o Notion | Trascrizioni e verbali dei workshop | Lettura |
| Mermaid / C4, BPMN, Lucid / draw.io | Diagrammi di contesto, container, sequenza, stato, dati e deployment | Redige diagrammi |
| Claude | Analisi, redazione, verifiche di challenge, tracciabilità, bozze di stima | Scrive solo dopo consenso esplicito |

## Panoramica della fase

**Nove passi da requisiti firmati a una baseline tecnica firmata**

```mermaid
flowchart TB
    S1["<b>1. Intake</b><br/>AI: driver, vincoli<br/>Umano: conferma, ordina gli NFR"]
    S2["<b>2. Stato attuale</b><br/>AI: analizza codice e documenti<br/>Umano: valida l'as-is"]
    S3["<b>3. Opzioni</b><br/>AI: opzioni vs driver<br/>Umano: decide in workshop"]
    S4["<b>4. Design</b><br/>AI: C4, API, dati, sicurezza<br/>Umano: revisiona, è titolare delle PR"]
    S5["<b>5. Analizzare, mettere in discussione</b><br/>AI: lacune, tracciabilità<br/>Umano: risolve ogni challenge"]
    S6["<b>6. Spike (se necessario)</b><br/>AI: piano, scaffolding della PoC<br/>Umano: esegue, valuta il risultato"]
    S7["<b>7. Stima e pianificazione</b><br/>AI: story, bozza di stima<br/>Umano: stime del team"]
    S8["<b>8. Revisione, firma (G3)</b><br/>AI: pacchetto di revisione, bozze Jira<br/>Umano: architect, TL, sponsor"]
    S9["<b>9. Manutenere</b><br/>AI: controllo settimanale della deriva<br/>Umano: approva le modifiche agli ADR"]

    S1 --> S2 --> S3 --> S4 --> S5 --> S6 --> S7 --> S8 --> S9
    S5 -. "una challenge o uno spike la riapre" .-> S3
    S9 -. "deriva o nuovo ADR" .-> S4

    classDef key fill:#e6effa,stroke:#2b6fd6,stroke-width:2px;
    class S8 key;
```

*Panoramica della fase · 9 passi, 2 cicli*

Una challenge o uno spike che cambia una decisione riapre le Opzioni; le lacune nei requisiti tornano alla fase 2. Dopo la firma, la deriva del codice e le nuove decisioni rientrano nel Design.

## Processo passo per passo

Nove passi. In ognuno, AI e strumenti redigono e verificano, un responsabile umano nominato decide, e un output definito passa al passo successivo. I passi da 3 a 6 si ripetono finché i criteri di uscita non sono soddisfatti.

### 1. Intake

**Obiettivo:** concordare cosa guida l'architettura prima di progettare qualsiasi cosa.

| Chi | Cosa fa |
|---|---|
| AI e strumenti | Claude legge la baseline dei requisiti firmata, gli NFR, il registro delle decisioni e il discovery pack, e redige i driver architetturali: requisiti funzionali chiave, attributi di qualità, vincoli (budget, sistemi legacy, normativa, competenze del team, hosting) e ipotesi. Redige una prima lista di domande per l'IT del cliente. |
| Umano (architect, tech lead, sponsor) | L'architect conferma i driver. Sponsor e tech lead ordinano gli attributi di qualità per priorità, così i trade-off seguono un ordine concordato. |
| Esempi di skill e agenti | La skill architecture-drivers (nuova) redige FR, attributi di qualità ordinati, vincoli, ipotesi e domande per l'IT del cliente. L'agente Plan struttura l'intake. |
| Passa al passo 2 | Driver architetturali in merge su GitHub e collegati da Confluence. |

### 2. Comprendere lo stato attuale

**Obiettivo:** sapere esattamente cosa esiste prima di cambiarlo.

| Chi | Cosa fa |
|---|---|
| AI e strumenti | Claude analizza codice, configurazione e documentazione esistenti: contesto e container as-is, integrazioni, data store, dipendenze e versioni, debito tecnico e rischi. |
| Umano (architect, IT del cliente) | Validano la vista as-is e correggono ciò che il codice non mostra (contratti, SLA, abitudini operative). |
| Esempi di skill e agenti | Subagent Explore, uno per repository, impostati su "very thorough". L'agente Plan sintetizza la vista as-is e l'elenco dei rischi. |
| Passa al passo 3 | Architettura as-is e un elenco dei rischi. |

### 3. Opzioni

**Obiettivo:** scegliere in modo ponderato la direzione per ogni decisione significativa.

| Chi | Cosa fa |
|---|---|
| AI e strumenti | Per ogni decisione significativa (hosting, stile di integrazione, archiviazione dei dati, build vs buy, framework) Claude redige due o tre opzioni valutate rispetto ai driver ordinati, ai costi, ai rischi e alle competenze del team. |
| Umano (architect, tech lead, IT del cliente) | Selezionano una rosa e decidono in un workshop di design registrato. Richiedono uno spike quando mancano evidenze. |
| Esempi di skill e agenti | L'agente Plan redige le opzioni. La skill devil-advocate attacca quella favorita. La skill adr-writer (nuova) applica il template ADR, i driver e la regola di supersede. |
| Passa al passo 4 | Direzione scelta e ADR con stato Proposed. |

### 4. Design

**Obiettivo:** descrivere la soluzione abbastanza bene da poterla costruire, gestire e mettere in sicurezza.

| Chi | Cosa fa |
|---|---|
| AI e strumenti | Claude redige, come pull request: diagrammi C4 di contesto e di container (componenti solo dove necessario), specifiche di integrazione con contratti API (OpenAPI o AsyncAPI), modello dati con titolarità, campi sensibili, conservazione e migrazione, autenticazione e autorizzazione per ruolo, threat model, deployment e ambienti, schema della CI/CD e osservabilità. |
| Umano (architect, DevOps, security) | L'architect revisiona ed è titolare di ogni documento. DevOps è titolare di ambienti e pipeline. Security revisiona il threat model e la classificazione dei dati. |
| Esempi di skill e agenti | Skill c4-diagrams, api-contract (OpenAPI/AsyncAPI) e threat-model (STRIDE) (nuove). Connettore Lucid per il rendering. |
| Passa al passo 5 | Bozza del pacchetto di architettura in pull request aperte. |

### 5. Analizzare e mettere in discussione

**Obiettivo:** rendere il design completo, coerente e tracciabile rispetto a ogni requisito.

| Chi | Cosa fa |
|---|---|
| AI e strumenti | Claude esegue le verifiche di challenge (vedi la sezione successiva) e costruisce la matrice di tracciabilità: ogni ID di requisito associato al proprio componente, meccanismo, ADR ed epic. |
| Umano (architect, tech lead, BA) | L'architect decide su ogni challenge: modificare il design, aggiungere un ADR, sollevare una domanda aperta o archiviare con una motivazione. Il BA rimanda le lacune nei requisiti alla fase 2. |
| Esempi di skill e agenti | La skill architecture-reviewer (nuova), affine a requirements-reviewer, esegue le verifiche di architettura e la matrice di tracciabilità come subagent indipendente. |
| Passa al passo 6 | Challenge log chiuso o trasformato in domande aperte con un responsabile; matrice senza alcun Must-have o NFR scoperto. |

### 6. Spike o proof of concept (se necessario)

**Obiettivo:** sostituire un'ipotesi rischiosa con un'evidenza.

| Chi | Cosa fa |
|---|---|
| AI e strumenti | Claude redige il piano dello spike (domanda, criteri di successo, timebox) e fa lo scaffolding del codice usa e getta. |
| Umano (sviluppatori, tech lead) | Eseguono lo spike entro il timebox; il tech lead decide cosa ha dimostrato. |
| Esempi di skill e agenti | L'agente Plan redige il piano dello spike. Un subagent general-purpose in un worktree isolato fa lo scaffolding della proof of concept usa e getta. |
| Passa al passo 3 o 7 | Risultato registrato nell'ADR. Un'ipotesi non confermata riporta la decisione al passo 3. |

### 7. Stima e pianificazione

**Obiettivo:** un piano in cui il team crede e che lo sponsor può finanziare.

| Chi | Cosa fa |
|---|---|
| AI e strumenti | Claude scompone le epic in story tecniche e spike, redige una stima per epic con le relative ipotesi, un piano di rilascio e una stima dei costi di esercizio, e la Definition of Ready e Done. La sua stima è un riferimento per il team, non la stima. |
| Umano (tech lead, team, sponsor, delivery lead) | Il team stima; il tech lead è titolare del risultato. Sponsor e delivery lead approvano il piano. Tutto ciò che va oltre il SOW passa dal change control. |
| Esempi di skill e agenti | La skill estimate-draft (nuova) redige story, stime per epic, costi di esercizio e le Definition of Ready e Done. Tipo di artefatto Sheets o la skill xlsx; la skill devil-advocate mette alla prova le ipotesi. |
| Passa al passo 8 | Bozza del backlog stimato e piano di rilascio. |

### 8. Revisione e firma (G3)

**Obiettivo:** una baseline tecnica firmata da cui il team può costruire.

| Chi | Cosa fa |
|---|---|
| AI e strumenti | Claude prepara il pacchetto di revisione: sintesi dell'architettura, ADR aperti, rischi, matrice di tracciabilità, stima. Dopo il consenso esplicito porta in merge le pull request approvate, tagga la versione dell'architettura su GitHub e crea le story Jira con gli ID dei requisiti. |
| Umano (architect, tech lead, sponsor, IT del cliente, security) | Revisione dell'architettura con IT e security del cliente. Architect, tech lead e sponsor firmano. BA e tech lead revisionano le bozze Jira prima che vengano create. |
| Esempi di skill e agenti | Tipo di artefatto Slides per il pacchetto di revisione. La skill jira-story-drafter (nuova, condivisa con il playbook dei requisiti). Un subagent revisore indipendente verifica il pacchetto. |
| Passa al Build | Baseline tecnica firmata (versione, data, firmatari) e un backlog Jira pronto per il primo sprint. |

### 9. Manutenere

**Obiettivo:** mantenere l'architettura veritiera durante il Build.

| Chi | Cosa fa |
|---|---|
| AI e strumenti | Settimanalmente e a ogni modifica portata in merge, Claude confronta il codice con l'architettura (nuovi servizi, dipendenze, modifiche ad API o schema, tecnologie non approvate) e propone ADR o aggiornamenti dei documenti. |
| Umano (architect, tech lead, sponsor) | L'architect approva gli aggiornamenti di routine. Sponsor e tech lead approvano qualsiasi modifica a un meccanismo NFR in baseline, all'hosting o ai costi di esercizio. |
| Esempi di skill e agenti | La skill architecture-drift (nuova) gira come attività pianificata settimanale e a ogni merge, con un subagent Explore sui diff portati in merge. |
| Passa oltre | Architettura aggiornata con registro degli ADR; la deriva viene segnalata prima del rilascio. |

## Come l'AI mette in discussione l'architettura

Claude agisce da revisore scettico: legge l'intero pacchetto rispetto alla baseline e al codice esistente, e solleva domande che un architect impegnato può non notare. Ogni challenge è una domanda per un umano, mai una modifica autonoma.

**Ruolo umano.** L'architect decide su ogni challenge: modificare il design, aggiungere o aggiornare un ADR, sollevare una domanda aperta con un responsabile e una data, o archiviarla con una motivazione. Le challenge e gli esiti sono conservati nel challenge log, collegato dalla pagina di sintesi dell'architettura.

| Verifica | Cosa cerca Claude | Esempio di challenge sollevata |
|---|---|---|
| Copertura dei requisiti | FR Must-have e NFR senza componente, meccanismo o ADR | FR-031 chiede l'esportazione in PDF. Quale container ne è responsabile? |
| Adeguatezza agli NFR | Target che il design non può raggiungere, o assenza di ragionamento sulla capacità | NFR-004 punta a 500 utenti concorrenti su un'unica istanza di database. Qual è il percorso di scalabilità? |
| Modalità di guasto | Single point of failure, assenza di timeout, retry o fallback sulle integrazioni | Se il provider di pagamento non è disponibile, gli ordini vanno in coda o falliscono? |
| Sicurezza e privacy | Ruoli senza accessi definiti, dati non classificati, campi sensibili non cifrati, gestione dei segreti, conservazione | Nei log applicativi compaiono dati personali. È consentito, e per quanto tempo? |
| Contratti di integrazione | Sistemi esterni senza titolare, protocollo, autenticazione, SLA, versioning o ambiente di test | L'API dell'ERP non ha una sandbox. Come la testeremo? |
| Dati | Entità senza titolare, assenza di un piano di migrazione, coerenza tra servizi | Chi migra i record clienti legacy, e quando? |
| Sistema attuale e standard | Design che differiscono dal codice o dai pattern esistenti; nuove tecnologie senza ADR | Il design aggiunge un nuovo message broker; la piattaforma ne ha già uno. Esiste un ADR? |
| Operabilità | Assenza di monitoraggio, alerting, backup e restore, strategia di deployment o di rollback | Come facciamo il rollback di una migrazione di schema fallita? |
| Costi | Costi di esercizio o di licenza non stimati, o oltre il budget | Qual è il costo mensile di hosting al carico previsto? |
| Realizzabilità | Competenze mancanti nel team, stime che ignorano la complessità del design | Nessuno nel team ha mai usato questo framework in produzione. Spike o formazione? |
| Ipotesi nascoste | Affermazioni valide solo se qualcosa al di fuori del sistema è vero | Questo presuppone che l'identity provider del cliente supporti OIDC. Confermato da chi? |
