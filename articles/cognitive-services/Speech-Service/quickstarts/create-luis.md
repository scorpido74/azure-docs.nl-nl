---
title: 'Snelstart: een LUIS-toets maken'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u een LUIS-toepassing maakt en een sleutel krijgt.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 5b1a5ac4867379457d161d07f4f4f2fc2d8ee6c3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77119599"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Snelstart: een LUIS-eindpuntsleutel krijgen

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de volgende items hebt voordat u met deze zelfstudie begint:

* Een LUIS-account. U kunt een gratis account aanvragen via het [LUIS portal](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>LUIS en spraakherkenning

LUIS kan worden geïntegreerd met de Speech-service voor het herkennen van intenties van spraak. U hebt geen abonnement op de Speech-service nodig, alleen op LUIS.

LUIS gebruikt drie soorten sleutels:

|Type sleutel|Doel|
|--------|-------|
|Ontwerpen|Hiermee u LUIS-apps programmatisch maken en wijzigen|
|Starter|Hiermee u uw LUIS-toepassing testen met alleen tekst|
|Eindpunt |Geeft toegang tot een bepaalde LUIS-app een vergunning|

Voor deze zelfstudie hebt u het type eindpuntsleutel nodig. De zelfstudie maakt gebruik van het voorbeeld Luis-app Voor domotica, die u maken door de [app Vooraf gebouwde app Voor domotica](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) gebruiken snel te volgen. Als u zelf een LUIS-app hebt gemaakt, u deze gebruiken.

Wanneer u een LUIS-app maakt, genereert LUIS automatisch een startsleutel, zodat u de app testen met tekstquery's. Deze sleutel maakt de integratie van de spraakservice niet mogelijk en werkt niet met deze zelfstudie. Maak een LUIS-bron in het Azure-dashboard en wijs deze toe aan de LUIS-app. U kunt de gratis versie van het abonnement gebruiken voor deze zelfstudie.

Nadat u de LUIS-bron in het Azure-dashboard hebt gemaakt, logt u in op de [LUIS-portal,](https://www.luis.ai/home)kiest u uw toepassing op de pagina **Mijn apps** en schakelt u over naar de pagina **Beheren van** de app. Selecteer ten slotte **Toetsen en Eindpunten** in de zijbalk.

![Instellingen voor sleutels en eindpunten in de LUIS-portal](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Ga als een op de pagina **Toetsen en Eindpuntinstellingen:**

1. Schuif omlaag naar de sectie **Resources en sleutels** en selecteer Resource **toewijzen**.
1. Breng in het dialoogvenster **Een sleutel toewijzen aan uw app** de volgende wijzigingen aan:

   * Kies **Onder Tenant**de optie **Microsoft**.
   * Kies **onder Abonnementsnaam**het Azure-abonnement dat de LUIS-bron bevat die u wilt gebruiken.
   * Kies **onder Sleutel**de LUIS-bron die u met de app wilt gebruiken.

   Na korte tijd wordt het nieuwe abonnement weergegeven in de tabel onderaan de pagina.

1. Selecteer het pictogram naast een sleutel om het naar het klembord te kopiëren. (U kunt beide sleutels gebruiken.)

![Abonnementssleutels voor LUIS-app](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Intents herkennen](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
