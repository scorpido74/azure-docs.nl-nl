---
title: GMSA instellen voor Containerservices voor Azure Service Fabric
description: Lees nu of u de groep Managed Service Accounts (gMSA) wilt instellen voor een container die wordt uitgevoerd in Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639204"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>GMSA instellen voor Windows-containers die worden uitgevoerd op Service Fabric

Als u gMSA (groepsmanaged serviceaccounts) wilt`credspec`instellen, wordt een bestand met referentiespecificatie ( ) op alle knooppunten in het cluster geplaatst. Het bestand kan op alle knooppunten worden gekopieerd met behulp van een VM-extensie.  Het `credspec` bestand moet de gMSA-accountgegevens bevatten. Zie [Een referentiespeconie maken](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)voor meer informatie over het `credspec` bestand. De referentiespecificatie `Hostname` en de tag zijn opgegeven in het toepassingsmanifest. De `Hostname` tag moet overeenkomen met de naam van het GMSA-account waarop de container wordt uitgevoerd.  Met `Hostname` de tag kan de container zich verifiëren naar andere services in het domein met Kerberos-verificatie.  In het volgende `Hostname` fragment `credspec` wordt een voorbeeld weergegeven voor het opgeven van het en het in het toepassingsmanifest:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Lees als volgende stap de volgende artikelen:

* [Een Windows-container implementeren voor servicefabric op Windows Server 2016](service-fabric-get-started-containers.md)
* [Een Docker-container implementeren voor servicefabric op Linux](service-fabric-get-started-containers-linux.md)
