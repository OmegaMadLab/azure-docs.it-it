---
title: 'Creare un gateway VPN basato su route: portale di Azure | Microsoft Docs'
description: Creare rapidamente un gateway VPN basato su route usando il portale di Azure
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: 550f655f6eac5a114636978255578eb3753e0d4b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Creare un gateway VPN basato su route usando il portale di Azure

Questo articolo illustra come creare rapidamente un gateway VPN di Azure basato su route usando il portale di Azure.  Un gateway VPN viene usato quando si crea una connessione VPN alla rete locale. È anche possibile usare il gateway VPN per connettere reti virtuali. 

Seguendo i passaggi in questo articolo si creeranno una rete virtuale, una subnet, una subnet del gateway e un gateway VPN basato su route, ovvero un gateway di rete virtuale. Al termine della creazione del gateway, sarà possibile creare le connessioni. Questi passaggi richiedono una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="vnet"></a>Creare una rete virtuale

1. In un browser passare al [portale di Azure](http://portal.azure.com) e accedere con l'account Azure.
2. Fare clic su **Crea una risorsa**. Nel campo **Cerca nel Marketplace** digitare "rete virtuale". Individuare **Rete virtuale** nell'elenco restituito e fare clic per aprire la pagina **Rete virtuale**.
3. Nella parte inferiore della pagina Rete virtuale verificare che sia selezionato **Resource Manager** nell'elenco a discesa **Selezionare un modello di distribuzione** e quindi fare clic su **Crea**. Verrà visualizzata la pagina **Crea rete virtuale**.
4. Nella pagina **Crea rete virtuale** configurare le impostazioni della rete virtuale. Durante la compilazione dei campi, il punto esclamativo rosso diventa un segno di spunta verde se i caratteri immessi nel campo sono validi. Usare i valori seguenti:

  - **Nome**: TestVNet1
  - **Spazio indirizzi**: 10.1.0.0/16
  - **Sottoscrizione**: verificare che la sottoscrizione elencata sia quella che si vuole usare. È possibile cambiare sottoscrizione tramite l'elenco a discesa.
  - **Gruppo di risorse**: TestRG1
  - **Location**: Stati Uniti orientali
  - **Subnet**: Frontend
  - **Intervallo di indirizzi**: 10.1.0.0/24

  ![Pagina Crea rete virtuale](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "Pagina Crea rete virtuale")
5. Dopo avere immesso i valori, selezionare **Aggiungi al dashboard** per trovare facilmente la rete virtuale nel dashboard e quindi fare clic su **Crea**. Dopo aver fatto clic su **Crea**, viene visualizzato un riquadro nel dashboard che riflette lo stato di avanzamento della rete virtuale. Il riquadro cambia durante la creazione della rete virtuale.

## <a name="gwsubnet"></a>Aggiungere una subnet del gateway

La subnet del gateway contiene gli indirizzi IP riservati usati dai servizi del gateway di rete virtuale. Creare una subnet del gateway.

1. Nel portale passare alla rete virtuale per cui si vuole creare un gateway di rete virtuale.
2. Nella pagina della rete virtuale fare clic su **Subnet** per espandere la pagina **VNet1 - Subnet**.
3. Fare clic su **+Subnet del gateway** in alto per aprire la pagina **Aggiungi subnet**.

  ![Aggiungere la subnet del gateway](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "Aggiungere la subnet del gateway")
4. Il **nome** della subnet verrà compilato automaticamente con il valore "GatewaySubnet". Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai valori seguenti:

  **Intervallo di indirizzi (blocco CIDR)**: 10.1.255.0/27

  ![Aggiunta della subnet del gateway](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "Aggiunta della subnet del gateway")
5. Fare clic su **OK** nella parte inferiore della pagina per creare la subnet del gateway.

## <a name="gwvalues"></a>Configurare le impostazioni del gateway

1. Nel lato sinistro della pagina del portale fare clic su **+ Crea una risorsa**, digitare "gateway di rete virtuale" nella casella di ricerca e quindi premere **INVIO**. In **Risultati** individuare e selezionare **Gateway di rete virtuale**.
2. Nella parte inferiore della pagina "Gateway di rete virtuale" fare clic su **Crea** nella parte inferiore per aprire la pagina **Crea gateway di rete virtuale**.
3. Nella pagina **Crea gateway di rete virtuale** specificare i valori per il gateway di rete virtuale.

  - **Nome**: Vnet1GW
  - **Tipo di gateway**: VPN 
  - **Tipo VPN**: Basato su route
  - **SKU**: VpnGw1
  - **Location**: Stati Uniti orientali
  - **Rete virtuale**: fare clic su **Rete virtuale/Scegliere una rete virtuale** per aprire la pagina **Scegliere una rete virtuale**. Selezionare **VNet1**.

  ![Configurare le impostazioni del gateway](./media/create-routebased-vpn-gateway-portal/configure-gateway.png "Configurare le impostazioni del gateway")

## <a name="pip"></a>Creare un indirizzo IP pubblico

Un gateway VPN deve avere un indirizzo IP pubblico allocato in modo dinamico. Si tratta dell'indirizzo IP a cui si connette il dispositivo locale quando si crea una connessione a un gateway VPN.

1. Selezionare **Prima configurazione IP Crea configurazione IP gateway** per richiedere un indirizzo IP pubblico.

  ![Prima configurazione IP](./media/create-routebased-vpn-gateway-portal/add-public-ip-address.png "Prima configurazione IP")
2. Nella pagina **Scegli indirizzo IP pubblico** fare clic su **+ Crea nuovo** per aprire la pagina **Crea indirizzo IP pubblico**.
3. Configurare le impostazioni con i valori seguenti:

  - **Nome**: **VNet1GWIP**
  - **SKU**: **Basic**

  ![Crea indirizzo IP pubblico](./media/create-routebased-vpn-gateway-portal/public-ip-address-name.png "")
4. Fare clic su **OK** nella parte inferiore di questa pagina per salvare le modifiche.

## <a name="creategw"></a>Creare il gateway VPN

1. Verificare le impostazioni nella pagina **Crea gateway di rete virtuale**. Modificare i valori, se necessario.

  ![Creare il gateway VPN](./media/create-routebased-vpn-gateway-portal/create-vpn-gateway.png "Creare il gateway VPN")
2. Fare clic su **Crea** nella parte inferiore della pagina.

Dopo avere fatto clic su **Crea**, le impostazioni vengono convalidate e nel dashboard viene visualizzato il riquadro **Deploying Virtual network gateway** (Distribuzione del gateway di rete virtuale). Per un gateway VPN possono essere necessari fino a 45 minuti. Potrebbe essere necessario aggiornare la pagina del portale per visualizzare lo stato di completamento.

## <a name="viewgw"></a>Visualizzare il gateway VPN

1. Dopo la creazione del gateway, passare a VNet1 nel portale. Il gateway VPN viene visualizzato nella pagina Panoramica come dispositivo connesso.

  ![Dispositivi connessi](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Dispositivi connessi")

2. Nell'elenco di dispositivi fare clic su **VNet1GW** per visualizzare altre informazioni.

  ![Visualizzare il gateway VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Visualizzare il gateway VPN")

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il gateway, è possibile creare una connessione tra la propria rete virtuale e un'altra rete virtuale oppure tra la propria rete virtuale e una posizione locale.

> [!div class="nextstepaction"]
> [Creare una connessione da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Creare una connessione da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Creare una connessione a un'altra rete virtuale](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)