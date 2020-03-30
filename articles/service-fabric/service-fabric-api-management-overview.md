---
title: Azure Service Fabric met API-beheeroverzicht
description: Dit artikel is een inleiding tot het gebruik van Azure API Management als gateway voor uw Service Fabric-toepassingen.
author: vturecek
ms.topic: conceptual
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 2a331715d4e4538cfdda8d958ff549a81b627b79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028545"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Overzicht van Service Fabric met Azure API Management

Cloudtoepassingen hebben meestal een gateway in de front-end nodig om een centraal ingangspunt te bieden voor gebruikers, apparaten of andere toepassingen. In Service Fabric kan een gateway elke stateloze service zijn, zoals een [ASP.NET Core-toepassing](service-fabric-reliable-services-communication-aspnetcore.md)of een andere service die is ontworpen voor verkeersinformatie, zoals [Gebeurtenishubs,](https://docs.microsoft.com/azure/event-hubs/) [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)of [Azure API-beheer.](https://docs.microsoft.com/azure/api-management/)

Dit artikel is een inleiding tot het gebruik van Azure API Management als gateway voor uw Service Fabric-toepassingen. API Management integreert rechtstreeks met Service Fabric, zodat u API's publiceren met een uitgebreide set routeringsregels naar uw back-end Service Fabric-services.

## <a name="availability"></a>Beschikbaarheid

> [!IMPORTANT]
> Deze functie is beschikbaar in de **premium-** en **ontwikkelaarsniveaus** van API-beheer vanwege de vereiste ondersteuning voor virtueel netwerk.

## <a name="architecture"></a>Architectuur

Een gemeenschappelijke Service Fabric-architectuur maakt gebruik van een webtoepassing met één pagina waarmee HTTP-aanroepen naar back-endservices worden gemaakt die HTTP-API's blootleggen. De [voorbeeldtoepassing Service Fabric die aan de slag](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) gaat, toont een voorbeeld van deze architectuur.

In dit scenario fungeert een stateloze webservice als gateway naar de Service Fabric-toepassing. Deze benadering vereist dat u een webservice schrijft die HTTP-aanvragen kan proxyn voor back-endservices, zoals in het volgende diagram wordt weergegeven:

![Overzicht van de topologie van Service Fabric met Azure API Management][sf-web-app-stateless-gateway]

Naarmate toepassingen in complexiteit groeien, nemen ook de gateways toe die een API moeten presenteren voor talloze back-endservices. Azure API-beheer is ontworpen om complexe API's te verwerken met routeringsregels, toegangscontrole, beperking van de snelheid, bewaking, logboekregistratie van gebeurtenissen en het plaatsen van reacties met minimale werkzaamheden van uw kant. Azure API Management ondersteunt Service Fabric-servicedetectie, partitieresolutie en replicaselectie om aanvragen op intelligente wijze rechtstreeks door te sturen naar back-endservices in Service Fabric, zodat u uw eigen stateless API-gateway niet hoeft te schrijven. 

In dit scenario wordt de webgebruikersinterface nog steeds weergegeven via een webservice, terwijl HTTP API-aanroepen worden beheerd en doorgestuurd via Azure API Management, zoals weergegeven in het volgende diagram:

![Overzicht van de topologie van Service Fabric met Azure API Management][sf-apim-web-app]

## <a name="application-scenarios"></a>Toepassingsscenario's

Services in Service Fabric kunnen stateloos of stateful zijn, en ze kunnen worden verdeeld met behulp van een van de drie schema's: singleton, int-64 bereik, en met de naam. Voor het oplossen van serviceeindpunten moet een specifieke partitie van een specifieke service-instantie worden geïdentificeerd. Bij het oplossen van een eindpunt van een service moeten `fabric:/myapp/myservice`zowel de naam van de serviceinstantie (bijvoorbeeld ) als de specifieke partitie van de service worden opgegeven, behalve in het geval van singleton partitie.

Azure API Management kan worden gebruikt met elke combinatie van stateless services, stateful services en elk partitioneringsschema.

## <a name="send-traffic-to-a-stateless-service"></a>Verkeer verzenden naar een staatloze service

In het eenvoudigste geval wordt het verkeer doorgestuurd naar een serviceinstantie zonder staat. Om dit te bereiken, bevat een API Management-bewerking een binnenkomend verwerkingsbeleid met een Back-end servicefabric dat wordt toegewezen aan een specifieke stateless service-instantie in de back-end van Service Fabric. Verzoeken die naar die service worden verzonden, worden naar een willekeurige instantie van de service verzonden.

**Voorbeeld**

In het volgende scenario bevat een Service Fabric-toepassing een statusloze service met de naam `fabric:/app/fooservice`, die een interne HTTP-API blootlegt. De naam van de service-instantie is bekend en kan direct worden gecodeerd in het inbound processing-beleid van API-beheer. 

![Overzicht van de topologie van Service Fabric met Azure API Management][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Verkeer naar een stateful service verzenden

Net als bij het statusloze servicescenario kan verkeer worden doorgestuurd naar een stateful service-instantie. In dit geval bevat een API-beheerbewerking een binnenkomend verwerkingsbeleid met een back-end van ServiceFabric waarmee een aanvraag wordt toegewezen aan een specifieke partitie van een specifieke *stateful* service-instantie. De partitie om elk verzoek in kaart te brengen, wordt berekend via een lambdamethode met behulp van enige invoer uit het binnenkomende HTTP-verzoek, zoals een waarde in het URL-pad. Het beleid kan worden geconfigureerd om alleen aanvragen naar de primaire replica of naar een willekeurige replica voor leesbewerkingen te verzenden.

**Voorbeeld**

In het volgende scenario bevat een Service Fabric-toepassing `fabric:/app/userservice` een partitieservice met de naam die een interne HTTP-API blootlegt. De naam van de service-instantie is bekend en kan direct worden gecodeerd in het inbound processing-beleid van API-beheer.  

De service wordt verdeeld met behulp van het Int64-partitieschema `Int64.MinValue` met `Int64.MaxValue`twee partities en een sleutelbereik dat zich uitstrekt tot . Het back-endbeleid berekent een partitiesleutel binnen `id` dat bereik door de waarde in het URL-aanvraagpad om te zetten naar een 64-bits geheel getal, hoewel elk algoritme hier kan worden gebruikt om de partitiesleutel te berekenen. 

![Overzicht van de topologie van Service Fabric met Azure API Management][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Verkeer verzenden naar meerdere staatloze services

In meer geavanceerde scenario's u een API-beheerbewerking definiëren waarmee aanvragen worden toegewezen aan meer dan één serviceinstantie. In dit geval bevat elke bewerking een beleid dat aanvragen toewijst aan een specifieke service-instantie op basis van waarden uit de binnenkomende HTTP-aanvraag, zoals het URL-pad of de querytekenreeks, en in het geval van stateful services een partitie binnen de service-instantie.

Om dit te bereiken, bevat een API Management-bewerking een binnenkomend verwerkingsbeleid met een back-end van Service Fabric dat wordt toegewezen aan een stateless service-instantie in de back-end van ServiceFabric op basis van waarden die zijn opgehaald uit de binnenkomende HTTP-aanvraag. Aanvragen voor een service worden verzonden naar een willekeurige instantie van de service.

**Voorbeeld**

In dit voorbeeld wordt een nieuwe stateless service-instantie gemaakt voor elke gebruiker van een toepassing met een dynamisch gegenereerde naam met behulp van de volgende formule:

- `fabric:/app/users/<username>`

  Elke service heeft een unieke naam, maar de namen zijn niet van tevoren bekend omdat de services zijn gemaakt in reactie op gebruikers- of beheerdersinvoer en dus niet hard gecodeerd kunnen worden in APIM-beleid of routeringsregels. In plaats daarvan wordt de naam van de service die een aanvraag `name` moet verzenden gegenereerd in de back-endbeleidsdefinitie van de waarde die wordt opgegeven in het URL-aanvraagpad. Bijvoorbeeld:

  - Een verzoek `/api/users/foo` om te worden doorgestuurd naar service-instantie`fabric:/app/users/foo`
  - Een verzoek `/api/users/bar` om te worden doorgestuurd naar service-instantie`fabric:/app/users/bar`

![Overzicht van de topologie van Service Fabric met Azure API Management][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Verkeer verzenden naar meerdere stateful services

Net als bij het stateless servicevoorbeeld kan een API-beheerbewerking aanvragen toewijzen aan meer dan één **stateful** service-instantie, in welk geval u mogelijk ook partitieresolutie moet uitvoeren voor elke stateful service-instantie.

Om dit te bereiken, bevat een API Management-bewerking een binnenkomend verwerkingsbeleid met een back-end van Service Fabric dat wordt toegewezen aan een stateful service-exemplaar in de back-end van ServiceFabric op basis van waarden die zijn opgehaald uit de binnenkomende HTTP-aanvraag. Naast het toewijzen van een aanvraag aan specifieke service-instantie, kan de aanvraag ook worden toegewezen aan een specifieke partitie binnen de service-instantie, en optioneel aan de primaire replica of een willekeurige secundaire replica binnen de partitie.

**Voorbeeld**

In dit voorbeeld wordt een nieuwe stateful service-instantie gemaakt voor elke gebruiker van de toepassing met een dynamisch gegenereerde naam met behulp van de volgende formule:

- `fabric:/app/users/<username>`

  Elke service heeft een unieke naam, maar de namen zijn niet van tevoren bekend omdat de services zijn gemaakt in reactie op gebruikers- of beheerdersinvoer en dus niet hard gecodeerd kunnen worden in APIM-beleid of routeringsregels. In plaats daarvan wordt de naam van de service die een aanvraag `name` moet verzenden gegenereerd in de back-endbeleidsdefinitie van de waarde op voorwaarde dat het URL-aanvraagpad wordt verstrekt. Bijvoorbeeld:

  - Een verzoek `/api/users/foo` om te worden doorgestuurd naar service-instantie`fabric:/app/users/foo`
  - Een verzoek `/api/users/bar` om te worden doorgestuurd naar service-instantie`fabric:/app/users/bar`

Elke serviceinstantie wordt ook verdeeld met behulp van het Int64-partitieschema `Int64.MinValue` `Int64.MaxValue`met twee partities en een sleutelbereik dat zich uitstrekt tot . Het back-endbeleid berekent een partitiesleutel binnen `id` dat bereik door de waarde in het URL-aanvraagpad om te zetten naar een 64-bits geheel getal, hoewel elk algoritme hier kan worden gebruikt om de partitiesleutel te berekenen. 

![Overzicht van de topologie van Service Fabric met Azure API Management][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Volgende stappen

Volg de [zelfstudie](service-fabric-tutorial-deploy-api-management.md) om uw eerste Service Fabric-cluster in te stellen met API-beheer- en stroomaanvragen via API-beheer naar uw services.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
