---
title: Apache Hadoop-clusters beheren in HDInsight met Azure-portal
description: Meer informatie over het maken en beheren van Azure HDInsight-clusters met behulp van de Azure-portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/12/2020
ms.openlocfilehash: b9d923b3272f9d8b3da39d7cdb771a766eee4eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272732"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Apache Hadoop-clusters beheren in HDInsight met behulp van de Azure-portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Met behulp van de [Azure-portal](https://portal.azure.com)u [Apache Hadoop-clusters](https://hadoop.apache.org/) beheren in Azure HDInsight. Gebruik de bovenstaande tabkiezer voor informatie over het beheren van Hadoop-clusters in HDInsight met behulp van andere hulpprogramma's.

## <a name="prerequisites"></a>Vereisten

Een bestaand Apache Hadoop cluster in HDInsight.  Zie [Clusters op basis van Linux maken in HDInsight met behulp van de Azure-portal.](hdinsight-hadoop-create-linux-clusters-portal.md)

## <a name="getting-started"></a>Aan de slag

Log hier [https://portal.azure.com](https://portal.azure.com)in

## <a name="list-and-show-clusters"></a><a name="showClusters"></a>Clusters weergeven en weergeven

Op de pagina **HDInsight-clusters** worden uw bestaande clusters weergegeven.  Van het portaal:
1. Selecteer **Alle services** in het linkermenu.
2. Selecteer **HDInsight-clusters** onder **ANALYTICS**.

## <a name="cluster-home-page"></a><a name="homePage"></a>Startpagina van het cluster

Selecteer uw clusternaam op de pagina [**HDInsight-clusters.**](#showClusters)  Hiermee wordt de **overzichtsweergave** geopend, die lijkt op de volgende afbeelding:

![Azure portal HDInsight cluster essentials](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Bovenste menu:**  

| Item| Beschrijving |
|---|---|
|Verplaatsen|Hiermee verplaatst u het cluster naar een andere resourcegroep of naar een ander abonnement.|
|Verwijderen|Hiermee verwijdert u het cluster. |
|Vernieuwen|Hiermee wordt de weergave vernieuwd.|

**Linkermenu:**  

  - **Menu linksboven**

    | Item| Beschrijving |
    |---|---|
    |Overzicht|Biedt algemene informatie voor uw cluster.|
    |Activiteitenlogboek|Logboeken voor en queryactiviteit weergeven en query's weergeven.|
    |Toegangsbeheer (IAM)|Roltoewijzingen gebruiken.  Zie [Roltoewijzingen gebruiken om toegang tot uw Azure-abonnementsbronnen te beheren](../role-based-access-control/role-assignments-portal.md).|
    |Tags|Hiermee u sleutel-/waardeparen instellen om een aangepaste taxonomie van uw cloudservices te definiëren. U bijvoorbeeld een **project**met de naam sleutel maken en vervolgens een gemeenschappelijke waarde gebruiken voor alle services die aan een specifiek project zijn gekoppeld.|
    |Problemen vaststellen en oplossen|Informatie over probleemoplossing weergeven.|
    |Snelstartgids|Geeft informatie weer waarmee u aan de slag met HDInsight.|
    |Hulpprogramma's|Help-informatie voor HDInsight-gerelateerde tools.|

  - **Menu Instellingen**  

    | Item| Beschrijving |
    |---|---|
    |Clustergrootte|Controleer, verhoog en verklein het aantal clusterworkerknooppunten. Zie [Schaalclusters](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Quotalimieten|Geef de gebruikte en beschikbare kernen voor uw abonnement weer.|
    |SSH + Cluster login|Toont de instructies om verbinding te maken met het cluster via Secure Shell (SSH) verbinding. Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie.|
    |Data Lake Storage Gen1|Toegang data lake storage Gen1 configureren.  Zie [Snelstart: Clusters instellen in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).|
    |Opslagaccounts|Bekijk de opslagaccounts en de sleutels. De opslagaccounts worden geconfigureerd tijdens het proces voor het maken van het cluster.|
    |Toepassingen|HDInsight-toepassingen toevoegen/verwijderen.  Zie [Aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md).|
    |Scriptacties|Voer Bash-scripts uit op het cluster. Zie [HdInsight-clusters op basis van Linux aanpassen met Scriptactie](hdinsight-hadoop-customize-cluster-linux.md).|
    |Externe metastores|Bekijk de [Apache Hive](https://hive.apache.org/) en [Apache Oozie](https://oozie.apache.org/) metastores. De metastores kunnen alleen worden geconfigureerd tijdens het proces voor het maken van het cluster.|
    |HDInsight-partner|Voeg de huidige HDInsight-partner toe/verwijder deze.|
    |Eigenschappen|Bekijk de [clustereigenschappen](#properties).|
    |Vergrendelingen|Voeg een vergrendeling toe om te voorkomen dat het cluster wordt gewijzigd of verwijderd.|
    |Sjabloon exporteren|De sjabloon Azure Resource Manager voor het cluster weergeven en exporteren. Momenteel u alleen het afhankelijke Azure-opslagaccount exporteren. Zie [Apache Hadoop-clusters op basis van Linux maken in HDInsight met Azure Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Monitormenu**

    | Item| Beschrijving |
    |---|---|
    |Waarschuwingen|Beheer de waarschuwingen en acties.|
    |Metrische gegevens|Controleer de clusterstatistieken in Azure Monitor-logboeken.|
    |Diagnose-instellingen|Instellingen voor het opslaan van de diagnosestatistieken.|
    |Azure Monitor|Houd uw cluster in azure monitor in de gaten.|

  - **Menu Ondersteuning + probleemoplossing**

    | Item| Beschrijving |
    |---|---|
    |Status van resources|Zie [overzicht van azure-bronstatus](../service-health/resource-health-overview.md).|
    |Nieuw ondersteuningsverzoek|Hiermee u een ondersteuningsticket maken met Microsoft-ondersteuning.|

## <a name="cluster-properties"></a><a name="properties"></a>Clustereigenschappen

Selecteer op de [startpagina van](#homePage)het cluster onder **Instellingen** De **optie Eigenschappen**.

|Item | Beschrijving |
|---|---|
|Hostname|Clusternaam.|
|CLUSTER-URL|De URL voor de Ambari-webinterface.|
|Privé-eindpunt|Het privéeindpunt voor het cluster.|
|Beveiligde shell (SSH)|De gebruikersnaam en hostnaam die u gebruiken bij de toegang tot het cluster via SSH.|
|STATUS|Een van: Afgebroken, Geaccepteerd, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operationeel, Lopend, Fout, Verwijderen, Verwijderen, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, Het formaat in de wachtrij wijzigen of Clusteraanpassing.|
|REGIO|Azure-locatie. Zie de vervolgkeuzelijst **Regio** over [HDInsight-prijzen](https://azure.microsoft.com/pricing/details/hdinsight/)voor een lijst met ondersteunde Azure-locaties.|
|DATUM GEMAAKT|De datum waarop het cluster is geïmplementeerd.|
|Besturingssysteem|**Windows** of **Linux.**|
|TYPE|Hadoop, HBase, Storm, Spark.|
|Versie|Zie [HDInsight-versies](hdinsight-component-versioning.md).|
|Minimale TLS-versie|De TLS-versie.|
|ABONNEMENT|Naam van het abonnement.|
|STANDAARDGEGEVENSBRON|Het standaardclusterbestandssysteem.|
|Grootte van werknemersknooppunten|De geselecteerde VM-grootte van de werknemersknooppunten.|
|Hoofdknooppuntgrootte|De geselecteerde VM-grootte van de hoofdknooppunten.|
|Virtueel netwerk|De naam van het virtuele netwerk, dat het cluster wordt geïmplementeerd, als er een is geselecteerd tijdens de implementatietijd.|

## <a name="move-clusters"></a>Clusters verplaatsen

U een HDInsight-cluster verplaatsen naar een andere Azure-brongroep of een ander abonnement.

Vanaf de startpagina van het [cluster:](#homePage)

1. Selecteer **Verplaatsen** in het bovenste menu.
2. Selecteer **Verplaatsen naar een andere resourcegroep** of Verplaatsen naar een ander **abonnement**.
3. Volg de instructies van de nieuwe pagina.

## <a name="delete-clusters"></a>Clusters verwijderen

Als u een cluster verwijdert, wordt het standaardopslagaccount en geen gekoppelde opslagaccounts verwijderd. U het cluster opnieuw maken met dezelfde opslagaccounts en dezelfde metastores. We raden u aan een nieuwe standaard Blob-container te gebruiken wanneer u het cluster opnieuw maakt.

Vanaf de startpagina van het [cluster:](#homePage)

1. Selecteer **Verwijderen** in het bovenste menu.
2. Volg de instructies van de nieuwe pagina.

Zie ook [Clusters onderbreken/afsluiten](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Extra opslagaccounts toevoegen

U aanvullende Azure Storage-accounts en Azure Data Lake Storage-accounts toevoegen nadat een cluster is gemaakt. Zie [Extra opslagaccounts toevoegen aan HDInsight](./hdinsight-hadoop-add-storage.md) voor meer informatie.

## <a name="scale-clusters"></a>Clusters schalen

Met de functie clusterschaling u het aantal werknemersknooppunten wijzigen dat door een Azure HDInsight-cluster wordt gebruikt, zonder dat u het cluster opnieuw hoeft te maken.

Zie [Scale HDInsight clusters](./hdinsight-scaling-best-practices.md) voor volledige informatie.

## <a name="pauseshut-down-clusters"></a>Clusters onderbreken/afsluiten

De meeste Hadoop-taken zijn batchtaken die slechts af en toe worden uitgevoerd. Voor de meeste Hadoop-clusters zijn er grote perioden dat het cluster niet wordt gebruikt voor verwerking. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is.
Er worden ook kosten in rekening gebracht voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

Er zijn vele manieren waarop u het proces programmeren:

- Azure-gegevensfabriek voor gebruikers. Zie [On-demand Apache Hadoop-clusters op basis van Linux maken in HDInsight met Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) voor het maken van on-demand HDInsight-gekoppelde services.
- Gebruik Azure PowerShell.  Zie [Vluchtvertragingsgegevens analyseren](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Gebruik Azure CLI. Zie [Azure HDInsight-clusters beheren met Azure CLI](hdinsight-administer-use-command-line.md).
- Gebruik HDInsight .NET SDK. Zie [Apache Hadoop-taken indienen](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Zie [HDInsight-prijzen voor](https://azure.microsoft.com/pricing/details/hdinsight/)de prijsinformatie. Zie [Clusters verwijderen](#delete-clusters) als u een cluster uit de portal wilt verwijderen

## <a name="upgrade-clusters"></a>Clusters bijwerken

Zie [HET CLUSTER HDInsight upgraden naar een nieuwere versie.](./hdinsight-upgrade-cluster.md)

## <a name="open-the-apache-ambari-web-ui"></a>Open de Apache Ambari web-gebruikersinterface

Ambari biedt een intuïtieve, gebruiksvriendelijke Hadoop-beheerweb-gebruikersinterface, ondersteund door zijn RESTful API's. Ambari stelt systeembeheerders in staat om Hadoop-clusters te beheren en te bewaken.

Vanaf de startpagina van het [cluster:](#homePage)

1. Selecteer **Clusterdashboards**.

    ![HDInsight Apache Hadoop clustermenu](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Selecteer **Ambari-huis** op de nieuwe pagina.
1. Voer de gebruikersnaam en het wachtwoord van het cluster in.  De standaardclustergebruikersnaam is _beheerder_.

Zie CLUSTERS beheren van HDInsight voor meer informatie [met de Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Wachtwoorden wijzigen

Een HDInsight-cluster kan twee gebruikersaccounts hebben. Het HDInsight-clustergebruikersaccount (HTTP-gebruikersaccount) en het SSH-gebruikersaccount worden tijdens het aanmaakproces gemaakt. U de portal gebruiken om het wachtwoord van het clustergebruikersaccount en scriptacties te wijzigen om het SSH-gebruikersaccount te wijzigen.

### <a name="change-the-cluster-user-password"></a>Het wachtwoord van de clustergebruiker wijzigen

> [!NOTE]  
> Als u het wachtwoord van de clustergebruiker (beheerder) wijzigt, kunnen scriptacties tegen dit cluster mislukken. Als u nog steeds doorgaande scriptacties hebt die de knooppunten van de werkgroep targeten, kunnen deze scripts mislukken wanneer u knooppunten aan het cluster toevoegt door het formaat van bewerkingen te wijzigen. Zie [HDInsight-clusters aanpassen met scriptacties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over scriptacties.

Vanaf de startpagina van het [cluster:](#homePage)
1. Selecteer **SSH + Cluster login** onder **Instellingen**.
2. Selecteer **Referentie opnieuw instellen**.
3. Voer een nieuw wachtwoord in en bevestig het nieuwe wachtwoord in de tekstvakken.
4. Selecteer **OK**.

Het wachtwoord wordt gewijzigd op alle knooppunten in het cluster.

### <a name="change-the-ssh-user-password"></a>Het SSH-gebruikerswachtwoord wijzigen

1. Sla met een teksteditor de volgende tekst op als bestand met de naam **changepassword.sh**.

    > [!IMPORTANT]  
    > U moet een editor gebruiken die LF als einde van de regel gebruikt. Als de editor CRLF gebruikt, werkt het script niet.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Upload het bestand naar een opslaglocatie die vanuit HDInsight toegankelijk is via een HTTP- of HTTPS-adres. Bijvoorbeeld een openbaar bestandsarchief zoals OneDrive- of Azure Blob-opslag. Sla de URI (HTTP- of HTTPS-adres) op in het bestand, omdat deze URI nodig is in de volgende stap.
3. Selecteer op de [startpagina van](#homePage)het cluster **Scriptacties** onder **Instellingen**.
4. Selecteer nieuw **indienen op**de pagina **Scriptacties** .
5. Voer op de **actiepagina Script verzenden** de volgende gegevens in:

   | Veld | Waarde |
   | --- | --- |
   | Scripttype | Selecteer **- Aangepast** in de vervolgkeuzelijst.|
   | Name |"Wijzigen ssh wachtwoord" |
   | Bash script URI |De URI naar het changepassword.sh bestand |
   | Knooppunttype(s): (hoofd, werknemer, Nimbus, supervisor of zookeeper.) |✓ voor alle vermelde knooppunttypen |
   | Parameters |Voer de SSH-gebruikersnaam en vervolgens het nieuwe wachtwoord in. Er moet één spatie zijn tussen de gebruikersnaam en het wachtwoord. |
   | Volhard deze scriptactie ... |Laat dit veld ongecontroleerd. |

6. Selecteer **Maken** om het script toe te passen. Zodra het script is voltooid, u verbinding maken met het cluster met behulp van SSH met het nieuwe wachtwoord.

## <a name="find-the-subscription-id"></a>De abonnements-id zoeken

Elk cluster is gekoppeld aan een Azure-abonnement.  De Azure-abonnements-ID is zichtbaar vanaf de startpagina van het [cluster](#homePage).

## <a name="find-the-resource-group"></a>De resourcegroep zoeken

In de Azure Resource Manager-modus wordt elk HDInsight-cluster gemaakt met een Azure Resource Manager-groep. De groep Resourcemanager is zichtbaar vanaf de startpagina van het [cluster](#homePage).

## <a name="find-the-storage-accounts"></a>De opslagaccounts zoeken

HDInsight-clusters gebruiken een Azure Storage-account of Azure Data Lake Storage om gegevens op te slaan. Elk HDInsight-cluster kan één standaardopslagaccount en een aantal gekoppelde opslagaccounts hebben. Als u de opslagaccounts wilt weergeven, selecteert u op de startpagina van het [cluster](#homePage) onder **Instellingen**de **optie Opslagaccounts**.

## <a name="monitor-jobs"></a>Taken controleren

Zie [HDInsight-clusters beheren met de Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Clustergrootte

De tegel **Clustergrootte** van de startpagina van [het cluster](#homePage) geeft het aantal kernen weer dat aan dit cluster is toegewezen en hoe deze worden toegewezen voor de knooppunten binnen dit cluster.

> [!IMPORTANT]  
> Als u de services van het HDInsight-cluster wilt controleren, moet u Ambari Web of de Ambari REST API gebruiken. Zie [HDInsight-clusters beheren met Apache Ambari voor](hdinsight-hadoop-manage-ambari.md) meer informatie over het gebruik van Ambari

## <a name="connect-to-a-cluster"></a>Verbinding maken met een cluster

- [Apache Hive gebruiken met HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een aantal elementaire administratieve functies geleerd. Zie voor meer informatie de volgende artikelen:

- [HDInsight beheren met Azure PowerShell](hdinsight-administer-use-powershell.md)
- [HDInsight beheren met Azure CLI](hdinsight-administer-use-command-line.md)
- [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md)
- [Details over het gebruik van de Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Apache Hive gebruiken in HDInsight](hadoop/hdinsight-use-hive.md)
- [Apache Sqoop gebruiken in HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Gebruik Python User Defined Functions (UDF) met Apache Hive en Apache Pig in HDInsight](hadoop/python-udf-hdinsight.md)
- [Welke versie van Apache Hadoop is in Azure HDInsight?](hdinsight-component-versioning.md)
