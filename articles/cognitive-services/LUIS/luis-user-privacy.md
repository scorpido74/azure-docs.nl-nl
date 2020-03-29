---
title: Gegevens exporteren & verwijderen - LUIS
titleSuffix: Azure Cognitive Services
description: U hebt volledige controle over het bekijken, exporteren en verwijderen van hun gegevens. Verwijder klantgegevens om privacy en naleving te garanderen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273367"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Uw klantgegevens exporteren en verwijderen in Language Understanding (LUIS) in Cognitive Services

Verwijder klantgegevens om privacy en naleving te garanderen.

## <a name="summary-of-customer-data-request-features"></a>Overzicht van de functies voor het aanvragen van klantgegevens
Language Understanding Intelligent Service (LUIS) behoudt klantinhoud om de service te bedienen, maar de LUIS-gebruiker heeft volledige controle over het bekijken, exporteren en verwijderen van zijn gegevens. Dit kan via de [LUIS-webportal](luis-reference-regions.md) of de [LUIS Authoring (ook bekend als Programmatic) API's.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Klantinhoud wordt versleuteld opgeslagen in de regionale Azure-opslag van Microsoft en bevat:

- Inhoud van het gebruikersaccount verzameld bij registratie
- Trainingsgegevens die nodig zijn om de modellen te bouwen
- Logged gebruikersquery's die worden gebruikt door [actief leren](luis-concept-review-endpoint-utterances.md) om het model te verbeteren
  - Gebruikers kunnen querylogboekregistratie uitschakelen `&log=false` door aan het verzoek te worden toegevoegd, details [hier](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Klantgegevens verwijderen
LUIS-gebruikers hebben de volledige controle om gebruikersinhoud te verwijderen, hetzij via de LUIS-webportal of de LUIS Authoring (ook bekend als Programmatic) API's. In de volgende tabel worden koppelingen weergegeven die helpen bij beide:

| | **Gebruikersaccount** | **Toepassing** | **Voorbeeld utterance(s)** | **Query's voor eindgebruikers** |
| --- | --- | --- | --- | --- |
| **Portal** | [Koppeling](luis-concept-data-storage.md#delete-an-account) | [Koppeling](luis-how-to-start-new-app.md#delete-app) | [Koppeling](luis-concept-data-storage.md#utterances-in-an-intent) | [Actieve leeruitingen](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Aangemelde uitingen](luis-concept-data-storage.md#disable-logging-utterances) |
| **API's** | [Koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Klantgegevens exporteren
LUIS-gebruikers hebben volledige controle om de gegevens op de portal te bekijken, maar deze moeten worden geëxporteerd via de LUIS Authoring (ook bekend als Programmatic) API's. In de volgende tabel worden koppelingen weergegeven die helpen bij het exporteren van gegevens via de API's voor luis-authoring (ook bekend als Programmatic):

| | **Gebruikersaccount** | **Toepassing** | **Utterance(s)** | **Query's voor eindgebruikers** |
| --- | --- | --- | --- | --- |
| **API's** | [Koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Locatie van actief leren

Om [actief leren](luis-how-to-review-endpoint-utterances.md#enable-active-learning)in te schakelen, worden de aangemelde uitingen van gebruikers die zijn ontvangen op de gepubliceerde LUIS-eindpunten, opgeslagen in de volgende Azure-regio's:

* [Europa](#europe)
* [Australië](#australia)
* [Verenigde Staten](#united-states)

Met uitzondering van actieve leergegevens (hieronder beschreven) volgt LUIS de [praktijken voor gegevensopslag voor regionale diensten](https://azuredatacentermap.azurewebsites.net/).

### <a name="europe"></a>Europa

De [eu.luis.ai](https://eu.luis.ai) portal en Europe Authoring (ook bekend als Programmatic API's) worden gehost in de Europese geografie van Azure. De eu.luis.ai portal en Europe Authoring (ook bekend als Programmatic API's) ondersteunen de implementatie van eindpunten naar de volgende Azure-regio's:

* Europa
* Frankrijk
* Verenigd Koninkrijk

Bij het implementeren naar deze Azure-regio's worden de uitingen die door het eindpunt van eindgebruikers van uw app worden ontvangen, opgeslagen in de regio Europa van Azure voor actief leren. U actief leren uitschakelen, zie [Actief leren uitschakelen](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Zie [Uitingen verwijderen](luis-how-to-review-endpoint-utterances.md#delete-utterance)als u opgeslagen uitingen wilt beheren.

### <a name="australia"></a>Australië

De [au.luis.ai](https://au.luis.ai) portal en Australia Authoring (ook bekend als Programmatic API's) worden gehost in azure's Australië geografie. De au.luis.ai portal en Australia Authoring (ook bekend als Programmatic API's) ondersteunen de implementatie van eindpunten naar de volgende Azure-regio's:

* Australië

Bij het implementeren naar deze Azure-regio's worden de uitingen die door het eindpunt van eindgebruikers van uw app worden ontvangen, opgeslagen in de geografie van Azure in Australië voor actief leren. U actief leren uitschakelen, zie [Actief leren uitschakelen](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Zie [Uitingen verwijderen](luis-how-to-review-endpoint-utterances.md#delete-utterance)als u opgeslagen uitingen wilt beheren.

### <a name="united-states"></a>Verenigde Staten

De [luis.ai](https://www.luis.ai) portal en United States Authoring (ook bekend als Programmatic API's) worden gehost in de Geografie van Azure in de Verenigde Staten. De luis.ai portal en United States Authoring (ook bekend als Programmatic API's) ondersteunen de implementatie van eindpunten naar de volgende Azure-regio's:

* Azure-regio's worden niet ondersteund door de europese of Australische ontwerpregio's

Bij het implementeren naar deze Azure-regio's worden de uitingen die door het eindpunt van eindgebruikers van uw app worden ontvangen, opgeslagen in de geografie van Azure in de Verenigde Staten voor actief leren. U actief leren uitschakelen, zie [Actief leren uitschakelen](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Zie [Uitingen verwijderen](luis-how-to-review-endpoint-utterances.md#delete-utterance)als u opgeslagen uitingen wilt beheren.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Luis-regio's referentie](./luis-reference-regions.md)
