---
title: Beveiligde service remoting communicatie met Java
description: Meer informatie over het beveiligen van serviceremoting-gebaseerde communicatie voor javabetrouwbare services die worden uitgevoerd in een Azure Service Fabric-cluster.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: adefeadf939d398268624343d82c18cbf5ec87cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609635"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Beveiligde service remoting communicatie in een Java-service
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java op Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Veiligheid is een van de belangrijkste aspecten van communicatie. Het application framework Reliable Services biedt een aantal vooraf gebouwde communicatiestacks en -tools die u gebruiken om de beveiliging te verbeteren. In dit artikel wordt besproken hoe u de beveiliging verbeteren wanneer u serviceremoting gebruikt in een Java-service. Het bouwt voort op een bestaand [voorbeeld](service-fabric-reliable-services-communication-remoting-java.md) dat uitlegt hoe remoting in te stellen voor betrouwbare diensten geschreven in Java. 

Voer de volgende stappen uit om een service te beveiligen wanneer u serviceremoting met Java-services gebruikt:

1. Maak een `HelloWorldStateless`interface, die de methoden definieert die beschikbaar zijn voor een externe procedureaanroep op uw service. Uw service `FabricTransportServiceRemotingListener`wordt gebruikt, die `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` wordt aangegeven in het pakket. Dit is `CommunicationListener` een implementatie die remoting mogelijkheden biedt.

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
2. Voeg listenerinstellingen en beveiligingsreferenties toe.

    Zorg ervoor dat het certificaat dat u wilt gebruiken om uw servicecommunicatie te beveiligen, is geïnstalleerd op alle knooppunten in het cluster. Voor services die op Linux worden uitgevoerd, moet het certificaat beschikbaar zijn als een PEM-formulierbestand; ofwel `.pem` een bestand dat de certificaat- `.crt` en privésleutel bevat, of een bestand met het certificaat en een `.key` bestand dat de privésleutel bevat. Zie [Locatie en formaat van X.509-certificaten op Linux-knooppunten](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes)voor meer informatie.
    
    Er zijn twee manieren waarop u listenerinstellingen en beveiligingsreferenties opgeven:

   1. Geef ze met behulp van een [config pakket:](service-fabric-application-and-service-manifests.md)

       Voeg een `TransportSettings` benoemde sectie toe in het bestand settings.xml.

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

       In dit geval `createServiceInstanceListeners` ziet de methode er als volgt uit:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Als u `TransportSettings` een sectie toevoegt in het bestand `FabricTransportListenerSettings` settings.xml zonder voorvoegsel, worden standaard alle instellingen uit deze sectie geladen.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        In dit geval `CreateServiceInstanceListeners` ziet de methode er als volgt uit:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Wanneer u methoden aanroept op een beveiligde service met behulp `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` van de remoting-stack, gebruikt u in plaats van de klasse om een serviceproxy te maken `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Als de clientcode wordt uitgevoerd als onderdeel `FabricTransportSettings` van een service, u laden vanuit het bestand settings.xml. Maak een sectie TransportInstellingen die vergelijkbaar is met de servicecode, zoals eerder wordt weergegeven. Breng de volgende wijzigingen aan in de clientcode:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
