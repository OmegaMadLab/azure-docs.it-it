---
title: Controllo degli accessi in base al ruolo nel portale di Azure | Microsoft Docs
description: Introduzione alla gestione degli accessi con il Controllo degli accessi in base al ruolo nel portale di Azure. Usare le assegnazioni di ruolo per assegnare autorizzazioni alle risorse.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 4ac7fda78f456a233c8dba90a6a50e19774991df
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203652"
---
# <a name="use-role-based-access-control-to-manage-access-to-your-azure-subscription-resources"></a>Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure
> [!div class="op_single_selector"]
> * [Gestire l'accesso per utente o gruppo](role-assignments-users.md)
> * [Gestire l'accesso per risorsa](role-assignments-portal.md)

Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. L'uso del Controllo degli accessi in base al ruolo permette di concedere agli utenti solo il livello di accesso necessario per lavorare. Questo articolo permette di iniziare subito a usare il Controllo degli accessi in base al ruolo nel portale di Azure. Per altri dettagli sulla gestione degli accessi, vedere l'articolo relativo al [Controllo degli accessi in base al ruolo](overview.md).

In ogni sottoscrizione è possibile concedere fino a 2000 assegnazioni di ruolo. 

## <a name="view-access"></a>Visualizzare l'accesso
È possibile visualizzare chi ha accesso a una risorsa, a un gruppo di risorse o a una sottoscrizione dal relativo pannello principale nel [portale di Azure](https://portal.azure.com). Ad esempio, si vuole vedere chi ha accesso a uno dei gruppi di risorse:

1. Selezionare **Gruppi di risorse** nella barra di spostamento a sinistra.  
    ![Gruppi di risorse, icona](./media/role-assignments-portal/resourcegroups_icon.png)
2. Selezionare il nome del gruppo di risorse nel pannello **Gruppi di risorse** .
3. Selezionare **Controllo di accesso (IAM)** dal menu a sinistra.  
4. Il pannello Controllo di accesso elenca tutti gli utenti, i gruppi e le applicazioni a cui è stato concesso l'accesso al gruppo di risorse.  
   
    ![Screenshot del pannello Utenti: accesso ereditato e assegnato](./media/role-assignments-portal/view-access.png)

Si noti che l'ambito di alcuni ruoli è **Questa risorsa**, mentre quello di altri è **Ereditato** da un altro ambito. L'accesso viene assegnato in modo specifico al gruppo di risorse oppure ereditato da un'assegnazione nella sottoscrizione padre.

> [!NOTE]
> Gli utenti con i ruoli Amministratore sottoscrizione classico e Coamministratore sono considerati proprietari della sottoscrizione nel nuovo modello Controllo degli accessi in base al ruolo.

## <a name="add-access"></a>Aggiungere un accesso
Si concede l'accesso dalla risorsa, dal gruppo di risorse o dalla sottoscrizione che costituisce l'ambito dell'assegnazione di ruolo.

1. Selezionare **Aggiungi** nel pannello Controllo di accesso.  
2. Selezionare il ruolo da assegnare nel pannello **Selezionare un ruolo** .
3. Selezionare l'utente, il gruppo o l'applicazione nella directory a cui si vuole concedere l'accesso. È possibile cercare nella directory usando nomi visualizzati, indirizzi di posta elettronica e identificatori di oggetto.  
   
    ![Screenshot del pannello Aggiungi utenti: casella di ricerca](./media/role-assignments-portal/grant-access2.png)
4. Selezionare **OK** per creare l'assegnazione. Il popup **Aggiunta dell'utente in corso** tiene traccia dello stato.  
    ![Indicatore di stato dell'aggiunta di utenti, schermata](./media/role-assignments-portal/addinguser_popup.png)

Dopo aver aggiunto un'assegnazione di ruolo, viene visualizzato il pannello **Utenti** .

## <a name="remove-access"></a>Rimuovere un accesso
1. Passare il puntatore del mouse sul nome dell'assegnazione da rimuovere. Accanto al nome verrà visualizzata una casella di controllo.
2. Usare le caselle di controllo per selezionare una o più assegnazioni di ruolo.
2. Selezionare **Rimuovi**.  
3. Selezionare **Sì** per confermare la rimozione.

Le assegnazioni ereditate non possono essere rimosse. Se si desidera rimuovere un'assegnazione ereditata, è necessario eseguire questa operazione laddove è stata creata l'assegnazione di ruolo. Nella colonna **Ambito**, accanto a **Ereditato** è presente un collegamento che consente di visualizzare le risorse in cui è stato assegnato questo ruolo. Passare alla risorsa inclusa nell'elenco per rimuovere l'assegnazione di ruolo.

![Screenshot del pannello Utenti: l'accesso ereditato disabilita il pulsante Rimuovi](./media/role-assignments-portal/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Altri strumenti per gestire l'accesso
È possibile assegnare i ruoli e gestire l'accesso con i comandi del Controllo degli accessi in base al ruolo di Azure in strumenti diversi dal portale di Azure.  Per altre informazioni sui prerequisiti e iniziare a usare i comandi del Controllo degli accessi in base al ruolo di Azure, usare i collegamenti seguenti.

* [Azure PowerShell](role-assignments-powershell.md)
* [Interfaccia della riga di comando di Azure](role-assignments-cli.md)
* [API REST](role-assignments-rest.md)

## <a name="next-steps"></a>Passaggi successivi
* [Creare un report della cronologia delle modifiche relative all'accesso](change-history-report.md)
* Vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](built-in-roles.md)
* Definire i [ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](custom-roles.md)

