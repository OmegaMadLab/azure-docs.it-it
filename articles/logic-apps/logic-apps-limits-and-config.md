---
title: Limiti e configurazione - App per la logica di Azure | Microsoft Docs
description: Limiti di servizio e valori di configurazione per App per la logica di Azure
services: logic-apps
documentationcenter: ''
author: ecfan
manager: cfowler
editor: ''
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/14/2018
ms.author: estfan
ms.openlocfilehash: 8c2ac4b8f55d25d5d3fcfdd6a9bcb6f6c8cfc201
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166302"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informazioni su limiti e configurazione per App per la logica di Azure

Questo articolo include informazioni dettagliate sui limiti e sulla configurazione per la creazione e l'esecuzione di flussi di lavoro automatici con App per la logica di Azure. Per Microsoft Flow, vedere [Limiti e configurazione in Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limiti delle definizioni

Ecco i limiti per una singola definizione di app per la logica:

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Azioni per flusso di lavoro | 500 | Per estendere questo limite, è possibile aggiungere flussi di lavoro annidati in base alle esigenze. |
| Livello di annidamento consentito per le azioni | 8 | Per estendere questo limite, è possibile aggiungere flussi di lavoro annidati in base alle esigenze. | 
| Flussi di lavoro per area per sottoscrizione | 1.000 | | 
| Trigger per flusso di lavoro | 10 | Quando si usa la visualizzazione codice e non la finestra di progettazione | 
| Limite ambito switch-case | 25 | | 
| Variabili per flusso di lavoro | 250 | | 
| Caratteri per espressione | 8.192 | | 
| Dimensioni massime per `trackedProperties` | 16.000 caratteri | 
| Nome per `action` o `trigger` | 80 caratteri | | 
| Lunghezza di `description` | 256 caratteri | | 
| Massimo per `parameters` | 50 | | 
| Massimo per `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Durata dell'esecuzione e limiti di conservazione

Ecco i limiti per una singola esecuzione di app per la logica:

| NOME | Limite | Note | 
|------|-------|-------| 
| Durata esecuzione | 90 giorni | Per modificare questo limite, vedere [Modificare la durata dell'esecuzione e la conservazione nella risorsa di archiviazione](#change-duration). | 
| Conservazione in risorsa di archiviazione | 90 giorni dalla data di inizio dell'esecuzione | Per modificare questo limite, vedere [Modificare la durata dell'esecuzione e la conservazione nella risorsa di archiviazione](#change-retention). | 
| Intervallo di ricorrenza minimo | 1 secondo | | 
| Intervallo di ricorrenza massimo | 500 giorni | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Modificare la durata dell'esecuzione e la conservazione nella risorsa di archiviazione

È possibile modificare questo limite specificando un valore compreso tra 7 e 90 giorni. Per superare il limite massimo, tuttavia, [contattare il team di App per la logica](mailto://logicappsemail@microsoft.com) per informazioni sui requisiti.

1. Nel menu dell'app per la logica nel portale di Azure scegliere **Impostazioni del flusso di lavoro**. 

2. In **Opzioni di runtime** scegliere **Personalizzata** nell'elenco **Conservazione cronologia di esecuzione in giorni**. 

3. Immettere un valore o trascinare il dispositivo di scorrimento in base al numero di giorni desiderato.

<a name="looping-debatching-limits"></a>

## <a name="looping-and-debatching-limits"></a>Limiti di esecuzione di cicli e debatching

Ecco i limiti per una singola esecuzione di app per la logica:

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Iterazioni Until | 5.000 | | 
| Elementi ForEach | 100.000 | È possibile usare l'[azione di query](../connectors/connectors-native-query.md) per filtrare matrici di dimensioni maggiori in base alle esigenze. | 
| Parallelismo ForEach | 50 | Il valore predefinito è 20. <p>Per impostare un livello specifico di parallelismo in un ciclo ForEach, impostare la proprietà `runtimeConfiguration` nell'azione `foreach`. <p>Per eseguire in modo sequenziale un ciclo ForEach, impostare la proprietà `operationOptions` su "Sequential" nell'azione `foreach`. | 
| Elementi SplitOn | 100.000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limiti di velocità effettiva

Ecco i limiti per una singola esecuzione di app per la logica:

| NOME | Limite | Note | 
| ----- | ----- | ----- | 
| Esecuzioni di azioni per 5 minuti | 100.000 | Per aumentare il limite a 300.000, è possibile eseguire un'app per la logica in modalità `High Throughput`. Per configurare la modalità di velocità effettiva elevata, in `runtimeConfiguration` della risorsa flusso di lavoro impostare la proprietà `operationOptions` su `OptimizedForHighThroughput`. <p>**Nota**: la modalità di velocità effettiva elevata è in anteprima. È anche possibile distribuire un carico di lavoro tra più app, se necessario. | 
| Chiamate in uscita simultanee di azioni | ~2.500 | Diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. | 
| Endpoint di runtime: chiamate in ingresso simultanee | ~1,000 | Diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. | 
| Endpoint di runtime: lettura delle chiamate per 5 minuti  | 60.000 | È possibile distribuire il carico di lavoro tra più app nel modo necessario. | 
| Endpoint di runtime: richiamata delle chiamate per 5 minuti| 45,000 |È possibile distribuire il carico di lavoro tra più app nel modo necessario. | 
|||| 

Per superare questi limiti nell'elaborazione normale o per eseguire test di carico che possono superare questi limiti, [contattare il team di App per la logica](mailto://logicappsemail@microsoft.com) per ottenere assistenza sui requisiti specifici.

<a name="request-limits"></a>

## <a name="http-request-limits"></a>Limiti delle richieste HTTP

Ecco i limiti per una singola richiesta HTTP o a una chiamata sincrona di un connettore:

#### <a name="timeout"></a>Timeout

Alcune operazioni dei connettori effettuano chiamate asincrone o sono in ascolto di richieste di webhook e di conseguenza il timeout per queste operazioni può essere più prolungato rispetto a questi limiti. Per altre informazioni, vedere i dettagli tecnici per il connettore specifico e anche [Trigger e azioni dei flussi di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Richiesta in uscita | 120 secondi | Per operazioni di esecuzione più lunghe, usare un [modello di polling asincrono](../logic-apps/logic-apps-create-api-app.md#async-pattern) o un [ciclo until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Risposta sincrona | 120 secondi | Perché la richiesta originale ottenga la risposta, tutti i passaggi nella risposta devono terminare entro il limite, a meno che non venga chiamata un'altra app per la logica come flusso di lavoro annidato. Per altre informazioni, vedere [Chiamare, attivare o annidare app per la logica](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Dimensioni dei messaggi

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Dimensioni dei messaggi | 100 MB | Per ignorare questo limite, vedere [Gestire messaggi di grandi dimensioni con la divisione in blocchi](../logic-apps/logic-apps-handle-large-messages.md). Tuttavia, alcuni connettori e API potrebbero non supportare la divisione in blocchi o addirittura il limite predefinito. | 
| Dimensione dei messaggi con la divisione in blocchi | 1 GB | Questo limite si applica alle azioni che supportano in modo nativo la divisione in blocchi o per cui può essere abilitato il supporto della divisione in blocchi nella configurazione di runtime. Per altre informazioni, vedere [Gestire messaggi di grandi dimensioni con la divisione in blocchi](../logic-apps/logic-apps-handle-large-messages.md). | 
| Limite per la valutazione delle espressioni | 131.072 caratteri | Le espressioni `@concat()`, `@base64()` e `@string()` non possono superare questo limite. | 
|||| 

#### <a name="retry-policy"></a>Criteri di ripetizione

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Tentativi | 90 | Il valore predefinito è 4. Per modificare il valore predefinito, usare il [parametro dei criteri di ripetizione](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Intervallo massimo tra i tentativi | 1 giorno | Per modificare il valore predefinito, usare il [parametro dei criteri di ripetizione](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Intervallo minimo tra i tentativi | 5 secondi | Per modificare il valore predefinito, usare il [parametro dei criteri di ripetizione](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limiti per i connettori personalizzati

Limiti per i connettori personalizzati che è possibile creare da API Web.

| NOME | Limite | 
| ---- | ----- | 
| Numero di connettori personalizzati | 1.000 per ogni sottoscrizione di Azure | 
| Numero di richieste al minuto per ogni connessione creata da un connettore personalizzato | 500 richieste per connessione |
|||| 

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limiti dell'account di integrazione

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limite di elementi per account di integrazione

Limiti per il numero di elementi per ogni account di integrazione. Per altre informazioni, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/).

*Livello gratuito*

| Elemento | Limite | Note | 
|----------|-------|-------| 
| Partner commerciali EDI | 25 | | 
| Contratti commerciali EDI | 10 | | 
| Mappe | 25 | | 
| Schemi | 25 | 
| Assembly | 10 | | 
| Configurazioni batch | 5 | 
| Certificati | 25 | | 
|||| 

*Livello Basic*

| Elemento | Limite | Note | 
|----------|-------|-------| 
| Partner commerciali EDI | 2 | | 
| Contratti commerciali EDI | 1 | | 
| Mappe | 500 | | 
| Schemi | 500 | 
| Assembly | 25 | | 
| Configurazioni batch | 1 | | 
| Certificati | 2 | | 
|||| 

*Livello Standard*

| Elemento | Limite | Note | 
|----------|-------|-------| 
| Partner commerciali EDI | 500 | | 
| Contratti commerciali EDI | 500 | | 
| Mappe | 500 | | 
| Schemi | 500 | 
| Assembly | 50 | | 
| Configurazioni batch | 5 |  
| Certificati | 50 | | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limiti di capacità degli elementi

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| SCHEMA | 8 MB | Per caricare file di dimensioni maggiori di 2 MB, usare l'[URI del BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Mappa (file XSLT) | 2 MB | | 
| Endpoint di runtime: lettura delle chiamate per 5 minuti | 60.000 | È possibile distribuire il carico di lavoro tra più account in base alle esigenze. | 
| Endpoint di runtime: richiamata delle chiamate per 5 minuti | 45,000 | È possibile distribuire il carico di lavoro tra più account in base alle esigenze. | 
| Endpoint di runtime: verifica delle chiamate per 5 minuti | 45,000 | È possibile distribuire il carico di lavoro tra più account in base alle esigenze. | 
| Endpoint di runtime: blocco delle chiamate simultanee | ~1,000 | È possibile diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Dimensioni dei messaggi per i protocolli B2B (AS2, X12, EDIFACT)

Ecco i limiti che si applicano ai protocolli B2B:

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Applicabile alla decodifica e alla codifica | 
| X12 | 50 MB | Applicabile alla decodifica e alla codifica | 
| EDIFACT | 50 MB | Applicabile alla decodifica e alla codifica | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Configurazione: indirizzi IP

### <a name="azure-logic-apps-service"></a>Servizio App per la logica

Tutte le app per la logica in un'area usano lo stesso intervallo di indirizzi IP.
Le chiamate effettuate direttamente dalle app per la logica tramite [HTTP](../connectors/connectors-native-http.md) o [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) o altre richieste HTTP provengono dagli indirizzi IP inclusi in questo elenco. 

| Area di App per la logica | IP in uscita |
|-------------------|-------------|
| Australia | 13.73.114.207, 13.77.3.139, 13.70.159.205 |
| Australia orientale | 13.75.149.4, 104.210.91.55, 104.210.90.241 |
| Brasile meridionale | 191.235.82.221, 191.235.91.7, 191.234.182.26 |
| Canada centrale | 52.233.29.92, 52.228.39.241, 52.228.39.244 |
| Canada orientale | 52.232.128.155, 52.229.120.45, 52.229.126.25 |
| India centrale | 52.172.154.168, 52.172.186.159, 52.172.185.79 |
| Stati Uniti centrali | 13.67.236.125, 104.208.25.27, 40.122.170.198 |
| Asia orientale | 13.75.94.173, 40.83.127.19, 52.175.33.254 |
| Stati Uniti orientali | 13.92.98.111, 40.121.91.41, 40.114.82.191 |
| Stati Uniti orientali 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174 |
| Giappone orientale | 13.71.158.3, 13.73.4.207, 13.71.158.120 |
| Giappone occidentale | 40.74.140.4, 104.214.137.243, 138.91.26.45 |
| Stati Uniti centro-settentrionali | 168.62.248.37, 157.55.210.61, 157.55.212.238 |
| Europa settentrionale | 40.113.12.95, 52.178.165.215, 52.178.166.21 |
| Stati Uniti centro-meridionali | 104.210.144.48, 13.65.82.17, 13.66.52.232 |
| India meridionale | 52.172.50.24, 52.172.55.231, 52.172.52.0 |
| Asia sudorientale | 13.76.133.155, 52.163.228.93, 52.163.230.166 |
| Stati Uniti centro-occidentali | 52.161.27.190, 52.161.18.218, 52.161.9.108 |
| Europa occidentale | 40.68.222.65, 40.68.209.23, 13.95.147.65 |
| India occidentale | 104.211.164.80, 104.211.162.205, 104.211.164.136 |
| Stati Uniti occidentali | 52.160.92.112, 40.118.244.241, 40.118.241.243 |
| Stati Uniti occidentali 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132 |
| Regno Unito meridionale | 51.140.74.14, 51.140.73.85, 51.140.78.44 |
| Regno Unito occidentale | 51.141.54.185, 51.141.45.238, 51.141.47.136 |
| | |

| Area di App per la logica | IP In ingresso |
|-------------------|-------------|
| Australia orientale | 3.75.153.66, 104.210.89.222, 104.210.89.244 |
| Australia sudorientale | 13.73.115.153, 40.115.78.70, 40.115.78.237 |
| Brasile meridionale | 191.235.86.199, 191.235.95.229, 191.235.94.220 |
| Canada centrale | 13.88.249.209, 52.233.30.218, 52.233.29.79 |
| Canada orientale | 52.232.129.143, 52.229.125.57, 52.232.133.109 |
| India centrale | 52.172.157.194, 52.172.184.192, 52.172.191.194 |
| Stati Uniti centrali | 13.67.236.76, 40.77.111.254, 40.77.31.87 |
| Asia orientale | 168.63.200.173, 13.75.89.159, 23.97.68.172 |
| Stati Uniti orientali | 137.135.106.54, 40.117.99.79, 40.117.100.228 |
| Stati Uniti orientali 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136 |
| Giappone orientale | 13.71.146.140, 13.78.84.187, 13.78.62.130 |
| Giappone occidentale | 40.74.140.173, 40.74.81.13, 40.74.85.215 |
| Stati Uniti centro-settentrionali | 168.62.249.81, 157.56.12.202, 65.52.211.164 |
| Europa settentrionale | 13.79.173.49, 52.169.218.253, 52.169.220.174 |
| Stati Uniti centro-meridionali | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| India meridionale | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Asia sudorientale | 52.163.93.214, 52.187.65.81, 52.187.65.155 |
| Stati Uniti centro-occidentali | 52.161.26.172, 52.161.8.128, 52.161.19.82 |
| Europa occidentale | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| India occidentale | 104.211.164.112, 104.211.165.81, 104.211.164.25 |
| Stati Uniti occidentali | 52.160.90.237, 138.91.188.137, 13.91.252.184 |
| Stati Uniti occidentali 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Regno Unito meridionale | 51.140.79.109, 51.140.78.71, 51.140.84.39 |
| Regno Unito occidentale | 51.141.48.98, 51.141.51.145, 51.141.53.164 |
| | |

### <a name="connectors"></a>Connettori

Le chiamate effettuate dai [connettori](../connectors/apis-list.md) provengono dagli indirizzi IP inclusi in questo elenco.

| Area di App per la logica | IP in uscita |
|-------------------|-------------|
| Australia orientale | 40.126.251.213 |
| Australia sudorientale | 40.127.80.34 |
| Brasile meridionale | 191.232.38.129 |
| Canada centrale | 52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13 |
| Canada orientale | 52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52 |
| India centrale | 104.211.98.164 |
| Stati Uniti centrali | 40.122.49.51 |
| Asia orientale | 23.99.116.181 |
| Stati Uniti orientali | 191.237.41.52 |
| Stati Uniti orientali 2 | 104.208.233.100 |
| Giappone orientale | 40.115.186.96 |
| Giappone occidentale | 40.74.130.77 |
| Stati Uniti centro-settentrionali | 65.52.218.230 |
| Europa settentrionale | 104.45.93.9 |
| Stati Uniti centro-meridionali | 104.214.70.191 |
| India meridionale | 104.211.227.225 |
| Asia sudorientale | 13.76.231.68 |
| Europa occidentale | 40.115.50.13 |
| India occidentale | 104.211.161.203 |
| Stati Uniti occidentali | 104.40.51.248 |
| Regno Unito meridionale | 51.140.80.51 |
| Regno Unito occidentale | 51.141.47.105 |
| | | 

## <a name="next-steps"></a>Passaggi successivi  

* [Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* [Common examples and scenarios](../logic-apps/logic-apps-examples-and-scenarios.md) (Esempi e scenari comuni)
* [Video: Automate business processes with Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) (Automatizzare processi aziendali con App per la logica) 
* [Video: Integrate your systems with Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462) (Integrare i sistemi con App per la logica di Azure)
