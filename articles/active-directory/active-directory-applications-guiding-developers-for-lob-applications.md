---
title: Registrare l'applicazione per l'uso di Azure Active Directory | Microsoft Docs
description: Scritto per i professionisti IT, questo articolo include linee guida per l'integrazione delle applicazioni di Azure in Active Directory.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2018
ms.author: kgremban
ms.custom: seohack1
ms.openlocfilehash: 66102836b954bf4fafc4379cf573658d89e0d409
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Sviluppare app line-of-business per Azure Active Directory
Questa guida offre una panoramica dello sviluppo di applicazioni line-of-business (LoB) per Azure Active Directory (AD) ed è rivolta agli amministratori globali di Active Directory/Office 365.

## <a name="overview"></a>Panoramica
La creazione di applicazioni integrate con Azure AD offre agli utenti dell'organizzazione servizi Single Sign-On per Office 365. La disponibilità dell'applicazione in Azure AD consente di avere il controllo dei criteri di autenticazione impostati per l'applicazione. Per altre informazioni sull'accesso condizionale e su come proteggere le applicazioni con l'autenticazione a più fattori (MFA), vedere [Configurare le regole di accesso](active-directory-conditional-access-azure-portal-get-started.md).

Registrare l'applicazione per l'uso di Azure Active Directory. Registrando l'applicazione, gli sviluppatori possono usare Azure AD per autenticare gli utenti e richiedere l'accesso a risorse degli utenti come posta elettronica, calendario e documenti.

Qualsiasi membro della directory (non guest) può registrare un'applicazione, operazione nota anche come *creazione di un oggetto applicazione*.

La registrazione di un'applicazione consente a qualsiasi utente di eseguire le operazioni seguenti:

* Ottenere un'identità per l'applicazione riconosciuta da Azure AD
* Ottenere uno o più segreti/chiavi utilizzabili dall'applicazione per l'autenticazione in AD
* Personalizzare l'applicazione con nome, logo e altri elementi nel portale di Azure
* Applicare le funzionalità di autorizzazione di Azure AD per la propria app, tra cui:

  * Controllo degli accessi in base al ruolo
  * Azure Active Directory come server di autorizzazione oAuth (proteggere un'API esposta dall'applicazione)
* Dichiarare le autorizzazioni necessarie per il funzionamento dell'applicazione nel modo previsto, tra cui:

      - Autorizzazioni per l'app (solo amministratori globali). Ad esempio: appartenenza ai ruoli in un'altra appartenenza di ruolo o applicazione Azure AD relativa a una risorsa, un gruppo di risorse o una sottoscrizione di Azure
      - Autorizzazioni delegate (qualsiasi utente). Ad esempio: Azure AD, Profilo di accesso e lettura

> [!NOTE]
> Per impostazione predefinita, qualsiasi membro può registrare un'applicazione. Per informazioni su come limitare le autorizzazioni per la registrazione delle applicazioni per membri specifici, vedere [Procedura per l'aggiunta delle applicazioni ad Azure AD](develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Ecco cosa deve fare l'amministratore globale per aiutare gli sviluppatori a rendere le loro applicazioni pronte per la produzione:

* Configurare regole di accesso (criteri di accesso/autenticazione a più fattori)
* Configurare l'app per richiedere l'assegnazione di utenti e assegnare gli utenti
* Evitare l'esperienza di autorizzazione utente predefinita

## <a name="configure-access-rules"></a>Configurare regole di accesso
Configurare regole di accesso per ogni applicazione nelle app SaaS. Ad esempio, è possibile richiedere l'autenticazione a più fattori oppure solo di consentire l'accesso agli utenti connessi a reti attendibili. I dettagli a questo scopo sono disponibili nel documento [Configurazione di regole di accesso](active-directory-conditional-access-azure-portal-get-started.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configurare l'app per richiedere l'assegnazione di utenti e assegnare gli utenti
Per impostazione predefinita, gli utenti possono accedere alle applicazioni senza esservi assegnati. Tuttavia, se l'applicazione espone ruoli o si desidera che venga visualizzata nel pannello di accesso dell'utente, l'assegnazione degli utenti dovrebbe essere richiesta.

[Richiedere l'assegnazione degli utenti](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Per gli abbonati ad Azure AD Premium o Enterprise Mobility Suite (EMS), è consigliabile usare i gruppi. L'assegnazione di gruppi per l'applicazione consente di delegare le attività di gestione continuativa degli accessi al proprietario del gruppo. È possibile creare il gruppo oppure richiedere al responsabile dell'organizzazione di creare il gruppo con gli strumenti per la gestione dei gruppi.

[Assegnazione di utenti a un'applicazione](active-directory-applications-guiding-developers-assigning-users.md)  
[Assegnazione di gruppi a un'applicazione](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Rimuovere l'esperienza di consenso dell'utente
Per impostazione predefinita, ogni utente è sottoposto a un'esperienza di consenso per poter accedere. L'esperienza di consenso, in cui viene chiesto di concedere le autorizzazioni per un'applicazione, può creare confusione per gli utenti che non hanno familiarità con questo tipo di decisioni.

Per le applicazioni attendibili, è possibile semplificare l'esperienza utente dando il consenso per l'applicazione per conto dell'organizzazione.

Per altre informazioni sul consenso dell'utente e l'esperienza di consenso in Azure, vedere [Integrazione di applicazioni con Azure Active Directory](active-directory-integrating-applications.md).

## <a name="related-articles"></a>Articoli correlati
* [Consentire l'accesso remoto sicuro ad applicazioni locali con il proxy di applicazione di Azure AD](manage-apps/application-proxy.md)
* [Anteprima dell'accesso condizionale di Azure per app SaaS](active-directory-conditional-access-azure-portal-get-started.md)
* [Gestione dell'accesso alle app tramite Azure AD](active-directory-managing-access-to-apps.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
