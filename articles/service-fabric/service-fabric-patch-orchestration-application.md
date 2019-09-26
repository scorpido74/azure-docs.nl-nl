---
title: Orchestrator-toepassing voor Azure Service Fabric-patch | Microsoft Docs
description: Toepassing voor het automatiseren van patches van besturings systemen op een Service Fabric cluster.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: 2aa2dd8373a9568478a02691ca5e6a43e80cd408
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71289411"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Het Windows-besturings systeem in uw Service Fabric cluster bijwerken

> 
> [!IMPORTANT]
> Toepassings versie 1,2. * wordt op 30 april 2019 niet meer ondersteund. Voer een upgrade uit naar de nieuwste versie.


[Azure virtual machine-schaalset upgrades van de besturingssysteem installatie kopie](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) is de best practice voor het bewaren van uw besturings systemen in Azure en de patch Orchestration Application (Poa) is een wrapper rond service fabrics RepairManager Systems service die maakt configuratie op basis van de besturingssysteem patch mogelijk voor niet door Azure gehoste clusters. POA is niet vereist voor niet door Azure gehoste clusters, maar het plannen van een patch-installatie door upgrade domeinen is vereist voor het patchen van Service Fabric clusters hosts zonder downtime.

POA is een Azure Service Fabric-toepassing waarmee de patching van besturings systemen op een Service Fabric cluster zonder downtime wordt geautomatiseerd.

De patch Orchestration-app biedt de volgende functies:

- **Automatische installatie**van de update van het besturings systeem. Updates van het besturings systeem worden automatisch gedownload en geïnstalleerd. Cluster knooppunten worden indien nodig opnieuw opgestart zonder uitval van het cluster.

- **Integratie van cluster bewuste patches en status**. Tijdens het Toep assen van updates, controleert de patch Orchestration-app de status van de cluster knooppunten. Cluster knooppunten worden één knoop punt tegelijk of één upgrade domein tegelijk bijgewerkt. Als de status van het cluster uitvalt vanwege het patch proces, wordt patches gestopt om te voor komen dat het probleem wordt verergerd.

## <a name="internal-details-of-the-app"></a>Interne Details van de app

De patch Orchestration-app bestaat uit de volgende subonderdelen:

- **Coördinator-service**: Deze stateful service is verantwoordelijk voor:
    - De Windows Update-taak coördineren op het hele cluster.
    - Het resultaat van voltooide Windows Update bewerkingen opslaan.
- **Knooppunt Agent-service**: Deze stateless service wordt uitgevoerd op alle Service Fabric cluster knooppunten. De service is verantwoordelijk voor:
    - De NTService van de knooppunt agent Boots trappen.
    - De NTService van de knooppunt agent bewaken.
- **NTService van knooppunt agent**: Deze Windows NT-service wordt uitgevoerd op een privilege op een hoger niveau (systeem). De node Agent-service en de coördinator service worden daarentegen uitgevoerd met een bevoegdheid op lager niveau (netwerk SERVICE). De service is verantwoordelijk voor het uitvoeren van de volgende Windows Update taken op alle cluster knooppunten:
    - Automatische Windows Update op het knoop punt uit te scha kelen.
    - Het downloaden en installeren van Windows Update op basis van het beleid dat door de gebruiker is verschaft.
    - De computer post opnieuw opstarten Windows Update installatie.
    - Het uploaden van de resultaten van Windows-updates naar de coördinator-service.
    - Rapportage van status rapporten als een bewerking is mislukt nadat alle pogingen zijn uitgeput.

> [!NOTE]
> De patch Orchestration-app maakt gebruik van de Service Fabric System Service reparatie beheer om het knoop punt uit te scha kelen of in te scha kelen en status controles uit te voeren. De herstel taak die is gemaakt door de patch indelings-app houdt de Windows Update voortgang voor elk knoop punt bij.

## <a name="prerequisites"></a>Vereisten

> [!NOTE]
> Mini maal vereiste versie van .NET Framework is 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>De service reparatie beheer inschakelen (als deze nog niet wordt uitgevoerd)

Voor de patch-indelings-app moet de System-Service voor herstel beheer zijn ingeschakeld op het cluster.

#### <a name="azure-clusters"></a>Azure-clusters

Voor Azure-clusters in de Silver-duurzaamheids categorie is de service reparatie beheer standaard ingeschakeld. Azure-clusters in de Gold-duurzaamheids categorie kunnen al dan niet de reparatie Manager-service inschakelen, afhankelijk van het moment waarop deze clusters zijn gemaakt. Voor Azure-clusters in de laag Bronze duurzaamheid is de service reparatie beheer standaard niet ingeschakeld. Als de service al is ingeschakeld, kunt u deze bekijken in de sectie systeem services van de Service Fabric Explorer.

##### <a name="azure-portal"></a>Azure Portal
U kunt de herstel Manager inschakelen op het moment dat het cluster wordt ingesteld Azure Portal. Selecteer **Repair Manager optie insluiten** onder **invoeg** toepassingen op het moment van de cluster configuratie.
![Afbeelding van het inschakelen van Repair Manager van Azure Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager implementatie model
U kunt ook het Azure Resource Manager- [implementatie model](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) gebruiken om de reparatie beheer-service op nieuwe en bestaande service Fabric-clusters in te scha kelen. Haal de sjabloon op voor het cluster dat u wilt implementeren. U kunt de voorbeeld sjablonen gebruiken of een aangepaste sjabloon voor Azure Resource Manager implementatie model maken. 

De service reparatie beheer met Azure Resource Manager- [implementatie model sjabloon](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)inschakelen:

1. Controleer eerst of de `apiversion` is ingesteld op `2017-07-01-preview` voor de `Microsoft.ServiceFabric/clusters` resource. Als dat niet het geval is, moet u de `apiVersion` waarde `2017-07-01-preview` bijwerken naar of hoger:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Schakel de reparatie beheer-service nu in door de `addonFeatures` volgende sectie toe `fabricSettings` te voegen na de sectie:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Nadat u uw cluster sjabloon met deze wijzigingen hebt bijgewerkt, past u deze toe en laat u de upgrade volt ooien. U kunt nu de reparatie Manager-systeem service zien die in uw cluster wordt uitgevoerd. Deze wordt aangeroepen `fabric:/System/RepairManagerService` in de sectie systeem services van de service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Zelfstandige on-premises clusters

U kunt de [configuratie-instellingen voor zelfstandige Windows-clusters](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) gebruiken om de service reparatie beheer in te scha kelen op nieuw en bestaand service Fabric cluster.

De service reparatie beheer inschakelen:

1. Controleer eerst of de `apiversion` [algemene cluster configuraties](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) zijn ingesteld op `04-2017` of hoger:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Schakel nu de herstel beheer-service in door `addonFeatures` de volgende sectie `fabricSettings` toe te voegen na de sectie zoals hieronder wordt weer gegeven:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Het cluster manifest bijwerken met deze wijzigingen, met behulp van het bijgewerkte cluster manifest [een nieuw cluster maken of een](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) [upgrade van de cluster configuratie uitvoeren](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). Zodra het cluster met het bijgewerkte cluster manifest wordt uitgevoerd, kunt u nu de System-service herstellen die wordt uitgevoerd in uw cluster, dat `fabric:/System/RepairManagerService`wordt aangeroepen onder systeem services in de service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Windows-updates configureren voor alle knoop punten

Automatische Windows-updates kunnen leiden tot een Beschikbaarheids verlies omdat meerdere cluster knooppunten tegelijkertijd opnieuw kunnen worden gestart. De app voor het Toep assen van patches probeert standaard de automatische Windows Update op elk cluster knooppunt uit te scha kelen. Als de instellingen echter worden beheerd door een beheerder of groepsbeleid, raden we u aan het beleid voor Windows Update expliciet in te stellen op ' waarschuwen voordat downloaden '.

## <a name="download-the-app-package"></a>Het app-pakket downloaden

Als u een toepassings pakket wilt downloaden, gaat u naar de [pagina](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) github release van patch Orchestration Application.

## <a name="configure-the-app"></a>De app configureren

Het gedrag van de patch indelings-app kan zodanig worden geconfigureerd dat deze aan uw behoeften voldoet. De standaard waarden onderdrukken door door gegeven in de toepassings parameter tijdens het maken of bijwerken van de toepassing. U kunt `ApplicationParameter` depara`Start-ServiceFabricApplicationUpgrade` meters van de toepassing opgeven door de cmdletsoptegeven.`New-ServiceFabricApplication`

|**Parameter**        |**Type**                          | **Details**|
|:-|-|-|
|MaxResultsToCache    |Lang                              | Het maximum aantal Windows Update resultaten dat in de cache moet worden opgeslagen. <br>De standaard waarde is 3000, uitgaande van het volgende: <br> -Het aantal knoop punten is 20. <br> -Het aantal updates dat plaatsvindt op een knoop punt per maand is vijf. <br> -Het aantal resultaten per bewerking mag 10 zijn. <br> -De resultaten van de afgelopen drie maanden moeten worden opgeslagen. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy geeft het beleid aan dat door de coördinator service moet worden gebruikt om Windows-updates op de Service Fabric cluster knooppunten te installeren.<br>                         Toegestane waarden zijn: <br>                                                           <b>NodeWise</b>. Windows Update is één knoop punt per keer geïnstalleerd. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update is één upgrade domein per keer geïnstalleerd. (Ten opzichte van alle knoop punten die deel uitmaken van een upgrade domein kunnen Windows Update.)<br> Raadpleeg de sectie [Veelgestelde vragen](#frequently-asked-questions) over hoe u kunt bepalen welk beleid het meest geschikt is voor uw cluster.
|LogsDiskQuotaInMB   |Lang  <br> Prijs 1024)               |Maximale grootte van Logboeken voor patch indelings-apps in MB, die lokaal kunnen worden bewaard op knoop punten.
| WUQuery               | string<br>Prijs "IsInstalled=0")                | Query uitvoeren om Windows-updates op te halen. Zie WuQuery voor meer informatie [.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Boolean-waarde <br> (standaard: onwaar)                 | Gebruik deze markering om te bepalen welke updates moeten worden gedownload en geïnstalleerd. De volgende waarden zijn toegestaan <br>True: installeert alleen updates van het Windows-besturings systeem.<br>onwaar: alle beschik bare updates op de computer worden geïnstalleerd.          |
| WUOperationTimeOutInMinutes | Int <br>Prijs 90)                   | Hiermee geeft u de time-out voor een Windows Update bewerking (zoeken of downloaden of installeren). Als de bewerking niet binnen de opgegeven time-out wordt voltooid, wordt deze afgebroken.       |
| WURescheduleCount     | Int <br> Prijs 5)                  | Het maximum aantal keren dat de service de Windows Update opnieuw plant als een bewerking permanent mislukt.          |
| WURescheduleTimeInMinutes | Int <br>Prijs 30) | Het interval waarmee de service de Windows Update opnieuw plant wanneer het probleem zich blijft voordoen. |
| WUFrequency           | Door komma's gescheiden teken reeks (standaard: "Week, woensdag, 7:00:00")     | De frequentie voor het installeren van Windows Update. De notatie en mogelijke waarden zijn: <br>-Maandelijks, DD, uu: MM: SS, bijvoorbeeld maandelijks, 5, 12:22:32.<br>Toegestane waarden voor het veld DD (dag) zijn getallen tussen het bereik 1-28 en de laatste. <br> -Wekelijks, dag, uu: MM: SS, bijvoorbeeld wekelijks, dinsdag, 12:22:32.  <br> -Dagelijks, uu: MM: SS, bijvoorbeeld Daily, 12:22:32.  <br> -Geen geeft aan dat Windows Update niet moet worden uitgevoerd.  <br><br> Houd er rekening mee dat de tijden in UTC zijn.|
| AcceptWindowsUpdateEula | Boolean-waarde <br>(Standaard: True) | Door deze vlag in te stellen, accepteert de toepassing de gebruiksrecht overeenkomst voor Windows Update namens de eigenaar van de computer.              |

> [!TIP]
> Als u wilt dat Windows update onmiddellijk plaatsvindt, stelt `WUFrequency` u in ten opzichte van de implementatie tijd van de toepassing. Stel dat u een test cluster met vijf knoop punten hebt en u de app op ongeveer 5:00 uur UTC wilt implementeren. Als u ervan uitgaat dat de upgrade of implementatie van de toepassing 30 minuten duurt, stelt u de WUFrequency in als ' dagelijks, 17:30:00 '

## <a name="deploy-the-app"></a>De app implementeren

1. Alle vereiste stappen volt ooien om het cluster voor te bereiden.
2. Implementeer de patch-indelings-app, zoals andere Service Fabric-apps. U kunt de app implementeren met behulp van Power shell. Volg de stappen in [toepassingen implementeren en verwijderen met behulp van Power shell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Als u de toepassing op het tijdstip van de implementatie wilt configureren `ApplicationParameter` , geeft `New-ServiceFabricApplication` u de door aan de cmdlet. Voor uw gemak hebben we het script Deploy. ps1 samen met de toepassing voorzien. Het script gebruiken:

    - Verbinding maken met een Service Fabric cluster met `Connect-ServiceFabricCluster`behulp van.
    - Voer het Power shell-script Deploy. ps1 uit `ApplicationParameter` met de juiste waarde.

> [!NOTE]
> Bewaar het script en de toepassingsmap PatchOrchestrationApplication in dezelfde map.

## <a name="upgrade-the-app"></a>De app upgraden

Als u een bestaande patch indelings-app wilt bijwerken met behulp van Power shell, volgt u de stappen in [service Fabric toepassing bijwerken met behulp van Power shell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>De app verwijderen

Als u de toepassing wilt verwijderen, volgt u de stappen in [toepassingen implementeren en verwijderen met behulp van Power shell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Voor uw gemak hebben we het script voor het ongedaan maakt van de implementatie. ps1 samen met de toepassing. Het script gebruiken:

  - Verbinding maken met een Service Fabric cluster met ```Connect-ServiceFabricCluster```behulp van.

  - Voer het Power shell-script undeploy. ps1 uit.

> [!NOTE]
> Bewaar het script en de toepassingsmap PatchOrchestrationApplication in dezelfde map.

## <a name="view-the-windows-update-results"></a>De Windows Update resultaten weer geven

Met de patch-indelings-app worden REST-Api's weer gegeven om de historische resultaten te bekijken aan de gebruiker. Een voor beeld van de JSON van het resultaat:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

De velden van de JSON worden hieronder beschreven.

Veld | Waarden | Details
-- | -- | --
OperationResult | 0: voltooid<br> 1-geslaagd met fouten<br> 2-mislukt<br> 3-afgebroken<br> 4-afgebroken met time-out | Hiermee wordt het resultaat van de algehele bewerking aangegeven (doorgaans het installeren van een of meer updates).
resultCode | Hetzelfde als kan operationresult niet | In dit veld wordt het resultaat van de installatie bewerking voor een afzonderlijke update aangegeven.
OperationType | 1-installatie<br> 0: zoeken en downloaden.| De installatie is de enige OperationType die standaard wordt weer gegeven in de resultaten.
WindowsUpdateQuery | De standaard waarde is ' IsInstalled = 0 ' |Windows Update-query die is gebruikt om naar updates te zoeken. Zie WuQuery voor meer informatie [.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | waar-opnieuw opstarten is vereist<br> ONWAAR-opnieuw opstarten is niet vereist | Hiermee wordt aangegeven of opnieuw opstarten is vereist om de installatie van updates te volt ooien.
OperationStartTime | DateTime | Geeft de tijd aan waarop de bewerking (downloaden/installeren) is gestart.
OperationTime | DateTime | Geeft de tijd aan waarop de bewerking (downloaden/installeren) is voltooid.
HResult | 0-geslaagd<br> overige-fout| Hiermee wordt de oorzaak van het mislukken van de Windows-Update met updateID ' 7392acaf-6a85-427c-8a8d-058c25beb0d6 ' aangegeven.

Als er nog geen update is gepland, is de JSON van het resultaat leeg.

Meld u aan bij het cluster om Windows Update resultaten op te vragen. Ga vervolgens naar het replica adres voor de primaire coördinator service en raak de URL aan vanuit&lt;de browser: http://replica-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/ GetWindowsUpdateResults.

Het REST-eind punt voor de coördinator service heeft een dynamische poort. Als u de exacte URL wilt controleren, raadpleegt u de Service Fabric Explorer. De resultaten zijn bijvoorbeeld beschikbaar op `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Afbeelding van REST-eind punt](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Als de omgekeerde proxy is ingeschakeld op het cluster, hebt u ook toegang tot de URL van buiten het cluster.
Het eind punt waarop moet worden geklikt&lt;,&gt;is http://&gt;SERVERURL:&lt;REVERSEPROXYPORT/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Als u de omgekeerde proxy op het cluster wilt inschakelen, volgt u de stappen in [reverse proxy in Azure service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Nadat de omgekeerde proxy is geconfigureerd, zijn alle micro Services in het cluster die een HTTP-eind punt beschikbaar stellen van buiten het cluster adresseerbaar.

## <a name="diagnosticshealth-events"></a>Diagnose/status gebeurtenissen

In de volgende sectie vindt u informatie over het opsporen en onderzoeken van problemen met patch-updates via patch Orchestration Application in Service Fabric-clusters.

> [!NOTE]
> U moet v 1.4.0-versie van POA geïnstalleerd hebben om veel van de hieronder genoemde verbeteringen voor automatische diagnose te verkrijgen.

De NodeAgentNTService maakt [herstel taken](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) om updates te installeren op de knoop punten. Elke taak wordt vervolgens voor bereid door CoordinatorService volgens het goedkeurings beleid voor taken. De voor bereide taken worden definitief goedgekeurd door Repair Manager waarbij geen enkele taak wordt goedgekeurd als het cluster een slechte status heeft. Stapsgewijze instructies voor het uitvoeren van updates op een knoop punt.

1. NodeAgentNTService die op elk knoop punt wordt uitgevoerd, zoekt naar beschik bare Windows Update op het geplande tijdstip. Als er updates beschikbaar zijn, worden deze gedownload op het knoop punt.
2. Nadat de updates zijn gedownload, wordt met NodeAgentNTService een bijbehorende herstel taak gemaakt voor het knoop punt met de naam POS___ < unique_id >. Eén kan deze herstel taken weer geven met behulp van cmdlet [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) of in sfx in de sectie knooppunt Details. Zodra de herstel taak is gemaakt, gaat u snel naar de [status geclaimd](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).
3. De coördinator-service zoekt regel matig naar herstel taken in de aangegeven staat en werkt deze vervolgens bij om de status op basis van de TaskApprovalPolicy voor te bereiden. Als de TaskApprovalPolicy is geconfigureerd om te worden NodeWise, wordt een herstel taak die overeenkomt met een knoop punt alleen voor bereid als er geen andere herstel taak is op het moment dat de status wordt voor bereid/goedgekeurd/wordt hersteld. Op dezelfde manier wordt in het geval van UpgradeWise TaskApprovalPolicy op elk moment dat er taken in de bovenstaande status zijn, alleen voor knoop punten die deel uitmaken van hetzelfde upgrade domein. Zodra een herstel taak is verplaatst om de status voor te bereiden, wordt het bijbehorende Service Fabric knoop punt met opzet [uitgeschakeld](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) als ' opnieuw opstarten '.

   POA (v 1.4.0 en hoger) plaatst gebeurtenissen met de eigenschap "ClusterPatchingStatus" op CoordinaterService om de knoop punten weer te geven die worden bijgewerkt. Onder afbeelding ziet u dat updates worden geïnstalleerd op _poanode_0:

    [![Afbeelding van de status van de cluster patching](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

4. Zodra het knoop punt is uitgeschakeld, wordt de herstel taak verplaatst naar de status bezig met uitvoeren.
   
   >[!NOTE]
   > Een knoop punt vastgelopen in een uitgeschakelde status kan een nieuwe herstel taak blok keren, waardoor de bewerking voor het uitvoeren van patches op het cluster wordt gestopt.

5. Zodra de herstel taak is uitgevoerd, wordt de installatie van de patch op dat knoop punt gestart. Zodra de patch is geïnstalleerd, wordt het knoop punt mogelijk niet opnieuw opgestart, afhankelijk van de patch. Post dat de herstel taak is verplaatst naar een herstel status, waardoor het knoop punt opnieuw wordt geactiveerd en vervolgens als voltooid is gemarkeerd.

   In v 1.4.0 en de bovenstaande versies van de toepassing kunt u de status van de update vinden door te kijken naar de status gebeurtenissen op NodeAgentService met de eigenschap ' WUOperationStatus-[knooppunt naam] '. In de gemarkeerde secties in de onderstaande installatie kopieën wordt de status van Windows Update weer gegeven op het knoop punt ' poanode_0 ' en ' poanode_2 ':

   [![Afbeelding van de bewerkings status van Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Afbeelding van de bewerkings status van Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Er kunnen ook gegevens worden opgehaald met behulp van Power shell, door verbinding te maken met het cluster en de status van de herstel taak op te halen met [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). Zoals in het onderstaande voor beeld ziet u dat de taak ' POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15 ' zich in de DownloadComplete-status bevindt. Dit betekent dat er updates zijn gedownload op het knoop punt ' poanode_2 ' en dat de installatie wordt uitgevoerd zodra de taak is verplaatst naar de uitvoerings status.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Als er nog steeds meer worden gevonden, meldt u zich aan bij specifieke VM-Vm's om meer te weten te komen over het probleem met behulp van Windows-gebeurtenis Logboeken. De hierboven genoemde herstel taak kan alleen de volgende uitvoeringen bevatten:

      ExecutorSubState | Details
    -- | -- 
      Geen = 1 |  Betekent dat er geen actieve bewerking op het knoop punt is. Mogelijke status overgangen.
      DownloadCompleted=2 | Impliceert dat de Download bewerking is voltooid met geslaagd, gedeeltelijk mislukt of mislukt.
      InstallationApproved=3 | Impliceert dat de Download bewerking eerder is voltooid en dat de installatie van Repair Manager is goedgekeurd.
      InstallationInProgress=4 | Komt overeen met de uitvoerings status van de herstel taak.
      InstallationCompleted=5 | Impliceert dat de installatie is voltooid met succes, gedeeltelijk geslaagd of mislukt.
      RestartRequested=6 | Impliceert dat de installatie van de patch is voltooid en dat de bewerking opnieuw moet worden gestart op het knoop punt.
      RestartNotNeeded = 7 |  Houdt in dat opnieuw opstarten niet nodig was na voltooiing van de installatie van de patch.
      RestartCompleted = 8 | Impliceert dat opnieuw opstarten is voltooid.
      OperationCompleted=9 | De Windows Update-bewerking is voltooid.
      OperationAborted=10 | Impliceert dat Windows Update-bewerking wordt afgebroken.

6. Als bij het bijwerken van een knoop punt in v 1.4.0 en hoger van de toepassing een gebeurtenis met de eigenschap ' WUOperationStatus-[nodenaam] ' wordt geplaatst, wordt deze in de NodeAgentService verzonden om te waarschuwen wanneer de volgende poging wordt gedaan om update te downloaden en te installeren, starten. Zie de onderstaande afbeelding:

     [![Afbeelding van de bewerkings status van Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostic-logs"></a>Diagnostische logboeken

Patch Orchestrator app-logboeken worden verzameld als onderdeel van Service Fabric runtime-Logboeken.

Als u logboeken wilt vastleggen via diagnostisch hulp programma/pijp lijn van uw keuze. Patch Orchestration-toepassing wordt onder de vaste provider-Id's gebruikt voor het registreren van gebeurtenissen via de [gebeurtenis bron](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Status rapporten

De patch-indelings-app publiceert ook status rapporten op basis van de coördinator service of de knooppunt Agent service in de volgende gevallen:

#### <a name="the-node-agent-ntservice-is-down"></a>De NTService van de knooppunt agent is niet beschikbaar

Als de NTService van de knoop punt op een knoop punt niet beschikbaar is, wordt er een status rapport op waarschuwings niveau gegenereerd voor de knoop punt Agent service.

#### <a name="the-repair-manager-service-is-not-enabled"></a>De service reparatie beheer is niet ingeschakeld

Als de service reparatie beheer niet op het cluster wordt gevonden, wordt er een status rapport op waarschuwings niveau gegenereerd voor de coördinator service.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

V. **Waarom kan ik mijn cluster zien met een fout status wanneer de patch Orchestration-app wordt uitgevoerd?**

A. Tijdens het installatie proces worden knoop punten door de patch-indelings-app uitgeschakeld of opnieuw gestart. Dit kan tijdelijk leiden tot de status van het cluster.

Op basis van het beleid voor de toepassing kan één knoop punt worden afgesloten tijdens een patch bewerking *of* kan een volledig upgrade domein tegelijkertijd worden uitgevoerd.

Aan het einde van de Windows Update-installatie worden de knoop punten opnieuw gestart na opnieuw opstarten.

In het volgende voor beeld heeft het cluster tijdelijk een fout status gekregen omdat twee knoop punten niet beschikbaar waren en het MaxPercentageUnhealthyNodes-beleid werd geschonden. De fout is tijdelijk totdat de patch bewerking wordt uitgevoerd.

![Afbeelding van een beschadigd cluster](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Als het probleem zich blijft voordoen, raadpleegt u de sectie probleem oplossing.

V. **Patch-indelings-app heeft een waarschuwings status**

A. Controleer of een status rapport dat is gepost op de toepassing de hoofd oorzaak is. Normaal gesp roken bevat de waarschuwing Details van het probleem. Als het probleem zich tijdelijk voordoet, wordt de toepassing naar verwachting automatisch hersteld vanuit deze status.

V. **Wat kan ik doen als mijn cluster beschadigd is en ik een urgente update van het besturings systeem moet uitvoeren?**

A. In de patch-indelings-app worden geen updates geïnstalleerd terwijl het cluster beschadigd is. Probeer het cluster naar een goede status om de app-werk stroom voor patch indeling te blok keren.

V. **Moet ik TaskApprovalPolicy instellen als ' NodeWise ' of ' UpgradeDomainWise ' voor mijn cluster?**

A. ' UpgradeDomainWise ' maakt de algehele cluster patch sneller door alle knoop punten die deel uitmaken van een upgrade domein parallel te patchen. Dit betekent dat knoop punten die deel uitmaken van een volledig upgrade domein, niet beschikbaar zijn (in [Uitgeschakelde](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) toestand) tijdens het patch proces.

In tegens telling tot het beleid ' NodeWise ' patches slechts één knoop punt tegelijk. Dit houdt in dat de totale cluster patching langer tijd in beslag neemt. Maxi maal, is er tijdens het patch proces echter slechts één knoop punt beschikbaar (in de status [uitgeschakeld](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) ).

Als uw cluster kan worden uitgevoerd op een N-1 aantal upgrade domeinen tijdens een patch-cyclus (waarbij N het totale aantal upgrade domeinen in uw cluster is), kunt u het beleid instellen op ' UpgradeDomainWise ', anders stelt u het in op ' NodeWise '.

V. **Hoe lang duurt het om een knoop punt te patchen?**

A. Het kan enkele minuten duren voor het bijwerken van een knoop punt (bijvoorbeeld: [Definitie-updates voor Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) tot uren (bijvoorbeeld: [Cumulatieve updates voor Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). De tijd die nodig is om een knoop punt te patchen is vooral afhankelijk van 
 - De grootte van updates
 - Aantal updates dat moet worden toegepast in een patch venster
 - Hoe lang het duurt om de updates te installeren, het knoop punt opnieuw op te starten (indien nodig) en na het opnieuw opstarten te volt ooien.
 - Prestaties van de voor waarden van VM/machine en netwerk.

V. **Hoe lang duurt het om een volledig cluster te patchen?**

A. De benodigde tijd voor het patchen van een volledig cluster is afhankelijk van de volgende factoren:

- Benodigde tijd voor het patchen van een knoop punt.
- Het beleid van de coördinator service. -Het standaard beleid, `NodeWise`, leidt ertoe dat er slechts één knoop punt tegelijk wordt bijgewerkt, wat langzamer zou zijn `UpgradeDomainWise`dan. Bijvoorbeeld: Als een knoop punt ongeveer 1 uur in beslag neemt om een patch uit te voeren, moet u een cluster met vijf upgrade domeinen met 4 knoop punten, om een 20 knoop punt (hetzelfde type knoop punten) te patchen.
    - Het duurt ongeveer 20 uur om het hele cluster bij te houden, als het beleid is`NodeWise`
    - Het duurt ongeveer 5 uur als het beleid is`UpgradeDomainWise`
- Cluster belasting: voor elke patch bewerking moet de werk belasting van de klant worden verplaatst naar andere beschik bare knoop punten in het cluster. Het knoop punt dat wordt bijgewerkt, zou tijdens deze periode de status [uitschakelen](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) kunnen hebben. Als het cluster wordt uitgevoerd bij een piek belasting, neemt het proces meer tijd in beslag. Daarom lijkt het mogelijk dat het algemene patch proces traag is in dergelijke extreme omstandigheden.
- Cluster status fouten tijdens patching: bij een [afname](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) in [de status van het cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) wordt het patch proces onderbroken. Dit zou toe te voegen aan de totale tijd die nodig is om het hele cluster te patchen.

V. **Waarom kan ik sommige updates zien in Windows Update resultaten die zijn verkregen via REST API, maar niet onder de Windows Update geschiedenis op de computer?**

A. Sommige product updates worden alleen weer gegeven in de bijbehorende update/patch-geschiedenis. Windows Defender-updates kunnen bijvoorbeeld worden weer gegeven in Windows Update geschiedenis van Windows Server 2016.

V. **Kan de indelings-app van patches worden gebruikt voor het patchen van mijn dev cluster (cluster met één knoop punt)?**

A. Nee, patch indelings-app kan niet worden gebruikt voor het patchen van een cluster met één knoop punt. Deze beperking is zo ontworpen als [service Fabric-systeem services](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) of klant-apps waarop uitval tijd van toepassing is en daarom wordt elke reparatie taak voor patching nooit goedgekeurd door Repair Manager.

V. **Hoe kan ik patch cluster knooppunten op Linux?**

A. Zie [installatie kopieën van virtuele machines van Azure automatisch bijwerken](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) voor het plannen van updates voor de configuratie van Linux.

V.**Waarom wordt de update cyclus zo lang duren?**

A. Voer een query uit voor de JSON van het resultaat en ga vervolgens door met de vermelding van de update cyclus voor alle knoop punten en vervolgens kunt u proberen de benodigde tijd voor de installatie van de update op elk knoop punt te achterhalen met behulp van OperationStartTime en OperationTime (OperationCompletionTime). Als er sprake is van een groot tijd venster waarin geen update is uitgevoerd, kan het zijn dat het cluster de fout status heeft en dat er door de reparatie Manager geen andere POA-herstel taken zijn goedgekeurd. Als de installatie van de update lang duurde op een wille keurig knoop punt, is het mogelijk dat het knoop punt niet lang is bijgewerkt en dat er een groot aantal updates in behandeling was. Dit duurde tijd. Er kan ook een situatie zijn waarin patches op een knoop punt zijn geblokkeerd omdat het knoop punt niet actief is in de status uitschakelen, wat meestal gebeurt omdat het uitschakelen van het knoop punt kan leiden tot problemen met het quorum of gegevens verlies.

V. **Waarom is het nodig om het knoop punt uit te scha kelen wanneer POA wordt bijgewerkt?**

A. Met patch Orchestration Application wordt het knoop punt uitgeschakeld met ' restart ', waarmee alle service Fabric-services die op het knoop punt worden uitgevoerd, worden gestopt/opnieuw worden toegewezen. Dit wordt gedaan om ervoor te zorgen dat toepassingen niet eindigen met een combi natie van nieuwe en oude dll's, dus het wordt afgeraden om een knoop punt te patchen zonder het uit te scha kelen.

## <a name="disclaimers"></a>Disclaimers

- De patch-indelings-app accepteert de gebruiksrecht overeenkomst van Windows Update namens de gebruiker. De instelling kan eventueel worden uitgeschakeld in de configuratie van de toepassing.

- De patch Orchestration-app verzamelt telemetrie om het gebruik en de prestaties bij te houden. De telemetrie van de toepassing volgt de instelling van de telemetrie-instelling van de Service Fabric runtime (deze is standaard ingeschakeld).

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="a-node-is-not-coming-back-to-up-state"></a>Een knoop punt is niet teruggestuurd naar de status up

**Het knoop punt is mogelijk vastgelopen in een status uitschakelen omdat**:

Er is een veiligheids controle in behandeling. Om deze situatie te verhelpen, moet u ervoor zorgen dat er voldoende knoop punten in een goede staat zijn.

**Het knoop punt is mogelijk vastgelopen met een uitgeschakelde status**:

- Het knoop punt is hand matig uitgeschakeld.
- Het knoop punt is uitgeschakeld vanwege een voortdurende Azure-infrastructuur taak.
- Het knoop punt is tijdelijk uitgeschakeld door de patch Orchestration-app om het knoop punt te patchen.

**Het knoop punt kan zich in de status omlaag bezitten omdat**:

- Het knoop punt is hand matig in de status omlaag gezet.
- Het knoop punt wordt opnieuw opgestart (wat kan worden geactiveerd door de patch Orchestration-app).
- Het knoop punt is niet beschikbaar vanwege een defecte VM-of computer-of netwerk verbindings problemen.

### <a name="updates-were-skipped-on-some-nodes"></a>Updates zijn overgeslagen op enkele knoop punten

De patch-indelings-app probeert een Windows-Update op basis van het beleid voor opnieuw plannen te installeren. De service probeert het knoop punt te herstellen en de update op basis van het toepassings beleid over te slaan.

In een dergelijk geval wordt een status rapport op waarschuwings niveau gegenereerd voor de knoop punt Agent service. Het resultaat voor Windows Update bevat ook de mogelijke reden voor de fout.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>De status van het cluster gaat naar een fout tijdens de installatie van de update

Een defecte Windows-Update kan de status van een toepassing of cluster op een bepaald knoop punt of upgrade domein innemen. De patch-indelings-app verloopt elke volgende Windows Update bewerking totdat het cluster weer in orde is.

Een beheerder moet ingrijpen en bepalen waarom de toepassing of het cluster slecht is geworden door Windows Update.

## <a name="release-notes"></a>Releaseopmerkingen

>[!NOTE]
> Vanaf versie 1.4.0 kunnen release opmerkingen en releases op de [pagina](https://github.com/microsoft/Service-Fabric-POA/releases/)release van github worden gevonden.

### <a name="version-110"></a>Versie 1.1.0
- Open bare versie

### <a name="version-111"></a>Versie 1.1.1
- Er is een fout opgelost in SetupEntryPoint van NodeAgentService waardoor NodeAgentNTService niet is geïnstalleerd.

### <a name="version-120"></a>Versie 1.2.0

- Fout oplossingen rond de werk stroom voor het opnieuw opstarten van het systeem.
- Fout oplossing bij het maken van RM-taken als gevolg waarvan de status controle tijdens het voorbereiden van herstel taken niet gebeurt zoals verwacht.
- De opstart modus voor Windows-service POANodeSvc gewijzigd van automatisch in delayed-auto.

### <a name="version-121"></a>Versie 1.2.1

- Fout oplossing in de werk stroom Scale-down van het cluster. Geïntroduceerde garbage-verzamelings logica voor POA-herstel taken die horen bij niet-bestaande knoop punten.

### <a name="version-122"></a>Versie 1.2.2

- Diverse oplossingen voor fouten.
- Binaire bestanden zijn nu ondertekend.
- De sfpkg-koppeling voor de toepassing is toegevoegd.

### <a name="version-130"></a>Versie 1.3.0

- Als InstallWindowsOSOnlyUpdates is ingesteld op False, worden alle beschik bare updates geïnstalleerd.
- De logica voor het uitschakelen van automatische updates is gewijzigd. Hiermee wordt een bug opgelost waarbij automatische updates niet zijn uitgeschakeld op de server 2016 en hoger.
- De plaatsings beperking met para meters voor de micro services van POA voor geavanceerde use cases.

### <a name="version-131"></a>Versie 1.3.1
- Correctie van regressie waarbij POA 1.3.0 niet werkt op Windows Server 2012 R2 of lager vanwege een fout bij het uitschakelen van automatische updates. 
- Bug corrigeren waarbij de InstallWindowsOSOnlyUpdates-configuratie altijd waar is.
- De standaard waarde van InstallWindowsOSOnlyUpdates wijzigen in false.

### <a name="version-132"></a>Versie 1.3.2
- Het oplossen van een probleem waarbij de levens cyclus van patches wordt toegepast op een knoop punt in het geval er knoop punten zijn met een naam die deel uitmaakt van de naam van het huidige knoop punt. Voor dergelijke knoop punten is het mogelijk dat patches worden gemist of opnieuw opstarten in behandeling is. 
