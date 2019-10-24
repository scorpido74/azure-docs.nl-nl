---
title: API-aanroepen gebruiken met een Conversation Learner model-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Meer informatie over het gebruik van API-aanroepen met een Conversation Learner model.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 261536932cc82a28ad4ee3ffc3575ea41fe9ec5b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703914"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>API-aanroepen toevoegen aan een Conversation Learner model

In deze zelf studie ziet u hoe u API-aanroepen toevoegt aan uw model. API-aanroepen zijn functies die u in uw bot definieert en schrijft en die Conversation Learner kunnen aanroepen.

## <a name="video"></a>Video

[![Preview van zelf studie voor API-aanroepen](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Vereisten
Voor deze zelf studie moet de bot ' tutorialAPICalls. TS ' worden uitgevoerd.

    npm run tutorial-api-calls

## <a name="details"></a>Details

- API-aanroepen kunnen entiteiten lezen en bewerken.
- API-aanroepen hebben toegang tot het object geheugen beheer.
- API-aanroepen kunnen argumenten hebben.

### <a name="open-the-demo"></a>De demo openen

Klik in de webgebruikersinterface op zelf studies importeren en selecteer het model met de naam ' zelf studie-14-APICalls '.

### <a name="entities"></a>Entiteiten

Er is één entiteit gedefinieerd in het model met `number`de naam.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API-aanroepen
De code voor de API-aanroepen wordt gedefinieerd in het volgende `C:\<installedpath>\src\demos\tutorialAPICalls.ts`bestand:.

![](../media/tutorial12_apicalls.PNG)

- De `RandomGreeting` call back retourneert een wille keurige begroeting die is gedefinieerd in de `greeting` matrix.
- De `Multiply` retour aanroep vermenigvuldigt twee getallen die worden door gegeven door de actie die deze aanroept en retourneert een resultaat dat kan worden weer gegeven in de gebruikers interface.
    - U ziet dat geheugen beheer het eerste argument is. 
    - U ziet dat API-call backs meerdere invoer kunnen hebben, `num1string` in `num2string`dit geval en.
- Met `ClearEntities` de call back wordt de nummer entiteit gewist, zodat de gebruiker een ander getal kan invoeren. 
    - Illustreert hoe API-aanroepen entiteiten kunnen manipuleren.

### <a name="actions"></a>Acties
We hebben vier acties gemaakt. Drie van deze zijn de API-acties zonder wacht tijd, met de vierde, een actie ' tekst ' die de gebruiker vraagt een vraag te zien die vergelijkbaar is met wat we in andere zelf studies hebben gezien. Ga als volgt te werk om te zien hoe elk is gemaakt:
1. Klik in het linkerdeel venster op acties en klik vervolgens op een van de vier acties die in het raster worden weer gegeven.
2. Let op de waarden van elk veld in het formulier dat wordt weer gegeven.
3. U ziet `Refresh` de knop naast het veld API.
    - Als we de bot zouden stoppen en de api's moeten worden gewijzigd terwijl de pagina met de gebruikers interface actief is, kunt u klikken `Refresh` op de knop om de laatste wijzigingen op te halen.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, vermenigvuldigen en RandomGreeting
Deze drie acties zijn het API-type. Ze zijn afhankelijk van de API call back-functies voor het uitvoeren van een werk en retour neren mogelijk een waarde die aan de gebruiker wordt gepresenteerd.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"Welk nummer wilt u vermenigvuldigen met 12"
Dit is de actie tekst en er wordt een vraag van de gebruiker gesteld. Hoewel deze actie niet daad werkelijk communiceert met een van de API-retour aanroepen, wordt de gebruiker gevraagd om te reageren met een getal dat naar het geheugen van een entiteit gaat, dat vervolgens kan worden gebruikt door de actie ' vermenigvuldigen ', waarbij een van de API-aanroepen wordt gebruikt.


### <a name="train-dialog"></a>Dialoog venster trainen

Laten we een ' trainings venster ' door lopen.

1. Klik in het linkerdeel venster op `Train Dialogs`en klik vervolgens `New Train Dialog` op de knop.
2. Typ "Hallo".
3. Klik op de knop `Score Actions`.
4. Selecteer `RandomGreeting`. 
    - Hiermee wordt de API-aanroep voor wille keurige begroeting uitgevoerd.
    - Hiermee wordt niet gewacht op een reactie van de gebruiker.
5. Selecteer `What number to do you want to multiply by 12?`
6. Typ een getal, een wille keurig getal en alleen een getal.
    - U ziet dat uw nummer automatisch als de `number` entiteit is gelabeld.
7. Klik op de knop `Score Actions`.
8. Selecteer de `Multiply` actie.
    - Let op het resultaat van de vermenigvuldiging met 12.
    - U ziet dat het geheugen nog steeds de waarde bevat die u hebt opgegeven voor`number`
9. Selecteer de `ClearEntities` actie.
    - U ziet dat de entiteits `number` waarde voor is gewist uit het geheugen.
10. Klik op de knop `Save`.

U hebt nu gezien hoe u API-retour aanroepen, hun algemene patronen kunt registreren en hoe u argumenten kunt definiëren en waarden en entiteiten kunt koppelen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Kaarten deel 1](./15-cards.md)
