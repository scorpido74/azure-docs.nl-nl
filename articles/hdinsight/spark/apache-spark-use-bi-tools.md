---
title: 'Zelfstudie: Gegevens van Apache Spark analyseren met Power BI in Azure HDInsight '
description: Zelf studie-micro soft Power BI gebruiken om Apache Spark gegevens opgeslagen HDInsight-clusters te visualiseren
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: fe7560f68690233d7a333724274a259d693123d0
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035686"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Zelfstudie: Gegevens van Apache Spark analyseren met Power BI in HDInsight

In deze zelf studie leert u hoe u [micro soft power bi](https://powerbi.microsoft.com/) kunt gebruiken om gegevens te visualiseren in een Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Spark-gegevens visualiseren met behulp van Power BI

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Voltooi het artikel [Tutorial: Gegevens laden en query's uitvoeren op een Apache Spark cluster in azure HDInsight @ no__t-0.

* [Power bi Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Optioneel: [Power bi proef abonnement](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>De gegevens controleren

Het [Jupyter Notebook](https://jupyter.org/) dat u hebt gemaakt in de [vorige zelfstudie](apache-spark-load-data-run-query.md) bevat code voor het maken van een `hvac`-tabel. Deze tabel is gebaseerd op het CSV-bestand dat beschikbaar is op alle HDInsight Spark-clusters op `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Gebruik de volgende procedure om de gegevens te controleren.

1. Plak de volgende code uit het Jupyter-notebook en druk vervolgens op **Shift+Enter**. Deze code controleert of de tabellen bestaan.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    De uitvoer ziet er als volgt uit:

    ![Tabellen weergeven in Spark](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Als u het notebook voorafgaand aan deze zelfstudie hebt gesloten, is `hvactemptable` opgeschoond en wordt deze niet opgenomen in de uitvoer.  Alleen Hive-tabellen die zijn opgeslagen in de metastore (aangegeven met **False** in de kolom **isTemporary**) zijn toegankelijk vanuit de BI-hulpprogramma's. In deze zelfstudie maakt u verbinding met de **hvac**-tabel die u hebt gemaakt.

2. Plak de volgende code in een lege cel en druk op **Shift+Enter**. De code controleert de gegevens in de tabel.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    De uitvoer ziet er als volgt uit:

    ![Rijen uit de hvac-tabel in Spark weergeven](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. Klik in het menu **File** van het notebook op **Close and Halt**. Sluit het notebook om de resources vrij te geven.

## <a name="visualize-the-data"></a>De gegevens visualiseren

In dit gedeelte gebruikt u Power BI om visualisaties, rapporten en dashboards te maken van de gegevens in het Spark-cluster.

### <a name="create-a-report-in-power-bi-desktop"></a>Een rapport maken in Power BI Desktop

De eerste stappen om te werken met Spark zijn verbinding maken met het cluster in Power BI Desktop, gegevens uit het cluster laden en eenvoudige visualisatie maken op basis van die gegevens.

> [!NOTE]  
> De connector die in dit artikel wordt gedemonstreerd is momenteel in preview. Eventuele feedback kunt u doorgeven via de site [Power BI-Community](https://community.powerbi.com/) of via [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Open Power BI Desktop. Sluit het opstart scherm als dit wordt geopend.

2. Ga op het tabblad **Start** naar **gegevens ophalen** > **meer..** .

    ![Gegevens van HDInsight Apache Spark overbrengen naar Power BI Desktop](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Gegevens van HDInsight Apache Spark overbrengen naar Power BI Desktop")

3. Geef `Spark` op in het zoekvak, selecteer **Azure HDInsight Spark**en selecteer vervolgens **verbinding maken**.

    ![Gegevens van Apache Spark BI overbrengen naar Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Gegevens van Apache Spark BI overbrengen naar Power BI")

4. Voer de cluster-URL in (in de vorm `mysparkcluster.azurehdinsight.net`) in het tekstvak **Server** .

5. Onder **modus voor gegevens connectiviteit**selecteert u **DirectQuery**. Selecteer vervolgens **OK**.

    U kunt beide gegevensverbindingsmodi gebruiken met Spark. Als u DirectQuery gebruikt, worden wijzigingen doorgevoerd in rapporten zonder dat de hele gegevensset wordt vernieuwd. Als u gegevens importeert, moet u de gegevensset vernieuwen om de wijzigingen te zien. Zie [DirectQuery gebruiken in Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/) voor meer informatie over hoe en wanneer u DirectQuery kunt gebruiken.

6. Voer de gegevens van het HDInsight-aanmeldings account in en selecteer vervolgens **verbinding maken**. De standaardaccountnaam is *admin*.

7. Selecteer de tabel `hvac`, wacht tot er een voor beeld van de gegevens wordt weer gegeven en selecteer vervolgens **laden**.

    ![Gebruikersnaam en wachtwoord voor Spark-cluster](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Gebruikersnaam en wachtwoord voor Spark-cluster")

    Power BI Desktop beschikt over de gegevens die nodig zijn om verbinding te maken met het Spark-cluster en om gegevens te laden uit de tabel `hvac`. De tabel en de kolommen worden weergegeven in het deelvenster **Velden**.

8. Visualiseer het verschil tussen de gewenste temperatuur en de werkelijke temperatuur voor elk gebouw:

    1. Selecteer **Vlakdiagram** in het deelvenster **Visualisaties**.

    2. Sleep het veld **BuildingID** naar **As**, en sleep de velden **ActualTemp** en **TargetTemp** naar **Waarde**.

        kolom met ![waarden toevoegen](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "kolommen met waarden") toevoegen

        Het diagram ziet er zo uit:

        (./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "som van grafiek totaal gebied") van ![gebieds grafiek]

        De visualisatie bevat standaard de som van **ActualTemp** en **TargetTemp**. Selecteer de pijl-omlaag naast **ActualTemp** en **TragetTemp** in het deel venster visualisaties, u kunt **Sum** selecteren.

    3. Selecteer de pijl-omlaag naast **ActualTemp** en **TragetTemp** in het deel venster visualisaties en selecteer **gemiddeld** om het gemiddelde van de werkelijke en doel temperaturen voor elk gebouw te verkrijgen.

        ![gemiddelde van waarden](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "gemiddelde van waarden")

        De gegevensvisualisatie moet er ongeveer uitzien zoals in de schermafbeelding. Beweeg de cursor over de visualisatie om knopinfo met relevante gegevens weer te geven.

        grafiek vlak ![grafiek](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "gebied")

9. Navigeer naar **File** > **Save**, voer de naam `BuildingTemperature` in voor het bestand en selecteer vervolgens **Opslaan**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Het rapport publiceren naar de Power BI-service (optioneel)

Met behulp van de Power BI-service kunt u rapporten en dashboards delen binnen uw organisatie. In dit gedeelte gaat u eerst de gegevensset en het rapport publiceren. Vervolgens maakt u het rapport vast aan een dashboard. Dash boards worden doorgaans gebruikt om zich te richten op een subset van gegevens in een rapport. U hebt slechts één visualisatie in het rapport, maar het is wel handig om de stappen door te lopen.

1. Open Power BI Desktop.

1. Klik op het tabblad **Startpagina** op **Publiceren**.

    ![Publiceren vanuit Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publiceren vanuit Power BI Desktop")

1. Selecteer de werkruimte waarnaar u de gegevensset wilt publiceren en rapporteren, en klik vervolgens op **Selecteren**. In de volgende afbeelding is de standaardwerkruimte **Mijn werkruimte** geselecteerd.

    ![Werkruimte selecteren voor publiceren en rapporteren van gegevensset](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Werkruimte selecteren voor publiceren en rapporteren van gegevensset") 

1. Nadat het publiceren is voltooid, klikt u op **'BuildingTemperature.pbix' openen in Power BI**.

    ![Publiceren voltooid, klikken om referenties in te voeren](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publiceren voltooid, klikken om referenties in te voeren") 

1. Klik in de Power BI-service op **Referenties invoeren**.

    ![Referenties invoeren in Power BI-service](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Referenties invoeren in Power BI-service")

1. Klik op **Referenties bewerken**.

    ![Referenties bewerken in Power BI-service](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Referenties bewerken in Power BI-service")

1. Voer de gegevens voor het aanmeldingsaccount van HDInsight in en klik vervolgens op **Aanmelden**. De standaardaccountnaam is *admin*.

    ![Aanmelden bij Spark-cluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Aanmelden bij Spark-cluster")

1. Ga in het linkerdeelvenster naar **Werkruimten** > **Mijn werkruimte** > **RAPPORTEN** en klik op **BuildingTemperature**.

    ![Rapport vermeld onder Rapporten in het linkerdeelvenster](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Rapport vermeld onder Rapporten in het linkerdeelvenster")

    Ook moet **BuildingTemperature** worden vermeld **GEGEVENSSETS** in het linkerdeelvenster.

    De visualisatie die u hebt gemaakt in Power BI Desktop is nu beschikbaar in de Power BI-service. 

1. Beweeg de cursor over de visualisatie en klik vervolgens op de speld in de rechterbovenhoek.

    ![Rapport in de Power BI-service](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Rapport in de Power BI-service")

1. Selecteer 'Nieuw dashboard', voer de naam `Building temperature` in en klik vervolgens op **Vastmaken**.

    ![Vastmaken aan nieuw dashboard](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Vastmaken aan nieuw dashboard")

1. Klik in het rapport op **Naar dashboard**.

De visualisatie wordt vastgemaakt aan het dashboard. U kunt andere visualisaties toevoegen aan het rapport en deze aan hetzelfde dashboard vastmaken. Zie [Rapporten in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) en [Dashboards in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) voor meer informatie over rapporten en dashboards.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de zelfstudie hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig kunt verwijderen wanneer deze niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs wanneer het niet in gebruik is. Omdat de kosten voor het cluster veel keren meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [een HDInsight-cluster verwijderen met behulp van uw browser, Power shell of de Azure cli](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u [micro soft power bi](https://powerbi.microsoft.com/) kunt gebruiken om gegevens te visualiseren in een Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Ga naar het volgende artikel om te zien hoe de gegevens die u hebt geregistreerd in Spark kunnen worden overgebracht naar een BI-hulpprogramma voor analyse zoals Power BI.

> [!div class="nextstepaction"]
> [Een Apache Spark-streamingtaak uitvoeren](apache-spark-eventhub-streaming.md)