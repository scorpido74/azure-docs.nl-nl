---
title: 'Quick Start: client bibliotheek voor persoonlijke instellingen voor .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Personaler-client bibliotheek voor .NET met een Learning-lus.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 411bd82ade2ca7b904b36a3a4408c1a00852fc2c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927829"
---
# <a name="quickstart-personalizer-client-library-for-net"></a>Quick Start: client bibliotheek voor persoonlijke instellingen voor .NET

Persoonlijke inhoud in deze C# Quick Start weer geven met de personaler service.

Ga aan de slag met de Personaler-client bibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen.

 * Rang schikking van een lijst met acties voor persoonlijke instellingen.
 * Rapport belonings Score die het succes van de beste geclassificeerde actie aangeeft.

[Referentie documentatie](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | -voor [beelden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Deze Snelstartgids gebruiken

Er zijn verschillende stappen voor het gebruik van deze Snelstartgids:

* Maak een persoonlijke resource in de Azure Portal
* Wijzig de update frequentie van het model in de Azure Portal voor de resource Personaler op de pagina **configuratie**
* Maak in een code-editor een code bestand en bewerk het code bestand
* Vanaf de opdracht regel of Terminal installeert u de SDK vanaf de opdracht regel
* Voer in de opdracht regel of Terminal het code bestand uit

## <a name="create-a-personalizer-azure-resource"></a>Een persoonlijke Azure-resource maken

Maak een resource voor Personaler met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. U kunt ook het volgende doen:

* Ontvang een [proef versie](https://azure.microsoft.com/try/cognitive-services) die 7 dagen gratis geldig is. Nadat u zich hebt aangemeld, is deze beschikbaar op de [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Bekijk uw resource op het [Azure Portal](https://portal.azure.com/).

Nadat u een sleutel van uw proef abonnement of resource hebt ontvangen, maakt u twee [omgevings variabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_RESOURCE_KEY` voor de resource sleutel.
* `PERSONALIZER_RESOURCE_ENDPOINT` voor het resource-eind punt.

In de Azure Portal zijn de sleutel-en eindpunt waarden beschikbaar via de pagina **Quick** start.

## <a name="change-the-model-update-frequency"></a>De update frequentie van het model wijzigen

Wijzig in de Azure Portal in de resource personaliseren op de pagina **configuratie** de **verwerkings frequentie** van het model in 10 seconden. Met deze korte duur wordt de service snel getraind, zodat u kunt zien hoe de belangrijkste actie voor elke herhaling wordt gewijzigd.

![Update frequentie van het model wijzigen](./media/settings/configure-model-update-frequency-settings.png)

Wanneer een aangepaste lus voor het eerst wordt geïnstantieerd, is er geen model, omdat er geen belonings-API-aanroepen zijn voor de trein van. Rang gesprekken retour neren een gelijke kansen voor elk item. Uw toepassing moet toch altijd inhoud plaatsen met de uitvoer van RewardActionId.

## <a name="create-a-new-c-application"></a>Een nieuwe C# toepassing maken

Maak een nieuwe .NET core-toepassing in uw voorkeurs editor of IDE. 

Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht DotNet `new` om een nieuwe console-app te maken met de naam `personalizer-quickstart`. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: `Program.cs`. 

```console
dotnet new console -n personalizer-quickstart
```

Wijzig uw directory in de zojuist gemaakte app-map. U kunt de toepassing samen stellen met:

```console
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="install-the-sdk"></a>De SDK installeren

Installeer in de toepassingsmap de Personaler-client bibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Als u de Visual Studio IDE gebruikt, is de client bibliotheek beschikbaar als een downloadbaar NuGet-pakket.

## <a name="object-model"></a>Object model

De Personaler-client is een [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) -object dat wordt geverifieerd bij Azure met behulp van micro soft. rest. ServiceClientCredentials, dat uw sleutel bevat.

Om te vragen om een positie van de inhoud, maakt u een [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)en geeft u het door aan de [client. Positie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) methode. De methode Rank retourneert een RankResponse met daarin de gerangschikte inhoud. 

Als u een beloning naar persoonlijker wilt verzenden, maakt u een [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)en geeft u het door aan de [client. Belonings](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) methode. 

Het bepalen van de beloning, in deze Snelstartgids is trivial. In een productie systeem kan de bepaling van wat invloed heeft op de [belonings Score](concept-rewards.md) en wat een complex proces kan zijn, u mogelijk besluiten om de tijd te wijzigen. Deze ontwerp beslissing moet een van de belangrijkste beslissingen van uw Personaler-architectuur zijn. 

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Personaler-client bibliotheek voor .NET:

* [Een Personaler-client maken](#create-a-personalizer-client)
* [Een positie aanvragen](#request-a-rank)
* [Een beloning verzenden](#send-a-reward)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het **Program.cs** -bestand in de map van het project in uw voorkeurs editor of IDE. Vervang de bestaande `using` code door de volgende `using`-instructies:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Informatie over persoonlijker resources toevoegen

Maak in de klasse **Program** de variabelen voor de Azure-sleutel en het eind punt van uw resource die worden opgehaald uit de omgevings variabelen, met de naam `PERSONALIZER_RESOURCE_KEY` en `PERSONALIZER_RESOURCE_ENDPOINT`. Als u de omgevings variabelen hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die deze uitvoert, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden verderop in deze Quick start gemaakt.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Een Personaler-client maken

Maak vervolgens een methode voor het retour neren van een Personaler-client. De para meter voor de methode is het `PERSONALIZER_RESOURCE_ENDPOINT` en de ApiKey is de `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Voedings gegevens ophalen als rangorde acties

Acties vertegenwoordigen de inhouds keuzes die persoonlijker moeten worden gerangschikt. Voeg de volgende methoden toe aan de klasse Program om de set acties weer te geven die moeten worden gerangschikt.

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Gebruikers voorkeuren voor context ophalen

Voeg de volgende methoden toe aan de klasse Program om de invoer van een gebruiker op te halen vanaf de opdracht regel voor het tijdstip van de dag en de huidige voedsel voorkeur. Deze worden gebruikt als context bij het rangschikken van de acties.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Beide methoden gebruiken de methode `GetKey` om de selectie van de gebruiker te lezen vanaf de opdracht regel. 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>De leer loop maken

De training voor persoonlijker leren is een cyclus van positie-en belonings aanroepen. In deze Quick Start wordt elke classificatie oproep, om de inhoud te personaliseren, gevolgd door een belonings oproep om persoonlijker te vertellen hoe goed de service de inhoud rangschikt. 

Met de volgende code in de `main` methode van het programma wordt een cyclus door lopen van de gebruiker om de voor keuren te stellen op de opdracht regel, waardoor die informatie naar persoonlijkere gebruikers kan worden geclassificeerd, waarin de gerangschikte selectie aan de klant wordt gepresenteerd om uit de lijst te kiezen. vervolgens verstuurt u een vergoeding voor persoonlijk gebruik waarmee wordt aangegeven dat de service de selectie heeft

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Voeg de volgende methoden toe, die [de inhouds keuzes ophalen](#get-food-items-as-rankable-actions)voordat u het code bestand uitvoert:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-a-rank"></a>Een positie aanvragen

Om de positie aanvraag te volt ooien, worden de voor keuren van de gebruiker gevraagd om een `currentContent` van de inhouds keuzes te maken. Het proces kan inhoud maken om uit te sluiten van de positie, weer gegeven als `excludeActions`. De rang aanvraag heeft de acties, eigenschap currentcontext, excludeActions en een unieke positie gebeurtenis-ID (als GUID) nodig om de geclassificeerde reactie te ontvangen. 

Deze Snelstartgids bevat eenvoudige context functies van de dag en de voor keur voor de gebruikers-levens duur. In productie systemen kan het bepalen en [evalueren](concept-feature-evaluation.md) van [acties en functies](concepts-features.md) een niet-triviale zaak zijn.  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Een beloning verzenden

Voor het volt ooien van de vraag, wordt de selectie van de gebruiker uit de opdracht regel opgehaald, waarna een numerieke waarde aan elke selectie wordt toegewezen, waarna de unieke positie gebeurtenis-ID en de numerieke waarde naar de belonings methode worden verzonden.

In deze Quick Start wordt een eenvoudig getal als beloning, een nul of een 1, toegewezen. In productie systemen kan worden bepaald wanneer en wat er moet worden verzonden naar de [belonings](concept-rewards.md) oproep, afhankelijk van uw specifieke behoeften. 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit met de opdracht DotNet `run` van de toepassingsmap.

```console
dotnet run
```

![In het Quick Start-programma wordt een aantal vragen gesteld om gebruikers voorkeuren, ook wel bekend als-functies, te verzamelen en de meest voorkomende actie te bieden.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

De [bron code voor deze Quick](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) start is beschikbaar in de GitHub-opslag plaats voor beelden van personaler.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Hoe Personaler werkt](how-personalizer-works.md)

* [Wat is persoonlijkere?](what-is-personalizer.md)
* [Waar kunt u Personaler gebruiken?](where-can-you-use-personalizer.md)
* [Problemen oplossen](troubleshooting.md)

