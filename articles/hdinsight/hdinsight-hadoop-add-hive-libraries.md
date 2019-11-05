---
title: Apache Hive bibliotheken tijdens het maken van het cluster-Azure HDInsight
description: Meer informatie over het toevoegen van Apache Hive bibliotheken (JAR-bestanden) aan een HDInsight-cluster tijdens het maken van het cluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 51a93aaec4abdb2dd9d8fad042c079a48d4ea7a3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494843"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Aangepaste Apache Hive bibliotheken toevoegen bij het maken van uw HDInsight-cluster

Meer informatie over het vooraf laden van [Apache Hive](https://hive.apache.org/) bibliotheken op HDInsight. Dit document bevat informatie over het gebruik van een script actie voor het vooraf laden van bibliotheken tijdens het maken van het cluster. Bibliotheken die zijn toegevoegd aan de hand van de stappen in dit document, zijn wereld wijd beschikbaar in Hive-het is niet nodig JAR toe te [voegen](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) om ze te laden.

## <a name="how-it-works"></a>Het werkt als volgt

Wanneer u een cluster maakt, kunt u een script actie gebruiken om cluster knooppunten te wijzigen wanneer ze worden gemaakt. In het script in dit document wordt één para meter geaccepteerd. Dit is de locatie van de bibliotheken. Deze locatie moet zich in een Azure Storage-account bevinden en de bibliotheken moeten worden opgeslagen als jar-bestanden.

Tijdens het maken van het cluster worden de bestanden door het script geïnventariseerd, worden deze gekopieerd naar de map `/usr/lib/customhivelibs/` op de hoofd-en worker-knoop punten. vervolgens worden deze toegevoegd aan de eigenschap `hive.aux.jars.path` in het `core-site.xml`-bestand. Op Linux-gebaseerde clusters werkt ook het `hive-env.sh` bestand bij met de locatie van de bestanden.

> [!NOTE]  
> Met de script acties in dit artikel maakt u de bibliotheken beschikbaar in de volgende scenario's:
>
> * **HDInsight op basis van Linux** : wanneer u gebruikmaakt van een Hive-client, **WebHCat**en **HiveServer2**.
> * **HDInsight op basis van Windows** : wanneer u de Hive-client en **WebHCat**gebruikt.

## <a name="the-script"></a>Het script

**Locatie van script**

Voor **op Linux gebaseerde clusters**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Voor op **Windows gebaseerde clusters**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**Vereisten**

* De scripts moeten worden toegepast op zowel de **hoofd knooppunten** als de **worker-knoop punten**.

* De potten die u wilt installeren, moeten worden opgeslagen in Azure Blob Storage in **één container**.

* Het opslag account met de bibliotheek van jar-bestanden **moet** worden gekoppeld aan het HDInsight-cluster tijdens het maken. Dit moet het standaard opslag account zijn of een account dat is toegevoegd via een __optionele configuratie__.

* Het WASB-pad naar de container moet worden opgegeven als een para meter voor de script actie. Als de potten bijvoorbeeld worden opgeslagen in een container met de naam **bibliotheken** op een opslag account met de naam **mijn opslag**, wordt de para meter **\@wasb://libs mystorage.blob.core.Windows.net/** .

  > [!NOTE]  
  > In dit document wordt ervan uitgegaan dat u al een opslag account, Blob-container hebt gemaakt en de bestanden erin hebt geladen.
  >
  > Als u nog geen opslag account hebt gemaakt, kunt u dit doen via de [Azure Portal](https://portal.azure.com). U kunt vervolgens een hulp programma zoals [Azure Storage Explorer](https://storageexplorer.com/) gebruiken om een container in het account te maken en bestanden te uploaden.

## <a name="create-a-cluster-using-the-script"></a>Een cluster maken met behulp van het script

> [!NOTE]  
> Met de volgende stappen maakt u een HDInsight-cluster op basis van Linux. Als u een Windows-cluster wilt maken, selecteert u **Windows** als cluster besturingssysteem bij het maken van het cluster en gebruikt u het Windows-script (Power shell) in plaats van het script bash.
>
> U kunt ook Azure PowerShell of de HDInsight .NET SDK gebruiken om een cluster te maken met behulp van dit script. Zie [HDInsight-clusters aanpassen met script acties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over het gebruik van deze methoden.

1. Begin met het inrichten van een cluster met behulp van de stappen bij het [inrichten van HDInsight-clusters in Linux](hdinsight-hadoop-provision-linux-clusters.md), maar het inrichten niet volt ooien.

2. Selecteer in de sectie **optionele configuratie** de optie **script acties**en geef de volgende informatie op:

   * **Naam**: Voer een beschrijvende naam in voor de script actie.

   * **Script-URI**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh.

   * **Kop**: Schakel deze optie in.

   * **Werk nemer**: Schakel deze optie in.

   * **ZOOKEEPER**: laat dit veld leeg.

   * **Para meters**: Voer het WASB-adres in voor de container en het opslag account dat de potten bevat. Bijvoorbeeld **wasb://libs\@mystorage.blob.core.Windows.net/** .

3. Gebruik de knop **selecteren** aan de onderkant van de **script acties**om de configuratie op te slaan.

4. Selecteer in de sectie **optionele configuratie** de optie **gekoppelde opslag accounts** en selecteer de koppeling **een opslag sleutel toevoegen** . Selecteer het opslag account dat de potten bevat. Gebruik vervolgens de **selectie** knoppen om instellingen op te slaan en de **optionele configuratie**te retour neren.

5. Als u de optionele configuratie wilt opslaan, gebruikt u de knop **selecteren** onder aan de sectie **optionele configuratie** .

6. Ga door met het inrichten van het cluster zoals beschreven in [HDInsight-clusters inrichten op Linux](hdinsight-hadoop-provision-linux-clusters.md).

Nadat het maken van het cluster is voltooid, kunt u de potten die via dit script zijn toegevoegd, uit de Hive gebruiken zonder de `ADD JAR`-instructie te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md) voor meer informatie over het werken met hive
