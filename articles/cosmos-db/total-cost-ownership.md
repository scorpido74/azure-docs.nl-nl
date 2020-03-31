---
title: Total Cost of Ownership (TCO) met Azure Cosmos DB
description: In dit artikel worden de totale eigendomskosten van Azure Cosmos DB vergeleken met IaaS- en on-premises databases
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: b24b69716e472082abfdb388e7d79e88a8e23e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754793"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Total Cost of Ownership (TCO) met Azure Cosmos DB

Azure Cosmos DB is ontworpen met verfijnde multitenancy en verfijnd resourcebeheer Vanwege dit ontwerp kost werken met Azure Cosmos DB aanzienlijk minder, waardoor gebruikers geld besparen. Momenteel biedt Azure Cosmos DB ondersteuning voor meer dan 280 workloads van klanten op één computer, waarbij de dichtheid voortdurend toeneemt, en voor duizenden workloads van klanten binnen een cluster. Het zorgt voor een taakverdeling van replica’s voor de workloads van klanten op verschillende computers in een cluster, en in verschillende clusters binnen een datacenter. Zie [Azure Cosmos DB: De grens van wereldwijd gedistribueerde databases verleggen](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/)voor meer informatie. Vanwege resource-governance, multi-tenancy en native integratie met de rest van Azure-infrastructuur is Azure Cosmos DB gemiddeld 4 tot 6 keer goedkoper dan MongoDB, Cassandra of andere OSS NoSQL die op IaaS wordt uitgevoerd en tot 10 keer goedkoper is dan de database motoren die op het terrein draaien. Zie het document over [de totale kosten van (niet) eigendom van een NoSQL-databasecloudservice.](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)

De OSS NoSQL database oplossingen, zoals Apache Cassandra, MongoDB, HBase, engines zijn ontworpen voor on-premises. Wanneer ze worden aangeboden als een beheerde service, zijn ze gelijk aan een Resource Manager-sjabloon met een tenantdatabase voor het beheren van de ingerichte clusters en monitoringondersteuning. OSS NoSQL-architecturen vereisen aanzienlijke operationele overhead en de expertise kan moeilijk en duur te vinden zijn. Aan de andere kant is Azure Cosmos DB een volledig beheerde cloudservice, waarmee ontwikkelaars zich kunnen richten op bedrijfsinnovatie in plaats van op het beheren en onderhouden van database-infrastructuur. 

In tegenstelling tot een cloud-native databaseservice Azure Cosmos DB, zijn OSS NoSQL-databaseengines niet ontworpen en gebouwd met de resourcegovernance of fijnmazige multi-tenancy als de fundamentele architecturale principes. OSS NoSQL database engines zoals Cassandra en MongoDB maken een fundamentele veronderstelling dat alle middelen van de virtuele machine waarop ze draaien beschikbaar zijn voor gebruik. Veel van deze databaseengines kunnen niet functioneren als de hoeveelheid resources onder een bepaalde drempelwaarde zakt. Bijvoorbeeld voor kleine VM-exemplaren en deze zijn beschikbaar met door leveranciers aanbevolen configuraties die doorgaans grootschalige VM's met hogere kosten suggereren. Het is dus niet mogelijk om een OSS NoSQL of een andere on-premises databaseengine te hosten en beschikbaar te stellen met behulp van een op verbruik gebaseerd laadmodel zoals aanvragen per seconde of verbruikte opslag.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Totale eigendomskosten van Azure Cosmos DB 

Het serverloze inrichtingsmodel van Azure Cosmos DB elimineert de noodzaak om de database-infrastructuur te overprovisioneren. Azure Cosmos DB-resources worden geleverd zonder gespecialiseerde configuraties of licenties. Als gevolg hiervan kunnen de Azure Cosmos DB-backed-toepassingen worden uitgevoerd met maar liefst 70 procent Totale eigendomskostenbesparing in vergelijking met OSS NoSQL-databases. Voor sommige real-time voorbeelden, zie [customer use-cases](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Andere voordelen van het Azure Cosmos DB-prijsmodel zijn:

* **Geweldige waarde voor de prijs:** Marktanalisten, klanten en partners hebben een grotere waarde bevestigd van alle functies die Azure Cosmos DB biedt voor een veel lagere prijs in vergelijking met wat klanten kunnen krijgen bij het implementeren van deze oplossingen op hun eigen of via andere leveranciers. De database beschikt over dergelijke wereldwijde distributie, multi-master, goed gedefinieerde en intuïtieve consistentie modellen, automatische indexering zijn sterk vereenvoudigd met Azure Cosmos DB zonder enige complexiteit, overhead, of downtime.

* **NoSQL DevOps-beheer is niet vereist:** Met Azure Cosmos DB hoeft men DevOps niet te gebruiken voor het beheren van implementaties, het uitvoeren van onderhoud, schaal of patch. U alle workloads uitvoeren die u zou doen met OSS NoSQL-cluster gehost on-premises of op cloud-infrastructuur.

![Azure Cosmos DB-eigendomskosten](./media/total-cost-ownership/tco.png)

* **Mogelijkheid om elastisch te schalen:** Azure Cosmos DB-doorvoer kan op en neer worden geschaald, zodat u de eigendomskosten tijdens niet-piekuren verlagen. OSS NoSQL-clusters die zijn geïmplementeerd op cloudinfrastructuur bieden beperkte elasticiteit en on-premises implementaties zijn per definitie niet elastisch. Als u in Azure Cosmos DB meer doorvoer indient, wordt de doorvoer gegarandeerd lineair geschaald. Deze garantie wordt ondersteund door financiële SLA's en op het 99e percentiel op elke schaal.

* **Schaalvoordelen:** Een beheerde service zoals Azure Cosmos DB werkt met een groot aantal knooppunten, native geïntegreerd met netwerken, opslag en computes. Dankzij de grootschalige grootschalige standaardisatie van Azure Cosmos DB u kosten besparen.

* **Geoptimaliseerd voor de cloud:** Azure Cosmos DB is vanaf de grond ontworpen met fijnkorrelige multi-huur- en prestatieisolatie. Dit maakt het mogelijk om duizenden tenants en hun workloads tussen clusters en datacenters optimaal te plaatsen, uit te voeren en te balanceren. De huidige generatie OSS NoSQL-databases werkt daarentegen on-premises met de volledige virtuele machine waarvan wordt aangenomen dat de werkbelasting van één tenant wordt uitgevoerd. Deze databases zijn ook niet ontworpen om de infrastructuur en hardware van een cloudprovider optimaal te benutten. Een OSS NoSQL-databaseengine is zich bijvoorbeeld niet bewust van de verschillen tussen een virtuele machine die down is vs een routine-image-upgrade, of het feit dat de premium schijf al drierichtingsgerepliceerd is. Het kan niet profiteren van deze voordelen en de voordelen en besparingen doorgeven aan klanten.

* **U betaalt per uur:** Voor grootschalige workloads, die op elk moment moeten worden opgeschaald, worden er alleen per uur kosten in rekening gebracht. De workloads op een toepassing variëren meestal per keer van het jaar en per query. Met Azure Cosmos DB u omhoog of omlaag schalen als u dat nodig hebt en alleen betalen voor wat u nodig hebt. Met on-premises of IaaS-gehoste systemen u dit model niet evenaren, omdat er geen manier is om de hardware elk uur buiten gebruik te stellen. In dergelijke gevallen u gemiddeld tussen de 10 en 14 keer besparen met Azure Cosmos DB.

* **Je krijgt tal van functies gratis:** In Azure Cosmos DB zijn schrijfworkloads aanzienlijk goedkoper in vergelijking met alternatieve databaseservices. Daarnaast biedt Azure Cosmos DB functies zoals [automatische indexering](indexing-policies.md), Time to [Live (TTL),](time-to-live.md) [Change Feed](change-feed.md) en anderen zonder extra kosten, iets dat andere databaseservices doorgaans in rekening brengen.

* **Gebruikt uniforme valuta voor diverse workloads:** In tegenstelling tot alternatieve aanbiedingen hoeft u in Azure Cosmos DB workloads niet te segmenteren, bijvoorbeeld in lezen en schrijven. Of provisionthroughput op een type per workload dat leesdoorvoer versus schrijfdoorvoer is. In Azure Cosmos DB is de ingerichte doorvoer gereserveerd met behulp van een uniforme en genormaliseerde valuta in termen van aanvraageenheden of RU/sec. Azure Cosmos DB dwingt u geen prioriteit toe te wijzen aan uw workloads, capaciteitsplanning uit te voeren of afzonderlijk te betalen voor elk type capaciteit. Een dergelijke aanpak stelt u in staat om eenvoudig dezelfde RU/s tussen verschillende bewerkingen en werkbelastingtypen uit te wisselen.

* **Hiervoor hoeft u vm's niet op te schalen:** De meeste operationele databases vereisen dat u met grote virtuele machines gaat om luidruchtige buren te vermijden en voor losse resource governance, als u schaal wilt. Dit legt de last en de upfront inzet van de kosten op de klanten. Met Azure Cosmos DB u klein beginnen en naadloos uitgroeien tot de grootschalige workloadgroottes, zonder downtime of impact op de beschikbaarheid van gegevens.

* **U de ingerichte doorvoer maximaal gebruiken:** Door de sub-core multiplexing in Azure Cosmos DB u de ingerichte doorvoer in grotere mate verzadigen dan iaas gehoste opties of aanbiedingen van derden. Deze methode bespaart veel meer dan de alternatieve oplossingen.

* **Diepgaande integratie van Azure Cosmos DB met andere Azure-services.** Azure Cosmos DB heeft een native integratie met Networking, Compute, Azure Functions (serverless), Azure IoT en andere Azure-services. Met deze integratie krijgt u de beste prestaties, snelheid van gegevensreplicatie over de hele wereld met robuuste garanties. De oplossingen van derden kunnen niet overeenkomen of brengen doorgaans een premie in rekening om dergelijke functies aan te bieden.

* **U krijgt automatisch een hoge beschikbaarheid, met ten minste 10-20 fout domeinen standaard:** Azure Cosmos DB ondersteunt de verdeling van workloads over foutdomeinen, een functie die essentieel is voor hoge beschikbaarheid. Het biedt 99.999 hoge beschikbaarheid voor leest en schrijft op het 99e percentiel over overal in de wereld. De kosten van het implementeren van iets als dit op uw eigen of via een oplossing van derden, zou hoog zijn.

* **U krijgt automatisch alle bedrijfsmogelijkheden, zonder extra kosten.** Azure Cosmos DB biedt de meest uitgebreide set compliance-certificeringen, beveiliging en versleuteling in rust en in beweging zonder extra kosten (in vergelijking met onze concurrentie). U krijgt automatisch overal ter wereld regionale beschikbaarheid. U uw database over een willekeurig aantal Azure-regio's heen slopen en op elk gewenst moment regio's toevoegen of verwijderen.

* **U tot 65% van de kosten besparen met gereserveerde capaciteit:** Met [de gereserveerde capaciteit van](cosmos-db-reserved-capacity.md) Azure Cosmos DB u geld besparen door een jaar of drie vooraf te betalen voor Azure Cosmos DB-resources. U uw kosten aanzienlijk verlagen met een jaar of drie jaar vooraf toezeggingen en bespaar tussen de 20-65% kortingen in vergelijking met de reguliere prijzen. Op uw bedrijfskritieke workloads u betere SLA's krijgen op het gebied van inrichtingscapaciteit.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [hoe azure cosmos DB-prijsmodel kosteneffectief is voor klanten](total-cost-ownership.md)
* Meer informatie over [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [het optimaliseren van doorvoerkosten](optimize-cost-throughput.md)
* Meer informatie over [het optimaliseren van opslagkosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lezen en schrijven](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](optimize-cost-queries.md)
* Meer informatie over [het optimaliseren van de kosten van multi-region Cosmos-accounts](optimize-cost-regions.md)
* Meer informatie over [de totale kosten van (niet)eigendom van een NoSQL Database Cloud Service](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
