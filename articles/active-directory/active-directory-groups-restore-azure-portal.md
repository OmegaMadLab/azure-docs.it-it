---
title: Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory | Microsoft Docs
description: Come ripristinare un gruppo eliminato, visualizzare i gruppi ripristinabili ed eliminare in modo permanente un gruppo in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 388c5617a040da396cb0c6a05b5697fb5fd22248
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory

Quando si elimina un gruppo di Office 365 in Azure Active Directory (Azure AD), il gruppo eliminato viene conservato ma non è visibile per 30 giorni dalla data di eliminazione. In questo modo il gruppo e i relativi contenuti possono essere ripristinati se necessario. Questa funzionalità è limitata esclusivamente ai gruppi di Office 365 in Azure AD. Non è disponibile per i gruppi di sicurezza e i gruppi di distribuzione.

> [!NOTE] 
> Non usare `Remove-MsolGroup`, perché elimina definitivamente il gruppo. Usare sempre `Remove-AzureADMSGroup` per eliminare un gruppo di Office 365. 

Per ripristinare un gruppo sono necessarie una delle autorizzazioni seguenti:

Ruolo  | Autorizzazioni 
--------- | ---------
Amministratore della società, supporto partner di livello 2 e amministratori del servizio di InTune | Possono ripristinare qualsiasi gruppo di Office 365 eliminato 
Amministratore dell'account utente e supporto partner di livello 1 | Possono ripristinare qualsiasi gruppo di Office 365 eliminato, ad eccezione di quelli assegnati al ruolo di amministratore della società 
Utente | Possono ripristinare qualsiasi gruppo di Office 365 eliminato che era di loro proprietà 


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>Visualizzare i gruppi di Office 365 eliminati che è possibile ripristinare
I cmdlet seguenti consente di visualizzare i gruppi eliminati per verificare che il gruppo o i gruppi a cui l'utente è interessato non siano stati ancora eliminati definitivamente. Questi cmdlet fanno parte del modulo [PowerShell di Azure AD](https://www.powershellgallery.com/packages/AzureAD/). Altre informazioni su questo modulo sono reperibili nell'articolo [PowerShell di Azure Active Directory versione 2](/powershell/azure/install-adv2?view=azureadps-2.0).

1.  Eseguire il cmdlet seguente per visualizzare tutti i gruppi di Office 365 eliminati nel tenant per cui è ancora possibile il ripristino.
  ```
  Get-AzureADMSDeletedGroup
  ```

2.  In alternativa, se si conosce l'objectID di un gruppo specifico (ed è possibile ottenerlo dal cmdlet nel passaggio 1), eseguire il cmdlet seguente per verificare che il gruppo eliminato specifico non sia stato ancora definitivamente eliminato.
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-your-deleted-office-365-group"></a>Come ripristinare il gruppo di Office 365 eliminato
Dopo avere verificato che è ancora possibile ripristinare il gruppo, ripristinare il gruppo eliminato con uno dei passaggi seguenti. Se il gruppo contiene documenti, siti SP o altri oggetti persistenti, potrebbero volerci fino a 24 ore per ripristinare completamente un gruppo e il relativo contenuto.

1.  Eseguire il cmdlet seguente per ripristinare il gruppo e il relativo contenuto.
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

In alternativa, è possibile eseguire il cmdlet seguente per rimuovere definitivamente il gruppo eliminato.
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>Come si verifica che l'azione è riuscita?
Per verificare di aver ripristinato correttamente un gruppo di Office 365, eseguire il cmdlet `Get-AzureADGroup –ObjectId <objectId>` per visualizzare informazioni relative al gruppo. Al completamento della richiesta di ripristino:
- Il gruppo verrà visualizzato nella barra di navigazione a sinistra su Exchange
- In Planner verrà visualizzato il piano per il gruppo
- I siti di Sharepoint e tutti i contenuti saranno disponibili
- È possibile accedere al gruppo da qualsiasi endpoint di Exchange e da altri carichi di lavoro di Office 365 che supportano i gruppi di Office 365


## <a name="next-steps"></a>Passaggi successivi
Questi articoli contengono informazioni aggiuntive sui gruppi di Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)
