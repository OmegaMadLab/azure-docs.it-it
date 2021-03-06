---
title: Gestire un account Azure Cosmos DB con il portale di Azure | Microsoft Docs
description: Informazioni su come gestire il proprio account Azure Cosmos DB tramite il portale di Azure. Trovare una guida sull'uso del portale di Azure per visualizzare, copiare, eliminare e accedere agli account.
keywords: Portale di Azure, azure, Microsoft Azure
services: cosmos-db
documentationcenter: ''
author: kirillg
manager: kfile
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: kirillg
ms.openlocfilehash: 9c8126c7f902beec348419c0362722a692cb393e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Come gestire un account Azure Cosmos DB
Informazioni su come impostare la coerenza globale, usare le chiavi ed eliminare un account Azure Cosmos DB nel portale di Azure.

## <a id="consistency"></a>Gestire le impostazioni di coerenza di Azure Cosmos DB
La scelta del livello di coerenza giusto dipende dalla semantica dell'applicazione. Acquisire familiarità con i livelli di coerenza disponibili in Azure Cosmos DB leggendo [Uso dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni di Azure Cosmos DB][consistency]. Azure Cosmos DB offre garanzia su coerenza, disponibilità e prestazioni a ogni livello di coerenza disponibile per l'account di database. La configurazione dell'account di database con un forte livello di coerenza richiede che i dati vengano limitati a una singola area di Azure, senza disponibilità globale. D'altra parte, i livelli di coerenza ampi (obsolescenza associata, sessione o finale) consentono di associare tutte le aree di Azure desiderate con l'account di database. La semplice procedura seguente mostra come selezionare il livello di coerenza predefinito per l'account di database.

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Per specificare la coerenza predefinita per un account Azure Cosmos DB
1. Nel [portale di Azure](https://portal.azure.com/) accedere all'account Azure Cosmos DB.
2. Nella pagina dell'account fare clic su **Coerenza predefinita**.
3. Nella pagina **Coerenza predefinita** selezionare il nuovo livello di coerenza e fare clic su **Salva**.
    ![Sessione coerenza predefinita][5]

## <a id="keys"></a>Visualizzare, copiare e rigenerare le chiavi di accesso e le password
Quando si crea un account Azure Cosmos DB, il servizio genera due chiavi di accesso principali (o due password per gli account di API MongoDB) che possono essere usate per l'autenticazione quando si accede all'account di Azure Cosmos DB. Generando due chiavi di accesso, Azure Cosmos DB consente di rigenerare le chiavi senza interruzioni dell'account Azure Cosmos DB. 

Nel [Portale di Azure](https://portal.azure.com/) accedere alla pagina **Chiavi** dal menu delle risorse nella pagina **Account Azure Cosmos DB** per visualizzare, copiare e rigenerare le chiavi di accesso usate per accedere all'account Azure Cosmos DB. Per gli account di API MongoDB, accedere alla pagina **Stringa di connessione** dal menu delle risorse per visualizzare, copiare e rigenerare le password usate per accedere all'account.

![Schermata del portale di Azure, pagina delle chiavi](./media/manage-account/keys.png)

> [!NOTE]
> La pagina **Chiavi** include anche le stringhe di connessione primaria e secondaria che possono essere usate per connettersi al proprio account dall' [Utilità di migrazione dati](import-data.md).
> 
> 

In questa pagina sono anche disponibili chiavi di sola lettura. Lettura e query sono operazioni di sola lettura, a differenza di creazione, eliminazione e sostituzione.

### <a name="copy-an-access-key-or-password-in-the-azure-portal"></a>Copiare una chiave di accesso o una password nel portale di Azure
Nella pagina **Chiavi** (o nella pagina **Stringa di connessione** per gli account di API MongoDB) fare clic sul pulsante **Copia** a destra della chiave o della password da copiare.

![Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys-and-passwords"></a>Rigenerare le chiavi di accesso e le password
È consigliabile modificare periodicamente le chiavi di accesso (e le password per gli account di API MongoDB) all'account Azure Cosmos DB per mantenere più sicure le connessioni. Vengono assegnate due chiavi di accesso o password per consentire di mantenere le connessioni all'account Azure Cosmos DB con una delle due chiavi mentre si rigenera l'altra.

> [!WARNING]
> La rigenerazione delle chiavi di accesso influisce su tutte le applicazioni che dipendono dalla chiave corrente. Per usare la nuova chiave è necessario aggiornare tutti i client che usano la chiave di accesso per accedere all'account Azure Cosmos DB.
> 
> 

Se si dispone di applicazioni o servizi cloud che usano l'account Azure Cosmos DB e si rigenerano le chiavi, si perderanno le connessioni, a meno che non si registrino le chiavi. I passaggi seguenti illustrano il processo necessario per la registrazione delle chiavi o delle password.

1. Aggiornare la chiave di accesso nel codice dell'applicazione in modo che faccia riferimento alla chiave di accesso secondaria dell'account Azure Cosmos DB.
2. Rigenerare la chiave di accesso primaria per l'account Azure Cosmos DB. Nel [portale di Azure](https://portal.azure.com/) accedere all'account Azure Cosmos DB.
3. Nella pagina **Account Azure Cosmos DB** fare clic su **Chiavi** (o **Stringa di connessione** per gli account MongoDB\*\*).
4. Nella pagina **Chiavi**/**Stringa di connessione** fare clic sul comando per rigenerare e quindi su **OK** per confermare che si vuole generare una nuova chiave.
    ![Per rigenerare le chiavi di accesso](./media/manage-account/regenerate-keys.png)
5. Dopo aver verificato che la nuova chiave sia disponibile per l'utilizzo (circa cinque minuti dopo la rigenerazione), aggiornare la chiave di accesso nel codice dell'applicazione in modo che faccia riferimento alla nuova chiave di accesso primaria.
6. Rigenerare la chiave di accesso secondaria.
   
    ![Per rigenerare le chiavi di accesso](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Potrebbero trascorrere diversi minuti prima che una chiave appena generata possa essere usata per accedere all'account Azure Cosmos DB.
> 
> 

## <a name="get-the-connection-string"></a>Ottenere la stringa di connessione
Per recuperare la stringa di connessione, eseguire le operazioni seguenti: 

1. Nel [portale di Azure](https://portal.azure.com) accedere all'account Azure Cosmos DB.
2. Nel menu delle risorse fare clic su **Chiavi** (o **Stringa di connessione** per gli account di API MongoDB).
3. Fare clic sul pulsante **Copy** (Copia) accanto alla casella **Primary Connection String** (Stringa di connessione primaria) o **Secondary Connection String** (stringa di connessione secondaria). 

Se si usa la stringa di connessione nello [strumento di migrazione del database di Azure Cosmos DB](import-data.md), aggiungere il nome del database alla fine della stringa di connessione. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a> Eliminare un account Azure Cosmos DB
Per rimuovere un account Azure Cosmos DB non più usato dal portale di Azure, fare clic con il pulsante destro del mouse sul nome dell'account e quindi su **Elimina account**.

![Come eliminare un account Azure Cosmos DB nel portale di Azure](./media/manage-account/deleteaccount.png)

1. Nel [portale di Azure](https://portal.azure.com/)accedere all'account Azure Cosmos DB che si desidera eliminare.
2. Nella pagina **Account Azure Cosmos DB** fare clic con il pulsante destro del mouse sull'account e quindi su **Elimina account**. 
3. Nella pagina di conferma risultante digitare il nome dell'account Azure Cosmos DB per confermarne l'eliminazione.
4. Fare clic sul pulsante **Elimina**.

![Come eliminare un account Azure Cosmos DB nel portale di Azure](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>Passaggi successivi
Informazioni su come [iniziare a usare l'account Azure Cosmos DB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
