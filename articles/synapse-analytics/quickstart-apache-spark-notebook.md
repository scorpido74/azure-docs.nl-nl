---
title: 'Snelstartgids: een Apache Spark notitie blok maken'
description: In deze Quick start ziet u hoe u met de webhulpprogramma's een Apache Spark groep maakt (preview) in azure Synapse Analytics en voert u een Spark SQL-query uit.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 04/15/2020
ms.openlocfilehash: ae866c8be8d162213077e0488a19a556399384b2
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692694"
---
# <a name="quickstart-create-an-apache-spark-pool-preview-in-azure-synapse-analytics-using-web-tools"></a>Snelstartgids: een Apache Spark groep maken (preview) in azure Synapse Analytics met behulp van web-hulpprogram ma's

In deze Quick Start leert u hoe u een Apache Spark pool (preview) maakt in azure Synapse met behulp van web tools. Vervolgens leert u hoe u verbinding maakt met de Apache Spark pool en Spark SQL-query's uitvoert op bestanden en tabellen. Apache Spark maakt het mogelijk om snelle gegevensanalyses en clusterberekeningen uit te voeren met behulp van verwerking in het geheugen. Zie [overzicht: Apache Spark op Azure Synapse](./spark/apache-spark-overview.md)voor meer informatie over Spark in azure Synapse.

> [!IMPORTANT]
> Facturering voor Spark-instanties wordt per minuut naar beneden geclassificeerd, ongeacht of u deze gebruikt. Zorg ervoor dat u de Spark-instantie afsluit nadat u deze hebt gebruikt of een korte time-out hebt ingesteld. Zie voor meer informatie de sectie **Resources opschonen** van dit artikel.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account aan voordat u begint](https:/azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https:/azure.microsoft.com/free/)
- [Synapse Analytics-werk ruimte](quickstart-create-workspace.md)
- [Apache Spark pool](quickstart-create-apache-spark-pool.md)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https:/portal.azure.com/).

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-a-notebook"></a>Een notebook maken

Een notebook is een interactieve omgeving die verschillende programmeer talen ondersteunt. Met het notitie blok kunt u communiceren met uw gegevens, code combi neren met prijs verlaging, tekst en eenvoudige visualisaties.

1. Selecteer in de weer gave Azure Portal voor de Azure Synapse-werk ruimte die u wilt gebruiken, de optie **Start Synapse Studio**.
2. Zodra Synapse Studio is gestart, selecteert u **ontwikkelen**. Beweeg vervolgens de muis aanwijzer over de **notitieblok** vermelding. Selecteer het beletsel teken (**...**).
3. Selecteer vervolgens **Nieuw notitie blok**. Er wordt een nieuw notitie blok gemaakt en geopend met een automatisch gegenereerde naam.
  ![Nieuw notitie blok](./media/quickstart-apache-spark-notebook/spark-get-started-new-notebook.png "Nieuw notitie blok")

4. Geef in het venster **Eigenschappen** een naam op voor het notitie blok.
5. Klik op de werk balk op **publiceren**.
6. Als er slechts één Apache Spark pool in uw werk ruimte is, wordt deze standaard geselecteerd. Gebruik de vervolg keuzelijst om de juiste Apache Spark pool te selecteren als er geen is geselecteerd.
7. Klik op **code toevoegen**. De standaard taal is `Pyspark`. U gaat een combi natie van Pyspark en Spark SQL gebruiken. de standaard keuze is dus prima.
8. Vervolgens maakt u een eenvoudig Spark data frame-object dat u kunt bewerken. In dit geval maakt u deze vanuit code. Er zijn drie rijen en drie kolommen:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Voer nu de cel uit met een van de volgende methoden:

   - Druk op **SHIFT + ENTER**.
   - Selecteer het blauwe afspeel pictogram links van de cel.
   - Selecteer de knop **alles uitvoeren** op de werk balk.

   ![Gegevens frame object maken](./media/quickstart-apache-spark-notebook/spark-get-started-create-data-frame-object.png "Uitvoer van de Spark-taak")

10. Als de instantie van de Apache Spark-groep niet al wordt uitgevoerd, wordt deze automatisch gestart. U ziet de status van het exemplaar van de Apache Spark pool onder de cel die u uitvoert en ook in het status paneel onder aan het notitie blok. Afhankelijk van de grootte van de pool, moet beginnen 2-5 minuten duren. Als de uitvoering van de code is voltooid, wordt in de informatie onder de cel weer gegeven hoe lang het duurde om te worden uitgevoerd en de uitvoering ervan. In de cel uitvoer ziet u de uitvoer.

    ![Uitvoer van het uitvoeren van een cel](./media/quickstart-apache-spark-notebook/run-cell-with-output.png "Uitvoer van de Spark-taak")

11. De gegevens bevinden zich nu in een data frame van waaruit u de gegevens op diverse manieren kunt gebruiken. U hebt deze nodig in verschillende indelingen voor de rest van deze Quick Start.
12. Voer de onderstaande code in een andere cel in en voer deze uit. Hiermee maakt u een Spark-tabel, een CSV en een Parquet-bestand met kopieën van de gegevens:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Als u de opslag Verkenner gebruikt, is het mogelijk om te zien wat het effect is van de twee verschillende manieren om een bestand te schrijven dat hierboven wordt gebruikt. Als er geen bestands systeem is opgegeven, wordt de standaard waarde gebruikt, in `default>user>trusted-service-user>demo_df`dit geval. De gegevens worden opgeslagen op de locatie van het opgegeven bestands systeem.

    U ziet in de indelingen ' CSV ' en ' Parquet ', schrijf bewerkingen een map wordt gemaakt met veel gepartitioneerde bestanden.

    ![De weer gave van de opslag Verkenner van de uitvoer](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage.png "De weer gave van de opslag Verkenner van de uitvoer")

    ![De weer gave van de opslag Verkenner van de uitvoer](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage2.png "De weer gave van de opslag Verkenner van de uitvoer")

## <a name="run-spark-sql-statements"></a>Spark SQL-instructies uitvoeren

Structured Query Language (SQL) is de meest gebruikte en veelgebruikte taal voor het uitvoeren van query's en het definiëren van gegevens. Spark SQL fungeert als een uitbreiding van Apache Spark voor het verwerken van gestructureerde gegevens, met behulp van de bekende SQL-syntaxis.

1. Plak de volgende code in een lege cel en voer de code uit. De opdracht geeft een lijst van de tabellen in de groep.

   ```sql
   %%sql
   SHOW TABLES
   ```

   Wanneer u een notebook gebruikt met uw Azure Synapse Apache Spark pool, krijgt u een vooraf `sqlContext` ingestelde voor instelling die u kunt gebruiken om query's uit te voeren met Spark SQL. `%%sql`geeft aan dat het notitie blok de `sqlContext` voor instelling moet gebruiken om de query uit te voeren. De query haalt de bovenste 10 rijen op uit een systeem tabel die standaard wordt geleverd met alle Azure Synapse-Apache Spark groepen.

2. Voer een andere query uit om de gegevens in `demo_df` te zien.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    De code produceert twee uitvoer cellen, een die gegevens bevat, de andere, waarin de taak weergave wordt weer gegeven.

    In de resultaten weergave wordt standaard een raster weer gegeven. Maar er is een weergave schakelaar onder het raster waarmee de weer gave kan overschakelen tussen raster-en grafiek weergaven.

    ![Query uitvoer in azure Synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query.png "Query uitvoer in azure Synapse Spark")

3. Selecteer in de **weergave** schakelaar de optie **diagram**.
4. Selecteer het pictogram **weergave opties** aan de rechter kant.
5. Selecteer in het veld **grafiek type** de optie staaf diagram.
6. Selecteer status in het kolom veld X-as.
7. Selecteer ' salaris ' in het kolom veld van de Y-as.
8. Selecteer in het veld **aggregatie** de optie ' Gem '.
9. Selecteer **Toepassen**.

   ![Grafiek uitvoer in azure Synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query-chart-output.png "Grafiek uitvoer in azure Synapse Spark")

10. Het is mogelijk om dezelfde ervaring te krijgen met het uitvoeren van SQL, maar zonder dat u talen hoeft te wijzigen. U kunt dit doen door de SQL-cel hierboven te vervangen door deze PySpark-cel, de uitvoer ervaring is hetzelfde omdat de **weer gave** -opdracht wordt gebruikt:

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Elk van de cellen die eerder zijn uitgevoerd, had de optie om naar de **geschiedenis server** en **controle**te gaan. Als u op de koppelingen klikt, gaat u naar verschillende onderdelen van de gebruikers ervaring.

## <a name="clean-up-resources"></a>Resources opschonen

Met Azure Synapse worden uw gegevens opgeslagen in Azure Data Lake Storage. U kunt een Spark-exemplaar veilig uitschakelen wanneer het niet in gebruik is. Er worden kosten in rekening gebracht voor een Azure Synapse Apache Spark pool zolang deze wordt uitgevoerd, zelfs als deze niet in gebruik is. De kosten voor de groep zijn vaak meer dan de kosten voor opslag. Als zodanig is het economisch zinnig dat Spark-instanties worden afgesloten wanneer ze niet worden gebruikt.

Om ervoor te zorgen dat de Spark-instantie wordt afgesloten, beëindigt u alle verbonden sessies (notebooks). De pool wordt afgesloten wanneer de **niet-actieve tijd** die is opgegeven in de Apache Spark pool wordt bereikt. U kunt ook **sessie beëindigen** selecteren in de status balk onder aan het notitie blok.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Synapse Apache Spark pool maakt en een eenvoudige Spark SQL-query uitvoert.

- [Azure Synapse Analytics](overview-what-is.md)
- [Documentatie voor .NET for Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Apache Spark officiële documentatie](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Sommige van de officiële Apache Spark documentatie is gebaseerd op het gebruik van de Spark-console, die niet beschikbaar is in azure Synapse Spark. Gebruik in plaats daarvan de ervaring [notebook](quickstart-apache-spark-notebook.md) of [IntelliJ](./spark/intellij-tool-synapse.md) .
