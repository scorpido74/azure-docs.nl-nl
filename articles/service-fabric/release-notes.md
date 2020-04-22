---
title: Azure Service Fabric releases
description: Opmerkingen vrijgeven voor Azure Service Fabric. Bevat informatie over de nieuwste functies en verbeteringen in Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 3e0f6c78b6e5dd066cbfbac6805bb3c42068e66a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729603"
---
# <a name="service-fabric-releases"></a>Service Fabric releases

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Voorbeelden van probleemoplossingsproblemen</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Probleemoplossing Voor</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">ondersteuningsopties</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">ondersteunde versies</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Code Samples</a>

In dit artikel vindt u meer informatie over de nieuwste releases en updates van de Runtime van Service Fabric en SDK's.

## <a name="whats-new-in-service-fabric"></a>Nieuwe nieuwe artikelen in Service Fabric

### <a name="service-fabric-71"></a>Servicefabric 7.1
Vanwege de huidige COVID-19 crisis, en rekening houdend met de uitdagingen waarmee onze klanten worden geconfronteerd, stellen we 7.1 beschikbaar, maar zullen we niet automatisch clusters upgraden die zijn ingesteld om automatische upgrades te ontvangen. We pauzeren automatische upgrades tot nader order om ervoor te zorgen dat klanten upgrades kunnen toepassen wanneer het het meest geschikt voor hen is, om onverwachte onderbrekingen te voorkomen.

U updaten naar 7.1 via de [Azure Portal](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) of via een Azure [Resource Manager-implementatie.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#set-the-upgrade-mode-using-a-resource-manager-template)

Service Fabric-clusters met automatische upgrades ingeschakeld, beginnen de 7.1-update automatisch te ontvangen zodra we de standaardimplementatieprocedure hervatten. We zullen nog een aankondiging doen voordat de standaard uitrol begint op de [Service Fabric Tech Community Site.](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)
We hebben ook updates gepubliceerd tot het einde van de ondersteuningsdatum voor belangrijke releases vanaf 6,5 tot 7,1 [hier.](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-versions) 

## <a name="what-is-new-in-service-fabric-71"></a>Wat is nieuwe in-service Fabric 7.1?
We zijn verheugd om de volgende release van Service Fabric aan te kondigen. Deze release is geladen met belangrijke functies en verbeteringen. Enkele van de belangrijkste functies worden hieronder gemarkeerd:
## <a name="key-announcements"></a>Belangrijkste aankondigingen
- **Algemene beschikbaarheid** van [ **beheerde identiteiten van servicefabric voor servicefabric-toepassingen**](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)
- [**Ondersteuning voor Ubuntu 1804**](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster)
 - [**Preview: VMSS Ephemeral OS disk support**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-azure-deployment-preparation#use-ephemeral-os-disks-for-virtual-machine-scale-sets)**: Ephemeral OS disks are storage created on the local virtual machine, and not saved to remote Azure Storage. Ze worden aanbevolen voor alle servicefabric-knooppunttypen (primair en secundair), omdat in vergelijking met traditionele permanente schijven van het besturingssysteem, kortstondige OS-schijven:
      -  Lees-/schrijflatentie naar OS-schijf verminderen
      -  Snellere beheerbewerkingen voor het opnieuw instellen/opnieuw weergeven van het knooppunt inschakelen
      -  Verlaag de totale kosten (de schijven zijn gratis en kosten geen extra opslagruimte)
- Ondersteuning voor de verklaring van [**Service Endpoint-certificaten van Service Fabric-toepassingen onder voorbehoud.**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
- [**Ondersteuning voor statussondes voor containerservices:**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage)Ondersteuning voor liveness probe-mechanisme voor gecontaineriseerde toepassingen. Liveness Probe helpen kondigen de levendigheid van de containerized applicatie en wanneer ze niet tijdig reageren, zal dit resulteren in een herstart. 
- [**Ondersteuning voor Initializer Code Packages**](https://docs.microsoft.com/azure/service-fabric/initializer-codepackages) voor [containers](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) en [gast uitvoerbare](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) toepassingen. Hierdoor kunnen codepakketten (bijvoorbeeld containers) in een bepaalde volgorde worden uitgevoerd om de initialisatie van het servicepakket uit te voeren.
- **FabricObserver en ClusterObserver** zijn stateloze toepassingen die Service Fabric Telemetrie vastleggen met betrekking tot verschillende aspecten van een SF-cluster. Beide toepassingen zijn klaar voor implementatie naar Windows-productieclusters om rijke telemetrie vast te leggen met geïmplementeerde ondersteuning voor ApplicationInsights, EventSource en LogAnalytics.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)- draait op alle knooppunten, genereert statusgebeurtenissen, zendt telemetrie uit wanneer door de gebruiker geconfigureerde drempelwaarden voor resourcegebruik worden bereikt. Deze release bevat verschillende verbeteringen in monitoring, gegevensbeheer, details van gezondheidsgebeurtenissen, gestructureerde telemetrie.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - draait op één knooppunt, legt telemetrie op clusterniveau op. In deze release bewaakt ClusterObserver ook de status van het knooppunt en zendt telemetrie uit wanneer het knooppunt langer is uitgeschakeld/uitgeschakeld/uitgeschakeld dan de door de gebruiker opgegeven periode.

### <a name="improve-application-life-cycle-experience"></a>De levenscycluservaring van toepassingen verbeteren

- **[Voorbeeld:Drain aanvragen](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-advanced#avoid-connection-drops-during-planned-downtime-of-stateless-services)**: Tijdens gepland serviceonderhoud, zoals service-upgrades of knooppuntdeactivering, wilt u toestaan dat de services de verbindingen op een elegante manier leeglopen. Met deze functie wordt een instantie voor afsluitingsvertraging toegevoegd in de serviceconfiguratie. Tijdens geplande bewerkingen verwijdert SF het adres van de Service uit de detectie en wacht deze duur voordat u de service afsluit.
- **[Automatische subclusterdetectie en -afweging:](https://docs.microsoft.com/azure/service-fabric/cluster-resource-manager-subclustering )** subclustering vindt plaats wanneer services met verschillende plaatsingsbeperkingen een gemeenschappelijke [loadmetric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-metrics)hebben. Als de belasting op de verschillende sets knooppunten aanzienlijk verschilt, is de Service Fabric Cluster Resource Manager van mening dat het cluster onevenwichtig is, zelfs als het de best mogelijke balans heeft vanwege de plaatsingsbeperkingen. Als gevolg hiervan probeert het cluster opnieuw in evenwicht te brengen, wat mogelijk onnodige servicebewegingen veroorzaakt (aangezien de "onbalans" niet substantieel kan worden verbeterd). Te beginnen met deze release, zal de Cluster Resource Manager nu proberen om automatisch te detecteren dit soort configuraties en begrijpen wanneer de onbalans kan worden vastgesteld door middel van beweging, en wanneer in plaats daarvan moet dingen met rust laten, omdat er geen substantiële verbetering kan worden gemaakt.  
- [**Verschillende verhuiskosten voor secundaire replica's:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)We hebben de kostenwaarde voor nieuwe verhuizingskosten VeryHigh geïntroduceerd die in sommige scenario's extra flexibiliteit biedt om te bepalen of afzonderlijke verhuiskosten moeten worden gebruikt voor secundaire replica's.
- Ingeschakeld [**Levendigheid Sonde**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage ) mechanisme voor gecontaineriseerde toepassingen. Liveness Probe helpen kondigen de levendigheid van de containerized applicatie en wanneer ze niet tijdig reageren, zal dit resulteren in een herstart.
- [**Uitvoeren tot voltooiing/eenmaal voor services**](https://docs.microsoft.com/azure/service-fabric/run-to-completion)**

### <a name="image-store-improvements"></a>Verbeteringen in de Image Store
 - Service Fabric 7.1 maakt gebruik **van aangepast transport om de bestandsoverdracht tussen knooppunten standaard te beveiligen.** De afhankelijkheid van SMB-bestandsshare wordt verwijderd uit versie 7.1. De beveiligde SMB-bestandsshares bestaan nog steeds op knooppunten die image store service-replica bevatten voor de keuze van de klant om zich af te melden voor standaard en voor upgraden en downgraden naar oude versie.
       
 ### <a name="reliable-collections-improvements"></a>Betrouwbare verbeteringen in collecties

- [**In het geheugen alleen op te slaan ondersteuning voor stateful diensten met behulp van betrouwbare collecties:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-work-with-reliable-collections#volatile-reliable-collections)Vluchtige betrouwbare collecties maakt het mogelijk gegevens te blijven schijven voor duurzaamheid tegen grootschalige storingen, kan worden gebruikt voor workloads zoals gerepliceerde cache bijvoorbeeld waar incidentele gegevens verlies kan worden getolereerd. Op basis van de [beperkingen en beperkingen van vluchtige betrouwbare verzamelingen](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines#volatile-reliable-collections)raden we dit aan voor workloads die geen persistentie nodig hebben, voor services die de zeldzame gevallen van Quorum Loss verwerken.
- [**Preview: Service Fabric Backup Explorer:**](https://github.com/microsoft/service-fabric-backup-explorer)Om het beheer van back-ups van betrouwbare collecties voor servicefabric-stateful-toepassingen te vergemakkelijken, stelt Service Fabric Backup Explorer gebruikers in staat om
    - De inhoud van de betrouwbare collecties controleren en beoordelen,
    - Huidige status bijwerken naar een consistente weergave
    - Back-up maken van de huidige momentopname van de betrouwbare verzamelingen
    - Gegevensbeschadiging oplossen
                 
### <a name="service-fabric-71-releases"></a>Service Fabric 7.1 releases
| Releasedatum | Release | Meer informatie |
|---|---|---|
| 20 april 2020 | [Azure-servicefabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|


### <a name="service-fabric-70"></a>Servicefabric 7.0

Azure Service Fabric 7.0 is nu beschikbaar! U updaten naar 7.0 via de Azure-portal of via een Azure Resource Manager-implementatie. Vanwege feedback van klanten over releases rond de vakantieperiode zullen we niet beginnen met het automatisch bijwerken van clusters die zijn ingesteld om automatische upgrades te ontvangen tot januari.
In januari hervatten we de standaard uitrolprocedure en clusters met automatische upgrades ingeschakeld, beginnen de 7.0-update automatisch te ontvangen. We zullen nog een aankondiging doen voordat de uitrol begint.
We zullen ook onze geplande releasedata bijwerken om aan te geven dat we met dit beleid rekening houden. Kijk hier voor updates over onze toekomstige [release schema's](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Dit is de nieuwste versie van Service Fabric en zit boordevol belangrijke functies en verbeteringen.

### <a name="key-announcements"></a>Belangrijkste aankondigingen
 - [**KeyVaultReference-ondersteuning voor toepassingsgeheimen (Preview)**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Service Fabric-toepassingen die [Managed Identities](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) hebben ingeschakeld, kunnen nu rechtstreeks verwijzen naar een geheime URL van Key Vault als omgevingsvariabele, toepassingsparameter of containerrepository-referentie. Service Fabric lost het geheim automatisch op met behulp van de beheerde identiteit van de toepassing. 
     
- **Verbeterde upgradeveiligheid voor stateless services:** Om de beschikbaarheid tijdens een toepassingsupgrade te garanderen, hebben we nieuwe configuraties geïntroduceerd om het minimumaantal exemplaren te definiëren [voor stateless services](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) die als beschikbaar moeten worden beschouwd. Voorheen was deze waarde 1 voor alle services en was deze niet veranderlijk. Met deze nieuwe veiligheidscontrole per service u ervoor zorgen dat uw services een minimum aantal up-instances behouden tijdens toepassingsupgrades, clusterupgrades en ander onderhoud dat is gebaseerd op de gezondheids- en veiligheidscontroles van Service Fabric.
  
- [**Resourcelimieten voor gebruikersservices:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)gebruikers kunnen resourcelimieten instellen voor de gebruikersservices op een knooppunt om scenario's zoals resource-uitputting van de servicefabric-systeemservices te voorkomen. 
  
- [**Zeer hoge service verhuiskosten**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) voor een replica type. Replica's met zeer hoge verhuiskosten worden alleen verplaatst als er een beperkingsschending in het cluster is die niet op een andere manier kan worden opgelost. Zie de documenten voor meer informatie over wanneer het gebruik van een "Zeer Hoge" verhuiskosten redelijk is en voor aanvullende overwegingen.
  
-  **Aanvullende clusterveiligheidscontroles**: In deze versie hebben we een configureerbare veiligheidscontrole van het seed node quorum geïntroduceerd. Hiermee u aanpassen hoeveel seednodes beschikbaar moeten zijn tijdens levenscyclus- en beheerscenario's voor het cluster. Bewerkingen die het cluster onder de geconfigureerde waarde zouden nemen, worden geblokkeerd. Vandaag de dag is de standaardwaarde altijd een quorum van de seedknooppunten, bijvoorbeeld als u 7 seednodes hebt, zou een bewerking die u onder de 5 seednodes zou brengen standaard worden geblokkeerd. Met deze wijziging, kon je de minimale veilige waarde 6, die zou toestaan dat slechts een zaad knooppunt naar beneden te zijn op een moment.
   
- Ondersteuning toegevoegd voor [**het beheren van de back-up- en herstelservice in Service Fabric Explorer.**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster) Dit maakt de volgende activiteiten direct mogelijk vanuit SFX: het ontdekken van de back-up- en herstelservice, het maken van back-ups, het inschakelen van automatische back-ups, het nemen van adhoc back-ups, het activeren van herstelbewerkingen en het doorbladeren van bestaande back-ups.

- Beschikbaarheid van de [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)aankondigen: Deze tool helpt te valideren dat typen die worden gebruikt in betrouwbare collecties voorwaarts en achterwaarts compatibel zijn tijdens een upgrade met rollende toepassingen. Dit helpt upgradefouten of gegevensverlies en gegevensbeschadiging te voorkomen als gevolg van ontbrekende of onverenigbare typen.

- [**Stabiele reads inschakelen op secundaire replica's**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1):Stabiele reads beperken secundaire replica's tot terugkerende waarden die zijn geplunderd.

Daarnaast bevat deze release andere nieuwe functies, bugfixes en ondersteuning, betrouwbaarheid en prestatieverbeteringen. Voor de volledige lijst met wijzigingen verwijzen wij u naar de [releasenotes.](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 releases

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 18 november 2019 | [Azure-servicefabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 januari 2020 | [Vernieuwen versie van Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 februari 2020 | [Vernieuwen versie van Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 maart 2020 | [Vernieuwen versie van Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>Servicefabric 6.5

Deze release bevat ondersteuning, betrouwbaarheid en prestatieverbeteringen, nieuwe functies, bugfixes en verbeteringen om het beheer van de cluster- en toepassingslevenscyclus te vergemakkelijken.

> [!IMPORTANT]
> Service Fabric 6.5 is de definitieve release met Service Fabric tools ondersteuning in Visual Studio 2015. Klanten wordt aangeraden om in de toekomst over te stappen naar [Visual Studio 2019.](https://visualstudio.microsoft.com/vs/)

Dit is er nieuw in Service Fabric 6.5:

- Service Fabric Explorer bevat een [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) voor het inspecteren van toepassingen die u hebt geüpload naar image store.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) versie [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) bevat veel zelfdiagnostische verbeteringen. Klanten van POA wordt aangeraden om naar deze versie te gaan.

- [EventStore-service is standaard ingeschakeld](service-fabric-visualizing-your-cluster.md#event-store) voor Service Fabric 6.5-clusters, tenzij u zich hebt afgemeld.

- [Replica-levenscyclusgebeurtenissen toegevoegd](service-fabric-diagnostics-event-generation-operational.md#replica-events) voor stateful services.

- [Betere zichtbaarheid van de status van het startknooppunt](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inclusief waarschuwingen op clusterniveau als een seed-knooppunt niet in orde is *(Omlaag,* *Verwijderd* of *Onbekend*).

- [Service Fabric Application Disaster Recovery Tool](https://github.com/Microsoft/Service-Fabric-AppDRTool) stelt Service Fabric stateful services in staat om snel te herstellen wanneer het primaire cluster een ramp tegenkomt. Gegevens uit het primaire cluster worden continu gesynchroniseerd op de secundaire stand-bytoepassing met behulp van periodieke back-up en herstel.

- Ondersteuning voor Visual Studio voor [het publiceren van .NET Core-apps naar Linux-gebaseerde clusters.](service-fabric-how-to-publish-linux-app-vs.md)

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) wordt automatisch geïnstalleerd voor Service Fabric 6.5 (en latere versies) wanneer u een upgrade uitvoert of een nieuw Linux-cluster op Azure maakt.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) is standaard geïnstalleerd op MacOS/Linux OneBox-clusters.

Zie voor meer informatie de [Service Fabric 6.5 Release Notes](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 releases

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 11 juni 2019 | [Azure-servicefabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 juli 2019 | [Azure Service Fabric 6.5 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 juli 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 augustus 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 okt 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Release notes] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>Vorige versies

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 releases

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 30 november 2018 | [Azure-servicefabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 december 2018 | [Azure Service Fabric 6.4 Refresh Release voor Windows-clusters](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 februari 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 maart 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 april 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 mei 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 mei 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Opmerkingen vrijgeven](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
