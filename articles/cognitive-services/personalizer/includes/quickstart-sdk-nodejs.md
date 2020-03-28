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
ms.openlocfilehash: 786efcb712557da4363384c9d05c33f4f16d6731
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122878"
---
[NPM-voorbeelden (Reference documentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[Library Source Code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Package)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Samples](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [Node.js](https://nodejs.org) en NPM.

## <a name="using-this-quickstart"></a>Deze quickstart gebruiken


Er zijn verschillende stappen om deze quickstart te gebruiken:

* Maak in de Azure-portal een personalizerbron
* Wijzig in de Azure-portal voor de personalizerbron op de pagina **Configuratie** de frequentie van modelupdate in een zeer kort interval
* Maak in een codeeditor een codebestand en bewerk het codebestand
* Installeer de SDK in de opdrachtregel of terminal vanaf de opdrachtregel
* Voer in de opdrachtregel of terminal het codebestand uit

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor uw app en navigeer ernaar.

```console
mkdir myapp && cd myapp
```

Voer `npm init -y` de opdracht `package.json` uit om een bestand te maken.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Installeer de Node.js-bibliotheek voor personalizer

Installeer de personalizerclientbibliotheek voor Node.js met de volgende opdracht:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Installeer de resterende NPM-pakketten voor deze quickstart:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Objectmodel

De Personalizer-client is een [PersonalizerClient-object](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) dat zich verifieert naar Azure met behulp van Microsoft.Rest.ServiceClientCredentials, dat uw sleutel bevat.

Als u het beste item van de inhoud wilt aanvragen, maakt u een [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)en geeft u deze door aan [de client. Rangmethode.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) De methode Rang retourneert een RankResponse.

Als u een beloning naar Personalizer wilt verzenden, maakt u een [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)en geeft u deze door aan de [beloningsmethode](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) in de klasse Gebeurtenissen.

Het bepalen van de beloning, in deze quickstart is triviaal. In een productiesysteem, de bepaling van wat de [beloningsscore](../concept-rewards.md) beïnvloedt en door hoeveel een complex proces kan zijn, dat u beslissen om in tijd te veranderen. Dit moet een van de belangrijkste ontwerpbeslissingen in uw personalizerarchitectuur zijn.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de personalizerclientbibliotheek voor Node.js:

* [Een gepersonaliseerde client maken](#create-a-personalizer-client)
* [Rank-API](#request-the-best-action)
* [Reward API](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak een nieuwe Node.js-toepassing in uw `sample.js`voorkeurseditor of IDE met de naam .

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het **sample.js-bestand** in de voorkeurseditor of IDE. Voeg het `requires` volgende toe om de NPM-pakketten toe te voegen:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Persoonlijkeinformatie toevoegen

Maak variabelen voor de Azure-sleutel en het eindpunt van uw `PERSONALIZER_KEY` `PERSONALIZER_ENDPOINT`resource die zijn opgehaald uit de omgevingsvariabelen, benoemd en . Als u de omgevingsvariabelen hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die wordt uitgevoerd, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later in deze quickstart gemaakt.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Een gepersonaliseerde client maken

Maak vervolgens een methode om een Personalizer-client terug te sturen. De parameter voor de `PERSONALIZER_RESOURCE_ENDPOINT` methode is de `PERSONALIZER_RESOURCE_KEY`en de ApiKey is de .

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Inhoudskeuzes weergeven als acties

Acties vertegenwoordigen de inhoudskeuzes waaruit u wilt dat Personalizer het beste inhoudsitem selecteert. Voeg de volgende methoden toe aan de klasse Programma om de set acties en hun functies weer te geven.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>De leerlus maken

De Personalizer learning loop is een cyclus van [Rank en](#request-the-best-action) [Reward](#send-a-reward) calls. In deze quickstart wordt elk Rang-gesprek, om de inhoud te personaliseren, gevolgd door een beloningsgesprek om Personalizer te vertellen hoe goed de service heeft gepresteerd.

De volgende code lussen door een cyclus van het vragen van de gebruiker hun voorkeuren op de command line, het verzenden van die informatie naar Personalizer om de beste actie te selecteren, de presentatie van de selectie aan de klant om uit te kiezen uit de lijst, dan het verzenden van een beloning naar Personalizer signalering hoe goed de dienst deed in haar selectie.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Neem een kijkje op de rang en beloning oproepen in de volgende secties.

Voeg de volgende methoden toe, waarmee [de inhoudsopties worden uitgevoerd](#get-content-choices-represented-as-actions)voordat u het codebestand uitvoert:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>De beste actie aanvragen

Om de Rank-aanvraag te voltooien, vraagt het programma de voorkeuren van de gebruiker om inhoudskeuzes te maken. Het proces kan inhoud maken om uit `excludeActions`te sluiten van de acties, weergegeven als . De Rang-aanvraag heeft de [acties](../concepts-features.md#actions-represent-a-list-of-options) en hun functies, huidigeContext-functies, excludeActions en een unieke ranggebeurtenis-ID nodig om het gerangschikte antwoord te ontvangen.

Deze quickstart heeft eenvoudige contextfuncties van het tijdstip van de dag en de voorkeur voor gebruikersvoeding. In productiesystemen kan het bepalen en [evalueren van](../concept-feature-evaluation.md) acties [en functies](../concepts-features.md) een niet-triviale zaak zijn.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Stuur een beloning


Om de beloningsscore te krijgen om het beloningsverzoek in te sturen, krijgt het programma de selectie van de gebruiker uit de opdrachtregel, wijst het een numerieke waarde toe aan de selectie en stuurt het vervolgens de unieke gebeurtenis-ID en de beloningsscore als de numerieke waarde naar de Reward API.

Deze quickstart kent een eenvoudig getal toe als beloningsscore, een nul of een 1. In productiesystemen kan bepalen wanneer en wat u naar de [Reward-oproep](../concept-rewards.md) moet worden verzonden, afhankelijk van uw specifieke behoeften.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit met de Node.js vanuit uw toepassingsmap.

```console
node sample.js
```

![Het quickstart-programma stelt een paar vragen om gebruikersvoorkeuren te verzamelen, bekend als functies, en biedt vervolgens de hoogste actie.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
