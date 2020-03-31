---
title: Een zoek-app maken in Azure-portal
titleSuffix: Azure Cognitive Search
description: Voer de wizard App maken (voorbeeld) uit om HTML-pagina's en -scripten voor een operationele web-app te genereren. De pagina bevat een zoekbalk, resultaatgebied, zijbalk en ondersteuning voor het voortypen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/25/2020
ms.openlocfilehash: 248ef093601eda7a180a6465ccb97e6fc1c9fe41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369713"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>Snelstart: een zoek-app maken in de portal (Azure Cognitive Search)

Gebruik de wizard **Zoekapp maken** van de portal om een downloadbare web-app in 'localhost'-stijl te genereren die in een browser wordt uitgevoerd. Afhankelijk van de configuratie is de gegenereerde app bij het eerste gebruik operationeel, met een live verbinding met een externe index. Een standaard-app kan een zoekbalk, resultaatgebied, sidebar-filters en ondersteuning voor het typen bevatten.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 

## <a name="prerequisites"></a>Vereisten

Upgrade naar de [nieuwste versie van Microsoft Edge](https://www.microsoft.com/edge) of gebruik de Chrome-browser van Google voor deze quickstart.

[Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U gebruik maken van een gratis service voor deze quickstart. 

[Maak een index](search-create-index-portal.md) om te gebruiken als basis van uw toepassing. 

Deze quickstart maakt gebruik van de ingebouwde voorbeeldgegevens en indexen voor onroerend goed omdat er miniatuurafbeeldingen zijn (de wizard ondersteunt het toevoegen van afbeeldingen aan de resultatenpagina). Als u de index wilt maken die in deze oefening wordt gebruikt, voert u de wizard **Gegevens importeren** uit en kiest u de gegevensbron voor *onroerend goed-ons-voorbeeld.*

![gegevensbronpagina voor voorbeeldgegevens](media/search-create-app-portal/import-data-realestate.png)

Wanneer de index klaar is voor gebruik, gaat u verder met de volgende stap.

## <a name="start-the-wizard"></a>De wizard starten

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en [zoek uw zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Selecteer op de pagina Overzicht in de koppelingen in het midden van de pagina De optie **Indexen**. 

1. Kies *realestate-us-sample-index* uit de lijst met bestaande indexen.

1. Selecteer op de indexpagina bovenaan de optie **Zoek-app maken (voorbeeld)** om de wizard te starten.

1. Selecteer op de eerste wizardpagina **Cross Origin Resource Sharing (CORS) inschakelen** om CORS-ondersteuning toe te voegen aan uw indexdefinitie. Deze stap is optioneel, maar uw lokale web-app maakt geen verbinding met de externe index zonder deze.

## <a name="configure-search-results"></a>Zoekresultaten configureren

De wizard biedt een basisindeling voor gerenderde zoekresultaten met ruimte voor een miniatuurafbeelding, een titel en beschrijving. Het ondersteunen van elk van deze elementen is een veld in uw index dat de gegevens biedt. 

1. Kies in Thumbnail het *miniatuurveld* in de *index voor onroerend goed-ons-voorbeeld.* Dit voorbeeld bevat afbeeldingsminiaturen in de vorm van aan de URL geadresseerde afbeeldingen die zijn opgeslagen in een veld met de naam *miniatuur.* Als uw index geen afbeeldingen heeft, laat u dit veld leeg.

1. Kies in Titel een veld dat de uniciteit van elk document overbrengt. In dit voorbeeld is de vermeldings-id een redelijke selectie.

1. Kies in Beschrijving een veld met details waarmee iemand kan beslissen of hij doorklikt naar dat specifieke document.

![gegevensbronpagina voor voorbeeldgegevens](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Een zijbalk toevoegen

De zoekservice ondersteunt gefacetteerde navigatie, die vaak wordt weergegeven als een zijbalk. Facetten zijn gebaseerd op filterbare en facetable velden, zoals uitgedrukt in het indexschema.

In Azure Cognitive Search is gefacetteerde navigatie een cumulatieve filterervaring. In een categorie worden de resultaten uitgebreid door meerdere filters te selecteren (bijvoorbeeld Seattle en Bellevue selecteren in City). In verschillende categorieën verkleint het selecteren van meerdere filters de resultaten.

> [!TIP]
> U het volledige indexschema in de portal bekijken. Zoek naar de koppeling **Indexdefinitie (JSON)** op de overzichtspagina van elke index. Velden die in aanmerking komen voor gefacetteerde navigatie hebben 'filterbaar: true' en 'facetable: true'-kenmerken.

Accepteer de huidige selectie van facetten en ga verder naar de volgende pagina.


## <a name="add-typeahead"></a>Typeahead toevoegen

Typeahead-functionaliteit is beschikbaar in de vorm van automatisch aanvullen en querysuggesties. De wizard ondersteunt querysuggesties. Op basis van toetsaanslagingangen die door de gebruiker worden geleverd, retourneert de zoekservice een lijst met 'voltooide' querytekenreeksen die als invoer kunnen worden geselecteerd.

Suggesties zijn ingeschakeld voor specifieke velddefinities. De wizard geeft u opties voor het configureren van hoeveel informatie is opgenomen in een suggestie. 

De volgende schermafbeelding toont opties in de wizard, naast een gerenderde pagina in de app. U zien hoe veldselecties worden gebruikt en hoe 'Veldnaam weergeven' wordt gebruikt om etikettering in de suggestie op te nemen of uit te sluiten.

![Configuratie van querysuggesties](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Maken, downloaden en uitvoeren

1. Selecteer **Zoek-app maken** om het HTML-bestand te genereren.

1. Selecteer de optie **Uw app downloaden** om het bestand te downloaden wanneer u daarom wordt gevraagd.

1. Open het bestand. U ziet een pagina die lijkt op de volgende schermafbeelding. Voer een term in en gebruik filters om de resultaten te verkleinen. 

De onderliggende index bestaat uit fictieve, gegenereerde gegevens die zijn gedupliceerd tussen documenten en beschrijvingen komen soms niet overeen met de afbeelding. U een meer samenhangende ervaring verwachten wanneer u een app maakt op basis van uw eigen indexen.

![De app uitvoeren](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling **Alle resources** of **Resourcegroepen** in het linkernavigatiedeelvenster.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u beperkt bent tot drie indexen, indexeerders en gegevensbronnen. U afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Hoewel de standaard-app handig is voor de eerste verkenning en kleine taken, helpt het vroegtijdig bekijken van de API's u om de concepten en werkstroom op een dieper niveau te begrijpen:

> [!div class="nextstepaction"]
> [Een index maken met behulp van de .NET-SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)