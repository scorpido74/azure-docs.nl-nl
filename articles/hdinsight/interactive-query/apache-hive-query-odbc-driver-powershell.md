---
title: Query Apache Hive met ODBC Driver & PowerShell - Azure HDInsight
description: Gebruik het Microsoft Hive ODBC-stuurprogramma en PowerShell om Apache Hive-clusters op Azure HDInsight op te vragen.
keywords: hive, hive odbc, powershell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: f6139bb98fa0272e43c8e180d4ec029f7a7538bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73494325"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Zelfstudie: Query Apache Hive met ODBC en PowerShell

Microsoft ODBC-stuurprogramma's bieden een flexibele manier om te communiceren met verschillende soorten gegevensbronnen, waaronder Apache Hive. U code schrijven in scripttalen zoals PowerShell die de ODBC-stuurprogramma's gebruiken om een verbinding met uw Hive-cluster te openen, een query naar keuze door te geven en de resultaten weer te geven.

In deze zelfstudie doe je de volgende taken:

> [!div class="checklist"]
> * Het Microsoft Hive ODBC-stuurprogramma downloaden en installeren
> * Een Apache Hive ODBC-gegevensbron maken die is gekoppeld aan uw cluster
> * Voorbeeldgegevens van uw cluster query's met PowerShell

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u beschikken over de volgende items:

* Een cluster interactieve query's op HDInsight. Zie [Aan de slag met Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)om er een te maken. Selecteer **Interactieve query** als clustertype.

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC-stuurprogramma installeren

Download en installeer de [Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC-gegevensbron maken

In de volgende stappen ziet u hoe u een Apache Hive ODBC-gegevensbron maakt.

1. Navigeer vanuit Windows naar**OdBC-gegevensbronnen (32-bits) /(64-bits)** starten naar De odbc-gegevensbronnen van > **Windows.** >  **Start**  Er wordt een **ODBC-gegevensbronbeheerdervenster** geopend.

    ![OBDC-gegevensbronbeheerder](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "Een DSN configureren met ODBC-gegevensbronbeheerder")

1. Selecteer op het tabblad **DSN** voor gebruiker de optie **Toevoegen** om het venster **Nieuwe gegevensbron maken** te openen.

1. Selecteer **Microsoft Hive ODBC-stuurprogramma**en selecteer **Vervolgens Voltooien** om het **DSN-installatievenster van Microsoft Hive ODBC-stuurprogramma's te** openen.

1. Typ of selecteer de volgende waarden:

   | Eigenschap | Beschrijving |
   | --- | --- |
   |  Naam van de gegevensbron |Geef uw gegevensbron een naam |
   |  Host(s) |Voer `CLUSTERNAME.azurehdinsight.net` in. Bijvoorbeeld: `myHDICluster.azurehdinsight.net` |
   |  Poort |Gebruik **443**.|
   |  Database |Standaardinstelling **gebruiken**. |
   |  Mechanisme |**Windows Azure HDInsight-service selecteren** |
   |  Gebruikersnaam |Voer de gebruikersgebruikersnaam van HDInsight-cluster HTTP in. De standaardgebruikersnaam **admin**. |
   |  Wachtwoord |Voer het wachtwoord van de gebruikers van het HDInsight-cluster in. Schakel het selectievakje **Wachtwoord opslaan (Versleuteld) in.**|

1. Optioneel: selecteer **Geavanceerde opties**.  

   | Parameter | Beschrijving |
   | --- | --- |
   |  Native query gebruiken |Wanneer het is geselecteerd, probeert het ODBC-stuurprogramma TSQL NIET om te zetten in HiveQL. Gebruik deze optie alleen als u er 100% zeker van bent dat u pure HiveQL-instructies indient. Wanneer u verbinding maakt met SQL Server of Azure SQL Database, moet u deze niet aangevinkt laten. |
   |  Rijen die per blok worden opgehaald |Bij het ophalen van een groot aantal records kan het afstemmen van deze parameter nodig zijn om optimale prestaties te garanderen. |
   |  Standaardtekenreekskolomlengte, Binaire kolomlengte, kolomschaal Decimaal |De lengtes en precisie van het gegevenstype kunnen van invloed zijn op de manier waarop gegevens worden geretourneerd. Ze zorgen ervoor dat onjuiste informatie wordt geretourneerd als gevolg van verlies van precisie en truncation. |

    ![Geavanceerde DSN-configuratieopties](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "Geavanceerde DSN-configuratieopties")

1. Selecteer **Testen** om de gegevensbron te testen. Wanneer de gegevensbron correct is geconfigureerd, toont het testresultaat **SUCCES**.  

1. Selecteer **OK** om het testvenster te sluiten.  

1. Selecteer **OK** om het **DSN-installatievenster van Microsoft Hive ODBC Driver** te sluiten.  

1. Selecteer **OK** om het venster **ODBC-gegevensbronbeheerder te** sluiten.  

## <a name="query-data-with-powershell"></a>Querygegevens met PowerShell

Het volgende PowerShell-script is een functie die ODBC om een Hive-cluster op te vragen.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

In het volgende codefragment wordt de bovenstaande functie gebruikt om een query uit te voeren op het cluster Interactieve query dat u aan het begin van de zelfstudie hebt gemaakt. Vervang `DATASOURCENAME` de **gegevensbronnaam** die u hebt opgegeven op het scherm **Microsoft Hive ODBC Driver DSN Setup.** Wanneer u om referenties wordt gevraagd, voert u de gebruikersnaam en het wachtwoord in die u hebt ingevoerd onder de gebruikersnaam van **het inloggen op het cluster** en het **inlogwachtwoord cluster** toen u het cluster hebt gemaakt.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de brongroep, het HDInsight-cluster en het opslagaccount wanneer dit niet meer nodig is. Selecteer hiervoor de brongroep waarin het cluster is gemaakt en klik op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u het Microsoft Hive ODBC-stuurprogramma en PowerShell gebruiken om gegevens uit uw Azure HDInsight Interactive Query-cluster op te halen.

> [!div class="nextstepaction"]
> [Excel verbinden met Apache Hive met ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
