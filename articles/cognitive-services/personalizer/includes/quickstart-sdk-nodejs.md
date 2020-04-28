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
ms.openlocfilehash: 237ba5ba390b4065a67f29611fbd43375c239578
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188852"
---
[Reference documentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |NPM-voor[beelden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) ([Source code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [package)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | voor referentie documentatie

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [node. js](https://nodejs.org) en NPM.

## <a name="using-this-quickstart"></a>Deze Snelstartgids gebruiken


Er zijn verschillende stappen voor het gebruik van deze Snelstartgids:

* Maak een persoonlijke resource in de Azure Portal
* Wijzig in de Azure Portal, voor de resource Personaler op de pagina **configuratie** , de update frequentie van het model in een zeer korte interval
* Maak in een code-editor een code bestand en bewerk het code bestand
* Vanaf de opdracht regel of Terminal installeert u de SDK vanaf de opdracht regel
* Voer in de opdracht regel of Terminal het code bestand uit

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar.

```console
mkdir myapp && cd myapp
```

Voer de `npm init -y` opdracht uit om een `package.json` bestand te maken.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>De node. JS-bibliotheek installeren voor persoonlijkere

Installeer de Personaler-client bibliotheek voor node. js met de volgende opdracht:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Installeer de resterende NPM-pakketten voor deze Quick Start:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Object model

De Personaler-client is een [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) -object dat wordt geverifieerd bij Azure met behulp van micro soft. rest. ServiceClientCredentials, dat uw sleutel bevat.

Om te vragen om één beste item van de inhoud, maakt u een [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)en geeft u het door aan de [client. Positie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) methode. De methode Rank retourneert een RankResponse.

Als u een beloning naar persoonlijker wilt verzenden, maakt u een [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)en geeft u deze door aan de [belonings](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) methode voor de klasse Events.

Het bepalen van de beloning, in deze Snelstartgids is trivial. In een productie systeem kan de bepaling van wat invloed heeft op de [belonings Score](../concept-rewards.md) en wat een complex proces kan zijn, u mogelijk besluiten om de tijd te wijzigen. Dit moet een van de belangrijkste ontwerp beslissingen zijn in uw persoonlijke architectuur.

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de Personaler-client bibliotheek voor node. js:

* [Een Personaler-client maken](#create-a-personalizer-client)
* [Rank-API](#request-the-best-action)
* [Belonings-API](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak een nieuwe node. js-toepassing in uw voorkeurs editor of IDE `sample.js`met de naam.

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het bestand **sample. js** in uw voorkeurs editor of IDE. Voeg het volgende `requires` toe om de NPM-pakketten toe te voegen:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Informatie over persoonlijker resources toevoegen

Maak variabelen voor de Azure-sleutel en het eind punt van uw resource die worden opgehaald `PERSONALIZER_KEY` uit `PERSONALIZER_ENDPOINT`de omgevings variabelen met de naam en. Als u de omgevings variabelen hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die deze uitvoert, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden verderop in deze Quick start gemaakt.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Een Personaler-client maken

Maak vervolgens een methode voor het retour neren van een Personaler-client. De para meter voor de methode is `PERSONALIZER_RESOURCE_ENDPOINT` de en de ApiKey is `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Inhouds keuzes ophalen die worden weer gegeven als acties

Acties vertegenwoordigen de inhouds keuzes waaruit u het beste inhouds item wilt selecteren. Voeg de volgende methoden toe aan de klasse Program om de set acties en hun functies weer te geven.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>De leer loop maken

De training voor persoonlijker leren is een cyclus van [positie](#request-the-best-action) -en [belonings](#send-a-reward) aanroepen. In deze Quick Start wordt elke classificatie oproep, om de inhoud te personaliseren, gevolgd door een belonings oproep om persoonlijker te vertellen hoe goed de service wordt uitgevoerd.

Met de volgende code wordt een cyclus door lopen van de gebruiker om de voor keuren te zien op de opdracht regel, waarna deze informatie wordt verzonden naar Personaler om de beste actie te selecteren, waarbij de selectie wordt gepresenteerd aan de klant om uit de lijst te kiezen en vervolgens een vergoeding te sturen naar een persoonlijker signaal om te bepalen hoe goed de service is geselecteerd.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Bekijk de rang en belonings gesprekken in de volgende secties.

Voeg de volgende methoden toe, die [de inhouds keuzes ophalen](#get-content-choices-represented-as-actions)voordat u het code bestand uitvoert:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>De beste actie aanvragen

Om de positie aanvraag te volt ooien, worden de voor keuren van de gebruiker gevraagd om inhouds keuzes te maken. Het proces kan inhoud maken om uit te sluiten van de acties, `excludeActions`weer gegeven als. De rang aanvraag heeft de [acties](../concepts-features.md#actions-represent-a-list-of-options) en hun functies, eigenschap currentcontext-functies, excludeActions en een unieke positie gebeurtenis-id nodig om de geclassificeerde reactie te ontvangen.

Deze Snelstartgids bevat eenvoudige context functies van de dag en de voor keur voor de gebruikers-levens duur. In productie systemen kan het bepalen en [evalueren](../concept-feature-evaluation.md) van [acties en functies](../concepts-features.md) een niet-triviale zaak zijn.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Een beloning verzenden


Om de belonings score te verkrijgen die in het belonings verzoek moet worden verzonden, haalt het programma de selectie van de gebruiker op via de opdracht regel, wijst een numerieke waarde toe aan de selectie. vervolgens worden de unieke gebeurtenis-ID en de belonings score als numerieke waarde naar de belonings-API verzonden.

In deze Quick Start wordt een eenvoudig getal met een belonings Score, ofwel een nul of een 1, toegewezen. In productie systemen kan worden bepaald wanneer en wat er moet worden verzonden naar de [belonings](../concept-rewards.md) oproep, afhankelijk van uw specifieke behoeften.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit met het knoop punt. js vanuit de map van de toepassing.

```console
node sample.js
```

![In het Quick Start-programma wordt een aantal vragen gesteld om gebruikers voorkeuren, ook wel bekend als-functies, te verzamelen en de meest voorkomende actie te bieden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
