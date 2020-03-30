---
title: Verbinding maken met en communiceren met services in Azure Service Fabric
description: Meer informatie over het oplossen, verbinden en communiceren met services in Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: e57d169decf482f8b8be1e3b31a07690bc222c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458243"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Verbinding maken en communiceren met services in Service Fabric
In Service Fabric wordt een service ergens in een Service Fabric-cluster uitgevoerd, meestal verdeeld over meerdere VM's. Het kan worden verplaatst van de ene plaats naar de andere, hetzij door de eigenaar van de service, of automatisch door Service Fabric. Services zijn niet statisch gekoppeld aan een bepaalde machine of adres.

Een Service Fabric-toepassing bestaat over het algemeen uit veel verschillende services, waarbij elke service een gespecialiseerde taak uitvoert. Deze services kunnen met elkaar communiceren om een volledige functie te vormen, zoals het renderen van verschillende delen van een webtoepassing. Er zijn ook clienttoepassingen die verbinding maken met en communiceren met services. In dit document wordt besproken hoe u communicatie instellen met en tussen uw services in Service Fabric.

## <a name="bring-your-own-protocol"></a>Neem je eigen protocol mee
Service Fabric helpt bij het beheren van de levenscyclus van uw services, maar neemt geen beslissingen over wat uw services doen. Dit geldt ook voor communicatie. Wanneer uw service wordt geopend door Service Fabric, is dat de mogelijkheid van uw service om een eindpunt in te stellen voor binnenkomende aanvragen, met behulp van elk protocol of communicatiestack dat u wilt. Uw service luistert op een normaal **IP:port-adres** met behulp van een adresseringsschema, zoals een URI. Meerdere service-exemplaren of replica's kunnen een hostproces delen, in welk geval ze verschillende poorten moeten gebruiken of een poortdesharingmechanisme moeten gebruiken, zoals het http.sys-kernelstuurprogramma in Windows. In beide gevallen moet elke service-instantie of replica in een hostproces uniek adresseerbaar zijn.

![serviceeindpunten][1]

## <a name="service-discovery-and-resolution"></a>Servicedetectie en -oplossing
In een gedistribueerd systeem kunnen services in de loop van de tijd van de ene machine naar de andere worden verplaatst. Dit kan om verschillende redenen gebeuren, waaronder resource balancing, upgrades, failovers of scale-out. Dit betekent dat serviceeindpuntadressen worden gewijzigd wanneer de service wordt verplaatst naar knooppunten met verschillende IP-adressen en dat deze op verschillende poorten kan worden geopend als de service een dynamisch geselecteerde poort gebruikt.

![Distributie van diensten][7]

Service Fabric biedt een detectie- en oplossingsservice genaamd de Naamgevingsservice. De naamgevingsservice onderhoudt een tabel die benoemde service-exemplaren toewijst aan de eindpuntadressen waarop ze luisteren. Alle benoemde service-exemplaren in Service Fabric hebben unieke namen `"fabric:/MyApplication/MyService"`die worden weergegeven als URI's, bijvoorbeeld . De naam van de service verandert niet gedurende de levensduur van de service, het zijn alleen de eindpuntadressen die kunnen veranderen wanneer services worden verplaatst. Dit is analoog aan websites met constante URL's, maar waar het IP-adres kan veranderen. En vergelijkbaar met DNS op het web, dat url's van websites naar IP-adressen oplost, heeft Service Fabric een registrar die servicenamen toewijst aan hun eindpuntadres.

![serviceeindpunten][2]

Het oplossen en verbinden met services omvat de volgende stappen die in een lus worden uitgevoerd:

* **Oplossen:** haal het eindpunt op dat een service heeft gepubliceerd van de naamgevingsservice.
* **Verbinding maken:** maak verbinding met de service via het protocol dat het op dat eindpunt gebruikt.
* **Opnieuw proberen**: een verbindingspoging kan om verschillende redenen mislukken, bijvoorbeeld als de service is verplaatst sinds de laatste keer dat het eindpuntadres is opgelost. In dat geval moeten de voorgaande stappen voor het oplossen en verbinden opnieuw worden geprobeerd en wordt deze cyclus herhaald totdat de verbinding is geslaagd.

## <a name="connecting-to-other-services"></a>Verbinding maken met andere services
Services die verbinding maken met elkaar binnen een cluster, hebben over het algemeen rechtstreeks toegang tot de eindpunten van andere services omdat de knooppunten in een cluster zich op hetzelfde lokale netwerk bevinden. Service Fabric biedt aanvullende services die gebruikmaken van de naamgevingsservice om verbinding te maken tussen services. Een DNS-service en een reverse proxy-service.


### <a name="dns-service"></a>DNS-service
Aangezien veel services, met name containeriseerde services, een bestaande URL-naam kunnen hebben, is het erg handig om deze op te lossen met behulp van het standaard DNS-protocol (in plaats van het Naming Service-protocol), vooral in toepassings "lift and shift"-scenario's. Dit is precies wat de DNS-service doet. Hiermee u DNS-namen toewijzen aan een servicenaam en dus eindpuntIP-adressen oplossen. 

Zoals in het volgende diagram wordt weergegeven, brengt de DNS-service, die wordt uitgevoerd in het cluster Servicefabric, DNS-namen toe aan servicenamen die vervolgens door de naamgevingsservice worden opgelost om de eindpuntadressen terug te sturen om verbinding mee te maken. De DNS-naam voor de service wordt verstrekt op het moment van maken. 

![serviceeindpunten][9]

Zie [DNS-service in](service-fabric-dnsservice.md) het artikel Azure Service Fabric voor meer informatie over het gebruik van de DNS-service.

### <a name="reverse-proxy-service"></a>Omgekeerde proxyservice
De omgekeerde proxy richt services in het cluster die HTTP-eindpunten, waaronder HTTPS, blootlegt. De omgekeerde proxy vereenvoudigt het aanroepen van andere services en hun methoden sterk door een specifieke URI-indeling te hebben en verwerkt de oplossing, verbinding maken, opnieuw proberen stappen die nodig zijn voor de ene service om met een andere service te communiceren met behulp van de naamgevingsservice. Met andere woorden, het verbergt de naamgevingsservice voor u wanneer u andere services aanroept door dit zo eenvoudig te maken als het aanroepen van een URL.

![serviceeindpunten][10]

Zie Reverse proxy in het artikel [Azure Service Fabric](service-fabric-reverseproxy.md) voor meer informatie over het gebruik van de omgekeerde proxyservice.

## <a name="connections-from-external-clients"></a>Verbindingen van externe clients
Services die verbinding maken met elkaar binnen een cluster, hebben over het algemeen rechtstreeks toegang tot de eindpunten van andere services omdat de knooppunten in een cluster zich op hetzelfde lokale netwerk bevinden. In sommige omgevingen kan een cluster zich echter achter een load balancer bevinden die extern binnendringend verkeer door een beperkte reeks poorten leidt. In deze gevallen kunnen services nog steeds met elkaar communiceren en adressen oplossen met behulp van de naamgevingsservice, maar er moeten extra stappen worden ondernomen om externe clients in staat te stellen verbinding te maken met services.

## <a name="service-fabric-in-azure"></a>Servicefabric in Azure
Een cluster van servicefabric in Azure wordt achter een Azure Load Balancer geplaatst. Al het externe verkeer naar het cluster moet door de load balancer gaan. De load balancer stuurt automatisch verkeer binnenop een bepaalde poort door naar een willekeurig *knooppunt* dat dezelfde poort heeft geopend. De Azure Load Balancer weet alleen van poorten die op de *knooppunten*worden geopend, maar weet niet van poorten die door afzonderlijke *services*worden geopend.

![Azure Load Balancer en Service Fabric-topologie][3]

Om bijvoorbeeld extern verkeer op poort **80**te accepteren, moeten de volgende dingen worden geconfigureerd:

1. Schrijf een dienst die luistert op poort 80. Configureer poort 80 in serviceManifest.xml van de service en open een listener in de service, bijvoorbeeld een zelf gehoste webserver.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Maak een cluster van servicestructuur in Azure en geef poort **80** op als aangepaste eindpuntpoort voor het knooppunttype dat de service host. Als u meer dan één knooppunttype hebt, u een *plaatsingsbeperking* voor de service instellen om ervoor te zorgen dat deze alleen wordt uitgevoerd op het knooppunttype waarop de aangepaste eindpuntpoort is geopend.

    ![Een poort openen op een knooppunttype][4]
3. Zodra het cluster is gemaakt, configureert u de Azure Load Balancer in de resourcegroep van het cluster om verkeer door te sturen op poort 80. Bij het maken van een cluster via de Azure-portal wordt dit automatisch ingesteld voor elke aangepaste eindpuntpoort die is geconfigureerd.

    ![Verkeer doorsturen in de Azure Load Balancer][5]
4. De Azure Load Balancer gebruikt een sonde om te bepalen of verkeer al dan niet naar een bepaald knooppunt moet worden verzonden. De sonde controleert periodiek een eindpunt op elk knooppunt om te bepalen of het knooppunt reageert. Als de sonde na een aantal keren geen antwoord meer ontvangt, stopt de load balancer met het verzenden van verkeer naar dat knooppunt. Bij het maken van een cluster via de Azure-portal wordt automatisch een sonde ingesteld voor elke aangepaste eindpuntpoort die is geconfigureerd.

    ![Verkeer doorsturen in de Azure Load Balancer][8]

Het is belangrijk om te onthouden dat de Azure Load Balancer en de sonde alleen op de hoogte zijn van de *knooppunten,* niet van de *services* die op de knooppunten worden uitgevoerd. De Azure Load Balancer stuurt altijd verkeer naar knooppunten die reageren op de sonde, dus zorg ervoor dat services beschikbaar zijn op de knooppunten die op de sonde kunnen reageren.

## <a name="reliable-services-built-in-communication-api-options"></a>Betrouwbare services: ingebouwde API-opties voor communicatie
Het Reliable Services framework wordt geleverd met verschillende vooraf gebouwde communicatiemogelijkheden. De beslissing over welke het beste voor u zal werken, hangt af van de keuze van het programmeermodel, het communicatiekader en de programmeertaal waarin uw diensten zijn geschreven.

* **Geen specifiek protocol:**  Als u niet beschikt over een bepaalde keuze van communicatie kader, maar je wilt iets snel aan de slag, dan is de ideale optie voor u is [service remoting](service-fabric-reliable-services-communication-remoting.md), die sterk getypte externe procedure oproepen voor betrouwbare diensten en betrouwbare actoren mogelijk maakt. Dit is de eenvoudigste en snelste manier om aan de slag te gaan met servicecommunicatie. Serviceremoting verwerkt de oplossing van serviceadressen, verbinding, opnieuw proberen en foutafhandeling. Dit is beschikbaar voor zowel C# als Java applicaties.
* **HTTP:** Voor taal-agnostische communicatie biedt HTTP een industriestandaard keuze met tools en HTTP-servers die beschikbaar zijn in veel verschillende talen, allemaal ondersteund door Service Fabric. Services kunnen elke beschikbare HTTP-stack gebruiken, inclusief [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) voor C#-toepassingen. Clients die in C# `ICommunicationClient` `ServicePartitionClient` zijn geschreven, kunnen gebruikmaken `CommunicationClient` `FabricServicePartitionClient` van de klassen en klassen, terwijl voor Java de klassen en klassen worden gebruikt [voor serviceresolutie, HTTP-verbindingen en loops opnieuw proberen.](service-fabric-reliable-services-communication.md)
* **WCF**: Als u bestaande code hebt die WCF als `WcfCommunicationListener` communicatiekader gebruikt, `WcfCommunicationClient` `ServicePartitionClient` kunt u de voor de serverkant en en de klassen voor de client gebruiken. Dit is echter alleen beschikbaar voor C# toepassingen op Windows gebaseerde clusters. Zie voor meer informatie dit artikel over [wcf-gebaseerde implementatie van de communicatiestack.](service-fabric-reliable-services-communication-wcf.md)

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Aangepaste protocollen en andere communicatiekaders gebruiken
Services kunnen elk protocol of framework gebruiken voor communicatie, of het nu gaat om een aangepast binair protocol via TCP-sockets of streaming-gebeurtenissen via [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) of Azure [IoT Hub.](https://azure.microsoft.com/services/iot-hub/) Service Fabric biedt communicatie-API's waar u uw communicatiestack op aansluiten, terwijl al het werk om te ontdekken en verbinding te maken van u wordt geabstraheerd. Zie dit artikel over het [reliable service communicatiemodel](service-fabric-reliable-services-communication.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Lees meer over de concepten en API's die beschikbaar zijn in het [communicatiemodel van Reliable Services,](service-fabric-reliable-services-communication.md)ga vervolgens snel aan de slag met [serviceremoting](service-fabric-reliable-services-communication-remoting.md) of ga dieper in op het schrijven van een communicatieluisteraar met behulp van [Web API met OWIN self-host](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
