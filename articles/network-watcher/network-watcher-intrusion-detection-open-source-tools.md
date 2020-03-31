---
title: Netwerkinbraakdetectie uitvoeren met open source-tools
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u Azure Network Watcher en open source-hulpprogramma's gebruiken om netwerkinbraakdetectie uit te voeren
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 781f3788c9001276315a2baed7060450fa00d77a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845020"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Netwerkinbraakdetectie uitvoeren met Network Watcher en open source-tools

Packet captures zijn een belangrijk onderdeel voor het implementeren van network intrusion detection systems (IDS) en het uitvoeren van Network Security Monitoring (NSM). Er zijn verschillende open source IDS-tools die pakketopnames verwerken en zoeken naar handtekeningen van mogelijke netwerkinbraken en schadelijke activiteiten. Met behulp van de pakketopnames van Network Watcher u uw netwerk analyseren op schadelijke inbraken of kwetsbaarheden.

Een van die open source-tool is Suricata, een IDS-engine die regelsets gebruikt om netwerkverkeer te monitoren en waarschuwingen activeert wanneer zich verdachte gebeurtenissen voordoen. Suricata biedt een multi-threaded motor, wat betekent dat het netwerkverkeer analyse kan uitvoeren met verhoogde snelheid en efficiëntie. Voor meer informatie over Suricata en haar https://suricata-ids.org/mogelijkheden, bezoek hun website op .

## <a name="scenario"></a>Scenario

In dit artikel wordt uitgelegd hoe u uw omgeving instelt om netwerkinbraakdetectie uit te voeren met behulp van Network Watcher, Suricata en de Elastic Stack. Network Watcher biedt u de pakketopnames die worden gebruikt om netwerkinbraakdetectie uit te voeren. Suricata verwerkt de pakketopnamen en activeert waarschuwingen op basis van pakketten die overeenkomen met de opgegeven regelset van bedreigingen. Deze waarschuwingen worden opgeslagen in een logboekbestand op uw lokale machine. Met behulp van de Elastic Stack kunnen de logboeken die door Suricata worden gegenereerd, worden geïndexeerd en gebruikt om een Kibana-dashboard te maken, waardoor u een visuele weergave van de logboeken krijgt en een middel om snel inzicht te krijgen in potentiële netwerkkwetsbaarheden.  

![eenvoudig scenario voor webtoepassingen][1]

Beide open source-hulpprogramma's kunnen worden ingesteld op een Azure VM, zodat u deze analyse uitvoeren binnen uw eigen Azure-netwerkomgeving.

## <a name="steps"></a>Stappen

### <a name="install-suricata"></a>Suricata installeren

Voor alle andere installatiemethoden uhttps://suricata.readthedocs.io/en/latest/install.html

1. Voer in de opdrachtregelterminal van uw VM de volgende opdrachten uit:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Als u uw installatie `suricata -h` wilt verifiëren, voert u de opdracht uit om de volledige lijst met opdrachten weer te geven.

### <a name="download-the-emerging-threats-ruleset"></a>Download de regelset nieuwe bedreigingen

In dit stadium hebben we geen regels voor Suricata te lopen. U uw eigen regels maken als er specifieke bedreigingen voor uw netwerk zijn die u wilt detecteren, of als u ook ontwikkelde regelsets van een aantal providers gebruiken, zoals Emerging Threats of VRT-regels van Snort. We gebruiken hier de vrij toegankelijke Emerging Threats ruleset:

Download de regelset en kopieer deze naar de map:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Procespakketopnames met Suricata

Voer de volgende opdracht uit om pakketopnamen met Suricata te verwerken:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Als u de resulterende waarschuwingen wilt controleren, leest u het bestand fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>De elasticstack instellen

Hoewel de logboeken die Suricata produceert waardevolle informatie bevatten over wat er op ons netwerk gebeurt, zijn deze logbestanden niet het gemakkelijkst te lezen en te begrijpen. Door Suricata te verbinden met de Elastic Stack, kunnen we een Kibana-dashboard maken waarmee we inzichten uit onze logboeken kunnen zoeken, grafieken, analyseren en afleiden.

#### <a name="install-elasticsearch"></a>Elasticsearch installeren

1. De Elastic Stack van versie 5.0 en hoger vereist Java 8. Voer de `java -version` opdracht uit om uw versie te controleren. Als u java niet hebt geïnstalleerd, raadpleegt u documentatie op de [Azure-suppored JDKs.](https://aka.ms/azure-jdks)

1. Download het juiste binaire pakket voor uw systeem:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Andere installatiemethoden zijn te vinden bij [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Controleer of Elasticsearch wordt uitgevoerd met de opdracht:

    ```
    curl http://127.0.0.1:9200
    ```

    U ziet een antwoord dat vergelijkbaar is met deze:

    ```
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

Raadpleeg voor verdere instructies over het installeren van Elastic search de pagina [Installatie](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Logstash installeren

1. Als u Logstash wilt installeren, voert u de volgende opdrachten uit:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Vervolgens moeten we Logstash configureren om te lezen uit de uitvoer van eve.json-bestand. Maak een logstash.conf bestand met:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Voeg de volgende inhoud toe aan het bestand (zorg ervoor dat het pad naar het eve.json-bestand juist is):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Zorg ervoor dat u de juiste machtigingen te geven aan de eve.json bestand, zodat Logstash kan het bestand inslikken.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Als u Logstash wilt starten, voert u de opdracht uit:

    ```
    sudo /etc/init.d/logstash start
    ```

Voor verdere instructies over het installeren van Logstash, verwijzen naar de [officiële documentatie](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Kibana installeren

1. Voer de volgende opdrachten uit om Kibana te installeren:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Als u Kibana wilt uitvoeren, gebruikt u de opdrachten:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Als u uw Kibana-webinterface wilt bekijken, navigeert u naar`http://localhost:5601`
1. Voor dit scenario is het indexpatroon dat wordt gebruikt voor de Suricata-logboeken "logstash-*"

1. Als u het Kibana-dashboard op afstand wilt bekijken, maakt u een binnenkomende NSG-regel die toegang geeft tot **poort 5601.**

### <a name="create-a-kibana-dashboard"></a>Een Kibana-dashboard maken

Voor dit artikel hebben we een voorbeelddashboard voor u beschikbaar gesteld om trends en details in uw waarschuwingen te bekijken.

1. Download [hier](https://aka.ms/networkwatchersuricatadashboard)het dashboardbestand, het visualisatiebestand [hier](https://aka.ms/networkwatchersuricatavisualization)en het opgeslagen zoekbestand [hier.](https://aka.ms/networkwatchersuricatasavedsearch)

1. Navigeer onder het tabblad **Beheer** van Kibana naar **Opgeslagen objecten** en importeer alle drie de bestanden. Vervolgens u vanaf het tabblad **Dashboard** het voorbeelddashboard openen en laden.

U ook uw eigen visualisaties en dashboards maken die zijn afgestemd op statistieken van uw eigen belang. Lees meer over het maken van Kibana visualisaties uit de [officiële documentatie](https://www.elastic.co/guide/en/kibana/current/visualize.html)van Kibana.

![kibana dashboard][2]

### <a name="visualize-ids-alert-logs"></a>IDS-waarschuwingslogboeken visualiseren

Het voorbeelddashboard biedt verschillende visualisaties van de Suricata-waarschuwingslogboeken:

1. Waarschuwingen per GeoIP – een kaart met de verdeling van waarschuwingen door hun land/regio van herkomst op basis van geografische locatie (bepaald door IP)

    ![geo ip][3]

1. Top 10 Waarschuwingen - een overzicht van de 10 meest voorkomende waarschuwingen en hun beschrijving. Als u op een afzonderlijke waarschuwing klikt, wordt het dashboard naar de informatie met betrekking tot die specifieke waarschuwing geklikt.

    ![afbeelding 4][4]

1. Aantal waarschuwingen – het totale aantal waarschuwingen dat door de regelset wordt geactiveerd

    ![afbeelding 5][5]

1. Top 20 Bron/Bestemming IP's /Ports - cirkeldiagrammen met de top 20 IP's en poorten waarop waarschuwingen zijn geactiveerd. U filteren op specifieke IP's / poorten om te zien hoeveel en wat voor soort waarschuwingen worden geactiveerd.

    ![afbeelding 6][6]

1. Waarschuwingsoverzicht – een tabel met specifieke details van elke afzonderlijke waarschuwing. U deze tabel aanpassen om andere parameters weer te geven die voor elke waarschuwing van belang zijn.

    ![afbeelding 7][7]

Zie de [officiële documentatie van Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html)voor meer documentatie over het maken van aangepaste visualisaties en dashboards.

## <a name="conclusion"></a>Conclusie

Door pakketopnames van Network Watcher te combineren met open source IDS-tools zoals Suricata, u netwerkinbraakdetectie uitvoeren voor een breed scala aan bedreigingen. Met deze dashboards u snel trends en afwijkingen in uw netwerk herkennen, en ook in de gegevens graven om de onderliggende oorzaken van waarschuwingen zoals kwaadwillende gebruikersagents of kwetsbare poorten te ontdekken. Met deze geëxtraheerde gegevens u weloverwogen beslissingen nemen over hoe u reageren op uw netwerk en uw netwerk beschermen tegen schadelijke inbraakpogingen, en regels maken om toekomstige inbraken in uw netwerk te voorkomen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het activeren van pakketopnames op basis van waarschuwingen door [pakketopname gebruiken om proactieve netwerkbewaking uit te voeren met Azure-functies](network-watcher-alert-triggered-packet-capture.md)

Meer informatie over het visualiseren van uw NSG-stroomlogboeken met Power BI door te gaan naar [Visualize NSG-stromenlogboeken met Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
