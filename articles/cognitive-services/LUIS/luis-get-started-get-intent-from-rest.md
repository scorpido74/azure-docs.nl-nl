---
title: "Quickstart: Intentie ophalen met REST API's - LUIS"
description: In deze snelstartgids voor REST API gebruikt u een beschikbare openbare LUIS-app om de intentie van een gebruiker te bepalen aan de hand van beschrijvende tekst.
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 0274cf1ac6a6d0cb37d2321f6fd02f98c784318f
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405264"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Quickstart: Intentie bepalen met REST API's

In deze quickstart gebruikt u een LUIS-app om de intentie van een gebruiker te bepalen aan de hand van beschrijvende tekst. Verzend de intentie van de gebruiker als tekst naar het HTTP-voorspellingseindpunt van de Pizza-app. Bij het eindpunt wordt via LUIS het model van de Pizza-app toegepast om de betekenis van tekst in natuurlijke taal te analyseren. Hiermee wordt de algehele intentie bepaald en worden gegevens geëxtraheerd die relevant zijn voor het onderwerpdomein van de app.

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
