---
title: "Procedura: apertura delle porte del firewall necessarie per un'applicazione Proxy di applicazione | Microsoft Docs"
description: Individuare le porte da aprire per fare in modo che il proxy dell'applicazione Azure Active Directory funzioni correttamente
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 72acfbd21159e15fe237be6d509cb2c4a2b1bffd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Procedura: apertura delle porte del firewall necessarie per un'applicazione Proxy di applicazione

Per visualizzare un elenco completo delle porte necessarie e la funzione di ciascuna porta, vedere la sezione dei prerequisiti della [documentazione del Proxy di applicazione](manage-apps/application-proxy-enable.md). Si noti che il Proxy di applicazione usa solo le porte in uscita.

È inoltre possibile controllare se tutte le porte di cui si dispone sono aperte mediante lo [strumento di test delle porte del connettore](https://aadap-portcheck.connectorporttest.msappproxy.net/) dalla rete locale. La presenza di più segni di spunta verdi indica una maggiore resilienza. 

## <a name="app-proxy-regions"></a>Aree del proxy dell'applicazione

In futuro sarà possibile specificare quali aree devono essere verdi per l'utente. Per il momento, assicurarsi che tutte siano verdi. Anche l'area Stati Uniti centrali è necessaria indipendentemente dall'area in cui ci si trova.

Per assicurarsi che lo strumento fornisca i risultati corretti, accertarsi di:

-   Aprire lo strumento in un browser dal server in cui è installato il connettore.

-   Assicurarsi che qualsiasi proxy o firewall applicabile per il connettore venga applicato anche a questa pagina. Questa operazione può essere eseguita in Internet Explorer selezionando **Impostazioni**  - &gt; **Opzioni Internet**  - &gt; **Connessioni**  - &gt; **Impostazioni Lan**. In questa pagina viene visualizzato il campo "Usa un server di proxy per la rete LAN". Selezionare questa casella e inserire l'indirizzo del proxy nel campo "Indirizzo".

## <a name="next-steps"></a>Passaggi successivi
[Comprendere i connettori del proxy applicazione di Azure AD](manage-apps/application-proxy-connectors.md)
