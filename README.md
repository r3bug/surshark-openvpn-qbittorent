# Surfshark + qBittorrent con Docker

Progetto Docker Compose per eseguire qBittorrent dietro una VPN Surfshark tramite [Gluetun](https://github.com/qdm12/gluetun). Tutto il traffico del client torrent passa attraverso il tunnel VPN OpenVPN, mentre i file scaricati vengono salvati in una cartella montata dall'host.

## Architettura

- `gluetun`: gestisce la connessione VPN OpenVPN verso Surfshark.
- `qbittorrent`: esegue il client qBittorrent e condivide la rete del container VPN.
- `vpn`: contiene i file `.ovpn` di Surfshark da usare come configurazione.

## Prerequisiti

- Docker e Docker Compose installati.
- Un abbonamento Surfshark con username e password validi per OpenVPN.
- Un file di configurazione `.ovpn` di Surfshark disponibile nella cartella `vpn`.

## Configurazione

1. Copia `env.example` in `.env`. 

2. Aggiorna i valori in base al tuo ambiente.

3. Assicurati che `OPENVPN_CONFIG` corrisponda esattamente al nome del file presente nella cartella `vpn`.
   Ad esempio, per collegarsi a un server Surfshark di Milano tramite TCP:
```env
OPENVPN_CONFIG=it-mil.prod.surfshark.com_tcp.ovpn

4. Assicurati che la directory dei dati di qBittorrent abbia i permessi corretti per Docker.
   In caso di problemi puoi temporaneamente usare:

```bash
chmod -R 777 /percorso/qbittorrent

Esempio:

```env
OPENVPN_CONFIG=ch-zur.prod.surfshark.com_udp.ovpn
OPENVPN_PROVIDER=SURFSHARK # Lasciare cosi
OPENVPN_USERNAME=tuo_username
OPENVPN_PASSWORD=la_tua_password
QBITTORENT_FILES_DIRECTORY=/Users/tuo-utente/Downloads/qbittorrent-downloads
TZ=Europe/Rome
```

## Variabili d'ambiente
- `OPENVPN_CONFIG`: nome del file `.ovpn` di Surfshark da far caricare a Gluetun.
- `OPENVPN_PROVIDER`: provider VPN; in questo progetto è impostato su `SURFSHARK`.
- `OPENVPN_USERNAME`: username OpenVPN di Surfshark.
- `OPENVPN_PASSWORD`: password OpenVPN di Surfshark.
- `QBITTORENT_FILES_DIRECTORY`: cartella dell'host dove salvare i download.
- `TZ`: timezone del container, ad esempio `Europe/Rome`.

## Avvio

Da una shell nella cartella del progetto:

```bash
docker compose up -d
```

Per fermare tutto:

```bash
docker compose down
```

## Accesso a qBittorrent

Dopo l'avvio, l'interfaccia web di qBittorrent è disponibile su:

- `http://localhost:8080`

La porta torrent è esposta su:

- `6881/tcp`
- `6881/udp`

È presente anche la porta `8888/tcp`, mappata dal container Gluetun come definito nel compose.

## Note utili

- - I file di configurazione VPN devono trovarsi nella cartella `vpn` del progetto.
- `qbittorrent` usa `network_mode: service:gluetun`, quindi eredita la rete del container VPN.
- Se il container VPN non si connette, controlla prima username, password e nome del file `.ovpn`, e sopratutto i log del container "docker compose logs" 
- Se i download non finiscono nella posizione prevista, verifica il valore di `QBITTORENT_FILES_DIRECTORY` e i permessi della cartella host.
