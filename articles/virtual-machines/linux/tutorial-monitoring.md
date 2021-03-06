---
title: Esercitazione - Monitorare e aggiornare le macchine virtuali Linux in Azure | Microsoft Docs
description: In questa esercitazione si apprenderà come monitorare la diagnostica di avvio e le metriche delle prestazioni e gestire gli aggiornamenti dei pacchetti in una macchina virtuale Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 0dc403d92855902daef09c91a5dd022beb23fd71
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Esercitazione: Come monitorare e aggiornare una macchina virtuale Linux in Azure

Per assicurarsi che le macchine virtuali in Azure vengano eseguite correttamente, è possibile esaminare la diagnostica di avvio e le metriche delle prestazioni e gestire gli aggiornamenti dei pacchetti. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la diagnostica di avvio in una macchina virtuale
> * Visualizzare la diagnostica di avvio
> * Visualizzare le metriche host
> * Abilitare l'estensione della diagnostica nella macchina virtuale
> * Visualizzare le metriche della macchina virtuale
> * Creare avvisi basati sulle metriche di diagnostica
> * Gestire gli aggiornamenti dei pacchetti
> * Monitorare le modifiche e l'inventario
> * Configurare il monitoraggio avanzato

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Creare una macchina virtuale

Per visualizzare la diagnostica e le metriche in azione, è necessaria una macchina virtuale. Creare prima un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupMonitor* nella località *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Creare quindi una macchina virtuale con il comando [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). L'esempio seguente crea una macchina virtuale denominata *myVM*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Abilitare la diagnostica di avvio

All'avvio delle macchine virtuali Linux, l'estensione diagnostica di avvio acquisisce l'output e lo memorizza nell'Archiviazione di Azure. Questi dati possono essere usati per risolvere i problemi di avvio della macchina virtuale. La diagnostica di avvio non viene abilitata automaticamente quando si crea una macchina virtuale Linux tramite l'interfaccia della riga di comando di Azure.

Prima di abilitare la diagnostica di avvio, è necessario creare un account di archiviazione per l'archiviazione dei log di avvio. Gli account di archiviazione devono avere un nome univoco globale, che abbia tra i 3 e i 24 caratteri e devono contenere solo numeri e lettere minuscole. Creare un account di archiviazione con il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create). In questo esempio, viene usata una stringa casuale per creare un nome univoco per l'account di archiviazione.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Quando si abilita la diagnostica di avvio, è necessario l'URI per il contenitore di archiviazione BLOB. Il comando seguente esegue una query sull'account di archiviazione per restituire questo URI. Il valore URI viene archiviato nei nomi di variabile *bloburi* e verrà usato nel passaggio successivo.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Abilitare ora la diagnostica di avvio con [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). Il valore `--storage` è l'URI del BLOB raccolto nel passaggio precedente.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Visualizzare la diagnostica di avvio

Quando la diagnostica di avvio è abilitata, ogni volta che si arresta e si avvia la macchina virtuale, in un file log vengono scritte le informazioni sul processo di avvio. Per questo esempio, deallocare prima la macchina virtuale con il comando [az vm deallocate](/cli/azure/vm#az_vm_deallocate) come illustrato di seguito:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Ora avviare la macchina virtuale con il comando [az vm start]( /cli/azure/vm#az_vm_stop) come illustrato di seguito:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

È possibile ottenere i dati della diagnostica di avvio per *myVM* con il comando [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) come illustrato di seguito:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Visualizzare le metriche host

Una macchina virtuale Linux è un host dedicato in Azure con cui interagisce. Le metriche per l'host vengono raccolte automaticamente e possono essere visualizzate nel portale di Azure come indicato di seguito:

1. Nel portale di Azure, fare clic su **Gruppi di risorse** selezionare **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.
1. Per visualizzare le prestazioni della macchina virtuale host, fare clic su **Metriche** nel pannello della macchina virtuale, quindi selezionare una metrica *[Host]* in **Metriche disponibili**.

    ![Visualizzare le metriche host](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Installare l'estensione di diagnostica

> [!IMPORTANT]
> Questo documento descrive la versione 2.3 dell'estensione Diagnostica per Linux, che è stata deprecata. La versione 2.3 sarà supportata fino al 30 giugno 2018.
>
> È invece possibile abilitare la versione 3.0 dell'estensione Diagnostica per Linux. Per altre informazioni, vedere [la documentazione](./diagnostic-extension.md).

Sono disponibili le metriche di base host, ma per visualizzare metriche specifiche per la VM e più granulari è necessario installare l'estensione Diagnostica di Azure nella VM. L'estensione Diagnostica di Azure consente un monitoraggio aggiuntivo e il recupero dei dati di diagnostica dalla macchina virtuale. È possibile visualizzare queste metriche delle prestazioni e creare avvisi in base al funzionamento della macchina virtuale. L'estensione Diagnostica viene installata tramite il portale di Azure come indicato di seguito:

1. Nel portale di Azure fare clic su **Gruppi di risorse**, selezionare **myResourceGroup** e quindi selezionare **myVM** nell'elenco delle risorse.
1. Fare clic su **Impostazioni di diagnostica**. L'elenco mostra che *Diagnostica di avvio* è già stata abilitata nella sezione precedente. Selezionare la casella di controllo per *Metriche di base*.
1. Nella sezione *Account di archiviazione* individuare e selezionare l'account *mydiagdata[1234]* creato nella sezione precedente.
1. Fare clic sul pulsante **Salva** .

    ![Visualizzare le metriche di diagnostica](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Visualizzare le metriche della macchina virtuale

È possibile visualizzare le metriche della macchina virtuale nello stesso modo in cui sono state visualizzate le metriche della macchina virtuale host:

1. Nel portale di Azure fare clic su **Gruppi di risorse**, selezionare **myResourceGroup** e quindi selezionare **myVM** nell'elenco delle risorse.
1. Per visualizzare le prestazioni della macchina virtuale, fare clic su **Metriche** nel pannello della macchina virtuale, quindi selezionare una metrica di diagnostica in **Metriche disponibili**.

    ![Visualizzare le metriche della macchina virtuale](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Creare avvisi

È possibile creare avvisi basati sulle metriche di prestazioni specifiche. Gli avvisi possono essere usati, ad esempio, per inviare una notifica quando l'uso medio della CPU supera una determinata soglia o lo spazio su disco disponibile è inferiore a una determinata quantità. Gli avvisi vengono visualizzati nel portale di Azure o possono essere inviati tramite posta elettronica. In risposta agli avvisi generati, è anche possibile attivare i runbook di Automazione di Azure o App per la logica di Azure.

L'esempio seguente crea un avviso per l'uso medio della CPU.

1. Nel portale di Azure fare clic su **Gruppi di risorse**, selezionare **myResourceGroup** e quindi selezionare **myVM** nell'elenco delle risorse.
2. Nel pannello della macchina virtuale fare clic su **Regole di avviso**, quindi fare clic su **Aggiungi avviso per la metrica** nella parte superiore del pannello degli avvisi.
3. Inserire un **Nome** per l'avviso, ad esempio *myAlertRule*
4. Per attivare un avviso quando la percentuale di CPU supera 1.0 per cinque minuti, lasciare tutte le altre impostazioni predefinite selezionate.
5. Facoltativamente, è possibile selezionare la casella per *Invia messaggio di posta elettronica a proprietari, collaboratori e lettori* per inviare una notifica tramite posta elettronica. L'azione predefinita è di presentare una notifica nel portale.
6. Fare clic sul pulsante **OK**.

## <a name="manage-package-updates"></a>Gestire gli aggiornamenti dei pacchetti

La gestione degli aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali Linux di Azure.
Direttamente dalla macchina virtuale è possibile valutare in modo rapido lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti ed esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente alla macchina virtuale.

Per informazioni sui prezzi, vedere [Prezzi di Automazione per la gestione degli aggiornamenti](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Abilitare Gestione aggiornamenti

Abilitare Gestione aggiornamenti per la macchina virtuale:

1. Sul lato sinistro della schermata selezionare **Macchine virtuali**.
2. Selezionare una macchina virtuale dall'elenco.
3. Nella schermata della macchina virtuale nella sezione **Operazioni** fare clic su **Gestione aggiornamenti**. Viene visualizzata la schermata **Abilita Gestione aggiornamenti**.

Viene eseguita una convalida per determinare se Gestione aggiornamenti è abilitata per la macchina virtuale.
La convalida include controlli per un'area di lavoro di Log Analytics e un account di Automazione collegato e verifica se la soluzione è presente nell'area di lavoro.

L'area di lavoro di [Log Analytics](../../log-analytics/log-analytics-overview.md) consente di raccogliere i dati generati da funzionalità e servizi, ad esempio Gestione aggiornamenti.
L'area di lavoro offre un'unica posizione per esaminare e analizzare i dati di più origini.
Per eseguire altre azioni nelle macchine virtuali che richiedono gli aggiornamenti, Automazione di Azure consente di eseguire runbook nelle macchine virtuali, ad esempio il download e l'applicazione degli aggiornamenti.

Il processo di convalida controlla anche se nella macchina virtuale è presente Microsoft Monitoring Agent (MMA) e un ruolo di lavoro ibrido per runbook di Automazione.
L'agente consente di comunicare con la macchina virtuale e ottenere informazioni sullo stato dell'aggiornamento.

Scegliere l'area di lavoro di Log Analytics e l'account di Automazione da usare e fare clic su **Abilita** per abilitare la soluzione. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

Se risultano mancanti durante l'onboarding, i prerequisiti seguenti vengono aggiunti automaticamente:

* Area di lavoro di [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automazione](../../automation/automation-offering-get-started.md)
* [Ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md) abilitato nella macchina virtuale

Viene visualizzata la schermata **Gestione aggiornamenti**. Configurare la posizione, l'area di lavoro di Log Analytics e l'account di Automazione da usare e fare clic su **Abilita**. Se i campi sono inattivi, significa che un'altra soluzione di automazione è abilitata per la VM e devono essere usati la stessa area di lavoro e lo stesso account di Automazione.

![Abilitare la soluzione Gestione aggiornamenti](./media/tutorial-monitoring/manage-updates-update-enable.png)

L'abilitazione della soluzione può richiedere fino a 15 minuti. Durante questo intervallo di tempo, non chiudere la finestra del browser. Dopo l'abilitazione della soluzione, le informazioni sugli aggiornamenti mancanti nella macchina virtuale passano a Log Analytics. Affinché i dati diventino disponibili per l'analisi, sarà necessario attendere da 30 minuti a 6 ore.

### <a name="view-update-assessment"></a>Visualizzare la valutazione degli aggiornamenti

Dopo aver abilitato **Gestione aggiornamenti**, viene visualizzata la schermata **Gestione aggiornamenti**. Dopo la valutazione degli aggiornamenti, viene visualizzato un elenco di aggiornamenti mancanti nella scheda **Aggiornamenti mancanti**.

 ![Visualizzare lo stato degli aggiornamenti](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Per installare gli aggiornamenti, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

Pianificare una nuova distribuzione di aggiornamenti per la macchina virtuale facendo clic su **Pianifica la distribuzione di aggiornamenti** nella parte superiore della schermata **Gestione aggiornamenti**. Nella schermata **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

* **Nome**: specificare un nome univoco per identificare la distribuzione di aggiornamenti.
* **Classificazioni aggiornamenti**: selezionare i tipi di software inclusi nella distribuzione di aggiornamenti. I tipi di classificazione sono:
  * Aggiornamenti critici e della sicurezza
  * Altri aggiornamenti
* **Aggiornamenti da escludere**: è possibile fornire un elenco di nomi di pacchetti da ignorare durante la distribuzione degli aggiornamenti. I nomi dei pacchetti supportano i caratteri jolly (ad esempio, \*kernel\*).

  ![Schermata di impostazioni della pianificazione di aggiornamenti](./media/tutorial-monitoring/manage-updates-exclude-linux.png)

* **Impostazioni di pianificazione**: è possibile accettare la data e l'ora predefinite, ovvero 30 minuti dopo l'ora corrente o specificare un'ora diversa.
  È anche possibile specificare se eseguire la distribuzione una sola volta o impostare una pianificazione ricorrente. Per impostare una pianificazione ricorrente, fare clic sull'opzione Ricorrente in Ricorrenza.

  ![Schermata di impostazioni della pianificazione di aggiornamenti](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Finestra di manutenzione (minuti)**: specificare il periodo di tempo nel quale eseguire la distribuzione di aggiornamenti. In questo modo è possibile garantire che le modifiche vengano eseguite negli intervalli di servizio definiti.

Dopo avere configurato la pianificazione, fare clic sul pulsante **Crea**. Viene nuovamente visualizzato il dashboard di stato.
Si noti che la tabella **Pianificata** mostra la pianificazione della distribuzione creata.

> [!WARNING]
> Per gli aggiornamenti che richiedono un riavvio, la macchina virtuale viene riavviata automaticamente.

### <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo avere avviato la distribuzione pianificata, è possibile visualizzare lo stato della distribuzione nella scheda **Distribuzioni di aggiornamenti** nella schermata **Gestione aggiornamenti**.
Se la distribuzione è in corso, viene visualizzato lo stato **In corso**. Quando la distribuzione viene completata correttamente, lo stato diventa **Completato**.
Se si verifica un errore in uno o più aggiornamenti della distribuzione, lo stato sarà **Parzialmente non riuscito**.
Fare clic sulla distribuzione di aggiornamenti completata per visualizzare il dashboard della distribuzione.

![Dashboard di stato di una distribuzione di aggiornamenti specifica](./media/tutorial-monitoring/manage-updates-view-results.png)

Il riquadro **Risultati aggiornamento** include un riepilogo del numero totale di aggiornamenti e dei risultati della distribuzione nella macchina virtuale.
La tabella di destra visualizza una descrizione dettagliata di ogni aggiornamento e i risultati dell'installazione che possono corrispondere a uno dei valori seguenti:

* **Tentativo non eseguito**: l'aggiornamento non è stato installato a causa di tempo disponibile non sufficiente basato sulla durata della finestra di manutenzione specificata.
* **Completato**: l'aggiornamento è stato completato
* **Non riuscito**: l'aggiornamento non è riuscito

Fare clic su **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Fare clic sul riquadro **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nella macchina virtuale di destinazione.

Fare clic su **Errori** per visualizzare informazioni dettagliate sugli errori della distribuzione.

## <a name="monitor-changes-and-inventory"></a>Monitorare le modifiche e l'inventario

È possibile raccogliere e visualizzare l'inventario per il software, i file, i daemon Linux, i servizi di Windows e le chiavi del Registro di sistema di Windows presenti nei computer. Tenendo traccia delle configurazioni dei computer, è possibile individuare i problemi operativi dell'ambiente e conoscere meglio lo stato dei computer.

### <a name="enable-change-and-inventory-management"></a>Abilitare la gestione delle modifiche e dell'inventario

Abilitare la gestione delle modifiche e dell'inventario per la macchina virtuale:

1. Sul lato sinistro della schermata selezionare **Macchine virtuali**.
2. Selezionare una macchina virtuale dall'elenco.
3. Nella sezione **Operazioni** della schermata della macchina virtuale fare clic su **Inventario** o **Rilevamento modifiche**. Verrà visualizzata la schermata **Enable Change Tracking and Inventory** (Abilita rilevamento modifiche e inventario).

Configurare la posizione, l'area di lavoro di Log Analytics e l'account di Automazione da usare e fare clic su **Abilita**. Se i campi sono inattivi, significa che un'altra soluzione di automazione è abilitata per la VM e devono essere usati la stessa area di lavoro e lo stesso account di Automazione. Sebbene le soluzioni siano presentate separatamente sul menu, rappresentano la stessa soluzione. L'abilitazione di una di queste le abilita entrambe per la macchina virtuale.

![Abilitare il rilevamento delle modifiche e dell'inventario](./media/tutorial-monitoring/manage-inventory-enable.png)

Dopo l'abilitazione della soluzione, la raccolta dell'inventario sulla macchina virtuale può richiedere alcuni minuti prima che i dati vengono visualizzati.

### <a name="track-changes"></a>Rilevare le modifiche

Nella macchina virtuale selezionare **Rilevamento modifiche** in **OPERAZIONI**. Fare clic su **Modifica impostazioni**. Verrà visualizzata la pagina **Rilevamento modifiche**. Selezionare il tipo di impostazione che si vuole rilevare e quindi fare clic su **+ Aggiungi** per configurare le impostazioni. L'opzione disponibile per Linux è **File Linux**

Per informazioni dettagliate sul rilevamento delle modifiche, vedere [Risolvere i problemi delle modifiche in una macchina virtuale](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Visualizzare l'inventario

Nella macchina virtuale selezionare **Inventario** in **OPERAZIONI**. Nella scheda **Software** è presente una tabella con l'elenco del software trovato. I dettagli generali di ogni record software sono visibili nella tabella. Questi dettagli includono il nome del software, la versione, l'autore, l'ora dell'ultimo aggiornamento.

![Visualizzare l'inventario](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorare i log attività e le modifiche

Dalla pagina **Rilevamento modifiche** della VM, selezionare **Gestisci connessione al log attività**. Si aprirà la pagina **Log attività di Azure**. Selezionare **Connetti** per connettere Rilevamento modifiche al log attività di Azure per la VM.

Con questa impostazione abilitata, passare alla pagina **Panoramica** della VM e selezionare **Arresta** per arrestare la macchina virtuale. Quando richiesto, selezionare **Sì** per arrestare la VM. Quando la VM è deallocata, selezionare **Avvia** per riavviarla.

Con l'arresto e l'avvio di una macchina virtuale viene registrato un evento nel registro attività. Tornare alla pagina **Rilevamento modifiche**. Selezionare la scheda **Eventi** nella parte inferiore della pagina. Dopo poco, gli eventi verranno visualizzati nel grafico e nella tabella. È possibile selezionare ogni evento per visualizzare le relative informazioni dettagliate.

![Visualizzare le modifiche nel log attività](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Il grafico mostra le modifiche nel tempo. Dopo aver aggiunto una connessione al log attività, il grafico a linee nella parte superiore visualizza gli eventi del log attività di Azure. Ogni riga dei grafici a barre rappresenta un tipo di modifica tracciabile diverso. Questi tipi sono i daemon, i file e il software Linux. La scheda delle modifiche visualizza i dettagli delle modifiche in ordine decrescente in base alla data della modifica, a partire dalla più recente.

## <a name="advanced-monitoring"></a>Monitoraggio avanzato

È possibile eseguire un monitoraggio più avanzato della macchina virtuale usando soluzioni quali Gestione aggiornamenti e Modifiche e inventario, disponibili in [Automazione di Azure](../../automation/automation-intro.md).

Quando si ha accesso all'area di lavoro di Log Analytics, è possibile trovare la chiave e l'identificatore dell'area di lavoro selezionando **Impostazioni avanzate** in **IMPOSTAZIONI**. Sostituire \<workspace-key\> e \<workspace-id\> con i valori dell'area di lavoro di Log Analytics. Sarà quindi possibile usare **az vm extension set** per aggiungere l'estensione alla macchina virtuale:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

Dopo alcuni minuti, la nuova macchina virtuale sarà visualizzata nell'area di lavoro di Log Analytics.

![Pannello OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati configurati, esaminati e gestiti gli aggiornamenti per una macchina virtuale. Si è appreso come:

> [!div class="checklist"]
> * Abilitare la diagnostica di avvio in una macchina virtuale
> * Visualizzare la diagnostica di avvio
> * Visualizzare le metriche host
> * Abilitare l'estensione della diagnostica nella macchina virtuale
> * Visualizzare le metriche della macchina virtuale
> * Creare avvisi basati sulle metriche di diagnostica
> * Gestire gli aggiornamenti dei pacchetti
> * Monitorare le modifiche e l'inventario
> * Configurare il monitoraggio avanzato

Passare all'esercitazione successiva per informazioni sul Centro sicurezza di Azure.

> [!div class="nextstepaction"]
> [Gestire la sicurezza delle VM](./tutorial-azure-security.md)
