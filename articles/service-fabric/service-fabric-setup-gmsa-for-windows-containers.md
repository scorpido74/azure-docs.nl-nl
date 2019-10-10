---
title: GMSA instellen voor Azure Service Fabric container Services | Microsoft Docs
description: Meer informatie over het instellen van gMSA voor een container die wordt uitgevoerd in azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: atsenthi
ms.openlocfilehash: 45fc4c924a8fb794ad81529de74b98ee812f46c5
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170427"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>GMSA instellen voor Windows-containers die worden uitgevoerd op Service Fabric

Voor het instellen van gMSA (door een groep beheerde service accounts) wordt een referentie bestand voor referenties (`credspec`) op alle knoop punten in het cluster geplaatst. Het bestand kan worden gekopieerd op alle knoop punten met behulp van een VM-extensie.  Het `credspec`-bestand moet de gMSA-account gegevens bevatten. Zie [een referentie specificatie maken](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)voor meer informatie over het `credspec`-bestand. De referentie specificatie en de code `Hostname` worden opgegeven in het manifest van de toepassing. De `Hostname`-tag moet overeenkomen met de naam van het gMSA-account dat de container wordt uitgevoerd.  Met de tag `Hostname` kan de container zichzelf verifiëren bij andere services in het domein met behulp van Kerberos-verificatie.  In het volgende code fragment wordt een voor beeld weer gegeven voor het opgeven van de `Hostname` en de `credspec` in het manifest van de toepassing:

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
