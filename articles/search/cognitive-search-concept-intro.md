---
title: Inleiding tot AI-verrijking
titleSuffix: Azure Cognitive Search
description: Contentextractie, natural language processing (NLP) en beeldverwerking worden gebruikt om doorzoekbare inhoud te maken in Azure Cognitive Search-indexen met zowel vooraf gedefinieerde cognitieve vaardigheden als aangepaste AI-algoritmen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: a41dcc9c7ec86f41c64a69ea1aba762b960b2633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283018"
---
# <a name="getting-started-with-ai-enrichment"></a>Aan de slag met AI-verrijking

AI-verrijking is een mogelijkheid voor Azure Cognitive Search-indexering die wordt gebruikt om tekst uit afbeeldingen, blobs en andere ongestructureerde gegevensbronnen te extraheren. Verrijking en extractie maken uw inhoud doorzoeker in een [index](search-what-is-an-index.md) of [kenniswinkel.](knowledge-store-concept-intro.md) Extractie en verrijking worden geïmplementeerd met behulp van *cognitieve vaardigheden* die zijn gekoppeld aan de indexeringpijplijn. Cognitieve vaardigheden ingebouwd in de dienst vallen in deze categorieën: 

+ **Natuurlijke taalverwerkingsvaardigheden** omvatten [entiteitsherkenning,](cognitive-search-skill-entity-recognition.md) [taaldetectie,](cognitive-search-skill-language-detection.md) [sleutelzinextractie,](cognitive-search-skill-keyphrases.md)tekstmanipulatie, [sentimentdetectie](cognitive-search-skill-sentiment.md)en [PII-detectie](cognitive-search-skill-pii-detection.md). Met deze vaardigheden wordt ongestructureerde tekst toegewezen als doorzoekbare en filterbare velden in een index.

+ **Beeldverwerkingsvaardigheden** omvatten [Optical Character Recognition (OCR)](cognitive-search-skill-ocr.md) en identificatie van [visuele functies,](cognitive-search-skill-image-analysis.md)zoals gezichtsherkenning, beeldinterpretatie, beeldherkenning (beroemde mensen en oriëntatiepunten) of kenmerken zoals beeldoriëntatie. Deze vaardigheden maken tekstrepresentaties van afbeeldingsinhoud, waardoor deze doorzoekbaar is met behulp van de querymogelijkheden van Azure Cognitive Search.

![Verrijkingspijplijndiagram](./media/cognitive-search-intro/cogsearch-architecture.png "overzicht van verrijkingspijplijn")

Cognitieve vaardigheden in Azure Cognitive Search zijn gebaseerd op vooraf getrainde machine learning-modellen in Cognitive Services API's: [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) en [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Natuurlijke taal- en beeldverwerking wordt toegepast tijdens de gegevensopnamefase, waarbij resultaten onderdeel worden van de samenstelling van een document in een doorzoekbare index in Azure Cognitive Search. Gegevens worden afkomstig van Azure-gegevensset en vervolgens door een indexeringspijplijn gepusht met behulp van de [ingebouwde vaardigheden](cognitive-search-predefined-skills.md) die u nodig hebt. De architectuur is uitbreidbaar, dus als de ingebouwde vaardigheden niet voldoende zijn, u [aangepaste vaardigheden](cognitive-search-create-custom-skill-example.md) maken en koppelen om aangepaste verwerking te integreren. Voorbeelden hiervan zijn een aangepaste entiteitsmodule of documentclassificatie die zich richt op een specifiek domein, zoals financiën, wetenschappelijke publicaties of medicijnen.

## <a name="when-to-use-ai-enrichment"></a>Wanneer AI-verrijking gebruiken

U moet overwegen om ingebouwde cognitieve vaardigheden te gebruiken als uw ruwe inhoud ongestructureerde tekst, afbeeldingsinhoud of inhoud is die taaldetectie en -vertaling vereist. Het toepassen van AI via de ingebouwde cognitieve vaardigheden kan deze inhoud ontgrendelen, waardoor de waarde en het nut ervan in uw zoek- en datascience-apps worden verhoogd. 

Bovendien u overwegen een aangepaste vaardigheid toe te voegen als u een open-source-, externe of first-party-code hebt die u in de pijplijn wilt integreren. Classificatiemodellen die opvallende kenmerken van verschillende documenttypen identificeren, vallen in deze categorie, maar elk pakket dat waarde toevoegt aan uw inhoud, kan worden gebruikt.

### <a name="more-about-built-in-skills"></a>Meer over ingebouwde vaardigheden

Een [skillset](cognitive-search-defining-skillset.md) die wordt samengesteld met behulp van ingebouwde vaardigheden is zeer geschikt voor de volgende toepassingsscenario's:

+ Gescande documenten (JPEG) die u wilt full-text doorzoekbaar te maken. U een OCR-vaardigheid (optical character recognition) toevoegen om tekst uit JPEG-bestanden te identificeren, extraheren en innemen.

+ PDF's met gecombineerde afbeelding en tekst. Tekst in PDF's kan worden geëxtraheerd tijdens het indexeren zonder het gebruik van verrijkingsstappen, maar de toevoeging van beeld- en natuurlijke taalverwerking kan vaak een beter resultaat opleveren dan een standaardindexering biedt.

+ Meertalige inhoud waartegen u taaldetectie en eventueel tekstvertaling wilt toepassen.

+ Ongestructureerde of semi-gestructureerde documenten die inhoud bevatten die inherente betekenis of context heeft die in het grotere document is verborgen. 

  Blobs in het bijzonder bevatten vaak een grote hoeveelheid inhoud die is verpakt in een singled "veld". Door beeld- en natuurlijke taalverwerkingsvaardigheden aan een indexeerder toe te voegen, u nieuwe informatie maken die in de ruwe inhoud aanwezig is, maar anders niet als afzonderlijke velden is opgedoken. Enkele kant-en-klare ingebouwde cognitieve vaardigheden die kunnen helpen: sleutelzinextractie, sentimentanalyse en entiteitsherkenning (mensen, organisaties en locaties).

  Bovendien kunnen ingebouwde vaardigheden ook worden gebruikt om inhoud te herstructureren via tekstsplitsings-, samenvoegings- en vormbewerkingen.

### <a name="more-about-custom-skills"></a>Meer informatie over aangepaste vaardigheden

Aangepaste vaardigheden kunnen complexere scenario's ondersteunen, zoals het herkennen van formulieren of aangepaste entiteitsdetectie met behulp van een model dat u verstrekt en in de [aangepaste webinterface voor vaardigheden](cognitive-search-custom-skill-interface.md)inpakt. Verschillende voorbeelden van aangepaste vaardigheden zijn [Forms Recognizer,](/azure/cognitive-services/form-recognizer/overview)integratie van de [Bing Entity Search API](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)en aangepaste [entiteitsherkenning](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="steps-in-an-enrichment-pipeline"></a>Stappen in een verrijkingspijplijn

Een verrijkingspijplijn is gebaseerd op [*indexers*](search-indexer-overview.md). Indexers vullen een index op basis van veld-naar-veldtoewijzingen tussen de index en uw gegevensbron voor het kraken van documenten. Vaardigheden, nu gekoppeld aan indexers, onderscheppen en verrijken documenten volgens de skillset(s) die u definieert. Eenmaal geïndexeerd, hebt u toegang tot inhoud via zoekverzoeken via alle [querytypen die worden ondersteund door Azure Cognitive Search.](search-query-overview.md)  Als u nieuw bent bij indexers, leidt deze sectie u door de stappen.

### <a name="step-1-connection-and-document-cracking-phase"></a>Stap 1: Fase van het kraken van verbindingen en documenten

Aan het begin van de pijplijn hebt u ongestructureerde tekst- of niet-tekstinhoud (zoals afbeeldingen, gescande documenten of JPEG-bestanden). Er moeten gegevens bestaan in een Azure-gegevensopslagservice die door een indexeerder kan worden geopend. Indexers kunnen brondocumenten "kraken" om tekst uit brongegevens te extraheren. Het kraken van documenten is het proces van het extraheren of maken van tekstinhoud uit niet-tekstbronnen tijdens het indexeren.

![Fase van het kraken van documenten](./media/cognitive-search-intro/document-cracking-phase-blowup.png "document kraken")

 Ondersteunde bronnen zijn Azure blob-opslag, Azure-tabelopslag, Azure SQL Database en Azure Cosmos DB. Inhoud op basis van tekst kan worden geëxtraheerd uit de volgende bestandstypen: PDF's, Word, PowerPoint, CSV-bestanden. Zie [Ondersteunde indelingen](search-howto-indexing-azure-blob-storage.md#supported-document-formats)voor de volledige lijst . Indexeren kost tijd, dus begin met een kleine, representatieve gegevensset en bouw deze stapsgewijs op naarmate uw oplossing rijpt.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Stap 2: Cognitieve vaardigheden en verrijkingsfase

Verrijking wordt uitgevoerd met *cognitieve vaardigheden* die atoomoperaties uitvoeren. Als u bijvoorbeeld een PDF hebt gekraakt, u entiteitsherkenning, taaldetectie of sleutelwoordextractie toepassen om nieuwe velden in uw index te produceren die niet native beschikbaar zijn in de bron. Al met al wordt het verzamelen van vaardigheden die in uw pijplijn worden gebruikt een *skillset genoemd.*  

![Verrijkingsfase](./media/cognitive-search-intro/enrichment-phase-blowup.png "verrijkingsfase")

Een skillset is gebaseerd op [ingebouwde cognitieve vaardigheden](cognitive-search-predefined-skills.md) of [aangepaste vaardigheden](cognitive-search-create-custom-skill-example.md) die je levert en maak verbinding met de vaardigheden. Een skillset kan minimaal of zeer complex zijn en bepaalt niet alleen het type verwerking, maar ook de volgorde van bewerkingen. Een skillset plus de veldtoewijzingen die zijn gedefinieerd als onderdeel van een indexer, geeft de verrijkingspijplijn volledig aan. Zie [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)voor meer informatie over het samenbrengen van al deze stukken.

Intern genereert de pijplijn een verzameling verrijkte documenten. U bepalen welke delen van de verrijkte documenten moeten worden toegewezen aan indexeerbare velden in uw zoekindex. Als u bijvoorbeeld de sleutelwoordextractie en de entiteitsherkenningsvaardigheden hebt toegepast, worden deze nieuwe velden onderdeel van het verrijkte document en kunnen ze worden toegewezen aan velden in uw index. Zie [Annotaties](cognitive-search-concept-annotations-syntax.md) voor meer informatie over invoer-/uitvoerformaties.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Een knowledgeStore-element toevoegen om verrijkingen op te slaan

[Zoek REST api-version=2019-05-06-Preview](search-api-preview.md) breidt `knowledgeStore` skillsets uit met een definitie die een Azure-opslagverbinding en projecties biedt die beschrijven hoe de verrijkingen worden opgeslagen. Dit is in aanvulling op uw index. In een standaard AI-pijplijn zijn verrijkte documenten van voorbijgaande aard, alleen gebruikt tijdens het indexeren en vervolgens weggegooid. Met kenniswinkel worden verrijkte documenten bewaard. Zie [Kennisarchief (preview) voor](knowledge-store-concept-intro.md)meer informatie.

### <a name="step-3-search-index-and-query-based-access"></a>Stap 3: Zoekindex en toegang op basis van query's

Wanneer de verwerking is voltooid, hebt u een zoekindex bestaande uit verrijkte documenten, volledig doorzoekbaar voor tekst in Azure Cognitive Search. [Het opvragen van de index](search-query-overview.md) is hoe ontwikkelaars en gebruikers toegang krijgen tot de verrijkte inhoud die door de pijplijn wordt gegenereerd. 

![Indexeren met zoekpictogram](./media/cognitive-search-intro/search-phase-blowup.png "Indexeren met zoekpictogram")

De index is net als alle andere die u voor Azure Cognitive Search maken: u deze aanvullen met aangepaste analysers, vage zoekopdrachten aanroepen, gefilterd zoeken toevoegen of experimenteren met scoreprofielen om de zoekresultaten opnieuw vorm te geven.

Indexen worden gegenereerd uit een indexschema dat de velden, kenmerken en andere constructies definieert die aan een specifieke index zijn gekoppeld, zoals scoreprofielen en synoniemenkaarten. Zodra een index is gedefinieerd en ingevuld, u stapsgewijs indexeren om nieuwe en bijgewerkte brondocumenten op te halen. Bepaalde wijzigingen vereisen een volledige wederopbouw. U moet een kleine gegevensset gebruiken totdat het schemaontwerp stabiel is. Zie [How to rebuild an index](search-howto-reindex.md) (Een index herbouwen) voor meer informatie.

**Checklist: een typische werkstroom**

1. Deel uw Azure-brongegevens in een representatief voorbeeld. Indexeren kost tijd, dus begin met een kleine, representatieve gegevensset en bouw deze stapsgewijs op naarmate uw oplossing rijpt.

1. Maak een [gegevensbronobject](https://docs.microsoft.com/rest/api/searchservice/create-data-source) in Azure Cognitive Search om een verbindingstekenreeks te bieden voor het ophalen van gegevens.

1. Maak een [skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) met verrijkingsstappen.

1. Definieer het [indexschema](https://docs.microsoft.com/rest/api/searchservice/create-index). De verzameling *Velden* bevat velden uit brongegevens. U moet ook extra velden verwijderen om gegenereerde waarden vast te houden voor inhoud die tijdens verrijking is gemaakt.

1. Definieer de [indexer](https://docs.microsoft.com/rest/api/searchservice/create-skillset) die verwijst naar de gegevensbron, vaardigheden en index.

1. Voeg in de *indexering outputFieldMappings*toe . In deze sectie wordt de uitvoer van de skillset (in stap 3) toegewezen aan de invoervelden in het indexschema (in stap 4).

1. Verzend het verzoek *voor Indexer* maken die u zojuist hebt gemaakt (een post-aanvraag met een indexerdefinitie in de aanvraaginstantie) om de indexer in Azure Cognitive Search uit te drukken. Met deze stap voert u de indexer uit en doet u een beroep op de pijplijn.

1. Voer query's uit om resultaten te evalueren en code te wijzigen om skillsets, schema' of indexerconfiguratie bij te werken.

1. [Stel de indexer opnieuw in](search-howto-reindex.md) voordat u de pijplijn opnieuw herstelt.

## <a name="next-steps"></a>Volgende stappen

+ [Koppelingen naar aI-verrijkingsdocumentatie](cognitive-search-resources-documentation.md)
+ [Voorbeeld: Een aangepaste vaardigheid maken voor AI-verrijking (C#)](cognitive-search-create-custom-skill-example.md)
+ [Quickstart: Probeer AI-verrijking in een portal-walk-through](cognitive-search-quickstart-blob.md)
+ [Zelfstudie: Meer informatie over de AI-verrijkings-API's](cognitive-search-tutorial-blob.md)
+ [Knowledge Store (preview)](knowledge-store-concept-intro.md)
+ [Maak een kenniswinkel in REST](knowledge-store-create-rest.md)
+ [Tips voor probleemoplossing](cognitive-search-concept-troubleshooting.md)
