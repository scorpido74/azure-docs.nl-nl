---
title: Inleiding tot AI-verrijking
titleSuffix: Azure Cognitive Search
description: Extractie van inhoud, verwerking van natuurlijke taal (NLP) en afbeeldings verwerking om Doorzoek bare inhoud in azure Cognitive Search indexeren te maken met behulp van cognitieve vaardig heden en AI-algoritmen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/04/2019
ms.openlocfilehash: 2ef7f273d6838b1bc051c70539ef7d9da59d7148
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754578"
---
# <a name="introduction-to-ai-in-azure-cognitive-search"></a>Inleiding tot AI in azure Cognitive Search

AI-verrijking is een mogelijkheid van Azure Cognitive Search indexering die wordt gebruikt om tekst uit afbeeldingen, blobs en andere ongestructureerde gegevens bronnen te extra heren, waardoor de inhoud in een index of kennis archief beter kan worden doorzocht. Extractie en verrijking worden geïmplementeerd via *cognitieve vaardig heden* die zijn gekoppeld aan een indexerings pijplijn. Cognitieve vaardig heden die in de service zijn ingebouwd, vallen in deze categorieën: 

+ Vaardig heden voor de **verwerking van natuurlijke taal** zijn onder andere [entiteits herkenning](cognitive-search-skill-entity-recognition.md), [taal detectie](cognitive-search-skill-language-detection.md), [extractie van sleutel zinnen](cognitive-search-skill-keyphrases.md), tekst manipulatie en [sentiment detectie](cognitive-search-skill-sentiment.md). Met deze vaardig heden kan ongestructureerde tekst uitgaan van nieuwe formulieren, toegewezen als Doorzoek bare en filter bare velden in een index.

+ Vaardig heden voor de **verwerking van afbeeldingen** zijn onder andere [optische teken herkenning (OCR)](cognitive-search-skill-ocr.md) en het identificeren van [visuele functies](cognitive-search-skill-image-analysis.md), zoals gezichts detectie, afbeeldings interpretatie, afbeeldings herkenning (beroemde personen en bezienswaardigheden) of kenmerken zoals kleuren of afbeeldings stand. U kunt tekst representaties van afbeeldings inhoud maken en doorzoekbaar met behulp van alle query mogelijkheden van Azure Cognitive Search.

![Diagram van verrijkings pijplijn](./media/cognitive-search-intro/cogsearch-architecture.png "overzicht van verrijkings pijplijn")

Cognitieve vaardig heden in azure Cognitive Search zijn gebaseerd op vooraf getrainde machine learning modellen in Cognitive Services-API's: [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) en [tekst analyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

De verwerking van natuurlijke taal en afbeelding wordt toegepast tijdens de fase van gegevens opname, met resultaten die deel uitmaken van de samen stelling van een document in een Doorzoek bare index in azure Cognitive Search. Gegevens worden als een Azure-gegevensset gedistribueerd en vervolgens gepusht via een indexerings pijplijn met behulp van de [ingebouwde vaardig heden](cognitive-search-predefined-skills.md) die u nodig hebt. De architectuur kan worden uitgebreid als de ingebouwde vaardig heden niet voldoende zijn, kunt u [aangepaste vaardig heden](cognitive-search-create-custom-skill-example.md) maken en koppelen om aangepaste verwerking te integreren. Voor beelden hiervan zijn een aangepaste entity module of document classificatie gericht op een specifiek domein, zoals financiën, weten schappelijke publicaties of medicijnen.

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="when-to-use-cognitive-skills"></a>Wanneer moet u cognitieve vaardig heden gebruiken?

U kunt overwegen ingebouwde cognitieve vaardig heden te gebruiken als uw onbewerkte inhoud ongestructureerde tekst, afbeeldings inhoud of inhoud bevat die taal detectie en-omzetting nodig heeft. Het Toep assen van AI via de ingebouwde cognitieve vaardig heden kan deze inhoud ontgrendelen, waardoor de waarde en het hulp programma in uw zoek-en data Science-apps worden verhoogd. 

Daarnaast kunt u overwegen een aangepaste vaardigheid toe te voegen als u een open source-, derden-of code van een andere partij hebt die u wilt integreren in de pijp lijn. Classificatie modellen die opvallende kenmerken van verschillende document typen identificeren, vallen in deze categorie, maar elk pakket dat waarde toevoegt aan uw inhoud, kan ook worden gebruikt.

### <a name="more-about-built-in-skills"></a>Meer informatie over ingebouwde vaardig heden

Een vaardig heden die is geassembleerd met behulp van ingebouwde vaardig heden, is goed geschikt voor de volgende toepassings scenario's:

+ Gescande documenten (JPEG) waarvoor u Zoek opdrachten in volledige tekst wilt uitvoeren. U kunt een OCR-vaardigheid (Optical Character Recognition) koppelen om tekst uit JPEG-bestanden te identificeren, uit te pakken en op te nemen.

+ Pdf's met gecombineerde afbeelding en tekst. Tekst in Pdf's kan worden geëxtraheerd tijdens het indexeren zonder het gebruik van stappen voor het verrijken, maar het toevoegen van afbeeldingen en natuurlijke taal verwerking levert vaak een beter resultaat op dan een standaard indexering.

+ Meertalige inhoud waartegen u taal detectie en mogelijk tekst omzetting wilt Toep assen.

+ Ongestructureerde of semi-gestructureerde documenten met inhoud met inherente betekenis of context die in het grotere document is verborgen. 

  Blobs in het bijzonder bevatten vaak een grote inhoud die in één veld wordt verpakt. Door de vaardig heden van afbeeldingen en natuurlijke taal verwerking aan een Indexeer functie toe te voegen, kunt u nieuwe informatie maken die extant is in de onbewerkte inhoud, maar niet op andere wijze als DISTINCT-velden. Sommige kant-en-klare cognitieve vaardig heden die u kunnen helpen: sleutel woordgroepen extractie, sentiment analyse en entiteits herkenning (personen, organisaties en locaties).

  Daarnaast kunnen ingebouwde vaardig heden ook worden gebruikt om inhoud te herstructureren met behulp van tekst splitsen, samen voegen en vorm bewerkingen.

### <a name="more-about-custom-skills"></a>Meer informatie over aangepaste vaardig heden

Aangepaste vaardig heden kunnen complexere scenario's ondersteunen, zoals het herkennen van formulieren of het detecteren van aangepaste entiteiten met behulp van een model dat u opgeeft en inpakt in de [aangepaste vaardigheids webinterface](cognitive-search-custom-skill-interface.md). Enkele voor beelden van aangepaste vaardig heden zijn [formulieren Recognizer](/azure/cognitive-services/form-recognizer/overview), integratie van de [Bing entiteiten zoeken-API](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)en [aangepaste entiteits herkenning](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="components-of-an-enrichment-pipeline"></a>Onderdelen van een verrijkings pijplijn

Een verrijkings pijplijn is gebaseerd op [*Indexeer functies*](search-indexer-overview.md) die gegevens bronnen verkennen en end-to-end index verwerking bieden. Vaardig heden zijn nu gekoppeld aan Indexeer functies, het onderscheppen en verrijken van documenten volgens de vaardig heden die u definieert. Zodra de index is geïndexeerd, hebt u toegang tot inhoud via Zoek opdrachten via alle [query typen die door Azure Cognitive Search worden ondersteund](search-query-overview.md).  Als u geen ervaring hebt met Indexeer functies, begeleidt deze sectie u stapsgewijs door de stappen.

### <a name="step-1-connection-and-document-cracking-phase"></a>Stap 1: de fase verbinding en document opkraken

Aan het begin van de pijp lijn hebt u ongestructureerde tekst-of niet-tekst inhoud (zoals afbeeldings-en gescande JPEG-bestanden). Gegevens moeten aanwezig zijn in een Azure Data Storage-service die toegankelijk is voor een Indexeer functie. Indexeer functies kunnen bron documenten ' barsten ' om tekst uit de bron gegevens op te halen.

![De fase voor het kraken van documenten](./media/cognitive-search-intro/document-cracking-phase-blowup.png "document kraken")

 De volgende bronnen worden ondersteund: Azure Blob-opslag, Azure-tabel opslag, Azure SQL Database en Azure Cosmos DB. Inhoud op basis van tekst kan worden geëxtraheerd uit de volgende bestands typen: Pdf's, Word, Power Point, CSV-bestanden. Zie [ondersteunde indelingen](search-howto-indexing-azure-blob-storage.md#supported-document-formats)voor de volledige lijst.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Stap 2: cognitieve vaardig heden en verrijkings fase

Verrijking is via *cognitieve vaardig heden* waarmee atomische bewerkingen worden uitgevoerd. Wanneer u bijvoorbeeld tekst inhoud van een PDF-bestand hebt, kunt u herkenning van de taal van de entiteit of de sleutel woord extractie Toep assen om nieuwe velden in uw index te maken die niet standaard beschikbaar zijn in de bron. Samen wordt de verzameling van de vaardig heden die in uw pijp lijn wordt gebruikt, een *vakkennis*genoemd.  

![Verrijkings fase](./media/cognitive-search-intro/enrichment-phase-blowup.png "verrijkings fase")

Een vaardig heden is gebaseerd op [ingebouwde cognitieve vaardig heden](cognitive-search-predefined-skills.md) of [aangepaste vaardig heden](cognitive-search-create-custom-skill-example.md) die u verstrekt en maakt verbinding met de vakkennisset. Een vaardighedenset kan mini maal of zeer complex zijn en bepaalt niet alleen het type verwerking, maar ook de volg orde van de bewerkingen. Een vaardig heden plus de veld toewijzingen die zijn gedefinieerd als onderdeel van een Indexeer functie, geeft volledig de verrijkings pijplijn op. Zie [een vaardig heden definiëren](cognitive-search-defining-skillset.md)voor meer informatie over het samen stellen van al deze onderdelen.

Intern genereert de pijp lijn een verzameling verrijkte documenten. U kunt bepalen welke delen van de verrijkte documenten moeten worden toegewezen aan Indexeer bare velden in uw zoek index. Als u bijvoorbeeld de sleutel frase extractie en de vaardig heden voor entiteits herkenning hebt toegepast, worden deze nieuwe velden onderdeel van het verrijkte document en kunnen ze worden toegewezen aan velden in uw index. Zie [annotaties](cognitive-search-concept-annotations-syntax.md) voor meer informatie over invoer/uitvoer-vormingen.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Een knowledgeStore-element toevoegen om verrijkingen op te slaan

[Search rest API-Version = 2019-05 -06-preview](search-api-preview.md) breidt vaardig heden uit met een `knowledgeStore` definitie die een Azure Storage-verbinding en projecties biedt die beschrijven hoe de verrijkingen worden opgeslagen. 

Het toevoegen van een kennis archief aan een vaardig heden biedt u de mogelijkheid om een weer gave van uw verrijkingen te projecteren voor andere scenario's dan zoeken in volledige tekst. Zie voor meer informatie [kennis archief (preview)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Stap 3: zoek index en op query's gebaseerde toegang

Wanneer de verwerking is voltooid, hebt u een zoek index die bestaat uit verrijkte documenten, met volledige tekst doorzoekbaar in azure Cognitive Search. Bij [het uitvoeren van een query op de index](search-query-overview.md) wordt uitgelegd hoe ontwikkel aars en gebruikers toegang hebben tot de verrijkte inhoud die door de pijp lijn wordt gegenereerd. 

![Index met zoek pictogram](./media/cognitive-search-intro/search-phase-blowup.png "Index met zoek pictogram")

De index is net als andere die u kunt maken voor Azure Cognitive Search: u kunt een aanvulling vormen op aangepaste analyse functies, query's voor fuzzy zoeken uitvoeren, een gefilterde zoek opdracht toevoegen, of experimenteren met Score profielen om de vorm van de zoek resultaten te wijzigen.

Indexen worden gegenereerd op basis van een index schema waarmee de velden, kenmerken en andere constructies worden gedefinieerd die zijn gekoppeld aan een specifieke index, zoals Score profielen en synoniemen. Zodra een index is gedefinieerd en ingevuld, kunt u incrementeel indexeren om nieuwe en bijgewerkte bron documenten op te halen. Bepaalde wijzigingen moeten volledig opnieuw worden opgebouwd. U moet een kleine gegevensset gebruiken totdat het schema ontwerp stabiel is. Zie [How to rebuild an index](search-howto-reindex.md) (Een index herbouwen) voor meer informatie.

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Belangrijke functies en concepten

| Concept | Beschrijving| Koppelingen |
|---------|------------|-------|
| Vaardig heden | Een resource op het hoogste niveau met een verzameling van vaardig heden. Een vakkennisset is de verrijkings pijplijn. De functie wordt aangeroepen tijdens het indexeren door een Indexeer functie. | Zie [een vaardig heden definiëren](cognitive-search-defining-skillset.md) |
| Cognitieve vaardigheid | Een Atomic-trans formatie in een verrijkings pijplijn. Vaak is het een onderdeel dat de structuur extraheert of afleidt, en waarmee u uw inzichten in de invoer gegevens kunt verbeteren. Bijna altijd is de uitvoer op tekst gebaseerd en de verwerking is natuurlijke taal verwerking of afbeeldings verwerking, die tekst uit de afbeeldings invoer extraheert of genereert. Uitvoer van een vaardigheid kan worden toegewezen aan een veld in een index of worden gebruikt als invoer voor een downstream-verrijking. Een vaardigheid is vooraf gedefinieerd en wordt verzorgd door micro soft, of aangepast: gemaakt en geïmplementeerd door u. | [Ingebouwde cognitieve vaardig heden](cognitive-search-predefined-skills.md) |
| Ophalen van gegevens | Geldt voor een breed scala aan verwerkings processen, maar met name voor AI-verrijking wordt de kwalificatie voor entiteits herkenning meestal gebruikt voor het extra heren van gegevens (een entiteit) van een bron die deze informatie niet zelf verstrekt. | Zie vaardigheid van [entiteits herkenning](cognitive-search-skill-entity-recognition.md) en [kwalificatie van document extractie (preview-versie)](cognitive-search-skill-document-extraction.md)| 
| Verwerking van installatiekopieën | Hiermee wordt tekst afgeleid van een afbeelding, zoals de mogelijkheid om een oriëntatie punt te herkennen of tekst uit een afbeelding uit te pakken. Veelvoorkomende voor beelden zijn onder andere OCR voor het opheffen van tekens uit een gescand document bestand (JPEG) of het herkennen van een straat naam in een foto met een straat teken. | Zie [afbeeldings vaardigheid vaardig heden](cognitive-search-skill-image-analysis.md) of [OCR-vaardigheid](cognitive-search-skill-ocr.md)
| Natuurlijke taalverwerking | Tekst verwerking voor inzichten en informatie over tekst invoer. Taal detectie, sentiment analyse en extractie van sleutel zinnen zijn vaardig heden die onder de natuurlijke taal verwerking vallen.  | Zie [Sleuteltermextractie vaardig heden](cognitive-search-skill-keyphrases.md), [taaldetectie vaardigheid](cognitive-search-skill-language-detection.md), [vaardigheid van tekst vertalingen (preview)](cognitive-search-skill-text-translation.md), [sentimentanalyse vaardigheid](cognitive-search-skill-sentiment.md) |
| Kraken van document | Het proces waarbij tekst inhoud wordt geëxtraheerd of gemaakt op basis van niet-tekst bronnen tijdens het indexeren. Optische teken herkenning (OCR) is een voor beeld, maar dit is in het algemeen de functionaliteit van de Indexeer functie, omdat de indexer inhoud uit toepassings bestanden haalt. De gegevens bron die de bron bestands locatie levert en de definitie van de Indexeer functie die veld Toewijzingen levert, zijn beide belangrijkste factoren in het kraken van documenten. | Zie [overzicht van Indexeer functies](search-indexer-overview.md) |
| Vorm geven | Consolideer tekst fragmenten in een grotere structuur, of splits grotere tekst segmenten in een beheersbaar formaat voor verdere downstream-verwerking. | Zie [shaper-vaardigheid](cognitive-search-skill-shaper.md), [vaardigheid van tekst fusie](cognitive-search-skill-textmerger.md), [vaardigheid tekst splitsen](cognitive-search-skill-textsplit.md) |
| Verrijkte documenten | Een interne tijdelijke structuur die tijdens de verwerking wordt gegenereerd, waarbij de uiteindelijke uitvoer wordt weer gegeven in een zoek index. Een vaardig heden bepaalt welke verrijkingen worden uitgevoerd. Veld Toewijzingen bepalen welke gegevens elementen aan de index worden toegevoegd. U kunt desgewenst een kennis archief maken om verrijkte documenten op te slaan en te verkennen met behulp van hulpprogram ma's als Storage Explorer, Power BI of een ander hulp programma dat verbinding maakt met Azure Blob Storage. | Zie [kennis archief (preview-versie)](knowledge-store-concept-intro.md) |
| Indexer |  Een crawler die Doorzoek bare gegevens en meta gegevens ophaalt uit een externe gegevens bron en een index vult op basis van veld-naar-veld Toewijzingen tussen de index en uw gegevens bron voor het kraken van documenten. Voor AI-verrijkingen roept de Indexeer functie een vaardig heden aan en bevat de veld toewijzingen die de verrijkings uitvoer koppelen aan doel velden in de index. De definitie van de Indexeer functie bevat alle instructies en verwijzingen voor pijplijn bewerkingen en de pijp lijn wordt geactiveerd wanneer u de Indexeer functie uitvoert. Met aanvullende configuratie kunt u bestaande verwerkte inhoud opnieuw gebruiken en alleen de stappen en vaardig heden uitvoeren die zijn gewijzigd. | Zie [Indexeer functies](search-indexer-overview.md) en [incrementele verrijking (preview-versie)](cognitive-search-incremental-indexing-conceptual.md). |
| Gegevensbron  | Een object dat door een Indexeer functie wordt gebruikt om verbinding te maken met een externe gegevens bron van ondersteunde typen in Azure. | Zie [overzicht van Indexeer functies](search-indexer-overview.md) |
| Index | Een persistente zoek index in azure Cognitive Search, gebouwd op basis van een index schema waarmee de veld structuur en het gebruik worden gedefinieerd. | Zie [een basis index maken](search-what-is-an-index.md) | 
| Knowledge Store | Een opslag account waar de verrijkte documenten kunnen worden gevormd en worden geprojecteerd naast de zoek index | Zie [Inleiding tot het kennis archief](knowledge-store-concept-intro.md) | 
| Cache | Een opslag account dat uitvoer in de cache bevat die is gemaakt door een verrijkings pijplijn. Als u de cache inschakelt, blijft bestaande uitvoer behouden die niet wordt beïnvloed door wijzigingen in een vaardig heden of andere onderdelen van de verrijkings pijplijn. | Zie [incrementele verrijking](cognitive-search-incremental-indexing-conceptual.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Waar moet ik beginnen?

**Stap 1: [een Azure Cognitive Search-resource maken](search-create-service-portal.md)** 

**Stap 2: Probeer enkele Snelstartgids en voor beelden voor praktijk ervaring**

+ [Snelstartgids (Portal)](cognitive-search-quickstart-blob.md)
+ [Zelf studie (HTTP-aanvragen)](cognitive-search-tutorial-blob.md)
+ [Voor beeld: een aangepaste vaardigheid maken voor AI-verrijking (C#)](cognitive-search-create-custom-skill-example.md)

We raden u aan de gratis service voor Learning doeleinden te maken, maar het aantal gratis trans acties is beperkt tot 20 documenten per dag. Als u zowel de Snelstartgids als zelf studie op één dag wilt uitvoeren, gebruikt u een kleinere set (10 documenten) zodat u in beide oefeningen kunt passen of verwijdert u de Indexeer functie die u in de Snelstartgids of zelf studie hebt gebruikt om de teller op nul te zetten.

**Stap 3: de API controleren**

U kunt REST-`api-version=2019-05-06` op aanvragen of de .NET SDK gebruiken. Als u het kennis archief wilt verkennen, gebruikt u de preview-REST API in plaats (`api-version=2019-05-06-Preview`).

In deze stap wordt gebruikgemaakt van de REST-Api's voor het bouwen van een AI-verrijkings oplossing. Er zijn slechts twee Api's toegevoegd of uitgebreid voor AI-verrijking. Andere Api's hebben dezelfde syntaxis als de algemeen beschik bare versies.

| REST API | Beschrijving |
|-----|-------------|
| [Gegevensbron maken](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Een bron voor het identificeren van een externe gegevens bron die bron gegevens levert die worden gebruikt voor het maken van verrijkte documenten.  |
| [Vaardig heden maken (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Deze API is specifiek voor AI-verrijking. Het is een resource die het gebruik van [ingebouwde vaardig heden](cognitive-search-predefined-skills.md) en [aangepaste cognitieve vaardig heden](cognitive-search-custom-skill-interface.md) die in een verrijkings pijplijn worden gebruikt, coördineert tijdens het indexeren. |
| [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Een schema waarmee een Azure Cognitive Search-index wordt uitgedrukt. Velden in de index toewijzing naar velden in bron gegevens of in velden die worden geproduceerd tijdens de verrijkings fase (bijvoorbeeld een veld voor organisatie namen die zijn gemaakt door entiteits herkenning). |
| [Indexeer functie maken (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Een resource waarmee onderdelen worden gedefinieerd die worden gebruikt tijdens de indexering: een gegevens bron, een vaardig heden, veld koppelingen van bron-en intermediair gegevens structuren naar doel index en de index zelf. Het uitvoeren van de Indexeer functie is de trigger voor gegevens opname en verrijking. De uitvoer is een zoek index op basis van het index schema, gevuld met bron gegevens, verrijkt via vaardig heden. Deze bestaande API wordt uitgebreid voor cognitieve Zoek scenario's met het opnemen van een vakkennisset-eigenschap. |

**Controle lijst: een typische werk stroom**

1. Deel uw Azure-bron gegevens in een representatieve steek proef. Het indexeren vergt enige tijd met een kleine, representatieve gegevensset en bouwt deze vervolgens incrementeel op als uw oplossing is gerijpt.

1. Maak een [gegevens bron object](https://docs.microsoft.com/rest/api/searchservice/create-data-source) in azure Cognitive Search om een Connection String op te geven voor het ophalen van gegevens.

1. Maak een [vaardig heden](https://docs.microsoft.com/rest/api/searchservice/create-skillset) met verrijkings stappen.

1. Definieer het [index schema](https://docs.microsoft.com/rest/api/searchservice/create-index). De verzameling *velden* bevat velden uit de bron gegevens. U moet ook extra velden uitstuben voor het opslaan van gegenereerde waarden voor inhoud die tijdens verrijking is gemaakt.

1. Definieer de [Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-skillset) die verwijst naar de gegevens bron, de vaardig heden en de index.

1. Voeg in de Indexeer functie *outputFieldMappings*toe. In deze sectie wordt de uitvoer van de vaardig heden (in stap 3) toegewezen aan de invoer velden in het index schema (in stap 4).

1. *Maak Create Indexing* -aanvraag die u zojuist hebt gemaakt (een post-aanvraag met een indexers definitie in de hoofd tekst van de aanvraag) om de Indexeer functie in azure Cognitive Search te drukken. Deze stap bestaat uit het uitvoeren van de Indexeer functie en het aanroepen van de pijp lijn.

1. Voer query's uit om de resultaten te evalueren en de code te wijzigen om vaardig heden, schema of Indexeer configuratie bij te werken.

1. [Stel de Indexeer functie opnieuw](search-howto-reindex.md) in voordat u de pijp lijn opnieuw bouwt.

Zie [probleemoplossings tips](cognitive-search-concept-troubleshooting.md)voor meer informatie over specifieke vragen of problemen.

## <a name="next-steps"></a>Volgende stappen

+ [Uitgebreide documentatie koppelingen voor AI-verrijking](cognitive-search-resources-documentation.md)
+ [Quick Start: AI-verrijking in een portal-overzicht](cognitive-search-quickstart-blob.md)
+ [Zelf studie: meer informatie over de AI-verrijkings Api's](cognitive-search-tutorial-blob.md)
+ [Kennis archief (preview-versie)](knowledge-store-concept-intro.md)
+ [Een kennis archief maken in REST](knowledge-store-create-rest.md)
