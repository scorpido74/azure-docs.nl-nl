---
title: Excel verbinden met Apache Hadoop met Power Query - Azure HDInsight
description: Meer informatie over hoe u profiteren van business intelligence-componenten en Power Query voor Excel gebruiken om toegang te krijgen tot gegevens die zijn opgeslagen in Hadoop op HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: e643c7fe7b18eed30843e7cab3977036435d2112
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435804"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Excel verbinden met Apache Hadoop met Power Query

Een belangrijk kenmerk van de Microsoft big-data-oplossing is de integratie van Microsoft business intelligence (BI)-componenten met Apache Hadoop-clusters in Azure HDInsight. Een primair voorbeeld is de mogelijkheid om Excel te verbinden met het Azure Storage-account dat de gegevens bevat die zijn gekoppeld aan uw Hadoop-cluster met behulp van de Microsoft Power Query voor Excel-invoegtoepassing. In dit artikel u zich uitleven hoe u Power Query instellen en gebruiken om gegevens op te vragen die zijn gekoppeld aan een Hadoop-cluster die met HDInsight wordt beheerd.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Een werkstation waarop Windows 10, 7, Windows Server 2008 R2 of een later besturingssysteem wordt uitgevoerd.
* Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone of Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Microsoft Power Query installeren

Power Query kan gegevens importeren die zijn uitgevoerd of die zijn gegenereerd door een Hadoop-taak die wordt uitgevoerd op een HDInsight-cluster.

In Excel 2016 is Power Query geïntegreerd in het lint Gegevens onder de sectie & transformeren. Download Microsoft Power Query voor Excel voor oudere Excel-versies vanuit het [Microsoft Downloadcentrum](https://go.microsoft.com/fwlink/?LinkID=286689) en installeert deze.

## <a name="import-hdinsight-data-into-excel"></a>HDInsight-gegevens importeren in Excel

Met de Power Query-invoegtoepassing voor Excel u eenvoudig gegevens uit uw HDInsight-cluster importeren in Excel, waar BI-hulpprogramma's zoals PowerPivot en Power Map kunnen worden gebruikt om de gegevens te inspecteren, te analyseren en te presenteren.

1. Excel starten.

1. Maak een nieuwe lege werkmap.

1. Voer de volgende stappen uit op basis van de Excel-versie:

   * Excel 2016

     * Selecteer **Data** > >**gegevens ophalen** > van**Azure** > **van Azure HDInsight (HDFS).**

       ![Hdi. PowerQuery.SelectHdiSource.2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * Selecteer **Power Query** > **uit Azure** > **van Microsoft Azure HDInsight**.

       ![Hdi. PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Let op:** Als u het menu **Power Query** niet ziet, gaat u naar**Invoegtoepassing** **Bestandsopties** > **Options** > en selecteert u **COM-invoegingen in** het vervolgkeuzevak **Beheren** onder aan de pagina. Schakel de knop **Go...** in en controleer of het vakje voor de Power Query voor Excel-invoegtoepassing is ingeschakeld.

       **Let op:** Met Power Query u ook gegevens van HDFS importeren door **Uit andere bronnen**te selecteren.

1. Voer in het dialoogvenster **Azure HDInsight (HDFS)** in het **tekstvak Accountnaam of URL** de naam in van het Azure Blob-opslagaccount dat aan uw cluster is gekoppeld. Selecteer vervolgens **OK**. Dit account kan het standaardopslagaccount of een gekoppeld opslagaccount zijn.  De indeling is `https://StorageAccountName.blob.core.windows.net/`.

1. Voer **voor Accountsleutel**de sleutel in voor het Blob-opslagaccount en selecteer **Verbinding maken**. (U hoeft de accountgegevens alleen in te voeren wanneer u deze winkel voor het eerst hebt geopend.)

1. Dubbelklik in het deelvenster **Navigator** links van de queryeditor op de naam van de Blob-opslagcontainer die aan uw cluster is gekoppeld. Standaard heeft de containernaam dezelfde naam als de clusternaam.

1. Zoek **HiveSampleData.txt** in de kolom **Naam** (het mappad is **.. /hive/warehouse/hivesampletable/**) en selecteer **vervolgens Binary** links van HiveSampleData.txt. HiveSampleData.txt wordt geleverd met het hele cluster. Optioneel u uw eigen bestand gebruiken.

    ![HDI Excel-gegevens voor het importeren van energiequery's](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Als u wilt, u de naam van de kolomnamen wijzigen. Wanneer u klaar bent, selecteert u **Sluiten & Laden**.  De gegevens zijn geladen in uw werkmap:

    ![Geïmporteerde tabel hdi Excel-energiequery](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Power Query gebruiken om gegevens uit HDInsight op te halen in Excel. Op dezelfde manier u gegevens uit HDInsight ophalen in Azure SQL Database. Het is ook mogelijk om gegevens te uploaden naar HDInsight. Zie voor meer informatie de volgende artikelen:

* [Visualiseer Apache Hive-gegevens met Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualiseer interactieve queryhive-gegevens met Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Gebruik Apache Zeppelin om Apache Hive-query's uit te voeren in Azure HDInsight.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Verbind Excel met HDInsight met de Microsoft Hive ODBC-driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Maak verbinding met Azure HDInsight en voer Apache Hive-query's uit met Data Lake Tools voor Visual Studio.](apache-hadoop-visual-studio-tools-get-started.md)
* [Gebruik Azure HDInsight Tool voor Visual Studio Code](../hdinsight-for-vscode.md).
* [Gegevens uploaden naar HDInsight.](./../hdinsight-upload-data.md)
