---
title: Passare le credenziali in Azure tramite Desired State Configuration
description: Informazioni su come passare in modo sicuro le credenziali alle macchine virtuali di Azure tramite PowerShell Desired State Configuration (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: 666253d16ac51dcc21174211f71794f8b0ede07d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012547"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Passare credenziali al gestore estensione DSC di Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Questo articolo illustra l'estensione DSC (Desired State Configuration) per Azure. Per una panoramica del gestore estensione DSC, vedere [Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure](dsc-overview.md).

## <a name="pass-in-credentials"></a>Passare le credenziali

Nell'ambito del processo di configurazione, potrebbe essere necessario configurare account utente, servizi di accesso o installare un programma in un contesto utente. Per eseguire queste operazioni, è necessario fornire le credenziali.

DSC consente di impostare le configurazioni con parametri. In una configurazione con parametri, le credenziali vengono passate nella configurazione e archiviate in modo sicuro in file .mof. Per semplificare la gestione delle credenziali, il gestore estensione di Azure offre la gestione automatica dei certificati.

Lo script di configurazione DSC seguente che crea un account utente locale con la password specificata:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

È importante includere **node localhost** come parte della configurazione. Il gestore estensione cerca specificatamente l'estensione **localhost nodo**. Se l'istruzione manca, la procedura seguente non funziona. È anche importante includere il cast di tipo **[PsCredential]**. Questo tipo specifico attiva l'estensione per crittografare le credenziali.

Pubblicare questo script nell'archivio BLOB di Azure:

`Publish-AzureRmVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Impostare l'estensione DSC di Azure e specificare la credenziale:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzureRmVM -Name 'example-1'

$vm = Set-AzureRmVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureRmVM
```

## <a name="how-a-credential-is-secured"></a>Modalità di protezione della credenziale

Durante l'esecuzione del codice viene chiesta una credenziale. Dopo che la credenziale viene fornita, viene archiviata per un breve periodo di tempo in memoria. Quando la credenziale viene pubblicata tramite il cmdlet **Set-AzureRmVMDscExtension**, la credenziale viene trasmessa tramite HTTPS alla macchina virtuale. Nella macchina virtuale, Azure archivia le credenziali crittografate su disco usando il certificato della macchina virtuale locale. La credenziale viene brevemente decrittografata nella memoria e quindi nuovamente crittografata per essere passata a DSC.

Questo processo è diverso dall' [uso di configurazioni sicure senza il gestore dell'estensione](/powershell/dsc/securemof). L'ambiente di Azure offre un modo per trasmettere i dati di configurazione in maniera sicura tramite certificati. Quando si usa il gestore dell'estensione DSC, non è necessario fornire **$CertificatePath** o una voce **$CertificateID**/ **$Thumbprint** in **ConfigurationData**.

## <a name="next-steps"></a>Passaggi successivi

- Ottenere un'[introduzione al gestore dell'estensione DSC di Azure](dsc-overview.md).
- Esaminare il [modello di Azure Resource Manager per l'estensione DSC](dsc-template.md).
- Per altre informazioni su PowerShell DSC, passare al [centro di documentazione di PowerShell](/powershell/dsc/overview).
- Per altre funzionalità che è possibile gestire tramite PowerShell DSC e per altre risorse DSC, cercare in [PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).