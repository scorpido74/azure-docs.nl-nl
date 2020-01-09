---
title: Beveiligde communicatie tussen veilige services en Java
description: Meer informatie over het beveiligen van communicatie tussen service op afstand voor Java reliable services die worden uitgevoerd in een Azure Service Fabric-cluster.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: adefeadf939d398268624343d82c18cbf5ec87cd
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609635"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Externe communicatie met beveiligde service in een Java-service
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java op Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Beveiliging is een van de belangrijkste aspecten van de communicatie. Het Reliable Services-toepassings raamwerk bevat enkele vooraf ontwikkelde communicatie stacks en hulpprogram ma's die u kunt gebruiken om de beveiliging te verbeteren. In dit artikel wordt beschreven hoe u de beveiliging verbetert wanneer u service remoting gebruikt in een Java-service. Het is gebaseerd op een bestaand [voor beeld](service-fabric-reliable-services-communication-remoting-java.md) waarin wordt uitgelegd hoe u externe toegang instelt voor betrouw bare services die zijn geschreven in Java. 

Voer de volgende stappen uit om een service te helpen beveiligen wanneer u service voor externe toegang met Java-Services gebruikt:

1. Maak een interface, `HelloWorldStateless`, waarmee de methoden worden gedefinieerd die beschikbaar zijn voor een externe procedure aanroep voor uw service. Uw service maakt gebruik van `FabricTransportServiceRemotingListener`, dat is gedeclareerd in het `microsoft.serviceFabric.services.remoting.fabricTransport.runtime`-pakket. Dit is een `CommunicationListener` implementatie die mogelijkheden biedt voor externe communicatie.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Luister instellingen en beveiligings referenties toevoegen.

    Zorg ervoor dat het certificaat dat u wilt gebruiken om uw service communicatie te beveiligen, is geïnstalleerd op alle knoop punten in het cluster. Voor services die worden uitgevoerd op Linux moet het certificaat beschikbaar zijn als een PEM-formmatted-bestand. een `.pem`-bestand dat het certificaat en de persoonlijke sleutel bevat of een `.crt` bestand dat het certificaat en een `.key` bestand met de persoonlijke sleutel bevat. Zie [locatie en indeling van X. 509-certificaten op Linux-knoop punten](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes)voor meer informatie.
    
    Er zijn twee manieren waarop u listener-instellingen en beveiligings referenties kunt opgeven:

   1. Geef ze op met behulp van een [configuratie pakket](service-fabric-application-and-service-manifests.md):

       Voeg een benoemde `TransportSettings` sectie toe aan het bestand settings. XML.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       In dit geval ziet de `createServiceInstanceListeners` methode er als volgt uit:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Als u een `TransportSettings` sectie toevoegt in het bestand settings. XML zonder voor voegsel, worden alle instellingen in deze sectie standaard geladen door `FabricTransportListenerSettings`.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        In dit geval ziet de `CreateServiceInstanceListeners` methode er als volgt uit:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Wanneer u methoden aanroept voor een beveiligde service met behulp van de externe stack, gebruikt u in plaats van de `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase`-klasse om een Service proxy te maken, `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Als de client code wordt uitgevoerd als onderdeel van een service, kunt u `FabricTransportSettings` laden vanuit het bestand settings. XML. Maak een TransportSettings-sectie die vergelijkbaar is met de service code, zoals eerder wordt weer gegeven. Breng de volgende wijzigingen aan in de client code:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
