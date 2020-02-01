---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: 58122066e65fbcb02b6b4333985785b219d3dbbd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900602"
---
U moet een LUIS-account en een project maken met behulp van de preview-portal van LUIS als u de Snelstartgids voor de intentie herkenning wilt volt ooien. Voor deze Quick start is alleen een LUIS-abonnement vereist. Een spraak service-abonnement *is niet* vereist.

Het eerste wat u moet doen, is het maken van een LUIS-account en-app met behulp van de LUIS preview Portal. De LUIS-app die u maakt, maakt gebruik van een vooraf gebouwd domein voor Start Automation, dat intenties, entiteiten en voor beeld uitingen biedt. Wanneer u klaar bent, hebt u een LUIS-eind punt dat wordt uitgevoerd in de cloud die u kunt aanroepen met de spraak-SDK. 

Volg deze instructies om uw LUIS-app te maken:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Snelstartgids: vooraf gebouwde domein-app bouwen<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Wanneer u klaar bent, hebt u vier dingen nodig:

* Opnieuw publiceren met **Speech gebeuren** in-/uitschakelen
* Uw primaire LUIS- **sleutel**
* Uw LUIS- **locatie**
* Uw LUIS **-App-ID**

Hier vindt u deze informatie in de [Luis preview-Portal](https://preview.luis.ai/):

1. Selecteer uw app in de preview-portal van LUIS en selecteer vervolgens de knop **publiceren** .

2. Selecteer de **productie** site als u **gebruikmaakt van `en-US`** de optie **Speech gebeuren** in-of uitschakelen. Selecteer vervolgens de knop **publiceren** .

    > [!IMPORTANT]
    > **Speech gebeuren** wordt sterk aanbevolen, omdat hiermee de nauw keurigheid van spraak herkenning wordt verbeterd.

    > [!div class="mx-imgBorder"]
    > ![publiceren LUIS naar eind punt](../../../media/luis/publish-app-popup.png)

3. Selecteer **beheren**in de preview-portal van Luis en selecteer vervolgens **Azure-resources**. Op deze pagina vindt u uw LUIS-sleutel en-locatie (ook wel _regio_genoemd).

   > [!div class="mx-imgBorder"]
   > ![LUIS-sleutel en-locatie](../../../media/luis/luis-key-region.png)

4. Nadat u uw sleutel en locatie hebt verkregen, hebt u de App-ID nodig. **Toepassings instellingen** selecteren: uw app-id is beschikbaar op deze pagina.

   > [!div class="mx-imgBorder"]
   > ![App-ID van LUIS](../../../media/luis/luis-app-id.png)
