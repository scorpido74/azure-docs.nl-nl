---
title: 'Quickstart: Een leertraject maken en gebruiken met SDK: Personalizer'
description: In deze quickstart ziet u hoe u uw Knowledge Base maakt en beheert met behulp van de client-SDK.
ms.topic: quickstart
ms.date: 07/30/2020
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 05657d350616502c3d32b500a32b394a748ab2f6
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461098"
---
# <a name="quickstart-personalizer-client-library"></a>Quickstart: Personalizer-clientbibliotheek

Geef gepersonaliseerde inhoud weer in deze quickstart met de Personalizer-service.

Aan de slag met de Personalizer-clientbibliotheek. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen.

 * Rank-API: hiermee wordt het beste item van de acties geselecteerd, op basis van realtime informatie die u over inhoud en context opgeeft.
 * Reward-API: u bepaalt de beloningsscore op basis van uw zakelijke behoeften en verzendt deze vervolgens naar Personalizer met deze API. Die score kan één waarde zijn, zoals 1 voor goed en 0 voor fout, of een algoritme dat u maakt op basis van uw zakelijke behoeften.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Hoe Personalizer werkt](how-personalizer-works.md)

* [Wat is Personalizer?](what-is-personalizer.md)
* [Waar kunt u Personalizer gebruiken?](where-can-you-use-personalizer.md)
* [Problemen oplossen](troubleshooting.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
