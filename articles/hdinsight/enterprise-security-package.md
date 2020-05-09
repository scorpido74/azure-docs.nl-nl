---
title: Enterprise Security Package voor Azure HDInsight
description: Meer informatie over de Enterprise Security Package onderdelen en versies in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 07210350826001bd3e0be6e04be211c9de43695a
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997254"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Enterprise Security Package voor Azure HDInsight

Enter prise Security is een optioneel pakket dat u kunt toevoegen aan uw HDInsight-cluster als onderdeel van de werk stroom voor het maken van een cluster. Het Enterprise Security Package ondersteunt:

* Integratie met Active Directory voor authenticatie.

    In het verleden hebt u HDInsight-clusters gemaakt met lokale beheerders gebruiker en lokale SSH-gebruiker. De lokale beheerder gebruiker heeft toegang tot alle bestanden, mappen, tabellen en kolommen.  Met Enterprise Security Package kunt u toegangs beheer op basis van rollen inschakelen door HDInsight te integreren met uw Active Directory. Met inbegrip van on-premises Active Directory, Azure Active Directory Domain Services. Of Active Directory op virtuele IaaS-machine. Domein beheerder op het cluster kan gebruikers toestaan hun eigen bedrijfs naam en wacht woord (domein) te gebruiken.

    Zie voor meer informatie:

    * [Een inleiding tot Apache Hadoop beveiliging met HDInsight-clusters die zijn toegevoegd aan een domein](./domain-joined/hdinsight-security-overview.md)

    * [Apache Hadoop clusters die zijn toegevoegd aan het Azure-domein plannen in HDInsight](./domain-joined/apache-domain-joined-architecture.md)

    * [Een aan een domein gekoppelde sandbox-omgeving configureren](./domain-joined/apache-domain-joined-configure.md)

    * [HDInsight-clusters die zijn gekoppeld aan een domein configureren met behulp van Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* Autorisatie voor gegevens

  * Integratie met Apache zwerver voor autorisatie voor Hive-, Spark SQL-en garen-wacht rijen.
  * U kunt toegangs beheer instellen voor bestanden en mappen.

    Zie [Apache Hive-beleid configureren in HDInsight die lid](./domain-joined/apache-domain-joined-run-hive.md) is van een domein voor meer informatie

* Bekijk de audit Logboeken om de toegang tot en het geconfigureerde beleid te bewaken.

## <a name="supported-cluster-types"></a>Ondersteunde cluster typen

Momenteel ondersteunen alleen de volgende cluster typen de Enterprise Security Package:

* Hadoop (alleen HDInsight 3,6)
* Spark
* Kafka
* HBase
* Interactive Query

## <a name="support-for-azure-data-lake-storage"></a>Ondersteuning voor Azure Data Lake Storage

De Enterprise Security Package ondersteunt het gebruik van Azure Data Lake Storage als de primaire opslag en de opslag van de invoeg toepassing.

## <a name="pricing-and-service-level-agreement-sla"></a>Prijzen en service level agreement (SLA)

Zie [prijzen voor HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/)voor meer informatie over prijzen en sla voor de Enterprise Security Package.

## <a name="next-steps"></a>Volgende stappen

* [Setup van het cluster voor Apache Hadoop, Spark en meer op HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Werken in Apache Hadoop op HDInsight vanaf een Windows-computer](hdinsight-hadoop-windows-tools.md)
* [Release opmerkingen voor Hortonworks die zijn gekoppeld aan Azure HDInsight-versies](./hortonworks-release-notes.md)
* [Apache-onderdelen op HDInsight](./hdinsight-component-versioning.md)