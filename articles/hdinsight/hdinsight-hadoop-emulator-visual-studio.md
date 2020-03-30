---
title: Data Lake-hulpprogramma's voor Visual Studio-& Hortonworks - Azure HDInsight
description: Meer informatie over het gebruik van de Azure Data Lake-hulpprogramma's voor Visual Studio met de Sandbox Hortonworks die wordt uitgevoerd in een lokale VM. Met deze hulpprogramma's u Hive- en Pig-taken maken en uitvoeren in de sandbox en de taakuitvoer en -geschiedenis bekijken.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: e128aaf6e1726b7a1341fefc6df3cdafd3beb880
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73500152"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>De Azure Data Lake-hulpprogramma's voor Visual Studio gebruiken met de Hortonworks Sandbox

Azure Data Lake bevat hulpprogramma's voor het werken met generieke Apache Hadoop-clusters. Dit document bevat de stappen die nodig zijn om de Data Lake-tools te gebruiken met de Hortonworks Sandbox die in een lokale virtuele machine wordt uitgevoerd.

Met behulp van de Hortonworks Sandbox u lokaal met Hadoop werken aan uw ontwikkelomgeving. Nadat u een oplossing hebt ontwikkeld en deze op schaal wilt implementeren, u vervolgens overgaan naar een HDInsight-cluster.

## <a name="prerequisites"></a>Vereisten

* De Hortonworks Sandbox draait in een virtuele machine op uw ontwikkelomgeving. Dit document is geschreven en getest met de sandbox die wordt uitgevoerd in Oracle VirtualBox. Zie de sandbox Aan de [slag met de sandbox van Hortonworks](hadoop/apache-hadoop-emulator-get-started.md) voor informatie over het instellen van de sandbox. Document.

* Visual Studio.

* De [Azure SDK voor .NET](https://azure.microsoft.com/downloads/) 2.7.1 of hoger.

* De [Azure Data Lake-hulpprogramma's voor Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Wachtwoorden configureren voor de sandbox

Zorg ervoor dat de Hortonworks Sandbox draait. Volg vervolgens de stappen in het [Document Aan de slag in het Document Hortonworks Sandbox.](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) Met deze stappen configureert `root` u het wachtwoord voor `admin` het SSH-account en het Apache Ambari-account. Deze wachtwoorden worden gebruikt wanneer u verbinding maakt met de sandbox van Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>De gereedschappen verbinden met de sandbox

1. Open Visual Studio, selecteer **Weergave**en selecteer **Vervolgens Serververkenner**.

2. Klik in **Server Explorer**met de rechtermuisknop op de **HDInsight-vermelding** en selecteer **vervolgens Verbinding maken met HDInsight Emulator**.

    ![Server Explorer, met Verbinding maken met HDInsight Emulator gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. Voer in het dialoogvenster **Verbinding maken met HDInsight Emulator** het wachtwoord in dat u voor Ambari hebt geconfigureerd.

    ![Schermafbeelding van het dialoogvenster, met het tekstvak ambari-wachtwoord gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Selecteer **Volgende** om door te gaan.

4. Gebruik het veld **Wachtwoord** om het wachtwoord `root` in te voeren dat u voor het account hebt geconfigureerd. Laat de andere velden op de standaardwaarde.

    ![Schermafbeelding van het dialoogvenster, met het tekstvak hoofdwachtwoord gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    Selecteer **Volgende** om door te gaan.

5. Wacht tot de validatie van de services is voltooid. In sommige gevallen kan de validatie mislukken en u vragen om de configuratie bij te werken. Als de validatie mislukt, selecteert u **Bijwerken**en wacht u tot de configuratie en verificatie van de service is voltooid.

    ![Schermafbeelding van het dialoogvenster, met de knop Bijwerken gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > Het updateproces gebruikt Ambari om de Configuratie van De Sandbox van Hortonworks aan te passen aan wat wordt verwacht door de Data Lake-tools voor Visual Studio.

6. Nadat de validatie is voltooid, selecteert u **Voltooien** om de configuratie te voltooien.
    ![Schermafbeelding van het dialoogvenster, met de knop Voltooien gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > Afhankelijk van de snelheid van uw ontwikkelomgeving en de hoeveelheid geheugen die aan de virtuele machine is toegewezen, kan het enkele minuten duren om de services te configureren en te valideren.

Nadat u deze stappen hebt uitgevoerd, hebt u nu een **hdinsight-lokale clustervermelding** in Server Explorer, onder de sectie **HDInsight.**

## <a name="write-an-apache-hive-query"></a>Een Apache Hive-query schrijven

Hive biedt een SQL-achtige querytaal (HiveQL) voor het werken met gestructureerde gegevens. Gebruik de volgende stappen om te leren hoe query's op aanvraag worden uitgevoerd tegen het lokale cluster.

1. Klik in **Server Explorer**met de rechtermuisknop op het item voor het lokale cluster dat u eerder hebt toegevoegd en selecteer Vervolgens **Een bijenkorfquery schrijven**.

    ![Schermafbeelding van Server Explorer, met Een bijenkorfquery schrijven gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    Er wordt een nieuw queryvenster geopend. Hier u snel een query schrijven en indienen bij het lokale cluster.

2. Voer in het nieuwe queryvenster de volgende opdracht in:

        select count(*) from sample_08;

    Als u de query wilt uitvoeren, selecteert **u Verzenden** boven aan het venster. Laat de andere waarden **(Batch-** en servernaam) bij de standaardwaarden.

    ![Schermafbeelding van het queryvenster, met de knop Verzenden gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    U ook het vervolgkeuzemenu naast **Verzenden** gebruiken om **Geavanceerd**te selecteren. Met geavanceerde opties u extra opties bieden wanneer u de taak indient.

    ![Schermafbeelding van het dialoogvenster Hive verzenden van script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. Nadat u de query hebt ingediend, wordt de taakstatus weergegeven. In de taakstatus wordt informatie over de taak weergegeven terwijl deze wordt verwerkt door Hadoop. **Job State** geeft de status van de taak. De status wordt periodiek bijgewerkt of u het vernieuwingspictogram gebruiken om de status handmatig te vernieuwen.

    ![Schermafbeelding van het dialoogvenster Taakweergave, waarin taakstatus is gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    Nadat de **taakstatus** is gewijzigd in **Voltooid,** wordt een gerichte acyclicgrafiek (DAG) weergegeven. In dit diagram wordt het uitvoeringspad beschreven dat door Tez is bepaald bij het verwerken van de Hive-query. Tez is de standaarduitvoeringsengine voor Hive op het lokale cluster.

    > [!NOTE]  
    > Apache Tez is ook de standaard wanneer u hdinsight-clusters op basis van Linux gebruikt. Het is niet de standaard op Windows-gebaseerde HDInsight. Als u deze daar wilt `set hive.execution.engine = tez;` gebruiken, moet u de regel toevoegen aan het begin van uw Hive-query.

    Gebruik de koppeling **Taakuitvoer** om de uitvoer weer te geven. In dit geval is het 823, het aantal rijen in de tabel sample_08. U diagnostische informatie over de taak bekijken via de koppelingen **Job Log** en Download **YARN Log.**

4. U Hive-taken ook interactief uitvoeren door het veld **Batch** te wijzigen in **Interactief**. Selecteer vervolgens **Uitvoeren**.

    ![Schermafbeelding van knoppen Interactief en uitvoeren gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    Een interactieve query streamt het uitvoerlogboek dat tijdens de verwerking wordt gegenereerd naar het venster **HiveServer2-uitvoer.**

    > [!NOTE]  
    > De informatie is hetzelfde als beschikbaar is via de koppeling **Taaklogboek** nadat een taak is voltooid.

    ![Schermafbeelding van HiveServer2-uitvoer](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Een Hive-project maken

U ook een project maken dat meerdere Hive-scripts bevat. Gebruik een project wanneer u gerelateerde scripts hebt of scripts wilt opslaan in een versiebeheersysteem.

1. Selecteer in Visual Studio **Bestand**, **Nieuw**en vervolgens **Project**.

2. Vouw in de lijst met projecten **sjablonen**uit, vouw **Azure Data Lake**uit en selecteer VERVOLGENS HIVE **(HDInsight).** Selecteer **Hive-voorbeeld**in de lijst met sjablonen . Voer een naam en locatie in en selecteer **OK**.

    ![Nieuw projectvenster, met Azure Data Lake, Hive-voorbeeld en OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

Het **Hive Sample** project bevat twee scripts, **WebLogAnalysis.hql** en **SensorDataAnalysis.hql**. U deze scripts indienen met dezelfde **knop Verzenden** boven aan het venster.

## <a name="create-an-apache-pig-project"></a>Een Apache Pig-project maken

Terwijl Hive een SQL-achtige taal biedt voor het werken met gestructureerde gegevens, werkt Pig door transformaties op gegevens uit te voeren. Pig biedt een taal (Pig Latin) waarmee u een pijplijn van transformaties ontwikkelen. Voer de volgende stappen uit om Varken met het lokale cluster te gebruiken:

1. Open Visual Studio en selecteer **Bestand**, **Nieuw**en vervolgens **Project**. Vouw in de lijst met projecten **sjablonen**uit, vouw **Azure Data Lake**uit en selecteer Vervolgens Pig **(HDInsight).** Selecteer **Varkenstoepassing**in de lijst met sjablonen . Voer een naam, locatie in en selecteer **OK**.

    ![Schermafbeelding van het venster Nieuw project, met Azure Data Lake, Pig, Pig Application en OK gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Voer de volgende tekst in als de inhoud van het **bestand script.pig** dat met dit project is gemaakt.

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

    Terwijl Pig een andere taal gebruikt dan Hive, is de manier waarop u de taken uitvoert consistent tussen beide talen, via de knop **Verzenden.** Als u de vervolgkeuzelijst naast **Verzenden** selecteert, wordt een geavanceerd dialoogvenster voor verzenden voor Varken weergegeven.

    ![Schermafbeelding van het dialoogvenster Varken verzenden van script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. De taakstatus en -uitvoer worden ook weergegeven, hetzelfde als een Hive-query.

    ![Schermafbeelding van een voltooide varkenstaak](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Vacatures weergeven

Met data lake-tools u ook eenvoudig informatie bekijken over taken die zijn uitgevoerd op Hadoop. Gebruik de volgende stappen om de taken te bekijken die zijn uitgevoerd op het lokale cluster.

1. Klik in **Server Explorer**met de rechtermuisknop op het lokale cluster en selecteer **Taken weergeven**. Er wordt een lijst weergegeven met taken die in het cluster zijn ingediend.

    ![Schermafbeelding van Server Explorer, met Weergavetaken gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. Selecteer er een in de lijst met taken om de taakgegevens weer te geven.

    ![Schermafbeelding van jobbrowser, met een van de taken gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    De weergegeven informatie is vergelijkbaar met wat u ziet na het uitvoeren van een Hive- of Pig-query, inclusief koppelingen om de uitvoer- en logboekgegevens weer te geven.

3. U de taak ook vanaf hier wijzigen en opnieuw indienen.

## <a name="view-hive-databases"></a>Hive-databases weergeven

1. Vouw in **Server Explorer**de **lokale clusterinvoer van HDInsight** uit en vouw **vervolgens Hive-databases**uit. De **standaard-** en **xademo-databases** op het lokale cluster worden weergegeven. Als u een database uitbreidt, worden de tabellen in de database weergegeven.

    ![Schermafbeelding van Server Explorer, met databases uitgevouwen](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. Als u een tabel uitvouwt, worden de kolommen voor die tabel weergegeven. Als u de gegevens snel wilt weergeven, klikt u met de rechtermuisknop op een tabel en selecteert u **Top 100 Rijen weergeven**.

    ![Server Explorer, met tabel uitgevouwen en Top 100-rijen weergeven geselecteerd](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Database- en tabeleigenschappen

U de eigenschappen van een database of tabel bekijken. Als **u Eigenschappen** selecteert, worden details weergegeven voor het geselecteerde item in het eigenschappenvenster. Zie bijvoorbeeld de informatie in de volgende schermafbeelding:

![Schermafbeelding van het venster Eigenschappen](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Een tabel maken

Als u een tabel wilt maken, klikt u met de rechtermuisknop op een database en selecteert u **Tabel maken**.

![Schermafbeelding van Server Explorer, met tabel maken gemarkeerd](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

U de tabel vervolgens maken met een formulier. Onderaan de volgende schermafbeelding ziet u de ruwe HiveQL die wordt gebruikt om de tabel te maken.

![Schermafbeelding van het formulier dat wordt gebruikt om een tabel te maken](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>Volgende stappen

* [Het leren van de touwen van de Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop tutorial - Aan de slag met HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
