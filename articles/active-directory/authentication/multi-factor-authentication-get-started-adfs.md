---
title: Verifica in due passaggi e AD FS - Azure MFA | Documentazione Microsoft
description: Questa è la pagina di Azure Multi-Factor Authentication che descrive come iniziare a usare Azure MFA e ADFS.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 08/25/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 5ee4f3eefb23b16bb135cb7b29511c1c0324ec2c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
---
# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Introduzione a Azure Multi-Factor Authentication e Active Directory Federation Services

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Se l'organizzazione ha federato l'istanza locale di Active Directory con Azure Active Directory tramite AD FS, sono disponibili due opzioni per l'uso di Azure Multi-Factor Authentication.

* Proteggere le risorse cloud mediante Azure Multi-Factor Authentication o Active Directory Federation Services
* Proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication

Nella tabella seguente viene riepilogata l'esperienza di verifica tra le risorse di protezione con Azure Multi-Factor Authentication e AD FS

| Esperienza di verifica - App basate su browser | Esperienza di verifica - App non basate su browser |
|:--- |:--- |:--- |
| Proteggere le risorse Azure AD utilizzando Azure Multi-Factor Authentication |<li>Il primo passaggio di verifica viene eseguito in locale usando AD FS.</li> <li>Il secondo passaggio è un metodo basato su telefono eseguito mediante l'autenticazione cloud.</li> |
| Proteggere le risorse di Azure AD con Active Directory Federation Services |<li>Il primo passaggio di verifica viene eseguito in locale usando AD FS.</li><li>Il secondo passaggio viene eseguito in locale rispettando l'attestazione.</li> |

Avvertenze per le password dell’app rivolte agli utenti federati:

* Le password dell'app vengono verificate mediante l'autenticazione cloud e di conseguenza ignorano la federazione. La federazione viene usata attivamente solo durante l'impostazione della password dell'app.
* Le impostazioni locali di Controllo di accesso client non vengono rispettate dalle password dell'app.
* Si perde la funzionalità di registrazione dell'autenticazione locale per le password dell'app.
* La disabilitazione/eliminazione dell'account può richiedere fino a tre ore per la sincronizzazione della directory, ritardando la disabilitazione/eliminazione delle password dell'app nell'identità cloud.

Per informazioni sulla configurazione di Azure Multi-Factor Authentication o del server Azure Multi-Factor Authentication con AD FS, vedere gli articoli seguenti:

* [Proteggere le risorse cloud mediante Azure Multi-Factor Authentication e ADFS](howto-mfa-adfs.md)
* [Proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication con ADFS Server Windows 2012 R2](howto-mfaserver-adfs-2012.md)
* [Proteggere le risorse del cloud e locali mediante Server Azure Multi-Factor Authentication con AD FS 2.0](howto-mfaserver-adfs-2.md)
