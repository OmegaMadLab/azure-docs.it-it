---
title: Monitorare l'integrità di Azure Kubernetes Service (AKS) (anteprima) | Microsoft Docs
description: Questo articolo descrive come esaminare le prestazioni del contenitore AKS per comprendere rapidamente l'uso dell'ambiente Kubernetes ospitato.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: magoedte
ms.openlocfilehash: f0501d4404375ee44b96ae4514c15e69b616d38a
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Monitorare l'integrità del contenitore Azure Kubernetes Service (AKS) (anteprima)

In questo articolo viene descritto come configurare e usare l'integrità dei contenitori di Monitoraggio di Azure per monitorare le prestazioni dei carichi di lavoro distribuiti in ambienti Kubernetes ospitati in Azure Kubernetes Service (AKS).  Il monitoraggio del cluster e dei contenitori Kubernetes è critico, soprattutto quando si gestisce un cluster di produzione su larga scala con più app.

L'integrità dei contenitori offre la possibilità di eseguire il monitoraggio delle prestazioni raccogliendo metriche sulla memoria e sul processore da controller, nodi e contenitori disponibili in Kubernetes tramite l'API Metriche.  Dopo aver abilitato integrità dei contenitori, queste metriche vengono raccolte automaticamente tramite una versione inserita nei contenitori dell'agente di OMS per Linux e archiviate nell'area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md).  Le visualizzazioni predefinite incluse mostrano i carichi di lavoro nei contenitori e gli elementi che influiscono sull'integrità delle prestazioni del cluster Kubernetes, in modo da poter comprendere:  

* I contenitori in esecuzione nel nodo e il relativo uso medio del processore e della memoria per identificare i colli di bottiglia delle risorse
* Identificare la posizione in cui risiede il contenitore in un controller e/o i pod per visualizzare le prestazioni complessive per un controller o un pod 
* Esaminare l'uso delle risorse dei carichi di lavoro in esecuzione nell'host non correlati ai processi standard che supportano il pod
* Comprendere il comportamento del cluster in condizioni di carico medio e massimo per identificare le esigenze di capacità e determinare il carico massimo che è in grado di sostenere 

Per informazioni sul monitoraggio e la gestione degli host di contenitori Docker e Windows per visualizzare la configurazione, il controllo e l'uso delle risorse, vedere la [soluzione Monitoraggio contenitori](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Requisiti 
Prima di iniziare, esaminare i dettagli seguenti in modo da poter comprendere i prerequisiti supportati.

- Sono supportate le versioni seguenti del cluster AKS: da 1.7.7 a 1.9.6.
- Un agente OMS nel contenitore per la versione Linux microsoft/oms:ciprod04202018 e successive. Questo agente viene installato automaticamente durante l'onboarding dell'integrità dei contenitori.  
- Un'area di lavoro di Log Analytics.  Può essere creata quando si abilita il monitoraggio del nuovo cluster AKS oppure è possibile crearne una tramite [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) o dal [portale di Azure](../log-analytics/log-analytics-quick-create-workspace.md).


## <a name="components"></a>Componenti 

Questa funzionalità si basa su un agente OMS nel contenitore per Linux per la raccolta dei dati su prestazioni ed eventi da tutti i nodi del cluster.  L'agente viene distribuito e registrato automaticamente con l'area di lavoro di Log Analytics specificata dopo aver abilitato il monitoraggio del contenitore. 

>[!NOTE] 
>Se è già stato distribuito un cluster AKS, il monitoraggio può essere abilitato usando un modello di Azure Resource Manager, come illustrato più avanti in questo articolo. Non è possibile usare `kubectl` per aggiornare, eliminare, distribuire o ridistribuire l'agente.  
>

## <a name="log-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Abilitare il monitoraggio dell'integrità dei contenitori per un nuovo cluster
È possibile abilitare il monitoraggio del cluster AKS solo quando lo si distribuisce dal portale di Azure.  Seguire la procedura descritta nell'articolo introduttivo [Distribuire un cluster Azure Kubernetes Service (AKS)](../aks/kubernetes-walkthrough-portal.md).  Nella pagina **Monitoraggio** selezionare **Sì** per l'opzione **Abilita monitoraggio** da abilitare, quindi selezionare un'area di lavoro esistente di Log Analytics o crearne una nuova.  

Dopo avere abilitato il monitoraggio e completato tutte le attività di configurazione, è possibile monitorare le prestazioni del cluster in due modi:

1. Direttamente dal cluster AKS, selezionando **Integrità** nel riquadro sinistro.<br><br> 
2. Facendo clic sul riquadro **Monitor container health** (Monitora integrità contenitori) nella pagina del cluster AKS per il cluster selezionato.  In Monitoraggio di Azure selezionare **Integrità** dal riquadro sinistro.  

![Opzioni per la selezione dell'integrità dei contenitori in AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Dopo aver abilitato il monitoraggio, potrebbero essere necessari circa 15 minuti prima di poter visualizzare i dati operativi per il cluster.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Abilitare il monitoraggio dell'integrità dei contenitori per cluster gestiti esistenti
L'abilitazione del monitoraggio del contenitore AKS già distribuito non può essere effettuata dal portale: può essere eseguita solo tramite il modello di Azure Resource Manager fornito con il cmdlet PowerShell **New-AzureRmResourceGroupDeployment** o l'interfaccia della riga di comando di Azure.  Un modello JSON specifica la configurazione per l'abilitazione del monitoraggio e l'altro modello JSON contiene i valori dei parametri da configurare per specificare quanto segue:

* ID risorsa del contenitore AKS 
* Gruppo di risorse in cui è distribuito il cluster 
* Area di lavoro di Log Analytics e area di Azure in cui creare l'area di lavoro 

L'area di lavoro di Log Analytics deve essere creata manualmente.  Per creare l'area di lavoro, è possibile configurarne una tramite [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), con [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) o dal [portale di Azure](../log-analytics/log-analytics-quick-create-workspace.md).

Se non si ha familiarità con i concetti relativi alla distribuzione delle risorse tramite un modello con PowerShell, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) oppure [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) per l'interfaccia della riga di comando di Azure.

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installare e usare l'interfaccia della riga di comando in locale.  È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.27 o successiva. Eseguire `az --version` per identificare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Creare ed eseguire il modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster resource id"
        }
    },
    "aksResourceLocation": {
      "type": "string",
      "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
        }
      },
      "workspaceId": {
        "type": "string",
        "metadata": {
          "description": "Azure Monitor Log Analytics resource id"
        }
      },
      "workspaceRegion": {
        "type": "string",
        "metadata": {
          "description": "Azure Monitor Log Analytics workspace region"
        }
      }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": true,
              "config": {
                "logAnalyticsWorkspaceResourceID": "[parameters('workspaceId')]"
              }
            }
          }
        }
      },
      {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
    }
    ```

2. Salvare questo file come **existingClusterOnboarding.json** in una cartella locale.
3. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "East US"
        },
        "workspaceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "workspaceRegion": {
          "value": "eastus"
        }
      }
    }
    ```

4. Modificare il valore di **aksResourceId** e **aksResourceLocation** con i valori indicati nella pagina **Panoramica del servizio contenitore di Azure** per il cluster AKS.  Il valore di **workspaceId** deve essere il nome di un'area di lavoro di Log Analytics. Specificare inoltre la posizione in cui creare l'area di lavoro in **workspaceRegion**.    
5. Salvare questo file come **existingClusterParam.json** in una cartella locale.
6. A questo punto è possibile distribuire il modello. 

    * Usare i comandi di PowerShell seguenti dalla cartella che contiene il modello:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente che include il risultato:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Per eseguire il comando seguente con l'interfaccia della riga di comando di Azure in Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente che include il risultato:

        ```azurecli
        provisioningState       : Succeeded
        ```
Dopo aver abilitato il monitoraggio, potrebbero essere necessari circa 15 minuti prima di poter visualizzare i dati operativi per il cluster.  

## <a name="verify-agent-deployed-successfully"></a>Verificare il completamento della distribuzione dell'agente
Per verificare che l'agente OMS sia stato distribuito correttamente, eseguire il comando seguente: ` kubectl get ds omsagent -—namespace=kube-system`.

Se la distribuzione è stata eseguita correttamente, l'output dovrebbe essere simile al seguente:

```
User@aksuser:~$ kubectl get ds omsagent -—namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Visualizzare l'uso delle prestazioni
Quando si apre l'integrità dei contenitori, la pagina visualizza immediatamente l'uso delle prestazioni dei nodi del cluster.  La visualizzazione delle informazioni sul cluster AKS è organizzata in base a tre prospettive:

- Nodi 
- Controller  
- Contenitori

La gerarchia di righe segue il modello a oggetti Kubernetes a partire da un nodo del cluster.  Espandendo il nodo, è possibile visualizzare uno o più pod in esecuzione nel nodo e, se sono presenti più contenitori raggruppati in un pod, questi vengono visualizzati nell'ultima riga nella gerarchia.<br><br> ![Gerarchia di nodi Kubernetes di esempio nella vista prestazioni](./media/monitoring-container-health/container-performance-and-health-view-03.png)

È possibile selezionare controller o contenitori nella parte superiore della pagina e controllare lo stato e l'uso delle risorse per tali oggetti.  Usare le caselle a discesa nella parte superiore della schermata per filtrare in base a spazio dei nomi, servizio e nodo. Se invece si vuole controllare l'uso della memoria, dall'elenco a discesa **Metrica** selezionare **Memoria RSS** o **Working set della memoria**.  **Memoria RSS** è supportato solo per Kubernetes versione 1.8 e successive. In caso contrario, per i valori di **% media** verrà visualizzato *NaN%*, che è un valore di tipo di dati numerico che rappresenta un valore non definito o non rappresentabile. 

![Vista prestazioni dei nodi del contenitore](./media/monitoring-container-health/container-performance-and-health-view-04.png)

Per impostazione predefinita, i dati sulle prestazioni sono basati sulle ultime sei ore, ma è possibile modificare la finestra tramite l'elenco a discesa **Intervallo di tempo** disponibile nell'angolo superiore destro della pagina. Attualmente, la pagina non viene aggiornata in modo automatico, quindi è necessario aggiornarla manualmente. 

Nell'esempio seguente è possibile osservare che per il nodo *aks-agentpool-3402399-0* il valore di **Contenitori** è 10, ovvero un rollup del numero totale di contenitori distribuiti.<br><br> ![Esempio di rollup dei contenitori per ogni nodo](./media/monitoring-container-health/container-performance-and-health-view-07.png)<br><br> In questo modo, è possibile determinare rapidamente se non è presente il bilanciamento corretto dei contenitori tra nodi del cluster.  

Nella tabella seguente sono descritte le informazioni presentate quando si visualizzano i nodi.

| Colonna | DESCRIZIONE | 
|--------|-------------|
| NOME | Nome dell'host |
| Status | Visualizzazione Kubernetes dello stato del nodo |
| % MEDIA | Percentuale media di nodi in base alla metrica selezionata per l'intervallo di tempo selezionato. |
| MEDIA | Valore medio effettivo dei nodi in base alla metrica selezionata per l'intervallo di tempo selezionato.  Il valore medio viene misurato dal limite di CPU/memoria impostato per un nodo. Per i pod e i contenitori, è il valore medio segnalato dall'host. |
| Contenitori | Numero di contenitori. |
| Uptime | Rappresenta il tempo dall'avvio o dal riavvio di un nodo. |
| Pod | Solo per i contenitori. Mostra i pod in cui è presente. |
| Controller | Solo per i contenitori e i pod. Mostra il controller in cui è presente. Non tutti i pod saranno in un controller, pertanto per alcuni potrebbe essere visualizzato N/A. | 
| % media tendenza | Grafico a barre della tendenza basato sulla metrica relativa alla percentuale media per il contenitore e il nodo. |


Dal selettore scegliere **Controller**.<br><br> ![Selezionare la visualizzazione controller](./media/monitoring-container-health/container-performance-and-health-view-08.png)

Qui è possibile visualizzare l'integrità delle prestazioni dei controller.<br><br> ![Vista prestazioni dei controller di <nome>](./media/monitoring-container-health/container-performance-and-health-view-05.png)

La gerarchia di righe inizia con un controller. Espandendo il controller, vengono visualizzati uno o più pod oppure uno o più contenitori.  Espandendo un pod, nell'ultima riga viene mostrato il contenitore raggruppato nel pod.  

Nella tabella seguente sono descritte le informazioni presentate quando si visualizzano i controller.

| Colonna | DESCRIZIONE | 
|--------|-------------|
| NOME | Nome del tipo di controller.|
| Status | Stato dei contenitori quando l'esecuzione è stata completata con uno stato, ad esempio *Terminato*, *Non riuscito*, *Arrestato* oppure *In pausa*. Se il contenitore è in esecuzione, ma lo stato non è stato presentato correttamente o non è stato rilevato dall'agente e non è stata inviata alcuna risposta per più di 30 minuti, lo stato sarà *Sconosciuto*. |
| % MEDIA | Rollup della media della percentuale media di ogni entità per la metrica selezionata. |
| MEDIA | Rollup del numero medio di millicore della CPU o delle prestazioni della memoria del contenitore.  Il valore medio viene misurato dal limite di CPU/memoria impostato per un pod. |
| Contenitori | Numero totale di contenitori per il controller o il pod. |
| Riavvii | Rollup del numero di riavvii dai contenitori. |
| Uptime | Rappresenta il tempo dall'avvio di un contenitore. |
| Pod | Solo per i contenitori. Mostra i pod in cui è presente. |
| Nodo | Solo per i contenitori e i pod. Mostra il controller in cui è presente. | 
| % media tendenza | Grafico a barre della tendenza che mostra la metrica relativa alla percentuale media per il contenitore. |

Dal selettore scegliere **Contenitori**.<br><br> ![Selezionare la visualizzazione contenitori](./media/monitoring-container-health/container-performance-and-health-view-09.png)

Qui è possibile visualizzare l'integrità delle prestazioni dei contenitori.<br><br> ![Vista prestazioni dei controller di <nome>](./media/monitoring-container-health/container-performance-and-health-view-06.png)

Nella tabella seguente sono descritte le informazioni presentate quando si visualizzano i contenitori.

| Colonna | DESCRIZIONE | 
|--------|-------------|
| NOME | Nome del tipo di controller.|
| Status | Rollup dello stato dei contenitori, se presenti. |
| % MEDIA | Rollup della media della percentuale media di ogni entità per la metrica selezionata. |
| MEDIA | Rollup del numero medio di millicore della CPU o delle prestazioni della memoria del contenitore. Il valore medio viene misurato dal limite di CPU/memoria impostato per un pod. |
| Contenitori | Numero totale di contenitori per il controller.|
| Riavvii | Rappresenta il tempo dall'avvio di un contenitore. |
| Uptime | Rappresenta il tempo dall'avvio o dal riavvio di un contenitore. |
| Pod | Informazioni sul pod in cui è presente. |
| Nodo |  Nodo in cui è presente il contenitore.  | 
| % media tendenza | Grafico a barre della tendenza che mostra la metrica relativa alla percentuale media per il contenitore. |

## <a name="container-data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati dei contenitori
L'integrità dei contenitori raccoglie le varie metriche delle prestazioni e i vari dati di log da host del contenitore e contenitori. I dati vengono raccolti ogni tre minuti.

### <a name="container-records"></a>Record dei contenitori

La tabella seguente mostra esempi di record raccolti dall'integrità dei contenitori e i tipi di dati visualizzati nei risultati della ricerca log.

| Tipo di dati | Tipo di dati in Ricerca log | Fields |
| --- | --- | --- |
| Prestazioni per host e contenitori | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Inventario contenitori | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventario delle immagini dei contenitori | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Log contenitori | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Log servizio contenitori | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Inventario di nodi contenitore | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Processo contenitore | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventario dei pod in un cluster Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId , ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventario dei nodi di un cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventi di Kubernetes | `KubeEvents_CL` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Servizi nel cluster Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriche delle prestazioni per la parte dei nodi del cluster Kubernetes | Perf &#124; where ObjectName == “K8SNode” | cpuUsageNanoCores, , memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes | 
| Metriche delle prestazioni per la parte dei contenitori del cluster Kubernetes | Perf &#124; where ObjectName == “K8SContainer” | cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes | 

## <a name="search-logs-to-analyze-data"></a>Eseguire ricerche nei log per analizzare i dati
Log Analytics consente di individuare tendenze, diagnosticare i colli di bottiglia, effettuare previsioni o correlare dati che consentono di determinare se la configurazione corrente del cluster è ottimale.  Sono disponibili ricerche predefinite nei log che è possibile iniziare immediatamente a usare o personalizzare per restituire le informazioni nel modo che si preferisce. 

È possibile eseguire un'analisi interattiva dei dati nell'area di lavoro selezionando l'opzione **Visualizza log**, disponibile all'estrema destra quando si espande un contenitore.  Verrà visualizzata la pagina **Ricerca log** sopra la pagina in cui ci si trovava nel portale.<br><br> ![Analizzare i dati in Log Analytics](./media/monitoring-container-health/container-performance-and-health-view-logs-01.png)   

L'output dei log del contenitore inoltrato a Log Analytics è costituito da STDOUT e STDERR. Poiché l'integrità dei contenitori monitora Kubernetes gestito da Azure (AKS), Kube-system attualmente non viene raccolto a causa della grande quantità di dati generati.     

### <a name="example-log-search-queries"></a>Esempio di query di ricerca log
Spesso è utile compilare query iniziando con qualche esempio da modificare in funzione dei requisiti. È possibile provare a usare le query di esempio seguenti prima di compilare query più avanzate.

| Query | DESCRIZIONE | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Elencare tutte le informazioni sul ciclo di vita del contenitore| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Eventi di Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Inventario delle immagini | 
| **In Analisi avanzata selezionare i grafici a linee**:<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | CPU del contenitore | 
| **In Analisi avanzata selezionare i grafici a linee**:<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Memoria del contenitore |

## <a name="how-to-stop-monitoring-with-container-health"></a>Come interrompere il monitoraggio con l'integrità dei contenitori
Se dopo aver abilitato il monitoraggio del contenitore AKS si decide che non è più necessario eseguire il monitoraggio, è possibile *rifiutare esplicitamente* usando il modello di Azure Resource Manager fornito con il cmdlet PowerShell **New-AzureRmResourceGroupDeployment** o l'interfaccia della riga di comando di Azure.  Un modello JSON specifica la configurazione da *rifiutare esplicitamente* e l'altro modello JSON contiene i valori dei parametri da configurare per specificare l'ID risorsa del cluster AKS e il gruppo di risorse in cui è distribuito il cluster.  Se non si ha familiarità con i concetti relativi alla distribuzione delle risorse tramite un modello con PowerShell, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) oppure [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) per l'interfaccia della riga di comando di Azure.

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installare e usare l'interfaccia della riga di comando in locale.  È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.27 o successiva. Eseguire `az --version` per identificare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Creare ed eseguire il modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster resource id"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. Salvare il file con il nome **OptOutTemplate.json** in una cartella locale.
3. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Modificare il valore di **aksResourceId** e **aksResourceLocation** con i valori del cluster AKS, indicati nella pagina **Proprietà** per il cluster selezionato.<br><br> ![Pagina delle proprietà del contenitore](./media/monitoring-container-health/container-properties-page.png)<br>

    Nella pagina **Proprietà** copiare anche l'**ID risorsa dell'area di lavoro**.  Questo valore è necessario se si decide di eliminare l'area di lavoro di Log Analytics in un secondo momento, operazione che non viene eseguita nell'ambito di questo processo.  

5. Salvare il file con il nome **OptOutParam.json** in una cartella locale.
6. A questo punto è possibile distribuire il modello. 

    * Per usare i comandi di PowerShell seguenti dalla cartella che contiene il modello:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente che include il risultato:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Per eseguire il comando seguente con l'interfaccia della riga di comando di Azure in Linux:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente che include il risultato:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Se l'area di lavoro è stata creata solo per supportare il monitoraggio del cluster e non è più richiesta, è necessario eliminarla manualmente. Se non si ha familiarità con le procedure per l'eliminazione di un'area di lavoro, vedere [Eliminare un'area di lavoro di Azure Log Analytics con il portale di Azure](../log-analytics/log-analytics-manage-del-workspace.md).  Non dimenticare l'**ID risorsa dell'area di lavoro** che è stato copiato in precedenza nel passaggio 4, perché sarà necessario in seguito.  

## <a name="troubleshooting"></a>risoluzione dei problemi
Questa sezione fornisce informazioni sulla risoluzione dei problemi relativi all'integrità dei contenitori.

Se l'integrità dei contenitori è stata abilitata e configurata correttamente ma non è possibile visualizzare informazioni sullo stato o risultati in Log Analytics quando si esegue una ricerca log, è possibile eseguire la procedura seguente per diagnosticare il problema.   

1. Controllare lo stato dell'agente eseguendo il comando seguente: `kubectl get ds omsagent --namespace=kube-system`

    Se l'agente è in esecuzione in tutti i nodi del cluster, l'output dovrebbe essere simile al seguente.  Ad esempio, questo cluster dispone di due nodi e il valore dovrebbe essere uguale al numero di nodi.  

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Controllare lo stato del pod per verificare se è in esecuzione o meno eseguendo il comando seguente: `kubectl get pods --namespace=kube-system`

    L'output dovrebbe essere simile al seguente con uno stato *In esecuzione* per omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

3. Controllare i log dell'agente. Quando l'agente nei contenitori viene distribuito, effettua un rapido controllo eseguendo i comandi OMI e visualizza la versione dell'agente e il provider Docker. Per verificare che l'onboarding dell'agente sia stato effettuato correttamente, eseguire il comando seguente: `kubectl logs omsagent-484hw --namespace=kube-system`

    Lo stato dovrebbe essere simile al seguente:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>Passaggi successivi

[Eseguire ricerche nei log](../log-analytics/log-analytics-log-search.md) per visualizzare informazioni dettagliate sull'integrità dei contenitori e sulle prestazioni delle applicazioni.  