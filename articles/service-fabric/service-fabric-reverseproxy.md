---
title: Omgekeerde proxy azure servicefabric
description: Gebruik de omgekeerde proxy van Service Fabric voor communicatie naar microservices van binnen en buiten het cluster.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 4fa4c6e46dd786b833087f892d995e85b5d2ea47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282222"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Omgekeerde proxy in Azure Service Fabric
Reverse proxy die is ingebouwd in Azure Service Fabric helpt microservices te ontdekken en te communiceren met andere services met http-eindpunten.

## <a name="microservices-communication-model"></a>Communicatiemodel microservices
Microservices in Service Fabric worden uitgevoerd op een subset van knooppunten in het cluster en kunnen om verschillende redenen tussen de knooppunten migreren. Hierdoor kunnen de eindpunten voor microservices dynamisch veranderen. Om andere services in het cluster te ontdekken en te communiceren, moet microservice de volgende stappen doorlopen:

1. Los de servicelocatie op via de naamgevingsservice.
2. Maak verbinding met de service.
3. De voorgaande stappen in een lus om serviceoplossing en opnieuw te proberen beleidsregels te implementeren die moeten worden toegepast op verbindingsfouten

Zie [Verbinden en communiceren met services](service-fabric-connect-and-communicate-with-services.md)voor meer informatie.

### <a name="communicating-by-using-the-reverse-proxy"></a>Communiceren met behulp van de omgekeerde proxy
Reverse proxy is een service die op elk knooppunt wordt uitgevoerd en eindpuntomzetting, automatische opnieuw proberen en andere verbindingsfouten namens clientservices verwerkt. Reverse proxy kan worden geconfigureerd om verschillende beleidsregels toe te passen omdat het aanvragen van clientservices verwerkt. Met behulp van een omgekeerde proxy kan de clientservice http-communicatiebibliotheken aan clientzijde gebruiken en is geen speciale oplossing seinen en logica opnieuw proberen in de service vereist. 

Reverse proxy stelt een of meer eindpunten op lokaal knooppunt bloot voor clientservices die moeten worden gebruikt voor het verzenden van aanvragen naar andere services.

![Interne communicatie][1]

> [!NOTE]
> **Ondersteunde platforms**
>
> Reverse proxy in Service Fabric ondersteunt momenteel de volgende platforms
> * *Windows-cluster*: Windows 8 en hoger of Windows Server 2012 en hoger
> * *Linux Cluster*: Reverse Proxy is momenteel niet beschikbaar voor Linux clusters
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Microservices van buiten het cluster bereiken
Het standaard externe communicatiemodel voor microservices is een opt-in-model waarbij elke service niet rechtstreeks vanuit externe clients kan worden geopend. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), een netwerkgrens tussen microservices en externe clients, voert netwerkadresvertaling uit en stuurt externe aanvragen door naar interne IP:port-eindpunten. Als u het eindpunt van een microservice direct toegankelijk wilt maken voor externe clients, moet u load balancer eerst configureren om verkeer door te sturen naar elke poort die de service in het cluster gebruikt. Bovendien leven de meeste microservices, vooral stateful microservices, niet op alle knooppunten van het cluster. De microservices kunnen worden verplaatst tussen knooppunten bij failover. In dergelijke gevallen kan Load Balancer niet effectief de locatie bepalen van het doelknooppunt van de replica's waarnaar het verkeer moet worden doorgestuurd.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Microservices bereiken via de omgekeerde proxy van buiten het cluster
In plaats van de poort van een afzonderlijke service in Load Balancer te configureren, u alleen de poort van de omgekeerde proxy configureren in Load Balancer. Met deze configuratie kunnen clients buiten het clusterservices binnen het cluster bereiken met behulp van de omgekeerde proxy zonder extra configuratie.

![Externe communicatie][0]

> [!WARNING]
> Wanneer u de poort van de omgekeerde proxy configureert in Load Balancer, zijn alle microservices in het cluster die een HTTP-eindpunt blootleggen, adresseerbaar van buiten het cluster. Dit betekent dat microservices bedoeld om intern te zijn kan worden vindbaar door een bepaalde kwaadwillende gebruiker. Dit brengt mogelijk ernstige kwetsbaarheden met zich mee die kunnen worden misbruikt; bijvoorbeeld:
>
> * Een kwaadwillende gebruiker kan een denial of service-aanval starten door herhaaldelijk een interne service te bellen die geen voldoende gehard aanvalsoppervlak heeft.
> * Een kwaadwillende gebruiker kan misvormde pakketten leveren aan een interne service wat resulteert in onbedoeld gedrag.
> * Een dienst die bedoeld is als intern, kan privé- of gevoelige informatie retourneren die niet bedoeld is om te worden blootgesteld aan services buiten het cluster, waardoor deze gevoelige informatie wordt blootgesteld aan een kwaadwillende gebruiker. 
>
> Zorg ervoor dat u de mogelijke beveiligingsgevolgen voor uw cluster en de apps die erop worden uitgevoerd volledig begrijpt en vermindert, voordat u de omgekeerde proxypoort openbaar maakt. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>URI-indeling voor het adresseren van services met behulp van de omgekeerde proxy
De omgekeerde proxy gebruikt een specifieke uri-indeling (uniform resource identifier) om de servicepartitie te identificeren waaraan de binnenkomende aanvraag moet worden doorgestuurd:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s):** De omgekeerde proxy kan worden geconfigureerd om HTTP- of HTTPS-verkeer te accepteren. Raadpleeg voor HTTPS-forwarding [verbinding maken met een beveiligde service met de omgekeerde proxy](service-fabric-reverseproxy-configure-secure-communication.md) zodra u de reverse proxy-instelling hebt ingesteld om op HTTPS te luisteren.
* **Cluster volledig gekwalificeerde domeinnaam (FQDN) | intern IP:** Voor externe clients u de omgekeerde proxy zo configureren dat deze bereikbaar is via het clusterdomein, zoals mycluster.eastus.cloudapp.azure.com. Standaard wordt de omgekeerde proxy op elk knooppunt uitgevoerd. Voor intern verkeer kan de omgekeerde proxy worden bereikt op localhost of op een intern knooppunt-IP, zoals 10.0.0.1.
* **Haven:** Dit is de poort, zoals 19081, die is opgegeven voor de omgekeerde proxy.
* **ServiceInstanceName:** Dit is de volledig gekwalificeerde naam van het geïmplementeerde serviceexemplaar dat u probeert te bereiken zonder de 'fabric:/' Regeling. Bijvoorbeeld, om de stof te *bereiken:/myapp/myservice/* service, zou u *myapp/myservice*gebruiken.

    De naam van de service-instantie is hoofdlettergevoelig. Als u een andere behuizing gebruikt voor de naam van de serviceinstantie in de URL, mislukken de aanvragen met 404 (Niet gevonden).
* **Achtervoegselpad:** Dit is het werkelijke URL-pad, zoals *myapi/values/add/3,* voor de service waarmee u verbinding wilt maken.
* **PartitionKey:** Voor een partitieservice is dit de berekende partitiesleutel van de partitie die u wilt bereiken. Houd er rekening mee dat dit *niet* de GUID voor partitie-ID is. Deze parameter is niet vereist voor services die het singleton-partitieschema gebruiken.
* **PartitionKind:** Dit is het servicepartitieschema. Dit kan 'Int64Range' of 'Named' zijn. Deze parameter is niet vereist voor services die het singleton-partitieschema gebruiken.
* **ListenerNaam** De eindpunten van de service zijn van het formulier {"Eindpunten":{"Listener1":"Endpoint1",,"Listener2":"Endpoint2" ...}}. Wanneer de service meerdere eindpunten blootlegt, wordt het eindpunt aangegeven waarnaar de clientaanvraag moet worden doorgestuurd. Dit kan worden weggelaten als de service slechts één listener heeft.
* **TargetReplicaSelector** Hiermee geeft u aan hoe de doelreplica of -instantie moet worden geselecteerd.
  * Wanneer de doelservice stateful is, kan de TargetReplicaSelector een van de volgende zijn: 'PrimaryReplica', 'RandomSecondaryReplica' of 'RandomReplica'. Wanneer deze parameter niet is opgegeven, is de standaardinstelling 'PrimaryReplica'.
  * Wanneer de doelservice stateloos is, kiest reverse proxy een willekeurige instantie van de servicepartitie om de aanvraag naar door te sturen.
* **Time-out:**  Hiermee geeft u de time-out op voor de HTTP-aanvraag die is gemaakt door de omgekeerde proxy voor de service namens de clientaanvraag. De standaardwaarde is 60 seconden. Dit is een optionele parameter.

### <a name="example-usage"></a>Gebruiksvoorbeelden
Laten we bijvoorbeeld de *fabric:/MyApp/MyService-service* nemen die een HTTP-listener op de volgende URL opent:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Hieronder volgen de bronnen voor de service:

* `/index.html`
* `/api/users/<userId>`

Als de service het singleton-partitieschema gebruikt, zijn de querytekenreeksparameters *PartitionKey* en *PartitionKind* niet vereist en kan de service worden bereikt met behulp van de gateway als:

* Extern:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Intern:`http://localhost:19081/MyApp/MyService`

Als de service het uniform int64-partitieschema gebruikt, moeten de querytekenreeksparameters *PartitionKey* en *PartitionKind* worden gebruikt om een partitie van de service te bereiken:

* Extern:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Intern:`http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Als u de bronnen wilt bereiken die de service blootlegt, plaatst u het resourcepad achter de servicenaam in de URL:

* Extern:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Intern:`http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

De gateway stuurt deze aanvragen vervolgens door naar de URL van de service:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Speciale afhandeling voor havendeeldiensten
De servicefabric reverse proxy probeert een serviceadres opnieuw op te lossen en probeert de aanvraag opnieuw wanneer een service niet kan worden bereikt. Wanneer een service niet kan worden bereikt, is de service-instantie of -replica over het algemeen naar een ander knooppunt verplaatst als onderdeel van de normale levenscyclus. Wanneer dit gebeurt, kan de omgekeerde proxy een fout in de netwerkverbinding ontvangen die aangeeft dat een eindpunt niet meer is geopend op het oorspronkelijk opgeloste adres.

Replica's of service-exemplaren kunnen echter een hostproces delen en kunnen ook een poort delen wanneer deze worden gehost door een op http.sys gebaseerde webserver, waaronder:

* [System.net.httpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

In deze situatie is het waarschijnlijk dat de webserver beschikbaar is in het hostproces en reageert op aanvragen, maar de opgeloste service-instantie of replica is niet langer beschikbaar op de host. In dit geval ontvangt de gateway een HTTP 404-antwoord van de webserver. Een HTTP 404-antwoord kan dus twee verschillende betekenissen hebben:

- Case #1: Het serviceadres is correct, maar de bron die de gebruiker heeft aangevraagd, bestaat niet.
- Case #2: het serviceadres is onjuist en de bron die de gebruiker heeft aangevraagd, kan op een ander knooppunt bestaan.

Het eerste geval is een normale HTTP 404, die wordt beschouwd als een gebruikersfout. In het tweede geval heeft de gebruiker echter een bron aangevraagd die wel bestaat. De omgekeerde proxy kan deze niet vinden omdat de service zelf is verplaatst. De omgekeerde proxy moet het adres opnieuw oplossen en het verzoek opnieuw proberen.

De omgekeerde proxy heeft dus een manier nodig om onderscheid te maken tussen deze twee gevallen. Om dat onderscheid te maken, is een hint van de server vereist.

* Standaard wordt de omgekeerde proxy aangenomen #2 en probeert het verzoek opnieuw op te lossen en uit te geven.
* Als u de #1 naar de omgekeerde proxy wilt aangeven, moet de service de volgende HTTP-antwoordkoptekst retourneren:

  `X-ServiceFabric : ResourceNotFound`

Deze HTTP-antwoordkop geeft een normale HTTP 404-situatie aan waarin de gevraagde resource niet bestaat en de omgekeerde proxy niet zal proberen het serviceadres opnieuw op te lossen.

## <a name="special-handling-for-services-running-in-containers"></a>Speciale afhandeling voor diensten die in containers worden uitgevoerd

Voor services die in containers worden uitgevoerd, u de omgevingsvariabele gebruiken `Fabric_NodeIPOrFQDN` om de url van de omgekeerde [proxy](#uri-format-for-addressing-services-by-using-the-reverse-proxy) te construeren zoals in de volgende code:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Voor het lokale `Fabric_NodeIPOrFQDN` cluster is standaard ingesteld op 'localhost'. Start het lokale `-UseMachineName` cluster met de parameter om ervoor te zorgen dat containers de omgekeerde proxy kunnen bereiken die op het knooppunt wordt uitgevoerd. Zie Uw [ontwikkelaarsomgeving configureren om containers te debuggen](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers)voor meer informatie.

Service Fabric-services die worden uitgevoerd in Docker Compose-containers vereisen een speciale *sectie* docker-compose.yml-poorten http: of https: configuratie. Zie [Docker Compose-implementatieondersteuning voor](service-fabric-docker-compose.md)meer informatie in Azure Service Fabric .

## <a name="next-steps"></a>Volgende stappen
* [Omgekeerde proxy instellen en configureren op een cluster](service-fabric-reverseproxy-setup.md).
* [Doorsturen instellen om http-service te beveiligen met de omgekeerde proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* [Omgekeerde-proxygebeurtenissen onderzoeken](service-fabric-reverse-proxy-diagnostics.md)
* Zie een voorbeeld van HTTP-communicatie tussen services in een [voorbeeldproject op GitHub.](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Externe proceduregesprekken met Betrouwbare Services remoting](service-fabric-reliable-services-communication-remoting.md)
* [Web API die OWIN gebruikt in Betrouwbare Services](service-fabric-reliable-services-communication-webapi.md)
* [WCF-communicatie met behulp van betrouwbare services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
