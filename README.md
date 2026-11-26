# DEEF Control

App gratuita per Android che mostra i dati dell'auto come quadranti, sul telefono e su **Android Auto**.
Pensata soprattutto per i motori **EA888** con centralina **Simos** (es. Audi S1, Golf R/GTI, Cupra, Leon, TT…), ma funziona anche con altre auto grazie ai PID personalizzati.

> ⚠️ **Avvertenze.** L'app è in sola lettura, ma è un progetto amatoriale e viene fornita "così com'è", senza garanzie.
> Usala a rischio tuo e non guardare lo schermo mentre guidi. Non è affiliata né approvata da Audi o dal gruppo Volkswagen.

## Funzioni

- Quadranti su telefono e su Android Auto (pannello, griglia o **schermo intero** con posizione e dimensione libere)
- 4 pagine con 3 quadranti ciascuna; stesso layout su tutte le pagine, esportabile e importabile
- "Pagina divisa": quadranti a sinistra e valori scritti a destra
- PID Simos già inclusi (pressione turbo, AFR, anticipo, knock, pressioni carburante, camme, coppia, olio…) e PID personalizzati
- Import di PID da file CSV in formato Torque
- Temperatura gas di scarico (EGT), cronometro drag (0-100, 100-200…) con il GPS del telefono
- Stima di potenza e coppia dalla pressione di sovralimentazione (indicativa)
- Collegamento con **ELM327 Bluetooth** oppure, in modo sperimentale, con il **dongle Simos ESP32 BLE** (sola lettura)

## Impostazioni di fabbrica

Alla prima apertura l'app parte già configurata per l'Audi S1: **schermo intero** su Android Auto, tre quadranti a distanza uguale, sfondo nero e cronometro **drag 100-200** attivo.
Le 4 pagine sono: **DEEF CONTROL** (EGT, pressione olio, temperatura olio), **Motore** (AFR, turbo, H2O), **Olio / EGT** e **Potenza** (giri, HP, Nm).
Puoi cambiare tutto dalle Impostazioni; il pulsante *Applica layout S1* ripristina questa configurazione.

## Requisiti

- Android 8.0 o successivo (non esiste versione per iPhone)
- Un adattatore OBD2: ELM327 Bluetooth, oppure il dongle ESP32 BLE per Simos
- Per l'uso in auto: Android Auto

## 1. Installazione

1. Scarica l'APK dalla pagina **Releases** di questo repository.
2. Aprilo dal telefono e consenti l'installazione da questa fonte.
3. Alla prima apertura concedi i permessi **Dispositivi nelle vicinanze** (Bluetooth) e **Posizione** (serve per il drag).

Se compare l'avviso di Play Protect ("app non verificata"), tocca *Altri dettagli → Installa comunque*.

## 2. Collegare l'adattatore OBD

### ELM327 Bluetooth

1. Inserisci l'adattatore nella presa OBD dell'auto e accendi il quadro.
2. Nelle impostazioni **Bluetooth** del telefono associa l'adattatore (di solito si chiama "OBDII" e il codice è `1234` o `0000`).
3. In DEEF vai su **Impostazioni → Adattatore OBD (Bluetooth già associato)** e tocca il tuo adattatore.
4. In **Protocollo OBD** scegli **CAN 11 bit 500k (Audi/VAG)**. Dopo averlo cambiato premi *Disconnetti* e *Connetti*.
5. Nella schermata **Dash** premi **Connetti**. In alto compare "Connesso · N letture/s".

### Dongle Simos ESP32 (BLE, sperimentale)

Funziona in sola lettura con i dongle che usano il firmware *esp32-isotp-ble-bridge* (provato con v1.09).

1. Accendi il quadro con il dongle inserito. **Non** associarlo dalle impostazioni Bluetooth del telefono.
2. In DEEF vai su **Impostazioni → Dongle BLE Simos (ESP32)** e attiva **"Usa il dongle BLE invece dell'ELM327"**.
3. Concedi il permesso Bluetooth se l'app lo chiede (pulsante *Concedi permesso*).
4. Scrivi la **password del dongle** (è quella del tuo dongle: se non l'hai cambiata è quella predefinita del produttore).
5. Tocca **Cerca dongle BLE** e scegli il dongle dall'elenco (di solito si chiama `BLE_TO_ISOTP20`).
6. Nella schermata **Dash** premi **Connetti**.

Controllo: in **Impostazioni → Diagnostica connessione → Visualizza registro** devono comparire "Password accettata" e "Firmware dongle: …".
Chiudi prima le altre app che usano il dongle (Simos App, SimosTools): il dongle accetta un solo collegamento alla volta.

## 3. Aggiungere i PID

I PID sono le "letture" da mostrare, per esempio pressione turbo, olio o EGT. Ci sono tre modi.

### a) PID già inclusi
Nella **Dash** tieni premuto su un quadrante (o tocca un quadro in *Impostazioni → Pagine e quadri*) e scegli il PID dall'elenco, con ricerca. Sono inclusi i PID standard e circa 40 PID Simos (categoria VAG).
In **Impostazioni** il pulsante *Applica layout S1* mette giri, turbo, H2O, olio, EGT e HP/Nm già pronti.

### b) Importare un file CSV (formato Torque)
1. Procurati un file CSV in formato Torque. Ne trovi per la tua auto cercando "PID Torque CSV" insieme a modello e motore, nei forum e nei gruppi dedicati, oppure usa quello di questo repository (`DEEF-PID-Simos.csv`).
2. Copia il file nella memoria del telefono.
3. In **Impostazioni → Importa → PID da CSV (Torque)** scegli il file.

Le colonne del file sono:

```
Name,ShortName,ModeAndPID,Equation,Min Value,Max Value,Units,Header
```

Esempio (temperatura olio Simos):

```
Temperatura olio,Olio,22202F,(((A*256)+B)-2731.4)/10,-40,200,°C,7E0
```

### c) Creare un PID a mano
In **Impostazioni → Nuovo PID personalizzato** compila:

- **Nome** e **Nome corto** (es. "Olio")
- **Unità** (es. °C, bar)
- **Comando** (es. `22202F`, oppure `010C` per un PID standard)
- **Header ECU** (per la Simos di solito `7E0`)
- **Formula** con A, B, C… che sono i byte della risposta, per esempio `(A*256+B)/4`
- **Min**, **Max** e **Allarme** (opzionale)

Tocca **Aggiungi PID**. Nelle formule puoi usare `+ - * /` e le parentesi; `S16(...)` converte un valore a 16 bit con segno.

## 4. Usarla su Android Auto

Le app installate da APK non compaiono in Android Auto con una semplice installazione. Servono questi passaggi.

### Opzioni sviluppatore di Android Auto

1. Apri **Android Auto** dalle impostazioni del telefono.
2. Tocca 7 volte su **Versione**, finché compare "Sei già uno sviluppatore".
3. Tocca i tre puntini in alto a destra → **Impostazioni sviluppatore** → attiva **Origini sconosciute**.

### Installazione con "attribuzione Google Play" (Shizuku + UnknownAuto)

Android Auto accetta le app con interfaccia a modelli, come DEEF, solo se risultano installate da Google Play.
Per ottenere questo effetto si usano due app esterne gratuite: **Shizuku** e **UnknownAuto**.

1. Scarica e installa le due app:
   - **Shizuku** (v13.6.0): [download](https://github.com/RikkaApps/Shizuku/releases/download/v13.6.0/shizuku-v13.6.0.r1086.2650830c-release.apk) · [tutte le versioni](https://github.com/RikkaApps/Shizuku/releases)
   - **UnknownAuto** (v1.6): [download](https://github.com/GitMag/UnknownAuto/releases/download/v1.6/UnknownAuto-1.6.apk) · [pagina del progetto](https://github.com/GitMag/UnknownAuto)

   I file non sono inclusi in questo repository: appartengono ai rispettivi autori.
2. Attiva le **opzioni sviluppatore** del telefono: Impostazioni → Info telefono → Informazioni software → tocca 7 volte *Numero build*.
3. Attiva il **Debug wireless**: Impostazioni → Opzioni sviluppatore → *Debug wireless* (il telefono deve essere collegato al Wi-Fi).
4. Avvia **Shizuku**:
   - apri Shizuku → *Avvia tramite debug wireless*;
   - in Opzioni sviluppatore → Debug wireless tocca *Associa dispositivo con codice di associazione*;
   - scrivi il codice di 6 cifre nella notifica di Shizuku e tocca *Avvia*;
   - deve comparire "Shizuku è in esecuzione".
5. Apri **UnknownAuto** (deve mostrare "Shizuku: installed"; consenti il permesso se lo chiede).
6. Tocca **Load APK or XAPK…** e scegli l'APK di DEEF Control.
7. Tocca **Install for Android Auto** e conferma.
8. Scollega e ricollega il telefono all'auto. Se DEEF non compare, apri Android Auto → **Personalizza avvio** e controlla che sia attivo.

Note:
- Se avevi già installato DEEF normalmente, **disinstallalo prima**.
- Dopo ogni **riavvio del telefono** Shizuku si ferma e va riavviato (passo 4), con il Wi-Fi acceso.
- Per aggiornare DEEF ripeti i passi 6 e 7 con il nuovo APK.
- Sui Samsung controlla che **Auto Blocker** (in italiano "Blocco automatico", in Impostazioni → Sicurezza e privacy) sia spento.

### Layout su Android Auto
In **Impostazioni → Layout su Android Auto** puoi scegliere *Schermo intero* (quadranti liberi), *Pannello* o *Griglia*.
In **Anteprima AA** puoi trascinare i quadranti, cambiarne la dimensione e distribuirli a distanza uguale.

## Problemi frequenti

| Problema | Cosa fare |
|---|---|
| Non compare in Android Auto | Origini sconosciute attivo, installazione con UnknownAuto, Personalizza avvio, riavvio del telefono |
| Valori "--" | Controlla la riga di stato in Dash, il registro (Impostazioni → Visualizza registro) e che l'adattatore sia quello giusto |
| Non si connette | Permessi Bluetooth concessi, adattatore associato (ELM327), nessun'altra app OBD collegata |
| Un PID non risponde | Controlla comando e header (Simos: `7E0`); se risponde "NO DATA" quel PID non esiste sulla tua centralina |
| Non si installa su Xiaomi | Permesso per la fonte dell'APK; in Opzioni sviluppatore attiva "Installa tramite USB" |

In **Impostazioni → Diagnostica Android Auto** tocca *Controlla* e usa *Copia* per allegare il testo a una segnalazione.

## Assistenza

Per domande o problemi scrivimi su Instagram: **[@federicodeef](https://instagram.com/federicodeef)**

## Sostieni il progetto

Se l'app ti è utile puoi offrirmi un caffè: [☕ Offrimi un caffè](https://www.paypal.me/federicodeef)

## Licenza

Copyright (C) 2026 DEEF ([@federicodeef](https://instagram.com/federicodeef))

DEEF Control è software libero, distribuito con licenza **GNU GPL versione 3** (vedi il file [LICENSE](LICENSE)).
Puoi usarlo, studiarlo, modificarlo e condividerlo, anche a pagamento, ma ogni versione modificata o derivata deve restare sotto la stessa licenza, con il codice sorgente a disposizione e l'indicazione dell'autore originale.
Il programma è fornito senza alcuna garanzia.
