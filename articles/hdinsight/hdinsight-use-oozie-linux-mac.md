---
title: Usare flussi di lavoro Oozie con Hadoop in HDInsight basato su Linux | Microsoft Docs
description: Usare Oozie di Hadoop in HDInsight basati su Linux. Informazioni su come definire un flusso di lavoro Oozie e inviare un processo Oozie.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 8a25507ab076c4eecccea4e8a503d68ff1441ae5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro in Azure HDInsight basato su Linux

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Informazioni su come usare Apache Oozie con Hadoop in Azure HDInsight. Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop. Oozie è integrato con lo stack Hadoop e supporta i processi seguenti:

* Apache MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Oozie può anche essere usato per pianificare processi specifici di un sistema, come programmi Java o script della shell.

> [!NOTE]
> Per definire i flussi di lavoro con HDInsight, è anche possibile usare Azure Data Factory. Per altre informazioni su Azure Data Factory, vedere [Usare Pig e Hive con Data Factory][azure-data-factory-pig-hive].

> [!IMPORTANT]
> Oozie non è abilitato su HDInsight appartenente al dominio.

## <a name="prerequisites"></a>prerequisiti

* **Un cluster di HDInsight**: vedere [Introduzione a HDInsight in Linux](/hadoop/apache-hadoop-linux-tutorial-get-started.md)

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="example-workflow"></a>Esempio di flusso di lavoro

Il flusso di lavoro usato in questo documento prevede due azioni. Le azioni sono definizioni di attività, ad esempio l'esecuzione di processi Hive, Sqoop, MapReduce o altri:

![Diagramma del flusso di lavoro][img-workflow-diagram]

1. Un'azione di Hive esegue uno script HiveQL per estrarre i record da **hivesampletable**, inclusa in HDInsight. Ogni riga di dati descrive una visita da un dispositivo mobile specifico. Il formato del record risulterà simile al testo seguente:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Lo script Hive usato in questo documento conta le visite totali per ogni piattaforma (ad esempio Android o iPhone) e archivia i conteggi in una nuova tabella Hive.

    Per altre informazioni su Hive, vedere [Usare Hive con HDInsight][hdinsight-use-hive].

2. Un'azione di Sqoop esporta il contenuto della nuova tabella Hive in una tabella creata nel database SQL di Azure. Per altre informazioni su Sqoop, vedere [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Per informazioni sulle versioni di Oozie supportate nei cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Creare la directory di lavoro

Oozie presuppone che tutte le risorse necessarie per un processo siano archiviate nella stessa directory. Questo esempio usa **wasb:///tutorials/useoozie**. Per creare questa directory, completare la procedura seguente:

1. Connettersi al cluster HDInsight usando SSH:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Sostituire `sshuser` con il nome utente SSH per il cluster. Sostituire `clustername` con il nome del cluster. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Per creare la directory, usare il comando seguente:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]
    > Il parametro `-p` crea tutte le directory nel percorso. La directory **data** viene usata per contenere i dati usati dallo script **useooziewf.hql**.

3. Per assicurarsi che Oozie possa rappresentare l'account utente, usare il comando seguente:

    ```bash
    sudo adduser username users
    ```

    Sostituire `username` con il nome utente SSH personale.

    > [!NOTE]
    > È possibile ignorare gli errori che indicano che l'utente è già un membro del gruppo `users`.

## <a name="add-a-database-driver"></a>Aggiungere un driver di database

Poiché questo flusso di lavoro usa Sqoop per esportare dati nel database SQL, è necessario che sia disponibile una copia del driver JDBC usato per interagire con il database SQL. Per copiare il driver JDBC nella directory di lavoro, usare il comando seguente della sessione SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

> [!NOTE]
> È possibile che venga visualizzato il messaggio "File già esistente".

Se il flusso di lavoro ha usato altre risorse, ad esempio un file con estensione jar contenente un'applicazione MapReduce, è necessario aggiungere anche queste risorse.

## <a name="define-the-hive-query"></a>Definire la query Hive

Usare la procedura seguente per creare uno script HiveQL (linguaggio di query Hive) che definisce una query che verrà usata in un flusso di lavoro Oozie più avanti in questo documento.

1. Dalla connessione SSH, usare il comando seguente per creare un file denominato `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. All'apertura dell'editor nano GNU, usare la query seguente come contenuto del file:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Nello script vengono usate due variabili:

    * `${hiveTableName}`: contiene il nome della tabella da creare.

    * `${hiveDataFolder}`: contiene il percorso in cui archiviare i file di dati per la tabella.

    Il file di definizione del flusso di lavoro, in questa esercitazione workflow.xml, passa questi valori allo script HiveQL in fase di esecuzione.

4. Per uscire dall'editor, premere CTRL+X. Quando richiesto, selezionare `Y` per salvare il file, immettere `useooziewf.hql` come nome del file e quindi premere **INVIO**.

5. Per copiare `useooziewf.hql` in `wasb:///tutorials/useoozie/useooziewf.hql`, usare i comandi riportati di seguito:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Questo comando archivia il file `useooziewf.hql` nella risorsa di archiviazione compatibile con HDFS per il cluster.

## <a name="define-the-workflow"></a>Definire il flusso di lavoro

Le definizioni del flusso di lavoro di Oozie sono scritte in un linguaggio di definizione dei processi XML denominato hPDL (Hadoop Process Definition Language). Usare i passaggi seguenti per definire il flusso di lavoro:

1. Usare l'istruzione seguente per creare e modificare un nuovo file:

    ```bash
    nano workflow.xml
    ```

2. All'apertura dell'editor nano, immettere il codice XML seguente come contenuto del file:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>sqljdbc41.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    Nel flusso di lavoro vengono definite due azioni:

   * `RunHiveScript`: azione di avvio che esegue lo script `useooziewf.hql` di Hive.

   * `RunSqoopExport`: azione che esporta i dati creati dallo script Hive nel database SQL tramite Sqoop. Questa azione viene eseguita solo se l'azione `RunHiveScript` ha esito positivo.

     Il flusso di lavoro include molte voci, ad esempio `${jobTracker}`. Sostituire queste voci con i valori usati nella definizione del processo, che verrà creata più avanti in questo documento.

     Notare anche la voce `<archive>sqljdbc4.jar</archive>` nella sezione Sqoop. Questa voce indica a Oozie di rendere disponibile questo archivio per Sqoop quando l'azione viene eseguita.

3. Per salvare il file, selezionare CTRL+X, immettere `Y` e quindi selezionare **INVIO**. 

4. Usare il comando seguente per copiare il file `workflow.xml` in `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Creare il database

Per creare un database SQL, attenersi alla procedura illustrata nel documento [Creare un database SQL](../sql-database/sql-database-get-started.md). Quando si crea il database, usare `oozietest` come nome del database. Annotare anche il nome del server di database.

### <a name="create-the-table"></a>Creare la tabella

> [!NOTE]
> Sono disponibili diversi modi per connettersi al database SQL per creare una tabella. Nei seguenti passaggi viene usato [FreeTDS](http://www.freetds.org/) dal cluster HDInsight.


1. Usare il comando seguente per installare FreeTDS nel cluster HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Dopo aver installato FreeTDS, usare il comando seguente per connettersi al server del database SQL creato in precedenza:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    L'output ricevuto ha un aspetto simile al testo seguente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Al prompt di `1>` , immettere il codice seguente:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Dopo aver immesso l'istruzione `GO`, vengono valutate le istruzioni precedenti. Queste istruzioni creano una tabella denominata **mobiledata**, usata dal flusso di lavoro.

    Per verificare che la tabella sia stata creata, usare i comandi seguenti:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Viene visualizzato un output simile al testo seguente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

4. Per uscire dall'utilità tsql, immettere `exit` al prompt `1>`.

## <a name="create-the-job-definition"></a>Creare la definizione del processo

La definizione del processo descrive dove trovare il file workflow.xml. Descrive inoltre dove trovare altri file usati dal flusso di lavoro, ad esempio `useooziewf.hql`. Definisce inoltre i valori delle proprietà usate nel flusso di lavoro e nei file associati.

1. Per ottenere l'indirizzo completo della risorsa di archiviazione predefinita, usare il comando seguente. L'indirizzo viene usato nel file di configurazione che verrà creato nel passaggio successivo.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Questo comando restituisce informazioni simili al codice XML seguente:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Se il cluster HDInsight usa l'archiviazione di Azure come percorso di archiviazione predefinito, il contenuto dell'elemento `<value>` inizia con `wasb://`. Se, invece, viene usato Azure Data Lake Store, il contenuto inizia con `adl://`.

    Salvare il contenuto dell'elemento `<value>`, necessario nei passaggi successivi.

2. Per creare la configurazione della definizione del processo Oozie, usare il comando seguente:

    ```bash
    nano job.xml
    ```

3. All'apertura dell'editor nano, usare il codice XML seguente come contenuto del file:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Sostituire tutte le istanze di `wasb://mycontainer@mystorageaccount.blob.core.windows.net` con il valore ricevuto in precedenza per la risorsa di archiviazione predefinita.

     > [!WARNING]
     > Se il percorso è un percorso `wasb`, è necessario usare il percorso completo, che non deve essere abbreviato in `wasb:///`.

   * Sostituire `YourName` con il proprio nome di accesso per il cluster HDInsight.
   * Sostituire `serverName`, `adminLogin` e `adminPassword` con le informazioni relative al database SQL.

     La maggior parte delle informazioni in questo file viene usata per popolare i valori usati nel file workflow.xml o ooziewf.hql (ad esempio `${nameNode}`).

     > [!NOTE]
     > La voce `oozie.wf.application.path` definisce il percorso in cui trovare il file workflow.xml, che contiene il flusso di lavoro eseguito da questo processo.

5. Per salvare il file, selezionare CTRL+X, immettere `Y` e quindi selezionare **INVIO**.

## <a name="submit-and-manage-the-job"></a>Inviare e gestire il processo

La procedura seguente usa il comando Oozie per inviare e gestire i flussi di lavoro di Oozie nel cluster. Il comando Oozie è un'interfaccia utente semplice per l' [API REST di Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> Quando si usa il comando Oozie, è necessario usare l'FQDN per il nodo head di HDInsight. Questo FQDN è accessibile solo dal cluster o, se il cluster si trova in una rete virtuale di Azure, da altri computer nella stessa rete.


1. Per ottenere l'URL del servizio di Oozie, usare questo comando:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Vengono restituite informazioni simili al codice XML seguente:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    La parte `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` è l'URL da usare con il comando Oozie.

2. Per creare una variabile di ambiente per l'URL, usare il codice seguente, in modo che non sia necessario eseguirne la digitazione per ogni comando:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Sostituire l'URL con quello ricevuto in precedenza.
3. Per inviare il processo, usare quanto segue:

    ```bash
    oozie job -config job.xml -submit
    ```

    Questo comando carica le informazioni sul processo da `job.xml` e le invia a Oozie, ma senza eseguirle.

    Dopo il completamento, il comando dovrebbe restituire l'ID del processo, ad esempio `0000005-150622124850154-oozie-oozi-W`. L'ID viene usato per gestire il processo.

4. Per visualizzare lo stato del processo, usare il comando seguente:

    ```bash
    oozie job -info <JOBID>
    ```

    > [!NOTE]
    > Sostituire `<JOBID>` con l'ID restituito nel passaggio precedente.

    Verranno restituite informazioni simili al testo seguente:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Lo stato del processo è `PREP`. Questo stato indica che il processo è stato creato, ma non avviato.

5. Per avviare il processo, usare il comando seguente:

    ```bash
    oozie job -start JOBID
    ```

    > [!NOTE]
    > Sostituire `<JOBID>` con l'ID restituito in precedenza.

    Dopo questo comando, lo stato risulterà in esecuzione e verranno restituite informazioni relative alle azioni all'interno del processo.

6. Dopo aver completato l'attività, è possibile verificare che i dati siano stati generati ed esportati nella tabella del database SQL tramite i comandi seguenti:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    Al prompt di `1>` immettere la query seguente:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Le informazioni restituite sono simili al testo seguente:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Per altre informazioni sul comando Oozie, vedere [Oozie command-line tool](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html) (Strumento da riga di comando di Oozie).

## <a name="oozie-rest-api"></a>API REST di Oozie

Con l'API REST di Oozie, è possibile compilare strumenti personalizzati che funzionano con Oozie. Di seguito sono riportate informazioni specifiche di HDInsight sull'uso dell'API REST di Oozie:

* **URI**: è possibile accedere all'API REST all'esterno del cluster in `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Autenticazione**: per eseguire l'autenticazione, usare l'API, l'account (admin) e la password HTTP del cluster, Ad esempio: 

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Per altre informazioni sull'uso dell'API REST di Oozie, vedere la pagina relativa all'[API dei servizi Web di Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Interfaccia utente Web di Oozie

L'interfaccia utente Web di Oozie fornisce una visualizzazione basata sul Web dello stato dei processi Oozie nel cluster. L'interfaccia utente Web consente di visualizzare le informazioni seguenti:

   * Stato processo
   * Definizione del processo
   * Configurazione
   * Grafico delle azioni nel processo
   * Log per il processo

È anche possibile visualizzare i dettagli delle azioni all'interno di un processo.

Per accedere all'interfaccia utente Web di Oozie, completare la procedura seguente:

1. Creare un tunnel SSH per il cluster HDInsight. Per altre informazioni, vedere [Usare il tunneling SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Dopo aver creato un tunnel, aprire l'interfaccia utente Web di Ambari nel Web browser. L'URI per il sito Ambari è `https://CLUSTERNAME.azurehdinsight.net`. Sostituire `CLUSTERNAME` con il nome del cluster HDInsight basato su Linux.

3. Nel lato sinistro della pagina selezionare **Oozie** > **Quick Links (Collegamenti rapidi)** > **Oozie Web UI (Interfaccia utente Web di Oozie)**.

    ![Immagine dei menu](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Per impostazione predefinita, nell'interfaccia utente Web di Oozie sono visualizzati i processi del flusso di lavoro in esecuzione. Per visualizzare tutti i processi del flusso di lavoro, selezionare **All Jobs** (Tutti i processi).

    ![Tutti i processi visualizzati](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Per visualizzare altre informazioni specifiche di un processo, selezionarlo.

    ![Job Info](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Nella scheda **Job Info** (Informazioni processo) è possibile visualizzare informazioni di base sul processo e le singole azioni all'interno del processo. È possibile usare le schede nella parte superiore per visualizzare **Job Definition** (Definizione processo) e **Job Configuration** (Configurazione processo), accedere a **Job Log** (Log processo) o visualizzare un grafo aciclico diretto (DAG) del processo in **Job DAG** (DAG processo).

   * **Job Log** (Log processo): selezionare il pulsante **Get Logs** (Ottieni log) per recuperare tutti i log relativi al processo o usare il campo **Enter Search Filter** (Immetti filtro di ricerca) per filtrare i log.

       ![Job Log (Log processo)](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Job DAG** (DAG processo): il grafo aciclico diretto (DAG) è una rappresentazione grafica dei percorsi dati rilevati nel flusso di lavoro.

       ![DAG del processo](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Se si seleziona una delle azioni dalla scheda **Job Info** (Informazioni processo), vengono visualizzate informazioni sull'azione. Selezionare ad esempio l'azione **RunSqoopExport**.

    ![Informazioni sull'azione](./media/hdinsight-use-oozie-linux-mac/action.png)

8. È possibile visualizzare i dettagli per l'azione, ad esempio un collegamento a **Console URL** (URL della console). Usare questo collegamento per visualizzare le informazioni di JobTracker per il processo.

## <a name="schedule-jobs"></a>Pianificare i processi

È possibile usare il coordinatore per specificare inizio, fine e frequenza dei processi. Per definire una pianificazione per il flusso di lavoro, completare la procedura seguente:

1. Usare il comando seguente per creare un file denominato **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    Usare il codice XML seguente come contenuto del file:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]
    > Le variabili `${...}` vengono sostituite dai valori nella definizione del processo in fase di esecuzione. Le variabili sono:
    >
    > * `${coordFrequency}`: tempo trascorso tra l'esecuzione delle istanze del processo.
    > * `${coordStart}`: ora di inizio del processo.
    > * `${coordEnd}`: ora di fine del processo.
    > * `${coordTimezone}`: i processi del coordinatore si trovano in un fuso orario fisso che non tiene conto dell'ora legale (in genere rappresentato dall'acronimo UTC). Questo fuso orario viene definito *Fuso orario di elaborazione Oozie*.
    > * `${wfPath}`: percorso del file workflow.xml.

2. Per salvare il file, selezionare CTRL+X, immettere `Y` e quindi selezionare **INVIO**.

3. Per copiare il file nella directory di lavoro del processo, usare il comando seguente:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Per modificare il file `job.xml`, usare il comando seguente:

    ```
    nano job.xml
    ```

    Apportare le modifiche seguenti:

   * Per indicare a Oozie di eseguire il file del coordinatore invece del flusso di lavoro, sostituire `<name>oozie.wf.application.path</name>` con `<name>oozie.coord.application.path</name>`.

   * Per impostare la variabile `workflowPath` usata dal coordinatore, aggiornare il codice XML seguente:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Sostituire il testo `wasb://mycontainer@mystorageaccount.blob.core.windows` con il valore usato nelle altre voci del file job.xlm.

   * Per definire l'inizio, la fine e la frequenza per il coordinatore, aggiungere il codice XML seguente:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Questi valori impostano l'ora di inizio sulle 12:00 del 10 maggio 2017 e l'ora di fine sul 12 maggio 2017. L'intervallo per l'esecuzione del processo viene impostato come giornaliero. La frequenza è espressa in minuti, quindi 24 ore x 60 minuti = 1440 minuti. Il fuso orario è infine impostato su UTC.

5. Per salvare il file, selezionare CTRL+X, immettere `Y` e quindi selezionare **INVIO**.

6. Per eseguire il processo, usare il comando seguente:

    ```
    oozie job -config job.xml -run
    ```

    Il comando invia e avvia il processo.

7. Se si accede all'interfaccia utente Web di Oozie e si seleziona la scheda **Coordinator Jobs** (Processi coordinatore), si ottengono informazioni simili a quelle nell'immagine seguente:

    ![Scheda Coordinator Jobs (Processi coordinatore)](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    La voce **Next Materialization** (Materializzazione successiva) contiene l'orario per l'esecuzione successiva del processo.

8. Come per il processo del flusso di lavoro precedente, se si seleziona la voce del processo nell'interfaccia utente Web vengono visualizzate informazioni sul processo:

    ![Informazioni processo del coordinatore](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]
    > Questa immagine visualizza solo le esecuzioni riuscite del processo, non le singole azioni nel flusso di lavoro pianificato. Per visualizzare le singole azioni, selezionare una delle voci relative alle **azioni**.

    ![Informazioni sull'azione](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>risoluzione dei problemi

L'interfaccia utente di Oozie consente di visualizzare i log di Oozie. Contiene anche collegamenti ai log di JobTracker per le attività di MapReduce avviate dal flusso di lavoro. Il modello per la risoluzione dei problemi deve essere il seguente:

   1. Visualizzare il processo nell'interfaccia utente Web di Oozie.

   2. Se si verifica un errore o un problema per un'azione specifica, selezionare l'azione per vedere se il campo **Error Message** fornisce altre informazioni sull'errore.

   3. Se disponibile, usare l'URL dall'azione per visualizzare altri dettagli relativi all'azione, ad esempio i log di JobTracker.

Di seguito sono riportati errori specifici che possono verificarsi e viene indicato come risolverli.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Cannot initialize cluster

**Sintomi**: lo stato del processo cambia in **SUSPENDED**. I dettagli del processo mostrano lo stato di `RunHiveScript` come **START_MANUAL**. Selezionando l'azione verrà visualizzato il messaggio di errore seguente:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Causa**: gli indirizzi di archiviazione BLOB di Azure usati nel file **job.xml** non includono il contenitore di archiviazione o il nome dell'account di archiviazione. Il formato dell'indirizzo di archiviazione BLOB deve essere `wasb://containername@storageaccountname.blob.core.windows.net`.

**Risoluzione**: modificare gli indirizzi di archiviazione BLOB usati dal processo.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie is not allowed to impersonate &lt;USER>

**Sintomi**: lo stato del processo cambia in **SUSPENDED**. I dettagli del processo mostrano lo stato di `RunHiveScript` come **START_MANUAL**. Se si seleziona l'azione, viene visualizzato il messaggio di errore seguente:

    JA002: User: oozie is not allowed to impersonate <USER>

**Causa**: le impostazioni di autorizzazione correnti non consentono a Oozie di rappresentare l'account utente specificato.

**Risoluzione**: Oozie può rappresentare gli utenti nel gruppo **users**. Usare `groups USERNAME` per visualizzare i gruppi di cui l'account utente è membro. Se l'utente non è un membro del gruppo **users** , usare il comando seguente per aggiungere l'utente al gruppo:

    sudo adduser USERNAME users

> [!NOTE]
> Potrebbero essere necessari alcuni minuti prima che HDInsight riconosca che l'utente è stato aggiunto al gruppo.

### <a name="launcher-error-sqoop"></a>ERRORE dell'utilità di avvio (Sqoop)

**Sintomi**: lo stato del processo cambia in **KILLED**. I dettagli del processo mostrano lo stato di `RunSqoopExport` come **ERROR**. Se si seleziona l'azione, viene visualizzato il messaggio di errore seguente:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Causa**: Sqoop non è in grado di caricare il driver del database necessario per accedere al database.

**Risoluzione**: quando si usa Sqoop da un processo Oozie, è necessario includere il driver del database con le altre risorse usate dal processo, ad esempio workflow.xml. Fare inoltre riferimento all'archivio contenente il driver del database dalla sezione `<sqoop>...</sqoop>` di workflow.xml.

Ad esempio, per il processo in questo documento si useranno i passaggi seguenti:

1. Copiare il file `sqljdbc4.1.jar` nella directory **/tutorials/useoozie**:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Modificare `workflow.xml` per aggiungere il codice XML seguente in una nuova riga sopra `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come definire un flusso di lavoro di Oozie e come eseguire un processo Oozie. Per altre informazioni sull'uso di HDInsight, vedere gli articoli seguenti:

* [Usare il coordinatore Oozie basato sul tempo con HDInsight][hdinsight-oozie-coordinator-time]
* [Caricare dati per processi Hadoop in HDInsight][hdinsight-upload-data]
* [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop]
* [Usare Hive con Hadoop in HDInsight][hdinsight-use-hive]
* [Usare Pig con Hadoop in HDInsight][hdinsight-use-pig]
* [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
