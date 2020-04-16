---
title: 'Snelstart: een Apache Spark-pool (voorbeeld) maken in Azure Synapse Analytics'
description: In deze quickstart wordt uitgelegd hoe u de webtools gebruiken om een Apache Spark-pool (preview) te maken in Azure Synapse Analytics en een Spark SQL-query uit te voeren.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 04/15/2020
ms.openlocfilehash: 5762c074b825c1282959c509c2c72d232f0a238c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424298"
---
# <a name="quickstart-create-an-apache-spark-pool-preview-in-synapse-analytics-using-web-tools"></a>Snelstart: een Apache Spark-pool (preview) maken in Synapse Analytics met webtools

In deze quickstart leert u hoe u een Apache Spark-pool (preview) maakt in Azure Synapse Analytics met behulp van webhulpprogramma's. Vervolgens leert u verbinding te maken met de Apache Spark-pool en spark SQL-query's uit te voeren tegen bestanden en tabellen. Apache Spark maakt het mogelijk om snelle gegevensanalyses en clusterberekeningen uit te voeren met behulp van verwerking in het geheugen. Zie [Overzicht: Apache Spark op Azure Synapse Analytics](apache-spark-overview.md)voor informatie over Spark over Synapse Analytics.

> [!IMPORTANT]
> Facturering voor Spark-exemplaren wordt naar rato per minuut berekend, of u ze nu gebruikt of niet. Sluit uw Spark-exemplaar af nadat u deze hebt gebruikt of stel een korte time-out in. Zie voor meer informatie de sectie **Resources opschonen** van dit artikel.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-apache-spark-pool"></a>Een Apache Spark-pool maken

In dit artikel ziet u hoe u een nieuwe Apache Spark-pool maakt met behulp van webtools.

## <a name="create-a-notebook"></a>Een notebook maken

Een notitieblok is een interactieve omgeving die verschillende programmeertalen ondersteunt. Met het notitieblok u communiceren met uw gegevens, code combineren met afwaardering, tekst en eenvoudige visualisaties uitvoeren.

1. Selecteer **Synapse Studio starten**in de Azure-portalweergave voor de Synapse Analytics-werkruimte die u wilt gebruiken.
2. Zodra Synapse Analytics Studio is gestart, selecteert u **Ontwikkelen**. Plaats vervolgens de plaats van de **ingang notitieblokken.** Selecteer de ellips (**...**).
3. Selecteer van daaruit **Nieuw notitieblok**. Er wordt een nieuw notitieblok gemaakt en geopend met een automatisch gegenereerde naam.
  ![Nieuw notitieblok](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-new-notebook.png "Nieuw notitieblok")

4. Geef in het venster **Eigenschappen** een naam op voor het notitieblok.
5. Klik op de werkbalk op **Publiceren**.
6. Als er slechts één Apache Spark-pool in uw werkruimte is, wordt deze standaard geselecteerd. Gebruik de vervolgkeuzelijst om de juiste Groep Apache Spark te selecteren als deze is geselecteerd.
7. Klik **op Code toevoegen**. De standaardtaal `Pyspark`is . Je gaat een mix van Pyspark en Spark SQL gebruiken, dus de standaardkeuze is prima.
8. Vervolgens maakt u een eenvoudig Spark DataFrame-object om te manipuleren. In dit geval maakt u het uit code. Er zijn drie rijen en drie kolommen:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Voer de cel nu uit met een van de volgende methoden:

   - Druk op **Shift + ENTER**.
   - Selecteer het blauwe afspeelpictogram links van de cel.
   - Selecteer de knop **Alles uitvoeren** op de werkbalk.

   ![Object voor gegevensframe maken](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-create-data-frame-object.png "Uitvoer van de spark-taak")

10. Als de instantie van de Apache Spark-pool nog niet wordt uitgevoerd, wordt deze automatisch gestart. U de status van de instantie apache spark pool onder de cel die u uitvoert en ook op het statuspaneel onder aan het notitieblok zien. Afhankelijk van de grootte van het zwembad, moet starten 2-5 minuten duren. Zodra de code is voltooid, geeft informatie onder de cel aan hoe lang het duurde om te worden uitgevoerd en hoe lang de uitvoering ervan is uitgevoerd. In de uitvoercel ziet u de uitvoer.

    ![Uitvoer van het uitvoeren van een cel](./media/apache-spark-notebook-create-spark-use-sql/run-cell-with-output.png "Uitvoer van de spark-taak")

11. De gegevens bestaan nu in een DataFrame vanaf daar u de gegevens op veel verschillende manieren gebruiken. Je gaat het nodig hebben in verschillende formaten voor de rest van deze quickstart.
12. Voer de onderstaande code in een andere cel in en voer deze uit, hiermee wordt een Spark-tabel, een CSV en een parketbestand gemaakt met kopieën van de gegevens:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Als u de storage explorer gebruikt, is het mogelijk om de impact van de twee verschillende manieren van schrijven van een bestand hierboven te zien. Wanneer er geen bestandssysteem is opgegeven, wordt `default>user>trusted-service-user>demo_df`de standaardinstelling gebruikt, in dit geval . De gegevens worden opgeslagen op de locatie van het opgegeven bestandssysteem.

    Let op in zowel de "csv" en "parket" formaten, schrijf bewerkingen een directory is gemaakt met veel partitiebestanden.

    ![Weergave opslagverkenner van de uitvoer](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage.png "Weergave opslagverkenner van de uitvoer")

    ![Weergave opslagverkenner van de uitvoer](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage2.png "Weergave opslagverkenner van de uitvoer")

## <a name="run-spark-sql-statements"></a>Spark SQL-instructies uitvoeren

SQL (Structured Query Language) is de meest voorkomende en gebruikte taal voor het uitvoeren van query's en het definiëren van gegevens. Spark SQL fungeert als een uitbreiding van Apache Spark voor het verwerken van gestructureerde gegevens, met behulp van de bekende SQL-syntaxis.

1. Plak de volgende code in een lege cel en voer de code uit. De opdracht geeft een lijst van de tabellen in het zwembad.

   ```sql
   %%sql
   SHOW TABLES
   ```

   Wanneer u een notitieblok gebruikt met uw Apache Spark-pool `sqlContext` van Synapse Analytics, krijgt u een voorinstelling die u gebruiken om query's uit te voeren met Spark SQL. `%%sql`vertelt het notitieblok de `sqlContext` voorinstelling te gebruiken om de query uit te voeren. De query haalt de bovenste 10 rijen op uit een systeemtabel die standaard wordt geleverd met alle Synapse Analytics Apache Spark-pools.

2. Voer een andere query uit om de gegevens in `demo_df` te zien.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    De code produceert twee uitvoercellen, een die gegevensresultaten bevat, de andere, die de taakweergave weergeeft.

    Standaard toont de resultatenweergave een raster, maar er is een view switcher onder het raster waarmee de weergave kan schakelen tussen raster- en grafiekweergaven.

    ![Query-uitvoer in Synapse Analytics Spark](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query.png "Query-uitvoer in Synapse Analytics Spark")

3. Selecteer **Grafiek** in de **switcher Van**
4. Selecteer het pictogram **Opties weergeven** aan de rechterkant.
5. Selecteer in het veld **Grafiektype** de optie 'staafdiagram'.
6. Selecteer in het kolomveld X-as de optie 'status'.
7. Selecteer in het kolomveld Y-as de optie 'salaris'.
8. Selecteer in het veld **Aggregatie** de optie 'AVG'.
9. Selecteer **Toepassen**.

   ![Grafiekuitvoer in Synapse Analytics Spark](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query-chart-output.png "Grafiekuitvoer in Synapse Analytics Spark")

10. Het is mogelijk om dezelfde ervaring te krijgen met het uitvoeren van SQL, maar zonder van taal te hoeven wisselen. U dit doen door de SQL-cel hierboven te vervangen door deze PySpark-cel, de uitvoerervaring is hetzelfde omdat de **weergaveopdracht** wordt gebruikt:

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Elk van de cellen die eerder zijn uitgevoerd, had de optie om naar **History Server** en **Monitoring**te gaan. Als u op de koppelingen klikt, gaat u naar verschillende delen van de gebruikerservaring.

## <a name="clean-up-resources"></a>Resources opschonen

Synapse Analytics slaat uw gegevens op in Azure Data Lake Storage. U een Spark-instantie veilig laten afsluiten wanneer deze niet in gebruik is. Er worden kosten in rekening gebracht voor een Synapse Analytics Apache Spark-pool zolang deze wordt uitgevoerd, zelfs als deze niet in gebruik is. Aangezien de kosten voor het zwembad zijn vele malen meer dan de kosten voor opslag, is het economisch zinvol om spark gevallen stilgelegd wanneer ze niet in gebruik zijn.

Als u wilt dat de Spark-instantie wordt afgesloten, beëindigt u alle verbonden sessies(notitieblokken). Het zwembad wordt afgesloten wanneer de in de Apache Spark-pool opgegeven **idle-tijd** is bereikt. U de **eindsessie** ook selecteren in de statusbalk onder aan het notitieblok.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Synapse Analytics Apache Spark-pool maken en een eenvoudige Spark SQL-query uitvoeren.

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET voor Apache Spark-documentatie](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Apache Spark officiële documentatie](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Sommige van de officiële Apache Spark-documentatie is gebaseerd op het gebruik van de Spark-console, die niet beschikbaar is op Azure Synapse Spark. Gebruik in plaats daarvan de [ervaringen van het notitieblok](../spark/apache-spark-notebook-create-spark-use-sql.md) of [IntelliJ.](../spark/intellij-tool-synapse.md)
