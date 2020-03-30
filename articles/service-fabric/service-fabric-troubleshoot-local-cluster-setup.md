---
title: Problemen met de installatie van uw lokale Azure Service Fabric-cluster oplossen
description: Dit artikel bevat een reeks suggesties voor het oplossen van problemen met uw lokale ontwikkelingscluster
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: ea313adb43f8d91ec9e57dd1d0b8d3447a8075f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465504"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Problemen met de instellingen van uw lokale ontwikkelingscluster oplossen
Als u een probleem tegenkomt tijdens de interactie met uw lokale Azure Service Fabric-ontwikkelingscluster, bekijkt u de volgende suggesties voor mogelijke oplossingen.

## <a name="cluster-setup-failures"></a>Fouten in clusterinstellingen
### <a name="cannot-clean-up-service-fabric-logs"></a>Logboeken van servicefabric kan niet worden opgeruimd
#### <a name="problem"></a>Probleem
Tijdens het uitvoeren van het script DevClusterSetup ziet u de volgende fout:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Oplossing
Sluit het huidige PowerShell-venster en open een nieuw PowerShell-venster als beheerder. U het script nu met succes uitvoeren.

## <a name="cluster-connection-failures"></a>Fouten in clusterverbinding

### <a name="type-initialization-exception"></a>Uitzondering voor initialisatie typen
#### <a name="problem"></a>Probleem
Wanneer u verbinding maakt met het cluster in PowerShell, ziet u de fout TypeInitializationException voor System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Oplossing
Uw padvariabele is tijdens de installatie niet correct ingesteld. Meld u af bij Windows en meld u weer aan. Dit vernieuwt je pad.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Clusterverbinding mislukt met 'Object is gesloten'
#### <a name="problem"></a>Probleem
Een oproep naar Connect-ServiceFabricCluster mislukt met een fout als deze:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Oplossing
Sluit het huidige PowerShell-venster en open een nieuw PowerShell-venster als beheerder.

### <a name="fabric-connection-denied-exception"></a>Uitzondering voor fabricverbinding geweigerd
#### <a name="problem"></a>Probleem
Wanneer u debugt vanuit Visual Studio, krijgt u een fout van FabricConnectionDeniedException.

#### <a name="solution"></a>Oplossing
Deze fout treedt meestal op wanneer u een servicehostproces handmatig probeert te starten.

Zorg ervoor dat u geen serviceprojecten hebt ingesteld als opstartprojecten in uw oplossing. Alleen servicefabric-toepassingsprojecten moeten worden ingesteld als opstartprojecten.

> [!TIP]
> Als uw lokale cluster zich na de installatie abnormaal begint te gedragen, u het opnieuw instellen met behulp van de toepassing van de lokale clusterbeheersysteemlade. Hiermee wordt het bestaande cluster verwijderd en een nieuw cluster ingesteld. Houd er rekening mee dat alle geïmplementeerde toepassingen en bijbehorende gegevens worden verwijderd.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* [Uw cluster begrijpen en oplossen met systeemstatusrapporten](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualisern van uw cluster met Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

