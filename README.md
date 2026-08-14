# Favignana Top Spot

![Version](https://img.shields.io/badge/version-3.5.1%2B6-blue.svg)
![Flutter](https://img.shields.io/badge/Flutter-3.0+-02569B.svg?logo=flutter)
![Platform](https://img.shields.io/badge/platform-Android%20%7C%20iOS-lightgrey.svg)

> **L'app turistica intelligente per scoprire le migliori spiagge di Favignana in base al vento**

## Panoramica

Favignana Top Spot è un'applicazione Flutter che aiuta i turisti e i residenti a trovare le spiagge ideali dell'isola di Favignana in base alle condizioni meteorologiche del momento. L'app analizza la direzione e la velocità del vento in tempo reale e indica quali spiagge sono protette e quali sono esposte.

## Funzionalità Principali

### 1. Analisi Meteo in Tempo Reale e Previsioni

- Aggiornamento delle condizioni meteo con cache locale
- Controllo automatico ogni 30 minuti, con riutilizzo dei dati recenti
- Visualizzazione velocità e direzione del vento, temperatura e umidità
- Nomenclatura tradizionale italiana dei venti (Tramontana, Grecale, Levante, ecc.)
- **Sistema di Previsione**: possibilità di selezionare e visualizzare le condizioni meteo per l'ora corrente ("ORA"), tra 6 ore ("6 ORE") e tra 24 ore ("24 ORE").
- **Schermetti informativi**: due nuovi pannelli interattivi sotto la mappa per selezionare l'orizzonte temporale e visualizzare i relativi dati di temperatura e umidità.
- Il sistema aggiorna automaticamente lo stato di tutte le spiagge sulla mappa (verde, giallo, rosso) in base alla fascia oraria selezionata.

### 2. Sistema "Top Spot"

Ogni spiaggia viene classificata con un sistema a semaforo:

| Colore | Stato | Descrizione |
|--------|-------|-------------|
| 🟢 Verde | Mare Calmo | Spiaggia protetta, condizioni ideali |
| 🟡 Giallo | Accettabile | Possibile qualche onda |
| 🔴 Rosso | Mare Mosso | Spiaggia esposta, sconsigliata |

### 3. Mappa Interattiva

- Visualizzazione di tutti i 17 spot sulla mappa dell'isola
- Marker colorati in base allo stato del mare
- Animazione delle particelle del vento
- **Zoom**: bottone in basso a sinistra per ingrandire la mappa 2x
- **Pan**: quando zoommato, trascina per navigare
- **Tap sugli spot**: tocca un marker per portare in evidenza la scheda corrispondente, con centratura adattiva e cornice pulsante rinforzata, sovrapposta al perimetro senza modificare le dimensioni della card
- **Condivisione spot**: ogni card può generare un messaggio localizzato con stato attuale, vento, accessibilità e segnaposto Google Maps tramite il foglio di condivisione del dispositivo

### 4. Navigazione GPS

- Bottone "Portami qui" su ogni scheda spiaggia
- Apertura di Google Maps con un segnaposto GPS, senza avvio automatico del percorso
- Schede spiaggia con grado di accessibilità turistico: facile, moderato o difficile

### 5. Localizzazione

- Supporto multilingua: Italiano, Inglese, Spagnolo, Francese e Tedesco
- Rilevamento automatico della lingua del dispositivo
- Fallback automatico su Inglese se la lingua del telefono non è tra quelle supportate
- Menu in alto a destra per cambiare lingua e aprire la privacy direttamente dall'app

### 6. Sistema Pubblicitario

- Banner dinamici caricati da configurazione remota
- Due tipologie di banner (sticky e native)
- Programmazione oraria dei banner

---

## Architettura del Progetto

```text
lib/
├── main.dart                 # Entry point
├── l10n/
│   └── app_translations.dart # Stringhe multilingua IT/EN/ES/FR/DE
├── models/
│   ├── beach.dart            # Modello spiaggia + dati statici
│   └── ad.dart               # Modello pubblicità
├── services/
│   ├── weather_service.dart  # Gestione dati meteo e cache
│   └── ad_service.dart       # Caricamento banner da Google Drive
├── utils/
│   └── wind_logic.dart       # Algoritmo calcolo stato spiagge
└── screens/
    ├── home_screen.dart      # Schermata principale
    └── components/
        ├── island_map.dart   # Widget mappa con zoom
        ├── beach_card.dart   # Card singola spiaggia
        └── ad_banner.dart    # Widget banner pubblicitario
```

---

## I 17 Spot di Favignana

| # | Nome | Esposizione tecnica | Accessibilità | Coordinate GPS |
|---|------|---------------------|---------------|----------------|
| 1 | Cala Rossa | NE | Difficile | 37.922464, 12.363907 |
| 2 | Cala San Nicola | NE | Facile | 37.935022, 12.346703 |
| 3 | Scalo Cavallo | NE | Difficile | 37.930894, 12.349999 |
| 4 | Bue Marino | E | Difficile | 37.917222, 12.369920 |
| 5 | Cala Azzurra | S | Facile | 37.908715, 12.361280 |
| 6 | Punta Marsala | SE | Moderato | 37.907444, 12.366500 |
| 7 | Lido Burrone | S | Facile | 37.919028, 12.338167 |
| 8 | Grotta Perciata | S | Moderato | 37.913322, 12.346139 |
| 9 | Spiaggia Marasolo | S | Moderato | 37.919326, 12.320892 |
| 10 | Cala Preveto | S | Difficile | 37.918953, 12.302630 |
| 11 | Spiaggia Praia | N | Facile | 37.929608, 12.325196 |
| 12 | Cala Faraglioni | N | Moderato | 37.954491, 12.306777 |
| 13 | Cala Trapanese | N | Difficile | 37.953404, 12.308071 |
| 14 | Cala del Pozzo | NW | Facile | 37.942171, 12.287885 |
| 15 | Cala Rotonda | W | Facile | 37.923715, 12.284060 |
| 16 | Cala Grande | W | Facile | 37.930833, 12.280389 |
| 17 | Punta Sottile | W | Moderato | 37.935420, 12.273359 |

---

## Logica del Vento (Wind Engine)

L'algoritmo in `wind_logic.dart` determina lo stato di ogni spiaggia:

```dart
// Vento debole (<= 6 km/h) = sempre VERDE
// Altrimenti:
// - Vento da TERRA (offshore > 130°) = VERDE fino a 28 km/h, GIALLO fino a 35 km/h
// - Vento LATERALE/FRONTALE (<= 130°) = GIALLO fino a 18 km/h, ROSSO oltre
// - Tetto ordinario: sopra 38 km/h sempre ROSSO
// - South Shelter (Cala Azzurra, Lido Burrone, Grotta Perciata, Spiaggia Marasolo):
//   N/NW/W = VERDE fino a 28 km/h, GIALLO fino a 40 km/h
//   E/SE/S/SW = GIALLO fino a 25 km/h
// - North Shelter (Spiaggia Praia):
//   S/SE/SW = VERDE fino a 28 km/h, GIALLO fino a 40 km/h
//   N/NE/NW = VERDE fino a 18 km/h, GIALLO fino a 28 km/h
// - West Shelter (Cala Grande, Cala Rotonda, Punta Sottile):
//   E/SE/NE/N = VERDE fino a 28 km/h, GIALLO fino a 40 km/h
//   W/NW/SW/S = VERDE fino a 15 km/h, GIALLO fino a 25 km/h
```

### Rosa dei Venti Italiana

| Direzione | Nome | Gradi |
|-----------|------|-------|
| N | Tramontana | 0° / 360° |
| NE | Grecale | 45° |
| E | Levante | 90° |
| SE | Scirocco | 135° |
| S | Ostro | 180° |
| SW | Libeccio | 225° |
| W | Ponente | 270° |
| NW | Maestrale | 315° |

---

## Sistema Pubblicitario - Guida Operativa

### Descrizione

L'app supporta banner pubblicitari dinamici caricati da un file JSON ospitato su Google Drive. Questo permette di aggiornare le pubblicità senza rilasciare una nuova versione dell'app.

### URL Configurazione

Il file JSON è scaricato da:

```text
https://********
```

Per cambiare il file:

1. Modifica l'ID del file in `lib/services/ad_service.dart` alla riga 16-17

### Formato JSON

Il file deve avere questa struttura:

```json
{
  "banners": [
    {
      "id": "banner_001",
      "clientName": "Nome Cliente",
      "imageUrl": "https://esempio.com/immagine.jpg",
      "linkUrl": "https://esempio.com/landing",
      "type": "STICKY_BOTTOM",
      "timeSlots": ["ALL_DAY"]
    },
    {
      "id": "banner_002",
      "clientName": "Ristorante Mare",
      "imageUrl": "https://esempio.com/ristorante.jpg",
      "linkUrl": "https://esempio.com/ristorante",
      "type": "NATIVE",
      "timeSlots": ["LUNCH", "DINNER"]
    }
  ]
}
```

### Campi Obbligatori

| Campo | Tipo | Descrizione |
|-------|------|-------------|
| `id` | string | Identificatore univoco del banner |
| `clientName` | string | Nome del cliente/sponsor |
| `imageUrl` | string | URL dell'immagine del banner (HTTPS) |
| `linkUrl` | string | URL di destinazione al tap sul banner |
| `type` | string | Tipo di banner (vedi sotto) |
| `timeSlots` | array | Fasce orarie di visualizzazione |

### Tipi di Banner (type)

| Valore | Posizione | Aspect Ratio | Descrizione |
|--------|-----------|--------------|-------------|
| `STICKY_BOTTOM` | Fondo schermo | 6:1 | Banner fisso sempre visibile |
| `NATIVE` | Lista spiagge | 16:9 | Appare dopo la 3ª spiaggia |

### Fasce Orarie (timeSlots)

| Valore | Orario | Uso tipico |
|--------|--------|------------|
| `ALL_DAY` | 00:00-24:00 | Banner sempre visibile |
| `MORNING` | 06:00-11:30 | Bar, escursioni, noleggi |
| `LUNCH` | 11:30-15:00 | Ristoranti, beach bar |
| `AFTERNOON` | 15:00-19:00 | Aperitivi, gelaterie, noleggi per il giorno dopo |
| `DINNER` | 19:00-24:00 | Cene, eventi, nightlife |

È possibile combinare più fasce: `["MORNING", "LUNCH"]`

### Dimensioni Immagini Consigliate

| Tipo | Larghezza | Altezza | Aspect Ratio |
|------|-----------|---------|--------------|
| STICKY_BOTTOM | 600px | 100px | 6:1 |
| NATIVE | 640px | 360px | 16:9 |

### Comportamento in Caso di Errore

Se il caricamento del JSON fallisce (rete assente, timeout, errore):

- L'app non mostra alcun banner e non occupa spazio vuoto nella UI
- Nessun crash o errore visibile all'utente
- Timeout: 10 secondi
- Il JSON remoto viene ricaricato solo al primo controllo, al cambio fascia oraria o dopo 6 ore
- Se l'immagine di un banner non viene caricata, il relativo widget resta invisibile

### Come Aggiornare i Banner

1. **Prepara le immagini**
   - Formato: JPG o PNG
   - Dimensioni: vedi tabella sopra
   - Ospita su server con HTTPS (es. Google Drive, Cloudinary)

2. **Ottieni URL diretti**
   - Per Google Drive: usa formato `https://drive.google.com/uc?export=download&id=FILE_ID`

3. **Modifica il JSON**
   - Aggiorna il file JSON su Google Drive
   - I banner si aggiorneranno automaticamente nelle app

4. **Test**
   - Per un controllo immediato, cancella i dati dell'app oppure attendi il cambio fascia oraria o il controllo dopo 6 ore
   - Verifica che le immagini si carichino correttamente
   - Testa il tap per verificare i link

---

## Gestione Dati Meteo

### Dati utilizzati

- `windspeed` (km/h)
- `winddirection` (gradi, 0-360)
- `temperature_2m`
- `relative_humidity_2m`

### Fallback Meteo

Se l'aggiornamento non è disponibile per rete mobile debole, timeout o errore temporaneo:

- l'app effettua un solo tentativo con timeout di 10 secondi;
- se esistono dati meteo salvati in locale o gia caricati nella sessione, li riutilizza come cache e li marca come non aggiornati;
- se non esiste cache, usa dati neutri solo per mantenere la UI operativa e mostrare l'avviso;
- in entrambi i casi la Home mostra un avviso professionale sotto la mappa e il badge diventa "Meteo non aggiornato".

I colori degli spot in fallback sono quindi indicativi e non devono essere letti come dato meteo live.

### Ottimizzazione degli aggiornamenti

Per ridurre trasferimenti, tempi di attesa e consumo batteria:

- i dati meteo vengono salvati in cache locale persistente con `shared_preferences`;
- apertura app, timer e ritorno in primo piano usano la cache se l'ultima lettura meteo scaricata ha meno di 45 minuti;
- il pulsante refresh manuale avvia una sincronizzazione effettiva solo se la cache ha più di 15 minuti e comunque non più di 2 volte in 1 ora per dispositivo;
- i banner remoti vengono salvati in cache locale e ricontrollati solo al primo controllo, al cambio fascia oraria o dopo 6 ore;
- meteo e banner sono caricati da servizi separati: un errore dei banner non blocca il calcolo degli spot.

---

## Requisiti di Sistema

- **Flutter SDK**: >= 3.0.0 < 4.0.0
- **Dart SDK**: >= 3.0.0
- **Android**: API 24+ (Android 7.0 Nougat)
- **iOS**: 12.0+

## Dipendenze

```yaml
dependencies:
  flutter_localizations: sdk
  http: ^1.1.0           # Networking
  url_launcher: ^6.2.0   # Apertura Google Maps
  share_plus: ^10.1.4    # Foglio di condivisione di sistema
  cached_network_image: ^3.3.0  # Cache immagini
  shared_preferences: ^2.2.3    # Cache locale persistente
  intl: any              # Internazionalizzazione
```

---

## Build e Deploy

### Sviluppo

```bash
flutter pub get
flutter run
```

### Build Release Android

```bash
flutter build apk --release
```

L'APK sarà in: `build/app/outputs/flutter-apk/app-release.apk`

### Build Release iOS

```bash
flutter build ios --release
```

---

## Assets Richiesti

### Immagini Spiagge

Posizione: `assets/spot/`

| File | Spiaggia |
|------|----------|
| `cala_rossa.jpg` | Cala Rossa |
| `cala_san_nicola.jpg` | Cala San Nicola |
| `scalo_cavallo.jpg` | Scalo Cavallo |
| `buemarino.jpg` | Bue Marino |
| `cala_azzurra.jpeg` | Cala Azzurra |
| `punta_marsala.jpg` | Punta Marsala |
| `lido_burrone.jpg` | Lido Burrone |
| `grotta_perciata.jpg` | Grotta Perciata |
| `spiaggia_marasolo.jpg` | Spiaggia Marasolo |
| `cala_preveto.jpg` | Cala Preveto |
| `spiaggia_praia.jpeg` | Spiaggia Praia |
| `cala_faraglioni.jpg` | Cala Faraglioni |
| `cala_trapanese.jpg` | Cala Trapanese |
| `cala_del_pozzo.jpg` | Cala del Pozzo |
| `cala_rotonda_new.jpg` | Cala Rotonda |
| `cala_grande.jpg` | Cala Grande |
| `punta_sottile.jpg` | Punta Sottile |

### Mappa

| File | Descrizione |
|------|-------------|
| `assets/mappaOK.png` | Mappa dell'isola per il widget |
| `assets/logo.png` | Logo app per launcher icon |

---

## Debug e Strumenti

### debug_map.html

File HTML per calibrare le coordinate (mapX, mapY) delle spiagge sulla mappa.

Uso:

1. Apri `debug_map.html` in un browser
2. Trascina i marker per riposizionarli
3. Copia le coordinate generate
4. Aggiorna `lib/models/beach.dart`

---

## Versione

**Versione attuale:** 3.5.1+6

## Localizzazione

L'app usa un sistema i18n interno basato su `AppTranslations`.

- Lingue disponibili: Italiano, Inglese, Spagnolo, Francese, Tedesco
- La lingua viene scelta automaticamente in base a quella del dispositivo
- Se la lingua del dispositivo non è supportata, l'app mostra l'Inglese
- Dal menu in alto a destra puoi aprire la selezione lingua senza uscire dalla home

## Contatti

Email progetto: [favignanatopspot@gmail.com](mailto:favignanatopspot@gmail.com)

---

## Licenza

Tutti i diritti riservati.
