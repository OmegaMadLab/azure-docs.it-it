---
title: Creare e gestire gruppi di azione nel portale di Azure | Microsoft Docs
description: Informazioni su come creare e gestire gruppi di azione nel portale di Azure.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: dukek
ms.openlocfilehash: 07e3c1a95aa223121117f3deba0269fb6cc280c2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170377"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creare e gestire gruppi di azione nel portale di Azure
## <a name="overview"></a>Panoramica ##
Questo articolo illustra come creare e gestire gruppi di azione nel portale di Azure.

I gruppi di azione consentono di configurare un elenco di azioni. Questi gruppi possono quindi essere usati da ogni avviso che viene definito, assicurandosi che vengono eseguite le stesse azioni ogni volta che viene generato un avviso.

Ogni azione è composta dalle seguenti proprietà:

* **Nome:** un identificatore univoco all'interno del gruppo di azione.  
* **Tipo di azione**: inviare una chiamata vocale o un SMS, inviare un messaggio di posta elettronica, chiamare un webhook, inviare dati a uno strumento ITSM, chiamare un'app per la logica, inviare una notifica push all'app Azure o eseguire un runbook di Automazione.
* **Dettagli**: l'URI del webhook, il numero di telefono o l'indirizzo di posta elettronica oppure le informazioni di connessione ITSM corrispondenti.

Per informazioni sull'uso dei modelli di Azure Resource Manager per configurare i gruppi di azione: [Modelli di Resource Manager per il gruppo di azione](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Creare un gruppo di azione usando il portale di Azure ##
1. Nel [portale](https://portal.azure.com)selezionare **Monitoraggio**. Il pannello **Monitoraggio** consolida tutte le impostazioni e i dati di monitoraggio in una vista.

    ![Servizio "Monitoraggio"](./media/monitoring-action-groups/home-monitor.png)
2. Nella sezione **Impostazioni** selezionare **Gruppi di azioni**.

    ![Scheda "Gruppi di azione"](./media/monitoring-action-groups/action-groups-blade.png)
3. Selezionare **Aggiungi gruppo di azione** e compilare i campi.

    ![Comando "Aggiungi gruppo di azione"](./media/monitoring-action-groups/add-action-group.png)
4. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.

      ![Finestra di dialogo "Aggiungi gruppo di azione"](./media/monitoring-action-groups/action-group-define.png)

5. Nella casella **Sottoscrizione** viene inserita automaticamente la sottoscrizione corrente. Il gruppo di azione verrà salvato in questa sottoscrizione.

6. Selezionare il **gruppo di risorse** in cui verrà salvato il gruppo di azione.

7. Definire un elenco di azioni fornendo i dati di ogni azione:

    a. **Nome**: immettere un identificatore univoco per questa azione.

    b. **Tipo di azione**: selezionare Massaggio di posta elettronica/SMS/Push/Voce, App per la logica, Webhook, ITSM o Runbook di Automazione.

    c. **Dettagli**: in base al tipo di azione, immettere un numero di telefono, un indirizzo di posta elettronica, l'URI del webhook, l'app Azure, la connessione ITSM o il runbook di Automazione. Per l'azione ITSM, specificare anche **Elemento di lavoro** e altri campi richiesti dallo strumento ITSM.

8. Fare clic su **OK** per creare il gruppo di azione.

## <a name="action-specific-information"></a>Informazioni specifiche delle azioni
<dl>
<dt>Push dell'app Azure</dt>
<dd>Un gruppo di azioni può contenere un massimo di 10 azioni dell'app Azure.</dd>
<dd>In questo momento l'azione dell'app Azure supporta solo gli avvisi ServiceHealth. Qualsiasi altro tipo di avviso verrà ignorato. Fare riferimento alle informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica di integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md).</dd>

<dt>
Messaggio di posta elettronica</dt>
<dd>Un gruppo di azioni può contenere un massimo di 50 azioni di tipo Massaggio di posta elettronica.</dd>
<dd>Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./monitoring-alerts-rate-limiting.md).</dd>

<dt>ITSM</dt>
<dd>Un gruppo di azioni può contenere un massimo di 10 azioni di tipo ITSM.</dd>
<dd>L'azione ITSM richiede una connessione ITSM. Informazioni su come creare una [connessione ITSM](../log-analytics/log-analytics-itsmc-overview.md).</dd>

<dt>
App per la logica</dt>
<dd>Un gruppo di azioni può contenere un massimo di 10 azioni di tipo App per la logica.</dd>

<dt>Runbook</dt>
<dd>Un gruppo di azioni può contenere un massimo di 10 azioni di tipo Runbook.</dd>

<dt>SMS</dt>
<dd>Un gruppo di azioni può contenere un massimo di 10 azioni di tipo SMS.</dd>
<dd>Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./monitoring-alerts-rate-limiting.md).</dd>
<dd>Vedere l'articolo relativo al [comportamento degli avvisi SMS](monitoring-sms-alert-behavior.md).</dd>

<dt>
Voce</dt>
<dd>Un gruppo di azioni può contenere un massimo di 10 azioni di tipo Voce.</dd>
<dd>Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./monitoring-alerts-rate-limiting.md).</dd>

<dt>Webhook</dt>
<dd>Un gruppo di azioni può contenere un massimo di 10 azioni di tipo Webhook.
<dd>Logica di ripetizione dei tentativi: il periodo di timeout per una risposta è 10 secondi. Verrà eseguito un massimo di 2 nuovi tentativi di chiamata webhook quando vengono restituiti i codici di stato HTTP 408, 429, 503, 504 o l'endpoint HTTP non risponde. La prima ripetizione del tentativo avviene dopo 10 secondi. La seconda e ultima ripetizione avviene dopo 100 secondi.</dd>
</dl>

## <a name="manage-your-action-groups"></a>Gestire i gruppi di azione ##
Dopo la creazione, il gruppo di azione sarà visibile nella sezione **Gruppi di azione** del pannello **Monitoraggio**. Selezionare il gruppo di azione da gestire per:

* Aggiungere, modificare o rimuovere azioni.
* Eliminare il gruppo di azione.

## <a name="next-steps"></a>Passaggi successivi ##
* Altre informazioni sul [Comportamento degli avvisi SMS](monitoring-sms-alert-behavior.md).  
* Leggere le [informazioni sullo schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md).  
* Altre informazioni sul [connettore ITSM](../log-analytics/log-analytics-itsmc-overview.md)
* Altre informazioni sulla [limitazione della frequenza](monitoring-alerts-rate-limiting.md) degli avvisi.
* Leggere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md).
