---
title: Releases van Azure Service Fabric
description: Release opmerkingen bij Azure Service Fabric. Bevat informatie over de nieuwste functies en verbeteringen in Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: cb4fdd56e9cf67c71ac690d423499929167f8977
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064238"
---
# <a name="service-fabric-releases"></a>Service Fabric releases

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">richt lijnen voor probleem oplossing</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">problemen bijhouden</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">ondersteunings opties</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">ondersteunde versies</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">code voorbeelden</a>

In dit artikel vindt u meer informatie over de nieuwste releases en updates voor de Service Fabric runtime en Sdk's.

## <a name="whats-new-in-service-fabric"></a>Wat is er nieuw in Service Fabric

### <a name="service-fabric-70"></a>Service Fabric 7,0

Azure Service Fabric 7,0 is nu beschikbaar. U kunt bijwerken naar 7,0 via de Azure Portal of via een Azure Resource Manager-implementatie. Als gevolg van feedback van klanten over de duur van de feest dagen, worden de clusters die zijn ingesteld om automatische upgrades tot en met januari te ontvangen, niet automatisch bijgewerkt.
In januari gaan we de standaard procedure voor samen vouwen en clusters met automatische upgrades hervatten, wordt de 7,0-update automatisch ontvangen. We zullen een andere aankondiging opgeven voordat de implementatie begint.
We zullen onze geplande release datums ook bijwerken om aan te geven dat we dit beleid in overweging nemen. Hier vindt u updates voor de toekomstige [release planningen](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Dit is de meest recente versie van Service Fabric en wordt geladen met belang rijke functies en verbeteringen.

### <a name="key-announcements"></a>Belang rijke aankondigingen
 - [**KeyVaultReference-ondersteuning voor toepassings geheimen (preview-versie)** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Service Fabric toepassingen die [beheerde identiteiten](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) hebben ingeschakeld, kunnen nu rechtstreeks verwijzen naar een Key Vault geheime URL als omgevings variabele, toepassings parameter of container opslagplaats referentie. Service Fabric zal het geheim automatisch oplossen met behulp van de beheerde identiteit van de toepassing. 
     
- **Verbeterde beveiliging van upgrades voor stateless Services**: als u de beschik baarheid tijdens een upgrade van een toepassing wilt garanderen, hebben we nieuwe configuraties geïntroduceerd om het [minimum aantal instanties te definiëren voor stateless Services](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) die beschikbaar moeten worden gesteld. Voorheen was deze waarde 1 voor alle services en kan deze niet worden gewijzigd. Met deze nieuwe beveiligings controle per service kunt u ervoor zorgen dat uw services een minimum aantal exemplaren bewaren tijdens de upgrade van toepassingen, cluster upgrades en andere onderhouds werkzaamheden die afhankelijk zijn van de status-en veiligheids controles van Service Fabric.
  
- [**Resource limieten voor gebruikers services**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): gebruikers kunnen resource limieten instellen voor de gebruikers services op een knoop punt om te voor komen dat scenario's zoals bron uitputting van de service Fabric systeem services. 
  
- [**Zeer hoge service kosten**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) voor een replica type. Replica's met zeer hoge verplaatsings kosten worden alleen verplaatst als er een beperkings schending in het cluster is die op een andere manier niet kan worden opgelost. Raadpleeg de documentatie voor meer informatie over wanneer het gebruik van een zeer hoge transport kosten redelijk is en voor aanvullende overwegingen.
  
-  **Aanvullende controle van de cluster beveiliging**: in deze release hebben we een Configureer bare quorum controle voor het Seed-knoop punt geïntroduceerd. Zo kunt u aanpassen hoeveel Seed-knoop punten beschikbaar moeten zijn tijdens de cluster levenscyclus-en beheer scenario's. Bewerkingen die het cluster onder de geconfigureerde waarde zouden nemen, worden geblokkeerd. Vandaag de standaard waarde is altijd een quorum van de Seed-knoop punten, bijvoorbeeld als u 7 Seed-knoop punten hebt, een bewerking die u onder 5 Seed-knoop punten zou nemen, wordt standaard geblokkeerd. Met deze wijziging kunt u de minimale veilige waarde 6 instellen, waardoor slechts één Seed-knoop punt tegelijk kan worden ingewisseld.
   
- Er is ondersteuning toegevoegd voor [**het beheer van de service voor back-up en herstel in service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Hierdoor kunnen de volgende activiteiten rechtstreeks vanuit SFX worden uitgevoerd: de service Backup en Restore detecteren, back-upbeleid maken, automatische back-ups inschakelen, ad hoc-back-ups uitvoeren, herstel bewerkingen activeren en bladeren door bestaande back-ups.

- De beschik baarheid van de [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)wordt aangekondigd: met dit hulp programma kunt u valideren of de typen die in betrouw bare verzamelingen worden gebruikt, vooruit en achterwaarts compatibel zijn tijdens de upgrade van een rolling toepassing. Dit helpt bij het voor komen van problemen met de upgrade of gegevens verlies en beschadiging van gegevens als gevolg van ontbrekende of incompatibele typen.

- [**Stabiele Lees bewerkingen inschakelen voor secundaire replica's**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1): met stabiele Lees bewerkingen worden secundaire replica's beperkt tot waarden die zijn bevestigd met een quorum.

Daarnaast bevat deze release andere nieuwe functies, oplossingen voor problemen en ondersteunings-, betrouw baarheid en prestatie verbeteringen. Raadpleeg de opmerkingen bij de [release](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)voor een volledige lijst met wijzigingen.

### <a name="service-fabric-70-releases"></a>Service Fabric 7,0 releases

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 18 november 2019 | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 januari 2020 | [Release van Azure Service Fabric 7,0 vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 februari 2020 | [Release van Azure Service Fabric 7,0 vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6,5

Deze release bevat verbeteringen op het gebied van ondersteuning, betrouw baarheid en prestaties, nieuwe functies, fout oplossingen en verbeteringen voor het vereenvoudigen van het beheer van de cluster-en toepassings levenscyclus.

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

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 11 juni 2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 juli 2019 | [Release van Azure Service Fabric 6,5 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 juli 2019 | [Release van Azure Service Fabric 6,5 vernieuwen](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Augustus 23, 2019 | [Release van Azure Service Fabric 6,5 vernieuwen](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 okt 2019 | [Release van Azure Service Fabric 6,5 vernieuwen](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Releaseopmerkingen](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Vorige versies

### <a name="service-fabric-64-releases"></a>Service Fabric 6,4 Releases

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 30 november 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 december 2018 | [Azure Service Fabric 6,4-release voor Windows-clusters vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 februari 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 maart 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 april 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 mei 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 mei 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
