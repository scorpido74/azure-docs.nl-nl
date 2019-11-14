---
title: Gegevens visualiseren vanuit Azure Data Explorer met behulp van Grafana
description: In dit artikel leert u hoe u Azure Data Explorer instelt als gegevens bron voor Grafana en vervolgens gegevens visualert vanuit een voorbeeld cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038044"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Gegevens visualiseren vanuit Azure Data Explorer in Grafana

Grafana is een analyse platform dat u in staat stelt om gegevens op te vragen en te visualiseren en vervolgens Dash boards te maken en te delen op basis van uw visualisaties. Grafana biedt een Azure Data Explorer- *invoeg toepassing*waarmee u verbinding kunt maken met en visualiseren van gegevens van Azure Data Explorer. In dit artikel leert u hoe u Azure Data Explorer instelt als gegevens bron voor Grafana en vervolgens gegevens visualert vanuit een voorbeeld cluster.

Gebruik de volgende video om te leren hoe u de Azure Data Explorer-invoeg toepassing van Grafana gebruikt, hoe u Azure Data Explorer instelt als gegevens bron voor Grafana en vervolgens gegevens visualiseren. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

U kunt [de gegevens bron ook configureren](#configure-the-data-source) en [gegevens visualiseren](#visualize-data) zoals beschreven in het onderstaande artikel.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om dit artikel te volt ooien:

* [Grafana-versie 5.3.0 of hoger](https://docs.grafana.org/installation/) voor uw besturings systeem

* De [Azure Data Explorer-invoeg toepassing](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) voor Grafana

* Een cluster dat de StormEvents-voorbeeld gegevens bevat. Zie [Quick Start: een azure Data Explorer-cluster en data base maken](create-cluster-database-portal.md) en [voorbeeld gegevens opnemen in azure Data Explorer](ingest-sample-data.md)voor meer informatie.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Eigenschappen opgeven en de verbinding testen

Wanneer de Service-Principal is toegewezen aan de rol *viewers* , geeft u nu eigenschappen op in uw exemplaar van Grafana en test u de verbinding met Azure Data Explorer.

1. Selecteer in Grafana in het menu links het tandwiel pictogram en vervolgens op **gegevens bronnen**.

    ![Gegevensbronnen](media/grafana/data-sources.png)

1. Selecteer **gegevens bron toevoegen**.

1. Voer op de pagina **gegevens bronnen/nieuw** een naam in voor de gegevens bron en selecteer vervolgens het type **Azure Data Explorer data source**.

    ![Verbindings naam en-type](media/grafana/connection-name-type.png)

1. Voer de naam van uw cluster in met de notatie https://{clustername}. {Region}. kusto. Windows. net. Voer de andere waarden van de Azure Portal of CLI in. Zie de tabel onder de volgende afbeelding voor een toewijzing.

    ![Verbindingseigenschappen](media/grafana/connection-properties.png)

    | Grafana-gebruikers interface | Azure-portal | Azure-CLI |
    | --- | --- | --- |
    | Abonnements-id | ABONNEMENTS-ID | SubscriptionId |
    | Tenant-id | Map-ID | tenant |
    | Client-id | Toepassings-id | appId |
    | Clientgeheim | Wachtwoord | wachtwoord |
    | | | |

1. Selecteer **& test opslaan**.

    Als de test is geslaagd, gaat u naar de volgende sectie. Als u problemen ondervindt, controleert u de waarden die u hebt opgegeven in Grafana en bekijkt u de vorige stappen.

## <a name="visualize-data"></a>Gegevens visualiseren

Nu u klaar bent met het configureren van Azure Data Explorer als gegevens bron voor Grafana, is het tijd om gegevens te visualiseren. Hier wordt een eenvoudig voor beeld weer gegeven, maar u kunt nog veel meer doen. We raden u aan [Schrijf query's voor Azure Data Explorer](write-queries.md) te bekijken om voor beelden van andere query's uit te voeren op basis van de set met voorbeeld gegevens.

1. Selecteer in Grafana in het menu links het plus pictogram en vervolgens het **dash board**.

    ![Dash board maken](media/grafana/create-dashboard.png)

1. Selecteer op het tabblad **toevoegen** de optie **grafiek**.

    ![Grafiek toevoegen](media/grafana/add-graph.png)

1. Selecteer in het deel venster grafiek **paneel titel** en vervolgens **bewerken**.

    ![Paneel bewerken](media/grafana/edit-panel.png)

1. Selecteer onder aan het deel venster **gegevens bron** en selecteer vervolgens de gegevens bron die u hebt geconfigureerd.

    ![Gegevensbron selecteren](media/grafana/select-data-source.png)

1. Kopieer in het query deel venster in de volgende query en selecteer vervolgens **uitvoeren**. De query Bucket het aantal gebeurtenissen per dag voor de ingestelde voorbeeld gegevens.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Query uitvoeren](media/grafana/run-query.png)

1. In de grafiek worden geen resultaten weer gegeven omdat deze standaard in de gegevens van de afgelopen zes uur valt. Selecteer in het bovenste menu de optie **laatste 6 uur**.

    ![Afgelopen zes uur](media/grafana/last-six-hours.png)

1. Geef een aangepast bereik op dat 2007, het jaar dat is opgenomen in de set met StormEvents-voorbeeld gegevens. Selecteer **Toepassen**.

    ![Aangepast datum bereik](media/grafana/custom-date-range.png)

    Nu toont de grafiek de gegevens van 2007, geintervald per dag.

    ![Voltooide grafiek](media/grafana/finished-graph.png)

1. Selecteer in het bovenste menu het pictogram opslaan: ![Het pictogram Save](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Waarschuwingen maken

1. Selecteer in het dash board start **meldingen** > **meldings kanalen** om een nieuw meldings kanaal te maken

    ![meldings kanaal maken](media/grafana/create-notification-channel.png)

1. Maak een nieuw **meldings kanaal**en **Sla**het op.

    ![Nieuw meldings kanaal maken](media/grafana/new-notification-channel-adx.png)

1. Selecteer in de vervolg keuzelijst **bewerken** in het **dash board**.

    ![Selecteer Bewerken in dash board](media/grafana/edit-panel-4-alert.png)

1. Selecteer het pictogram waarschuwings klok om het deel venster **waarschuwing** te openen. Selecteer **waarschuwing maken**. Voer de volgende eigenschappen in het deel venster **waarschuwing** uit.

    ![waarschuwings eigenschappen](media/grafana/alert-properties.png)

1. Selecteer het pictogram **dash board opslaan** om uw wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

* [Query's schrijven voor Azure Data Explorer](write-queries.md)
