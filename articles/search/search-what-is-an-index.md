---
title: Een indexdefinitie en -concepten maken
titleSuffix: Azure Cognitive Search
description: Inleiding tot indextermen en concepten in Azure Cognitive Search, inclusief onderdelen en fysieke structuur.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282781"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Een basisindex maken in Azure Cognitive Search

In Azure Cognitive Search is een *index* een permanente opslag van *documenten* en andere constructies die worden gebruikt voor gefilterd en full text zoeken op een Azure Cognitive Search-service. Conceptueel is een document één eenheid doorzoekbare gegevens in uw index. Een e-commercedetailhandel heeft bijvoorbeeld een document voor elk item dat wordt verkocht, een nieuwsbureau heeft een document voor elk artikel, enzovoort. Deze begrippen aan betrouwbaardere database-equivalenten toewijzen: een *index* lijkt conceptueel gezien op een *tabel* en *documenten* lijken ruwweg op *rijen* in een tabel.

Wanneer u een index toevoegt of uploadt, maakt Azure Cognitive Search fysieke structuren op basis van het schema dat u opgeeft. Als een veld in uw index bijvoorbeeld is gemarkeerd als doorzoekbaar, wordt voor dat veld een omgekeerde index gemaakt. Wanneer u later documenten toevoegt of uploadt of zoekopdrachten verzendt naar Azure Cognitive Search, verzendt u aanvragen naar een specifieke index in uw zoekservice. Laden van velden met documentwaarden wordt *indexering* of gegevensopname genoemd.

U een index maken in de portal, [REST API](search-create-index-rest-api.md)of [.NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Aanbevolen werkstroom

Het bereiken van het juiste indexontwerp wordt meestal bereikt door meerdere iteraties. Met behulp van een combinatie van tools en API's u uw ontwerp snel afronden.

1. Bepaal of u een [indexeerder](search-indexer-overview.md#supported-data-sources)gebruiken. Als uw externe gegevens een van de ondersteunde gegevensbronnen zijn, u een prototype en een index laden met de wizard [**Gegevens importeren.**](search-import-data-portal.md)

2. Als u **Importgegevens**niet gebruiken, u nog steeds [een eerste index maken in de portal,](search-create-index-portal.md)velden, gegevenstypen toevoegen en kenmerken toewijzen met besturingselementen op de pagina Index **toevoegen.** De portal toont u welke kenmerken beschikbaar zijn voor verschillende gegevenstypen. Als u nieuw bent in indexontwerp, is dit handig.

   ![Indexpagina toevoegen met kenmerken op gegevenstype](media/search-create-index-portal/field-attributes.png "Indexpagina toevoegen met kenmerken op gegevenstype")
  
   Wanneer u op **Maken**klikt, worden alle fysieke structuren die uw index ondersteunen, gemaakt in uw zoekservice.

3. Download het indexschema met [Get Index REST API](https://docs.microsoft.com/rest/api/searchservice/get-index) en een webtesttool zoals [Postman](search-get-started-postman.md). U hebt nu een JSON-weergave van de index die u in de portal hebt gemaakt. 

   U schakelt op dit moment over naar een codegebaseerde aanpak. De portal is niet geschikt voor iteratie omdat u een reeds gemaakte index niet bewerken. Maar u Postman en REST gebruiken voor de overige taken.

4. [Laad uw index met gegevens](search-what-is-data-import.md). Azure Cognitive Search accepteert JSON-documenten. Om uw gegevens programmatisch te laden, u Postman gebruiken met JSON-documenten in de aanvraagpayload. Als uw gegevens niet gemakkelijk worden uitgedrukt als JSON, zal deze stap de meest arbeidsintensieve zijn.

5. Bevraag uw index, bestudeer de resultaten en veroordeel verder op het indexschema totdat u de verwachte resultaten begint te zien. U [**Zoekverkenner**](search-explorer.md) of Postbode gebruiken om uw index op te vragen.

6. Blijf code gebruiken om uw ontwerp te herhalen.  

Omdat fysieke structuren in de service worden gemaakt, is het verwijderen [en opnieuw maken van indexen](search-howto-reindex.md) noodzakelijk wanneer u wezenlijke wijzigingen aanbrengt in een bestaande velddefinitie. Dit betekent dat tijdens de ontwikkeling, moet u van plan op frequente herbouwt. U overwegen om met een subset van uw gegevens te werken om de herbouw sneller te laten verlopen. 

Code, in plaats van een portal benadering, wordt aanbevolen voor iteratief ontwerp. Als u vertrouwt op de portal voor indexdefinitie, moet u de indexdefinitie op elke herbouw invullen. Als alternatief zijn tools zoals [Postman en de REST API](search-get-started-postman.md) handig voor proof-of-concept testen wanneer ontwikkelingsprojecten zich nog in een vroeg stadium bevinden. U incrementele wijzigingen aanbrengen in een indexdefinitie in een aanvraaginstantie en vervolgens het verzoek naar uw service verzenden om een index opnieuw te maken met behulp van een bijgewerkt schema.

## <a name="components-of-an-index"></a>Componenten van een index

Schematisch bestaat een Azure Cognitive Search-index uit de volgende elementen. 

De [*veldenverzameling*](#fields-collection) is doorgaans het grootste deel van een index, waarbij elk veld wordt benoemd, getypt en toegeschreven met toegestaan gedrag dat bepaalt hoe het wordt gebruikt. Andere elementen zijn [suggesters,](#suggesters) [scoreprofielen,](#scoring-profiles) [analysators](#analyzers) met onderdelen ter ondersteuning van aanpassingsmogelijkheden, [CORS](#cors) en [encryptiesleutelopties.](#encryption-key)

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Verzamelings- en veldkenmerken velden

Bij het definiëren van het schema moet u de naam, het type en de kenmerken van elk veld in de index opgeven. Het veldtype classificeert de gegevens die in dat veld worden opgeslagen. Kenmerken worden ingesteld op afzonderlijke velden om op te geven hoe het veld wordt gebruikt. De volgende tabellen bevatten de typen en kenmerken die u kunt opgeven.

### <a name="data-types"></a>Gegevenstypen
| Type | Beschrijving |
| --- | --- |
| *Edm.String* |Tekst die optioneel kan worden getokeniseerd voor full-text search (woordbrekend, stemming, enzovoort). |
| *Collection(EDM.String)* |Een lijst met tekenreeksen die van tokens kan worden voorzien om te zoeken in de volledige tekst. Er is geen theoretische bovengrens voor het aantal items in een verzameling, maar de bovengrens van 16 MB voor de nettolading geldt voor alle verzamelingen. |
| *Edm.Boolean* |Bevat de waarden waar/niet waar. |
| *Edm.Int32* |32-bits waarden van een heel getal. |
| *Edm.Int64* |64-bits waarden van een heel getal. |
| *Edm.Double* |Numerieke gegevens met dubbele precisie. |
| *Edm.DateTimeOffset* |Datumtijdwaarden die worden weergegeven in de notatie `yyyy-MM-ddTHH:mm:ss.fffZ` `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`OData V4 (bijvoorbeeld of ). |
| *Edm.GeographyPoint* |Een punt voor een geografische locatie op de wereld. |

Meer gedetailleerde informatie over de ondersteunde [gegevenstypen](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)van Azure Cognitive Search vindt u hier.

### <a name="index-attributes"></a>Indexkenmerken

Precies één veld in uw index moet het aangewezen zijn als een **sleutelveld** dat elk document op unieke wijze identificeert.

Andere kenmerken bepalen hoe een veld wordt gebruikt in een toepassing. Het **doorzoekbare** kenmerk wordt bijvoorbeeld toegewezen aan elk veld dat moet worden opgenomen in een zoekopdracht met volledige tekst. 

De API's die u gebruikt om een index op te bouwen, hebben verschillende standaardgedragingen. Voor de [REST-API's](https://docs.microsoft.com/rest/api/searchservice/Create-Index)zijn de meeste kenmerken standaard ingeschakeld (bijvoorbeeld **doorzoekbaar** en **opvraagbaar** zijn voor tekenreeksvelden) en hoeft u ze vaak alleen in te stellen als u ze wilt uitschakelen. Voor de .NET SDK is het tegenovergestelde waar. Op een eigenschap die u niet expliciet instelt, is de standaardinstelling om het bijbehorende zoekgedrag uit te schakelen, tenzij u dit specifiek inschakelt.

| Kenmerk | Beschrijving |
| --- | --- |
| `key` |Een tekenreeks met de unieke id van elk document. Deze reeks wordt gebruikt om op te zoeken. Elke index moet een sleutel hebben. Slechts één veld kan de sleutel zijn en het type moet zijn ingesteld op Edm.String. |
| `retrievable` |Hiermee geeft u op of een veld in een zoekresultaat kan worden geretourneerd. |
| `filterable` |Hiermee kan het veld in een filterquery's worden gebruikt. |
| `Sortable` |Hiermee kan een query de zoekresultaten sorteren op basis van dit veld. |
| `facetable` |Hiermee kunt u een veld gebruiken in een [meervoudige navigatie](search-faceted-navigation.md)structuur om op de gebruiker te filteren. Doorgaans werken velden met terugkerende waarden die u kunt gebruiken om meerdere documenten te groeperen (bijvoorbeeld meerdere documenten die in een bepaalde merk- of servicecategorie vallen) het beste als facetten. |
| `searchable` |Hiermee kunt u in dit veld in de volledige tekst zoeken. |

## <a name="index-size"></a>Indexgrootte

De grootte van een index wordt bepaald door de grootte van de documenten die u uploadt, plus indexconfiguratie, zoals of u suggesties opneemt en hoe u kenmerken instelt op afzonderlijke velden. De volgende schermafbeelding illustreert indexopslagpatronen die voortvloeien uit verschillende combinaties van kenmerken.

De index is gebaseerd op de [ingebouwde gegevensbron voor het vastgoedvoorbeeld,](search-get-started-portal.md) die u indexeren en opvragen in de portal. Hoewel de indexschema's niet worden weergegeven, u de kenmerken afleiden op basis van de indexnaam. Bijvoorbeeld, *onroerend goed doorzoekbare* index heeft de **doorzoekbare** attribuut geselecteerd en niets anders, *onroerend goed opvraagbare* index heeft de **opvraagbare** attribuut geselecteerd en niets anders, enzovoort.

![Indexgrootte op basis van kenmerkselectie](./media/search-what-is-an-index/realestate-index-size.png "Indexgrootte op basis van kenmerkselectie")

Hoewel deze indexvarianten kunstmatig zijn, kunnen we ernaar verwijzen voor brede vergelijkingen van hoe kenmerken de opslag beïnvloeden. Vergroot het instellen **van de** indexgrootte? Nee. Vergroot het toevoegen van velden aan een **Suggester** de indexgrootte? Ja.

Indexen die filter en sort ondersteunen zijn proportioneel groter dan de indexen die alleen zoeken in volledige tekst ondersteunen. Filter en sorteer bewerkingen scannen op exacte overeenkomsten, waarbij de aanwezigheid van intacte documenten vereist is. Daarentegen worden doorzoekbare velden ondersteund die omgekeerde indexen met volledige tekst en fuzzy search ondersteunen, die worden gevuld met tokenized termen die minder ruimte inbeslagnemen dan hele documenten. 

> [!Note]
> Opslagarchitectuur wordt beschouwd als een implementatiedetail van Azure Cognitive Search en kan zonder kennisgeving worden gewijzigd. Er is geen garantie dat het huidige gedrag zal blijven bestaan in de toekomst.

## <a name="suggesters"></a>Suggesties
Een suggestieis een gedeelte van het schema dat bepaalt welke velden in een index worden gebruikt om automatisch aanvullende of vooraf-query's in zoekopdrachten te ondersteunen. Gedeeltelijke zoektekenreeksen worden meestal verzonden naar de [REST API (Suggestions)](https://docs.microsoft.com/rest/api/searchservice/suggestions) terwijl de gebruiker een zoekopdracht typt en de API retourneert een set voorgestelde documenten of woordgroepen. 

Velden die aan een suggestiezijn toegevoegd, worden gebruikt om zoektermen vooraf te bouwen. Alle zoektermen worden gemaakt tijdens het indexeren en apart opgeslagen. Zie [Suggesties toevoegen voor](index-add-suggesters.md)meer informatie over het maken van een suggestiestructuur.

## <a name="scoring-profiles"></a>Scoreprofielen

Een [scoreprofiel](index-add-scoring-profiles.md) is een gedeelte van het schema dat aangepast scoregedrag definieert waarmee u invloed uitoefenen op welke items hoger in de zoekresultaten worden weergegeven. Scoreprofielen bestaan uit veldgewichten en functies. Als u ze wilt gebruiken, geeft u een profiel op naam op de querytekenreeks.

Achter de schermen wordt een standaard score profiel gebruikt om een zoekscore te berekenen voor elk item in een resultaatset. U het interne, naamloze scoreprofiel gebruiken. U ook **standaardScoreProfile** instellen om een aangepast profiel te gebruiken als standaardprofiel, aangeroepen wanneer een aangepast profiel niet is opgegeven in de querytekenreeks.

## <a name="analyzers"></a>Analyses

In het element analysers wordt de naam van de taalanalyzer ingesteld die voor het veld moet worden gebruikt. Zie [Analysers toevoegen aan een Azure Cognitive Search-index](search-analyzers.md)voor meer informatie over het bereik van analysers dat voor u beschikbaar is. Analysers kunnen alleen worden gebruikt met doorzoekbare velden. Zodra de analyzer is toegewezen aan een veld, kan deze niet worden gewijzigd, tenzij u de index opnieuw opbouwt.

## <a name="cors"></a>CORS

JavaScript aan clientzijde kan standaard geen API's aanroepen, omdat de browser alle cross-origine aanvragen voorkomt. Als u kruisoorsprongsquery's wilt toestaan in uw index, schakelt u CORS (Cross-Origin Resource Sharing) in door het kenmerk **corsOptions** in te stellen. Om veiligheidsredenen ondersteunen alleen query-API's CORS. 

De volgende opties kunnen worden ingesteld voor CORS:

+ **allowedOrigins** (vereist): Dit is een lijst met oorsprongdie toegang krijgt tot uw index. Dit betekent dat elke JavaScript-code die van die oorsprong wordt geserveerd, uw index mag opvragen (ervan uitgaande dat deze de juiste api-sleutel biedt). Elke oorsprong is typisch `protocol://<fully-qualified-domain-name>:<port>` van `<port>` de vorm hoewel vaak wordt weggelaten. Zie [Cross-origin resource sharing (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) voor meer informatie.

  Als je toegang wilt geven tot `*` alle oorsprong, neem dan op als één item in de **toegestane Origins-array.** *Dit wordt niet aanbevolen praktijk voor de productie zoekdiensten,* maar het is vaak nuttig voor de ontwikkeling en debugging.

+ **maxAgeInSeconds** (optioneel): Browsers gebruiken deze waarde om de duur (in seconden) te bepalen om CORS preflight-antwoorden in de cache op te slaan. Dit moet een niet-negatief geheel getal zijn. Hoe groter deze waarde is, hoe beter de prestaties zullen zijn, maar hoe langer het duurt voordat cors-beleidswijzigingen van kracht worden. Als deze niet is ingesteld, wordt een standaardduur van 5 minuten gebruikt.

## <a name="encryption-key"></a>Coderingssleutel

Hoewel alle Azure Cognitive Search-indexen standaard worden versleuteld met door Microsoft beheerde sleutels, kunnen indexen worden geconfigureerd om te worden versleuteld met **door de klant beheerde sleutels** in Key Vault. Zie [Versleutelingssleutels beheren in Azure Cognitive Search](search-security-manage-encryption-keys.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Met inzicht in de indexsamenstelling u verder gaan in de portal om uw eerste index te maken.

> [!div class="nextstepaction"]
> [Een index toevoegen (portal)](search-create-index-portal.md)
