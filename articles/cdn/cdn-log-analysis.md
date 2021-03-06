---
title: Analizzare i modelli di utilizzo della rete CDN di Azure | Microsoft Docs
description: Questo articolo descrive i diversi tipi di report di analisi disponibili per i prodotti della rete CDN di Azure.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: rli; v-deasim
ms.openlocfilehash: 61fbe6e29df787048a9694138d3c9095f5cba76b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analizzare i modelli di utilizzo della rete CDN di Azure

Dopo aver abilitato la rete CDN per l'applicazione, è possibile monitorare l'uso della rete CDN, controllare l'integrità della distribuzione e risolvere i potenziali problemi. La rete CDN di Azure fornisce queste funzionalità nei modi seguenti: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Analisi principale tramite i log di diagnostica di Azure

Le funzionalità di analisi di base sono disponibili per tutti gli endpoint di rete CDN per tutti i piani tariffari. I log di diagnostica di Azure consentono di esportare l'analisi principale in archiviazione di Azure, negli hub eventi o in Log Analytics di Azure. Log Analytics di Azure offre una soluzione con grafici che sono configurabili e personalizzabili dall'utente. Per altre informazioni sui log di diagnostica di Azure, vedere [Log di diagnostica di Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Report principali da Verizon

Come utente di rete CDN di Azure con un profilo di **rete CDN Standard di Azure con tecnologia Verizon** o di **rete CDN Premium di Azure con tecnologia Verizon**, è possibile visualizzare i report principali da Verizon nel portale supplementare di Verizon. I report principali da Verizon offrono un'ampia gamma di grafici e visualizzazioni e sono accessibili tramite l'opzione **Gestisci** dal portale di Azure. Per altre informazioni, vedere [Report principali da Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Report personalizzati da Verizon

Come utente di rete CDN di Azure con un profilo di **rete CDN Standard di Azure con tecnologia Verizon** o di **rete CDN Premium di Azure con tecnologia Verizon**, è possibile visualizzare i report personalizzati da Verizon nel portale supplementare di Verizon. I report personalizzati da Verizon sono accessibili tramite l'opzione **Gestisci** dal portale di Azure. La pagina dei report personalizzati da Verizon riporta il numero di riscontri o di dati trasferiti per ogni CNAME periferico appartenente a un profilo di rete CDN di Azure. I dati possono essere raggruppati per stato della cache o codice di risposta HTTP in qualsiasi periodo di tempo. Per altre informazioni, vedere [Custom Reports from Verizon](cdn-verizon-custom-reports.md) (Report personalizzati da Verizon).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Report di rete CDN Premium di Azure con tecnologia Verizon

Con **Rete CDN Premium di Azure fornita da Verizon** è possibile accedere ai seguenti report:
   * [Report HTTP avanzati](cdn-advanced-http-reports.md)
   * [Statistiche in tempo reale](cdn-real-time-stats.md)
   * [Prestazioni del nodo perimetrale](cdn-edge-performance.md)

