---
title: AI gebruiken om blob-opslaggegevens te begrijpen
titleSuffix: Azure Cognitive Search
description: Voeg semantische, natuurlijke taalverwerking en beeldanalyse toe aan Azure blobs met behulp van een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496443"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>AI gebruiken om blob-opslaggegevens te begrijpen

Gegevens in Azure Blob-opslag zijn vaak een verscheidenheid aan ongestructureerde inhoud, zoals afbeeldingen, lange tekst, PDF's en Office-documenten. Door gebruik te maken van de AI-mogelijkheden in Azure Cognitive Search, u op verschillende manieren waardevolle informatie uit blobs begrijpen en extraheren. Voorbeelden van het toepassen van AI op blob-inhoud zijn:

+ Tekst uit afbeeldingen extraheren met behulp van optische tekenherkenning (OCR)
+ Een scènebeschrijving of -tags van een foto maken
+ Taal detecteren en tekst vertalen in verschillende talen
+ Tekst verwerken met benoemde entiteitsherkenning (NER) om verwijzingen naar personen, datums, plaatsen of organisaties te zoeken 

Hoewel u misschien slechts één van deze AI-mogelijkheden nodig hebt, is het gebruikelijk om meerdere van deze ai's in dezelfde pijplijn te combineren (bijvoorbeeld tekst uit een gescande afbeelding extraheren en vervolgens alle datums en plaatsen vinden waarnaar daarin wordt verwezen). 

AI-verrijking creëert nieuwe informatie, vastgelegd als tekst, opgeslagen in velden. Na verrijking u deze informatie openen vanuit een zoekindex via zoeken in volledige tekst of verrijkte documenten terugsturen naar Azure-opslag om nieuwe toepassingservaringen van stroom te voorzien, waaronder het verkennen van gegevens voor detectie- of analysescenario's. 

In dit artikel bekijken we AI-verrijking via een brede lens, zodat u snel het hele proces begrijpen, van het transformeren van ruwe gegevens in blobs tot opvraagbare informatie in een zoekindex of een kenniswinkel.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Wat het betekent om blobgegevens te "verrijken" met AI

*AI-verrijking* maakt deel uit van de indexeringsarchitectuur van Azure Cognitive Search die ingebouwde AI van Microsoft of aangepaste AI die u aanbiedt, integreert. Het helpt u bij het implementeren van end-to-end scenario's waarbij u blobs moet verwerken (zowel bestaande als nieuwe wanneer ze binnenkomen of worden bijgewerkt), alle bestandsindelingen openen om afbeeldingen en tekst te extraheren, de gewenste informatie te extraheren met behulp van verschillende AI-mogelijkheden en indexeer ze in een zoekindex voor snel zoeken, ophalen en verkennen. 

Invoer zijn uw blobs, in één container, in Azure Blob-opslag. Blobs kunnen bijna elke vorm van tekst of afbeeldinggegevens zijn. 

Uitvoer is altijd een zoekindex, die wordt gebruikt voor snel zoeken naar tekst, ophalen en verkennen in clienttoepassingen. Bovendien kan uitvoer ook een *kennisarchief* zijn dat verrijkte documenten projecteert in Azure-blobs of Azure-tabellen voor downstream-analyse in hulpprogramma's zoals Power BI of in gegevenswetenschapsworkloads.

Daartussen zit de pijpleidingarchitectuur zelf. De pijplijn is gebaseerd op de *indexerfunctie,* waaraan u een *skillset*toewijzen, die bestaat uit een of meer *vaardigheden* die de AI bieden. Het doel van de pijplijn is om *verrijkte documenten* te produceren die als ruwe inhoud worden ingevoerd, maar extra structuur, context en informatie oppikken terwijl u door de pijplijn gaat. Verrijkte documenten worden verbruikt tijdens het indexeren om omgekeerde indexen en andere structuren te maken die worden gebruikt bij het zoeken naar of verkennen en analyseren van volledige tekst.

## <a name="start-with-services"></a>Begin met services

U hebt Azure Cognitive Search en Azure Blob-opslag nodig. Binnen Blob-opslag hebt u een container nodig die broninhoud biedt.

U direct beginnen op de pagina Met uw opslagaccountportal. Klik op de linkernavigatiepagina onder **Blob-service** op **Azure Cognitive Search toevoegen** om een nieuwe service te maken of een bestaande service te selecteren. 

Zodra u Azure Cognitive Search aan uw opslagaccount hebt toegevoegd, u het standaardproces volgen om gegevens in elke Azure-gegevensbron te verrijken. We raden de wizard **Gegevens importeren** in Azure Cognitive Search aan voor een eenvoudige eerste kennismaking met AI-verrijking. Deze snelle start leidt u door de stappen: [Maak een AI-verrijkingspijplijn in de portal.](cognitive-search-quickstart-blob.md) 

In de volgende secties verkennen we meer componenten en concepten.

## <a name="use-a-blob-indexer"></a>Een Blob-indexer gebruiken

AI-verrijking is een add-on voor een indexeringspijplijn en in Azure Cognitive Search worden deze pijplijnen bovenop een *indexer*gebouwd. Een indexer is een gegevensbronbewuste subservice die is uitgerust met interne logica voor het samplen van gegevens, het lezen van metagegevens, het ophalen van gegevens en het serialiseren van gegevens uit native formaten in JSON-documenten voor latere import. Indexers worden vaak zelf gebruikt voor import, los van AI, maar als u een AI-verrijkingspijplijn wilt bouwen, hebt u een indexer en een skillset nodig om mee te gaan. In deze sectie wordt de indexeerder uitgelicht; de volgende sectie richt zich op skillsets.

Blobs in Azure Storage worden geïndexeerd met de [Azure Cognitive Search Blob-opslagindexeerder](search-howto-indexing-azure-blob-storage.md). U deze indexer aanroepen met de wizard **Gegevens importeren,** een REST-API of de .NET SDK. In code gebruikt u deze indexer door het type in te stellen en door verbindingsgegevens te verstrekken die een Azure Storage-account bevatten, samen met een blobcontainer. U uw blobs subseten door een virtuele map te maken, die u vervolgens als parameter doorgeven, of door te filteren op een bestandstypeextensie.

Een indexeerder doet het "document kraken", het openen van een blob om inhoud te inspecteren. Na het verbinden met de gegevensbron is dit de eerste stap in de pijplijn. Voor blobgegevens wordt dit waar PDF,office-documenten, afbeeldingen en andere inhoudstypen worden gedetecteerd. Het kraken van documenten met tekstextractie is geen kosten in rekening. Het kraken van documenten met beeldextractie wordt in rekening gebracht tegen tarieven die u vinden op de [prijspagina.](https://azure.microsoft.com/pricing/details/search/)

Hoewel alle documenten zullen worden gekraakt, verrijking treedt alleen op als u expliciet de vaardigheden om dit te doen. Als uw pijplijn bijvoorbeeld uitsluitend uit beeldanalyse bestaat, wordt tekst in uw container of documenten genegeerd.

De Blob-indexer wordt geleverd met configuratieparameters en ondersteunt het bijhouden van wijzigingen als de onderliggende gegevens voldoende informatie bieden. Meer informatie over de kernfunctionaliteit vindt u in [Azure Cognitive Search Blob-opslagindexeerder](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>AI-componenten toevoegen

AI-verrijking verwijst naar modules die zoeken naar patronen of kenmerken en vervolgens een bewerking dienovereenkomstig uitvoeren. Gezichtsherkenning in foto's, tekstbeschrijvingen van foto's, het detecteren van sleutelzinnen in een document en OCR (of het herkennen van gedrukte of handgeschreven tekst in binaire bestanden) zijn illustratieve voorbeelden.

In Azure Cognitive Search zijn *vaardigheden* de afzonderlijke onderdelen van AI-verwerking die u zelfstandig gebruiken, of in combinatie met andere vaardigheden. 

+ Ingebouwde vaardigheden worden ondersteund door Cognitive Services, met beeldanalyse op basis van Computer Vision en natuurlijke taalverwerking op basis van Text Analytics. Zie [Ingebouwde vaardigheden voor contentverrijking voor](cognitive-search-predefined-skills.md)de volledige lijst.

+ Aangepaste vaardigheden zijn aangepaste code, verpakt in een [interfacedefinitie](cognitive-search-custom-skill-interface.md) die integratie in de pijplijn mogelijk maakt. In klantoplossingen is het gebruikelijk om beide te gebruiken, met aangepaste vaardigheden die open-source- of ai-modules van derden bieden.

Een *skillset* is de verzameling van vaardigheden die worden gebruikt in een pijplijn, en het wordt aangeroepen nadat de fase van het kraken van documenten inhoud beschikbaar maakt. Een indexer kan precies één skillset verbruiken, maar die skillset bestaat onafhankelijk van een indexer, zodat u deze in andere scenario's hergebruiken.

Aangepaste vaardigheden klinken misschien complex, maar kunnen eenvoudig en eenvoudig zijn in termen van implementatie. Als u bestaande pakketten hebt die patroonmatching of classificatiemodellen bieden, kan de inhoud die u uit blobs haalt, worden doorgegeven aan deze modellen voor verwerking. Aangezien AI-verrijking op Azure is gebaseerd, moet uw model ook op Azure staan. Enkele veelvoorkomende hostingmethodologieën zijn het gebruik van [Azure-functies](cognitive-search-create-custom-skill-example.md) of [containers.](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)

Ingebouwde vaardigheden die worden ondersteund door Cognitive Services vereisen een [gekoppelde Cognitive Services](cognitive-search-attach-cognitive-services.md) all-in-one-abonnementssleutel die u toegang geeft tot de bron. Een alles-in-één-toets geeft u beeldanalyse, taaldetectie, tekstvertaling en tekstanalyse. Andere ingebouwde vaardigheden zijn functies van Azure Cognitive Search en vereisen geen extra service of sleutel. Tekstvormer, splitter en fusie zijn voorbeelden van helpervaardigheden die soms nodig zijn bij het ontwerpen van de pijplijn.

Als u alleen aangepaste vaardigheden en ingebouwde hulpprogrammavaardigheden gebruikt, is er geen afhankelijkheid of kosten verbonden aan cognitieve services.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>AI-verrijkte output verbruiken in downstream-oplossingen

De uitvoer van AI-verrijking is een zoekindex in Azure Cognitive Search of een [kennisarchief](knowledge-store-concept-intro.md) in Azure Storage.

In Azure Cognitive Search wordt een zoekindex gebruikt voor interactieve verkenning met behulp van gratis tekst en gefilterde query's in een client-app. Verrijkte documenten die via AI zijn gemaakt, worden opgemaakt in JSON en geïndexeerd op dezelfde manier dat alle documenten worden geïndexeerd in Azure Cognitive Search, waarbij gebruik wordt gemaakt van alle voordelen die een indexeerder biedt. Tijdens het indexeren verwijst de blob-indexer bijvoorbeeld naar configuratieparameters en -instellingen om veldtoewijzingen te gebruiken of detectielogica te wijzigen. Dergelijke instellingen zijn volledig beschikbaar voor regelmatige indexering en AI-verrijkte workloads. Wanneer inhoud wordt opgeslagen in Azure Cognitive Search, u uitgebreide query's en filterexpressies maken om uw inhoud te begrijpen.

In Azure Storage heeft een kennisarchief twee manifestaties: een blobcontainer of tabellen in tabelopslag. 

+ Een blobcontainer legt verrijkte documenten in hun geheel vast, wat handig is als u wilt verwerken in andere processen. 

+ In de tabelopslag kunnen daarentegen fysieke projecties van verrijkte documenten worden ondergebracht. U segmenten of lagen verrijkte documenten maken die specifieke onderdelen bevatten of uitsluiten. Voor analyse in Power BI worden de tabellen in Azure Table-opslag de gegevensbron voor verdere visualisatie en verkenning.

Een verrijkt document aan het einde van de pijplijn verschilt van de oorspronkelijke invoerversie door de aanwezigheid van extra velden die nieuwe informatie bevatten die tijdens de verrijking is geëxtraheerd of gegenereerd. Als zodanig u werken met een combinatie van originele en gemaakte inhoud, ongeacht welke uitvoerstructuur u gebruikt.

## <a name="next-steps"></a>Volgende stappen

Er is nog veel meer dat u doen met AI-verrijking om het meeste uit uw gegevens in Azure Storage te halen, waaronder het combineren van Cognitieve Services op verschillende manieren en het ontwerpen van aangepaste vaardigheden voor gevallen waarin er geen bestaande cognitive service voor het scenario is. Meer informatie kan je volgen via onderstaande links.

+ [Blobs uploaden, downloaden en aanbieden met de Azure-portal (Azure Blob-opslag)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Een blob-indexer instellen (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Overzicht van AI-verrijking (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [Een skillset maken (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Knooppunten toewijzen in een annotatiestructuur (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)
