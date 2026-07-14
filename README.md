# Favignana Top Spot

![Version](https://img.shields.io/badge/version-3.1.2-blue.svg)
![Flutter](https://img.shields.io/badge/Flutter-3.0+-02569B.svg?logo=flutter)
![Platform](https://img.shields.io/badge/platform-Android%20%7C%20iOS-lightgrey.svg)

> **L'app turistica intelligente per scoprire le migliori spiagge di Favignana in base al vento**

## Panoramica

Favignana Top Spot è un'applicazione Flutter che aiuta i turisti e i residenti a trovare le spiagge ideali dell'isola di Favignana in base alle condizioni meteorologiche del momento. L'app analizza la direzione e la velocità del vento in tempo reale e indica quali spiagge sono protette e quali sono esposte.

## Funzionalità Principali

### 1. Analisi Meteo in Tempo Reale e Previsioni

- Recupero dati meteo dall'API Open-Meteo
- Aggiornamento automatico ogni 30 minuti
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

- Visualizzazione di tutte le 15 spiagge sulla mappa dell'isola
- Marker colorati in base allo stato del mare
- Animazione delle particelle del vento
- **Zoom**: bottone in basso a sinistra per ingrandire la mappa 2x
- **Pan**: quando zoommato, trascina per navigare
- **Tap sugli spot**: tocca un marker per scorrere alla scheda corrispondente

### 4. Navigazione GPS

- Bottone "Portami qui" su ogni scheda spiaggia
- Apertura automatica di Google Maps con destinazione

### 5. Localizzazione

- Supporto multilingua: Italiano, Inglese, Francese e Tedesco
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
│   └── app_translations.dart # Stringhe multilingua IT/EN/FR/DE
├── models/
│   ├── beach.dart            # Modello spiaggia + dati statici
│   └── ad.dart               # Modello pubblicità
├── services/
│   ├── weather_service.dart  # API Open-Meteo
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

## Le 15 Spiagge di Favignana

| # | Nome | Esposizione | Coordinate GPS |
|---|------|-------------|----------------|
| 1 | Cala Rossa | NE | 37.922464, 12.363907 |
| 2 | Cala San Nicola | NE | 37.935022, 12.346703 |
| 3 | Scalo Cavallo | NE | 37.930894, 12.349999 |
| 4 | Bue Marino | E | 37.917222, 12.369920 |
| 5 | Cala Azzurra | S | 37.908715, 12.361280 |
| 6 | Punta Marsala | SE | 37.907304, 12.366618 |
| 7 | Lido Burrone | S | 37.918272, 12.338202 |
| 8 | Cala Preveto | S | 37.918953, 12.302630 |
| 9 | Spiaggia Praia | N | 37.929608, 12.325196 |
| 10 | Cala Faraglioni | N | 37.954491, 12.306777 |
| 11 | Cala Trapanese | N | 37.953404, 12.308071 |
| 12 | Cala del Pozzo | NW | 37.942171, 12.287885 |
| 13 | Cala Rotonda | W | 37.923715, 12.284060 |
| 14 | Cala Grande | W | 37.931035, 12.279523 |
| 15 | Spiaggia di Ponente | W | 37.935384, 12.276704 |

---

## Logica del Vento (Wind Engine)

L'algoritmo in `wind_logic.dart` determina lo stato di ogni spiaggia:

```dart
// Vento debole (<= 6 km/h) = sempre VERDE
// Altrimenti:
// - Vento da TERRA (offshore > 130°) = VERDE fino a 28 km/h, GIALLO fino a 35 km/h
// - Vento LATERALE/FRONTALE (<= 130°) = GIALLO fino a 18 km/h, ROSSO oltre
// - Tetto ordinario: sopra 38 km/h sempre ROSSO
// - Cala Azzurra con N/NW/W: VERDE fino a 28 km/h, GIALLO fino a 40 km/h
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
https://drive.google.com/uc?export=download&id=1pav7QBy4-EFbxP_Q_2h3W1jJq2Ra6T0Z
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
| `MORNING` | 06:00-11:00 | Colazioni, escursioni mattutine |
| `LUNCH` | 11:00-15:00 | Ristoranti, bar |
| `DINNER` | 19:00-24:00 | Ristoranti, vita notturna |

È possibile combinare più fasce: `["MORNING", "LUNCH"]`

### Dimensioni Immagini Consigliate

| Tipo | Larghezza | Altezza | Aspect Ratio |
|------|-----------|---------|--------------|
| STICKY_BOTTOM | 600px | 100px | 6:1 |
| NATIVE | 640px | 360px | 16:9 |

### Comportamento Fallback

Se il caricamento del JSON fallisce (rete assente, timeout, errore):

- L'app mostra un banner di fallback predefinito
- Nessun crash o errore visibile all'utente
- Timeout: 15 secondi

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
   - Riavvia l'app per forzare il caricamento
   - Verifica che le immagini si carichino correttamente
   - Testa il tap per verificare i link

---

## API Meteo

### Open-Meteo (Gratuita, senza API key)

Endpoint utilizzato:

```text
https://api.open-meteo.com/v1/forecast
  ?latitude=37.9284
  &longitude=12.3267
  &current_weather=true
```

Dati utilizzati:

- `windspeed` (km/h)
- `winddirection` (gradi, 0-360)

---

## Requisiti di Sistema

- **Flutter SDK**: >= 3.0.0 < 4.0.0
- **Dart SDK**: >= 3.0.0
- **Android**: API 21+ (Android 5.0)
- **iOS**: 12.0+

## Dipendenze

```yaml
dependencies:
  flutter_localizations: sdk
  http: ^1.1.0           # Networking
  url_launcher: ^6.2.0   # Apertura Google Maps
  cached_network_image: ^3.3.0  # Cache immagini
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
| `cala_preveto.jpg` | Cala Preveto |
| `spiaggia_praia.jpeg` | Spiaggia Praia |
| `cala_faraglioni.jpg` | Cala Faraglioni |
| `cala_trapanese.jpg` | Cala Trapanese |
| `cala_del_pozzo.jpg` | Cala del Pozzo |
| `cala_rotonda.jpeg` | Cala Rotonda |
| `cala_grande.jpg` | Cala Grande |
| `spiaggia_di_ponente.jpg` | Spiaggia di Ponente |

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

**Versione attuale:** 3.1.2

## Localizzazione

L'app usa un sistema i18n interno basato su `AppTranslations`.

- Lingue disponibili: Italiano, Inglese, Francese, Tedesco
- La lingua viene scelta automaticamente in base a quella del dispositivo
- Se la lingua del dispositivo non è supportata, l'app mostra l'Inglese
- Dal menu in alto a destra puoi aprire la selezione lingua senza uscire dalla home

## Contatti

Email progetto: [favignanatopspot@gmail.com](mailto:favignanatopspot@gmail.com)

---

## Licenza

Tutti i diritti riservati.
