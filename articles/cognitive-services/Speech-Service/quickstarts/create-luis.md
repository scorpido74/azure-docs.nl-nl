---
title: 'Quick Start: een LUIS-sleutel maken'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie leert u hoe u snel een LUIS-toepassing kunt maken.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: 95becb2574a45536610aef87f8045b3dad5a5003
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504079"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Quick Start: een LUIS-eindpunt sleutel ophalen

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de volgende items hebt voordat u met deze zelf studie begint:

* Een LUIS-account. U kunt een gratis account aanvragen via het [LUIS portal](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>LUIS en spraakherkenning

LUIS integreert met de spraak Services om intenties van spraak te herkennen. U hebt geen spraak Services-abonnement nodig, alleen LUIS.

LUIS maakt gebruik van drie soorten sleutels:

|Type sleutel|Doel|
|--------|-------|
|Ontwerpen|Hiermee kunt u LUIS-apps programmatisch maken en wijzigen|
|Starter|Hiermee kunt u uw LUIS-toepassing testen met alleen tekst|
|Eindpunt |Hiermee wordt toegang tot een bepaalde LUIS-app geautoriseerd|

Voor deze zelf studie hebt u het type eindpunt sleutel nodig. In de zelf studie wordt gebruikgemaakt van de voor beeld-app Home Automation LUIS, die u kunt maken met [behulp van vooraf ontwikkelde Start Automation-apps](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) Als u zelf een LUIS-app hebt gemaakt, kunt u deze gebruiken in plaats daarvan.

Wanneer u een LUIS-app maakt, wordt door LUIS automatisch een start sleutel gegenereerd zodat u de app kunt testen met behulp van tekst query's. Deze sleutel biedt geen ondersteuning voor spraak Services en werkt niet met deze zelf studie. Maak een LUIS-resource in het Azure-dash board en wijs deze toe aan de LUIS-app. U kunt de gratis versie van het abonnement gebruiken voor deze zelfstudie.

Nadat u de LUIS-resource hebt gemaakt in het Azure-dash board, meldt u zich aan bij de [Luis-Portal](https://www.luis.ai/home), kiest u uw toepassing op de pagina **mijn apps** en gaat u naar de pagina **beheren** van de app. Ten slotte selecteert u de **toetsen en eind punten** in de zijbalk.

![Instellingen voor sleutels en eindpunten in de LUIS-portal](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Op de pagina **sleutels en eindpunt instellingen** :

1. Schuif omlaag naar de sectie **resources en sleutels** en selecteer **resource toewijzen**.
1. Breng in het dialoog venster **een sleutel toewijzen aan uw app** de volgende wijzigingen aan:

   * Onder **Tenant**kiest u **micro soft**.
   * Kies onder **abonnements naam**het Azure-abonnement met de Luis-resource die u wilt gebruiken.
   * Kies onder **sleutel**de Luis-resource die u wilt gebruiken met de app.

   Na korte tijd wordt het nieuwe abonnement weergegeven in de tabel onderaan de pagina. 

1. Selecteer het pictogram naast een sleutel om het naar het klem bord te kopiëren. (U kunt beide sleutels gebruiken.)

![Abonnementssleutels voor LUIS-app](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Intenties herkennen](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
