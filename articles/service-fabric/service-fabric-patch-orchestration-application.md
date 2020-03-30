---
title: Het Windows-besturingssysteem patchen in uw Service Fabric-cluster
description: In dit artikel wordt besproken hoe u patching van het besturingssysteem op een Service Fabric-cluster automatiseren met behulp van Patch Orchestration-toepassing.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 857a4da0b24d600ecc572933af578e2e8faf501a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366319"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Het Windows-besturingssysteem patchen in uw Service Fabric-cluster

> 
> [!IMPORTANT]
> Vanaf 30 april 2019 wordt Patch Orchestration Application versie 1.2.* niet meer ondersteund. Zorg ervoor dat u een upgrade uitvoert naar de nieuwste versie.

> [!NOTE]
> Het krijgen van [automatische os-afbeeldingsupgrades op uw virtuele machineschaalset](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) is de beste manier om uw besturingssysteem te laten patchen in Azure. Upgrades van virtuele machineschaalsets op basis van automatische os-afbeeldingvereisen zilver of meer duurzaamheid op een schaalset.
>

 Patch Orchestration Application (POA) is een wrapper rond de Azure Service Fabric Repair Manager-service, waarmee op configuratie gebaseerde OS-patchplanning voor niet-Azure gehoste clusters mogelijk is. POA is niet vereist voor niet-door Azure gehoste clusters, maar het plannen van patchinstallatie per updatedomein is vereist om clusterhosts van Service Fabric te patchen zonder downtime.

POA is een Service Fabric-toepassing die patching van besturingssystemen op een Service Fabric-cluster automatiseert zonder downtime.

POA biedt de volgende functies:

- **Installatie van automatische update van het besturingssysteem**. Updates van het besturingssysteem worden automatisch gedownload en geïnstalleerd. Clusterknooppunten worden indien nodig opnieuw opgestart zonder dat clusterdowntime nodig is.

- **Clusterbewuste patching en gezondheidsintegratie**. Terwijl POA updates toepast, wordt de status van de clusterknooppunten bewaakt. Clusterknooppunten worden één knooppunt tegelijk bijgewerkt of één updatedomein tegelijk. Als de status van het cluster naar beneden gaat als gevolg van het patchproces, wordt patchen gestopt om verzwarende het probleem te voorkomen.

## <a name="internal-details-of-poa"></a>Interne details van POA

POA bestaat uit de volgende subcomponenten:

- **Coördinator Service**: Deze statige dienst is verantwoordelijk voor:
    - Coördineren van de Windows Update-taak op het hele cluster.
    - Het resultaat van voltooide Windows Update-bewerkingen opslaan.

- **Node Agent Service**: Deze statusloze service wordt uitgevoerd op alle clusterknooppunten van ServiceFabric. De dienst is verantwoordelijk voor:
    - Bootstrapping the Node Agent NTService.
    - Toezicht houden op de Node Agent NTService.

- **Node Agent NTService**: Deze Windows NT-service wordt uitgevoerd op een hoger niveau bevoegdheid (SYSTEEM). De Node Agent Service en de Coördinatorservice draaien daarentegen op een lager niveau (NETWORK SERVICE). De service is verantwoordelijk voor het uitvoeren van de volgende Windows Update-taken op alle clusterknooppunten:
    - Automatische Windows-updates op het knooppunt uitschakelen.
    - Het downloaden en installeren van Windows-updates volgens het beleid dat de gebruiker heeft opgegeven.
    - Het opnieuw starten van de installatie van updates na Windows.
    - Uploadde de resultaten van Windows-updates naar de coördinatorservice.
    - Statusrapporten rapporteren als een bewerking is mislukt nadat alle pogingen zijn uitput.

> [!NOTE]
> POA gebruikt de service Service Fabric Repair Manager-service om het knooppunt uit te schakelen of in te schakelen en gezondheidscontroles uit te voeren. De reparatietaak die door POA is gemaakt, houdt de voortgang van Windows Update voor elk knooppunt bij.

## <a name="prerequisites"></a>Vereisten

> [!NOTE]
> De vereiste minimum .NET Framework-versie is 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>De service Reparatiebeheer inschakelen (als deze nog niet wordt uitgevoerd)

POA vereist dat de service Reparatiebeheer is ingeschakeld op het cluster.

#### <a name="azure-clusters"></a>Azure-clusters

Azure-clusters in de zilverduurzaamheidslaag hebben standaard de Service Reparatiebeheer ingeschakeld. Azure-clusters in de goudduurzaamheidslaag kunnen de Reparatiemanager-service wel of niet hebben ingeschakeld, afhankelijk van wanneer deze clusters zijn gemaakt. Azure-clusters in de bronzen duurzaamheidslaag hebben standaard niet de service Reparatiebeheer ingeschakeld. Als de service al is ingeschakeld, u deze zien in de sectie Systeemservices in Service Fabric Explorer.

##### <a name="the-azure-portal"></a>Azure Portal
U Reparatiebeheer inschakelen vanuit de Azure-portal wanneer u het cluster instelt. Wanneer u het cluster configureert, selecteert u de optie **Reparatiebeheer opnemen** onder **Invoegtoepassingfuncties**.

![Afbeelding van het inschakelen van Reparatiebeheer vanuit de Azure-portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Het implementatiemodel azure resource manager
U ook het [implementatiemodel azure resource manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) gebruiken om de service Reparatiebeheer in te schakelen op nieuwe en bestaande clusters van servicefabric. Download de sjabloon voor het cluster dat u wilt implementeren. U de voorbeeldsjablonen gebruiken of een aangepaste Azure Resource Manager-implementatiemodelsjabloon maken. 

Ga als volgt te werk om de service Reparatiebeheer in te schakelen met de [sjabloon Azure Resource Manager-implementatiemodel:](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)

1. Controleer of `apiVersion` dit is ingesteld op *2017-07-01-preview* voor de *Microsoft.ServiceFabric/clusters-bron.* Als het anders is, moet `apiVersion` u updaten naar *2017-07-01-preview* of hoger:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Schakel de service Reparatiebeheer `addonFeatures` in door `fabricSettings` de volgende sectie na de sectie toe te voegen:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Nadat u uw clustersjabloon met deze wijzigingen hebt bijgewerkt, past u deze toe en laat u de update voltooien. U nu de service Reparatiebeheer in uw cluster zien. Het heet *fabric:/System/RepairManagerService* in de sectie systeemservices in Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Zelfstandige on-premises clusters

Als u de service Reparatiebeheer wilt inschakelen op een nieuw of bestaand cluster van Servicefabric, u de [configuratie-instellingen voor het zelfstandige Windows-cluster gebruiken.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)

Ga als u de service Reparatiebeheer inschakelen:

1. Controleer of `apiVersion` in [Algemene clusterconfiguraties](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) is ingesteld op *04-2017* of hoger, zoals hier wordt weergegeven:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Schakel de service Reparatiebeheer `addonFeatures` in door `fabricSettings` de volgende sectie na de sectie toe te voegen, zoals hier wordt weergegeven:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Werk uw clustermanifest bij met deze wijzigingen met behulp van het bijgewerkte clustermanifest [en maak een nieuw cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) of upgrade de [clusterconfiguratie.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server) 

   Nadat het cluster is uitgevoerd met een bijgewerkt clustermanifest, u de service Reparatiebeheer in uw cluster zien. Het heet *fabric:/System/RepairManagerService,* en het staat in de sectie systeemservices in Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Windows-updates configureren voor alle knooppunten

Automatische Windows-updates kunnen leiden tot verlies van beschikbaarheid, omdat meerdere clusterknooppunten tegelijkertijd opnieuw kunnen worden opgestart. POA probeert standaard de automatische Windows-updates op elk clusterknooppunt uit te schakelen. Als de instellingen echter worden beheerd door een beheerder of een groepsbeleid, raden we u aan het windows update-beleid expliciet in te stellen op 'Melden voor downloaden'.

## <a name="download-the-application-package"></a>Het toepassingspakket downloaden

Als u het toepassingspakket wilt downloaden, gaat u naar de [releasepagina patchorchestration-toepassing](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) op GitHub.

## <a name="configure-poa-behavior"></a>POA-gedrag configureren

U POA-gedrag configureren om aan uw behoeften te voldoen. Overschrijf de standaardwaarden door de toepassingsparameter door te geven terwijl u de toepassing maakt of bijwerkt. U toepassingsparameters opgeven `ApplicationParameter` door `Start-ServiceFabricApplicationUpgrade` `New-ServiceFabricApplication` de of cmdlets op te geven.

| Parameter        | Type                          | Details |
|:-|-|-|
|MaxResultsToCache    |Lange                              | Het maximum aantal Windows Update-resultaten dat in de cache moet worden opgeslagen. <br><br>De standaardwaarde is 3000, ervan uitgaande dat: <br> &nbsp;&nbsp;- Het aantal knooppunten is 20. <br> &nbsp;&nbsp;- Het aantal updates voor een knooppunt per maand is 5. <br> &nbsp;&nbsp;- Het aantal resultaten per operatie kan 10 zijn. <br> &nbsp;&nbsp;- De resultaten van de afgelopen drie maanden moeten worden opgeslagen. |
|Goedkeuringsbeleid voor taken   |Enum <br> { NodeWise, UpgradeDomainWise }                          |Taakgoedkeuringsbeleid geeft het beleid aan dat door de coördinatorservice moet worden gebruikt om Windows-updates te installeren op de clusterknooppunten van de servicestructuur.<br><br>De toegestane waarden zijn: <br>*NodeWise*: Windows-updates worden één knooppunt tegelijk geïnstalleerd. <br> *UpgradeDomainWise:* Windows-updates worden één updatedomein tegelijk geïnstalleerd. (Hoogstens kunnen alle knooppunten die behoren tot een updatedomein voor een Windows-update gaan.)<br><br> Zie de [sectie Veelgestelde vragen](#frequently-asked-questions) om te bepalen welk beleid het meest geschikt is voor uw cluster.
|LogsDiskQuotaInMB   |Lange  <br> (Standaard: *1024*)               | De maximale grootte van patch orchestration app logs in MB, die lokaal kan worden gehandhaafd op knooppunten.
| WUQuery WUQuery               | tekenreeks<br>(Standaard: *IsInstalled=0*)                | Query om Windows-updates te ontvangen. Zie WuQuery voor meer [informatie.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| WindowsOSOnlyUpdates installeren | *Booleaanse* <br> (standaard: onwaar)                 | Gebruik deze vlag om te bepalen welke updates moeten worden gedownload en geïnstalleerd. Volgende waarden zijn toegestaan <br>true - Installeert alleen Windows-besturingssysteem updates.<br>false - Installeert alle beschikbare updates op de machine.          |
| WUOperationTimeOutInMinutes WUOperationTimeOutInMinutes | Int <br>(Standaard: *90*)                   | Hiermee geeft u de time-out op voor een Windows Update-bewerking (zoeken of downloaden of installeren). Als de bewerking niet binnen de opgegeven time-out is voltooid, wordt deze afgebroken.       |
| WURescheduleCount     | Int <br> (Standaard: *5*)                  | Het maximum aantal keren dat de service de Windows-update opnieuw plant als een bewerking voortdurend mislukt.          |
| WURescheduleTimeInMinutes | Int <br>(Standaard: *30*) | Het interval waarmee de service de Windows-updates opnieuw plant als de fout is mislukt, blijft bestaan. |
| WUFrequency WUFrequency           | Door komma's gescheiden tekenreeks (standaard: *wekelijks, woensdag, 7:00:00*)     | De frequentie voor het installeren van Windows-updates. De notatie en mogelijke waarden zijn: <br>&nbsp;&nbsp;- Maandelijks: DD, HH:MM:SS (bijvoorbeeld *maandelijks, 5,12:22:32*)<br>Toegestane waarden voor veld DD (dag) zijn getallen van 1 tot en met 28 en "laatste". <br> &nbsp;&nbsp;- Weekblad, DAG, HH:MM:SS (bijvoorbeeld *Wekelijks, dinsdag, 12:22:32*)  <br> &nbsp;&nbsp;- Dagelijks, HH:MM:SS (bijvoorbeeld *Dagelijks, 12:22:32*)  <br> &nbsp;&nbsp;-  *Geen* geeft aan dat Windows-updates niet moeten worden uitgevoerd.  <br><br> Tijden zijn in UTC.|
| WindowsUpdateEula accepteren | Booleaans <br>(Standaard: *true)* | Door deze vlag in te stellen, accepteert de toepassing de licentieovereenkomst voor eindgebruikers voor Windows Update namens de eigenaar van de machine.              |

> [!TIP]
> Als u wilt dat Windows-updates onmiddellijk plaatsvinden, stelt u in `WUFrequency` ten opzichte van de implementatietijd van de toepassing. Stel dat u een testcluster met vijf nodes hebt en van plan bent de app rond 17:00 uur UTC te implementeren. Als u ervan uitgaat dat de upgrade of implementatie van de toepassing hooguit 30 minuten duurt, stelt u de WUFrequency in als *dagelijks, 17:30:00*.

## <a name="deploy-poa"></a>POA implementeren

1. Voltooi alle vereiste stappen om het cluster voor te bereiden.
1. Implementeer POA zoals elke andere Service Fabric-app. Zie [Toepassingen implementeren en verwijderen met PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)als u deze wilt implementeren door PowerShell te gebruiken.
1. Als u de toepassing wilt configureren `ApplicationParameter` op `New-ServiceFabricApplication` het moment van implementatie, geeft u de toepassing door aan de cmdlet. Voor uw gemak hebben we het script Deploy.ps1 samen met de toepassing geleverd. Ga als u het script gaat gebruiken:

    - Maak verbinding met een `Connect-ServiceFabricCluster`cluster van servicefabric met behulp van .
    - Voer het PowerShell-script Deploy.ps1 uit met de juiste `ApplicationParameter` waarde.

> [!NOTE]
> Bewaar het script en de toepassingsmap *PatchOrchestrationApplication* in dezelfde map.

## <a name="upgrade-poa"></a>POA upgraden

Als u uw POA-versie wilt upgraden met PowerShell, volgt u de instructies in de upgrade van de [Service Fabric-toepassing met PowerShell.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)

## <a name="remove-poa"></a>POA verwijderen

Als u de toepassing wilt verwijderen, volgt u de instructies in [Toepassingen implementeren en verwijdert u toepassingen met PowerShell.](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)

Voor uw gemak hebben we het Undeploy.ps1-script samen met de toepassing geleverd. Ga als u het script gaat gebruiken:

  - Maak verbinding met een ```Connect-ServiceFabricCluster```cluster van servicefabric met behulp van .
  - Voer het PowerShell-script Undeploy.ps1 uit.

> [!NOTE]
> Bewaar het script en de toepassingsmap *PatchOrchestrationApplication* in dezelfde map.

## <a name="view-the-windows-update-results"></a>De resultaten van Windows Update weergeven

POA stelt REST API's bloot om de historische resultaten aan gebruikers weer te geven. Hier is een voorbeeld van het resultaat JSON:

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

De VELDEN JSON worden beschreven in de volgende tabel:

Veld | Waarden | Details
-- | -- | --
OperationResult (OperationResult) | 0 - Geslaagd<br> 1 - Geslaagd met fouten<br> 2 - Mislukt<br> 3 - Afgebroken<br> 4 - Afgebroken met time-out | Geeft het resultaat van de algehele bewerking aan, waarbij gewoonlijk een of meer updates worden geïnstalleerd.
Resultaatcode | Hetzelfde als OperationResult | Dit veld geeft het resultaat van de installatiebewerking voor een individuele update aan.
OperationType | 1 - Installatie<br> 0 - Zoeken en downloaden| Installatie is standaard het enige OperationType dat in de resultaten wordt weergegeven.
WindowsUpdateQuery | Standaard is 'IsInstalled=0' | De Windows Update-query die is gebruikt om naar updates te zoeken. Zie [WuQuery voor](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)meer informatie.
Opnieuw opstartenvereist | true - reboot was vereist<br> false - reboot was niet nodig | Geeft aan of een reboot nodig was om de installatie van updates te voltooien.
OperationStarttijd | DateTime | Geeft aan op welk tijdstip de bewerking (Download/installatie) is gestart.
OperationTime (OperationTime) | DateTime | Geeft het tijdstip aan waarop de bewerking is voltooid (Downloaden/installeren).
Hresult | 0 - Succesvol<br> andere - mislukking| Geeft de reden voor het mislukken van de Windows-update met updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Als er nog geen update is gepland, is het resultaat JSON leeg.

Meld u aan bij het cluster om de resultaten van Windows Update op te vragen. Ontdek het replica-IP-adres voor het primaire adres van de Coördinatorservice en&gt;&lt;open de volgende URL vanuit de browser: http://&lt;REPLICA-IP : ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

Het REST-eindpunt voor de CoördinatorService heeft een dynamische poort. Als u de exacte URL wilt controleren, raadpleegt u Service Fabric Explorer. De resultaten zijn bijvoorbeeld *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults*beschikbaar op .

![Afbeelding van het REST-eindpunt](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Als de omgekeerde proxy is ingeschakeld op het cluster, hebt u ook toegang tot de URL van buiten het cluster.

Het eindpunt dat u moet raken is *http://&lt;SERVERURL:&gt;&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Als u de omgekeerde proxy in het cluster wilt inschakelen, volgt u de instructies in [Reverse proxy in Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Nadat de omgekeerde proxy is geconfigureerd, zijn alle microservices in het cluster die een HTTP-eindpunt blootleggen, adresseerbaar van buiten het cluster.

## <a name="diagnostics-and-health-events"></a>Diagnostische en gezondheidsevenementen

In dit gedeelte wordt besproken hoe u problemen met patchupdates opsporen of diagnosticeren via POA op servicefabricclusters.

> [!NOTE]
> Om veel van de volgende opgeroepen, zelfdiagnostische verbeteringen te krijgen, moet u POA-versie 1.4.0 of hoger hebben geïnstalleerd.

De Node Agent NTService maakt [reparatietaken](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) voor het installeren van updates op de knooppunten. Elke taak wordt vervolgens voorbereid door de coördinator service volgens de taak goedkeuring beleid. Ten slotte worden de voorbereide taken goedgekeurd door Reparatiebeheer, dat geen taak goedkeurt als het cluster in een ongezonde staat verkeert. 

Om u te helpen begrijpen hoe updates verlopen op een knooppunt, gaan we stap voor stap:

1. NodeAgentNTService, dat op elk knooppunt wordt uitgevoerd, zoekt naar beschikbare Windows-updates op het geplande tijdstip. Als er updates beschikbaar zijn, worden ze gedownload op het knooppunt.

1. Nadat de updates zijn gedownload, maakt de Node Agent NTService een bijbehorende reparatietaak voor het knooppunt met de naam *POS___\<unique_id>*. U deze reparatietaken bekijken met de cmdlet [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) of met SFX in de sectie knooppuntdetails. Nadat de reparatietaak is gemaakt, wordt deze snel verplaatst naar [ *de* status Geclaimd](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).

1. De coördinatorservice zoekt regelmatig naar reparatietaken in *geclaimde* status en werkt deze vervolgens bij naar *de status Voorbereiden* op basis van taakgoedkeuringsbeleid. Als Taakgoedkeuringsbeleid is geconfigureerd als NodeWise, wordt een reparatietaak die overeenkomt met een knooppunt alleen voorbereid als er momenteel geen andere reparatietaak is in *de status Voorbereiden,* *Goedgekeurd,* *Uitvoeren*of *Herstellen.* 

   In het geval van UpgradeWise TaskApprovalPolicy zijn er taken in de voorgaande statussen alleen voor knooppunten die tot hetzelfde updatedomein behoren. Nadat een reparatietaak is verplaatst naar de status *Voorbereiden,* wordt het bijbehorende servicestructuurknooppunt [uitgeschakeld](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) met de intentie om *opnieuw op*te starten .

   POA-versies 1.4.0 en later gebeurtenissen plaatsen met de eigenschap ClusterPatchingStatus op CoordinatorService om de knooppunten weer te geven die worden gepatcht. De updates worden geïnstalleerd op _poanode_0, zoals weergegeven in de volgende afbeelding:

    [![Afbeelding van de clusterpatchstatus](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Nadat het knooppunt is uitgeschakeld, wordt de reparatietaak verplaatst naar *de status Uitvoeren.* 
   
   > [!NOTE]
   > Een knooppunt dat vastzit in *de status Uitgeschakeld,* kan een nieuwe reparatietaak blokkeren, waardoor de patchbewerking op het cluster wordt gestopt.

1. Wanneer de reparatietaak in *de* uitvoeringsstatus staat, begint de patchinstallatie op dat knooppunt. Nadat de patch is geïnstalleerd, kan het knooppunt wel of niet opnieuw worden gestart, afhankelijk van de patch. Vervolgens wordt de reparatietaak verplaatst naar *Herstelstatus,* waarmee het knooppunt opnieuw wordt inschakelt. De reparatietaak wordt vervolgens gemarkeerd als voltooid.

   In POA-versies 1.4.0 en hoger u de status van de update vinden door de\<statusgebeurtenissen op NodeAgentService te bekijken met de eigenschap WUOperationStatus-NodeName>. De gemarkeerde secties in de volgende afbeeldingen tonen de status van Windows-updates op knooppunten *poanode_0* en *poanode_2:*

   [![Afbeelding van de bewerkingsstatus van Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Afbeelding van de bewerkingsstatus van Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   U de details ook krijgen met PowerShell. Om dit te doen, maakt u verbinding met het cluster en haalt u de status van de reparatietaak op met [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). 
   
   In het volgende voorbeeld bevindt de taak 'POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15' de taak *DownloadComplete.* Dit betekent dat updates zijn gedownload op het *poanode_2* knooppunt en dat de installatie wordt geprobeerd wanneer de taak wordt verplaatst naar de status *Uitvoeren.*

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Als er nog meer problemen moeten worden gevonden, meldt u zich aan bij uw virtuele machine (VM) of VM's en leert u hierover met Windows-gebeurtenislogboeken. De eerder genoemde reparatietaak kan alleen in de volgende executorsubstates bestaan:

      ExecuteurSubState | Beschrijving
    -- | -- 
      None |  Impliceert dat er geen lopende operatie op het knooppunt. De staat kan in transitie zijn.
      DownloadCompleted=2 | Impliceert dat de downloadbewerking is voltooid met succes, gedeeltelijke fout of uitval.
      Installatiegoedgekeurd=3 | Impliceert dat de downloadbewerking eerder is voltooid en Reparatiebeheer de installatie heeft goedgekeurd.
      InstallationInProgress=4 | Komt overeen met de stand van uitvoering van de reparatietaak.
      Installatievoltooid=5 | Impliceert dat de installatie met succes, gedeeltelijk succes, of mislukking werd voltooid.
      Opnieuw opstartenGevraagd=6 | Impliceert dat de patchinstallatie is voltooid en dat er een in behandeling zijnde herstartactie op het knooppunt is.
      Opnieuw opstartenNietnodig=7 |  Impliceert dat de herstart niet nodig was nadat de patchinstallatie was voltooid.
      RestartCompleted=8 | Impliceert dat de herstart met succes is voltooid.
      OperationCompleted=9 | De windows update-bewerking is voltooid.
      OperationAborted=10 | Impliceert dat de Windows Update-bewerking is afgebroken.

1. In POA-versies 1.4.0 en hoger, wanneer een node-updatepoging is voltooid, wordt een gebeurtenis met de eigenschap "WUOperationStatus-[NodeName]" op NodeAgentService geplaatst om u op de hoogte te stellen wanneer de volgende poging om de Windows-updates te downloaden en te installeren begint. Dit wordt weergegeven in de volgende afbeelding:

     [![Afbeelding van de bewerkingsstatus van Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Logboeken met diagnostische gegevens

Patch orchestration-toepassingslogboeken worden verzameld als onderdeel van de runtime-logboeken van Service Fabric.

U logboeken vastleggen met behulp van het diagnoseprogramma of de pijplijn naar keuze. POA gebruikt de volgende vaste provider---geïdentificeerde gegevens om gebeurtenissen te registreren via [gebeurtenisbron:](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Gezondheidsrapporten

POA publiceert ook gezondheidsrapporten tegen de Node Agent Service of de Coördinator Service in de volgende scenario's:

* De Node Agent NTService is uitgeschakeld

   Als de Node Agent NTService op een knooppunt staat, wordt een gezondheidsrapport op waarschuwingniveau gegenereerd tegen de Node Agent Service.

* De service Reparatiebeheer is niet ingeschakeld

   Als de service Reparatiebeheer niet op het cluster wordt gevonden, wordt een gezondheidsrapport op waarschuwingniveau gegenereerd voor de coördinatorservice.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Waarom zie ik mijn cluster in een foutstatus wanneer POA wordt uitgevoerd?**

A: Tijdens het installatieproces schakelt POA knooppunten uit of start opnieuw op, wat tijdelijk kan resulteren in een ongezond cluster.

Afhankelijk van het beleid van de toepassing kan één knooppunt naar beneden gaan tijdens een patchbewerking *of* kan een volledig updatedomein in één keer naar beneden gaan.

Tegen het einde van de installatie van Windows-updates worden de knooppunten na het opnieuw opstarten opnieuw ingeschakeld.

In het volgende voorbeeld ging het cluster tijdelijk naar een foutstatus omdat twee knooppunten waren uitgeschakeld en het beleid MaxPercentageUnhealthyNodes is geschonden. De fout is tijdelijk totdat de patchbewerking kan beginnen.

![Afbeelding van ongezonde cluster](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Als het probleem blijft bestaan, raadpleegt u de sectie Probleemoplossing.

**V: Wat kan ik doen als POA in een waarschuwingsstatus verkeert?**

A: Controleer of een gezondheidsrapport dat is geplaatst tegen de toepassing de hoofdoorzaak aangeeft. Meestal bevat de waarschuwing details van het probleem. Als het probleem van voorbijgaande aard is, wordt verwacht dat de toepassing automatisch wordt hersteld.

**V: Wat kan ik doen als mijn cluster ongezond is en ik een dringende update van het besturingssysteem moet uitvoeren?**

A: POA installeert geen updates terwijl het cluster niet in orde is. Probeer uw cluster naar een gezonde staat te brengen om de POA-werkstroom te deblokkeren.

**V: Moet ik het goedkeuringsbeleid instellen als "NodeWise" of "UpgradeDomainWise" voor mijn cluster?**

A: De instelling "UpgradeDomainWise" versnelt de algehele clusterreparatie door alle knooppunten die deel uitmaken van een updatedomein parallel te patchen. Tijdens het proces zijn knooppunten die deel uitmaken van een volledig updatedomein niet beschikbaar (in [ *de status Uitgeschakeld).* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)

De instelling 'NodeWise' patcht daarentegen slechts één knooppunt tegelijk, wat zou betekenen dat het automatisch patchen van de cluster langer kan duren. Echter, slechts een knooppunt op zijn hoogst zou niet beschikbaar zijn (in *uitgeschakeldstaat)* tijdens het patchproces.

Als uw cluster kan tolereren dat wordt uitgevoerd op een N-1-aantal updatedomeinen tijdens de patchcyclus (waarbij N het totale aantal updatedomeinen op uw cluster is), u het beleid instellen als 'UpgradeDomainWise'. Anders stelt u het in op 'NodeWise'.

**V: Hoeveel tijd kost het om een knooppunt te patchen?**

A: Het patchen van een knooppunt kan enkele minuten (bijvoorbeeld [Windows Defender-definitieupdates)](https://www.microsoft.com/en-us/wdsi/definitions)tot uren duren (bijvoorbeeld [Windows Cumulatieve updates).](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update) De tijd die nodig is om een knooppunt te patchen, hangt vooral af van: 
 - De grootte van de updates.
 - Het aantal updates, die moeten worden toegepast in een patchvenster.
 - De tijd die nodig is om de updates te installeren, het knooppunt opnieuw op te starten (indien nodig) en de installatiestappen na het opnieuw opstarten te voltooien.
 - De prestaties van de VM of machine en netwerkomstandigheden.

**V: Hoe lang duurt het om een heel cluster te patchen?**

A: De tijd die nodig is om een heel cluster te patchen, is afhankelijk van:

- De tijd die nodig is om een knooppunt te patchen.

- Het beleid van de Coördinator Service. Het standaardbeleid, 'NodeWise', resulteert in het patchen van slechts één knooppunt tegelijk, een benadering die langzamer is dan het gebruik van 'UpgradeDomainWise'. 

   Bijvoorbeeld: Als het patchen van een knooppunt ~1 uur duurt, vereist het patchen van een cluster met 20 knooppunten (hetzelfde type knooppunten) met 5 updatedomeinen, die elk 4 knooppunten bevatten:
    - Voor "NodeWise": ~20 uur.
    - Voor "UpgradeDomainwise": ~5 uur.

- De clusterbelasting. Elke patchbewerking vereist het verplaatsen van de werkbelasting van de klant naar andere beschikbare knooppunten in het cluster. Een knooppunt dat wordt gepatcht zou in [ *uitschakelen* staat](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) gedurende deze tijd. Als het cluster bijna piekbelasting uitvoert, duurt het uitschakelen langer. Daarom lijkt het algehele patchproces traag te zijn in dergelijke stressomstandigheden.

- Clusterstatusfouten tijdens het patchen. Elke [verslechtering](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) [van](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) de status van het cluster zou het patchproces onderbreken. Dit probleem zou toevoegen aan de totale tijd die nodig is om het hele cluster patch.

**V: Waarom zie ik een aantal updates in de Windows Update-resultaten die worden verkregen via REST API, maar niet onder de Windows Update-geschiedenis op de machine?**

A: Sommige productupdates worden alleen weergegeven in hun eigen update- of patchgeschiedenis. Windows Defender-updates worden bijvoorbeeld al dan niet weergegeven in de geschiedenis van Windows Update op Windows Server 2016.

**V: Kan POA worden gebruikt om mijn dev-cluster (cluster met één knooppunt) te patchen?**

A: Nee, POA kan niet worden gebruikt om een cluster met één knooppunt te patchen. Deze beperking is door het ontwerp, omdat [Service Fabric-systeemservices](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) of andere klantapps downtime zouden veroorzaken. Daarom zou patchen reparatie taken nooit worden goedgekeurd door Repair Manager.

**V: Hoe patch ik clusternodes op Linux?**

A: Zie [Azure virtual machine scale set automatic OS image upgrades](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)voor meer informatie over het orkestreren van updates op Linux.

**V: Waarom duurt de updatecyclus zo lang?**

A: Query voor het resultaat JSON, voer de updatecyclus in voor alle knooppunten en vervolgens u proberen de tijd te vinden die nodig is voor de installatie van de update op elk knooppunt met behulp van OperationStartTime en OperationTime (OperationCompletionTime). 

Als er een groot tijdvenster is waarin geen update plaatsvindt, kan het cluster in een foutstatus staan en kan Reparatiebeheer daarom geen POA-reparatietaken goedkeuren. Als de update-installatie lang duurt op een knooppunt, is dat knooppunt mogelijk niet bijgewerkt in een tijdje. Veel updates kunnen in afwachting van de installatie, wat kan leiden tot vertragingen. 

Het is ook mogelijk dat node patching is geblokkeerd omdat het vastzit in *de status Uitschakelen.* Dit gebeurt meestal omdat het uitschakelen van het knooppunt kan leiden tot situaties van quorum- of gegevensverlies.

**V: Waarom moet het knooppunt worden uitgeschakeld wanneer POA het patcht?**

A: POA schakelt het knooppunt uit met een intentie *voor opnieuw starten,* waardoor alle Service Fabric-services die op het knooppunt worden uitgevoerd, worden gestopt of opnieuw worden toegewezen. POA doet dit om ervoor te zorgen dat toepassingen niet eindigen met behulp van een mix van nieuwe en oude DLL's, dus we raden niet patchen van een knooppunt zonder uit te schakelen.

**V: Wat is het maximum aantal knooppunten dat kan worden bijgewerkt met behulp van POA?**

A: POA gebruikt Service Fabric Repair Manager om reparatietaken voor knooppunten voor updates te maken. Er kunnen echter niet meer dan 250 reparatietaken tegelijkertijd aanwezig zijn. Momenteel maakt POA reparatietaken voor elk knooppunt op hetzelfde moment, zodat POA niet meer dan 250 knooppunten in een cluster kan bijwerken. 

## <a name="disclaimers"></a>Disclaimers

- POA accepteert namens de gebruiker de licentieovereenkomst voor eindgebruikers voor Windows Update. Optioneel kan de instelling worden uitgeschakeld in de configuratie van de toepassing.

- POA verzamelt telemetrie om het gebruik en de prestaties bij te houden. De telemetrie van de toepassing volgt de instelling van de telemetrie-instelling van de Service Fabric-runtime (die standaard is ingeschakeld).

## <a name="troubleshooting"></a>Problemen oplossen

In deze sectie worden mogelijke oplossingen voor het oplossen van problemen met patchknooppunten mogelijk gemaakt.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Een knooppunt komt niet terug naar de staat

* Het knooppunt zit mogelijk vast in de status *Uitschakelen* omdat:

  - Er is een veiligheidscontrole in behandeling. Om deze situatie te verhelpen, moet u ervoor zorgen dat er voldoende knooppunten beschikbaar zijn in een gezonde staat.

* Het knooppunt zit mogelijk vast in *de status Uitgeschakeld* omdat:

  - Het was handmatig uitgeschakeld.
  - Het werd uitgeschakeld vanwege een lopende Azure-infrastructuurtaak.
  - Het werd tijdelijk uitgeschakeld door POA om het knooppunt te patchen.

* Het knooppunt kan in down staat worden geplakt omdat:

  - Het werd handmatig in down state geplaatst.
  - Het ondergaat een herstart (die kan worden geactiveerd door POA).
  - Het heeft een defecte VM of machine, of het heeft problemen met de netwerkverbinding.

### <a name="updates-were-skipped-on-some-nodes"></a>Updates werden overgeslagen op sommige knooppunten

POA probeert een Windows-update te installeren volgens het herschikkingsbeleid. De service probeert het knooppunt te herstellen en de update over te slaan volgens het toepassingsbeleid.

In een dergelijk geval wordt een gezondheidsrapport op waarschuwingsniveau gegenereerd tegen de Node Agent Service. Het resultaat van Windows Update bevat ook de mogelijke reden voor de storing.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>De status van het cluster gaat naar fouten tijdens de installatie van de update

Een defecte Windows-update kan de status van een toepassing of cluster op een bepaald knooppunt of updatedomein naar beneden halen. POA stopt met een volgende Windows Update-bewerking totdat het cluster weer in orde is.

Een beheerder moet ingrijpen en bepalen waarom de toepassing of het cluster ongezond is geworden vanwege Windows Update.

## <a name="poa-release-notes"></a>POA release notes

>[!NOTE]
> Voor POA-versies 1.4.0 en hoger vindt u releasenotes en releases op de [releasepagina patchorchestration-toepassing](https://github.com/microsoft/Service-Fabric-POA/releases/) op GitHub.

### <a name="version-110"></a>Versie 1.1.0
- Publieke release

### <a name="version-111"></a>Versie 1.1.1
- Een bug opgelost in SetupEntryPoint van NodeAgentService waardoor de installatie van NodeAgentNTService werd verhinderd.

### <a name="version-120"></a>Versie 1.2.0

- Bugfixes rond het opnieuw opstarten van het systeem workflow.
- Bug fix in het maken van RM taken als gevolg van welke health check tijdens het voorbereiden van reparatie taken gebeurde niet zoals verwacht.
- Wijzigde de opstartmodus voor Windows-service POANodeSvc van automatisch naar vertraagd automatisch.

### <a name="version-121"></a>Versie 1.2.1

- Bug fix in cluster scale-down workflow. Geïntroduceerd garbage collection logica voor POA reparatie taken die behoren tot niet-bestaande knooppunten.

### <a name="version-122"></a>Versie 1.2.2

- Diverse bugfixes.
- Binaries zijn nu ondertekend.
- Toegevoegd sfpkg link voor de toepassing.

### <a name="version-130"></a>Versie 1.3.0

- Als u WindowsOSOnlyUpdates instelt op false, worden nu alle beschikbare updates geïnstalleerd.
- Hiermee is de logica van het uitschakelen van automatische updates gewijzigd. Dit lost een bug op waarbij automatische updates niet werden uitgeschakeld op Server 2016 en hoger.
- Parameterized plaatsingsbeperking voor zowel de microservices van POA voor geavanceerde use cases.

### <a name="version-131"></a>Versie 1.3.1
- Regressie oplossen waarbij POA 1.3.0 niet werkt op Windows Server 2012 R2 of eerder vanwege een storing in het uitschakelen van automatische updates. 
- Het oplossen van bug waar installWindowsOSOnlyUpdates configuratie wordt altijd gekozen als True.
- Standaardwaarde van WindowsOSOnlyUpdates installeren wijzigen in False.

### <a name="version-132"></a>Versie 1.3.2
- Als er knooppunten zijn met een naam die een subset is van de huidige knooppuntnaam, wordt een probleem opgelost dat van invloed is op de levenscyclus van het patchen op een knooppunt. Voor dergelijke knooppunten is het mogelijk dat patchen is gemist of dat er een reboot in behandeling is.
