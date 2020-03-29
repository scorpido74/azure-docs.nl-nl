---
title: Azure Service Fabric Standalone-pakket voor Windows Server
description: Beschrijving en inhoud van het Azure Service Fabric Standalone-pakket voor Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451852"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Inhoud van het standalone pakket van servicefabric fabric voor Windows Server
In het [gedownloade](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric Standalone-pakket vindt u de volgende bestanden:

| **Bestandsnaam** | **Korte beschrijving** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Een PowerShell-script dat het cluster maakt met de instellingen in ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Een PowerShell-script dat een cluster verwijdert met de instellingen in ClusterConfig.json. |
| AddNode.ps1 |Een PowerShell-script voor het toevoegen van een knooppunt aan een bestaand geïmplementeerd cluster op de huidige machine. |
| RemoveNode.ps1 |Een PowerShell-script voor het verwijderen van een knooppunt uit een bestaand geïmplementeerd cluster uit de huidige machine. |
| CleanFabric.ps1 |Een PowerShell-script voor het reinigen van een zelfstandige Service Fabric-installatie van de huidige machine. Eerdere MSI-installaties moeten worden verwijderd met behulp van hun eigen bijbehorende niet-installateurs. |
| TestConfiguration.ps1 |Een PowerShell-script voor het analyseren van de infrastructuur zoals opgegeven in cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Een PowerShell-script dat wordt gebruikt voor het downloaden van het nieuwste runtime-pakket buiten de band, voor scenario's waarin de implementatiemachine niet is verbonden met het internet. |
| DeploymentComponentsAutoextractor.exe |Zelf-extraherend archief met implementatiecomponenten die worden gebruikt door de standalone-pakketscripts. |
| EULA_ENU.txt |De licentievoorwaarden voor het gebruik van microsoft Azure Service Fabric standalone Windows Server-pakket. U nu [een exemplaar van de EULA downloaden.](https://go.microsoft.com/fwlink/?LinkID=733084) |
| Readme.txt |Een link naar de release notes en basisinstallatie-instructies. Het is een subset van de instructies in dit document. |
| ThirdPartyNotice.rtf |Bericht van software van derden die in het pakket. |
| Hulpprogramma's\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe die op aanvraag wordt uitgevoerd om trace logs te verzamelen en uploaden naar Microsoft voor ondersteuningsdoeleinden. |
| Tools\ServiceFabricUpdateService.zip |Een tool die wordt gebruikt om automatische code-upgrade in te schakelen voor clusters die geen toegang tot internet hebben. Meer details vindt u [hier](service-fabric-cluster-upgrade-windows-server.md)|

**Sjablonen** 

| **Bestandsnaam** | **Korte beschrijving** |
| --- | --- |
| ClusterConfig.Onveilig.DevCluster.json |Een clusterconfiguratievoorbeeldbestand dat de instellingen bevat voor een onbeveiligd cluster met drie delen, één machine (of virtuele machine), inclusief de informatie voor elk knooppunt in het cluster. |
| ClusterConfig.Onsecure.MultiMachine.json |Een clusterconfiguratievoorbeeldbestand dat de instellingen bevat voor een onbeveiligd cluster met meerdere machines (of virtuele machines), inclusief de informatie voor elke machine in het cluster. |
| ClusterConfig.Windows.DevCluster.json |Een clusterconfiguratievoorbeeldbestand dat alle instellingen bevat voor een cluster met drie delen, één machine (of virtuele machine), inclusief de informatie voor elk knooppunt dat zich in het cluster bevindt. Het cluster wordt beveiligd met [Windows-identiteiten.](https://msdn.microsoft.com/library/ff649396.aspx) |
| ClusterConfig.Windows.MultiMachine.json |Een voorbeeldbestand voor clusterconfiguratie dat alle instellingen bevat voor een beveiligd cluster met meerdere machines (of virtuele machines) met Windows-beveiliging, inclusief de informatie voor elke machine die zich in het beveiligde cluster bevindt. Het cluster wordt beveiligd met [Windows-identiteiten.](https://msdn.microsoft.com/library/ff649396.aspx) |
| ClusterConfig.x509.DevCluster.json |Een clusterconfiguratievoorbeeldbestand dat alle instellingen bevat voor een beveiligd cluster met drie delen, één machine (of virtuele machine), inclusief de informatie voor elk knooppunt in het cluster. Het cluster is beveiligd met x509-certificaten. |
| ClusterConfig.x509.MultiMachine.json |Een clusterconfiguratievoorbeeldbestand dat alle instellingen bevat voor het cluster met meerdere machines (of virtuele machines), inclusief de informatie voor elk knooppunt in het beveiligde cluster. Het cluster is beveiligd met x509-certificaten. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Een clusterconfiguratievoorbeeldbestand dat alle instellingen bevat voor het cluster met meerdere machines (of virtuele machines), inclusief de informatie voor elk knooppunt in het beveiligde cluster. Het cluster is beveiligd met [groepsbeheerserviceaccounts](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Clusterconfiguratievoorbeelden
De nieuwste versies van clusterconfiguratiesjablonen zijn te vinden op de GitHub-pagina: [Standalone Cluster Configuration Samples](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Onafhankelijk runtime-pakket
Het nieuwste runtime-pakket wordt automatisch gedownload tijdens de implementatie van het cluster van [Download Link - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Verwant
* [Een zelfstandig Azure Service Fabric-cluster maken](service-fabric-cluster-creation-for-windows-server.md)
* [Beveiligingsscenario's voor servicefabric-cluster](service-fabric-windows-cluster-windows-security.md)
