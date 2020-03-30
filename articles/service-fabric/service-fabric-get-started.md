---
title: Een Windows-ontwikkelomgeving instellen
description: Installeer de runtime, SDK en hulpprogramma's en maak een lokaal ontwikkelcluster. Zodra u dit hebt gedaan, kunt u toepassingen bouwen in Windows.
author: peterpogorski
ms.topic: conceptual
ms.date: 03/02/2020
ms.custom: sfrev
ms.openlocfilehash: f08c6b0675475b4e15ce6db3a9dbe0e2863b9ddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252773"
---
# <a name="prepare-your-development-environment-on-windows"></a>Uw ontwikkelomgeving voorbereiden in Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>

Als u [Azure Service Fabric-toepassingen][1] wilt bouwen en uitvoeren op een Windows-ontwikkelmachine, moet u de Service Fabric-runtime, SDK en hulpprogramma's installeren. U moet ook [de uitvoering van de Windows PowerShell-scripts in](#enable-powershell-script-execution) de SDK inschakelen.

## <a name="prerequisites"></a>Vereisten

### <a name="supported-operating-system-versions"></a>Ondersteunde versies van besturingssystemen

De volgende versies van besturingssystemen worden ondersteund voor de ontwikkeling:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Ondersteuning voor Windows 7:
> - Windows 7 bevat standaard alleen Windows PowerShell 2.0. Voor Service Fabric PowerShell-cmdlets is PowerShell 3.0 of hoger vereist. U [Windows PowerShell 5.1 downloaden][powershell5-download] vanuit het Microsoft Downloadcentrum.
> - Er is geen omgekeerde proxy voor Service Fabric beschikbaar in Windows 7.

## <a name="install-the-sdk-and-tools"></a>De SDK en hulpprogramma's installeren

Web Platform Installer (WebPI) is de aanbevolen manier om de SDK en tools te installeren. Als u runtime-fouten ontvangt met WebPI, u in de releasenotes ook directe koppelingen naar de installateurs vinden voor een specifieke Service Fabric-release. De release notes zijn te vinden in de verschillende release aankondigingen op de [Service Fabric team blog](https://blogs.msdn.microsoft.com/azureservicefabric/).

> [!NOTE]
> Upgrades van lokale servicefabric-ontwikkelingsclusterworden niet ondersteund.

### <a name="to-use-visual-studio-2017-or-2019"></a>Visual Studio 2017 of 2019 gebruiken

De Hulpprogramma's voor servicefabriczijn onderdeel van de Azure Development-workload in Visual Studio 2017 en 2019. Schakel deze workload in als onderdeel van de Visual Studio-installatie.
Bovendien moet u de Microsoft Azure Service Fabric SDK en runtime installeren met behulp van het webplatforminstallatieprogramma.

* [Microsoft Azure Service Fabric SDK installeren][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Visual Studio 2015 gebruiken (hiervoor is Visual Studio 2015 Update 2 of later vereist)

In Visual Studio 2015 worden de hulpprogramma's voor Service Fabric samen met de SDK en runtime geïnstalleerd met behulp van het webplatforminstallatieprogramma:

* [De Microsoft Azure Service Fabric SDK en -hulpprogramma's installeren][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Alleen SDK-installatie

Als u alleen de SDK nodig hebt, kunt u dit pakket installeren:

* [Microsoft Azure Service Fabric SDK installeren][core-sdk]

De huidige versies zijn:

* Service Fabric SDK en Tools 4.0.470
* Runtime van servicestof 7.0.470

Zie [Service Fabric-versies](service-fabric-versions.md) voor een lijst met ondersteunde versies

> [!NOTE]
> Single machine clusters (OneBox) worden niet ondersteund voor application of cluster upgrades; Verwijder het OneBox-cluster en maak het opnieuw als u een clusterupgrade moet uitvoeren of problemen hebt met het uitvoeren van een toepassingsupgrade. 

## <a name="enable-powershell-script-execution"></a>Uitvoering van PowerShell-script inschakelen

Service Fabric gebruikt Windows PowerShell-scripts om een lokaal ontwikkelcluster te maken en om toepassingen vanuit Visual Studio te implementeren. Standaard worden deze scripts door Windows geblokkeerd zodat ze niet worden uitgevoerd. Als u ze wilt inschakelen, moet u het PowerShell-uitvoeringsbeleid wijzigen. Open PowerShell als een beheerder en voer de volgende opdracht in:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>Docker installeren (optioneel)

[Service Fabric is een containerorchestrator](service-fabric-containers-overview.md) voor het implementeren van microservices in een cluster van machines. Als u Windows-containertoepassingen wilt uitvoeren op uw lokale ontwikkelingscluster, moet u docker voor Windows eerst installeren. Haal [Docker CE voor Windows (stabiel)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Nadat u Docker hebt geïnstalleerd en gestart, klikt u met de rechtermuisknop op het systeemvakpictogram en selecteert u **Overschakelen naar Windows-containers**. Deze stap is vereist voor het uitvoeren van Docker-installatiekopieën onder Windows.

## <a name="next-steps"></a>Volgende stappen

Nu u uw ontwikkelingsomgeving hebt ingesteld, kunt u apps ontwikkelen en uitvoeren.

* [Meer informatie over het maken, implementeren en beheren van toepassingen](service-fabric-tutorial-create-dotnet-app.md)
* [Meer informatie over de programmeermodellen: Reliable Services en Reliable Actors](service-fabric-choose-framework.md)
* [Voorbeelden van Service Fabric-code op GitHub bekijken](https://aka.ms/servicefabricsamples)
* [Uw cluster visualiseren door gebruik te maken van Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric-campagnepagina"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI-koppeling"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI-koppeling"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI-koppeling"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
