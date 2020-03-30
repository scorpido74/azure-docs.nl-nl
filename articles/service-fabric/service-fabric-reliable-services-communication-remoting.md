---
title: Service remoting met behulp van C# in Service Fabric
description: Service Fabric remoting stelt klanten en services in staat om te communiceren met C# services met behulp van een externe procedure oproep.
author: vturecek
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 0d59275f25931a11b2d551a2e9eb019838e4c1b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433872"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Service remoting in C# met betrouwbare services

> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java op Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Voor services die niet zijn gekoppeld aan een bepaald communicatieprotocol of -stack, zoals een web-API, Windows Communication Foundation of anderen, biedt het Reliable Services-framework een remoting-mechanisme om snel en eenvoudig externe procedureoproepen in te stellen Diensten. In dit artikel wordt besproken hoe u externe procedureoproepen voor services instellen die met C#zijn geschreven.

## <a name="set-up-remoting-on-a-service"></a>Remoting instellen op een service

U remoting voor een service instellen in twee eenvoudige stappen:

1. Maak een interface die uw service kan implementeren. Deze interface definieert de methoden die beschikbaar zijn voor een externe procedureaanroep op uw service. De methoden moeten asynchrone methoden voor taakterugsturen zijn. De interface `Microsoft.ServiceFabric.Services.Remoting.IService` moet implementeren om aan te geven dat de service een remoting-interface heeft.
2. Gebruik een remoting luisteraar in uw dienst. Een remoting luisteraar `ICommunicationListener` is een implementatie die remoting mogelijkheden biedt. De `Microsoft.ServiceFabric.Services.Remoting.Runtime` naamruimte bevat `CreateServiceRemotingInstanceListeners` de extensiemethode voor zowel stateloze als stateful services die kunnen worden gebruikt om een remoting listener te maken met behulp van het standaard remoting transportprotocol.

>[!NOTE]
>De `Remoting` naamruimte is beschikbaar als een `Microsoft.ServiceFabric.Services.Remoting`apart NuGet-pakket genaamd .

De volgende stateloze service legt bijvoorbeeld één methode bloot om 'Hello World' te krijgen via een externe procedureoproep.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> De argumenten en de retourtypen in de serviceinterface kunnen elke eenvoudige, complexe of aangepaste typen zijn, maar ze moeten kunnen worden geserialiseerd door de .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Externe servicemethoden aanroepen

Aanroepen methoden op een service met behulp van de remoting stack `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` wordt gedaan met behulp van een lokale proxy om de dienst via de klasse. De `ServiceProxy` methode maakt een lokale proxy met dezelfde interface die de service implementeert. Met die proxy u methoden op afstand op de interface aanroepen.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Het remoting-framework propt uitzonderingen die door de service naar de client worden gegooid. Als gevolg hiervan `ServiceProxy`is de klant bij gebruik verantwoordelijk voor de afhandeling van de uitzonderingen die door de service worden gegooid.

## <a name="service-proxy-lifetime"></a>Levensduur van serviceproxy

Het maken van serviceproxy's is een lichtgewicht bewerking, dus u er zoveel maken als u nodig hebt. Serviceproxy-exemplaren kunnen worden hergebruikt zolang ze nodig zijn. Als een externe procedureoproep een uitzondering maakt, u nog steeds dezelfde proxy-instantie opnieuw gebruiken. Elke serviceproxy bevat een communicatieclient die wordt gebruikt om berichten via de draad te verzenden. Terwijl u een beroep doet op externe oproepen, worden interne controles uitgevoerd om te bepalen of de communicatieclient geldig is. Op basis van de resultaten van deze controles wordt de communicatieclient indien nodig opnieuw gemaakt. Als er dus een uitzondering optreedt, hoeft `ServiceProxy`u deze niet opnieuw te maken.

### <a name="service-proxy-factory-lifetime"></a>Levensduur van de serviceproxy

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) is een fabriek die proxy-exemplaren maakt voor verschillende remoting-interfaces. Als u de `ServiceProxyFactory.CreateServiceProxy` API gebruikt om een proxy te maken, maakt het framework een proxy van de singletonservice.
Het is handig om er handmatig een te maken wanneer u de eigenschappen [van IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) moet overschrijven.
Fabriekscreatie is een dure operatie. Een serviceproxyfabriek onderhoudt een interne cache van de communicatieclient.
Een aanbevolen praktijk is om de service proxy fabriek cache zo lang mogelijk.

## <a name="remoting-exception-handling"></a>Verwerking van remoting-uitzonderingen

Alle externe uitzonderingen die door de service-API worden gegenereerd, worden teruggestuurd naar de client als AggregateException. Uitzonderingen op afstand moeten kunnen worden geserialiseerd door DataContract. Als dit niet het zo is, gooit de proxy-API [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) met de serialisatiefout erin.

De serviceproxy verwerkt alle failoveruitzonderingen voor de servicepartitie waarvoor deze is gemaakt. De eindpunten worden opnieuw opgelost als er failoveruitzonderingen zijn (niet-tijdelijke uitzonderingen) en probeert de aanroep opnieuw met het juiste eindpunt. Het aantal nieuwe pogingen voor failoveruitzonderingen is voor onbepaalde tijd.
Als er tijdelijke uitzonderingen optreden, wordt de aanroep opnieuw geprobeerd door de proxy.

Standaardparameters voor het opnieuw proberen worden geleverd door [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Een gebruiker kan deze waarden configureren door het object OperationRetrySettings door te geven aan de constructor ServiceProxyFactory.

## <a name="use-the-remoting-v2-stack"></a>Gebruik de remoting V2-stack

Vanaf versie 2.8 van het NuGet remoting-pakket heb je de mogelijkheid om de remoting V2 stack te gebruiken. De remoting V2 stack presteert beter. Het biedt ook functies zoals aangepaste serialisatie en meer pluggable API's.
Sjablooncode blijft de remoting V1-stack gebruiken.
Remoting V2 is niet compatibel met V1 (de vorige remoting stack). Volg de instructies in het artikel [Upgrade van V1 naar V2](#upgrade-from-remoting-v1-to-remoting-v2) om effecten op de beschikbaarheid van de service te voorkomen.

De volgende benaderingen zijn beschikbaar om de V2-stack in te schakelen.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Een verzamelkenmerk gebruiken om de V2-stack te gebruiken

Met deze stappen wordt de sjablooncode gewijzigd om de V2-stack te gebruiken met behulp van een onderdeelkenmerk.

1. Wijzig de eindpuntbron `"ServiceEndpoint"` `"ServiceEndpointV2"` van naar in het servicemanifest.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Gebruik `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` de extensiemethode om remoting-listeners te maken (gelijk aan zowel V1 als V2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Markeer de assemblage die de `FabricTransportServiceRemotingProvider` remoting-interfaces bevat met een kenmerk.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Er zijn geen codewijzigingen vereist in het clientproject.
Bouw de clientassemblage met de interfaceassemblage om ervoor te zorgen dat het eerder getoonde assemblagekenmerk wordt gebruikt.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Expliciete V2-klassen gebruiken om de V2-stack te gebruiken

Als alternatief voor het gebruik van een assemblagekenmerk kan de V2-stack ook worden ingeschakeld met behulp van expliciete V2-klassen.

Met deze stappen wordt de sjablooncode gewijzigd om de V2-stack te gebruiken met behulp van expliciete V2-klassen.

1. Wijzig de eindpuntbron `"ServiceEndpoint"` `"ServiceEndpointV2"` van naar in het servicemanifest.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Gebruik [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` vanuit de naamruimte.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. Gebruik [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` vanuit de naamruimte om clients te maken.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Upgrade van remoting V1 naar remoting V2

Om te upgraden van V1 naar V2, zijn upgrades in twee stappen vereist. Volg de stappen in deze reeks.

1. Upgrade de V1-service naar V2-service met dit kenmerk.
Deze wijziging zorgt ervoor dat de dienst luistert op de V1 en V2 luisteraar.

    a. Voeg een eindpuntbron met de naam "ServiceEndpointV2" toe in het servicemanifest.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Gebruik de volgende extensiemethode om een remoting listener te maken.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Voeg een assemblagekenmerk toe aan remoting-interfaces om de V1- en V2-listener en de V2-client te gebruiken.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Upgrade de V1-client naar een V2-client met behulp van het V2-clientkenmerk.
Deze stap zorgt ervoor dat de client de V2-stack gebruikt.
Er is geen wijziging in het clientproject/-service vereist. Het bouwen van klantprojecten met bijgewerkte interfaceassemblage is voldoende.

3. Deze stap is optioneel. Gebruik het kenmerk V2-listener en upgrade de V2-service.
Deze stap zorgt ervoor dat de service alleen luistert op de V2-luisteraar.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>De remoting V2 -stack (interface compatible) gebruiken

 De remoting V2 (interface compatibel, bekend als V2_1) stack heeft alle functies van de V2 remoting stack. De interface stack is compatibel met de remoting V1 stack, maar het is niet achterwaarts compatibel met V2 en V1. Als u wilt upgraden van V1 naar V2_1 zonder dat dit gevolgen heeft voor de beschikbaarheid van de service, voert u de stappen in het artikel Upgrade van V1 naar V2 (interfacecompatibel).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Gebruik een verzamelkenmerk om de remoting V2-stack (interface compatible) te gebruiken

Volg deze stappen om over te schakelen naar een V2_1 stapel.

1. Voeg een eindpuntbron met de naam 'ServiceEndpointV2_1' toe in het servicemanifest.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Gebruik de remoting-extensiemethode om een remoting-listener te maken.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Voeg een [assemblagekenmerk](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) toe aan remoting-interfaces.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Er zijn geen wijzigingen nodig in het clientproject.
Bouw de clientassemblage met de interfaceassemblage om ervoor te zorgen dat het vorige assemblagekenmerk wordt gebruikt.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Expliciete remoting-klassen gebruiken om een listener/clientfabriek te maken voor de V2-versie (interfacecompatibel).

Volg deze stappen:

1. Voeg een eindpuntbron met de naam 'ServiceEndpointV2_1' toe in het servicemanifest.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Gebruik de [remoting V2-luisteraar.](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet) De standaardserviceeindpuntbronnaam die wordt gebruikt, is 'ServiceEndpointV2_1'. Het moet worden gedefinieerd in het dienstenmanifest.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. Gebruik de [V2-clientfabriek](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Upgrade van remoting V1 naar remoting V2 (interface compatible)

Om te upgraden van V1 naar V2 (interface compatibel, bekend als V2_1), zijn upgrades in twee stappen vereist. Volg de stappen in deze reeks.

> [!NOTE]
> Wanneer u van V1 naar `Remoting` V2 upgradet, moet u ervoor zorgen dat de naamruimte wordt bijgewerkt om V2 te gebruiken. Voorbeeld: 'Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client'
>
>

1. Upgrade de V1-service naar V2_1 service met behulp van het volgende kenmerk.
Deze wijziging zorgt ervoor dat de dienst luistert op de V1 en de V2_1 luisteraar.

    a. Voeg een eindpuntbron met de naam 'ServiceEndpointV2_1' toe in het servicemanifest.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Gebruik de volgende extensiemethode om een remoting listener te maken.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Voeg een assemblagekenmerk toe aan remoting-interfaces om de V1-, V2_1-listener en V2_1 client te gebruiken.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Upgrade de V1-client naar de V2_1 client met behulp van het kenmerk V2_1 client.
Deze stap zorgt ervoor dat de client de V2_1 stack gebruikt.
Er is geen wijziging in het clientproject/-service vereist. Het bouwen van klantprojecten met bijgewerkte interfaceassemblage is voldoende.

3. Deze stap is optioneel. Verwijder de V1-listenerversie uit het kenmerk en upgrade de V2-service.
Deze stap zorgt ervoor dat de service alleen luistert op de V2-luisteraar.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Aangepaste serialisatie gebruiken met een uitgepakt bericht dat wordt geremoerend

Voor een in een dialoogvenster verpakt bericht maken we één verpakt object met alle parameters als veld erin.
Volg deze stappen:

1. Implementeer `IServiceRemotingMessageSerializationProvider` de interface om implementatie te bieden voor aangepaste serialisatie.
    Dit codefragment laat zien hoe de implementatie eruit ziet.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. Overschrijf de standaard `JsonSerializationProvider` serialisatieprovider met voor een remoting-listener.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. Overschrijven van de `JsonSerializationProvider` standaard serialisatieprovider met voor een remoting-clientfabriek.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Volgende stappen

* [Web API met OWIN in betrouwbare services](service-fabric-reliable-services-communication-webapi.md)
* [Windows Communication Foundation communicatie met betrouwbare services](service-fabric-reliable-services-communication-wcf.md)
* [Veilige communicatie voor betrouwbare services](service-fabric-reliable-services-secure-communication.md)
