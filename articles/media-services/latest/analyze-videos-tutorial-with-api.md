---
title: Analizzare i video con Servizi multimediali di Azure | Microsoft Docs
description: Seguire i passaggi di questa esercitazione per analizzare i video usando Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: 0fdc8c6dc9fae96a79e2ab2b05b7db3012834c1e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362295"
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>Esercitazione: Analizzare i video con Servizi multimediali di Azure 

Questa esercitazione illustra come analizzare i video usando Servizi multimediali di Azure. Esistono molti scenari in cui può essere opportuno acquisire informazioni dettagliate da video registrati e contenuti audio. Le organizzazioni, ad esempio, per migliorare la soddisfazione dei clienti, possono convertire in testo scritto l'audio delle registrazioni del supporto clienti e trasformarle in un catalogo di consultazione, con indici e dashboard. In questo modo, possono acquisire informazioni dettagliate sull'andamento dell'attività, generando ad esempio un elenco dei reclami frequenti, delle origini di tali reclami e così via.

Questa esercitazione illustra come:    

> [!div class="checklist"]
> * Avviare Azure Cloud Shell
> * Creare un account di Servizi multimediali
> * Accedere all'API di Servizi multimediali
> * Configurare l'app di esempio
> * Esaminare in dettaglio il codice di esempio
> * Esecuzione dell'app
> * Esaminare l'output
> * Pulire le risorse

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

Se Visual Studio non è installato, è possibile scaricare [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Scaricare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-sample-code-in-detail"></a>Esaminare in dettaglio il codice di esempio

Questa sezione esamina le funzioni definite nel file [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) del progetto *AnalyzeVideos*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, è necessario creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Prima di tutto è necessario ottenere un token e quindi creare un oggetto **ClientCredential** dal token restituito. Nel codice che è stato clonato all'inizio dell'articolo, per ottenere il token viene usato l'oggetto **ArmClientCredential**.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Creare un asset di output per archiviare il risultato di un processo 

L'[asset](https://docs.microsoft.com/rest/api/media/assets) di output archivia il risultato del processo. Il progetto definisce la funzione **DownloadResults** che scarica i risultati dall'asset di output nella cartella "output", consentendo così di visualizzare ciò che si è ottenuto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Creare una trasformazione e un processo per analizzare i video

Quando si codificano o si elaborano contenuti in Servizi multimediali, è prassi comune configurare le impostazioni di codifica come una serie di istruzioni e quindi inviare un oggetto **Job**, ovvero il processo per applicare tali istruzioni a un video. Inviando nuovi processi per ogni nuovo video, si applicano le istruzioni a tutti i video nella libreria. In Servizi multimediali una serie di istruzioni di questo tipo è definita trasformazione ed è rappresentata dall'oggetto **Transform**. Per altre informazioni, vedere [Trasformazioni e processi](transform-concept.md). L'esempio illustrato in questa esercitazione definisce una serie di istruzioni che analizza il video specificato. 

#### <a name="transform"></a>Trasformare

Quando si crea una nuova istanza dell'oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms), è necessario specificare ciò che dovrà generare come output. Il parametro obbligatorio è costituito da un oggetto **TransformOutput**, come illustrato nel codice precedente. Ogni **TransformOutput** contiene un parametro **Preset**. In **Preset** sono descritte le istruzioni dettagliate delle operazioni di elaborazione di contenuti video e/o audio che devono essere usate per generare l'oggetto **TransformOutput** desiderato. In questo esempio viene usato il set di impostazioni **VideoAnalyzerPreset** e viene passata la lingua ("en-US") al relativo costruttore. Questo set di impostazioni consente di estrarre da un video più informazioni dettagliate sui contenuti audio e video. Se è necessario estrarre da un video solo le informazioni dettagliate relative all'audio, è possibile usare il set di impostazioni **AudioAnalyzerPreset**. 

Quando si crea un oggetto **Transform**, è necessario verificare se ne esiste già uno tramite il metodo **Get**, come illustrato nel codice seguente.  In Servizi multimediali v3 i metodi **Get** eseguiti su entità restituiscono **null** se determinano che l'entità non esiste, effettuando un controllo del nome senza distinzione tra maiuscole e minuscole.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Processo

Come indicato sopra, l'oggetto [Transform](https://docs.microsoft.com/rest/api/media/transforms) è la serie di istruzioni, mentre l'oggetto [Job](https://docs.microsoft.com/en-us/rest/api/media/jobs) è la richiesta effettiva inviata a Servizi multimediali per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. L'oggetto **Job** specifica informazioni come la posizione del video di input e quella dell'output. È possibile specificare la posizione del video tramite URL HTTPS, URL di firma di accesso condiviso o asset inclusi nell'account di Servizi multimediali. 

In questo esempio, l'input del processo è un video locale.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Attendere il completamento del processo

Il completamento del processo richiede tempo e al termine dell'elaborazione può essere opportuno ricevere una notifica. Per la generazione di notifiche relative al completamento del [processo](https://docs.microsoft.com/en-us/rest/api/media/jobs) sono disponibili varie opzioni. La più semplice, qui illustrata, consiste nell'uso del polling. 

Il polling non è una procedura consigliata per le applicazioni di produzione poiché comporta rischi di latenza. Il polling può essere limitato se usato eccessivamente su un account. In alternativa, è preferibile che gli sviluppatori usino Griglia di eventi.

Griglia di eventi è un servizio progettato per garantire disponibilità elevata, coerenza nelle prestazioni e scalabilità dinamica. Con Griglia di eventi, le app possono rimanere in ascolto e reagire agli eventi praticamente da tutti i servizi di Azure, oltre che da origini personalizzate. Questo semplice servizio di gestione degli eventi, reattivo e basato su HTTP, consente di creare soluzioni efficienti tramite funzioni intelligenti di filtraggio e routing di eventi. Vedere [Instradare gli eventi verso un endpoint Web personalizzato](job-state-events-cli-how-to.md).

L'oggetto **Job** assume progressivamente gli stati seguenti: **Scheduled**, **Queued**, **Processing**, **Finished** (lo stato finale). Se nel corso del processo si verifica un errore, viene restituito lo stato **Error**. Se il processo è in fase di annullamento, vengono restituiti lo stato **Canceling** e, al termine, lo stato **Canceled**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="download-the-result-of-the-job"></a>Scaricare il risultato del processo

La funzione seguente scarica i risultati del processo dall'[asset](https://docs.microsoft.com/rest/api/media/assets) di output nella cartella "output" per consentire di esaminarlo. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Pulire le risorse nell'account di Servizi multimediali

Normalmente è necessario pulire tutti gli oggetti tranne quelli che si prevede di riutilizzare. In genere si riutilizzano gli oggetti Transform e si salvano in modo permanente oggetti come StreamingLocator. Se dopo l'attività di sperimentazione si vuole pulire il proprio account, è necessario eliminare le risorse che non si prevede di riutilizzare. Il codice seguente, ad esempio, elimina gli oggetti Job.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Premere CTRL+F5 per eseguire l'applicazione *AnalyzeVideos*.

Quando si esegue il programma, il processo genera anteprime per ogni volto rilevato nel video, oltre al file insights.json.

## <a name="examine-the-output"></a>Esaminare l'output

Il file di output risultante dall'analisi dei video è denominato insights.json e contiene informazioni dettagliate sul video analizzato. La descrizione degli elementi presenti nel file json è riportata nell'articolo [Intelligenza dei contenuti multimediali](intelligence-concept.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza. A tal fine è possibile usare lo strumento **CloudShell**.

In **CloudShell** eseguire il comando seguente:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Gli SDK di Servizi multimediali di Azure v3 non sono thread-safe. Quando si usa un'applicazione multithreading, è necessario generare un nuovo oggetto AzureMediaServicesClient per ogni thread.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il caricamento, la codifica e lo streaming di video](stream-files-tutorial-with-api.md)
