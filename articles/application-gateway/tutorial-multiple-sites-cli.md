---
title: Creare un gateway applicazione che ospita più siti Web - Interfaccia della riga di comando di Azure
description: Informazioni su come creare un gateway applicazione che ospita più siti Web usando l'interfaccia della riga di comando di Azure.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 3/22/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 921c68b6743749f9976d99d20a6c47311006f570
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356053"
---
# <a name="tutorial-create-an-application-gateway-that-hosts-multiple-web-sites-using-the-azure-cli"></a>Esercitazione: Creare un gateway applicazione che ospita più siti Web usando l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per [configurare l'hosting di più siti Web](multiple-site-overview.md) quando si crea un [gateway applicazione](overview.md). In questa esercitazione si definiranno pool di indirizzi back-end usando set di scalabilità di macchine virtuali. e quindi si configurano i listener e le regole in base ai domini di cui si è proprietari per assicurarsi che il traffico Web raggiunga i server appropriati nei pool. Questa esercitazione presuppone che l'utente sia proprietario di più domini e che usi gli esempi di *www.contoso.com* e *www.fabrikam.com*.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare un gateway applicazione
> * Creare i listener back-end
> * Creare le regole di routing
> * Creare set di scalabilità di macchine virtuali con i pool back-end
> * Creare un record CNAME nel dominio

![Esempio di routing multisito](./media/tutorial-multiple-sites-cli/scenario.png)


Se si preferisce, è possibile completare questa esercitazione usando [Azure PowerShell](tutorial-multiple-sites-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse usando [az group create](/cli/azure/group#create).

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAG* nella località *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete 

Creare la rete virtuale e la subnet denominata *myVNet* usando [az network vnet create](/cli/azure/network/vnet#az_net). È quindi possibile aggiungere la subnet necessaria per i server back-end usando [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Creare l'indirizzo IP pubblico denominato *myAGPublicIPAddress* tramite [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

È possibile usare [az network application-gateway create](/cli/azure/application-gateway#create) per creare il gateway applicazione. Quando si crea un gateway applicazione usando l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. Il gateway applicazione viene assegnato alla subnet *myAGSubnet* e all'indirizzo IP pubblico *myAGPublicIPAddress* creati in precedenza. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

Il processo di creazione del gateway applicazione può richiedere alcuni minuti. Dopo aver creato il gateway applicazione, saranno disponibili le nuove funzionalità seguenti:

- *appGatewayBackendPool*: un gateway applicazione deve avere almeno un pool di indirizzi back-end.
- *appGatewayBackendHttpSettings*: specifica che per le comunicazioni vengono usati la porta 80 e il protocollo HTTP.
- *appGatewayHttpListener*: il listener predefinito associato ad *appGatewayBackendPool*.
- *appGatewayFrontendIP*: assegna *myAGPublicIPAddress* ad *appGatewayHttpListener*.
- *rule1*: regola di routing predefinita associata ad *appGatewayHttpListener*.

### <a name="add-the-backend-pools"></a>Aggiungere i pool back-end

Aggiungere i pool back-end necessari in cui includere i server back-end usando [az network application-gateway address-pool create](/cli/azure/application-gateway#az_network_application_gateway_address_pool_create).

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-backend-listeners"></a>Aggiungere i listener back-end

Aggiungere i listener back-end necessari per instradare il traffico usando [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com

az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Aggiungere le regole di routing

Le regole vengono elaborate nell'ordine in cui sono elencate e il traffico viene indirizzato usando la prima regola corrispondente indipendentemente dalla specificità. Se ad esempio si dispone di due regole, una che usa un listener di base e una che usa un listener multisito, entrambe sulla stessa porta, la regola con il listener multisito deve essere elencata prima della regola con il listener di base per funzionare come previsto. 

In questo esempio si creano due nuove regole e si elimina la regola predefinita che è stata creata al momento della creazione del gateway applicazione. È possibile aggiungere la regola usando [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool

az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Creare set di scalabilità di macchine virtuali

In questo esempio si creano tre set di scalabilità di macchine virtuali che supportano i tre pool back-end nel gateway applicazione. I set di scalabilità creati sono denominati *myvmss1*, *myvmss2* e *myvmss3*. Ogni set di scalabilità contiene due istanze di macchina virtuale in cui si installa IIS.

```azurecli-interactive
for i in `seq 1 2`; do

  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi

  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Installare NGINX

```azurecli-interactive
for i in `seq 1 2`; do

  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="create-a-cname-record-in-your-domain"></a>Creare un record CNAME nel dominio

Dopo aver creato il gateway applicazione con l'indirizzo IP pubblico, è possibile ottenere l'indirizzo DNS e usarlo per creare un record CNAME nel dominio. Per ottenere l'indirizzo DNS del gateway applicazione è possibile usare [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiare il valore *fqdn* di DNSSettings e usarlo come valore del record CNAME creato. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Non è consigliabile usare record A perché l'indirizzo VIP può cambiare quando il gateway applicazione viene riavviato.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Immettere il nome di dominio nella barra degli indirizzi del browser. Ad esempio, http://www.contoso.com.

![Testare il sito contoso nel gateway applicazione](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest1.png)

Sostituire l'indirizzo con l'altro dominio come nell'esempio seguente:

![Testare il sito fabrikam nel gateway applicazione](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest2.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare un gateway applicazione
> * Creare i listener back-end
> * Creare le regole di routing
> * Creare set di scalabilità di macchine virtuali con i pool back-end
> * Creare un record CNAME nel dominio

> [!div class="nextstepaction"]
> [Creare un gateway applicazione con regole di routing basate su percorsi URL](./tutorial-url-route-cli.md)