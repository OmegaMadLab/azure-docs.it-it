---
title: 'Esercitazione: Integrazione di Azure Active Directory con Clever | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Clever.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: d151aba43a6795c5a691077fe6729fc9853012bf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341485"
---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>Esercitazione: Integrazione di Azure Active Directory con Clever

Questa esercitazione descrive come integrare Clever con Azure Active Directory (Azure AD).

L'integrazione di Clever con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Clever.
- È possibile abilitare gli utenti per l'accesso automatico a Clever (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Clever, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Clever abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Clever dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-clever-from-the-gallery"></a>Aggiunta di Clever dalla raccolta
Per configurare l'integrazione di Clever in Azure AD, è necessario aggiungere Clever dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Clever dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Clever**, selezionare **Clever** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Clever nell'elenco risultati](./media/active-directory-saas-clever-tutorial/tutorial_clever_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Clever usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Clever corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Clever.

Per stabilire la relazione di collegamento, in Clever assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Clever, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Clever](#create-a-clever-test-user)**: per avere una controparte di Britta Simon in Clever collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Clever.

**Per configurare l'accesso Single Sign-On di Azure AD con Clever, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Clever** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-clever-tutorial/tutorial_clever_samlbase.png)

3. Nella sezione **URL e dominio Clever** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Clever](./media/active-directory-saas-clever-tutorial/tutorial_clever_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://clever.com/in/<companyname>`

    b. Nella casella di testo **Identificatore** digitare l'URL: `https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > Poiché questo non è un valore reale, è necessario aggiornare questo valore con l'URL di accesso Sign-On effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Clever](https://clever.com/about/contact/).

4. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.
    
    ![Configure Single Sign-On](./media/active-directory-saas-clever-tutorial/tutorial_metadataurl.png)

5. L'applicazione Clever prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione di **Attributi token SAML**.

    La schermata seguente illustra un esempio relativo a questa operazione.

    ![Configure Single Sign-On](./media/active-directory-saas-clever-tutorial/tutorial_clever_07.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo  | Valore attributo |
    | --------------- | -------------------- |
    | clever.teacher.credentials.district_username|user.userprincipalname|
    | clever.student.credentials.district_username| user.userprincipalname |
    | Firstname  | user.givenname |
    | Lastname  | user.surname |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/active-directory-saas-clever-tutorial/tutorial_attribute_04.png)
    
    ![Configure Single Sign-On](./media/active-directory-saas-clever-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella di testo **Spazio dei nomi**.
    
    d. Fare clic su **OK**.
    
7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-clever-tutorial/tutorial_general_400.png)

8. In un'altra finestra del Web browser accedere al sito aziendale di Clever come amministratore.

9. Nella barra degli strumenti fare clic su **Instant Login**.

    ![Instant Login](./media/active-directory-saas-clever-tutorial/ic798984.png "Instant Login")

    > [!NOTE]
    > Prima di poter testare l'accesso Single Sign-On, è necessario contattare il [team di supporto Clever Client](https://clever.com/about/contact/) per abilitare SSO di Office 365 nel back-end.

10. Nella pagina **Instant Login** seguire questa procedura:
    
      ![Instant Login](./media/active-directory-saas-clever-tutorial/ic798985.png "Instant Login")
    
      a. Digitare un valore in **URL di accesso**.
    
      >[!NOTE]
      >Per **Login URL** usare un valore personalizzato. Per ottenere questo valore, contattare il [team di supporto clienti di Clever](https://clever.com/about/contact/).
    
      b. Per **Identity System** (Sistema di identità) scegliere **ADFS**.

      c. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.
    
      d. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-clever-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-clever-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-clever-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-clever-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.

### <a name="create-a-clever-test-user"></a>Creare un utente di test di Clever

Per consentire agli utenti di Azure AD di accedere a Clever, è necessario effettuarne il provisioning in Clever.

Nel caso di Clever, collaborare con il [team di supporto clienti di Clever](https://clever.com/about/contact/) per aggiungere gli utenti nella piattaforma Clever. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente di Clever per effettuare il provisioning degli account utente di Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Clever.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a Clever, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Clever**.

    ![Collegamento di Clever nell'elenco delle applicazioni](./media/active-directory-saas-clever-tutorial/tutorial_clever_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Clever nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Clever.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clever-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clever-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clever-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clever-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clever-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clever-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clever-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clever-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clever-tutorial/tutorial_general_203.png

