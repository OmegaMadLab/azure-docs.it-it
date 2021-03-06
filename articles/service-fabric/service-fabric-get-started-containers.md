---
title: Creare un'applicazione contenitore di Azure Service Fabric | Microsoft Docs
description: Creare la prima applicazione contenitore Windows in Azure Service Fabric. Creare un'immagine Docker con un'applicazione Python, effettuare il push dell'immagine in un registro contenitori e compilare e distribuire un'applicazione contenitore di Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/18/2018
ms.author: ryanwi
ms.openlocfilehash: 5fcd42a2453bddbfc1c1d1939dd9e63e7e09bdb0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366529"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Creare la prima applicazione contenitore di Service Fabric in Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Per eseguire un'applicazione esistente in un contenitore Windows in un cluster di Service Fabric non è necessario apportare modifiche all'applicazione. Questo articolo illustra come creare un'immagine Docker contenente un'applicazione Web Python [Flask](http://flask.pocoo.org/) e come distribuirla in un cluster di Service Fabric. Si condividerà anche l'applicazione in contenitore tramite [Registro contenitori di Azure](/azure/container-registry/). L'articolo presuppone una conoscenza di base di Docker. Per informazioni su Docker, vedere [Docker overview](https://docs.docker.com/engine/understanding-docker/) (Panoramica su Docker).

## <a name="prerequisites"></a>prerequisiti
Un computer di sviluppo che esegue:
* Visual Studio 2015 o Visual Studio 2017.
* [SDK e strumenti di Service Fabric](service-fabric-get-started.md).
*  Docker per Windows. [Scaricare Docker CE per Windows (stabile)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Dopo aver installato e avviato Docker, fare clic con il pulsante destro del mouse sull'icona nell'area di notifica e selezionare **Switch to Windows containers** (Passa ai contenitori Windows). Questo passaggio è necessario per eseguire le immagini Docker basate su Windows.

Un cluster di Windows con tre o più nodi in esecuzione in Windows Server 2016 con Contenitori. A questo scopo, [creare un cluster](service-fabric-cluster-creation-via-portal.md) o [provare Service Fabric gratuitamente](https://aka.ms/tryservicefabric).

Un registro all'interno del registro contenitori di Azure. A questo scopo, [creare un registro contenitori](../container-registry/container-registry-get-started-portal.md) nella sottoscrizione di Azure.

> [!NOTE]
> La distribuzione di contenitori in un cluster di Service Fabric in Windows 10 o in un cluster con Docker CE non è ancora supportata. Questa procedura dettagliata esegue localmente i test tramite il motore Docker su Windows 10 e infine distribuisce i servizi contenitori in un cluster di Windows Server in Azure che esegue Docker EE. 
>   

> [!NOTE]
> Service Fabric versione 6.1 offre il supporto in anteprima per Windows Server versione 1709. Le reti aperte e il servizio DNS di Service Fabric non funzionano con Windows Server versione 1709. 
> 

## <a name="define-the-docker-container"></a>Definire il contenitore Docker
Compilare un'immagine in base all'[immagine Python](https://hub.docker.com/_/python/) disponibile nell'hub Docker.

Specificare il contenitore Docker in un Dockerfile. Il Dockerfile è costituito da istruzioni per la configurazione dell'ambiente all'interno del contenitore, il caricamento dell'applicazione da eseguire e il mapping delle porte. Il file Dockerfile rappresenta l'input per il comando `docker build` che crea l'immagine.

Creare una directory vuota e il file *Dockerfile* (senza estensione file). Aggiungere quanto segue a *Dockerfile* e salvare le modifiche:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Per altre informazioni, vedere [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) (Informazioni di riferimento su Dockerfile).

## <a name="create-a-basic-web-application"></a>Creare un'applicazione Web di base
Creare un'applicazione Web Flask in ascolto sulla porta 80 che restituisce `Hello World!`. Nella stessa directory creare il file *requirements.txt*. Aggiungere quanto segue e salvare le modifiche:
```
Flask
```

Creare anche il file *app.py* e aggiungere il frammento di codice seguente:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():

    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

<a id="Build-Containers"></a>
## <a name="build-the-image"></a>Compilare l'immagine
Eseguire il comando `docker build` per creare l'immagine che esegue l'applicazione Web. Aprire una finestra di PowerShell e passare alla directory contenente il Dockerfile. Eseguire il comando seguente:

```
docker build -t helloworldapp .
```

Questo comando crea la nuova immagine usando le istruzioni contenute nel Dockerfile e denomina l'immagine `helloworldapp`, ovvero le assegna un tag -t. Per creare un'immagine del contenitore, l'immagine di base viene prima scaricata dall'hub Docker nel quale viene aggiunta l'applicazione. 

Al termine dell'esecuzione del comando di compilazione, eseguire il comando `docker images` per visualizzare le informazioni sulla nuova immagine:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale
Verifica l'immagine in locale prima di effettuarne il push nel registro contenitori. 

Eseguire l'applicazione:

```
docker run -d --name my-web-site helloworldapp
```

*name* assegna un nome al contenitore in esecuzione, anziché l'ID contenitore.

Dopo aver avviato il contenitore, trovare il relativo indirizzo IP per consentire la connessione al contenitore in esecuzione da un browser:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Se il comando non restituisce alcun valore, eseguire questo comando ed esaminare l'elemento **NetworkSettings**->**Networks** per l'indirizzo IP:
```
docker inspect my-web-site
```

Connettersi al contenitore in esecuzione. Aprire un Web browser puntando all'indirizzo IP restituito, ad esempio "http://172.31.194.61". Verrà visualizzata l'intestazione "Hello World!" nel browser.

Per arrestare il contenitore, eseguire:

```
docker stop my-web-site
```

Per eliminare il contenitore dal computer di sviluppo, eseguire:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>Effettuare il push dell'immagine nel registro contenitori
Dopo aver verificato l'esecuzione del contenitore nel computer di sviluppo, effettuare il push dell'immagine nel registro all'interno del registro contenitori di Azure.

Eseguire ``docker login`` per accedere al registro di contenitori con le [credenziali del registro](../container-registry/container-registry-authentication.md).

L'esempio seguente passa l'ID e la password di un'[entità servizio](../active-directory/active-directory-application-objects.md) di Azure Active Directory. Ad esempio, è possibile che sia stata assegnata un'entità servizio al registro per uno scenario di automazione. In alternativa, è possibile eseguire l'accesso usando il nome utente e la password del registro.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Il comando seguente crea un tag, o alias, dell'immagine, con un percorso completo del registro. Questo esempio inserisce l'immagine dello spazio dei nomi ```samples``` per evitare confusione nella radice del registro.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Effettuare il push dell'immagine nel registro contenitori:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Creare il servizio in contenitori in Visual Studio
L'SDK e gli strumenti di Service Fabric offrono un modello di servizio che consente di creare un'applicazione in contenitori.

1. Avviare Visual Studio. Selezionare **File** > **Nuovo** > **Progetto**.
2. Selezionare l'**applicazione di Service Fabric**, denominarla "MyFirstContainer" e fare clic su **OK**.
3. Scegliere **Contenitore** dall'elenco dei **modelli di servizio**.
4. In **Nome immagine** immettere "myregistry.azurecr.io/samples/helloworldapp", vale a dire l'immagine di cui è stato effettuato il push nel repository dei contenitori.
5. Assegnare un nome al servizio e fare clic su **OK**.

## <a name="configure-communication"></a>Configurare la comunicazione
Il servizio in contenitori richiede un endpoint per la comunicazione. Aggiungere un elemento `Endpoint` con il protocollo, la porta e il tipo al file ServiceManifest.xml. In questo esempio viene usata una porta fissa 8081. Se non vengono specificate porte, verrà scelta una porta casuale dall'intervallo di porte dell'applicazione. 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```

Definendo un endpoint, Service Fabric pubblica l'endpoint nel servizio Naming. Altri servizi in esecuzione nel cluster possono risolvere questo contenitore. È anche possibile eseguire la comunicazione da contenitore a contenitore usando il [proxy inverso](service-fabric-reverseproxy.md). La comunicazione avviene specificando la porta di ascolto HTTP del proxy inverso e il nome dei servizi con cui si vuole comunicare come variabili di ambiente.

Il servizio è in ascolto su una porta specifica, 8081 in questo esempio. Quando l'applicazione viene distribuita in un cluster in Azure, sia il cluster che l'applicazione vengono eseguiti dietro un servizio di bilanciamento del carico di Azure. La porta dell'applicazione deve essere aperta nel servizio di bilanciamento del carico di Azure in modo che il traffico in entrata possa raggiungere il servizio.  È possibile aprire questa porta nel servizio di bilanciamento carico di Azure usando uno [script di PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) o il [portale di Azure](https://portal.azure.com).

## <a name="configure-and-set-environment-variables"></a>Configurare e impostare variabili di ambiente
È possibile specificare variabili di ambiente per ogni pacchetto di codice nel manifesto del servizio. Questa funzionalità è disponibile per tutti i servizi, siano essi distribuiti come contenitori, processi o eseguibili guest. È possibile sostituire i valori delle variabili di ambiente nel manifesto dell'applicazione oppure specificarli durante la distribuzione come parametri dell'applicazione.

Il frammento XML del manifesto del servizio seguente offre un esempio di come specificare le variabili di ambiente per un pacchetto di codice:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

È possibile eseguire l'override di queste variabili di ambiente nel manifesto dell'applicazione:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Configurare il mapping tra porta del contenitore e porta dell'host e l'individuazione da contenitore a contenitore
Configurare una porta dell'host per la comunicazione con il contenitore. Il binding di porta esegue il mapping tra la porta su cui il servizio è in ascolto nel contenitore e una porta nell'host. Aggiungere un elemento `PortBinding` nell'elemento `ContainerHostPolicies` del file ApplicationManifest.xml. Per questo articolo, il valore di `ContainerPort` è 80, vale a dire che il contenitore espone la porta 80, come specificato nel Dockerfile, e il valore di `EndpointRef` è "Guest1TypeEndpoint", ovvero l'endpoint definito in precedenza nel manifesto del servizio. Per le richieste in ingresso per il servizio sulla porta 8081 viene eseguito il mapping alla porta 80 del contenitore.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

## <a name="configure-container-registry-authentication"></a>Configurare l'autenticazione del registro contenitori
Configurare l'autenticazione del registro contenitori aggiungendo `RepositoryCredentials` a `ContainerHostPolicies` nel file ApplicationManifest.xml. Aggiungere l'account e la password per il contenitore myregistry.azurecr.io, per consentire al servizio di scaricare l'immagine del contenitore dal repository.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

È consigliabile crittografare la password del repository con un certificato di crittografia distribuito in tutti i nodi del cluster. Quando Service Fabric distribuisce il pacchetto del servizio nel cluster, il certificato di crittografia viene usato per decrittografare il testo crittografato. Il cmdlet Invoke-ServiceFabricEncryptText viene usato per creare il testo crittografato della password, che viene aggiunto al file ApplicationManifest.xml.

Lo script seguente crea un nuovo certificato autofirmato e lo esporta in un file PFX. Il certificato viene importato in un insieme di credenziali delle chiavi esistente e quindi distribuito nel cluster di Service Fabric.

```powershell
# Variables.
$certpwd = ConvertTo-SecureString -String "Pa$$word321!" -Force -AsPlainText
$filepath = "C:\MyCertificates\dataenciphermentcert.pfx"
$subjectname = "dataencipherment"
$vaultname = "mykeyvault"
$certificateName = "dataenciphermentcert"
$groupname="myclustergroup"
$clustername = "mycluster"

$subscriptionId = "subscription ID"

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Create a self signed cert, export to PFX file.
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject $subjectname -Provider 'Microsoft Enhanced Cryptographic Provider v1.0' `
| Export-PfxCertificate -FilePath $filepath -Password $certpwd

# Import the certificate to an existing key vault. The key vault must be enabled for deployment.
$cer = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certificateName -FilePath $filepath -Password $certpwd

Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $groupname -EnabledForDeployment

# Add the certificate to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $cer.SecretId
```
Crittografare la password usando il cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps).

```powershell
$text = "=P==/==/=8=/=+u4lyOB=+=nWzEeRfF="
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint $cer.Thumbprint -Text $text -StoreLocation Local -StoreName My
```

Sostituire la password con il testo crittografato restituito dal cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) e impostare `PasswordEncrypted` su "true".

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

## <a name="configure-isolation-mode"></a>Configurare la modalità di isolamento
Windows supporta due modalità di isolamento per i contenitori: la modalità processo e la modalità Hyper-V. Nella modalità di isolamento del processo tutti i contenitori in esecuzione nello stesso computer host condividono il kernel con l'host. Nella modalità di isolamento Hyper-V i kernel sono isolati tra i singoli contenitori Hyper-V e il contenitore host. La modalità di isolamento è specificata nell'elemento `ContainerHostPolicies` nel file manifesto dell'applicazione. Le modalità di isolamento specificabili sono `process`, `hyperv` e `default`. La modalità di isolamento assume come impostazione predefinita `process` negli host Windows Server e `hyperv` negli host Windows 10. Il frammento seguente indica come è specificata la modalità di isolamento nel file manifesto dell'applicazione.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > La modalità di isolamento Hyper-V è disponibile negli SKU di Azure Ev3 e Dv3 che hanno il supporto per la virtualizzazione annidato. 
   >
   >

## <a name="configure-resource-governance"></a>Configurare la governance delle risorse
La [governance delle risorse](service-fabric-resource-governance.md) limita le risorse che possono essere usate dal contenitore nell'host. L'elemento `ResourceGovernancePolicy`, specificato nel manifesto dell'applicazione, viene usato per dichiarare limiti di risorse per il pacchetto di codice di un servizio. È possibile impostare limiti per le risorse seguenti: Memory, MemorySwap, CpuShares (peso relativo CPU), MemoryReservationInMB, BlkioWeight (peso relativo BlockIO). In questo esempio, il pacchetto del servizio Guest1Pkg ottiene un core nei nodi del cluster in cui è posizionato. I limiti di memoria sono assoluti, quindi i pacchetti di codice sono limitati a 1024 MB di memoria, con prenotazione a garanzia flessibile. I pacchetti di codice (contenitori o pacchetti) non sono in grado di allocare una quantità di memoria superiore a questo limite. Un'operazione di questo tipo genererebbe un'eccezione di memoria esaurita. Perché l'imposizione di un limite di risorse funzioni, è necessario che tutti i pacchetti di codice inclusi in un pacchetto del servizio abbiano limiti di memoria specificati.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```
## <a name="configure-docker-healthcheck"></a>Configurare docker HEALTHCHECK 

A partire dalla versione 6.1, Service Fabric integra automaticamente gli eventi di [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) nel report relativo all'integrità del sistema. Se **HEALTHCHECK** è stato abilitato nel contenitore, Service Fabric fornirà informazioni sull'integrità ogni volta che lo stato dell'integrità del contenitore subisce modifiche, in base a quanto segnalato da Docker. Un report sull'integrità di tipo **OK** verrà visualizzato in [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) quando il valore *health_status* è *healthy* e un report di tipo **AVVISO** verrà visualizzato quando il valore *health_status* è *unhealthy*. L'istruzione **HEALTHCHECK** che fa riferimento alla verifica effettiva eseguita per il monitoraggio dell'integrità dei contenitori deve essere presente nel Dockerfile usato durante la generazione dell'immagine del contenitore. 

![HealthCheckHealthy][3]

![HealthCheckUnealthyApp][4]

![HealthCheckUnhealthyDsp][5]

È possibile configurare il comportamento di **HEALTHCHECK** per ogni contenitore specificando le opzioni di **HealthConfig** come parte di **ContainerHostPolicies** in ApplicationManifest.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true" RestartContainerOnUnhealthyDockerHealthStatus="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Per impostazione predefinita, l'opzione *IncludeDockerHealthStatusInSystemHealthReport* è impostata su **true** e l'opzione *RestartContainerOnUnhealthyDockerHealthStatus* è impostata su **false**. Se l'opzione *RestartContainerOnUnhealthyDockerHealthStatus* è impostata su **true**, un contenitore che segnala ripetutamente uno stato non integro viene riavviato, possibilmente su altri nodi.

Se si vuole disabilitare l'integrazione di **HEALTHCHECK** per l'intero cluster di Service Fabric, sarà necessario impostare [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) su **false**.

## <a name="deploy-the-container-application"></a>Distribuire l'applicazione del contenitore
Salvare tutte le modifiche e compilare l'applicazione. Per pubblicare l'applicazione, fare clic con il pulsante destro del mouse su **MyFirstContainer** in Esplora soluzioni e scegliere **Pubblica**.

In **Endpoint connessione** immettere l'endpoint di gestione per il cluster, ad esempio "containercluster.westus2.cloudapp.azure.com:19000". L'endpoint di connessione del client è disponibile nella scheda Panoramica del cluster nel [portale di Azure](https://portal.azure.com).

Fare clic su **Pubblica**.

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) è uno strumento basato sul Web che permette di analizzare e gestire applicazioni e nodi in un cluster di Service Fabric. Aprire un browser Web e passare a http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ per seguire la distribuzione dell'applicazione. L'applicazione viene distribuita, ma rimane in stato di errore fino a quando l'immagine non viene scaricata nei nodi del cluster. L'operazione può richiedere del tempo, a seconda delle dimensioni dell'immagine: ![Errore][1]

L'applicazione è pronta quando è in stato ```Ready```: ![Pronto][2]

Aprire un browser e passare a http://containercluster.westus2.cloudapp.azure.com:8081. Verrà visualizzata l'intestazione "Hello World!" nel browser.

## <a name="clean-up"></a>Eseguire la pulizia
Mentre il cluster è in esecuzione, continuano a essere addebitati i relativi costi. È quindi consigliabile [eliminare il cluster](service-fabric-cluster-delete.md). I [party cluster](https://try.servicefabric.azure.com/) vengono eliminati automaticamente dopo alcune ore.

Dopo aver effettuato il push dell'immagine nel registro contenitori, è possibile eliminare l'immagine locale dal computer di sviluppo:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="specify-os-build-specific-container-images"></a>Specificare immagini del contenitore specifiche per la build del sistema operativo 

È possibile che i contenitori di Windows Server in modalità di isolamento dei processi non siano compatibili con versioni più recenti del sistema operativo. Ad esempio, i contenitori di Windows Server creati tramite Windows Server 2016 non funzionano in Windows Server versione 1709. Se i nodi del cluster vengono aggiornati alla versione più recente, è quindi possibile che si verifichino errori nei servizi contenitori creati usando le versioni precedenti del sistema operativo. Per aggirare questo problema con la versione 6.1 del runtime e con versioni successive, Service Fabric supporta la possibilità di specificare più immagini del sistema operativo per ogni contenitore e di contrassegnarle con le versioni della build del sistema operativo, ottenute tramite l'esecuzione di `winver` in un prompt dei comandi di Windows. Aggiornare i manifesti dell'applicazione e specificare gli override delle immagini per ogni versione del sistema operativo prima di aggiornare il sistema operativo nei nodi. Il frammento di codice seguente mostra come specificare più immagini del contenitore nel manifesto dell'applicazione, **ApplicationManifest.xml**:


```xml
<ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
     </ContainerHostPolicies> 
```
La versione della build per WIndows Server 2016 è 14393 e la versione della build per Windows Server 1709 è 16299. Il manifesto del servizio continua a specificare solo un'immagine per ogni servizio contenitore, come mostrato di seguito:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > Le funzionalità di contrassegno delle versioni della build del sistema operativo sono disponibili solo per Service Fabric su Windows
   >

Se la build del sistema operativo sottostante sulla VM è 16299 (versione 1709), Service Fabric seleziona l'immagine del contenitore corrispondente a tale versione di Windows Server. Se nel manifesto dell'applicazione viene fornita anche un'immagine del contenitore non contrassegnata insieme alle immagini del contenitore contrassegnate, Service Fabric considera l'immagine non contrassegnata come quella in grado di funzionare in tutte le versioni. Contrassegnare le immagini del contenitore in modo esplicito per evitare problemi durante gli aggiornamenti.

L'immagine del contenitore non contrassegnata eseguirà l'override dell'immagine specificata nel file ServiceManifest. L'immagine "myregistry.azurecr.io/samples/helloworldappDefault" eseguirà quindi l'override del nome immagine "myregistry.azurecr.io/samples/helloworldapp" nel file ServiceManifest.

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Manifesti di esempio completi del servizio e dell'applicazione di Service Fabric
Di seguito sono riportati i manifesti completi del servizio e dell'applicazione usati in questo articolo.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>Configurare l'intervallo di tempo prima della terminazione forzata del contenitore

È possibile configurare un intervallo di tempo di attesa del runtime prima che il contenitore venga rimosso dopo l'avvio dell'eliminazione del servizio (o di un passaggio a un altro nodo). Configurando l'intervallo di tempo, viene inviato il comando `docker stop <time in seconds>` al contenitore.  Per altri dettagli, vedere [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). L'intervallo di tempo per l'attesa viene specificato nella sezione `Hosting`. Il frammento di manifesto del cluster seguente illustra come impostare l'intervallo di attesa:

```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```
L'intervallo di tempo predefinito è impostato su 10 secondi. Poiché questa configurazione è dinamica, un aggiornamento della sola configurazione nel cluster aggiorna il timeout. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Configurare il runtime per rimuovere le immagini del contenitore non usate

È possibile configurate il cluster di Service Fabric per rimuovere le immagini del contenitore non usate dal nodo. Questa configurazione consente di riacquisire spazio su disco se nel nodo sono presenti troppe immagini del contenitore. Per abilitare questa funzionalità, aggiornare la sezione `Hosting` nel manifesto del cluster, come illustrato nel frammento seguente: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
} 
```

Le immagini che non devono essere eliminate possono essere specificate nel parametro `ContainerImagesToSkip`. 


## <a name="configure-container-image-download-time"></a>Configurare il tempo di download delle immagini del contenitore

Il runtime di Service Fabric alloca 20 minuti per il download e l'estrazione delle immagini del contenitore, perché tale limite è appropriato per la maggior parte delle immagini del contenitore. Per immagini di grandi dimensioni o in caso di connessione di rete lenta potrebbe essere necessario aumentare il tempo di attesa prima dell'interruzione del download e dell'estrazione dell'immagine. Questo timeout viene configurato tramite l'attributo **ContainerImageDownloadTimeout** nella sezione **Hosting** del manifesto del cluster, come mostrato nel frammento di codice seguente:

```json
{
"name": "Hosting",
        "parameters": [
          {
              "name": " ContainerImageDownloadTimeout ",
              "value": "1200"
          }
]
}
```


## <a name="set-container-retention-policy"></a>Configurare i criteri di conservazione dei contenitori

Per semplificare la diagnosi degli errori di avvio dei contenitori, Service Fabric (versione 6.1 o versioni successive) supporta la conservazione di contenitori terminati o il cui avvio non è riuscito. Questo criterio può essere configurato nel file **ApplicationManifest.xml**, come mostrato nel frammento di codice seguente:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

L'impostazione **ContainersRetentionCount** specifica il numero di contenitori da conservare in caso di errore. Se viene specificato un valore negativo, verranno conservati tutti i contenitori con errori. Quando l'attributo **ContainersRetentionCount** non viene specificato, non verrà conservato alcun contenitore. L'attributo **ContainersRetentionCount** supporta anche i parametri dell'applicazione, quindi gli utenti possono specificare valori diversi per cluster di test e di produzione. Usare vincoli di posizionamento per specificare come destinazione un nodo specifico per il servizio contenitore quando si usa questa funzionalità, per evitare che il servizio contenitore passi ad altri nodi. Eventuali contenitori conservati tramite questa funzionalità devono essere rimossi manualmente.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Avviare il daemon Docker con argomenti personalizzati

A partire dalla versione 6.2 del runtime di Service Fabric è possibile avviare il daemon Docker con argomenti personalizzati. Quando vengono specificati argomenti personalizzati, Service Fabric non passa altri argomenti al motore Docker, ad eccezione dell'argomento `--pidfile`. Di conseguenza, `--pidfile` non deve essere passato come argomento. L'argomento deve continuare ad avere il daemon Docker in ascolto sulla named pipe predefinita in Windows (o sul socket di dominio Unix in Linux) perché Service Fabric possa comunicare con il daemon. Gli argomenti personalizzati vengono passati nel manifesto del cluster nella sezione **Hosting** in **ContainerServiceArguments**, come mostrato nel frammento di codice seguente: 
 

```json
{ 
   "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'esecuzione di [contenitori in Service Fabric](service-fabric-containers-overview.md).
* Vedere l'esercitazione [Distribuire un'applicazione .NET in un contenitore](service-fabric-host-app-in-a-container.md).
* Altre informazioni sul [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md) di Service Fabric.
* Vedere gli [esempi di codice del contenitore di Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) in GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
