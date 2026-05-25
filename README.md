# Surfshark + qBittorrent con Docker

Progetto Docker Compose per eseguire qBittorrent dietro una VPN Surfshark tramite Gluetun.

Tutto il traffico del client torrent passa attraverso il tunnel VPN OpenVPN.

## Architettura

Il progetto è composto da due servizi principali:

- `gluetun`: gestisce la connessione VPN OpenVPN verso Surfshark.
- `qbittorrent`: esegue qBittorrent condividendo la rete del container VPN.


---

## Prerequisiti

Prima di iniziare assicurati di avere:

- Docker installato
- Docker Compose installato
- Un abbonamento Surfshark attivo
- Username e password OpenVPN di Surfshark

---

## Configurazione

### 1. Crea il file `.env`

Copia il file `env.example`:

```bash
cp env.example .env
```

---

### 2. Configura le variabili d'ambiente

Aggiorna il file `.env` con i valori corretti per il tuo ambiente.

Esempio:

```env
OPENVPN_CONFIG=ch-zur.prod.surfshark.com_udp.ovpn
OPENVPN_PROVIDER=SURFSHARK
OPENVPN_USERNAME=tuo_username
OPENVPN_PASSWORD=la_tua_password
QBITTORENT_FILES_DIRECTORY=/Users/tuo-utente/Downloads/qbittorrent-downloads
TZ=Europe/Rome
```

---

### 3. Verifica il file `.ovpn`

Assicurati che `OPENVPN_CONFIG` corrisponda esattamente al nome del file presente nella cartella `vpn`.

Esempio:

```env
OPENVPN_CONFIG=it-mil.prod.surfshark.com_tcp.ovpn
```

---

### 4. Verifica i permessi della cartella download

La directory usata da qBittorrent deve avere permessi accessibili al container Docker.

In caso di problemi puoi temporaneamente usare:

```bash
chmod -R 777 /percorso/qbittorrent
```

> `777` è utile solo per test/debug. In produzione è consigliato configurare permessi più restrittivi.

---

## Variabili d'ambiente

| Variabile | Descrizione |
|---|---|
| `OPENVPN_CONFIG` | Nome del file `.ovpn` da caricare in Gluetun già presente nella cartella VPN|
| `OPENVPN_PROVIDER` | Provider VPN (`SURFSHARK`) |
| `OPENVPN_USERNAME` | Username OpenVPN di Surfshark |
| `OPENVPN_PASSWORD` | Password OpenVPN di Surfshark |
| `QBITTORENT_FILES_DIRECTORY` | Cartella host dove salvare i download |
| `TZ` | Timezone del container (es. `Europe/Rome`) |

---

## Avvio

Avvia i container:

```bash
docker compose up -d
```

Ferma i container:

```bash
docker compose down
```

Visualizza i log:

```bash
docker compose logs -f
```

---

## Accesso a qBittorrent

Dopo l'avvio, l'interfaccia web di qBittorrent sarà disponibile su:

```text
http://localhost:8080
```
```text
http://127.0.0.1:8080
```
```text
http://LOCAL_IP:8080
```

Porte esposte:

| Porta | Protocollo | Descrizione |
|---|---|---|
| `8080` | TCP | Interfaccia Web qBittorrent |
| `6881` | TCP | Traffico torrent |
| `6881` | UDP | Traffico torrent |
| `8888` | TCP | Porta esposta da Gluetun |

---

## Note utili

- I file di configurazione VPN già pronti si trovano nella cartella `vpn`.
- `qbittorrent` utilizza `network_mode: service:gluetun`, quindi tutto il traffico passa attraverso la VPN.
- Se la VPN non si connette:
  - verifica username e password
  - controlla il nome del file `.ovpn`
  - controlla i log con:

```bash
docker compose logs -f
```

- Se i download non vengono salvati nella posizione corretta:
  - verifica `QBITTORENT_FILES_DIRECTORY`
  - controlla i permessi della cartella host

---

## Disclaimer

Questo progetto è fornito a scopo educativo e personale. Assicurati di rispettare le leggi locali e i termini di servizio del tuo provider VPN e dei contenuti scaricati.