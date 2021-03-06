---
title: Assegnare la licenza per la reimpostazione della password self-service - Azure Active Directory
description: Requisiti di licenza per la reimpostazione password self-service di Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 78d4d721f2821a8365185c0bad6d795c67a75292
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33864664"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Requisiti di licenza per la reimpostazione password self-service di Azure AD

Per consentire il funzionamento della reimpostazione password di Azure Active Directory (Azure AD), *è necessario che nell'organizzazione sia presente almeno una licenza assegnata*. La reimpostazione password non prevede l'applicazione delle licenze per utente. Una licenza appropriata è necessaria se un utente sfrutta direttamente o indirettamente i vantaggi di eventuali funzionalità che rientrano in tale licenza.

* **Utenti solo cloud**: Office 365 e SKU a pagamento o Azure AD Basic
* **Utenti cloud** o **utenti locali**: Azure AD Premium P1 o P2, Enterprise Mobility + Security (EMS) o Microsoft 365

## <a name="licenses-required-for-password-writeback"></a>Licenze richieste per il writeback delle password

Per usare il writeback delle password, è necessario disporre una delle licenze seguenti assegnate nel tenant:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5
* Microsoft 365 F1

> [!WARNING]
> I piani di licenza Office 365 autonomi *non supportano il writeback delle password* e richiedono uno dei piani precedenti per l'uso della funzionalità.
>

Informazioni aggiuntive sulle licenze, ad esempio i costi, sono disponibili nelle pagine seguenti:

* [Sito sui prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Caratteristiche e funzionalità di Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Abilitare le licenze per gruppi o per utente

Azure AD ora supporta licenze basate sui gruppi. Gli amministratori possono assegnare le licenze in blocco a un gruppo di utenti, anziché assegnarle loro singolarmente. Per altre informazioni, vedere [Assegnare, verificare e risolvere i problemi relativi alle licenze](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses).

Alcuni servizi Microsoft non sono disponibili in tutte le posizioni. Per assegnare una licenza a un utente, l'amministratore deve prima specificare la proprietà **Località di utilizzo** per l'utente. L'assegnazione delle licenze può essere eseguita nella sezione **Utente** > **Profilo** > **Impostazioni** nel portale di Azure. *Quando si usa l'assegnazione di licenze ai gruppi, tutti gli utenti per cui non è specificata un percorso d'uso ereditano il percorso della directory.*

## <a name="next-steps"></a>Passaggi successivi

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](howto-sspr-deployment.md)
* [Reimpostare o modificare la password](../active-directory-passwords-update-your-own-password.md)
* [Registrarsi per la reimpostazione della password self-service](../active-directory-passwords-reset-register.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](howto-sspr-authenticationdata.md)
* [Metodi di autenticazione disponibili per gli utenti](concept-sspr-howitworks.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](concept-sspr-policy.md)
* [Panoramica del writeback delle password](howto-sspr-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](howto-sspr-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](concept-sspr-howitworks.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)
