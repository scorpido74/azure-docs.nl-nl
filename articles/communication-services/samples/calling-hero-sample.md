---
title: Hero-voorbeeld van groepsgesprek
titleSuffix: An Azure Communication Services sample overview
description: Overzicht van hero-voorbeeld voor aanroepen waarin gebruik wordt gemaakt van Azure Communication Services om ontwikkelaars in staat te stellen meer te weten te komen over de interne mechanismen van het voorbeeld.
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: caee5686695594604f49dcbade54342a9134abc0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943757"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>Aan de slag met het hero-voorbeeld voor groepsgesprekken

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

Het **hero-voorbeeld van groepsgesprekken** laat zien hoe de webclientbibliotheek van Communication Services-aanroepen kan worden gebruikt voor het bouwen van een groepsgesprekservaring.

In deze quickstart over hero-voorbeelden komt u te weten hoe het voorbeeld werkt voordat u het op uw lokale computer gaat uitvoeren. Vervolgens gaat u het voorbeeld in Azure implementeren met behulp van uw eigen Azure Communication Services-resources.

> [!IMPORTANT]
> [Het voorbeeld downloaden uit GitHub](https://github.com/Azure/Communication/tree/master/samples)

## <a name="overview"></a>Overzicht

Het voorbeeld heeft zowel een toepassing aan de clientzijde als een toepassing aan de serverzijde. De **toepassing aan de clientzijde** is een React/Redux-webtoepassing die gebruikmaakt van het Fluent UI-framework van Microsoft. Met deze toepassing worden aanvragen verzonden naar een ASP.NET Core-**toepassing aan de serverzijde** waarmee de toepassing aan de clientzijde kan worden verbonden met Azure. 

Het voorbeeld ziet er als volgt uit:

:::image type="content" source="./media/calling/landing-page.png" alt-text="Schermopname van de landingspagina van de voorbeeldtoepassing.":::

Wanneer de knop Een gesprek starten kiest, wordt door de webtoepassing een toegangstoken voor gebruikers opgehaald bij de toepassing op de server. Vervolgens wordt dit token gebruikt om de client-app met Azure Communication Services te verbinden. Zodra het token is opgehaald, wordt u gevraagd om de camera en de microfoon die u wilt gebruiken, op te geven. Met behulp van de wisselknoppen kunt u uw apparaten in-/uitschakelen:

:::image type="content" source="./media/calling/pre-call.png" alt-text="Schermopname van het scherm voorafgaand aan het gesprek van de voorbeeldtoepassing.":::

Zodra u uw weergavenaam en apparaten hebt geconfigureerd, kunt u deelnemen aan de gesprekssessie. Nu wordt het hoofdcanvas van het gesprek weergegeven. Dit is de locatie van de basisgesprekservaring.

:::image type="content" source="./media/calling/main-app.png" alt-text="Schermopname van het hoofdscherm van de voorbeeldtoepassing.":::

Onderdelen van het hoofdgespreksscherm:

- **Mediagalerie**: Het hoofdgebied waarin de deelnemers worden weergegeven. Als deelnemers hun camera hebben ingeschakeld, wordt hier hun videofeed weergegeven. Elke deelnemer beschikt over een afzonderlijke tegel waarop hun weergavenaam en videostream (indien beschikbaar) worden weergegeven
- **Header**: Dit is het deel met de primaire besturingselementen voor het gesprek waarmee u tussen de instellingen en de zijbalk met deelnemers kunt wisselen, beeld en geluid kunt in- en uitschakelen, het scherm kunt delen en het gesprek kunt verlaten.
- **Zijbalk**: Hier worden informatie over de deelnemers en de instellingen weergegeven wanneer u tussen de besturingselementen in de header wisselt. U kunt het onderdeel verwijderen door op X te klikken in de rechterbovenhoek. In de zijbalk met deelnemers ziet u een lijst met alle deelnemers en een koppeling om meer gebruikers voor het gesprek uit te nodigen. In de zijbalk met instellingen kunt u de microfoon- en camera-instellingen configureren.

Hieronder vindt u meer informatie over de voorwaarden, stappen om het voorbeeld in te stellen en stapsgewijze zelfstudies om bekend te raken met de verschillende onderdelen.

## <a name="prerequisites"></a>Vereisten

- Maak een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie
- [Node.js (12.18.4 en hoger)](https://nodejs.org/en/download/)
- [Visual Studio (2019 en hoger)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2) (vergeet niet de versie te installeren die overeenkomt met uw Visual Studio-instantie, 32-bits of 64-bits)
- Maak een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../quickstarts/create-communication-resource.md) voor meer informatie. Voor deze quickstart moet u de **verbindingsreeks** van uw resource vastleggen.

## <a name="locally-deploy-the-service--client-applications"></a>De service en clienttoepassingen lokaal implementeren

Het voorbeeld voor groepsgesprekken bestaat in feite uit twee toepassingen: ClientApp en de Service.NET-app.

Als u het voorbeeld lokaal wilt implementeren, moeten beide toepassingen worden gestart. Wanneer u de server-app opent vanuit de browser, wordt de lokaal geïmplementeerde ClientApp gebruikt voor de gebruikerservaring.

U kunt het voorbeeld lokaal testen door meerdere browsersessies te openen met de URL van uw gesprek om een gesprek met meerdere gebruikers te simuleren.

### <a name="before-running-the-sample-for-the-first-time"></a>Voordat u het voorbeeld voor de eerste keer uitvoert

1. Open een instantie van PowerShell, Windows Terminal, de opdrachtprompt of een vergelijkbare service en ga naar de map waarnaar u het voorbeeld wilt klonen.
2. `git clone`
3. Ga naar **Gesprek/ClientApp-map** en voer `npm run setup` uit
   1. Als fout 1 wordt weergegeven, zoekt u hierboven in de uitvoer naar een URL die u moet openen om uw client te autoriseren. (De URL ziet er als volgt uit: `app.vssps.visualstudio.com/oauth2/authorize?clientid=...`) Zodra u de URL in een browser hebt bezocht, kopieert u de opdracht uit het browservenster en voert u deze uit.
   2. Voltooi de vorige stap en voer de opdracht `npm run setup-vsts-auth` opnieuw uit.
4. Haal de `Connection String` op uit Azure Portal. Zie [Een Azure Communications-resource maken](../quickstarts/create-communication-resource.md) voor meer informatie over verbindingsreeksen
5. Zodra u de verbindingsreeks hebt opgehaald, voegt u deze toe aan het bestand **Calling/appsetting.json**. U vindt dit bestand in de Service .NET-map. Voer in de variabele uw verbindingsreeks in: `ResourceConnectionString`.

### <a name="local-run"></a>Lokaal uitvoeren

1. Ga naar de map Aanroepen
2. De `Calling.csproj`-oplossing in Visual Studio openen
2. Het `Calling`-project uitvoeren*

*De browser wordt geopend in `localhost:5000` (waar het knooppunt de client-app implementeert). De app wordt niet ondersteund in Internet Explorer.

#### <a name="troubleshooting"></a>Problemen oplossen

- De oplossing wordt niet gebouwd; er worden fouten weergegeven tijdens de installatie/bouw van NPM.

   Probeer de projecten te wissen/opnieuw te bouwen.

## <a name="publish-the-sample-to-azure"></a>Het voorbeeld naar Azure publiceren

1. Klik met de rechtermuisknop op het `Calling`-project en selecteer Publiceren.
2. Maak een nieuw publicatieprofiel en selecteer uw Azure-abonnement.
3. Voeg vóór publicatie uw verbindingsreeks toe met `Edit App Service Settings`, vul `ResourceConnectionString` in als de sleutel en geef uw verbindingsreeks (gekopieerd uit appsettings.json) als de waarde op.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen:

- Vertrouwd raken met [het gebruik van de clientbibliotheek voor aanroepen](../quickstarts/voice-video-calling/calling-client-samples.md)
- Meer informatie over de [mogelijkheden van de clientbibliotheek voor aanroepen](../quickstarts/voice-video-calling/calling-client-samples.md)
- Meer informatie over [de werking van aanroepen](../concepts/voice-video-calling/about-call-types.md)

## <a name="additional-reading"></a>Meer artikelen

- [Azure Communication (preview-versie)](https://github.com/Azure/communication-preview): voor meer informatie over de web-SDK voor aanroepen
- [Redux](https://redux.js.org/): statusbeheer op de client
- [FluentUI](https://developer.microsoft.com/fluentui#/): door Microsoft ondersteunde bibliotheek voor de gebruikersinterface
- [React](https://reactjs.org/): bibliotheek voor het ontwikkelen van gebruikersinterfaces
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true): een framework voor het bouwen van webtoepassingen
