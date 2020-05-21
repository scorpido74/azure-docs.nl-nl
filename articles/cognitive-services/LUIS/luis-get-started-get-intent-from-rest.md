---
title: "Quick Start: profiteren van intenties met REST-Api's-LUIS"
description: Gebruik in deze REST API Snelstartgids een beschik bare open bare LUIS-app om de bedoeling van een gebruiker te bepalen op basis van de conversatie tekst.
ms.topic: quickstart
ms.date: 05/18/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 90d98e56e53e28991fb5aada9eab5a7e9c2e69c3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654341"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Quick Start: profiteren van de bedoeling van REST-Api's

In deze Snelstartgids gebruikt u een LUIS-app om de bedoeling van een gebruiker te bepalen op basis van de tekst van de conversatie. Verzend de voor waarde van de gebruiker als tekst naar het HTTP prediction-eind punt van de pizza-app. Op het eind punt past LUIS het model van de pizza-app toe om de tekst in de natuurlijke taal te analyseren, waardoor de algemene intentie kan worden bepaald en gegevens worden geëxtraheerd die relevant zijn voor het onderwerps domein van de app.

Deze snelstart gebruikt het REST-API-eindpunt. Zie voor meer informatie de [documentatie bij de eindpunt-API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

Voor dit artikel hebt u een gratis [LUIS](https://www.luis.ai)-account nodig.

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
