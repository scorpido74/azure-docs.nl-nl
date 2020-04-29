---
title: Een index definitie en concepten maken
titleSuffix: Azure Cognitive Search
description: Inleiding tot index termen en concepten in azure Cognitive Search, inclusief onderdeel onderdelen en fysieke structuur.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282781"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Een Basic-index maken in azure Cognitive Search

In azure Cognitive Search is een *index* een permanente opslag van *documenten* en andere constructies die worden gebruikt voor gefilterde en zoek opdrachten in de volledige tekst van een Azure Cognitive Search-service. Een document is conceptueel gezien een enkele eenheid van Doorzoek bare gegevens in uw index. Een e-commercedetailhandel heeft bijvoorbeeld een document voor elk item dat wordt verkocht, een nieuwsbureau heeft een document voor elk artikel, enzovoort. Deze begrippen aan betrouwbaardere database-equivalenten toewijzen: een *index* lijkt conceptueel gezien op een *tabel* en *documenten* lijken ruwweg op *rijen* in een tabel.

Wanneer u een index toevoegt of uploadt, worden in azure Cognitive Search fysieke structuren gemaakt op basis van het schema dat u opgeeft. Als een veld in uw index bijvoorbeeld als doorzoekbaar is gemarkeerd, wordt een omgekeerde index voor dat veld gemaakt. Wanneer u later documenten toevoegt of uploadt of zoek query's naar Azure Cognitive Search verzendt, verzendt u aanvragen naar een specifieke index in uw zoek service. Het laden van velden met document waarden wordt *indexering* of gegevens opname genoemd.

U kunt een index maken in de portal, [rest API](search-create-index-rest-api.md)of [.NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Aanbevolen werk stroom

Het bereiken van het juiste index ontwerp wordt doorgaans bereikt via meerdere iteraties. Met een combi natie van hulpprogram ma's en Api's kunt u uw ontwerp snel volt ooien.

1. Bepaal of u een [Indexeer functie](search-indexer-overview.md#supported-data-sources)kunt gebruiken. Als uw externe gegevens een van de ondersteunde gegevens bronnen zijn, kunt u een index maken en laden met behulp van de wizard [**gegevens importeren**](search-import-data-portal.md) .

2. Als u geen **import gegevens**kunt gebruiken, kunt u nog steeds [een initiële index maken in de portal](search-create-index-portal.md), velden en gegevens typen toevoegen en kenmerken toewijzen met behulp van de besturings elementen op de pagina **index toevoegen** . In de portal ziet u welke kenmerken beschikbaar zijn voor verschillende gegevens typen. Als u niet bekend bent met het ontwerp van de index, is dit nuttig.

   ![Index pagina met kenmerken op gegevens type toevoegen](media/search-create-index-portal/field-attributes.png "Index pagina met kenmerken op gegevens type toevoegen")
  
   Wanneer u op **maken**klikt, worden alle fysieke structuren die uw index ondersteunen, in uw zoek service gemaakt.

3. Down load het index schema met [Get index rest API](https://docs.microsoft.com/rest/api/searchservice/get-index) en een hulp programma voor het testen van webtoepassingen zoals in het [bericht](search-get-started-postman.md). U hebt nu een JSON-weer gave van de index die u in de portal hebt gemaakt. 

   U schakelt op dit punt over op code gebaseerde aanpak. De portal is niet geschikt voor herhaling omdat u geen index kunt bewerken die al is gemaakt. U kunt postman gebruiken en REST voor de resterende taken.

4. [Laad uw index met gegevens](search-what-is-data-import.md). Azure Cognitive Search accepteert JSON-documenten. Als u gegevens wilt laden via een programma, kunt u postman gebruiken met JSON-documenten in de aanvraag lading. Als uw gegevens niet eenvoudig kunnen worden uitgedrukt als JSON, is deze stap het meest arbeids intensief.

5. Zoek uw index op, Bekijk de resultaten en herhaal verder naar het index schema totdat u begint met de resultaten die u verwacht. U kunt [**Search Explorer**](search-explorer.md) of postman gebruiken om de index op te vragen.

6. Ga door met het gebruik van code om het ontwerp te herhalen.  

Omdat er fysieke structuren in de service zijn gemaakt, worden de [indexen verwijderd en opnieuw gemaakt](search-howto-reindex.md) wanneer u belang rijke wijzigingen doorvoert in een bestaande veld definitie. Dit betekent dat u tijdens de ontwikkeling regel matig opnieuw bouwt. U kunt ook met een subset van uw gegevens werken om het opnieuw opbouwen sneller te laten verlopen. 

Code, in plaats van een portal-benadering, wordt aanbevolen voor iteratief ontwerp. Als u vertrouwt op de portal voor de index definitie, moet u de index definitie invullen voor elke opnieuw samen stellen. Als alternatief kunt u hulpprogram ma's zoals [postman en de rest API](search-get-started-postman.md) nuttig vinden bij het testen van het haalbaarheids test wanneer ontwikkel projecten nog steeds in vroege fasen zijn. U kunt incrementele wijzigingen aanbrengen in een index definitie in een aanvraag tekst en vervolgens de aanvraag verzenden naar uw service om een index opnieuw te maken met behulp van een bijgewerkt schema.

## <a name="components-of-an-index"></a>Onderdelen van een index

Schematisch, een Azure Cognitive Search index bestaat uit de volgende elementen. 

De [*verzameling velden*](#fields-collection) is doorgaans het grootste deel van een index, waarbij elk veld een naam heeft, wordt getypt en is voorzien van een toegestaan gedrag dat bepaalt hoe het wordt gebruikt. Andere elementen omvatten [suggesties](#suggesters), [Score profielen](#scoring-profiles), [analyse](#analyzers) functies met onderdeel onderdelen ter ondersteuning van aanpassings-, [CORS](#cors) -en [versleutelings sleutel](#encryption-key) opties.

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

## <a name="fields-collection-and-field-attributes"></a>Velden verzameling en veld kenmerken

Bij het definiëren van het schema moet u de naam, het type en de kenmerken van elk veld in de index opgeven. Het veldtype classificeert de gegevens die in dat veld worden opgeslagen. Kenmerken worden ingesteld op afzonderlijke velden om op te geven hoe het veld wordt gebruikt. De volgende tabellen bevatten de typen en kenmerken die u kunt opgeven.

### <a name="data-types"></a>Gegevenstypen
| Type | Beschrijving |
| --- | --- |
| *Edm.String* |Tekst die eventueel kan worden getokend voor Zoek opdrachten in volledige tekst (woord afbreking, stam bestand, enzovoort). |
| *Collection(EDM.String)* |Een lijst met tekenreeksen die van tokens kan worden voorzien om te zoeken in de volledige tekst. Er is geen theoretische bovengrens voor het aantal items in een verzameling, maar de bovengrens van 16 MB voor de nettolading geldt voor alle verzamelingen. |
| *Edm.Boolean* |Bevat de waarden waar/niet waar. |
| *Edm.Int32* |32-bits waarden van een heel getal. |
| *Edm.Int64* |64-bits waarden van een heel getal. |
| *Edm.Double* |Numerieke gegevens met dubbele precisie. |
| *Edm.DateTimeOffset* |Datum en tijd waarden die worden weer gegeven in de OData v4- `yyyy-MM-ddTHH:mm:ss.fffZ` indeling `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`(bijvoorbeeld of). |
| *Edm.GeographyPoint* |Een punt voor een geografische locatie op de wereld. |

Hier vindt u meer gedetailleerde informatie over de [ondersteunde gegevens typen](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)van Azure Cognitive Search.

### <a name="index-attributes"></a>Indexkenmerken

Er moet precies één veld in de index zijn opgegeven als een **sleutel** veld dat elk document uniek identificeert.

Andere kenmerken bepalen hoe een veld in een toepassing wordt gebruikt. Het kenmerk **searchable** wordt bijvoorbeeld toegewezen aan elk veld dat moet worden opgenomen in een zoek opdracht in volledige tekst. 

De Api's die u gebruikt om een index te maken, hebben verschillende standaard gedragingen. Voor de [rest-api's](https://docs.microsoft.com/rest/api/searchservice/Create-Index)zijn de meeste kenmerken standaard ingeschakeld (bijvoorbeeld **Doorzoek** bare en **opgehaalde** waarden zijn waar voor teken reeks velden). u hoeft ze vaak alleen in te stellen als u ze wilt uitschakelen. Voor de .NET SDK is het tegenovergestelde waar. Op elke eigenschap die u niet expliciet instelt, is de standaard instelling om het bijbehorende Zoek gedrag uit te scha kelen, tenzij u dit specifiek inschakelt.

| Kenmerk | Beschrijving |
| --- | --- |
| `key` |Een tekenreeks met de unieke id van elk document. Deze reeks wordt gebruikt om op te zoeken. Elke index moet een sleutel hebben. Slechts één veld kan de sleutel zijn en het type moet zijn ingesteld op Edm.String. |
| `retrievable` |Hiermee geeft u op of een veld in een zoekresultaat kan worden geretourneerd. |
| `filterable` |Hiermee kan het veld in een filterquery's worden gebruikt. |
| `Sortable` |Hiermee kan een query de zoekresultaten sorteren op basis van dit veld. |
| `facetable` |Hiermee kunt u een veld gebruiken in een [meervoudige navigatie](search-faceted-navigation.md)structuur om op de gebruiker te filteren. Doorgaans werken velden met terugkerende waarden die u kunt gebruiken om meerdere documenten te groeperen (bijvoorbeeld meerdere documenten die in een bepaalde merk- of servicecategorie vallen) het beste als facetten. |
| `searchable` |Hiermee kunt u in dit veld in de volledige tekst zoeken. |

## <a name="index-size"></a>Index grootte

De grootte van een index wordt bepaald door de grootte van de documenten die u uploadt, plus de index configuratie, zoals of u Voorst Ellen opneemt en hoe u kenmerken instelt voor afzonderlijke velden. In de volgende scherm afbeelding ziet u de index opslag patronen die voortkomen uit verschillende combi Naties van kenmerken.

De index is gebaseerd op de [ingebouwde voorbeeld](search-get-started-portal.md) gegevens bron van onroerend goed, die u in de portal kunt indexeren en doorzoeken. Hoewel de index schema's niet worden weer gegeven, kunt u de kenmerken afleiden op basis van de naam van de index. *Realestate-Doorzoek bare* index heeft bijvoorbeeld het kenmerk dat kan worden **doorzocht** en niets anders, het ophalen van *realestate-* index heeft het kenmerk **retrievable** selected en niets anders, enzovoort.

![Index grootte op basis van kenmerk selectie](./media/search-what-is-an-index/realestate-index-size.png "Index grootte op basis van kenmerk selectie")

Hoewel deze index varianten kunst matig zijn, kunnen we ernaar verwijzen naar een uitgebreidere vergelijking van de manier waarop kenmerken van opslag worden beïnvloed. Kan de indexerings grootte die kan worden **opgehaald** , worden ingesteld? Nee. Voegt velden toe aan een **suggestie** voor het verg Roten van index grootte? Ja.

Indexen die ondersteuning bieden voor filteren en sorteren, zijn proportioneel groter dan het ondersteunen van de volledige zoek opdracht voor tekst. Filter-en sorteer bewerkingen scannen op exacte overeenkomsten, waardoor de aanwezigheid van intacte documenten vereist is. In tegens telling tot Doorzoek bare velden die ondersteuning bieden voor volledige tekst en fuzzy Search, worden omgekeerde indexen gebruikt. deze worden gevuld met tokens met een sleutel die minder ruimte in beslag neemt dan hele documenten. 

> [!Note]
> Opslag architectuur wordt beschouwd als een implementatie details van Azure Cognitive Search en kan zonder kennisgeving worden gewijzigd. Er is geen garantie dat het huidige gedrag in de toekomst blijft behouden.

## <a name="suggesters"></a>Suggesties
Een suggestie is een sectie van het schema waarmee wordt gedefinieerd welke velden in een index worden gebruikt voor het ondersteunen van automatisch aanvullen of het type-ahead query's in Zoek opdrachten. Normaal gesp roken worden gedeeltelijke Zoek reeksen naar de [suggesties (rest API)](https://docs.microsoft.com/rest/api/searchservice/suggestions) verzonden terwijl de gebruiker een zoek opdracht typt. de API retourneert een aantal voorgestelde documenten of zinsdelen. 

Velden die zijn toegevoegd aan een suggestie, worden gebruikt voor het bouwen van type-ahead zoek termen. Alle zoek termen worden tijdens het indexeren gemaakt en afzonderlijk opgeslagen. Zie Voorst [Ellen toevoegen](index-add-suggesters.md)voor meer informatie over het maken van een boom structuur.

## <a name="scoring-profiles"></a>Scoreprofielen

Een [Score profiel](index-add-scoring-profiles.md) is een sectie van het schema waarmee aangepaste Score gedragingen worden gedefinieerd waarmee u kunt beïnvloeden welke items hoger worden weer gegeven in de zoek resultaten. Score profielen bestaan uit veld gewichten en-functies. Als u deze wilt gebruiken, geeft u een profiel op naam op voor de query teken reeks.

Een standaard Score profiel werkt achter de schermen om een zoek score te berekenen voor elk item in een resultatenset. U kunt het interne, niet-genaamde Score profiel gebruiken. U kunt ook **defaultScoringProfile** instellen om een aangepast profiel te gebruiken als de standaard instelling, wanneer er geen aangepast profiel is opgegeven in de query reeks.

## <a name="analyzers"></a>Analyses

Het element analyse functies stelt de naam in van de taal analyse die moet worden gebruikt voor het veld. Zie [analyse functies toevoegen aan een Azure Cognitive search-index](search-analyzers.md)voor meer informatie over het aanbod dat voor u beschikbaar is. Analyseerers kunnen alleen worden gebruikt met Doorzoek bare velden. Zodra de analyse functie is toegewezen aan een veld, kan deze niet meer worden gewijzigd, tenzij u de index opnieuw opbouwt.

## <a name="cors"></a>CORS

Java script aan de client zijde kan standaard geen Api's aanroepen omdat de browser alle cross-Origin-aanvragen voor komt. Als u cross-Origin query's naar uw index wilt toestaan, schakelt u CORS (cross-Origin Resource Sharing) in door het kenmerk **corsOptions** in te stellen. Uit veiligheids overwegingen wordt alleen voor de query-Api's CORS ondersteund. 

De volgende opties kunnen worden ingesteld voor CORS:

+ **allowedOrigins** (vereist): dit is een lijst met oorsprongen waaraan toegang tot uw index wordt verleend. Dit betekent dat elke Java script-code die door deze oorsprongen wordt bediend, een query kan uitvoeren op uw index (ervan uitgaande dat deze de juiste API-sleutel bevat). Elke oorsprong is doorgaans van het formulier `protocol://<fully-qualified-domain-name>:<port>` Hoewel `<port>` deze vaak wordt wegge laten. Zie [Cross-Origin Resource Sharing (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) voor meer informatie.

  Als u toegang tot alle oorsprongen wilt toestaan, neemt `*` u op als één item in de **allowedOrigins** -matrix. *Dit wordt niet aanbevolen voor productie Zoek Services* , maar het is vaak handig voor het ontwikkelen en opsporen van fouten.

+ **maxAgeInSeconds** (optioneel): browsers gebruiken deze waarde om de duur (in seconden) te bepalen voor het cachen van CORS-Preflight-reacties. Dit moet een niet-negatief geheel getal zijn. Hoe groter deze waarde is, hoe beter de prestaties, maar hoe langer het duurt om de wijzigingen van het CORS-beleid van kracht te laten worden. Als deze niet is ingesteld, wordt een standaard duur van 5 minuten gebruikt.

## <a name="encryption-key"></a>Versleutelings sleutel

Hoewel alle Azure Cognitive Search-indexen standaard worden versleuteld met door micro soft beheerde sleutels, kunnen indexen worden geconfigureerd om te worden versleuteld met door de **klant beheerde sleutels** in Key Vault. Zie [versleutelings sleutels beheren in Azure Cognitive Search](search-security-manage-encryption-keys.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Met een goed idee van index samenstelling kunt u door gaan in de portal om uw eerste index te maken.

> [!div class="nextstepaction"]
> [Een index toevoegen (Portal)](search-create-index-portal.md)
