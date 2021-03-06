---
title: Creare un ambiente di sviluppo Kubernetes nel cloud utilizzando .NET Core e Visual Studio | Documentazione Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenitori
manager: douge
ms.openlocfilehash: 012efcbd3fa87268f3a68fdac524ce8310d10120
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362057"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core-and-visual-studio"></a>Guida introduttiva ad Azure Dev Spaces con .NET Core e Visual Studio

In questa guida si apprenderà come:

- Creare un ambiente basato su Kubernetes ottimizzato per lo sviluppo in Azure.
- Sviluppare codice in modo iterativo nei contenitori con Visual Studio.
- Sviluppare in modo indipendente due servizi distinti e utilizzare l'individuazione del servizio DNS di Kubernetes per effettuare una chiamata a un altro servizio.
- Sviluppare e testare il codice in modo produttivo in un ambiente di team.

[!INCLUDE[](includes/see-troubleshooting.md)]

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="get-the-visual-studio-tools"></a>Ottenere gli strumenti di Visual Studio
1. Installare l'ultima versione di [Visual Studio 2017](https://www.visualstudio.com/vs/)
1. Nel programma di installazione di Visual Studio assicurarsi che sia selezionato il carico di lavoro seguente:
    * Sviluppo Web e ASP.NET
1. Installare l'[estensione di Visual Studio per Azure Dev Spaces](https://aka.ms/get-azds-visualstudio)

È ora possibile creare un'app Web ASP.NET con Visual Studio.

## <a name="create-an-aspnet-web-app"></a>Creare un'app Web ASP.NET

In Visual Studio 2017 creare un nuovo progetto. Attualmente deve trattarsi di un progetto **Applicazione Web ASP.NET Core**. Assegnare al progetto il nome "**webfrontend**".

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Selezionare il modello **Applicazione Web (MVC)** e assicurarsi che nei due elenchi a discesa nella parte superiore della finestra di dialogo siano selezionati **.NET Core** e **ASP.NET Core 2.0**. Fare clic su **OK** per creare il progetto.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)


## <a name="create-a-dev-environment-in-azure"></a>Creare un ambiente di sviluppo in Azure

Con Azure Dev Spaces è possibile creare ambienti di sviluppo basati su Kubernetes completamente gestiti da Azure e ottimizzati per lo sviluppo. Con il progetto appena creato aperto, selezionare **Azure Dev Spaces** nell'elenco a discesa delle impostazioni di avvio, come illustrato di seguito.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Nella finestra di dialogo che viene quindi visualizzata verificare di aver effettuato l'accesso con l'account appropriato e quindi selezionare un cluster Kubernetes esistente.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Per il momento, nell'elenco a discesa **Spazio** lasciare l'impostazione predefinita `default`. Questa opzione verrà approfondita in seguito. Selezionare la casella di controllo **Publicly Accessible** (Accessibile pubblicamente) affinché l'app Web sia accessibile tramite un endpoint pubblico. Questa impostazione non è obbligatoria, ma sarà utile per illustrare alcuni concetti più avanti in questa procedura dettagliata. In ogni caso sarà comunque possibile eseguire il debug del sito Web con Visual Studio.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Fare clic su **OK** per selezionare o creare il cluster.

Se si sceglie un cluster che non è stato abilitato per l'uso di Azure Dev Spaces, verrà visualizzato un messaggio che chiede se si vuole eseguire la configurazione.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Scegliere **OK**.

 Verrà avviata un'attività in background a tale scopo. Il completamento dell'attività richiederà alcuni minuti. Per verificare se è ancora in corso la creazione, passare il puntatore sull'icona **Attività in background** nell'angolo inferiore sinistro della barra di stato, come illustrato nell'immagine seguente.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> Finché non viene completata la creazione dell'ambiente di sviluppo, non è possibile eseguire il debug dell'applicazione.

## <a name="look-at-the-files-added-to-project"></a>Esaminare i file aggiunti al progetto
Mentre si attende che venga creato l'ambiente di sviluppo, esaminare i file che sono stati aggiunti al progetto quando si è scelto di usare un ambiente di sviluppo.

Per prima cosa, è possibile osservare che è stata aggiunta una cartella denominata `charts` e che in tale cartella è stato eseguito lo scaffolding di un [chart Helm](https://docs.helm.sh) per l'applicazione. Questi file vengono usati per distribuire l'applicazione nell'ambiente di sviluppo.

Si noterà che è stato aggiunto un file denominato `Dockerfile`, che contiene le informazioni necessarie per creare un pacchetto dell'applicazione nel formato Docker standard. Viene creato anche un file `HeaderPropagation.cs`, che verrà illustrato più avanti nella procedura dettagliata. 

Si noterà infine un file denominato `azds.yaml`, che contiene le informazioni di configurazione necessarie all'ambiente di sviluppo, ad esempio per determinare se l'applicazione dovrà essere accessibile tramite un endpoint pubblico.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Eseguire il debug di un contenitore in Kubernetes
Al termine della creazione dell'ambiente di sviluppo, è possibile eseguire il debug dell'applicazione. Impostare un punto di interruzione nel codice, ad esempio alla riga 20 del file `HomeController.cs` in cui viene impostata la variabile `Message`. Premere **F5** per avviare il debug. 

Visual Studio comunicherà con l'ambiente di sviluppo per compilare e distribuire l'applicazione e quindi aprire un browser con l'app Web in esecuzione. Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nell'ambiente di sviluppo in Azure. L'indirizzo localhost viene usato perché Azure Dev Spaces crea un tunnel SSH temporaneo per il contenitore eseguito in Azure.

Fare clic sul collegamento **About** nella parte superiore della pagina per attivare il punto di interruzione. Le informazioni di debug, come stack di chiamate, variabili locali, informazioni sulle eccezioni e così via, sono completamente accessibili come in caso di esecuzione del codice in locale.

## <a name="call-another-container"></a>Chiamare un altro contenitore
In questa sezione si creerà un secondo servizio `mywebapi` al quale `webfrontend` assegnerà un nome. Ogni servizio viene eseguito in contenitori separati. Verrà quindi eseguito il debug in entrambi i contenitori.

![](media/common/multi-container.png)

## <a name="download-sample-code-for-mywebapi"></a>Scaricare il codice di esempio per *mywebapi*
Per motivi di tempo, scarichiamo il codice di esempio da un repository GitHub. Passare a https://github.com/Azure/dev-spaces e selezionare **Clone or Download** (Clona o scarica) per scaricare il repository GitHub. Il codice per questa sezione è disponibile in `samples/dotnetcore/getting-started/mywebapi`.

## <a name="run-mywebapi"></a>Eseguire *mywebapi*
1. Aprire il progetto `mywebapi` in una *finestra di Visual Studio separata*.
1. Selezionare **Azure Dev Spaces** nell'elenco a discesa delle impostazioni di avvio, come in precedenza per il progetto `webfrontend`. Anziché creare un nuovo ambiente di sviluppo ora, selezionare lo stesso già creato. Come in precedenza, lasciare lo spazio impostato sul valore predefinito `default` e fare clic su **OK**. Nella finestra Output si nota che Visual Studio inizia a riscaldare il nuovo servizio nell'ambiente di sviluppo per velocizzare le operazioni quando si avvia il debug.
1. Premere F5 e attendere la compilazione e la distribuzione del servizio. Il servizio è pronto quando la barra di stato di Visual Studio diventa arancione
1. Prendere nota dell'URL dell'endpoint visualizzato nel riquadro **Azure Dev Spaces per AKS** nella finestra **Output**. Sarà simile a http://localhost:\<portnumber\>. Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nell'ambiente di sviluppo in Azure.
2. Quando `mywebapi` è pronto, aprire il browser all'indirizzo localhost e aggiungere `/api/values` all'URL per richiamare l'API GET predefinita per `ValuesController`. 
3. Se tutte le operazioni hanno avuto esito positivo, dovrebbe venire visualizzata una risposta da parte del servizio `mywebapi` simile a quanto segue.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

## <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Creare una richiesta da *webfrontend* a *mywebapi*
Ora scriviamo il codice in `webfrontend` che crea una richiesta a `mywebapi`. Passare alla finestra di Visual Studio con il progetto `webfrontend`. Nel file `HomeController.cs` *sostituire* il codice per il metodo About con il codice seguente:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      // Use HeaderPropagatingHttpClient instead of HttpClient so we can propagate
      // headers in the incoming request to any outgoing requests
      using (var client = new HeaderPropagatingHttpClient(this.Request))
      {
          // Call *mywebapi*, and display its response in the page
          var response = await client.GetAsync("http://mywebapi/api/values/1");
          ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
      }

      return View();
   }
   ```

Si noti come l'individuazione del servizio DNS di Kubernetes venga utilizzata per fare riferimento al servizio come `http://mywebapi`. **Il codice nell'ambiente di sviluppo viene eseguito come in produzione**.

L'esempio di codice precedente utilizza anche una classe `HeaderPropagatingHttpClient`. Questa classe helper è il file `HeaderPropagation.cs` che è stato aggiunto al progetto quando è stato configurato per l'uso di Azure Dev Spaces. `HeaderPropagatingHttpClient` è derivato dalla nota classe `HttpClient` e aggiunge funzionalità per propagare intestazioni specifiche da un oggetto HttpRequest ASP .NET esistente in un oggetto HttpRequestMessage in uscita. Più avanti si vedrà come ciò agevoli un'esperienza di sviluppo più produttiva negli scenari di team.

## <a name="debug-across-multiple-services"></a>Eseguire il debug in più servizi
1. A questo punto `mywebapi` dovrebbe ancora essere in esecuzione con il debugger collegato. In caso contrario, premere F5 nel progetto `mywebapi`.
1. Impostare un punto di interruzione nel metodo `Get(int id)` nel file `ValuesController.cs` che gestisce richieste GET `api/values/{id}`.
1. Nel punto del progetto `webfrontend` in cui è stato incollato il codice precedente, impostare un punto di interruzione prima che una richiesta GET venga inviata a `mywebapi/api/values`.
1. Premere F5 nel progetto `webfrontend`. Visual Studio apre di nuovo un browser sulla porta localhost appropriata e viene visualizzata l'app Web.
1. Fare clic sul collegamento **About** nella parte superiore della pagina per attivare il punto di interruzione nel progetto `webfrontend`. 
1. Premere F10 per continuare. Il punto di interruzione nel progetto `mywebapi` viene attivato.
1. Premere F5 per continuare e tornare al codice nel progetto `webfrontend`.
1. Se si preme F5 ancora una volta la richiesta viene completata e si torna a una pagina nel browser. Nell'app Web, la pagina About contiene un messaggio concatenato dai due servizi: "Salve da webfrontend e Salve da mywebapi".

Ecco fatto! È ora disponibile un'applicazione multicontenitore in cui è possibile sviluppare e distribuire separatamente ogni contenitore.

## <a name="learn-about-team-development"></a>Informazioni sul team di sviluppo

Finora il codice dell'applicazione è stato eseguito come se si fosse l'unico sviluppatore a lavorare sull'app. In questa sezione, si apprenderà come Azure Dev Spaces semplifichi lo sviluppo in team:
* Consente a un team di sviluppatori di lavorare nello stesso ambiente di sviluppo.
* Supporta ogni sviluppatore che itera sul proprio codice in isolamento senza il timore di interrompere gli altri.
* Permette di testare il codice end-to-end, prima di eseguirne il commit, senza dover creare simulazioni delle dipendenze.

### <a name="challenges-with-developing-microservices"></a>Problemi con lo sviluppo di microservizi
L'applicazione di esempio non è molto complessa al momento. Nello sviluppo nel mondo reale, tuttavia, le sfide emergono appena si aggiungono più servizi e il team di sviluppo cresce.

Si immagini di lavorare a un servizio che interagisce con dozzine di altri servizi.

- Può diventare irrealistico eseguire tutto in locale per lo sviluppo. Il computer di sviluppo potrebbe non disporre di risorse sufficienti per eseguire l'intera app. In alternativa, è probabile che l'app disponga di endpoint che devono essere raggiungibili pubblicamente (ad esempio l'app risponde a un webhook da un'app SaaS).
- È possibile provare a eseguire solo i servizi da cui si dipende, ma in tal caso sarebbe necessario conoscere la chiusura completa delle dipendenze (ad esempio le dipendenze delle dipendenze). Potrebbe anche non essere semplice capire come compilare ed eseguire le proprie dipendenze perché non si è lavorato su di esse.
- Alcuni sviluppatori ricorrono alla simulazione di molte delle loro dipendenze di servizio. Ciò può essere utile a volte, ma la gestione di quelle simulazioni può pesare presto sul proprio lavoro di sviluppo. Inoltre, in questo modo l'ambiente di sviluppo è visto in modo diverso dalla produzione e potrebbero insorgere bug.
- È quindi difficile eseguire qualsiasi tipo di test end-to-end. I test di integrazione possono avvenire in modo realistico solo dopo il commit, il che significa che i problemi verranno riscontrati più avanti nel ciclo di sviluppo.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-development-environment"></a>Lavorare in un ambiente di sviluppo condiviso
Con Azure Dev Spaces è possibile impostare un ambiente di sviluppo *condiviso* in Azure. Ogni sviluppatore può concentrarsi solo sulla propria parte dell'applicazione e in modo iterativo può sviluppare *codice pre-commit* in un ambiente che contiene già tutti gli altri servizi e le risorse cloud da cui dipendono i propri scenari. Le dipendenze sono sempre aggiornate e gli sviluppatori lavorano in un modo che rispecchia la produzione.

### <a name="work-in-your-own-space"></a>Lavorare nello spazio personale
Quando si sviluppa codice per un servizio, prima che sia pronto per l'archiviazione, il codice spesso non è in buono stato. Sono ancora in corso attività iterative di modellazione, test e sperimentazione con le soluzioni. Azure Dev Spaces fornisce il concetto di uno **spazio**, che consente di lavorare in isolamento senza timore di interrompere i membri del team.

Procedere come segue per assicurarsi che entrambi i servizi `webfrontend` e `mywebapi` siano in esecuzione nell'ambiente di sviluppo **e nello spazio `default`**.
1. Chiudere qualsiasi sessione di debug/F5 per entrambi i servizi, ma tenere aperti i progetti nelle finestre di Visual Studio.
2. Passare alla finestra di Visual Studio con il progetto `mywebapi` e premere CTRL+F5 per eseguire il servizio senza debugger collegato
3. Passare alla finestra di Visual Studio con il progetto `webfrontend` e premere CTRL+F5 per eseguire anche questo.

> [!Note]
> In alcuni casi è necessario aggiornare il browser dopo la visualizzazione iniziale della pagina Web dopo CTRL+F5.

Chiunque apra l'URL pubblico e navighi all'app Web richiama il percorso del codice scritto in precedenza che viene eseguito in entrambi i servizi con lo spazio `default` predefinito. Si supponga di voler continuare a sviluppare `mywebapi`. Come è possibile eseguire questa operazione senza interrompere altri sviluppatori che utilizzano l'ambiente di sviluppo? A tale scopo, è necessario impostare il proprio spazio.

### <a name="create-a-new-space"></a>Creare un nuovo spazio
Da Visual Studio, è possibile creare spazi aggiuntivi che verranno utilizzati quando si utilizza F5 o CTRL+F5 nel servizio. È possibile assegnare qualsiasi nome allo spazio con la massima flessibilità di significato (ad esempio `sprint4` o `demo`).

Per creare un nuovo spazio, eseguire questa operazione:
1. Passare alla finestra di Visual Studio con il progetto `mywebapi`.
2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Proprietà**.
3. Selezionare la scheda **Debug** a sinistra per visualizzare le impostazioni di Azure Dev Spaces.
4. A questo punto, è possibile modificare o creare il cluster e/o lo spazio che sarà utilizzato quando si usa F5 o CTRL+F5. *Assicurarsi che sia selezionato lo spazio Azure Dev Spaces creato in precedenza*.
5. Nell'elenco a discesa Spazio, selezionare **<Crea nuovo spazio...>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Nella finestra di dialogo **Aggiungi spazio** digitare un nome per lo spazio e fare clic su **OK**. È possibile utilizzare il proprio nome (ad esempio "scott") per il nuovo spazio in modo che ai colleghi sia chiaro quale spazio sia in uso dallo sviluppatore.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. L'ambiente di sviluppo dovrebbe essere visibile e il nuovo spazio selezionato nella pagina delle proprietà del progetto.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Aggiornare il codice per *mywebapi*

1. Nel progetto `mywebapi` apportare una modifica al codice per il metodo `string Get(int id)` nel file `ValuesController.cs` come indicato di seguito:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Impostare un punto di interruzione in questo blocco di codice aggiornato (potrebbe essercene già uno impostato in precedenza).
3. Premere F5 per avviare il servizio `mywebapi`. Il servizio viene avviato nell'ambiente di sviluppo utilizzando lo spazio selezionato, che in questo caso è `scott`.

Di seguito è riportato un diagramma che aiuta a capire il funzionamento dei diversi spazi. Il percorso blu mostra una richiesta tramite lo spazio `default`, che è il percorso predefinito utilizzato se non viene anteposto un altro spazio all'URL. Il percorso verde indica una richiesta tramite lo spazio `scott`.

![](media/common/Space-Routing.png)

Questa funzionalità integrata di Azure Dev Spaces consente di testare codice end-to-end in un ambiente condiviso senza richiedere a ogni sviluppatore di ricreare lo stack completo dei servizi nel proprio spazio. Questo routing richiede l'inoltro delle intestazioni di propagazione nel codice dell'app, come illustrato nel passaggio precedente di questa Guida.

### <a name="test-code-running-in-the-scott-space"></a>Testare il codice in esecuzione nello spazio `scott`
Per testare la nuova versione di `mywebapi` in combinazione con `webfrontend`, aprire il browser all'URL di punto di accesso pubblico per `webfrontend`, ad esempio http://webfrontend-teamenv.123456abcdef.eastus.aksapp.io), e passare alla pagina delle informazioni. Verrà visualizzato il messaggio originale "Salve da webfrontend e Salve da mywebapi".

A questo punto, aggiungere "scott.s". all'URL in modo che sia simile a http://scott.s.webfrontend-teamenv.123456abcdef.eastus.aksapp.io e aggiornare il browser. Si dovrebbe raggiungere il punto di interruzione impostato nel progetto `mywebapi`. Premere F5 per continuare e nel browser dovrebbe essere visualizzato il nuovo messaggio "Salve da webfrontend e mywebapi ora dice qualcosa di nuovo". Ciò è dovuto al fatto che il percorso al codice aggiornato in `mywebapi` è in esecuzione nello spazio `scott`.

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
