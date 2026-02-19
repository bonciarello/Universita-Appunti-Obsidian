---
aliases: [Protocollo Universal Second Factor, U2F]
tags: [metodi-e-strumenti-per-la-sicurezza]
---
Il **U2F** è uno standard di autenticazione che fornisce un secondo livello di [[Sicurezza|sicurezza]] per i servizi online, progettato per essere sicuro contro attacchi aggressivi e rispettoso della privacy.

## 1. Caratteristiche Principali
*   **Chiavi Hardware:** Si basa sull'uso di dispositivi crittografici fisici (chiavi USB, NFC, Bluetooth).
*   **Semplicità:** Gli utenti registrano il dispositivo autonomamente con i diversi servizi.
*   **Verifica Forte:** Oltre alle credenziali tradizionali (User/Pass), l'utente deve inserire o toccare la chiave fisica.

## 2. Resistenza al Phishing
L'obiettivo primario di U2F è eliminare il successo del phishing e del dirottamento delle credenziali. Poiché la chiave è fisicamente separata e utilizza [[Crittografia Asimmetrica|crittografia asimmetrica]] legata all'identità del server, un hacker non può simulare la presenza del dispositivo fisico né riutilizzare i codici intercettati su siti falsi.
