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
ms.openlocfilehash: 0b09f15eb05a94bb62f4484ac362467fbdb0af8b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188851"
---
[Reference documentation](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | NuGet-voor[beelden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples) ([Source code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [package)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | voor referentie documentatie

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Deze Snelstartgids gebruiken

Er zijn verschillende stappen voor het gebruik van deze Snelstartgids:

* Maak een persoonlijke resource in de Azure Portal
* Wijzig in de Azure Portal, voor de resource Personaler op de pagina **configuratie** , de update frequentie van het model in een zeer korte interval
* Maak in een code-editor een code bestand en bewerk het code bestand
* Vanaf de opdracht regel of Terminal installeert u de SDK vanaf de opdracht regel
* Voer in de opdracht regel of Terminal het code bestand uit

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET core-toepassing in uw voorkeurs editor of IDE.

Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht DotNet `new` om een nieuwe console-app met de naam `personalizer-quickstart`te maken. Met deze opdracht maakt u een eenvoudig ' Hallo wereld ' C#-project met één bron `Program.cs`bestand:.

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

Om te vragen om één beste item van de inhoud, maakt u een [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)en geeft u het door aan de [client. Positie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) methode. De methode Rank retourneert een RankResponse.

Als u een belonings score naar een persoonlijker wilt verzenden, maakt u een [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)en geeft u het door aan de [client. Belonings](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) methode.

Het bepalen van de belonings Score in deze Snelstartgids is trivial. In een productie systeem kan de bepaling van wat invloed heeft op de [belonings Score](../concept-rewards.md) en wat een complex proces kan zijn, u mogelijk besluiten om de tijd te wijzigen. Deze ontwerp beslissing moet een van de belangrijkste beslissingen van uw Personaler-architectuur zijn.

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Personaler-client bibliotheek voor .NET:

* [Een Personaler-client maken](#create-a-personalizer-client)
* [Rank-API](#request-the-best-action)
* [Belonings-API](#send-a-reward)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het **Program.cs** -bestand in de map van het project in uw voorkeurs editor of IDE. Vervang de bestaande `using` code door de volgende `using` instructies:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Informatie over persoonlijker resources toevoegen

Maak in de klasse **Program** de variabelen voor de Azure-sleutel en het eind punt van uw resource die worden opgehaald `PERSONALIZER_RESOURCE_KEY` uit `PERSONALIZER_RESOURCE_ENDPOINT`de omgevings variabelen met de naam en. Als u de omgevings variabelen hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die deze uitvoert, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden verderop in deze Quick start gemaakt.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Een Personaler-client maken

Maak vervolgens een methode voor het retour neren van een Personaler-client. De para meter voor de methode is `PERSONALIZER_RESOURCE_ENDPOINT` de en de ApiKey is `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Voedings gegevens ophalen als rangorde acties

Acties vertegenwoordigen de inhouds keuzes waaruit u het beste inhouds item wilt selecteren. Voeg de volgende methoden toe aan de klasse Program om de set acties en hun functies weer te geven. 

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Gebruikers voorkeuren voor context ophalen

Voeg de volgende methoden toe aan de klasse Program om de invoer van een gebruiker op te halen vanaf de opdracht regel voor het tijdstip van de dag en de huidige voedsel voorkeur. Deze worden gebruikt als context functies.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Beide methoden gebruiken de `GetKey` -methode om de selectie van de gebruiker te lezen vanaf de opdracht regel.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>De leer loop maken

De training voor persoonlijker leren is een cyclus van [positie](#request-the-best-action) -en [belonings](#send-a-reward) aanroepen. In deze Quick Start wordt elke classificatie oproep, om de inhoud te personaliseren, gevolgd door een belonings oproep om persoonlijker te vertellen hoe goed de service wordt uitgevoerd.

Met de volgende code wordt een cyclus voor het bepalen van de voor keuren van de gebruiker op de opdracht regel weer gegeven. deze gegevens worden naar persoonlijk gepersonaliseerd om de beste actie te selecteren, waarbij de selectie voor de klant wordt weer gegeven in de lijst. vervolgens wordt er een belonings Score verzonden naar Personaler waarmee wordt aangegeven hoe goed de service is geselecteerd.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Voeg de volgende methoden toe, die [de inhouds keuzes ophalen](#get-food-items-as-rankable-actions)voordat u het code bestand uitvoert:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>De beste actie aanvragen

Om de positie aanvraag te volt ooien, worden de voor keuren van de gebruiker `currentContent` gevraagd om een van de inhouds keuzes te maken. Het proces kan inhoud maken om uit te sluiten van de acties, `excludeActions`weer gegeven als. De rang aanvraag heeft de acties en hun functies, eigenschap currentcontext functies, excludeActions en een unieke gebeurtenis-ID nodig om de reactie te ontvangen.

Deze Snelstartgids bevat eenvoudige context functies van de dag en de voor keur voor de gebruikers-levens duur. In productie systemen kan het bepalen en [evalueren](../concept-feature-evaluation.md) van [acties en functies](../concepts-features.md) een niet-triviale zaak zijn.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Een beloning verzenden

Om de belonings score te verkrijgen die in het belonings verzoek moet worden verzonden, haalt het programma de selectie van de gebruiker op via de opdracht regel, wijst een numerieke waarde toe aan de selectie. vervolgens worden de unieke gebeurtenis-ID en de belonings score als numerieke waarde naar de belonings-API verzonden.

In deze Quick Start wordt een eenvoudig getal met een belonings Score, ofwel een nul of een 1, toegewezen. In productie systemen kan worden bepaald wanneer en wat er moet worden verzonden naar de [belonings](../concept-rewards.md) oproep, afhankelijk van uw specifieke behoeften.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit met de `run` opdracht DotNet in de map van de toepassing.

```console
dotnet run
```

![In het Quick Start-programma wordt een aantal vragen gesteld om gebruikers voorkeuren, ook wel bekend als-functies, te verzamelen en de meest voorkomende actie te bieden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

De [bron code voor deze Quick](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) start is beschikbaar in de GitHub-opslag plaats voor beelden van personaler.
