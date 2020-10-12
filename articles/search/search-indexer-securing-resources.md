---
title: Veilig toegang tot Indexeer bronnen
titleSuffix: Azure Cognitive Search
description: Conceptueel overzicht van de beveiligings opties op netwerk niveau voor Azure Data Access door Indexeer functies in azure Cognitive Search.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 5075c4858f9584cb19442e19d9009d46d0e00ff8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463550"
---
# <a name="indexer-access-to-data-sources-using-azure-network-security-features"></a>Indexeer functie toegang tot gegevens bronnen met behulp van Azure-netwerk beveiligings functies

Azure Cognitive Search Indexeer functies kunnen tijdens de uitvoering uitgaande aanroepen naar verschillende Azure-resources maken. In dit artikel worden de concepten uitgelegd van de toegang van de Indexeer functie tot bronnen wanneer deze bronnen worden beveiligd door IP-firewalls, persoonlijke eind punten en andere beveiligings mechanismen op netwerk niveau. De mogelijke resource typen die een Indexeer functie in een typische uitvoering kan openen, worden weer gegeven in de onderstaande tabel.

| Resource | Doel in Indexeer functie uitvoeren |
| --- | --- |
| Azure Storage (blobs, tabellen, ADLS gen 2) | Gegevensbron |
| Azure Storage (blobs, tabellen) | Vaardig heden (in cache opslaan van verrijkte documenten en kennis archief-projecties opslaan) |
| Azure Cosmos DB (verschillende Api's) | Gegevensbron |
| Azure SQL Database | Gegevensbron |
| SQL Server op Azure IaaS Vm's | Gegevensbron |
| Beheerde SQL-exemplaren | Gegevensbron |
| Azure Functions | Host voor aangepaste web-API-vaardig heden |
| Cognitive Services | Gekoppeld aan de vaardig heden die worden gebruikt voor het factureren van verrijking buiten de limiet van 20 vrije documenten |

> [!NOTE]
> De cognitieve service resource die aan een vakkennisset is gekoppeld, wordt gebruikt voor facturering, op basis van de verrijkingen die worden uitgevoerd en geschreven in de zoek index. Het wordt niet gebruikt voor toegang tot de Cognitive Services-API's. Toegang tot de verrijkings pijplijn van een Indexeer functie op Cognitive Services-API's vindt plaats via een beveiligd communicatie kanaal, waarbij gegevens tijdens de overdracht sterk worden versleuteld en nooit op rest worden opgeslagen.

Klanten kunnen deze bronnen beveiligen via verschillende netwerk isolatie mechanismen die worden geboden door Azure. Met uitzonde ring van de cognitieve Service Resource hebben Indexeer functies beperkte mogelijkheid om toegang te krijgen tot alle andere resources, zelfs als ze in de onderstaande tabel worden beschreven.

| Resource | IP-beperking | Privé-eindpunt |
| --- | --- | ---- |
| Azure Storage (blobs, tabellen, ADLS gen 2) | Alleen ondersteund als het opslag account en de zoek service zich in verschillende regio's bevinden | Ondersteund |
| Azure Cosmos DB-SQL-API | Ondersteund | Ondersteund |
| Azure Cosmos DB-Cassandra, Mongo en Gremlin-API | Ondersteund | Niet ondersteund |
| Azure SQL Database | Ondersteund | Ondersteund |
| SQL Server op Azure IaaS Vm's | Ondersteund | N.v.t. |
| Beheerde SQL-exemplaren | Ondersteund | N.v.t. |
| Azure Functions | Ondersteund | Ondersteund, alleen voor bepaalde Sku's van Azure functions |

> [!NOTE]
> Naast de bovenstaande opties kunt u voor Azure-opslag accounts die zijn beveiligd met het netwerk, het feit gebruikmaken dat Azure Cognitive Search een [vertrouwde micro soft-service](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)is. Dit betekent dat een specifieke zoek service virtuele netwerk-of IP-beperkingen kan omzeilen voor het opslag account en toegang kan krijgen tot gegevens in het opslag account als het juiste op rollen gebaseerd toegangs beheer is ingeschakeld voor het opslag account. Meer informatie vindt u in de [hand leiding](search-indexer-howto-access-trusted-service-exception.md). Deze optie kan worden gebruikt in plaats van de IP-beperkings route, in het geval het opslag account of de zoek service niet kan worden verplaatst naar een andere regio.

Houd rekening met de volgende beperkingen bij het kiezen van het mechanisme voor beveiligde toegang dat een Indexeer functie moet gebruiken:

- [Service-eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) worden niet ondersteund voor een Azure-resource.
- Er kan geen zoek service worden ingericht in een specifiek virtueel netwerk. deze functionaliteit wordt niet aangeboden door Azure Cognitive Search.
- Wanneer Indexeer functies (uitgaand) persoonlijke eind punten gebruiken om toegang te krijgen tot resources, kunnen er extra [kosten](https://azure.microsoft.com/pricing/details/search/) in rekening worden gebracht.

## <a name="indexer-execution-environment"></a>Uitvoerings omgeving van de Indexeer functie

Azure Cognitive Search Indexeer functies kunnen inhoud efficiënt ophalen uit gegevens bronnen, verrijkingen toevoegen aan de geëxtraheerde inhoud, eventueel projecties genereren voordat de resultaten naar de zoek index worden geschreven. Afhankelijk van het aantal verantwoordelijkheden dat aan een Indexeer functie is toegewezen, kan dit in een van de twee omgevingen worden uitgevoerd:

- Een omgeving die privé is voor een specifieke zoek service. Indexeer functies die in dergelijke omgevingen worden uitgevoerd, delen resources met andere workloads (zoals andere klanten die indexering of query uitvoeren op de werk belasting). Normaal gesp roken alleen Indexeer functies die geen veel bronnen nodig hebben (bijvoorbeeld geen vaardig heden gebruiken) die in deze omgeving worden uitgevoerd.
- Een omgeving met meerdere tenants die als host fungeert voor Indexeer functies die veel resources zijn, zoals bij een vaardig heden. Bronnen die zeer veel worden uitgevoerd in deze omgeving om optimale prestaties te bieden en ervoor te zorgen dat de bronnen van de zoek service beschikbaar zijn voor andere werk belastingen. Deze omgeving met meerdere tenants wordt beheerd en beveiligd door Azure Cognitive Search, zonder extra kosten aan de klant.

Voor elke gewenste uitvoering van de Indexeer functie bepaalt Azure Cognitive Search de beste omgeving waarin de Indexeer functie wordt uitgevoerd.

## <a name="granting-access-to-indexer-ip-ranges"></a>Toegang verlenen tot de IP-bereiken van de Indexeer functie

Als de bron waartoe uw Indexeer functie probeert toegang wordt beperkt tot slechts een bepaalde set IP-bereiken, moet u de set uitbreiden om de mogelijke IP-bereiken op te nemen waaruit een Indexeer functie-aanvraag kan afkomstig zijn. Zoals hierboven vermeld, zijn er twee mogelijke omgevingen waarin Indexeer functies worden uitgevoerd en van waaruit de toegangs aanvragen afkomstig zijn. U moet de IP-adressen van __beide__ omgevingen toevoegen zodat de toegang tot de Indexeer functie werkt.

- Om het IP-adres van de zoek service te verkrijgen, `nslookup` (of `ping` ) de Fully QUALIFIED domain name (FQDN) van uw zoek service. De FQDN-namen van een zoek service in de open bare Cloud zijn bijvoorbeeld `<service-name>.search.windows.net` . Deze informatie is beschikbaar op de Azure Portal.
- De IP-adressen van de omgevingen met meerdere tenants zijn beschikbaar via de `AzureCognitiveSearch` service-tag. [Azure-service Tags](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) hebben een gepubliceerd bereik van IP-adressen voor elke service: dit is beschikbaar via een [detectie-API (preview)](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview) of een [downloadbaar JSON-bestand](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files). In beide gevallen worden IP-bereiken onderverdeeld per regio: u kunt alleen de IP-bereiken kiezen die zijn toegewezen voor de regio waarin uw zoek service is ingericht.

Voor bepaalde gegevens bronnen kan de servicetag zelf rechtstreeks worden gebruikt in plaats van het opsommen van de lijst met IP-bereiken (het IP-adres van de zoek service moet nog steeds expliciet worden gebruikt). Deze gegevens bronnen beperken de toegang door middel van het instellen van een regel voor een [netwerk beveiligings groep](https://docs.microsoft.com/azure/virtual-network/security-overview), die systeem eigen ondersteuning biedt voor het toevoegen van een servicetag, in tegens telling tot IP-regels, zoals de services die worden aangeboden door Azure Storage, CosmosDB, Azure SQL, enzovoort, de gegevens bronnen die de mogelijkheid bieden om de `AzureCognitiveSearch` servicetag direct naast de zoek service-IP-adressen te gebruiken:

- [SQL Server op IaaS Vm's](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers#restrict-access-to-the-azure-cognitive-search)

- [SQL Managed instances](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers#verify-nsg-rules)

Details worden beschreven in de [hand leiding](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Toegang verlenen via persoonlijke eind punten

Indexeer functies kunnen privé- [eind punten](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) gebruiken om toegang te krijgen tot resources, de toegang die is vergrendeld om virtuele netwerken te selecteren of waarvoor geen open bare toegang is ingeschakeld.
Deze functionaliteit is alleen beschikbaar voor betaalde Services, met limieten voor het aantal privé-eind punten dat wordt gemaakt. Details over de limieten worden beschreven op de [pagina Azure Search limieten](search-limits-quotas-capacity.md).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Stap 1: een persoonlijk eind punt maken voor de beveiligde resource

Klanten moeten de bewerking voor het beheer van zoek bewerkingen aanroepen, de resource-API van de [ *gedeelde persoonlijke koppeling* maken of bijwerken](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) om een privé-eindpunt verbinding te maken met de beveiligde bron (bijvoorbeeld een opslag account). Verkeer dat overgaat op deze (uitgaand) particuliere endpoint-verbinding, is alleen afkomstig van het virtuele netwerk in de zoek service specifieke ' persoonlijke ' uitvoerings omgeving.

Azure Cognitive Search valideert dat aanroepers van deze API machtigingen hebben voor het goed keuren van verbindings aanvragen voor een privé-eind punt naar de beveiligde bron. Als u bijvoorbeeld een verbinding met een persoonlijk eind punt aanvraagt met een opslag account waartoe u geen toegang hebt, wordt deze aanroep geweigerd.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Stap 2: de verbinding met een privé-eind punt goed keuren

Wanneer de (asynchrone) bewerking die een gedeelde persoonlijke koppelings bron maakt, is voltooid, wordt een verbinding voor een persoonlijk eind punt gemaakt met de status in behandeling. Er is nog geen verkeer dat via de verbinding loopt.
De klant wordt vervolgens naar verwachting deze aanvraag te vinden op hun beveiligde resource en ' goed keuren '. Normaal gesp roken kunt u dit doen via de portal of via de [rest API](https://docs.microsoft.com/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Stap 3: Indexeer functies kunnen worden uitgevoerd in de ' persoonlijke ' omgeving

Een goedgekeurd privé-eind punt staat uitgaande aanroepen van de zoek service toe aan een resource die een bepaalde vorm van toegangs beperkingen op netwerk niveau heeft (bijvoorbeeld een gegevens bron van het opslag account die alleen is geconfigureerd voor toegang tot bepaalde virtuele netwerken).
Dit betekent dat elke Indexeer functie die kan worden bereikt met een dergelijke gegevens bron via het persoonlijke eind punt, slaagt.
Als het persoonlijke eind punt niet is goedgekeurd of als de Indexeer functie de verbinding met het persoonlijke eind punt niet gebruikt, wordt de Indexeer functie uiteindelijk uitgevoerd in `transientFailure` .

Als u Indexeer functies wilt inschakelen voor toegang tot resources via verbindingen met een privé-eind punt, moet u de `executionEnvironment` Indexeer functie instellen op `"Private"` om ervoor te zorgen dat alle Indexeer functie-uitvoeringen het persoonlijke eind punt kunnen gebruiken. Dit komt doordat persoonlijke eind punten worden ingericht binnen de persoonlijke zoek service-specifieke omgeving.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Deze stappen worden uitgebreid beschreven in de [hand leiding](search-indexer-howto-access-private.md).
Zodra u een goedgekeurd privé-eind punt aan een resource hebt, hebben Indexeer functies die zijn ingesteld op *persoonlijke* pogingen om toegang te verkrijgen via de verbinding van het particuliere eind punt.

### <a name="limits"></a>Limieten

Om te zorgen voor optimale prestaties en stabiliteit van de zoek service, worden er beperkingen opgelegd (door de zoek service-SKU) op de volgende dimensies:

- De soorten Indexeer functies die kunnen worden ingesteld als *persoonlijk*.
- Het aantal gedeelde persoonlijke koppelings resources dat kan worden gemaakt.
- Het aantal afzonderlijke resource typen waarvoor gedeelde persoonlijke koppelings resources kunnen worden gemaakt.

Deze limieten worden beschreven in [service limieten](search-limits-quotas-capacity.md).
