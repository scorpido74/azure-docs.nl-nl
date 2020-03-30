---
title: 'Beveiligde serviceremoting communicatie met C #'
description: Meer informatie over het beveiligen van serviceremoting-gebaseerde communicatie voor c#-betrouwbare services die worden uitgevoerd in een Azure Service Fabric-cluster.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ee2f1d70f4094ccc7d80edbfaf16509b5124f607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609618"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Beveiligde service die communicatie in een C#-service remoting
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java op Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Veiligheid is een van de belangrijkste aspecten van communicatie. Het application framework Reliable Services biedt een aantal vooraf gebouwde communicatiestacks en -tools die u gebruiken om de beveiliging te verbeteren. In dit artikel wordt besproken hoe u de beveiliging verbeteren wanneer u serviceremoting gebruikt in een C#-service. Het bouwt voort op een bestaand [voorbeeld](service-fabric-reliable-services-communication-remoting.md) dat uitlegt hoe remoting in te stellen voor betrouwbare services geschreven in C#. 

Voer de volgende stappen uit om een service te beveiligen wanneer u serviceremoting met C#-services gebruikt:

1. Maak een `IHelloWorldStateful`interface, die de methoden definieert die beschikbaar zijn voor een externe procedureaanroep op uw service. Uw service `FabricTransportServiceRemotingListener`wordt gebruikt , `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` die is aangegeven in de naamruimte. Dit is `ICommunicationListener` een implementatie die remoting mogelijkheden biedt.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Voeg listenerinstellingen en beveiligingsreferenties toe.

    Zorg ervoor dat het certificaat dat u wilt gebruiken om uw servicecommunicatie te beveiligen, is geïnstalleerd op alle knooppunten in het cluster. 
    
    > [!NOTE]
    > Op Linux-knooppunten moet het certificaat aanwezig zijn als PEM-geformatteerde bestanden in de */var/lib/sfcerts* directory. Zie [Locatie en formaat van X.509-certificaten op Linux-knooppunten](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes)voor meer informatie. 

    Er zijn twee manieren waarop u listenerinstellingen en beveiligingsreferenties opgeven:

   1. Geef ze rechtstreeks in de servicecode:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Geef ze met behulp van een [config pakket:](service-fabric-application-and-service-manifests.md)

       Voeg een `TransportSettings` benoemde sectie toe in het bestand settings.xml.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       In dit geval `CreateServiceReplicaListeners` ziet de methode er als volgt uit:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Als u `TransportSettings` een sectie toevoegt in `FabricTransportRemotingListenerSettings` het bestand settings.xml, worden standaard alle instellingen uit deze sectie geladen.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        In dit geval `CreateServiceReplicaListeners` ziet de methode er als volgt uit:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Wanneer u methoden aanroept op een beveiligde service met behulp `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` van de remoting-stack, gebruikt u in plaats van de klasse om een serviceproxy te maken `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Pass `FabricTransportRemotingSettings`in , `SecurityCredentials`die bevat .

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Als de clientcode wordt uitgevoerd als onderdeel `FabricTransportRemotingSettings` van een service, u laden vanuit het bestand settings.xml. Maak een helloworldclienttransportinstellingen sectie die vergelijkbaar is met de servicecode, zoals eerder weergegeven. Breng de volgende wijzigingen aan in de clientcode:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Als de client niet wordt uitgevoerd als onderdeel van een service, u een bestand client_name.settings.xml maken op dezelfde locatie waar de client_name.exe zich bevindt. Maak vervolgens een sectie TransportInstellingen in dat bestand.

    Als u een `TransportSettings` sectie toevoegt in clientinstellingen.xml/client_name.settings.xml.xml, `FabricTransportRemotingSettings` laadt u net als de service standaard alle instellingen uit deze sectie.

    In dat geval wordt de eerdere code nog verder vereenvoudigd:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Lees als volgende stap [Web API met OWIN in Reliable Services.](service-fabric-reliable-services-communication-webapi.md)
