---
title: Wat is Azure Cache voor Redis?
description: Kom erachter hoe u met Azure Cache voor Redis cache-aside, cacheopslag van inhoud, gebruikerssessie opslaan in cache, wachtrij met taken en berichten, en gedistribueerde transacties kunt inschakelen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 75c6f8ec8410ff90b3da4fb6a50c9ef8ba7d1618
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184616"
---
# <a name="azure-cache-for-redis"></a>Azure Cache voor Redis

Azure Cache voor Redis biedt gegevensopslag in het geheugen op basis van de opensourcesoftware [Redis](https://redis.io/). Wanneer Redis als cache wordt gebruikt, worden de prestaties en schaalbaarheid verbeterd van systemen die sterk afhankelijk zijn van back-endgegevensopslag. De prestaties worden verbeterd door veelgebruikte gegevens te kopiëren naar een snelle opslag die zich vlak bij de toepassing bevindt. Met Azure Cache voor Redis bevindt deze snelle opslag zich in het geheugen in plaats van dat de opslag door een database vanaf een schijf wordt geladen.

Azure Cache voor Redis kan worden gebruikt als gedistribueerde gegevenscache, sessieopslag en berichtenbroker. De prestaties van toepassingen worden verbeterd door gebruik te maken van de snelle gegevensdoorvoer met lage latentie van de Redis-engine.

Azure Cache voor Redis biedt toegang tot een beveiligde, toegewezen Redis-cache. De cache wordt beheerd door Microsoft, gehost op Azure en is toegankelijk voor alle toepassingen van Azure en daarbuiten.

## <a name="using-azure-cache-for-redis"></a>Azure Cache voor Redis gebruiken

Azure Cache voor Redis verbetert de prestaties van toepassingen door veelgebruikte architectuurpatronen van toepassingen te ondersteunen. Sommige van de meest voorkomende zijn de volgende:

| Patroon      | Beschrijving                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Databases zijn vaak te groot om rechtstreeks in een cache te laden. Het is gebruikelijk om het [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside)-patroon te gebruiken om alleen de benodigde gegevens in de cache te laden. Als het systeem wijzigingen aanbrengt in de gegevens, kan het ook de cache bijwerken, die vervolgens wordt gedistribueerd naar andere clients. Het systeem kan bovendien een vervaldatum instellen voor gegevens of een verwijderingsbeleid gebruiken om gegevensupdates in de cache te activeren.|
| [Inhoud in cache opslaan](cache-aspnet-output-cache-provider.md) | Veel webpagina's worden gegenereerd op basis van sjablonen die gebruikmaken van statische inhoud, zoals kopteksten, voetteksten en banners. Deze statische items worden meestal niet vaak bijgewerkt. Cache in het geheugen biedt snelle toegang tot statische inhoud vergeleken met back-endgegevensarchieven. Dit patroon vermindert de verwerkingstijd en serverbelasting, waardoor webservers sneller kunnen reageren. Zo hebt u minder servers nodig om belasting te verwerken. Azure Cache voor Redis biedt de Redis Output Cache Provider om dit patroon te ondersteunen met ASP.NET.|
| [Gebruikerssessie opslaan in cache](cache-aspnet-session-state-provider.md) | Dit patroon wordt vaak gebruikt met winkelwagens en andere gegevens van de gebruikersgeschiedenis die een webtoepassing mogelijk wil koppelen aan gebruikerscookies. Te veel informatie opslaan in een cookie kan negatieve gevolgen hebben voor de prestaties als de cookie groter wordt en bij elke aanvraag wordt doorgegeven en gevalideerd. Een gangbare oplossing is om de cookie als sleutel te gebruiken voor het opvragen van gegevens in een database. Het gebruik van een cache in het geheugen zoals Azure Cache voor Redis om gegevens te koppelen aan een gebruiker, is veel sneller dan interactie met een volledige relationele database. |
| Wachtrij met taken en berichten | Toepassingen voegen taken vaak toe aan een wachtrij als er tijd nodig is om de bewerkingen van een bepaalde aanvraag uit te voeren. Langdurige bewerkingen worden in de wachtrij gezet en op volgorde verwerkt, vaak door een andere server.  Deze methode van werk uitstellen heet taken in de wachtrij plaatsen. Azure Cache voor Redis biedt een gedistribueerde wachtrij om dit patroon in te schakelen in uw toepassing.|
| Gedistribueerde transacties | Toepassingen vereisen soms een reeks opdrachten voor een back-endgegevensopslag die moeten worden uitgevoerd als één atomische bewerking. Alle opdrachten moeten slagen of alle moet worden teruggezet naar de beginstatus. Azure Cache voor Redis ondersteunt het uitvoeren van een batch met opdrachten als één [transactie](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Aanbiedingen voor Azure Cache voor Redis

Azure Cache voor Redis is beschikbaar in de volgende lagen:

| Laag | Beschrijving |
|---|---|
Basic | Een cache met één knooppunt. Deze laag ondersteunt meerdere geheugenformaten (250 MB - 53 GB) en is ideaal voor ontwikkel-/test- of niet-kritieke workloads. De Basic-laag heeft geen SLA (Service Level Agreement) |
| Standard | Een gerepliceerde cache in een configuratie met twee knooppunten (primair/replica) die door Azure wordt beheerd, met een SLA met hoge beschikbaarheid (99,9%) |
| Premium | De Premium-laag is geschikt voor zakelijk gebruik. Caches op de Premium-laag ondersteunen meer functies en hebben een hogere doorvoersnelheid met een lagere latentie. Caches op de Premium-laag worden geïmplementeerd op krachtigere hardware en bieden daarom betere prestaties in vergelijking met de Basic- of Standard-laag. Dit voordeel betekent dat de doorvoer voor een cache van dezelfde grootte sneller gaat bij Premium dan bij Standard. |

> [!TIP]
> Raadpleeg [Veelgestelde vragen over Azure Cache voor Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) voor meer informatie over de grootte, doorvoer en bandbreedte van premiumcaches.
>

U kunt de cache opschalen naar een hogere laag nadat u de cache hebt gemaakt. Omlaag schalen naar een lagere laag wordt niet ondersteund. Zie voor stapsgewijze instructies [De schaal aanpassen van Azure Cache voor Redis](cache-how-to-scale.md) en [Een schaalbewerking automatiseren](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Vergelijking van functies

De pagina [Prijzen van Azure Cache voor Redis](https://azure.microsoft.com/pricing/details/cache/) bevat een gedetailleerde vergelijking van elke laag. In de volgende tabel worden de functies beschreven die door elke laag worden ondersteund:

| Omschrijving | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Service Level Agreement (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis-gegevenspersistentie](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis-cluster](cache-how-to-premium-clustering.md) |✔|-|-|
| [Beveiliging via de firewallregels](cache-configure.md#firewall) |✔|✔|✔|
| Versleuteling tijdens overdracht |✔|✔|✔|
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
