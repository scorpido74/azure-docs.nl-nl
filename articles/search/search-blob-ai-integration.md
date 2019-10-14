---
title: AI gebruiken voor het begrijpen van BLOB-gegevens
titleSuffix: Azure Search
description: Voeg semantische, verwerking van natuurlijke taal en afbeeldings analyse toe aan Azure-blobs met behulp van een AI-pijp lijn in Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 2513825fcb275aeb3c4f0ca49ff5f2a6bd9441f0
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303013"
---
# <a name="use-ai-to-understand-blob-data"></a>AI gebruiken voor het begrijpen van BLOB-gegevens

Gegevens in Azure Blob Storage zijn vaak een verscheidenheid aan ongestructureerde inhoud zoals afbeeldingen, lange tekst, Pdf's en Office-documenten. Door gebruik te maken van de AI-mogelijkheden in Azure Search, kunt u op verschillende manieren waardevolle informatie uit blobs begrijpen en extra heren. Voor beelden van het Toep assen van AI op blob-inhoud zijn:

+ Tekst uit afbeeldingen extra heren met behulp van optische teken herkenning (OCR)
+ Een beschrijving of tags van een scène maken op basis van een foto
+ Taal detecteren en tekst vertalen in verschillende talen
+ Tekst verwerken met name entity Recognition (NER) om verwijzingen naar personen, datums, plaatsen of organisaties te vinden 

Hoewel u mogelijk slechts een van deze AI-mogelijkheden nodig hebt, is het gebruikelijk om meerdere van deze te combi neren in dezelfde pijp lijn (bijvoorbeeld door tekst op te halen uit een gescande afbeelding en vervolgens alle datums en plaatsen te vinden waarnaar wordt verwezen). 

Met AI-verrijking maakt u nieuwe informatie, vastgelegd als tekst, opgeslagen in velden. Post-verrijking, u kunt deze gegevens openen vanuit een zoek index door te zoeken in volledige tekst of verrijkte documenten terug te sturen naar Azure Storage om nieuwe ervaringen van toepassingen te bieden die gegevens verkennen voor detectie-en analyse scenario's. 

In dit artikel bekijken we AI-verrijking via een brede lens, zodat u snel het hele proces kunt begrijpt, van het transformeren van onbewerkte gegevens in blobs, naar Doorzoek bare gegevens in een zoek index of een kennis archief.

## <a name="what-it-means-to-enrich-blob-data"></a>Wat betekent ' verrijkt ' BLOB-gegevens

*AI-verrijking* maakt deel uit van de indexerings architectuur van Azure Search die ingebouwde AI integreert van micro soft of aangepaste AI die u opgeeft. Het helpt u bij het implementeren van end-to-end scenario's waarin u blobs moet verwerken (zowel bestaande als nieuwe, zoals deze worden geleverd of bijgewerkt), kraken alle bestands indelingen openen om afbeeldingen en tekst te extra heren, de gewenste informatie extra heren met behulp van verschillende AI-mogelijkheden en Indexeer ze in een Azure Search index voor snel zoeken, ophalen en verkennen. 

Invoer zijn uw blobs in een enkele container in Azure Blob Storage. Blobs kunnen bijna elk soort tekst-of afbeeldings gegevens zijn. 

Output is altijd een Azure Search index, die wordt gebruikt voor het snel zoeken van tekst, ophalen en verkennen in client toepassingen. Daarnaast kan de uitvoer ook een *kennis archief* zijn dat projecten verrijkt in azure blobs of Azure-tabellen voor downstream-analyses in hulpprogram ma's zoals Power bi of in data Science-workloads.

Tussen is de pijplijn architectuur zelf. De pijp lijn is gebaseerd op de *Indexeer* functie, waaraan u een *vaardig heden*kunt toewijzen. deze bestaat uit een of meer *vaardig heden* die de AI bieden. Het doel van de pijp lijn is om *verrijkte documenten* te produceren die als onbewerkte inhoud worden ingevoerd, maar extra structuur, context en informatie ophalen terwijl u door de pijp lijn beweegt. Verrijkte documenten worden tijdens de indexering gebruikt om omgekeerde indexen en andere structuren te maken die worden gebruikt voor Zoek opdrachten in volledige tekst of verkennen en analyses.

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag

U kunt rechtstreeks beginnen op de portal-pagina van uw opslag account. Klik op **Azure Search toevoegen** en maak een nieuwe Azure Search-service of selecteer een bestaand account. Als u al een bestaande zoek service in hetzelfde abonnement hebt, klikt u op **toevoegen Azure Search** opent u de wizard gegevens importeren, zodat u direct kunt door lopen met indexeren, verrijking en index definitie.

Nadat u Azure Search aan uw opslag account hebt toegevoegd, kunt u het standaard proces volgen om gegevens in een Azure-gegevens bron te verrijken. Ervan uitgaande dat u al blob-inhoud hebt, kunt u de wizard gegevens importeren in Azure Search gebruiken voor een eenvoudige inleiding tot AI-verrijking. In deze Quick Start worden de stappen uitgelegd: [een AI-verrijkings pijplijn maken in de portal](cognitive-search-quickstart-blob.md). 

In de volgende secties gaan we meer onderdelen en concepten verkennen.

## <a name="use-blob-indexers"></a>BLOB-Indexeer functies gebruiken

AI-verrijking is een invoeg toepassing voor een indexerings pijplijn en in Azure Search zijn deze pijp lijnen gebaseerd op een *Indexeer functie*. Een Indexeer functie is een subservice met gegevens bronnen die is voorzien van interne logica voor het bemonsteren van gegevens, het lezen van meta gegevens, het ophalen van gegevens en het serialiseren van gegevens van systeem eigen indelingen in JSON-documenten voor de volgende import. Indexeer functies worden vaak gebruikt voor importeren, gescheiden van AI, maar als u een AI-verrijkings pijplijn wilt maken, hebt u een Indexeer functie en een vakkennisset nodig om ermee te kunnen werken. In deze sectie wordt de nadruk gelegd op de Indexeer functie zelf.

Blobs in Azure Storage worden geïndexeerd met behulp van de [Azure Search Blob Storage-indexer](search-howto-indexing-azure-blob-storage.md). U roept deze Indexeer functie aan door het type in te stellen en door verbindings gegevens op te geven die een Azure Storage account bevatten samen met een BLOB-container. Tenzij u eerder blobs in een virtuele map hebt ingedeeld, die u vervolgens als een para meter kunt door geven, haalt de BLOB-Indexeer functie uit de hele container op.

Een Indexeer functie doet het ' document kraken ' en na het verbinden met de gegevens bron is de eerste stap in de pijp lijn. In het geval van BLOB-gegevens worden er PDF-, Office-docs-, afbeeldings-en andere inhouds typen gedetecteerd. Het kraken van documenten met tekst extractie wordt niet in rekening gebracht. Het kraken van documenten met afbeeldings extractie wordt in rekening gebracht tegen tarieven die u kunt vinden op de pagina met Azure Search [prijzen](https://azure.microsoft.com/pricing/details/search/).

Hoewel alle documenten worden gekraakt, treedt er alleen uitgebreide verrijking op als u de vaardig heden expliciet opgeeft. Als uw pijp lijn bijvoorbeeld uitsluitend bestaat uit tekst analyse, worden afbeeldingen in uw container of documenten genegeerd.

De BLOB-indexer wordt geleverd met configuratie parameters en biedt ondersteuning voor het bijhouden van wijzigingen als de onderliggende gegevens voldoende informatie bevatten. Meer informatie over de kern functionaliteit vindt u in [Azure Search Blob Storage indexer](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai"></a>AI toevoegen

*Vaardig heden* zijn de afzonderlijke onderdelen van AI-verwerking die u zelfstandig of in combi natie met andere vaardig heden kunt gebruiken voor sequentiële verwerking. 

+ Ingebouwde vaardig heden worden ondersteund door Cognitive Services, met afbeeldings analyse op basis van Computer Vision en de verwerking van natuurlijke taal op basis van Text Analytics. Enkele voor beelden hiervan zijn [OCR](cognitive-search-skill-ocr.md), [herkenning van entiteiten](cognitive-search-skill-entity-recognition.md)en [afbeeldings analyse](cognitive-search-skill-image-analysis.md). U kunt de volledige lijst met ingebouwde vaardig heden bekijken in [vooraf gedefinieerde vaardig heden voor inhouds verrijking](cognitive-search-predefined-skills.md).

+ Aangepaste vaardig heden zijn aangepaste code, verpakt in een interface definitie waarmee integratie kan worden geïntegreerd in de pijp lijn. In klant oplossingen is het gebruikelijk om beide te gebruiken met aangepaste vaardig heden, AI-modules van derden of van derden.

Een *vakkennisset* is de verzameling van de vaardig heden die worden gebruikt in een pijp lijn en deze wordt aangeroepen nadat de fase voor het kraken van het document de inhoud beschikbaar maakt. Een Indexeer functie kan precies één vaardig heden gebruiken, maar deze vaardig heden bestaan onafhankelijk van een indexer, zodat u deze in andere scenario's opnieuw kunt gebruiken.

Aangepaste vaardig heden kunnen worden gefragmenteerd, maar zijn gemakkelijk en eenvoudig in termen van implementatie. Als u bestaande pakketten hebt die patroon overeenkomsten of classificatie modellen bieden, kan de inhoud die u ophaalt uit blobs worden door gegeven aan deze modellen voor verwerking. Omdat AI-verrijking op basis van Azure is, moet uw model ook in azure zijn. Enkele veelgebruikte hosting methoden zijn het gebruik van [Azure functions](cognitive-search-create-custom-skill-example.md) of [containers](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Voor ingebouwde vaardig heden die door Cognitive Services worden ondersteund, is een [bijgevoegde Cognitive Services](cognitive-search-attach-cognitive-services.md) alles-in-één-abonnements sleutel nodig waarmee u toegang krijgt tot de resource. Een alles-in-één-sleutel geeft u de analyse van afbeeldingen, taal detectie, tekst omzetting en tekst analyse. Andere ingebouwde vaardig heden zijn functies van Azure Search en vereisen geen extra service of sleutel. Text shaper, splitter en fusie zijn voor beelden van helper-vaardig heden die soms nodig zijn bij het ontwerpen van de pijp lijn.

Als u alleen aangepaste vaardig heden en ingebouwde hulp programma-vaardig heden gebruikt, zijn er geen afhankelijkheden of kosten met betrekking tot Cognitive Services.

## <a name="order-of-operations"></a>Volg orde van bewerkingen

Nu hebben we Indexeer functies, extractie van inhoud en vaardig heden gedekt, kunnen we de pijplijn mechanismen en de volg orde van de bewerkingen nader bekijken.

Een vaardig heden is een samen stelling van een of meer vaardig heden. Wanneer er meerdere vaardig heden betrokken zijn, fungeert de vaardig heden als sequentiële pijp lijn, waardoor er afhankelijkheids grafieken worden geproduceerd waarbij de uitvoer van de ene vaardigheid wordt ingevoerd in een andere. 

Als er bijvoorbeeld sprake is van een grote blob van ongestructureerde tekst, kan een voorbeeld volgorde van bewerkingen voor tekst analyse er als volgt uitzien:

1. Gebruik tekst splitter om de BLOB te splitsen in kleinere delen.
1. Gebruik Taaldetectie om te bepalen of inhoud Engels of een andere taal is.
1. Gebruik Text Translator om alle tekst in een gemeen schappelijke taal op te halen.
1. Voer entiteits herkenning, Sleuteltermextractie of Sentimentanalyse uit op segment tekst. In deze stap worden nieuwe velden gemaakt en ingevuld. Entiteiten kunnen locatie, personen, organisatie, datums zijn. Sleutel zinnen zijn korte combi Naties van woorden die bij elkaar horen. Sentiment Score is een classificatie voor continuüm van negatieve (0) tot positieve (1) sentiment.
1. Gebruik tekst fusie om het document uit kleinere segmenten te maken.


## <a name="outputs-and-use-cases"></a>Uitvoer en use cases

Een verrijkt document aan het einde van de pijp lijn wijkt af van de oorspronkelijke invoer versie door de aanwezigheid van aanvullende velden die nieuwe informatie bevatten die tijdens de verrijking is geëxtraheerd of gegenereerd. Zo kunt u op verschillende manieren werken met een combi natie van originele en gemaakte waarden.

De uitvoer formulieren zijn een zoek index op Azure Search of een Knowledge Store in Azure Storage.

In Azure Search worden verrijkte documenten ingedeeld in JSON en kunnen ze op dezelfde manier worden geïndexeerd als alle documenten worden geïndexeerd, met de voor delen die een Indexeer functie biedt. Velden van verrijkte documenten worden toegewezen aan een index schema. Tijdens het indexeren verwijst de BLOB-Indexeer functie naar configuratie parameters en-instellingen om eventuele veld toewijzingen of de door u opgegeven detectie logica te gebruiken. Na het indexeren kunt u, wanneer inhoud wordt opgeslagen op Azure Search, uitgebreide query's bouwen en expressies filteren om inzicht te krijgen in de inhoud.

In Azure Storage heeft een kennis archief twee manifesten: een BLOB-container of tabellen in tabel opslag. Een BLOB-container legt verrijkte documenten in hun geheel vast. Dit is handig als u een feed wilt maken voor andere processen. Tabel opslag kan daarentegen de fysieke projecties van verrijkte documenten bevatten. U kunt segmenten of lagen maken met verrijkte documenten die specifieke onderdelen bevatten of uitsluiten. Voor analyse in Power BI worden de tabellen in azure Table Storage de gegevens bron geworden voor verdere visualisatie en onderzoek.

## <a name="next-steps"></a>Volgende stappen

U kunt veel meer doen met AI-verrijking om optimaal gebruik te maken van uw gegevens in Azure Storage, waaronder het combi neren van Cognitive Services op verschillende manieren, en het ontwerpen van aangepaste vaardig heden voor gevallen waarin u geen ervaring hebt met de cognitieve service voor het scenario. Meer informatie vindt u op de onderstaande koppelingen.

> [!div class="nextstepaction"]
> [Overzicht van AI-verrijking](cognitive-search-concept-intro.md) 
> [een vaardig heden maken](cognitive-search-defining-skillset.md)
> [kaart knooppunten in een aantekening structuur](cognitive-search-output-field-mapping.md)
