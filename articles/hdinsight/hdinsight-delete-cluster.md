---
title: Come eliminare un cluster HDInsight - Azure | Microsoft Docs
description: Informazioni sui vari modi per eliminare un cluster HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 01c59d4970bca54417c9b860ec177ecf5a37d8c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure

La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più. Questo documento spiega come eliminare un cluster tramite il portale di Azure, Azure PowerShell e l'interfaccia della riga di comando di Azure 1.0.

> [!IMPORTANT]
> L'eliminazione di un cluster HDInsight non comporta l'eliminazione degli account di archiviazione di Azure o Data Lake Store associati al cluster. È possibile usare nuovamente in futuro i dati archiviati in questi servizi.

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare il cluster HDInsight. Se il cluster HDInsight non è bloccato sul dashboard, è possibile cercarlo per nome usando il campo di ricerca.
   
    ![ricerca nel portale](./media/hdinsight-delete-cluster/navbar.png)

2. Nelle impostazioni del cluster selezionare l'icona **Elimina**. Quando richiesto, selezionare **Sì** per eliminare il cluster.
   
    ![icona di eliminazione](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Da un prompt di PowerShell, usare il comando seguente per eliminare il cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

## <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0

Da un prompt, usare il comando seguente per eliminare il cluster:

    azure hdinsight cluster delete CLUSTERNAME

Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

> [!NOTE]
> L'interfaccia della riga di comando di Azure 2.0 non supporta attualmente l'eliminazione dei cluster HDInsight (23 ottobre 2017).
