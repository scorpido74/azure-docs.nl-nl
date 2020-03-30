---
title: Gegevens uit Azure Data Explorer visualiseren met Grafana
description: In dit artikel leert u Azure Data Explorer in te stellen als gegevensbron voor Grafana en vervolgens gegevens uit een voorbeeldcluster te visualiseren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038044"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Gegevens van Azure Data Explorer in Grafana visualiseren

Grafana is een analyseplatform waarmee u gegevens opvragen en visualiseren en vervolgens dashboards maken en delen op basis van uw visualisaties. Grafana biedt een Azure Data *Explorer-plug-in*waarmee u verbinding maken met en gegevens uit Azure Data Explorer visualiseren. In dit artikel leert u Azure Data Explorer in te stellen als gegevensbron voor Grafana en vervolgens gegevens uit een voorbeeldcluster te visualiseren.

Gebruik de volgende video voor meer informatie over het gebruik van de Azure Data Explorer-plug-in van Grafana, het instellen van Azure Data Explorer als gegevensbron voor Grafana en vervolgens gegevens visualiseren. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

U ook [de gegevensbron configureren](#configure-the-data-source) en [gegevens visualiseren](#visualize-data) zoals beschreven in het onderstaande artikel.

## <a name="prerequisites"></a>Vereisten

Je hebt het volgende nodig om dit artikel te voltooien:

* [Grafana versie 5.3.0 of hoger](https://docs.grafana.org/installation/) voor uw besturingssysteem

* De [Azure Data Explorer-plug-in](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) voor Grafana

* Een cluster met voorbeeldgegevens van StormEvents. Zie [Snelstart: Een Azure Data Explorer-cluster en -database maken](create-cluster-database-portal.md) en [voorbeeldgegevens opnemen in Azure Data Explorer](ingest-sample-data.md)voor meer informatie.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Eigenschappen opgeven en de verbinding testen

Als de serviceprincipal is toegewezen aan de *kijkersrol,* geeft u nu eigenschappen op in uw exemplaar van Grafana en test u de verbinding met Azure Data Explorer.

1. Selecteer in Grafana in het linkermenu het tandwielpictogram **en vervolgens Gegevensbronnen**.

    ![Gegevensbronnen](media/grafana/data-sources.png)

1. Selecteer **Gegevensbron toevoegen**.

1. Voer op de pagina **Gegevensbronnen / Nieuw** een naam voor de gegevensbron in en selecteer vervolgens het type **Azure Data Explorer Datasource**.

    ![Verbindingsnaam en -type](media/grafana/connection-name-type.png)

1. Voer de naam van uw cluster in het formulier https://{ClusterName}. {Regio}.kusto.windows.net. Voer de andere waarden uit de Azure-portal of CLI in. Zie de tabel onder de volgende afbeelding voor een toewijzing.

    ![Verbindingseigenschappen](media/grafana/connection-properties.png)

    | Grafana-gebruikersinterface | Azure Portal | Azure-CLI |
    | --- | --- | --- |
    | Abonnements-id | ABONNEMENTS-ID | SubscriptionId |
    | Tenant-id | Directory-id | tenant |
    | Client-id | Toepassings-id | appId |
    | Clientgeheim | Wachtwoord | wachtwoord |
    | | | |

1. Selecteer **&-test opslaan**.

    Als de test succesvol is, gaat u naar de volgende sectie. Als u problemen tegenkomt, controleert u de waarden die u in Grafana hebt opgegeven en controleert u eerdere stappen.

## <a name="visualize-data"></a>Gegevens visualiseren

Nu u klaar bent met het configureren van Azure Data Explorer als gegevensbron voor Grafana, is het tijd om gegevens te visualiseren. We laten hier een basisvoorbeeld zien, maar je nog veel meer doen. We raden u aan [schrijfquery's voor Azure Data Explorer](write-queries.md) te bekijken voor voorbeelden van andere query's die moeten worden uitgevoerd tegen de voorbeeldgegevensset.

1. Selecteer in Grafana in het linkermenu het pluspictogram en **vervolgens Dashboard**.

    ![Dashboard maken](media/grafana/create-dashboard.png)

1. Selecteer **Grafiek**onder het tabblad **Toevoegen** .

    ![Grafiek toevoegen](media/grafana/add-graph.png)

1. Selecteer in het deelvenster Grafiek de optie **Deelvenstertitel** en **bewerk**.

    ![Deelvenster Bewerken](media/grafana/edit-panel.png)

1. Selecteer onder aan het deelvenster **Gegevensbron** en selecteer vervolgens de gegevensbron die u hebt geconfigureerd.

    ![Gegevensbron selecteren](media/grafana/select-data-source.png)

1. Kopieer in het queryvenster in de volgende query en selecteer **Uitvoeren**. De query buckets het aantal gebeurtenissen per dag voor de voorbeeldgegevensset.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Query uitvoeren](media/grafana/run-query.png)

1. De grafiek geeft geen resultaten weer omdat deze standaard wordt weergegeven in gegevens van de afgelopen zes uur. Selecteer Last 6 **uur**in het bovenste menu.

    ![Laatste zes uur](media/grafana/last-six-hours.png)

1. Geef een aangepast bereik op dat betrekking heeft op 2007, het jaar dat is opgenomen in onze voorbeeldgegevensset StormEvents. Selecteer **Toepassen**.

    ![Aangepast datumbereik](media/grafana/custom-date-range.png)

    Nu toont de grafiek de gegevens van 2007, die door dag worden geemmerd.

    ![Voltooide grafiek](media/grafana/finished-graph.png)

1. Selecteer in het bovenste menu het pictogram Opslaan: ![Het pictogram Save](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Waarschuwingen maken

1. Selecteer in het startdashboard de optie > **Meldingskanalen** **waarschuwen**om een nieuw meldingskanaal te maken

    ![meldingskanaal maken](media/grafana/create-notification-channel.png)

1. Een nieuw **meldingskanaal**maken en vervolgens **opslaan**.

    ![Nieuw meldingskanaal maken](media/grafana/new-notification-channel-adx.png)

1. Selecteer **Bewerken** in de vervolgkeuzelijst in het **dashboard.**

    ![selecteren bewerken in dashboard](media/grafana/edit-panel-4-alert.png)

1. Selecteer het waarschuwingsbelpictogram om het **deelvenster Waarschuwing te** openen. Selecteer **Waarschuwing maken**. Voltooi de volgende eigenschappen in het **deelvenster Waarschuwing.**

    ![waarschuwingseigenschappen](media/grafana/alert-properties.png)

1. Selecteer het **pictogram Dashboard opslaan** om uw wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

* [Query's schrijven voor Azure Data Explorer](write-queries.md)
