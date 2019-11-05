---
title: Data Lake-hulpprogram ma's voor Visual Studio & Hortonworks-Azure HDInsight
description: Meer informatie over het gebruik van de Azure Data Lake-hulpprogram ma's voor Visual Studio met de sandbox Hortonworks die wordt uitgevoerd op een lokale VM. Met deze hulpprogram ma's kunt u Hive-en Pig-taken in de sandbox maken en uitvoeren en taak uitvoer en-geschiedenis weer geven.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: e128aaf6e1726b7a1341fefc6df3cdafd3beb880
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500152"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>De Azure Data Lake-hulpprogram ma's voor Visual Studio gebruiken met de sandbox Hortonworks

Azure Data Lake bevat hulpprogram ma's voor het werken met algemene Apache Hadoop-clusters. Dit document bevat de stappen die nodig zijn voor het gebruik van de Data Lake-hulpprogram ma's met de sandbox Hortonworks die op een lokale virtuele machine wordt uitgevoerd.

Met de Hortonworks sandbox kunt u lokaal met Hadoop in uw ontwikkel omgeving werken. Nadat u een oplossing hebt ontwikkeld en deze op schaal wilt implementeren, kunt u vervolgens naar een HDInsight-cluster gaan.

## <a name="prerequisites"></a>Vereisten

* De Hortonworks sandbox, die wordt uitgevoerd op een virtuele machine in uw ontwikkel omgeving. Dit document is geschreven en getest met de sandbox die wordt uitgevoerd in Oracle VirtualBox. Zie [aan de slag met de sandbox Hortonworks](hadoop/apache-hadoop-emulator-get-started.md) voor meer informatie over het instellen van de sandbox. document.

* Visual Studio.

* De [Azure SDK voor .net](https://azure.microsoft.com/downloads/) 2.7.1 of hoger.

* De [Azure data Lake-hulpprogram ma's voor Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Wacht woorden voor de sandbox configureren

Zorg ervoor dat de Hortonworks sandbox wordt uitgevoerd. Volg vervolgens de stappen in het [Hortonworks aan de slag in het sandbox-document van de module](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) . Met deze stappen configureert u het wacht woord voor de SSH-`root` account en het Apache Ambari `admin`-account. Deze wacht woorden worden gebruikt wanneer u verbinding maakt met de sandbox vanuit Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>De hulpprogram ma's verbinden met de sandbox

1. Open Visual Studio, selecteer **weer gave**en selecteer vervolgens **Server Explorer**.

2. Klik vanuit **Server Explorer**met de rechter muisknop op het **HDInsight** -item en selecteer vervolgens **verbinding maken met HDInsight-emulator**.

    ![Server Explorer, waarbij Connect to HDInsight emulator is gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. Voer in het dialoog venster **verbinding maken met HDInsight-emulator** het wacht woord in dat u hebt geconfigureerd voor Ambari.

    ![Scherm afbeelding van het dialoog venster met het tekstvak ambari password is gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Selecteer **Volgende** om door te gaan.

4. Gebruik het veld **wacht woord** om het wacht woord in te voeren dat u hebt geconfigureerd voor het `root`-account. De overige velden op de standaard waarde laten staan.

    ![Scherm afbeelding van het dialoog venster met het tekstvak basis wachtwoord gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    Selecteer **Volgende** om door te gaan.

5. Wacht tot validatie van de Services is voltooid. In sommige gevallen kan de validatie mislukken en wordt u gevraagd om de configuratie bij te werken. Als de validatie mislukt, selecteert u **bijwerken**en wacht u totdat de configuratie en verificatie voor de service is voltooid.

    ![Scherm afbeelding van het dialoog venster met de knop bijwerken gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > Het update proces maakt gebruik van Ambari om de configuratie van de Hortonworks-sandbox te wijzigen in wat er wordt verwacht door de Data Lake-hulpprogram ma's voor Visual Studio.

6. Nadat de validatie is voltooid, selecteert u **volt ooien** om de configuratie te volt ooien.
    Scherm afbeelding van het dialoog venster ![met de knop volt ooien gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > Afhankelijk van de snelheid van uw ontwikkel omgeving en de hoeveelheid geheugen die aan de virtuele machine is toegewezen, kan het enkele minuten duren om de services te configureren en valideren.

Nadat u deze stappen hebt uitgevoerd, hebt u nu een **lokale HDInsight-cluster** vermelding in Server Explorer onder het gedeelte **HDInsight** .

## <a name="write-an-apache-hive-query"></a>Een Apache Hive-query schrijven

Hive biedt een SQL-achtige query taal (HiveQL) voor het werken met gestructureerde gegevens. Gebruik de volgende stappen om te leren hoe u query's op aanvraag kunt uitvoeren op het lokale cluster.

1. Klik in **Server Explorer**met de rechter muisknop op de vermelding voor het lokale cluster dat u eerder hebt toegevoegd en selecteer vervolgens **een Hive-query schrijven**.

    ![Scherm opname van Server Explorer, waarbij een Hive-query is gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    Er wordt een nieuw queryvenster geopend. Hier kunt u snel een query schrijven en verzenden naar het lokale cluster.

2. Voer in het venster nieuwe query de volgende opdracht in:

        select count(*) from sample_08;

    Als u de query wilt uitvoeren, selecteert u **verzenden** boven aan het venster. Zorg dat de andere waarden (**batch** -en server naam) op de standaard waarden staan.

    ![Scherm afbeelding van query venster met de knop verzenden gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    U kunt ook de vervolg keuzelijst naast **verzenden** gebruiken om **Geavanceerd**te selecteren. Met geavanceerde opties kunt u extra opties opgeven wanneer u de taak verzendt.

    ![Scherm afbeelding van het dialoog venster script indienen](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. Nadat u de query hebt verzonden, wordt de taak status weer gegeven. De taak status bevat informatie over de taak, zoals deze wordt verwerkt door Hadoop. **Taak status** geeft de status van de taak. De status wordt regel matig bijgewerkt of u kunt het vernieuwings pictogram gebruiken om de status hand matig te vernieuwen.

    ![Scherm afbeelding van het dialoog venster taak weergave, met de taak status gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    Nadat de **taak status** is gewijzigd in **voltooid**, wordt een gerichte acyclische grafiek (dag) weer gegeven. Dit diagram beschrijft het pad voor uitvoering dat door TEZ is bepaald tijdens het verwerken van de Hive-query. TEZ is de standaard engine voor het uitvoeren van een component op het lokale cluster.

    > [!NOTE]  
    > Apache TEZ is ook de standaard instelling wanneer u gebruikmaakt van HDInsight-clusters op basis van Linux. Dit is niet de standaard waarde op HDInsight op basis van Windows. Als u deze wilt gebruiken, moet u de regel `set hive.execution.engine = tez;` toevoegen aan het begin van de Hive-query.

    Gebruik de koppeling **taak uitvoer** om de uitvoer weer te geven. In dit geval is dit 823, het aantal rijen in de tabel sample_08. U kunt Diagnostische gegevens over de taak weer geven met behulp van het **taak logboek** en het downloaden van koppelingen tussen **garens** .

4. U kunt Hive-taken ook interactief uitvoeren door het **batch** -veld te wijzigen in **Interactive**. Selecteer vervolgens **uitvoeren**.

    ![Scherm opname van interactieve en uitvoer knoppen gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    Met een interactieve query wordt het uitvoer logboek dat tijdens de verwerking is gegenereerd, gestreamd naar het **uitvoer venster HiveServer2** .

    > [!NOTE]  
    > De informatie is hetzelfde als die vanaf de koppeling van het **taak logboek** beschikbaar is nadat een taak is voltooid.

    ![Scherm opname van HiveServer2-uitvoer](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Een Hive-project maken

U kunt ook een project maken dat meerdere Hive-scripts bevat. Gebruik een project wanneer u gerelateerde scripts hebt of scripts wilt opslaan in een versie beheersysteem.

1. Selecteer in Visual Studio **File**, **New**en **project**.

2. Vouw in de lijst met projecten het item **sjablonen**uit, vouw **Azure data Lake**uit en selecteer vervolgens **Hive (HDInsight)** . Selecteer in de lijst met sjablonen Hive-voor **beeld**. Voer een naam en locatie in en selecteer **OK**.

    ![Nieuw project venster, met Azure Data Lake, Hive-voor beeld en OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

Het **Hive-voorbeeld** project bevat twee scripts: **WebLogAnalysis. HQL** en **SensorDataAnalysis. HQL**. U kunt deze scripts verzenden met behulp van dezelfde knop **verzenden** bovenaan het venster.

## <a name="create-an-apache-pig-project"></a>Een Apache Pig-project maken

De component biedt een SQL-achtige taal voor het werken met gestructureerde gegevens en werkt met trans formaties voor gegevens. Varken biedt een taal (Latijn) waarmee u een pijp lijn van trans formaties kunt ontwikkelen. Als u Pig wilt gebruiken met het lokale cluster, voert u de volgende stappen uit:

1. Open Visual Studio en selecteer **bestand**, **Nieuw**en **project**. Vouw in de lijst met projecten het item **sjablonen**uit, vouw **Azure data Lake**uit en selecteer vervolgens **varken (HDInsight)** . Selecteer in de lijst met sjablonen de optie **varken Application**. Voer een naam en locatie in en selecteer **OK**.

    ![Scherm opname van het nieuwe project venster met Azure Data Lake, Pig, Pig-toepassing en OK gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Voer de volgende tekst in als de inhoud van het bestand **script. Pig** dat met dit project is gemaakt.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Omdat Pig gebruikmaakt van een andere taal dan Hive, is de manier waarop u de taken uitvoert, consistent tussen beide talen, met de knop **verzenden** . Als u de vervolg keuzelijst naast **verzenden** selecteert, wordt het dialoog venster Geavanceerd verzenden voor Pig weer gegeven.

    ![Scherm afbeelding van het dialoog venster voor het verzenden van scripts](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. De taak status en uitvoer worden ook weer gegeven, net als bij een Hive-query.

    ![Scherm afbeelding van een voltooide Pig-taak](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Taken weer geven

Met Data Lake-hulpprogram ma's kunt u eenvoudig informatie bekijken over taken die zijn uitgevoerd op Hadoop. Gebruik de volgende stappen om de taken weer te geven die zijn uitgevoerd op het lokale cluster.

1. Klik vanuit **Server Explorer**met de rechter muisknop op het lokale cluster en selecteer vervolgens **taken weer geven**. Er wordt een lijst met taken weer gegeven die zijn verzonden naar het cluster.

    ![Scherm opname van Server Explorer, met gemarkeerde weergave taken](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. Selecteer in de lijst met taken één om de taak details weer te geven.

    ![Scherm opname van de taak browser, met een van de gemarkeerde taken](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    De informatie die wordt weer gegeven, is vergelijkbaar met wat u ziet nadat u een Hive-of Pig-query hebt uitgevoerd, inclusief koppelingen om de uitvoer-en logboek gegevens weer te geven.

3. U kunt de taak ook wijzigen en opnieuw verzenden.

## <a name="view-hive-databases"></a>Hive-data bases weer geven

1. Vouw in **Server Explorer**de vermelding **HDInsight Local cluster** uit en vouw vervolgens **Hive-data bases**uit. De **standaard** -en **xademo** -data bases op het lokale cluster worden weer gegeven. Als u een Data Base uitbreidt, worden de tabellen in de Data Base weer gegeven.

    ![Scherm opname van Server Explorer, met uitgevouwen data bases](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. Als u een tabel uitbreidt, worden de kolommen voor die tabel weer gegeven. Als u de gegevens snel wilt weer geven, klikt u met de rechter muisknop op een tabel en selecteert u **bovenste 100 rijen weer geven**.

    ![Server Explorer, waarbij de tabel is uitgevouwen en de bovenste 100 rijen weer geven geselecteerd](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Data Base-en tabel eigenschappen

U kunt de eigenschappen van een Data Base of tabel weer geven. Als u **Eigenschappen** selecteert, worden details weer gegeven voor het geselecteerde item in het venster Eigenschappen. Zie bijvoorbeeld de informatie in de volgende scherm afbeelding:

![Scherm opname van venster Eigenschappen](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Een tabel maken

Als u een tabel wilt maken, klikt u met de rechter muisknop op een Data Base en selecteert u vervolgens **tabel maken**.

![Scherm opname van Server Explorer, met de markering tabel maken gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

U kunt de tabel vervolgens maken met behulp van een formulier. Onder aan de volgende scherm afbeelding ziet u de onbewerkte HiveQL die wordt gebruikt om de tabel te maken.

![Scherm afbeelding van het formulier dat wordt gebruikt om een tabel te maken](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>Volgende stappen

* [De kabels van de Hortonworks-sandbox leren](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop zelf studie-aan de slag met HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
