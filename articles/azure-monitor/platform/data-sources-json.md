---
title: Aangepaste JSON-gegevens verzamelen in Azure Monitor | Microsoft Documenten
description: Aangepaste JSON-gegevensbronnen kunnen worden verzameld in Azure Monitor met behulp van de Log Analytics Agent voor Linux.  Deze aangepaste gegevensbronnen kunnen eenvoudige scripts zijn die JSON retourneren, zoals curl of een van fluentd's 300+ plug-ins. In dit artikel wordt de configuratie beschreven die nodig is voor het verzamelen van gegevens.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 49eb3fa22bc9afffb9e93f3152cdc00323b76d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662158"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Aangepaste JSON-gegevensbronnen verzamelen met de Log Analytics-agent voor Linux in Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Aangepaste JSON-gegevensbronnen kunnen worden verzameld in [Azure Monitor](data-platform.md) met behulp van de Log Analytics-agent voor Linux.  Deze aangepaste gegevensbronnen kunnen eenvoudige scripts zijn die JSON retourneren, zoals [curl](https://curl.haxx.se/) of een van [fluentd's 300+ plug-ins.](https://www.fluentd.org/plugins/all) In dit artikel wordt de configuratie beschreven die nodig is voor het verzamelen van gegevens.


> [!NOTE]
> Log Analytics-agent voor Linux v1.1.0-217+ is vereist voor aangepaste JSON-gegevens

## <a name="configuration"></a>Configuratie

### <a name="configure-input-plugin"></a>Invoerplug-in configureren

Als u JSON-gegevens wilt `oms.api.` verzamelen in Azure Monitor, voegt u toe aan het begin van een FluentD-tag in een invoerplug-in.

Bijvoorbeeld, volgt is een `exec-json.conf` apart `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`configuratiebestand in .  Dit maakt gebruik `exec` van de FluentD plugin om elke 30 seconden een krulopdracht uit te voeren.  De uitvoer van deze opdracht wordt verzameld door de JSON-uitvoerplugin.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
Het configuratiebestand `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` dat onder wordt toegevoegd, moet het eigendom ervan wijzigen met de volgende opdracht.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Uitvoerplug-in configureren 
Voeg de volgende uitvoerplug-inconfiguratie `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` toe aan de hoofdconfiguratie in of als een apart configuratiebestand dat in`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Log Analytics-agent opnieuw starten voor Linux
Start de Log Analytics-agent voor Linux-service opnieuw met de volgende opdracht.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Uitvoer
De gegevens worden verzameld in Azure Monitor `<FLUENTD_TAG>_CL`met een recordtype .

Bijvoorbeeld de aangepaste `tag oms.api.tomcat` tag in Azure Monitor `tomcat_CL`met een recordtype .  U alle records van dit type ophalen met de volgende logboekquery.

    Type=tomcat_CL

Geneste JSON-gegevensbronnen worden ondersteund, maar worden geïndexeerd op basis van bovenliggend veld. De volgende JSON-gegevens worden bijvoorbeeld geretourneerd `tag_s : "[{ "a":"1", "b":"2" }]`uit een logboekquery als .

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en -oplossingen. 
