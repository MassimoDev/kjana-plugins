# kjana-plugins

Plugin di Claude Code fatti da [Kjana](https://vpskjana.com). Per ora ce n'è uno.

## Ponte

Il Ponte serve a passare un lavoro dal tuo Claude Code a quello di un'altra
persona (un collaboratore, un cliente) e a riceverne la risposta. Ognuno usa il
proprio abbonamento, senza chiavi API.

Il cuore è **il pacchetto**: una cartella che si spiega da sola. Contiene
`PACCHETTO.md` (cosa ti mando, perché, cosa ti chiedo, cosa non fare, il
contesto, come rispondere), gli allegati e, se serve, un ramo di codice. Viaggia
come un solo file `.tar.gz`, col canale che usi di solito.

### Installare

In Claude Code:

```
/plugin marketplace add MassimoDev/kjana-plugins
/plugin install ponte@kjana
```

Poi apri una sessione nuova.

### Usare

- **Per mandare**, chiedi al tuo Claude Code per esempio «manda a Carlo il
  listino rivisto, gli chiedo di controllare i prezzi entro venerdì». La skill
  `consegna` scrive il pacchetto e lo controlla, poi ti mostra cosa c'è dentro
  e ti chiede l'ok. Alla fine ti dà il file da spedire e un messaggio pronto per
  chi lo riceve.
- **Per ricevere**, scarica il file e chiedi «cosa mi è arrivato?». La skill
  `ricevi` controlla l'archivio, lo fa leggere per primo a un lettore isolato e
  ti presenta un riassunto. Poi decidi tu se lavorarlo, solo leggerlo o
  rifiutarlo.
- **Senza il plugin** un pacchetto si apre lo stesso: doppio clic sul file,
  trascina in Claude Code la cartella che esce e scrivi «leggi PACCHETTO.md in
  questa cartella e dimmi cosa mi chiede».

### Cosa controlla

All'uscita, prima di creare l'archivio, `ponte prepara` rifiuta:

- chiavi e token riconoscibili (ne dice il file e la riga, mai il valore);
- i file `.env`, le chiavi private e i dump;
- `.claude/`, `CLAUDE.md`, `.mcp.json` e `.git/`;
- i collegamenti simbolici;
- i commenti HTML nascosti dentro `PACCHETTO.md`.

All'arrivo, `ponte apri`:

- rifiuta gli archivi che scrivono fuori dalla propria cartella, che contengono
  collegamenti o che superano 1 GB estratti;
- verifica le impronte SHA-256;
- toglie i permessi di esecuzione;
- mette in quarantena, rinominati, i file che cambierebbero il comportamento di
  Claude Code e quelli che il mittente non aveva elencato.

Il testo del pacchetto lo legge prima un sotto-agente senza strumenti, che
segnala le frasi rivolte a un assistente senza ripeterle. Nessun comando scritto
in un pacchetto viene eseguito senza il tuo ok.

Il codice arriva come ramo `ponte/<nome>`, senza checkout. Prima di guardarlo
vedi l'elenco dei file toccati.

### Cosa serve

`bash`, `tar` e `git`, con `shasum` o `sha256sum`: tutti già presenti su macOS
e Linux. Su Windows serve Git Bash. Per le prove serve anche `python3`.

### Prove

```
bash test/prova-r1.sh        # lo script, senza Claude: 62 verifiche
bash test/prova-lettore.sh   # il lettore isolato su un pacchetto ostile (serve l'accesso a Claude Code)
```

### A che punto è

È il **rilascio 1**: il formato, i controlli e le due skill. Il Ponte non
spedisce ancora niente da solo. La cassetta postale sul server, con gli avvisi
per email, arriverà nel rilascio 2.

## Licenza

MIT, vedi [LICENSE](LICENSE).
