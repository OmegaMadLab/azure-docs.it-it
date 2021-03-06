---
title: "Esercitazione su Kubernetes in Azure: aggiornare un'applicazione"
description: "Esercitazione sul servizio contenitore di Azure: aggiornare un'applicazione"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: df118a2b5bd8e31bd3fe6101d1d3f631092b6f24
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Esercitazione: aggiornare un'applicazione in Azure Kubernetes Service (AKS)

Dopo la distribuzione di un'applicazione in Kubernetes, è possibile aggiornarla specificando una nuova immagine del contenitore o una nuova versione dell'immagine. A questo scopo, l'aggiornamento viene eseguito a fasi in modo che solo una parte della distribuzione venga aggiornata contemporaneamente. Questo aggiornamento a fasi consente all'applicazione di rimanere in esecuzione durante l'aggiornamento. Fornisce inoltre un meccanismo di ripristino dello stato precedente se si verifica un errore di distribuzione.

In questa esercitazione, la sesta di otto, viene aggiornata l'app Azure Vote di esempio. Le attività da completare comprendono:

> [!div class="checklist"]
> * Aggiornamento del codice dell'applicazione front-end
> * Creazione di un'immagine del contenitore aggiornata
> * Push dell'immagine del contenitore in Registro contenitori di Azure
> * Distribuzione di un'immagine del contenitore aggiornata

Nelle esercitazioni successive viene configurato Log Analytics per il monitoraggio del cluster Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi in Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. L'applicazione è stata quindi eseguita nel cluster Kubernetes.

È stato clonato anche un repository di applicazione che include il codice sorgente dell'applicazione e un file Docker Compose creato in precedenza usato in questa esercitazione. Verificare che sia stato creato un clone del repository e che si abbia cambiato le directory nella directory clonata. All'interno si trova una directory denominata `azure-vote` e un file denominato `docker-compose.yaml`.

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Esercitazione 1 - Creare immagini del contenitore][aks-tutorial-prepare-app].

## <a name="update-application"></a>Aggiornare l'applicazione

Per questa esercitazione, viene apportata una modifica all'applicazione e l'applicazione aggiornata viene distribuita nel cluster Kubernetes.

Il codice sorgente dell'applicazione è disponibile nella directory `azure-vote`. Aprire il file `config_file.cfg` con qualsiasi editor di testo o codice. In questo esempio viene usato `vi` .

```console
vi azure-vote/azure-vote/config_file.cfg
```

Cambiare i valori per `VOTE1VALUE` e `VOTE2VALUE`, quindi salvare il file.

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Salvare e chiudere il file.

## <a name="update-container-image"></a>Aggiornare l'immagine del contenitore

Usare [docker-compose][docker-compose] per ricreare l'immagine front-end ed eseguire l'applicazione aggiornata. L'argomento `--build` viene usato per indicare a Docker Compose di ricreare l'immagine dell'applicazione.

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Testare l'applicazione in locale

Passare a http://localhost:8080 per vedere l'applicazione aggiornata.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Applicare tag ed eseguire il push delle immagini

Applicare il tag loginServer del registro contenitori all'immagine `azure-vote-front`.

Ottenere il nome del server di accesso con il comando [az acr list](/cli/azure/acr#az_acr_list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Usare [docker tag][docker-tag] per assegnare il tag all'immagine. Sostituire `<acrLoginServer>` con il nome di accesso del server del Registro contenitori di Azure o un nome host di un registro pubblico. Si noti anche che la versione dell'immagine viene aggiornata a `v2`.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Usare [docker push][docker-push] per caricare l'immagine nel registro. Sostituire `<acrLoginServer>` con il nome del server di accesso del Registro contenitori di Azure. In caso di problemi nell'esecuzione del push nella propria istanza di Registro contenitori di Azure, verificare di aver eseguito il comando [az acr login][az-acr-login].

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-application"></a>Distribuire l'aggiornamento dell'applicazione

Per garantire il tempo di attività massimo, è necessario eseguire più istanze del pod dell'applicazione. Verificare questa configurazione con il comando [kubectl get pod][kubectl-get].

```
kubectl get pod
```

Output:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Se non si dispone di più pod che eseguono l'immagine azure-vote-front, ridimensionare la distribuzione di `azure-vote-front`.


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

Per aggiornare l'applicazione, usare il comando [kubectl set][kubectl-set]. Aggiornare `<acrLoginServer>` con il server di accesso o il nome host del registro contenitori.

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Per monitorare la distribuzione, usare il comando [kubectl get pod][kubectl-get]. Quando l'applicazione aggiornata viene distribuita, le unità vengono terminate e ricreate con la nuova immagine del contenitore.

```azurecli
kubectl get pod
```

Output:

```
NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-updated-application"></a>Testare l'applicazione aggiornata

Ottenere l'indirizzo IP esterno del servizio `azure-vote-front`.

```azurecli
kubectl get service azure-vote-front
```

Passare all'indirizzo IP per vedere l'applicazione aggiornata.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata aggiornata un'applicazione e l'aggiornamento è stato distribuito in un cluster Kubernetes. Sono state completate le attività seguenti:

> [!div class="checklist"]
> * Aggiornamento del codice dell'applicazione front-end
> * Creazione di un'immagine del contenitore aggiornata
> * Push dell'immagine del contenitore in Registro contenitori di Azure
> * Distribuzione dell'applicazione aggiornata

Passare alla prossima esercitazione per apprendere come monitorare Kubernetes con Log Analytics.

> [!div class="nextstepaction"]
> [Monitorare Kubernetes con Log Analytics][aks-tutorial-monitor]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-monitor]: ./tutorial-kubernetes-monitor.md
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login