---
title: Het zelfstandige Azure Service Fabric-pakket voor Windows Server
description: Beschrijving en de inhoud van het Azure Service Fabric zelfstandige pakket voor Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451852"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Inhoud van Service Fabric zelfstandige pakket voor Windows Server
In de [gedownload](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric zelfstandige pakket, vindt u de volgende bestanden:

| **Bestandsnaam** | **Korte beschrijving** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Een PowerShell-script dat maakt het cluster met de instellingen in ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Een PowerShell-script dat een cluster met de instellingen in ClusterConfig.json verwijdert. |
| AddNode.ps1 |Een PowerShell-script voor het toevoegen van een knooppunt aan een bestaand cluster op de huidige computer geïmplementeerd. |
| RemoveNode.ps1 |Een PowerShell-script voor het verwijderen van een knooppunt van een bestaand cluster op basis van de huidige computer geïmplementeerd. |
| CleanFabric.ps1 |Een PowerShell-script voor het opschonen van een zelfstandige installatie van Service Fabric uit de huidige computer. Vorige MSI-installaties moeten worden verwijderd met behulp van hun eigen gekoppelde uninstallers. |
| TestConfiguration.ps1 |Een PowerShell-script voor het analyseren van de infrastructuur zoals opgegeven in de Cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Een PowerShell-script gebruikt voor het downloaden van de meest recente runtimepakket buiten-bandbeheer, voor scenario's waarin de implementeren machine niet is verbonden met internet. |
| DeploymentComponentsAutoextractor.exe |Zelfuitpakkend archief met onderdelen voor implementatie door het zelfstandige pakket scripts gebruikt. |
| EULA_ENU.txt |De licentievoorwaarden voor het gebruik van Microsoft Azure Service Fabric-pakket voor zelfstandige Windows Server. U kunt [download een exemplaar van de gebruiksrechtovereenkomst](https://go.microsoft.com/fwlink/?LinkID=733084) nu. |
| Leesmij |Een koppeling naar de release-opmerkingen en eenvoudige installatie-instructies. Dit is een subset van de instructies in dit document. |
| ThirdPartyNotice.rtf |Aankondiging van de software van derden die in het pakket. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe die wordt uitgevoerd op aanvraag voor het verzamelen en uploaden van logboeken met traceringen naar Microsoft voor ondersteuning van doel. |
| Tools\ServiceFabricUpdateService.zip |Een hulpprogramma dat wordt gebruikt voor het inschakelen van automatisch code-upgrade voor clusters die geen toegang tot internet hebben. Meer informatie vindt [hier](service-fabric-cluster-upgrade-windows-server.md)|

**Sjablonen** 

| **Bestandsnaam** | **Korte beschrijving** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Een cluster voorbeeld configuratiebestand met de instellingen voor een niet-beveiligde, drie knooppunten, één machine (of virtuele machine) ontwikkelingscluster wijzigen, met inbegrip van de gegevens voor elk knooppunt in het cluster. |
| ClusterConfig.Unsecure.MultiMachine.json |Een cluster voorbeeld configuratiebestand met de instellingen voor een niet-beveiligde, meerdere (of virtuele machine)-cluster, met inbegrip van de gegevens voor elke computer in het cluster. |
| ClusterConfig.Windows.DevCluster.json |Een cluster voorbeeld configuratiebestand met de instellingen voor een veilige, drie knooppunten, één machine (of virtuele machine) ontwikkelingscluster, met inbegrip van de informatie voor elk knooppunt in het cluster. Het cluster is beveiligd met behulp van [Windows identiteiten](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Een cluster voorbeeld configuratiebestand met de instellingen voor een veilige, meerdere (of virtuele machine)-cluster met behulp van Windows-beveiliging, met inbegrip van de gegevens voor elke machine die in het beveiligde cluster. Het cluster is beveiligd met behulp van [Windows identiteiten](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Een cluster voorbeeld configuratiebestand met de instellingen voor een veilige, drie knooppunten, één machine (of virtuele machine) ontwikkelingscluster, met inbegrip van de gegevens voor elk knooppunt in het cluster. Het cluster is beveiligd met behulp van x509 certificaten. |
| ClusterConfig.x509.MultiMachine.json |Een cluster voorbeeld configuratiebestand met de instellingen voor het cluster veilig, meerdere (of virtuele machine), met inbegrip van de gegevens voor elk knooppunt in het beveiligde cluster. Het cluster is beveiligd met behulp van x509 certificaten. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Een cluster voorbeeld configuratiebestand met de instellingen voor het cluster veilig, meerdere (of virtuele machine), met inbegrip van de gegevens voor elk knooppunt in het beveiligde cluster. Het cluster is beveiligd met [Group Managed Service Accounts](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Voorbeelden van configuraties van cluster
Meest recente versies van configuratiesjablonen voor cluster kunnen worden gevonden op de GitHub-pagina: [voorbeelden van configuraties voor zelfstandige Cluster](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Onafhankelijke Runtime-pakket
De meest recente runtimepakket wordt automatisch gedownload tijdens de implementatie van het cluster uit [koppeling downloaden - Service Fabric-Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Verwant
* [Een zelfstandig Azure Service Fabric-cluster maken](service-fabric-cluster-creation-for-windows-server.md)
* [Service Fabric-clusterbeveiligingsscenario 's](service-fabric-windows-cluster-windows-security.md)
