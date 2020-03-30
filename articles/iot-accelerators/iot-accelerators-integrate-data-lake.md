---
title: Gegevens streamen van externe bewaking naar Data Lake Store - Azure | Microsoft Documenten
description: Meer informatie over het integreren van de oplossing voor externe bewaking met Azure Data Lake Store met behulp van een Azure Stream Analytics-taak.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 0a684151e01b298c60ff17ef1470e0648a425850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889240"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>De oplossing voor externe bewaking integreren met Azure Data Lake Store

Mogelijk hebt u geavanceerde analysevereisten die verder gaan dan wat wordt aangeboden in de oplossing voor externe monitoring. Azure Data Lake Store is ideaal voor deze toepassing omdat het gegevens uit enorme en diverse gegevenssets kan opslaan en kan integreren met Azure Data Lake Analytics om on-demand analyses te bieden.

In deze how-to gebruikt u een Azure Stream Analytics-taak om gegevens van de IoT-hub in uw oplossing voor externe bewaking te streamen naar een Azure Data Lake Store.

## <a name="prerequisites"></a>Vereisten

Om deze how-to te voltooien, heb je het volgende nodig:

* [Implementeer de remote monitoring oplossingsversneller.](quickstart-remote-monitoring-deploy.md)
  * De oplossing voor externe bewaking implementeert de Functie IoT-hub en Azure Stream Analytics die in dit artikel wordt gebruikt in uw Azure-abonnement.
* [Een Azure Data Lake Store implementeren](../data-lake-store/data-lake-store-get-started-portal.md)
  * Uw Data Lake Store moet worden geïmplementeerd in dezelfde regio als uw oplossing voor externe bewaking.
  * [Maak een map](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) met de naam 'streaming' in je account.

## <a name="create-a-consumer-group"></a>Een consumentengroep maken

Maak een speciale consumentengroep in de IoT-hub van uw Remote Monitoring-oplossing. Dit wordt gebruikt door de streamanalytics-taak voor het streamen van gegevens naar uw Data Lake Store.

> [!NOTE]
> Consumentengroepen worden door toepassingen gebruikt om gegevens uit Azure IoT Hub te halen. U moet een nieuwe consumentengroep creëren voor elke vijf outputconsumenten. U maximaal 32 consumentengroepen maken.

1. Meld u aan bij Azure Portal.

1. Klik in de Azure-portal op de knop **Cloud Shell.**

    ![Pictogram Portal-lancering](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Voer deze opdracht uit om een nieuwe consumentengroep te maken:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Gebruik de resourcegroep en IoT-hubnamen van uw oplossing voor externe bewaking.

## <a name="create-stream-analytics-job"></a>Functie Stream Analytics maken

Maak een Azure Stream Analytics-taak om de gegevens van uw IoT-hub naar uw Azure Data Lake-winkel te streamen.

1. Klik **op Een bron maken,** selecteer Internet of Things op de Marketplace en klik op De taak Analytics **streamen**.

    ![Nieuwe functie voor Stream Analytics](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Voer een taaknaam in en selecteer de juiste groep Abonnement en Resource.

1. Selecteer een locatie in de buurt of in dezelfde regio als uw Data Lake Store. Hier gebruiken we Oost-VS.

1. Zorg ervoor dat u de hostingomgeving verlaat als de **standaardcloud.**

1. Klik **op Maken**.

    ![Functie Stream Analytics maken](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>De taak Stream Analytics configureren

1. Ga naar de **functie Stream Analytics** in de brongroep externe bewakingsoplossingen.

1. Klik op de pagina Overzicht op **Invoer**.

    ![Overzichtspagina](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Klik **op Streaminvoer toevoegen** en selecteer **IoT-hub** in de vervolgkeuzelijst.

    ![Invoer toevoegen](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Voer op het tabblad Nieuwe invoer een invoeralias van **IoTHub**in .

1. Selecteer in de vervolgkeuzelijst Consumentengroep de consumentengroep die u eerder hebt gemaakt. Hier zijn we met behulp van **streamanalyticsjob**.

    ![Invoer selecteren](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Klik op **Opslaan**.

1. Klik op de pagina Overzicht op **Uitvoer**.

    ![Gegevens meerarchief toevoegen](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Klik **op Toevoegen** en selecteer Data Lake **Store** in de vervolgkeuzelijst.

    ![Uitvoer toevoegen](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Voer op het tabblad Nieuwe uitvoer een uitvoeralias van **DataLakeStore in**.

1. Selecteer het Data Lake Store-account dat u in eerdere stappen hebt gemaakt en geef mapstructuur om gegevens naar de winkel te streamen.

1. Voer in het veld Datumnotatie **/streaming/{date}/{time}** in . Laat de standaarddatumnotatie van YYYY/MM/DD en Tijdnotatie van HH.

    ![Mapstructuur bieden](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Klik **op Autoriseren**.

    U moet autoriseren met Data Lake Store om de opdracht Stream analytics toegang te geven tot het bestandssysteem.

    ![Streamanalytics autoriseren in Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    U ziet een pop-up en zodra de pop-up sluit Authorizeing knop zal worden grijs uitgeschakeld nadat de autorisatie is voltooid.

    > [!NOTE]
    > Als er een fout in het pop-upvenster wordt weergegeven, opent u een nieuw browservenster in de incognitomodus en probeert u het opnieuw.

1. Klik op **Opslaan**.

## <a name="edit-the-stream-analytics-query"></a>De query Stream Analytics bewerken

Azure Stream Analytics gebruikt een SQL-achtige querytaal om een invoerbron op te geven die gegevens streamt, die gegevens naar wens transformeert en uitvoer naar verschillende opslag- of verwerkingsbestemmingen.

1. Klik op het tabblad Overzicht op **Query bewerken**.

    ![Query bewerken](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. Vervang in de queryeditor de tijdelijke aanduidingen [YourOutputAlias] en [YourInputAlias] door de waarden die u eerder hebt gedefinieerd.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Query voor streamanalyses](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Klik op **Opslaan**.
1. Klik **op Ja** om de wijzigingen te accepteren.

## <a name="start-the-stream-analytics-job"></a>De functie Stream Analytics starten

1. Klik op het tabblad Overzicht op **Start**.

    ![Stream Analytics-taak starten](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Klik op het tabblad Taak starten op **Aangepast**.

1. Stel de aangepaste tijd in om een paar uur terug te gaan om gegevens op te halen van wanneer uw apparaat is begonnen met streamen.

1. Klik op **Start**.

    ![Aangepaste datum kiezen](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Wacht tot de taak in de loopstatus wordt uitgevoerd, als u fouten ziet die uit uw query kunnen komen, controleert u of de syntaxis juist is.

    ![Taak die loopt](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    De streamingtaak begint gegevens uit uw IoT-hub te lezen en de gegevens op te slaan in uw Data Lake Store. Het kan enkele minuten duren voordat de gegevens in uw Data Lake Store worden weergegeven.

## <a name="explore-the-streaming-data"></a>Ontdek de streaminggegevens

1. Ga naar uw Data Lake Store.

1. Klik op het tabblad Overzicht op **Gegevensverkenner**.

1. Inzoomen op de map **/streaming** in de gegevensverkenner. U ziet mappen die zijn gemaakt met yyyy/mm/dd/hh-indeling.

    ![Streaminggegevens verkennen](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    U ziet json-bestanden met één bestand per uur.

    ![Streaminggegevens verkennen](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Volgende stappen

Azure Data Lake Analytics kan worden gebruikt om big data-analyses uit te voeren op uw Data Lake Store-gegevenssets. Meer informatie over de [Data Lake Analytics-documentatie](https://docs.microsoft.com/azure/data-lake-analytics).
