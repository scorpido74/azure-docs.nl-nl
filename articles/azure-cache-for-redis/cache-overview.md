---
title: Wat is Azure Cache voor Redis?
description: Meer informatie over Azure Cache voor Redis om cacheopzij, cache-cache, cache cache, cache-caching, cachesessie cache, taak- en berichtwachtrij en gedistribueerde transacties in te schakelen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: 38936000e426d560237295105b5456429d9ae16d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79126362"
---
# <a name="azure-cache-for-redis-description"></a>Beschrijving van Azure Cache voor Redis

Azure Cache for Redis biedt een in-memory data store op basis van de open-source software [Redis.](https://redis.io/) Wanneer Redis als cache wordt gebruikt, verbetert het de prestaties en schaalbaarheid van systemen die sterk afhankelijk zijn van backend-gegevensopslag. De prestaties worden verbeterd door vaak geopende gegevens te kopiëren naar snelle opslag in de buurt van de toepassing. Met Azure Cache voor Redis bevindt deze snelle opslag zich in het geheugen in plaats van dat deze door een database van de schijf wordt geladen.

Azure Cache voor Redis kan worden gebruikt als een in-memory data structuur archief, een gedistribueerde niet-relationele database, en een bericht makelaar. De prestaties van toepassingen worden verbeterd door gebruik te maken van de snelle gegevensdoorvoer met lage latentie van de Redis-engine.

Azure Cache voor Redis biedt toegang tot een beveiligde, speciale Redis-cache. Azure Cache voor Redis wordt beheerd door Microsoft, gehost binnen Azure en toegankelijk voor elke toepassing binnen of buiten Azure.

## <a name="using-azure-cache-for-redis"></a>Azure-cache gebruiken voor Redis

Azure Cache voor Redis verbetert de prestaties van toepassingen door algemene toepassingsarchitectuurpatronen te ondersteunen. Sommige van de meest voorkomende zijn de volgende:

| Patroon      | Beschrijving                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Databases zijn vaak te groot om rechtstreeks in een cache te laden. Het is gebruikelijk om het [cache-aside-patroon](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) te gebruiken om gegevens alleen in de cache te laden als dat nodig is. Wanneer het systeem wijzigingen aanbrengt in de gegevens, kan het systeem ook de cache bijwerken, die vervolgens wordt gedistribueerd naar andere clients. Bovendien kan het systeem een vervaldatum instellen op gegevens of een uitzettingsbeleid gebruiken om gegevensupdates in de cache te activeren.|
| [Inhoud in cache opslaan](cache-aspnet-output-cache-provider.md) | Veel webpagina's worden gegenereerd uit sjablonen die statische inhoud gebruiken, zoals kopteksten, voetteksten, banners. Deze statische items mogen niet vaak veranderen. Het gebruik van een in-memory cache biedt snelle toegang tot statische inhoud in vergelijking met backend datastores. Dit patroon vermindert de verwerkingstijd en de serverbelasting, waardoor webservers responsiever kunnen reageren. Hiermee u het aantal servers verminderen dat nodig is om ladingen te verwerken. Azure Cache for Redis biedt de Redis Output Cache Provider om dit patroon te ondersteunen met ASP.NET.|
| [Gebruikerssessie opslaan in cache](cache-aspnet-session-state-provider.md) | Dit patroon wordt vaak gebruikt met winkelwagentjes en andere gebruikersgeschiedenisgegevens die een webtoepassing mogelijk wil koppelen aan gebruikerscookies. Te veel informatie opslaan in een cookie kan negatieve gevolgen hebben voor de prestaties als de cookie groter wordt en bij elke aanvraag wordt doorgegeven en gevalideerd. Een typische oplossing gebruikt de cookie als een sleutel om de gegevens in een database op te vragen. Het gebruik van een cache in het geheugen zoals Azure Cache voor Redis om gegevens te koppelen aan een gebruiker, is veel sneller dan interactie met een volledige relationele database. |
| Wachtrij met taken en berichten | Toepassingen voegen vaak taken toe aan een wachtrij wanneer de bewerkingen die aan de aanvraag zijn gekoppeld tijd vergen om uit te voeren. Langere lopende bewerkingen staan in de wachtrij om achter elkaar te worden verwerkt, vaak door een andere server.  Deze methode van werk uitstellen heet taken in de wachtrij plaatsen. Azure Cache voor Redis biedt een gedistribueerde wachtrij om dit patroon in uw toepassing in te schakelen.|
| Gedistribueerde transacties | Toepassingen vereisen soms een reeks opdrachten tegen een backend-gegevensarchief om uit te voeren als één atoombewerking. Alle opdrachten moeten slagen of alle moet worden teruggezet naar de beginstatus. Azure Cache for Redis ondersteunt het uitvoeren van een batch opdrachten als één [transactie.](https://redis.io/topics/transactions) |

## <a name="azure-cache-for-redis-offerings"></a>Aanbiedingen voor Azure Cache voor Redis

Azure Cache voor Redis is beschikbaar in de volgende lagen:

| Laag | Beschrijving |
|---|---|
Basic | Een cache met één knooppunt. Deze laag ondersteunt meerdere geheugenformaten (250 MB - 53 GB) en is ideaal voor ontwikkeling/test en niet-kritieke workloads. De Basic-laag heeft geen SLA (Service Level Agreement) |
| Standard | Een gerepliceerde cache in een primaire/secundaire configuratie met twee knooppunten die wordt beheerd door Azure met een SLA met hoge beschikbaarheid (99,9%) |
| Premium | De Premium-laag is de enterprise-ready-laag. Caches op de Premium-laag ondersteunen meer functies en hebben een hogere doorvoersnelheid met een lagere latentie. Caches op de Premium-laag worden geïmplementeerd op krachtigere hardware en bieden daarom betere prestaties in vergelijking met de Basic- of Standard-laag. Dit voordeel betekent dat de doorvoer voor een cache van dezelfde grootte hoger zal zijn in Premium in vergelijking met de standaardlaag. |

> [!TIP]
> Raadpleeg [Veelgestelde vragen over Azure Cache voor Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) voor meer informatie over de grootte, doorvoer en bandbreedte van premiumcaches.
>

U uw cache opschalen naar een hogere laag nadat deze is gemaakt. Omlaag schalen naar een lagere laag wordt niet ondersteund. Zie voor stapsgewijze instructies [De schaal aanpassen van Azure Cache voor Redis](cache-how-to-scale.md) en [Een schaalbewerking automatiseren](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Vergelijking van functies

De pagina [Prijzen van Azure Cache voor Redis](https://azure.microsoft.com/pricing/details/cache/) bevat een gedetailleerde vergelijking van elke laag. In de volgende tabel worden de functies beschreven die door elke laag worden ondersteund:

| Omschrijving | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Service Level Agreement (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis-gegevenspersistentie](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis-cluster](cache-how-to-premium-clustering.md) |✔|-|-|
| [Beveiliging via de firewallregels](cache-configure.md#firewall) |✔|✔|✔|
| Versleuteling 'in transit' |✔|✔|✔|
| [Verbeterde veiligheid en isolatie met VNet](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/export](cache-how-to-import-export-data.md) |✔|-|-|
| [Geplande updates](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Geo-replicatie](cache-how-to-geo-replication.md) |✔|-|-|
| [Opnieuw opstarten](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Volgende stappen

* [Snelstart ASP.NET-webapp](cache-web-app-howto.md) Maak een eenvoudige ASP.NET-webapp die gebruikmaakt van een Azure Cache voor Redis.
* [Snelstart .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Maak een .NET-app die gebruikmaakt van een Azure Cache voor Redis.
* [Snelstart .NET Core](cache-dotnet-core-quickstart.md) Maak een .NET Core-app die gebruikmaakt van een Azure Cache voor Redis.
* [Snelstart Node.js](cache-nodejs-get-started.md) Maak een eenvoudige Node.js-app die gebruikmaakt van een Azure Cache voor Redis.
* [Snelstart Java](cache-java-get-started.md) Maak een eenvoudige Java-app die gebruikmaakt van een Azure Cache voor Redis.
* [Snelstart Python](cache-python-get-started.md) Maak een Python-app die gebruikmaakt van een Azure Cache voor Redis.
