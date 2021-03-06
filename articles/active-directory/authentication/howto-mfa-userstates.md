---
title: Stati utente in Microsoft Azure Multi-Factor Authentication
description: Informazioni sugli stati utente in Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: b809097e50a17178da12fdb424eba08dc8e0c4cb
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Come richiedere la verifica in due passaggi per un utente o un gruppo

Sono disponibili due modi per richiedere la verifica in due passaggi. Il primo prevede l'abilitazione di ogni utente per Azure Multi-Factor Authentication (MFA). Gli utenti abilitati singolarmente devono eseguire la verifica in due passaggi a ogni accesso, con alcune eccezioni, ad esempio se accedono da indirizzi IP attendibili o quando è attiva la funzionalità relativa ai _dispositivi memorizzati_. Il secondo modo prevede la configurazione di criteri di accesso condizionale che richiedano la verifica in due passaggi in presenza di determinate condizioni.

>[!TIP] 
>Scegliere uno dei due metodi per richiedere la verifica in due passaggi, non entrambi. L'abilitazione di un utente per Azure Multi-Factor Authentication sostituisce infatti eventuali criteri di accesso condizionale.

## <a name="which-option-is-right-for-you"></a>Scelta dell'opzione più adatta alle proprie esigenze

**L'abilitazione di Azure Multi-Factor Authentication modificando gli stati utente** è l'approccio tradizionalmente usato per richiedere la verifica in due passaggi. Può essere usato sia per Azure MFA nel cloud sia per Azure MFA Server. Tutti gli utenti abilitati devono effettuare la verifica in due passaggi ogni volta che accedono. L'abilitazione di un utente sostituisce eventuali criteri di accesso condizionale in vigore per l'utente. 

**L'abilitazione di Azure Multi-Factor Authentication con criteri di accesso condizionale** è un approccio più flessibile per richiedere la verifica in due passaggi. Può essere tuttavia usato solo per Azure MFA nel cloud e l'_accesso condizionale_ è una [funzionalità a pagamento di Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). È possibile creare criteri di accesso condizionale da applicare sia a gruppi che a singoli utenti. È possibile, ad esempio, assegnare ai gruppi ad alto rischio più restrizioni rispetto ai gruppi a basso rischio oppure richiedere la verifica in due passaggi solo per le app cloud ad alto rischio e non per quelle a basso rischio. 

In entrambi i casi, gli utenti devono registrarsi ad Azure Multi-Factor Authentication la prima volta che accedono dopo l'attivazione dei requisiti. Entrambe le opzioni, inoltre, possono interagire con le [impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md) configurabili.

## <a name="enable-azure-mfa-by-changing-user-status"></a>Abilitare Azure MFA modificando lo stato utente

Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

| Status | DESCRIZIONE | App interessate non basate su browser | App interessate basate su browser | Autenticazione moderna interessata |
|:---:|:---:|:---:|:--:|:--:|
| Disabled |Stato predefinito per un nuovo utente non registrato in Azure MFA. |No  |No  |No  |
| Attivato |L'utente è stato iscritto ad Azure MFA, ma non ha eseguito la registrazione. Viene richiesto di eseguire la registrazione al successivo accesso. |di serie  Continuano a funzionare fino al completamento della registrazione. | Sì. Dopo la scadenza della sessione, è necessaria la registrazione ad Azure MFA.| Sì. Dopo la scadenza dei token di accesso, è necessaria la registrazione ad Azure MFA. |
| Enforced |L'utente è stato iscritto e ha completato il processo di registrazione per Azure MFA. |Sì.  Le app richiedono password per le app. |Sì. Azure MFA è necessario all'accesso. | Sì. Azure MFA è necessario all'accesso. |

Lo stato dell'utente indica se un amministratore ha eseguito la relativa iscrizione in Azure MFA e se l'utente ha completato il processo di registrazione.

Tutti gli utenti iniziano con *Disabilitato*. Quando si registrano gli utenti in Azure MFA, il relativo stato cambia in *Abilitato*. Quando gli utenti abilitati accedono e completano il processo di registrazione, il relativo stato viene modificato in *Applicato*.  

### <a name="view-the-status-for-a-user"></a>Visualizzare lo stato di un utente

Per accedere alla pagina in cui è possibile visualizzare e gestire gli stati utente, procedere come segue:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
   ![Selezionare Multi-Factor Authentication](./media/howto-mfa-userstates/selectmfa.png)
4. Verrà visualizzata una nuova pagina in cui sono elencati gli stati utente.
   ![Stati utente in Microsoft Azure Multi-Factor Authentication - screenshot](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Modificare lo stato di un utente

1. Usare la procedura precedente per visualizzare la pagina **utenti** di Azure Multi-Factor Authentication.
2. Trovare l'utente che si vuole abilitare per Azure MFA. Potrebbe essere necessario modificare la visualizzazione nella parte superiore. 
   ![Trova utente - screenshot](./media/howto-mfa-userstates/enable1.png)
3. Selezionare la casella accanto al nome.
4. A destra, sotto **Azioni rapide** scegliere **Abilita** o **Disabilita**.
   ![Abilitare l'utente selezionato - screenshot](./media/howto-mfa-userstates/user1.png)

   >[!TIP]
   >Gli utenti *abilitati* diventano automaticamente *applicati* quando si registrano ad Azure MFA. Non modificare manualmente lo stato di un utente su *Applicato*. 

5. Confermare la selezione nella finestra popup che viene visualizzata. 

È consigliabile inviare una notifica tramite posta elettronica agli utenti dopo averli abilitati. Informarli anche che verrà chiesto loro di eseguire la registrazione al successivo accesso e che, se l'organizzazione usa app non basate su browser che non supportano l'autenticazione moderna, devono creare password per l'app. È possibile anche includere un collegamento alla [guida dell'utente finale ad Azure MFA](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md) con informazioni utili per iniziare.

### <a name="use-powershell"></a>Usare PowerShell
Per modificare lo stato dell'utente usando [Azure AD PowerShell](/powershell/azure/overview), modificare `$st.State`. Esistono tre possibili stati:

* Attivato
* Enforced
* Disabled  

Un utente non può essere spostato direttamente sullo stato *Applicato*. Se si esegue questa operazione, le app non basate su browser smettono di funzionare poiché l'utente non ha effettuato la registrazione ad Azure MFA e non ha ottenuto una [password delle app](howto-mfa-mfasettings.md#app-passwords). 

L'uso di PowerShell è la scelta migliore quando è necessario abilitare utenti in massa. Creare uno script di PowerShell che scorra un intero elenco di utenti e li abiliti:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Di seguito è riportato uno script di esempio:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Abilitare Azure MFA con criteri di accesso condizionale

L'_accesso condizionale_ è una funzionalità a pagamento di Azure Active Directory caratterizzata da numerose opzioni di configurazione. Per creare criteri di accesso condizionale, seguire questa procedura. Per altre informazioni, vedere [Accesso condizionale in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare **Azure Active Directory** > **Accesso condizionale**.
3. Selezionare **Nuovi criteri**.
4. In **Assegnazioni** selezionare **Utenti e gruppi**. Usare le schede **Includi** ed **Escludi** per specificare quali utenti e gruppi sono gestiti dai criteri.
5. In **Assegnazioni** selezionare **App cloud**. Scegliere di includere **Tutte le app cloud**.
6. In **Controlli di accesso** selezionare **Concedi**. Selezionare **Richiedi autenticazione a più fattori**.
7. Impostare **Abilita criterio** su **On** e quindi selezionare **Salva**.

Le altre opzioni relative ai criteri di accesso condizionale consentono di specificare esattamente quando deve essere richiesta la verifica in due passaggi. È possibile, ad esempio, creare criteri in base ai quali se un terzista tenta di accedere all'app per gli acquisti da reti non attendibili su dispositivi non appartenenti al dominio, è necessario richiedere la verifica in due passaggi. 

## <a name="next-steps"></a>Passaggi successivi

- Ottenere suggerimenti sulle [Procedure consigliate per l'accesso condizionale](../active-directory-conditional-access-best-practices.md).

- Gestire le impostazioni di Azure Multi-Factor Authentication per [utenti e dispositivi](howto-mfa-userdevicesettings.md).
