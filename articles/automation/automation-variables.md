---
title: Asset di tipo variabile in Automazione di Azure
description: Gli asset di tipo variabile sono valori disponibili per tutti i runbook e le configurazioni DSC in Automazione di Azure.  Questo articolo illustra nel dettaglio le variabili e spiega come usarle nella creazione testuale e grafica.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ea6aae349bfbec0d1b6538010df42e7a0fb22d8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="variable-assets-in-azure-automation"></a>Asset di tipo variabile in Automazione di Azure

Gli asset di tipo variabile sono valori disponibili per tutti i runbook e le configurazioni DSC nell'account di automazione. Possono essere creati, modificati e recuperati dal portale di Azure, da Windows PowerShell e dall'interno di un runbook o di una configurazione DSC. Le variabili di automazione sono utili per gli scenari seguenti:

- Condivisione di un valore tra più runbook o configurazioni DSC.

- Condivisione di un valore tra più processi dello stesso runbook o configurazione DSC.

- Gestione di un valore dal portale o dalla riga di comando di Windows PowerShell usata da runbook o configurazioni DSC, ad esempio un set di elementi di configurazione comuni come un elenco specifico di nomi di VM, un gruppo di risorse specifico, un nome di dominio AD e così via.  

Le variabili di automazione vengono salvate in maniera permanente in modo da continuare a essere disponibili anche in caso di errore del runbook o della configurazione DSC. In tal modo, è anche possibile impostare in un runbook un valore che verrà quindi usato da un altro runbook oppure dallo stesso runbook o configurazione DSC alla successiva esecuzione.

Quando si crea una variabile, è possibile specificare che venga archiviata in modalità crittografata. Se una variabile viene crittografata, viene archiviata in modo sicuro in Automazione di Azure e non è possibile recuperarne il valore con il cmdlet [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) incluso nel modulo Azure PowerShell. L'unico modo in cui è possibile recuperare un valore crittografato è dall'attività **Get-AutomationVariable** in un runbook o configurazione DSC.

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di automazione. Questa chiave viene archiviata in Key Vault. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset.

## <a name="variable-types"></a>Tipi di variabile

Quando si crea una variabile con il portale di Azure, è necessario selezionare un tipo di dati nell'elenco a discesa, in modo che nel portale possa essere visualizzato il controllo appropriato per immettere il valore della variabile. La variabile non è limitata a questo tipo di dati, ma è necessario impostarla usando Windows PowerShell se si intende specificare un valore di tipo diverso. Se si specifica **Non definito**, il valore della variabile verrà impostato su **$null** e sarà necessario impostare il valore con il cmdlet [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) o l'attività **Set-AutomationVariable**. Non è possibile creare o modificare il valore per un tipo di variabile complesso nel portale, ma è possibile indicare un valore di qualsiasi tipo usando Windows PowerShell. I tipi complessi verranno restituiti come [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

È possibile archiviare più valori in una singola variabile creando una matrice o una tabella hash e salvandola nella variabile.

Di seguito è riportato un elenco dei tipi di variabile disponibili in Automazione:

* string
* Integer
* Datetime
* boolean
* Null

## <a name="azurerm-powershell-cmdlets"></a>Cmdlet di PowerShell AzureRM
Per AzureRM, per creare e gestire asset di credenziali di automazione con Windows PowerShell, vengono usati i cmdlet della tabella seguente. Sono inclusi nel [modulo AzureRM.Automation](/powershell/azure/overview), disponibile per l'uso nei runbook di Automazione e nelle configurazioni DSC.

| Cmdlets | DESCRIZIONE |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Recupera il valore di una variabile esistente.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Crea una nuova variabile e ne imposta il valore.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Rimuove una variabile esistente.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Imposta il valore di una variabile esistente.|

## <a name="activities"></a>attività
Le attività incluse nella tabella seguente vengono usate per accedere alle credenziali in un Runbook o nelle configurazioni DSC.

| attività | DESCRIZIONE |
|:---|:---|
|Get-AutomationVariable|Recupera il valore di una variabile esistente.|
|Set-AutomationVariable|Imposta il valore di una variabile esistente.|

> [!NOTE] 
> È consigliabile evitare di usare le variabili nel parametro –Name di **Get-AutomationVariable** in un runbook o una configurazione DSC perché ciò può complicare l'individuazione delle dipendenze tra i runbook o la configurazione DSC e le variabili di automazione in fase di progettazione.

Le funzioni nella tabella seguente vengono usate per accedere e recuperare le variabili in un runbook di Python2. 

|Funzioni Python2|DESCRIZIONE|
|:---|:---|
|automationassets.get_automation_variable|Recupera il valore di una variabile esistente. |
|automationassets.set_automation_variable|Imposta il valore di una variabile esistente. |

> [!NOTE] 
> È necessario importare il modulo "automationassets" nella parte superiore del runbook Python per poter accedere alle funzioni dell'asset.

## <a name="creating-a-new-automation-variable"></a>Creazione di una nuova variabile di automazione

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Per creare una nuova variabile con il portale di Azure

1. Dall'account di automazione fare clic sul riquadro **Asset** e poi sul pannello **Asset** selezionare **Variabili**.
2. Nel riquadro **Variabili** selezionare **Aggiungi variabile**.
3. Completare le opzioni nel pannello **Nuova variabile**, fare clic su **Crea** e salvare la nuova variabile.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Per creare una nuova variabile con Windows PowerShell

Il cmdlet [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) crea una nuova variabile e ne imposta il valore iniziale. È possibile recuperare il valore usando [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Se il valore è di tipo semplice, verrà restituito lo stesso tipo. Se è di tipo complesso, verrà restituito un **PSCustomObject** .

I comandi di esempio seguenti mostrano come creare una variabile di tipo stringa e restituirne il valore.

    New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

I comandi di esempio seguenti mostrano come creare una variabile di tipo complesso e restituirne le relative proprietà. In questo caso, viene usato un oggetto macchina virtuale recuperato da **Get-AzureRmVm**.

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress



## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Uso di una variabile in un Runbook o in una configurazione DSC

Usare l'attività **Set-AutomationVariable** per impostare il valore di una variabile di automazione in un runbook di PowerShell o una configurazione DSC e **Get-AutomationVariable** per recuperarlo. Non è consigliabile usare il cmdlet **Set-AzureRMAutomationVariable** o **Get-AzureRMAutomationVariable** in un runbook o una configurazione DSC perché sono meno efficienti rispetto alle attività del flusso di lavoro. Non è inoltre possibile recuperare il valore delle variabili sicure con **Get-AzureRMAutomationVariable**. L'unico modo per creare una nuova variabile dall'interno di un runbook o una configurazione DSC consiste nell'usare il cmdlet [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable).


### <a name="textual-runbook-samples"></a>Esempi di runbook testuali

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Impostazione e recupero di un valore semplice da una variabile

I comandi di esempio seguenti mostrano come impostare e recuperare una variabile in un runbook testuale. In questo esempio si presuppone che siano già state create variabili di tipo integer denominate *NumberOfIterations* e *NumberOfRunnings* e una variabile di tipo stringa denominata *SampleMessage*.

    $NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Impostazione e recupero di un oggetto complesso in una variabile

Il codice di esempio seguente mostra come aggiornare una variabile con un valore complesso in un runbook testuale. In questo esempio una macchina virtuale di Azure viene recuperata con **Get-AzureVM** e salvata in una variabile di automazione esistente.  Come illustrato in [Tipi di variabile](#variable-types), viene archiviata come PSCustomObject.

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm

Nel codice seguente il valore viene recuperato dalla variabile e usato per avviare la macchina virtuale.

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Impostazione e recupero di una raccolta in una variabile

Il codice di esempio seguente mostra come usare una variabile con una raccolta di valori complessi in un runbook testuale. In questo esempio più macchine virtuali di Azure vengono recuperate con **Get-AzureVM** e salvate in una variabile di automazione esistente. Come illustrato in [Tipi di variabile](#variable-types), viene archiviata una raccolta di PSCustomObject.

    $vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

Nel codice seguente la raccolta viene recuperata dalla variabile e usata per avviare ogni macchina virtuale.

    $vmValues = Get-AutomationVariable -Name "MyComplexVariable"
    ForEach ($vmValue in $vmValues)
    {
       if ($vmValue.PowerState -eq 'Stopped') {
          Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
       }
    }
    
#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Impostazione e recupero di una variabile in Python2
L'esempio di codice seguente illustra come usare una variabile, impostare una variabile e gestire un'eccezione per una variabile non esistente in un runbook di Python2.

    import automationassets
    from automationassets import AutomationAssetNotFound

    # get a variable
    value = automationassets.get_automation_variable("test-variable")
    print value

    # set a variable (value can be int/bool/string)
    automationassets.set_automation_variable("test-variable", True)
    automationassets.set_automation_variable("test-variable", 4)
    automationassets.set_automation_variable("test-variable", "test-string")

    # handle a non-existent variable exception
    try:
        value = automationassets.get_automation_variable("non-existing variable")
    except AutomationAssetNotFound:
        print "variable not found"


### <a name="graphical-runbook-samples"></a>Esempi di runbook grafici

In un runbook grafico è possibile aggiungere **Get-AutomationVariable** o **Set-AutomationVariable** facendo clic con il pulsante destro del mouse sulla variabile nel riquadro della libreria dell'editor grafico e scegliendo l'attività desiderata.

![Aggiungere una variabile al canvas](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Impostazione dei valori in una variabile
La figura seguente illustra attività di esempio per aggiornare una variabile con un valore semplice in un runbook grafico. In questo esempio viene recuperata una singola macchina virtuale di Azure con **Get-AzureRmVM** e il nome computer viene salvato in una variabile di automazione esistente di tipo stringa. Non è importante se il [collegamento è una pipeline o una sequenza](automation-graphical-authoring-intro.md#links-and-workflow) poiché è prevista la presenza di un solo oggetto nell'output.

![Impostare una variabile semplice](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come collegare attività nella creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md#links-and-workflow)
* Per iniziare a usare runbook grafici, vedere [Il primo runbook grafico](automation-first-runbook-graphical.md) 
