---
title: Personalizzare l'immagine del contenitore usata per la distribuzione dei modelli di Azure ML | Microsoft Docs
description: Questo articolo descrive come personalizzare un'immagine del contenitore per i modelli di Azure Machine Learning
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: mldocs, raymondl
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ms.openlocfilehash: 8ff2bca405bbd8faeaa4527f493804950fced6ce
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Personalizzare l'immagine del contenitore usata per i modelli di Azure ML

Questo articolo descrive come personalizzare un'immagine del contenitore per i modelli di Azure Machine Learning.  Azure Machine Learning Workbench usa i contenitori per distribuire i modelli di apprendimento automatico. I modelli vengono distribuiti con le relative dipendenze e Azure Machine Learning crea un'immagine dal modello, dalle dipendenze e dai file associati.

## <a name="how-to-customize-the-docker-image"></a>Come personalizzare l'immagine Docker
Personalizzare l'immagine Docker distribuita da Azure ML usando:

1. Un file `dependencies.yml`: per gestire le dipendenze installabili da [PyPi]( https://pypi.python.org/pypi), è possibile usare il file `conda_dependencies.yml` dal progetto Workbench o crearne uno personale. Questo è l'approccio consigliato per installare le dipendenze di Python che sono installabili con Pip.

   Esempio di comando dell'interfaccia della riga di comando:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   Esempio del file conda_dependencies: 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. Un file di passaggi Docker: con questa opzione è possibile personalizzare l'immagine distribuita installando le dipendenze che non possono essere installate da PyPi. 

   Il file deve includere i passaggi di installazione di Docker, ad esempio DockerFile. I comandi seguenti sono consentiti nel file: 

    RUN, ENV, ARG, LABEL, EXPOSE

   Esempio di comando dell'interfaccia della riga di comando:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   I comandi Image, Manifest e Service accettano il flag docker-file.

   Esempio del file di passaggi Docker:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> L'immagine di base per i contenitori di Azure ML è Ubuntu e non può essere modificata. Se si specifica un'immagine di base diversa, questa verrà ignorata.

## <a name="next-steps"></a>Passaggi successivi
Ora che l'immagine del contenitore è stata personalizzata, è possibile distribuirla in un cluster per usarla su larga scala.  Per informazioni dettagliate sull'impostazione di un cluster per la distribuzione dei servizi Web, vedere [Model Management Configuration](deployment-setup-configuration.md) (Configurazione della Gestione modelli). 
