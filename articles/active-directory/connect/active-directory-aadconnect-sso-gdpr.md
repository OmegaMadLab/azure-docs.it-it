---
title: Privacy dell'utente e accesso Single Sign-On facile di Azure AD | Microsoft Docs
description: Questo articolo riguarda l'accesso Single Sign-On facile di Azure Active Directory (Azure AD) e la conformità al Regolamento generale sulla protezione dei dati (RGPD).
services: active-directory
keywords: che cos'è Azure AD Connect, RGPD, componenti richiesti per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: bffa5a3aa57c5b01e3361bc6fc6b284348707800
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149528"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Privacy dell'utente e accesso Single Sign-On facile di Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Panoramica


L'accesso Single Sign-On facile di Azure AD crea il tipo di log seguente che può contenere informazioni personali degli utenti finali:

- File di log di traccia di Azure AD Connect.

La conformità alla privacy dell'utente per l'accesso Single Sign-On facile può essere ottenuta in due modi:

1.  Qualora richiesto, estrarre i dati per un utente e rimuovere i dati di tale utente dalle installazioni.
2.  Verificare che nessun dato venga conservato per più di 48 ore.

La seconda opzione è consigliabile perché risulta più semplice da implementare e gestire. Vedere le istruzioni seguenti per ogni tipo di log:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminare i file di log di traccia di Azure AD Connect

Controllare il contenuto della cartella **%ProgramData%\AADConnect** ed eliminare il contenuto del log di traccia (file **trace-\*.log**) di questa cartella entro 48 ore dall'installazione o dall'aggiornamento di Azure AD Connect o dalla modifica della configurazione dell'accesso Single Sign-On facile, perché questa azione può creare dati a cui si applica il regolamento RGPD.

>[!IMPORTANT]
>Non eliminare il file **PersistedState.xml** in questa cartella, perché questo file viene usato per mantenere lo stato dell'installazione precedente di Azure AD Connect e viene usato quando viene eseguita un'installazione di aggiornamento. Questo file non conterrà mai dati relativi a un utente e non deve essere mai eliminato.

È possibile esaminare ed eliminare questi file di log di traccia tramite Esplora risorse oppure è possibile usare lo script di PowerShell seguente per eseguire le azioni necessarie:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Salvare lo script in un file con estensione "PS1". Eseguire questo script quando necessario.

Per altre informazioni sui requisiti RGPD per Azure AD Connect correlati, vedere [questo articolo](active-directory-aadconnect-gdpr.md).

### <a name="note-about-domain-controller-logs"></a>Nota sui log del controller di dominio

Se è abilitata la registrazione di controllo, questo prodotto può generare log di sicurezza per i controller di dominio. Per altre informazioni sulla configurazione dei criteri di controllo, leggere questo [articolo](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Passaggi successivi
* [Consultare l'Informativa sulla privacy Microsoft nel Trust Center](https://www.microsoft.com/trustcenter)
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.
