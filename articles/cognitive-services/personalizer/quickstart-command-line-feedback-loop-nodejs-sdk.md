---
title: 'Quickstart: Personaler-client bibliotheek voor node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Personaler-client bibliotheek voor node. js met een Learning-lus.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: b7ea28f98340d3c3547b0ed66771b3982a03143d
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345244"
---
# <a name="quickstart-personalize-client-library-for-nodejs"></a>Quickstart: Client bibliotheek personaliseren voor node. js

Aangepaste inhoud in deze node. js-Snelstartgids weer geven met de Personaler service.

Ga aan de slag met de Personaler-client bibliotheek voor node. js. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen.

 * Rang schikking van een lijst met acties voor persoonlijke instellingen.
 * Rapport belonings Score die het succes van de beste geclassificeerde actie aangeeft.

[](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | Voor[beelden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) van bron code pakket voor bibliotheek[(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [node. js](https://nodejs.org) en NPM.

## <a name="using-this-quickstart"></a>Deze Snelstartgids gebruiken


Er zijn verschillende stappen voor het gebruik van deze Snelstartgids:

* Maak een persoonlijke resource in de Azure Portal
* Wijzig de update frequentie van het model op de pagina **instellingen** van de Azure portal voor de persoonlijke resource.
* Maak in een code-editor een code bestand en bewerk het code bestand
* Vanaf de opdracht regel of Terminal installeert u de SDK vanaf de opdracht regel
* Voer in de opdracht regel of Terminal het code bestand uit


## <a name="create-a-personalizer-azure-resource"></a>Een persoonlijke Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor Personaler met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef versie](https://azure.microsoft.com/try/cognitive-services) die 7 dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/).

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, maakt u twee [omgevings variabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY`voor de resource sleutel.
* `PERSONALIZER_ENDPOINT`voor het resource-eind punt.

In de Azure Portal zijn de sleutel-en eindpunt waarden beschikbaar op de pagina **Quick Start** .


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

## <a name="change-the-model-update-frequency"></a>De update frequentie van het model wijzigen

Wijzig in het Azure Portal, in de resource Personaler op de pagina **instellingen** , de **Update frequentie** van het model in 10 seconden. Hiermee wordt de service snel getraind, zodat u kunt zien hoe de belangrijkste actie voor elke herhaling wordt gewijzigd.

![Update frequentie van het model wijzigen](./media/settings/configure-model-update-frequency-settings.png)

Wanneer een aangepaste lus voor het eerst wordt geïnstantieerd, is er geen model, omdat er geen belonings-API-aanroepen zijn voor de trein van. Rang gesprekken retour neren een gelijke kansen voor elk item. Uw toepassing moet toch altijd inhoud plaatsen met de uitvoer van RewardActionId.

## <a name="object-model"></a>Object model

De Personaler-client is een PersonalizerClient-object dat wordt geverifieerd bij Azure met behulp van micro soft. rest. ServiceClientCredentials, dat uw sleutel bevat.

Om te vragen om een positie van de inhoud, maakt u een RankRequest en geeft u het door aan de client. Positie methode. De methode Rank retourneert een RankResponse met daarin de gerangschikte inhoud. 

Als u een beloning naar persoonlijker wilt verzenden, maakt u een RewardRequest en geeft u het door aan de client. Belonings methode. 

Het bepalen van de beloning, in deze Snelstartgids is trivial. In een productie systeem kan de bepaling van wat invloed heeft op de [belonings Score](concept-rewards.md) en wat een complex proces kan zijn, u mogelijk besluiten om de tijd te wijzigen. Dit moet een van de belangrijkste ontwerp beslissingen zijn in uw persoonlijke architectuur. 

## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de Personaler-client bibliotheek voor node. js:

* [Een Personaler-client maken](#create-a-personalizer-client)
* [Een positie aanvragen](#request-a-rank)
* [Een beloning verzenden](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak een nieuwe node. js-toepassing in uw voorkeurs editor of IDE `sample.js`met de naam. 

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het bestand **sample. js** in uw voorkeurs editor of IDE. Voeg het volgende `requires` toe om de NPM-pakketten toe te voegen:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Informatie over persoonlijker resources toevoegen

Maak variabelen voor de Azure-sleutel en het eind punt van uw resource die worden opgehaald `PERSONALIZER_KEY` uit `PERSONALIZER_ENDPOINT`de omgevings variabelen met de naam en. Als u de omgevings variabelen hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die deze uitvoert, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden verderop in deze Quick start gemaakt.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Een Personaler-client maken

Maak vervolgens een methode voor het retour neren van een Personaler-client. De para meter voor de methode is `PERSONALIZER_RESOURCE_ENDPOINT` de en de ApiKey `PERSONALIZER_RESOURCE_KEY`is.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Inhouds keuzes ophalen die worden weer gegeven als acties

Acties vertegenwoordigen de inhouds keuzes die persoonlijker moeten worden gerangschikt. Voeg de volgende methoden toe aan de klasse Program om de invoer van een gebruiker op te halen vanaf de opdracht regel voor het tijdstip van de dag en de huidige voedsel voorkeur.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>De leer loop maken

De training voor persoonlijker leren is een cyclus van [positie](#request-a-rank) -en [belonings](#send-a-reward) aanroepen. In deze Quick Start wordt elke classificatie oproep, om de inhoud te personaliseren, gevolgd door een belonings oproep om persoonlijker te vertellen hoe goed de service de inhoud rangschikt. 

De volgende herhalings code loopt door een cyclus waarbij de gebruiker om voor keuren wordt gevraagd op de opdracht regel, waardoor die informatie naar persoonlijker kan worden geclassificeerd, waarbij de gerangschikte selectie aan de klant wordt gepresenteerd waaruit kan worden gekozen in de lijst. vervolgens wordt er een beloning verzonden naar Persoonlijkere signalen geven aan hoe goed de service de selectie heeft gevolgd.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Bekijk de rang en belonings gesprekken in de volgende secties.

Voeg de volgende methoden toe, die [de inhouds keuzes ophalen](#get-content-choices-represented-as-actions)voordat u het code bestand uitvoert:

* getActionsList
* getContextFeaturesList

## <a name="request-a-rank"></a>Een positie aanvragen

Om de positie aanvraag te volt ooien, worden de voor keuren van de gebruiker gevraagd om inhouds keuzes te maken. Het proces kan inhoud maken om uit te sluiten van de positie, `excludeActions`weer gegeven als. De rang aanvraag heeft de [acties](concepts-features.md#actions-represent-a-list-of-options), eigenschap currentcontext, excludeActions en een unieke positie gebeurtenis-id (als GUID) nodig om de geclassificeerde reactie te ontvangen. 

Deze Snelstartgids bevat eenvoudige context functies van de dag en de voor keur voor de gebruikers-levens duur. In productie systemen kan het bepalen en [evalueren](concept-feature-evaluation.md) van [acties en functies](concepts-features.md) een niet-triviale zaak zijn.  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Een beloning verzenden

Voor het volt ooien van de vraag, wordt de selectie van de gebruiker uit de opdracht regel opgehaald, waarna een numerieke waarde aan elke selectie wordt toegewezen, waarna de unieke positie gebeurtenis-ID en de numerieke waarde naar de belonings methode worden verzonden.

In deze Quick Start wordt een eenvoudig getal als beloning, een nul of een 1, toegewezen. In productie systemen kan worden bepaald wanneer en wat er moet worden verzonden naar de [belonings](concept-rewards.md) oproep, afhankelijk van uw specifieke behoeften. 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit met het knoop punt. js vanuit de map van de toepassing.

```console
node sample.js
```

![In het Quick Start-programma wordt een aantal vragen gesteld om gebruikers voorkeuren, ook wel bekend als-functies, te verzamelen en de meest voorkomende actie te bieden.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Hoe Personaler werkt](how-personalizer-works.md)

* [Wat is persoonlijkere?](what-is-personalizer.md)
* [Waar kunt u Personaler gebruiken?](where-can-you-use-personalizer.md)
* [Problemen oplossen](troubleshooting.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js).
