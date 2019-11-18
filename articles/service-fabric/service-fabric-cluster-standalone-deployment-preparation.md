---
title: Azure Service Fabric zelfstandige Cluster implementatie voorbereiden | Microsoft Docs
description: Documentatie met betrekking tot de omgeving voorbereiden en het maken van de clusterconfiguratie, om te worden beschouwd als vóór de implementatie van een cluster dat is bestemd voor het verwerken van een productie-werkbelasting.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 8b9f659098e563a3dc0692530ad798a5c763551f
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133395"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Plannen en voorbereiden van uw Service Fabric een zelfstandige Clusterimplementatie

<a id="preparemachines"></a>Voer de volgende stappen uit voordat u het cluster maakt.

## <a name="plan-your-cluster-infrastructure"></a>Uw clusterinfrastructuur plannen
U staat op het maken van een Service Fabric-cluster op machines die u 'eigenaar', zodat u kunt beslissen welke soorten fouten die het cluster om te overbruggen. Bijvoorbeeld, u moet Scheid power lijnen of verbindingen via Internet geleverd aan deze machines? Bovendien kunt u overwegen de fysieke beveiliging van deze machines. Waar bevinden de machines zich en die behoefte hebben aan de toegang tot? Nadat u deze beslissingen nemen, kunt u de machines logisch toewijzen aan verschillende domeinen met fouten (Zie de volgende stap). De infrastructuur plannen voor productieclusters is meer betrokken dan voor testclusters.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Bepaalt het aantal foutdomeinen en upgradedomeinen
Een [ *foutdomein* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) is een fysieke eenheid van de fout en is direct gerelateerd aan de fysieke infrastructuur in de datacenters. Een foutdomein bestaat uit hardware-onderdelen (computers, switches, netwerken en meer) die een single point of failure delen. Hoewel er geen 1:1 toewijzing tussen domeinen met fouten en rekken, licht spreken, elk rack kan worden beschouwd als een domein met fouten.

Wanneer u Foutdomeinen in ClusterConfig.json opgeeft, kunt u de naam op voor elke FD kiezen. Service Fabric ondersteunt hiërarchische FD's, zodat u de infrastructuurtopologie van uw erin kunt spiegelen.  Bijvoorbeeld, gelden de volgende FD's:

* "faultDomain": "fd: / Rack1-Room1/Machine1"
* "faultDomain": "fd: / FD1"
* 'faultDomain":" fd: / Room1/Rack1/PDU1/M1 '

Een *upgradedomein* (UD) is een logische eenheid van de knooppunten. Tijdens upgrades van de Service Fabric ingedeeld (een upgrade van de toepassing of het upgraden van een cluster), worden alle knooppunten in een UD offline gezet om uit te voeren van de upgrade knooppunten in andere ud's blijven beschikbaar voor het verzenden van aanvragen. De firmware-upgrades u op uw virtuele machines uitvoeren kunnen niet in acht neemt ud's, zodat u ze een moet doen machine op een tijdstip.

De eenvoudigste manier om na te denken over deze concepten is om te overwegen FD's als de eenheid van niet-geplande storingen en ud's als de eenheid van gepland onderhoud.

Wanneer u ud's in ClusterConfig.json opgeeft, kunt u de naam op voor elke UD kiezen. De volgende namen zijn bijvoorbeeld geldige:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Zie voor meer informatie over Foutdomeinen en Upgradedomeinen, [met een beschrijving van een Service Fabric-cluster](service-fabric-cluster-resource-manager-cluster-description.md).

Een cluster in de productieomgeving moet ten minste drie Foutdomeinen omvatten om te worden ondersteund in een productieomgeving, hebt u volledige controle over het onderhoud en beheer van de knooppunten, dat wil zeggen, u bent verantwoordelijk voor het bijwerken en machines vervangen. Voor clusters die worden uitgevoerd in een omgeving (dat wil zeggen, Amazon Web Services VM-exemplaren) waar u geen volledige controle over de computers, moet u een minimum van vijf Foutdomeinen hebben in uw cluster. Elke FD kan een of meer knooppunten hebben. Zo wordt voorkomen dat problemen veroorzaakt door machine upgrades en updates, afhankelijk van de timing kan leiden tot problemen met het uitvoeren van toepassingen en services in clusters.

## <a name="determine-the-initial-cluster-size"></a>De eerste clustergrootte bepalen

Over het algemeen het aantal knooppunten in uw cluster wordt bepaald op basis van de behoeften van uw bedrijf, die is, hoeveel services en containers wordt op het cluster worden uitgevoerd en hoeveel bronnen moet u mogelijkheden om uw workloads. Voor productieclusters raden wij dat ten minste vijf knooppunten in uw cluster spanning 5 FD's. Echter, als hierboven beschreven, als u volledige controle over uw knooppunten hebt en drie Foutdomeinen kan omspannen, klikt u vervolgens drie knooppunten moeten ook doen de taak.

Testclusters met stateful werkbelastingen moeten drie knooppunten hebben dat alleen het uitvoeren van staatloze werkbelastingen alleen testclusters één knooppunt moeten. Ook moet worden opgemerkt voor ontwikkelingsdoeleinden, kunt u meer dan één knooppunt hebt op een bepaalde computer. In een productieomgeving, Service Fabric ondersteunt slechts één knooppunt per fysieke of virtuele machine.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>De machines die als knooppunten fungeren moeten voorbereiden

Hier volgen enkele aanbevolen specificaties voor elke machine die u wilt toevoegen aan het cluster:

* Een minimum van 16 GB aan RAM-geheugen
* Een minimum van 40 GB beschikbare schijfruimte
* Een 4-core of hoger CPU
* Verbinding met een beveiligd netwerk of netwerken voor alle machines
* Windows Server-besturings systeem geïnstalleerd (geldige versies: 2012 R2, 2016, 1709 of 1803). Service Fabric versie 6.4.654.9590 en hoger ondersteunt ook server 2019 en 1809.
* [.NET framework 4.5.1 of hoger](https://www.microsoft.com/download/details.aspx?id=40773), volledige installatie
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/install/installing-windows-powershell)
* De [RemoteRegistry service](https://technet.microsoft.com/library/cc754820) moet worden uitgevoerd op alle computers
* Service Fabric installatie station moet een NTFS-bestands systeem zijn

De Clusterbeheerder implementeren en configureren van het cluster moet hebben [administrator-bevoegdheden](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) op elk van de machines. U kunt Service Fabric niet installeren op een domeincontroller.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Het zelfstandige pakket met Service Fabric voor Windows Server downloaden
[Link - pakket met Service Fabric zelfstandige - Windows-Server downloaden](https://go.microsoft.com/fwlink/?LinkId=730690) en pak het pakket, met een implementatiecomputer die geen deel uitmaakt van het cluster of op een van de machines die een onderdeel van uw cluster zal zijn.

## <a name="modify-cluster-configuration"></a>Configuratie van het cluster wijzigen
Voor het maken van een zelfstandige cluster die u moet maken van een zelfstandige cluster ClusterConfig.json configuratiebestand, waarin wordt beschreven van de specificatie van het cluster. U kunt het configuratiebestand op de sjablonen die zijn gevonden op basis van de onderstaande koppeling. <br>
[Configuraties van clusters op zelfstandige](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Zie voor meer informatie over de secties in dit bestand, [configuratie-instellingen voor zelfstandige Windows cluster](service-fabric-cluster-manifest.md).

Open een van de ClusterConfig.json-bestanden van het pakket dat u hebt gedownload en wijzig de volgende instellingen:

| **Configuratie-instelling** | **Beschrijving** |
| --- | --- |
| **NodeTypes** |Knooppunttypen kunnen u uw clusterknooppunten verdelen in verschillende groepen. Een cluster moet ten minste één NodeType hebben. Alle knooppunten in een groep hebben de volgende algemene kenmerken: <br> **Naam** -dit is de naam van het knooppunt. <br>**Eindpuntpoorten** - deze heten verschillende eindpunten (poorten) die gekoppeld aan dit knooppunttype zijn. U kunt een ander poortnummer die u wenst, zolang ze niet in strijd is met andere onderdelen in deze manifest en zijn niet al in gebruik door een andere toepassing die wordt uitgevoerd op de virtuele machine/machine gebruiken. <br> **Plaatsingseigenschappen** -deze beschrijving van eigenschappen voor dit knooppunttype die u als plaatsingsbeperkingen voor de systeemservices of uw services gebruikt. Deze eigenschappen worden de gebruiker gedefinieerde sleutel/waarde-paren die extra metagegevens voor een bepaald knooppunt bieden. Voorbeelden van eigenschappen van het knooppunt zijn of het knooppunt heeft een vaste schijf of grafische kaart, het aantal aandrijfassen in de harde schijf, kernen en andere fysieke eigenschappen. <br> **Capaciteiten** -knooppuntcapaciteit definieert de naam en het bedrag van een bepaalde resource dat een bepaald knooppunt beschikbaar voor gebruik is. Een knooppunt kan bijvoorbeeld dat er capaciteit voor een metrische waarde 'MemoryInMb' genoemd en 2048 MB beschikbare schijfruimte heeft standaard definiëren. Deze capaciteiten worden tijdens runtime gebruikt om ervoor te zorgen dat de services waarvoor bepaalde hoeveelheden resources zijn geplaatst op de knooppunten die deze resources zijn beschikbaar in de hoeveelheden die zijn vereist.<br>**IsPrimary** - hebt u meer dan één NodeType gedefinieerd ervoor te zorgen dat slechts één is ingesteld op primaire met de waarde *waar*, dit is waar de systeemservices uitvoeren. Alle andere typen moeten worden ingesteld op de waarde *false* |
| **Knooppunten** |Dit zijn de details voor elk van de knooppunten die deel van het cluster (knooppunttype, naam van het knooppunt IP-adres, foutdomein en upgradedomein van het knooppunt uitmaken). De machines die het cluster op hoeft te worden hier vermeld met de IP-adressen moeten worden gemaakt. <br> Als u hetzelfde IP-adres voor alle knooppunten, klikt u vervolgens een in-één cluster is gemaakt, die u gebruiken kunt voor testdoeleinden. Gebruik geen in-één clusters voor het implementeren van werkbelastingen voor productie. |

Nadat de configuratie van het cluster alle instellingen die zijn geconfigureerd voor de omgeving heeft, kan deze kan worden getest op basis van de clusteromgeving (stap 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Omgeving instellen

Wanneer een Clusterbeheerder een zelfstandige Service Fabric-cluster configureert, wordt de omgeving moet worden ingesteld met de volgende criteria: <br>
1. De gebruiker die het cluster te maken hebt beheerdersniveau beveiligingsrechten op alle machines die worden vermeld als knooppunten in het configuratiebestand van het cluster.
2. Computer van waaruit het cluster is gemaakt, evenals op elke computer met het knooppunt cluster moet het volgende doen:
   * Hebt u Service Fabric-SDK geïnstalleerd
   * Hebt u Service Fabric-runtime is verwijderd
   * Hebben de Windows Firewall-service (mpsvc) ingeschakeld
   * Hebben de Remote Registry-Service (remote registry) ingeschakeld
   * Bestand die delen (SMB) ingeschakeld
   * Vereiste poorten geopend zijn op basis van poorten voor cluster-configuratie
   * Vereiste poorten zijn geopend voor SMB in Windows en de Remote Registry-service: 135, 137, 138, 139 en 445
   * Netwerkverbinding hebben met elkaar
3. Geen van de cluster-knooppunt machines moet een domeincontroller.
4. Als het cluster worden geïmplementeerd, een beveiligd cluster is, controleert u de benodigde beveiligingsreferenties vereisten zijn in plaats en correct zijn geconfigureerd op basis van de configuratie.
5. Als de cluster-machines niet toegankelijk is met internet zijn, moet u het volgende instellen in de configuratie van het cluster:
   * Telemetrie uitschakelen: onder *eigenschappen* ingesteld *"enableTelemetry": false*
   * Automatische Fabric versie gedownload en dat de huidige clusterversie einde van ondersteuning wordt binnenkort meldingen uitschakelen: onder *eigenschappen* ingesteld *"fabricClusterAutoupgradeEnabled": false*
   * U kunt ook als internet toegang tot het netwerk beperkt tot acceptatielijstdomeinen is, de domeinen die hieronder zijn vereist voor automatisch bijwerken: go.microsoft.com download.microsoft.com

6. Juiste antivirus uitsluitingen voor Service Fabric instellen:

| **Antivirus uitgesloten mappen** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (van de configuratie van het cluster) |
| FabricLogRoot (van de configuratie van het cluster) |

| **Antivirus uitgesloten processen** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Valideren van de omgeving met behulp van de TestConfiguration-script
Het script TestConfiguration.ps1 vindt u in het zelfstandige pakket. Het wordt gebruikt als een Best Practices Analyzer voor enkele van de bovenstaande criteria valideren en moet worden gebruikt als een controle om te valideren of een cluster kan worden geïmplementeerd op een bepaalde omgeving. Als er een fout is, raadpleegt u de lijst onder [omgeving](service-fabric-cluster-standalone-deployment-preparation.md) voor het oplossen van problemen.

Met dit script kan worden uitgevoerd op elke computer die de beheerderstoegang heeft tot alle machines die worden weergegeven als knooppunten in het configuratiebestand van het cluster. De machine die met dit script wordt uitgevoerd op geen deel uitmaken van het cluster.

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

Deze configuratie testen-module heeft de beveiligingsconfiguratie momenteel niet valideren zodat dit onafhankelijk van elkaar worden uitgevoerd.

> [!NOTE]
> Voortdurend maken we verbeteringen voor het maken van deze module krachtiger, dus als er een aanvraag defect of ontbrekend waarvan u denkt dat is niet op dit moment worden opgepikt door de TestConfiguration, laat het ons weten via onze [ondersteunen kanalen](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
>
>

## <a name="next-steps"></a>Volgende stappen
* [Een zelfstandige cluster uitgevoerd op Windows Server maken](service-fabric-cluster-creation-for-windows-server.md)
