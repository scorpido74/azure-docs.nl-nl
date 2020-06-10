---
title: 'Snelstartgids: Learning Lusbewerking maken en gebruiken met SDK-Personaler'
description: In deze Quick start ziet u hoe u uw Knowledge Base maakt en beheert met behulp van de client-SDK.
ms.topic: quickstart
ms.date: 04/27/2020
ms.custom: tracking-python
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: d4706e93470134751ade5f16e46a4323fd690f94
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608635"
---
# <a name="quickstart-personalizer-client-library"></a>Snelstartgids: client bibliotheek voor personalisatie

Persoonlijke inhoud in deze Quick Start weer geven met de Personaler service.

Aan de slag met de Personaler-client bibliotheek. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen.

 * Rank API: selecteert het beste item, van acties, op basis van real-time gegevens die u verstrekt over inhoud en context.
 * Belonings-API: u bepaalt de belonings Score op basis van uw bedrijfs behoeften en verzendt deze vervolgens naar Personaler met deze API. Deze score kan één waarde hebben, zoals 1 voor goed, en 0 voor slecht, of een algoritme die u maakt op basis van uw bedrijfs behoeften.

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

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Hoe Personalizer werkt](how-personalizer-works.md)

* [Wat is Personalizer?](what-is-personalizer.md)
* [Waar kunt u Personalizer gebruiken?](where-can-you-use-personalizer.md)
* [Problemen oplossen](troubleshooting.md)
* De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
