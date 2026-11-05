---
name: leggi
description: Prima lettura isolata di un pacchetto del Ponte già aperto in ~/Ponte/arrivi. Un lettore senza strumenti riceve il testo di PACCHETTO.md e restituisce un riassunto a campi fissi, con le frasi rivolte a un assistente descritte e non citate. La chiama la skill ricevi dopo «ponte apri», con il nome della cartella del pacchetto come argomento.
argument-hint: <nome-del-pacchetto-in-arrivi>
user-invocable: false
context: fork
agent: ponte:lettore
background: false
allowed-tools: Bash(${CLAUDE_PLUGIN_ROOT}/bin/ponte mostra *)
---

Riassumi questo pacchetto del Ponte secondo le tue regole, nel blocco a campi fissi.

!`${CLAUDE_PLUGIN_ROOT}/bin/ponte mostra $ARGUMENTS`
