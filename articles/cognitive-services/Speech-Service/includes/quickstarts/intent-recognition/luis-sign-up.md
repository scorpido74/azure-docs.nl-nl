---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a41c9dc888e85baf021712e3302da7ae6106db47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422243"
---
Als u de intentieherkenning snel wilt voltooien, moet u een LUIS-account en een project maken met behulp van de LUIS-voorbeeldportal. Voor deze quickstart is alleen een LUIS-abonnement vereist. Een *spraakserviceabonnement is niet* vereist.

Het eerste wat u moet doen is een LUIS-account en -app maken met behulp van de LUIS-voorbeeldportal. De LUIS-app die u maakt, gebruikt een vooraf gebouwd domein voor domotica, dat intents, entiteiten en voorbeelduitingen biedt. Wanneer u klaar bent, wordt er een LUIS-eindpunt uitgevoerd in de cloud die u aanroepen met de SpraakSDK. 

Volg deze instructies om uw LUIS-app te maken:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Snelstart: vooraf gebouwde domein-app bouwen<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Als je klaar bent, heb je vier dingen nodig:

* Opnieuw publiceren met **spraakpriming** ingeschakeld
* Uw **LUIS-primaire sleutel**
* Uw **LUIS-locatie**
* Uw **LUIS-app-id**

Hier vindt u deze informatie in de [LUIS preview portal:](https://preview.luis.ai/)

1. Selecteer in de LUIS-voorbeeldportal uw app en selecteer vervolgens de knop **Publiceren.**

2. Selecteer de **productiesleuf** als u `en-US` de optie **Aan-priming** inschakelt in de positie **Aan.** Selecteer vervolgens de knop **Publiceren.**

    > [!IMPORTANT]
    > **Spraakpriming** wordt ten zeerste aanbevolen omdat het de nauwkeurigheid van spraakherkenning zal verbeteren.

    > [!div class="mx-imgBorder"]
    > ![LUIS naar eindpunt publiceren](../../../media/luis/publish-app-popup.png)

3. Selecteer in de LUIS-voorbeeldportal **Beheren**en selecteer **vervolgens Azure Resources**. Op deze pagina vindt u uw LUIS-sleutel en locatie (soms aangeduid als _regio)._

   > [!div class="mx-imgBorder"]
   > ![LUIS-sleutel en -locatie](../../../media/luis/luis-key-region.png)

4. Nadat je je sleutel en locatie hebt, heb je de app-id nodig. Selecteer **Toepassingsinstellingen** : uw app-id is beschikbaar op deze pagina.

   > [!div class="mx-imgBorder"]
   > ![LUIS-app-id](../../../media/luis/luis-app-id.png)
