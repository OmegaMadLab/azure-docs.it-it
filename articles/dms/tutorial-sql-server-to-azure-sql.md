---
title: Usare il Servizio Migrazione del database di Azure per eseguire la migrazione di SQL Server nel database SQL di Azure | Microsoft Docs
description: Informazioni su come eseguire la migrazione da SQL Server locale al database SQL di Azure con il servizio Migrazione del database di Azure.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/07/2018
ms.openlocfilehash: c110011f3b4c3c677354bc8423c8cd86cca6ac90
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776177"
---
# <a name="migrate-sql-server-to-azure-sql-database-using-dms"></a>Eseguire la migrazione di SQL Server nel database SQL di Azure mediante il servizio Migrazione del database di Azure
È possibile usare il servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di SQL Server locale al [database SQL di Azure](https://docs.microsoft.com/en-us/azure/sql-database/). In questa esercitazione si esegue la migrazione del database **AdventureWorks2012** ripristinato in un'istanza locale di SQL Server 2016 o versione successiva verso un database SQL di Azure tramite il servizio Migrazione del database di Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Valutare il database locale usando Data Migration Assistant.
> * Eseguire la migrazione dello schema di esempio con Data Migration Assistant.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.
> * Scaricare un report di migrazione.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, è necessario:

- Scaricare e installare [SQL Server 2016 o versione successiva](https://www.microsoft.com/sql-server/sql-server-downloads) (qualsiasi edizione).
- Abilitare il protocollo TCP/IP, che viene disabilitato per impostazione predefinita durante l'installazione di SQL Server Express, seguendo le istruzioni riportate nell'articolo [Abilitare o disabilitare un protocollo di rete del server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Creare un'istanza dell'istanza del database SQL di Azure seguendo le istruzioni riportate nell'articolo [Creare un database SQL di Azure nel portale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Scaricare e installare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3 o versione successiva.
- Creare una rete virtuale per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Verificare che le regole dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure non blocchino le porte di comunicazione seguenti: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere a SQL Server di origine (per impostazione predefinita attraverso la porta TCP 1433).
- Se si eseguono più istanze di SQL Server denominate usando le porte dinamiche, è consigliabile abilitare il Servizio browser SQL e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che il Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
- Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
- Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello di server per il server di database SQL di Azure per consentire al Servizio Migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.
- Assicurarsi che le credenziali usate per connettersi all'istanza di origine di SQL Server abbiano le autorizzazioni [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Assicurarsi che le credenziali usate per connettersi all'istanza del database SQL di Azure di destinazione abbiano l'autorizzazione CONTROL DATABASE nei database SQL di Azure di destinazione.

## <a name="assess-your-on-premises-database"></a>Valutare il database locale
Prima di eseguire la migrazione dei dati da un'istanza di SQL Server locale al database SQL Azure, è necessario esaminare il database SQL Server per rilevare eventuali problemi di blocco che potrebbero impedire la migrazione. Usando Data Migration Assistant v3.3 o versione successiva, seguire la procedura descritta nell'articolo [Eseguire una valutazione della migrazione a SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) per completare la valutazione del database locale. Di seguito è riportato un riepilogo dei passaggi necessari:
1.  In Data Migration Assistant selezionare l'icona Nuovo (+) e quindi selezionare il tipo di progetto **Valutazione**.
2.  Specificare il nome del progetto, nella casella di testo **Tipo del server di origine** selezionare **SQL Server**, nella casella di testo **Tipo del server di destinazione** selezionare **Database SQL di Azure** e quindi selezionare **Crea** per creare il progetto.

    Quando si esegue la valutazione della migrazione del database di SQL Server di origine al database SQL di Azure, è possibile scegliere uno o entrambi i tipi di report di valutazione seguenti:
    - Check database compatibility (Verificare la compatibilità del database)
    - Check feature parity (Verificare la parità di funzionalità)

    Entrambi i tipi di report sono selezionati per impostazione predefinita.

4.  In Data Migration Assistant selezionare **Avanti** nella schermata **Opzioni**.
5.  Nella schermata **Seleziona origini** della finestra di dialogo **Connetti a un server** indicare i dettagli della connessione a SQL Server e quindi selezionare **Connetti**.
6.  Nella finestra di dialogo **Aggiungi origini** selezionare **AdventureWorks2012**, selezionare **Aggiungi** e quindi selezionare **Start Assessment** (Avvia valutazione).

    Al termine della valutazione, i risultati vengono visualizzati come mostrato nella figura seguente:

    ![Valutare la migrazione dei dati](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    Per il database SQL di Azure, le valutazioni identificano i problemi di parità della funzionalità e i problemi che causano un blocco della migrazione.

    - La categoria di **parità delle funzionalità di SQL Server** offre un set completo di indicazioni, approcci alternativi disponibili in Azure e procedure di mitigazione che consentono di pianificare le attività nei progetti di migrazione.
    - La categoria relativa ai **problemi di compatibilità** identifica funzionalità parzialmente supportate o non supportate che riflettono i problemi di compatibilità che potrebbero bloccare la migrazione dei database di SQL Server locali aL database SQL di Azure. Vengono anche fornite raccomandazioni che consentono di risolvere tali problemi.

7.  Esaminare i risultati della valutazione per identificare problemi di blocco della migrazione e problemi di parità della funzionalità selezionando le opzioni specifiche.

## <a name="migrate-the-sample-schema"></a>Eseguire la migrazione dello schema di esempio
Dopo aver acquisito familiarità con la valutazione e aver verificato che il database scelto sia un candidato idoneo per la migrazione al database SQL di Azure, usare Data Migration Assistant per eseguire la migrazione dello schema al database SQL di Azure.

> [!NOTE]
> Prima di creare un progetto di migrazione in Data Migration Assistant, assicurarsi di aver già eseguito il provisioning di un database SQL di Azure come indicato nei prerequisiti. Ai fini di questa esercitazione, si presuppone che il nome del database SQL di Azure sia **AdventureWorksAzure**, ma è possibile specificare il nome che si preferisce.

Per eseguire la migrazione dello schema **AdventureWorks2012** al database SQL di Azure, eseguire le operazioni seguenti:

1.  In Data Migration Assistant selezionare l'icona Nuovo (+) e quindi in **Tipo di progetto** selezionare **Migrazione**.
3.  Specificare il nome del progetto, nella casella di testo **Source server type** (Tipo di server di origine) selezionare **SQL Server** e quindi nella casella di testo **Target server type** (Tipo di server di destinazione) selezionare **Database SQL di Azure**.
4.  In **Migration Scope** (Ambito della migrazione) selezionare **Schema only** (Solo schema).

    Dopo aver eseguito i passaggi precedenti, viene visualizzata l'interfaccia di Data Migration Assistant come illustrato nella figura seguente:
    
    ![Creare un progetto di Data Migration Assistant](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  Selezionare **Crea** per creare il progetto.
6.  In Data Migration Assistant specificare i dettagli della connessione di origine per Server SQL, selezionare **Connetti**, quindi selezionare il database **AdventureWorks2012**.

    ![Dettagli della connessione di origine Data Migration Assistant](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)

7.  Selezionare **Avanti** in **Connect to target server** (Connetti al server di destinazione), specificare i dettagli della connessione di destinazione per il database SQL di Azure, selezionare **Connetti**, quindi selezionare il database **AdventureWorksAzure** di cui è già stato eseguito il provisioning nel database SQL di Azure.

    ![Dettagli della connessione di destinazione Data Migration Assistant](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)

8.  Selezionare **Avanti** per passare alla schermata **Seleziona oggetti**, in cui è possibile specificare gli oggetti dello schema nel database **AdventureWorks2012** che devono essere distribuiti in database SQL di Azure.

    Per impostazione predefinita, sono selezionati tutti gli oggetti.

    ![Generare script SQL](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)

9.  Selezionare **Genera script SQL** per creare gli script SQL e quindi esaminare gli script per individuare eventuali errori.

    ![Script dello schema](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)

10. Selezionare **Deploy schema** (Distribuisci schema) per distribuire lo schema nel database SQL di Azure e in seguito alla distribuzione dello schema, verificare che il server di destinazione non presenti anomalie.

    ![Distribuire lo schema](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration
1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.
 
   ![Mostra le sottoscrizioni del portale](media\tutorial-sql-server-to-azure-sql\portal-select-subscription1.png)
       
2. Selezionare la sottoscrizione in cui si desidera creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.
 
    ![Visualizzare i provider di risorse](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)
    
3.  Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.
 
    ![Registrare il provider di risorse](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Creare un'istanza
1.  Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)

2.  Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.
 
    ![Creare l'istanza del Servizio Migrazione del database di Azure](media\tutorial-sql-server-to-azure-sql\dms-create1.png)
  
3.  Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare una rete virtuale (VNET) esistente o crearne una nuova.

    La VNET consente al servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza del database SQL di Azure di destinazione.

    Per altre informazioni su come creare una VNET nel portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

5. Selezione di un piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    Se serve assistenza nella scelta del piano tariffario del servizio Migrazione del database di Azure, vedere i suggerimenti in questo [post](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media\tutorial-sql-server-to-azure-sql\dms-settings1.png)

6.  Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione
Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.
 
      ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. Nella schermata **Servizi Migrazione del database di Azure** cercare il nome dell'istanza Servizio Migrazione del database di Azure appena creata e quindi selezionare l'istanza.
 
     ![Individuare l'istanza del Servizio Migrazione del database di Azure](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Selezionare **+ Nuovo progetto di migrazione**.
4. Nella schermata **New migration project** (Nuovo progetto di migrazione) specificare il nome del progetto, nella casella di testo **Source server type** (Tipo di server di origine) selezionare **SQL Server** e quindi nella casella di testo **Target server type** (Tipo di server di destinazione) selezionare **Database SQL di Azure**.

    ![Creare il progetto del Servizio Migrazione del database](media\tutorial-sql-server-to-azure-sql\dms-create-project1.png)

5.  Selezionare **Crea** per creare il progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine
1. Nella schermata **Dettagli origine** specificare i dettagli di connessione per il nome dell'istanza di SQL Server di origine.
 
    Accertarsi di usare un nome di dominio completo (FQDN) per il nome dell'istanza di SQL Server di origine. Nelle situazioni in cui la risoluzione del nome DNS non è possibile, si può usare l'indirizzo IP.

2. Se nel server di origine non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server**.

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni SSL crittografate con un certificato autofirmato non forniscono una sicurezza avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. È consigliabile non usare SSL con i certificati autofirmati in un ambiente di produzione o in server connessi a Internet.

   ![Dettagli origine](media\tutorial-sql-server-to-azure-sql\dms-source-details1.png)
  
2. Selezionare **Salva**, quindi selezionare il database **AdventureWorks2012** per la migrazione.

    ![Selezionare il database di origine](media\tutorial-sql-server-to-azure-sql\dms-select-source-db1.png)

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione
1. Selezionare **Salva**, quindi nella schermata **Dettagli destinazione** specificare i dettagli di connessione per il server di database SQL di Azure di destinazione, ovvero il database SQL di Azure di cui è già stato eseguito il provisioning e in cui lo schema **AdventureWorks2012** è stato distribuito tramite Data Migration Assistant.

    ![Selezionare la destinazione](media\tutorial-sql-server-to-azure-sql\dms-select-target1.png)

2. Selezionare **Salva** per salvare il progetto.

3. Nella schermata **Riepilogo progetto** esaminare e verificare i dettagli associati al progetto di migrazione.

    ![Riepilogo del Servizio Migrazione del database](media\tutorial-sql-server-to-azure-sql\dms-summary1.png)

4. Selezionare **Salva**.

## <a name="run-the-migration"></a>Eseguire la migrazione
1.  Selezionare il progetto salvato di recente, selezionare **+ Nuova attività** e quindi selezionare **Esegui migrazione**.

    ![Nuova attività](media\tutorial-sql-server-to-azure-sql\dms-new-activity1.png)

2.  Quando richiesto, immettere le credenziali per i server di origine e di destinazione e quindi selezionare **Salva**.

3.  Nella schermata **Map to target databases** (Esegui il mapping nel database di destinazione) eseguire il mapping del database di origine e del database di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome di database del database di origine, Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Eseguire il mapping nei database di destinazione](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity1.png)

4. Selezionare **Salva**, quindi nella schermata **Seleziona tabelle** espandere l'elenco delle tabelle ed esaminare l'elenco dei campi interessati.

    Tenere presente che il servizio Migrazione del database di Azure seleziona automaticamente tutte le tabelle di origine vuote esistenti nell'istanza di database SQL di Azure di destinazione. Se si vuole eseguire di nuovo la migrazione delle tabelle che includono già dati, occorre selezionarle esplicitamente in questo pannello.

    ![Selezionare le tabelle](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity1.png)

5.  Selezionare **Salva** nella schermata **Migration summary** (Riepilogo migrazione), nella casella di testo **Nome attività** specificare un nome per l'attività di migrazione.

6. Espandere la sezione **Opzione di convalida** per visualizzare la schermata **Scegli l'opzione di convalida**, che varia a seconda che si voglia convalidare il confronto schema, la coerenza dei dati e la correttezza delle query nei database di cui è stata eseguita la migrazione.
    
    ![Scegliere l'opzione di convalida](media\tutorial-sql-server-to-azure-sql\dms-configuration1.png)

6.  Selezionare **Salva**, esaminare il riepilogo per assicurarsi che i dettagli di origine e di destinazione corrispondano a quanto specificato in precedenza.

    ![Riepilogo della migrazione](media\tutorial-sql-server-to-azure-sql\dms-run-migration1.png)

7.  Selezionare **Esegui migrazione**.

    Viene visualizzata la finestra dell'attività di migrazione con il campo **Stato** dell'attività impostato su **In sospeso**.

    ![Stato attività](media\tutorial-sql-server-to-azure-sql\dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorare la migrazione
1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **Completata**.

    ![Stato attività: Completata](media\tutorial-sql-server-to-azure-sql\dms-completed-activity1.png)

2. Al termine della migrazione, selezionare **Scarica report** per ottenere un report con i dettagli associati al processo di migrazione.

3. Verificare i database di destinazione nel database SQL di Azure di destinazione.

### <a name="additional-resources"></a>Risorse aggiuntive

 * Laboratorio pratico sulla [migrazione SQL con il servizio Migrazione del database di Azure](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587).