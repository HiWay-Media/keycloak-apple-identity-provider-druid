# Changelog

Tutte le modifiche rilevanti a questo progetto sono documentate in questo file.

Il formato segue [Keep a Changelog](https://keepachangelog.com/it/1.0.0/) e il progetto aderisce al [Semantic Versioning](https://semver.org/lang/it/).

---

## [Unreleased]

> Modifiche non ancora rilasciate in una versione stabile.

---

## [1.1.1] - 2026-05-04

### Corretto
- **`Dockerfile.22.0.1-dev`**: risolto problema critico per cui `fitp-enricher-1.0.0.jar` non veniva caricato da Keycloak a runtime.
  - **Causa**: la struttura multi-stage con due stage separati (`builder` e `druid`), ognuno con la propria `kc.sh build`, generava artifact di build in conflitto. Il `COPY --from=druid` sovrascriveva gli artifact del `builder`, e il `kc.sh build` finale operava su uno stato inconsistente, ignorando alcuni provider.
  - **Soluzione**: consolidati tutti i provider (`apple`, `druid`, `fitp-enricher`) in un unico stage `builder` con una singola esecuzione di `kc.sh build`. Rimosso lo stage `druid` separato e il secondo `kc.sh build` nel stage finale.

---

## [1.1.0] - 2026-05-04

### Aggiunto
- **`Dockerfile.22.0.1-dev`**: nuova immagine Docker dedicata all'ambiente di sviluppo basata su `keycloak:22.0.1`.
  - Include il **FITP Enricher** (`fitp-enricher-1.0.0.jar` da `HiWay-Media/keycloak-fitp-enricher@v0.1.0`) per l'arricchimento dei token.
  - Include la variante **dev** del Druid Social Identity Provider (`druid-social-identity-provider-dev-1.1.0.jar`).
  - Feature aggiuntive abilitate: `scripts`, `impersonation`, `admin-fine-grained-authz`, `token-exchange`.
- **`Dockerfile.latest`**: aggiunto stage separato `druid-builder` per il provider Druid, allineando la struttura multi-stage a quella del `Dockerfile.22.0.1`.
- **`README.md`**: documentazione completamente riscritta con:
  - Tabella comparativa delle immagini Docker disponibili.
  - Istruzioni di build e avvio del container.
  - Guida alla configurazione di Apple Identity Provider e Druid SSO.
  - Tabella delle dipendenze con versioni e link alle sorgenti.
  - Sezione requisiti.

### Modificato
- **`Dockerfile.22.0.1`**: refactoring dello stage Druid rinominato da `druid-builder` a `druid` per coerenza interna; aggiunto `RUN ls -l /opt/keycloak/` per debug della struttura file durante la build.
- **`Dockerfile.latest`**: lo stage finale ora copia da entrambi gli stage `builder` (Apple) e `druid-builder` (Druid), consolidando i provider in un'unica immagine ottimizzata.

---

## [1.0.0] - data iniziale

### Aggiunto
- **`Dockerfile.latest`**: prima immagine Docker basata su `quay.io/keycloak/keycloak:latest` con:
  - **Apple Social Identity Provider** `apple-identity-provider-1.10.0.jar` da `klausbetz/apple-identity-provider-keycloak@1.10.0`.
  - **Druid Social Identity Provider (prod)** `druid-social-identity-provider-prod-1.1.0.jar` da `HiWay-Media/keycloak-identity-provider-druid@v1.0.1`.
  - Feature `token-exchange` e metriche abilitate (`KC_METRICS_ENABLED=true`).
  - Build multi-stage ottimizzata.
- **`Dockerfile.22.0.1`**: immagine Docker identica alla `latest` ma basata su `quay.io/keycloak/keycloak:22.0.1` per ambienti che richiedono una versione Keycloak fissa e riproducibile.
- **`LICENSE`**: licenza MIT.
- **`README.md`**: documentazione iniziale del progetto.

---

[Unreleased]: https://github.com/keycloak-apple-identity-provider-druid/compare/v1.1.0...HEAD
[1.1.0]: https://github.com/keycloak-apple-identity-provider-druid/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/keycloak-apple-identity-provider-druid/releases/tag/v1.0.0
