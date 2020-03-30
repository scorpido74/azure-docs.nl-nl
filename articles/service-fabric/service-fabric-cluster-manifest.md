---
title: Uw zelfstandige Azure Service Fabric-cluster configureren
description: Meer informatie over het configureren van uw zelfstandige of on-premises Azure Service Fabric-cluster.
author: dkkapur
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: 0f9b625dfbe9c39bea7771dcc5fd58805ce19811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458372"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Configuratie-instellingen voor een zelfstandig Windows-cluster
In dit artikel worden de configuratie-instellingen beschreven van een zelfstandig Azure Service Fabric-cluster dat kan worden ingesteld in het *clusterClusterConfig.json.* U gebruikt dit bestand om informatie op te geven over de knooppunten van het cluster, beveiligingsconfiguraties en de netwerktopologie in termen van fout- en upgradedomeinen.  Nadat u configuratie-instellingen hebt gewijzigd of toegevoegd, u [een zelfstandig cluster maken](service-fabric-cluster-creation-for-windows-server.md) of de configuratie van een zelfstandig cluster [upgraden.](service-fabric-cluster-config-upgrade-windows-server.md)

Wanneer u [het standalone Service Fabric-pakket downloadt,](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)worden clusterconfig.json-voorbeelden ook meegeleverd. De voorbeelden met "DevCluster" in hun naam maken een cluster met alle drie de knooppunten op dezelfde machine, met behulp van logische knooppunten. Van deze knooppunten moet ten minste één als primair knooppunt worden gemarkeerd. Dit type cluster is handig voor ontwikkel- of testomgevingen. Het wordt niet ondersteund als een productiecluster. De voorbeelden met "MultiMachine" in hun naam helpen bij het maken van clusters van productiekwaliteit, waarbij elk knooppunt op een afzonderlijke machine wordt gebruikt. Het aantal primaire knooppunten voor deze clusters is gebaseerd op het [betrouwbaarheidsniveau](#reliability)van het cluster. In versie 5.7, API Versie 05-2017, hebben we de eigenschap op betrouwbaarheidsniveau verwijderd. In plaats daarvan berekent onze code het meest geoptimaliseerde betrouwbaarheidsniveau voor uw cluster. Probeer geen waarde voor deze eigenschap in te stellen in versie 5.7.

* ClusterConfig.Unsecure.DevCluster.json en ClusterConfig.Unsecure.MultiMachine.json laten zien hoe u respectievelijk een onbeveiligd test- of productiecluster maken.

* ClusterConfig.Windows.DevCluster.json en ClusterConfig.Windows.MultiMachine.json laten zien hoe u test- of productieclusters maakt die zijn beveiligd met [Windows-beveiliging](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json en ClusterConfig.X509.MultiMachine.json laten zien hoe u test- of productieclusters maken die zijn beveiligd met behulp van [x509-certificaatgebaseerde beveiliging](service-fabric-windows-cluster-x509-security.md).

Laten we nu eens kijken naar de verschillende secties van een ClusterConfig.json bestand.

## <a name="general-cluster-configurations"></a>Algemene clusterconfiguraties
Algemene clusterconfiguraties hebben betrekking op de brede clusterspecifieke configuraties, zoals weergegeven in het volgende JSON-fragment:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

U elke vriendelijke naam aan uw Service Fabric-cluster geven door deze toe te wijsaan de naamvariabele. De clusterConfigurationVersion is het versienummer van uw cluster. Verhoog het elke keer dat u uw Service Fabric-cluster upgradet. Laat apiVersion instellen op de standaardwaarde.

## <a name="nodes-on-the-cluster"></a>Knooppunten op het cluster
U de knooppunten op uw cluster servicestructuur configureren met behulp van de sectie knooppunten, zoals in het volgende fragment wordt weergegeven:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Een cluster van servicefabric moet ten minste drie knooppunten bevatten. U meer knooppunten aan deze sectie toevoegen op basis van uw installatie. In de volgende tabel worden de configuratie-instellingen voor elk knooppunt uitgelegd:

| **Knooppuntconfiguratie** | **Beschrijving** |
| --- | --- |
| nodeNaam |U elke vriendelijke naam aan het knooppunt geven. |
| iPAddress |Ontdek het IP-adres van uw knooppunt door `ipconfig`een opdrachtvenster te openen en te typen. Noteer het IPV4-adres en wijs het toe aan de variabele iPAddress. |
| nodeTypeRef |Aan elk knooppunt kan een ander knooppunttype worden toegewezen. De [typen knooppunts](#node-types) worden gedefinieerd in de volgende sectie. |
| foutDomein |Foutdomeinen stellen clusterbeheerders in staat om de fysieke knooppunten te definiëren die mogelijk tegelijkertijd mislukken als gevolg van gedeelde fysieke afhankelijkheden. |
| upgradeDomein |Upgradedomeinen beschrijven sets knooppunten die op ongeveer hetzelfde moment zijn afgesloten voor Service Fabric-upgrades. U kiezen welke knooppunten u wilt toewijzen aan welke upgradedomeinen, omdat deze niet worden beperkt door fysieke vereisten. |

## <a name="cluster-properties"></a>Clustereigenschappen
De sectie Eigenschappen in het clusterConfig.json wordt gebruikt om het cluster te configureren zoals weergegeven:

### <a name="reliability"></a>Betrouwbaarheid
Het concept betrouwbaarheidNiveau definieert het aantal replica's of exemplaren van de Service Fabric-systeemservices die kunnen worden uitgevoerd op de primaire knooppunten van het cluster. Het bepaalt de betrouwbaarheid van deze diensten en dus het cluster. De waarde wordt berekend door het systeem op clustercreatie- en upgradetijd.

### <a name="diagnostics"></a>Diagnostiek
In de sectie diagnosticsStore u parameters configureren om diagnose- en probleemoplossingsknooppunt- of clusterfouten in te schakelen, zoals in het volgende fragment wordt weergegeven: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

De metagegevens zijn een beschrijving van uw clusterdiagnostiek en kunnen worden ingesteld op basis van uw installatie. Deze variabelen helpen bij het verzamelen van ETW trace logs en crash dumps evenals prestatietellers. Zie [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) en [ETW tracing](https://msdn.microsoft.com/library/ms751538.aspx)voor meer informatie over ETW traceerlogs. Alle [logboeken, inclusief crashdumps](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) en [prestatiemeteritems,](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)kunnen worden gericht op de map connectionString op uw machine. U AzureStorage ook gebruiken om diagnostische gegevens op te slaan. Zie het volgende voorbeeld:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Beveiliging
De beveiligingssectie is noodzakelijk voor een beveiligd standalone Service Fabric-cluster. In het volgende fragment ziet u een deel van deze sectie:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

De metadata is een beschrijving van uw beveiligde cluster en kan worden ingesteld op basis van uw setup. Het ClusterCredentialType en ServerCredentialType bepalen het type beveiliging dat het cluster en de knooppunten implementeren. Ze kunnen worden ingesteld op *X509* voor een op certificaten gebaseerde beveiliging of *Windows* voor active directory-gebaseerde beveiliging. De rest van de beveiligingssectie is gebaseerd op het type beveiliging. Zie Beveiliging op basis van certificaten in een zelfstandig cluster of [Windows-beveiliging](service-fabric-windows-cluster-x509-security.md) [in een zelfstandig cluster in een zelfstandig cluster](service-fabric-windows-cluster-windows-security.md)voor informatie over het invullen van de rest van de beveiligingssectie.

### <a name="node-types"></a>Knooppunttypen
In de sectie nodetypes wordt het type knooppunten beschreven dat uw cluster heeft. Voor een cluster moet ten minste één knooppunttype worden opgegeven, zoals in het volgende fragment wordt weergegeven: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

De naam is de vriendelijke naam voor dit specifieke knooppunttype. Als u een knooppunt van dit knooppunttype wilt maken, wijst u de vriendelijke naam toe aan de variabele nodeTypeRef voor dat knooppunt, zoals [eerder vermeld.](#nodes-on-the-cluster) Definieer voor elk knooppunttype de verbindingseindpunten die worden gebruikt. U een poortnummer voor deze verbindingseindpunten kiezen, zolang deze niet in strijd zijn met andere eindpunten in dit cluster. In een cluster met meerdere knooppunten zijn er een of meer primaire knooppunten (dat wil zeggen dat isPrimair is ingesteld op *true),* afhankelijk van het [betrouwbaarheidsniveau.](#reliability) Zie Overwegingen voor [capaciteitsplanning van het cluster servicestructuur](service-fabric-cluster-capacity.md) voor informatie over knooppunttypen en betrouwbaarheidsniveau voor meer informatie over primaire en niet-primaire knooppunttypen. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Eindpunten die worden gebruikt om de knooppunttypen te configureren
* clientConnectionEndpointPort is de poort die door de client wordt gebruikt om verbinding te maken met het cluster wanneer client-API's worden gebruikt. 
* clusterConnectionEndpointPort is de poort waar de knooppunten met elkaar communiceren.
* leaseDriverEndpointPort is de poort die wordt gebruikt door de clusterleasedriver om uit te zoeken of de knooppunten nog actief zijn. 
* serviceConnectionEndpointPort is de poort die wordt gebruikt door de toepassingen en services die op een knooppunt worden geïmplementeerd om met de Service Fabric-client op dat specifieke knooppunt te communiceren.
* httpGatewayEndpointPort is de poort die wordt gebruikt door Service Fabric Explorer om verbinding te maken met het cluster.
* efemerePorts overschrijven de [dynamische poorten die worden gebruikt door het BE](https://support.microsoft.com/kb/929851). Service Fabric gebruikt een deel van deze poorten als toepassingspoorten en de overige zijn beschikbaar voor het besturingssysteem. Het brengt dit bereik ook in kaart met het bestaande bereik in het besturingssysteem, dus voor alle doeleinden u de bereiken gebruiken die in de voorbeeld-JSON-bestanden worden gegeven. Zorg ervoor dat het verschil tussen de start- en de eindpoorten ten minste 255 is. U conflicten tegenkomen als dit verschil te laag is, omdat dit bereik wordt gedeeld met het besturingssysteem. Voer het geconfigureerde dynamische poortbereik uit om het geconfigureerde dynamische poortbereik te bekijken. `netsh int ipv4 show dynamicport tcp`
* applicationPorts zijn de poorten die worden gebruikt door de Service Fabric-toepassingen. Het bereik van de toepassingspoort moet groot genoeg zijn om de eindpuntvereiste van uw toepassingen te dekken. Dit bereik moet exclusief zijn van het dynamische poortbereik op de machine, dat wil zeggen het kortstondigePorts-bereik zoals ingesteld in de configuratie. Service Fabric gebruikt deze poorten wanneer er nieuwe poorten nodig zijn en zorgt voor het openen van de firewall voor deze poorten. 
* reverseProxyEndpointPort is een optioneel eindpunt voor reverse proxy. Zie [Service Fabric reverse proxy voor](service-fabric-reverseproxy.md)meer informatie. 

### <a name="log-settings"></a>Logboekinstellingen
In de sectie fabricSettings u de rootmappen instellen voor de gegevens en logboeken van de Service Fabric. U deze mappen alleen aanpassen tijdens het maken van het eerste cluster. Zie het volgende voorbeeldfragment van deze sectie:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

We raden u aan een niet-OS-station te gebruiken als de FabricDataRoot en FabricLogRoot. Het biedt meer betrouwbaarheid bij het vermijden van situaties wanneer het besturingssysteem niet meer reageert. Als u alleen de gegevenswortel aanpast, wordt de logroot één niveau onder de gegevenswortel geplaatst.

### <a name="stateful-reliable-services-settings"></a>Stateful Reliable Services-instellingen
In de sectie KtlLogger u de algemene configuratie-instellingen voor betrouwbare services instellen. Zie [Statusful Reliable Services configureren](service-fabric-reliable-services-configuration.md)voor meer informatie over deze instellingen. In het volgende voorbeeld ziet u hoe u het gedeelde transactielogboek wijzigen dat wordt gemaakt om betrouwbare verzamelingen voor stateful services te backleren:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Invoegfuncties
Als u add-on-functies wilt configureren, configureert u de apiVersie als 04-2017 of hoger en configureert u de addonFeatures zoals hier wordt weergegeven:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Alle beschikbare add-on functies zijn te zien in de [Service Fabric REST API Reference.](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-addonfeatures)

### <a name="container-support"></a>Ondersteuning voor containers
Om containerondersteuning voor zowel Windows Server-containers als Hyper-V-containers voor zelfstandige clusters in te schakelen, moet de dnsservice-invoegfunctie zijn ingeschakeld.

## <a name="next-steps"></a>Volgende stappen
Nadat u een volledig *ClusterConfig.json-bestand* hebt geconfigureerd op basis van uw zelfstandige clusterinstelling, u uw cluster implementeren. Volg de stappen in [Een zelfstandig cluster van servicefabric maken](service-fabric-cluster-creation-for-windows-server.md). 

Als u een stand-alone cluster hebt geïmplementeerd, u ook [de configuratie van een zelfstandig cluster upgraden.](service-fabric-cluster-config-upgrade-windows-server.md) 

Meer informatie over het [visualiseren van uw cluster met Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

