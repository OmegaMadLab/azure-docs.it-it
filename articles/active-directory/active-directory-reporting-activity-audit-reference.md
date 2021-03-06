---
title: Informazioni di riferimento sulle attività di controllo di Azure Active Directory (Azure AD) | Microsoft Docs
description: Vedi una panoramica delle attività di controllo che possono essere registrate nei log di controllo in Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: be19130f7a2f45049cd7e08bb961ac6cb5aa14ff
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-audit-activity-reference"></a>Informazioni di riferimento sulle attività di controllo di Azure AD

I report di Azure Active Directory (Azure AD) offrono tutte le informazioni necessarie per determinare lo stato dell'ambiente.

L'architettura di reporting in Azure AD include i componenti seguenti:

- **Attività** 
    - **Attività di accesso** : informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso utente
    - **Log di controllo**: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, come utenti, app, gruppi, ruoli, criteri, autenticazioni e così via.
- **Sicurezza** 
    - **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. Per informazioni dettagliate, vedere Accessi a rischio.
    - **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. Per informazioni dettagliate, vedere Utenti contrassegnati per il rischio.

Questo articolo elenca le attività di controllo che possono essere registrate nei log di controllo.
 


## <a name="access-reviews"></a>Verifiche di accesso

|Categoria di controllo|Attività|
|---|---|
|Gestione directory|Aggiungere un'unità amministrativa|
|Gestione directory|Aggiungere un membro all'unità amministrativa|
|Gestione directory|Eliminare un'unità amministrativa|
|Gestione directory|Rimuovere un membro da un'unità amministrativa|
|Gestione directory|Aggiornare un'unità amministrativa|
|Gestione directory|Administration|
|User Management|Operazione sulla directory|
|User Management|Esportazione|
|User Management|Importa|
|User Management|Altri|
|User Management|Elaborare il deposito|
|User Management|Azione della regola di sincronizzazione|
|User Management|Aggiunta di un'applicazione|
|User Management|Eliminare applicazione|
|User Management|Aggiornare l'applicazione|
|User Management|Aggiornare la modalità di accesso Single Sign-On dell'applicazione|
|User Management|Automated Password Rollover (Rollover automatizzato delle password)|
|User Management|Aggiungere autorizzazioni applicazione V2|
|User Management|Creare un'applicazione V1|
|User Management|Creare un'applicazione V2|
|User Management|Eliminare un'applicazione V1|
|User Management|Eliminare un'applicazione V2|
|User Management|Eliminare le concessioni di autorizzazione per l'applicazione V2|
|User Management|Ottenere applicazioni V1 e V2|
|User Management|Ottenere applicazione V1|
|User Management|Ottenere applicazioni V1|
|User Management|Ottenere applicazione V2|
|User Management|Ottenere applicazioni V2|




## <a name="account-provisioning"></a>Provisioning degli account

|Categoria di controllo|Attività|
|---|---|
|Gestione di applicazioni|Recuperare le concessioni di autorizzazione per l'applicazione V2|
|Gestione di applicazioni|Recuperare le entità servizio dell'applicazione V2 nel tenant corrente|
|Gestione di applicazioni|Aggiornare l'applicazione V1|
|Gestione di applicazioni|Aggiornare l'applicazione V2|
|Gestione di applicazioni|Aggiornare le concessioni di autorizzazione per l'applicazione V2|
|Gestione di applicazioni|Aggiungere OAuth2PermissionGrant|
|Gestione di applicazioni|Aggiungere un'assegnazione di ruolo app a un'entità servizio|

## <a name="application-proxy"></a>Proxy dell'applicazione

|Categoria di controllo|Attività|
|---|---|
|Gestione di applicazioni|Aggiunta di un'applicazione|
|Gestione di applicazioni|Aggiungere un proprietario a un'applicazione|
|Gestione di applicazioni|Aggiungere un proprietario a un'entità servizio|
|Gestione di applicazioni|Aggiungere un criterio a un'entità servizio|
|Gestione directory|Aggiunta di un'entità servizio|
|Gestione directory|Aggiunta delle credenziali dell'entità servizio|
|Gestione directory|Dare il consenso a un'applicazione|
|Gestione directory|Eliminare applicazione|
|Gestione directory|Eliminare definitivamente un'applicazione|
|Gestione directory|Rimuovere OAuth2PermissionGrant|
|Gestione directory|Rimuovere un'assegnazione di ruolo app da un'entità servizio|
|Gestione directory|Rimuovere il proprietario da un'applicazione|
|Risorsa|Rimuovere il proprietario da un'entità servizio|
|Risorsa|Rimuovere i criteri da un'entità servizio|
|Risorsa|Rimozione di un'entità servizio|


## <a name="automated-password-rollover"></a>Rollover automatizzato delle password

|Categoria di controllo|Attività|
|---|---|
|Gestione di applicazioni|Rimozione delle credenziali dell'entità servizio|


## <a name="b2c"></a>B2C

|Categoria di controllo|Attività|
|---|---|
|Gestione di applicazioni|Ripristinare l'applicazione|
|Gestione di applicazioni|Revocare il consenso|
|Gestione di applicazioni|Aggiornare l'applicazione|
|Gestione di applicazioni|Vengono aggiornati i segreti esterni.|
|Gestione di applicazioni|Aggiornare un'entità servizio|
|Gestione di applicazioni|Emettere un token di accesso per l'applicazione|
|Gestione di applicazioni|Emettere un codice di autorizzazione per l'applicazione|
|Gestione di applicazioni|Emettere un id_token per l'applicazione|
|Gestione di applicazioni|Convalidare le credenziali dell'account utente locale|
|Gestione di applicazioni|Convalidare l'autenticazione utente|
|Gestione di applicazioni|Aggiungere autorizzazioni applicazione V2|
|Gestione di applicazioni|Aggiungere una chiave basata sul segreto ASCII a un contenitore di chiavi CPIM|
|Gestione di applicazioni|Aggiungere una chiave a un contenitore di chiavi CPIM|
|Gestione di applicazioni|AdminPolicyDatas-SetResources|
|Gestione di applicazioni|AdminUserJourneys-GetResources|
|Gestione di applicazioni|AdminUserJourneys-RemoveResources|
|Authentication|AdminUserJourneys-SetResources|
|Authentication|Creare un provider di identità|
|Authentication|Creare un'applicazione V1|
|Authentication|Creare un'applicazione V2|
|Authentication|Creare un dominio personalizzato nel tenant|
|Authorization|Crea un nuovo AdminUserJourney|
|Authorization|Creare JSON di risorse localizzate|
|Authorization|Creare un nuovo provider di identità personalizzato|
|Authorization|Creare un nuovo provider di identità|
|Authorization|Creare o aggiornare una risorsa della directory B2C|
|Authorization|Crea criteri|
|Authorization|Creare criteri trustFramework|
|Authorization|Creare criteri trustFramework con prefisso configurabile|
|Authorization|Creare un attributo utente|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|Creare o aggiornare un nuovo AdminUserJourney|
|Authorization|Eliminare un provider di identità|
|Authorization|Eliminare un provider di identità|
|Authorization|Eliminare un'applicazione V1|
|Authorization|Eliminare un'applicazione V2|
|Authorization|Eliminare le concessioni di autorizzazione per l'applicazione V2|
|Authorization|Eliminare una risorsa della directory B2C|
|Authorization|Eliminare un contenitore di chiavi CPIM|
|Authorization|Eliminare criteri trustFramework|
|Authorization|Eliminare un attributo utente|
|Authorization|Abilitare la funzionalità B2C|
|Authorization|Ottenere le risorse di directory B2C in una sottoscrizione|
|Authorization|Ottenere un provider di identità personalizzato|
|Authorization|Ottenere un provider di identità|
|Authorization|Ottenere applicazioni V1 e V2|
|Authorization|Ottenere applicazione V1|
|Authorization|Ottenere applicazioni V1|
|Authorization|Ottenere applicazione V2|
|Authorization|Ottenere applicazioni V2|
|Authorization|Ottenere una risorsa della directory B2C|
|Authorization|Ottenere un elenco di domini personalizzati nel tenant|
|Authorization|Ottenere un percorso utente|
|Authorization|Ottenere le attestazioni dell'applicazione per il percorso utente|
|Authorization|Ottenere le attestazioni autocertificate consentite per il percorso utente|
|Authorization|Ottenere le attestazioni autocertificate consentite dei criteri|
|Authorization|Ottenere l'elenco di attestazioni di output disponibili|
|Authorization|Ottenere le definizioni del contenuto per il percorso utente|
|Authorization|Ottenere i provider di identità per un flusso di amministrazione specifico|
|Authorization|Ottenere i metadati di chiave attivi del contenitore di chiavi in JWK|
|Authorization|Ottenere l'elenco di tutti i flussi di amministrazione|
|Authorization|Ottenere l'elenco di tag per tutti i flussi di amministratore per tutti gli utenti|
|Authorization|Ottenere l'elenco di tenant per un utente|
|Authorization|Ottenere le attestazioni autocertificate degli account locali|
|Authorization|Ottenere JSON di risorse localizzate|
|Authorization|Ottenere le operazioni del provider di risorse Microsoft.AzureActiveDirectory|
|Authorization|Ottenere criteri|
|Authorization|Ottenere un criterio|
|Authorization|Ottenere le proprietà delle risorse di un tenant|
|Authorization|Ottenere l'elenco di provider di identità supportati|
|Authorization|Ottenere l'elenco di provider di identità supportati del percorso utente|
|Authorization|Ottenere le informazioni del tenant|
|Authorization|Ottenere le funzionalità consentite del tenant|
|Authorization|Ottenere un elenco di provider di identità personalizzati definiti dal tenant|
|Authorization|Ottenere un elenco di provider di identità definiti dal tenant|
|Authorization|Ottenere un elenco di provider di identità locali definiti dal tenant|
|Authorization|Ottenere i dettagli del tenant per un utente per la creazione di risorse|
|Authorization|Ottenere l'elenco di tenant|
|Authorization|Ottenere tenantDomains|
|Authorization|Ottenere le impostazioni cultura supportate predefinite per CPIM|
|Authorization|Ottenere i dettagli di un flusso di amministrazione|
|Authorization|Ottenere l'elenco di percorsi utente per questo tenant|
|Authorization|Ottenere il set di impostazioni cultura supportate disponibili per CPIM|
|Authorization|Ottenere il criterio trustFramework|
|Authorization|Ottenere il criterio trustFramework in formato XML|
|Authorization|Ottenere l'attributo utente|
|Authorization|Ottenere gli attributi utente|
|Authorization|Ottenere un elenco di percorsi utente|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|Ottiene un contenitore di chiavi CPIM in formato JWK|
|Authorization|Ottiene un elenco di contenitori di chiavi nel tenant|
|Authorization|Ottiene il tipo di tenant|
|Authorization|MigrateTenantMetadata|
|Authorization|Applicare patch a un provider di identità|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|Rimuovere un percorso utente|
|Authorization|Ripristinare un backup del contenitore di chiavi CPIM|
|Authorization|Recuperare le concessioni di autorizzazione per l'applicazione V2|
|Authorization|Recuperare le entità servizio dell'applicazione V2 nel tenant corrente|
|Authorization|Aggiornare un provider di identità personalizzato|
|Authorization|Aggiornare un provider di identità|
|Authorization|Aggiornare un provider di identità locale|
|Authorization|Aggiornare l'applicazione V1|
|Authorization|Aggiornare l'applicazione V2|
|Authorization|Aggiornare le concessioni di autorizzazione per l'applicazione V2|
|Authorization|Aggiorna criteri|
|Authorization|Aggiornare l'attributo utente|
|Authorization|Caricare una chiave crittografata CPIM|
|Authorization|Autorizzazione utente: l'API è disabilitata per il set di funzionalità del tenant|
|Authorization|Autorizzazione utente: accesso concesso all'utente come 'Amministratore tenant'|
|Authorization|Autorizzazione utente: all'utente sono stati concessi diritti di accesso 'Utenti autenticati'|
|Authorization|Verificare se la funzionalità B2C è abilitata|
|Authorization|Verificare se la funzionalità è abilitata|
|Authorization|Creare un programma|
|Authorization|Eliminare un programma|
|Authorization|Creare un collegamento al controllo del programma|
|Authorization|Eseguire l'onboarding alle verifiche di accesso di Azure AD|
|Authorization|Eliminare il collegamento al controllo del programma|
|Authorization|Aggiornare un programma|
|Authorization|Disabilitare l'accesso Single Sign-On del desktop|
|Authorization|Disabilitare l'accesso Single Sign-On del desktop per un dominio specifico|
|Authorization|Disabilitare il proxy di applicazione|
|Authorization|Disabilitare l'autenticazione pass-through|
|Authorization|Abilitare l'accesso Single Sign-On del desktop|
|Gestione directory|Abilitare l'accesso Single Sign-On del desktop per un dominio specifico|
|Gestione directory|Abilitare il proxy di applicazione|
|Gestione directory|Abilitare l'autenticazione pass-through|
|Gestione directory|Creare un dominio personalizzato nel tenant|
|Gestione directory|Abilitare la funzionalità B2C|
|Gestione directory|Ottenere un elenco di domini personalizzati nel tenant|
|Gestione directory|Ottenere le proprietà delle risorse di un tenant|
|Gestione directory|Ottenere le informazioni del tenant|
|Gestione directory|Ottenere le funzionalità consentite del tenant|
|Gestione directory|Ottenere tenantDomains|
|Chiave|Ottiene il tipo di tenant|
|Chiave|Verificare se la funzionalità B2C è abilitata|
|Chiave|Verificare se la funzionalità è abilitata|
|Chiave|Aggiunta di un partner alla società|
|Chiave|Aggiungere un dominio non verificato|
|Chiave|Aggiungere un dominio verificato|
|Chiave|Creare un'azienda|
|Chiave|Vengono create le impostazioni aziendali.|
|Chiave|Vengono eliminate le impostazioni aziendali.|
|Chiave|Abbassare un partner di livello|
|Chiave|Directory eliminata|
|Altri|Directory eliminata in modo permanente|
|Altri|Directory pianificata per l'eliminazione|
|Risorsa|Innalzare un'azienda a livello di partner|
|Risorsa|Ripulire le proprietà di Rights Management|
|Risorsa|Rimuovere un partner dalla società|
|Risorsa|Rimuovere un dominio non verificato|
|Risorsa|Rimuovere un dominio verificato|
|Risorsa|Impostazione informazioni società|
|Risorsa|Impostare la funzionalità DirSync|
|Risorsa|Impostare il flag DirSyncEnabled|
|Risorsa|Configurare una relazione|
|Risorsa|Configurare una soglia di eliminazione accidentale|
|Risorsa|Configurare la posizione dei dati consentita per l'azienda|
|Risorsa|Abilitare la funzionalità multinazionale per l'azienda|
|Risorsa|Impostare una funzionalità directory nel tenant|
|Risorsa|Impostazione dell'autenticazione del dominio|
|Risorsa|Configurazione delle impostazioni di federazione nel dominio|
|Risorsa|Impostare il criterio password|
|Risorsa|Impostare le proprietà di Rights Management|
|Risorsa|Aggiornare l'azienda|
|Risorsa|Aggiornare le impostazioni aziendali|
|Risorsa|Aggiornamento dominio|
|Risorsa|Verifica dominio|
|Risorsa|Verifica del dominio tramite la verifica di posta elettronica|
|Risorsa|Onboarding|
|Risorsa|Aggiornare le impostazioni dell'avviso|
|Risorsa|Aggiornare le impostazioni del digest settimanale|
|Risorsa|Disabilitare il writeback delle password per la directory|
|Risorsa|Abilitare il writeback delle password per la directory|
|Risorsa|Aggiungere un'assegnazione di ruolo app a un gruppo|
|Risorsa|Aggiungi gruppo|
|Risorsa|Aggiungere un membro al gruppo|
|Risorsa|Aggiungere un proprietario a un gruppo|
|Risorsa|Creare le impostazioni dei gruppi|
|Risorsa|Eliminare gruppo|
|Risorsa|Eliminare le impostazioni dei gruppi|
|Risorsa|Terminare l'applicazione di licenze basate sui gruppi agli utenti|
|Risorsa|Eliminare definitivamente un gruppo|
|Risorsa|Rimuovere un'assegnazione di ruolo app da un gruppo|
|Risorsa|Rimuovere un membro dal gruppo|
|Risorsa|Rimuovere il proprietario da un gruppo|
|Risorsa|Ripristinare un gruppo|
|Risorsa|Configurare la licenza di un gruppo|
|Risorsa|È stato configurato il gruppo che deve essere gestito dall'utente.|
|Risorsa|Avviare l'applicazione di licenze basate sui gruppi agli utenti|
|Risorsa|Attivare il ricalcolo della licenza del gruppo|
|Risorsa|Aggiornare un gruppo|
|Risorsa|Aggiornare le impostazioni dei gruppi|
|Risorsa|Aggiungere un membro|
|Risorsa|Creare un gruppo|
|Risorsa|Eliminare un gruppo|
|Risorsa|Rimuovere un membro|
|Risorsa|Aggiornare un gruppo|
|Risorsa|Approvare una richiesta di partecipazione a un gruppo in sospeso|
|Risorsa|Annullare una richiesta di partecipazione a un gruppo in sospeso|
|Risorsa|Creare criteri di gestione del ciclo di vita|
|Risorsa|Eliminare una richiesta di partecipazione a un gruppo in sospeso|
|Risorsa|Rifiutare una richiesta di partecipazione a un gruppo in sospeso|
|Risorsa|Rinnovare un gruppo|
|Risorsa|Richiedere di partecipare a un gruppo|
|Risorsa|Impostare proprietà di gruppi dinamici|
|Risorsa|Aggiornare criteri di gestione del ciclo di vita|
|Risorsa|Aggiungere una chiave basata sul segreto ASCII a un contenitore di chiavi CPIM|
|Risorsa|Aggiungere una chiave a un contenitore di chiavi CPIM|
|Risorsa|Eliminare un contenitore di chiavi CPIM|
|Risorsa|Eliminare un contenitore di chiavi|
|Risorsa|Ottenere i metadati di chiave attivi del contenitore di chiavi in JWK|
|Risorsa|Ottenere i metadati del contenitore di chiavi|
|Risorsa|Ottiene un contenitore di chiavi CPIM in formato JWK|
|Risorsa|Ottiene un elenco di contenitori di chiavi nel tenant|
|Risorsa|Ripristinare un backup del contenitore di chiavi CPIM|
|Risorsa|Salvare un contenitore di chiavi|
|Risorsa|Caricare una chiave crittografata CPIM|
|Risorsa|Emettere un codice di autorizzazione per l'applicazione|
|Risorsa|Emettere un id_token per l'applicazione|


## <a name="core-directory"></a>Directory principale

|Categoria di controllo|Attività|
|---|---|
|Gestione delle unità amministrative|Scaricare un singolo tipo di evento di rischio|
|Gestione delle unità amministrative|Scaricare amministratori e stato del consenso esplicito per il digest settimanale|
|Gestione delle unità amministrative|Scaricare tutti i tipi di eventi di rischio|
|Gestione delle unità amministrative|Scaricare gli eventi di rischio degli utenti del piano gratuito|
|Gestione delle unità amministrative|Scaricare gli utenti contrassegnati per il rischio|
|Gestione di applicazioni|Inviti batch elaborati|
|Gestione di applicazioni|Inviti batch caricati|
|Gestione di applicazioni|Aggiungere un proprietario a un criterio|
|Gestione di applicazioni|Aggiungi criteri|
|Gestione di applicazioni|Elimina criteri|
|Gestione di applicazioni|Rimuovere le credenziali dei criteri|
|Gestione di applicazioni|Aggiorna criteri|
|Gestione di applicazioni|Impostare i criteri della registrazione MFA|
|Gestione di applicazioni|Impostare i criteri di rischio di accesso|
|Gestione di applicazioni|Impostare i criteri di rischio utente|
|Gestione di applicazioni|Accettare le condizioni per l'utilizzo|
|Gestione di applicazioni|Creare le condizioni per l'utilizzo|
|Gestione di applicazioni|Rifiutare le condizioni per l'utilizzo|
|Gestione di applicazioni|Eliminare le condizioni per l'utilizzo|
|Gestione di applicazioni|Modificare le condizioni per l'utilizzo|
|Gestione di applicazioni|Pubblicare le condizioni per l'utilizzo|
|Gestione di applicazioni|Annullare la pubblicazione delle condizioni per l'utilizzo|
|Gestione di applicazioni|Aggiungere il certificato SSL dell'applicazione|
|Gestione di applicazioni|Eliminare il binding SSL|
|Gestione di applicazioni|Registrare il connettore|
|Gestione di applicazioni|AdminPolicyDatas-RemoveResources|
|Gestione di applicazioni|AdminPolicyDatas-SetResources|
|Gestione di applicazioni|AdminUserJourneys-GetResources|
|Gestione directory|AdminUserJourneys-RemoveResources|
|Gestione directory|AdminUserJourneys-SetResources|
|Gestione directory|Creare un provider di identità|
|Gestione directory|Crea un nuovo AdminUserJourney|
|Gestione directory|Creare JSON di risorse localizzate|
|Gestione directory|Creare un nuovo provider di identità personalizzato|
|Gestione directory|Creare un nuovo provider di identità|
|Gestione directory|Creare o aggiornare una risorsa della directory B2C|
|Gestione directory|Crea criteri|
|Gestione directory|Creare criteri trustFramework|
|Gestione directory|Creare criteri trustFramework con prefisso configurabile|
|Gestione directory|Creare un attributo utente|
|Gestione directory|CreateTrustFrameworkPolicy|
|Gestione directory|Eliminare un provider di identità|
|Gestione directory|Eliminare un provider di identità|
|Gestione directory|Eliminare una risorsa della directory B2C|
|Gestione directory|Eliminare criteri trustFramework|
|Gestione directory|Eliminare un attributo utente|
|Gestione directory|Ottenere le risorse di directory B2C in un gruppo di risorse|
|Gestione directory|Ottenere le risorse di directory B2C in una sottoscrizione|
|Gestione directory|Ottenere un provider di identità personalizzato|
|Gestione directory|Ottenere un provider di identità|
|Gestione directory|Ottenere una risorsa della directory B2C|
|Gestione directory|Ottenere un percorso utente|
|Gestione directory|Ottenere le attestazioni dell'applicazione per il percorso utente|
|Gestione directory|Ottenere le attestazioni autocertificate consentite per il percorso utente|
|Gestione directory|Ottenere le attestazioni autocertificate consentite dei criteri|
|Gestione directory|Ottenere l'elenco di attestazioni di output disponibili|
|Gestione directory|Ottenere le definizioni del contenuto per il percorso utente|
|Gestione directory|Ottenere i provider di identità per un flusso di amministrazione specifico|
|Gestione directory|Ottenere l'elenco di tutti i flussi di amministrazione|
|Gestione directory|Ottenere l'elenco di tag per tutti i flussi di amministratore per tutti gli utenti|
|Gestione di gruppi|Ottenere l'elenco di tenant per un utente|
|Gestione di gruppi|Ottenere le attestazioni autocertificate degli account locali|
|Gestione di gruppi|Ottenere JSON di risorse localizzate|
|Gestione di gruppi|Ottenere le operazioni del provider di risorse Microsoft.AzureActiveDirectory|
|Gestione di gruppi|Ottenere criteri|
|Gestione di gruppi|Ottenere un criterio|
|Gestione di gruppi|Ottenere l'elenco di provider di identità supportati|
|Gestione di gruppi|Ottenere l'elenco di provider di identità supportati del percorso utente|
|Gestione di gruppi|Ottenere un elenco di provider di identità personalizzati definiti dal tenant|
|Gestione di gruppi|Ottenere un elenco di provider di identità definiti dal tenant|
|Gestione di gruppi|Ottenere un elenco di provider di identità locali definiti dal tenant|
|Gestione di gruppi|Ottenere i dettagli del tenant per un utente per la creazione di risorse|
|Gestione di gruppi|Ottenere le impostazioni cultura supportate predefinite per CPIM|
|Gestione di gruppi|Ottenere i dettagli di un flusso di amministrazione|
|Gestione di gruppi|Ottenere l'elenco di percorsi utente per questo tenant|
|Gestione di gruppi|Ottenere il set di impostazioni cultura supportate disponibili per CPIM|
|Gestione di gruppi|Ottenere il criterio trustFramework|
|Gestione di gruppi|Ottenere il criterio trustFramework in formato XML|
|Gestione di gruppi|Ottenere l'attributo utente|
|Gestione dei criteri|Ottenere gli attributi utente|
|Gestione dei criteri|Ottenere un elenco di percorsi utente|
|Gestione dei criteri|GetIEFPolicies|
|Gestione dei criteri|GetIdentityProviders|
|Gestione dei criteri|GetTrustFrameworkPolicy|
|Risorsa|MigrateTenantMetadata|
|Risorsa|Spostare le risorse|
|Risorsa|Applicare patch a un provider di identità|
|Risorsa|PutTrustFrameworkPolicy|
|Risorsa|PutTrustFrameworkpolicy|
|Risorsa|Rimuovere un percorso utente|
|Risorsa|Aggiornare un provider di identità personalizzato|
|Risorsa|Aggiornare un provider di identità|
|Risorsa|Aggiornare un provider di identità locale|
|Risorsa|Aggiornare una risorsa della directory B2C|
|Risorsa|Aggiorna criteri|
|Risorsa|Aggiornare lo stato della sottoscrizione|
|Gestione dei ruoli|Aggiornare l'attributo utente|
|Gestione dei ruoli|Convalidare lo spostamento di risorse|
|Gestione dei ruoli|Aggiungere un dispositivo|
|Gestione dei ruoli|Aggiungere una configurazione del dispositivo|
|Gestione dei ruoli|Aggiungere un proprietario registrato a un dispositivo|
|Gestione dei ruoli|Aggiungere utenti registrati a un dispositivo|
|Gestione dei ruoli|Eliminare un dispositivo|
|Gestione dei ruoli|Eliminazione della configurazione del dispositivo|
|Gestione dei ruoli|Dispositivo non più conforme|
|Gestione dei ruoli|Dispositivo non più gestito|
|User Management|Rimuovere il proprietario registrato da un dispositivo|
|User Management|Rimuovere utenti registrati da un dispositivo|
|User Management|Aggiornare un dispositivo|
|User Management|Aggiornare la configurazione di un dispositivo|
|User Management|Aggiungere un membro idoneo al ruolo|
|User Management|Aggiungere un membro a un ruolo|
|User Management|Aggiungere l'assegnazione di ruolo alla definizione del ruolo|
|User Management|Aggiungere un ruolo da un modello|
|User Management|Aggiungere un membro con ambito a un ruolo|
|User Management|Rimuovere un membro idoneo da un ruolo|
|User Management|Rimuovere un membro da un ruolo|
|User Management|Rimuovere l'assegnazione di ruolo dalla definizione del ruolo|
|User Management|Rimuovere un membro con ambito da un ruolo|
|User Management|Aggiornare un ruolo|
|User Management|AccessReview_Review|
|User Management|AccessReview_Update|
|User Management|ActivationAborted|
|User Management|ActivationApproved|
|User Management|ActivationCanceled|
|User Management|ActivationRequested|
|User Management|Aggiunto|
|User Management|Assegnare|


## <a name="identity-protection"></a>Identity Protection

|Categoria di controllo|Attività|
|---|---|
|Gestione directory|Elevare|
|Gestione directory|Rimosso|
|Gestione directory|Modifiche alle impostazioni dei ruoli|
|Altri|ScanAlertsNow|
|Altri|Iscrizione|
|Altri|Annullare elevazione|
|Altri|UpdateAlertSettings|
|Altri|UpdateCurrentState|
|Gestione dei criteri|Verifica di accesso terminata|
|Gestione dei criteri|Aggiungere un responsabile approvazione all'approvazione delle richieste|
|Gestione dei criteri|Aggiungere un revisore alla verifica di accesso|
|User Management|Applicare la verifica di accesso|
|User Management|Creare una verifica di accesso|


## <a name="invited-users"></a>Utenti invitati

|Categoria di controllo|Attività|
|---|---|
|Altri|Creare un'approvazione richiesta|
|Altri|Eliminare la verifica di accesso|
|User Management|Rimuovere un revisore dalla verifica di accesso|
|User Management|Richiedere il risultato dell'applicazione della verifica|
|User Management|Richiedere l'arresto della verifica|
|User Management|Verificare l'assegnazione di app|
|User Management|Verificare l'appartenenza a gruppi|
|User Management|Verificare l'appartenenza al ruolo di controllo degli accessi in base al ruolo|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Categoria di controllo|Attività|
|---|---|
|Gestione di gruppi|Verificare la richiesta di approvazione richiesta|
|Gestione di gruppi|Aggiornare la verifica di accesso|
|Gestione di gruppi|Aggiornare le impostazioni per le notifiche tramite posta elettronica delle verifiche di accesso|
|Gestione di gruppi|Aggiornare l'impostazione di conteggio ricorrenze delle verifiche di accesso|
|Gestione di gruppi|Aggiornare l'impostazione di durata in giorni delle ricorrenze delle verifiche di accesso|
|User Management|Aggiornare l'impostazione del tipo End delle ricorrenze delle verifiche di accesso|
|User Management|Aggiornare l'impostazione del tipo delle ricorrenze delle verifiche di accesso|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Categoria di controllo|Attività|
|---|---|
|Gestione dei ruoli|Aggiornare le impostazioni per i promemoria delle verifiche dell'accesso|
|Gestione dei ruoli|Aggiornare un'approvazione richiesta|
|Gestione dei ruoli|Aggiungere un'assegnazione di ruolo app a un utente|
|Gestione dei ruoli|Add user|
|Gestione dei ruoli|Aggiungere dettagli dell'app telefonica per l'autenticazione avanzata degli utenti|
|Gestione dei ruoli|Modifica licenza utente|
|Gestione dei ruoli|Modifica password utente|
|Gestione dei ruoli|Convertire un utente federato in utente gestito|
|Gestione dei ruoli|Creare una password applicazione per un utente|
|Gestione dei ruoli|Eliminare una password applicazione per un utente|
|Gestione dei ruoli|Eliminare un utente|
|Gestione dei ruoli|Disabilitare l'account|
|Gestione dei ruoli|Abilitare l'autenticazione avanzata|
|Gestione dei ruoli|Eliminare definitivamente un utente|
|Gestione dei ruoli|Rimuovere un'assegnazione di ruolo app da un utente|
|Gestione dei ruoli|Rimuovere dettagli dell'app telefonica per l'autenticazione avanzata degli utenti|



## <a name="self-service-group-management"></a>Gestione di gruppi self-service

|Categoria di controllo|Attività|
|---|---|
|Gestione di gruppi|Reimpostazione password utente|
|Gestione di gruppi|Ripristinare un utente|
|Gestione di gruppi|Impostazione forzatura per la modifica delle password utente|
|Gestione di gruppi|Impostare la gestione utenti|
|Gestione di gruppi|Abilitare i metadati del token OATH degli utenti|
|Gestione di gruppi|Aggiornare il timestamp StsRefreshTokenValidFrom|
|Gestione di gruppi|Vengono aggiornati i segreti esterni.|
|Gestione di gruppi|Aggiornamento utente|
|Gestione di gruppi|L'amministrazione genera una password temporanea|


## <a name="self-service-password-management"></a>Gestione delle password self-service

|Categoria di controllo|Attività|
|---|---|
|Gestione directory|L'amministratore richiede all'utente di reimpostare la password|
|Gestione directory|Assegnare utenti esterni all'applicazione|
|User Management|Messaggio di posta elettronica non inviato, l'utente ha annullato la sottoscrizione|
|User Management|Invitare utente esterno|
|User Management|Riscattare un invito per utente esterno|
|User Management|Creazione del tenant virale|
|User Management|Creazione dell'utente virale|
|User Management|Registrazione della password utente|
|User Management|Reimpostazione della password utente|
|User Management|Bloccato dalla reimpostazione password self-service|


## <a name="terms-of-use"></a>Condizioni per l'utilizzo

|Categoria di controllo|Attività|
|---|---|
|Gestione dei criteri|Modificare la password (self-service)|
|Gestione dei criteri|Reimpostare la password (amministratore)|
|Gestione dei criteri|Reimpostare la password (self-service)|
|Gestione dei criteri|Stato dell'attività di reimpostazione delle password self-service|
|Gestione dei criteri|Stato dell'attività di reimpostazione delle password self-service|
|Gestione dei criteri|Sbloccare l'account utente (self-service)|
|Gestione dei criteri|Utente registrato per la reimpostazione della password self-service|




## <a name="next-steps"></a>Passaggi successivi

Per una panoramica di:

- Creazione di report, vedere [Creazione di report in Azure Active Directory](active-directory-reporting-azure-portal.md).

- Report delle attività di controllo, vedere [Report delle attività di controllo nel portale di Azure Active Directory](active-directory-reporting-activity-audit-logs.md). 

