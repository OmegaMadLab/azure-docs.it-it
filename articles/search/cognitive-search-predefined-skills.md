---
title: Estrazione dei dati predefinita, linguaggio naturale, competenze di elaborazione dell'immagine (Ricerca di Azure) | Microsoft Docs
description: Estrazione dei dati, linguaggio naturale, competenze cognitive di elaborazione delle immagini, semantica e struttura per il contenuto non elaborato in una pipeline di Ricerca di Azure.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 870cf9629c7af8faee0ce5709199b64910b27ffb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786700"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Competenze predefinite per l'arricchimento del contenuto (Ricerca di Azure)

Questo articolo illustra i concetti relativi alle competenze cognitive fornite con Ricerca cognitiva. Una *competenza cognitiva* è un'operazione che trasforma in qualche modo il contenuto. Spesso si tratta di un componente che consente di estrarre dati o dedurre la struttura e pertanto aumenta il riconoscimento dei dati di input. L'output è quasi sempre basato su testo. Un *set di competenze* è una raccolta di competenze che definiscono la pipeline di arricchimento. 

## <a name="predefined-skills"></a>Competenze predefinite

Diverse competenze sono flessibili in relazione a ciò che usano o producono. In generale, la maggior parte delle competenze sono basate su modelli con training preliminare, ovvero di cui non è possibile eseguire il training usando i propri dati di training. Per informazioni sulla creazione di una competenza personalizzata, vedere [Come definire un'interfaccia personalizzata](cognitive-search-custom-skill-interface.md) ed [Esempio: creazione di una competenza personalizzata](cognitive-search-create-custom-skill-example.md). Nella tabella seguente sono enumerate e descritte le competenze fornite da Microsoft. 

| Competenza | DESCRIZIONE |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Questa competenza usa un modello con training preliminare per rilevare le frasi importanti in base alla posizione del termine, alle regole linguistiche, alla prossimità ad altri termini e a quanto il termine è inusuale all'interno dei dati di origine. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Questa competenza usa un modello con training preliminare per rilevare la lingua usata (un ID lingua per ogni documento). Quando vengono usate più lingue all'interno dei segmenti dello stesso testo, l'output è l'identificatore LCID della lingua usata prevalentemente.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | Consolida il testo da una raccolta di campi in un unico campo.  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | Questa competenza usa un modello con training preliminare per stabilire le entità per un set fisso di categorie: persone, posizione, organizzazione. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Questa competenza usa un modello con training preliminare per assegnare un punteggio positivo o negativo della valutazione in base al record. Il valore è compreso tra 0 e 1. Si verificano punteggi neutri per entrambi i casi null quando la valutazione non può essere rilevata e per il testo che viene considerato neutro.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Suddivide il testo in più pagine in modo che sia possibile arricchire o aumentare il contenuto in modo incrementale. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Questa competenza usa un algoritmo di rilevamento dell'immagine per identificare il contenuto di un'immagine e generare una descrizione di testo. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Riconoscimento ottico dei caratteri. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Esegue il mapping di output a un tipo complesso (un tipo di dati multiparte, che può essere usato per un nome completo, un indirizzo su più righe o una combinazione di cognome e ID personale.) |

## <a name="see-also"></a>Vedere anche 

+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Definizione dell'interfaccia delle competenze personalizzate](cognitive-search-custom-skill-interface.md)
+ [Esercitazione: indicizzazione arricchita con ricerca cognitiva](cognitive-search-tutorial-blob.md)