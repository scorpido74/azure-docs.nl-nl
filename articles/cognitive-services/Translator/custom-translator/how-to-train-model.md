---
title: Train een model - Custom Translator
titleSuffix: Azure Cognitive Services
description: Het trainen van een model is een belangrijke stap bij het bouwen van een vertaalmodel. Training gebeurt op basis van documenten die u voor die trainingen selecteert.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595682"
---
# <a name="train-a-model"></a>Een model trainen

Het trainen van een model is de belangrijke stap om een vertaalmodel te bouwen, want zonder een opleiding kan model niet worden gebouwd. Training gebeurt op basis van documenten die u selecteert voor de trainingen.

Een model trainen:

1.  Selecteer het project waar u een model wilt maken.

2.  Op het tabblad Gegevens voor het project worden alle relevante documenten voor het projecttaalpaar weergegeven. Selecteer handmatig de documenten die u wilt gebruiken om uw model te trainen. U trainings-, tuning- en testdocumenten selecteren op dit scherm. Ook selecteert u gewoon de trainingsset en laat Custom Translator de tuning- en testsets voor u maken.

    -  Documentnaam: Naam van het document.

    -  Koppeling: als dit document een parallel of eentalig document is. Eentalige documenten worden momenteel niet ondersteund voor training.

    -  Documenttype: kan training, tuning, testen of woordenboek zijn.

    -  Taalpaar: hiermee wordt de bron- en doeltaal voor het project weergegeven.

    -  Bronzinnen: geeft het aantal zinnen weer dat uit het bronbestand wordt gehaald.

    -  Doelzinnen: geeft het aantal zinnen weer dat uit het doelbestand wordt gehaald.

    ![Model trainen](media/how-to/how-to-train-model.png)

3.  Klik op de knop Trainen.

4.  Geef in het dialoogvenster een naam voor uw model op.

5.  Klik op Treinmodel.

    ![Dialoogvenster Treinmodel](media/how-to/how-to-train-model-2.png)

6.  Custom Translator zal de training indienen en de status van de training weergeven op het tabblad modellen.

    ![Treinmodelpagina](media/how-to/how-to-train-model-3.png)

>[!Note]
>Custom Translator ondersteunt 10 gelijktijdige trainingen binnen een werkruimte op elk moment.


## <a name="edit-a-model"></a>Een model bewerken

U een model bewerken via de koppeling Bewerken op de pagina Modeldetails.

1.  Klik op het potloodpictogram.

    ![Model bewerken](media/how-to/how-to-edit-model.png)

2.  In de dialoogvensterwijziging

    1.  Modelnaam (vereist): Geef uw model een betekenisvolle naam.

        ![Dialoogvenster meer bewerken](media/how-to/how-to-edit-model-dialog.png)

3.  Klik op Opslaan.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het weergeven van modeldetails](how-to-view-model-details.md).
