---
title: Netwerkbeveiligingsgroeplogboeken analyseren van Azure-netwerkbeveiliging - Graylog | Microsoft Documenten
description: Meer informatie over het beheren en analyseren van netwerkbeveiligingsgroepstroomlogboeken in Azure met Behulp van Network Watcher en Graylog.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 1e597a81967a8fb6be2959d53e65ad01135e5e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842900"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Netwerkbeveiligingsgroepstroomlogboeken in Azure beheren en analyseren met Behulp van Network Watcher en Graylog

[Netwerkbeveiligingsgroepstroomlogboeken](network-watcher-nsg-flow-logging-overview.md) bieden informatie die u gebruiken om inzicht te krijgen in het binnendringen en het uitgangen van IP-verkeer voor Azure-netwerkinterfaces. Stroomlogboeken tonen uitgaande en binnenkomende stromen op basis van de regel van de netwerkbeveiligingsgroep, de netwerkinterface waarop de stroom van toepassing is, 5-tuple-informatie (Bron/Bestemming-IP, Bron/Doelpoort, Protocol) over de stroom en als het verkeer is toegestaan of geweigerd.

U veel netwerkbeveiligingsgroepen in uw netwerk hebben met stroomlogboekregistratie ingeschakeld. Verschillende netwerkbeveiligingsgroepen met ingeschakelde stroomlogboekregistratie kunnen het omslachtig maken om te ontleeden en inzichten te krijgen uit uw logboeken. Dit artikel biedt een oplossing voor het centraal beheren van deze netwerkbeveiligingsgroepstroomlogboeken met Graylog, een open source-logbeheer- en analysetool, en Logstash, een open source-pijplijn voor gegevensverwerking aan de serverzijde.

> [!Warning]
> De volgende stappen werken met stroomlogboeken versie 1. Zie Inleiding [tot stroomlogboekregistratie voor netwerkbeveiligingsgroepen voor](network-watcher-nsg-flow-logging-overview.md)meer informatie. De volgende instructies werken niet met versie 2 van de logbestanden, zonder wijziging.

## <a name="scenario"></a>Scenario

Netwerkbeveiligingsgroepstroomlogboeken zijn ingeschakeld met Network Watcher. Stroomlogboeken stromen binnen in Azure blob-opslag. Een Logstash-plug-in wordt gebruikt om stroomlogboeken van blobopslag aan te sluiten en te verwerken en naar Graylog te verzenden. Zodra de stroomlogboeken zijn opgeslagen in Graylog, kunnen ze worden geanalyseerd en gevisualiseerd in aangepaste dashboards.

![Graylog-werkstroom](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Installatiestappen

### <a name="enable-network-security-group-flow-logging"></a>Logboekregistratie van netwerkbeveiligingsgroepen inschakelen

Voor dit scenario moet u de logboekregistratie van de netwerkbeveiligingsgroep hebben ingeschakeld in ten minste één netwerkbeveiligingsgroep in uw account. Raadpleeg het volgende artikel [Inleiding tot stroomregistratie voor netwerkbeveiligingsgroepen voor](network-watcher-nsg-flow-logging-overview.md)instructies voor het inschakelen van netwerkbeveiligingsgroepstroomlogboeken.

### <a name="setting-up-graylog"></a>Graylog instellen

In dit voorbeeld zijn zowel Graylog als Logstash geconfigureerd op een Ubuntu 14.04-server, geïmplementeerd in Azure.

- Raadpleeg de [documentatie](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) van Graylog, voor stapsgewijze instructies over hoe u op Ubuntu installeert.
- Zorg ervoor dat u ook de graylog-webinterface configureert door de documentatie te [volgen.](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif)

In dit voorbeeld wordt de minimale Graylog-instelling gebruikt (d.w.z. een enkel exemplaar van een Graylog), maar Graylog kan worden ontworpen om te schalen tussen resources, afhankelijk van uw systeem en productiebehoeften. Voor meer informatie over architectonische overwegingen of een diepe architectonische gids, zie Graylog's [documentatie](https://docs.graylog.org/en/2.2/pages/architecture.html) en [architectuurgids.](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture)

Graylog kan op vele manieren worden geïnstalleerd, afhankelijk van uw platform en voorkeuren. Voor een volledige lijst van mogelijke installatiemethoden, verwijzen naar de officiële [documentatie](https://docs.graylog.org/en/2.2/pages/installation.html)graylog's . De Graylog-servertoepassing draait op Linux-distributies en heeft de volgende vereisten:

-  Java SE 8 of hoger – [Azul Azure JDK-documentatie](https://aka.ms/azure-jdks)
-  Elastic Search 2.x (2.1.0 of hoger) - [Elasticsearch installatiedocumentatie](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 of hoger – [MongoDB installatiedocumentatie](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Logstash installeren

Logstash wordt gebruikt om de JSON-geformatteerde stroomlogboeken af te vlakken naar een stroomtuple-niveau. Door de stroomlogboeken af te vlakken, kunnen de logboeken gemakkelijker worden georganiseerd en gezocht in Graylog.

1. Voer de volgende opdrachten uit om Logstash te installeren:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Configureer Logstash om de stroomlogboeken te ontwenen en naar Graylog te sturen. Maak een Logstash.conf-bestand:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Voeg de volgende inhoud toe aan het bestand. Wijzig de gemarkeerde waarden om uw opslagaccountgegevens weer te geven:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   Het meegeleverde logstash config-bestand bestaat uit drie delen: de invoer, het filter en de uitvoer. De invoersectie wijst de invoerbron aan van de logboeken die Logstash zal verwerken - in dit geval gaat u een Azure-bloginvoerplug-in-instantie gebruiken (geïnstalleerd in de volgende stappen) waarmee we toegang hebben tot de netwerkbeveiligingsgroepstroomlogboekJSON-bestanden die zijn opgeslagen in blob-opslag.

De filtersectie hiermee wordt elk stroomlogboekbestand afgevlakt, zodat elke afzonderlijke stroomtuple en de bijbehorende eigenschappen een afzonderlijke Logstash-gebeurtenis worden.

Ten slotte stuurt de uitvoersectie elke Logstash-gebeurtenis door naar de Graylog-server. Om aan uw specifieke behoeften te voldoen, wijzigt u het Logstash config-bestand, indien nodig.

   > [!NOTE]
   > In het vorige config-bestand wordt ervan uitgegaan dat de Graylog-server is geconfigureerd op het IP-adres van de lokale hostloopback 127.0.0.1. Als dit niet het zo is, moet u de hostparameter in de uitvoersectie wijzigen in het juiste IP-adres.

Zie de [Logstash-documentatie](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)voor verdere instructies over het installeren van Logstash.

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>De logstash-invoerplug-in voor Azure blob-opslag installeren

Met de Logstash-plug-in u rechtstreeks toegang krijgen tot de stroomlogboeken van hun aangewezen blob-opslagaccount. Voer de volgende opdracht uit om de plug-in te installeren in de standaard logstash-installatiemap (in dit geval /usr/share/logstash/bin):

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Zie de [documentatie](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)voor meer informatie over deze plug-in.

### <a name="set-up-connection-from-logstash-to-graylog"></a>Verbinding instellen van Logstash naar Graylog

Nu u een verbinding met de stroomlogboeken hebt gemaakt met Logstash en de Graylog-server hebt ingesteld, moet u Graylog configureren om de binnenkomende logboekbestanden te accepteren.

1. Navigeer naar de webinterface van de Graylog Server met de URL die u hiervoor hebt geconfigureerd. U hebt toegang tot de interface door uw browser`http://<graylog-server-ip>:9000/`

2. Als u naar de configuratiepagina wilt navigeren, selecteert u het vervolgkeuzemenu **Systeem** in de bovenste navigatiebalk rechtsboven en klikt u op **Invoer**.
   U ook naar`http://<graylog-server-ip>:9000/system/inputs`

   ![Aan de slag](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Als u de nieuwe invoer wilt starten, selecteert u *GELF UDP* in de vervolgkeuzelijst **Invoer selecteren** en vult u het formulier in. GELF staat voor Graylog Extended Log Format. Het GELF formaat is ontwikkeld door Graylog. Zie de [Graylog-documentatie](https://docs.graylog.org/en/2.2/pages/gelf.html)voor meer informatie over de voordelen ervan.

   Zorg ervoor dat u de invoer bindt aan het IP waarop u uw Graylog-server hebt geconfigureerd. Het IP-adres moet overeenkomen met het **hostveld** van de UDP-uitvoer van het Logstash-configuratiebestand. De standaardpoort moet *12201*zijn. Zorg ervoor dat de poort overeenkomt met het **poortveld** in de UDP-uitvoer die is aangewezen in het bestand Logstash config.

   ![Invoer](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Zodra u de invoer start, ziet u deze worden weergegeven onder de sectie **Lokale invoer,** zoals in de volgende afbeelding wordt weergegeven:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Raadpleeg de [documentatie](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs)voor meer informatie over de invoer van Graylog-berichten.

4. Zodra deze configuraties zijn gemaakt, u Logstash starten om te `sudo systemctl start logstash.service`beginnen met het lezen in stroomlogboeken met de volgende opdracht:.

### <a name="search-through-graylog-messages"></a>Zoeken via Graylog-berichten

Nadat u uw Graylog-server enige tijd hebt gegeven om berichten te verzamelen, u de berichten doorzoeken. Als u wilt controleren welke berichten naar uw Graylog-server worden verzonden, klikt u op de **configuratiepagina Invoer** op de knop Ontvangen**berichten weergeven**van de GELF UDP-invoer die u hebt gemaakt. U wordt doorverwezen naar een scherm dat lijkt op de volgende afbeelding: 

![Histogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Als u op de blauwe koppeling '%{Message}' klikt, wordt elk bericht uitgebreid om de parameters van elke stroomtuple weer te geven, zoals in de volgende afbeelding wordt weergegeven:

![Berichten](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Standaard worden alle berichtvelden in de zoekopdracht opgenomen als u geen specifiek berichtveld selecteert om naar te zoeken. Als u wilt zoeken naar specifieke berichten (d.w.z. – stroomt tuples van een specifieke bron-IP) u de Graylog-zoekquerytaal gebruiken zoals [gedocumenteerd](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Netwerkbeveiligingsgroepstroomlogboeken analyseren met Graylog

Nu Graylog het uitvoeren van de functie heeft ingesteld, u een deel van de functionaliteit gebruiken om uw stroomlogboekgegevens beter te begrijpen. Een van deze manieren is door dashboards te gebruiken om specifieke weergaven van uw gegevens te maken.

### <a name="create-a-dashboard"></a>Een dashboard maken

1. Selecteer **dashboards** in de bovenste navigatiebalk of navigeer naar`http://<graylog-server-ip>:9000/dashboards/`

2. Klik van daaruit op de groene **knop Dashboard maken** en vul het korte formulier in met de titel en beschrijving van uw dashboard. Druk op de knop **Opslaan** om het nieuwe dashboard te maken. U ziet een dashboard dat lijkt op de volgende afbeelding:

    ![Dashboards](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Widgets toevoegen

U op de titel van het dashboard klikken om het te zien, maar op dit moment is het leeg, omdat we geen widgets hebben toegevoegd. Een eenvoudig en nuttig typeobject dat aan het dashboard moet worden toegevoegd, zijn **grafieken met snelle waarden,** die een lijst met waarden van het geselecteerde veld en de verdeling ervan weergeven.

1. Navigeer terug naar de zoekresultaten van de UDP-invoer die stroomlogboeken ontvangt door **Zoeken** te selecteren op de bovenste navigatiebalk.

2. Zoek onder het deelvenster **Resultaat zoeken** aan de linkerkant van het scherm het tabblad **Velden,** waarin de verschillende velden van elk binnenkomend stroombericht worden weergegeven.

3. Selecteer een gewenste parameter waarin u wilt visualiseren (in dit voorbeeld wordt de IP-bron geselecteerd). Als u de lijst met mogelijke widgets wilt weergeven, klikt u op de blauwe vervolgkeuzepijl links van het veld en selecteert u **Vervolgens Snelle waarden** om de widget te genereren. U ziet iets wat lijkt op de volgende afbeelding:

   ![Bron-IP](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Van daaruit u de knop **Toevoegen aan dashboard** in de rechterbovenhoek van de widget selecteren en het bijbehorende dashboard selecteren dat u wilt toevoegen.

5. Navigeer terug naar het dashboard om de widget te zien die u zojuist hebt toegevoegd.

   U verschillende andere widgets toevoegen, zoals histogrammen en tellingen aan uw dashboard om belangrijke statistieken bij te houden, zoals het voorbeelddashboard in de volgende afbeelding:

   ![Dashboard Flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Voor verdere uitleg over dashboards en de andere soorten widgets, verwijzen naar graylog's [documentatie](https://docs.graylog.org/en/2.2/pages/dashboards.html).

Door Network Watcher te integreren met Graylog, hebt u nu een handige en gecentraliseerde manier om netwerkbeveiligingsgroepstroomlogboeken te beheren en te visualiseren. Graylog heeft een aantal andere krachtige functies, zoals streams en waarschuwingen die ook kunnen worden gebruikt om stroomlogboeken verder te beheren en uw netwerkverkeer beter te begrijpen. Nu u Graylog hebt ingesteld en verbonden met Azure, u de andere functionaliteit die het biedt, blijven verkennen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het visualiseren van uw netwerkbeveiligingsgroepstroomlogboeken met Power BI door te gaan naar [Netwerkbeveiligingsgroepstromenlogboeken visualiseren met Power BI.](network-watcher-visualize-nsg-flow-logs-power-bi.md)
