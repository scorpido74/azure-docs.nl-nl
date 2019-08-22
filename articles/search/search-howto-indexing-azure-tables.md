---
title: Inhoud indexeren vanuit Azure Table Storage voor zoeken in volledige tekst-Azure Search
description: Meer informatie over het indexeren van gegevens die zijn opgeslagen in azure Table Storage met een Azure Search indexer.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: dffb0a41dbf33cd86014115b089036d69a8e4718
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648191"
---
# <a name="index-azure-table-storage-with-azure-search"></a>Azure-tabel opslag indexeren met Azure Search
In dit artikel wordt beschreven hoe u Azure Search kunt gebruiken voor het indexeren van gegevens die zijn opgeslagen in azure-tabel opslag.

## <a name="set-up-azure-table-storage-indexing"></a>Indexering van Azure Table Storage instellen

U kunt een Azure Table Storage-indexer instellen met behulp van de volgende resources:

* [Azure-portal](https://ms.portal.azure.com)
* Azure Search [rest API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Search [.NET SDK](https://aka.ms/search-sdk)

Hier demonstreren we de stroom met behulp van de REST API. 

### <a name="step-1-create-a-datasource"></a>Stap 1: Een gegevensbron maken

In een gegevens bron wordt opgegeven welke gegevens moeten worden geïndexeerd, de referenties die nodig zijn voor toegang tot de gegevens en de beleids regels waarmee Azure Search efficiënt wijzigingen in de gegevens kunt identificeren.

Voor het indexeren van tabellen moet de gegevens bron de volgende eigenschappen hebben:

- **naam** is de unieke naam van de gegevens bron in uw zoek service.
- **type** moet zijn `azuretable`.
- de para meter **referenties** bevat het Connection String van het opslag account. Zie de sectie [referenties opgeven](#Credentials) voor meer informatie.
- **container** stelt de naam van de tabel en een optionele query in.
    - Geef de naam van de tabel op `name` met behulp van de para meter.
    - Geef eventueel een query op met behulp van `query` de para meter. 

> [!IMPORTANT] 
> Gebruik, indien mogelijk, een filter op PartitionKey voor betere prestaties. Elke andere query voert een volledige tabel scan uit, wat leidt tot slechte prestaties voor grote tabellen. Zie de sectie [prestatie overwegingen](#Performance) .


Een gegevens bron maken:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Zie [Create Data Source](https://docs.microsoft.com/rest/api/searchservice/create-data-source)voor meer informatie over de Create Data Source-API.

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Manieren om referenties op te geven ####

U kunt de referenties voor de tabel op een van de volgende manieren opgeven: 

- **Connection String voor volledige toegang tot opslag account**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`U kunt de Connection String ophalen via de Azure portal door naar de > Blade**instellingen** > **sleutels** van het **opslag account**te gaan (voor klassieke opslag accounts) of **instellingen** > **toegangs sleutels** (voor Azure Resource Manager Storage-accounts).
- **Connection String van de hand tekening voor gedeelde toegang voor opslag accounts**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl`De Shared Access-hand tekening moet de lijst en lees machtigingen hebben voor containers (tabellen in dit geval) en objecten (tabel rijen).
-  **Gedeelde toegangs handtekening**van de tabel: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r`De Shared Access-hand tekening moet query-(Lees) machtigingen hebben voor de tabel.

Zie [using Shared Access signatures](../storage/common/storage-dotnet-shared-access-signature-part-1.md)(Engelstalig) voor meer informatie over gedeelde toegangs handtekeningen voor opslag.

> [!NOTE]
> Als u referenties voor een Shared Access-hand tekening gebruikt, moet u de referenties van de gegevens bron regel matig bijwerken met de vernieuwde hand tekeningen om te voor komen dat ze verlopen. Als referenties voor de hand tekening van de gedeelde toegang verlopen, mislukt de Indexeer functie met een fout bericht dat lijkt op de referenties die in de connection string zijn gegeven, ongeldig of verlopen zijn.  

### <a name="step-2-create-an-index"></a>Stap 2: Een index maken
De index specificeert de velden in een document, de kenmerken en andere constructies die de zoek ervaring vormen.

Een index maken:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Zie [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over het maken van indexen.

### <a name="step-3-create-an-indexer"></a>Stap 3: Een indexeerfunctie maken
Een Indexeer functie verbindt een gegevens bron met een doel zoek index en biedt een schema om het vernieuwen van gegevens te automatiseren. 

Nadat de index en gegevens bron zijn gemaakt, bent u klaar om de Indexeer functie te maken:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Deze Indexeer functie wordt elke twee uur uitgevoerd. (Het interval van de planning is ingesteld op ' PT2H '.) Als u een Indexeer functie elke 30 minuten wilt uitvoeren, stelt u het interval in op ' PT30M '. Het kortste ondersteunde interval is vijf minuten. De planning is optioneel; Als u dit weglaat, wordt een Indexeer functie slechts eenmaal uitgevoerd wanneer deze wordt gemaakt. U kunt een Indexeer functie echter op elk gewenst moment uitvoeren.   

Zie [Indexeer functie maken](https://docs.microsoft.com/rest/api/searchservice/create-indexer)voor meer informatie over het maken van de API voor indexering.

Zie [Indexeer functies plannen voor Azure Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van Indexeer functie schema's.

## <a name="deal-with-different-field-names"></a>Omgaan met verschillende veld namen
Soms verschillen de veld namen in uw bestaande index van de eigenschaps namen in de tabel. U kunt veld toewijzingen gebruiken om de namen van eigenschappen van de tabel toe te wijzen aan de veld namen in uw zoek index. Zie voor meer informatie over veld toewijzingen [Azure Search Indexeer functie veld Toewijzingen Bridget de verschillen tussen gegevens bronnen en zoek indexen](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Document sleutels verwerken
In Azure Search is de document sleutel een unieke aanduiding van een document. Elke zoek index moet precies één sleutel veld van het type `Edm.String`bevatten. Het sleutel veld is vereist voor elk document dat wordt toegevoegd aan de index. (In feite is dit het enige vereiste veld.)

Omdat tabel rijen een samengestelde sleutel hebben, genereert Azure Search een synthetisch veld met `Key` de naam dat een samen voeging van partitie sleutel-en rijwaarden. `PK1` Als bijvoorbeeld de PartitionKey van een rij en RowKey is `RK1`, is `PK1RK1`de waarde van `Key` het veld.

> [!NOTE]
> De `Key` waarde kan tekens bevatten die ongeldig zijn in document sleutels, zoals streepjes. Met de `base64Encode` [functie voor veld toewijzing](search-indexer-field-mappings.md#base64EncodeFunction)kunt u met ongeldige tekens omgaan. Als u dit doet, moet u ook gebruikmaken van URL-veilige base64-code ring bij het door geven van document sleutels in API-aanroepen zoals lookup.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Incrementele indexering en detectie van verwijderingen
Wanneer u een tabel Indexeer functie zo instelt dat deze volgens een planning wordt uitgevoerd, worden alleen nieuwe of bijgewerkte rijen opnieuw geïndexeerd, zoals wordt bepaald door de `Timestamp` waarde van een rij. U hoeft geen beleid voor wijzigingen detectie op te geven. Incrementele indexering is automatisch ingeschakeld voor u.

Om aan te geven dat bepaalde documenten uit de index moeten worden verwijderd, kunt u een voorlopig verwijderings strategie gebruiken. In plaats van een rij te verwijderen, voegt u een eigenschap toe om aan te geven dat deze is verwijderd en stelt u een voorlopig detectie beleid voor het verwijderen van de gegevens bron in. Het volgende beleid is bijvoorbeeld van mening dat een rij wordt verwijderd als de rij een eigenschap `IsDeleted` heeft met de waarde: `"true"`

    PUT https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Prestatieoverwegingen

Azure Search maakt standaard gebruik van het volgende query filter: `Timestamp >= HighWaterMarkValue`. Omdat Azure-tabellen geen secundaire index op het `Timestamp` veld hebben, is voor dit type query een volledige tabel Scan vereist en is dit daarom langzaam voor grote tabellen.


Hier volgen twee mogelijke benaderingen voor het verbeteren van de prestaties van tabel indexen. Beide benaderingen zijn afhankelijk van het gebruik van tabel partities: 

- Als uw gegevens natuurlijk in verschillende partitielay-bereiken kunnen worden gepartitioneerd, maakt u een gegevens bron en een bijbehorende Indexeer functie voor elk partitie bereik. Elke Indexeer functie moet nu alleen een specifiek partitie bereik verwerken, wat resulteert in betere query prestaties. Als de gegevens die moeten worden geïndexeerd, een klein aantal vaste partities hebben, nog beter: elke Indexeer functie heeft alleen een partitie scan. Als u bijvoorbeeld een Data Source wilt maken voor het verwerken van een partitie bereik `000` met `100`sleutels van naar, gebruikt u een query als volgt: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Als uw gegevens op tijd zijn gepartitioneerd (bijvoorbeeld elke dag of week een nieuwe partitie maken), moet u rekening houden met de volgende benadering: 
    - Gebruik een query van het formulier: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Bewaak de voortgang van de Indexeer functie met behulp van de [API voor Indexeer functie ophalen](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)en werk de `<TimeStamp>` voor waarde van de query regel matig bij op basis van de meest recente waarde voor hoge water merken. 
    - Als u deze aanpak wilt activeren, moet u de data source-query opnieuw instellen als u de Indexeer functie opnieuw wilt instellen. 


## <a name="help-us-make-azure-search-better"></a>Help ons Azure Search beter te maken
Als u een functie verzoek of ideeën voor verbeteringen hebt, dient u deze in te dienen op onze [UserVoice-site](https://feedback.azure.com/forums/263029-azure-search/).
