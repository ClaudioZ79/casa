# Casa — versione web

Versione web dell'app Casa, pensata per essere installata sull'iPhone dalla schermata di Safari
(**Condividi → Aggiungi a Home**), senza Xcode né Modalità sviluppatore.

Nessuna dipendenza esterna: è un solo file HTML con CSS e JavaScript inline.

## Dati

- I dati sono salvati nel browser del telefono (`localStorage`, chiave `casa.v1`).
- **Esporta JSON** e **Importa JSON** nella scheda Generale fanno il backup a mano. Il formato è lo stesso
  dell'app Swift (`attivita` e `scorte`), quindi l'import dalla dashboard web si fa con lo stesso file.
- Safari può cancellare i dati di un sito non aperto per alcune settimane: fai un export ogni tanto.
  La sincronizzazione su Google Drive è il prossimo passo.

## Condivisione tra Claudio e Monica (Firebase)

Le liste possono stare in un database condiviso (Cloud Firestore, progetto Google Cloud/Firebase `casa-511922`,
piano gratuito Spark, località `eur3`). Dalla scheda Generale → **Condivisione** → *Accedi con Google*:
chi accede vede le stesse liste e le modifiche arrivano subito agli altri telefoni.

- **Chi può entrare**: solo i due account indicati nelle regole di sicurezza del database
  (Claudio e Monica: gli indirizzi stanno nelle regole, non in questo file). Le regole sono pubblicate nella console Firebase
  (*Firestore → Regole*); per cambiare le persone vanno modificate lì.
- **Come sono salvati i dati**: un documento per voce nelle collezioni `attivita` e `scorte`.
  Si manda solo ciò che cambia, quindi se i due modificano cose diverse non si sovrascrivono.
- **Primo accesso su un telefono**: se sul telefono ci sono già dati, l'app chiede se aggiungerli alle liste condivise.
- **Senza connessione**: l'app funziona e ricorda le modifiche; la prima volta che si riapre online l'app
  propone di aggiungerle alle liste condivise.
- **Chiave API in `index.html`**: è l'identificativo pubblico dell'app web. La protezione sono le regole del database.
- **Domini autorizzati** (Firebase → Authentication → Impostazioni): `claudioz79.github.io` e `localhost`.
- **Accesso Google**: l'app Google Cloud è in stato *Test*, quindi possono accedere solo gli utenti di prova
  (i due indirizzi sopra). Per aggiungerne altri: Google Auth Platform → Pubblico → Utenti di prova.
- **Importa JSON** con la condivisione attiva sostituisce le liste per tutti.
- Con la condivisione attiva la sezione Google Drive sparisce, per evitare di sovrascrivere le liste con una copia vecchia.

## Decidere cosa va nella lista della spesa

Su ogni scorta (scheda Scorte) ci sono due pulsanti:

- **Aggiungi alla spesa**: mette l'articolo in lista adesso, anche se la scorta non è bassa. Se è già in lista
  mostra "In lista della spesa"; se l'avevi tolto a mano, lo rimette.
- **Non aggiungere**: l'articolo non entra più da solo in lista e le sue voci ancora da comprare spariscono.
  Premuto di nuovo torna automatico. "Aggiungi alla spesa" funziona comunque, una volta sola.

Il campo `nonInLista` compare sulla scorta solo quando è attivo. Le voci aggiunte a mano non seguono la quantità
della scorta, quelle automatiche sì.

## Correggere i nomi

Toccando il nome di un impegno, di una scorta o di una voce della spesa la riga diventa un campo con **Salva** e
**Annulla** (anche Invio e Esc). Gli spazi in più vengono tolti e il nome non può restare vuoto.

- Rinominare una **scorta** cambia il nome anche delle sue voci nella lista della spesa (anche quelle già comprate).
- Rinominare dalla **Spesa** una voce collegata a una scorta cambia il nome della scorta; una voce libera cambia solo se stessa.
- Se la correzione arriva dall'altro telefono, le voci della spesa si allineano da sole.
- Mentre stai scrivendo, un aggiornamento in arrivo non cancella il testo.

## Impegni che si ripetono

Nel modulo "Nuova attività" la scelta **Si ripete** (Mai, Ogni giorno, Ogni settimana, Ogni mese) fa tornare
l'impegno da solo. Si può cambiare anche dopo, toccando la scritta sotto il titolo.

- Campi aggiunti all'impegno (solo se si ripete): `ripeti`, `inizio` (la prima volta, AAAA-MM-GG) e `fattaIl`
  (l'ultima volta che l'hai segnato fatto). Le date in cui torna si calcolano da `inizio`: restano agganciate
  allo stesso giorno della settimana o del mese (il 31 diventa l'ultimo giorno nei mesi più corti).
- Segnarlo fatto vale per il giro in corso: va tra le *Completate* con "torna il …" e al giro dopo è di nuovo da fare.
  Se salti un giro non si accumulano: conta solo l'ultimo.
- Se la prima volta è nel futuro, sta in *In programma* finché non arriva la data.
- "Rimuovi le completate" non tocca gli impegni ripetuti.
- Il cambio di giorno si controlla ogni minuto e quando si riapre l'app, quindi a mezzanotte la lista si aggiorna da sola.
- Non ci sono notifiche: l'app non può avvisarti, i ripetuti compaiono quando la apri.

## Google Drive (copia personale, senza condivisione)

La scheda Generale ha una sezione **Google Drive**: *Collega*, *Carica su Drive*, *Scarica da Drive*, *Scollega*.
Il file `casa.json` finisce in `appDataFolder`, una cartella nascosta visibile solo a questa app (ambito `drive.appdata`).
Il formato è lo stesso dell'app Swift, quindi i due client leggono lo stesso file.

Per attivarla serve un ID client di tipo **Applicazione web** nel progetto Google Cloud `casa-511922`
(quello di tipo iOS non funziona sul web):

1. Google Cloud → **Google Auth Platform → Client → Crea client → Applicazione web**.
2. In **Origini JavaScript autorizzate** inserisci solo l'origine, senza percorso:
   `https://<utente>.github.io` (e `http://localhost:8080` per provare in locale).
3. Copia l'ID client e incollalo in `GOOGLE_CLIENT_ID` in `index.html` (già fatto per il client "Casa Web").
   Il segreto client mostrato da Google non serve e non va mai scritto nel codice.

Note:
- L'app resta in stato **Test**: solo gli utenti di prova possono accedere, e Google mostra l'avviso "app non verificata".
- Il token di accesso dura circa un'ora e non viene salvato: al riavvio serve un nuovo tocco su *Carica* o *Scarica*.
- *Scarica da Drive* sostituisce i dati di questo dispositivo.

## Pubblicazione su GitHub Pages

GitHub Pages pubblica la cartella radice del ramo oppure la cartella `docs/`. Per usarla:

1. Crea un repository su GitHub (per esempio `casa`).
2. Sposta il contenuto di questa cartella in `docs/` (oppure usala come radice).
3. In **Settings → Pages**, scegli il ramo e la cartella scelta.
4. Apri l'URL che GitHub ti mostra (`https://<utente>.github.io/casa/`) su Safari dell'iPhone.
5. **Condividi → Aggiungi a Home**.

## Da fare

- Icona dell'app: `icon-180.png` (apple-touch-icon), `icon-192.png` e `icon-512.png` (manifest) sono
  ricavate dall'icona dell'app Swift. Se cambi l'icona, rigenerale con `sips -z <px> <px>`.
  Dopo averla cambiata, su iPhone rimuovi la vecchia icona dalla Home e aggiungila di nuovo.
- Icone in stile iOS: al momento le stanze usano emoji. Si possono sostituire con SVG.
- Sincronizzazione con Google Drive (OAuth, ID client da creare su Google Cloud).
