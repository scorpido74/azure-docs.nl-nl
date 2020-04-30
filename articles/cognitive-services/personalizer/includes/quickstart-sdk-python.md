---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: d35f11125809167c4834b33f913d9369a6402ac0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188853"
---
[Reference documentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | Pypi-voor[beelden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py) ([Source code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [package)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | voor referentie documentatie

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Deze Snelstartgids gebruiken


Er zijn verschillende stappen voor het gebruik van deze Snelstartgids:

* Maak een persoonlijke resource in de Azure Portal
* Wijzig in de Azure Portal, voor de resource Personaler op de pagina **configuratie** , de update frequentie van het model in een zeer korte interval
* Maak in een code-editor een code bestand en bewerk het code bestand
* Vanaf de opdracht regel of Terminal installeert u de SDK vanaf de opdracht regel
* Voer in de opdracht regel of Terminal het code bestand uit

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>De python-bibliotheek installeren voor personaliseren

Installeer de Personaler-client bibliotheek voor python met de volgende opdracht:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Object model

De Personaler-client is een [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) -object dat wordt geverifieerd bij Azure met behulp van micro soft. rest. ServiceClientCredentials, dat uw sleutel bevat.

Om te vragen om één beste item van de inhoud, maakt u een [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)en geeft u het door aan de client. Positie methode. De methode Rank retourneert een RankResponse.

Als u een belonings score naar een persoonlijker wilt verzenden, stelt u de gebeurtenis-ID en de belonings Score (waarde) in die u wilt verzenden naar de [belonings](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) methode voor de EventOperations-klasse.

Het bepalen van de beloning, in deze Snelstartgids is trivial. In een productie systeem kan de bepaling van wat invloed heeft op de [belonings Score](../concept-rewards.md) en wat een complex proces kan zijn, u mogelijk besluiten om de tijd te wijzigen. Dit moet een van de belangrijkste ontwerp beslissingen zijn in uw persoonlijke architectuur.

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de Personaler-client bibliotheek voor python:

* [Een Personaler-client maken](#create-a-personalizer-client)
* [Rank-API](#request-the-best-action)
* [Belonings-API](#send-a-reward)

## <a name="create-a-new-python-application"></a>Een nieuwe python-toepassing maken

Maak een nieuwe python-toepassing in uw voorkeurs editor of IDE `sample.py`met de naam.

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het **sample.py** -bestand in de map van het project in uw voorkeurs editor of IDE. Voeg het volgende toe:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Informatie over persoonlijker resources toevoegen

Maak variabelen voor de Azure-sleutel en het eind punt van uw resource die worden opgehaald `PERSONALIZER_RESOURCE_KEY` uit `PERSONALIZER_RESOURCE_ENDPOINT`de omgevings variabelen met de naam en. Als u de omgevings variabelen hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die deze uitvoert, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden verderop in deze Quick start gemaakt.

De resource naam maakt deel uit van de eind punt `https://<your-resource-name>.api.cognitive.microsoft.com/`-URL:.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Een Personaler-client maken

Maak vervolgens een methode voor het retour neren van een Personaler-client. De para meter voor de methode is `PERSONALIZER_RESOURCE_ENDPOINT` de en de ApiKey is `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Inhouds keuzes ophalen die worden weer gegeven als acties

Acties vertegenwoordigen de inhouds keuzes waaruit u het beste inhouds item wilt selecteren. Voeg de volgende methoden toe aan de klasse Program om de set acties en hun functies weer te geven.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>De leer loop maken

De training voor persoonlijker leren is een cyclus van [positie](#request-the-best-action) -en [belonings](#send-a-reward) aanroepen. In deze Quick Start wordt elke classificatie oproep, om de inhoud te personaliseren, gevolgd door een belonings oproep om persoonlijker te vertellen hoe goed de service wordt uitgevoerd.

Met de volgende code wordt een cyclus door lopen van de gebruiker om de voor keuren te zien op de opdracht regel, waarna deze informatie wordt verzonden naar Personaler om de beste actie te selecteren, waarbij de selectie wordt gepresenteerd aan de klant om uit de lijst te kiezen en vervolgens een vergoeding te sturen naar een persoonlijker signaal om te bepalen hoe goed de service is geselecteerd.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Voeg de volgende methoden toe, die [de inhouds keuzes ophalen](#get-content-choices-represented-as-actions)voordat u het code bestand uitvoert:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>De beste actie aanvragen


Om de positie aanvraag te volt ooien, worden de voor keuren van de gebruiker `currentContent` gevraagd om een van de inhouds keuzes te maken. Het proces kan inhoud maken om uit te sluiten van de acties, `excludeActions`weer gegeven als. De rang aanvraag heeft de acties en hun functies, eigenschap currentcontext functies, excludeActions en een unieke gebeurtenis-ID nodig om de reactie te ontvangen.

Deze Snelstartgids bevat eenvoudige context functies van de dag en de voor keur voor de gebruikers-levens duur. In productie systemen kan het bepalen en [evalueren](../concept-feature-evaluation.md) van [acties en functies](../concepts-features.md) een niet-triviale zaak zijn.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Een beloning verzenden


Om de belonings score te verkrijgen die in het belonings verzoek moet worden verzonden, haalt het programma de selectie van de gebruiker op via de opdracht regel, wijst een numerieke waarde toe aan de selectie. vervolgens worden de unieke gebeurtenis-ID en de belonings score als numerieke waarde naar de belonings-API verzonden.

In deze Quick Start wordt een eenvoudig getal met een belonings Score, ofwel een nul of een 1, toegewezen. In productie systemen kan worden bepaald wanneer en wat er moet worden verzonden naar de [belonings](../concept-rewards.md) oproep, afhankelijk van uw specifieke behoeften.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit met de python vanuit uw toepassingsmap.

```console
python sample.py
```

![In het Quick Start-programma wordt een aantal vragen gesteld om gebruikers voorkeuren, ook wel bekend als-functies, te verzamelen en de meest voorkomende actie te bieden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)