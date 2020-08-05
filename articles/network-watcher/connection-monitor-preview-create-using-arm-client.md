---
title: De preview-versie van de verbindings monitor maken-ARMClient
titleSuffix: Azure Network Watcher
description: Meer informatie over het maken van een verbindings monitor (preview) met behulp van de ARMClient.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 7d35799cd73ff4d065cb58189f2325dc4dac6840
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567936"
---
# <a name="create-a-connection-monitor-preview-using-the-armclient"></a>Een verbindings monitor (preview) maken met behulp van de ARMClient

Meer informatie over het maken van een verbindings monitor (preview) voor het bewaken van de communicatie tussen uw resources met behulp van de ARMClient. Het ondersteunt hybride en Azure-Cloud implementaties.

## <a name="before-you-begin"></a>Voordat u begint 

Bij verbindings monitors die u in verbindings monitor (preview) maakt, kunt u zowel on-premises machines als Azure-Vm's toevoegen als bronnen. Deze verbindings monitors kunnen ook de connectiviteit met eind punten bewaken. De eind punten kunnen zich op Azure of een andere URL of een ander IP-adres bevindt.

Verbindings monitor (preview) omvat de volgende entiteiten:

* **Bron van de verbindings monitor** : een regio-specifieke Azure-resource. Alle volgende entiteiten zijn eigenschappen van een verbindingscontrole resource.
* **Eind punt** – een bron of doel die deel uitmaakt van connectiviteits controles. Voor beelden van eind punten zijn Azure-Vm's, on-premises agents, Url's en Ip's.
* **Test configuratie** : een protocol-specifieke configuratie voor een test. Op basis van het protocol dat u hebt gekozen, kunt u de poort, drempel waarden, test frequentie en andere para meters definiëren.
* **Test groep** : de groep die bron-eind punten, bestemmings eindpunten en test configuraties bevat. Een verbindings monitor kan meer dan één test groep bevatten.
* **Testen** : de combi natie van een bron eindpunt, bestemmings eindpunt en test configuratie. Een test is het meest gedetailleerde niveau waarmee bewakings gegevens beschikbaar zijn. De bewakings gegevens omvatten het percentage controles dat is mislukt en de round-trip tijd (RTT).

    ![Diagram van een verbindings monitor, waarbij de relatie tussen test groepen en tests wordt gedefinieerd](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Stappen voor het maken van een voor beeld van een ARM-sjabloon

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

## <a name="description-of-properties"></a>Beschrijving van eigenschappen

* connectionMonitorName: naam van de bron van de verbindings monitor

* Subabonnement-ID van het abonnement waarvoor u de verbindings monitor wilt maken

* NW-Network Watcher Resource-ID waarin CM wordt gemaakt 

* locatie-regio waarin de verbindings monitor wordt gemaakt

* Eindpunten
    * naam: unieke naam voor elk eind punt
    * resourceId: voor Azure-eind punten verwijst de resource-ID naar de resource-ID van de Azure Resource Manager (ARM) voor virtuele machines. Voor niet-Azure-eind punten verwijst de resource-ID naar de ARM-Resource-ID voor de Log Analytics werk ruimte die is gekoppeld aan niet-Azure-agents.
    * adres: alleen van toepassing als de resource-ID niet is opgegeven of als de resource-ID Log Analytics werk ruimte is. Als dit wordt gebruikt in combi natie met Log Analytics Resource-ID, verwijst dit naar de FQDN van de agent die kan worden gebruikt voor de bewaking. Als u zonder Resource-ID gebruikt, kan dit de URL of het IP-adres van een openbaar eind punt zijn.
    * filter: voor niet-Azure-eind punten gebruikt u filter om agents te selecteren uit Log Analytics werk ruimte die wordt gebruikt voor de bewaking van de bron van de verbindings monitor. Als er geen filters zijn ingesteld, kunnen alle agents die deel uitmaken van de Log Analytics-werk ruimte worden gebruikt voor de bewaking
        * type: Stel type in als agent adres
        * adres: Stel adres in als de FQDN van uw on-premises agent

* Test groepen
    * name: naam van de test groep.
    * testConfigurations-test configuraties op basis waarvan de bron-eind punten verbinding maken met doel eindpunten
    * bronnen: Kies uit de hierboven gemaakte eind punten. Op Azure-bron eindpunten moet Azure Network Watcher-extensie zijn geïnstalleerd en op niet-Azure gebaseerde bron-eind punten moet Log Analytics agent zijn geïnstalleerd. Zie [bewakings agenten installeren](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)voor informatie over het installeren van een agent voor uw bron.
    * doelen: Kies uit de hierboven gemaakte eind punten. U kunt de verbinding met virtuele machines van Azure of een eind punt (een openbaar IP-adres, URL of FQDN) bewaken door ze als bestemming op te geven. In één test groep kunt u Azure-Vm's, Office 365 Url's, Dynamics 365-Url's en aangepaste eind punten toevoegen.
    * uitschakelen: gebruik dit veld om de bewaking uit te scha kelen voor alle bronnen en doelen die door de test groep worden opgegeven.

* Test configuraties
    * naam: naam van de test configuratie.
    * testFrequencySec: Geef op hoe vaak bronnen worden gepingd op het protocol en de poort die u hebt opgegeven. U kunt kiezen uit 30 seconden, 1 minuut, 5 minuten, 15 minuten of 30 minuten. Bronnen testen de connectiviteit met bestemmingen op basis van de waarde die u kiest. Als u bijvoorbeeld 30 seconden selecteert, wordt de verbinding met de bestemming ten minste eenmaal in een periode van 30 seconden gecontroleerd.
    * Protocol: u kunt TCP, ICMP, HTTP of HTTPS kiezen. Afhankelijk van het protocol kunt u bepaalde configuraties van specifieke protocollen uitvoeren
        * preferHTTPS: Geef op of HTTPS via HTTP moet worden gebruikt
        * poort: Geef de doel poort van uw keuze op.
        * disableTraceRoute: dit is van toepassing op test groepen waarvan het protocol TCP of ICMP is. Het stoppen van bronnen van het detecteren van topologie en hop-by-Hop RTT wordt gestopt.
    * successThreshold-u kunt drempels instellen voor de volgende netwerk parameters:
        * checksFailedPercent: Stel het percentage controles in dat kan mislukken wanneer bronnen de connectiviteit met bestemmingen controleren met behulp van de criteria die u hebt opgegeven. Voor het TCP-of ICMP-protocol kan het percentage mislukte controles worden vergeleken met het percentage pakket verlies. Voor het HTTP-protocol vertegenwoordigt dit veld het percentage HTTP-aanvragen dat geen antwoord heeft ontvangen.
        * roundTripTimeMs: Stel de RTT in milliseconden in om te bepalen hoe lang bronnen kunnen worden verbonden met het doel via de test configuratie.

## <a name="scale-limits"></a>Schaal limieten

Verbindings monitors hebben de volgende schaal limieten:

* Maximum aantal verbindings monitors per abonnement per regio: 100
* Maximum aantal test groepen per verbindings monitor: 20
* Maximum aantal bronnen en bestemmingen per verbindings monitor: 100
* Maximum aantal test configuraties per verbindings monitor: 20 via ARMClient

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het analyseren van bewakings gegevens en het instellen van waarschuwingen](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Meer informatie [over het vaststellen van problemen in uw netwerk](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
