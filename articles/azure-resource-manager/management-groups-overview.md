---
title: Organizzare le risorse con i gruppi di gestione di Azure | Microsoft Docs
description: Informazioni sui gruppi di gestione e sul loro uso.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2018
ms.author: rithorn
ms.openlocfilehash: 53de4afb42e9ea5b7845a9c862dc1e06c6de36df
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizzare le risorse con i gruppi di gestione di Azure 

Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. I gruppi di gestione di Azure forniscono un livello di ambito oltre le sottoscrizioni. Le sottoscrizioni sono organizzate in contenitori chiamati "gruppi di gestione" a cui vengono applicate le condizioni di governance. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente le condizioni applicate al gruppo di gestione. I gruppi di gestione offrono gestione di livello aziendale su larga scala, indipendentemente dal tipo di sottoscrizioni che si posseggono.

La funzionalità dei gruppi di gestione è disponibile come anteprima pubblica. Per iniziare a usare i gruppi di gestione, accedere al [portale di Azure](https://portal.azure.com) e cercare **Gruppi di gestione** nella sezione **Tutti i servizi**. 

Ad esempio, è possibile applicare a un gruppo di gestione criteri che limitano le aree disponibili per la creazione di macchine virtuali (VM). Questi criteri verranno applicati a tutti i gruppi di gestione, le sottoscrizioni e le risorse all'interno del gruppo, consentendo la creazione di macchine virtuali esclusivamente in tale area.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Gerarchia di gruppi di gestione e sottoscrizioni 

È possibile creare una struttura flessibile di gruppi di gestione e sottoscrizioni, in modo da organizzare le risorse in una gerarchia per la gestione unificata di accesso e criteri. Il diagramma seguente mostra una gerarchia di esempio costituita da gruppi di gestione e sottoscrizioni organizzati in base ai reparti.    

![albero](media/management-groups/MG_overview.png)

Tramite la creazione di una gerarchia raggruppata per reparti, si è in grado di assegnare ruoli [Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md) che vengono *ereditati* dai reparti sottostanti al gruppo di gestione. Usando i gruppi di gestione è possibile ridurre il carico di lavoro e il rischio di errori, grazie alla possibilità di assegnare il ruolo una sola volta. 

### <a name="important-facts-about-management-groups"></a>Informazioni importanti sui gruppi di gestione
- Una singola directory può supportare 10.000 gruppi di gestione. 
- L'albero di un gruppo di gestione può supportare fino a sei livelli di profondità.
    - Questo limite non include il livello radice o il livello sottoscrizione.
- Ogni gruppo di gestione può supportare un solo elemento padre.
- Ogni gruppo di gestione può avere più elementi figlio. 

### <a name="preview-subscription-visibility-limitation"></a>Limitazione della visibilità delle sottoscrizioni nell'anteprima 
Attualmente esiste una limitazione nell'anteprima che non consente di visualizzare le sottoscrizioni per cui è stato ereditato l'accesso. L'accesso alla sottoscrizione è stato ereditato, ma Azure Resource Manager non è ancora in grado di riconoscere questo tipo di accesso.  

Se si usa l'API REST per ottenere informazioni sulla sottoscrizione, vengono restituiti i dettagli poiché in realtà si dispone dell'accesso, ma nel portale di Azure e in Azure PowerShell le sottoscrizioni non vengono visualizzate. 

Questa limitazione è attualmente in fase di analisi e verrà risolta prima dell'annuncio della "Disponibilità generale" dei gruppi di gestione.  

### <a name="cloud-solution-providercsp-limitation-during-preview"></a>Limitazione per Provider di soluzioni cloud (CSP) nell'anteprima 
Attualmente esiste una limitazione per i partner Provider di soluzioni cloud (CSP) a causa della quale non sono in grado di creare o gestire gruppi di gestione dei clienti all'interno della directory dei clienti.  
Questa limitazione è attualmente in fase di analisi e verrà risolta prima dell'annuncio della "Disponibilità generale" dei gruppi di gestione.


## <a name="root-management-group-for-each-directory"></a>Gruppo di gestione radice per ogni directory

A ogni directory viene assegnato un gruppo di gestione principale denominato gruppo di gestione "radice". Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo di gestione radice consente l'applicazione di criteri globali e assegnazioni di Controllo degli accessi in base al ruolo a livello di directory. Inizialmente l'[Amministratore directory deve elevare se stesso](../role-based-access-control/elevate-access-global-admin.md) al ruolo di proprietario del gruppo radice. Una diventato proprietario del gruppo, l'amministratore può assegnare qualsiasi ruolo Controllo degli accessi in base al ruolo ad altri utenti della directory o gruppi per gestire la gerarchia.  

### <a name="important-facts-about-the-root-management-group"></a>Informazioni importanti sul gruppo di gestione radice
- Il nome e l'ID del gruppo di gestione radice corrispondono per impostazione predefinita all'ID di Azure Active Directory. Il nome visualizzato può essere aggiornato in qualsiasi momento in modo che appaia in modo diverso all'interno del portale di Azure. 
- Tutte le sottoscrizioni e i gruppi di gestione possono essere ricondotti all'unico gruppo di gestione radice all'interno della directory.  
    - È consigliabile ricondurre tutti gli elementi nella directory al gruppo di gestione radice ai fini della gestione globale.  
    - Durante l'anteprima pubblica, le sottoscrizioni all'interno della directory non vengono rese automaticamente elementi figlio del gruppo radice.   
    - Durante l'anteprima pubblica, le nuove sottoscrizioni non vengono inserite automaticamente nel gruppo di gestione radice. 
- A differenza degli altri gruppi di gestione, il gruppo di gestione radice non può essere spostato o eliminato. 
  
## <a name="management-group-access"></a>Accesso ai gruppi di gestione

I gruppi di gestione di Azure supportano il [Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md) per tutte le definizioni di ruolo e gli accessi alle risorse. Queste autorizzazioni vengono ereditate dalle risorse figlio presenti nella gerarchia.   

Anche se è possibile assegnare qualsiasi [ruolo Controllo degli accessi in base al ruolo predefinito](../role-based-access-control/overview.md#built-in-roles) a un gruppo di gestione, sono disponibili quattro ruoli di uso comune: 
- **Proprietario** ha accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. 
- **Collaboratore** può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri.
- **Collaboratore ai criteri delle risorse** può creare e gestire i criteri nella directory sulle risorse.     
- **Lettore** può visualizzare le risorse di Azure esistenti. 


## <a name="next-steps"></a>Passaggi successivi 
Per altre informazioni sui gruppi di gestione, vedere: 
- [Creare gruppi di gestione per organizzare le risorse di Azure](management-groups-create.md)
- [Come modificare, eliminare o gestire i gruppi di gestione](management-groups-manage.md)
- [Installare il modulo Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Esaminare la specifica di dell'API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Installare l'estensione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

