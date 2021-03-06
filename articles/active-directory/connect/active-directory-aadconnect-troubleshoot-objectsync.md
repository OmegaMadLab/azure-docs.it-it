---
title: 'Azure AD Connect: Risolvere i problemi di sincronizzazione degli oggetti | Microsoft Docs'
description: Questo argomento include la procedura per la risoluzione dei problemi relativi alla sincronizzazione degli oggetti tramite l'attività di risoluzione dei problemi.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: billmath
ms.openlocfilehash: 9945ad30cc7d8882d8b99f6b4278f2063ab4b7f7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193764"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Risolvere i problemi di sincronizzazione degli oggetti con la sincronizzazione di Azure AD Connect
Questo documento include la procedura per la risoluzione dei problemi relativi alla sincronizzazione degli oggetti tramite l'attività di risoluzione dei problemi.

## <a name="troubleshooting-task"></a>Attività di risoluzione dei problemi
Per la distribuzione di Azure Active Directory (AAD) Connect versione 1.1.749.0 o successiva, usare l'attività specifica nella procedura guidata per la risoluzione dei problemi di sincronizzazione degli oggetti. Per le versioni precedenti, eseguire manualmente la risoluzione dei problemi come illustrato [qui](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Eseguire l'attività di risoluzione dei problemi nella procedura guidata
Per eseguire l'attività di risoluzione dei problemi nella procedura guidata, seguire questa procedura:

1.  Aprire una nuova sessione di Windows PowerShell nel server di Azure AD Connect con l'opzione Esegui come amministratore.
2.  Eseguire `Set-ExecutionPolicy RemoteSigned` o `Set-ExecutionPolicy Unrestricted`.
3.  Avviare la procedura guidata di Azure AD Connect.
4.  Passare alla pagina Attività aggiuntive, selezionare Risoluzione dei problemi e fare clic su Avanti.
5.  Nella pagina Risoluzione dei problemi fare clic su Avvia per avviare il menu per la risoluzione dei problemi in PowerShell.
6.  Dal menu principale scegliere l'opzione per la risoluzione dei problemi della sincronizzazione degli oggetti.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Parametri di input per la risoluzione dei problemi
I parametri di input seguenti sono necessari per l'attività di risoluzione dei problemi:
1.  **Nome distinto oggetto**: si tratta del nome distinto dell'oggetto che necessita di risoluzione dei problemi.
2.  **Nome connettore di Active Directory**: si tratta del nome della foresta di AD in cui si trova l'oggetto indicato in precedenza.
3.  Credenziali di amministratore globale del tenant di Azure AD.![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Informazioni sui risultati dell'attività di risoluzione dei problemi
L'attività di risoluzione dei problemi effettua i controlli seguenti:

1.  Individuare la mancata corrispondenza del nome dell'entità utente in caso di sincronizzazione dell'oggetto con Azure Active Directory
2.  Verificare se l'oggetto viene escluso a causa dei filtri di dominio
3.  Verificare se l'oggetto viene escluso a causa dei filtri dell'unità organizzativa
4.  Verificare se la sincronizzazione degli oggetti è bloccata a causa di una cassetta postale collegata
5. Verificare se l'oggetto è un gruppo di distribuzione dinamica che non dovrebbe essere sincronizzato

La parte restante di questa sezione descrive i risultati specifici restituiti dall'attività. In ogni caso l'attività fornisce un'analisi seguita dalle azioni consigliate per risolvere il problema.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Individuare la mancata corrispondenza del nome dell'entità utente in caso di sincronizzazione dell'oggetto con Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Il suffisso del nome dell'entità utente NON viene verificato nel tenant di Azure AD
Se il nome dell'entità utente o l'ID di accesso alternativo non viene verificato nel tenant di Azure AD, Azure Active Directory sostituisce i suffissi del nome dell'entità utente con il nome di dominio predefinito "onmicrosoft.com".

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Modifica del suffisso dl nome dell'entità utente da un dominio federato a un altro dominio federato
Azure Active Directory non consente la sincronizzazione del suffisso del nome dell'entità utente o dell'ID di accesso alternativo da un dominio federato a un altro dominio federato. Questo approccio si applica ai domini verificati nel tenant di Azure AD e con tipo di autenticazione impostato su Federato.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>La funzionalità DirSync 'SynchronizeUpnForManagedUsers' del tenant di Azure AD è disabilitata
Se la funzionalità DirSync 'SynchronizeUpnForManagedUsers' del tenant di Azure AD è disabilitata, Azure Active Directory non consente aggiornamenti alla sincronizzazione per il nome dell'entità utente o l'ID di accesso alternativo per account utente con licenza e autenticazione gestita.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>L'oggetto viene escluso a causa dei filtri di dominio
### <a name="domain-is-not-configured-to-sync"></a>Il dominio non è configurato per la sincronizzazione
L'oggetto non è compreso nell'ambito a causa della mancata configurazione del dominio. Nell'esempio seguente l'oggetto non è compreso nell'ambito di sincronizzazione, perché il dominio a cui appartiene viene escluso dalla sincronizzazione tramite filtri.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Il dominio è configurato per la sincronizzazione ma alcuni profili di esecuzione o passaggi di esecuzione non sono presenti
L'oggetto non è compreso nell'ambito perché nel dominio non sono presenti alcuni profili di esecuzione o passaggi di esecuzione. Nell'esempio seguente l'oggetto non è compreso nell'ambito di sincronizzazione, perché il dominio a cui appartiene non include i passaggi di esecuzione per il profilo di esecuzione dell'importazione completa.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>L'oggetto viene escluso a causa dei filtri dell'unità organizzativa
L'oggetto non è compreso nell'ambito di sincronizzazione a causa della configurazione dei filtri dell'unità organizzativa. Nell'esempio seguente l'oggetto appartiene a OU=NoSync,DC=bvtadwbackdc,DC=com.  Questa unità organizzativa non è inclusa nell'ambito di sincronizzazione.</br>

![Unità organizzativa](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problema relativo alla cassetta postale collegata
Una cassetta postale collegata deve essere associata a un account master esterno che si trova in un'altra foresta account attendibile. Se tale account master esterno non è presente, Azure AD Connect non sincronizzerà l'account utente che corrisponde alla cassetta postale collegata nella foresta Exchange con il tentant Azure AD tenant.</br>
![Cassetta postale collegata](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Problema relativo al gruppo di distribuzione dinamico
A causa di varie differenze l'Active Directory locale e Azure Active Directory, Azure AD Connect non sincronizza i gruppi di distribuzione dinamici con il tenant di Azure AD.

![Gruppo di distribuzione dinamico](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch13.png)

## <a name="html-report"></a>Report HTML
Oltre ad analizzare l'oggetto, l'attività di risoluzione dei problemi genera anche un report HTML che include tutte le informazioni note sull'oggetto. Il report HTML può essere condiviso con il team del supporto tecnico per operazioni di risoluzione dei problemi aggiuntive, se necessario.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch8.png)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
