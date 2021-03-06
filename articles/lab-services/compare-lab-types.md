---
title: Confrontare tipi diversi di lab in Azure Lab Services | Microsoft Docs
description: Descrive e confronta i diversi tipi di lab che è possibile creare usando Azure Lab Services (in precedenza DevTest Labs).
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 22a1c90dd1a1ca305431d91a801e5293a6d08703
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361183"
---
# <a name="compare-managed-and-devtest-labs-in-azure-lab-services"></a>Confrontare lab gestiti e DevTest Labs in Azure Lab Services
È possibile creare due tipi di lab, **lab gestiti** con Azure Lab Services e **lab personalizzati** con Azure DevTest Labs. Se si vuole semplicemente indicare le caratteristiche richieste per il lab e consentire al servizio di configurare e gestire l'infrastruttura necessaria per il lab, scegliere uno dei **lab gestiti**. Attualmente il **lab per le classi** è l'unico tipo di lab gestito che è possibile creare con Azure Lab Services. Se si vuole gestire la propria infrastruttura, creare un **lab personalizzato** tramite Azure DevTest Labs.

Le sezioni seguenti includono altre informazioni dettagliate su queste opzioni. 

## <a name="managed-labs"></a>Lab gestiti
I lab gestiti includono diversi tipi di lab per soddisfare esigenze specifiche. Attualmente Azure Lab Services supporta solo il **lab per le classi** come lab gestito. I lab gestiti consentono di iniziare subito, con una configurazione minima. Il servizio controlla completamente la gestione dell'infrastruttura per il lab, dall'attivazione delle macchine virtuali alla gestione degli errori e alla scalabilità dell'infrastruttura. Per creare un lab gestito, è necessario creare innanzitutto un account del lab per l'organizzazione. L'account del lab funge da account centrale in cui vengono gestiti tutti i lab dell'organizzazione. 

Quando si creano e si usano risorse di Azure in questi lab gestiti, il servizio crea e gestisce le risorse nelle sottoscrizioni interne di Microsoft. Le risorse non vengono create nella sottoscrizione di Azure. Il servizio tiene traccia dell'utilizzo di tali risorse nelle sottoscrizioni interne di Microsoft. Il costo di tale utilizzo viene addebitato alla sottoscrizione di Azure che contiene l'account del lab.   

Ecco alcuni **casi d'uso per i lab gestiti**: 

- Garantire agli studenti un lab di macchine virtuali configurate con tutto il necessario per una classe. Assegnare a ogni studente un numero limitato di ore per l'uso delle macchine virtuali per compiti scolastici o progetti personali.
- Configurare un pool di macchine virtuali di calcolo a prestazioni elevate per eseguire ricerche con elevato utilizzo di calcolo o di grafica. Eseguire le macchine virtuali in base alle esigenze e al termine dell'operazione pulire i computer. 
- Spostare il lab del computer fisico della scuola nel cloud. Ridimensionare automaticamente il numero di macchine virtuali in base all'utilizzo massimo e alla soglia di costo impostati nel lab.  
- Eseguire rapidamente il provisioning di un lab di macchine virtuali per ospitare un hackathon. Al termine dell'operazione, eliminare il lab con un solo clic. 


## <a name="devtest-labs"></a>DevTest Labs
In alcuni scenari è possibile gestire autonomamente l'infrastruttura e la configurazione, all'interno della propria sottoscrizione. A tale scopo, creare un lab personalizzato in Azure DevTest Labs nel portale di Azure. Per questi lab non è necessario creare un account del lab. Tali lab non vengono visualizzati nell'account del lab, che è destinato ai lab gestiti.  

Ecco alcuni **casi d'uso per DevTest Labs**: 

- Eseguire rapidamente il provisioning di un lab di macchine virtuali per ospitare un hackathon o una sessione pratica durante una conferenza. Al termine dell'operazione, eliminare il lab con un solo clic. 
- Creare un pool di macchine virtuali configurate con l'applicazione e consentire al team di usare facilmente una macchina virtuale per i bug bash.  
- Garantire agli sviluppatori macchine virtuali configurate con tutti gli strumenti necessari. Pianificare l'avvio e l'arresto automatico per ridurre i costi. 
- Creare ripetutamente un lab di computer di test come parte della distribuzione. Testare i bit più recenti e pulire il computer di test al termine dell'operazione. 
- Impostare una serie di macchine virtuali configurate in modo diverso e più agenti di test per i test delle prestazioni e della scalabilità. 
- Offrire sessioni di training ai clienti usando un lab configurato con la versione più recente del prodotto. Assegnare a ogni cliente un numero limitato di ore per l'uso del lab. 


## <a name="managed-labs-vs-devtest-labs"></a>Confronto tra lab gestiti e DevTest Labs
La tabella seguente confronta due tipi di lab supportati da Azure Lab Services: 

| Funzionalità | Lab gestiti | DevTest Labs |
| -------- | ----------------  | ---------- |
| Gestione dell'infrastruttura di Azure nel lab. |  Gestita automaticamente dal servizio | Gestita autonomamente  |
| Resilienza predefinita per i problemi dell'infrastruttura | Gestita automaticamente dal servizio | Gestita autonomamente  |
| Gestione sottoscrizioni | Il servizio gestisce l'allocazione delle risorse all'interno delle sottoscrizioni di Microsoft che supportano il servizio. La scalabilità viene gestita automaticamente dal servizio. | Gestita autonomamente nella propria sottoscrizione di Azure. Nessuna scalabilità automatica delle sottoscrizioni. |
| Distribuzione di Azure Resource Manager nel lab | Non disponibile | Disponibile |

## <a name="next-steps"></a>Passaggi successivi
Introduzione alla configurazione di un lab usando Azure Lab Services:

- [Configurare un lab per le classi](tutorial-setup-classroom-lab.md)
- [Configurare un lab personalizzato](tutorial-create-custom-lab.md)
