---
title: Het zelfstandige Azure Service Fabric-pakket voor Windows Server
description: Beschrijving en inhoud van het zelfstandige Azure Service Fabric-pakket voor Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451852"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Inhoud van Service Fabric zelfstandig pakket voor Windows Server
In het zelfstandige pakket [gedownloade](https://go.microsoft.com/fwlink/?LinkId=730690) service Fabric ziet u de volgende bestanden:

| **Bestandsnaam** | **Korte beschrijving** |
| --- | --- |
| CreateServiceFabricCluster. ps1 |Een Power shell-script dat het cluster maakt met behulp van de instellingen in ClusterConfig. json. |
| RemoveServiceFabricCluster. ps1 |Een Power shell-script waarmee een cluster wordt verwijderd met behulp van de instellingen in ClusterConfig. json. |
| AddNode. ps1 |Een Power shell-script voor het toevoegen van een knoop punt aan een bestaand geïmplementeerd cluster op de huidige computer. |
| RemoveNode. ps1 |Een Power shell-script voor het verwijderen van een knoop punt uit een bestaand geïmplementeerd cluster op de huidige computer. |
| CleanFabric. ps1 |Een Power shell-script voor het opschonen van een zelfstandige Service Fabric installatie van de huidige computer. Eerdere MSI-installaties moeten worden verwijderd met behulp van hun eigen bijbehorende verwijderingen. |
| De testconfiguration. ps1 |Een Power shell-script voor het analyseren van de infra structuur zoals opgegeven in de cluster. json. |
| DownloadServiceFabricRuntimePackage. ps1 |Een Power shell-script dat wordt gebruikt voor het buiten-band downloaden van het meest recente runtime pakket, voor scenario's waarbij de implementatie van de computer niet is verbonden met internet. |
| DeploymentComponentsAutoextractor. exe |Zelfuitpakkend archief met implementatie onderdelen die worden gebruikt door de scripts van het zelfstandige pakket. |
| EULA_ENU. txt |De licentie voorwaarden voor het gebruik van Microsoft Azure Service Fabric zelfstandige Windows Server-pakket. U kunt nu [een exemplaar van de gebruiksrecht overeenkomst downloaden](https://go.microsoft.com/fwlink/?LinkID=733084) . |
| README. txt |Een koppeling naar de release opmerkingen en de basis installatie-instructies. Het is een subset van de instructies in dit document. |
| ThirdPartyNotice. RTF |Kennisgeving van software van derden die deel uitmaakt van het pakket. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector. exe die op aanvraag wordt uitgevoerd om traceer logboeken te verzamelen en uploaden naar micro soft voor ondersteunings doeleinden. |
| Tools\ServiceFabricUpdateService.zip |Een hulp programma dat wordt gebruikt om automatische code-upgrade in te scha kelen voor clusters die geen toegang tot internet hebben. Meer informatie vindt u [hier](service-fabric-cluster-upgrade-windows-server.md)|

**Sjablonen** 

| **Bestandsnaam** | **Korte beschrijving** |
| --- | --- |
| ClusterConfig. unsecure. DevCluster. json |Een voorbeeld bestand van een cluster configuratie met daarin de instellingen voor een niet-beveiligd, drie knoop punt (of virtuele machine) ontwikkel cluster, met inbegrip van de informatie voor elk knoop punt in het cluster. |
| ClusterConfig. onbeveiligde. json |Een voorbeeld bestand van een cluster configuratie met de instellingen voor een niet-beveiligd cluster voor meerdere machines (of virtuele machines), met inbegrip van de informatie voor elke computer in het cluster. |
| ClusterConfig. Windows. DevCluster. json |Een voor beeld van een cluster configuratie dat alle instellingen bevat voor een veilig ontwikkel cluster met één computer (of virtuele machine) met een enkele machine, met inbegrip van de informatie voor elk knoop punt in het cluster. Het cluster wordt beveiligd met behulp van [Windows-identiteiten](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig. Windows. multimachine. json |Een voor beeld van een cluster configuratie dat alle instellingen bevat voor een beveiligde, multi machine-of virtuele-machine-cluster met behulp van Windows-beveiliging, met inbegrip van de informatie voor elke computer die zich in het beveiligde cluster bevindt. Het cluster wordt beveiligd met behulp van [Windows-identiteiten](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig. x509. DevCluster. json |Een voor beeld van een cluster configuratie dat alle instellingen bevat voor een veilig ontwikkel cluster met één computer (of virtuele machine) met een enkele machine, met inbegrip van de informatie voor elk knoop punt in het cluster. Het cluster wordt beveiligd met x509-certificaten. |
| ClusterConfig. x509. multimachine. json |Een voorbeeld bestand van een cluster configuratie dat alle instellingen bevat voor het beveiligde, meerdere machine-of virtuele-machine-cluster, inclusief de informatie voor elk knoop punt in het beveiligde cluster. Het cluster wordt beveiligd met x509-certificaten. |
| ClusterConfig. gMSA. Windows. multimachine. json |Een voorbeeld bestand van een cluster configuratie dat alle instellingen bevat voor het beveiligde, meerdere machine-of virtuele-machine-cluster, inclusief de informatie voor elk knoop punt in het beveiligde cluster. Het cluster wordt beveiligd met [beheerde service accounts voor groepen](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Voor beelden van cluster configuratie
De meest recente versies van de cluster configuratie sjablonen vindt u op de pagina GitHub: voor [beelden van zelfstandige cluster configuraties](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Onafhankelijk runtime pakket
Het meest recente runtime pakket wordt automatisch gedownload tijdens de cluster implementatie vanaf de [Download koppeling-service Fabric runtime-Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Verwant
* [Een zelfstandige Azure Service Fabric-cluster maken](service-fabric-cluster-creation-for-windows-server.md)
* [Beveiligings scenario's voor Service Fabric cluster](service-fabric-windows-cluster-windows-security.md)
