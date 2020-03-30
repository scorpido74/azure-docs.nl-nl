---
title: Zelfstandige clusterimplementatievoorbereiding
description: Documentatie met betrekking tot het voorbereiden van de omgeving en het maken van de clusterconfiguratie, die moet worden overwogen voordat een cluster wordt geïmplementeerd dat is bedoeld voor het verwerken van een productiewerkbelasting.
author: dkkapur
ms.topic: conceptual
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 6a00b7d1b72d594c08021982b2448de6275414c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610060"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Uw servicefabric-implementatie van standalone-clusterplannen en voorbereiden

<a id="preparemachines"></a>Voer de volgende stappen uit voordat u uw cluster maakt.

## <a name="plan-your-cluster-infrastructure"></a>Uw clusterinfrastructuur plannen
U staat op het punt een Cluster Service Fabric te maken op machines die u bezit, zodat u bepalen wat voor fouten u wilt dat het cluster overleeft. Heeft u bijvoorbeeld aparte stroomlijnen of internetverbindingen nodig die op deze machines worden geleverd? Houd bovendien rekening met de fysieke veiligheid van deze machines. Waar bevinden zich de machines en wie heeft er toegang toe nodig? Nadat u deze beslissingen hebt genomen, u de machines logischerwijs toewijzen aan verschillende foutdomeinen (zie volgende stap). De infrastructuurplanning voor productieclusters is meer betrokken dan voor testclusters.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Het aantal foutdomeinen en upgradedomeinen bepalen
Een [ *foutdomein* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) is een fysieke storingseenheid en is direct gerelateerd aan de fysieke infrastructuur in de datacenters. Een foutdomein bestaat uit hardwarecomponenten (computers, switches, netwerken en meer) die één storingspunt delen. Hoewel er geen 1:1 mapping tussen fout domeinen en racks, losjes gesproken, elk rack kan worden beschouwd als een fout domein.

Wanneer u fd's opgeeft in ClusterConfig.json, u de naam voor elke FD kiezen. Service Fabric ondersteunt hiërarchische FD's, zodat u uw infrastructuurtopologie daarin weergeven.  De volgende FD's zijn bijvoorbeeld geldig:

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

Een *upgradedomein* (UD) is een logische eenheid van knooppunten. Tijdens servicefabric georkestreerde upgrades (een upgrade van een toepassing of een clusterupgrade) worden alle knooppunten in een UD naar beneden gehaald om de upgrade uit te voeren, terwijl knooppunten in andere UD's beschikbaar blijven om aanvragen te serveren. De firmware-upgrades die u uitvoert op uw machines niet eer UDs, dus je moet ze doen een machine tegelijk.

De eenvoudigste manier om na te denken over deze concepten is om fd's te beschouwen als de eenheid van ongeplande mislukking en UDs als de eenheid van gepland onderhoud.

Wanneer u UD's opgeeft in ClusterConfig.json, u de naam voor elke UD kiezen. De volgende namen zijn bijvoorbeeld geldig:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blauw"

Zie [Een cluster van servicefabricen beschrijven](service-fabric-cluster-resource-manager-cluster-description.md)voor meer gedetailleerde informatie over OEM's en UD's.

Een cluster in productie moet ten minste drie fd's omvatten om te worden ondersteund in een productieomgeving, als u volledige controle hebt over het onderhoud en beheer van de knooppunten, dat wil zeggen dat u verantwoordelijk bent voor het bijwerken en vervangen van machines. Voor clusters die worden uitgevoerd in omgevingen (dat wil zeggen Amazon Web Services VM-exemplaren) waar u geen volledige controle over de machines hebt, moet u minimaal vijf fd's in uw cluster hebben. Elk FD kan een of meer knooppunten hebben. Dit is om problemen te voorkomen die worden veroorzaakt door machine-upgrades en updates, die afhankelijk van hun timing, kunnen interfereren met de werking van toepassingen en services in clusters.

## <a name="determine-the-initial-cluster-size"></a>De oorspronkelijke clustergrootte bepalen

Over het algemeen wordt het aantal knooppunten in uw cluster bepaald op basis van uw bedrijfsbehoeften, dat wil zeggen hoeveel services en containers op het cluster worden uitgevoerd en hoeveel resources u nodig hebt om uw workloads te ondersteunen. Voor productieclusters raden we aan om ten minste vijf knooppunten in uw cluster te hebben, verspreid over 5 fd's. Echter, zoals hierboven beschreven, als u volledige controle over uw knooppunten en kan drie FD's overspannen, dan drie knooppunten moet ook het werk doen.

Testclusters met stateful workloads moeten drie knooppunten hebben, terwijl testclusters die alleen stateless workloads uitvoeren, slechts één knooppunt nodig hebben. Er moet ook worden opgemerkt dat voor ontwikkelingsdoeleinden, u meer dan een knooppunt op een bepaalde machine. In een productieomgeving ondersteunt Service Fabric echter slechts één knooppunt per fysieke of virtuele machine.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Bereid de machines voor die als knooppunten dienen

Hier volgen enkele aanbevolen specificaties voor elke machine die u aan het cluster wilt toevoegen:

* Minimaal 16 GB RAM
* Minimaal 40 GB beschikbare schijfruimte
* Een 4-core of meer CPU
* Connectiviteit met een beveiligd netwerk of netwerken voor alle machines
* Windows Server OS geïnstalleerd (geldige versies: 2012 R2, 2016, 1709 of 1803). Service Fabric versie 6.4.654.9590 en later ondersteunt ook Server 2019 en 1809.
* [.NET Framework 4.5.1 of hoger,](https://www.microsoft.com/download/details.aspx?id=40773)volledige installatie
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/install/installing-windows-powershell)
* De [RemoteRegistry-service](https://technet.microsoft.com/library/cc754820) moet op alle machines worden uitgevoerd
* Installatiestation Service Fabric moet NTFS-bestandssysteem zijn

De clusterbeheerder die het cluster implementeert en configureert, moet [beheerdersbevoegdheden](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) hebben voor elk van de machines. U kunt Service Fabric niet installeren op een domeincontroller.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Het standalone pakket Service Fabric voor Windows Server downloaden
[Download link - Service Fabric Standalone Package - Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) en rits het pakket uit, hetzij naar een implementatiemachine die geen deel uitmaakt van het cluster, hetzij naar een van de machines die deel uitmaken van uw cluster.

## <a name="modify-cluster-configuration"></a>Clusterconfiguratie wijzigen
Als u een zelfstandig cluster wilt maken, moet u een zelfstandig clusterconfiguratie ClusterConfig.json-bestand maken, waarin de specificatie van het cluster wordt beschreven. U het configuratiebestand baseren op de sjablonen op de onderstaande link. <br>
[Zelfstandige clusterconfiguraties](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Zie [Configuratie-instellingen voor het zelfstandige Windows-cluster voor](service-fabric-cluster-manifest.md)meer informatie over de secties in dit bestand.

Open een van de ClusterConfig.json-bestanden van het pakket dat u hebt gedownload en wijzig de volgende instellingen:

| **Configuratie-instelling** | **Beschrijving** |
| --- | --- |
| **NodeTypes** |Met knooppunttypen u uw clusterknooppunten in verschillende groepen scheiden. Een cluster moet ten minste één NodeType hebben. Alle knooppunten in een groep hebben de volgende gemeenschappelijke kenmerken: <br> **Naam** - Dit is de naam van het knooppunttype. <br>**Eindpuntpoorten** - Dit zijn verschillende benoemde eindpunten (poorten) die zijn gekoppeld aan dit knooppunttype. U elk poortnummer gebruiken dat u wenst, zolang ze niet in strijd zijn met iets anders in dit manifest en nog niet in gebruik zijn door een andere toepassing die op de machine/VM wordt uitgevoerd. <br> **Plaatsingseigenschappen** : deze beschrijven eigenschappen voor dit knooppunttype dat u gebruikt als plaatsingsbeperkingen voor de systeemservices of uw services. Deze eigenschappen zijn door de gebruiker gedefinieerde sleutel-/waardeparen die extra metagegevens voor een bepaald knooppunt leveren. Voorbeelden van knooppunteigenschappen zijn of het knooppunt een harde schijf of grafische kaart heeft, het aantal spindels in de harde schijf, kernen en andere fysieke eigenschappen. <br> **Capaciteiten** - Knooppuntcapaciteiten definiëren de naam en de hoeveelheid van een bepaalde resource die een bepaald knooppunt beschikbaar heeft voor consumptie. Een knooppunt kan bijvoorbeeld bepalen dat het capaciteit heeft voor een statistiek met de naam "MemoryInMb" en dat het standaard 2048 MB beschikbaar heeft. Deze capaciteiten worden gebruikt tijdens de runtime om ervoor te zorgen dat services waarvoor bepaalde hoeveelheden resources nodig zijn, worden geplaatst op de knooppunten die over deze resources beschikken in de vereiste bedragen.<br>**IsPrimary** - Als u meer dan één NodeType hebt gedefinieerd, moet u ervoor zorgen dat er slechts één is ingesteld op primair met de waarde *true,* en dat is waar de systeemservices worden uitgevoerd. Alle andere knooppunttypen moeten worden ingesteld op de waarde *false* |
| **Knooppunten** |Dit zijn de details voor elk van de knooppunten die deel uitmaken van het cluster (knooppunttype, knooppuntnaam, IP-adres, foutdomein en upgradedomein van het knooppunt). De machines waarop het cluster moet worden gemaakt, moeten hier worden vermeld met hun IP-adressen. <br> Als u hetzelfde IP-adres voor alle knooppunten gebruikt, wordt een cluster met één doos gemaakt, dat u gebruiken voor testdoeleinden. Gebruik geen One-box clusters voor het implementeren van productieworkloads. |

Nadat de clusterconfiguratie alle instellingen heeft geconfigureerd voor de omgeving, kan deze worden getest op basis van de clusteromgeving (stap 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Configuratie van de omgeving

Wanneer een clusterbeheerder een zelfstandige cluster servicestructuur configureert, moet de omgeving worden ingesteld met de volgende criteria: <br>
1. De gebruiker die het cluster maakt, moet beveiligingsbevoegdheden op administratorniveau hebben voor alle machines die worden weergegeven als knooppunten in het clusterconfiguratiebestand.
2. Machine waaruit het cluster is gemaakt, evenals elke clusterknooppuntmachine moet:
   * Service Fabric SDK laten verwijderd
   * Runtime van servicestof laten verwijderd zijn
   * De Windows Firewall-service (mpssvc) ingeschakeld hebben
   * De Remote Registry Service (remote registry) ingeschakeld hebben
   * Bestandsdeling (SMB) ingeschakeld
   * De benodigde poorten laten openen op basis van clusterconfiguratiepoorten
   * De benodigde poorten zijn geopend voor Windows SMB en Remote Registry-service: 135, 137, 138, 139 en 445
   * Netwerkconnectiviteit met elkaar hebben
3. Geen van de clusterknooppuntmachines mag een domeincontroller zijn.
4. Als het te implementeren cluster een beveiligd cluster is, zijn de vereiste beveiligingsvereisten gevalideerd en correct geconfigureerd tegen de configuratie.
5. Als de clustermachines niet internettoegankelijk zijn, stelt u het volgende in de clusterconfiguratie in:
   * Telemetrie uitschakelen: onder *eigenschappen* set *"enableTelemetry": false*
   * Automatische Fabric-versie downloaden uitschakelen & meldingen dat de huidige clusterversie het einde van de ondersteuning nadert: Onder *eigenschappenset* *'fabricClusterAutoupgradeEnabled': false*
   * Als internettoegang voor het netwerk beperkt is tot domeinen die op de witte lijst staan, zijn de onderstaande domeinen ook vereist voor automatische upgrade: go.microsoft.com download.microsoft.com

6. Stel de uitsluitingen van de juiste antivirusprogramma's van Service Fabric in:

| **Antivirus uitgesloten mappen** |
| --- |
| Programmabestanden\Microsoft Service Fabric |
| FabricDataRoot (van clusterconfiguratie) |
| FabricLogRoot (van clusterconfiguratie) |

| **Antivirus uitgesloten processen** |
| --- |
| Fabric.exe (Fabric.exe) |
| FabricHost.exe (FabricHost.exe) |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe (FabricGateway.exe) |
| FabricDCA.exe (FabricDCA.exe) |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe (FabricRM.exe) |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Omgeving valideren met het script TestConfiguration
Het TestConfiguration.ps1 script is te vinden in het standalone pakket. Het wordt gebruikt als een Best Practices Analyzer om een aantal van de bovenstaande criteria te valideren en moet worden gebruikt als een geestelijke gezondheid te controleren om te valideren of een cluster kan worden geïmplementeerd op een bepaalde omgeving. Als er een storing optreedt, raadpleegt u de lijst onder [Omgevinginstelling](service-fabric-cluster-standalone-deployment-preparation.md) voor het oplossen van problemen.

Dit script kan worden uitgevoerd op elke machine die beheerderstoegang heeft tot alle machines die worden vermeld als knooppunten in het clusterconfiguratiebestand. De machine waarop dit script wordt uitgevoerd, hoeft geen deel uit te maken van het cluster.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
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

Momenteel valideert deze configuratietestmodule de beveiligingsconfiguratie niet, dus dit moet onafhankelijk van elkaar worden gedaan.

> [!NOTE]
> We zijn voortdurend het maken van verbeteringen om deze module robuuster te maken, dus als er een defecte of ontbrekende zaak die u gelooft is momenteel niet gevangen door TestConfiguration, laat het ons weten via onze [ondersteuningskanalen](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
>
>

## <a name="next-steps"></a>Volgende stappen
* [Een zelfstandig cluster maken dat wordt uitgevoerd in Windows Server](service-fabric-cluster-creation-for-windows-server.md)
