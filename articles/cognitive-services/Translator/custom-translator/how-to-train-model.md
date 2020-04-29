---
title: Een model trainen-aangepaste Translator
titleSuffix: Azure Cognitive Services
description: Trainings een model is een belang rijke stap bij het bouwen van een Vertaal model. De training vindt plaats op basis van documenten die u voor die trainingen selecteert.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68595682"
---
# <a name="train-a-model"></a>Een model trainen

Training a model is de belang rijke stap bij het bouwen van een Vertaal model, omdat zonder trainingen geen training kan worden gebouwd. De training vindt plaats op basis van documenten die u voor de trainingen selecteert.

Een model trainen:

1.  Selecteer het project waar u een model wilt bouwen.

2.  Op het tabblad gegevens voor het project worden alle relevante documenten voor het taal paar van het project weer gegeven. Selecteer hand matig de documenten die u wilt gebruiken voor het trainen van uw model. U kunt in dit scherm trainings-, afstemmings-en test documenten selecteren. U hoeft alleen de Trainingsset te selecteren en aangepaste Translator te hebben om de afstemmings-en test sets voor u te maken.

    -  Document naam: naam van het document.

    -  Koppelen: als dit document een parallel-of Monolingual-document is. Monolingual documenten worden momenteel niet ondersteund voor training.

    -  Document type: kan training, afstemming, testen of woorden lijst zijn.

    -  Taal paar: hier worden de bron-en doel taal voor het project weer gegeven.

    -  Bron zinnen: toont het aantal zinnen dat is geëxtraheerd uit het bron bestand.

    -  Doel zinnen: toont het aantal zinnen dat is geëxtraheerd uit het doel bestand.

    ![Model trainen](media/how-to/how-to-train-model.png)

3.  Klik op de knop trainen.

4.  Geef in het dialoog venster een naam op voor uw model.

5.  Klik op model trainen.

    ![Dialoog venster voor Train model](media/how-to/how-to-train-model-2.png)

6.  De training wordt door aangepast Translator verzonden en de status van de training wordt weer gegeven op het tabblad modellen.

    ![Model pagina Train](media/how-to/how-to-train-model-3.png)

>[!Note]
>Custom Translator ondersteunt tien gelijktijdige trainingen binnen een werk ruimte op elk gewenst moment.


## <a name="edit-a-model"></a>Een model bewerken

U kunt een model bewerken met behulp van de koppeling bewerken op de pagina model Details.

1.  Klik op het potlood pictogram.

    ![Model bewerken](media/how-to/how-to-edit-model.png)

2.  In het dialoog venster wijzigen,

    1.  Model naam (vereist): Geef uw model een zinvolle naam.

        ![Meer dialoog venster bewerken](media/how-to/how-to-edit-model-dialog.png)

3.  Klik op Opslaan.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over het weer geven van model Details](how-to-view-model-details.md).
