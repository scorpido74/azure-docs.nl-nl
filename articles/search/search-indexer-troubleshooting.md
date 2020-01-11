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
ms.openlocfilehash: d1efd44614cc2384043b32da20f38c91f006459c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863101"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Veelvoorkomende problemen met Indexeer functies in azure Cognitive Search oplossen

Indexeer functies kunnen in een aantal problemen worden uitgevoerd bij het indexeren van gegevens in azure Cognitive Search. De belangrijkste categorieën fouten zijn onder andere:

* [Verbinding maken met een gegevens bron of andere resources](#connection-errors)
* [Document verwerking](#document-processing-errors)
* [Opname van documenten naar een index documenteren](#index-errors)

## <a name="connection-errors"></a>Verbindingsfouten

> [!NOTE]
> Indexeer functies hebben beperkte ondersteuning voor toegang tot gegevens bronnen en andere bronnen die worden beveiligd door Azure-netwerk beveiligings mechanismen. Op dit moment kunnen Indexeer functies alleen toegang krijgen tot gegevens bronnen via de bijbehorende beperkings mechanismen voor IP-adres bereik of NSG regels, indien van toepassing. Details voor toegang tot elke ondersteunde gegevens bron vindt u hieronder.
>
> U kunt het IP-adres van uw zoek service nagaan door de Fully Qualified Domain Name (bijvoorbeeld `<your-search-service-name>.search.windows.net`) te pingen.
>
> U kunt het IP-adres bereik van `AzureCognitiveSearch` [servicetag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) vinden voor de specifieke regio waarin uw Azure Cognitive Search-service aanwezig is door gebruik te maken van [Download bare json-bestanden](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) of via de [service tag discovery-API](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview). Het IP-adres bereik wordt wekelijks bijgewerkt.

### <a name="configure-firewall-rules"></a>Firewall regels configureren

Azure Storage, CosmosDB en Azure SQL bieden een Configureer bare firewall. Er is geen specifiek fout bericht wanneer de firewall is ingeschakeld. Normaal gesp roken zijn firewall fouten algemeen en zien ze eruit als `The remote server returned an error: (403) Forbidden` of `Credentials provided in the connection string are invalid or have expired`.

Er zijn twee opties voor het toestaan van Indexeer functies voor toegang tot deze resources:

* Schakel de firewall uit door toegang vanaf **alle netwerken** toe te staan (indien mogelijk).
* U kunt ook toegang toestaan voor het IP-adres van uw zoek service en het IP-adres bereik van `AzureCognitiveSearch` servicetag in de firewall regels van uw bron ( [beperking voor IP](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -adres bereik).

Details voor het configureren van IP-adres bereik beperkingen voor elk gegevens bron type vindt u op de volgende koppelingen:

* [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Beperking**: zoals vermeld in de bovenstaande documentatie voor Azure Storage, werken IP-adres bereik beperkingen alleen als uw zoek service en uw opslag account zich in verschillende regio's bevinden.

Azure functions (die kunnen worden gebruikt als [aangepaste web API-vaardigheid](cognitive-search-custom-skill-web-api.md)) ondersteunen ook [IP-adres beperkingen](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions). De lijst met IP-adressen die moeten worden geconfigureerd, is het IP-adres van de zoek service en het IP-adres bereik van `AzureCognitiveSearch` servicetag.

Details voor het openen van gegevens in SQL Server op een virtuele machine van Azure worden [hier](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) beschreven

### <a name="configure-network-security-group-nsg-rules"></a>Regels voor netwerk beveiligings groepen (NSG) configureren

Bij het openen van gegevens in een SQL Managed instance of wanneer een virtuele machine van Azure wordt gebruikt als de webservice-URI voor een [aangepaste web API-vaardigheid](cognitive-search-custom-skill-web-api.md), hoeven klanten geen gebruik te maken van specifieke IP-adressen.

In dergelijke gevallen kan de virtuele machine van Azure of het beheerde exemplaar van SQL worden geconfigureerd zodat deze zich binnen een virtueel netwerk bevindt. Vervolgens kan een netwerk beveiligings groep worden geconfigureerd om het type netwerk verkeer te filteren dat in en uit de subnetten van het virtuele netwerk en netwerk interfaces kan stromen.

De code van de `AzureCognitiveSearch`-service kan rechtstreeks worden gebruikt in de regels voor inkomende [NSG](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) zonder dat het IP-adres bereik moet worden opgezocht.

Meer informatie over het openen van gegevens in een SQL Managed instance wordt [hier](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md) beschreven

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB ' indexeren ' is niet ingeschakeld

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
