---
title: "Quickstart: Intentie ophalen met REST API's - LUIS"
description: In deze snelstartgids voor REST API gebruikt u een beschikbare openbare LUIS-app om de intentie van een gebruiker te bepalen aan de hand van beschrijvende tekst.
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 977348fcaf0cabf92268d71282222e1652321fac
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919268"
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
