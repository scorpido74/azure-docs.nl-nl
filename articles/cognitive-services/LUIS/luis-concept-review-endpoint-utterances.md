---
title: Uiting van de gebruiker controleren - LUIS
description: Met actief leren u eindpuntuitingen controleren voor de juiste intentie en entiteit. LUIS kiest eindpuntuitingen waarvan het niet zeker is.
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 8d267fc441dc2cbf7f8ae3746486d5e7be55f135
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546854"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Concepten voor het inschakelen van actief leren door eindpuntuitingen te bekijken
Actief leren is een van de drie strategieën om de nauwkeurigheid van voorspellingen te verbeteren en het gemakkelijkst te implementeren. Met actief leren u eindpuntuitingen controleren voor de juiste intentie en entiteit. LUIS kiest eindpuntuitingen waarvan het niet zeker is.

## <a name="what-is-active-learning"></a>Wat is actief leren
Actief leren is een proces in twee stappen. Eerst selecteert LUIS uitingen die het ontvangt op het eindpunt van de app die validatie nodig hebben. De tweede stap wordt uitgevoerd door de eigenaar of medewerker van de app om de geselecteerde uitingen ter [controle](luis-how-to-review-endpoint-utterances.md)te valideren, inclusief de juiste intentie en entiteiten binnen de intentie. Nadat u de uitingen hebt bekeken, traint en publiceert u de app opnieuw.

## <a name="which-utterances-are-on-the-review-list"></a>Welke uitingen op de beoordelingslijst staan
LUIS voegt uitingen toe aan de beoordelingslijst wanneer de bovenste vuurintentie een lage score heeft of de scores van de bovenste twee intents te dicht bij elkaar liggen.

## <a name="single-pool-for-utterances-per-app"></a>Eén groep voor uitingen per app
De lijst **Eindpuntuitingen controleren** wordt niet gewijzigd op basis van de versie. Er is één groep uitingen om te beoordelen, ongeacht welke versie van de uiting u actief bewerkt of welke versie van de app wordt gepubliceerd op het eindpunt.

In de [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9)is de naam van de versie vereist en moet deze in de toepassing bestaan, maar wordt deze niet gebruikt na die validatie. De revisieuitingen zijn van toepassing op een volledige toepassing. Als u uitingen uit één _versie_verwijdert, worden alle versies beïnvloed.

## <a name="where-are-the-utterances-from"></a>Waar zijn de uitingen van
Eindpuntuitingen worden overgenomen uit query's van eindgebruikers op het HTTP-eindpunt van de toepassing. Als uw app nog niet is gepubliceerd of nog geen hits heeft ontvangen, u geen uitingen bekijken. Als er geen eindpunttreffers worden ontvangen voor een specifieke intentie of entiteit, hebt u geen uitingen om deze te controleren.

## <a name="schedule-review-periodically"></a>Periodiek controleren plannen
Het beoordelen van voorgestelde uitingen hoeft niet elke dag te worden gedaan, maar moet deel uitmaken van uw reguliere onderhoud van LUIS.

## <a name="delete-review-items-programmatically"></a>Controleitems programmatisch verwijderen
Gebruik de API **[voor niet-gelabelde uitingen verwijderen.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** Een back-up van deze uitingen maken voordat u deze verwijderd door **[het exporteren van de logbestanden](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="enable-active-learning"></a>Actief leren inschakelen

Als u actief leren wilt inschakelen, moet u gebruikersquery's registreren. Dit wordt bereikt door de [eindpuntquery](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) aan te roepen met de parameter en waarde van de `log=true` querytekenreeks.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [controleren van](luis-how-to-review-endpoint-utterances.md) eindpuntuitingen
