---
title: Releases van Azure Service Fabric
description: Release opmerkingen voor de nieuwste functies en verbeteringen in Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 984e7ed82ba81d8c40688efd0a0a06e095be7c06
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018989"
---
# <a name="service-fabric-releases"></a>Service Fabric releases

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Probleemoplossings handleidingen</a> 
| ondersteunings<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Opties</a> 
| voor<a href="https://github.com/Azure/service-fabric-issues" target="blank">probleem</a> 
| oplossing<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">ondersteunde versies</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">code voorbeelden</a>

In dit artikel vindt u meer informatie over de nieuwste releases en updates voor de Service Fabric runtime en Sdk's.

## <a name="whats-new-in-service-fabric"></a>Wat is er nieuw in Service Fabric

### <a name="service-fabric-65"></a>Service Fabric 6,5

De nieuwste Service Fabric versie bevat verbeteringen op het gebied van ondersteuning, betrouw baarheid en prestaties, nieuwe functies, fout oplossingen en verbeteringen voor het vereenvoudigen van het beheer van de cluster-en toepassings levenscyclus.

> [!IMPORTANT]
> Service Fabric 6,5 is de definitieve versie met de ondersteuning voor Service Fabric-hulpprogram ma's in Visual Studio 2015. Klanten wordt geadviseerd om over te stappen op [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) .

Hier is what's nieuw in Service Fabric 6,5:

- Service Fabric Explorer bevat een [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) voor het controleren van de toepassingen die u hebt geüpload naar het archief met installatie kopieën.

- De [poa-versie (patch Orchestration Application)](service-fabric-patch-orchestration-application.md) [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) bevat veel zelf diagnostische verbeteringen. Klanten van POA worden aanbevolen om over te stappen op deze versie.

- De [Event Store-service is standaard ingeschakeld](service-fabric-visualizing-your-cluster.md#event-store) voor service Fabric 6,5-clusters, tenzij u ervoor hebt gekozen.

- Er zijn [replica levenscyclus gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md#replica-events) voor stateful services toegevoegd.

- [Betere zicht baarheid van de status van het Seed-knoop punt](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inclusief waarschuwingen op cluster niveau als een Seed-knoop punt een slechte status heeft (niet*beschikbaar*, *verwijderd* of *onbekend*).

- Met [service Fabric hulp programma voor nood herstel](https://github.com/Microsoft/Service-Fabric-AppDRTool) van de toepassing kunnen service Fabric stateful Services snel worden hersteld wanneer het primaire cluster een nood geval tegen komt. Gegevens van een primair cluster worden voortdurend gesynchroniseerd met de secundaire stand-by-toepassing met periodieke back-ups en herstel bewerkingen.

- Visual Studio-ondersteuning voor [het publiceren van .net core-apps naar op Linux gebaseerde clusters](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure service Fabric cli (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) wordt automatisch geïnstalleerd voor service Fabric 6,5 (en latere versies) wanneer u een upgrade uitvoert of een nieuw Linux-cluster maakt in Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) wordt standaard geïnstalleerd in MacOS/Linux Onebox-clusters.

Zie de [release opmerkingen voor Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)voor meer informatie.

### <a name="service-fabric-65-releases"></a>Service Fabric 6,5 Releases

| Uitgebracht op | Release | Meer informatie |
|---|---|---|
| 11 juni 2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 juli 2019 | [Release van Azure Service Fabric 6,5 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 juli 2019 | [Release van Azure Service Fabric 6,5 vernieuwen](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Augustus 23, 2019 | [Release van Azure Service Fabric 6,5 vernieuwen](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |

## <a name="previous-versions"></a>Vorige versies

### <a name="service-fabric-64-releases"></a>Service Fabric 6,4 Releases

| Uitgebracht op | Release | Meer informatie |
|---|---|---|
| 30 november 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 december 2018 | [Azure Service Fabric 6,4-release voor Windows-clusters vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 februari 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 maart 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 april 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 mei 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 mei 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
