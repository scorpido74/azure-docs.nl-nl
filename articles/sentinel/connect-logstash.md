---
title: Verbinding maken met gegevens bronnen via Logstash met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van Logstash voor het door sturen van logboeken van externe gegevens bronnen naar Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: 79d29ef228fc27655da30edbeb64abcb01e45d5e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84237176"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Logstash gebruiken om gegevens bronnen te verbinden met Azure Sentinel

> [!IMPORTANT]
> Gegevens opname met behulp van de Logstash-uitvoer-invoeg toepassing is momenteel beschikbaar als open bare preview. Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de nieuwe uitvoer-invoeg toepassing van Azure Sentinel voor de **Logstash-engine voor gegevens verzameling**kunt u nu elk type logboek dat u wilt door Logstash rechtstreeks naar uw log Analytics-werk ruimte verzenden in azure Sentinel. Uw logboeken worden verzonden naar een aangepaste tabel die u gaat definiëren met behulp van de uitvoer-invoeg toepassing.

Zie aan de slag [met Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)voor meer informatie over het werken met de Logstash-engine voor het verzamelen van gegevens.

## <a name="overview"></a>Overzicht

### <a name="architecture-and-background"></a>Architectuur en achtergrond

   ![Logstash-architectuur](./media/connect-logstash/logstash-architecture.png)

De Logstash-engine bestaat uit drie onderdelen:

- Invoer-invoeg toepassingen-aangepaste verzameling van gegevens uit verschillende bronnen
- Invoeg toepassingen filteren: manipulatie en normalisatie van gegevens volgens opgegeven criteria
- Uitvoer-invoeg toepassingen-aangepaste verzen ding van verzamelde en verwerkte gegevens naar verschillende bestemmingen

> [!NOTE]
> Azure Sentinel ondersteunt alleen de eigen meegeleverde uitvoer bare invoeg toepassing. Het biedt geen ondersteuning voor uitvoer-invoeg toepassingen van derden voor Azure Sentinel of een andere Logstash-invoeg toepassing van elk type.

De Azure Sentinel output-invoeg toepassing voor Logstash verzendt gegevens in JSON-indeling naar uw Log Analytics-werk ruimte met behulp van de Log Analytics HTTP-gegevens verzamelaar REST API. De gegevens worden opgenomen in aangepaste Logboeken.

- Meer [informatie over de Log Analytics rest API](https://docs.microsoft.com/rest/api/loganalytics/create-request).
- Meer [informatie over aangepaste logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-custom-logs).

## <a name="installing-and-configuring-the-azure-sentinel-output-plugin-in-logstash"></a>De Azure Sentinel output-invoeg toepassing installeren en configureren in Logstash

1. **Installatie**

    De Azure Sentinel output-invoeg toepassing is beschikbaar in de Logstash-verzameling.

    - Volg de instructies in het document Logstash [Working with pluginss](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) voor het installeren van de ***micro soft-Logstash-output-Azure-loganalytics*** -invoeg toepassing.
    - Als uw Logstash-systeem geen Internet toegang heeft, volgt u de instructies in het Logstash [offline-invoeg toepassing](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) voor het beheren van een offline-invoeg toepassing. (Hiervoor moet u een ander Logstash-systeem met Internet toegang maken.)

1. **Configuratie**

    Met behulp van de informatie in de Logstash- [structuur van een configuratie bestand](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) document, voegt u de Azure Sentinel output-invoeg toepassing toe aan de configuratie met de volgende sleutels en waarden (Zie de syntaxis van het juiste configuratie bestand onder de tabel):

    | **Veldnaam** | **Gegevenstype** | **Beschrijving** |
    |----------------|---------------|-----------------|
    | `workspace_id` | tekenreeks | Voer de GUID van uw werk ruimte-ID in |
    | `workspace_key` | tekenreeks | Voer de GUID van de primaire sleutel van uw werk ruimte in |
    | `custom_log_table_name` | tekenreeks | Stel de naam in van de tabel waarin de logboeken worden opgenomen. Er kan slechts één tabel naam per uitvoer-invoeg toepassing worden geconfigureerd. De logboek tabel wordt weer gegeven in azure Sentinel onder **Logboeken**, in **tabellen** in de categorie **aangepaste logboeken** met een `_CL` achtervoegsel. |
    | `endpoint` | tekenreeks | Optioneel veld. Standaard is dit het Log Analytics-eind punt. Gebruik dit veld om een alternatief eind punt in te stellen. |
    | `time_generated_field` | tekenreeks | Optioneel veld. Met deze eigenschap wordt het standaard **TimeGenerated** -veld in log Analytics overschreven. Voer de naam van het tijds tempel veld in de gegevens bron in. De gegevens in het veld moeten voldoen aan de ISO 8601-indeling ( `YYYY-MM-DDThh:mm:ssZ` ) |
    | `key_names` | matrix | Geef een lijst met Log Analytics uitvoer schema velden op. Elk lijst item moet tussen enkele aanhalings tekens worden geplaatst en de items worden gescheiden door komma's en de volledige lijst tussen vier Kante haken. Zie het onderstaande voor beeld. |
    | `plugin_flush_interval` | getal | Optioneel veld. Stel in om het maximum interval (in seconden) te definiëren tussen het verzenden van berichten naar Log Analytics. De standaard waarde is 5. |
    | `amount_resizing` | booleaans | Waar/onwaar. Het mechanisme voor automatisch schalen in-of uitschakelen, waarbij de grootte van de bericht buffer wordt aangepast op basis van het volume van de ontvangen logboek gegevens. |
    | `max_items` | getal | Optioneel veld. Geldt alleen indien `amount_resizing` ingesteld op ' false '. Gebruiken om een limiet in te stellen voor de buffer grootte van het bericht (in records). De standaardwaarde is 2000.  |

    **Voorbeeld configuratie**

        input {
            tcp {
                port => 514
                type => syslog
            }
        }

        filter {
            grok {
                match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
            }
        }

        output {
            logstash-output-azure-loganalytics {
                workspace_id => "<WS_ID>"
                workspace_key => "${WS_KEY}"
                custom_log_table_name => "logstashCustomTable"
                key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
                plugin_flush_interval => 5
            }
        } 

    > [!NOTE]
    > Ga naar de [github](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) van de uitvoer-invoeg toepassing voor meer informatie over de interne werking, configuratie en prestatie-instellingen.

1. **Logstash opnieuw starten**

1. **Inkomende logboeken weer geven in azure Sentinel**

    1. Controleer of berichten worden verzonden naar de uitvoer-invoeg toepassing.

    1. Klik in het navigatie menu van de Azure-Sentinel op **Logboeken**. Vouw onder de kop **tabellen** de categorie **aangepaste logboeken** uit. Zoek en klik op de naam van de tabel die u hebt opgegeven (met een `_CL` achtervoegsel) in de configuratie.

        ![Aangepaste logboeken van Logstash](./media/connect-logstash/logstash-custom-logs-menu.png)

    1. Als u records in de tabel wilt weer geven, moet u een query uitvoeren op de tabel met behulp van de tabel naam als schema.

        ![Query voor aangepaste logboeken van Logstash](./media/connect-logstash/logstash-custom-logs-query.png)

## <a name="monitor-output-plugin-audit-logs"></a>Audit logboeken voor uitvoer van invoeg toepassingen bewaken

Als u de connectiviteit en activiteit van de Azure Sentinel output-invoeg toepassing wilt controleren, schakelt u het juiste Logstash-logboek bestand in. Zie het document [Logstash Directory-indeling](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) voor de locatie van het logboek bestand.

Als u geen gegevens in dit logboek bestand ziet, genereert en verzendt u gebeurtenissen lokaal (via de invoer-en filter-invoeg toepassingen) om te controleren of de uitvoer-invoeg toepassing gegevens ontvangt. Azure Sentinel ondersteunt alleen problemen met betrekking tot de uitvoer-invoeg toepassing.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Logstash kunt gebruiken om externe gegevens bronnen met Azure Sentinel te verbinden. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met het detecteren van bedreigingen met Azure Sentinel, met behulp [van ingebouwde](tutorial-detect-threats-built-in.md) of [aangepaste](tutorial-detect-threats-custom.md) regels.