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
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: fe800280a7a652b5d9a397a21a5b0a66b40af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122920"
---
[Referentiedocumentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [(pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Deze quickstart gebruiken


Er zijn verschillende stappen om deze quickstart te gebruiken:

* Maak in de Azure-portal een personalizerbron
* Wijzig in de Azure-portal voor de personalizerbron op de pagina **Configuratie** de frequentie van modelupdate in een zeer kort interval
* Maak in een codeeditor een codebestand en bewerk het codebestand
* Installeer de SDK in de opdrachtregel of terminal vanaf de opdrachtregel
* Voer in de opdrachtregel of terminal het codebestand uit

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>De Python-bibliotheek installeren voor personalizer

Installeer de personalizer-clientbibliotheek voor Python met de volgende opdracht:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Objectmodel

De Personalizer-client is een [PersonalizerClient-object](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) dat zich verifieert naar Azure met behulp van Microsoft.Rest.ServiceClientCredentials, dat uw sleutel bevat.

Als u het beste item van de inhoud wilt aanvragen, maakt u een [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)en geeft u deze door aan de client. Rangmethode. De methode Rang retourneert een RankResponse.

Als u een beloningsscore naar Personalizer wilt sturen, stelt u de gebeurtenis-id en de beloningsscore (waarde) in die u naar de [beloningsmethode](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) in de klasse EventOperations wilt sturen.

Het bepalen van de beloning, in deze quickstart is triviaal. In een productiesysteem, de bepaling van wat de [beloningsscore](../concept-rewards.md) beïnvloedt en door hoeveel een complex proces kan zijn, dat u beslissen om in tijd te veranderen. Dit moet een van de belangrijkste ontwerpbeslissingen in uw personalizerarchitectuur zijn.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de personalizer-clientbibliotheek voor Python:

* [Een gepersonaliseerde client maken](#create-a-personalizer-client)
* [Rank-API](#request-the-best-action)
* [Reward API](#send-a-reward)

## <a name="create-a-new-python-application"></a>Een nieuwe python-toepassing maken

Maak een nieuwe Python-toepassing in uw `sample.py`voorkeurseditor of IDE met de naam .

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open in de projectmap het **sample.py-bestand** in uw voorkeurseditor of IDE. Voeg het volgende toe:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Persoonlijkeinformatie toevoegen

Maak variabelen voor de Azure-sleutel en het eindpunt van uw `PERSONALIZER_RESOURCE_KEY` `PERSONALIZER_RESOURCE_ENDPOINT`resource die zijn opgehaald uit de omgevingsvariabelen, benoemd en . Als u de omgevingsvariabelen hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die wordt uitgevoerd, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later in deze quickstart gemaakt.

De resourcenaam maakt deel uit `https://<your-resource-name>.api.cognitive.microsoft.com/`van de URL van het eindpunt: .

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Een gepersonaliseerde client maken

Maak vervolgens een methode om een Personalizer-client terug te sturen. De parameter voor de `PERSONALIZER_RESOURCE_ENDPOINT` methode is de `PERSONALIZER_RESOURCE_KEY`en de ApiKey is de .

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Inhoudskeuzes weergeven als acties

Acties vertegenwoordigen de inhoudskeuzes waaruit u wilt dat Personalizer het beste inhoudsitem selecteert. Voeg de volgende methoden toe aan de klasse Programma om de set acties en hun functies weer te geven.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>De leerlus maken

De Personalizer learning loop is een cyclus van [Rank en](#request-the-best-action) [Reward](#send-a-reward) calls. In deze quickstart wordt elk rangcall, om de inhoud te personaliseren, gevolgd door een beloningsoproep om Personalizer te vertellen hoe goed de service heeft gepresteerd.

De volgende code lussen door een cyclus van het vragen van de gebruiker hun voorkeuren op de command line, het verzenden van die informatie naar Personalizer om de beste actie te selecteren, de presentatie van de selectie aan de klant om uit te kiezen uit de lijst, dan het verzenden van een beloning naar Personalizer signalering hoe goed de dienst deed in haar selectie.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Voeg de volgende methoden toe, waarmee [de inhoudsopties worden uitgevoerd](#get-content-choices-represented-as-actions)voordat u het codebestand uitvoert:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>De beste actie aanvragen


Om de rank-aanvraag te voltooien, vraagt het programma `currentContent` de voorkeuren van de gebruiker om een van de inhoudskeuzes te maken. Het proces kan inhoud maken om uit `excludeActions`te sluiten van de acties, weergegeven als . De rangaanvraag heeft de acties en hun functies, huidigeContextfuncties, excludeActions en een unieke gebeurtenis-id nodig om het antwoord te ontvangen.

Deze quickstart heeft eenvoudige contextfuncties van het tijdstip van de dag en de voorkeur voor gebruikersvoeding. In productiesystemen kan het bepalen en [evalueren van](../concept-feature-evaluation.md) acties [en functies](../concepts-features.md) een niet-triviale zaak zijn.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Stuur een beloning


Om de beloningsscore te krijgen om het beloningsverzoek in te sturen, krijgt het programma de selectie van de gebruiker uit de opdrachtregel, wijst het een numerieke waarde toe aan de selectie en stuurt het vervolgens de unieke gebeurtenis-ID en de beloningsscore als de numerieke waarde naar de Reward API.

Deze quickstart kent een eenvoudig getal toe als beloningsscore, een nul of een 1. In productiesystemen kan bepalen wanneer en wat u naar de [Reward-oproep](../concept-rewards.md) moet worden verzonden, afhankelijk van uw specifieke behoeften.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit met de python vanuit uw toepassingsmap.

```console
python sample.py
```

![Het quickstart-programma stelt een paar vragen om gebruikersvoorkeuren te verzamelen, bekend als functies, en biedt vervolgens de hoogste actie.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)