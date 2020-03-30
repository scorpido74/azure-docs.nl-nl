---
title: Clusters van Enterprise Security Package beheren - Azure HDInsight
description: Meer informatie over het beheren van Azure HDInsight-clusters met Enterprise Security Package.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 93880269edd72477f3aa85b2dbdc9d9f3ec8ef25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435887"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>HDInsight-clusters beheren met enterprise security pakket

Leer de gebruikers en de rollen in HDInsight Enterprise Security Package (ESP) en hoe u ESP-clusters beheert.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Gebruik VSCode om een koppeling te maken met een cluster dat aan een domein is toegevoegd

U een normaal cluster koppelen met de door Apache Ambari beheerde gebruikersnaam, ook `user1@contoso.com`een apache hadoopcluster voor beveiliging koppelen met behulp van domeingebruikersnaam (zoals: ).

1. Visual [Studio-code openen](https://code.visualstudio.com/). Zorg ervoor dat de uitbreiding [Spark & Hive Tools](../hdinsight-for-vscode.md) is geïnstalleerd.

1. Volg de stappen van [Een cluster koppelen](../hdinsight-for-vscode.md#link-a-cluster) voor Visual Studio Code.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Gebruik IntelliJ om een koppeling te maken met een cluster dat aan een domein is toegevoegd

U een normaal cluster koppelen met de gebruikersnaam van Ambari beheerd, ook een `user1@contoso.com`beveiligings-hadoopcluster koppelen met behulp van domeingebruikersnaam (zoals: ).

1. Open IntelliJ IDEA. Zorg ervoor dat aan alle [voorwaarden](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) wordt voldaan.

1. Volg de stappen van [Een cluster koppelen](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) voor IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Gebruik Eclipse om een koppeling te maken met een cluster dat aan een domein is toegevoegd

U een normaal cluster koppelen met de gebruikersnaam van Ambari beheerd, ook een `user1@contoso.com`beveiligings-hadoopcluster koppelen met behulp van domeingebruikersnaam (zoals: ).

1. Open Eclipse. Zorg ervoor dat aan alle [voorwaarden](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) wordt voldaan.

1. Volg de stappen van [Een cluster](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) koppelen voor Eclipse.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Toegang tot de clusters met Enterprise Security Package

Enterprise Security Package (voorheen BEKEND als HDInsight Premium) biedt multi-user toegang tot het cluster, waar verificatie wordt gedaan door Active Directory en autorisatie door Apache Ranger en Storage ALS (ADLS ACL's). Autorisatie biedt veilige grenzen tussen meerdere gebruikers en biedt alleen bevoorrechte gebruikers toegang tot de gegevens op basis van het autorisatiebeleid.

Beveiliging en gebruikersisolatie zijn belangrijk voor een HDInsight-cluster met Enterprise Security Package. Om aan deze vereisten te voldoen, wordt ssh-toegang tot het cluster met enterprise security package geblokkeerd. In de volgende tabel worden de aanbevolen toegangsmethoden voor elk clustertype weergegeven:

|Workload|Scenario|Toegangsmethode|
|--------|--------|-------------|
|Apache Hadoop|Hive - Interactieve vacatures / query's  |<ul><li>[Beeline Beeline](#beeline)</li><li>[Hive-weergave](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Hulpmiddelen voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interactieve vacatures/query's, Interactieve PySpark|<ul><li>[Beeline Beeline](#beeline)</li><li>[Zeppelin met Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive-weergave](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Hulpmiddelen voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Batch scenario's – Spark indienen, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interactieve query (LLAP)|Interactief|<ul><li>[Beeline Beeline](#beeline)</li><li>[Hive-weergave](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Hulpmiddelen voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Alle|Aangepaste toepassing installeren|<ul><li>[Scriptacties](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter is niet geïnstalleerd/ondersteund in enterprise security pakket.

Het gebruik van de standaard API's helpt vanuit beveiligingsperspectief. U krijgt ook de volgende voordelen:

- **Beheer** - U uw code beheren en taken automatiseren met behulp van standaard API's - Livy, HS2 etc.
- **Audit** - Met SSH is er geen manier om te controleren welke gebruikers SSH'd naar het cluster. Dit zou niet het geval zijn wanneer taken worden gebouwd via standaard eindpunten zoals ze zouden worden uitgevoerd in de context van de gebruiker.

### <a name="use-beeline"></a><a name="beeline"></a>Beeline gebruiken

Installeer Beeline op uw machine en maak verbinding via het openbare internet, gebruik de volgende parameters:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Als Beeline lokaal is geïnstalleerd en verbinding maakt via een Azure Virtual Network, gebruikt u de volgende parameters:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Als u de volledig gekwalificeerde domeinnaam van een headnode wilt vinden, gebruikt u de informatie in het MANAGE HDInsight met behulp van het Ambari REST API-document.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Gebruikers van HDInsight clusters met ESP

Een niet-ESP HDInsight-cluster heeft twee gebruikersaccounts die tijdens het maken van het cluster worden gemaakt:

- **Ambari admin:** Dit account is ook bekend als *Hadoop gebruiker* of *HTTP-gebruiker*. Dit account kan worden gebruikt om u `https://CLUSTERNAME.azurehdinsight.net`aan te melden bij Ambari op . Het kan ook worden gebruikt om query's uit te voeren op Ambari-weergaven, taken uit te voeren via externe hulpprogramma's (bijvoorbeeld PowerShell, Templeton, Visual Studio) en te verifiëren met de Hive ODBC-stuurprogramma en BI-hulpprogramma's (bijvoorbeeld Excel, Power BI of Tableau).

Een HDInsight-cluster met ESP heeft naast Ambari Admin drie nieuwe gebruikers.

- **Ranger admin:** Dit account is de lokale Apache Ranger admin account. Het is geen gebruiker van active directory-domeinen. Dit account kan worden gebruikt om beleid in te stellen en andere gebruikers beheerders of gedelegeerde beheerders te maken (zodat deze gebruikers beleid kunnen beheren). Standaard is de gebruikersnaam *beheerder* en is het wachtwoord hetzelfde als het ambari-beheerderswachtwoord. Het wachtwoord kan worden bijgewerkt vanaf de pagina Instellingen in Ranger.

- **Clusteradmin domein gebruiker**: Dit account is een active directory domein gebruiker aangewezen als de Hadoop cluster admin met inbegrip van Ambari en Ranger. U moet de referenties van deze gebruiker opgeven tijdens het maken van het cluster. Deze gebruiker heeft de volgende bevoegdheden:
    - Sluit machines aan bij het domein en plaats ze binnen de organisatie-eenheid die u opgeeft tijdens het maken van het cluster.
    - Maak serviceprincipals binnen de organisatie-eenheid die u opgeeft tijdens het maken van het cluster.
    - Omgekeerde DNS-vermeldingen maken.

    Let op: de andere AD-gebruikers hebben ook deze bevoegdheden.

    Er zijn een aantal eindpunten binnen het cluster (bijvoorbeeld Templeton) die niet worden beheerd door Ranger, en dus niet veilig zijn. Deze eindpunten zijn vergrendeld voor alle gebruikers, behalve de domeingebruiker van de clusterbeheerder.

- **Regelmatig:** Tijdens het maken van het cluster u meerdere actieve directorygroepen verstrekken. De gebruikers in deze groepen worden gesynchroniseerd met Ranger en Ambari. Deze gebruikers zijn domeingebruikers en hebben alleen toegang tot door Ranger beheerde eindpunten (bijvoorbeeld Hiveserver2). Alle RBAC-beleidsregels en -audits zijn van toepassing op deze gebruikers.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Rollen van HDInsight-clusters met ESP

HDInsight Enterprise Security Package heeft de volgende rollen:

- Clusterbeheerder
- Clusteroperator
- Servicebeheerder
- Serviceoperator
- Clustergebruiker

**De machtigingen van deze rollen bekijken**

1. Open de Ambari Management UI.  Zie [De gebruikersinterface van Ambari Management openen.](#open-the-ambari-management-ui)
2. Selecteer **Rollen**in het linkermenu .
3. Selecteer het blauwe vraagteken om de machtigingen te bekijken:

    ![Machtigingen voor ESP HDInsight-rollen](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Open de Ambari Management UI

1. Navigeer `https://CLUSTERNAME.azurehdinsight.net/` naar de plaats waar CLUSTERNAME de naam van uw cluster is.
1. Meld u aan bij Ambari met de gebruikersnaam en het wachtwoord van het clusterbeheerder-domein.
1. Selecteer het vervolgkeuzemenu **beheerder** in de rechterbovenhoek en selecteer **Ambari beheren**.

    ![ESP HDInsight beheert Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    De gebruikersinterface ziet eruit als volgt:

    ![ESP HDInsight Apache Ambari management UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>De domeingebruikers weergeven die zijn gesynchroniseerd vanuit uw Active Directory

1. Open de Ambari Management UI.  Zie [De gebruikersinterface van Ambari Management openen.](#open-the-ambari-management-ui)
2. Selecteer **Gebruikers**in het linkermenu . U ziet alle gebruikers die zijn gesynchroniseerd vanuit uw Active Directory naar het HDInsight-cluster.

    ![Gebruikers van esp HDInsight Ambari-beheergebruikers](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>De domeingroepen weergeven die zijn gesynchroniseerd vanuit uw Active Directory

1. Open de Ambari Management UI.  Zie [De gebruikersinterface van Ambari Management openen.](#open-the-ambari-management-ui)
2. Selecteer **Groepen**in het linkermenu. U ziet alle groepen die zijn gesynchroniseerd vanuit uw Active Directory met het HDInsight-cluster.

    ![ESP HDInsight Ambari management UI lijstgroepen](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Machtigingen voor hiveweergaven configureren

1. Open de Ambari Management UI.  Zie [De gebruikersinterface van Ambari Management openen.](#open-the-ambari-management-ui)
2. Selecteer **Weergaven**in het linkermenu.
3. Selecteer **HIVE** om de details weer te geven.

    ![ESP HDInsight Ambari management UI Hive Views](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Selecteer de koppeling **Hive View** om Hive-weergaven te configureren.
5. Schuif omlaag naar de sectie **Machtigingen.**

    ![Gebruikersinterfaceweergaven voor ESP HDInsight Ambari-beheer configureren](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Selecteer **Gebruiker toevoegen** of Groep **toevoegen**en geef vervolgens de gebruikers of groepen op die Hive-weergaven kunnen gebruiken.

## <a name="configure-users-for-the-roles"></a>Gebruikers configureren voor de rollen

 Zie Rollen van HDInsight-clusters met ESP voor een lijst met rollen en hun machtigingen.

1. Open de Ambari Management UI.  Zie [De gebruikersinterface van Ambari Management openen.](#open-the-ambari-management-ui)
2. Selecteer **Rollen**in het linkermenu .
3. Selecteer **Gebruiker toevoegen** of Groep **toevoegen** om gebruikers en groepen toe te wijzen aan verschillende rollen.

## <a name="next-steps"></a>Volgende stappen

- Zie [HDInsight-clusters configureren met ESP](apache-domain-joined-configure.md)voor het configureren van een HDInsight-cluster met Enterprise Security Package.
- Zie [Apache Hive-beleid configureren voor HDInsight-clusters configureren met ESP](apache-domain-joined-run-hive.md)voor het configureren van Hive-beleid en het uitvoeren van Hive-query's.
