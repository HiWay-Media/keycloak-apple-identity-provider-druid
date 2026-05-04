# keycloak-apple-identity-provider-druid

Docker images per Keycloak con integrazione dell'Apple Identity Provider e del provider SSO per Apache Druid. Basato sui plugin open-source di [klausbetz](https://github.com/klausbetz/apple-identity-provider-keycloak) e [HiWay-Media](https://github.com/HiWay-Media/keycloak-identity-provider-druid).

## Introduzione

Questo repository fornisce immagini Docker pronte all'uso di Keycloak con le seguenti estensioni pre-installate:

- **Apple Social Identity Provider** (`apple-identity-provider-1.10.0.jar`) — permette agli utenti di autenticarsi con Apple ID tramite Keycloak.
- **Druid Social Identity Provider** (`druid-social-identity-provider-prod-1.1.0.jar` / `druid-social-identity-provider-dev-1.1.0.jar`) — abilita il Single Sign-On (SSO) tra Keycloak e Apache Druid.
- **FITP Enricher** (`fitp-enricher-1.0.0.jar`) — arricchimento token aggiuntivo, disponibile solo nell'immagine dev.

## Funzionalità

- **Apple ID Authentication**: Login tramite Apple ID integrato direttamente in Keycloak.
- **Druid SSO**: SSO gestito da Keycloak per Apache Druid, semplificando l'accesso ai dashboard.
- **Token Exchange**: Feature `token-exchange` abilitata di default.
- **Metrics**: Metriche Keycloak abilitate (`KC_METRICS_ENABLED=true`).
- **Multi-stage Docker build**: Build ottimizzata con stage separati per Apple provider e Druid provider.
- **Varianti di immagine**: Disponibili versioni `latest`, `22.0.1` e `22.0.1-dev`.

## Immagini Docker disponibili

| Dockerfile | Base image | Providers inclusi | Feature aggiuntive |
|---|---|---|---|
| `Dockerfile.latest` | `keycloak:latest` | Apple 1.10.0, Druid prod 1.1.0 | `token-exchange` |
| `Dockerfile.22.0.1` | `keycloak:22.0.1` | Apple 1.10.0, Druid prod 1.1.0 | `token-exchange` |
| `Dockerfile.22.0.1-dev` | `keycloak:22.0.1` | Apple 1.10.0, Druid dev 1.1.0, FITP Enricher 1.0.0 | `token-exchange`, `scripts`, `impersonation`, `admin-fine-grained-authz` |

## Utilizzo

### Build dell'immagine

```bash
# Immagine latest
docker build -f Dockerfile.latest -t keycloak-apple-druid:latest .

# Immagine versione 22.0.1 (produzione)
docker build -f Dockerfile.22.0.1 -t keycloak-apple-druid:22.0.1 .

# Immagine versione 22.0.1 (sviluppo)
docker build -f Dockerfile.22.0.1-dev -t keycloak-apple-druid:22.0.1-dev .
```

### Avvio del container

```bash
docker run -p 8080:8080 \
  -e KEYCLOAK_ADMIN=admin \
  -e KEYCLOAK_ADMIN_PASSWORD=admin \
  keycloak-apple-druid:latest \
  start-dev
```

### Configurazione Apple Identity Provider

Dopo il deploy, configurare il provider Apple in Keycloak:

1. Accedere alla Admin Console di Keycloak.
2. Selezionare il realm desiderato → **Identity Providers** → **Add provider** → **Apple**.
3. Inserire `Client ID`, `Team ID`, `Key ID` e la chiave privata `.p8` ottenuti da [Apple Developer](https://developer.apple.com/).

### Configurazione Druid SSO

1. In Keycloak, configurare un client dedicato per Apache Druid con il flusso OIDC.
2. In Druid, configurare `druid.auth.authenticatorChain` con il provider Keycloak.
3. Riferirsi alla documentazione del plugin: [keycloak-identity-provider-druid](https://github.com/HiWay-Media/keycloak-identity-provider-druid).

## Requisiti

- Docker >= 20.x
- Accesso a `quay.io` per il pull dell'immagine base Keycloak
- Credenziali Apple Developer (per il provider Apple)

## Dipendenze

| Componente | Versione | Fonte |
|---|---|---|
| Apple Identity Provider | 1.10.0 | [klausbetz/apple-identity-provider-keycloak](https://github.com/klausbetz/apple-identity-provider-keycloak) |
| Druid Identity Provider (prod) | 1.1.0 | [HiWay-Media/keycloak-identity-provider-druid](https://github.com/HiWay-Media/keycloak-identity-provider-druid) |
| Druid Identity Provider (dev) | 1.1.0 | [HiWay-Media/keycloak-identity-provider-druid](https://github.com/HiWay-Media/keycloak-identity-provider-druid) |
| FITP Enricher | 1.0.0 | [HiWay-Media/keycloak-fitp-enricher](https://github.com/HiWay-Media/keycloak-fitp-enricher) |

## Contribuire

I contributi sono benvenuti! Per favore apri una issue o una pull request descrivendo la modifica proposta.

## Licenza

Questo progetto è distribuito sotto licenza MIT. Consulta il file [LICENSE](LICENSE) per i dettagli.

## Ringraziamenti

Un ringraziamento alle community di Keycloak, Apache Druid, [klausbetz](https://github.com/klausbetz) e [HiWay-Media](https://github.com/HiWay-Media) per il loro contributo all'ecosistema open-source.
