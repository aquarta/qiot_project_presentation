# 📡 Q-IoT Platform
### Ecosistema IoT End-to-End per la Raccolta Dati da Sensori BLE Eterogenei

![Version](https://img.shields.io/badge/version-1.0.0-blue) ![Status](https://img.shields.io/badge/status-stable-success) ![License](https://img.shields.io/badge/license-MIT-green) ![Technlogy](https://img.shields.io/badge/stack-Go%20%7C%20React%20%7C%20Kotlin%20%7C%20MQTT-orange)

**Q-IoT** è una piattaforma distribuita progettata per orchestrare l'intero ciclo di vita del dato sperimentale in ambito IoT: dalla definizione logica dell'esperimento fino alla storicizzazione su database Time-Series.
A differenza delle app verticali (che spesso limitano il numero di connessioni o supportano solo hardware proprietario) e delle piattaforme monolitiche server-side, Q-IoT implementa un paradigma di **Configurable Edge Computing**, spostando l'intelligenza di parsing direttamente sul gateway mobile.

---

## 🏗 Architettura del Sistema

Il sistema si basa su un'architettura a 4 livelli logici che garantisce flessibilità e scalabilità:

<img src="./assets/architecture.png" alt="Architettura Q-IoT" width="600"/>

### 1. Management Layer (Control Plane)
Il backend (**Go**) funge da configuratore:
<img src="./assets/configuration.png">

### 2. Edge Layer (Android Gateway)
L'applicazione mobile (**Kotlin**) funge da gateway intelligente:
*   **Zero-Touch Provisioning:** Scarica la configurazione e si connette automaticamente ai soli dispositivi autorizzati.
*   **Dynamic Parsing:** Decodifica i dati binari (Raw Data) in JSON direttamente sul dispositivo usando regole definite in file **YAML**.
*   **Supporto Multi-Protocollo:** Gestisce nativamente sia profili GATT standard che protocolli complessi come **Movesense Whiteboard** (REST-over-BLE).

### 3. Transport & Storage Layer
*   **Broker (EMQX 5.x):** Gestisce la trasmissione asincrona via MQTT, disaccoppiando i dispositivi dal server.
*   **Database (InfluxDB v2):** Storicizza le serie temporali ad alta frequenza (es. IMU a 50Hz/100Hz).

| EMQX Dashboard | InfluxDB Dashboard |
|---|---|
| <img src="./assets/dashboard_emqx.png" width="300"/> | <img src="./assets/dashboard_influx.png" width="300"/> |
---

### 4. Management Layer (Dashboards)
La webapp oltre che configurare ci permette di visualizzare i dati in tempo reale con grafici interattivi (Apache ECharts):

<img src="assets/dashboard.png" alt="Dashboard" width="600"/>

## 🚀 Key Features

### ⚡ Edge Computing & Parsing Dinamico
Non è necessario ricompilare l'app per supportare un nuovo sensore. Basta aggiornare la mappa YAML sul server.
*Esempio di definizione YAML per il parsing binario:*
```yaml
structParser:
  endianness: "LITTLE_ENDIAN"
  fields:
    - name: "acc_x"
      type: "short" # 2 bytes
    - name: "acc_y"
      type: "short"
```

### 🤖 Automazione "Zero-Touch"
Il sistema elimina l'errore umano durante i setup sperimentali complessi:
1.  **Auto-Connect:** Scansione e connessione automatica basata su MAC Address whitelist.
2.  **Auto-Subscribe:** Attivazione automatica delle notifiche (Characteristics) configurate.

### 🔌 Integrazione Avanzata Movesense
Supporto completo per la libreria **MDS** e il protocollo **Whiteboard**.
Il sistema astrae la complessità delle chiamate REST-like (GET, PUT, SUBSCRIBE) tipiche dei sensori Movesense, permettendo la sottoscrizione a stream dati complessi (ECG, HR, IMU9) insieme a sensori GATT standard.

---

## 🛠 Tech Stack

| Componente | Tecnologia | Versione | Dettagli |
| :--- | :--- | :--- | :--- |
| 📱 **Mobile App** | Kotlin / Android SDK | 35 (Min 29) | Coroutines, MDS Lib 3.33.1 |
| 🖥️ **Backend** | Go (Golang) | 1.25+ | Goroutines, High Concurrency |
| 🎨 **Frontend** | React | 19.x | Apache ECharts integration |
| 🔄 **Broker** | EMQX | 5.10 | MQTT 5.0, Rule Engine |
| 📊 **Database** | InfluxDB | v2.7 (OSS) | Flux Query Language |
| 🐳 **Deploy** | Docker Compose | - | Containerized Infrastructure |

---

## 📸 Validazione Funzionale

Il sistema è stato validato in scenari reali con dispositivi eterogenei:
*   **Dispositivi Testati:** Movesense HR+, ST BlueNRG-LPS, SensorTile.box, BlueTile.
*   **Scenario:** Configurazione remota di un esperimento multi-sensore e monitoraggio della rotazione inerziale.

> **Risultato:** L'applicazione Android ha normalizzato e trasmesso i dati inerziali (Giroscopio/Accelerometro) con latenza trascurabile, visualizzati istantaneamente sulla Dashboard React.

*(Inserire qui uno screenshot della Dashboard con i grafici)*

---

## 🔮 Roadmap & Sviluppi Futuri

*   [ ] **Offline-First:** Implementazione di un database locale (Store & Forward) su Android per garantire zero data loss in assenza di rete.
*   [ ] **HAL (Hardware Abstraction Layer):** Estensione a nuovi protocolli oltre al BLE (es. Zigbee, MQTT diretto).
*   [ ] **Attuazione:** Supporto per l'invio di comandi di scrittura (configurazione sensori) dal Cloud al dispositivo.

---

### Credits
Progetto sviluppato per il corso di *Internet of Things* - **Università del Salento**.
**Studenti:** Antonio e Andrea Quarta.
**Docente:** Prof. Luigi Patrono.