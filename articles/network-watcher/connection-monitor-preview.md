---
title: Verbindings monitor (preview-versie) | Microsoft Docs
description: Meer informatie over het gebruik van verbindings monitor (preview) voor het bewaken van netwerk communicatie in een gedistribueerde omgeving.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 52d33e7292ebe7b27eede2b89aa605780f826392
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737611"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Bewaking van netwerk connectiviteit met verbindings monitor (preview-versie)

Verbindings monitor (preview) biedt een geïntegreerde end-to-end-verbindings bewaking in azure Network Watcher. De functie verbindings monitor (preview) ondersteunt hybride en Azure-Cloud implementaties. Network Watcher biedt hulpprogram ma's voor het bewaken, diagnosticeren en weer geven van connectiviteits gegevens voor uw Azure-implementaties.

Hier volgen enkele gebruiks voorbeelden voor verbindings monitor (preview-versie):

- De front-end-webserver-VM communiceert met een database server-VM in een toepassing met meerdere lagen. U de netwerk verbinding tussen de twee virtuele machines wilt controleren.
- U wilt virtuele machines in de regio VS-Oost om Vm's te pingen in de regio VS-Midden en u wilt verschillende netwerk latentiesen in meerdere regio's te vergelijken.
- U hebt meerdere on-premises Office-sites in Seattle, Washington en Ashburn, Virginia. Uw Office-sites maken verbinding met Office 365-Url's. Vergelijk de latenties tussen Seattle en Ashburn voor uw gebruikers van Office 365-Url's.
- Uw hybride toepassing heeft verbinding nodig met een Azure Storage-eind punt. Uw on-premises site en uw Azure-toepassing maken verbinding met hetzelfde Azure Storage-eind punt. U wilt de latenties van de on-premises site vergelijken met de latenties van de Azure-toepassing.
- U wilt de connectiviteit controleren tussen uw on-premises instellingen en de virtuele Azure-machines die uw Cloud toepassing hosten.

In de preview-fase combineert verbindings monitor het beste van twee functies: de Network Watcher [verbindingscontrole](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) functie en de [service connectiviteits controle](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) van Netwerkprestatiemeter (NPM).

Hier volgen enkele voor delen van de verbindings monitor (preview-versie):

* Geïntegreerde, intuïtieve ervaring voor Azure en hybride bewakings behoeften
* Bewaking van meerdere regio's, connectiviteit tussen werk ruimten
* Hogere probing-frequenties en betere zicht baarheid van netwerk prestaties
* Snellere waarschuwingen voor uw hybride implementaties
* Ondersteuning voor connectiviteits controles die zijn gebaseerd op HTTP, TCP en ICMP 
* Metrische gegevens en Log Analytics ondersteuning voor instellingen voor Azure en niet-Azure-tests

![Diagram waarin wordt getoond hoe verbindings monitor communiceert met Azure-Vm's, niet-Azure-hosts,-eind punten en opslag locaties voor gegevens](./media/connection-monitor-2-preview/hero-graphic.png)

Voer de volgende stappen uit om verbindings monitor (preview) te gebruiken voor bewaking: 

1. Bewakings agents installeren.
1. Schakel Network Watcher in voor uw abonnement.
1. Maak een verbindings monitor.
1. Gegevens analyse en waarschuwingen instellen.
1. Problemen in uw netwerk diagnosticeren.

In de volgende secties vindt u meer informatie over deze stappen.

## <a name="install-monitoring-agents"></a>Bewakings agents installeren

Verbindings monitor is afhankelijk van licht gewicht uitvoer bare bestanden om connectiviteits controles uit te voeren.  Het ondersteunt connectiviteits controles vanuit Azure-omgevingen en on-premises omgevingen. Het uitvoer bare bestand dat u gebruikt, is afhankelijk van of uw virtuele machine wordt gehost op Azure of on-premises.

### <a name="agents-for-azure-virtual-machines"></a>Agents voor Azure virtual machines

Als u wilt dat de verbindings monitor uw Azure-Vm's als bewakings bronnen herkent, installeert u de Network Watcher-extensie voor de virtuele machine van de agent. Deze extensie wordt ook wel de *Network Watcher extensie*genoemd. Voor virtuele Azure-machines is de extensie vereist om end-to-end controle en andere geavanceerde functionaliteit te activeren. 

U kunt de Network Watcher-extensie installeren wanneer u [een VM maakt](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). U kunt ook de Network Watcher-extensie voor [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) en [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)afzonderlijk installeren, configureren en problemen oplossen.

Regels voor een netwerk beveiligings groep (NSG) of firewall kunnen de communicatie tussen de bron en de bestemming blok keren. De verbindings monitor detecteert dit probleem en geeft deze weer als een diagnostisch bericht in de topologie. Als u verbindings bewaking wilt inschakelen, moet u ervoor zorgen dat de NSG-en firewall regels pakketten toestaan via TCP of ICMP tussen de bron en de bestemming.

### <a name="agents-for-on-premises-machines"></a>Agents voor on-premises machines

Als u wilt dat verbindings monitor uw on-premises computers als bronnen voor bewaking herkent, installeert u de Log Analytics-agent op de computers. Schakel vervolgens de Netwerkprestatiemeter-oplossing in. Deze agents zijn gekoppeld aan Log Analytics werk ruimten, dus u moet de werk ruimte-ID en de primaire sleutel instellen voordat de agents kunnen worden gecontroleerd.

Zie [Azure monitor virtuele-machine-extensie voor Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)om de log Analytics agent voor Windows-computers te installeren.

Als het pad firewalls of virtuele netwerk apparaten (Nva's) bevat, moet u ervoor zorgen dat de bestemming bereikbaar is.

## <a name="enable-network-watcher-on-your-subscription"></a>Network Watcher voor uw abonnement inschakelen

Alle abonnementen met een virtueel netwerk zijn ingeschakeld met Network Watcher. Wanneer u een virtueel netwerk in uw abonnement maakt, wordt Network Watcher automatisch ingeschakeld in de regio en het abonnement van het virtuele netwerk. Deze automatische inschakeling heeft geen invloed op uw resources of er worden kosten in rekening gebracht. Zorg ervoor dat Network Watcher niet expliciet is uitgeschakeld voor uw abonnement. 

Zie [enable Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)(Engelstalig) voor meer informatie.

## <a name="create-a-connection-monitor"></a>Een verbindingsmonitor maken 

Met verbindings monitor wordt de communicatie met regel matige intervallen gecontroleerd. U wordt geïnformeerd over wijzigingen in de bereik baarheid en latentie. U kunt ook de huidige en historische netwerk topologie tussen bron agenten en doel eindpunten controleren.

Bronnen kunnen Azure-Vm's of on-premises machines zijn met een geïnstalleerde bewakings agent. Doel eindpunten kunnen Office 365 Url's, Dynamics 365 Url's, aangepaste Url's, Azure VM-resource-Id's, IPv4, IPv6, FQDN of een domein naam zijn.

### <a name="access-connection-monitor-preview"></a>Toegang tot verbindings monitor (preview-versie)

1. Ga op de start pagina van Azure Portal naar **Network Watcher**.
1. Selecteer aan de linkerkant in het gedeelte **controle** de optie **verbindings monitor (preview-versie)**.
1. U ziet alle verbindings monitors die zijn gemaakt in de verbindings monitor (preview-versie). Ga naar het tabblad **verbindings controle** om de verbindings monitors weer te geven die zijn gemaakt in de klassieke ervaring van de verbindings monitor.

    ![Scherm afbeelding met verbindings monitors die zijn gemaakt in de verbindings monitor (preview)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Een verbindingsmonitor maken

Bij verbindings monitors die u in verbindings monitor (preview) maakt, kunt u zowel on-premises machines als Azure-Vm's toevoegen als bronnen. Deze verbindings monitors kunnen ook de connectiviteit met eind punten bewaken. De eind punten kunnen zich op Azure of een andere URL of een ander IP-adres bevindt.

Verbindings monitor (preview) omvat de volgende entiteiten:

* **Bron van de verbindings monitor** : een regio-specifieke Azure-resource. Alle volgende entiteiten zijn eigenschappen van een verbindingscontrole resource.
* **Eind punt** – een bron of doel die deel uitmaakt van connectiviteits controles. Voor beelden van eind punten zijn Azure-Vm's, on-premises agents, Url's en Ip's.
* **Test configuratie** : een protocol-specifieke configuratie voor een test. Op basis van het protocol dat u hebt gekozen, kunt u de poort, drempel waarden, test frequentie en andere para meters definiëren.
* **Test groep** : de groep die bron-eind punten, bestemmings eindpunten en test configuraties bevat. Een verbindings monitor kan meer dan één test groep bevatten.
* **Testen** : de combi natie van een bron eindpunt, bestemmings eindpunt en test configuratie. Een test is het meest gedetailleerde niveau waarmee bewakings gegevens beschikbaar zijn. De bewakings gegevens omvatten het percentage controles dat is mislukt en de round-trip tijd (RTT).

 ![Diagram van een verbindings monitor, waarbij de relatie tussen test groepen en tests wordt gedefinieerd](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Een verbindings monitor maken op basis van de Azure Portal

Voer de volgende stappen uit om een verbindings monitor te maken op basis van de Azure Portal:

1. Selecteer in de linkerbovenhoek van het dash board **verbindings monitor (preview)** de optie **maken**.
1. Voer op het tabblad **basis beginselen** informatie in voor uw verbindings monitor:
   * **Naam van de verbindings monitor** : Voeg de naam van de verbindings monitor toe. Gebruik de standaard naamgevings regels voor Azure-resources.
   * **Abonnement** : Kies een abonnement voor uw verbindings monitor.
   * **Regio** : Kies een regio voor de verbindings monitor. U kunt alleen de bron-Vm's selecteren die in deze regio worden gemaakt.
   * **Werkruimte configuratie** : uw werk ruimte bevat uw bewakings gegevens. U kunt een aangepaste werk ruimte of de standaardwerk ruimte gebruiken. 
       * Als u de standaardwerk ruimte wilt gebruiken, schakelt u het selectie vakje in. 
       * Als u een aangepaste werk ruimte wilt kiezen, schakelt u het selectie vakje uit. Kies vervolgens het abonnement en de regio voor uw aangepaste werk ruimte. 
1. Selecteer onder aan het tabblad **volgende: test groepen**.

   ![Scherm opname van het tabblad basis beginselen in verbindings monitor](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Selecteer op het tabblad **test groepen** de optie **+ test groep**. Zie [test groepen maken in verbindings monitor](#create-test-groups-in-a-connection-monitor)om uw test groepen in te stellen. 
1. Selecteer onder aan het tabblad **volgende: controleren + maken** om uw verbindings monitor te controleren.

   ![Scherm afbeelding van het tabblad test groepen en het deel venster waar u details van test groep toevoegt](./media/connection-monitor-2-preview/create-tg.png)

1. Controleer op het tabblad **controleren en maken** de basis gegevens en test groepen voordat u de verbindings monitor maakt. Als u de verbindings monitor wilt bewerken:
   * Als u de basis gegevens wilt bewerken, selecteert u het potlood pictogram.
   * Als u een test groep wilt bewerken, selecteert u deze.

   > [!NOTE] 
   > Op het tabblad **controleren en maken** worden de kosten per maand weer gegeven tijdens de preview-fase van de verbindings monitor. Op dit moment worden er geen kosten in rekening gebracht voor de kolom **huidige kosten** . Als verbindings monitor algemeen beschikbaar wordt, wordt in deze kolom een maandelijks bedrag weer gegeven. 
   > 
   > Zelfs in de preview-fase van de verbindings monitor zijn de Log Analytics opname kosten van toepassing.

1. Wanneer u klaar bent om de verbindings monitor te maken, selecteert u onder aan het tabblad **controleren en maken** de optie **maken**.

   ![Scherm opname van de verbindings monitor, met het tabblad controleren + maken](./media/connection-monitor-2-preview/review-create-cm.png)

Met verbindings monitor (preview) wordt de bron van de verbindings monitor gemaakt op de achtergrond.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Een verbindings monitor maken met behulp van ARMClient

Gebruik de volgende code om een verbindings monitor te maken met behulp van ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Hier volgt de implementatie opdracht:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Test groepen maken in een verbindings monitor

Elke test groep in een verbindings monitor bevat bronnen en bestemmingen die worden getest op netwerk parameters. Ze zijn getest op het percentage controles dat mislukt en de RTT over test configuraties.

Als u in de Azure Portal een test groep wilt maken in een verbindings monitor, geeft u waarden op voor de volgende velden:

* **Test groep uitschakelen** : u kunt dit veld selecteren om bewaking uit te scha kelen voor alle bronnen en doelen die door de test groep worden opgegeven. Deze selectie is standaard uitgeschakeld.
* **Naam** : Geef uw test groep een naam.
* **Bronnen** : u kunt zowel virtuele Azure-machines als lokale computers opgeven als bronnen als er agents op zijn geïnstalleerd. Zie [bewakings agenten installeren](#install-monitoring-agents)voor informatie over het installeren van een agent voor uw bron.
   * Als u Azure-agents wilt kiezen, selecteert u het tabblad **Azure agents** . Hier ziet u alleen de virtuele machines die zijn gebonden aan de regio die u hebt opgegeven tijdens het maken van de verbindings monitor. Standaard worden Vm's gegroepeerd in het abonnement waarvan ze deel uitmaken. Deze groepen worden samengevouwen. 
   
       U kunt inzoomen op het abonnements niveau tot andere niveaus in de hiërarchie:

      **Abonnement**  >  **Resource groepen**  >  **VNETs**  >  **Subnetten**  >  **Vm's met agents**

      U kunt ook de waarde van het veld **groeperen op** wijzigen om de structuur te starten vanaf een ander niveau. Als u bijvoorbeeld per virtueel netwerk groepeert, ziet u de virtuele machines die agents hebben in de hiërarchie **VNETs**  >  **subnetten**  >  **vm's met agents**.

      ![Scherm opname van de verbindings monitor, met het paneel bronnen toevoegen en het tabblad Azure agents](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Als u on-premises agents wilt kiezen, selecteert u het tabblad **niet-Azure-agents** . Standaard worden agents gegroepeerd in werk ruimten per regio. Voor al deze werk ruimten is de Netwerkprestatiemeter oplossing geconfigureerd. 
   
       Als u Netwerkprestatiemeter aan uw werk ruimte wilt toevoegen, haalt u deze op via [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Zie [oplossingen controleren in azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)voor informatie over het toevoegen van Netwerkprestatiemeter. 
   
       In de weer gave **verbindings monitor maken** op het tabblad **basis beginselen** is de standaard regio geselecteerd. Als u de regio wijzigt, kunt u agents kiezen uit werk ruimten in de nieuwe regio. U kunt ook de waarde van het veld **groeperen op** wijzigen in gegroepeerd op subnetten.

      ![Scherm opname van de verbindings monitor, met daarin het paneel bronnen toevoegen en het tabblad niet-Azure-agents](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Als u de door u geselecteerde Azure-en niet-Azure-agents wilt bekijken, gaat u naar het tabblad **controleren** .

      ![Scherm opname van de verbindings monitor, met het paneel bronnen toevoegen en het tabblad controleren](./media/connection-monitor-2-preview/review-sources.png)

   * Wanneer u klaar bent met het instellen van bronnen, selecteert u aan de onderkant van het paneel **bronnen toevoegen** de optie **gereed**.

* **Doelen** : u kunt de verbinding met virtuele machines van Azure of een eind punt (een openbaar IP-adres, URL of FQDN) bewaken door ze op te geven als bestemming. In één test groep kunt u Azure-Vm's, Office 365 Url's, Dynamics 365-Url's en aangepaste eind punten toevoegen.

    * Als u virtuele Azure-machines als doelen wilt kiezen, selecteert u het tabblad **Azure vm's** . Standaard worden de Azure-Vm's gegroepeerd in een abonnements hiërarchie die zich in dezelfde regio bevindt die u hebt geselecteerd in de weer gave **verbindings controle maken** op het tabblad **basis** . U kunt de regio wijzigen en Azure-Vm's kiezen uit de zojuist geselecteerde regio. Vervolgens kunt u inzoomen op abonnements niveau naar andere niveaus in de hiërarchie, zoals het niveau van Azure-agents.

       ![Scherm opname van het deel venster bestemmingen toevoegen met het tabblad Azure-Vm's](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Scherm afbeelding van het deel venster bestemmingen toevoegen met het abonnements niveau](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Als u eind punten wilt kiezen als bestemmingen, selecteert u het tabblad **eind punten** . De lijst met eind punten omvat Office 365-test-Url's en Dynamics 365-test-Url's, gegroepeerd op naam. Naast deze eind punten kunt u een eind punt kiezen dat is gemaakt in andere test groepen in dezelfde verbindings monitor. 
    
        Als u een nieuw eind punt wilt toevoegen, selecteert u in de rechter bovenhoek **+ eind punten**. Geef vervolgens de naam en URL, het IP-adres of de FQDN van het eind punt op.

       ![Scherm opname van het toevoegen van eind punten als bestemming in de verbindings monitor](./media/connection-monitor-2-preview/add-endpoints.png)

    * Als u de Azure-Vm's en-eind punten wilt bekijken die u hebt gekozen, selecteert u het tabblad **controleren** .
    * Wanneer u klaar bent met het kiezen van bestemmingen, selecteert u **gereed**.

* **Test configuraties** : u kunt test configuraties koppelen aan een test groep. De Azure Portal slechts één test configuratie per test groep toestaan, maar u kunt ARMClient gebruiken om meer toe te voegen.

    * **Naam** : Geef de configuratie van de test op.
    * **Protocol** : Kies TCP, ICMP of http. Als u HTTP-naar-HTTPS wilt wijzigen, selecteert u **http** als protocol en selecteert u **443** als poort.
        * **Test configuratie voor het netwerk maken** : dit selectie vakje wordt alleen weer gegeven als u **http** in het veld **protocol** selecteert. Schakel dit selectie vakje in om een andere test configuratie te maken die gebruikmaakt van dezelfde bronnen en bestemmingen die u elders hebt opgegeven in uw configuratie. De zojuist gemaakte test configuratie heet `<the name of your test configuration>_networkTestConfig` .
        * **Traceroute uitschakelen** : dit veld is van toepassing op test groepen waarvan het protocol TCP of ICMP is. Schakel dit selectie vakje in om te voor komen dat bronnen worden gedetecteerd voor de detectie van de topologie en de hop-by-Hop-RTT.
    * **Doel poort** – u kunt dit veld aanpassen met een doel poort van uw keuze.
    * **Test frequentie** : gebruik dit veld om te kiezen hoe vaak bronnen worden gepingd op het door u opgegeven protocol en poort. U kunt kiezen uit 30 seconden, 1 minuut, 5 minuten, 15 minuten of 30 minuten. Bronnen testen de connectiviteit met bestemmingen op basis van de waarde die u kiest.  Als u bijvoorbeeld 30 seconden selecteert, wordt de verbinding met de bestemming ten minste eenmaal in een periode van 30 seconden gecontroleerd.
    * **Drempel waarde voor geslaagd** : u kunt drempels instellen voor de volgende netwerk parameters:
       * **Controles mislukt** : Stel het percentage controles in dat kan mislukken wanneer bronnen de connectiviteit met bestemmingen controleren door gebruik te maken van de criteria die u hebt opgegeven. Voor het TCP-of ICMP-protocol kan het percentage mislukte controles worden vergeleken met het percentage pakket verlies. Voor het HTTP-protocol vertegenwoordigt dit veld het percentage HTTP-aanvragen dat geen antwoord heeft ontvangen.
       * **Retour tijd** : Stel de RTT in milliseconden in om te bepalen hoe lang bronnen kunnen worden uitgevoerd om verbinding te maken met de bestemming via de test configuratie.
    
       ![Scherm opname waarin wordt getoond hoe een test configuratie moet worden ingesteld in de verbindings monitor](./media/connection-monitor-2-preview/add-test-config.png)

Alle bronnen, doelen en test configuraties die u toevoegt aan een test groep, worden opgesplitst naar afzonderlijke tests. Hier volgt een voor beeld van hoe bronnen en bestemmingen worden uitgesplitst:

* Test groep: TG1
* Bronnen: 3 (A, B, C)
* Doelen: 2 (D, E)
* Test configuraties: 2 (configuratie 1, configuratie 2)
* Totaal aantal gemaakte tests: 12

| Test nummer | Bron | Doel | Configuratie testen |
| --- | --- | --- | --- |
| 1 | A | D | Configuratie 1 |
| 2 | A | D | Configuratie 2 |
| 3 | A | E | Configuratie 1 |
| 4 | A | E | Configuratie 2 |
| 5 | B | D | Configuratie 1 |
| 6 | B | D | Configuratie 2 |
| 7 | B | E | Configuratie 1 |
| 8 | B | E | Configuratie 2 |
| 9 | C | D | Configuratie 1 |
| 10 | C | D | Configuratie 2 |
| 11 | C | E | Configuratie 1 |
| 12 | C | E | Configuratie 2 |

### <a name="scale-limits"></a>Schaal limieten

Verbindings monitors hebben de volgende schaal limieten:

* Maximum aantal verbindings monitors per abonnement per regio: 100
* Maximum aantal test groepen per verbindings monitor: 20
* Maximum aantal bronnen en bestemmingen per verbindings monitor: 100
* Maximum aantal test configuraties per verbindings monitor: 
    * 20 via ARMClient
    * 2 via de Azure Portal

## <a name="analyze-monitoring-data-and-set-alerts"></a>Bewakings gegevens analyseren en waarschuwingen instellen

Nadat u een verbindings monitor hebt gemaakt, controleren bronnen de connectiviteit met bestemmingen op basis van uw test configuratie.

### <a name="checks-in-a-test"></a>Controles in een test

Op basis van het protocol dat u hebt gekozen in de test configuratie, voert verbindings controle (preview) een reeks controles uit voor het koppelen van de bron bestemming. De controles worden uitgevoerd op basis van de door u gekozen test frequentie.

Als u HTTP gebruikt, berekent de service het aantal HTTP-antwoorden dat een antwoord code heeft geretourneerd. Het resultaat bepaalt het percentage mislukte controles. De service meet de tijd tussen een HTTP-aanroep en het antwoord om de RTT te berekenen.

Als u TCP of ICMP gebruikt, berekent de service het percentage pakket verlies om het percentage mislukte controles te bepalen. Voor de berekening van de RTT wordt de tijd gemeten die nodig is om de bevestiging (ACK) te ontvangen voor de pakketten die zijn verzonden. Als u traceroute-gegevens voor uw netwerk tests hebt ingeschakeld, kunt u het verlies en de latentie van hops per hop voor uw on-premises netwerk weer geven.

### <a name="states-of-a-test"></a>Statussen van een test

Op basis van de gegevens die de controles retour neren, kunnen tests de volgende statussen hebben:

* **Geslaagd** : werkelijke waarden voor het percentage mislukte controles en de RTT bevinden zich binnen de opgegeven drempel waarden.
* **Mislukt** : werkelijke waarden voor het percentage mislukte controles of de RTT heeft de opgegeven drempel waarden overschreden. Als er geen drempel waarde is opgegeven, wordt de fout status door een test bereikt wanneer het percentage mislukte controles 100 is.
* **Waarschuwing** : er zijn geen criteria opgegeven voor het percentage mislukte controles. Als de opgegeven criteria ontbreken, wijst verbindings monitor (preview) automatisch een drempel waarde toe. Wanneer deze drempel waarde wordt overschreden, wordt de test status gewijzigd in waarschuwing.

### <a name="data-collection-analysis-and-alerts"></a>Gegevens verzameling,-analyse en-waarschuwingen

De gegevens die door verbindings monitor (preview) worden verzameld, worden opgeslagen in de werk ruimte Log Analytics. U kunt deze werk ruimte instellen tijdens het maken van de verbindings monitor. 

Bewakings gegevens zijn ook beschikbaar in Azure Monitor meet waarden. U kunt Log Analytics gebruiken om uw bewakings gegevens even lang te houden als u wilt. Azure Monitor metrische gegevens standaard slechts 30 dagen worden opgeslagen. 

U kunt [op metrische op basis van de gegevens waarschuwingen instellen](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Dash boards controleren

Op de Dash boards controleren ziet u een lijst met de verbindings monitors die u kunt openen voor uw abonnementen, regio's, tijds tempels, bronnen en doel typen.

Wanneer u naar de verbindings monitor (preview) gaat vanuit Network Watcher, kunt u gegevens weer geven op:

* **Verbindings monitor** : een lijst met alle verbindings monitors die zijn gemaakt voor uw abonnementen, regio's, tijds tempels, bronnen en doel typen. Deze weer gave is de standaard instelling.
* **Test groepen** : een lijst met alle test groepen die zijn gemaakt voor uw abonnementen, regio's, tijds tempels, bronnen en doel typen. Deze test groepen worden niet gefilterd op verbindings monitors.
* **Test** : een lijst van alle tests die worden uitgevoerd voor uw abonnementen, regio's, tijds tempels, bronnen en doel typen. Deze tests worden niet gefilterd door verbindings monitors of test groepen.

In de volgende afbeelding worden de drie gegevens weergaven aangeduid met pijl 1.

Op het dash board kunt u elke verbindings monitor uitvouwen om de test groepen weer te geven. Vervolgens kunt u elke test groep uitvouwen om te zien welke tests er worden uitgevoerd. 

U kunt een lijst filteren op basis van:

* **Filters op het hoogste niveau** : Kies abonnementen, regio's, tijds tempel bronnen en doel typen. Zie vak 2 in de volgende afbeelding.
* **Op status gebaseerde filters** : filteren op de status van de verbindings monitor, test groep of test. Zie pijl 3 in de volgende afbeelding.
* **Aangepaste filters** : Kies **Alles selecteren** om een algemene zoek opdracht uit te voeren. Als u wilt zoeken op een specifieke entiteit, selecteert u in de vervolg keuzelijst. Zie pijl 4 in de volgende afbeelding.

![Scherm afbeelding die laat zien hoe u de weer gaven van verbindings monitors, test groepen en tests in verbindings monitor kunt filteren (preview-versie)](./media/connection-monitor-2-preview/cm-view.png)

Bijvoorbeeld, om te kijken naar alle tests in verbindings monitor (preview) waarbij het bron-IP-adres 10.192.64.56 is:
1. Wijzig de weer gave om deze te **testen**.
1. Typ *10.192.64.56* in het zoek veld
1. Selecteer in de vervolg keuzelijst **bronnen**.

Alleen mislukte testen weer geven in verbindings monitor (preview) waarbij het bron-IP-adres 10.192.64.56 is:
1. Wijzig de weer gave om deze te **testen**.
1. Selecteer voor het filter op basis van status de optie **mislukt**.
1. Typ *10.192.64.56* in het zoek veld
1. Selecteer in de vervolg keuzelijst **bronnen**.

Alleen mislukte testen weer geven in verbindings monitor (preview) waarbij de bestemming outlook.office365.com is:
1. Wijzig de weer gave om deze te **testen**.
1. Selecteer voor het filter op basis van status de optie **mislukt**.
1. Voer in het zoek veld *Outlook.office365.com* in
1. Selecteer in de vervolg keuzelijst **bestemmingen**.

   ![Scherm opname van een weer gave die is gefilterd zodat alleen mislukte tests voor het Outlook.Office365.com doel worden weer gegeven](./media/connection-monitor-2-preview/tests-view.png)

De trends in de RTT en het percentage mislukte controles voor een verbindings monitor weer geven:
1. Selecteer de verbindings monitor die u wilt onderzoeken. De bewakings gegevens zijn standaard ingedeeld op test groep.

   ![Scherm afbeelding met metrische gegevens voor een verbindings monitor, weer gegeven per test groep](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Kies de test groep die u wilt onderzoeken.

   ![Scherm afbeelding die laat zien waar een test groep moet worden geselecteerd](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    U ziet de vijf meest voorkomende mislukte tests van de test groep, op basis van de RTT of het percentage mislukte controles. Voor elke test ziet u de RTT en trend lijnen voor het percentage mislukte controles.
1. Selecteer een test in de lijst of kies een andere test om te onderzoeken. Voor uw tijds interval en het percentage mislukte controles ziet u de drempel waarde en de werkelijke waarden. Voor RTT ziet u de waarden voor drempel waarde, gemiddelde, minimum en maximum.

   ![Scherm opname van de resultaten van een test voor de RTT en het percentage mislukte controles](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Wijzig het tijds interval om meer gegevens weer te geven.
1. Wijzig de weer gave om bronnen, doelen of test configuraties weer te geven. 
1. Kies een bron op basis van mislukte testen en onderzoek de vijf mislukte testen. Kies bijvoorbeeld **weer geven per**  >  **bronnen** en **weer geven op**  >  **bestemmingen** om de relevante tests in de verbindings monitor te onderzoeken.

   ![Scherm opname van de prestatie gegevens voor de top vijf mislukte tests](./media/connection-monitor-2-preview/cm-drill-select-source.png)

De trends in de RTT en het percentage mislukte controles voor een test groep weer geven:

1. Selecteer de test groep die u wilt onderzoeken. 

    Standaard worden de bewakings gegevens gerangschikt op bronnen, doelen en test configuraties (tests). U kunt de weer gave later wijzigen van test groepen in bronnen, doelen of test configuraties. Kies vervolgens een entiteit om de vijf belangrijkste mislukte tests te onderzoeken. Wijzig de weer gave bijvoorbeeld in bronnen en doelen om de relevante tests in de geselecteerde verbindings monitor te onderzoeken.
1. Kies de test die u wilt onderzoeken.

   ![Scherm afbeelding die laat zien waar een test moet worden geselecteerd](./media/connection-monitor-2-preview/tg-drill.png)

    Voor uw tijds interval en voor het percentage mislukte controles ziet u drempel waarden en werkelijke waarden. Voor de RTT ziet u waarden voor drempel waarde, gemiddelde, minimum en maximum. U ziet ook gebrande waarschuwingen voor de test die u hebt geselecteerd.
1. Wijzig het tijds interval om meer gegevens weer te geven.

De trends in de RTT en het percentage mislukte controles voor een test bekijken:
1. Selecteer de bron-, doel-en test configuratie die u wilt onderzoeken.

    Voor uw tijds interval en het percentage mislukte controles ziet u drempel waarden en werkelijke waarden. Voor de RTT ziet u waarden voor drempel waarde, gemiddelde, minimum en maximum. U ziet ook gebrande waarschuwingen voor de test die u hebt geselecteerd.

   ![Scherm opname met metrische gegevens voor een test](./media/connection-monitor-2-preview/test-drill.png)

1. Selecteer **topologie**om de netwerk topologie te bekijken.

   ![Scherm opname van het tabblad netwerk topologie](./media/connection-monitor-2-preview/test-topo.png)

1. Als u de geïdentificeerde problemen wilt weer geven, selecteert u in de topologie een hop in het pad. (Deze hops zijn Azure-resources.) Deze functionaliteit is momenteel niet beschikbaar voor on-premises netwerken.

   ![Scherm afbeelding met een geselecteerde hop-koppeling op het tabblad topologie](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Query's in Log Analytics vastleggen

Gebruik Log Analytics om aangepaste weer gaven van uw bewakings gegevens te maken. Alle gegevens die in de gebruikers interface worden weer gegeven, zijn afkomstig uit Log Analytics. U kunt gegevens in de opslag plaats interactief analyseren. Correleer de gegevens van Status van agent of andere oplossingen die zijn gebaseerd op Log Analytics. De gegevens exporteren naar Excel of Power BI, of een deel bare koppeling maken.

#### <a name="metrics-in-azure-monitor"></a>Metrische gegevens in Azure Monitor

In verbindings monitors die zijn gemaakt vóór de ervaring van de verbindings monitor (preview-versie) zijn alle vier de metrische gegevens beschikbaar:% probe failed, AverageRoundtripMs, ChecksFailedPercent (preview) en RoundTripTimeMs (preview). In verbindings monitors die zijn gemaakt in de ervaring van de verbindings monitor (preview-versie) zijn gegevens alleen beschikbaar voor de metrische waarden die zijn gelabeld met *(preview)*.

![Scherm opname met metrische gegevens in de verbindings monitor (preview-versie)](./media/connection-monitor-2-preview/monitor-metrics.png)

Wanneer u metrische gegevens gebruikt, stelt u het resource type in als micro soft. Network/networkWatchers/connectionMonitors

| Gegevens | Weergavenaam | Eenheid | Type aggregatie | Description | Dimensies |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % Tests mislukt | Percentage | Average | Het percentage van de connectiviteits controle tests is mislukt. | Geen dimensies |
| AverageRoundtripMs | Gem. retour tijd (MS) | Milliseconden | Average | Gemiddelde netwerk-RTT voor connectiviteits controle tests die zijn verzonden tussen de bron en de bestemming. |             Geen dimensies |
| ChecksFailedPercent (preview-versie) | % Controles mislukt (preview-versie) | Percentage | Average | Percentage mislukte controles voor een test. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>Doel locatie <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Regio |
| RoundTripTimeMs (preview-versie) | Retour tijd (MS) (preview-versie) | Milliseconden | Average | RTT voor controles die worden verzonden tussen de bron en de bestemming. Deze waarde is niet van het gemiddelde. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>Doel locatie <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Regio |

#### <a name="metric-alerts-in-azure-monitor"></a>Metrische waarschuwingen in Azure Monitor

Een waarschuwing maken in Azure Monitor:

1. Kies de bron van de verbindings monitor die u hebt gemaakt in de verbindings monitor (preview-versie).
1. Zorg ervoor dat **metrische gegevens** worden weer gegeven als signaal type voor de verbindings monitor.
1. In voor **waarde toevoegen**selecteert u voor de **signaal naam** **ChecksFailedPercent (preview)** of **RoundTripTimeMs (preview)**.
1. Kies **metrische gegevens**bij **signaal type**. Selecteer bijvoorbeeld **ChecksFailedPercent (preview)**.
1. Alle dimensies voor de metriek worden weer gegeven. Kies de dimensie naam en dimensie waarde. Selecteer bijvoorbeeld **bron adres** en voer vervolgens het IP-adres in van een bron in de verbindings monitor.
1. Vul in **waarschuwings logica**de volgende gegevens in:
   * **Voorwaarde type**: **static**.
   * **Voor waarde** en **drempel**waarde.
   * **Aggregatie granulatie en frequentie van de evaluatie: met**verbindings monitor (preview) worden gegevens elke minuut bijgewerkt.
1. Kies in **acties**uw actie groep.
1. Geef waarschuwings Details op.
1. De waarschuwings regel maken.

   ![Scherm opname met het regel gebied maken in Azure Monitor; ' Bron adres ' en ' naam van bron eindpunt ' zijn gemarkeerd](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Problemen in uw netwerk vaststellen

Met verbindings monitor (preview) kunt u problemen in uw verbindings monitor en uw netwerk diagnosticeren. Problemen in uw hybride netwerk worden gedetecteerd door de Log Analytics agents die u eerder hebt geïnstalleerd. Problemen in Azure worden gedetecteerd door de extensie Network Watcher. 

U kunt problemen weer geven in het Azure-netwerk in de netwerk topologie.

Voor netwerken waarvan de bronnen on-premises Vm's zijn, kunnen de volgende problemen worden gedetecteerd:

* Time-out van aanvraag.
* Het eind punt is niet omgezet door DNS: tijdelijk of persistent. De URL is ongeldig.
* Geen hosts gevonden.
* De bron kan geen verbinding maken met de bestemming. Doel niet bereikbaar via ICMP.
* Certificaat problemen: 
    * Het client certificaat is vereist voor de verificatie van de agent. 
    * De lijst met herlocatie van het certificaat is niet toegankelijk. 
    * De hostnaam van het eind punt komt niet overeen met de naam van het onderwerp of de alternatieve onderwerpnaam van het certificaat. 
    * Het basis certificaat ontbreekt in het archief met vertrouwde certificerings instanties van de bron van de lokale computer. 
    * Het SSL-certificaat is verlopen, ongeldig, ingetrokken of niet compatibel.

Voor netwerken waarvan de bronnen virtuele Azure-machines zijn, kunnen de volgende problemen worden gedetecteerd:

* Agent problemen:
    * De agent is gestopt.
    * De DNS-omzetting is mislukt.
    * Er luistert geen toepassing of listener op de doel poort.
    * Socket kan niet worden geopend.
* Problemen met de VM-status: 
    * Starten
    * Stoppen
    * Gestopt
    * Vrijgeven
    * Toewijzing ongedaan gemaakt
    * Opnieuw opstarten
    * Niet toegewezen
* De vermelding in de ARP-tabel ontbreekt.
* Verkeer is geblokkeerd vanwege lokale firewall problemen of NSG-regels.
* Problemen met virtuele netwerk gateway: 
    * Ontbrekende routes.
    * De tunnel tussen twee gateways is losgekoppeld of ontbreekt.
    * De tweede gateway is niet gevonden door de tunnel.
    * Er zijn geen peering-gegevens gevonden.
* De route ontbreekt in micro soft Edge.
* Verkeer is gestopt vanwege systeem routes of UDR.
* BGP is niet ingeschakeld op de gateway verbinding.
* De DIP-test is niet beschikbaar op de load balancer.
