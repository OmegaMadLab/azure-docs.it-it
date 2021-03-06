---
title: Eseguire il provisioning di un dispositivo con il servizio Device Provisioning in hub IoT di Azure| Microsoft Docs
description: Eseguire il provisioning del dispositivo in un singolo hub IoT con il servizio Device Provisioning in hub IoT di Azure
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/12/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 9f151a8fbcdc20124467a1db290f6a05f574e4fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Eseguire il provisioning del dispositivo in un hub IoT con il servizio Device Provisioning in hub IoT di Azure

Nell'esercitazione precedente abbiamo appreso come configurare un dispositivo per la connessione al servizio Device Provisioning. In questa esercitazione si apprenderà come usare questo servizio per eseguire il provisioning del dispositivo in un singolo hub IoT usando il provisioning automatico e gli **_elenchi di registrazione_**. Questa esercitazione illustra come:

> [!div class="checklist"]
> * Registrare il dispositivo
> * Avviare il dispositivo
> * Verificare che il dispositivo sia registrato

## <a name="prerequisites"></a>prerequisiti

Prima di procedere, assicurarsi di configurare il dispositivo come descritto nell'esercitazione [Configurare un dispositivo per il provisioning usando il servizio Device Provisioning in hub IoT di Azure](./tutorial-set-up-device.md).

Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](concepts-auto-provisioning.md) prima di continuare.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Registrare il dispositivo

Questo passaggio prevede l'aggiunta di elementi di sicurezza esclusivi del dispositivo al servizio Device Provisioning. Questi elementi di sicurezza si basano sul [Meccanismo di attestazione](concepts-device.md#attestation-mechanism) del dispositivo, come indicato di seguito:

- Per i dispositivi basati su TPM sono necessari:
    - *Chiave di verifica dell'autenticità*, che è univoca per ogni chip TPM o simulazione e che deve essere richiesta al produttore del chip TPM.  Per altre informazioni leggere [Informazioni sulla chiave di verifica dell'autenticità del TPM](https://technet.microsoft.com/library/cc770443.aspx).
    - *ID di registrazione*, usato per identificare in modo univoco un dispositivo nell'ambito o nello spazio dei nomi. L'ID può coincidere o meno con l'ID dispositivo. L'ID è obbligatorio per ogni dispositivo. Per i dispositivi basati su TPM, l'ID di registrazione può essere derivato dal TPM stesso, ad esempio un hash SHA-256 della chiave di verifica dell'autenticità del TPM.

    [![Informazioni di registrazione per TPM nel portale](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- Per i dispositivi basati su X.509 sono necessari:
    - [Certificato emesso nel chip X.509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) o nella simulazione, sotto forma di file con estensione *pem* o *cer*. Per la registrazione individuale è necessario usare il *certificato del firmatario* per dispositivo per il sistema X.509, mentre per i gruppi di registrazione è necessario usare il *certificato radice*. 

    [![Aggiungere una registrazione singola per l'attestazione X.509 nel portale](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

È possibile registrare il dispositivo nel servizio Device Provisioning in due modi:

- **Enrollment Groups** (Gruppi di registrazione) Rappresenta un gruppo di dispositivi che condividono un meccanismo di attestazione specifico. È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant.

    [![Aggiungere una registrazione di gruppo per l'attestazione X.509 nel portale](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **Individual Enrollments** (Registrazioni individuali) Rappresenta una voce per un singolo dispositivo che può eseguire la registrazione al servizio Device Provisioning. Le registrazioni individuali possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM reale o virtuale) come meccanismo di attestazione. È consigliabile usare le registrazioni individuali per i dispositivi che richiedono configurazioni iniziali univoche e per i dispositivi che possono usare solo token di firma di accesso condiviso tramite TPM o TPM virtuale come meccanismo di attestazione. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

Ora si registra il dispositivo con l'istanza del servizio Device Provisioning, usando gli elementi di sicurezza necessari in base al meccanismo di attestazione del dispositivo: 

1. Accedere al portale di Azure, fare clic sul pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio Device Provisioning.

2. Nel pannello di riepilogo del servizio Device Provisioning selezionare **Manage enrollments** (Gestisci registrazioni). Selezionare la scheda **Individual Enrollments** (Registrazioni individuali) o **Enrollment Groups** (Gruppi di registrazione) in base alla configurazione del dispositivo. Fare clic sul pulsante **Add** (Aggiungi) in alto. Selezionare **TPM** o **X.509** come *meccanismo* di attestazione dell'identità e immettere gli elementi di sicurezza appropriati come descritto in precedenza. È possibile immettere un nuovo **ID di dispositivo hub IoT**. Al termine, fare clic sul pulsante **Save** (Salva). 

3. Quando la registrazione è completata, il dispositivo verrà visualizzato nel portale come riportato di seguito:

    ![Successful TPM enrollment in the portal (Registrazione TPM nel portale riuscita)](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

Dopo la registrazione, il servizio di provisioning attende che il dispositivo venga avviato per potersi connettere successivamente. Quando il dispositivo viene avviato per la prima volta, la libreria SDK del client interagisce con il chip per estrarre gli elementi di sicurezza dal dispositivo e verifica la registrazione con il servizio Device Provisioning. 

## <a name="start-the-device"></a>Avviare il dispositivo

A questo punto la configurazione seguente è pronta per la registrazione del dispositivo:

1. Il dispositivo o il gruppo di dispositivi sono registrati per il servizio Device Provisioning e 
2. Il dispositivo è pronto con il meccanismo di attestazione configurato ed è accessibile attraverso l'applicazione usando Device Provisioning Service client SDK.

Avviare il dispositivo per consentire all'applicazione client di eseguire la registrazione al servizio Device Provisioning.  

## <a name="verify-the-device-is-registered"></a>Verificare che il dispositivo sia registrato

In seguito all'avvio del dispositivo dovrebbero verificarsi le azioni seguenti. Vedere l'applicazione di esempio del simulatore TPM [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) per altri dettagli. 

1. Il dispositivo invia una richiesta di registrazione al servizio Device Provisioning.
2. Per i dispositivi TPM il servizio Device Provisioning invia di nuovo una richiesta di registrazione a cui risponde il dispositivo. 
3. Al termine della registrazione il servizio Device Provisioning invia l'URI dell'hub IoT, l'ID dispositivo e la chiave crittografata di nuovo al dispositivo. 
4. L'applicazione client dell'hub IoT nel dispositivo si connette quindi all'hub. 
5. Successivamente alla connessione all'hub il dispositivo dovrebbero essere visualizzato in **Device Explorer** dell'hub IoT. 

    ![Successful connection to hub in the portal (Connessione all'hub nel portale riuscita)](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Registrare il dispositivo
> * Avviare il dispositivo
> * Verificare che il dispositivo sia registrato

Passare all'esercitazione successiva per informazioni su come eseguire il provisioning di più dispositivi tra hub con bilanciamento del carico. 

> [!div class="nextstepaction"]
> [Eseguire il provisioning dei dispositivi in più hub IoT con bilanciamento del carico](./tutorial-provision-multiple-hubs.md)
