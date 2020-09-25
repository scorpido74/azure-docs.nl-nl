---
title: Hero-voorbeeld van groeps-chat
titleSuffix: An Azure Communication Services sample overview
description: Overzicht van hero-voorbeeld voor chatten waarin gebruik wordt gemaakt van Azure Communication Services om ontwikkelaars in staat te stellen meer te weten te komen over de interne mechanismen van het voorbeeld en hoe u dit kunt wijzigen.
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 295c4bde64ad21a19d21fd48f2556114b26b202d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943668"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>Aan de slag met het hero-voorbeeld van groeps-chat

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> links to our Hero Sample repo need to be updated when the sample is publicly available.
---->

Het **hero-voorbeeld van groeps-chat** laat zien hoe de webclientbibliotheek van de Communication Services-chat kan worden gebruikt voor het bouwen van een groepsgesprekservaring.

In deze quickstart over hero-voorbeelden komt u te weten hoe het voorbeeld werkt voordat u het op uw lokale computer gaat uitvoeren. Vervolgens gaat u het voorbeeld in Azure implementeren met behulp van uw eigen Azure Communication Services-resources.

> [!IMPORTANT]
> [Het voorbeeld downloaden uit GitHub](https://github.com/Azure/Communication/tree/master/samples)

## <a name="overview"></a>Overzicht

Het voorbeeld heeft zowel een toepassing aan de clientzijde als een toepassing aan de serverzijde. De **toepassing** aan de clientzijde is een React/Redux-webtoepassing die gebruikmaakt van het Fluent UI-framework van Microsoft. Met deze toepassing worden aanvragen verzonden naar een ASP.NET Core-**toepassing aan de serverzijde** zodat de toepassing aan de clientzijde kan worden verbonden met Azure. 

Het voorbeeld ziet er als volgt uit:

:::image type="content" source="./media/chat/landing-page.png" alt-text="Schermopname van de landingspagina van de voorbeeldtoepassing.":::

Wanneer u op de knop 'Chat starten' klikt, wordt er met de webtoepassing een token voor gebruikerstoegang opgehaald bij de toepassing aan de serverzijde. Dit token wordt vervolgens gebruikt om de client-app te verbinden met Azure Communication Services. Zodra het token is opgehaald, wordt u gevraagd om uw weergavenaam en emoji op te geven die tijdens chatsessies waaraan u deelneemt, worden weergeven. 

:::image type="content" source="./media/chat/pre-chat.png" alt-text="Schermopname van het venster van de toepassing vóór de chatsessie.":::

Zodra u uw weergavenaam en emoji hebt geconfigureerd, kunt u deelnemen aan de chatsessie. Nu ziet u het hoofdcanvas van de chatruimte waarin zich het belangrijkste van een chatsessie afspeelt.

:::image type="content" source="./media/chat/main-app.png" alt-text="Schermopname van het hoofdscherm van de voorbeeldtoepassing.":::

Onderdelen van het belangrijkste chatscherm:

- **Belangrijkste chatgebied**: Dit is het belangrijkste gebied van een chatsessie, waarin gebruikers berichten kunnen verzenden en ontvangen. Als u berichten wilt verzenden, kunt u daarvoor het invoergebied gebruiken en op Enter drukken (of de verzendknop gebruiken). Ontvangen chatberichten worden gecategoriseerd op afzender met behulp van de juiste naam en emoji. U ziet twee typen meldingen in het chatgebied: 1) Typmeldingen die laten zien dat een gebruiker typt, en 2) meldingen met betrekking tot het verzenden en lezen van berichten.
- **Header**: Hier ziet de gebruiker de titel van de chatthread en de besturingselementen voor het schakelen tussen zijbalken voor deelnemers en instellingen, evenals een knop om de chatsessie af te sluiten.
- **Zijbalk**: Hier worden gegevens over de deelnemers en de instellingen weergegeven wanneer u de besturingselementen in de header gebruikt om heen en weer te schakelen. De zijbalk met de deelnemers bevat een lijst met de deelnemers aan de chatsessie en een koppeling om deelnemers uit te nodigen voor de chatsessie. Via de zijbalk voor de instellingen kunt u de titel van de chatthread configureren. 

Hieronder vindt u meer informatie over de vereisten en stappen voor het instellen van het voorbeeld.

## <a name="prerequisites"></a>Vereisten

- Maak een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie.
- [Node.js (8.11.2 en hoger)](https://nodejs.org/en/download/)
- [Visual Studio (2017 en hoger)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2) (vergeet niet de versie te installeren die overeenkomt met uw Visual Studio-instantie, 32-bits of 64-bits)
- Maak een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../quickstarts/create-communication-resource.md) voor meer informatie. Voor deze quickstart moet u de **verbindingsreeks** van uw resource vastleggen.

## <a name="locally-deploying-the-service--client-app"></a>De service en client-app lokaal implementeren

De voorbeeldchat met één thread bestaat in wezen uit twee 'toepassingen': een client- en een servertoepassing.

Open Visual Studio voor chat.csproj en schakel de foutopsporingsmodus in. Hiermee wordt de front-endservice van de chat gestart. Wanneer de server-app vanuit de browser wordt benaderd, wordt het verkeer omgeleid naar de lokaal geïmplementeerde front-endservice voor de chat.

U kunt het voorbeeld lokaal testen door meerdere browsersessies te openen met de URL van uw chat om een chat met meerdere gebruikers te simuleren.

### <a name="before-running-the-sample-for-the-first-time"></a>Voordat u het voorbeeld voor de eerste keer uitvoert

1. Open een instantie van PowerShell, Windows Terminal, de opdrachtprompt of een vergelijkbare service en ga naar de map waarnaar u het voorbeeld wilt klonen.
2. `git clone`
3. Ga naar de map **Chat/ClientApp** en voer `npm run setup` uit
   1. Als fout 1 wordt weergegeven, zoekt u hierboven in de uitvoer naar een URL die u moet openen om uw client te autoriseren. (De URL ziet er als volgt uit: `app.vssps.visualstudio.com/oauth2/authorize?clientid=...`) Zodra u de URL in een browser hebt geopend, kopieert u de opdracht uit het browservenster en voert u deze uit.
   2. Voltooi de vorige stap en voer de opdracht `npm run setup` opnieuw uit.
4. Haal de `Connection String` op uit Azure Portal. Zie [Een Azure Communications-resource maken](../quickstarts/create-communication-resource.md) voor meer informatie over verbindingsreeksen
5. Nadat u de `Connection String`hebt opgehaald, voegt u de verbindingsreeks toe aan het bestand **Chat/appsettings.json** dat zich in de map Chat bevindt. Voer in de variabele uw verbindingsreeks in: `ResourceConnectionString`.

### <a name="local-run"></a>Lokaal uitvoeren

1. Ga naar de map Chat
2. Open oplossing `Chat.csproj` in Visual Studio
3. Voer project `Chat` uit.*

*De browser wordt geopend op localhost:5000 (waar het knooppunt de client-app implementeert). De app wordt niet ondersteund in Internet Explorer.

#### <a name="troubleshooting"></a>Problemen oplossen

- De oplossing kan niet worden gebouwd; er worden fouten weergegeven tijdens de installatie/bouw van NPM

De C#-oplossing opschonen/herbouwen

## <a name="publish-the-sample-to-azure"></a>Het voorbeeld naar Azure publiceren

1. Klik met de rechtermuisknop op project `Chat` en selecteer Publiceren.
2. Maak een nieuw publicatieprofiel en selecteer uw Azure-abonnement.
3. Voeg vóór publicatie uw verbindingsreeks toe met `Edit App Service Settings`, vul `ResourceConnectionString` in als de sleutel en geef uw verbindingsreeks (gekopieerd uit appsettings.json) als de waarde op.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen:

- Meer informatie over [chatconcepten](../concepts/chat/concepts.md)
- Uzelf bekend maken met de [chatclientbibliotheek](../concepts/chat/sdk-features.md)

## <a name="additional-reading"></a>Meer artikelen

- [Azure Communication (preview-versie)](https://github.com/Azure/communication-preview): voor meer informatie over de web-SDK voor chatten
- [Redux](https://redux.js.org/): statusbeheer op de client
- [FluentUI](https://developer.microsoft.com/fluentui#/): door Microsoft ondersteunde bibliotheek voor de gebruikersinterface
- [React](https://reactjs.org/): bibliotheek voor het ontwikkelen van gebruikersinterfaces
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true): een framework voor het bouwen van webtoepassingen
