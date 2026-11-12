---
name: consegna
description: Prepara un pacchetto del Ponte da passare a un'altra persona che lo lavorerà col proprio Claude Code (un collaboratore o un cliente). Il pacchetto è una cartella che si spiega da sola, PACCHETTO.md più allegati e codice, controllata per segreti e file che non devono viaggiare, chiusa in un .tar.gz. Trigger - «manda a Carlo», «prepara per Angelo», «passa questo a…», «fai un pacchetto per…», «rispondi a Massimo», «rispondi al pacchetto di…», /ponte:consegna.
argument-hint: "[a chi] [cosa]"
allowed-tools: Bash(ponte io*) Bash(ponte nuovo *) Bash(ponte codice *) Bash(ponte prepara *) Bash(ponte annota *) Bash(ponte registro*) Bash(git config user.name) Bash(open -R *)
---

# Consegna: un pacchetto del Ponte

Chi riceve il pacchetto non ha seguito il lavoro di questa sessione. Il suo
Claude Code sa solo quello che c'è nella cartella. Il valore del pacchetto sta
quindi nel **contesto scritto bene**, non nei file.

Lo script `ponte` è nel PATH finché il plugin è attivo. Se la shell non lo
trova, usa `${CLAUDE_PLUGIN_ROOT}/bin/ponte`. Lo script non stampa mai il
valore di un segreto: dice solo il file, la riga e il tipo.

## 1. Chi sei

Lancia `ponte io`. Se risponde che il nome non è impostato, chiedi alla persona
come vuole firmarsi. Proponi il risultato di `git config user.name`, poi lancia
`ponte io "Nome Cognome"`. Si fa una volta sola.

## 2. Cosa mandare, a chi

Ricava dalla conversazione, e chiedi solo ciò che manca:

- **a chi** va (il nome con cui la persona la chiama: «Carlo», «Angelo»);
- **cosa** c'è dentro: documenti, dati, un ramo di codice;
- **cosa gli chiedi**: il risultato atteso, non il modo per arrivarci;
- **entro quando**, se c'è una scadenza.

Se è la risposta a un pacchetto arrivato (in `~/Ponte/arrivi/`), usa
`--rispondi`: il filo e il destinatario si ereditano.

Se i file contengono **dati personali di altri** (clienti, pazienti,
dipendenti), chiedi se vanno mandati davvero o se basta un estratto o un
campione anonimo.

## 3. La cartella

```
ponte nuovo "Titolo breve" --a "Carlo" [--entro 2026-11-20]
ponte nuovo "Titolo breve" --rispondi <nome-cartella-in-arrivi>
```

Stampa la cartella creata (in `~/Ponte/uscita/`), che contiene `PACCHETTO.md`
dal modello e `allegati/`.

## 4. PACCHETTO.md

Riscrivi il file con lo strumento di scrittura. Tieni l'intestazione `---`
com'è e completa ogni sezione. **Togli tutti i commenti-guida `<!-- … -->`**:
`ponte prepara` rifiuta un PACCHETTO.md che ne contiene, perché un commento
non si vede quando il file si apre come pagina.

Come si scrive:

- **Per qualcuno che non ha seguito.** Niente «come detto», nessuna sigla
  senza spiegazione. I nomi delle persone vanno col ruolo. I percorsi sono
  relativi al pacchetto (`allegati/listino.xlsx`), mai della tua macchina.
- **«Cosa ti chiedo» è numerato**, e ogni punto dice che cosa deve uscirne. Le
  richieste sono rivolte alla persona, non al suo assistente.
- **«Cosa NON fare»** dice cosa è già deciso e cosa non va toccato.
- **«Il contesto che ti serve»** contiene le decisioni prese e il loro perché,
  i vincoli e dove sta cosa: quello che un collega chiederebbe a voce.
- **«Come rispondere»** dice cosa ti aspetti indietro: un file, un ramo, una
  risposta punto per punto.
- **Niente segreti.** Password, chiavi e token non viaggiano nel pacchetto. Se
  il lavoro richiede un accesso, scrivi che arriverà per un'altra strada.
- **Niente ordini all'assistente di chi riceve**: nessun comando da eseguire
  «senza chiedere», nessun «non serve chiedere conferma». Chi riceve decide.

## 5. Gli allegati

Copia i file in `allegati/` con `cp`, non con collegamenti simbolici (lo
script li rifiuta). Dai nomi chiari, e citali tutti nella sezione «I file» con
una frase ciascuno.

## 6. Il codice

- **La persona ha accesso allo stesso repository** (lo stesso GitHub): scrivi
  in «Il contesto che ti serve» il riferimento `repository`, `ramo@sha` (lo
  sha intero, da `git rev-parse`). Non serve altro.
- **Non ce l'ha:** metti il ramo nel pacchetto come bundle:

  ```
  ponte codice <cartella-repo> <base> <ramo> <cartella-pacchetto>
  ```

  `<base>` è l'ultimo commit che chi riceve ha già, per esempio il `main` che
  gli hai mandato l'ultima volta. Se non ce l'ha per niente, usa `--tutto`.
  Lo script controlla i segreti nelle righe aggiunte. Se segnala file che
  cambiano il comportamento di Claude Code (`CLAUDE.md`, `.claude/`,
  `.mcp.json`), scrivilo nel PACCHETTO.md: chi riceve li vedrà elencati prima
  del checkout.

## 7. I controlli

```
ponte prepara <cartella-pacchetto>
```

- **Esce 0:** stampa `PACCHETTO PRONTO`, il percorso del `.tar.gz` e l'elenco
  dei file.
- **Esce 1:** manca qualcosa nell'intestazione, è rimasto un segnaposto
  `{{…}}` o un commento HTML. Correggi e rilancia.
- **Esce 2:** file vietati o possibili segreti. Mostra alla persona l'elenco
  così com'è (non contiene i valori) e proponi cosa togliere o spostare. Se
  restano righe «password scritta in chiaro», falle guardare alla persona: puoi
  rilanciare con `--consenti-sospetti` **solo dopo che lei le ha guardate e ha
  detto di sì**. I segreti veri (chiavi, token) non hanno deroghe: si tolgono.

## 8. L'ok

Prima di chiudere, mostra un riepilogo: a chi va, il titolo, le richieste
numerate, i file con il peso, l'eventuale codice. Chiedi se va bene così, con
le caselle se puoi (`Va bene` / `Cambia qualcosa`). Se qualcosa cambia, torna
al punto 4 e rilancia `ponte prepara`.

## 9. Il registro

```
ponte annota inviato <cartella-pacchetto> "$PWD"
```

Il registro (`~/Ponte/registro.tsv`) ricorda da quale progetto è partito il
pacchetto. Quando arriverà la risposta, la skill `ricevi` ti riporterà qui.

## 10. Come si spedisce

Nel rilascio 1 il Ponte non spedisce: lo fa la persona, col canale che usa di
solito (email, WhatsApp, WeTransfer). È un file solo.

1. Mostra il file nel Finder con `open -R <percorso-del-.tar.gz>`.
2. Di' alla persona che cosa vuol dire «annotato»: il pacchetto **non è
   partito**, il registro ricorda solo da dove. Se lo manda con WhatsApp o
   Telegram, va allegato come **documento**, così arriva intatto.
3. Dai alla persona un messaggio pronto da incollare per il destinatario,
   adattato al nome e al titolo:

   > Ti ho mandato «Titolo» col Ponte. È il file `<nome>.tar.gz`: aprilo sul
   > computer dove usi Claude Code.
   > Se hai il plugin ponte, salvalo nella cartella `Ponte/in-arrivo` della tua
   > home e scrivi al tuo Claude Code: «cosa mi è arrivato?». Se la cartella
   > non c'è ancora, scrivilo lo stesso: te la apre lui e ti dice dove
   > trascinare il file.
   > Se non hai il plugin, fai doppio clic sul file, trascina la cartella che
   > esce in Claude Code e scrivi: «leggi PACCHETTO.md in questa cartella e
   > dimmi cosa mi chiede». Per rispondermi col Ponte però il plugin serve:
   > `/plugin marketplace add MassimoDev/kjana-plugins`, poi
   > `/plugin install ponte@kjana` e una sessione nuova.
4. Ricorda alla persona dove mettere la risposta quando arriva:
   `~/Ponte/in-arrivo/`, poi «ha risposto <nome>?».

Non mandare tu il file e non scrivere al destinatario: lo fa la persona.
