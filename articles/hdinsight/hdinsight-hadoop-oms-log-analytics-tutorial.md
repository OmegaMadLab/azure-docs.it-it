---
title: Usare Log Analytics per monitorare i cluster Azure HDInsight | Microsoft Docs
description: Informazioni su come usare Azure Log Analytics per monitorare i processi in esecuzione in un cluster HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: e11c9672e2e96f3370c69b33f57a007f7d63ccf2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Usare Azure Log Analytics per monitorare i cluster HDInsight

Informazioni su come usare Azure Log Analytics per monitorare le operazioni del cluster Hadoop in HDInsight.

[Log Analytics](../log-analytics/log-analytics-overview.md) è un servizio che monitora gli ambienti cloud e locali per mantenerne la disponibilità e le prestazioni. Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini. 

## <a name="prerequisites"></a>prerequisiti

* **Una sottoscrizione di Azure**. Prima di iniziare questa esercitazione, è necessario disporre di un abbonamento ad Azure. Vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/free).

* **Un cluster HDInsight di Azure**. Attualmente, è possibile usare Log Analytics con i tipi di cluster HDInsight seguenti:

    * Hadoop
    * hbase
    * Interactive Query
    * Kafka
    * Spark
    * Storm

    Per istruzioni su come creare un cluster HDInsight, vedere [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Un'area di lavoro di Log Analytics**. Un'area di lavoro è un ambiente di Log Analytics univoco con un archivio dati, origini dati e soluzioni. È necessario avere un'area di lavoro di questo tipo già creata, da poter associare ai cluster Azure HDInsight. Per istruzioni, vedere [Creare un'area di lavoro di Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="enable-log-analytics-by-using-the-portal"></a>Abilitare Log Analytics tramite il portale

In questa sezione si configura un cluster Hadoop HDInsight esistente per usare un'area di lavoro di Azure Log Analytics per monitorare i processi, i log di debug e così via.

1. Nel portale di Azure aprire un cluster HDInsight.
2. Nel riquadro sinistro fare clic su **Monitoraggio**.
3. Nel riquadro destro fare clic su **Abilita**, selezionare un'area di lavoro di Log Analytics esistente e fare clic su **Salva**.

    ![Abilitare il monitoraggio per i cluster HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Abilitare il monitoraggio per i cluster HDInsight")

    Sono necessari alcuni minuti per salvare l'impostazione.  Al termine, è possibile visualizzare un pulsante **Aprire il dashboard OMS** nella parte superiore. 

    ![Aprire il dashboard Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Aprire il dashboard OMS")

5. Fare clic su **Aprire il dashboard OMS**.
6. Immettere le credenziali di Azure, se richiesto.

    ![Portale di Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Portale di Operations Management Suite")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Abilitare Log Analytics tramite Azure PowerShell

È possibile abilitare Log Analytics tramite Azure PowerShell. Il cmdlet è:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
```

Vedere [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Per disabilitare, il cmdlet è: 

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
```

Vedere [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).


## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere soluzioni di gestione di cluster HDInsight in Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
