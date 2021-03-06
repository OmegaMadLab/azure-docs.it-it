---
title: Modello di consumo in Mappe di Azure | Microsoft Docs
description: Informazioni sul modello di consumo in Mappe di Azure
services: azure-maps
keywords: ''
author: subbarayudukamma
ms.author: skamma
ms.date: 5/8/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 146ea084c02bb3de0c74da79ca85021589207de8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="consumption-model"></a>Modello di utilizzo

Pianificazione percorso fornisce un set di parametri per una descrizione dettagliata del modello di consumo specifico del veicolo.
A seconda del valore di **vehicleEngineType**, sono supportati due modelli di consumo principali: _Combustione_ ed _Elettrico_. È un errore specificare parametri che appartengono a modelli diversi nella stessa richiesta.
Non è possibile usare il modello di consumo con i valori _bicicletta_ e _pedonale_ di **travelMode**.

## <a name="parameter-constraints-for-consumption-model"></a>Vincoli dei parametri per il modello di consumo

In entrambi i modelli di consumo, per specificare esplicitamente alcuni parametri è necessario specificarne anche altri. Le dipendenze sono le seguenti:

* Tutti i parametri richiedono che l'utente specifichi **constantSpeedConsumption**. È un errore specificare qualsiasi altro parametro del modello di consumo, ad eccezione di **vehicleWeight**, se non è specificato **constantSpeedConsumption***.
* **accelerationEfficiency** e **decelerationEfficiency** devono essere sempre specificati in coppia (ovvero entrambi o nessuno).
* Se vengono specificati **accelerationEfficiency** e **decelerationEfficiency**, il prodotto dei loro valori non deve essere maggiore di 1 per evitare il moto perpetuo.
* **uphillEfficiency** e **downhillEfficiency** devono essere sempre specificati in coppia (ovvero entrambi o nessuno).
* Se vengono specificati **uphillEfficiency** e **downhillEfficiency** , il prodotto dei loro valori non deve essere maggiore di 1 per evitare il moto perpetuo.
* Se l'utente specifica parametri di \***efficienza**, è necessario specificare anche **vehicleWeight**. Quando **vehicleEngineType** è _combustion_, è necessario specificare anche **fuelEnergyDensityInMJoulesPerLiter**.
* **maxChargeInkWh** e **currentChargeInkWh** devono essere sempre specificati in coppia (ovvero entrambi o nessuno).

> [!NOTE]
> Se viene specificato solo **constantSpeedConsumption**, per il calcolo dei consumi non vengono presi in considerazione altri aspetti correlati ai consumi, quali le pendenze e l'accelerazione del veicolo.

## <a name="combustion-consumption-model"></a>Modello consumo Combustione

Si usa il modello di consumo Combustione quando **vehicleEngineType** è impostato su _combustion_.
I parametri che appartengono a questo modello sono riportati di seguito. Vedere la sezione Parametri per una descrizione dettagliata.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Modello di consumo Elettrico

Si usa il modello di consumo Elettrico quando **vehicleEngineType** è impostato su _electric_.
I parametri che appartengono a questo modello sono riportati di seguito. Vedere la sezione Parametri per una descrizione dettagliata.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Valori sensibili dei parametri di consumo

Un particolare set di parametri di consumo può essere rifiutato anche se soddisfa tutti i requisiti espliciti sopraindicati. Ciò si verifica quando il valore di un determinato parametro, o una combinazione di valori di più parametri, può determinare volumi irragionevoli di valori di consumo. Se ciò accade, è molto probabile che ci sia un errore di input, in quanto tutti i valori sensibili dei parametri di consumo vengono generalmente presi in considerazione. Nel caso in cui un determinato set di parametri di consumo venga rifiutato, il messaggio di errore visualizzato conterrà una spiegazione testuale dei motivi.
Le descrizioni dettagliate dei parametri contengono esempi di valori sensibili per entrambi i modelli.
