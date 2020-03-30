---
title: Excel & Apache Hadoop met ODBC Driver - Azure HDInsight
description: Meer informatie over het instellen en gebruiken van het Microsoft Hive ODBC-stuurprogramma voor Excel om gegevens in HDInsight-clusters van Microsoft Excel op te vragen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/02/2020
ms.openlocfilehash: f356009197c0446efa2ea2d7f0e90040229df47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251072"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Excel verbinden met Apache Hadoop in Azure HDIsight met behulp van het Hive ODBC-stuurprogramma van Microsoft

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

De Big Data-oplossing van Microsoft integreert BI-componenten (Microsoft Business Intelligence) met Apache Hadoop-clusters die zijn geïmplementeerd in Azure HDInsight. Een voorbeeld van deze integratie is de mogelijkheid om Excel te verbinden met het Hive-gegevensmagazijn van een Hadoop-cluster in HDInsight met behulp van het ODBC-stuurprogramma (Microsoft Hive Open Database Connectivity).

Het is ook mogelijk om de gegevens die zijn gekoppeld aan een HDInsight-cluster en andere gegevensbronnen, waaronder andere (niet-HDInsight) Hadoop-clusters, vanuit Excel te verbinden met behulp van de Microsoft Power Query-invoegtoepassing voor Excel. Zie [Excel verbinden met HDInsight met Power Query](../hdinsight-connect-excel-power-query.md)voor informatie over het installeren en gebruiken van Power Query.

## <a name="prerequisites"></a>Vereisten

Voordat u met dit artikel begint, moet u de volgende objecten hebben:

* Een HDInsight Hadoop cluster. Zie [Aan de slag met Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)om er een te maken.
* Een werkstation met Office 2010 Professional Plus of hoger of Excel 2010 of hoger.

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC-stuurprogramma installeren

Download en installeer de [Microsoft Hive ODBC Driver-versie](https://www.microsoft.com/download/details.aspx?id=40886) die overeenkomt met de versie van de toepassing waar u het ODBC-stuurprogramma gebruikt.  Voor dit artikel wordt het stuurprogramma gebruikt voor Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC-gegevensbron maken

In de volgende stappen ziet u hoe u een OdBC-gegevensbron van Hive maakt.

1. Navigeer vanuit Windows naar Start > Windows-beheerprogramma's > ODBC-gegevensbronnen (32-bits)/(64-bits).  Hiermee wordt het venster **ODBC-gegevensbronbeheerder** geopend.

    ![OBDC-gegevensbronbeheerder](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Een DSN configureren met ODBC-gegevensbronbeheerder")

1. Selecteer op het tabblad **DSN** voor gebruiker de optie **Toevoegen** om het venster **Nieuwe gegevensbron maken** te openen.

1. Selecteer **Microsoft Hive ODBC-stuurprogramma**en selecteer **Vervolgens Voltooien** om het **DSN-installatievenster van Microsoft Hive ODBC-stuurprogramma's te** openen.

1. Typ of selecteer de volgende waarden:

   | Eigenschap | Beschrijving |
   | --- | --- |
   |  Naam van de gegevensbron |Geef uw gegevensbron een naam |
   |  Host(s) |Voer `HDInsightClusterName.azurehdinsight.net` in. Bijvoorbeeld `myHDICluster.azurehdinsight.net`. Opmerking: `HDInsightClusterName-int.azurehdinsight.net` wordt ondersteund zolang de client-VM wordt gekeken naar hetzelfde virtuele netwerk. |
   |  Poort |Gebruik **443**. (Deze poort is gewijzigd van 563 in 443.) |
   |  Database |Standaardinstelling **gebruiken**. |
   |  Mechanisme |**Windows Azure HDInsight-service selecteren** |
   |  Gebruikersnaam |Voer de gebruikersgebruikersnaam van HDInsight-cluster HTTP in. De standaardgebruikersnaam **admin**. |
   |  Wachtwoord |Voer het wachtwoord van de gebruikers van het HDInsight-cluster in. Schakel het selectievakje **Wachtwoord opslaan (Versleuteld) in.**|

1. Optioneel: **selecteer geavanceerde opties...**  

   | Parameter | Beschrijving |
   | --- | --- |
   |  Native query gebruiken |Wanneer het is geselecteerd, probeert het ODBC-stuurprogramma TSQL NIET om te zetten in HiveQL. Je gebruikt het alleen als je 100% zeker weet dat je pure HiveQL-verklaringen indient. Wanneer u verbinding maakt met SQL Server of Azure SQL Database, moet u deze niet aangevinkt laten. |
   |  Rijen die per blok worden opgehaald |Bij het ophalen van een groot aantal records kan het afstemmen van deze parameter nodig zijn om optimale prestaties te garanderen. |
   |  Standaardtekenreekskolomlengte, Binaire kolomlengte, kolomschaal Decimaal |De lengtes en precisie van het gegevenstype kunnen van invloed zijn op de manier waarop gegevens worden geretourneerd. Ze zorgen ervoor dat onjuiste informatie wordt geretourneerd als gevolg van verlies van precisie en/of truncation. |

    ![Geavanceerde DSN-configuratieopties](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Geavanceerde DSN-configuratieopties")

1. Selecteer **Testen** om de gegevensbron te testen. Wanneer de gegevensbron correct is geconfigureerd, toont het testresultaat **SUCCES!**.  

1. Selecteer **OK** om het testvenster te sluiten.  

1. Selecteer **OK** om het **DSN-installatievenster van Microsoft Hive ODBC Driver** te sluiten.  

1. Selecteer **OK** om het venster **ODBC-gegevensbronbeheerder te** sluiten.  

## <a name="import-data-into-excel-from-hdinsight"></a>Gegevens in Excel importeren vanuit HDInsight

In de volgende stappen wordt beschreven hoe gegevens uit een Hive-tabel in een Excel-werkmap kunnen worden geïmporteerd met behulp van de ODBC-gegevensbron die u in de vorige sectie hebt gemaakt.

1. Open een nieuwe of bestaande werkmap in Excel.

2. Navigeer op het tabblad **Gegevens** naar **Gegevens** > **ophalen uit andere bronnen** > **van ODBC** om het **odbc-venster te** starten.

    ![Wizard Excel-gegevensverbinding openen](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Wizard Excel-gegevensverbinding openen")

3. Selecteer in de vervolgkeuzelijst de naam van de gegevensbron die u in de laatste sectie hebt gemaakt en selecteer **OK**.

4. Voor het eerste gebruik wordt een **ODBC-stuurprogrammadialoogvenster** geopend. Selecteer **Windows** in het linkermenu. Selecteer vervolgens **Verbinding maken** om het **venster Navigator** te openen.

5. Navigeer vanuit **Navigator**naar **hive** > **standaard** > **hivesampletable**en selecteer **Laden**. Het duurt even voordat gegevens worden geïmporteerd naar Excel.

    ![HDInsight Excel Hive ODBC navigator](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "HDInsight Excel Hive ODBC navigator")

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u het Microsoft Hive ODBC-stuurprogramma gebruiken om gegevens van de HDInsight-service in Excel op te halen. Op dezelfde manier u gegevens van de HDInsight-service ophalen in SQL Database. Het is ook mogelijk om gegevens te uploaden naar een HDInsight Service. Voor meer informatie zie:

* [Visualiseer Apache Hive-gegevens met Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualiseer interactieve queryhive-gegevens met Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Gebruik Apache Zeppelin om Apache Hive-query's uit te voeren in Azure HDInsight.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Sluit Excel aan op Apache Hadoop met Power Query](apache-hadoop-connect-excel-power-query.md).
* [Maak verbinding met Azure HDInsight en voer Apache Hive-query's uit met Data Lake Tools voor Visual Studio.](apache-hadoop-visual-studio-tools-get-started.md)
* [Gebruik Azure HDInsight Tool voor Visual Studio Code](../hdinsight-for-vscode.md).
* [Gegevens uploaden naar HDInsight.](./../hdinsight-upload-data.md)
