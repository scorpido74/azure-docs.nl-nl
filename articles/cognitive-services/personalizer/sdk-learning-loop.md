---
title: 'Quickstart: Leerlus maken en gebruiken met SDK - Personalizer'
description: Deze quickstart laat u zien hoe u uw kennisbank maken en beheren met behulp van de client SDK.
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 7ebe22227b4323b2e6b1c3fc9ca31e171d1d97cd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77524866"
---
# <a name="quickstart-personalizer-client-library"></a>Snelstart: gepersonaliseerde clientbibliotheek

Geef gepersonaliseerde content in deze quickstart weer met de Personalizer-service.

Ga aan de slag met de personalizer clientbibliotheek. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit.

 * Rank API - Selecteert het beste item, uit inhouditems, op basis van realtime informatie die u verstrekt over inhoud en context.
 * Reward API - U bepaalt de beloningsscore op basis van uw bedrijfsbehoeften en stuurt deze vervolgens naar Personalizer met deze API. Die score kan een enkele waarde zijn, zoals 1 voor goed, en 0 voor slecht, of een algoritme dat u maakt op basis van uw zakelijke behoeften.

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

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Hoe Personalizer werkt](how-personalizer-works.md)

* [Wat is Personalizer?](what-is-personalizer.md)
* [Waar kunt u Personalizer gebruiken?](where-can-you-use-personalizer.md)
* [Probleemoplossing](troubleshooting.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)
