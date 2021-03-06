---
title: Uso di Kafka MirrorMaker con Hub eventi di Azure per l'ecosistema Kafka | Microsoft Docs
description: Usare Kafka MirrorMaker per eseguire il mirroring di un cluster Kafka in Hub eventi.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 05/07/2018
ms.author: bahariri
ms.openlocfilehash: 819071321d5609728e7c62abb5b25bf354107850
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941239"
---
# <a name="using-kafka-mirrormaker-with-event-hubs-for-kafka-ecosystems"></a>Uso di Kafka MirrorMaker con Hub eventi per gli ecosistemi Kafka

> [!NOTE]
> Questo esempio è disponibile su [GitHub](https://github.com/Azure/azure-event-hubs)

Una delle considerazioni principali per le moderne app a livello del cloud è la possibilità di aggiornare, migliorare e modificare l'infrastruttura senza interrompere il servizio. Questa esercitazione illustra in che modo Hub eventi con supporto per Kafka e Kafka MirrorMaker siano in grado di integrare una pipeline Kafka esistente in Azure eseguendo il "mirroring" del flusso di input Kafka nel servizio Hub eventi. 

Un endpoint Kafka di Hub eventi di Azure consente la connessione a Hub eventi di Azure tramite il protocollo Kafka, ovvero i client Kafka. Apportando modifiche minime a un'applicazione Kafka, è possibile connettersi a Hub eventi di Azure e sfruttare i vantaggi dell'ecosistema di Azure. Hub eventi con supporto per Kafka supporta attualmente Kafka 1.0 e versioni successive.

Questo esempio mostra come eseguire il mirroring di un broker Kafka in Hub eventi con supporto per Kafka usando Kafka MirrorMaker.

   ![Kafka MirrorMaker con Hub eventi](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, accertarsi di avere:

* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * In Ubuntu eseguire `apt-get install default-jdk` per installare JDK.
    * Assicurarsi di impostare la variabile di ambiente JAVA_HOME in modo che faccia riferimento alla cartella di installazione di JDK.
* [Scaricare](http://maven.apache.org/download.cgi) e [installare](http://maven.apache.org/install.html) un archivio binario Maven.
    * In Ubuntu è possibile eseguire `apt-get install maven` per installare Maven.
* [Git](https://www.git-scm.com/downloads)
    * In Ubuntu è possibile eseguire `sudo apt-get install git` per installare Git.

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Per l'invio e la ricezione da qualsiasi servizio Hub eventi è richiesto uno spazio dei nomi di Hub eventi. Vedere [Creazione di un hub eventi con supporto per Kafka](event-hubs-create.md) per istruzioni su come ottenere un endpoint Kafka di Hub eventi. Assicurarsi di copiare la stringa di connessione di Hub eventi per usarla in seguito.

## <a name="clone-the-example-project"></a>Clonare il progetto di esempio

Dopo avere creato una stringa di connessione di Hub eventi con supporto per Kafka, clonare il repository di Hub eventi di Azure e passare alla sottocartella `mirror-maker`:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configurare un cluster Kafka

Usare la [Guida introduttiva per Kafka](https://kafka.apache.org/quickstart) per configurare un cluster con le impostazioni desiderate (o usare un cluster Kafka esistente).

## <a name="kafka-mirrormaker"></a>Kafka MirrorMaker

Kafka MirrorMaker consente il "mirroring" di un flusso. Dati i cluster Kafka di origine e di destinazione, MirrorMaker garantisce che qualsiasi messaggio inviato al cluster di origine venga ricevuto da entrambi i cluster di origine e di destinazione. Questo esempio illustra come eseguire il mirroring di un cluster Kafka di origine con una destinazione Kafka con supporto per Hub eventi. Questo scenario può essere usato per inviare i dati da una pipeline Kafka esistente a Hub eventi senza interrompere il flusso dei dati. 

Per informazioni più dettagliate su Kafka MirrorMaker, vedere la guida [Kafka Mirroring/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (Mirroring Kafka/MirrorMaker).

### <a name="configuration"></a>Configurazione

Per configurare Kafka MirrorMaker, assegnare un cluster Kafka come consumer/origine e un hub eventi con supporto per Kafka come producer/destinazione.

#### <a name="consumer-configuration"></a>Configurazione del consumer

Aggiornare il file di configurazione del consumer `source-kafka.config`, che specifica a MirrorMaker le proprietà del cluster Kafka di origine.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configurazione del producer

Aggiornare quindi il file di configurazione del producer `mirror-eventhub.config`, che specifica a MirrorMaker di inviare i dati duplicati (o "con mirroring") al servizio Hub eventi. In particolare, modificare `bootstrap.servers` e `sasl.jaas.config` in modo che puntino all'endpoint Kafka di Hub eventi. Il servizio Hub eventi richiede la comunicazione sicura (SASL), che viene garantita impostando le ultime tre proprietà nella configurazione seguente: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-mirrormaker"></a>Eseguire MirrorMaker

Eseguire lo script di Kafka MirrorMaker dalla directory radice di Kafka usando i file di configurazione appena aggiornati. Assicurarsi di copiare i file di configurazione nella directory radice di Kafka o di aggiornare i relativi percorsi nel comando seguente.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Per verificare che gli eventi raggiungano l'hub eventi con supporto per Kafka, visualizzare le statistiche in ingresso nel [portale di Azure](https://azure.microsoft.com/features/azure-portal/) o eseguire un consumer nell'hub eventi.

Con MirrorMaker in esecuzione, qualsiasi evento inviato al cluster Kafka di origine viene ricevuto sia dal cluster Kafka che dal servizio Hub eventi con supporto per Kafka con mirroring. Usando MirrorMaker e un endpoint Kafka di Hub eventi, è possibile migrare una pipeline Kafka esistente al servizio Hub eventi di Azure gestito senza modificare il cluster esistente o interrompere eventuali flussi di dati in corso.

## <a name="next-steps"></a>Passaggi successivi

* [Leggere le informazioni su Hub eventi](event-hubs-what-is-event-hubs.md)
* [Leggere le informazioni su Hub eventi per l'ecosistema Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Leggere le informazioni su [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) per trasmettere in streaming eventi dall'istanza di Kafka locale all'istanza di Hub eventi con supporto per Kafka nel cloud.
