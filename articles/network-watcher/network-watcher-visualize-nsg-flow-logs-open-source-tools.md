---
title: NSG-stroomlogboeken visualiseren - Elastic Stack
titleSuffix: Azure Network Watcher
description: Netwerkbeveiligingsgroepstroomlogboeken beheren en analyseren in Azure met Behulp van Network Watcher en Elastic Stack.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: e567994038fb4f71ef86dc577760ecf4699a0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840635"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>NSG-stroomlogboeken van Azure Network Watcher visualiseren met open-sourcehulpprogramma's

Stroomlogboeken voor netwerkbeveiligingen bieden informatie die kan worden gebruikt om IP-verkeer op netwerkbeveiligingsgroepen te begrijpen en te verwijderen. Deze stroomlogboeken tonen uitgaande en binnenkomende stromen op basis van regel, de NIC waarop de stroom van toepassing is, 5 tuple-informatie over de stroom (Bron/Bestemming-IP, Bron/Bestemmingspoort, Protocol) en als het verkeer is toegestaan of geweigerd.

Deze stroomlogboeken kunnen moeilijk zijn om handmatig te ontleeden en inzichten uit te krijgen. Er zijn echter verschillende open source-tools die kunnen helpen bij het visualiseren van deze gegevens. Dit artikel biedt een oplossing om deze logboeken te visualiseren met behulp van de Elastic Stack, waarmee u snel uw stroomlogboeken op een Kibana-dashboard indexeren en visualiseren.

> [!Warning]  
> De volgende stappen werken met stroomlogboeken versie 1. Zie Inleiding [tot stroomlogboekregistratie voor netwerkbeveiligingsgroepen voor](network-watcher-nsg-flow-logging-overview.md)meer informatie. De volgende instructies werken niet met versie 2 van de logbestanden, zonder wijziging.

## <a name="scenario"></a>Scenario

In dit artikel zullen we een oplossing opzetten waarmee u stroomlogboeken van de netwerkbeveiligingsgroep visualiseren met behulp van de Elastic Stack.  Een Logstash-invoerplug-in verkrijgt de stroomlogboeken rechtstreeks van de opslagblob die is geconfigureerd voor het bevatten van de stroomlogboeken. Vervolgens worden de stroomlogboeken geïndexeerd en gebruikt om een Kibana-dashboard te maken om de informatie te visualiseren.

![scenario][scenario]

## <a name="steps"></a>Stappen

### <a name="enable-network-security-group-flow-logging"></a>Logboekregistratie van netwerkbeveiligingsgroep inschakelen
Voor dit scenario moet u Network Security Group Flow Logging hebben ingeschakeld in ten minste één netwerkbeveiligingsgroep in uw account. Raadpleeg het volgende artikel [Inleiding tot stroomregistratie voor netwerkbeveiligingsgroepen voor](network-watcher-nsg-flow-logging-overview.md)instructies voor het inschakelen van netwerkbeveiligingsstroomlogboeken.

### <a name="set-up-the-elastic-stack"></a>De elasticstack instellen
Door NSG-stroomlogboeken te verbinden met de Elastic Stack, kunnen we een Kibana-dashboard maken waarmee we inzichten uit onze logboeken kunnen zoeken, grafieken, analyseren en afleiden.

#### <a name="install-elasticsearch"></a>Elasticsearch installeren

1. De Elastic Stack van versie 5.0 en hoger vereist Java 8. Voer de `java -version` opdracht uit om uw versie te controleren. Als u java niet hebt geïnstalleerd, raadpleegt u documentatie op de [Azure-suppored JDKs.](https://aka.ms/azure-jdks)
2. Download het juiste binaire pakket voor uw systeem:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Andere installatiemethoden zijn te vinden bij [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Controleer of Elasticsearch wordt uitgevoerd met de opdracht:

    ```bash
    curl http://127.0.0.1:9200
    ```

    U ziet een antwoord dat vergelijkbaar is met deze:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Voor verdere instructies over het installeren van Elastic search verwijzen naar [Installatie-instructies](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Logstash installeren

1. Als u Logstash wilt installeren, voert u de volgende opdrachten uit:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Vervolgens moeten we Logstash configureren om toegang te krijgen tot en ontsmet ten eer aan de stroomlogboeken. Maak een logstash.conf bestand met:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. De volgende inhoud toevoegen aan het bestand:

   ```
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

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
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
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
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

Voor verdere instructies over het installeren van Logstash, verwijzen wij u naar de [officiële documentatie.](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>De logstash-invoerplug-in voor Azure blob-opslag installeren

Met deze Logstash-plug-in u rechtstreeks toegang krijgen tot de stroomlogboeken van hun aangewezen opslagaccount. Als u deze plug-in wilt installeren, voert u vanuit de standaard logstash-installatiemap (in dit geval /usr/share/logstash/bin) de opdracht uit:

```bash
logstash-plugin install logstash-input-azureblob
```

Als u Logstash wilt starten, voert u de opdracht uit:

```bash
sudo /etc/init.d/logstash start
```

Voor meer informatie over deze plugin, verwijzen wij u naar de [documentatie](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Kibana installeren

1. Voer de volgende opdrachten uit om Kibana te installeren:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Als u Kibana wilt uitvoeren, gebruikt u de opdrachten:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Als u uw Kibana-webinterface wilt bekijken, navigeert u naar`http://localhost:5601`
4. Voor dit scenario is het indexpatroon dat wordt gebruikt voor de stroomlogboeken "nsg-flow-logs". U het indexpatroon wijzigen in het gedeelte uitvoer van uw logstash.conf-bestand.
5. Als u het Kibana-dashboard op afstand wilt bekijken, maakt u een binnenkomende NSG-regel die toegang geeft tot **poort 5601.**

### <a name="create-a-kibana-dashboard"></a>Een Kibana-dashboard maken

Een voorbeelddashboard om trends en details in uw waarschuwingen weer te geven, wordt weergegeven in de volgende afbeelding:

![afbeelding 1][1]

Download het [dashboardbestand,](https://aka.ms/networkwatchernsgflowlogdashboard)het [visualisatiebestand](https://aka.ms/networkwatchernsgflowlogvisualizations)en het [opgeslagen zoekbestand](https://aka.ms/networkwatchernsgflowlogsearch).

Navigeer onder het tabblad **Beheer** van Kibana naar **Opgeslagen objecten** en importeer alle drie de bestanden. Vervolgens u vanaf het tabblad **Dashboard** het voorbeelddashboard openen en laden.

U ook uw eigen visualisaties en dashboards maken die zijn afgestemd op statistieken van uw eigen belang. Lees meer over het maken van Kibana visualisaties uit de [officiële documentatie](https://www.elastic.co/guide/en/kibana/current/visualize.html)van Kibana.

### <a name="visualize-nsg-flow-logs"></a>NSG-stroomlogboeken visualiseren

Het voorbeelddashboard biedt verschillende visualisaties van de stroomlogboeken:

1. Flows by Decision/Direction Over Time - tijdreeksgrafieken met het aantal stromen in de periode. U de eenheid tijd en tijdspanne van beide visualisaties bewerken. Flows by Decision toont het aandeel van de genomen beslissingen voor het toestaan of weigeren, terwijl Flows by Direction het aandeel van inkomend en uitgaand verkeer weergeeft. Met deze visuals u verkeerstrends in de loop van de tijd onderzoeken en zoeken naar pieken of ongebruikelijke patronen.

   ![figuur2][2]

2. Stromen per bestemming/bronpoort – cirkeldiagrammen met de uitsplitsing van stromen naar hun respectievelijke poorten. Met deze weergave u uw meest gebruikte poorten zien. Als u op een specifieke poort in het cirkeldiagram klikt, wordt de rest van het dashboard gefilterd naar stromen van die poort.

   ![figuur3][3]

3. Aantal stromen en vroegste logboektijd : statistieken die u het aantal geboekte stromen en de datum van het vroegste logboek weergeven.

   ![figuur4][4]

4. Stromen op NSG en Regel – een staafdiagram met de verdeling van de stromen binnen elke NSG, evenals de verdeling van de regels binnen elke NSG. Vanaf hier u zien welke NSG en regels het meeste verkeer genereerden.

   ![figuur5][5]

5. Top 10 Source/Destination IP's – staafdiagrammen met de top 10 bron- en bestemmings-IP's. U deze grafieken aanpassen om min of meer top IP's weer te geven. Vanaf hier u de meest voorkomende IP's zien, evenals de verkeersbeslissing (toestaan of weigeren) die naar elk IP wordt gemaakt.

   ![figuur6][6]

6. Flow Tuples – deze tabel toont u de informatie in elke stroomtuple, evenals de bijbehorende NGS en regel.

   ![figuur 7][7]

Met de querybalk boven aan het dashboard u het dashboard filteren op basis van een parameter van de stromen, zoals abonnements-ID, resourcegroepen, regel of een andere variabele van belang. Voor meer informatie over kibana's vragen en filters, raadpleeg de [officiële documentatie](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Conclusie

Door de flowlogs van de Netwerkbeveiligingsgroep te combineren met de Elastic Stack, hebben we een krachtige en aanpasbare manier gevonden om ons netwerkverkeer te visualiseren. Met deze dashboards u snel inzichten over uw netwerkverkeer verkrijgen en delen, en filteren en onderzoeken op mogelijke afwijkingen. Met Kibana u deze dashboards aanpassen en specifieke visualisaties maken om te voldoen aan alle beveiligings-, audit- en nalevingsbehoeften.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het visualiseren van uw NSG-stroomlogboeken met Power BI door te gaan naar [Visualize NSG-stromenlogboeken met Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
