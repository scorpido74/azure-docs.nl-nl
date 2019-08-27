---
title: Een Windows-ontwikkelomgeving voor Azure-microservices instellen | Microsoft Docs
description: Installeer de runtime, SDK en hulpprogramma's en maak een lokaal ontwikkelcluster. Zodra u dit hebt gedaan, kunt u toepassingen bouwen in Windows.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/22/2019
ms.author: atsenthi
ms.openlocfilehash: 0bc8a5bbecc4e7d58265e9c10c2c8a3351fcc312
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036015"
---
# <a name="prepare-your-development-environment-on-windows"></a>Uw ontwikkelomgeving voorbereiden in Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Als u [Azure service Fabric-toepassingen][1] wilt bouwen en uitvoeren op uw Windows-ontwikkel computer, installeert u de service Fabric runtime, SDK en hulpprogram ma's. U moet ook [de uitvoering van de Windows PowerShell-scripts inschakelen](#enable-powershell-script-execution) die in de SDK zijn opgenomen.

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
> - Windows 7 bevat standaard alleen Windows PowerShell 2.0. Voor Service Fabric PowerShell-cmdlets is PowerShell 3.0 of hoger vereist. U kunt [Windows Power shell 5,0 downloaden][powershell5-download] van het micro soft Download centrum.
> - Er is geen omgekeerde proxy voor Service Fabric beschikbaar in Windows 7.
>

## <a name="install-the-sdk-and-tools"></a>De SDK en hulpprogramma's installeren
Web platform Installer (WebPI) is de aanbevolen manier om de SDK en hulpprogram ma's te installeren. Als er runtime-fouten worden ontvangen met behulp van WebPI, kunt u ook directe koppelingen naar de installatie Programma's in de release opmerkingen vinden voor een specifieke Service Fabric versie. De release opmerkingen vindt u in de verschillende release mededelingen op het [service Fabric team blog](https://blogs.msdn.microsoft.com/azureservicefabric/).

> [!NOTE]
> Lokale Service Fabric ontwikkeling van cluster upgrades worden niet ondersteund.

### <a name="to-use-visual-studio-2017-or-2019"></a>Visual Studio 2017 of 2019 gebruiken
De Service Fabric-Hulpprogram Ma's maken deel uit van de Azure Development-werk belasting in Visual Studio 2017 en 2019. Schakel deze workload in als onderdeel van de Visual Studio-installatie.
Bovendien moet u de Microsoft Azure Service Fabric SDK en runtime installeren met behulp van het webplatforminstallatieprogramma.

* [Microsoft Azure Service Fabric SDK installeren][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Visual Studio 2015 gebruiken (hiervoor is Visual Studio 2015 Update 2 of later vereist)
In Visual Studio 2015 worden de hulpprogramma's voor Service Fabric samen met de SDK en runtime geïnstalleerd met behulp van het webplatforminstallatieprogramma:

* [De Microsoft Azure Service Fabric SDK en Hulpprogram Ma's installeren][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Alleen SDK-installatie
Als u alleen de SDK nodig hebt, kunt u dit pakket installeren:
* [Microsoft Azure Service Fabric SDK installeren][core-sdk]

De huidige versies zijn:
* Service Fabric SDK en Hulpprogram Ma's 3.4.664
* Service Fabric runtime-6.5.664
* Service Fabric-Hulpprogram Ma's voor Visual Studio 2015 2.5.20615.1
* Visual Studio 2017 15,9 bevat Service Fabric-Hulpprogram Ma's voor Visual Studio 2.4.11024.1 
* Visual Studio 2019 16,1 bevat Service Fabric-Hulpprogram Ma's voor Visual Studio 2.5.20423.3

Zie [service Fabric versies](service-fabric-versions.md) voor een lijst met ondersteunde versies

> [!NOTE]
> Clusters met één computer (OneBox) worden niet ondersteund voor upgrades van toepassingen of clusters; Verwijder het OneBox-cluster en maak het opnieuw als u een cluster upgrade moet uitvoeren of als u problemen hebt met het uitvoeren van een toepassings upgrade. 

## <a name="enable-powershell-script-execution"></a>Uitvoering van PowerShell-script inschakelen
Service Fabric gebruikt Windows PowerShell-scripts om een lokaal ontwikkelcluster te maken en om toepassingen vanuit Visual Studio te implementeren. Standaard worden deze scripts door Windows geblokkeerd zodat ze niet worden uitgevoerd. Als u ze wilt inschakelen, moet u het PowerShell-uitvoeringsbeleid wijzigen. Open PowerShell als een beheerder en voer de volgende opdracht in:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Docker installeren (optioneel)
[Service Fabric is een container Orchestrator](service-fabric-containers-overview.md) voor het implementeren van micro Services op een cluster van machines. Als u Windows-container toepassingen wilt uitvoeren op uw lokale ontwikkel cluster, moet u eerst docker voor Windows installeren. Haal [docker CE voor Windows (stabiel) op](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Nadat u Docker hebt geïnstalleerd en gestart, klikt u met de rechtermuisknop op het systeemvakpictogram en selecteert u **Overschakelen naar Windows-containers**. Deze stap is vereist voor het uitvoeren van Docker-installatiekopieën onder Windows.

## <a name="next-steps"></a>Volgende stappen
Nu u uw ontwikkelingsomgeving hebt ingesteld, kunt u apps ontwikkelen en uitvoeren.

* [Meer informatie over het maken, implementeren en beheren van toepassingen](service-fabric-tutorial-create-dotnet-app.md)
* [Meer informatie over de programmeer modellen: Reliable Services en Reliable Actors](service-fabric-choose-framework.md)
* [Voorbeelden van Service Fabric-code op GitHub bekijken](https://aka.ms/servicefabricsamples)
* [Uw cluster visualiseren door gebruik te maken van Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric-campagnepagina"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI-koppeling"
[full-bundle-dev15]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI-koppeling"
[core-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI-koppeling"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
