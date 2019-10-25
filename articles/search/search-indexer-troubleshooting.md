---
title: Veelvoorkomende problemen met zoek indexen oplossen
titleSuffix: Azure Cognitive Search
description: Corrigeer fouten en veelvoorkomende problemen met Indexeer functies in azure Cognitive Search, waaronder gegevens bron verbinding, firewall en ontbrekende documenten.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c5a16d957f1e0414f92d0cc03442d88d438e4c92
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793631"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Veelvoorkomende problemen met Indexeer functies in azure Cognitive Search oplossen

Indexeer functies kunnen in een aantal problemen worden uitgevoerd bij het indexeren van gegevens in azure Cognitive Search. De belangrijkste categorieën fouten zijn onder andere:

* [Verbinding maken met een gegevens bron](#data-source-connection-errors)
* [Document verwerking](#document-processing-errors)
* [Opname van documenten naar een index documenteren](#index-errors)

## <a name="data-source-connection-errors"></a>Verbindings fouten van gegevens bronnen

### <a name="blob-storage"></a>Blobopslag

#### <a name="storage-account-firewall"></a>Firewall voor opslag account

Azure Storage biedt een Configureer bare firewall. De firewall is standaard uitgeschakeld, zodat Azure Cognitive Search verbinding kan maken met uw opslag account.

Er is geen specifiek fout bericht wanneer de firewall is ingeschakeld. Normaal gesp roken zien Firewall fouten er als `The remote server returned an error: (403) Forbidden`.

U kunt controleren of de firewall is ingeschakeld in de [Portal](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). De enige ondersteunde tijdelijke oplossing is om de firewall uit te scha kelen door ervoor te kiezen om toegang vanaf [alle netwerken](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)toe te staan.

Als uw Indexeer functie geen bijbehorende vaardig heden heeft, _kunt_ u [een uitzonde ring toevoegen](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) voor de IP-adressen van uw zoek service. Dit scenario wordt echter niet ondersteund en is niet gegarandeerd.

U kunt het IP-adres van uw zoek service nagaan door de FQDN (`<your-search-service-name>.search.windows.net`) te pingen.

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>Indexeren is niet ingeschakeld

Azure Cognitive Search heeft een impliciete afhankelijkheid van Cosmos DB indexering. Als u automatische indexering in Cosmos DB uitschakelt, wordt door Azure Cognitive Search een succes volle status geretourneerd, maar kan de inhoud van de container niet worden geïndexeerd. Zie [Manage Indexing in azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal)voor instructies over het controleren van instellingen en het inschakelen van indexeren.

## <a name="document-processing-errors"></a>Document verwerkings fouten

### <a name="unprocessable-or-unsupported-documents"></a>Niet-verwerkte of niet-ondersteunde documenten

De BLOB-Indexeer [documenten die worden ondersteund door de document indelingen expliciet](search-howto-indexing-azure-blob-storage.md#SupportedFormats) Soms bevat een BLOB storage-container niet-ondersteunde documenten. Het kan zijn dat er problemen zijn met de documenten. U kunt voor komen dat uw Indexeer functie op deze documenten wordt gestopt door [configuratie opties te wijzigen](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

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
* [Veld Toewijzingen](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) of [AI-verrijking](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) hebben het document gewijzigd en het ziet er anders uit dan verwacht.
* Gebruik de [opzoek document-API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) om uw document te vinden.
