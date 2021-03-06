---
title: Creare un piano nello Stack di Azure | Documenti Microsoft
description: Come un amministratore del cloud, creare un piano che consente alle macchine virtuali di provisioning ai sottoscrittori.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: b1bfff16c4f51a9fa53204930df78cbd2cf19b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-plan-in-azure-stack"></a>Creare un piano in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

I [piani](azure-stack-key-features.md) sono raggruppamenti di uno o più servizi. Come un provider, è possibile creare piani per offrire agli utenti. A sua volta, gli utenti sottoscrivono le offerte di utilizzare i piani e i servizi che includono. In questo esempio viene illustrato come creare un piano che include il calcolo, rete e i provider di risorse di archiviazione. Questo piano offre i sottoscrittori la possibilità di eseguire il provisioning di macchine virtuali.

1. Accedi al portale di amministrazione di Azure Stack (https://adminportal.local.azurestack.external).

2. Per creare un piano e dell'offerta che gli utenti possano sottoscrivere, selezionare **New** > **offre + piani** > **piano**.  
   ![Selezionare un piano](media/azure-stack-create-plan/select-plan.png)

3. Nel **nuovo piano** riquadro, compilare **nome visualizzato** e **nome risorsa**. Il nome visualizzato è nome descrittivo del piano visualizzato dagli utenti. Solo l'amministratore è possibile visualizzare il nome di risorsa, ovvero il nome che gli amministratori utilizzano per funzionare con il piano come una risorsa di gestione risorse di Azure.  
   ![Specificare i dettagli](media/azure-stack-create-plan/plan-name.png)

4. Creare un nuovo **gruppo di risorse**, o selezionarne uno esistente, come un contenitore per il piano.  
   ![Specificare il gruppo di risorse](media/azure-stack-create-plan/resource-group.png)

5. Selezionare **Services** e quindi selezionare la casella di controllo **Microsoft. COMPUTE**, **Microsoft. Network**, e **appartenga**. Scegliere poi **selezionare** per salvare la configurazione. Caselle di controllo vengono visualizzate quando il mouse passa su ogni opzione.  
   ![Selezionare i servizi](media/azure-stack-create-plan/services.png)

6. Selezionare **quote**, **appartenga (locale)**, quindi scegliere la quota predefinita oppure selezionare **Crea nuova quota** per personalizzare la quota.  
   ![Quote](media/azure-stack-create-plan/quotas.png)

7. Se si crea una nuova quota, immettere una **nome** per la quota > specificare i valori di quota > selezionare **OK**. Il **Crea quota** riquadro verrà chiuso.
   ![Nuova quota](media/azure-stack-create-plan/new-quota.png)

   È quindi possibile selezionare la nuova quota che è stato creato. Selezionando la quota viene assegnato e chiude il riquadro di selezione.  
   ![Assegnare la quota](media/azure-stack-create-plan/assign-quota.png)

8. Ripetere i passaggi 6 e 7 per creare e assegnare le quote per **Microsoft. Network (locale)** e **Microsoft. Compute (locale)**.  Quando tutti e tre i servizi sono state assegnate quote, sono interpretati simile all'immagine seguente.  
   ![Assegnazioni di quota completo](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Nel **quote** riquadro, scegliere **OK**e quindi nel **nuovo piano** riquadro, scegliere **crea** per creare il piano.  
    ![Creare il piano](media/azure-stack-create-plan/create.png)
10. Per visualizzare il nuovo piano, selezionare **tutte le risorse**, quindi eseguire la ricerca per il piano e selezionarne il nome. Se l'elenco delle risorse è lungo, utilizzare **ricerca** per individuare il piano in base al nome.  
   ![Rivedere il piano](media/azure-stack-create-plan/plan-overview.png)

### <a name="next-steps"></a>Passaggi successivi
[Creare un'offerta](azure-stack-create-offer.md)
