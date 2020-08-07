---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 07/30/2020
ms.openlocfilehash: 55bc13caec8a48ed058ea38bdc0ebe487839eef3
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461099"
---
[Referentiedocumentatie](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Het gebruik van deze quickstart

Er zijn verschillende stappen voor het gebruik van deze quickstart:

* Maak een Personalizer-resource in Azure Portal
* Wijzig voor de Personalizer-resource, op de pagina **Configuratie** in Azure Portal, de frequentie van de modelupdate in een zeer korte interval
* Maak in een code-editor een codebestand en bewerk het codebestand
* Installeer de SDK vanaf de opdrachtregel of terminal
* Voer het codebestand uit vanaf de opdrachtregel of terminal

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw favoriete editor of IDE.

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht dotnet `new` om een nieuwe console-app te maken met de naam `personalizer-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: `Program.cs`.

```console
dotnet new console -n personalizer-quickstart
```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

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

Installeer in de toepassingsmap de Personalizer-clientbibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Als u de Visual Studio-IDE, gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

## <a name="object-model"></a>Objectmodel

De Personalizer-client is een [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet)-object dat wordt geverifieerd bij Azure met behulp van Microsoft.Rest.ServiceClientCredentials, dat uw sleutel bevat.

Als u het beste item van de inhoud wilt hebben, maakt u een [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview) en geeft u dit door aan de methode [client.Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview). Met de methode Rank wordt een RankResponse geretourneerd.

Als u een beloningsscore naar een Personalizer wilt verzenden, maakt u een [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview) en geeft u dit door aan de methode [client.Reward](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview).

Het bepalen van de beloningsscore in deze quickstart is triviaal. In een productiesysteem kan de bepaling wat van invloed is op de [beloningsscore](../concept-rewards.md) en in welke mate, een complex proces zijn, dat u in de loop van de tijd zou kunnen wijzigen. Deze ontwerpbeslissing moet een van de belangrijkste beslissingen in uw Personalizer-architectuur zijn.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Personalizer-clientbibliotheek voor .NET:

* [Personalizer-client maken](#create-a-personalizer-client)
* [Positie API](#request-the-best-action)
* [Beloning API](#send-a-reward)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open vanuit de projectmap het bestand **Program.cs** in uw favoriete editor of IDE. Vervang de bestaande `using`-code door de volgende `using`-instructies:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Informatie over Personalizer-resource toevoegen

Bewerk in de **Programma**-klasse de sleutel- en eindpuntvariabelen boven aan het codebestand bij voor de Azure-sleutel en het Azure-eindpunt van uw resource. 

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Personalizer-client maken

Maak vervolgens een methode voor het retourneren van een Personalizer-client. De parameter voor de methode is `PERSONALIZER_RESOURCE_ENDPOINT` en de ApiKey is `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Levensmiddelen ophalen als te rangschikken acties

Acties vertegenwoordigen de inhoudskeuzes waaruit Personalizer het beste inhoudsitem moet selecteren. Voeg de volgende methoden aan de klasse Programma toe om de reeks acties en hun functies te representeren. 

[!code-csharp[Food items as actions](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Gebruikersvoorkeuren voor context ophalen

Voeg de volgende methoden aan de klasse Programma toe om vanaf de opdrachtregel de invoer van een gebruiker op te halen voor het tijdstip van de dag en de huidige voedselvoorkeur. Deze worden als contextkenmerken gebruikt.

[!code-csharp[Present time out day preference to the user](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createUserFeatureTastePreference)]

Beide methoden maken gebruik van de methode `GetKey` om de vanaf de opdrachtregel selectie van de gebruiker te lezen.

[!code-csharp[Read user's choice from the command line](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>De leerlus maken

De Personalizer-leerlus is een cyclus van [Positie](#request-the-best-action)- en [Beloning](#send-a-reward)-aanroepen. In deze quickstart wordt elke Positie-oproep, voor het personaliseren van de inhoud, gevolgd door een Beloning-oproep om Personalizer te laten weten hoe goed de service is uitgevoerd.

Met de volgende code wordt herhaaldelijk vanaf de opdrachtregel de cyclus voor het vragen naar de voorkeuren van de gebruiker doorlopen. Deze gegevens worden naar Personalizer gestuurd om de beste actie te selecteren, waarbij de selectie aan de klant in een lijst wordt gepresenteerd om een keuze te kunnen maken. Vervolgens wordt er een beloningsscore naar Personalizer verzonden waarmee wordt aangegeven hoe goed de service de selectie heeft uitgevoerd.

[!code-csharp[Learning loop](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=mainLoop)]

Voeg de volgende methoden toe, waarmee [de inhoudskeuzes worden opgehaald](#get-food-items-as-rankable-actions), voordat u het codebestand uitvoert:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>De beste actie aanvragen

Als u de Positie-aanvraag wilt voltooien, wordt naar de voorkeuren van de gebruiker gevraagd om een `currentContent` van de inhoudskeuzes te maken. Het proces kan inhoud maken die van de acties wordt uitgesloten, weergegeven als `excludeActions`. De Positie-aanvraag heeft de acties en hun kenmerken, currentcontext-kenmerken, excludeActions en een unieke gebeurtenis-id nodig om de reactie te kunnen ontvangen.

Deze quickstart bevat eenvoudige contextkenmerken over het tijdstip van de dag en de voedselvoorkeur van de gebruiker. In productiesystemen kan het bepalen en [evalueren](../concept-feature-evaluation.md) van [acties en kenmerken](../concepts-features.md) een ingewikkelde zaak zijn.

[!code-csharp[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Een beloning verzenden

Om ervoor te zorgen dat de beloningsscore de Beloning-aanvraag instuurt, haalt het programma de selectie van de gebruiker op vanaf de opdrachtregel, wijst een numerieke waarde toe aan de selectie en verzendt vervolgens de unieke gebeurtenis-id en de beloningsscore als de numerieke waarde naar de Beloning API.

In deze quickstart wordt een eenvoudig getal, een 0 of een 1, aan een beloningsscore toegewezen. In productiesystemen kan het bepalen van wanneer en wat er naar de [Beloning](../concept-rewards.md)-oproep moet worden gezonden een ingewikkelde zaak zijn, afhankelijk van uw specifieke behoeften.

[!code-csharp[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=reward)]

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit vanuit uw toepassingsmap met de opdracht dotnet `run`.

```console
dotnet run
```

![Het quickstart-programma stelt een aantal vragen om gebruikersvoorkeuren te verzamelen, zogenaamde kenmerken. Vervolgens wordt de beste actie geboden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

De [broncode voor deze quickstart](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer) is beschikbaar.
