---
title: Beheer van configuratie functies Azure-app
description: Een overzicht van hoe Azure-app configuratie kan worden gebruikt om toepassings functies op aanvraag in of uit te scha kelen.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 85228854f6c106c68cedc3eeea81e15fd662774a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898734"
---
# <a name="feature-management-overview"></a>Overzicht van functie beheer

Normaal gesp roken moet u voor het verzenden van een nieuwe toepassings functie een volledige herimplementatie van de toepassing zelf uitvoeren. Als u een functie wilt testen, moet u uw toepassing waarschijnlijk veel keren implementeren om te bepalen wanneer de functie zichtbaar wordt of die wordt weer gegeven.

Feature Management is een moderne software-ontwikkelings praktijk die de functie release van code-implementatie loskoppelt en de beschik baarheid van functies op aanvraag snel kunt wijzigen. Er wordt gebruikgemaakt van een techniek die *functie vlaggen* (ook wel *functie* *Schakel opties, functie switches*, enzovoort) gebruikt om de levens cyclus van een functie dynamisch te beheren.

Met functie beheer kunnen ontwikkel aars de volgende problemen oplossen:

* **Code Branch Management**: functie vlaggen worden vaak gebruikt om de functionaliteit van nieuwe toepassingen die wordt ontwikkeld, te verpakken. Deze functionaliteit is standaard verborgen. U kunt de functie veilig verzenden, zelfs als deze nog niet is voltooid, en deze blijft in productie. Door gebruik te maken van deze methode, een *donkere implementatie*genoemd, kunt u al uw code aan het einde van elke ontwikkel cyclus vrijgeven. Het is niet meer nodig om de code vertakking in meerdere cycli te hand haven omdat er meer dan één cyclus is voltooid.
* **Test in productie**: u kunt functie vlaggen gebruiken om vroegtijdige toegang te verlenen tot nieuwe functionaliteit in de productie omgeving. U kunt deze toegang bijvoorbeeld beperken tot alleen uw team leden of interne bèta testers. Deze gebruikers krijgen de hoogwaardige productie-ervaring, in plaats van een gesimuleerde of gedeeltelijke ervaring in een test omgeving.
* **Flighting**: u kunt ook functie vlaggen gebruiken om de nieuwe functionaliteit voor eind gebruikers te verhogen. U kunt eerst een klein percentage van uw gebruikers populatie richten en dat percentage geleidelijk verhogen na verloop van tijd, nadat u het vertrouwen in de implementatie hebt gewonnen.
* **Instant Kill-Switch**: functie vlaggen bieden een inherenend veiligheids netwerk voor het vrijgeven van nieuwe functionaliteit. Met deze functies kunt u toepassingen eenvoudig in-en uitschakelen. Als dat nodig is, kunt u een functie snel uitschakelen zonder uw toepassing opnieuw te bouwen en opnieuw te implementeren.
* **Selectief activeren**: Hoewel de meeste functie vlaggen alleen bestaan tot de bijbehorende functionaliteit is vrijgegeven, kunnen sommige tijd duren. U kunt functie vlaggen gebruiken om uw gebruikers te segmenteren en een specifieke set functies aan elke groep te leveren. Het is bijvoorbeeld mogelijk dat u een functie hebt die alleen werkt in een bepaalde webbrowser. U kunt een functie vlag definiëren zodat alleen gebruikers van die browser de functie kunnen zien en gebruiken. Met deze methode kunt u de ondersteunde browser lijst later eenvoudig uitbreiden zonder dat u code wijzigingen hoeft aan te brengen.

## <a name="basic-concepts"></a>Basisbegrippen

Hier volgen enkele nieuwe voor waarden met betrekking tot het beheer van functies:

* **Functie vlag**: een functie vlag is een variabele met de binaire status *in* of *uit*. De functie vlag heeft ook een bijbehorend code blok. De status van de functie vlag geeft aan of het code blok wordt uitgevoerd of niet.
* **Functie beheer**: een functie beheerder is een toepassings pakket dat de levens cyclus van alle functie vlaggen in een toepassing afhandelt. Functie beheer biedt doorgaans extra functionaliteit, zoals het opslaan van functie vlaggen in de cache en het bijwerken van de statussen.
* **Filter**: een filter is een regel voor het evalueren van de status van een functie vlag. Een gebruikers groep, een apparaat of een browser type, een geografische locatie en een tijd venster zijn alle voor beelden van wat een filter kan vertegenwoordigen.

Een efficiënte implementatie van feature Management bestaat uit ten minste twee onderdelen die in overleg werken:

* Een toepassing die gebruikmaakt van functie vlaggen.
* Een afzonderlijke opslag plaats waarin de functie vlaggen en de huidige statussen worden opgeslagen.

Hoe deze onderdelen communiceren, worden geïllustreerd in de volgende voor beelden.

## <a name="feature-flag-usage-in-code"></a>Gebruik van functie vlaggen in code

Het basis patroon voor het implementeren van functie vlaggen in een toepassing is eenvoudig. U kunt een functie vlag beschouwen als een Boole-status variabele die wordt gebruikt met een `if` voorwaardelijke instructie in uw code:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Als `featureFlag` is ingesteld op `True`, wordt het Inge sloten code blok uitgevoerd. anders wordt het overgeslagen. U kunt de waarde van `featureFlag` statisch instellen, zoals in het volgende code voorbeeld:

```csharp
bool featureFlag = true;
```

U kunt ook de status van de vlag evalueren op basis van bepaalde regels:

```csharp
bool featureFlag = isBetaUser();
```

Een iets ingewik kelder functie vlag patroon bevat ook een `else`-instructie:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

U kunt dit gedrag echter in het basis patroon herschrijven. [Functie vlaggen gebruiken in een ASP.net core-app](./use-feature-flags-dotnet-core.md) toont de voor delen van het standaardiseren van een eenvoudig code patroon. Bijvoorbeeld:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Opslag plaats voor functie vlaggen

Als u functie vlaggen effectief wilt gebruiken, moet u alle functie vlaggen Externalize die in een toepassing worden gebruikt. Met deze aanpak kunt u de status van de functie vlaggen wijzigen zonder de toepassing zelf te wijzigen en opnieuw te implementeren.

Azure-app configuratie is ontworpen als een centrale opslag plaats voor functie vlaggen. U kunt dit gebruiken om verschillende soorten functie vlaggen te definiëren en hun status snel en met vertrouwen te manipuleren. Vervolgens kunt u de app-configuratie bibliotheken voor verschillende programmeer taal raamwerken gebruiken om gemakkelijk toegang te krijgen tot deze functie vlaggen vanuit uw toepassing.

[Gebruik functie vlaggen in een ASP.net core-app](./use-feature-flags-dotnet-core.md) om te zien hoe de .net core-app configuratie provider en onderdelen beheer bibliotheken samen worden gebruikt om functie vlaggen voor uw ASP.NET-webtoepassing te implementeren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Functie vlaggen toevoegen aan een ASP.NET Core web-app](./quickstart-feature-flag-aspnet-core.md)  
