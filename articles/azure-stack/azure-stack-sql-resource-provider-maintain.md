---
title: Utilizzo di database SQL Azure stack | Documenti Microsoft
description: Informazioni su come distribuire i database SQL come servizio in Azure Stack e la procedura per distribuire l'adapter di provider di risorse di SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 53436d131672622ae1a72a1bb84d5aa83fdbdc0c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
---
# <a name="maintenance-operations"></a>Operazioni di manutenzione 
Il provider di risorse SQL è un controllo bloccato inattivo macchina virtuale. Aggiornamento della protezione della macchina virtuale la risorsa del provider può essere eseguita tramite l'endpoint di PowerShell Just Enough Administration (JEA) _DBAdapterMaintenance_. Uno script viene fornito con il pacchetto di installazione del componente per semplificare queste operazioni.

## <a name="patching-and-updating"></a>L'applicazione di patch e aggiornamento
Il provider di risorse SQL non viene gestito come parte dello Stack di Azure non è un componente aggiuntivo. Microsoft fornirà gli aggiornamenti al provider di risorse SQL in base alle esigenze. Il provider di risorse SQL viene creata un'istanza in un _utente_ macchina virtuale con la sottoscrizione del Provider predefinito. Pertanto, è necessario fornire le patch di Windows, le firme antivirus, e così via. Aggiornamenti di Windows di pacchetti forniti come parte del ciclo di patch e aggiornamento può essere utilizzati per applicare gli aggiornamenti per la macchina virtuale Windows. Quando viene rilasciato un adapter aggiornato, viene fornito uno script per applicare l'aggiornamento. Questo script crea una nuova VM RP e qualsiasi stato che si dispone già di eseguire la migrazione.

 ## <a name="backuprestoredisaster-recovery"></a>Backup/Restore/ripristino di emergenza
 Il provider di risorse SQL non è eseguito come parte del processo di Azure Stack BC ripristino di emergenza, è un componente aggiuntivo. Verranno forniti per semplificare gli script:
- Backup delle informazioni necessarie per lo stato (archiviate in un account di archiviazione di Azure Stack)
- Ripristino al relying Party non è più necessario un ripristino completo dello stack.
I server di database devono essere recuperati prima (se necessario), prima che la risorsa viene ripristinato provider.

## <a name="updating-sql-credentials"></a>Aggiornamento delle credenziali SQL
È responsabile per la creazione e gestione di account di amministratore di sistema nel computer SQL Server. Il provider di risorse è necessario un account con i privilegi per gestire i database per conto degli utenti: non è necessario l'accesso ai dati in questi database. Se è necessario aggiornare le password sa di SQL Server, è possibile utilizzare la funzionalità di aggiornamento dell'interfaccia di amministrazione del provider di risorse per modificare la password archiviata utilizzata dal provider di risorse. Queste password sono archiviate in un insieme di credenziali chiave sull'istanza dello Stack di Azure.

Per modificare le impostazioni, fare clic su **Sfoglia** &gt; **risorse amministrative** &gt; **server di Hosting SQL** &gt; **Account di accesso SQL** e selezionare un nome di account di accesso. La modifica deve essere apportata nell'istanza di SQL prima (e tutte le repliche, se necessario). Nel **impostazioni** pannello, fare clic su **Password**.

![Aggiornare la password dell'amministratore](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="update-the-virtual-machine-operating-system"></a>Aggiornare il sistema operativo della macchina virtuale
Esistono diversi modi per aggiornare la macchina virtuale di Windows Server:
* Installare il pacchetto di provider di risorse più recente usando un'immagine di Windows Server 2016 Core attualmente con patch
* Installa un pacchetto di Windows Update durante l'installazione o aggiornamento del componente

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aggiornare le definizioni di Windows Defender di macchina virtuale
Seguire questi passaggi per aggiornare le definizioni di Defender:

1. Download di aggiornare le definizioni di Windows Defender [Windows Defender definizione](https://www.microsoft.com/en-us/wdsi/definitions)

    In questa pagina in "Scaricare e installare manualmente le definizioni" Scarica "Antivirus di Windows Defender per Windows 10 e Windows 8.1" il file a 64 bit. 
    
    Collegamento diretto: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Creare una sessione di PowerShell all'endpoint di manutenzione della macchina virtuale di SQL RP adapter
3. Copiare il file di aggiornamento delle definizioni al computer di adapter DB utilizzando la sessione di manutenzione endpoint
4. Nella pagina Manutenzione PowerShell sessione richiamare il _aggiornamento DBAdapterWindowsDefenderDefinitions_ comando
5. Dopo l'installazione, è consigliabile rimuovere il file di aggiornamento tra le definizioni. Può essere rimosso della sessione di manutenzione utilizzando la _Remove-ItemOnUserDrive)_ comando.


Di seguito è riportato un esempio di script per aggiornare le definizioni di Defender (sostituire l'indirizzo o il nome della macchina virtuale con il valore effettivo):

```powershell
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```


## <a name="collect-diagnostic-logs"></a>Raccogliere i log di diagnostica
Il provider di risorse SQL è un controllo bloccato inattivo macchina virtuale. Se è necessario raccogliere i log dalla macchina virtuale, un endpoint di PowerShell Just Enough Administration (JEA) _DBAdapterDiagnostics_ viene fornito a tale scopo. Sono disponibili due comandi tramite questo endpoint:

* Get-AzsDBAdapterLog - prepara un pacchetto zip contenente i registri di diagnostica RP e lo inserisce nell'unità sessione utente. Il comando può essere chiamato senza parametri e raccoglierà le ultime quattro ore dei log.
* Remove-AzsDBAdapterLog - pulisce i pacchetti di log esistenti nel provider di risorse macchina virtuale

Un account utente denominato _dbadapterdiag_ viene creato durante la distribuzione di relying Party o aggiornamento per la connessione all'endpoint di diagnostica per l'estrazione dei registri di relying Party. La password dell'account è uguale a quella fornita per l'account amministratore locale durante l'installazione/aggiornamento.

Per utilizzare questi comandi, è necessario creare una sessione remota di PowerShell per la macchina virtuale del provider di risorse e richiamare il comando. È anche possibile specificare parametri FromDate e ToDate. Se non si specifica una o entrambe queste opzioni, FromDate sarà quattro ore prima dell'ora corrente e il ToDate sarà l'ora corrente.

Questo script di esempio viene illustrato l'utilizzo di questi comandi:

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere i server di hosting SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
