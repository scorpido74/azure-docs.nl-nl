---
title: Uw project bijwerken naar de 3.0 API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het bijwerken van Custom Vision-projecten vanaf de vorige versie van de API naar de 3.0 API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647503"
---
# <a name="update-to-the-30-api"></a>Update naar de 3.0 API

Custom Vision heeft nu de algemene beschikbaarheid bereikt en heeft een API-update ondergaan.
Deze update bevat een paar nieuwe functies en, belangrijker nog, een paar baanbrekende wijzigingen:

* De Voorspelling-API is nu opgesplitst in twee op basis van het projecttype.
* De Vision AI Developer Kit (VAIDK) exportoptie vereist het maken van een project op een specifieke manier.
* Standaarditeraties zijn verwijderd ten gunste van een publicatie / onpublicatie van een benoemde iteratie.

In deze handleiding ziet u hoe u uw projecten bijwerken om te werken met de nieuwe API-versie. Zie de [releasenotes](release-notes.md) voor een volledige lijst met de wijzigingen.

## <a name="use-the-updated-prediction-api"></a>De bijgewerkte voorspellings-API gebruiken

De 2.x API's gebruikten dezelfde voorspellingsoproep voor zowel beeldclassificaties als objectdetectorprojecten. Beide projecttypen waren acceptabel voor de aanroepen **PredictImage** en **PredictImageUrl.** Vanaf 3.0 hebben we deze API gesplitst, zodat u het projecttype moet koppelen aan de aanroep:

* Gebruik **[ClassificerenAfbeelding](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** en **[ClassificerenImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** om voorspellingen voor beeldclassificatieprojecten te krijgen.
* Gebruik **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** en **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** om voorspellingen te krijgen voor objectdetectieprojecten.

## <a name="use-the-new-iteration-publishing-workflow"></a>De nieuwe iteratiepublicatiewerkstroom gebruiken

De 2.x API's gebruikten de standaarditeratie of een opgegeven iteratie-id om de iteratie te kiezen die moet worden gebruikt voor voorspelling. Vanaf 3.0 hebben we een publicatiestroom aangenomen waarbij u eerst een iteratie publiceert onder een bepaalde naam uit de trainings-API. Vervolgens geeft u de naam door aan de voorspellingsmethoden om op te geven welke iteratie u moet gebruiken.

> [!IMPORTANT]
> De 3.0 API's maken geen gebruik van de standaarditeratiefunctie. Totdat we de oudere API's hebben afnodigt, u de 2.x API's blijven gebruiken om een iteratie als standaardoptie te schakelen. Deze API's worden gedurende een bepaalde periode gehandhaafd en u de methode **[UpdateIteratie](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** aanroepen om een iteratie als standaard te markeren.

### <a name="publish-an-iteration"></a>Een iteratie publiceren

Zodra een iteratie is getraind, u deze beschikbaar maken voor voorspelling met behulp van de **[publishiteratiemethode.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** Als u een iteratie wilt publiceren, hebt u de voorspellingsbron-id nodig, die beschikbaar is op de instellingenpagina van de CustomVision-website.

![De pagina Met de informatiebron-ID van Custom Vision met de voorgestelde bron-id.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> U deze informatie ook ophalen uit de [Azure Portal](https://portal.azure.com) door naar de aangepaste visievoorspellingsbron te gaan en **Eigenschappen**te selecteren.

Zodra uw iteratie is gepubliceerd, kunnen apps deze gebruiken voor voorspelling door de naam op te geven in hun voorspellings-API-aanroep. Als u een iteratie niet beschikbaar wilt maken voor voorspellingsoproepen, gebruikt u de API **[voor publicatieherhaling.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)**

## <a name="next-steps"></a>Volgende stappen

* [Trainings-API-referentiedocumentatie (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Api-referentiedocumentatie voor voorspelling (REST)](https://go.microsoft.com/fwlink/?linkid=865445)