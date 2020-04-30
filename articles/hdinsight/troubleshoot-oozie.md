---
title: Problemen met Apache Oozie in azure HDInsight oplossen
description: Problemen met bepaalde Apache Oozie-fouten in azure HDInsight oplossen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 18831832f82cdbc8cec69e368f006f7acd4836c1
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205259"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Problemen met Apache Oozie in azure HDInsight oplossen

Met de Apache Oozie-gebruikers interface kunt u Oozie-logboeken weer geven. De Oozie-gebruikers interface bevat ook koppelingen naar de JobTracker-logboeken voor de MapReduce taken die door de werk stroom zijn gestart. Het patroon voor het oplossen van problemen moet zijn:

1. Bekijk de taak in de Oozie-webgebruikersinterface.

2. Als er een fout of fout optreedt voor een specifieke actie, selecteert u de actie om te zien of het **fout bericht** veld meer informatie over de fout bevat.

3. Gebruik, indien beschikbaar, de URL van de actie om meer details weer te geven, zoals de JobTracker-logboeken, voor de actie.

Hieronder vindt u specifieke fouten die u kunt tegen komen en hoe u deze kunt oplossen.

## <a name="ja009-cant-initialize-cluster"></a>JA009: kan cluster niet initialiseren

### <a name="issue"></a>Probleem

De taak status wordt gewijzigd in **opgeschort**. Details voor de taak geven de `RunHiveScript` status weer als **START_MANUAL**. Als u de actie selecteert, wordt het volgende fout bericht weer gegeven:

    JA009: Cannot initialize Cluster. Please check your configuration for map

### <a name="cause"></a>Oorzaak

De Azure Blob-opslag adressen die worden gebruikt in het bestand **Job. XML** bevatten niet de opslag container of de naam van het opslag account. De indeling van het Blob-opslag `wasbs://containername@storageaccountname.blob.core.windows.net`adres moet zijn.

### <a name="resolution"></a>Oplossing

Wijzig de Blob Storage-adressen die de taak gebruikt.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie is niet toegestaan om de &lt;gebruiker te imiteren&gt;

### <a name="issue"></a>Probleem

De taak status wordt gewijzigd in **opgeschort**. Details voor de taak geven de `RunHiveScript` status weer als **START_MANUAL**. Als u de actie selecteert, wordt het volgende fout bericht weer gegeven:

    JA002: User: oozie is not allowed to impersonate <USER>

### <a name="cause"></a>Oorzaak

De huidige machtigings instellingen staan niet toe dat Oozie het opgegeven gebruikers account imiteert.

### <a name="resolution"></a>Oplossing

Oozie kunnen gebruikers in de **`users`** groep imiteren. Gebruik de `groups USERNAME` om de groepen weer te geven waarvan het gebruikers account lid is. Als de gebruiker geen lid is van de **`users`** groep, gebruikt u de volgende opdracht om de gebruiker toe te voegen aan de groep:

    sudo adduser USERNAME users

> [!NOTE]  
> Het kan enkele minuten duren voordat HDInsight detecteert dat de gebruiker is toegevoegd aan de groep.

---

## <a name="launcher-error-sqoop"></a>FOUT bij starten (Sqoop)

### <a name="issue"></a>Probleem

De taak status wordt gewijzigd in **beëindigd**. Details voor de taak tonen de `RunSqoopExport` status als **fout**. Als u de actie selecteert, wordt het volgende fout bericht weer gegeven:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

### <a name="cause"></a>Oorzaak

Sqoop kan het database stuur programma dat is vereist voor toegang tot de data base niet laden.

### <a name="resolution"></a>Oplossing

Wanneer u Sqoop van een Oozie-taak gebruikt, moet u het database stuur programma toevoegen aan de andere resources, zoals de werk stroom. XML, die door de taak wordt gebruikt. U kunt ook verwijzen naar het archief dat het database stuur programma `<sqoop>...</sqoop>` bevat vanuit de sectie van de werk stroom. XML.

U kunt bijvoorbeeld voor het taak voorbeeld [Hadoop Oozie-werk stromen](hdinsight-use-oozie-linux-mac.md)gebruiken door de volgende stappen uit te voeren:

1. Kopieer het `mssql-jdbc-7.0.0.jre8.jar` bestand naar de **/tutorials/useoozie** -map:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Wijzig de `workflow.xml` om de volgende XML toe te voegen op een `</sqoop>`nieuwe regel:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
