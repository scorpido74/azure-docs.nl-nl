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
ms.openlocfilehash: 7fd63841cabd91d46dd311f571fd100bbcfdd0fe
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122885"
---
[Voorbeeld van naslagdocumentatie](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [(NuGet)-voorbeelden](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Samples](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Deze quickstart gebruiken

Er zijn verschillende stappen om deze quickstart te gebruiken:

* Maak in de Azure-portal een personalizerbron
* Wijzig in de Azure-portal voor de personalizerbron op de pagina **Configuratie** de frequentie van modelupdate in een zeer kort interval
* Maak in een codeeditor een codebestand en bewerk het codebestand
* Installeer de SDK in de opdrachtregel of terminal vanaf de opdrachtregel
* Voer in de opdrachtregel of terminal het codebestand uit

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw voorkeurseditor of IDE.

Gebruik in een consolevenster (zoals cmd, PowerShell of `new` Bash) de dotnetopdracht `personalizer-quickstart`om een nieuwe console-app met de naam te maken. Met deze opdracht wordt een eenvoudig "Hello World" `Program.cs`C#-project gemaakt met één bronbestand: .

```console
dotnet new console -n personalizer-quickstart
```

Wijzig uw map in de nieuw gemaakte app-map. U de toepassing bouwen met:

```console
dotnet build
```

De buildoutput mag geen waarschuwingen of fouten bevatten.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="install-the-sdk"></a>De SDK installeren

Installeer in de toepassingsmap de personalizerclientbibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Als u de Visual Studio IDE gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

## <a name="object-model"></a>Objectmodel

De Personalizer-client is een [PersonalizerClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) dat zich verifieert naar Azure met behulp van Microsoft.Rest.ServiceClientCredentials, dat uw sleutel bevat.

Als u het beste item van de inhoud wilt aanvragen, maakt u een [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)en geeft u deze door aan [de client. Rangmethode.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) De methode Rang retourneert een RankResponse.

Als u een beloningsscore naar Personalizer wilt verzenden, maakt u een [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)en geeft u deze door aan de [klant. Beloningsmethode.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview)

Het bepalen van de beloningsscore, in deze quickstart is triviaal. In een productiesysteem, de bepaling van wat de [beloningsscore](../concept-rewards.md) beïnvloedt en door hoeveel een complex proces kan zijn, dat u beslissen om in tijd te veranderen. Deze ontwerpbeslissing moet een van de belangrijkste beslissingen in uw Personalizer-architectuur zijn.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken uitvoeren met de personalizerclientbibliotheek voor .NET:

* [Een gepersonaliseerde client maken](#create-a-personalizer-client)
* [Rank-API](#request-the-best-action)
* [Reward API](#send-a-reward)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open in de projectmap het **Program.cs** bestand in uw voorkeurseditor of IDE. Vervang de `using` bestaande code `using` door de volgende richtlijnen:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Persoonlijkeinformatie toevoegen

Maak in de klasse **Program** variabelen voor de Azure-sleutel en het eindpunt `PERSONALIZER_RESOURCE_KEY` van `PERSONALIZER_RESOURCE_ENDPOINT`uw resource die zijn opgehaald uit de omgevingsvariabelen, met de naam en . Als u de omgevingsvariabelen hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die wordt uitgevoerd, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later in deze quickstart gemaakt.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Een gepersonaliseerde client maken

Maak vervolgens een methode om een Personalizer-client terug te sturen. De parameter voor de `PERSONALIZER_RESOURCE_ENDPOINT` methode is de `PERSONALIZER_RESOURCE_KEY`en de ApiKey is de .

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Krijg etenswaren als rankable acties

Acties vertegenwoordigen de inhoudskeuzes waaruit u wilt dat Personalizer het beste inhoudsitem selecteert. Voeg de volgende methoden toe aan de klasse Programma om de set acties en hun functies weer te geven.

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Gebruikersvoorkeuren voor context opvragen

Voeg de volgende methoden toe aan de klasse Programma om de invoer van een gebruiker van de opdrachtregel te krijgen voor het tijdstip van de dag en de huidige voedselvoorkeur. Deze worden gebruikt als contextfuncties.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Beide methoden gebruiken `GetKey` de methode om de selectie van de gebruiker uit de opdrachtregel te lezen.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>De leerlus maken

De Personalizer learning loop is een cyclus van [Rank en](#request-the-best-action) [Reward](#send-a-reward) calls. In deze quickstart wordt elk Rang-gesprek, om de inhoud te personaliseren, gevolgd door een beloningsgesprek om Personalizer te vertellen hoe goed de service heeft gepresteerd.

De volgende code lussen door een cyclus van het vragen van de gebruiker hun voorkeuren op de command line, het verzenden van die informatie naar Personalizer om de beste actie te selecteren, de presentatie van de selectie aan de klant om uit te kiezen uit de lijst, dan het verzenden van een beloning score naar Personalizer signalering hoe goed de dienst deed in haar selectie.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Voeg de volgende methoden toe, waarmee [de inhoudsopties worden uitgevoerd](#get-food-items-as-rankable-actions)voordat u het codebestand uitvoert:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>De beste actie aanvragen

Om de rank-aanvraag te voltooien, vraagt het programma `currentContent` de voorkeuren van de gebruiker om een van de inhoudskeuzes te maken. Het proces kan inhoud maken om uit `excludeActions`te sluiten van de acties, weergegeven als . De rangaanvraag heeft de acties en hun functies, huidigeContextfuncties, excludeActions en een unieke gebeurtenis-id nodig om het antwoord te ontvangen.

Deze quickstart heeft eenvoudige contextfuncties van het tijdstip van de dag en de voorkeur voor gebruikersvoeding. In productiesystemen kan het bepalen en [evalueren van](../concept-feature-evaluation.md) acties [en functies](../concepts-features.md) een niet-triviale zaak zijn.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Stuur een beloning

Om de beloningsscore te krijgen om het beloningsverzoek in te sturen, krijgt het programma de selectie van de gebruiker uit de opdrachtregel, wijst het een numerieke waarde toe aan de selectie en stuurt het vervolgens de unieke gebeurtenis-ID en de beloningsscore als de numerieke waarde naar de Reward API.

Deze quickstart kent een eenvoudig getal toe als beloningsscore, een nul of een 1. In productiesystemen kan bepalen wanneer en wat u naar de [Reward-oproep](../concept-rewards.md) moet worden verzonden, afhankelijk van uw specifieke behoeften.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit `run` met de dotnet-opdracht in uw toepassingsmap.

```console
dotnet run
```

![Het quickstart-programma stelt een paar vragen om gebruikersvoorkeuren te verzamelen, bekend als functies, en biedt vervolgens de hoogste actie.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

De [broncode voor deze quickstart](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) is beschikbaar in de Personalizer samples GitHub repository.
