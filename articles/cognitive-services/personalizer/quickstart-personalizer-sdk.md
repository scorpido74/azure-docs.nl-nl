---
title: 'Quickstart: Een leertraject maken en gebruiken met SDK: Personalizer'
description: In deze quickstart ziet u hoe u uw Knowledge Base maakt en beheert met behulp van de Personalizer-clientbibliotheek.
ms.topic: quickstart
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personalizer, Azure personalizer, machine learning
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: dccdec0888f2968fb7089c4ff80c9338215de135
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055952"
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
> [Hoe werkt Personalizer?](how-personalizer-works.md)
> [Waar kunt u Personalizer gebruiken?](where-can-you-use-personalizer.md)
