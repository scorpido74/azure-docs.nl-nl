---
title: Inzicht in functiebeheer met Azure App-configuratie
description: Functies in- en uitschakelen met Azure App-configuratie
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8227810c154078fc8424b2cadd373394d07e9730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523727"
---
# <a name="feature-management-overview"></a>Overzicht van functiebeheer

Traditioneel vereist het verzenden van een nieuwe toepassingsfunctie een volledige herschikking van de toepassing zelf. Het testen van een functie vereist vaak meerdere implementaties van de toepassing.  Elke implementatie kan de functie wijzigen of de functie blootstellen aan verschillende klanten voor het testen.  

Feature management is een moderne software-ontwikkeling praktijk die functie release los van code implementatie ontkoppelt en maakt snelle wijzigingen in de beschikbaarheid van functies op aanvraag. Het maakt gebruik van een techniek genaamd *feature flags* (ook bekend als *feature toggles,* *feature switches,* enzovoort) om dynamisch beheer van de levenscyclus van een functie.

Functiebeheer helpt ontwikkelaars bij het aanpakken van de volgende problemen:

* **Beheer van codebranch**: Gebruik functievlaggen om nieuwe toepassingsfunctionaliteit te verpakken die momenteel in ontwikkeling is. Dergelijke functionaliteit is standaard "verborgen". U de functie veilig verzenden, ook al is deze niet voltooid, en blijft het slapend in productie. Met behulp van deze aanpak, *genaamd donkere implementatie,* u al uw code vrijgeven aan het einde van elke ontwikkelingscyclus. U hoeft codebranches niet langer te onderhouden in meerdere ontwikkelingscycli, omdat een bepaalde functie meer dan één cyclus vereist om te voltooien.
* **Test in productie**: Gebruik functievlaggen om vroege toegang te verlenen tot nieuwe functionaliteit in de productie. U bijvoorbeeld de toegang tot teamleden of interne bètatesters beperken. Deze gebruikers zullen de full-fidelity productie-ervaring ervaren in plaats van een gesimuleerde of gedeeltelijke ervaring in een testomgeving.
* **Flighting**: Gebruik functievlaggen om stapsgewijs nieuwe functionaliteit uit te rollen voor eindgebruikers. U eerst een klein percentage van uw gebruikerspopulatie targeten en dat percentage geleidelijk in de loop van de tijd verhogen.
* **Instant kill switch**: Feature flags bieden een inherent vangnet voor het vrijgeven van nieuwe functionaliteit. U toepassingsfuncties in- en uitschakelen zonder code opnieuw te implementeren. Indien nodig u een functie snel uitschakelen zonder uw toepassing opnieuw te bouwen en opnieuw te implementeren.
* **Selectieve activering:** gebruik functievlaggen om uw gebruikers te segmenteren en een specifieke set functies aan elke groep te leveren. Mogelijk hebt u een functie die alleen werkt op een bepaalde webbrowser. U een functievlag definiëren, zodat alleen gebruikers van die browser de functie kunnen zien en gebruiken. Met deze aanpak u de ondersteunde browserlijst later eenvoudig uitbreiden zonder dat u wijzigingen in de code hoeft aan te brengen.

## <a name="basic-concepts"></a>Basisbegrippen

Hier zijn een aantal nieuwe termen met betrekking tot feature management:

* **Functievlag**: Een functievlag is een variabele met een binaire status van *aan* of *uit*. De functievlag heeft ook een bijbehorend codeblok. De status van de functievlag activeert of het codeblok wordt uitgevoerd.
* **Feature manager**: Een feature manager is een applicatiepakket dat de levenscyclus van alle functievlaggen in een toepassing verwerkt. De functiemanager biedt ook extra functionaliteit, waaronder het incachen van functievlaggen en het bijwerken van hun status.
* **Filter:** een filter is een regel voor het evalueren van de status van een functievlag. Mogelijke filters zijn gebruikersgroepen, apparaat- of browsertypen, geografische locaties en tijdvensters.

Een effectieve implementatie van feature management bestaat uit ten minste twee componenten die in overleg werken:

* Een toepassing die gebruik maakt van functievlaggen.
* Een aparte opslagplaats die de functievlaggen en hun huidige statussen opslaat.

## <a name="using-feature-flags-in-your-code"></a>Functievlaggen gebruiken in uw code

Het basispatroon voor het implementeren van functievlaggen in een toepassing is eenvoudig. Een functievlag is een Booleaanse statusvariabele die een voorwaardelijke instructie in uw code regelt:

```csharp
if (featureFlag) {
    // Run the following code
}
```

U de `featureFlag` waarde van statisch instellen.

```csharp
bool featureFlag = true;
```

U de status van de vlag evalueren op basis van bepaalde regels:

```csharp
bool featureFlag = isBetaUser();
```

U het voorwaardelijke instellen uitbreiden om toepassingsgedrag in te stellen voor een van beide statussen:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Opslagplaats functievlag

Als u functievlaggen effectief wilt gebruiken, moet u alle functievlaggen die in een toepassing worden gebruikt, extern maken. Hiermee u functievlagstatussen wijzigen zonder de toepassing zelf te wijzigen en opnieuw te implementeren.

Azure App Configuration biedt een gecentraliseerde opslagplaats voor functievlaggen. U het gebruiken om verschillende soorten functievlaggen te definiëren en hun toestanden snel en vol vertrouwen te manipuleren. U vervolgens de app-configuratiebibliotheken gebruiken voor verschillende programmeertaalframeworks om eenvoudig toegang te krijgen tot deze functievlaggen vanuit uw toepassing.

[Gebruik functievlaggen in een ASP.NET Core-app](./use-feature-flags-dotnet-core.md) laat zien hoe de .NET Core App Configuration provider en Feature Management libraries samen worden gebruikt om functievlaggen voor uw ASP.NET webapplicatie te implementeren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Functievlaggen toevoegen aan een ASP.NET Core-web-app](./quickstart-feature-flag-aspnet-core.md)  
