---
title: Een zelfstandig Azure Service Fabric-cluster maken
description: Maak een Azure Service Fabric-cluster op elke machine (fysiek of virtueel) met Windows Server, of het nu on-premises of in een cloud is.
author: dkkapur
ms.topic: conceptual
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 461d6021a201ca1fa5722bb44c427baca2a7728e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258822"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Een zelfstandig cluster maken dat wordt uitgevoerd in Windows Server
U Azure Service Fabric gebruiken om Service Fabric-clusters te maken op virtuele machines of computers met Windows Server. Dit betekent dat u Service Fabric-toepassingen implementeren en uitvoeren in elke omgeving die een set onderling verbonden Windows Server-computers bevat, of het nu on-premises is of met elke cloudprovider. Service Fabric biedt een installatiepakket voor het maken van Service Fabric-clusters, het standalone Windows Server-pakket. Traditionele Service Fabric-clusters op Azure zijn beschikbaar als beheerde service, terwijl zelfstandige Service Fabric-clusters selfservice zijn. Zie [Azure- en zelfstandige servicefabricclusters vergelijken](./service-fabric-deploy-anywhere.md)voor meer informatie over de verschillen.

In dit artikel vindt u de stappen voor het maken van een zelfstandig cluster servicestructuur.

> [!NOTE]
> Dit standalone Windows Server-pakket is gratis beschikbaar en kan worden gebruikt voor productie-implementaties. Dit pakket kan nieuwe Functies van Service Fabric bevatten die zich in 'Preview' bevinden. Scroll naar beneden naar '[Preview-functies in dit pakket.'](#previewfeatures_anchor) sectie voor de lijst met de voorbeeldfuncties. U nu [een exemplaar van de EULA downloaden.](https://go.microsoft.com/fwlink/?LinkID=733084)
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Ondersteuning krijgen voor het Service Fabric voor Windows Server-pakket
* Vraag de community naar het standalone servicepakket voor Windows Server in het [Azure Service Fabric-forum.](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?)
* Open een ticket voor [Professional Support for Service Fabric.](https://support.microsoft.com/oas/default.aspx?prid=16146)  Meer informatie over professionele ondersteuning van Microsoft [vindt u hier](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* U ook ondersteuning voor dit pakket krijgen als onderdeel van [Microsoft Premier Support.](https://support.microsoft.com/en-us/premier)
* Zie [ondersteuningsopties voor Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)voor meer informatie.
* Voer het [collector servicefabric-logboekophaalmateriaal](service-fabric-cluster-standalone-package-contents.md)uit om logboeken te verzamelen voor ondersteuningsdoeleinden.

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Het pakket Service Fabric voor Windows Server downloaden
Als u het cluster wilt maken, gebruikt u het Service Fabric for Windows Server-pakket (Windows Server 2012 R2 en nieuwer) dat hier wordt gevonden: <br>
[Downloadkoppeling - Zelfstandig pakket servicefabric - Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Hier vindt u meer informatie over de inhoud van het [pakket.](service-fabric-cluster-standalone-package-contents.md)

Het runtime-pakket Service Fabric wordt automatisch gedownload op het moment van het maken van het cluster. Als u de implementatie uitvoert vanaf een machine die niet met internet is verbonden, download dan het runtime-pakket vanaf hier: <br>
[Downloadkoppeling - Runtime van servicefabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Zelfstandige clusterconfiguratievoorbeelden zoeken op: <br>
[Zelfstandige clusterconfiguratievoorbeelden](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Het cluster maken
Met het installatiepakket worden diverse voorbeelden van clusterconfiguraties geïnstalleerd. *ClusterConfig.Unsecure.DevCluster.json* is de eenvoudigste clusterconfiguratie: een onbeveiligd cluster met drie knooppunten die worden uitgevoerd op één computer.  Andere configuratiebestanden beschrijven clusters voor één of meerdere machines die zijn beveiligd met een x.509-certificaat of met Windows-beveiliging.  U hoeft de standaardconfiguratie-instellingen voor deze zelfstudie niet te wijzigen, maar neem wel het configuratiebestand door om vertrouwd te raken met de instellingen.  De sectie **nodes** beschrijft de drie knooppunten in het cluster: naam, IP-adres, [knooppunttype, foutdomein en upgradedomein](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  De sectie **properties** definieert [de beveiliging, het betrouwbaarheidsniveau, de diagnostische verzameling en de typen knooppunten](service-fabric-cluster-manifest.md#cluster-properties) voor het cluster.

Het cluster dat in dit artikel is gemaakt, is onveilig.  Iedereen kan anoniem verbinding maken en beheerbewerkingen uitvoeren. Productieclusters moeten dus altijd worden beveiligd met X.509-certificaten of Windows-beveiliging.  Beveiliging kan alleen worden geconfigureerd tijdens het maken van het cluster. Het is niet mogelijk beveiliging in te schakelen nadat het cluster is gemaakt. Update het config-bestand in schakel [certificaatbeveiliging](service-fabric-windows-cluster-x509-security.md) of [Windows-beveiliging in.](service-fabric-windows-cluster-windows-security.md) Lees [Een cluster beveiligen](service-fabric-cluster-security.md) voor meer informatie over de beveiliging van Service Fabric-clusters.

### <a name="step-1-create-the-cluster"></a>Stap 1: Het cluster maken

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Scenario A: Een onbeveiligd lokaal ontwikkelingscluster maken
Service Fabric kan worden geïmplementeerd in een cluster voor één machineontwikkeling met behulp van het bestand *ClusterConfig.Unsecure.DevCluster.json* in [Samples](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Pak het standalone pakket uit naar uw machine, kopieer het voorbeeldconfig-bestand naar de lokale machine en voer het *Script CreateServiceFabricCluster.ps1* uit via een PowerShell-sessie van de beheerder, vanuit de zelfstandige pakketmap.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Bekijk de sectie Omgevingsinstellingen bij [Plannen en bereid uw clusterimplementatie](service-fabric-cluster-standalone-deployment-preparation.md) voor op details over probleemoplossing.

Als u klaar bent met het uitvoeren van ontwikkelingsscenario's, u het cluster Servicefabric uit de machine verwijderen door te verwijzen naar stappen in sectie[Een cluster verwijderen Verwijderen.](#removecluster_anchor) 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Scenario B: Een cluster met meerdere machines maken
Nadat u de plannings- en voorbereidingsstappen hebt doorlopen die bij Plan zijn beschreven [en uw clusterimplementatie hebben voorbereid,](service-fabric-cluster-standalone-deployment-preparation.md)bent u klaar om uw productiecluster te maken met behulp van uw clusterconfiguratiebestand.

De clusterbeheerder die het cluster implementeert en configureert, moet administratorbevoegdheden hebben op de computer. U kunt Service Fabric niet installeren op een domeincontroller.

1. Het script *TestConfiguration.ps1* in het zelfstandige pakket wordt gebruikt als Best Practices Analyzer om te valideren of een cluster in een bepaalde omgeving kan worden geïmplementeerd. In de [Implementatievoorbereiding](service-fabric-cluster-standalone-deployment-preparation.md) vindt u de implementatie- en omgevingsvereisten. Voer het script uit om te controleren of u het ontwikkelingscluster kunt maken:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    De uitvoer ziet er als volgt uit. Als het onderste veld 'Geslaagd' wordt geretourneerd als 'True', zijn de verstandsaniteitscontroles geslaagd en lijkt het cluster te kunnen worden geïmplementeerd op basis van de invoerconfiguratie.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Het cluster maken: voer het script *CreateServiceFabricCluster.ps1* uit om het cluster Servicefabric op elke machine in de configuratie te implementeren. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Er worden implementatietraceringen weggeschreven naar de VM/computer waarop u het PowerShell-script CreateServiceFabricCluster.ps1 hebt uitgevoerd. U vindt deze traceringen in de submap DeploymentTraces, in de map van waaruit het script is uitgevoerd. U weet dat Service Fabric goed is geïmplementeerd op een computer als de geïnstalleerde bestanden in de map FabricDataRoot staan, zoals beschreven in de sectie FabricSettings van het configuratiebestand voor het cluster (standaard c:\ProgramData\SF). Daarnaast moeten de processen FabricHost.exe en Fabric.exe actief zijn in Taakbeheer.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Scenario C: Een offline cluster maken (zonder verbinding met internet)
Het runtime-pakket Service Fabric wordt automatisch gedownload bij het maken van clusteren. Wanneer u een cluster implementeert op machines die niet met internet zijn verbonden, moet u het runtime-pakket Service Fabric afzonderlijk downloaden en het pad naar het cluster maken.
Het runtime-pakket kan afzonderlijk worden gedownload, vanaf een andere machine die is aangesloten op het internet, bij [Download Link - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Kopieer het runtime-pakket naar de plaats waar u het offlinecluster implementeert en maak het cluster door te worden uitgevoerd `CreateServiceFabricCluster.ps1` met de `-FabricRuntimePackagePath` parameter die is opgenomen, zoals in dit voorbeeld wordt weergegeven: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json* en *.\MicrosoftAzureServiceFabric.cab* zijn respectievelijk de paden naar de clusterconfiguratie en het runtime .cab-bestand.

### <a name="step-2-connect-to-the-cluster"></a>Stap 2: Verbinding maken met het cluster
Maak verbinding met het cluster om te controleren of het cluster actief en beschikbaar is. De Service Fabric PowerShell-module wordt met de runtime geïnstalleerd.  U verbinding maken met het cluster vanaf een van de clusterknooppunten of vanaf een externe computer met de runtime van Service Fabric.  De cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) brengt een verbinding met het cluster tot stand.

Voer de volgende PowerShell-opdracht uit om verbinding te maken met een onveilig cluster:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Bijvoorbeeld:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```

Zie [Connect to a secure cluster](service-fabric-connect-to-secure-cluster.md) (Verbinding maken met een beveiligd cluster) voor meer voorbeelden van clusterverbindingen. Nadat u verbinding hebt gemaakt met het cluster, gebruikt u de cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) om een lijst weer te geven van de knooppunten in het cluster, evenals statusinformatie voor elk knooppunt. **HealthState** moet *OK* zijn voor elk knooppunt.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Stap 3: Het cluster visualiseren met behulp van De verkenner van de stof van de dienst
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) is een goed hulpmiddel om een cluster te visualiseren en toepassingen te beheren.  Service Fabric Explorer is een service die wordt uitgevoerd in het [http://localhost:19080/Explorer](http://localhost:19080/Explorer)cluster, die u opent met behulp van een browser door te navigeren naar .

Het clusterdashboard bevat een overzicht van het cluster, inclusief een overzicht van de toepassings- en knooppuntstatus. In de knooppuntweergave ziet u de fysieke indeling van het cluster. Voor elk knooppunt kunt u controleren voor welke toepassingen er op het knooppunt code is geïmplementeerd.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Knooppunten toevoegen en verwijderen
U kunt knooppunten toevoegen aan of verwijderen uit uw zelfstandige Service Fabric-cluster wanneer de bedrijfsbehoeften veranderen. Zie [Knooppunten toevoegen aan of verwijderen uit een zelfstandig Service Fabric-cluster](service-fabric-cluster-windows-server-add-remove-nodes.md) voor gedetailleerde stappen.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Een cluster verwijderen
Als u een cluster wilt verwijderen, voert u het PowerShell-script *RemoveServiceFabricCluster.ps1* uit vanuit de pakketmap en geeft u daarbij het pad op naar het JSON-configuratiebestand. Optioneel kunt u een locatie voor het verwijderingslogboek opgeven.

Dit script kan worden uitgevoerd op elke machine die beheerderstoegang heeft tot alle machines die worden vermeld als knooppunten in het clusterconfiguratiebestand. De machine waarop dit script wordt uitgevoerd, hoeft geen deel uit te maken van het cluster.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Telemetriegegevens verzameld en hoe u zich ervoor afmelden
Standaard verzamelt het product telemetrie op het gebruik van de Service Fabric om het product te verbeteren. De Best Practice Analyzer die wordt uitgevoerd als onderdeel [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)van de installatie controleert op connectiviteit met . Als deze niet bereikbaar is, mislukt de installatie, tenzij u zich afziet van telemetrie.

1. De telemetriepijplijn probeert de [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) volgende gegevens elke dag te uploaden. Het is een best-effort upload en heeft geen invloed op de clusterfunctionaliteit. De telemetrie wordt alleen verzonden vanaf het knooppunt waarop de failovermanager primair wordt uitgevoerd. Geen andere knooppunten sturen telemetrie uit.
2. De telemetrie bestaat uit:

* Aantal diensten
* Aantal servicetypen
* Aantal aanvragen
* Aantal toepassingsupgrades
* Aantal failovereenheden
* Aantal InBuildFailoverUnits
* Aantal ongezonde Failovereenheden
* Aantal replica's
* Aantal InBuildReplica's
* Aantal standbyreplica's
* Aantal offlinereplica's
* CommonQueueLengte
* QueryWachtrijlengte
* FailoverUnitQueueLength
* CommitQueueLengte
* Aantal knooppunten
* IsContextComplete: Waar/Onwaar
* ClusterId: Dit is een GUID willekeurig gegenereerd voor elk cluster
* ServiceFabricVersie
* IP-adres van de virtuele machine of machine van waaruit de telemetrie wordt geüpload

Als u telemetrie wilt uitschakelen, voegt u het volgende toe aan *eigenschappen* in uw clusterconfig: *inschakelen Telemetrie: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Preview-functies in dit pakket
Geen.


> [!NOTE]
> Vanaf de nieuwe [GA-versie van het standalone cluster voor Windows Server (versie 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)u uw cluster handmatig of automatisch upgraden naar toekomstige releases. Raadpleeg [Een afzonderlijk clusterversiedocument voor servicestructuur](service-fabric-cluster-upgrade-windows-server.md) voor meer informatie.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* [Toepassingen implementeren en verwijderen met PowerShell](service-fabric-deploy-remove-applications.md)
* [Configuratie-instellingen voor een zelfstandig Windows-cluster](service-fabric-cluster-manifest.md)
* [Knooppunten toevoegen of verwijderen aan een zelfstandig cluster van servicefabric](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Een zelfstandige clusterversie van Service Fabric upgraden](service-fabric-cluster-upgrade-windows-server.md)
* [Een zelfstandig ServiceFabric-cluster maken met Azure VM's met Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Een zelfstandig cluster beveiligen op Windows met Windows-beveiliging](service-fabric-windows-cluster-windows-security.md)
* [Een zelfstandig cluster op Windows beveiligen met X509-certificaten](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
