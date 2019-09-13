---
title: Hive-beleid in HDInsight configureren met Enterprise Security Package-Azure
description: Meer informatie over het configureren van Apache zwerver-beleids regels voor Hive in een Azure HDInsight-service met Enterprise Security Package.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b0213fc1a96b38b615cbd8b7b6374a6716b9f840
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918187"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Apache Hive-beleidsregels configureren in HDInsight met Enterprise Security Package
Meer informatie over het configureren van Apache zwerver-beleids regels voor Apache Hive. In dit artikel maakt u twee Ranger-beleidsregels om toegang tot de hivesampletable te beperken. De hivesampletable wordt geleverd met HDInsight-clusters. Nadat u de beleidsregels hebt geconfigureerd, gebruikt u Excel en het ODBC-stuurprogramma om verbinding te maken met Hive-tabellen in HDInsight.

## <a name="prerequisites"></a>Vereisten
* Een HDInsight-cluster met Enterprise Security Package. Zie [HDInsight-clusters configureren met ESP](apache-domain-joined-configure.md).
* Een werkstation met Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, een zelfstandige versie van Excel 2013 of Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Verbinding maken met de beheerinterface van Apache Ranger
**Verbinding maken met de beheerinterface van Ranger**

1. Maak vanuit een browser verbinding met de beheerinterface van Ranger. De URL is https://&lt;ClusterName>.azurehdinsight.net/Ranger/.

   > [!NOTE]  
   > Zwerver gebruikt andere referenties dan Apache Hadoop cluster. Als u browsers wilt voor komen dat Hadoop-referenties in de cache worden gebruikt, gebruikt u het nieuwe InPrivate-browser venster om verbinding te maken met de gebruikers interface van zwerver.

2. Meld u aan met de gebruikersnaam en het wachtwoord van het clusterbeheerdomein:

    ![Start pagina van HDInsight ESP zwerver](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Op dit moment werkt Ranger alleen met Yarn en Hive.

## <a name="create-domain-users"></a>Domeingebruikers maken
Zie [een HDInsight-cluster met ESP maken](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)voor informatie over het maken van hiveruser1 en hiveuser2. U gebruikt de twee gebruikers accounts in dit artikel.

## <a name="create-ranger-policies"></a>Ranger-beleidsregels maken
In deze sectie maakt u twee zwerver-beleids regels voor toegang tot hivesampletable. U geeft de machtiging SELECT op voor verschillende sets kolommen. Beide gebruikers zijn gemaakt met behulp [van een HDInsight-cluster met ESP maken](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp). In de volgende sectie test u de twee beleidsregels in Excel.

**Ranger-beleidsregels maken**

1. Open de beheerinterface van Ranger. Zie verbinding maken met de gebruikers interface van Apache zwerver.
2. Klik op **&lt;ClusterName>_hive** onder **Hive**. Er worden twee vooraf geconfigureerde beleidsregels weergegeven.
3. Klik op **Nieuw beleid toevoegen** en voer de volgende waarden in:

   * Beleidsnaam: read-hivesampletable-all
   * Hive-database: standaard
   * Tabel: hivesampletable
   * Hive-kolom:*
   * Gebruiker selecteren: hiveuser1
   * Machtigingen: SELECT

     ![HDInsight ESP zwerver-Hive-beleid configureren](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]  
     > Als een domeingebruiker niet is ingevuld in Gebruiker selecteren, wacht u even, zodat Ranger met AAD kan synchroniseren.
     >
     >
4. Klik op **Toevoegen** om het beleid op te slaan.
5. Herhaal de laatste twee stappen, zodat u een ander beleid kunt maken met de volgende eigenschappen:

   * Beleidsnaam: read-hivesampletable-devicemake
   * Hive-database: standaard
   * Tabel: hivesampletable
   * Hive-kolom: clientid, devicemake
   * Gebruiker selecteren: hiveuser2
   * Machtigingen: SELECT

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-gegevensbron maken
De instructies vindt u in [Hive ODBC-gegevensbron maken](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Eigenschap  |Description |
 | --- | --- |
 | Naam van de gegevensbron | Geef uw gegevensbron een naam |
 | Host | Voer &lt;HDInsightClusterName>.azurehdinsight.net in. Bijvoorbeeld: myHDICluster.azurehdinsight.net |
 | Port | Gebruik **443**. (Deze poort is gewijzigd van 563 in 443.) |
 | Database | Gebruik **Standaard**. |
 | Type Hive-server | Selecteer **Hive Server 2** |
 | Mechanisme | Selecteer **Azure HDInsight Service** |
 | HTTP-pad | Laat dit leeg. |
 | Naam van gebruiker | Voer hiveuser1@contoso158.onmicrosoft.com in. Werk de domein naam bij als deze niet overeenkomt. |
 | Wachtwoord | Voer het wachtwoord van hiveuser1 in. |

Zorg ervoor dat u op **Test** klikt voordat u de gegevensbron opslaat.

## <a name="import-data-into-excel-from-hdinsight"></a>Gegevens in Excel importeren vanuit HDInsight
In de laatste sectie hebt u twee beleidsregels geconfigureerd.  hiveuser1 heeft de machtiging SELECT voor alle kolommen en hiveuser2 heeft de machtiging SELECT voor twee kolommen. In deze sectie imiteert u de twee gebruikers, zodat u gegevens kunt importeren in Excel.

1. Open een nieuwe of bestaande werkmap in Excel.
2. Klik op het tabblad **Gegevens** op de optie **Van andere gegevensbronnen** en klik vervolgens op **Van wizard Gegevensverbinding** om de **Wizard Gegevensverbinding** te starten.

    ![Open de Wizard Gegevensverbinding][img hdi simbahiveodbc.excel.dataconnection]
3. Selecteer **ODBC DSN** als de gegevensbron en klik op **Volgende**.
4. Selecteer uit ODBC-gegevensbronnen de naam van de gegevensbron die u in de vorige stap hebt gemaakt en klik op **Volgende**.
5. Geef het wacht woord voor het cluster opnieuw op in de wizard en klik vervolgens op **OK**. Wacht totdat het dialoogvenster **Database en tabel selecteren** wordt geopend. Dit kan een paar seconden duren.
6. Selecteer **hivesampletable** en klik op **Volgende**.
7. Klik op **Voltooien**.
8. In het dialoogvenster **Gegevens importeren** kunt u de query wijzigen of opgeven. Als u dit wilt doen, klikt u op **Eigenschappen**. Dit kan een paar seconden duren.
9. Klik op het tabblad **Definitie**. De opdrachttekst is:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Bij de Ranger-beleidsregels hebt u gedefinieerd dat hiveuser1 de machtiging SELECT heeft voor alle kolommen.  Deze query werkt daarom met hiveuser1-referenties, maar deze query werkt niet met hiveuser2-referenties.

   ![Verbindingseigenschappen][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Klik op **OK** om het dialoogvenster Verbindingseigenschappen te sluiten.
11. Klik op **OK** om het dialoogvenster **Gegevens importeren** te sluiten.  
12. Voer het wachtwoord van hiveuser1 opnieuw in en klik op **OK**. Het duurt een paar seconden voordat de gegevens naar Excel worden geïmporteerd. Wanneer dit is voltooid, worden er 11 kolommen met gegevens weergegeven.

Als u het tweede beleid wilt testen (Read-hivesampletable-devicemake), hebt u in de laatste sectie gemaakt

1. Voeg een nieuw blad in Excel toe.
2. Voer de vorige procedure uit om de gegevens te importeren.  De enige wijziging die u aanbrengt, is het gebruik van hiveuser2-referenties in plaats van hiveuser1. Dit mislukt omdat hiveuser2 alleen machtigingen heeft om twee kolommen weer te geven. De volgende fout wordt weergegeven:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Voer dezelfde procedure uit om gegevens te importeren. Gebruik deze keer de referenties van hiveuser2 en wijzig ook de SELECT-instructie van:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    in:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Wanneer dit is voltooid, worden er twee kolommen met geïmporteerde gegevens weergegeven.

## <a name="next-steps"></a>Volgende stappen
* Zie [hdinsight-clusters met ESP configureren](apache-domain-joined-configure.md)voor meer informatie over het configureren van een hdinsight-cluster met Enterprise Security Package.
* Zie [hdinsight-clusters beheren met ESP](apache-domain-joined-manage.md)voor meer informatie over het beheren van een hdinsight-cluster met ESP.
* Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)voor het uitvoeren van Hive-QUERY'S met SSH op HDInsight-clusters met ESP.
* Zie [verbinding maken met Apache Hive in azure HDInsight met behulp van het Hive JDBC-stuur programma voor het](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md) verbinden van de component met hive JDBC.
* Zie [Excel verbinden met Apache Hadoop met het micro soft Hive ODBC-station voor het](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) verbinden van Excel met Hadoop met behulp van Hive ODBC.
* Zie [Excel verbinden met Apache Hadoop met behulp van Power query](../hadoop/apache-hadoop-connect-excel-power-query.md) om Excel te verbinden met Hadoop met behulp van Power query
