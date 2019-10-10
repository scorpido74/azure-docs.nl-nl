---
title: Beveiligde service communicatie op basis van WCF in azure Service Fabric | Microsoft Docs
description: Meer informatie over het beveiligen van op WCF gebaseerde communicatie voor betrouw bare services die worden uitgevoerd in een Azure Service Fabric-cluster.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: 31a7a3a42436f3a818fcf48f2af5ca395fa02386
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170420"
---
# <a name="secure-wcf-based-communications-for-a-service"></a>Beveiligde communicatie op basis van WCF voor een service
Beveiliging is een van de belangrijkste aspecten van de communicatie. Het Reliable Services-toepassings raamwerk bevat enkele vooraf ontwikkelde communicatie stacks en hulpprogram ma's die u kunt gebruiken om de beveiliging te verbeteren. In dit artikel wordt uitgelegd hoe u de beveiliging kunt verbeteren wanneer u service remoting gebruikt.

We gebruiken een bestaand [voor beeld](service-fabric-reliable-services-communication-wcf.md) waarin wordt uitgelegd hoe u een op WCF gebaseerde communicatie stack instelt voor betrouw bare Services. Voer de volgende stappen uit om een service te helpen beveiligen wanneer u een op WCF gebaseerde communicatie stack gebruikt:

1. Voor de service moet u helpen de WCF Communication listener (`WcfCommunicationListener`) te beveiligen die u maakt. U doet dit door de `CreateServiceReplicaListeners`-methode te wijzigen.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. In de-client blijft de `WcfCommunicationClient`-klasse die in het vorige [voor beeld](service-fabric-reliable-services-communication-wcf.md) is gemaakt, ongewijzigd. Maar u moet de methode `CreateClientAsync` van `WcfCommunicationClientFactory` overschrijven:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Gebruik `SecureWcfCommunicationClientFactory` om een WCF-communicatie client (`WcfCommunicationClient`) te maken. Gebruik de-client om service methoden aan te roepen.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

Als volgende stap leest u [Web API with OWIN in reliable Services](service-fabric-reliable-services-communication-webapi.md).
