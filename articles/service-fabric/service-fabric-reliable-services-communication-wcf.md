---
title: Betrouwbare Services WCF communicatiestack
description: De ingebouwde WCF-communicatiestack in Service Fabric biedt klantservice WCF-communicatie voor betrouwbare services.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: 7f3b3974893316a488270f755b8f8822080658d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433864"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>WCF-gebaseerde communicatiestack voor betrouwbare services
Met het Framework Reliable Services kunnen serviceauteurs de communicatiestack kiezen die ze voor hun service willen gebruiken. Ze kunnen de communicatiestack van hun keuze aansluiten via de **ICommunicationListener** die is geretourneerd via de methoden [CreateServiceReplicaListeners of CreateServiceInstanceListeners.](service-fabric-reliable-services-communication.md) Het framework biedt een implementatie van de communicatiestack op basis van de Windows Communication Foundation (WCF) voor serviceauteurs die wcf-gebaseerde communicatie willen gebruiken.

## <a name="wcf-communication-listener"></a>WCF-communicatieluisteraar
De WCF-specifieke implementatie van **ICommunicationListener** wordt verzorgd door de **klasse Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener.**

Stel dat we een servicecontract van het type hebben`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

We kunnen een WCF-communicatieluisteraar in de service op de volgende manier maken.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Klanten schrijven voor de WCF-communicatiestack
Voor het schrijven van klanten om te communiceren met diensten met behulp van WCF, het kader biedt **WcfClientCommunicationFactory**, dat is de WCF-specifieke implementatie van [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Het WCF communicatiekanaal is toegankelijk via de **WcfCommunicationClient** die is gemaakt door de **WcfCommunicationClientFactory.**

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Clientcode kan de **WcfCommunicationClientFactory** gebruiken samen met de **WcfCommunicationClient** die **ServicePartitionClient** implementeert om het serviceeindpunt te bepalen en met de service te communiceren.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> De standaardServicePartitionResolver gaat ervan uit dat de client wordt uitgevoerd in hetzelfde cluster als de service. Als dat niet het geval is, maakt u een object ServicePartitionResolver en geeft u de eindpunten van de clusterverbinding door.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* [Externe procedureoproep met Betrouwbare Services remoting](service-fabric-reliable-services-communication-remoting.md)
* [Web API met OWIN in betrouwbare services](service-fabric-reliable-services-communication-webapi.md)
* [Communicatie beveiligen voor betrouwbare services](service-fabric-reliable-services-secure-communication-wcf.md)

