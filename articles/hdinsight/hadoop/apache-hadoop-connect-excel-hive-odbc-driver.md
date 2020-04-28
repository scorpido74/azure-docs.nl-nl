---
title: Excel & Apache Hadoop met stuur programma voor Open Database Connectivity (ODBC)-Azure HDInsight
description: Meer informatie over het instellen en gebruiken van het micro soft Hive ODBC-stuur programma voor Excel voor het opvragen van gegevens in HDInsight-clusters vanuit micro soft Excel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 388f59f5090be43469acfde5197a658942e817f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82182443"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Excel verbinden met Apache Hadoop in Azure HDIsight met behulp van het Hive ODBC-stuurprogramma van Microsoft

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

De Big Data-oplossing van micro soft integreert micro soft Business Intelligence-onderdelen (BI) met Apache Hadoop clusters die in HDInsight zijn geïmplementeerd. Een voor beeld is de mogelijkheid om Excel te verbinden met het Hive-Data Warehouse van een Hadoop-cluster. Verbinding maken via het micro soft ODBC-stuur programma (component Open Database Connectivity).

U kunt de gegevens die zijn gekoppeld aan een HDInsight-cluster via Excel verbinden met de micro soft Power Query-invoeg toepassing voor Excel. Zie [Excel verbinden met HDInsight met Power query](../hdinsight-connect-excel-power-query.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voordat u met dit artikel begint, moet u beschikken over de volgende items:

* An HDInsight Hadoop-cluster. Zie aan de [slag met Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)om een account te maken.
* Een werk station met Office 2010 Professional Plus of hoger of Excel 2010 of hoger.

## <a name="install-microsoft-hive-odbc-driver"></a>Micro soft Hive ODBC-stuur programma installeren

Down load en installeer het [micro soft Hive ODBC-stuur programma](https://www.microsoft.com/download/details.aspx?id=40886). Kies de versie die overeenkomt met de versie van de toepassing waarin u het ODBC-stuur programma gaat gebruiken.  Voor dit artikel wordt het stuur programma gebruikt voor Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC-gegevens bron maken

De volgende stappen laten zien hoe u een Hive-ODBC-gegevens bron maakt.

1. Ga vanuit Windows naar **Start > Windows-beheer programma's > ODBC-gegevens bronnen (32-bits)/(64-bits)**.  Met deze actie wordt het venster **ODBC-gegevens bron beheer** geopend.

    ![OBDC-gegevens bron beheerder](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Een DSN configureren met behulp van ODBC-gegevens bron beheer")

1. Klik op het tabblad **gebruikers-DSN** op **toevoegen** om het venster **nieuwe gegevens bron maken** te openen.

1. Selecteer **micro soft Hive ODBC-stuur programma**en selecteer vervolgens **volt ooien** om het venster **micro soft Hive ODBC-stuur programma voor DSN-instellingen** te openen.

1. Typ of selecteer de volgende waarden:

   | Eigenschap | Beschrijving |
   | --- | --- |
   |  Naam van de gegevensbron |Geef uw gegevensbron een naam |
   |  Host (s) |Voer `HDInsightClusterName.azurehdinsight.net` in. Bijvoorbeeld `myHDICluster.azurehdinsight.net`. Opmerking: `HDInsightClusterName-int.azurehdinsight.net` wordt ondersteund zolang de client-VM is gekoppeld aan hetzelfde virtuele netwerk. |
   |  Poort |Gebruik **443**. (Deze poort is gewijzigd van 563 in 443.) |
   |  Database |**Standaard instelling**gebruiken. |
   |  Mechanisme |Selecteer de **Windows Azure HDInsight-service** |
   |  Gebruikersnaam |Voer de gebruikers naam van het HDInsight-cluster in. De standaardgebruikersnaam **admin**. |
   |  Wachtwoord |Voer het gebruikers wachtwoord voor het HDInsight-cluster in. Schakel het selectie vakje **wacht woord opslaan (versleuteld)** in.|

1. Optioneel: Selecteer **Geavanceerde opties...**  

   | Parameter | Beschrijving |
   | --- | --- |
   |  Systeem eigen query gebruiken |Wanneer deze is geselecteerd, probeert het ODBC-stuur programma geen TSQL te converteren naar HiveQL. U dient deze alleen te gebruiken als u 100% zeker weet dat u HiveQL-instructies verzendt. Wanneer u verbinding maakt met SQL Server of Azure SQL Database, moet u het selectie vakje uitgeschakeld laten. |
   |  Opgehaalde rijen per blok |Bij het ophalen van een groot aantal records is het mogelijk dat deze para meter moet worden afgestemd om optimale prestaties te garanderen. |
   |  Standaard lengte van een teken reeks kolom, binaire kolom lengte, decimale kolom schaal |De lengten en nauw keurigheid van het gegevens type kunnen van invloed zijn op hoe gegevens worden geretourneerd. Ze leiden ertoe dat onjuiste gegevens worden geretourneerd vanwege verlies van nauw keurigheid en, of afkap ping. |

    ![Geavanceerde configuratie opties voor DSN](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Geavanceerde configuratie opties voor DSN")

1. Selecteer **testen** om de gegevens bron te testen. Wanneer de gegevens bron op de juiste wijze is geconfigureerd, toont het test resultaat de uitkomst **.**

1. Selecteer **OK** om het test venster te sluiten.  

1. Selecteer **OK** om het venster **micro soft Hive ODBC-stuur programma DSN-instellingen** te sluiten.  

1. Selecteer **OK** om het venster **ODBC-gegevens bron beheer** te sluiten.  

## <a name="import-data-into-excel-from-hdinsight"></a>Gegevens in Excel importeren vanuit HDInsight

In de volgende stappen wordt beschreven hoe u gegevens uit een Hive-tabel importeert in een Excel-werkmap met behulp van de ODBC-gegevens bron die u hebt gemaakt in de vorige sectie.

1. Open een nieuwe of bestaande werkmap in Excel.

2. Ga op het tabblad **gegevens** naar **gegevens** > ophalen**uit andere bronnen** > **van ODBC** om het venster **van ODBC** te starten.

    ![Wizard Excel-gegevens verbinding openen](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Wizard Excel-gegevens verbinding openen")

3. Selecteer in de vervolg keuzelijst de naam van de gegevens bron die u in de laatste sectie hebt gemaakt en selecteer vervolgens **OK**.

4. Voor het eerste gebruik wordt het dialoog venster **ODBC-stuur programma** geopend. Selecteer **Windows** in het menu links. Selecteer vervolgens **verbinding maken** om het **Navigator** venster te openen.

5. Navigeer **vanuit Navigator**naar **Hive** > **default** > **hivesampletable**en selecteer **Load**. Het duurt enkele minuten voordat de gegevens in Excel worden geïmporteerd.

    ![ODBC-Navigator Excel-Hive van HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "ODBC-Navigator Excel-Hive van HDInsight")

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u het micro soft Hive ODBC-stuur programma gebruikt om gegevens op te halen uit de HDInsight-service in Excel. Op dezelfde manier kunt u gegevens ophalen van de HDInsight-service in SQL Database. Het is ook mogelijk om gegevens te uploaden naar een HDInsight-service. Voor meer informatie zie:

* [Visualiseer Apache Hive gegevens met micro soft-power bi in azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisatie gegevens van interactieve Query's visualiseren met Power bi in azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Verbinding maken tussen Excel en Apache Hadoop met behulp van Power query](apache-hadoop-connect-excel-power-query.md).
* [Verbinding maken met Azure HDInsight en Apache Hive query's uitvoeren met behulp van data Lake-Hulpprogram ma's voor Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
