---
title: Apache Hive-beleid in Apache Ranger - Azure HDInsight
description: Meer informatie over het configureren van Apache Ranger-beleidsregels voor Hive in een Azure HDInsight-service met Enterprise Security Package.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 90d7da9c8ddd8c9c595f2209dcc34e2f595acfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196923"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Apache Hive-beleidsregels configureren in HDInsight met Enterprise Security Package

Meer informatie over het configureren van Apache Ranger-beleidsregels voor Apache Hive. In dit artikel maakt u twee Ranger-beleidsregels om toegang tot de hivesampletable te beperken. De hivesampletable wordt geleverd met HDInsight-clusters. Nadat u het beleid hebt geconfigureerd, gebruikt u het Excel- en ODBC-stuurprogramma om verbinding te maken met Hive-tabellen in HDInsight.

## <a name="prerequisites"></a>Vereisten

* Een HDInsight-cluster met Enterprise Security Package. Zie [HDInsight-clusters configureren met ESP](apache-domain-joined-configure.md).
* Een werkstation met Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, een zelfstandige versie van Excel 2013 of Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Verbinding maken met de beheerinterface van Apache Ranger
**Verbinding maken met de beheerinterface van Ranger**

1. Navigeer vanuit een browser naar de `https://CLUSTERNAME.azurehdinsight.net/Ranger/` gebruikersinterface van de Ranger-beheerder op de plaats waar CLUSTERNAME de naam van uw cluster is.

   > [!NOTE]  
   > Ranger gebruikt andere referenties dan Apache Hadoop cluster. Als u wilt voorkomen dat browsers hadoop-referenties in de cache gebruiken, gebruikt u het nieuwe InPrivate-browservenster om verbinding te maken met de gebruikersinterface van de Ranger-beheerder.

2. Meld u aan met de gebruikersnaam en het wachtwoord van het clusterbeheerdomein:

    ![HDInsight ESP Ranger startpagina](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Op dit moment werkt Ranger alleen met Yarn en Hive.

## <a name="create-domain-users"></a>Domeingebruikers maken

Zie [Een HDInsight-cluster maken met ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp), voor informatie over het maken van hiveruser1 en hiveuser2. U gebruikt de twee gebruikersaccounts in dit artikel.

## <a name="create-ranger-policies"></a>Ranger-beleidsregels maken

In deze sectie maakt u twee Ranger-beleidsregels voor toegang tot de voorbeeldtabel voor hivesamplen. U geeft de machtiging SELECT op voor verschillende sets kolommen. Beide gebruikers zijn gemaakt met [behulp van Create a HDInsight-cluster met ESP.](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp) In het volgende gedeelte test u de twee beleidsregels in Excel.

**Ranger-beleidsregels maken**

1. Open de beheerinterface van Ranger. Zie Verbinding maken met de beheerinterface van Apache Ranger.
2. Selecteer **CLUSTERNAME_Hive**onder **Hive**. Er worden twee vooraf geconfigureerde beleidsregels weergegeven.
3. Selecteer **Nieuw beleid toevoegen**en voer de volgende waarden in:

    |Eigenschap |Waarde |
    |---|---|
    |Policy Name|read-hivesampletable-all|
    |Hive-database|standaardinstelling|
    |tabel|hivesampletabel|
    |Hive-kolom|*|
    |Select User|hiveuser1|
    |Machtigingen|Selecteer|

    ![HDInsight ESP Ranger Hive-beleid configureren](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Als een domeingebruiker niet is ingevuld in Gebruiker selecteren, wacht u even, zodat Ranger met AAD kan synchroniseren.

4. Selecteer **Toevoegen** om het beleid op te slaan.

5. Herhaal de laatste twee stappen, zodat u een ander beleid kunt maken met de volgende eigenschappen:

    |Eigenschap |Waarde |
    |---|---|
    |Policy Name|read-hivesampletable-devicemake|
    |Hive-database|standaardinstelling|
    |tabel|hivesampletabel|
    |Hive kolom|clientid, devicemake|
    |Select User|hiveuser2|
    |Machtigingen|Selecteer|

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-gegevensbron maken

De instructies vindt u in [Hive ODBC-gegevensbron maken](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Eigenschap  |Beschrijving |
 | --- | --- |
 | Naam van de gegevensbron | Geef uw gegevensbron een naam |
 | Host | Voer CLUSTERNAME.azurehdinsight.net in. Bijvoorbeeld: myHDICluster.azurehdinsight.net |
 | Poort | Gebruik **443**. (Deze poort is gewijzigd van 563 in 443.) |
 | Database | **Standaard gebruik maken**. |
 | Type Hive-server | Selecteer **Hive Server 2** |
 | Mechanisme | Selecteer **Azure HDInsight Service** |
 | HTTP-pad | Laat dit leeg. |
 | Gebruikersnaam | Voer hiveuser1@contoso158.onmicrosoft.com in. Werk de domeinnaam bij als deze anders is. |
 | Wachtwoord | Voer het wachtwoord van hiveuser1 in. |

Zorg ervoor dat u op **Test** klikt voordat u de gegevensbron opslaat.

## <a name="import-data-into-excel-from-hdinsight"></a>Gegevens in Excel importeren vanuit HDInsight

In het laatste gedeelte hebt u twee beleidsregels geconfigureerd.  hiveuser1 heeft de machtiging SELECT voor alle kolommen en hiveuser2 heeft de machtiging SELECT voor twee kolommen. In deze sectie imiteert u de twee gebruikers, zodat u gegevens kunt importeren in Excel.

1. Open een nieuwe of bestaande werkmap in Excel.

1. Navigeer op het tabblad **Gegevens** naar **Gegevens** > **ophalen uit andere bronnen** > **van ODBC** om het **odbc-venster te** starten.

    ![Wizard Gegevensverbinding openen](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. Selecteer in de vervolgkeuzelijst de naam van de gegevensbron die u in de laatste sectie hebt gemaakt en selecteer **OK**.

1. Voor het eerste gebruik wordt een **ODBC-stuurprogrammadialoogvenster** geopend. Selecteer **Windows** in het linkermenu. Selecteer vervolgens **Verbinding maken** om het **venster Navigator** te openen.

1. Wacht totdat het dialoogvenster **Database en tabel selecteren** wordt geopend. Dit kan een paar seconden duren.

1. Selecteer **de tabel Hivesample**en selecteer **Volgende**.

1. Selecteer **Finish**.

1. In het dialoogvenster **Gegevens importeren** kunt u de query wijzigen of opgeven. Selecteer **Hiervoor Eigenschappen**. Dit kan een paar seconden duren.

1. Selecteer het tabblad **Definitie.** De opdrachttekst is:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Bij de Ranger-beleidsregels hebt u gedefinieerd dat hiveuser1 de machtiging SELECT heeft voor alle kolommen.  Deze query werkt dus met de referenties van hiveuser1, maar deze query werkt niet met de referenties van Hiveuser2.

1. Selecteer **OK** om het dialoogvenster Verbindingseigenschappen te sluiten.

1. Selecteer **OK** om het dialoogvenster **Gegevens importeren** te sluiten.  

1. Voer het wachtwoord van hiveuser1 opnieuw in en klik op **OK**. Het duurt een paar seconden voordat de gegevens naar Excel worden geïmporteerd. Als het klaar is, ziet u 11 kolommen met gegevens.

Als u het tweede beleid wilt testen (read-hivesampletable-devicemake), hebt u in de laatste sectie

1. Voeg een nieuw blad in Excel toe.
2. Voer de vorige procedure uit om de gegevens te importeren.  De enige wijziging die u aanbrengt is het gebruik van hiveuser2 referenties in plaats van hiveuser1's. Dit mislukt omdat hiveuser2 alleen toestemming heeft om twee kolommen te zien. De volgende fout wordt weergegeven:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
        
3. Voer dezelfde procedure uit om gegevens te importeren. Gebruik deze keer de referenties van hiveuser2 en wijzig ook de SELECT-instructie van:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    in:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Wanneer dit is gedaan, ziet u twee kolommen met gegevens geïmporteerd.

## <a name="next-steps"></a>Volgende stappen

* Zie [HDInsight-clusters configureren met ESP](apache-domain-joined-configure.md)voor het configureren van een HDInsight-cluster met Enterprise Security Package.
* Zie [HDInsight-clusters](apache-domain-joined-manage.md)beheren met ESP voor het beheren van een HDInsight-cluster met ESP.
* Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)voor het uitvoeren van Hive-query's met SSH op HDInsight-clusters met ESP.
* Zie [Verbinding maken met Apache Hive op Azure HDInsight voor](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md) het verbinden van Hive met Hive JDBC- stuurprogramma
* Zie Excel verbinden met [Apache Hadoop met het Microsoft Hive ODBC-station](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) voor het verbinden van Excel met Hadoop met Hadoop voor het verbinden van Excel met Hadoop met Hive ODBC
* Zie Excel verbinden met [Apache Hadoop met Power Query met Power Query voor](../hadoop/apache-hadoop-connect-excel-power-query.md) het verbinden van Excel met Hadoop met Power Query
