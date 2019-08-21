---
title: Veelvoorkomende problemen met zoek indexen oplossen-Azure Search
description: Corrigeer fouten en veelvoorkomende problemen met Indexeer functies in Azure Search, waaronder gegevens bron verbinding, firewall en ontbrekende documenten.
author: mgottein
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.openlocfilehash: 4692be287e9b38cf116107d2e7c1043f23a6b34b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640601"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Veelvoorkomende problemen met Indexeer functies in Azure Search oplossen

Indexeer functies kunnen in een aantal problemen worden uitgevoerd bij het indexeren van gegevens in Azure Search. De belangrijkste categorieën fouten zijn onder andere:

* [Verbinding maken met een gegevens bron](#data-source-connection-errors)
* [Document verwerking](#document-processing-errors)
* [Opname van documenten naar een index documenteren](#index-errors)

## <a name="data-source-connection-errors"></a>Verbindings fouten van gegevens bronnen

### <a name="blob-storage"></a>Blob-opslag

#### <a name="storage-account-firewall"></a>Firewall voor opslag account

Azure Storage biedt een Configureer bare firewall. De firewall is standaard uitgeschakeld, zodat Azure Search verbinding kan maken met uw opslag account.

Er is geen specifiek fout bericht wanneer de firewall is ingeschakeld. Meestal zien Firewall fouten er als `The remote server returned an error: (403) Forbidden`volgt uit.

U kunt controleren of de firewall is ingeschakeld in de [Portal](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). De enige ondersteunde tijdelijke oplossing is om de firewall uit te scha kelen door ervoor te kiezen om toegang vanaf [alle netwerken](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)toe te staan.

Als uw Indexeer functie geen bijbehorende vaardig heden heeft, _kunt_ u [een uitzonde ring toevoegen](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) voor de IP-adressen van uw zoek service. Dit scenario wordt echter niet ondersteund en is niet gegarandeerd.

U kunt het IP-adres van uw zoek service vinden door de FQDN (`<your-search-service-name>.search.windows.net`) te pingen.

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>Indexeren is niet ingeschakeld

Azure Search heeft een impliciete afhankelijkheid van Cosmos DB indexeren. Als u automatische indexering in Cosmos DB uitschakelt, wordt door Azure Search een succes volle status geretourneerd, maar kan de inhoud van de container niet worden geïndexeerd. Zie [Manage Indexing in azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal)voor instructies over het controleren van instellingen en het inschakelen van indexeren.

## <a name="document-processing-errors"></a>Document verwerkings fouten

### <a name="unprocessable-or-unsupported-documents"></a>Niet-verwerkte of niet-ondersteunde documenten

De BLOB-Indexeer [documenten die worden ondersteund door de document indelingen expliciet](search-howto-indexing-azure-blob-storage.md#supported-document-formats) Soms bevat een BLOB storage-container niet-ondersteunde documenten. Het kan zijn dat er problemen zijn met de documenten. U kunt voor komen dat uw Indexeer functie op deze documenten wordt gestopt door [configuratie opties te wijzigen](search-howto-indexing-azure-blob-storage.md#dealing-with-errors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Ontbrekende document inhoud

De BLOB-indexer [zoekt en extraheert tekst van blobs in een container](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Enkele problemen met het ophalen van tekst zijn onder andere:

* Het document bevat alleen gescande afbeeldingen. PDF-blobs met niet-tekst inhoud, zoals gescande afbeeldingen (JPGs), produceren geen resultaten in een standaard-BLOB-indexerings pijplijn. Als u afbeeldings inhoud met tekst elementen hebt, kunt u met [cognitieve zoek opdracht](cognitive-search-concept-image-scenarios.md) de tekst zoeken en extra heren.
* De BLOB-indexer is zo geconfigureerd dat alleen meta gegevens worden geïndexeerd. Als u inhoud wilt extra heren, moet de BLOB-indexer worden geconfigureerd voor het [extra heren van inhoud en meta gegevens](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Index fouten

### <a name="missing-documents"></a>Ontbrekende documenten

Indexeer functies vinden documenten van een [gegevens bron](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Soms lijkt een document uit de gegevens bron dat moest worden geïndexeerd, te ontbreken in een index. Er zijn enkele veelvoorkomende redenen waarom deze fouten zich kunnen voordoen:

* Het document is niet geïndexeerd. Controleer de portal op de uitvoering van een geslaagde indexer.
* Het document is bijgewerkt nadat de Indexeer functie is uitgevoerd. Als uw Indexeer functie volgens een [schema is gepland](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), wordt het document uiteindelijk opnieuw uitgevoerd en opgehaald.
* De [query](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) die is opgegeven in de gegevens bron, sluit het document uit. Indexeer functies kunnen geen documenten indexeren die geen deel uitmaken van de gegevens bron.
* [Veld Toewijzingen](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) of [cognitieve zoek opdracht](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) hebben het document gewijzigd en het ziet er anders uit dan verwacht.
* Gebruik de [opzoek document-API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) om uw document te vinden.
