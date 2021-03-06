---
title: Caricare file in un account di Servizi multimediali mediante .NET | Microsoft Docs
description: Informazioni su come ottenere contenuti multimediali in Servizi multimediali creando e caricando asset.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2017
ms.author: juliako
ms.openlocfilehash: 0197aabbb3a0594ed8a7c54282491298fdf7cd23
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Caricare file in un account di Servizi multimediali mediante .NET
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portale](media-services-portal-upload-files.md)
> 
> 

In Servizi multimediali i file digitali vengono caricati (o inseriti) in un asset. L'entità **Asset** può contenere video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati, oltre ai metadati relativi a questi file.  Dopo aver caricato i file, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming.

I file nell'asset sono denominati **File di asset**. L'istanza di **AssetFile** e il file multimediale effettivo sono due oggetti distinti. L'istanza di AssetFile contiene metadati relativi al file multimediale, mentre quest'ultimo contiene l'effettivo contenuto multimediale.

> [!NOTE]
> Si applicano le considerazioni seguenti:
> 
> * Servizi multimediali usa il valore della proprietà IAssetFile.Name durante la creazione di URL per i contenuti in streaming, ad esempio http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters. Per questo motivo, la codifica percentuale non è consentita. Il valore della proprietà **Name** non può contenere i [caratteri riservati per la codifica percentuale](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) seguenti: !*'();:@&=+$,/?%#[]". L'estensione del nome di file, inoltre, può essere preceduta da un solo punto (.).
> * La lunghezza del nome non deve essere superare i 260 caratteri.
> * È previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. Vedere [questo](media-services-quotas-and-limitations.md) articolo per informazioni dettagliate sulla limitazione per le dimensioni dei file.
> * È previsto un limite di 1.000.000 di criteri per i diversi criteri AMS (ad esempio per i criteri Locator o ContentKeyAuthorizationPolicy). Usare lo stesso ID criterio se si usano sempre gli stessi giorni/autorizzazioni di accesso, come nel cado di criteri per i localizzatori che devono rimanere attivi per molto tempo (criteri di non caricamento). Per altre informazioni, vedere [questo](media-services-dotnet-manage-entities.md#limit-access-policies) articolo.
> 

Quando si creano asset, è possibile specificare le opzioni di crittografia seguenti:

* **None** : non viene usata alcuna crittografia. Si tratta del valore predefinito. Quando si usa questa opzione il contenuto non è protetto durante il transito, né nell'archiviazione locale.
  Se si pianifica la distribuzione di un file MP4 con il download progressivo, usare questa opzione: 
* **CommonEncryption** : usare questa opzione per caricare contenuti già crittografati e protetti con Common Encryption o PlayReady DRM (ad esempio, Smooth Streaming protetto con PlayReady DRM).
* **EnvelopeEncrypted** : usare questa opzione se si sta caricando contenuto HLS crittografato con AES. I file devono essere stati codificati e crittografati da Transform Manager.
* **StorageEncrypted** : crittografa il contenuto non crittografato localmente usando la crittografia AES a 256 bit, quindi li carica in Archiviazione di Azure dove vengono archiviati con crittografia in modo inattivo. Gli asset protetti con la crittografia di archiviazione vengono decrittografati automaticamente e inseriti in un file system crittografato prima della codifica, quindi ricrittografati facoltativamente prima di essere ricaricati di nuovo come nuovo asset di output. La crittografia di archiviazione viene usata principalmente quando si vogliono proteggere i file multimediali con input di alta qualità con una crittografia avanzata sul disco locale.
  
    Servizi multimediali offre una crittografia di archiviazione su disco per gli asset, non in rete come Digital Rights Management (DRM).
  
    Se l'asset è protetto con crittografia di archiviazione, è necessario configurare i criteri di distribuzione degli asset. Per altre informazioni, vedere [Configurazione dei criteri di distribuzione degli asset](media-services-dotnet-configure-asset-delivery-policy.md).

Se si specifica che l'asset deve essere crittografato con un'opzione **CommonEncrypted** o **EnvelopeEncypted**, sarà necessario associare l'asset a **ContentKey**. Per alte informazioni, vedere [Creare entità ContentKey mediante .NET](media-services-dotnet-create-contentkey.md). 

Se si specifica che l'asset deve essere crittografato con un'opzione **StorageEncrypted**, Media Services SDK per .NET creerà un'entità **StorateEncrypted** **ContentKey** per l'asset.

Questo articolo illustra come usare l'SDK di Servizi multimediali per .NET e le relative estensioni per caricare file in un asset di Servizi multimediali.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Caricare un singolo file con SDK di Servizi multimediali per .NET
Il codice seguente usa .NET per caricare un singolo file. AccessPolicy e Localizzatore vengono creati e distrutti dalla funzione di caricamento. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Caricare più file con SDK di Servizi multimediali per .NET
Il seguente codice illustra come creare un asset e caricare più file.

Il codice esegue le seguenti attività:

* Crea un'entità Asset vuota usando il metodo CreateEmptyAsset definito nel passaggio precedente.
* Crea un'istanza di **AccessPolicy** che definisce le autorizzazioni e la durata dell'accesso all'asset.
* Crea un'istanza di **Locator** che consente l'accesso all'asset.
* Crea un'istanza di **BlobTransferClient** . Questo tipo rappresenta un client che agisce sugli oggetti BLOB di Azure. In questo esempio il client esegue il monitoraggio dello stato del caricamento. 
* Enumera i file della directory specificata e crea un'istanza di **AssetFile** per ogni file.
* Carica i file in Servizi multimediali usando il metodo **UploadAsync** . 

> [!NOTE]
> Usare il metodo UploadAsync in modo che non si verifichino blocchi delle chiamate e i file vengano caricati in parallelo.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Quando si carica un numero elevato di asset, prendere in considerazione quanto segue:

* Creare un nuovo oggetto **CloudMediaContext** per thread. La classe **CloudMediaContext** non è di tipo thread-safe.
* Aumentare il valore di NumberOfConcurrentTransfers sostituendo il valore 2 predefinito con uno maggiore, ad esempio 5. L'impostazione di questa proprietà ha effetto su tutte le istanze di **CloudMediaContext**. 
* Mantenere su 10 l'impostazione predefinita di ParallelTransferThreadCount.

## <a id="ingest_in_bulk"></a>Inserimento di asset in blocco tramite SDK di Servizi multimediali per .NET
Il caricamento di file di asset di grandi dimensioni costituisce un collo di bottiglia nella creazione di asset. L'inserimento di asset in blocco prevede il disaccoppiamento del processo di creazione di asset da quello di caricamento. Per usare un approccio di inserimento in blocco, creare un manifesto (IngestManifest) in cui vengono descritti l'asset e i relativi file associati. Usare quindi il metodo di caricamento che si preferisce per caricare i file associati nel contenitore BLOB del manifesto. Servizi multimediali di Microsoft Azure controlla il contenitore BLOB associato al manifesto. Dopo che un file è stato caricato nel contenitore BLOB, Servizi multimediali di Microsoft Azure completa la creazione dell'asset in base alla configurazione dell'asset nel manifesto (IngestManifestAsset).

Per creare una nuova entità IngestManifest, chiamare il metodo Create esposto dalla raccolta IngestManifests in CloudMediaContext. Questo metodo creerà una nuova entità IngestManifest con il nome di manifesto specificato.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Creare gli asset che verranno associati all'entità IngestManifest in blocco. Configurare le opzioni di crittografia desiderate nell'asset per l'inserimento in blocco.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

Un'entità IngestManifestAsset associa un Asset a un IngestManifest in blocco per l'inserimento in blocco. Associa anche le entità AssetFiles che costituiranno i singoli asset. Per creare un'entità IngestManifestAsset, usare il metodo Create nel contesto server.

Il seguente esempio illustra come aggiungere due nuove entità IngestManifestAssets che associano i due asset creati in precedenza al manifesto di inserimento in blocco. Ogni entità IngestManifestAsset associa anche un set di file che verrà caricato per ogni asset durante l'inserimento in blocco.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

È possibile usare qualsiasi applicazione client ad alta velocità in grado di caricare i file di asset nell'URI del contenitore di archiviazione BLOB fornito dalla proprietà **IIngestManifest.BlobStorageUriForUpload** di IngestManifest. Un servizio di caricamento ad alta velocità consigliato è l'applicazione [Aspera On Demand for Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). È anche possibile scrivere codice per il caricamento dei file di asset, come descritto nel seguente esempio di codice.

```csharp
    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

Il codice per il caricamento dei file di asset per l'esempio usato in questo articolo è riportato nel seguente esempio:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

È possibile determinare lo stato dell'inserimento in blocco per tutti gli asset associati a un'entità **IngestManifest** eseguendo il polling della proprietà Statistics di **IngestManifest**. Per aggiornare le informazioni sullo stato, è necessario usare una nuova entità **CloudMediaContext** ogni volta che si esegue il polling della proprietà Statistics.

Il seguente esempio illustra il polling di un'entità IngestManifest in base alla relativa proprietà **Id**.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Caricare file mediante le estensioni dell'SDK per .NET
L'esempio seguente mostra come caricare un singolo file mediante le estensioni dell'SDK per .NET. In questo caso viene usato il metodo **CreateFromFile**, ma è disponibile anche la versione asincrona (**CreateFromFileAsync**). Il metodo **CreateFromFile** consente di specificare il nome del file, l'opzione di crittografia e un callback per segnalare lo stato di caricamento del file.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

Il seguente esempio esegue una chiamata alla funzione UploadFile e specifica la crittografia di archiviazione come opzione di creazione dell'asset.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Passaggi successivi

Ora è possibile codificare gli asset caricati. Per altre informazioni, vedere [Encode assets](media-services-portal-encode.md)(Codificare gli asset).

È anche possibile usare Funzioni di Azure per attivare un processo di codifica basato su un file che arriva nel contenitore configurato. Per altre informazioni, vedere [questo esempio](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Passaggio successivo
Dopo aver caricato un asset in Servizi multimediali, è possibile passare all'articolo [Procedura: Ottenere un'istanza del processore di contenuti multimediali][How to Get a Media Processor].

[How to Get a Media Processor]: media-services-get-media-processor.md

