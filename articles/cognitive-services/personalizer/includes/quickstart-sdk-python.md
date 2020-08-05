---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.openlocfilehash: b602ab1a6aa6f9c4c153924bce63da12d872a62e
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133814"
---
[Referentiedocumentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [Package (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Het gebruik van deze quickstart


Er zijn verschillende stappen voor het gebruik van deze quickstart:

* Maak een Personalizer-resource in Azure Portal
* Wijzig voor de Personalizer-resource, op de pagina **Configuratie** in Azure Portal, de frequentie van de modelupdate in een zeer korte interval
* Maak in een code-editor een codebestand en bewerk het codebestand
* Installeer de SDK vanaf de opdrachtregel of terminal
* Voer het codebestand uit vanaf de opdrachtregel of terminal

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>De Python-bibliotheek installeren voor Personalizer

Installeer de Personalizer-clientbibliotheek voor Python met de volgende opdracht:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Objectmodel

De Personalizer-client is een [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python)-object dat wordt geverifieerd bij Azure met behulp van Microsoft.Rest.ServiceClientCredentials, dat uw sleutel bevat.

Als u het beste item van de inhoud wilt hebben, maakt u een [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python) en geeft u dit door aan de methode client.Rank. Met de methode Rank wordt een RankResponse geretourneerd.

Als u een beloningsscore naar Personalizer wilt verzenden, stelt u de gebeurtenis-id en de beloningsscore (waarde) in die u wilt verzenden naar de methode [Reward](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) op de EventOperations-klasse.

Het bepalen van de belonings in deze quickstart is triviaal. In een productiesysteem kan de bepaling wat van invloed is op de [beloningsscore](../concept-rewards.md) en in welke mate, een complex proces zijn, dat u in de loop van de tijd zou kunnen wijzigen. Dit moet een van de belangrijkste ontwerpbeslissingen in uw Personalizer-architectuur zijn.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Personalizer-clientbibliotheek voor Python:

* [Personalizer-client maken](#create-a-personalizer-client)
* [Positie API](#request-the-best-action)
* [Beloning API](#send-a-reward)

## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuwe Python-toepassing in uw favoriete editor of IDE met de naam `sample.py`.

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open vanuit de projectmap het bestand **sample.py** in uw favoriete editor of IDE. Voeg het volgende toe:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Informatie over Personalizer-resource toevoegen

Maak variabelen voor de Azure-sleutel en het eindpunt van de resource die uit de omgevingsvariabelen zijn opgehaald. Deze hebben de namen `PERSONALIZER_RESOURCE_KEY` en `PERSONALIZER_RESOURCE_ENDPOINT`. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw laden om toegang te krijgen tot de variabele. De methoden worden verderop in deze quickstart gemaakt.

De resourcenaam maakt deel uit van de eindpunt-URL: `https://<your-resource-name>.api.cognitive.microsoft.com/`.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Personalizer-client maken

Maak vervolgens een methode voor het retourneren van een Personalizer-client. De parameter voor de methode is `PERSONALIZER_RESOURCE_ENDPOINT` en de ApiKey is `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Inhoudskeuzes ophalen die worden weergegeven als acties

Acties vertegenwoordigen de inhoudskeuzes waaruit Personalizer het beste inhoudsitem moet selecteren. Voeg de volgende methoden aan de klasse Programma toe om de reeks acties en hun functies te representeren.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>De leerlus maken

De Personalizer-leerlus is een cyclus van [Positie](#request-the-best-action)- en [Beloning](#send-a-reward)-aanroepen. In deze quickstart wordt elke positie-oproep, voor het personaliseren van de inhoud, gevolgd door een beloning-oproep om Personalizer te laten weten hoe goed de service is uitgevoerd.

Met de volgende code wordt herhaaldelijk vanaf de opdrachtregel de cyclus voor het vragen naar de voorkeuren van de gebruiker doorlopen. Deze gegevens worden naar Personalizer gestuurd om de beste actie te selecteren, waarbij de selectie aan de klant in een lijst wordt gepresenteerd om een keuze te kunnen maken. Vervolgens wordt er een beloning naar Personalizer verzonden waarmee wordt aangegeven hoe goed de service de selectie heeft uitgevoerd.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Voeg de volgende methoden toe, waarmee [de inhoudskeuzes worden opgehaald](#get-content-choices-represented-as-actions), voordat u het codebestand uitvoert:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>De beste actie aanvragen


Als u de Positie-aanvraag wilt voltooien, wordt naar de voorkeuren van de gebruiker gevraagd om een `currentContent` van de inhoudskeuzes te maken. Het proces kan inhoud maken die van de acties wordt uitgesloten, weergegeven als `excludeActions`. De Positie-aanvraag heeft de acties en hun kenmerken, currentcontext-kenmerken, excludeActions en een unieke gebeurtenis-id nodig om de reactie te kunnen ontvangen.

Deze quickstart bevat eenvoudige contextkenmerken over het tijdstip van de dag en de voedselvoorkeur van de gebruiker. In productiesystemen kan het bepalen en [evalueren](../concept-feature-evaluation.md) van [acties en kenmerken](../concepts-features.md) een ingewikkelde zaak zijn.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Een beloning verzenden


Om ervoor te zorgen dat de beloningsscore de Beloning-aanvraag instuurt, haalt het programma de selectie van de gebruiker op vanaf de opdrachtregel, wijst een numerieke waarde toe aan de selectie en verzendt vervolgens de unieke gebeurtenis-id en de beloningsscore als de numerieke waarde naar de Beloning API.

In deze quickstart wordt een eenvoudig getal, een 0 of een 1, aan een beloningsscore toegewezen. In productiesystemen kan het bepalen van wanneer en wat er naar de [Beloning](../concept-rewards.md)-oproep moet worden gezonden een ingewikkelde zaak zijn, afhankelijk van uw specifieke behoeften.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit met de Python vanuit uw toepassingsmap.

```console
python sample.py
```

![Het quickstart-programma stelt een aantal vragen om gebruikersvoorkeuren te verzamelen, zogenaamde kenmerken. Vervolgens wordt de beste actie geboden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)