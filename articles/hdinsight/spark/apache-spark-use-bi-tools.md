---
title: 'Zelfstudie: Azure HDInsight Apache Spark-gegevens analyseren met Power BI'
description: Zelfstudie - Microsoft Power BI gebruiken om Apache Spark-gegevens die zijn opgeslagen hdInsight-clusters te visualiseren
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 03/02/2020
ms.openlocfilehash: d7330225ecbdc6715847821a47c140a3c2b8d1b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251955"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Zelfstudie: Gegevens van Apache Spark analyseren met Power BI in HDInsight

In deze zelfstudie leert u hoe u [Microsoft Power BI kunt](https://powerbi.microsoft.com/) gebruiken om gegevens in een Apache Spark-cluster in Azure [HDInsight](https://azure.microsoft.com/services/hdinsight/)te visualiseren.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Spark-gegevens visualiseren met behulp van Power BI

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Deze zelfstudie uitvoeren[: Gegevens laden en query's uitvoeren in een Apache Spark-cluster in Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI-bureaublad](https://powerbi.microsoft.com/en-us/desktop/).

* Optioneel: [Abonnement op Power BI-proefversie](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>De gegevens controleren

Het [Jupyter Notebook](https://jupyter.org/) dat u hebt gemaakt in de [vorige zelfstudie](apache-spark-load-data-run-query.md) bevat code voor het maken van een `hvac`-tabel. Deze tabel is gebaseerd op het CSV-bestand `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`dat beschikbaar is op alle HDInsight Spark-clusters op . Gebruik de volgende procedure om de gegevens te controleren.

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

1. Open Power BI Desktop. Sluit het welkomstscherm voor het opstarten als het wordt geopend.

2. Navigeer op het tabblad **Start** naar **Gegevens** > **meer ophalen..**.

    ![Haal gegevens in Power BI Desktop van HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Haal gegevens in Power BI van Apache Spark BI")

3. Typ `Spark` in het zoekvak Azure **HDInsight Spark**en selecteer **Connect**.

    ![Haal gegevens in Power BI van Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Haal gegevens in Power BI van Apache Spark BI")

4. Voer de URL van `mysparkcluster.azurehdinsight.net`uw cluster (in het formulier) in het tekstvak **Server** in.

5. Selecteer **DirectQuery**onder **de modus Gegevensconnectiviteit**. Selecteer vervolgens **OK**.

    U kunt beide gegevensverbindingsmodi gebruiken met Spark. Als u DirectQuery gebruikt, worden wijzigingen doorgevoerd in rapporten zonder dat de hele gegevensset wordt vernieuwd. Als u gegevens importeert, moet u de gegevensset vernieuwen om de wijzigingen te zien. Zie [DirectQuery gebruiken in Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/) voor meer informatie over hoe en wanneer u DirectQuery kunt gebruiken.

6. Voer de HDInsight-inloggegevens in en selecteer **Verbinding maken**. De standaardaccountnaam is *admin*.

7. Selecteer `hvac` de tabel, wacht op een voorbeeld van de gegevens en selecteer **Laden**.

    ![Spark-clustergebruikersnaam en -wachtwoord](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark-clustergebruikersnaam en -wachtwoord")

    Power BI Desktop beschikt over de gegevens die nodig zijn om verbinding te maken met het Spark-cluster en om gegevens te laden uit de tabel `hvac`. De tabel en de kolommen worden weergegeven in het deelvenster **Velden**.

8. Visualiseer het verschil tussen de gewenste temperatuur en de werkelijke temperatuur voor elk gebouw:

    1. Selecteer **Vlakdiagram** in het deelvenster **Visualisaties**.

    2. Sleep het veld **BuildingID** naar **As**, en sleep de velden **ActualTemp** en **TargetTemp** naar **Waarde**.

        ![waardekolommen toevoegen](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "waardekolommen toevoegen")

        Het diagram ziet er zo uit:

        ![som van de gebiedsgrafiek](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "som van de gebiedsgrafiek")

        De visualisatie bevat standaard de som van **ActualTemp** en **TargetTemp**. Selecteer de pijl-omlaag naast **ActualTemp** en **TragetTemp** in het deelvenster Visualisaties, u zien **dat Som** is geselecteerd.

    3. Selecteer de pijl-omlaag naast **ActualTemp** en **TragetTemp** in het deelvenster Visualisaties en selecteer **Gemiddeld** om een gemiddelde werkelijke en doeltemperaturen voor elk gebouw te krijgen.

        ![gemiddelde van de waarden](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "gemiddelde van de waarden")

        De gegevensvisualisatie moet er ongeveer uitzien zoals in de schermafbeelding. Beweeg de cursor over de visualisatie om knopinfo met relevante gegevens weer te geven.

        ![gebiedsgrafiek](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "gebiedsgrafiek")

9. Navigeer naar **Bestand** > **Opslaan,** voer de naam `BuildingTemperature` voor het bestand in en selecteer **Opslaan**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Het rapport publiceren naar de Power BI-service (optioneel)

Met behulp van de Power BI-service kunt u rapporten en dashboards delen binnen uw organisatie. In dit gedeelte gaat u eerst de gegevensset en het rapport publiceren. Vervolgens maakt u het rapport vast aan een dashboard. Dashboards worden meestal gebruikt om zich te concentreren op een subset van gegevens in een rapport. U hebt slechts één visualisatie in uw rapport, maar het is nog steeds handig om de stappen te doorlopen.

1. Open Power BI Desktop.

1. **Selecteer**Publiceren op het tabblad **Start** .

    ![Publiceren vanaf Power BI-bureaublad](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publiceren vanuit Power BI Desktop")

1. Selecteer een werkruimte om uw gegevensset te publiceren en rapporteert aan en selecteer **Vervolgens Selecteren**. In de volgende afbeelding is de standaardwerkruimte **Mijn werkruimte** geselecteerd.

    ![Werkruimte selecteren om gegevensset te publiceren en rapporteren aan](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Werkruimte selecteren om gegevensset te publiceren en rapporteren aan")

1. Nadat de publicatie is geslaagd, selecteert u **Open 'BuildingTemperature.pbix' in Power BI**.

    ![Succes publiceren, klik om referenties in te voeren](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Succes publiceren, klik om referenties in te voeren")

1. Selecteer in de Power BI-service De optie **Referenties invoeren**.

    ![Referenties invoeren in De Dienst van Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Referenties invoeren in De Dienst van Power BI")

1. Selecteer **Referenties bewerken**.

    ![Referenties bewerken in De Power BI-service](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Referenties bewerken in De Power BI-service")

1. Voer de inloggegevens van HDInsight in en selecteer **Aanmelden**. De standaardaccountnaam is *admin*.

    ![Aanmelden bij Spark-cluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Aanmelden bij Spark-cluster")

1. Ga in het linkerdeelvenster naar **Rapporten voor werkruimten** > **mijn werkruimte** > **en**selecteer **Vervolgens Gebouwtemperatuur**.

    ![Rapport onder rapporten in linkerdeelvenster](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Rapport onder rapporten in linkerdeelvenster")

    Ook moet **BuildingTemperature** worden vermeld **GEGEVENSSETS** in het linkerdeelvenster.

    De visualisatie die u hebt gemaakt in Power BI Desktop is nu beschikbaar in de Power BI-service.

1. Plaats de cursor boven de visualisatie en selecteer het pictogram van de pin in de rechterbovenhoek.

    ![Rapport in de Power BI-service](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Rapport in de Power BI-service")

1. Selecteer 'Nieuw dashboard', `Building temperature`voer de naam in en selecteer **Vastmaken**.

    ![Vastmaken aan nieuw dashboard](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Vastmaken aan nieuw dashboard")

1. Selecteer in het rapport **Ga naar dashboard**.

De visualisatie wordt vastgemaakt aan het dashboard. U kunt andere visualisaties toevoegen aan het rapport en deze aan hetzelfde dashboard vastmaken. Zie [Rapporten in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) en [Dashboards in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) voor meer informatie over rapporten en dashboards.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de zelfstudie hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. Er worden ook kosten in rekening gebracht voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

Zie [Een HDInsight-cluster verwijderen met uw browser, PowerShell of Azure CLI](../hdinsight-delete-cluster.md)als u een cluster wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u [Microsoft Power BI kunt](https://powerbi.microsoft.com/) gebruiken om gegevens in een Apache Spark-cluster in Azure [HDInsight](https://azure.microsoft.com/services/hdinsight/)te visualiseren. Ga door naar het volgende artikel om te zien dat u een machine learning-toepassing maken.

> [!div class="nextstepaction"]
> [Een machine learning-toepassing maken](./apache-spark-ipython-notebook-machine-learning.md)