---
title: Wat is Azure Cache voor Redis?
description: Meer informatie over Azure cache voor redis voor het inschakelen van cache opslag, caching van inhoud, het opslaan van gebruikers sessies, taak-en Message Queuing-en gedistribueerde trans acties.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: 38936000e426d560237295105b5456429d9ae16d
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126362"
---
# <a name="azure-cache-for-redis-description"></a>Azure cache voor redis-beschrijving

Azure cache voor redis voorziet in een gegevens archief in het geheugen op basis van de open-source software [redis](https://redis.io/). Redis verbetert de prestaties en schaal baarheid van systemen die sterk afhankelijk zijn van back-end-gegevens opslag, wanneer ze worden gebruikt als een cache. De prestaties zijn verbeterd door regel matig gebruikte gegevens te kopiëren naar snelle opslag die dicht bij de toepassing staat. Met Azure cache voor redis bevindt deze snelle opslag zich in het geheugen, in plaats van dat ze van schijf worden geladen door een Data Base.

Azure cache voor redis kan worden gebruikt als een gegevens structuur archief in het geheugen, een gedistribueerde niet-relationele data base en een Message Broker. De prestaties van toepassingen worden verbeterd door gebruik te maken van de snelle gegevensdoorvoer met lage latentie van de Redis-engine.

Azure cache voor redis biedt toegang tot een beveiligde, toegewezen redis-cache. Azure cache voor redis wordt beheerd door micro soft, gehost in Azure en is toegankelijk voor elke toepassing binnen of buiten Azure.

## <a name="using-azure-cache-for-redis"></a>Azure cache gebruiken voor redis

Azure cache voor redis verbetert de prestaties van toepassingen door algemene toepassings architectuur patronen te ondersteunen. Sommige van de meest voorkomende zijn de volgende:

| Patroon      | Beschrijving                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Data bases zijn vaak te groot om rechtstreeks in een cache te laden. Het is gebruikelijk om het [cache-gereserveerde](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) patroon te gebruiken om alleen gegevens in de cache te laden als dat nodig is. Wanneer het systeem wijzigingen in de gegevens aanbrengt, kan het systeem ook de cache bijwerken, die vervolgens wordt gedistribueerd naar andere clients. Daarnaast kan het systeem een verloop tijd instellen op gegevens of een verwijderings beleid gebruiken om gegevens updates in de cache te activeren.|
| [Inhoud in cache opslaan](cache-aspnet-output-cache-provider.md) | Veel webpagina's worden gegenereerd op basis van sjablonen die gebruikmaken van statische inhoud, zoals kopteksten, voet teksten en banners. Deze statische items mogen niet vaak worden gewijzigd. Het gebruik van een in-memory cache biedt snelle toegang tot statische inhoud vergeleken met back-gegevens opslag. Dit patroon vermindert de verwerkings tijd en server belasting, waardoor webservers sneller kunnen reageren. Zo kunt u het aantal servers beperken dat nodig is voor het verwerken van belasting. Azure cache voor redis biedt de redis-uitvoer cache provider voor de ondersteuning van dit patroon met ASP.NET.|
| [Gebruikerssessie opslaan in cache](cache-aspnet-session-state-provider.md) | Dit patroon wordt vaak gebruikt met winkel wagens en andere geschiedenis gegevens van gebruikers die een webtoepassing mogelijk wil koppelen aan gebruikers cookies. Te veel informatie opslaan in een cookie kan negatieve gevolgen hebben voor de prestaties als de cookie groter wordt en bij elke aanvraag wordt doorgegeven en gevalideerd. Een typische oplossing gebruikt de cookie als een sleutel om de gegevens in een Data Base op te vragen. Het gebruik van een cache in het geheugen zoals Azure Cache voor Redis om gegevens te koppelen aan een gebruiker, is veel sneller dan interactie met een volledige relationele database. |
| Wachtrij met taken en berichten | Toepassingen voegen vaak taken toe aan een wachtrij wanneer de bewerkingen die zijn gekoppeld aan de aanvraag tijd duren om uit te voeren. Meer actieve bewerkingen worden in de wachtrij geplaatst om te worden verwerkt in de volg orde, vaak door een andere server.  Deze methode van werk uitstellen heet taken in de wachtrij plaatsen. Azure cache voor redis biedt een gedistribueerde wachtrij waarmee dit patroon in uw toepassing kan worden ingeschakeld.|
| Gedistribueerde transacties | Toepassingen vereisen soms een reeks opdrachten voor een back-end-gegevens opslag die als één atomische bewerking moet worden uitgevoerd. Alle opdrachten moeten slagen of alle moet worden teruggezet naar de beginstatus. Azure cache voor redis ondersteunt het uitvoeren van een batch-opdracht als één [trans actie](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Aanbiedingen voor Azure Cache voor Redis

Azure Cache voor Redis is beschikbaar in de volgende lagen:

| Laag | Beschrijving |
|---|---|
Basic | Een cache met één knooppunt. Deze laag ondersteunt meerdere geheugen grootten (250 MB-53 GB) en is ideaal voor ontwikkelings-en test-en niet-essentiële workloads. De Basic-laag heeft geen SLA (Service Level Agreement) |
| Standard | Een gerepliceerde cache in een configuratie met twee knoop punten, primair/secundair, die wordt beheerd door Azure met een SLA met hoge Beschik baarheid (99,9%) |
| Premium | De Premium-laag is de op ondernemings niveau geschikte laag. Caches op de Premium-laag ondersteunen meer functies en hebben een hogere doorvoersnelheid met een lagere latentie. Caches op de Premium-laag worden geïmplementeerd op krachtigere hardware en bieden daarom betere prestaties in vergelijking met de Basic- of Standard-laag. Dit voor deel betekent dat de door Voer voor een cache met dezelfde grootte hoger is in de Premium-waarde ten opzichte van de Standard-laag. |

> [!TIP]
> Raadpleeg [Veelgestelde vragen over Azure Cache voor Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) voor meer informatie over de grootte, doorvoer en bandbreedte van premiumcaches.
>

U kunt uw cache schalen tot een hogere laag nadat deze is gemaakt. Omlaag schalen naar een lagere laag wordt niet ondersteund. Zie voor stapsgewijze instructies [De schaal aanpassen van Azure Cache voor Redis](cache-how-to-scale.md) en [Een schaalbewerking automatiseren](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Vergelijking van functies

De pagina [Prijzen van Azure Cache voor Redis](https://azure.microsoft.com/pricing/details/cache/) bevat een gedetailleerde vergelijking van elke laag. In de volgende tabel worden de functies beschreven die door elke laag worden ondersteund:

| Omschrijving | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Service Level Agreement (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis-gegevenspersistentie](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis-cluster](cache-how-to-premium-clustering.md) |✔|-|-|
| [Beveiliging via de firewallregels](cache-configure.md#firewall) |✔|✔|✔|
| Versleuteling in transit |✔|✔|✔|
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
