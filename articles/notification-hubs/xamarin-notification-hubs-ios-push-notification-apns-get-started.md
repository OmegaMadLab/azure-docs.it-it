---
title: Eseguire il push di notifiche alle app Xamarin.iOS con Hub di notifica di Azure | Microsoft Docs
description: In questa esercitazione, sono presenti informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Xamarin iOS.
services: notification-hubs
keywords: notifiche push di ios,messaggi push,notifiche push,messaggio push
documentationcenter: xamarin
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: babd6bff3cec38318cacc0d55394a7563f8e69a4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Esercitazione: eseguire il push di notifiche alle app Xamarin.iOS con Hub di notifica di Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per iOS. Si crea un'app Xamarin.iOS vuota che riceve notifiche push attraverso [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html). 

Al termine è possibile usare l'hub di notifica per trasmettere le notifiche push a tutti i dispositivi che eseguono l'app. Il codice compilato è disponibile nell'esempio di [app NotificationHubs][GitHub].

In questa esercitazione si crea/aggiorna il codice per eseguire le attività seguenti: 

> [!div class="checklist"]
> * Generare il file della richiesta di firma del certificato
> * Registrare l'app per le notifiche push
> * Creare un profilo di provisioning per l'app
> * Configurare l'hub di notifica per l'invio di notifiche push di iOS
> * Inviare notifiche push di prova

## <a name="prerequisites"></a>prerequisiti

- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Versione più recente di [Xcode][Install Xcode]
- Dispositivo compatibile con iOS 10 o versione successiva
- [Apple Developer Program](https://developer.apple.com/programs/) .
- [Visual Studio per Mac]
  
  > [!NOTE]
  > Considerati i requisiti di configurazione delle notifiche push di iOS, è necessario distribuire e testare l'applicazione di esempio in un dispositivo iOS, iPhone o iPad, anziché in un simulatore.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app Xamarin.iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurare l'hub di notifica per l'invio di notifiche push di iOS
Questa sezione illustra come creare un nuovo hub di notifica e configurare l'autenticazione con il servizio APN usando il certificato push **.p12** creato in precedenza. Se si vuole usare un hub di notifica che è già stato creato, è possibile ignorare il passaggio 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-ios-settings-for-the-notification-hub"></a>Configurare le impostazioni di iOS per l'hub di notifica
1. Selezionare **Apple (APNS)** nel gruppo **NOTIFICATION SETTINGS** (IMPOSTAZIONI DI NOTIFICA). 
2. Selezionare **Certificato**, fare clic sull'icona del **file** e quindi selezionare il file con estensione **p12** esportato in precedenza. 
3. Immettere la **password** per il certificato. 
4. Selezionare la modalità **Sandbox**. Usare la modalità **Production** (Produzione) solo se si vuole inviare notifiche push agli utenti che hanno acquistato l'app dallo Store.

    ![Configurare il servizio APN nel portale di Azure][6]

    ![Configurare il certificato APN nel portale di Azure][7]

L'hub di notifica è ora configurato per l'uso con il servizio APN e sono disponibili le stringhe di connessione per la registrazione dell'app e l'invio di notifiche push.

## <a name="connect-your-app-to-the-notification-hub"></a>Connettere l'app all'hub di notifica
#### <a name="create-a-new-project"></a>Creare un nuovo progetto
1. In Visual Studio creare un nuovo progetto iOS e selezionare il modello **App visualizzazione singola**, quindi fare clic su **Avanti**
   
     ![Visual Studio - Selezionare il tipo di applicazione][31]

2. Immettere il nome dell'app e l'identificatore dell'organizzazione, quindi fare clic su **Avanti** e su **Crea**

3. Nella visualizzazione della soluzione fare doppio clic su *Info.plist* e in **Identità** assicurarsi che l'identificatore del bundle corrisponda a quello usato per la creazione del profilo di provisioning. In **Firma** accertarsi che sotto **Team** sia selezionato il proprio account per sviluppatore, che sia selezionato "Automatically manage signing" (Gestisci firma automaticamente) e che vengano automaticamente selezionati il certificato di firma e il profilo di provisioning.

    ![Visual Studio - Configurazione di un'app iOS][32]

4. Aggiungere il pacchetto di messaggistica di Azure. Nella visualizzazione della soluzione fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Aggiungi pacchetti NuGet**. Cercare **Xamarin.Azure.NotificationHubs.iOS** e aggiungere il pacchetto al progetto.

5. Aggiungere un nuovo file alla classe, assegnargli il nome **Constants.cs**, aggiungere le variabili seguenti e sostituire i segnaposto dei valori letterali stringa con il *nome dell'hub* e il valore *DefaultListenSharedAccessSignature* annotati in precedenza.
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. In **AppDelegate.cs**aggiungere l'istruzione using seguente:
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. Dichiarare un'istanza di **SBNotificationHub**:
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. In **AppDelegate.cs** aggiornare **FinishedLaunching()** in base al codice seguente:
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

9. Eseguire l'override del metodo **RegisteredForRemoteNotifications()** in **AppDelegate.cs**:
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

10. Eseguire l'override del metodo **ReceivedRemoteNotification()** in **AppDelegate.cs**:
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. Creare il metodo **ProcessNotification()** seguente in **AppDelegate.cs**:
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > È possibile scegliere di eseguire l'override di **FailedToRegisterForRemoteNotifications()** per gestire situazioni quali l'assenza di una connessione di rete. Questo risulta particolarmente importante nei casi in cui l'utente può avviare l'applicazione in modalità offline, ad esempio in aereo, e si vogliono gestire scenari di messaggistica push specifici per l'applicazione.
  

12. Eseguire l'app sul dispositivo.

## <a name="send-test-push-notifications"></a>Inviare notifiche push di prova
È possibile testare rapidamente la ricezione di notifiche nell'app con l'opzione *Invio di prova* nel [portale di Azure]. Verrà inviata una notifica push di prova al dispositivo.

![Portale di Azure - Invio di prova][30]

Le notifiche push vengono in genere inviate in un servizio back-end come App per dispositivi mobili o ASP.NET usando una libreria compatibile. Se non è disponibile una libreria per il back-end è anche possibile usare direttamente l'API REST per inviare messaggi di notifica.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione le notifiche sono state trasmesse a tutti i dispositivi iOS registrati con il back-end. Per informazioni sulle procedure per eseguire il push di notifiche a dispositivi iOS specifici, passare all'esercitazione seguente: 

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a dispositivi specifici](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio per Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Portale di Azure]: https://portal.azure.com
