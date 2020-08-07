---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file, devx-track-javascript
ms.date: 07/30/2020
ms.openlocfilehash: 4f57d3a6c959a8ec912722a617496c52f412c13f
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461102"
---
[Referentiedocumentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/)
* De huidige versie van [Node.js](https://nodejs.org) en NPM.

## <a name="using-this-quickstart"></a>Het gebruik van deze quickstart


Er zijn verschillende stappen voor het gebruik van deze quickstart:

* Maak een Personalizer-resource in Azure Portal
* Wijzig voor de Personalizer-resource, op de pagina **Configuratie** in Azure Portal, de frequentie van de modelupdate in een zeer korte interval
* Maak in een code-editor een codebestand en bewerk het codebestand
* Installeer de SDK vanaf de opdrachtregel of terminal
* Voer het codebestand uit vanaf de opdrachtregel of terminal

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map.

```console
mkdir myapp && cd myapp
```

Voer de opdracht `npm init -y` uit om een bestand `package.json` te maken.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>De Node.js-bibliotheek installeren voor Personalizer

Installeer de Personalizer-clientbibliotheek voor Node.js met de volgende opdracht:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Installeer de resterende NPM-pakketten voor deze quickstart:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Objectmodel

De Personalizer-client is een [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest)-object dat wordt geverifieerd bij Azure met behulp van Microsoft.Rest.ServiceClientCredentials, dat uw sleutel bevat.

Als u het beste item van de inhoud wilt hebben, maakt u een [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest) en geeft u dit door aan de methode [client.Rank](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-). Met de methode Rank wordt een RankResponse geretourneerd.

Als u een belonings naar een Personalizer wilt verzenden, maakt u een [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest) en geeft u dit door aan de methode [Reward](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) in de Gebeurtenisklasse.

Het bepalen van de belonings in deze quickstart is triviaal. In een productiesysteem kan de bepaling wat van invloed is op de [beloningsscore](../concept-rewards.md) en in welke mate, een complex proces zijn, dat u in de loop van de tijd zou kunnen wijzigen. Dit moet een van de belangrijkste ontwerpbeslissingen in uw Personalizer-architectuur zijn.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende kunt uitvoeren met de Personalizer-clientbibliotheek voor Node.js:

* [Personalizer-client maken](#create-a-personalizer-client)
* [Positie API](#request-the-best-action)
* [Beloning API](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak een nieuwe Node.jse-toepassing in uw favoriete editor of IDE genaamd `sample.js`.

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het bestand **sample.js** in uw voorkeurseditor of IDE. Voeg de volgende `requires` toe om de NPM-pakketten toe te voegen:

[!code-javascript[Add module dependencies](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Informatie over Personalizer-resource toevoegen

Bewerk de sleutel- en eindpuntvariabelen boven aan het codebestand bij voor de Azure-sleutel en het Azure-eindpunt van uw resource. 

[!code-javascript[Add Personalizer resource information](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Personalizer-client maken

Maak vervolgens een methode voor het retourneren van een Personalizer-client. De parameter voor de methode is `PERSONALIZER_RESOURCE_ENDPOINT` en de ApiKey is `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Inhoudskeuzes ophalen die worden weergegeven als acties

Acties vertegenwoordigen de inhoudskeuzes waaruit Personalizer het beste inhoudsitem moet selecteren. Voeg de volgende methoden aan de klasse Programma toe om de reeks acties en hun functies te representeren.

[!code-javascript[Create user features](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>De leerlus maken

De Personalizer-leerlus is een cyclus van [Positie](#request-the-best-action)- en [Beloning](#send-a-reward)-aanroepen. In deze quickstart wordt elke Positie-oproep, voor het personaliseren van de inhoud, gevolgd door een Beloning-oproep om Personalizer te laten weten hoe goed de service is uitgevoerd.

Met de volgende code wordt herhaaldelijk vanaf de opdrachtregel de cyclus voor het vragen naar de voorkeuren van de gebruiker doorlopen. Deze gegevens worden naar Personalizer gestuurd om de beste actie te selecteren, waarbij de selectie aan de klant in een lijst wordt gepresenteerd om een keuze te kunnen maken. Vervolgens wordt er een beloning naar Personalizer verzonden waarmee wordt aangegeven hoe goed de service de selectie heeft uitgevoerd.

[!code-javascript[Create the learning loop](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=mainLoop)]

Bekijk de Rank- en Reward-aanroepen in de volgende secties.

Voeg de volgende methoden toe, waarmee [de inhoudskeuzes worden opgehaald](#get-content-choices-represented-as-actions), voordat u het codebestand uitvoert:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>De beste actie aanvragen

Als u de Positie-aanvraag wilt voltooien, wordt naar de voorkeuren van de gebruiker gevraagd om inhoudskeuzes te maken. Het proces kan inhoud maken die van de acties wordt uitgesloten, weergegeven als `excludeActions`. De Positie-aanvraag heeft de [acties](../concepts-features.md#actions-represent-a-list-of-options) en hun kenmerken, currentcontext-kenmerken, excludeActions en een unieke ranggebeurtenis-id nodig om de gerangschikte reactie te kunnen ontvangen.

Deze quickstart bevat eenvoudige contextkenmerken over het tijdstip van de dag en de voedselvoorkeur van de gebruiker. In productiesystemen kan het bepalen en [evalueren](../concept-feature-evaluation.md) van [acties en kenmerken](../concepts-features.md) een ingewikkelde zaak zijn.

[!code-javascript[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=rank)]

## <a name="send-a-reward"></a>Een beloning verzenden


Om ervoor te zorgen dat de beloningsscore de Beloning-aanvraag instuurt, haalt het programma de selectie van de gebruiker op vanaf de opdrachtregel, wijst een numerieke waarde toe aan de selectie en verzendt vervolgens de unieke gebeurtenis-id en de beloningsscore als de numerieke waarde naar de Beloning API.

In deze quickstart wordt een eenvoudig getal, een 0 of een 1, aan een beloningsscore toegewezen. In productiesystemen kan het bepalen van wanneer en wat er naar de [Beloning](../concept-rewards.md)-oproep moet worden gezonden een ingewikkelde zaak zijn, afhankelijk van uw specifieke behoeften.

[!code-javascript[The Personalizer learning loop sends a reward.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=reward)]

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit met de Node.js vanuit uw toepassingsmap.

```console
node sample.js
```

![Het quickstart-programma stelt een aantal vragen om gebruikersvoorkeuren te verzamelen, zogenaamde kenmerken. Vervolgens wordt de beste actie geboden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
