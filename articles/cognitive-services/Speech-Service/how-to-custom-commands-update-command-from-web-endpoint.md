---
title: Een opdracht bijwerken vanuit een webeindpunt
titleSuffix: Azure Cognitive Services
description: een opdracht bijwerken vanuit een webeindpunt
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571198"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Een opdracht bijwerken vanuit een webeindpunt

Als voor uw client toepassing de status van een doorlopende opdracht moet worden bijgewerkt zonder spraak invoer, kunt u een aanroep naar een webeindpunt gebruiken om de opdracht bij te werken.

In dit artikel wordt beschreven hoe u een doorlopende opdracht bijwerkt vanuit een webeindpunt.

## <a name="prerequisites"></a>Vereisten
> [!div class = "checklist"]
> * Een eerder [gemaakte aangepaste opdrachten-app](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Een Azure-functie maken 

Voor dit voor beeld hebben we een HTTP-Triggered [Azure-functie](https://docs.microsoft.com/azure/azure-functions/) nodig die de volgende invoer (of een subset van deze invoer) ondersteunt.

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Hiermee worden de belangrijkste kenmerken van deze invoer gecontroleerd.

| Kenmerk | Uitleg |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | ' conversationId ' is de unieke id van de conversatie. deze id kan worden gegenereerd op basis van de client-app. |
| **currentCommand** | ' currentCommand ' is de opdracht die momenteel actief is in de conversatie. |
| **name** | ' naam ' is de naam van de opdracht en ' para meters ' is een toewijzing met de huidige waarden van de para meters. |
| **currentGlobalParameters** | ' currentGlobalParameters ' is ook een toewijzing zoals ' para meters ', maar wordt gebruikt voor algemene para meters. |

De uitvoer van de Azure-functie moet de volgende indeling ondersteunen.

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

U kunt deze indeling herkennen omdat deze hetzelfde is als de naam die wordt gebruikt bij het [bijwerken van een opdracht van de client](./how-to-custom-commands-update-command-from-client.md). 

Maak nu een Azure-functie op basis van NodeJS en kopieer-plak deze code

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Wanneer we deze Azure-functie aanroepen vanuit aangepaste opdrachten, verzenden we de huidige waarden van de conversatie en retour neren we de para meters die u wilt bijwerken of als u de huidige opdracht wilt annuleren.

## <a name="update-the-existing-custom-commands-app"></a>De bestaande aangepaste opdrachten-app bijwerken

We gaan nu de Azure-functie koppelen aan de bestaande aangepaste opdrachten voor apps.

1. Voeg een nieuwe opdracht met de naam IncrementCounter.
1. Voeg slechts één voorbeeld zin toe met de waarde ' verhogen '.
1. Voeg een nieuwe para meter met de naam counter (dezelfde naam zoals opgegeven in de Azure-functie) van het type getal met de standaard waarde 0 toe.
1. Voeg een nieuw webeind punt toe met de naam IncrementEndpoint met de URL van uw Azure-functie en met externe updates ingeschakeld.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Webeindpunt instellen met externe updates":::
1. Maak een nieuwe interactie regel met de naam ' IncrementRule ' en voeg een aanroep-Web-eindpunt actie toe.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Regel voor incrementeel verhogen":::
1. In de actie configuratie selecteert u de IncrementEndpoint, configureert u op geslaagd om spraak reacties te verzenden met de waarde van Counter en bij fout met een fout bericht.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Eind punt voor item aanroepen instellen":::
1. De status na uitvoering van de regel instellen om te wachten op invoer van de gebruiker

## <a name="test-it"></a>Testen

1. Uw app opslaan en trainen
1. Klik op testen
1. Verzend een paar keer ' verhogen ' (dit is de voorbeeld zin voor de opdracht IncrementCounter)
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Voor beeld van een oplopend item":::

U ziet hoe de waarde van de teller-para meter op elke beurt wordt verhoogd door de Azure-functie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een CI/CD-proces inschakelen voor uw aangepaste opdrachten-toepassing](./how-to-custom-commands-deploy-cicd.md)