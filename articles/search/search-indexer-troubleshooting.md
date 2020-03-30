---
title: Veelvoorkomende problemen met zoekindexeroplossen
titleSuffix: Azure Cognitive Search
description: Los fouten en veelvoorkomende problemen op met indexeerders in Azure Cognitive Search, waaronder gegevensbronverbinding, firewall en ontbrekende documenten.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190935"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Problemen met veelvoorkomende indexeren oplossen in Azure Cognitive Search

Indexers kunnen een aantal problemen ondervinden bij het indexeren van gegevens in Azure Cognitive Search. De belangrijkste categorieën van mislukkingen omvatten:

* [Verbinding maken met een gegevensbron of andere bronnen](#connection-errors)
* [Documentverwerking](#document-processing-errors)
* [Inname van documenten in een index](#index-errors)

## <a name="connection-errors"></a>Verbindingsfouten

> [!NOTE]
> Indexers hebben beperkte ondersteuning voor toegang tot gegevensbronnen en andere bronnen die zijn beveiligd door Azure-netwerkbeveiligingsmechanismen. Momenteel hebben indexeerders alleen toegang tot gegevensbronnen via overeenkomstige IP-adresbereikbeperkingsmechanismen of NSG-regels indien van toepassing. Details voor de toegang tot elke ondersteunde gegevensbron vindt u hieronder.
>
> U het IP-adres van uw zoekservice achterhalen door de `<your-search-service-name>.search.windows.net`volledig gekwalificeerde domeinnaam te pingen (bijv., ).
>
> U `AzureCognitiveSearch` kunt het IP-adresbereik van de [servicetag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) achterhalen door [downloadbare JSON-bestanden](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) te gebruiken of via de Service Tag Discovery [API.](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview) Het IP-adresbereik wordt wekelijks bijgewerkt.

### <a name="configure-firewall-rules"></a>Firewallregels configureren

Azure Storage, CosmosDB en Azure SQL bieden een configureerbare firewall. Er is geen specifiek foutbericht wanneer de firewall is ingeschakeld. Meestal zijn firewallfouten algemeen en `The remote server returned an error: (403) Forbidden` `Credentials provided in the connection string are invalid or have expired`lijken ze op of .

Er zijn 2 opties om indexeerders toegang te geven tot deze bronnen in een dergelijk geval:

* Schakel de firewall uit door toegang vanaf **alle netwerken** toe te staan (indien mogelijk).
* U ook toegang verlenen voor het IP-adres van uw `AzureCognitiveSearch` zoekservice en het IP-adresbereik van [servicetag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) in de firewallregels van uw bron (beperking ip-adresbereik).

Details voor het configureren van ip-adresbereikbeperkingen voor elk gegevensbrontype zijn te vinden in de volgende koppelingen:

* [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Beperking:** Zoals vermeld in de bovenstaande documentatie voor Azure Storage, werken ip-adresbereikbeperkingen alleen als uw zoekservice en uw opslagaccount zich in verschillende regio's bevinden.

Azure-functies (die kunnen worden gebruikt als [een Custom Web Api-vaardigheid)](cognitive-search-custom-skill-web-api.md)ondersteunen ook [IP-adresbeperkingen.](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions) De lijst met IP-adressen die u wilt configureren, is het `AzureCognitiveSearch` IP-adres van uw zoekservice en het IP-adresbereik van de servicetag.

Details voor toegang tot gegevens in SQL-server op een Azure VM worden [hier](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) beschreven

### <a name="configure-network-security-group-nsg-rules"></a>NSG-regels (Network Security Group) configureren

Wanneer u toegang krijgt tot gegevens in een SQL-beheerde instantie of wanneer een Azure VM wordt gebruikt als webservice URI voor een [Custom Web Api-vaardigheid,](cognitive-search-custom-skill-web-api.md)hoeven klanten zich geen zorgen te maken over specifieke IP-adressen.

In dergelijke gevallen kan de Azure VM of de SQL-beheerde instantie worden geconfigureerd om zich in een virtueel netwerk te bevinden. Vervolgens kan een netwerkbeveiligingsgroep worden geconfigureerd om het type netwerkverkeer te filteren dat in en uit de virtuele netwerksubnetten en netwerkinterfaces kan stromen.

De `AzureCognitiveSearch` servicetag kan direct worden gebruikt in de binnenkomende [NSG-regels](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) zonder dat u het IP-adresbereik hoeft op te zoeken.

Meer details voor toegang tot gegevens in een SQL-beheerde instantie worden [hier](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md) beschreven

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "Indexing" is niet ingeschakeld

Azure Cognitive Search heeft een impliciete afhankelijkheid van Cosmos DB-indexering. Als u automatische indexering uitschakelt in Cosmos DB, retourneert Azure Cognitive Search een geslaagde status, maar wordt de inhoud van de container niet geïndexeerd. Zie Indexering beheren in Azure Cosmos DB [voor](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal)instructies over het controleren van instellingen en het inschakelen van indexering.

## <a name="document-processing-errors"></a>Fouten in documentverwerking

### <a name="unprocessable-or-unsupported-documents"></a>Niet-verwerkbare of niet-ondersteunde documenten

De blob-indexerdocumenteert [welke documentindelingen expliciet worden ondersteund.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Soms bevat een blobopslagcontainer niet-ondersteunde documenten. Andere keren kunnen er problematische documenten zijn. U voorkomen dat uw indexer op deze documenten wordt gestopt door [configuratieopties te wijzigen:](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Ontbrekende documentinhoud

De blobindexer [vindt en extraheert tekst uit blobs in een container](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Enkele problemen met het extraheren van tekst zijn:

* Het document bevat alleen gescande afbeeldingen. PDF-blobs met niet-tekstinhoud, zoals gescande afbeeldingen (JPGs), leveren geen resultaten op in een standaard blobindexeringspijplijn. Als u afbeeldingsinhoud met tekstelementen hebt, u [cognitief zoeken](cognitive-search-concept-image-scenarios.md) gebruiken om de tekst te zoeken en te extraheren.
* De blob-indexer is geconfigureerd om alleen metagegevens te indexeren. Als u inhoud wilt extraheren, moet de blob-indexer zijn geconfigureerd om [zowel inhoud als metagegevens](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)te extraheren:

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Indexfouten

### <a name="missing-documents"></a>Ontbrekende documenten

Indexers vinden documenten uit een [gegevensbron](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Soms lijkt een document uit de gegevensbron dat had moeten worden geïndexeerd, te ontbreken in een index. Er zijn een paar veel voorkomende redenen waarom deze fouten kunnen gebeuren:

* Het document is niet geïndexeerd. Controleer de portal voor een succesvolle indexerrun.
* Het document is bijgewerkt nadat de indexer is uitgevoerd. Als uw indexer op een [schema staat,](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)wordt het uiteindelijk opnieuw uitgevoerd en wordt het document opgehaald.
* De [query](/rest/api/searchservice/create-data-source) die in de gegevensbron is opgegeven, sluit het document uit. Indexers kunnen geen documenten indexeren die geen deel uitmaken van de gegevensbron.
* [Veldtoewijzingen](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) of [AI-verrijking](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) hebben het document gewijzigd en het ziet er anders uit dan u verwacht.
* Gebruik de [opzoekdocument-API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) om uw document te vinden.
