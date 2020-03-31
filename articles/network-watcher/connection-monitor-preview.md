---
title: Verbindingsmonitor (voorbeeld) | Microsoft Documenten
description: Meer informatie over het gebruik van Connection Monitor (Preview) om netwerkcommunicatie in een gedistribueerde omgeving te monitoren.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 8f3a6f002fbebe215699c9b97a6dce63177c446f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599325"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Netwerkconnectiviteitsbewaking met verbindingsmonitor (voorbeeld)

Connection Monitor (Preview) biedt uniforme end-to-end verbindingsbewaking in Azure Network Watcher. De functie Verbindingsmonitor (Preview) ondersteunt hybride en Azure-cloudimplementaties. Network Watcher biedt hulpprogramma's voor het bewaken, diagnosticeren en weergeven van connectiviteitsgerelateerde statistieken voor uw Azure-implementaties.

Hier volgen enkele use cases voor Connection Monitor (Preview):

- Uw front-end webserver VM communiceert met een databaseserver VM in een multi-tier applicatie. U wilt de netwerkverbinding tussen de twee VM's controleren.
- U wilt dat VM's in de regio Oost-VS VM's pingen in de regio Centraal-VS en u wilt netwerklatencies in verschillende regio's vergelijken.
- Je hebt meerdere on-premises kantoorlocaties in Seattle, Washington en in Ashburn, Virginia. Uw kantoorsites maken verbinding met Office 365-URL's. Vergelijk de latencies tussen Seattle en Ashburn voor uw gebruikers van Office 365-URL's.
- Uw hybride toepassing heeft connectiviteit nodig met een Azure Storage-eindpunt. Uw on-premises site en uw Azure-toepassing maken verbinding met hetzelfde Azure Storage-eindpunt. U wilt de latencies van de on-premises site vergelijken met de latencies van de Azure-toepassing.
- U wilt de connectiviteit controleren tussen uw on-premises opstellingen en de Azure VM's die uw cloudtoepassing hosten.

In de previewfase combineert Connection Monitor de beste van twee functies: de functie Network Watcher [Connection Monitor](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) en de functie Network Performance Monitor (NPM) [Service Connectivity Monitor.](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)

Hier volgen enkele voordelen van Verbindingsmonitor (Voorbeeld):

* Uniforme, intuïtieve ervaring voor Azure- en hybride bewakingsbehoeften
* Bewaking van connectiviteit tussen verschillende regio's en -werkruimtes
* Hogere indringende frequenties en beter inzicht in netwerkprestaties
* Sneller waarschuwen voor uw hybride implementaties
* Ondersteuning voor connectiviteitscontroles die zijn gebaseerd op HTTP, TCP en ICMP 
* Ondersteuning voor metrische gegevens en Logboekanalyse voor zowel Azure- als niet-Azure-testinstellingen

![Diagram met de interactie van Verbindingsmonitor met Azure VM's, niet-Azure-hosts, eindpunten en gegevensopslaglocaties](./media/connection-monitor-2-preview/hero-graphic.png)

Voer de volgende stappen uit om verbindingsmonitor (voorbeeld) voor controle te gebruiken: 

1. Installeer bewakingsagenten.
1. Schakel Network Watcher in op uw abonnement.
1. Maak een verbindingsmonitor.
1. Gegevensanalyse en waarschuwingen instellen.
1. Diagnose problemen in uw netwerk.

In de volgende secties vindt u meer informatie over deze stappen.

## <a name="install-monitoring-agents"></a>Bewakingsagents installeren

Connection Monitor is afhankelijk van lichtgewicht uitvoerbare bestanden om connectiviteitscontroles uit te voeren.  Het ondersteunt connectiviteitscontroles van zowel Azure-omgevingen als on-premises omgevingen. Het uitvoerbare bestand dat u gebruikt, is afhankelijk van de vraag of uw vm wordt gehost op Azure of on-premises.

### <a name="agents-for-azure-virtual-machines"></a>Agents voor virtuele Azure-machines

Als u wilt dat de verbindingsmonitor uw Azure VM's als bewakingsbronnen herkent, installeert u de extensie voor virtuele machine network watcher agent erop. Deze extensie is ook bekend als de *Network Watcher extensie*. Azure virtuele machines vereisen de extensie om end-to-end monitoring en andere geavanceerde functionaliteit te activeren. 

U de uitbreiding Network Watcher installeren wanneer u [een VM maakt.](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm) U ook de Network Watcher-extensie voor [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) en [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)afzonderlijk installeren, configureren en oplossen.

Regels voor een netwerkbeveiligingsgroep (NSG) of firewall kunnen de communicatie tussen de bron en de bestemming blokkeren. Connection Monitor detecteert dit probleem en toont het als een diagnostisch bericht in de topologie. Als u verbindingsbewaking wilt inschakelen, moet u ervoor zorgen dat NSG- en firewallregels pakketten via TCP of ICMP tussen de bron en de bestemming toestaan.

### <a name="agents-for-on-premises-machines"></a>Agenten voor on-premises machines

Installeer de Log Analytics-agent op de machines om ervoor te zorgen dat Connection Monitor uw on-premises machines herkent als bronnen voor bewaking. Schakel vervolgens de oplossing netwerkprestatiemeter in. Deze agents zijn gekoppeld aan Log Analytics-werkruimten, dus u moet de werkruimte-id en primaire sleutel instellen voordat de agents kunnen beginnen met het controleren.

Zie [Azure Monitor-extensie voor virtuele machines voor Windows als](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)u de agent Log Analytics voor Windows-machines wilt installeren.

Als het pad firewalls of virtuele netwerkapparaten (NVA's) bevat, zorg er dan voor dat de bestemming bereikbaar is.

## <a name="enable-network-watcher-on-your-subscription"></a>Network Watcher inschakelen bij uw abonnement

Alle abonnementen met een virtueel netwerk zijn ingeschakeld met Network Watcher. Wanneer u een virtueel netwerk in uw abonnement maakt, wordt Network Watcher automatisch ingeschakeld in de regio en het abonnement van het virtuele netwerk. Deze automatische in- en inschakelen heeft geen invloed op uw resources of brengt kosten met zich mee. Zorg ervoor dat Network Watcher niet expliciet is uitgeschakeld bij uw abonnement. 

Zie [Netwerkwatcher inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)voor meer informatie.

## <a name="create-a-connection-monitor"></a>Een verbindingsmonitor maken 

Connection Monitor bewaakt de communicatie op regelmatige tijdstippen. Het informeert u over veranderingen in bereikbaarheid en latentie. U ook de huidige en historische netwerktopologie tussen bronagents en doeleindpunten controleren.

Bronnen kunnen Azure VM's of on-premises machines zijn met een geïnstalleerde bewakingsagent. Doeleindpunten kunnen Office 365-URL's, Dynamics 365-URL's, aangepaste URL's, Azure VM-bron-id's, IPv4, IPv6, FQDN of een domeinnaam zijn.

### <a name="access-connection-monitor-preview"></a>Access Connection Monitor (Preview)

1. Ga op de startpagina van de Azure-portal naar **Network Watcher**.
1. Selecteer aan de linkerkant in de sectie **Controle** de optie **Verbindingsmonitor (Voorbeeld)**.
1. U ziet alle verbindingsmonitors die zijn gemaakt in verbindingsmonitor (Voorbeeld). Als u de verbindingsmonitors wilt zien die zijn gemaakt in de klassieke ervaring van Verbindingsmonitor, gaat u naar het tabblad **Verbindingsmonitor.**

    ![Schermafbeelding van verbindingsmonitors die zijn gemaakt in verbindingsmonitor (voorbeeld)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Een verbindingsmonitor maken

In verbindingsmonitors die u in Verbindingsmonitor (Voorbeeld) maakt, u zowel on-premises machines als Azure VM's als bronnen toevoegen. Deze verbindingsmonitors kunnen ook de connectiviteit met eindpunten bewaken. De eindpunten kunnen op Azure of een andere URL of IP zijn.

Verbindingsmonitor (voorbeeld) bevat de volgende entiteiten:

* **Verbindingsmonitorbron** – Een regiospecifieke Azure-bron. Alle volgende entiteiten zijn eigenschappen van een verbindingsmonitorbron.
* **Eindpunt** : een bron of bestemming die deelneemt aan connectiviteitscontroles. Voorbeelden van eindpunten zijn Azure VM's, on-premises agents, URL's en IP's.
* **Testconfiguratie** – Een protocolspecifieke configuratie voor een test. Op basis van het door u gekozen protocol u de poort, drempels, testfrequentie en andere parameters definiëren.
* **Testgroep** : de groep die broneindpunten, doeleindpunten en testconfiguraties bevat. Een verbindingsmonitor kan meer dan één testgroep bevatten.
* **Test** : de combinatie van een broneindpunt, doeleindpunt en testconfiguratie. Een test is het meest gedetailleerde niveau waarop bewakingsgegevens beschikbaar zijn. De bewakingsgegevens omvatten het percentage controles dat is mislukt en de retourtijd (RTT).

 ![Diagram met een verbindingsmonitor waarin de relatie tussen testgroepen en tests wordt gedefinieerd](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Een verbindingsmonitor maken vanuit de Azure-portal

Voer de volgende stappen uit om een verbindingsmonitor te maken vanuit de Azure-portal:

1. Selecteer in het dashboard **Verbindingsmonitor (Voorvertoning)** in de linkerbovenhoek de optie **Maken**.
1. Voer op het tabblad **Basisbeginselen** gegevens in voor uw verbindingsmonitor:
   * **Naam van verbindingsmonitor** : voeg de naam van uw verbindingsmonitor toe. Gebruik de standaardnaamgevingsregels voor Azure-resources.
   * **Abonnement** : kies een abonnement voor uw verbindingsmonitor.
   * **Regio** – Kies een regio voor uw verbindingsmonitor. U alleen de bron-VM's selecteren die in deze regio zijn gemaakt.
   * **Werkruimteconfiguratie** - Uw werkruimte bevat uw bewakingsgegevens. U een aangepaste werkruimte of de standaardwerkruimte gebruiken. 
       * Als u de standaardwerkruimte wilt gebruiken, schakelt u het selectievakje in. 
       * Als u een aangepaste werkruimte wilt kiezen, schakelt u het selectievakje uit. Kies vervolgens het abonnement en de regio voor uw aangepaste werkruimte. 
1. Selecteer **Volgende: Testgroepen**onder aan het tabblad .

   ![Schermafbeelding van het tabblad Basisbeginselen in verbindingsmonitor](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Selecteer op het tabblad **Testgroepen** de optie **+ Testgroep**. Zie [Testgroepen maken in Verbindingsmonitor](#create-test-groups-in-a-connection-monitor)als u uw testgroepen wilt instellen. 
1. Selecteer Onder aan het tabblad **Volgende: Controleren + maken** om uw verbindingsmonitor te bekijken.

   ![Schermafbeelding van het tabblad Testgroepen en het deelvenster waarin u testgroepdetails toevoegt](./media/connection-monitor-2-preview/create-tg.png)

1. Bekijk op het tabblad **Controleren + maken** de basisgegevens en testgroepen voordat u de verbindingsmonitor maakt. Als u de verbindingsmonitor moet bewerken:
   * Als u basisdetails wilt bewerken, selecteert u het potloodpictogram.
   * Als u een testgroep wilt bewerken, selecteert u deze.

   > [!NOTE] 
   > Op het tabblad **Controleren + maken** worden de kosten per maand weergegeven tijdens de previewfase van de verbindingsmonitor. Momenteel worden in de kolom **HUIDIGE KOSTEN** geen kosten weergegeven. Wanneer verbindingsmonitor algemeen beschikbaar wordt, wordt in deze kolom een maandelijkse kosten weergegeven. 
   > 
   > Zelfs in de previewfase van de verbindingsmonitor zijn de innamekosten van Log Analytics van toepassing.

1. Wanneer u klaar bent om de verbindingsmonitor te maken, selecteert u onder aan het tabblad **Controleren + maken** de optie **Maken**.

   ![Schermafbeelding van verbindingsmonitor met het tabblad Controleren + maken](./media/connection-monitor-2-preview/review-create-cm.png)

Met verbindingsmonitor (voorbeeld) wordt de verbindingsmonitorbron op de achtergrond gemaakt.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Een verbindingsmonitor maken met ARMClient

Gebruik de volgende code om een verbindingsmonitor te maken met ARMClient.

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

address: '\&lt;FQDN of your on-premises agent'

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

Hier is de opdracht implementatie:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Testgroepen maken in een verbindingsmonitor

Elke testgroep in een verbindingsmonitor bevat bronnen en bestemmingen die worden getest op netwerkparameters. Ze worden getest op het percentage controles dat mislukt en de RTT ten opzichte van testconfiguraties.

Als u vanuit de Azure-portal een testgroep wilt maken in een verbindingsmonitor, geeft u waarden op voor de volgende velden:

* **Testgroep uitschakelen:** u dit veld selecteren om de controle uit te schakelen voor alle bronnen en bestemmingen die de testgroep opgeeft. Deze selectie wordt standaard gewist.
* **Naam** – Geef uw testgroep een naam.
* **Bronnen** : u zowel Azure VM's als on-premises machines opgeven als bronnen als agents erop zijn geïnstalleerd. Zie [Bewakingsagents installeren](#install-monitoring-agents)als u een agent voor uw bron wilt installeren.
   * Als u Azure-agents wilt kiezen, selecteert u het tabblad **Azure Agents.** Hier ziet u alleen VM's die zijn gekoppeld aan het gebied dat u hebt opgegeven toen u de verbindingsmonitor hebt gemaakt. Standaard worden VM's gegroepeerd in het abonnement waartoe ze behoren. Deze groepen zijn ingestort. 
   
       U inzoomen van het abonnementsniveau naar andere niveaus in de hiërarchie:

      **Abonnementsbrongroepen** > **Resource groups** > **VNETs** > **Subnets** > **VM's met agents**

      U ook de waarde van de **groep** per veld wijzigen om de boom vanaf een ander niveau te starten. Als u bijvoorbeeld groepeert op virtueel netwerk, ziet u de VM's met agents in de hiërarchie **VNETs-vm's** > **Subnets** > **met agents**.

      ![Schermafbeelding van verbindingsmonitor met het deelvenster Bronnen toevoegen en het tabblad Azure Agents](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Als u on-premises agents wilt kiezen, selecteert u het tabblad **Niet-Azure Agents.** Standaard worden agents gegroepeerd in werkruimten per regio. Al deze werkruimten hebben de oplossing Network Performance Monitor geconfigureerd. 
   
       Als u netwerkprestatiemeter aan uw werkruimte wilt toevoegen, haalt u deze op van [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) Zie [Bewakingsoplossingen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)voor informatie over het toevoegen van netwerkprestatiemeter. 
   
       In de weergave **Verbindingsmonitor maken** wordt op het tabblad **Basisbeginselen** het standaardgebied geselecteerd. Als u de regio wijzigt, u agents kiezen uit werkruimten in de nieuwe regio. U ook de waarde van de **groep per** veld wijzigen in groep op subnetten.

      ![Schermafbeelding van verbindingsmonitor met het deelvenster Bronnen toevoegen en het tabblad Niet-Azure Agents](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Als u de Azure- en niet-Azure-agents wilt controleren die u hebt geselecteerd, gaat u naar het tabblad **Controleren.**

      ![Schermafbeelding van verbindingsmonitor met het deelvenster Bronnen toevoegen en het tabblad Controleren](./media/connection-monitor-2-preview/review-sources.png)

   * Wanneer u bronnen hebt ingesteld, selecteert u onder aan het deelvenster **Bronnen toevoegen** de optie **Gereed**.

* **Bestemmingen** : u de connectiviteit met Azure VM's of een eindpunt (een openbaar IP,URL of FQDN) controleren door ze op te geven als bestemmingen. In één testgroep u Azure VM's, Office 365-URL's, Dynamics 365-URL's en aangepaste eindpunten toevoegen.

    * Als u Azure VM's als bestemmingen wilt kiezen, selecteert u het tabblad **Azure VM's.** Standaard worden de Azure VM's gegroepeerd in een abonnementshiërarchie die zich in dezelfde regio bevindt die u hebt geselecteerd in de weergave **Verbindingsmonitor maken,** op het tabblad **Basisbeginselen.** U de regio wijzigen en Azure-VM's kiezen uit het nieuw geselecteerde gebied. Vervolgens u inzoomen van abonnementsniveau naar andere niveaus in de hiërarchie, zoals het Azure Agents-niveau.

       ![Schermafbeelding van het deelvenster Bestemmingen toevoegen met het tabblad Azure VM's](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Schermafbeelding van het deelvenster Bestemmingen toevoegen met het abonnementsniveau](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Als u eindpunten als bestemmingen wilt kiezen, selecteert u het tabblad **Eindpunten.** De lijst met eindpunten bevat URL's voor Office 365-tests en Dynamics 365-test-URL's, gegroepeerd op naam. Naast deze eindpunten u een eindpunt kiezen dat is gemaakt in andere testgroepen in dezelfde verbindingsmonitor. 
    
        Als u een nieuw eindpunt wilt toevoegen, selecteert u in de rechterbovenhoek **+ Eindpunten**. Geef vervolgens een eindpuntnaam en URL, IP of FQDN op.

       ![Schermafbeelding van waar eindpunten als bestemmingen kunnen worden toegevoegd in verbindingsmonitor](./media/connection-monitor-2-preview/add-endpoints.png)

    * Als u de Azure VM's en eindpunten wilt controleren die u hebt gekozen, selecteert u het tabblad **Controleren.**
    * Wanneer u klaar bent met het kiezen van bestemmingen, selecteert u **Gereed**.

* **Testconfiguraties** : u testconfiguraties in een testgroep koppelen. De Azure-portal staat slechts één testconfiguratie per testgroep toe, maar u ARMClient gebruiken om meer toe te voegen.

    * **Naam** – Geef de testconfiguratie een naam.
    * **Protocol** : kies TCP, ICMP of HTTP. Als u HTTP wilt wijzigen in HTTPS, selecteert u **HTTP** als protocol en selecteert u **443** als poort.
        * **Netwerktestconfiguratie maken:** dit selectievakje wordt alleen weergegeven als u **HTTP** inschakelt in het veld **Protocol.** Selecteer dit vak om een andere testconfiguratie te maken die dezelfde bronnen en bestemmingen gebruikt die u elders in uw configuratie hebt opgegeven. De nieuw gemaakte testconfiguratie heeft de naam `<the name of your test configuration>_networkTestConfig`.
        * **Traceroute uitschakelen** : dit veld is van toepassing op testgroepen waarvan het protocol TCP of ICMP is. Selecteer dit vak om te voorkomen dat bronnen topologie en hop-by-hop RTT ontdekken.
    * **Bestemmingspoort** – U dit veld aanpassen met een bestemmingspoort naar keuze.
    * **Testfrequentie** : gebruik dit veld om te kiezen hoe vaak bronnen bestemmingen pingen op het door u opgegeven protocol en poort. U kiezen voor 30 seconden, 1 minuut, 5 minuten, 15 minuten of 30 minuten. Bronnen testen de connectiviteit met bestemmingen op basis van de waarde die u kiest.  Als u bijvoorbeeld 30 seconden selecteert, controleren bronnen de verbinding met de bestemming ten minste eenmaal in een periode van 30 seconden.
    * **Succesdrempel** : u drempelwaarden instellen voor de volgende netwerkparameters:
       * **Controles zijn mislukt:** stel het percentage controles in dat kan mislukken wanneer bronnen de verbinding met bestemmingen controleren met behulp van de criteria die u hebt opgegeven. Voor TCP- of ICMP-protocol kan het percentage mislukte controles worden gelijkgesteld met het percentage pakketverlies. Voor http-protocol vertegenwoordigt dit veld het percentage HTTP-aanvragen dat geen antwoord heeft ontvangen.
       * **Retourtijd** – Stel de RTT in milliseconden in voor hoe lang bronnen kunnen duren om verbinding te maken met de bestemming via de testconfiguratie.
    
       ![Schermafbeelding van de plaats waar u een testconfiguratie in stelt in verbindingsmonitor](./media/connection-monitor-2-preview/add-test-config.png)

Alle bronnen, bestemmingen en testconfiguraties die u aan een testgroep toevoegt, worden opgesplitst in afzonderlijke tests. Hier is een voorbeeld van hoe bronnen en bestemmingen worden opgesplitst:

* Testgroep: TG1
* Bronnen: 3 (A, B, C)
* Bestemmingen: 2 (D, E)
* Testconfiguraties: 2 (Config 1, Config 2)
* Totaal aantal gemaakte tests: 12

| Testnummer | Bron | Doel | Testconfiguratie |
| --- | --- | --- | --- |
| 1 | A | D | Config 1 |
| 2 | A | D | Config 2 |
| 3 | A | E | Config 1 |
| 4 | A | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a>Schaallimieten

Verbindingsmonitors hebben de volgende schaallimieten:

* Maximale aansluitmonitoren per abonnement per regio: 100
* Maximale testgroepen per verbindingsmonitor: 20
* Maximale bronnen en bestemmingen per verbindingsmonitor: 100
* Maximale testconfiguraties per verbindingsmonitor: 
    * 20 via ARMClient
    * 2 via de Azure-portal

## <a name="analyze-monitoring-data-and-set-alerts"></a>Monitoringgegevens analyseren en waarschuwingen instellen

Nadat u een verbindingsmonitor hebt gemaakt, controleren bronnen de verbinding met bestemmingen op basis van uw testconfiguratie.

### <a name="checks-in-a-test"></a>Controles in een test

Op basis van het protocol dat u in de testconfiguratie hebt gekozen, voert Connection Monitor (Preview) een reeks controles uit voor het bron-doelpaar. De controles worden uitgevoerd volgens de testfrequentie die u hebt gekozen.

Als u HTTP gebruikt, berekent de service het aantal HTTP-antwoorden dat een antwoordcode heeft geretourneerd. Het resultaat bepaalt het percentage mislukte controles. Om RTT te berekenen, meet de service de tijd tussen een HTTP-gesprek en het antwoord.

Als u TCP of ICMP gebruikt, berekent de service het percentage pakketverlies om het percentage mislukte controles te bepalen. Om RTT te berekenen, meet de service de tijd die nodig is om de bevestiging (ACK) te ontvangen voor de pakketten die zijn verzonden. Als u traceroutegegevens hebt ingeschakeld voor uw netwerktests, u hop-by-hopverlies en latentie voor uw on-premises netwerk zien.

### <a name="states-of-a-test"></a>Staten van een test

Op basis van de gegevens die de controles retourneren, kunnen tests de volgende statussen hebben:

* **Pass** : werkelijke waarden voor het percentage mislukte controles en RTT liggen binnen de opgegeven drempelwaarden.
* **Mislukt** : werkelijke waarden voor het percentage mislukte controles of RTT overschreden de opgegeven drempelwaarden. Als er geen drempelwaarde is opgegeven, bereikt een test de status Fail wanneer het percentage mislukte controles 100 is.
* **Waarschuwing** : er zijn geen criteria opgegeven voor het percentage mislukte controles. Bij afwezigheid van opgegeven criteria wijst Connection Monitor (Preview) automatisch een drempelwaarde toe. Wanneer die drempel wordt overschreden, wordt de teststatus gewijzigd in Waarschuwing.

### <a name="data-collection-analysis-and-alerts"></a>Gegevensverzameling, analyse en waarschuwingen

De gegevens die Connection Monitor (Preview) verzamelt, worden opgeslagen in de werkruimte Log Analytics. U stelt deze werkruimte in toen u de verbindingsmonitor hebt gemaakt. 

Bewakingsgegevens zijn ook beschikbaar in Azure Monitor Metrics. U Log Analytics gebruiken om uw bewakingsgegevens zo lang te bewaren als u wilt. Azure Monitor slaat standaard slechts 30 dagen statistieken op. 

U [waarschuwingen op basis van metrische gegevens instellen op basis van de gegevens.](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)

#### <a name="monitoring-dashboards"></a>Dashboards bewaken

Op de bewakingsdashboards ziet u een lijst met de verbindingsmonitors die u openen voor uw abonnementen, regio's, tijdstempels, bronnen en doeltypen.

Wanneer u naar Connection Monitor (Preview) van Network Watcher gaat, u gegevens bekijken op:

* **Verbindingsmonitor** : lijst van alle verbindingsmonitors die zijn gemaakt voor uw abonnementen, regio's, tijdstempels, bronnen en doeltypen. Deze weergave is de standaardweergave.
* **Testgroepen** : lijst van alle testgroepen die zijn gemaakt voor uw abonnementen, regio's, tijdstempels, bronnen en doeltypen. Deze testgroepen worden niet gefilterd op verbindingsmonitors.
* **Test:** lijst van alle tests die worden uitgevoerd voor uw abonnementen, regio's, tijdstempels, bronnen en doeltypen. Deze tests worden niet gefilterd op verbindingsmonitors of testgroepen.

In de volgende afbeelding worden de drie gegevensweergaven aangegeven met pijl 1.

Op het dashboard u elke verbindingsmonitor uitbreiden om de testgroepen te zien. Vervolgens u elke testgroep uitbreiden om de tests te zien die erin worden uitgevoerd. 

U een lijst filteren op basis van:

* **Filters op het hoogste niveau** : kies abonnementen, regio's, tijdstempelbronnen en doeltypen. Zie vak 2 in de volgende afbeelding.
* **Filters op basis van status** : filter op de status van de verbindingsmonitor, testgroep of test. Zie pijl 3 in de volgende afbeelding.
* **Aangepaste filters** : kies **Alles selecteren** om een algemene zoekopdracht uit te zoeken. Als u wilt zoeken op een specifieke entiteit, selecteert u in de vervolgkeuzelijst. Zie pijl 4 in de volgende afbeelding.

![Schermafbeelding van het filteren van weergaven van verbindingsmonitors, testgroepen en tests in Verbindingsmonitor (voorbeeld)](./media/connection-monitor-2-preview/cm-view.png)

Als u bijvoorbeeld alle tests in verbindingsmonitor (voorbeeld) wilt bekijken waarbij het bron-IP 10.192.64.56 is:
1. De weergave wijzigen in **Testen**.
1. Typ in het zoekveld *10.192.64.56*
1. Selecteer **Bronnen**in de vervolgkeuzelijst .

Als u alleen mislukte tests wilt weergeven in de verbindingsmonitor (voorbeeld) waarbij het bron-IP 10.192.64.56 is:
1. De weergave wijzigen in **Testen**.
1. Selecteer **Fail**.
1. Typ in het zoekveld *10.192.64.56*
1. Selecteer **Bronnen**in de vervolgkeuzelijst .

Ga als volgt te werk om alleen mislukte tests weer te geven in de verbindingsmonitor (Preview) waar de bestemming is outlook.office365.com:
1. Weergave wijzigen in **Testen**.
1. Selecteer **Fail**.
1. Voer in het zoekveld *outlook.office365.com* in
1. Selecteer Bestemmingen in de vervolgkeuzelijst **.**

   ![Schermafbeelding van een weergave die is gefilterd om alleen mislukte tests voor de Outlook.Office365.com-bestemming weer te geven](./media/connection-monitor-2-preview/tests-view.png)

Ga als volgt te werk om de trends in RTT en het percentage mislukte controles voor een verbindingsmonitor weer te geven:
1. Selecteer de verbindingsmonitor die u wilt onderzoeken. Standaard worden de bewakingsgegevens georganiseerd per testgroep.

   ![Schermafbeelding met statistieken voor een verbindingsmonitor, weergegeven per testgroep](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Kies de testgroep die u wilt onderzoeken.

   ![Schermafbeelding van de plaats waar u een testgroep moet selecteren](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    U ziet de top vijf mislukte tests van uw testgroep, op basis van de RTT of het percentage mislukte controles. Voor elke test ziet u de RTT- en trendlijnen voor het percentage mislukte controles.
1. Selecteer een test in de lijst of kies een andere test om te onderzoeken. Voor uw tijdsinterval en het percentage mislukte controles ziet u drempelwaarde en werkelijke waarden. Voor RTT ziet u de waarden voor drempelwaarde, gemiddelde, minimum en maximum.

   ![Schermafbeelding van de resultaten van een test voor RTT en het percentage mislukte controles](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Wijzig het tijdsinterval om meer gegevens weer te geven.
1. Wijzig de weergave om bronnen, bestemmingen of testconfiguraties te bekijken. 
1. Kies een bron op basis van mislukte tests en onderzoek de top vijf mislukte tests. Kies bijvoorbeeld **Weergave op** > **bronnen** en **Weergave op** > **bestemmingen** om de relevante tests in de verbindingsmonitor te onderzoeken.

   ![Schermafbeelding van prestatiestatistieken voor de vijf beste mislukte tests](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Ga als volgt te werk om de trends in RTT en het percentage mislukte controles voor een testgroep te bekijken:

1. Selecteer de testgroep die u wilt onderzoeken. 

    Standaard worden de bewakingsgegevens gerangschikt op bronnen, bestemmingen en testconfiguraties (tests). Later u de weergave wijzigen van testgroepen naar bronnen, bestemmingen of testconfiguraties. Kies vervolgens een entiteit om de top vijf mislukte tests te onderzoeken. Wijzig bijvoorbeeld de weergave naar bronnen en bestemmingen om de relevante tests in de geselecteerde verbindingsmonitor te onderzoeken.
1. Kies de test die u wilt onderzoeken.

   ![Schermafbeelding van de plaats waar u een test selecteren](./media/connection-monitor-2-preview/tg-drill.png)

    Voor uw tijdsinterval en voor uw percentage mislukte controles ziet u drempelwaarden en werkelijke waarden. Voor RTT ziet u waarden voor drempelwaarde, gemiddelde, minimum en maximum. U ziet ook ontslagen waarschuwingen voor de test die u hebt geselecteerd.
1. Wijzig het tijdsinterval om meer gegevens weer te geven.

Ga als volgt te werk om de trends in RTT en het percentage mislukte controles voor een test te bekijken:
1. Selecteer de bron-, bestemmings- en testconfiguratie die u wilt onderzoeken.

    Voor uw tijdsinterval en voor het percentage mislukte controles ziet u drempelwaarden en werkelijke waarden. Voor RTT ziet u waarden voor drempelwaarde, gemiddelde, minimum en maximum. U ziet ook ontslagen waarschuwingen voor de test die u hebt geselecteerd.

   ![Schermafbeelding van statistieken voor een test](./media/connection-monitor-2-preview/test-drill.png)

1. Als u de netwerktopologie wilt bekijken, selecteert u **Topologie**.

   ![Schermafbeelding van het tabblad Netwerktopologie](./media/connection-monitor-2-preview/test-topo.png)

1. Als u de geïdentificeerde problemen wilt zien, selecteert u in de topologie elke hop in het pad. (Deze hop zijn Azure-bronnen.) Deze functionaliteit is momenteel niet beschikbaar voor on-premises netwerken.

   ![Schermafbeelding van een geselecteerde hopkoppeling op het tabblad Topologie](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Query's aanmelden in Log Analytics

Gebruik Log Analytics om aangepaste weergaven van uw bewakingsgegevens te maken. Alle gegevens die de gebruikersinterface weergeeft, zijn afkomstig van Log Analytics. U op interactieve wijze gegevens analyseren in de repository. Correleer de gegevens van Agent Health of andere oplossingen die zijn gebaseerd in Log Analytics. Exporteer de gegevens naar Excel of Power BI of maak een deelbare koppeling.

#### <a name="metrics-in-azure-monitor"></a>Metrische gegevens in Azure Monitor

In verbindingsmonitors die vóór de ervaring van de verbindingsmonitor (Preview) zijn gemaakt, zijn alle vier de statistieken beschikbaar: % Probes failed, AverageRoundtripMs, ChecksFailedPercent (Preview) en RoundTripTimeMs (Preview). In verbindingsmonitors die zijn gemaakt in de ervaring van de verbindingsmonitor (Preview), zijn gegevens alleen beschikbaar voor de statistieken die zijn getagd met *(Voorbeeld).*

![Schermafbeelding van statistieken in verbindingsmonitor (voorbeeld)](./media/connection-monitor-2-preview/monitor-metrics.png)

Wanneer u statistieken gebruikt, stelt u het brontype in als Microsoft.Network/networkWatchers/connectionMonitors

| Gegevens | Weergavenaam | Eenheid | Aggregatietype | Beschrijving | Dimensies |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % Sondes mislukt | Percentage | Average | Percentage detecties van connectiviteitissen is mislukt. | Geen afmetingen |
| Gemiddelde RoundtripMs | Avg. Retourtijd (ms) | Milliseconden | Average | Gemiddelde NETWERK RTT voor connectiviteitsbewaking sondes verzonden tussen bron en bestemming. |             Geen afmetingen |
| Checksfailedpercent (preview) | % controles mislukt (voorbeeld) | Percentage | Average | Percentage mislukte controles voor een test. | ConnectionMonitorResourceid <br>SourceAddress <br>Sourcename <br>SourceResourceId <br>SourceType <br>Protocol <br>Bestemmingsadres <br>Bestemmingsnaam <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestgroepNaam <br>Naam testconfiguratie <br>Regio |
| RoundTripTimeMs (voorbeeld) | Retourtijd (ms) (voorbeeld) | Milliseconden | Average | RTT voor controles tussen bron en bestemming. Deze waarde wordt niet gemiddeld. | ConnectionMonitorResourceid <br>SourceAddress <br>Sourcename <br>SourceResourceId <br>SourceType <br>Protocol <br>Bestemmingsadres <br>Bestemmingsnaam <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestgroepNaam <br>Naam testconfiguratie <br>Regio |

#### <a name="metric-alerts-in-azure-monitor"></a>Metrische waarschuwingen in Azure Monitor

Een waarschuwing maken in Azure Monitor:

1. Kies de verbindingsmonitorbron die u hebt gemaakt in Verbindingsmonitor (Voorbeeld).
1. Controleer of **Metric** wordt weergegeven als signaaltype voor de verbindingsmonitor.
1. Selecteer in **Voorwaarde toevoegen**voor de **signaalnaam**de optie **ChecksFailedPercent(Preview)** of **RoundTripTimeMs(Preview).**
1. Kies Voor **Signaaltype**de optie **Metrische gegevens**. Selecteer bijvoorbeeld **ChecksFailedPercent(Preview).**
1. Alle dimensies voor de statistiek worden weergegeven. Kies de dimensienaam en dimensiewaarde. Selecteer bijvoorbeeld **Bronadres** en voer vervolgens het IP-adres van een bron in uw verbindingsmonitor in.
1. Vul in **Waarschuwingslogica**de volgende details in:
   * **Voorwaardetype**: **Statisch**.
   * **Conditie** en **drempelwaarde**.
   * **Aggregatie granulariteit en frequentie van evaluatie:** Connection Monitor (Preview) werkt gegevens elke minuut bij.
1. Kies **in Acties**uw actiegroep.
1. Geef waarschuwingsgegevens op.
1. Maak de waarschuwingsregel.

   ![Schermafbeelding van het regelgebied maken in Azure Monitor; 'Bronadres' en 'Naam broneindpunt' worden gemarkeerd](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Problemen in uw netwerk diagnosticeren

Met Connection Monitor (Preview) u problemen in uw verbindingsmonitor en uw netwerk diagnosticeren. Problemen in uw hybride netwerk worden gedetecteerd door de Log Analytics-agents die u eerder hebt geïnstalleerd. Problemen in Azure worden gedetecteerd door de Network Watcher-extensie. 

U problemen in het Azure-netwerk weergeven in de netwerktopologie.

Voor netwerken waarvan de bronnen on-premises VM's zijn, kunnen de volgende problemen worden gedetecteerd:

* Aanvraag time-out.
* Eindpunt niet opgelost door DNS – tijdelijk of blijvend. URL ongeldig.
* Geen gastheren gevonden.
* Bron kan geen verbinding maken met de bestemming. Doel niet bereikbaar via ICMP.
* Problemen met certificaten: 
    * Clientcertificaat vereist om agent te verifiëren. 
    * De lijst met certificaatverplaatsingen is niet toegankelijk. 
    * De hostnaam van het eindpunt komt niet overeen met de alternatieve naam van het certificaat of het onderwerp. 
    * Rootcertificaat ontbreekt in het archief lokale computervertrouwde certificeringsinstanties van de bron. 
    * SSL-certificaat is verlopen, ongeldig, ingetrokken of onverenigbaar.

Voor netwerken waarvan de bronnen Azure VM's zijn, kunnen de volgende problemen worden gedetecteerd:

* Agent problemen:
    * De agent is gestopt.
    * Mislukte DNS-resolutie.
    * Geen toepassing of luisteraar luisteren op de bestemmingspoort.
    * Socket kon niet worden geopend.
* VM-statusproblemen: 
    * Starten
    * Stoppen
    * Gestopt
    * Vrijgeven
    * Toewijzing ongedaan gemaakt
    * Rebooten
    * Niet toegewezen
* ARP-tabelvermelding ontbreekt.
* Het verkeer werd geblokkeerd vanwege lokale firewallproblemen of NSG-regels.
* Problemen met virtuele netwerkgateway: 
    * Ontbrekende routes.
    * De tunnel tussen twee gateways is losgekoppeld of ontbreekt.
    * De tweede poort is niet gevonden door de tunnel.
    * Er is geen peering info gevonden.
* Route ontbrak in Microsoft Edge.
* Verkeer gestopt vanwege systeemroutes of UDR.
* BGP is niet ingeschakeld op de gatewayverbinding.
* De DIP-sonde is bij de load balancer.
