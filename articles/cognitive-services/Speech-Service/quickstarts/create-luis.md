---
title: 'Quick Start: een LUIS-sleutel maken'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start leert u hoe u een LUIS-toepassing maakt en een sleutel krijgt.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: wolfma
ms.openlocfilehash: 23f0b459b67088518375fbb4fd0b106da3aaf57c
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815841"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Quick Start: een LUIS-eindpunt sleutel ophalen

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de volgende items hebt voordat u met deze zelf studie begint:

* Een LUIS-account. U kunt een gratis account aanvragen via het [LUIS portal](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>LUIS en spraakherkenning

LUIS kan worden geïntegreerd met de Speech-service voor het herkennen van intenties van spraak. U hebt geen abonnement op de Speech-service nodig, alleen op LUIS.

LUIS maakt gebruik van drie soorten sleutels:

|Type sleutel|Doel|
|--------|-------|
|Ontwerpen|Hiermee kunt u LUIS-apps programmatisch maken en wijzigen|
|Starter|Hiermee kunt u uw LUIS-toepassing testen met alleen tekst|
|Eindpunt |Hiermee wordt toegang tot een bepaalde LUIS-app geautoriseerd|

Voor deze zelf studie hebt u het type eindpunt sleutel nodig. In de zelf studie wordt gebruikgemaakt van de voor beeld-app Home Automation LUIS, die u kunt maken met [behulp van vooraf ontwikkelde Start Automation-apps](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) Als u zelf een LUIS-app hebt gemaakt, kunt u deze gebruiken in plaats daarvan.

Wanneer u een LUIS-app maakt, wordt door LUIS automatisch een start sleutel gegenereerd zodat u de app kunt testen met behulp van tekst query's. Met deze sleutel wordt de integratie van de spraak service niet ingeschakeld en werkt deze niet met deze zelf studie. Maak een LUIS-resource in het Azure-dash board en wijs deze toe aan de LUIS-app. U kunt de gratis versie van het abonnement gebruiken voor deze zelfstudie.

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
