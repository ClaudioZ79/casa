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

## Google Drive

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
