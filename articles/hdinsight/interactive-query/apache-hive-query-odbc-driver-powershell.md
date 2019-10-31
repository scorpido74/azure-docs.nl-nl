---
title: Query Apache Hive met ODBC-stuur programma en Power shell-Azure HDInsight
description: Gebruik het micro soft Hive ODBC-stuur programma en Power shell om Apache Hive clusters in azure HDInsight op te vragen.
keywords: Hive, Hive ODBC, Power shell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: 4ec580c3d4c30fa4d86c24defb7eaded464dea3b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177315"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Zelf studie: query Apache Hive met ODBC en Power shell

Micro soft ODBC-stuur Programma's bieden een flexibele manier om te communiceren met verschillende soorten gegevens bronnen, met inbegrip van Apache Hive. U kunt code schrijven in script talen zoals Power shell die gebruikmaken van de ODBC-stuur Programma's om een verbinding met uw Hive-cluster te openen, een query van uw keuze door te geven en de resultaten weer te geven.

In deze zelf studie voert u de volgende taken uit:

> [!div class="checklist"]
> * Down load en installeer het micro soft Hive ODBC-stuur programma
> * Een Apache Hive ODBC-gegevens bron maken die aan uw cluster is gekoppeld
> * Voorbeeld gegevens uit uw cluster opvragen met Power shell

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u beschikken over de volgende items:

* Een interactief query cluster op HDInsight. Zie aan de [slag met Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)om een account te maken. Selecteer **interactieve query** als cluster type.

## <a name="install-microsoft-hive-odbc-driver"></a>Micro soft Hive ODBC-stuur programma installeren

Down load en installeer het [micro soft Hive ODBC-stuur programma](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC-gegevens bron maken

De volgende stappen laten zien hoe u een Apache Hive ODBC-gegevens bron maakt.

1. Ga vanuit Windows naar **Start** > **Windows-beheer Programma's** > **ODBC-gegevens bronnen (32-bits)/(64-bits)** .  Er wordt een venster **ODBC-gegevens bron beheer** geopend.

    ![OBDC-gegevens bron beheerder](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "Een DSN configureren met behulp van ODBC-gegevens bron beheer")

1. Klik op het tabblad **gebruikers-DSN** op **toevoegen** om het venster **nieuwe gegevens bron maken** te openen.

1. Selecteer **micro soft Hive ODBC-stuur programma**en selecteer vervolgens **volt ooien** om het venster **micro soft Hive ODBC-stuur programma voor DSN-instellingen** te openen.

1. Typ of selecteer de volgende waarden:

   | Eigenschap | Beschrijving |
   | --- | --- |
   |  Naam van de gegevensbron |Geef uw gegevensbron een naam |
   |  Host (s) |Voer `CLUSTERNAME.azurehdinsight.net` in. Bijvoorbeeld: `myHDICluster.azurehdinsight.net` |
   |  Port |Gebruik **443**.|
   |  Database |**Standaard instelling**gebruiken. |
   |  Mechanisme |Selecteer de **Windows Azure HDInsight-service** |
   |  Gebruikersnaam |Voer de gebruikers naam van het HDInsight-cluster in. De standaardgebruikersnaam **admin**. |
   |  Wachtwoord |Voer het gebruikers wachtwoord voor het HDInsight-cluster in. Schakel het selectie vakje **wacht woord opslaan (versleuteld)** in.|

1. Optioneel: Selecteer **Geavanceerde opties**.  

   | Parameter | Beschrijving |
   | --- | --- |
   |  Systeem eigen query gebruiken |Wanneer deze is geselecteerd, probeert het ODBC-stuur programma geen TSQL te converteren naar HiveQL. Gebruik deze optie alleen als u 100% zeker weet dat u zuivere HiveQL-instructies verzendt. Wanneer u verbinding maakt met SQL Server of Azure SQL Database, moet u het selectie vakje uitgeschakeld laten. |
   |  Opgehaalde rijen per blok |Bij het ophalen van een groot aantal records is het mogelijk dat deze para meter moet worden afgestemd om optimale prestaties te garanderen. |
   |  Standaard lengte van een teken reeks kolom, binaire kolom lengte, decimale kolom schaal |De lengten en nauw keurigheid van het gegevens type kunnen van invloed zijn op hoe gegevens worden geretourneerd. Ze leiden ertoe dat onjuiste gegevens worden geretourneerd vanwege verlies van nauw keurigheid en afkap ping. |

    ![Geavanceerde configuratie opties voor DSN](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "Geavanceerde configuratie opties voor DSN")

1. Selecteer **testen** om de gegevens bron te testen. Wanneer de gegevens bron op de juiste wijze is geconfigureerd, wordt in het test resultaat **geslaagd**weer gegeven.  

1. Selecteer **OK** om het test venster te sluiten.  

1. Selecteer **OK** om het venster **micro soft Hive ODBC-stuur programma DSN-instellingen** te sluiten.  

1. Selecteer **OK** om het venster **ODBC-gegevens bron beheer** te sluiten.  

## <a name="query-data-with-powershell"></a>Gegevens opvragen met Power shell

Het volgende Power shell-script is een functie die door ODBC kan worden gebruikt om een Hive-cluster op te vragen.

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

In het volgende code fragment wordt de bovenstaande functie gebruikt om een query uit te voeren op het interactieve query cluster dat u aan het begin van de zelf studie hebt gemaakt. Vervang `DATASOURCENAME` door de **naam van de gegevens bron** die u hebt opgegeven in het **micro soft Hive ODBC-stuur programma DSN-installatie** scherm. Wanneer u om referenties wordt gevraagd, voert u de gebruikers naam en het wacht woord in die u hebt opgegeven bij de **gebruikers naam** en het wacht woord voor **aanmelding** bij het cluster wanneer u het cluster hebt gemaakt.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, het HDInsight-cluster en het opslag account. Als u dit wilt doen, selecteert u de resource groep waarin het cluster is gemaakt en klikt u op **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u het micro soft Hive ODBC-stuur programma en Power shell kunt gebruiken om gegevens op te halen uit uw Azure HDInsight Interactive query-cluster.

> [!div class="nextstepaction"]
> [Excel verbinden met Apache Hive met behulp van ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
