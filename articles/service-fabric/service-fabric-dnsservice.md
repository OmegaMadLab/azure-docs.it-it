---
title: Servizio DNS di Azure Service Fabric | Microsoft Docs
description: Usare il servizio DNS di Service Fabric per individuare microservizi dall'interno del cluster.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 656aed1f1fbd3294c4318520058ace480fd2219c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204995"
---
# <a name="dns-service-in-azure-service-fabric"></a>Servizio DNS in Azure Service Fabric
Il servizio DNS è un servizio di sistema facoltativo che è possibile abilitare nel cluster per individuare altri servizi usando il protocollo DNS. 

Molti servizi, in particolare quelli in contenitori, possono avere un nome di URL esistente. La possibilità di risolverli usando il protocollo DNS standard, invece del protocollo di servizio Naming, è preferibile, in particolare negli scenari di applicazioni "lift-and-shift". Il servizio DNS consente di eseguire il mapping di nomi DNS a nomi di servizi e di conseguenza di risolvere gli indirizzi IP degli endpoint. 

Il servizio DNS esegue il mapping dei nomi DNS ai nomi di servizi, che vengono quindi risolti dal servizio Naming per restituire l'endpoint di servizio. Il nome DNS per il servizio viene fornito al momento della creazione.

![Endpoint del servizio](./media/service-fabric-dnsservice/dns.png)

Le porte dinamiche non sono supportate dal servizio DNS. Per risolvere i servizi esposti su porte dinamiche, usare il [servizio proxy inverso](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Abilitazione del servizio DNS
Quando si crea un cluster tramite il portale, il servizio DNS è abilitato per impostazione predefinita nella casella di controllo **Includi servizio DNS** nel menu **Configurazione cluster**:

![Abilitazione del servizio DNS tramite il portale][2]

Se non si usa il portale per creare il cluster o se si sta aggiornando un cluster esistente, è necessario abilitare il servizio DNS in un modello:

- Per distribuire un nuovo cluster è possibile usare i [modelli di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) oppure creare un proprio modello di Gestione risorse. 
- Per aggiornare un cluster esistente, è possibile passare al gruppo di risorse del cluster nel portale e fare clic su **Script di automazione** per lavorare con un modello che rispecchia lo stato corrente del cluster e delle altre risorse del gruppo. Per maggiori informazioni, vedere [Esportare il modello da un gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group).

Quando si dispone di un modello, è possibile abilitare il servizio DNS seguendo questa procedura:

1. Verificare che `apiversion` sia impostato su `2017-07-01-preview` o versione successiva per la risorsa `Microsoft.ServiceFabric/clusters` e, se non lo è, aggiornarlo come illustrato nel frammento seguente:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. A questo punto abilitare il servizio DNS aggiungendo la sezione `addonFeatures` seguente dopo la sezione `fabricSettings`, come illustrato nel frammento seguente: 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. Dopo avere aggiornato il modello di cluster con le modifiche precedenti, applicarle e consentire il completamento dell'aggiornamento. Al termine dell'aggiornamento, il servizio di sistema DNS viene avviato nel cluster. Il nome del servizio è `fabric:/System/DnsService`, e sarà possibile trovarlo nella sezione **Sistema** del servizio in Service Fabric Explorer. 


## <a name="setting-the-dns-name-for-your-service"></a>Impostazione del nome DNS del servizio
Quando il servizio DNS è in esecuzione nel cluster, è possibile impostare un nome DNS per i servizi scegliendo tra il modo dichiarativo, per i servizi predefiniti in `ApplicationManifest.xml`, oppure tramite i comandi di PowerShell.

Il nome DNS per il servizio può essere risolto in tutto il cluster. È consigliabile utilizzare uno schema di denominazione `<ServiceDnsName>.<AppInstanceName>`, ad esempio `service1.application1`. Tale pratica assicura l'univocità del nome DNS in tutto il cluster. Se un'applicazione viene distribuita usando Docker Compose, i nomi DNS vengono automaticamente assegnati ai servizi utilizzando questo schema di denominazione.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Impostazione del nome DNS per un servizio predefinito nel file ApplicationManifest.xml
Aprire il progetto in Visual Studio, o nell'editor preferito, quindi aprire il file `ApplicationManifest.xml`. Passare alla sezione relativa ai servizi predefiniti e per ciascuno di esso aggiungere l'attributo `ServiceDnsName`. Nell'esempio seguente viene mostrato come impostare il nome DNS del servizio su `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Dopo aver distribuito l'applicazione, l'istanza del servizio in Service Fabric Explorer visualizza il nome DNS di tale istanza, come mostrato nella figura seguente: 

![Endpoint del servizio][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Impostazione del nome DNS per un servizio mediante PowerShell
È possibile impostare il nome DNS di un servizio al momento della creazione usando `New-ServiceFabricService` Powershell. Nell'esempio seguente viene creato un nuovo servizio senza stato con il nome DNS `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>Uso del protocollo DNS nei servizi
Se si distribuisce più di un servizio, è possibile trovare gli endpoint di altri servizi con cui comunicare usando un nome DNS. Il servizio DNS è valido solo per i servizi senza stato perché il protocollo DNS non può comunicare con i servizi con stato. Per i servizi con stato, è possibile usare il [servizio proxy inverso](./service-fabric-reverseproxy.md) predefinito in modo che le chiamate HTTP siano dirette a una particolare partizione del servizio. Le porte dinamiche non sono supportate dal servizio DNS. Per risolvere i servizi su porte dinamiche, è possibile usare il proxy inverso.

Il codice seguente illustra come chiamare un altro servizio. Si tratta semplicemente di una normale chiamata HTTP in cui si specificano la porta ed eventuali percorsi facoltativi come parte dell'URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla comunicazione tra i servizi all'interno del cluster, vedere [Connettersi e comunicare con i servizi in Service Fabric](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
