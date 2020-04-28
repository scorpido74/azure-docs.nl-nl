---
title: GMSA instellen voor Azure Service Fabric container Services
description: Meer informatie over het instellen van door een groep beheerde service accounts (gMSA) voor een container die wordt uitgevoerd in azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75639204"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>GMSA instellen voor Windows-containers die worden uitgevoerd op Service Fabric

Voor het instellen van gMSA (door een groep beheerde service accounts), wordt een`credspec`referentie-specificatie bestand () op alle knoop punten in het cluster geplaatst. Het bestand kan worden gekopieerd op alle knoop punten met behulp van een VM-extensie.  Het `credspec` bestand moet de gMSA-account gegevens bevatten. Zie `credspec` [een referentie specificatie maken](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)voor meer informatie over het bestand. De referentie specificatie en de `Hostname` -tag worden opgegeven in het manifest van de toepassing. Het `Hostname` label moet overeenkomen met de naam van het gMSA-account dat de container wordt uitgevoerd.  Met `Hostname` de tag kan de container zichzelf verifiëren bij andere services in het domein met behulp van Kerberos-verificatie.  In het volgende code fragment `Hostname` wordt een `credspec` voor beeld gegeven voor het opgeven en de in het manifest van de toepassing:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Lees de volgende artikelen als volgende stap:

* [Een Windows-container implementeren voor het Service Fabric op Windows Server 2016](service-fabric-get-started-containers.md)
* [Een docker-container implementeren voor Service Fabric op Linux](service-fabric-get-started-containers-linux.md)
