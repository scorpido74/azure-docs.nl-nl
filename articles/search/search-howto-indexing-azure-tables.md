---
title: Zoeken via azure table-opslaginhoud
titleSuffix: Azure Cognitive Search
description: Meer informatie over het indexeren van gegevens die zijn opgeslagen in Azure Table-opslag met een Azure Cognitive Search-indexeerder.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8f6c0454497b1cb1d62417e566e9662469c56d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112994"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Tabellen indexeren vanuit Azure Table-opslag met Azure Cognitive Search

In dit artikel ziet u hoe u Azure Cognitive Search gebruiken om gegevens te indexeren die zijn opgeslagen in Azure Table-opslag.

## <a name="set-up-azure-table-storage-indexing"></a>Azure Table-opslagindexering instellen

U een Azure Table-opslagindexeerder instellen met behulp van de volgende bronnen:

* [Azure-portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](https://aka.ms/search-sdk)

Hier tonen we de flow met behulp van de REST API. 

### <a name="step-1-create-a-datasource"></a>Stap 1: Een gegevensbron maken

Een gegevensbron geeft aan welke gegevens moeten worden geïndexeerd, welke referenties nodig zijn om toegang te krijgen tot de gegevens en het beleid waarmee Azure Cognitive Search wijzigingen in de gegevens efficiënt kan identificeren.

Voor tabelindexering moet de gegevensbron de volgende eigenschappen hebben:

- **naam** is de unieke naam van de gegevensbron binnen uw zoekservice.
- **type** moet `azuretable`zijn .
- parameter **credentials** bevat de tekenreeks voor de verbindingsreeks voor opslagaccount. Zie de sectie [Referenties opgeven](#Credentials) voor meer informatie.
- **container** stelt de tabelnaam en een optionele query in.
    - Geef de tabelnaam `name` op met behulp van de parameter.
    - Geef destijds een `query` query op met behulp van de parameter. 

> [!IMPORTANT] 
> Gebruik waar mogelijk een filter op PartitionKey voor betere prestaties. Elke andere query doet een volledige tabelscan, wat resulteert in slechte prestaties voor grote tabellen. Zie de sectie [Prestatieoverwegingen.](#Performance)


Ga als lid van het nieuws:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Zie [Gegevensbron maken](https://docs.microsoft.com/rest/api/searchservice/create-data-source)voor meer informatie over de API Gegevensbron maken.

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Manieren om referenties op te geven ####

U de referenties voor de tabel op een van de volgende manieren verstrekken: 

- **Tekenreeks voor opslagaccountverbinding** `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` met volledige toegang : u de verbindingstekenreeks van de Azure-portal ophalen door naar de**instellingensleutels** **voor het opslagaccountblad** > **(voor** > klassieke opslagaccounts) of**Instellingentoegangssleutels** **Settings** > (voor opslagaccounts van Azure Resource Manager) te gaan.
- **Tekenreeks voor gedeelde toegang voor gedeelde toegang:** `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` de handtekening voor gedeelde toegang moet de lijst- en leesmachtigingen voor containers (tabellen in dit geval) en objecten (tabelrijen) hebben.
-  **Handtekening voor**gedeelde `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` toegang tabel : de handtekening voor gedeelde toegang moet querymachtigingen (lees)in de tabel hebben.

Zie Handtekeningen [voor gedeelde toegang gebruiken](../storage/common/storage-dotnet-shared-access-signature-part-1.md)voor meer informatie over gedeelde toegangshandtekeningen voor opslag.

> [!NOTE]
> Als u handtekeningreferenties voor gedeelde toegang gebruikt, moet u de gegevensbronreferenties periodiek bijwerken met vernieuwde handtekeningen om te voorkomen dat ze verlopen. Als de referenties van gedeelde toegangshandtekeningen verlopen, mislukt de indexer met een foutbericht dat vergelijkbaar is met 'Referenties in de verbindingstekenreeks zijn ongeldig of verlopen'.  

### <a name="step-2-create-an-index"></a>Stap 2: een index maken
De index geeft de velden in een document, de kenmerken en andere constructies op die de zoekervaring vormen.

Ga als u een index maken:

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

Zie [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over het maken van indexen.

### <a name="step-3-create-an-indexer"></a>Stap 3: Een indexer maken
Een indexer verbindt een gegevensbron met een doelzoekindex en biedt een planning om de gegevensvernieuwing te automatiseren. 

Nadat de index en gegevensbron zijn gemaakt, bent u klaar om de indexer te maken:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Deze indexer draait elke twee uur. (Het schemainterval is ingesteld op "PT2H".) Als u elke 30 minuten een indexer wilt uitvoeren, stelt u het interval in op "PT30M". Het kortste ondersteunde interval is vijf minuten. Het schema is optioneel; als deze wordt weggelaten, wordt een indexer slechts één keer uitgevoerd wanneer deze is gemaakt. U echter op elk gewenst moment een indexer op aanvraag uitvoeren.   

Zie Indexer maken voor meer informatie over de API [Indexer maken.](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Zie [Indexeerders voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van indexerschema's.

## <a name="deal-with-different-field-names"></a>Omgaan met verschillende veldnamen
Soms verschillen de veldnamen in uw bestaande index van de eigenschapsnamen in uw tabel. U veldtoewijzingen gebruiken om de eigenschapsnamen van de tabel in kaart te brengen van de veldnamen in uw zoekindex. Zie [Azure Cognitive Search-indexerveldtoewijzingen overbrug de verschillen tussen gegevensbronnen en zoekindexen](search-indexer-field-mappings.md)voor meer informatie over veldtoewijzingen.

## <a name="handle-document-keys"></a>Documentsleutels verwerken
In Azure Cognitive Search identificeert de documentsleutel op unieke wijze een document. Elke zoekindex moet precies één `Edm.String`belangrijk veld van het type hebben. Het sleutelveld is vereist voor elk document dat aan de index wordt toegevoegd. (In feite is het het enige vereiste veld.)

Omdat tabelrijen een samengestelde sleutel hebben, genereert `Key` Azure Cognitive Search een synthetisch veld genaamd dat een samenvoeging is van partitiesleutel- en rijsleutelwaarden. Als de PartitionKey van een `PK1` rij bijvoorbeeld `RK1`is en `Key` RowKey is, dan is `PK1RK1`de waarde van het veld .

> [!NOTE]
> De `Key` waarde kan tekens bevatten die ongeldig zijn in documentsleutels, zoals streepjes. U ongeldige tekens `base64Encode` afhandelen met de [veldtoewijzingsfunctie](search-indexer-field-mappings.md#base64EncodeFunction). Vergeet niet, als u dit doet, om ook URL-safe Base64-codering te gebruiken bij het doorgeven van documentsleutels in API-aanroepen zoals Opzoeken.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Detectie van incrementele indexering en verwijdering
Wanneer u een tabelindexer instelt die volgens een planning wordt uitgevoerd, worden alleen nieuwe `Timestamp` of bijgewerkte rijen opnieuw geïndexeerd, zoals bepaald door de waarde van een rij. U hoeft geen wijzigingsdetectiebeleid op te geven. Incrementele indexering is automatisch voor u ingeschakeld.

Als u wilt aangeven dat bepaalde documenten uit de index moeten worden verwijderd, u een soft delete-strategie gebruiken. In plaats van een rij te verwijderen, voegt u een eigenschap toe om aan te geven dat deze is verwijderd en stelt u een beleid voor het verwijderen van zachte verwijderingsdetectie in op de gegevensbron. In het volgende beleid wordt bijvoorbeeld van mening dat een `IsDeleted` rij `"true"`wordt verwijderd als de rij een eigenschap heeft met de waarde:

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

Azure Cognitive Search gebruikt standaard het `Timestamp >= HighWaterMarkValue`volgende queryfilter: . Omdat Azure-tabellen geen secundaire index `Timestamp` in het veld hebben, vereist dit type query een volledige tabelscan en is het daarom traag voor grote tabellen.


Hier zijn twee mogelijke benaderingen voor het verbeteren van de tabel indexering prestaties. Beide benaderingen zijn afhankelijk van het gebruik van tabelpartities: 

- Als uw gegevens natuurlijk kunnen worden verdeeld in verschillende partitiebereiken, maakt u een gegevensbron en een bijbehorende indexer voor elk partitiebereik. Elke indexer hoeft nu alleen een specifiek partitiebereik te verwerken, wat resulteert in betere queryprestaties. Als de gegevens die moeten worden geïndexeerd een klein aantal vaste partities hebben, nog beter: elke indexer doet alleen een partitiescan. Als u bijvoorbeeld een gegevensbron wilt maken voor `000` `100`het verwerken van een partitiebereik met sleutels van tot , gebruikt u een query als volgt: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Als uw gegevens worden verdeeld naar tijd (u maakt bijvoorbeeld elke dag of week een nieuwe partitie), moet u de volgende benadering overwegen: 
    - Gebruik een query van `(PartitionKey ge <TimeStamp>) and (other filters)`het formulier: . 
    - Monitor de voortgang van indexeren met behulp van [Get Indexer Status API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)en werk periodiek de conditie van de `<TimeStamp>` query bij op basis van de laatste succesvolle hoogwatermerkwaarde. 
    - Als u met deze aanpak een volledige reindexering moet activeren, moet u de gegevensbronquery opnieuw instellen naast het opnieuw instellen van de indexer. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Help ons Azure Cognitive Search beter te maken
Als u functieverzoeken of ideeën voor verbeteringen hebt, dient u deze in op onze [UserVoice-site.](https://feedback.azure.com/forums/263029-azure-search/)
