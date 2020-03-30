---
title: Apache Hive-bibliotheken tijdens het maken van clusteren - Azure HDInsight
description: Meer informatie over het toevoegen van Apache Hive-bibliotheken (jar-bestanden) aan een HDInsight-cluster tijdens het maken van een cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: 0b746963cea5a950ba47d8b4dfeb074cb0910436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471020"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Aangepaste Apache Hive-bibliotheken toevoegen bij het maken van uw HDInsight-cluster

Meer informatie over het vooraf laden van [Apache Hive-bibliotheken](https://hive.apache.org/) op HDInsight. Dit document bevat informatie over het gebruik van een scriptactie om bibliotheken vooraf te laden tijdens het maken van het cluster. Bibliotheken die zijn toegevoegd met behulp van de stappen in dit document zijn wereldwijd beschikbaar in Hive - het is niet nodig om [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) te gebruiken om ze te laden.

## <a name="how-it-works"></a>Hoe werkt het?

Wanneer u een cluster maakt, u een scriptactie gebruiken om clusterknooppunten te wijzigen terwijl ze worden gemaakt. Het script in dit document accepteert één parameter, namelijk de locatie van de bibliotheken. Deze locatie moet zich in een Azure Storage-account bevinden en de bibliotheken moeten worden opgeslagen als jar-bestanden.

Tijdens het maken van het cluster somt het `/usr/lib/customhivelibs/` script de bestanden op, kopieert `hive.aux.jars.path` ze `core-site.xml` naar de map op hoofd- en werknemersknooppunten en voegt ze vervolgens toe aan de eigenschap in het bestand. Op Linux-gebaseerde clusters, `hive-env.sh` het werkt ook het bestand met de locatie van de bestanden.

Als u de scriptactie in dit artikel gebruikt, worden de bibliotheken beschikbaar wanneer u een Hive-client voor **WebHCat**en **HiveServer2**gebruikt.

## <a name="the-script"></a>Het script

**Scriptlocatie**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Vereisten

* De scripts moeten worden toegepast op zowel de **hoofdknooppunten** als **de worker-knooppunten.**

* De potten die u wilt installeren, moeten in Azure Blob Storage in **één container**worden opgeslagen.

* Het opslagaccount met de bibliotheek met jarbestanden **moet** tijdens het maken worden gekoppeld aan het HDInsight-cluster. Het moet het standaardopslagaccount zijn of een account dat is toegevoegd via __instellingen voor opslagaccount.__

* Het WASB-pad naar de container moet worden opgegeven als parameter voor de Scriptactie. Als de potten bijvoorbeeld worden opgeslagen in een container met de naam **libs** `wasbs://libs@mystorage.blob.core.windows.net/`op een opslagaccount met de naam **mystorage,** is de parameter .

  > [!NOTE]  
  > In dit document wordt ervan uitgegaan dat u al een opslagaccount, blobcontainer en de bestanden naar het document hebt gemaakt.
  >
  > Als u geen opslagaccount hebt gemaakt, u dit doen via de [Azure-portal.](https://portal.azure.com) U vervolgens een hulpprogramma zoals [Azure Storage Explorer](https://storageexplorer.com/) gebruiken om een container in het account te maken en bestanden naar het account te uploaden.

## <a name="create-a-cluster-using-the-script"></a>Een cluster maken met behulp van het script

1. Begin met het inrichten van een cluster met behulp van de stappen in [Provision HDInsight-clusters op Linux,](hdinsight-hadoop-provision-linux-clusters.md)maar voltooi de inrichting niet. U Azure PowerShell of de HDInsight .NET SDK ook gebruiken om een cluster te maken met dit script. Zie [HDInsight-clusters aanpassen met Scriptacties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over het gebruik van deze methoden. Selecteer voor de Azure-portal op het tabblad **Configuratie + prijzen** de actie Script **toevoegen**.

1. Als **het**opslagaccount met de bibliotheek met jar-bestanden anders is dan het account dat voor het cluster wordt gebruikt, vult u **Extra opslagaccounts**in .

1. Geef voor **Scriptacties**de volgende informatie op:

    |Eigenschap |Waarde |
    |---|---|
    |Scripttype|- Aangepast|
    |Name|Bibliotheken |
    |Bash script URI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Knooppunttype(s)|Hoofd, Arbeider|
    |Parameters|Voer het WASB-adres in op het container- en opslagaccount dat de potten bevat. Bijvoorbeeld `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Voor Apache Spark 2.1, gebruik `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`dit bash script URI: .

1. Doorgaan met het inrichten van het cluster zoals beschreven in [provision HDInsight-clusters op Linux](hdinsight-hadoop-provision-linux-clusters.md).

Zodra het maken van een cluster is voltooid, u de potten gebruiken `ADD JAR` die via dit script zijn toegevoegd vanuit Hive zonder de instructie te hoeven gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [Apache Hive gebruiken met HDInsight voor](hadoop/hdinsight-use-hive.md) meer informatie over het werken met Hive
