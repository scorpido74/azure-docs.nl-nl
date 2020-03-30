---
title: 'Snelstart: een skillset maken in de Azure-portal'
titleSuffix: Azure Cognitive Search
description: Leer in deze snelstart van deze portal hoe u de wizard Gegevens importeren gebruiken om cognitieve vaardigheden toe te voegen aan een indexeringspijplijn in Azure Cognitive Search. Vaardigheden zijn onder andere Optical Character Recognition (OCR) en natural language processing.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/20/2019
ms.openlocfilehash: e2e17ba6af60fa495a03e7d46a07cfe6b66f4e68
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472414"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Snelstart: een cognitieve vaardigheden voor Azure Cognitive Search maken in de Azure-portal

Een skillset is een AI-functie die informatie en structuur uit grote ongedifferentieerde tekst- of afbeeldingsbestanden haalt en deze indexeerbaar en doorzoekbaar maakt voor zoekopdrachten in volledige tekst in Azure Cognitive Search. 

In deze quickstart combineert u services en gegevens in de Azure-cloud om de vaardigheden te maken. Zodra alles op zijn plaats is, voert u de wizard **Gegevens importeren** in de portal uit om alles bij elkaar te brengen. Het eindresultaat is een doorzoekbare index gevuld met gegevens die zijn gemaakt door AI-verwerking die u opvragen in de portal[(Zoekverkenner).](search-explorer.md)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-services-and-load-data"></a>Services maken en gegevens laden

Deze quickstart maakt gebruik van Azure Cognitive Search, [Azure Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/)en [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) voor de AI. 

Omdat de werklast zo klein is, wordt Cognitive Services achter de schermen afgetapt om gratis verwerking te bieden voor maximaal 20 transacties. Voor zo'n kleine gegevensset u het maken of koppelen van een resource voor Cognitive Services overslaan.

1. [Download de voorbeeldgegevens](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) die bestaan uit een kleine set van verschillende typen bestanden. Rits de bestanden open.

1. [Maak een Azure-opslagaccount](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) aan of [zoek een bestaand account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Kies dezelfde regio als Azure Cognitive Search om bandbreedtekosten te voorkomen. 
   
   Kies het accounttype StorageV2 (v2 voor algemeen gebruik) als u de functie voor de kenniswinkel later wilt uitproberen, in een andere walkthrough. Anders kiest u elk type.

1. Open de pagina's blobservices en maak een container. U het standaardtoegangsniveau voor het publiek gebruiken. 

1. Klik in de container op **Uploaden** om de voorbeeldbestanden te uploaden die u in de eerste stap hebt gedownload. Merk op dat u een breed scala aan inhoudstypen hebt, waaronder afbeeldingen en toepassingsbestanden die niet volledig doorzoekbaar zijn in hun oorspronkelijke indelingen.

   ![Bronbestanden in Azure-blobopslag](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). U gebruik maken van een gratis service voor deze quickstart.

U bent nu klaar om verder te gaan met de wizard Gegevens importeren.

## <a name="run-the-import-data-wizard"></a>De wizard Gegevens importeren uitvoeren

Klik op de pagina Overzicht van de zoekservice op **Gegevens importeren** op de opdrachtbalk om cognitieve verrijking in vier stappen in te stellen.

  ![Opdracht Gegevens importeren](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1---create-a-data-source"></a>Stap 1 - Een gegevensbron maken

1. Kies **in Verbinding maken met uw gegevens**Azure **Blob-opslag**, selecteer het opslagaccount en de container die u hebt gemaakt. Geef een naam op voor de gegevensbron en gebruik standaardwaarden voor de rest. 

   ![Azure-blobconfiguratie](./media/cognitive-search-quickstart-blob/blob-datasource.png)

    Ga door naar de volgende pagina.

### <a name="step-2---add-cognitive-skills"></a>Stap 2 - Cognitieve vaardigheden toevoegen

Configureer vervolgens AI-verrijking om OCR, beeldanalyse en verwerking van natuurlijke talen aan te roepen. 

1. Voor deze quickstart maken we gebruik van de **Free** Cognitive Services-bron. De voorbeeldgegevens bestaan uit 14 bestanden, dus de gratis toewijzing van 20 transacties op Cognitive Services is voldoende voor deze quickstart. 

   ![Cognitive Services toevoegen](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Uitbreiding **Verrijkingen toevoegen** en vier selecties maken. 

   Ocr inschakelen om vaardigheden voor afbeeldingsanalyse toe te voegen aan de wizardpagina.

   Stel granulariteit in op pagina's om tekst in kleinere segmenten op te splitsen. Verschillende tekstvaardigheden zijn beperkt tot 5-KB-ingangen.

   Kies entiteitsherkenning (personen, organisaties, locaties) en vaardigheden op het het in beeldanalyse.

   ![Cognitive Services toevoegen](media/cognitive-search-quickstart-blob/skillset.png)

   Ga door naar de volgende pagina.

### <a name="step-3---configure-the-index"></a>Stap 3 - De index configureren

Een index bevat uw doorzoekbare inhoud en de wizard **Gegevens importeren** kan meestal het schema voor u maken door de gegevensbron te samplen. Bekijk in deze stap het gegenereerde schema en wijzig mogelijk eventuele instellingen. Hieronder vindt u het standaardschema dat is gemaakt voor de demo-blob-gegevensset.

De wizard biedt goede standaardinstellingen voor deze snelstart:  

+ Standaardvelden zijn gebaseerd op eigenschappen voor bestaande blobs plus nieuwe `people` `organizations`velden `locations`die verrijkingsuitvoer bevatten (bijvoorbeeld , , ). Gegevenstypen worden afgeleid uit metagegevens en gegevensbemonstering.

+ Standaarddocumentsleutel is *metadata_storage_path* (geselecteerd omdat het veld unieke waarden bevat).

+ Standaardkenmerken zijn **opvraagbaar** en **doorzoekbaar**. **Doorzoekbaar** maakt full text zoeken in een veld. **Opvraagbaar** betekent dat veldwaarden kunnen worden geretourneerd in resultaten. De wizard gaat ervan uit dat deze velden ophaalbaar en doorzoekbaar moeten zijn omdat u ze hebt gemaakt via een set vaardigheden.

  ![Indexvelden](media/cognitive-search-quickstart-blob/index-fields.png)

Let op het doorgehaalde vinkje en het vraagteken in de kolom **Ophaalbaar** voor het veld `content`. Voor blob-documenten met veel tekst bevat het veld `content` het grootste deel van het bestand, mogelijk wel duizenden regels. Een veld als dit is onhandig in de zoekresultaten en je moet het uitsluiten voor deze demo. 

Als u echter de inhoud van het bestand moet doorgeven aan de clientcode, moet u ervoor zorgen dat **Opmaakbaar** blijft geselecteerd. Anders u overwegen `content` dit kenmerk te wissen `people` `organizations`op `locations`de vraag of de geëxtraheerde elementen (zoals,, enzovoort) voldoende zijn.

Als u een veld markeert als **Ophaalbaar**, betekent niet dat het veld aanwezig *moet* zijn in de lijst met zoekresultaten. U kunt de samenstelling van zoekresultaten nauwkeurig beheren met behulp van de queryparameter **$select** door op te geven welke velden u wilt opnemen. Voor velden met veel tekst zoals `content`, is de parameter **$select** de oplossing voor het aanbieden van beheersbare zoekresultaten aan de gebruikers van uw toepassing, terwijl clientcode via het kenmerk **Ophaalbaar** toegang heeft tot alle gegevens die nodig zijn.
  
Ga door naar de volgende pagina.

### <a name="step-4---configure-the-indexer"></a>Stap 4 - De indexer configureren

De indexeerfunctie is een belangrijke resource die het indexeerproces aandrijft. Hiermee specificeert u de naam van de gegevensbron, een doelindex en de uitvoerfrequentie. Met de wizard **Gegevens importeren** worden verschillende objecten gemaakt en daarvan is het altijd een indexer die u herhaaldelijk uitvoeren.

1. Op de pagina **Indexer** u de standaardnaam accepteren en op de optie **Eenmaal** schema klikken om deze onmiddellijk uit te voeren. 

   ![Definitie van de indexeerfunctie](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Klik op **Verzenden** om de indexeerfunctie te maken en tegelijkertijd uit te voeren.

## <a name="monitor-status"></a>Monitorstatus

Het indexeren van cognitieve vaardigheden duurt langer dan typische tekstindexering, met name OCR- en beeldanalyse. Als u de voortgang wilt bijhouden, gaat u naar de pagina Overzicht en klikt u op **Indexers** in het midden van de pagina.

  ![Azure Cognitive Search-melding](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Waarschuwingen zijn normaal gezien het brede scala aan inhoudstypen. Sommige inhoudstypen zijn niet geldig voor bepaalde vaardigheden en op lagere niveaus zijn de gemeenschappelijke [indexerlimieten](search-limits-quotas-capacity.md#indexer-limits)tegenkomen . Afgekapte meldingen van 32.000 tekens zijn bijvoorbeeld een indexerlimiet voor de laag Gratis. Als u deze demo op een hoger niveau zou uitvoeren, zouden veel afgekapte waarschuwingen verdwijnen.

Als u waarschuwingen of fouten wilt controleren, klikt u op de waarschuwingsstatus in de lijst Indexers om de pagina Uitvoeringsgeschiedenis te openen.

Klik op die pagina nogmaals op De status Van waarschuwingen waarschuwen om de lijst met waarschuwingen weer te geven die vergelijkbaar zijn met de onderstaande. 

  ![Waarschuwingslijst indexeren](./media/cognitive-search-quickstart-blob/indexer-warnings.png)

Details worden weergegeven wanneer u op een specifieke statusregel klikt. Deze waarschuwing zegt dat het samenvoegen gestopt na het bereiken van een maximale drempel (deze specifieke PDF is groot).

  ![Waarschuwingsgegevens](./media/cognitive-search-quickstart-blob/warning-detail.png)

## <a name="query-in-search-explorer"></a>Query uitvoeren in Search Explorer

Nadat een index is gemaakt, u query's uitvoeren om resultaten te retourneren. Gebruik **zoeken** explorer voor deze taak in de portal. 

1. Klik op de dashboardpagina van de zoekservice op **Search Explorer** in de opdrachtbalk.

1. Selecteer bovenaan **Index wijzigen** om de index die u hebt gemaakt te selecteren.

1. Voer een zoekopdracht in om een query op de index toe te passen, zoals `search=Microsoft&$select=people,organizations,locations,imageTags`.

Resultaten worden geretourneerd als JSON, dat uitgebreid en moeilijk te lezen is, vooral in grote documenten afkomstig van Azure-blobs. Enkele tips voor het zoeken in deze tool zijn de volgende technieken:

+ Toevoegen `$select` om op te geven welke velden in de resultaten moeten worden opgenomen. 
+ Gebruik CTRL-F om in de JSON te zoeken naar specifieke eigenschappen of termen.

Querytekenreeksen zijn hoofdlettergevoelig, dus als u een bericht 'onbekend veld' ontvangt, schakelt u **Velden** of **Indexdefinitie (JSON)** in om naam en aanvraag te verifiëren. 

  ![Voorbeeld Search Explorer](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Opgedane kennis

Je hebt nu je eerste skillset gemaakt en belangrijke concepten geleerd die nuttig zijn voor het prototypen van een verrijkte zoekoplossing met behulp van je eigen gegevens.

Een van de belangrijke concepten die we wilden overbrengen, is de afhankelijkheid van Azure-gegevensbronnen. Een skillset is gebonden aan een indexeerder en indexeerders zijn Azure en bronspecifiek. Hoewel deze snelstart Azure Blob-opslag gebruikt, zijn andere Azure-gegevensbronnen ook mogelijk. Zie [Indexers in Azure Cognitive Search](search-indexer-overview.md)voor meer informatie. 

Een ander belangrijk concept is dat vaardigheden werken via inhoudstypen, en bij het werken met heterogene inhoud worden sommige ingangen overgeslagen. Grote bestanden of velden kunnen ook de indexerlimieten van uw servicelaag overschrijden. Het is normaal om waarschuwingen te zien wanneer deze gebeurtenissen optreden. 

Uitvoer wordt gericht op een zoekindex en er is een toewijzing tussen naamwaardeparen die zijn gemaakt tijdens indexering en afzonderlijke velden in uw index. Intern stelt de portal [aantekeningen](cognitive-search-concept-annotations-syntax.md) in en definieert een [set vaardigheden](cognitive-search-defining-skillset.md), waarmee de volgorde van bewerkingen en de algemene stroom wordt bepaald. Deze stappen zijn verborgen in de portal, maar wanneer u begint met het schrijven van code worden deze concepten belangrijk.

Ten slotte hebt u geleerd dat inhoud kan worden geverifieerd door de index op te vragen. Uiteindelijk biedt Azure Cognitive Search een doorzoekbare index, die u opvragen met behulp van de [eenvoudige](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) of [volledig uitgebreide querysyntaxis.](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) Een index met verrijkte velden werkt zoals elke andere index. Als u standaard- of [aangepaste analysers,](search-analyzers.md) [scoreprofielen,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) [synoniemen,](search-synonyms.md) [gefacetteerde filters,](search-filters-facets.md)geo-search of een andere Azure Cognitive Search-functie wilt opnemen, u dit zeker doen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling **Alle resources** of **Resourcegroepen** in het linkernavigatiedeelvenster.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u beperkt bent tot drie indexen, indexeerders en gegevensbronnen. U afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

U skillsets maken met behulp van de portal, .NET SDK of REST API. Om uw kennis te bevorderen, probeer de REST API met behulp van Postman en meer voorbeeldgegevens.

> [!div class="nextstepaction"]
> [Zelfstudie: Tekst en structuur uit JSON-blobs extraheren met REST-API's](cognitive-search-tutorial-blob.md)

> [!Tip]
> Als u deze oefening wilt herhalen of een andere AI-verrijkingswalkthrough wilt proberen, verwijdert u de indexer in de portal. Als u de indexer verwijderen, wordt de gratis dagelijkse transactieteller teruggezet naar nul voor verwerking van Cognitive Services.