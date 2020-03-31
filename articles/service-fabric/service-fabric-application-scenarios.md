---
title: Toepassingsscenario's en ontwerp
description: Overzicht van categorieën cloudtoepassingen in Service Fabric. Bespreekt toepassingsontwerp dat stateful en stateless services gebruikt.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024737"
---
# <a name="service-fabric-application-scenarios"></a>Scenario's voor servicefabric-toepassingen

Azure Service Fabric biedt een betrouwbaar en flexibel platform waar u veel soorten bedrijfstoepassingen en -services schrijven en uitvoeren. Deze toepassingen en microservices kunnen stateloos of stateful zijn en zijn resource-balanced voor virtuele machines om de efficiëntie te maximaliseren.

De unieke architectuur van Service Fabric stelt u in staat om bijna realtime gegevensanalyse, in-memory berekening, parallelle transacties en gebeurtenisverwerking uit te voeren in uw toepassingen. U uw toepassingen eenvoudig in- of uitschalen, afhankelijk van uw veranderende resourcevereisten.

Lees voor ontwerprichtlijnen voor het bouwen van toepassingen [de architectuur microservices op Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) en Aanbevolen procedures voor [toepassingsontwerp met Service Fabric.](service-fabric-best-practices-applications.md)

Overweeg het Service Fabric-platform te gebruiken voor de volgende typen toepassingen:

* **Gegevens verzamelen, verwerken en IoT**: Service Fabric verwerkt op grote schaal en heeft een lage latentie via zijn stateful services. Het kan helpen bij het verwerken van gegevens op miljoenen apparaten waar de gegevens voor het apparaat en de berekening zijn samengeplaatst.

    Klanten die IoT-diensten hebben gebouwd met behulp van Service Fabric zijn [honeywell,](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure) [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron,](https://customers.microsoft.com/story/crestron-partner-professional-services-azure) [BMW,](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/) [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)en [Mesh Systems.](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)

* **Gaming en sessiegebaseerde interactieve toepassingen**: Service Fabric is handig als uw toepassing lezen en schrijven met een lage latentie vereist, zoals in online gaming of instant messaging. Met Service Fabric u deze interactieve, stateful toepassingen bouwen zonder dat u een aparte winkel of cache hoeft te maken. Ga naar [Azure-gamingoplossingen](https://azure.microsoft.com/solutions/gaming/) voor ontwerprichtlijnen voor [het gebruik van Service Fabric in gamingservices.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Klanten die gaming diensten hebben gebouwd zijn [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) en [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Klanten die interactieve sessies hebben gebouwd, zijn [honeywell met Hololens.](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Gegevensanalyse en workflowverwerking:** toepassingen die gebeurtenissen of gegevensstromen betrouwbaar moeten verwerken, profiteren van de geoptimaliseerde lees- en schrijfbewerkingen in Service Fabric. Service Fabric ondersteunt ook toepassingsverwerkingspijplijnen, waarbij de resultaten betrouwbaar moeten zijn en zonder verlies moeten worden doorgegeven aan de volgende verwerkingsfase. Deze pijplijnen omvatten transactionele en financiële systemen, waarbij gegevensconsistentie en berekeningsgaranties essentieel zijn.

    Klanten die zakelijke workflowservices hebben opgebouwd, zijn [Zeiss Group,](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)en Société [General.](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)

* **Berekening op gegevens:** Met Service Fabric u stateful toepassingen bouwen die intensieve gegevensberekening uitvoeren. Service Fabric maakt de colocatie van verwerking (berekening) en gegevens in toepassingen mogelijk. 

   Normaal gesproken beperkt de berekeningstijd wanneer uw toepassing toegang tot gegevens vereist, netwerklatentie die is gekoppeld aan een externe gegevenscache of opslaglaag. Stateful Service Fabric-services elimineren die latentie, waardoor meer geoptimaliseerde lees- en schrijfbewerkingen mogelijk zijn.

   Overweeg bijvoorbeeld een toepassing die bijna realtime aanbevelingsselecties voor klanten uitvoert, met een retourtijdvereiste van minder dan 100 milliseconden. De latentie en prestatiekenmerken van Service Fabric-services bieden de gebruiker een responsieve ervaring, vergeleken met het standaardimplementatiemodel dat de benodigde gegevens uit externe opslag moeten worden opgehaald. Het systeem reageert sneller omdat de berekening van de aanbevelingselectie wordt gekoppeld aan de gegevens en regels.

    Klanten die rekenservices hebben gebouwd, zijn [solidsoft reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) en [infosupport.](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Beschikbaar services:** Service Fabric biedt een snelle failover door meerdere secundaire servicereplica's te maken. Als een knooppunt, proces of afzonderlijke service uitvalt als gevolg van hardware of andere fouten, wordt een van de secundaire replica's gepromoot naar een primaire replica met minimaal serviceverlies.

* **Schaalbare services**: Afzonderlijke services kunnen worden verdeeld, zodat de status over het cluster kan worden geschaald. Individuele diensten kunnen ook worden gemaakt en verwijderd op de vlieg. U services uitschalen van een paar instanties op een paar knooppunten naar duizenden exemplaren op veel knooppunten en deze vervolgens opnieuw schalen als dat nodig is. U Service Fabric gebruiken om deze services te bouwen en hun volledige levenscyclus te beheren.

## <a name="application-design-case-studies"></a>Casestudies voor toepassingsontwerp

Casestudies die laten zien hoe Service Fabric wordt gebruikt om toepassingen te ontwerpen, worden gepubliceerd op de [verhalen van](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) klanten en [Microservices in Azure-sites.](https://azure.microsoft.com/solutions/microservice-applications/)

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Ontwerpen van applicaties die bestaan uit stateless en stateful microservices

Het bouwen van toepassingen met Azure Cloud Services-werknemersrollen is een voorbeeld van een stateless service. Stateful microservices behouden daarentegen hun gezaghebbende status buiten het verzoek en de reactie ervan. Deze functionaliteit biedt een hoge beschikbaarheid en consistentie van de status door middel van eenvoudige API's die transactionele garanties bieden die worden ondersteund door replicatie.

Stateful services in Service Fabric bieden een hoge beschikbaarheid voor alle soorten toepassingen, niet alleen databases en andere gegevensopslag. Dit is een natuurlijke progressie. Toepassingen zijn al overgestapt van het gebruik van zuiver relationele databases voor hoge beschikbaarheid naar NoSQL-databases. Nu kunnen de toepassingen zelf hun "hete" status en gegevens binnen hen beheren voor extra prestatiewinst zonder in te boeten aan betrouwbaarheid, consistentie of beschikbaarheid.

Wanneer u toepassingen bouwt die bestaan uit microservices, hebt u meestal een combinatie van stateless web-apps (zoals ASP.NET en Node.js) die bellen naar stateloze en stateful zakelijke middle-tier services. De apps en services worden allemaal geïmplementeerd in hetzelfde cluster van Service Fabric via de implementatieopdrachten van Service Fabric. Elk van deze services is onafhankelijk met betrekking tot schaal, betrouwbaarheid en resourcegebruik. Deze onafhankelijkheid verbetert de wendbaarheid en flexibiliteit in ontwikkeling en levenscyclusmanagement.

Stateful microservices vereenvoudigen toepassingsontwerpen omdat ze de noodzaak voor de extra wachtrijen en caches die traditioneel nodig zijn om de beschikbaarheid en latentievereisten van zuiver stateless toepassingen aan te pakken, verwijderen. Omdat stateful services een hoge beschikbaarheid en lage latentie hebben, zijn er minder details te beheren in uw toepassing.

De volgende diagrammen illustreren de verschillen tussen het ontwerpen van een toepassing die stateloos is en een toepassing die stateful is. Door gebruik te maken van de [programmeermodellen Reliable Services](service-fabric-reliable-services-introduction.md) en [Reliable Actors](service-fabric-reliable-actors-introduction.md) verminderen stateful services de complexiteit van toepassingen en bereiken ze een hoge doorvoer en lage latentie.

Hier is een voorbeeldtoepassing die stateless services gebruikt: ![toepassing die stateloze services gebruikt][Image1]

Hier is een voorbeeldtoepassing die gebruikmaakt van stateful services: ![toepassing die stateful services gebruikt][Image2]

## <a name="next-steps"></a>Volgende stappen

* Ga aan de slag met het bouwen van stateloze en stateful services met de programmeermodellen Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) en [Reliable Actors.](service-fabric-reliable-actors-get-started.md)
* Ga naar het Azure Architecture Center voor richtlijnen voor [het bouwen van microservices op Azure.](https://docs.microsoft.com/azure/architecture/microservices/)
* Ga naar [Azure Service Fabric-toepassing en clusteraanbevolen procedures](service-fabric-best-practices-overview.md) voor richtlijnen voor het ontwerpen van toepassingen.

* Zie ook:
  * [Uitleg over microservices](service-fabric-overview-microservices.md)
  * [Servicestatus definiëren en beheren](service-fabric-concepts-state.md)
  * [Beschikbaarheid van services](service-fabric-availability-services.md)
  * [Schaalservices](service-fabric-concepts-scalability.md)
  * [Partitieservices](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
