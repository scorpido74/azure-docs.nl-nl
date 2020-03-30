---
title: Fouten opsporen in Windows-containers met Service Fabric en VS
description: Meer informatie over het opsporen van Windows-containers in Azure Service Fabric met Visual Studio 2019.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 2a00a352d09562ffe46dc8e6e63a5d4963ac3a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127632"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>How to: Windows-containers debuggen in Azure Service Fabric met Visual Studio 2019

Met Visual Studio 2019 u .NET-toepassingen in containers debuggen als Service Fabric-services. In dit artikel ziet u hoe u uw omgeving configureert en vervolgens een .NET-toepassing debugt in een container die wordt uitgevoerd in een lokaal cluster van Servicefabric.

## <a name="prerequisites"></a>Vereisten

* Volg deze snelstart in Windows 10 om [Windows 10 te configureren voor het uitvoeren van Windows-containers](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* Volg op Windows Server 2016 deze snelstart om [Windows 2016 te configureren voor het uitvoeren van Windows-containers](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Uw lokale Service Fabric-omgeving instellen door [uw ontwikkelomgeving voorbereiden op Windows te](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) volgen

## <a name="configure-your-developer-environment-to-debug-containers"></a>Uw ontwikkelaarsomgeving configureren om containers te opsporen

1. Controleer of de docker voor vensterservice wordt uitgevoerd voordat u verdergaat met de volgende stap.

1. Als u DNS-resolutie tussen containers wilt ondersteunen, moet u uw lokale ontwikkelingscluster instellen met de naam van de machine. Deze stappen zijn ook nodig als u services via de omgekeerde proxy wilt aanpakken.
   1. PowerShell openen als beheerder
   2. Navigeer naar de installatiemap van `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`het SDK-cluster, meestal .
   3. Het script uitvoeren`DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > U `-CreateOneNodeCluster` de cluster met één knooppunt instellen. Met de standaardinstelling wordt een lokaal cluster met vijf nodes gemaakt.
      >

      Zie [DNS-service in Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice)voor meer informatie over de DNS-service in servicefabric. Zie Speciale afhandeling van proxy voor services die [in containers worden uitgevoerd voor](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers)meer informatie over het gebruik van de reverse proxy van Service Fabric van services die in een container worden uitgevoerd.

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Bekende beperkingen bij het debuggen van containers in Service Fabric

Hieronder vindt u een lijst met bekende beperkingen met foutopsporingscontainers in Service Fabric en mogelijke oplossingen:

* Het gebruik van localhost voor ClusterFQDNorIP ondersteunt geen DNS-resolutie in containers.
    * Resolutie: het lokale cluster instellen met de naam van de machine (zie hierboven)
* Als u Windows10 in een virtuele machine uitvoert, wordt het DNS-antwoord niet terugnaar de container getreid.
    * Resolutie: UDP-controlesom uitschakelen voor IPv4 op de NIC virtuele machines
    * Als u Windows10 uitvoert, worden de netwerkprestaties op de machine verslechteren.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Het oplossen van services in dezelfde toepassing met DNS-servicenaam werkt niet op Windows10, als de toepassing is geïmplementeerd met Docker Compose
    * Oplossing: Servicename.applicationname gebruiken om serviceeindpunten op te lossen
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Als u IP-adres gebruikt voor ClusterFQDNorIP, wordt het wijzigen van primaire IP op de host afgebroken van DNS-functionaliteit.
    * Resolutie: Maak het cluster opnieuw met het nieuwe primaire IP op de host of gebruik de naam van de machine. Deze breuk is door het ontwerp.
* Als de FQDN waarmee het cluster is gemaakt niet oplosbaar is op het netwerk, mislukt DNS.
    * Oplossing: maak het lokale cluster opnieuw met het primaire IP-adres van de host. Deze fout is door het ontwerp.
* Wanneer u een container debugt, zijn dockerlogboeken alleen beschikbaar in het uitvoervenster Visual Studio, niet via Service Fabric API's, waaronder Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Foutopsporing van een .NET-toepassing die wordt uitgevoerd in dockercontainers op ServiceFabric

1. Voer Visual Studio uit als beheerder.

1. Open een bestaande .NET-toepassing of maak een nieuwe toepassing.

1. Klik met de rechtermuisknop op het project en selecteer Ondersteuning voor **> containerorkestor** toevoegen >

1. Druk op **F5** om de toepassing te debuggen.

    Visual Studio ondersteunt console- en ASP.NET projecttypen voor .NET en .NET Core.

## <a name="next-steps"></a>Volgende stappen
Zie Service [Fabric-containers overzicht](service-fabric-containers-overview.md)voor meer informatie over de mogelijkheden van Service Fabric en containers.
