---
title: Limieten van Azure Cosmos DB Gremlin
description: Referentie documentatie voor runtime beperkingen van Graph engine
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: f1fe4cfac22a651f44338986d0a767fe3e1a360b
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911086"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limieten voor Azure Cosmos DB Gremlin
In dit artikel vindt u informatie over de limieten van Azure Cosmos DB Gremlin-engine en wordt uitgelegd hoe deze van invloed kunnen zijn op klant navigatie.

Cosmos DB Gremlin is gebaseerd op Cosmos DB-infra structuur, waarom alle limieten in [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) nog steeds van toepassing zijn. 

## <a name="limits"></a>Limieten

Wanneer de limiet voor Gremlin is bereikt, wordt het door sturen geannuleerd met **x-MS-status-code** = 429, wat een beperkings fout aangeeft.

**Resource**    | **Standaardlimiet** | **Uitleg**
--- | --- | ---
*Geheugen per aanvraag* | **2 GB** | Maximale hoeveelheid geheugen die een aanvraag kan verbruiken tijdens de verwerking. Aanvragen die grote gegevens sets moeten berekenen, nemen extra geheugen in beslag. Overweeg het bereik van aanvragen naar kleinere gegevens sets om deze limiet te voor komen of om OLAP-oplossingen te gebruiken.
*Script lengte* | **64 kB** | Maximale lengte van een Gremlin-doorkruisingsscript per aanvraag.
*Diepte van operator* | **400** |  Totaal aantal unieke stappen in een doorkruising. ```g.V().out()``` Heeft bijvoorbeeld het aantal opera tors van 2: V () en out () ```g.V('label').repeat(out()).times(100)``` heeft een operator diepte van 3: V (), Repeat () en out () omdat ```.times(100)``` dit een para meter voor ```.repeat()``` de operator is.
*Mate van parallelle uitvoering* | **32** | Maximum aantal opslagpartities dat in één aanvraag naar de opslaglaag wordt bevraagd. Grafieken met honderden partities worden beïnvloed door deze limiet.
*Herhalings limiet* | **32** | Maximum aantal iteraties dat een ```.repeat()```-operator kan uitvoeren. ```.repeat()``` Elke stap in de meeste gevallen voert de breedte-eerste-passage uit, wat betekent dat alle navigatie is beperkt tot Maxi maal 32 hops tussen hoek punten.
*Time-out voor door lopen* | **30 seconden** | Door lopen wordt geannuleerd wanneer deze de tijd overschrijdt. Cosmos DB Graph is een OLTP-database waar de meeste doorkruisingen binnen enkele milliseconden worden uitgevoerd. Als u OLAP-query's wilt uitvoeren op Cosmos DB grafiek, gebruikt u [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) met [grafiek gegevens frames](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) en [Cosmos DB Spark-connector](https://github.com/Azure/azure-cosmosdb-spark).
*Limiet voor predikaten* | **20** | Aantal ```.has()```- of ```.hasNot()```-stappen dat wordt toegepast op één hoekpunt of rand. Wanneer deze limiet wordt bereikt, wordt de fout ```The SQL query exceeded the maximum number of joins. The allowed limit is 20``` in de toepassing weergegeven. Het is een tijdelijk ongemak dat team werkt om deze limiet op te heffen. 
*Time-out voor niet-actieve verbinding* | **5 uur** | Hoeveelheid tijd dat de WebSocket-verbinding door de grafiek server wordt geopend zonder verkeer. TCP Keep-Alive-pakketten of HTTP Keep-Alive-aanvragen breiden de duur van de verbinding niet verder uit dan deze limiet, maar als ze niet worden verzonden, kan de verbinding nog sneller worden gesloten. Cosmos DB Graph-engine wordt beschouwd als inactief als er geen Gremlin-passages worden uitgevoerd.
*Bron Token per uur* | **100** | Het aantal unieke bron tokens dat door Gremlin-clients wordt gebruikt om verbinding te maken met het Gremlin-account in een regio. Wanneer de toepassing een unieke token limiet van elk uur `"Exceeded allowed resource token limit of 100 that can be used concurrently"` overschrijdt, wordt de volgende verificatie aanvraag geretourneerd.

## <a name="next-steps"></a>Volgende stappen
* [Gremlin-antwoord headers Azure Cosmos DB](gremlin-headers.md) 
* [Azure Cosmos DB bron tokens met Gremlin](how-to-use-resource-tokens-gremlin.md)
