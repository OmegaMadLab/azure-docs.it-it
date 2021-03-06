---
title: Installare Strumenti Azure Data Lake per Visual Studio | Microsoft Docs
description: Informazioni su come installare Strumenti Azure Data Lake per Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2018
ms.author: saveenr, yanacai
ms.openlocfilehash: 37b01c404006bbba79b185049aea6c7f77ce3c68
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887351"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Installare Data Lake Tools per Visual Studio

Informazioni su come usare Visual Studio per creare account Azure Data Lake Analytics, definire processi in [U-SQL](data-lake-analytics-u-sql-get-started.md) e inviare processi al servizio Data Lake Analytics. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>prerequisiti

* **Visual Studio**: sono supportate tutte le versioni ad eccezione della versione Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK per .NET** versione 2.7.1 o successiva.  Eseguire l'installazione usando [Installazione guidata piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
* Un account **Data Lake Analytics**. Per creare un account, vedere [Introduzione ad Azure Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Installare Strumenti Azure Data Lake per Visual Studio 2017

Strumenti Azure Data Lake per Visual Studio è supportato in Visual Studio 2017 15.3 o versioni successive. Lo strumento fa parte dei carichi di lavoro **Elaborazione ed archiviazione dati** e **Sviluppo di Azure** nel programma di installazione di Visual Studio. Abilitare uno di questi due carichi di lavoro durante l'installazione di Visual Studio.  

Abilitare il carico di lavoro **Elaborazione ed archiviazione dati** come illustrato: ![Abilitare il carico di lavoro Elaborazione ed archiviazione dati](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Abilitare il carico di lavoro **Sviluppo di Azure** come illustrato: ![Abilitare il carico di lavoro Sviluppo di Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Installare Strumenti Azure Data Lake per Visual Studio 2013 e 2015

Scaricare e installare Strumenti Azure Data Lake per Visual Studio dall'[Area download](http://aka.ms/adltoolsvs). Dopo l'installazione si noti che:
* Il nodo **Esplora server** > **Azure** contiene un nodo **Data Lake Analytics**. 
* Il menu **Strumenti** include una voce **Data Lake**.


## <a name="next-steps"></a>Passaggi successivi
* Per registrare informazioni di diagnostica, vedere [Accesso ai log di diagnostica per Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Per usare la visualizzazione esecuzioni vertici, vedere [Usare la visualizzazione esecuzioni vertici in Azure Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
