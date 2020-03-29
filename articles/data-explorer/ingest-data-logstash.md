---
title: Gegevens uit Logstash opnemen in Azure Data Explorer
description: In dit artikel leert u hoe u gegevens innemen (laden) in Azure Data Explorer van Logstash
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 86f6732cbf2409d3c79a3d7709100e8af24988a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66494548"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer"></a>Gegevens uit Logstash opnemen in Azure Data Explorer

[Logstash](https://www.elastic.co/products/logstash) is een open source pijplijn voor gegevensverwerking aan de serverzijde waarmee gegevens uit vele bronnen tegelijkertijd worden opgenomen, deze gegevens worden getransformeerd en vervolgens worden verzonden naar uw favoriete 'stash'. In dit artikel stuurt u die gegevens naar Azure Data Explorer, een snelle en zeer schaalbare gegevensverkenningsservice voor logboek- en telemetriegegevens. In eerste instantie maakt u een tabel en gegevenstoewijzing in een testcluster. Vervolgens zorgt u ervoor dat Logstash gegevens in de tabel verzendt en de resultaten valideert.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-account hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Een Azure Data Explorer-[testcluster en -database maken](create-cluster-database-portal.md)
* [Installatie-instructies](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html) voor Logstash versie 6+

## <a name="create-a-table"></a>Een tabel maken

Wanneer u een cluster en een database hebt, kunt u een tabel maken.

1. Voer de volgende opdracht uit in uw databasequeryvenster om een tabel te maken:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Voer de volgende opdracht uit om te controleren of de nieuwe tabel `logs` is gemaakt en leeg is:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Een toewijzing maken

Toewijzing wordt door Azure Data Explorer gebruikt om de binnenkomende gegevens te transformeren in het schema van de doeltabel. Met de volgende opdracht maakt u een nieuwe toewijzing met de naam `basicmsg` die eigenschappen uit de binnenkomende json haalt zoals wordt aangegeven door het `path` en deze uitvoert naar de `column`.

Voer in het queryvenster de volgende opdracht uit:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>De Logstash-invoegtoepassing voor uitvoer installeren

De Logstash-invoegtoepassing voor uitvoer communiceert met Azure Data Explorer en verzendt de gegevens naar de service.
Voer de volgende opdracht in de Logstash-hoofdmap uit om de invoegtoepassing te installeren:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Logstash configureren om een voorbeeldgegevensset te genereren

Logstash kan voorbeeldgebeurtenissen genereren waarmee een end-to-end-pijplijn kan worden getest.
Als u al met Logstash werkt en toegang hebt tot uw eigen gebeurtenisstroom, kunt u verder gaan met de volgende sectie. 

> [!NOTE]
> Als u uw eigen gegevens gebruikt, wijzigt u de tabel en toewijzingsobjecten die in de vorige stappen zijn gedefinieerd.

1. Bewerk een nieuw tekstbestand dat de vereiste pijplijninstellingen bevat (met behulp van vi):

    ```sh
    vi test.conf
    ```

1. Plak de volgende instellingen om Logstash te instrueren om 1000 testgebeurtenissen te genereren:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Deze configuratie omvat ook de invoerinvoegtoepassing `stdin` die u in staat stelt om zelf meer berichten te schrijven (zorg ervoor dat u *Enter* gebruikt om ze in te dienen in de pijplijn).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Logstash configureren om gegevens te verzenden naar Azure Data Explorer

Plak de volgende instellingen in hetzelfde config-bestand dat u ook in de vorige stap hebt gebruikt. Vervang alle tijdelijke aanduidingen door de relevante waarden voor uw installatie. Zie [Creating an AAD Application](/azure/kusto/management/access-control/how-to-provision-aad-app) (Een AAD-toepassing maken) voor meer informatie. 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| Parameternaam | Beschrijving |
| --- | --- |
| **Pad** | De Logstash-invoegtoepassing schrijft gebeurtenissen naar tijdelijke bestanden voordat ze naar Azure Data Explorer worden verzonden. Deze parameter bevat het pad waarnaar bestanden moeten worden geschreven en een tijdexpressie voor bestandsrotatie om een upload naar de service Azure Data Explorer te activeren.|
| **ingest_url** | Het Kusto-eindpunt voor opname-gerelateerde communicatie.|
| **app_id**, **app_key** en **app_tenant**| Referenties vereist om verbinding te maken met Azure Data Explorer. Zorg dat u een toepassing gebruikt die opnamebevoegdheden heeft. |
| **database**| De naam van de database waarin gebeurtenissen moeten worden geplaatst. |
| **Tabel** | De naam van de tabel waarin gebeurtenissen moeten worden geplaatst. |
| **mapping** | Toewijzing wordt gebruikt om de json-tekenreeks van een inkomende gebeurtenis toe te wijzen in de juiste rij-indeling (bepaalt welke eigenschap in welke kolom wordt geplaatst). |

## <a name="run-logstash"></a>Logstash uitvoeren

Nu kunt u Logstash uitvoeren en de instellingen testen.

1. Voer de volgende opdracht uit in de hoofdmap van Logstash:

    ```sh
    bin/logstash -f test.conf
    ```

    Eerst wordt er informatie op het scherm afgedrukt en vervolgens worden er door onze voorbeeldconfiguratie 1000 berichten gegenereerd. Op dit moment kunt u ook handmatig meer berichten invoeren.

1. Na enkele minuten voert u de volgende Data Explorer-query uit om de berichten weer te geven in de tabel die u hebt gedefinieerd:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Druk op Ctrl+C om Logstash af te sluiten

## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende opdracht uit in uw database om de tabel `logs` op te schonen:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Volgende stappen

* [Query's schrijven](write-queries.md)