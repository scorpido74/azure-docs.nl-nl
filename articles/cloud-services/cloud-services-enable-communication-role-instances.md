---
title: Communicatie voor rollen in cloudservices | Microsoft Documenten
description: Rolexemplaren in Cloud Services kunnen eindpunten (http, https, tcp, udp) hebben gedefinieerd die voor hen worden gedefinieerd en die communiceren met de buitenkant of tussen andere rolexemplaren.
services: cloud-services
documentationcenter: ''
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.topic: article
ms.date: 12/14/2016
ms.author: tagore
ms.openlocfilehash: 094e08becf4f3a60c98d89bfae7e7c3a69b677f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386337"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Communicatie inschakelen voor rolexemplaren in Azure
Cloudservicerollen communiceren via interne en externe verbindingen. Externe verbindingen worden **invoereindpunten** genoemd, terwijl interne verbindingen **interne eindpunten**worden genoemd. In dit onderwerp wordt beschreven hoe u de [servicedefinitie](cloud-services-model-and-package.md#csdef) wijzigt om eindpunten te maken.

## <a name="input-endpoint"></a>Eindpunt voor invoer
Het invoereindpunt wordt gebruikt wanneer u een poort aan de buitenkant wilt blootstellen. U geeft het protocoltype en de poort van het eindpunt op, dat vervolgens geldt voor zowel de externe als de interne poorten voor het eindpunt. Als u wilt, u een andere interne poort opgeven voor het eindpunt met het kenmerk [localPort.](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint)

Het invoereindpunt kan de volgende protocollen gebruiken: **http, https, tcp, udp**.

Als u een invoereindpunt wilt maken, voegt u het onderliggende element **InputEndpoint** toe aan het element **Eindpunten** van een web- of werknemersrol.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Eindpunt voor instantieinvoer
Instance-invoereindpunten zijn vergelijkbaar met invoereindpunten, maar hiermee u specifieke openbare poorten voor elke afzonderlijke rolinstantie toewijzen door poortforwarding te gebruiken op de load balancer. U één poort op het openbare gezicht of een reeks poorten opgeven.

Het eindpunt voor instantieinvoer kan alleen **tcp** of **udp** als protocol gebruiken.

Als u een eindpunt voor instantieinvoer wilt maken, voegt u het onderliggende element **InstanceInputEndpoint** toe aan het element **Eindpunten** van een web- of werknemersrol.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Intern eindpunt
Interne eindpunten zijn beschikbaar voor bijvoorbeeld communicatie. De poort is optioneel en als deze wordt weggelaten, wordt een dynamische poort toegewezen aan het eindpunt. Er kan een poortbereik worden gebruikt. Er is een limiet van vijf interne eindpunten per rol.

Het interne eindpunt kan de volgende protocollen gebruiken: **http, tcp, udp, any**.

Als u een intern invoereindpunt wilt maken, voegt u het onderliggende element **InternalEndpoint** toe aan het element **Eindpunten** van een web- of werknemersrol.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

U ook gebruik maken van een poortbereik.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Functierollen voor werknemers versus webrollen
Er is een klein verschil met eindpunten bij het werken met zowel werknemer als webrollen. De webrol moet ten minste één invoereindpunt hebben met behulp van het **HTTP-protocol.**

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>De .NET SDK gebruiken om toegang te krijgen tot een eindpunt
De Azure Managed Library biedt methoden voor rolexemplaren om te communiceren tijdens runtime. Vanuit code die binnen een rolinstantie wordt uitgevoerd, u informatie ophalen over het bestaan van andere rolinstanties en hun eindpunten, evenals informatie over de huidige rolinstantie.

> [!NOTE]
> U alleen informatie ophalen over rolexemplaren die worden uitgevoerd in uw cloudservice en die ten minste één intern eindpunt definiëren. U geen gegevens verkrijgen over rolexemplaren die in een andere service worden uitgevoerd.
> 
> 

U de eigenschap [Instanties](/previous-versions/azure/reference/ee741904(v=azure.100)) gebruiken om exemplaren van een rol op te halen. Gebruik eerst de [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) om een verwijzing naar de huidige rolinstantie terug te sturen en gebruik vervolgens de eigenschap [Role](/previous-versions/azure/reference/ee741918(v=azure.100)) om een verwijzing naar de rol zelf terug te geven.

Wanneer u via de .NET SDK programmatisch verbinding maakt met een rolinstantie, is het relatief eenvoudig om toegang te krijgen tot de eindpuntgegevens. Nadat u bijvoorbeeld al verbinding hebt gemaakt met een specifieke rolomgeving, u de poort van een specifiek eindpunt met deze code krijgen:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

De eigenschap **Instanties** retourneert een verzameling **RoleInstance-objecten.** Deze collectie bevat altijd de huidige instantie. Als de rol geen intern eindpunt definieert, bevat de verzameling de huidige instantie, maar geen andere instanties. Het aantal rolexemplaren in de verzameling is altijd 1 in het geval dat er geen intern eindpunt voor de rol is gedefinieerd. Als de rol een intern eindpunt definieert, zijn de exemplaren ervan te ontdekken tijdens uitvoering en komt het aantal exemplaren in de verzameling overeen met het aantal exemplaren dat is opgegeven voor de rol in het serviceconfiguratiebestand.

> [!NOTE]
> De Azure Managed Library biedt geen manier om de status van andere rolinstanties te bepalen, maar u dergelijke statusbeoordelingen zelf implementeren als uw service deze functionaliteit nodig heeft. U [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) gebruiken om informatie te verkrijgen over het uitvoeren van rolexemplaren.
> 
> 

Als u het poortnummer voor een intern eindpunt voor een rolinstantie wilt bepalen, u de eigenschap [InstanceEndpoints](/previous-versions/azure/reference/ee741917(v=azure.100)) gebruiken om een object Dictionary met eindpuntnamen en de bijbehorende IP-adressen en poorten terug te sturen. De eigenschap [IPEndpoint](/previous-versions/azure/reference/ee741919(v=azure.100)) retourneert het IP-adres en de poort voor een opgegeven eindpunt. De eigenschap **PublicIPEndpoint** retourneert de poort voor een laadpunt voor een laadgebalanceerde eindpunt. Het IP-adresgedeelte van de eigenschap **PublicIPEndpoint** wordt niet gebruikt.

Hier is een voorbeeld dat rolexemplaren aangeeft.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Hier is een voorbeeld van een werkrol die het eindpunt wordt blootgesteld via de servicedefinitie en begint te luisteren naar verbindingen.

> [!WARNING]
> Deze code werkt alleen voor een geïmplementeerde service. Bij het uitvoeren in de Azure Compute Emulator worden serviceconfiguratie-elementen die direct poorteindpunten **(InstanceInputEndpoint-elementen)** maken, genegeerd.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Netwerkverkeersregels voor het beheren van rolcommunicatie
Nadat u interne eindpunten hebt gedefinieerd, u netwerkverkeersregels toevoegen (op basis van de eindpunten die u hebt gemaakt) om te bepalen hoe rolinstanties met elkaar kunnen communiceren. In het volgende diagram worden enkele veelvoorkomende scenario's weergegeven voor het beheren van rolcommunicatie:

![Scenario's voor netwerkverkeersregels](./media/cloud-services-enable-communication-role-instances/scenarios.png "Scenario's voor netwerkverkeersregels")

In het volgende codevoorbeeld worden roldefinities weergegeven voor de rollen die in het vorige diagram worden weergegeven. Elke roldefinitie omvat ten minste één intern eindpunt gedefinieerd:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Beperking van de communicatie tussen rollen kan optreden met interne eindpunten van zowel vaste als automatisch toegewezen poorten.
> 
> 

Nadat een intern eindpunt is gedefinieerd, kan de communicatie standaard zonder beperkingen van elke rol naar het interne eindpunt van een rol stromen. Als u de communicatie wilt beperken, moet u een element **NetworkTrafficRules** toevoegen aan het element **ServiceDefinitie** in het servicedefinitiebestand.

### <a name="scenario-1"></a>Scenario 1
Sta alleen netwerkverkeer toe van **WebRole1** naar **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scenario 2
Hiermee wordt alleen netwerkverkeer van **WebRole1** naar **WorkerRole1** en **WorkerRole2 mogelijk**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scenario 3
Hiermee wordt alleen netwerkverkeer van **WebRole1** naar **WorkerRole1**en **WorkerRole1** naar **WorkerRole2 mogelijk**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scenario 4
Hiermee wordt alleen netwerkverkeer van **WebRole1** naar **WorkerRole1,** **WebRole1** naar **WorkerRole2**en **WorkerRole1** naar **WorkerRole2 mogelijk**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Een XML-schemareferentie voor de hierboven gebruikte elementen is [hier](/previous-versions/azure/reference/gg557551(v=azure.100))te vinden .

## <a name="next-steps"></a>Volgende stappen
Lees meer over het Cloud [Service-model](cloud-services-model-and-package.md).




