---
title: Continuità aziendale e ripristino di emergenza nelle aree abbinate di Azure | Microsoft Docs
description: Informazioni sulle coppie di aree di Azure per assicurare la resilienza delle applicazioni in caso di errori del data center.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: multiple
ms.topic: article
ms.date: 05/09/2018
ms.author: raynew
ms.openlocfilehash: e2c288af881fa925c1680efdb0f86deec60b7510
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302679"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure

## <a name="what-are-paired-regions"></a>Definizione di aree abbinate

Azure è disponibile in più aree geografiche del mondo. Un'area geografica di Azure è un'area definita del mondo che include almeno un'area di Azure. Un'area di Azure all'interno di un'area geografica include uno o più data center.

Ogni area di Azure è associata a un'altra area con la stessa ubicazione geografica, che insieme formano una coppia di aree. L'eccezione è il Brasile meridionale che è associato a un'area fuori dalla relativa ubicazione geografica.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figura 1: coppie di aree di Azure

| Area geografica | Aree abbinate |  |
|:--- |:--- |:--- |
| Asia |Asia orientale |Asia sudorientale |
| Australia |Australia orientale |Australia sudorientale |
| Australia |Australia centrale |Australia centrale (2) |
| Brasile |Brasile meridionale (2) |Stati Uniti centro-meridionali |
| Canada |Canada centrale |Canada orientale |
| Cina |Cina settentrionale |Cina orientale|
| Europa |Europa settentrionale |Europa occidentale |
| Germania |Germania centrale |Germania nord-orientale |
| India |India centrale |India meridionale |
| India |India occidentale (1) |India meridionale |
| Giappone |Giappone orientale |Giappone occidentale |
| Corea del Sud |Corea del Sud centrale |Corea del Sud meridionale |
| America del Nord |Stati Uniti orientali |Stati Uniti occidentali |
| America del Nord |Stati Uniti orientali 2 |Stati Uniti centrali |
| America del Nord |Stati Uniti centro-settentrionali |Stati Uniti centro-meridionali |
| America del Nord |Stati Uniti occidentali 2 |Stati Uniti centro-occidentali 
| Regno Unito |Regno Unito occidentale |Regno Unito meridionale |
| Dipartimento della difesa degli Stati Uniti |Dipartimento della difesa Stati Uniti orientali |Dipartimento della difesa Stati Uniti centrali |
| Governo degli Stati Uniti |Governo degli Stati Uniti - Arizona |Governo degli Stati Uniti - Texas |
| Governo degli Stati Uniti |US Gov Iowa (3) |US Gov Virginia |
| Governo degli Stati Uniti |US Gov Virginia (4) |Governo degli Stati Uniti - Texas |

Tabella 1 - Mapping di coppie di aree di Azure

- (1) L'area India occidentale è diversa perché è associata a un'altra area in una sola direzione. L'area secondaria dell'area India occidentale è India meridionale, mentre l'area secondaria dell'India meridionale è India centrale.
- (2) L'area Brasile meridionale è unica, perché è abbinata a un'area esterna alla propria area geografica. L'area secondaria del Brasile meridionale sono gli Stati Uniti centro-meridionali, ma l'area secondaria degli Stati Uniti centro-meridionali non è il Brasile meridionale.
- (3) L'area secondaria per US Gov Iowa è US Gov Virginia, ma l'area secondaria per US Gov Virginia non è US Gov Iowa.
- (4) L'area secondaria per US Gov Virginia è US Gov Texas, ma l'area secondaria per US Gov Texas non è US Gov Virginia.


È consigliabile replicare i carichi di lavoro tra le coppie di aree per sfruttare i vantaggi dei criteri di isolamento e disponibilità di Azure. Ad esempio, gli aggiornamenti di sistema di Azure pianificati vengono distribuiti in sequenza (non contemporaneamente) tra le aree abbinate. Ciò significa che anche nel raro caso di un aggiornamento non corretto, non saranno interessate contemporaneamente entrambe le aree. Inoltre, nell'improbabile caso di un'interruzione su vasta scala, viene data priorità al ripristino di almeno un'area di ogni coppia.

## <a name="an-example-of-paired-regions"></a>Esempio di aree abbinate
La Figura 2 mostra un'ipotetica applicazione che utilizza la coppia internazionali per il ripristino di emergenza. I numeri in verde evidenziano le attività tra aree di tre servizi di Azure (calcolo, archiviazione e database di Azure) e come vengono configurate per la replica tra aree. I vantaggi esclusivi della distribuzione tra aree abbinate sono evidenziali dai numeri in arancione.

![Panoramica dei vantaggi delle aree abbinate](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2: ipotetica coppia di aree di Azure

## <a name="cross-region-activities"></a>Attività tra aree
Come indicato nella figura 2.

![PaaS](./media/best-practices-availability-paired-regions/1Green.png) **Calcolo di Azure (PaaS)**: è necessario eseguire anticipatamente il provisioning di risorse di calcolo aggiuntive per assicurare che siano disponibili in un'altra area in caso di emergenza. Per altre informazioni, vedere [Informazioni tecniche sulla resilienza di Azure](resiliency/resiliency-technical-guidance.md).

![Archiviazione](./media/best-practices-availability-paired-regions/2Green.png) **Archiviazione di Azure**: per impostazione predefinita, quando si crea un account di archiviazione di Azure viene configurata l'archiviazione con ridondanza geografica. Con questa opzione di replica, i dati vengono replicati per tre volte all'interno dell'area primaria e per tre volte nell'area abbinata. Per altre informazioni, vedere [Opzioni di ridondanza di Archiviazione di Azure](storage/common/storage-redundancy.md).

![SQL Azure](./media/best-practices-availability-paired-regions/3Green.png) **Database SQL Azure**: con la replica geografica Standard di SQL Azure è possibile configurare la replica asincrona delle transazioni in un'area associata. Con la replica geografica Premium è possibile configurare la replica per tutte le aree del mondo, tuttavia è consigliabile distribuire queste risorse in un'area abbinata per la maggior parte degli scenari di ripristino di emergenza. Per altre informazioni, vedere l'articolo relativo alla [replica geografica nel database SQL di Azure](sql-database/sql-database-geo-replication-overview.md).

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png)**Azure Resource Manager**: Resource Manager fornisce implicitamente l'isolamento logico dei componenti di gestione del servizio tra le aree. In questo modo, è meno probabile che gli errori logici in un'area abbiano un impatto su un'altra.

## <a name="benefits-of-paired-regions"></a>Vantaggi delle aree abbinate
Come indicato nella figura 2.  

![Isolamento](./media/best-practices-availability-paired-regions/5Orange.png)
**Isolamento fisico**: quando possibile, per Azure è preferibile una separazione di almeno 480 km tra i data center di una coppia di aree, anche se ciò non è sempre pratico o possibile in tutte le aree geografiche. La separazione dei data center fisici riduce la possibilità che calamità naturali, agitazioni sociali, interruzioni dell'alimentazione o interruzioni della rete fisica interessino entrambe le aree contemporaneamente. L'isolamento è soggetto ai vincoli presenti all'interno dell'area geografica (dimensioni dell'area geografica, disponibilità dell'infrastruttura di rete/alimentazione, normative e così via).  

![Replica](./media/best-practices-availability-paired-regions/6Orange.png)
**Replica fornita dalla piattaforma**: alcuni servizi, ad esempio l'archiviazione con ridondanza geografica, offrono la replica automatica nell'area abbinata.

![Ripristino](./media/best-practices-availability-paired-regions/7Orange.png)
**Ordine di ripristino dell'area**: nel caso di un'interruzione su vasta scala, viene definita la priorità di ripristino di un'area per ogni coppia. Per le applicazioni distribuite in aree abbinate viene garantito che una delle aree sarà ripristinata con priorità. Se un'applicazione viene distribuita in aree non abbinate, il ripristino potrebbe essere ritardato: nel peggiore dei casi le aree scelte potrebbero essere le ultime due a essere ripristinate.

![Aggiornamenti](./media/best-practices-availability-paired-regions/8Orange.png)
**Aggiornamenti sequenziali**: gli aggiornamenti di sistema di Azure pianificati vengono implementati nelle aree abbinate in modo sequenziale (non contemporaneamente) per ridurre al minimo i tempi di inattività, l'effetto dei bug e gli errori logici nel raro caso di un aggiornamento non valido.

![Dati](./media/best-practices-availability-paired-regions/9Orange.png)
 **Residenza dei dati**: un'area si trova all'interno della stessa geografia della propria coppia (a eccezione del Brasile meridionale) per soddisfare i requisiti di residenza dei dati ai fini della giurisdizione per le imposizioni fiscali e normative.
