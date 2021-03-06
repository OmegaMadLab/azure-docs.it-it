---
title: Configurare gli endpoint su una VM Windows classica | Microsoft Docs
description: Informazioni su come configurare gli endpoint per una macchina virtuale Windows classica nel portale di Azure per consentire la comunicazione con una macchina virtuale Windows in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: cca9adb40557cf7bf9e1d4129fc6bd61cbf0df4f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Come configurare gli endpoint in una macchina virtuale Windows classica in Azure
Tutte le macchine virtuali Windows create in Azure con il modello di distribuzione classico possono comunicare automaticamente mediante un canale di rete privato con altre macchine virtuali dello stesso servizio cloud o nella stessa rete virtuale. Tuttavia, i computer in Internet o altre reti virtuali richiedono gli endpoint per indirizzare il traffico di rete in ingresso a una macchina virtuale. Questo articolo è disponibile anche per le [macchine virtuali Linux](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Nel modello di distribuzione **Resource Manager** gli endpoint vengono configurati tramite **Gruppi di sicurezza di rete**. Per altre informazioni, vedere [Consentire l'accesso esterno alla VM mediante il portale di Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quando si crea una macchina virtuale Windows nel portale di Azure, gli endpoint comuni, ad esempio quelli di Desktop remoto e Comunicazione remota di Windows PowerShell, sono in genere creati automaticamente. È possibile configurare altri endpoint durante la creazione della macchina virtuale o successivamente all'occorrenza.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Passaggi successivi
* Per usare un cmdlet di Azure PowerShell per impostare un endpoint di VM, vedere [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Per usare un cmdlet di Azure PowerShell per gestire un ACL in un endpoint, vedere [Come gestire gli elenchi di controllo di accesso per gli endpoint tramite PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Se una macchina virtuale è stata creata nel modello di distribuzione Resource Manager, è possibile usare Azure PowerShell per [creare gruppi di sicurezza di rete](../../../virtual-network/tutorial-filter-network-traffic.md) per controllare il traffico verso la VM.
