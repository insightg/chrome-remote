# Chrome Remote Access

Chrome browser accessibile via web con HTTPS.

## Struttura Directory

```
chrome-remote/
├── docker-compose.yml          # Configurazione Docker Compose
├── .env                        # Variabili d'ambiente
├── README.md                   # Questa documentazione
├── nginx/
│   ├── conf/
│   │   └── default.conf       # Configurazione Nginx
│   └── certs/
│       ├── selfsigned.crt     # Certificato SSL
│       └── selfsigned.key     # Chiave privata SSL
└── chrome/
    ├── config/                 # Configurazione Chrome
    └── downloads/              # File scaricati
```

## Avvio Rapido

```bash
# Avvia i servizi
docker-compose up -d

# Visualizza i log
docker-compose logs -f

# Ferma i servizi
docker-compose down

# Riavvia i servizi
docker-compose restart
```

## Accesso

Apri il browser e vai su:

**https://localhost:8443**

oppure da remoto:

**https://[IP-SERVER]:8443**

Nota: Dovrai accettare il certificato SSL self-signed.

## Configurazione

Modifica il file `.env` per cambiare le porte o altre impostazioni:

- `HTTPS_PORT`: Porta HTTPS (default: 8443)
- `CHROME_PORT`: Porta interna Chrome (default: 3010)
- `TZ`: Timezone (default: Europe/Rome)
- `PUID/PGID`: User/Group ID (default: 1000)

Dopo aver modificato `.env`, riavvia i servizi:

```bash
docker-compose down && docker-compose up -d
```

## File Scaricati

I file scaricati da Chrome si trovano in:

```bash
./chrome/downloads/
```

oppure il path completo:

```bash
/Data/pgr/chrome-remote/chrome/downloads/
```

## Volumi Docker

Il progetto monta le seguenti directory locali:

- `./chrome/config` → `/config` (configurazione Chrome e profilo utente)
- `./chrome/downloads` → `/config/downloads` (file scaricati)
- `./nginx/conf` → `/etc/nginx/conf.d` (configurazione Nginx)
- `./nginx/certs` → `/etc/nginx/certs` (certificati SSL)

Tutti i dati persistono sulla macchina host in `/Data/pgr/chrome-remote/`

## Comandi Utili

```bash
# Vedere lo stato dei container
docker-compose ps

# Vedere i log
docker-compose logs chrome
docker-compose logs nginx-proxy

# Riavviare un singolo servizio
docker-compose restart chrome
docker-compose restart nginx-proxy

# Aggiornare le immagini
docker-compose pull
docker-compose up -d

# Fermare e rimuovere tutto
docker-compose down -v
```

## Sicurezza

Per abilitare l'autenticazione, modifica `nginx/conf/default.conf` aggiungendo:

```nginx
auth_basic "Chrome Remote Access";
auth_basic_user_file /etc/nginx/conf.d/.htpasswd;
```

Poi crea il file password:

```bash
printf "admin:$(openssl passwd -apr1 PASSWORD)\n" > nginx/conf/.htpasswd
docker-compose restart nginx-proxy
```

## Troubleshooting

### Il browser non si carica

```bash
# Verifica che i container siano attivi
docker-compose ps

# Controlla i log
docker-compose logs
```

### Errore "port already allocated"

Cambia la porta in `.env`:

```bash
HTTPS_PORT=9443
```

Poi riavvia:

```bash
docker-compose down && docker-compose up -d
```
