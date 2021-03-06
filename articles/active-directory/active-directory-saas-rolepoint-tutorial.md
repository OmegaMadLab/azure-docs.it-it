---
title: 'Esercitazione: Integrazione di Azure Active Directory con RolePoint | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RolePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 68d37f40-15da-45f5-a9e1-d53f78e786d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: jeedes
ms.openlocfilehash: fea5a205813937b92d3edf7617caae52212eb3ae
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>Esercitazione: Integrazione di Azure Active Directory con RolePoint

Questa esercitazione descrive come integrare RolePoint con Azure Active Directory (Azure AD).

L'integrazione di RolePoint con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a RolePoint
- È possibile abilitare gli utenti per l'accesso automatico a RolePoint (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con RolePoint, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di RolePoint abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di RolePoint dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-rolepoint-from-the-gallery"></a>Aggiunta di RolePoint dalla raccolta
Per configurare l'integrazione di RolePoint in Azure AD, è necessario aggiungere RolePoint dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere RolePoint dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **RolePoint**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_search.png)

5. Nel pannello dei risultati selezionare **RolePoint** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con RolePoint in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di RolePoint che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RolePoint.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in RolePoint.

Per configurare e testare l'accesso Single Sign-On di Azure AD con RolePoint, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test RolePoint](#creating-a-rolepoint-test-user)**: per avere una controparte di Britta Simon in RolePoint collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione RolePoint.

**Per configurare l'accesso Single Sign-On di Azure AD con RolePoint, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **RolePoint** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_samlbase.png)

3. Nella sezione **URL e dominio RolePoint** eseguire i passaggi descritti di seguito:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.rolepoint.com/login`
    
    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://app.rolepoint.com/<instancename>`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. In questo caso, è consigliabile usare il valore univoco della stringa in Identificatore. Contattare il [team di supporto RolePoint](mailto:info@rolepoint.com) per ottenere il valore. 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-rolepoint-tutorial/tutorial_general_400.png)


6. Per configurare l'accesso Single Sign-On sul lato **RolePoint**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di RolePoint](mailto:info@rolepoint.com).

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-rolepoint-test-user"></a>Creazione di un utente test di RolePoint

In questa sezione viene creato un utente chiamato Britta Simon in RolePoint. Collaborare con il [team di supporto di RolePoint](mailto:info@rolepoint.com) per aggiungere utenti nella piattaforma RolePoint.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a RolePoint.

![Assegna utente][200] 

**Per assegnare Britta Simon a RolePoint, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **RolePoint**.

    ![Configure Single Sign-On](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro RolePoint nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RolePoint. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_203.png

