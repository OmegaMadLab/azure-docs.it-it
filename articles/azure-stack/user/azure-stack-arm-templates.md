---
title: Utilizzare i modelli di gestione risorse di Azure nello Stack di Azure | Documenti Microsoft
description: Informazioni su come utilizzare i modelli di gestione risorse di Azure nello Stack di Azure per le risorse di provisioning.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 9c4d538f77ae056163fd17aa547162a4ad3eff63
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Usare i modelli di Gestione risorse di Azure in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile utilizzare i modelli di Azure Resource Manager per distribuire ed eseguire il provisioning di tutte le risorse per l'applicazione in un'operazione singola, coordinata. È anche possibile ridistribuire i modelli per apportare modifiche alle risorse in un gruppo di risorse.

Questi modelli possono essere distribuiti con il portale di Microsoft Azure Stack, PowerShell, la riga di comando e Visual Studio.

I seguenti modelli di avvio rapido sono disponibili nel [GitHub](http://aka.ms/azurestackgithub).

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Distribuire SharePoint Server (distribuzione della disponibilità elevata)

Utilizzare l'estensione DSC PowerShell per creare una farm di SharePoint Server 2013 che include le risorse seguenti:

* Una rete virtuale
* Tre account di archiviazione
* Due servizi di bilanciamento del carico esterni
* Una macchina virtuale (VM) configurata come controller di dominio per una nuova foresta con un singolo dominio
* Una VM configurata come server autonomo di SQL Server 2014
* Una macchina virtuale configurata come una farm di SharePoint Server 2013 una macchina

## <a name="deploy-ad-non-high-availability-deployment"></a>Distribuire Active Directory (non-elevata--distribuzione con disponibilità)

Utilizzare l'estensione DSC PowerShell per creare un server di controller di dominio Active Directory che include le risorse seguenti:

* Una rete virtuale
* Un account di archiviazione
* Un servizio di bilanciamento del carico esterno
* Una macchina virtuale (VM) configurata come controller di dominio per una nuova foresta con un singolo dominio

## <a name="deploy-adsql-non-high-availability-deployment"></a>Distribuire Active Directory/SQL (non-elevata--distribuzione con disponibilità)

Utilizzare l'estensione DSC PowerShell per creare un server autonomo di SQL Server 2014 che include le risorse seguenti:

* Una rete virtuale
* Due account di archiviazione
* Un servizio di bilanciamento del carico esterno
* Una macchina virtuale (VM) configurata come controller di dominio per una nuova foresta con un singolo dominio
* Una VM configurata come server autonomo di SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Usare l'estensione DSC di PowerShell per configurare una macchina virtuale esistente di Gestione configurazione locale e registrarla a un server di pull DSC dell'account di Automazione di Azure.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Creare una macchina virtuale da un'immagine dell'utente

Creare una macchina virtuale da un'immagine dell'utente personalizzata. Questo modello distribuisce anche una rete virtuale (con DNS), un indirizzo IP pubblico e un'interfaccia di rete.

## <a name="basic-virtual-machine"></a>Macchina virtuale di base

Distribuire una macchina virtuale di Windows che include un'interfaccia di rete, indirizzo IP pubblico e una rete virtuale (con DNS).

## <a name="cancel-a-running-template-deployment"></a>Annullare la distribuzione di un modello in esecuzione

Per annullare una distribuzione modello in esecuzione, utilizzare il `Stop-AzureRmResourceGroupDeployment` cmdlet di PowerShell.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire modelli con il portale](azure-stack-deploy-template-portal.md)
* [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
