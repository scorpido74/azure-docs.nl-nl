---
title: Verbindings monitor (preview-versie) | Microsoft Docs
description: Meer informatie over het gebruik van verbindings monitor (preview) voor het bewaken van netwerk communicatie in een gedistribueerde omgeving
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 5dc705fbd17a12ee001e1e8de15b49e841f08b81
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049207"
---
# <a name="unified-connectivity-monitoring-with-connection-monitor-preview"></a>Geïntegreerde controle van connectiviteit met verbindings monitor (preview-versie)

Verbindings monitor (preview) biedt een geïntegreerde end-to-end functionaliteit voor verbindings bewaking in azure Network Watcher voor hybride en Azure-Cloud implementaties. Azure Network Watcher biedt hulpprogram ma's voor het bewaken, diagnosticeren en weer geven van connectiviteits gegevens voor uw Azure-implementaties.

Belangrijkste use cases:

- U hebt een front-end-webserver-VM die communiceert met een database server-VM in een toepassing met meerdere lagen. U de netwerk verbinding tussen de twee virtuele machines wilt controleren.
- U wilt dat virtuele machines in de regio VS Oost de virtuele machines in de regio VS Central pingen en netwerk latentie van meerdere regio's vergelijken
- U hebt meerdere on-premises Office-sites in steden zoals Seattle. verbinding maken met Office 365-Url's. U wilt de latenties van gebruikers met Office 365 Url's van Seattle en Ashburn vergelijken.
- U hebt een hybride toepassing die verbinding moet maken met een Azure Storage-eind punt. U wilt de latentie tussen een on-premises site en de Azure-toepassing vergelijken met hetzelfde Azure Storage-eind punt.
- U wilt de connectiviteit controleren van virtuele Azure-machines die uw Cloud toepassing hosten in uw on-premises installatie.

In deze preview-fase combineert de oplossing het beste van twee belang rijke mogelijkheden: de [verbindings monitor](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) van Network Watcher en NETWERKPRESTATIEMETER (NPM) van de [service verbindings monitor](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity).

Hoogte

* Geïntegreerde, intuïtieve ervaring voor Azure en hybride bewakings behoeften
* Bewaking van de connectiviteit tussen meerdere regio's, meerdere werk ruimten
* Hogere probing-frequenties en betere zicht baarheid van netwerk prestaties
* Snellere waarschuwingen voor uw hybride implementaties
* Ondersteuning voor verificatie op basis van HTTP-, TCP-en ICMP-verbindingen
* Metrische gegevens en Log Analytics ondersteuning voor instellingen voor Azure en niet-Azure-tests

![Verbindingsmonitor](./media/connection-monitor-2-preview/hero-graphic.png)

Volg de onderstaande stappen om de bewaking te starten met behulp van de verbindings monitor (preview-versie)

## <a name="step-1-install-monitoring-agents"></a>Stap 1: bewakings agenten installeren

Verbindings monitor is afhankelijk van lichte uitvoer bare bestanden om connectiviteits controles uit te voeren.  We ondersteunen connectiviteits controles vanuit Azure-en on-premises omgevingen. Het specifieke uitvoer bare bestand dat moet worden gebruikt, is afhankelijk van het feit of uw virtuele machine wordt gehost op Azure of on-premises.

### <a name="agents-for-azure-virtual-machines"></a>Agents voor Azure virtual machines

Als u wilt dat uw Azure-Vm's worden herkend als bron voor bewaking, moet u de extensie van de virtuele machine van de Network Watcher-agent (ook wel Network Watcher extensie) installeren. De uitbrei ding Network Watcher agent is een vereiste voor het activeren van end-to-end-bewaking en andere geavanceerde functionaliteit op virtuele machines van Azure. U kunt [een virtuele machine maken en de Network Watcher extensie](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[erop](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)installeren.  U kunt ook Network Watcher extensie voor [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) en [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) afzonderlijk installeren, configureren en problemen oplossen.

Als NSG of firewall regels de communicatie tussen de bron en de bestemming blok keren, detecteert de verbindings monitor het probleem en wordt deze weer gegeven als een diagnostisch bericht in de topologie. Als u verbindings bewaking wilt inschakelen, moet u ervoor zorgen dat de NSG-en firewall regels pakketten toestaan via TCP of ICMP tussen de bron-en doel locatie.

### <a name="agents-for-on-premise-machines"></a>Agents voor on-premises machines

Als u wilt dat de verbindings monitor uw on-premises computers herkent als bronnen voor bewaking, moet u de Log Analytics-agent installeren op de computers en de oplossing voor netwerk prestatie bewaking inschakelen. Deze agents zijn gekoppeld aan Log Analytics werk ruimten en moeten werk ruimte-ID en primaire sleutel instellen voordat ze kunnen worden gecontroleerd.

Als u Log Analytics agent voor Windows-computers wilt installeren, volgt u de instructies in [deze koppeling](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)

Zorg ervoor dat de bestemming bereikbaar is als er firewalls of virtuele netwerk apparaten (NVA) aanwezig zijn in het pad.

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>Stap 2: Network Watcher inschakelen voor uw abonnement

Alle abonnementen met een VNET worden ingeschakeld met Network Watcher. Wanneer u een virtueel netwerk in uw abonnement maakt, wordt Network Watcher automatisch ingeschakeld in de regio en het abonnement van Virtual Network. Er zijn geen gevolgen voor uw resources of kosten voor het automatisch inschakelen van Network Watcher. Zorg ervoor dat Network Watcher niet expliciet is uitgeschakeld voor uw abonnement. Zie [enable Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)(Engelstalig) voor meer informatie.

## <a name="step-3-create-connection-monitor"></a>Stap 3: verbindings monitor maken 

Met _verbindings monitor_ wordt de communicatie met regel matige tussen pozen bewaakt en wordt u geïnformeerd over de bereik baarheid, latentie en netwerk topologie wijzigingen tussen bron agenten en doel eindpunten. Bronnen kunnen bestaan uit virtuele machines van Azure of op lokale computers waarop een bewakings agent is geïnstalleerd. Doel eindpunten kunnen Office 365 Url's, Dynamics 365 Url's, aangepaste Url's, Azure VM-resource-Id's, IPv4, IPv6, FQDN of een domein naam zijn.

### <a name="accessing-connection-monitor-preview"></a>Toegang tot de verbindings monitor (preview-versie)

1. Ga vanaf de start pagina van Azure Portal naar Network Watcher
2. Klik in het linkerdeel venster van Network Watcher op het tabblad verbindings monitor (preview).
3. U kunt alle verbindings monitors weer geven die zijn gemaakt met behulp van de ervaring van de verbindings monitor (preview). Alle verbindings monitors die zijn gemaakt met behulp van de klassieke ervaring van het tabblad verbindings controle, worden weer gegeven op het tabblad verbindings controle.

    ![Een verbindings monitor maken](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>Een verbindings monitor maken

Verbindings monitors die zijn gemaakt met behulp van verbindings controle (preview) bieden de mogelijkheid om zowel lokale als Azure-Vm's toe te voegen als bronnen en om verbinding te maken met eind punten, die Azure of een andere URL/IP kunnen omvatten.

Hieronder ziet u de entiteiten in een verbindings monitor:

* Bron van de verbindings monitor: regio-specifieke Azure-resource. Alle hieronder genoemde entiteiten zijn eigenschappen van een verbindingscontrole resource.
* Eind punten: alle bronnen en doelen die deel uitmaken van connectiviteits controles worden als eind punten genoemd. Voor beelden van eind punt – Azure-Vm's, on-premises agents, Url's, Ip's
* Configuratie testen: elke test configuratie is protocol-specifiek. Op basis van het gekozen protocol kunt u poort, drempel waarden, test frequentie en andere para meters definiëren
* Test groep: elke test groep bevat bron-eind punten, bestemmings eindpunten en test configuraties. Elke verbindings monitor kan meer dan één test groep bevatten
* Test: een combi natie van een bron eindpunt, bestemmings eindpunt en test configuratie maken één test. De test is het laagste niveau waarop de bewakings gegevens (controles zijn mislukt% en RTT) beschikbaar zijn

 ![Een verbindings monitor maken](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>Van portal

Als u een verbindings monitor wilt maken, volgt u de onderstaande stappen:

1. In het dash board verbindings monitor (preview) klikt u op maken in de linkerbovenhoek.
2. Voer op het tabblad basis informatie in voor de verbindings monitor
   1. Naam van de verbindings monitor: naam van de verbindings monitor. Standaard naamgevings regels voor Azure-resources zijn hier van toepassing.
   2. Abonnement: Kies een abonnement voor uw verbindings monitor.
   3. Regio: Kies een regio voor de bron van de verbindings monitor. U kunt alleen de bron-Vm's selecteren die in deze regio worden gemaakt.
   4. Werkruimte configuratie: u kunt de standaardwerk ruimte die door de verbindings monitor is gemaakt gebruiken om uw bewakings gegevens op te slaan door te klikken op het standaard selectie vakje. Schakel dit selectie vakje uit als u een aangepaste werk ruimte wilt kiezen. Kies het abonnement en de regio om de werk ruimte te selecteren die uw bewakings gegevens zal bevatten.
   5. Klik op volgende: test groepen om test groepen toe te voegen

      ![Een verbindings monitor maken](./media/connection-monitor-2-preview/create-cm-basics.png)

3. Klik op het tabblad test groepen op ' + Test groep ' om een test groep toe te voegen. Gebruik _test groepen maken in verbindings monitor_ om test groepen toe te voegen. Klik op ' bekijken + maken ' om de verbindings monitor te controleren.

   ![Een verbindings monitor maken](./media/connection-monitor-2-preview/create-tg.png)

4. Controleer op het tabblad ' controleren en maken ' basis informatie en test groepen voordat u de verbindings monitor maakt. De verbindings monitor bewerken vanuit de weer gave ' bekijken en maken ':
   1. Als u de basis gegevens wilt bewerken, gebruikt u het potlood pictogram zoals opgegeven in vak 1 van de afbeelding 2
   2. Als u de afzonderlijke test groepen wilt bewerken, klikt u op de test groep die u wilt bewerken om de test groep te openen in de bewerkings modus.
   3. Huidige kosten/maand geeft de kosten weer tijdens de preview-periode. Er wordt momenteel geen kosten in rekening gebracht voor het gebruik van de verbindings monitor, zodat in deze kolom de waarde nul wordt weer gegeven. Werkelijke kosten/maand geeft de prijs aan die na de algemene Beschik baarheid wordt gefactureerd. Let op: log Analytics-opname kosten zullen zelfs tijdens de preview van toepassing zijn.

5. Klik op het tabblad ' bekijken + maken ' op de knop maken om de verbindings monitor te maken.

   ![Een verbindings monitor maken](./media/connection-monitor-2-preview/review-create-cm.png)

6.  Met verbindings monitor (preview) wordt de bron van de verbindings monitor gemaakt op de achtergrond.

#### <a name="from-armclient"></a>Van Armclient

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premise agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

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

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

Implementatie opdracht:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>Test groepen maken in verbindings monitor

Elke test groep in de verbindings monitor bevat bronnen en bestemming die worden getest op netwerk parameters van mislukte controles en RTT via test configuraties.

#### <a name="from-portal"></a>Van portal

Als u een test groep wilt maken in een verbindings monitor, geeft u de waarde voor de onderstaande velden op:

1. Test groep uitschakelen: als u dit selectie vakje inschakelt, wordt de bewaking uitgeschakeld voor alle bronnen en bestemmingen die zijn opgegeven in de test groep. U ziet deze optie standaard uitgeschakeld.
2. Naam: naam van de test groep
3. Bronnen: u kunt zowel virtuele Azure-machines als lokale computers opgeven als bronnen als er agents in zijn geïnstalleerd. Raadpleeg stap 1 om de agent te installeren die specifiek is voor uw bron.
   1. Klik op het tabblad Azure-agents om Azure-agents te selecteren. Deze Vm's worden alleen weer gegeven die zijn gebonden aan de regio die u hebt opgegeven op het moment dat de verbindings monitor wordt gemaakt. Vm's zijn standaard gegroepeerd in het abonnement waartoe ze behoren en de groepen worden samengevouwen. U kunt inzoomen op het niveau van abonnementen op andere niveaus in de hiërarchie:

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      U kunt ook de waarde van het veld Group by wijzigen om de structuur te starten vanaf een ander niveau. Bijvoorbeeld: Group by – VNET geeft de Vm's weer met agents in de hiërarchie VNETs-\&gt; Subnetten-\&gt; Vm's met agents.

      ![Bronnen toevoegen](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. Klik op het tabblad niet-Azure-agents om on-premises agents te selecteren. Standaard ziet u agents die zijn gegroepeerd in werk ruimten in een regio. Alleen de werk ruimten waarvoor Netwerkprestatiemeter oplossing is geconfigureerd, worden weer gegeven. Voeg de Netwerkprestatiemeter oplossing toe aan uw werk ruimte vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). U kunt ook het proces dat wordt beschreven in [Azure monitor oplossingen toevoegen van de Oplossingengalerie](https://docs.microsoft.com/azure/azure-monitor/insights/solutions) gebruiken. Standaard ziet u de regio die is geselecteerd op het tabblad basis informatie in de weer gave verbindings controle maken. U kunt de regio wijzigen en agents kiezen uit werk ruimten uit de zojuist geselecteerde regio. U kunt ook de waarde van het veld Group by wijzigen in groep op subnetten.

      ![Niet-Azure-bronnen](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. Klik op ' controleren ' om de geselecteerde Azure-en niet-Azure-agents te bekijken.

      ![Bronnen controleren](./media/connection-monitor-2-preview/review-sources.png)

   4. Klik op gereed zodra u klaar bent met het selecteren van de bronnen.

4. Doelen: u kunt de connectiviteit controleren op virtuele machines van Azure of een eind punt (open bare IP, URL, FQDN) door ze op te geven als bestemming. U kunt in één test groep Azure-Vm's, O365-Url's, D365-Url's of aangepaste eind punten toevoegen.

   1. Klik op het tabblad Azure Vm's om virtuele Azure-machines als doelen te selecteren. Standaard ziet u de Azure-Vm's gegroepeerd in de abonnements hiërarchie in dezelfde regio die is geselecteerd op het tabblad basis informatie in de weer gave verbindings controle maken. U kunt de regio wijzigen en Azure-Vm's kiezen uit de zojuist geselecteerde regio. U kunt inzoomen op het niveau van abonnementen op andere niveaus in de hiërarchie, zoals Azure-agents.

      ![Doelen toevoegen](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![Bestemmingen toevoegen 2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. Klik op het tabblad eind punten om virtuele Azure-machines als doelen te selecteren. De lijst met eind punten wordt gevuld met de test-Url's O365 en D365, gegroepeerd op naam.  U kunt ook een eind punt kiezen dat is gemaakt in andere test groepen in dezelfde verbindings monitor. Als u een nieuw eind punt wilt toevoegen, klikt u op + eind punt in de rechter bovenhoek van het scherm en geeft u eind punt-URL/IP/FQDN en naam op

      ![Eind punten toevoegen](./media/connection-monitor-2-preview/add-endpoints.png)

   3. Klik op ' controleren ' om de geselecteerde Azure-en niet-Azure-agents te bekijken.
   4. Klik op gereed zodra u klaar bent met het selecteren van de bronnen.

5. Configuratie testen: u kunt een wille keurig aantal test configuraties koppelen aan een bepaalde test groep. De portal beperkt deze in één test configuratie per test groep, maar gebruikt Armclient om meer toe te voegen.
   1. Naam: naam voor de test configuratie
   2. Protocol: u kunt kiezen tussen TCP, ICMP of HTTP. Als u HTTP-naar-HTTPS wilt wijzigen, selecteert u HTTP als protocol en 443 als poort
   3. Test configuratie voor netwerk maken: u ziet dit selectie vakje alleen als u HTTP in het veld protocol selecteert. Schakel dit veld in om een andere test configuratie te maken met dezelfde bronnen en bestemmingen die zijn opgegeven in stap 3 en 4 via het TCP/ICMP-protocol. De zojuist gemaakte test configuratie heeft de naam '\&lt; opgegeven in 5. a\&gt;\_networkTestConfig "
   4. Traceroute uitschakelen: dit veld is van toepassing op test groepen met TCP of ICMP as protocol.  Schakel dit selectie vakje in om te voor komen dat bronnen de round-trip tijd voor het detecteren van de topologie en de hop-by-hop ontdekken.
   5. Doel poort – u kunt dit veld aanpassen om een doel poort van uw keuze te plaatsen.
   6. Test frequentie: dit veld bepaalt hoe vaak bronnen worden gepingd op het protocol en de poort die hierboven is opgegeven. U kunt kiezen tussen 30 seconden, 1 minuut, 5 minuten, 15 minuten en 30 minuten. Bronnen testen de connectiviteit met bestemmingen op basis van de waarde die u kiest.  Als u bijvoorbeeld 30 seconden selecteert, wordt de verbinding met de bestemming ten minste eenmaal in de periode van 30 seconden gecontroleerd.
   7. Status drempels: u kunt drempels instellen voor de netwerk parameters die hieronder worden vermeld
      1. Controles mislukt in%: het percentage controles is mislukt wanneer bronnen de connectiviteit met bestemming controleren volgens de bovenstaande criteria. Voor het TCP/ICMP-protocol kunnen de controles die zijn mislukt in% worden vergeleken met pakket verlies%. Voor het HTTP-protocol vertegenwoordigt dit veld het aantal HTTP-aanvragen dat geen antwoord heeft ontvangen.
      2. RTT in milliseconden-retour tijd in milliseconden wanneer bronnen verbinding maken met bestemming via de hierboven opgegeven test configuratie.

      ![TG toevoegen](./media/connection-monitor-2-preview/add-test-config.png)

Alle bronnen en bestemmingen die zijn toegevoegd aan een test groep met de opgegeven test configuratie, worden verdeeld over afzonderlijke tests. Bijvoorbeeld:

* Test groep: TG1
* Bronnen: 3 (A, B, C)
* Doelen: 2 (D, E)
* Test configuratie: 2 (configuratie 1, configuratie 2)
* Gemaakte tests: totaal = 12

| **Test nummer** | **Bron** | **Beoogde** | **Configuratie naam testen** |
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

* Max. aantal verbindings monitors per abonnement per regio – 100
* Maximum aantal test groepen per verbindings monitor-20
* Max # bronnen + bestemmingen per verbindings monitor – 100
* Max. aantal test configuraties per verbindings monitor – 20 via Armclient. 2 via de portal.

## <a name="step-4--data-analysis-and-alerts"></a>Stap 4: gegevens analyse en waarschuwingen

Wanneer een verbindings monitor is gemaakt, controleren bronnen de connectiviteit met bestemmingen op basis van de opgegeven test configuratie.

### <a name="checks-in-a-test"></a>Controles in een test

Op basis van het protocol dat door een gebruiker is geselecteerd in de test configuratie, voert de verbindings monitor (preview) een reeks controles uit voor het bron doel van de gekozen test frequentie.

Als HTTP is geselecteerd, berekent de service het aantal HTTP-antwoorden dat een antwoord code heeft geretourneerd om te bepalen of de controles zijn mislukt%.  Voor het berekenen van de RTT meten we de tijd die nodig is om het antwoord van een HTTP-aanroep te ontvangen.

Als TCP of ICMP is geselecteerd, berekent de service het pakket% om te bepalen of de controles zijn mislukt%. Voor het berekenen van de RTT meten we de tijd die nodig is om de ACK voor verzonden pakketten te ontvangen. Als u traceroute-gegevens voor uw netwerk tests hebt ingeschakeld, kunt u verlies en latentie van hops voor uw on-premises netwerk zien.

### <a name="states-of-a-test"></a>Statussen van een test

Op basis van de gegevens die door controles in een test worden geretourneerd, kan elke test de volgende statussen hebben:

* Pass = wanneer de werkelijke waarden voor de mislukte controles% en de RTT binnen de opgegeven drempel waarden vallen
* Fail = wanneer de werkelijke waarden voor de controles zijn mislukt% of er zijn drempel waarden opgegeven voor de RTT. Als er geen drempel waarde is opgegeven, wordt een test gemarkeerd als mislukt bij mislukte controles% = 100%
* Waarschuwing: wanneer er geen criteria voor controles zijn opgegeven, wordt er geen uitgevoerd. In een dergelijk geval maakt verbindings monitor (preview) gebruik van een automatisch ingesteld criterium als drempel en wanneer die drempel waarde van de test is geschonden, wordt ingesteld op waarschuwing

### <a name="data-collection-analysis-and-alerts"></a>Gegevens verzameling,-analyse en-waarschuwingen

Alle gegevens die worden verzameld door de verbindings monitor (preview), worden opgeslagen in de werk ruimte Log Analytics die is geconfigureerd op het moment dat de verbindings monitor wordt gemaakt. Bewakings gegevens zijn ook beschikbaar in Azure Monitor meet waarden. U kunt Log Analytics gebruiken om uw bewakings gegevens zo lang mogelijk te houden, maar Azure Monitor metrieken standaard gedurende 30 dagen **.** U kunt vervolgens [waarschuwingen op basis van metrieken instellen voor de gegevens](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>Dash boards bewaken in de oplossing voor verbindings controle

U ziet een lijst met uw verbindings monitor waartoe u toegang hebt voor een bepaalde selectie van abonnementen, regio's, tijds tempel, bron en doel type.

Wanneer u van Network Watcher service naar verbindings monitor (preview) navigeert, kunt u kiezen **uit:**

* Verbindings monitor (standaard): een lijst met alle verbindings monitors die zijn gemaakt voor gekozen abonnementen, regio's, tijds tempel, bron en doel type
* Test groepen: een lijst met alle test groepen die zijn gemaakt voor gekozen abonnementen, regio's, tijds tempel, bron en doel type. Deze test groepen worden niet gefilterd op de verbindings monitor
* Tests: een lijst van alle tests die worden uitgevoerd voor gekozen abonnementen, regio's, tijds tempel, bron-en doel typen. Deze tests worden niet gefilterd op verbindings monitor of test groepen.

U kunt elke verbindings monitor in de test groepen en elke test groep uitvouwen in de verschillende afzonderlijke tests die in het dash board worden uitgevoerd. Gemarkeerd als [1] in de onderstaande afbeelding.

U kunt deze lijst filteren op basis van:

* Filters op het hoogste niveau: abonnementen, regio's, tijds tempel bron en doel typen. Gemarkeerd als [2] in de onderstaande afbeelding.
* Op status gebaseerde filters-filter op tweede niveau voor status van verbindings monitor/test groep/testen. Gemarkeerd als [3] in de onderstaande afbeelding.
* Zoek veld: Kies alle om een algemene zoek opdracht uit te voeren. Als u op een specifieke entiteit wilt zoeken, gebruikt u de vervolg keuzelijst om de zoek resultaten te verfijnen. Gemarkeerd als [4] in de onderstaande afbeelding.

![Filter tests](./media/connection-monitor-2-preview/cm-view.png)

Bijvoorbeeld:

1. Om te kijken naar alle tests in alle verbindings monitor (preview) waarbij bron-IP = 10.192.64.56:
   1. Weer gave wijzigen door naar ' tests '
   2. Gearchiveerde zoek opdracht = 10.192.64.56
   3. Gebruik de vervolg keuzelijst naast waarde om bronnen te selecteren
2. Alleen mislukte testen voor alle verbindings monitor (preview-versie) uitfilteren waarbij bron-IP = 10.192.64.56
   1. Weer gave wijzigen door naar ' tests '
   2. Selecteer ' Fail ' in filters op basis van status.
   3. Zoek veld = 10.192.64.56
   4. Gebruik de vervolg keuzelijst naast waarde om bronnen te selecteren
3. Alleen mislukte testen voor alle verbindings monitor (preview-versie) uitfilteren waarbij bestemming outlook.office365.com is
   1. Weer gave wijzigen door naar ' tests '
   2. Selecteer ' Fail ' in filters op basis van status.
   3. Zoek veld = outlook.office365.com
   4. Gebruik de vervolg keuzelijst naast waarde om "bestemmingen" te selecteren

   ![Mislukte tests](./media/connection-monitor-2-preview/tests-view.png)

De trends van mislukte controles weer geven% en RTT voor:

1. Verbindingsmonitor
   1. Klik op de verbindings monitor die u gedetailleerd wilt onderzoeken
   2. Standaard bekijkt u bewakings gegevens op test groepen

      ![Metrische gegevens weer geven op](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. Kies de test groep die u gedetailleerd wilt onderzoeken

      ![Metrische gegevens per TG](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. Voor de test groep die u in de vorige stap hebt gekozen, ziet u de belangrijkste vijf mislukte tests voor controles die zijn mislukt% of Round msecs. Voor elke test ziet u trend lijnen voor mislukte controles,% en RTT (msec)
   5. Selecteer een test in de bovenstaande lijst of kies een andere test om details te onderzoeken.
   6. Voor het geselecteerde tijds interval, voor mislukte controles, worden de drempel waarde en de werkelijke waarden weer geven. Voor RTT (msec) worden drempelwaarde, Gem, min en maximum waarden weer geven.

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. Het tijds interval voor het weer geven van meer gegevens wijzigen
  8. U kunt de weer gave in stap b wijzigen en kiezen voor het weer geven van bronnen, doelen of test configuraties. Kies vervolgens een bron op basis van mislukte tests en onderzoek de belangrijkste 5 mislukte tests.  Bijvoorbeeld: Kies weer geven per: bronnen en bestemmingen om alle tests te onderzoeken die worden uitgevoerd tussen die combi natie in de geselecteerde verbindings monitor.

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. Test groep
   1. Klik op de test groep die u gedetailleerd wilt onderzoeken
   2. Standaard bekijkt u bewakings gegevens op basis van de configuratie van source + Destination + test (test).

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. Kies de test die u gedetailleerd wilt onderzoeken
   4. Voor het geselecteerde tijds interval, voor mislukte controles, worden de drempel waarde en de werkelijke waarden weer geven. Voor RTT (msec) worden drempelwaarde, Gem, min en maximum waarden weer geven. U ziet ook geactiveerde waarschuwingen die specifiek zijn voor de test die u hebt geselecteerd.
   5. Het tijds interval voor het weer geven van meer gegevens wijzigen
   6. U kunt de weer gave in stap b wijzigen en kiezen voor het weer geven van bronnen, doelen of test configuraties. Kies vervolgens een entiteit om de belangrijkste 5 mislukte tests te onderzoeken.  Bijvoorbeeld: Kies weer geven per: bronnen en bestemmingen om alle tests te onderzoeken die worden uitgevoerd tussen die combi natie in de geselecteerde verbindings monitor.

3. Test
   1. Klik op de bron + bestemming + configuratie van de test die u gedetailleerd wilt onderzoeken
   2. Voor het geselecteerde tijds interval, voor mislukte controles, worden de drempel waarde en de werkelijke waarden weer geven. Voor RTT (msec) worden drempelwaarde, Gem, min en maximum waarden weer geven. U ziet ook geactiveerde waarschuwingen die specifiek zijn voor de test die u hebt geselecteerd.

      ![Test1](./media/connection-monitor-2-preview/test-drill.png)

   3. U kunt ook op ' topologie ' klikken om de netwerk topologie op elk gewenst moment weer te geven.

      ![Test2](./media/connection-monitor-2-preview/test-topo.png)

   4. U kunt klikken op een wille keurige hop van koppeling voor Azure-netwerk om de problemen te zien die door verbindings monitor worden geïdentificeerd. Deze mogelijkheid is op dit moment niet beschikbaar voor on-premises netwerken.

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Query's vastleggen in Azure Monitor Log Analytics

Gebruik Log Analytics om aangepaste weer gaven van uw bewakings gegevens te maken. Alle gegevens die worden weer gegeven in de gebruikers interface, worden ingevuld op basis van Log Analytics. U kunt een interactieve analyse van gegevens in de opslag plaats uitvoeren en gegevens van verschillende bronnen, zoals de status van de agent en andere toepassingen op basis van Log Analytics, correleren. U kunt de gegevens ook exporteren naar Excel, Power BI of een deel bare koppeling.

#### <a name="metrics-in-azure-monitor"></a>Metrische gegevens in Azure Monitor

Voor verbindings monitor die is gemaakt vóór de ervaring van de verbindings monitor (preview), zijn alle vier de metrische gegevens beschikbaar. Voor verbindings monitors die zijn gemaakt met behulp van de ervaring van de verbindings monitor (preview), zijn gegevens alleen beschikbaar voor de metrieken die zijn gelabeld met ' (preview) '.

Resource type: micro soft. Network/networkWatchers/connectionMonitors

| Gegevens | De naam van de metrische gegevens weergeven | Eenheid | Aggregatietype | Beschrijving | Dimensies |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % Tests mislukt | Procent | Average | % van de controles van connectiviteits controle is mislukt | Er zijn geen dimensies |
| AverageRoundtripMs | Gem. retour tijd (MS) | MilliSeconds | Average | Gemiddelde Round-retour tijd van het netwerk (MS) voor connectiviteits controle tests die zijn verzonden tussen de bron en het doel |             Er zijn geen dimensies |
| ChecksFailedPercent (preview-versie) | % Controles mislukt (preview-versie) | Procent | Average | % van de controles voor een test is mislukt | * ConnectionMonitorResourceId <br> * SourceAddress <br> * Bronnaam <br> * SourceResourceId <br> * Source type <br> *-Protocol <br> * DestinationAddress <br> * Doelpad <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * Regio |
| RoundTripTimeMs (preview-versie) | Retour tijd (MS) (preview-versie) | Milliseconden | Average | Retour tijd (MS) voor controles die worden verzonden tussen de bron en de bestemming. Deze waarde is niet gemiddeld | * ConnectionMonitorResourceId <br> * SourceAddress <br> * Bronnaam <br> * SourceResourceId <br> * Source type <br> *-Protocol <br> * DestinationAddress <br> * Doelpad <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * Regio |

 ![Metrische gegevens bewaken](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Metrische waarschuwingen in Azure Monitor

Een waarschuwing maken:

1. Kies uw verbindings monitor-resource die is gemaakt met behulp van de verbindings monitor (preview-versie)
2. Zorg ervoor dat metrische gegevens worden weer gegeven als signaal type voor de resource die in de vorige stap is geselecteerd
3. In voor waarde toevoegen kiest u signaal naam als ChecksFailedPercent (preview) of RoundTripTimeMs (preview) en signaal type als metrische gegevens. Voor beeld: choose ChecksFailedPercent (preview)
4. Alle dimensies die van toepassing zijn op basis van de metrische gegevens worden weer gegeven.  Kies de dimensie naam en dimensie waarde. Voor beeld: Kies bron adres en geef het IP-adres op van alle bronnen die zijn betrokken bij de verbindings monitor resource die u hebt gekozen in stap 1
5. Kies in logica voor waarschuwingen:
   1. Voorwaarde type: statisch
   2. Voor waarde en drempel waarde
   3. Aggregatie granulatie en frequentie van evaluatie: verbindings monitor (preview) gegevens elke 1 minuut bijwerken.
6.  Kies in acties uw actie groep
7. Waarschuwings Details opgeven
8. Waarschuwings regel maken

   ![Waarschuwingen](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>Stap 5: problemen in uw netwerk vaststellen

Met verbindings monitor kunt u problemen vaststellen die overeenkomen met de bron van de verbindings monitor en in uw netwerk. Problemen in uw hybride netwerk worden gedetecteerd door de Log Analytics agents die u in stap 1 hebt geïnstalleerd en problemen in Azure worden gedetecteerd door de Network Watcher-extensie.  Problemen in het hybride netwerk worden weer gegeven op de pagina Diagnostische gegevens en problemen in azure-netwerk worden weer gegeven in de netwerk topologie.

Voor netwerken met on-premises Vm's als bronnen detecteren we het volgende:

* Time-out van aanvraag
* Het eind punt is niet omgezet door DNS: tijdelijk of persistent. De URL is ongeldig.
* Geen hosts gevonden.
* De bron kan geen verbinding maken met de bestemming. Doel niet bereikbaar via ICMP.
* Certificaat verwant probleem-client certificaat dat is vereist voor verificatie van de agent, de herlocatie lijst van het certificaat is niet toegankelijk, de hostnaam van het eind punt komt niet overeen met de naam van het onderwerp of de alternatieve onderwerpnaam van het certificaat, het basis certificaat ontbreekt in het Archief voor vertrouwde certificerings instanties van de bron computer, het SSL-certificaat is verlopen/ongeldig/ingetrokken,

Voor netwerken met Azure-Vm's zijn bronnen te vinden:

* Agent problemen: de agent is gestopt, de DNS-omzetting is mislukt, er luistert geen toepassing/listener op de doel poort, de socket kan niet worden geopend
* Problemen met de VM-status: starten, stoppen, gestopt, toewijzing ongedaan maakt, toewijzing ongedaan gemaakt, opnieuw opstarten, niet toegewezen
* Ontbrekende ARP-tabel vermelding
* Verkeer geblokkeerd vanwege lokale firewall problemen, NSG-regels
* VNET-gateway: er ontbreken routes, de tunnel tussen twee gateways is losgekoppeld of ontbreekt of de tweede gateway is niet gevonden door de tunnel, geen peering-informatie gevonden
* Er ontbreekt een route in MS Edge.
* Verkeer gestopt vanwege systeem routes of UDR
* BGP is niet ingeschakeld op de gateway verbinding
* DIP-test omlaag op het Load Balancer
