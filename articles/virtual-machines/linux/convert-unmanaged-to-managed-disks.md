---
title: Convertire una macchina virtuale Linux in Azure da dischi non gestiti a dischi gestiti - Azure Managed Disks | Microsoft Docs
description: Come convertire una macchina virtuale Linux da dischi non gestiti a dischi gestiti usando l'interfaccia della riga di comando di Azure 2.0 nel modello di distribuzione Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: rogarana
ms.openlocfilehash: a3a2bbc15dd94ef09755d34a20e69c97854416b3
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2018
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Convertire una macchina virtuale Linux da dischi non gestiti a dischi gestiti

Se sono presenti macchine virtuali (VM) Linux che usano dischi non gestiti, è possibile convertire le VM per l'uso di [Azure Managed Disks](../linux/managed-disks-overview.md). Questo processo consente di convertire sia il disco del sistema operativo che eventuali dischi dati collegati.

Questo articolo illustra come convertire le macchine virtuali usando l'interfaccia della riga di comando di Azure. Se è necessario installarla o aggiornarla, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Prima di iniziare
* Vedere le [domande frequenti sulla migrazione a Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Convertire VM a istanza singola
Questa sezione descrive come convertire i dischi delle macchine virtuali di Azure a istanza singola da non gestiti a gestiti. Se le macchine virtuali si trovano in un set di disponibilità, vedere la sezione successiva. È possibile usare questo processo per convertire le macchine virtuali da dischi non gestiti Premium (SDD) a dischi gestiti Premium o da dischi non gestiti standard (HDD) a dischi gestiti standard.

1. Deallocare la macchina virtuale con il comando [az vm deallocate](/cli/azure/vm#az_vm_deallocate). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Convertire la macchina virtuale per l'utilizzo di dischi gestiti con il comando [az vm convert](/cli/azure/vm#az_vm_convert). Il processo seguente converte la macchina virtuale denominata `myVM`, incluso il disco del sistema operativo ed eventuali dischi dati:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Avviare la macchina virtuale dopo la conversione in dischi gestiti con il comando [az vm start](/cli/azure/vm#az_vm_start). L'esempio seguente avvia la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Convertire VM in un set di disponibilità

Se le macchine virtuali che si desidera convertire in dischi gestiti si trovano in un set di disponibilità, è innanzitutto necessario convertire il set di disponibilità in un set di disponibilità gestito.

Tutte le macchine virtuali nel set di disponibilità devono essere deallocate prima di convertire il set di disponibilità. Pianificare la conversione di tutte le macchine virtuali per l'utilizzo di dischi gestiti dopo la conversione del set di disponibilità stesso in un set gestito. Avviare quindi tutte le macchine virtuali e continuare a usarle normalmente.

1. Elencare tutte le macchine virtuali in un set di disponibilità con il comando [az vm availability-set list](/cli/azure/vm/availability-set#az_vm_availability_set_list). L'esempio seguente elenca tutte le macchine virtuali nel set di disponibilità denominato `myAvailabilitySet` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Deallocare tutte le macchine virtuali con il comando [az vm deallocate](/cli/azure/vm#az_vm_deallocate). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Convertire il set di disponibilità con il comando [az vm availability-set convert](/cli/azure/vm/availability-set#az_vm_availability_set_convert). L'esempio seguente converte il set di disponibilità denominato `myAvailabilitySet` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Convertire tutte le macchine virtuali per l'utilizzo di dischi gestiti con il comando [az vm convert](/cli/azure/vm#az_vm_convert). Il processo seguente converte la macchina virtuale denominata `myVM`, incluso il disco del sistema operativo ed eventuali dischi dati:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Avviare tutte le macchine virtuali dopo la conversione in dischi gestiti con il comando [az vm start](/cli/azure/vm#az_vm_start). Nell'esempio seguente viene avviata la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle opzioni di archiviazione, vedere [Panoramica di Azure Managed Disks](../windows/managed-disks-overview.md).
