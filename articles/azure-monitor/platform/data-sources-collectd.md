---
title: Gegevens verzamelen van verzameld in Azure-monitor | Microsoft Documenten
description: CollectD is een open source Linux daemon die periodiek gegevens verzamelt van applicaties en systeemniveau informatie.  In dit artikel vindt u informatie over het verzamelen van gegevens van CollectD in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: b8c09d4ac5d0856eb0d448a1cabd9adc567850c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670607"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Gegevens verzamelen van verzameld op Linux-agents in Azure Monitor
[CollectD](https://collectd.org/) is een open source Linux daemon die periodiek prestatiestatistieken verzamelt van applicaties en systeemniveauinformatie. Voorbeelden van de Java Virtual Machine (JVM), MySQL Server en Nginx. In dit artikel vindt u informatie over het verzamelen van prestatiegegevens van CollectD in Azure Monitor.

Een volledige lijst van beschikbare plugins is te vinden op [Tabel van Plugins](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Overzicht van collectd](media/data-sources-collectd/overview.png)

De volgende CollectD-configuratie is opgenomen in de Log Analytics-agent voor Linux om verzamelde gegevens door te sturen naar de Log Analytics-agent voor Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Als u een versie van collectd vóór 5.5 gebruikt, gebruikt u bovendien de volgende configuratie.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

De CollectD-configuratie`write_http` gebruikt de standaardplug-in om prestatiemetrische gegevens over poort 26000 te verzenden naar de Log Analytics-agent voor Linux. 

> [!NOTE]
> Deze poort kan zo nodig worden geconfigureerd naar een aangepaste poort.

De Log Analytics-agent voor Linux luistert ook op poort 26000 naar CollectD-statistieken en converteert deze vervolgens naar Azure Monitor-schemastatistieken. Het volgende is de Log `collectd.conf`Analytics-agent voor Linux-configuratie.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>

> [!NOTE]
> Collectd standaard is ingesteld op het lezen van waarden op een [interval](https://collectd.org/wiki/index.php/Interval)van 10 seconden . Aangezien dit rechtstreeks van invloed is op het volume van gegevens die naar Azure Monitor-logboeken worden verzonden, moet u dit interval mogelijk afstemmen binnen de CollectD-configuratie om een goede balans te vinden tussen de bewakingsvereisten en de bijbehorende kosten en het gebruik voor Azure Monitor-logboeken.

## <a name="versions-supported"></a>Ondersteunde versies
- Azure Monitor ondersteunt momenteel CollectD-versie 4.8 en hoger.
- Log Analytics-agent voor Linux v1.1.0-217 of hoger is vereist voor het verzamelen van collectd-statistieken.


## <a name="configuration"></a>Configuratie
Hieronder volgen basisstappen voor het configureren van verzameling verzamelde gegevens in Azure Monitor.

1. Configureer CollectD om gegevens naar de Log Analytics-agent voor Linux te verzenden met de write_http-plug-in.  
2. Configureer de Log Analytics-agent voor Linux om naar de verzamelde gegevens op de juiste poort te luisteren.
3. Start de agent CollectD en Log Analytics opnieuw voor Linux.

### <a name="configure-collectd-to-forward-data"></a>Verzamelde configureren om gegevens door te sturen 

1. Als u verzamelde gegevens wilt doorsturen `oms.conf` naar de Log Analytics-agent voor Linux, moet u worden toegevoegd aan de configuratiemap van CollectD. De bestemming van dit bestand is afhankelijk van de Linux distro van uw machine.

    Als uw CollectD config directory zich bevindt in /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Als uw CollectD config directory zich bevindt in /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Voor CollectD-versies vóór 5.5 `oms.conf` moet u de tags in zoals hierboven weergegeven wijzigen.
    >

2. Kopieer collectd.conf naar de omsagent configuratie directory van de gewenste werkruimte.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Start de agent CollectD en Log Analytics opnieuw voor Linux met de volgende opdrachten.

    sudo service verzameld herstart sudo /opt/microsoft/omsagent/bin/service_control opnieuw opgestart

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Verzamelde statistieken naar Azure Monitor-schemaconversie
Om een vertrouwd model te onderhouden tussen infrastructuurstatistieken die al zijn verzameld door log analytics-agent voor Linux en de nieuwe statistieken die door CollectD zijn verzameld, wordt de volgende schematoewijzing gebruikt:

| Verzameld metrische veld | Azure Monitor,veld |
|:--|:--|
| `host` | Computer |
| `plugin` | Geen |
| `plugin_instance` | Instantienaam<br>Als **plugin_instance** *null* is, wordt InstanceName="*_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterNaam<br>Als **type_instance** *null* is, is CounterName=**leeg** |
| `dsnames[]` | CounterNaam |
| `dstypes` | Geen |
| `values[]` | Tegenwaarde |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en -oplossingen. 
* Gebruik [Aangepaste velden](custom-fields.md) om gegevens uit syslogrecords in afzonderlijke velden te ontleeden.
