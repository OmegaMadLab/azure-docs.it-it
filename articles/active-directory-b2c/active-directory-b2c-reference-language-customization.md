---
title: Personalizzazione della lingua in Azure AD B2C | Microsoft Docs
description: Informazioni sulla personalizzazione dell'esperienza per la lingua.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/26/2018
ms.author: davidmu
ms.openlocfilehash: 097033b78e3e4f640e7bf4008fd970c53315d5d7
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200553"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalizzazione della lingua in Azure AD B2C

La personalizzazione della lingua in Azure Active Directory B2C (Azure AD B2C) consente ai criteri di modificare la lingua del percorso utente in base alle esigenze dei clienti.  Microsoft fornisce le traduzioni per [36 lingue](#supported-languages), ma è anche possibile inserire le proprie traduzioni per qualsiasi lingua. Anche se l'esperienza è disponibile per una sola lingua, è possibile personalizzare qualsiasi testo nelle pagine.  

## <a name="how-language-customization-works"></a>Funzionamento della personalizzazione della lingua
La personalizzazione della lingua consente di selezionare le lingue in cui è disponibile il percorso utente. Dopo aver abilitato la funzionalità, è possibile specificare il parametro della stringa di query, `ui_locales`, dall'applicazione. Quando si esegue la chiamata ad Azure AD B2C, la pagina viene tradotta nelle impostazioni locali indicate. Questo tipo di configurazione garantisce il controllo completo sulle lingue del percorso utente e ignora le impostazioni della lingua del browser del cliente. 

Potrebbe non essere necessario un tale livello di controllo sulle lingue visualizzate dal cliente. Se non si specifica un parametro `ui_locales`, l'esperienza del cliente è determinata dalle impostazioni del browser.  È comunque possibile controllare le lingue in cui il percorso utente è tradotto aggiungendole come lingue supportate. Se il browser del cliente è impostato su una lingua che non si vuole supportare, viene visualizzata la lingua selezionata come predefinita nelle impostazioni cultura supportate.

- **Lingua specificata tramite ui-locales**: dopo aver abilitato la personalizzazione della lingua, il percorso utente viene tradotto nella lingua qui specificata.
- **Lingua richiesta dal browser**: se non è stato specificato alcun parametro `ui_locales`, il percorso utente viene tradotto nella lingua richiesta dal browser *se è supportata*.
- **Lingua predefinita nei criteri**: se il browser non specifica alcuna lingua oppure ne specifica una non supportata, il percorso utente viene tradotto nella lingua predefinita nei criteri.

>[!NOTE]
>Se si fa uso di attributi utente personalizzati, è necessario fornire le traduzioni. Per altre informazioni, vedere [Personalizzazione delle stringhe](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Supporto delle lingue richieste per ui_locales 
Per i criteri creati prima della disponibilità generale della personalizzazione della lingua occorre prima abilitare questa funzionalità. Per i criteri creati successivamente la personalizzazione della lingua è abilitata per impostazione predefinita. 

Quando si abilita la personalizzazione della lingua nei criteri, è possibile controllare la lingua del percorso utente aggiungendo il parametro `ui_locales`.
1. [Passare alla pagina delle funzionalità B2C nel portale di Azure](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings).
2. Passare al criterio da abilitare per le traduzioni.
3. Selezionare **Personalizzazione della lingua**.  
4. Selezionare **Abilita personalizzazione della lingua**.
5. Leggere le informazioni nella finestra di dialogo e selezionare **Sì**.

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Selezionare le lingue abilitate per il percorso utente 
Abilitare un set di lingue per il percorso utente da convertire quando richiesto dal browser senza il parametro `ui_locales`.
1. Assicurarsi che la personalizzazione della lingua sia abilitata nei criteri in base alle istruzioni precedenti.
2. Nella pagina **Modifica criterio** selezionare **Personalizzazione della lingua**.
3. Selezionare una lingua che si vuole supportare.
4. Nel riquadro delle proprietà impostare **Attivato** su **Sì**.  
5. Scegliere **Salva** nella parte superiore del riquadro delle proprietà.

>[!NOTE]
>Se non viene specificato un parametro `ui_locales`, la pagina viene tradotta nella lingua del browser del cliente solo se è abilitata.
>

## <a name="customize-your-strings"></a>Personalizzazione delle stringhe
La personalizzazione della lingua consente di personalizzare qualsiasi stringa nel percorso utente.
1. Assicurarsi che la personalizzazione della lingua sia abilitata nei criteri in base alle istruzioni precedenti.
2. Nella pagina **Modifica criterio** selezionare **Personalizzazione della lingua**.
3. Selezionare la lingua da personalizzare.
4. Selezionare la pagina da modificare.
5. Selezionare **Scarica impostazioni predefinite** (o **Scarica override** se in precedenza si è già modificata questa lingua). 

Questa procedura permette di ottenere un file JSON con cui iniziare a modificare le stringhe.

### <a name="change-any-string-on-the-page"></a>Modificare stringhe nella pagina
1. In un editor JSON aprire il file JSON scaricato seguendo le istruzioni precedenti.
2. Trovare l'elemento da modificare.  È possibile trovare il valore `StringId` della stringa che si sta cercando o cercare l'attributo `Value` che si vuole modificare.
3. Aggiornare l'attributo `Value` con i dati da visualizzare.
4. Per ogni stringa che si vuole modificare impostare `Override` su `true`.
5. Salvare il file e caricare le modifiche. Il controllo di caricamento si trova nella stessa posizione in cui è stato scaricato il file JSON. 

>[!IMPORTANT]
>Se è necessario eseguire l'override di una stringa, assicurarsi di impostare il valore `Override` su `true`.  Se il valore non viene modificato, la voce viene ignorata. 
>

### <a name="change-extension-attributes"></a>Cambiare gli attributi di estensione
Per modificare la stringa di un attributo utente personalizzato o aggiungerne uno al file JSON, usare il formato seguente:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Sostituire `<ExtensionAttribute>` con il nome dell'attributo utente personalizzato.  

Sostituire `<ExtensionAttributeValue>` con la nuova stringa da visualizzare.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornire un elenco di valori usando LocalizedCollections
Se si vuole specificare un elenco preimpostato di valori per le risposte, è necessario creare un attributo `LocalizedCollections`.  `LocalizedCollections` è una matrice di coppie `Name`-`Value`. L'ordine degli elementi sarà l'ordine in che cui vengono visualizzati.  Per aggiungere `LocalizedCollections`, usare il formato seguente:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` è l'attributo utente per il quale l'attributo `LocalizedCollections` rappresenta una risposta.
* `Name` è il valore che viene visualizzato all'utente.
* `Value` è il valore restituito nell'attestazione quando questa opzione è selezionata.

### <a name="upload-your-changes"></a>Caricamento delle modifiche
1. Dopo aver completato le modifiche al file JSON, tornare al tenant B2C.
2. Nella pagina **Modifica criterio** selezionare **Personalizzazione della lingua**.
3. Selezionare la lingua in cui si vuole tradurre.
4. Selezionare la pagina per cui si vogliono fornire traduzioni.
5. Selezionare l'icona della cartella e selezionare il file JSON da caricare.
 
Le modifiche vengono salvate automaticamente nel criterio.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalizzare l'interfaccia utente della pagina usando la personalizzazione della lingua

Esistono due modi per localizzare il contenuto HTML. Un modo consiste nell'attivare la [personalizzazione della lingua](active-directory-b2c-reference-language-customization.md). L'abilitazione di questa funzionalità consente ad Azure AD B2C di inoltrare il parametro Open ID Connect, `ui-locales`, all'endpoint.  Il server di contenuti può usare questo parametro per fornire pagine HTML personalizzate specifiche della lingua.

In alternativa, è possibile eseguire il pull del contenuto da posizioni diverse in base alle impostazioni locali in uso. Nell'endpoint abilitato per CORS è possibile configurare una struttura di cartelle in cui ospitare il contenuto per specifiche lingue. Se si inserisce il valore del carattere jolly `{Culture:RFC5646}` verrà chiamata la lingua corretta.  Ad esempio, supponiamo che questo sia l'URI della pagina personalizzata:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
È possibile caricare la pagina in `fr`. Quando la pagina esegue il pull del contenuto HTML e CSS, lo fa da:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Aggiungere lingue personalizzate

È anche possibile aggiungere per cui attualmente Microsoft non fornisce traduzioni. Sarà necessario fornire le traduzioni per tutte le stringhe contenute nei criteri.  I codici della lingua e delle impostazioni internazionali sono limitati a quelli nello standard ISO 639-1. 

1. Nella pagina **Modifica criterio** selezionare **Personalizzazione della lingua**.
2. Selezionare **Aggiungi lingua personalizzata** nella parte superiore della pagina.
3. Nel riquadro del contesto che viene visualizzato identificare la lingua per cui si stanno inserendo le traduzioni immettendo un codice delle impostazioni locali valido.
4. Per ogni pagina è possibile scaricare un set di sostituzioni per la lingua inglese e lavorare sulle traduzioni.
5. Una volta terminato con i file JSON, è possibile caricarli per ogni pagina.
6. Selezionare **Abilita** e i criteri potranno ora visualizzare la lingua per gli utenti.
7. Salvare la lingua.

>[!IMPORTANT]
>È necessario abilitare le lingue personalizzate o caricare gli override per la lingua prima di poterla salvare.
>

## <a name="additional-information"></a>Informazioni aggiuntive

### <a name="page-ui-customization-labels-as-overrides"></a>Etichette di personalizzazione dell'interfaccia utente della pagina come sostituzioni
Quando si abilita la personalizzazione della lingua, le modifiche precedenti apportate alle etichette usando la personalizzazione dell'interfaccia utente della pagina vengono rese persistenti in un file JSON per la lingua Inglese (en). È possibile continuare a modificare le etichette e le altre stringhe caricando le risorse di lingua nella funzionalità di personalizzazione della lingua.
### <a name="up-to-date-translations"></a>Traduzioni aggiornate
Microsoft si impegna a fornire le traduzioni più aggiornate. Continuerà a migliorare le traduzioni e a garantirne la conformità. Verranno identificati i bug e le modifiche alla terminologia globale e verranno eseguiti aggiornamenti compatibili con il percorso utente.
### <a name="support-for-right-to-left-languages"></a>Supporto per lingue da destra a sinistra
Microsoft non fornisce attualmente supporto per le lingue da destra a sinistra. A questo scopo, è possibile usare impostazioni locali personalizzate e modificare la modalità di visualizzazione delle stringhe tramite CSS.  Se si ha bisogno di questa funzionalità, votarla in [Commenti e suggerimenti su Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traduzioni per provider di identità basati su social network
Microsoft fornisce il parametro OIDC `ui_locales` per gli account di accesso ai social network, che però non viene riconosciuto da alcuni provider di identità basati su social network, tra cui Facebook e Google. 
### <a name="browser-behavior"></a>Comportamento dei browser
Chrome e Firefox richiedono entrambi la relativa lingua preimpostata. Se è supportata, tale lingua viene visualizzata prima di quella predefinita. Microsoft Edge attualmente non richiede alcuna lingua e passa direttamente alla lingua predefinita.

### <a name="supported-languages"></a>Lingue supportate

| Linguaggio              | Codice lingua |
|-----------------------|---------------|
| Bengalese                | bn            |
| Ceco                 | cs            |
| Danese                | da            |
| Tedesco                | de            |
| Greco                 | el            |
| Inglese               | en            |
| Spagnolo               | es            |
| Finlandese               | fi            |
| Francese                | fr            |
| Gujarati              | gu            |
| Hindi                 | hi            |
| Croato              | hr            |
| Ungherese             | hu            |
| Italiano               | it            |
| Giapponese              | ja            |
| Kannada               | kn            |
| Coreano                | ko            |
| Malayalam             | ml            |
| Marathi               | mr            |
| Malese                 | ms            |
| Norvegese bokmål      | nb            |
| Olandese                 | nl            |
| Punjabi               | pa            |
| Polacco                | pl            |
| Portoghese (Brasile)   | pt-br         |
| Portoghese (Portogallo) | pt-pt         |
| Romeno              | ro            |
| Russo               | ru            |
| Slovacco                | sk            |
| Svedese               | sv            |
| Tamil                 | ta            |
| Telugu                | te            |
| Thai                  | th            |
| Turco               | tr            |
| Cinese (semplificato)  | zh-hans       |
| Cinese (tradizionale) | zh-hant       |
