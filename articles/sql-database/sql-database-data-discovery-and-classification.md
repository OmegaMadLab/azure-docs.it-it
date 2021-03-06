---
title: Individuazione dati e classificazione nel database SQL di Azure | Microsoft Docs
description: Individuazione dati e classificazione nel database SQL di Azure
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/18/2018
ms.author: giladm
ms.openlocfilehash: b43b010a88f313930217289549448de30a82a070
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363809"
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Individuazione dati e classificazione nel database SQL di Azure
Individuazione dati e classificazione (attualmente in anteprima) offre funzionalità avanzate incorporate nel database SQL di Azure per l'**individuazione**, la **classificazione**, l'**aggiunta di etichette** e la  & **protezione** dei dati sensibili presenti nel database.
L'individuazione e la classificazione dei dati più sensibili (dati commerciali, finanziari e relativi all'assistenza sanitaria, informazioni personali e così via) possono svolgere un ruolo fondamentale per il livello di protezione delle informazioni aziendali. Individuazione dati e classificazione può svolgere la funzione di infrastruttura per:
* Contribuire a soddisfare gli standard e i requisiti di conformità alle normative sulla privacy dei dati, come GDPR.
* Vari scenari di sicurezza, ad esempio monitoraggio (controllo) e invio di avvisi sulle anomalie di accesso a dati sensibili.
* Controllare l'accesso ai database che contengono dati riservati e rafforzare la sicurezza di questi.

Individuazione dati e classificazione fa parte dell'offerta [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) (Protezione avanzata dalle minacce SQL) (ATP), che è un pacchetto unificato per le funzionalità avanzate di sicurezza SQL. È possibile accedere e gestire Individuazione dati e classificazione tramite il portale centrale ATP SQL.

> [!NOTE]
> Questo documento si riferisce solo al database SQL di Azure. Per SQL Server (locale), vedere [SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999) (Individuazione e classificazione dei dati SQL).

## <a id="subheading-1"></a>Che cosa è Individuazione dati e classificazione?
Individuazione dati e classificazione introduce un set di servizi avanzati e nuove funzionalità SQL, che formano un nuovo paradigma di Information Protection per SQL finalizzato a proteggere i dati e non solo il database:
* **Individuazione e suggerimenti**: il motore di classificazione esegue l'analisi del database e identifica le colonne contenenti dati potenzialmente sensibili. Costituisce quindi un mezzo semplice e intuitivo per rivedere e applicare i suggerimenti di classificazione appropriati tramite il portale di Azure.
* **Assegnazione di etichette**: le colonne possono essere contrassegnate in modo permanente con etichette di classificazione della sensibilità tramite i nuovi attributi dei metadati di classificazione introdotti nel motore SQL. Questi metadati possono quindi essere usati per scenari avanzati di protezione e controllo basati sulla sensibilità.
* **Sensibilità del set di risultati della query** : la sensibilità del set di risultati della query viene calcolata in tempo reale a scopo di controllo.
* **Visibilità**: lo stato di classificazione del database può essere visualizzato in un dashboard dettagliato nel portale. È possibile anche scaricare un report (in formato Excel) da usare a scopo di conformità e controllo o per altre esigenze.

## <a id="subheading-2"></a>Individuare, classificare e assegnare un'etichetta alle colonne sensibili
La sezione seguente descrive la procedura per individuare, classificare e assegnare etichette a colonne contenenti dati sensibili nel database, nonché per visualizzare lo stato di classificazione corrente del database e per esportare report.

La classificazione include due attributi di metadati:
* Etichette: i principali attributi di classificazione, usati per definire il livello di sensibilità dei dati archiviati nella colonna.  
* Tipi di informazioni: forniscono granularità aggiuntiva al tipo di dati archiviati nella colonna.

## <a name="classify-your-sql-database"></a>Classificare il database SQL

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare ad **Advanced Threat Protection** sotto l'intestazione Sicurezza nel riquadro del database SQL di Azure. Fare clic per abilitare Advanced Threat Protection e quindi fare clic sulla scheda **Individuazione dati e classificazione (anteprima)**.

   ![Eseguire l'analisi di un database](./media/sql-data-discovery-and-classification/data_classification.png) 

3. Nella scheda **Panoramica** è disponibile un riepilogo dello stato di classificazione corrente del database e un elenco dettagliato di tutte le colonne classificate, che è possibile anche filtrare in modo da visualizzare solo parti dello schema, tipi di informazioni o etichette specifiche. Se non è ancora stata classificata alcuna colonna, [andare al passaggio 5](#step-5).

   ![Riepilogo dello stato corrente di classificazione](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png) 

4. Per scaricare un report in formato Excel, fare clic sull'opzione **Esporta** nel menu superiore della finestra.

   ![Eseguire l'esportazione in Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png) 

5.  <a id="step-5"></a>Per iniziare la classificazione dei dati, fare clic sulla scheda **Classificazione** nella parte superiore della finestra.

    ![Classificare i dati](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png) 

6. Il motore di classificazione esegue l'analisi del database per identificare eventuali colonne contenenti dati potenzialmente sensibili e fornisce un elenco di **classificazioni di colonna consigliate**. Per visualizzare e applicare i suggerimenti di classificazione:

    * Per visualizzare l'elenco delle classificazioni di colonna consigliate, fare clic sul pannello dei suggerimenti nella parte inferiore della finestra:
    
      ![Classificare i dati](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png) 

    * Esaminare l'elenco di suggerimenti: per accettare un suggerimento per una determinata colonna, selezionare la casella di controllo nella colonna sinistra della riga corrispondente. È possibile anche contrassegnare *tutti i suggerimenti* come accettati selezionando la casella di controllo nell'intestazione della tabella dei suggerimenti.

       ![Esaminare l'elenco degli elementi consigliati](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png) 

    * Per applicare i suggerimenti selezionati, fare clic sul pulsante blu **Accept selected recommendations** (Accetta suggerimenti selezionati).

      ![Applicare le raccomandazioni](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png) 

7. Come alternativa o in aggiunta alla classificazione basata sui suggerimenti, è possibile anche **classificare manualmente** le colonne:

    * Fare clic su **Aggiungi classificazione** nel menu superiore della finestra.
  
      ![Aggiungere manualmente la classificazione](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png) 

    * Nella finestra di contesto visualizzata, selezionare lo schema, la tabella e la colonna che si vuole classificare, oltre al tipo di informazioni e all'etichetta di sensibilità. Fare clic sul pulsante blu **Aggiungi classificazione** nella parte inferiore della finestra di contesto.

      ![Selezionare una colonna da classificare](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png) 

8. Per completare la classificazione ed etichettare in modo permanente le colonne del database con i nuovi metadati di classificazione, fare clic su **Salva** nel menu superiore della finestra.

   ![Salva](./media/sql-data-discovery-and-classification/10_data_classification_save.png) 

## <a id="subheading-3"></a>Controllo dell'accesso ai dati sensibili

Un aspetto importante del paradigma di Information Protection è la possibilità di monitorare l'accesso ai dati sensibili. Il [servizio di controllo del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) è stato aggiornato e nel log di controllo è stato aggiunto un nuovo campo denominato *data_sensitivity_information*, in cui vengono registrate le classificazioni (etichette) di sensibilità dei dati effettivi restituiti dalla query.

![Log di controllo](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png) 

## <a id="subheading-4"></a>Passaggi successivi

- Per altre informazioni, vedere [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) (Protezione avanzata dalle minacce SQL).
- Valutare l'opportunità di configurare [il servizio di controllo del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) per il monitoraggio e il controllo dell'accesso ai dati sensibili classificati.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4