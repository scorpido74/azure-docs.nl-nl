---
title: NSG-stroomlogboeken beheren met Grafana
titleSuffix: Azure Network Watcher
description: Netwerkbeveiligingsgroepstroomlogboeken beheren en analyseren in Azure met Behulp van Network Watcher en Grafana.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: c48d5a02cdb8ef63904642c6c2c76cb5d61e1f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840907"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Stroomlogboeken van netwerkbeveiligingsgroepen beheren en analyseren met Behulp van Network Watcher en Grafana

[Netwerkbeveiligingsgroep (NSG)-stroomlogboeken](network-watcher-nsg-flow-logging-overview.md) bieden informatie die kan worden gebruikt om inzicht te krijgen in binnendringen en IP-verkeer op netwerkinterfaces te verwijderen. Deze stroomlogboeken tonen uitgaande en binnenkomende stromen op basis van een NSG-regel, de NIC waarop de stroom van toepassing is, 5-tuple-informatie over de stroom (Bron/Bestemming-IP, Bron/Bestemmingspoort, Protocol) en als het verkeer is toegestaan of geweigerd.

> [!Warning]  
> De volgende stappen werken met stroomlogboeken versie 1. Zie Inleiding [tot stroomlogboekregistratie voor netwerkbeveiligingsgroepen voor](network-watcher-nsg-flow-logging-overview.md)meer informatie. De volgende instructies werken niet met versie 2 van de logbestanden, zonder wijziging.

U veel NSG's in uw netwerk hebben met flow logging ingeschakeld. Deze hoeveelheid logboekregistratiegegevens maakt het omslachtig om te ontleeden en inzichten te krijgen uit uw logboeken. Dit artikel biedt een oplossing voor het centraal beheren van deze NSG-stroomlogboeken met Grafana, een open source-grafiektool, ElasticSearch, een gedistribueerde zoek- en analyse-engine en Logstash, een open source-pijplijn voor gegevensverwerking aan de serverzijde.  

## <a name="scenario"></a>Scenario

NSG-stroomlogboeken zijn ingeschakeld met Network Watcher en worden opgeslagen in Azure blob-opslag. Een Logstash-plug-in wordt gebruikt om stroomlogboeken van blobopslag aan te sluiten en te verwerken en deze naar ElasticSearch te sturen.  Zodra de stroomlogboeken zijn opgeslagen in ElasticSearch, kunnen ze worden geanalyseerd en gevisualiseerd in aangepaste dashboards in Grafana.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Installatiestappen

### <a name="enable-network-security-group-flow-logging"></a>Logboekregistratie van netwerkbeveiligingsgroep inschakelen

Voor dit scenario moet u Network Security Group Flow Logging hebben ingeschakeld in ten minste één netwerkbeveiligingsgroep in uw account. Raadpleeg het volgende artikel [Inleiding tot stroomregistratie voor netwerkbeveiligingsgroepen voor](network-watcher-nsg-flow-logging-overview.md)instructies voor het inschakelen van netwerkbeveiligingsstroomlogboeken.

### <a name="setup-considerations"></a>Overwegingen bij de installatie

In dit voorbeeld worden Grafana, ElasticSearch en Logstash geconfigureerd op een Ubuntu 16.04 LTS-server die in Azure is geïmplementeerd. Deze minimale setup wordt gebruikt voor het uitvoeren van alle drie de componenten - ze draaien allemaal op dezelfde VM. Deze instelling mag alleen worden gebruikt voor het testen en niet-kritieke workloads. Logstash, Elasticsearch en Grafana kunnen allemaal worden ontworpen om onafhankelijk te schalen in vele gevallen. Zie voor meer informatie de documentatie voor elk van deze onderdelen.

### <a name="install-logstash"></a>Logstash installeren

U gebruikt Logstash om de JSON-opgemaakte stroomlogboeken af te vlakken naar een stroomtupleniveau.

1. Voer de volgende opdrachten uit om Logstash te installeren:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Configureer Logstash om de stroomlogboeken te ontwenen en naar ElasticSearch te sturen. Maak een Logstash.conf-bestand met:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Voeg de volgende inhoud toe aan het bestand. Wijzig de naam en toegangssleutel voor het opslagaccount om uw opslagaccountgegevens weer te geven:

   ```bash
    input {
      azureblob
      {
        storage_account_name => "mystorageaccount"
        storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
        container => "insights-logs-networksecuritygroupflowevent"
        codec => "json"
        # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
        # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
        file_head_bytes => 12
        file_tail_bytes => 2
        # Enable / tweak these settings when event is too big for codec to handle.
        # break_json_down_policy => "with_head_tail"
        # break_json_batch_count => 2
      }
    }
    filter {
      split { field => "[records]" }
      split { field => "[records][properties][flows]"}
      split { field => "[records][properties][flows][flows]"}
      split { field => "[records][properties][flows][flows][flowTuples]"}

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
        add_field => {
          "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
          "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
          "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
          "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
          "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
          "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
          "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
          "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
        }
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
        match => ["unixtimestamp" , "UNIX"]
      }
    }
    output {
      stdout { codec => rubydebug }
      elasticsearch {
        hosts => "localhost"
        index => "nsg-flow-logs"
      }
    }
   ```

Het meegeleverde logstash config-bestand bestaat uit drie delen: de invoer, het filter en de uitvoer.
De invoersectie wijst de invoerbron aan van de logboeken die Logstash zal verwerken - in dit geval gaan we een "azureblob"-invoerplug-in gebruiken (geïnstalleerd in de volgende stappen) waarmee we toegang hebben tot de JSON-bestanden van de NSG-stroomlogboek die zijn opgeslagen in blobopslag. 

De filtersectie hiermee wordt elk stroomlogboekbestand afgevlakt, zodat elke afzonderlijke stroomtuple en de bijbehorende eigenschappen een afzonderlijke Logstash-gebeurtenis worden.

Ten slotte stuurt de uitvoersectie elke Logstash-gebeurtenis door naar de ElasticSearch-server. Voel je vrij om het Logstash config-bestand aan te passen aan uw specifieke behoeften.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>De logstash-invoerplug-in voor Azure Blob-opslag installeren

Met deze Logstash-plug-in u rechtstreeks toegang krijgen tot de stroomlogboeken van hun aangewezen blob-opslagaccount. Als u deze plug-in wilt installeren, voert u in de standaard logstash-installatiemap (in dit geval /usr/share/logstash/bin) de opdracht uit:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Zie [Logstash-invoerplug-in voor Azure Storage Blobs voor](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)meer informatie over deze plug-in.

### <a name="install-elasticsearch"></a>ElasticSearch installeren

U het volgende script gebruiken om ElasticSearch te installeren. Zie [Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html)voor informatie over het installeren van ElasticSearch.

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Grafana installeren

Voer de volgende opdrachten uit om Grafana te installeren en uit te voeren:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Zie Installeren op [Debian / Ubuntu voor](https://docs.grafana.org/installation/debian/)aanvullende installatiegegevens.

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>De ElasticSearch-server toevoegen als gegevensbron

Vervolgens moet u de ElasticSearch-index met stroomlogboeken toevoegen als gegevensbron. U een gegevensbron toevoegen door **Gegevensbron toevoegen** te selecteren en het formulier met de relevante informatie in te vullen. Een voorbeeld van deze configuratie is te vinden in de volgende schermafbeelding:

![Gegevensbron toevoegen](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Een dashboard maken

Nu u Grafana met succes hebt geconfigureerd om te lezen uit de ElasticSearch-index met NSG-stroomlogboeken, u dashboards maken en personaliseren. Als u een nieuw dashboard wilt maken, selecteert **u Uw eerste dashboard maken**. In de volgende voorbeeldgrafiekconfiguratie worden stromen weergegeven die zijn gesegmenteerd door de NSG-regel:

![Dashboardgrafiek](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

De volgende screenshot toont een grafiek en grafiek met de bovenste stromen en hun frequentie. Stromen worden ook weergegeven door NSG regel en stromen per besluit. Grafana is zeer aanpasbaar, dus het is raadzaam dat u dashboards maakt die passen bij uw specifieke bewakingsbehoeften. In het volgende voorbeeld ziet u een typisch dashboard:

![Dashboardgrafiek](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Conclusie

Door Network Watcher te integreren met ElasticSearch en Grafana, hebt u nu een handige en gecentraliseerde manier om NSG-stroomlogboeken en andere gegevens te beheren en te visualiseren. Grafana heeft een aantal andere krachtige grafische functies die ook kunnen worden gebruikt om stroomlogboeken verder te beheren en uw netwerkverkeer beter te begrijpen. Nu u een Grafana-exemplaar hebt ingesteld en verbonden met Azure, u de andere functionaliteit die het biedt, blijven verkennen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van [Network Watcher](network-watcher-monitoring-overview.md).

