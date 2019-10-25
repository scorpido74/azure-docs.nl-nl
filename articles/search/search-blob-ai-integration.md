---
title: AI gebruiken voor het begrijpen van Blob Storage-gegevens
titleSuffix: Azure Search
description: Voeg semantische, verwerking van natuurlijke taal en afbeeldings analyse toe aan Azure-blobs met behulp van een AI-pijp lijn in Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 5f65667ac8ffacdd12e57ae0d46e25c586624a31
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792477"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>AI gebruiken voor het begrijpen van Blob Storage-gegevens

Gegevens in Azure Blob Storage zijn vaak een verscheidenheid aan ongestructureerde inhoud zoals afbeeldingen, lange tekst, Pdf's en Office-documenten. Door gebruik te maken van de AI-mogelijkheden in Azure Search, kunt u op verschillende manieren waardevolle informatie uit blobs begrijpen en extra heren. Voor beelden van het Toep assen van AI op blob-inhoud zijn:

+ Tekst uit afbeeldingen extra heren met behulp van optische teken herkenning (OCR)
+ Een beschrijving of tags van een scène maken op basis van een foto
+ Taal detecteren en tekst vertalen in verschillende talen
+ Tekst verwerken met name entity Recognition (NER) om verwijzingen naar personen, datums, plaatsen of organisaties te vinden 

Hoewel u mogelijk slechts een van deze AI-mogelijkheden nodig hebt, is het gebruikelijk om meerdere van deze te combi neren in dezelfde pijp lijn (bijvoorbeeld door tekst op te halen uit een gescande afbeelding en vervolgens alle datums en plaatsen te vinden waarnaar wordt verwezen). 

Met AI-verrijking maakt u nieuwe informatie, vastgelegd als tekst, opgeslagen in velden. Post-verrijking, u kunt deze gegevens openen vanuit een zoek index door te zoeken in volledige tekst of verrijkte documenten terug te sturen naar Azure Storage om nieuwe ervaringen van toepassingen te bieden die gegevens verkennen voor detectie-en analyse scenario's. 

In dit artikel bekijken we AI-verrijking via een brede lens, zodat u snel het hele proces kunt begrijpt, van het transformeren van onbewerkte gegevens in blobs, naar Doorzoek bare gegevens in een zoek index of een kennis archief.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Wat betekent ' verrijkt ' BLOB-gegevens met AI

*AI-verrijking* maakt deel uit van de indexerings architectuur van Azure Search die ingebouwde AI integreert van micro soft of aangepaste AI die u opgeeft. Het helpt u bij het implementeren van end-to-end scenario's waarin u blobs moet verwerken (zowel bestaande als nieuwe, zoals deze worden geleverd of bijgewerkt), kraken alle bestands indelingen openen om afbeeldingen en tekst te extra heren, de gewenste informatie extra heren met behulp van verschillende AI-mogelijkheden en Indexeer ze in een Azure Search index voor snel zoeken, ophalen en verkennen. 

Invoer zijn uw blobs in een enkele container in Azure Blob Storage. Blobs kunnen bijna elk soort tekst-of afbeeldings gegevens zijn. 

Output is altijd een Azure Search index, die wordt gebruikt voor het snel zoeken van tekst, ophalen en verkennen in client toepassingen. Daarnaast kan de uitvoer ook een *kennis archief* zijn dat projecten verrijkt in azure blobs of Azure-tabellen voor downstream-analyses in hulpprogram ma's zoals Power bi of in data Science-workloads.

Tussen is de pijplijn architectuur zelf. De pijp lijn is gebaseerd op de *Indexeer* functie, waaraan u een *vaardig heden*kunt toewijzen. deze bestaat uit een of meer *vaardig heden* die de AI bieden. Het doel van de pijp lijn is om *verrijkte documenten* te produceren die als onbewerkte inhoud worden ingevoerd, maar extra structuur, context en informatie ophalen terwijl u door de pijp lijn beweegt. Verrijkte documenten worden tijdens de indexering gebruikt om omgekeerde indexen en andere structuren te maken die worden gebruikt voor Zoek opdrachten in volledige tekst of verkennen en analyses.

## <a name="start-with-services"></a>Beginnen met Services

U hebt Azure Search en Azure Blob-opslag nodig. In Blob Storage hebt u een container nodig die bron inhoud levert.

U kunt rechtstreeks beginnen op de portal-pagina van uw opslag account. Klik in de linker navigatie pagina onder **BLOB service** op **Azure Search toevoegen** om een nieuwe service te maken of een bestaande te selecteren. 

Nadat u Azure Search aan uw opslag account hebt toegevoegd, kunt u het standaard proces volgen om gegevens in een Azure-gegevens bron te verrijken. U wordt aangeraden de wizard **gegevens importeren** in azure Search te openen voor een eenvoudige inleiding tot AI-verrijking. Deze Snelstartgids begeleidt u stapsgewijs door de stappen: [een AI-pijp lijn maken in de portal](cognitive-search-quickstart-blob.md). 

In de volgende secties gaan we meer onderdelen en concepten verkennen.

## <a name="use-a-blob-indexer"></a>Een BLOB-indexer gebruiken

AI-verrijking is een invoeg toepassing voor een indexerings pijplijn en in Azure Search zijn deze pijp lijnen gebaseerd op een *Indexeer functie*. Een Indexeer functie is een subservice met gegevens bronnen die is voorzien van interne logica voor het bemonsteren van gegevens, het lezen van meta gegevens, het ophalen van gegevens en het serialiseren van gegevens van systeem eigen indelingen in JSON-documenten voor de volgende import. Indexeer functies worden vaak gebruikt voor importeren, gescheiden van AI, maar als u een AI-verrijkings pijplijn wilt maken, hebt u een Indexeer functie en een vakkennisset nodig om ermee te kunnen werken. In deze sectie wordt de Indexeer functie gemarkeerd. de volgende sectie richt zich op vaardig heden.

Blobs in Azure Storage worden geïndexeerd met behulp van de [Azure Search Blob Storage-indexer](search-howto-indexing-azure-blob-storage.md). U kunt deze Indexeer functie aanroepen met behulp van de wizard **gegevens importeren** , een rest API of de .NET SDK. In code gebruikt u deze Indexeer functie door het type in te stellen en door verbindings informatie op te geven die een Azure Storage account bevat samen met een BLOB-container. U kunt uw blobs samendeelnen door een virtuele map te maken, die u vervolgens kunt door geven als een para meter of door te filteren op een bestands extensie.

Een Indexeer functie voert het ' document kraken ' uit en opent een blob om inhoud te controleren. Nadat u verbinding hebt gemaakt met de gegevens bron, is de eerste stap in de pijp lijn. In het geval van BLOB-gegevens worden er PDF-, Office-docs-, afbeeldings-en andere inhouds typen gedetecteerd. Het kraken van documenten met tekst extractie wordt niet in rekening gebracht. Het kraken van documenten met afbeeldings extractie wordt in rekening gebracht tegen tarieven die u kunt vinden op de pagina met Azure Search [prijzen](https://azure.microsoft.com/pricing/details/search/).

Hoewel alle documenten worden gekraakt, treedt er alleen uitgebreide verrijking op als u de vaardig heden expliciet opgeeft. Als uw pijp lijn bijvoorbeeld uitsluitend bestaat uit afbeeldings analyse, wordt tekst in uw container of documenten genegeerd.

De BLOB-indexer wordt geleverd met configuratie parameters en biedt ondersteuning voor het bijhouden van wijzigingen als de onderliggende gegevens voldoende informatie bevatten. Meer informatie over de kern functionaliteit vindt u in [Azure Search Blob Storage indexer](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>AI-onderdelen toevoegen

AI-verrijking verwijst naar modules die naar patronen of kenmerken zoeken en voert vervolgens een bewerking uit. Gezichts herkenning in Foto's, tekst beschrijvingen van Foto's, het detecteren van sleutel zinnen in een document en OCR (of het herkennen van gedrukte of handgeschreven tekst in binaire bestanden) zijn voor beelden van illustreren.

In Azure Search zijn *vaardig heden* de afzonderlijke onderdelen van AI-verwerking die u zelfstandig kunt gebruiken of in combi natie met andere vaardig heden. 

+ Ingebouwde vaardig heden worden ondersteund door Cognitive Services, met afbeeldings analyse op basis van Computer Vision en de verwerking van natuurlijke taal op basis van Text Analytics. Zie [ingebouwde vaardig heden voor inhouds verrijking](cognitive-search-predefined-skills.md)voor de volledige lijst.

+ Aangepaste vaardig heden zijn aangepaste code, verpakt in een [interface definitie](cognitive-search-custom-skill-interface.md) waarmee integratie kan worden geïntegreerd in de pijp lijn. In klant oplossingen is het gebruikelijk om beide te gebruiken met aangepaste vaardig heden, AI-modules van derden of van derden.

Een *vakkennisset* is de verzameling van de vaardig heden die worden gebruikt in een pijp lijn en deze wordt aangeroepen nadat de fase voor het kraken van het document de inhoud beschikbaar maakt. Een Indexeer functie kan precies één vaardig heden gebruiken, maar deze vaardig heden bestaan onafhankelijk van een indexer, zodat u deze in andere scenario's opnieuw kunt gebruiken.

Aangepaste vaardig heden kunnen worden gefragmenteerd, maar zijn gemakkelijk en eenvoudig in termen van implementatie. Als u bestaande pakketten hebt die patroon overeenkomsten of classificatie modellen bieden, kan de inhoud die u ophaalt uit blobs worden door gegeven aan deze modellen voor verwerking. Omdat AI-verrijking op basis van Azure is, moet uw model ook in azure zijn. Enkele veelgebruikte hosting methoden zijn het gebruik van [Azure functions](cognitive-search-create-custom-skill-example.md) of [containers](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Voor ingebouwde vaardig heden die door Cognitive Services worden ondersteund, is een [bijgevoegde Cognitive Services](cognitive-search-attach-cognitive-services.md) alles-in-één-abonnements sleutel nodig waarmee u toegang krijgt tot de resource. Een alles-in-één-sleutel geeft u de analyse van afbeeldingen, taal detectie, tekst omzetting en tekst analyse. Andere ingebouwde vaardig heden zijn functies van Azure Search en vereisen geen extra service of sleutel. Text shaper, splitter en fusie zijn voor beelden van helper-vaardig heden die soms nodig zijn bij het ontwerpen van de pijp lijn.

Als u alleen aangepaste vaardig heden en ingebouwde hulp programma-vaardig heden gebruikt, zijn er geen afhankelijkheden of kosten met betrekking tot Cognitive Services.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>AI-verrijkte uitvoer in downstream-oplossingen gebruiken

De uitvoer van AI-verrijking is een zoek index op Azure Search of een [Knowledge Store](knowledge-store-concept-intro.md) in azure Storage.

In Azure Search wordt een zoek index gebruikt voor interactief verkennen met behulp van vrije tekst en gefilterde query's in een client-app. Verrijkte documenten die zijn gemaakt met AI, worden ingedeeld in JSON en geïndexeerd op dezelfde manier als alle documenten worden geïndexeerd in Azure Search, met alle voor delen van een Indexeer functie. Tijdens het indexeren verwijst de BLOB-Indexeer functie bijvoorbeeld naar configuratie parameters en-instellingen om eventuele veld toewijzingen of wijzigingen detectie logica te gebruiken. Deze instellingen zijn volledig beschikbaar voor reguliere indexering en AI-verrijkte workloads. Na het indexeren kunt u, wanneer inhoud wordt opgeslagen op Azure Search, uitgebreide query's bouwen en expressies filteren om inzicht te krijgen in de inhoud.

In Azure Storage heeft een kennis archief twee manifesten: een BLOB-container of tabellen in tabel opslag. 

+ Een BLOB-container legt verrijkte documenten in hun geheel vast. Dit is handig als u een feed wilt maken voor andere processen. 

+ Tabel opslag kan daarentegen de fysieke projecties van verrijkte documenten bevatten. U kunt segmenten of lagen maken met verrijkte documenten die specifieke onderdelen bevatten of uitsluiten. Voor analyse in Power BI worden de tabellen in azure Table Storage de gegevens bron geworden voor verdere visualisatie en onderzoek.

Een verrijkt document aan het einde van de pijp lijn wijkt af van de oorspronkelijke invoer versie door de aanwezigheid van aanvullende velden die nieuwe informatie bevatten die tijdens de verrijking is geëxtraheerd of gegenereerd. Zo kunt u werken met een combi natie van originele en gemaakte inhoud, ongeacht de uitvoer structuur die u gebruikt.

## <a name="next-steps"></a>Volgende stappen

U kunt veel meer doen met AI-verrijking om optimaal gebruik te maken van uw gegevens in Azure Storage, waaronder het combi neren van Cognitive Services op verschillende manieren, en het ontwerpen van aangepaste vaardig heden voor gevallen waarin u geen ervaring hebt met de cognitieve service voor het scenario. Meer informatie vindt u op de onderstaande koppelingen.

+ [Het uploaden, downloaden en vermelden van blobs met de Azure Portal (Azure Blob-opslag)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Een BLOB-indexer instellen (Azure Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Overzicht van AI-verrijking (Azure Search)](cognitive-search-concept-intro.md) 
+ [Een vaardig heden maken (Azure Search)](cognitive-search-defining-skillset.md)
+ [Knoop punten toewijzen in een aantekening structuur (Azure Search)](cognitive-search-output-field-mapping.md)
