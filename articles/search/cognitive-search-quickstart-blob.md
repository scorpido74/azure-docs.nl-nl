---
title: 'Quick Start: een vakkennisset maken in de Azure Portal'
titleSuffix: Azure Cognitive Search
description: In deze Quick Start van de portal leert u hoe u de wizard gegevens importeren gebruikt om cognitieve vaardig heden toe te voegen aan een Indexing-pijp lijn in azure Cognitive Search. Vaardig heden zijn onder andere optische teken herkenning (OCR) en natuurlijke taal verwerking.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/20/2019
ms.openlocfilehash: e2e17ba6af60fa495a03e7d46a07cfe6b66f4e68
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77472414"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Snelstartgids: een Azure Cognitive Search cognitieve-vaardigheids maken in de Azure Portal

Een vaardig heden is een AI-functie waarmee informatie en structuur worden geëxtraheerd uit grote niet-gedifferentieerde tekst-of afbeeldings bestanden, en kan worden geïndexeerd en doorzoekbaar is voor Zoek opdrachten in volledige tekst in azure Cognitive Search. 

In deze Quick Start combineert u services en gegevens in de Azure-Cloud om de vaardig heden te maken. Zodra alles aanwezig is, voert u de wizard **gegevens importeren** in de portal uit om het allemaal samen te halen. Het eind resultaat is een Doorzoek bare index die is gevuld met gegevens die door de AI-verwerking zijn gemaakt en die u in de portal kunt doorzoeken ([Search Explorer](search-explorer.md)).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-services-and-load-data"></a>Services maken en gegevens laden

Deze Snelstartgids maakt gebruik van Azure Cognitive Search, [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/)en [Azure COGNITIVE Services](https://azure.microsoft.com/services/cognitive-services/) voor de AI. 

Omdat de werk belasting zo klein is, wordt Cognitive Services op de achtergrond getikt om gratis verwerking te bieden voor Maxi maal 20 trans acties. Voor een dergelijke kleine gegevensset kunt u het maken of koppelen van een Cognitive Services resource overs Laan.

1. [Download de voorbeeldgegevens](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) die bestaan uit een kleine set van verschillende typen bestanden. Pak de bestanden uit.

1. [Een Azure Storage-account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) of [een bestaand account zoeken](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Kies dezelfde regio als Azure Cognitive Search om bandbreedte kosten te voor komen. 
   
   Kies het account type StorageV2 (algemeen gebruik v2) als u de Knowledge Store-functie later in een andere walkthrough wilt proberen. Anders kiest u een wille keurig type.

1. Open de pagina's van de BLOB Services en maak een container. U kunt het standaard niveau voor open bare toegang gebruiken. 

1. Klik in container op **uploaden** om de voorbeeld bestanden te uploaden die u in de eerste stap hebt gedownload. Merk op dat u een breed scala aan inhouds typen hebt, waaronder afbeeldingen en toepassings bestanden die niet in volledige tekst kunnen worden doorzocht in de oorspronkelijke indeling.

   ![Bronbestanden in Azure-blobopslag](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Een Azure Cognitive Search-service maken](search-create-service-portal.md) of [een bestaande service zoeken](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). U kunt een gratis service voor deze Quick Start gebruiken.

U bent nu klaar om door te gaan met de wizard gegevens importeren.

## <a name="run-the-import-data-wizard"></a>De wizard gegevens importeren uitvoeren

Klik op de pagina overzicht van de zoek service op **gegevens importeren** op de opdracht balk om een cognitieve verrijking in te stellen in vier stappen.

  ![Opdracht Gegevens importeren](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1---create-a-data-source"></a>Stap 1: een gegevens bron maken

1. Kies in **verbinding maken met uw gegevens** **Azure Blob-opslag**, selecteer het opslag account en de container die u hebt gemaakt. Geef een naam op voor de gegevensbron en gebruik standaardwaarden voor de rest. 

   ![Azure-blobconfiguratie](./media/cognitive-search-quickstart-blob/blob-datasource.png)

    Ga door naar de volgende pagina.

### <a name="step-2---add-cognitive-skills"></a>Stap 2: cognitieve vaardig heden toevoegen

Configureer vervolgens AI-verrijking om OCR, afbeeldings analyse en natuurlijke taal verwerking aan te roepen. 

1. Voor deze Snelstartgids gebruiken we de **gratis** Cognitive Services-resource. De voorbeeld gegevens bestaan uit 14 bestanden. de gratis toewijzing van 20 trans acties op Cognitive Services is voldoende voor deze Quick Start. 

   ![Cognitive Services toevoegen](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Vouw **verrijkingen toevoegen** uit en maak vier selecties. 

   Schakel OCR in om de vaardig heden voor afbeeldings analyse toe te voegen aan de wizard pagina.

   Stel granulariteit in op pagina's om tekst in kleinere segmenten op te splitsen. Verschillende tekst vaardigheden zijn beperkt tot vijf KB-invoer.

   Kies entiteits herkenning (personen, organisaties, locaties) en vaardig heden voor afbeeldings analyse.

   ![Cognitive Services toevoegen](media/cognitive-search-quickstart-blob/skillset.png)

   Ga door naar de volgende pagina.

### <a name="step-3---configure-the-index"></a>Stap 3: de index configureren

Een index bevat uw Doorzoek bare inhoud en de wizard **gegevens importeren** kan meestal het schema voor u maken door de gegevens bron te bemonsteren. In deze stap controleert u het gegenereerde schema en kunt u alle instellingen wijzigen. Hieronder ziet u het standaard schema dat is gemaakt voor de demo-BLOB-gegevensset.

De wizard biedt goede standaardinstellingen voor deze snelstart:  

+ Standaard velden zijn gebaseerd op Eigenschappen voor bestaande blobs plus nieuwe velden die een verrijkings uitvoer (bijvoorbeeld `people` `organizations`,, `locations`) bevatten. Gegevens typen worden afgeleid van meta gegevens en door middel van gegevens bemonstering.

+ De standaard document sleutel is *metadata_storage_path* (geselecteerd omdat het veld unieke waarden bevat).

+ Standaard kenmerken kunnen worden **opgehaald** en **doorzoekbaar**. **Doorzoekbaar** staat het zoeken in volledige tekst in een veld toe. **Ophalenbaar** betekent dat veld waarden in resultaten kunnen worden geretourneerd. De wizard gaat ervan uit dat deze velden ophaalbaar en doorzoekbaar moeten zijn omdat u ze hebt gemaakt via een set vaardigheden.

  ![Indexvelden](media/cognitive-search-quickstart-blob/index-fields.png)

Let op het doorgehaalde vinkje en het vraagteken in de kolom **Ophaalbaar** voor het veld `content`. Voor blob-documenten met veel tekst bevat het veld `content` het grootste deel van het bestand, mogelijk wel duizenden regels. Een veld zoals dit is in Zoek resultaten onhandiger en u moet dit voor deze demo uitsluiten. 

Als u echter bestands inhoud wilt door geven aan client code, moet u ervoor zorgen dat **ophalen** is geselecteerd. Als dat niet het geval is, kunt u dit kenmerk uitschakelen als de geëxtraheerde `organizations`elementen `locations`(zoals `people`,,, enzovoort) voldoende zijn. `content`

Als u een veld markeert als **Ophaalbaar**, betekent niet dat het veld aanwezig *moet* zijn in de lijst met zoekresultaten. U kunt de samenstelling van zoekresultaten nauwkeurig beheren met behulp van de queryparameter **$select** door op te geven welke velden u wilt opnemen. Voor velden met veel tekst zoals `content`, is de parameter **$select** de oplossing voor het aanbieden van beheersbare zoekresultaten aan de gebruikers van uw toepassing, terwijl clientcode via het kenmerk **Ophaalbaar** toegang heeft tot alle gegevens die nodig zijn.
  
Ga door naar de volgende pagina.

### <a name="step-4---configure-the-indexer"></a>Stap 4: de Indexeer functie configureren

De indexeerfunctie is een belangrijke resource die het indexeerproces aandrijft. Hiermee specificeert u de naam van de gegevensbron, een doelindex en de uitvoerfrequentie. De wizard **gegevens importeren** maakt verschillende objecten, en ze zijn altijd een Indexeer functie die u herhaaldelijk kunt uitvoeren.

1. Op de pagina **Indexeer functie** kunt u de standaard naam accepteren en op de optie voor het **eenmalige** schema klikken om deze direct uit te voeren. 

   ![Definitie van de indexeerfunctie](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Klik op **Verzenden** om de indexeerfunctie te maken en tegelijkertijd uit te voeren.

## <a name="monitor-status"></a>Monitor status

Het indexeren van cognitieve vaardig heden duurt langer dan gebruikelijk indexering op basis van tekst, met name voor het uitvoeren van een OCR-en afbeeldings analyse. Als u de voortgang wilt bewaken, gaat u naar de pagina overzicht en klikt u op **Indexeer functies** in het midden van de pagina.

  ![Azure Cognitive Search-melding](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Waarschuwingen zijn normaal gezien het grote aantal inhouds typen. Sommige inhouds typen zijn niet geldig voor bepaalde vaardig heden en op lagere lagen om de [indexerings limieten](search-limits-quotas-capacity.md#indexer-limits)te ondervinden. Afgekapte meldingen van 32.000 tekens zijn bijvoorbeeld een indexerings limiet voor de gratis laag. Als u deze demo op een hogere laag hebt uitgevoerd, gaan veel afgekapte waarschuwingen weg.

Als u de waarschuwingen of fouten wilt controleren, klikt u op de waarschuwings status in de lijst Indexeer functies om de pagina uitvoerings geschiedenis te openen.

Klik op die pagina opnieuw op waarschuwings status om de lijst met waarschuwingen weer te geven, zoals hieronder wordt weer gegeven. 

  ![Lijst met indexerings waarschuwingen](./media/cognitive-search-quickstart-blob/indexer-warnings.png)

Details worden weer gegeven wanneer u op een specifieke status regel klikt. Deze waarschuwing geeft aan dat het samen voegen is gestopt na het bereiken van een maximum drempel (deze specifieke PDF is groot).

  ![Waarschuwings Details](./media/cognitive-search-quickstart-blob/warning-detail.png)

## <a name="query-in-search-explorer"></a>Query uitvoeren in Search Explorer

Nadat een index is gemaakt, kunt u query's uitvoeren om resultaten te retour neren. Gebruik in de portal **Search Explorer** voor deze taak. 

1. Klik op de dashboardpagina van de zoekservice op **Search Explorer** in de opdrachtbalk.

1. Selecteer bovenaan **Index wijzigen** om de index die u hebt gemaakt te selecteren.

1. Voer een zoekopdracht in om een query op de index toe te passen, zoals `search=Microsoft&$select=people,organizations,locations,imageTags`.

Resultaten worden geretourneerd als JSON, wat kan worden uitgebreid en moeilijk te lezen zijn, met name in grote documenten die afkomstig zijn van Azure-blobs. Enkele tips voor het zoeken in dit hulp programma zijn de volgende technieken:

+ Toevoegen `$select` om op te geven welke velden moeten worden meegenomen in de resultaten. 
+ Gebruik CTRL-F om binnen de JSON te zoeken naar specifieke eigenschappen of termen.

Query reeksen zijn hoofdletter gevoelig, dus als u het bericht ' onbekend veld ' ontvangt, controleert u de **velden** of de **index definitie (JSON)** om de naam en case te controleren. 

  ![Voorbeeld Search Explorer](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Opgedane kennis

U hebt nu uw eerste vaardig heden gemaakt en belang rijke concepten geleerd die nuttig zijn voor het prototypen van een verrijkte Zoek oplossing met behulp van uw eigen gegevens.

Een van de belangrijke concepten die we wilden overbrengen, is de afhankelijkheid van Azure-gegevensbronnen. Een vaardig heden is gebonden aan een Indexeer functie en Indexeer functies zijn Azure en zijn specifiek voor de bron. Hoewel deze snelstart Azure Blob-opslag gebruikt, zijn andere Azure-gegevensbronnen ook mogelijk. Zie [Indexeer functies in Azure Cognitive Search](search-indexer-overview.md)voor meer informatie. 

Een ander belang rijk concept is dat vaardig heden over inhouds typen werken en wanneer ze met heterogene inhoud werken, worden sommige invoer overgeslagen. Het is ook mogelijk dat grote bestanden of velden de indexerings limieten van uw servicelaag overschrijden. Het is normaal dat er waarschuwingen worden weer gegeven wanneer deze gebeurtenissen optreden. 

Uitvoer wordt omgeleid naar een zoek index en er is een toewijzing tussen naam/waarde-paren die tijdens het indexeren en afzonderlijke velden in uw index worden gemaakt. Intern stelt de portal [aantekeningen](cognitive-search-concept-annotations-syntax.md) in en definieert een [set vaardigheden](cognitive-search-defining-skillset.md), waarmee de volgorde van bewerkingen en de algemene stroom wordt bepaald. Deze stappen zijn verborgen in de portal, maar wanneer u begint met het schrijven van code worden deze concepten belangrijk.

Ten slotte hebt u geleerd dat inhoud kan worden gecontroleerd door de index te doorzoeken. In het eind, wat Azure Cognitive Search biedt, is een Doorzoek bare index, die u kunt opvragen met de [eenvoudige](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) of een [volledig uitgebreide query syntaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Een index met verrijkte velden werkt zoals elke andere index. Als u standaard of [aangepaste analyse](search-analyzers.md)functies, [Score profielen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synoniemen](search-synonyms.md), [facet filters](search-filters-facets.md), Geo-search of andere Azure Cognitive Search-functie wilt opnemen, kunt u dit zeker doen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

U kunt vaardig heden maken met behulp van de portal, de .NET-SDK of de REST API. Probeer het REST API met behulp van Postman en meer voorbeeld gegevens om uw kennis te verfijnen.

> [!div class="nextstepaction"]
> [Zelf studie: tekst en structuur van JSON-blobs extra heren met behulp van REST-Api's](cognitive-search-tutorial-blob.md)

> [!Tip]
> Als u deze oefening wilt herhalen of als u een andere AI-verrijkings scenario wilt proberen, verwijdert u de Indexeer functie in de portal. Als u de Indexeer functie verwijdert, wordt de gratis dagelijkse transactie teller weer ingesteld op nul voor Cognitive Services verwerking.