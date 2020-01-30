---
title: Een zoek-app maken in Azure Portal
titleSuffix: Azure Cognitive Search
description: Voer de wizard app maken (preview) uit om HTML-pagina's en-scripts te genereren voor een operationele web-app. De pagina bevat een zoek balk, resultaten gebied, Sidebar en typeahead-ondersteuning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: 9ed62ea6fdfc0732128ed340cc0bc48481eb73ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841077"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>Snelstartgids: een zoek-app maken in de portal (Azure Cognitive Search)

Gebruik de wizard **Zoek opdracht app maken** om een download bare, ' localhost '-web-app te genereren die in een browser wordt uitgevoerd. Afhankelijk van de configuratie is de gegenereerde app operationeel bij het eerste gebruik, met een live-verbinding met een externe index. Een standaard-app kan een zoek balk, resultaat gebied, Sidebar-filters en typeahead-ondersteuning bevatten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

U kunt het beste een upgrade uitvoeren naar de [nieuwste versie van micro soft Edge](https://www.microsoft.com/edge) of gebruikmaken van de Chrome-browser van Google voor deze Quick Start.

[Een Azure Cognitive Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze Quick Start gebruiken. 

[Maak een index](search-create-index-portal.md) die moet worden gebruikt als basis voor uw toepassing. 

Deze Snelstartgids maakt gebruik van de ingebouwde voorbeeld gegevens en-index van onroerend goed, omdat deze miniatuur afbeeldingen heeft. Als u de index wilt maken die in deze oefening wordt gebruikt, voert u de wizard **gegevens importeren** uit en kiest u de gegevens bron *realestate-US-sample* .

![gegevens bron pagina voor voorbeeld gegevens](media/search-create-app-portal/import-data-realestate.png)

Wanneer de index gereed is voor gebruik, gaat u verder met de volgende stap.

## <a name="start-the-wizard"></a>De wizard starten

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en [Zoek uw zoek service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Selecteer op de pagina overzicht de optie **indexen**in de koppelingen aan het midden van de pagina. 

1. Kies *realestate-US-sample-index* in de lijst met bestaande indexen.

1. Selecteer op de pagina index bovenaan de optie **Zoek-app maken (preview)** om de wizard te starten.

1. Selecteer op de eerste wizard pagina **Cross Origin Resource Sharing (CORS) inschakelen** om cors-ondersteuning toe te voegen aan de index definitie. Deze stap is optioneel, maar uw lokale web-app kan geen verbinding maken met de externe index zonder deze.

## <a name="configure-search-results"></a>Zoek resultaten configureren

De wizard biedt een eenvoudige indeling voor gerenderde Zoek resultaten die ruimte bevat voor een miniatuur afbeelding, een titel en een beschrijving. Het maken van een back-up van elk van deze elementen is een veld in uw index dat de gegevens levert. 

1. Kies in miniatuur het veld *miniatuur* in de index *realestate-US-* -voor beeld. Dit voor beeld gaat over afbeeldings miniaturen in de vorm van URL-geadresseerde afbeeldingen die zijn opgeslagen in een veld met de naam *miniatuur*. Als uw index geen afbeeldingen bevat, laat u dit veld leeg.

1. Kies in titel een veld dat de uniekheid van elk document overbrengt. In dit voor beeld is de vermelding ID een redelijke selectie.

1. Kies in Beschrijving een veld met details waarmee iemand kan bepalen of er moet worden geklikt op het desbetreffende document.

![gegevens bron pagina voor voorbeeld gegevens](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Een zijbalk toevoegen

De zoek service ondersteunt facet navigatie, wat vaak als een zijbalk wordt weer gegeven. Facetten zijn gebaseerd op filter bare en facet bare velden, zoals uitgedrukt in het index schema.

In azure Cognitive Search is facet navigatie een cumulatieve filter ervaring. Binnen een categorie selecteert u meerdere filters om de resultaten uit te breiden (bijvoorbeeld Seattle en Bellevue in plaats te selecteren). Bij categorieën selecteert u meerdere filters om de resultaten te beperken.

> [!TIP]
> U kunt het volledige index schema weer geven in de portal. Zoek naar de koppeling **index definitie (JSON)** op de overzichts pagina van elke index. Velden die in aanmerking komen voor facet navigatie, hebben de kenmerken ' filterable: True ' en ' facetable: True '.

Accepteer de huidige selectie van facetten en ga door naar de volgende pagina.


## <a name="add-typeahead"></a>Typeahead toevoegen

De functionaliteit van typeahead is beschikbaar in de vorm van AutoAanvullen en suggesties voor query's. De wizard ondersteunt query suggesties. Op basis van toetsaanslag invoer die door de gebruiker wordt opgegeven, retourneert de zoek service een lijst met ' voltooide ' query teken reeksen die als invoer kunnen worden geselecteerd.

Suggesties zijn ingeschakeld voor specifieke veld definities. De wizard biedt opties voor het configureren van de hoeveelheid informatie die is opgenomen in een suggestie. 

Op de volgende scherm afbeelding ziet u de opties in de wizard juxtaposed met een gerenderde pagina in de app. U kunt zien hoe veld selecties worden gebruikt, en hoe veld naam weer geven wordt gebruikt voor het opnemen of uitsluiten van labels in de suggestie.

![Configuratie van de query suggestie](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Maken, downloaden en uitvoeren

1. Selecteer **Zoek-app maken** om het HTML-bestand te genereren.

1. Wanneer u hierom wordt gevraagd, selecteert u **uw app downloaden** om het bestand te downloaden.

1. Open het bestand. Er wordt een pagina weer gegeven die vergelijkbaar is met de volgende scherm afbeelding. Voer een term in en gebruik filters om de resultaten te beperken. 

De onderliggende index bestaat uit fictieve, gegenereerde gegevens die zijn gedupliceerd in documenten, en beschrijvingen die soms niet overeenkomen met de installatie kopie. U kunt een meer samenhangende ervaring verwachten wanneer u een app maakt op basis van uw eigen indexen.

![De app uitvoeren](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee aan het einde van een project om te bepalen of u nog steeds de resources nodig hebt die u hebt gemaakt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Hoewel de standaard-app nuttig is voor de eerste keer verkennen en kleine taken, kunt u op een uitgebreidere manier inzicht krijgen in de concepten en de werk stroom op een lager niveau:

> [!div class="nextstepaction"]
> [Een index maken met behulp van de .NET-SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)