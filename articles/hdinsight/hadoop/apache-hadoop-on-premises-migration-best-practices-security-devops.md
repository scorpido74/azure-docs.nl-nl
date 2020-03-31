---
title: 'Beveiliging: Migreren on-premises Apache Hadoop naar Azure HDInsight'
description: Leer de aanbevolen procedures voor beveiliging en DevOps voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 4ceefcbbbb53e3ae13f8ced930ae8417fb00965f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974406"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>On-premises Apache Hadoop-clusters migreren naar Azure HDInsight - aanbevolen procedures voor beveiliging en DevOps

In dit artikel worden aanbevelingen gegeven voor beveiliging en DevOps in Azure HDInsight-systemen. Het maakt deel uit van een serie die best practices biedt om te helpen bij het migreren van on-premises Apache Hadoop-systemen naar Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Cluster beveiligen en beheren met Enterprise Security Package

Het Enterprise Security Package (ESP) ondersteunt Active Directory-gebaseerde verificatie, ondersteuning voor meerdere gebruikers en op rollen gebaseerd toegangsbeheer. Met de gekozen ESP-optie wordt het HDInsight-cluster gekoppeld aan het Active Directory-domein en kan de bedrijfsbeheerder op rollen gebaseerde toegangscontrole (RBAC) configureren voor Apache Hive-beveiliging met behulp van Apache Ranger. De beheerder kan ook de toegang tot gegevens door werknemers en eventuele wijzigingen in het toegangscontrolebeleid controleren.

ESP is beschikbaar op de volgende clustertypen: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka en Interactive Query (Hive LLAP).

Gebruik de volgende stappen om het hdinsight-cluster met domein te implementeren:

- Azure Active Directory (AAD) implementeren door de domeinnaam door te geven.
- Azure Active Directory Domain Services (AAD DS) implementeren.
- Maak het vereiste virtuele netwerk en subnet.
- Implementeer een VM in het virtuele netwerk om AAD DS te beheren.
- Word lid van de VM naar het domein.
- Installeer AD- en DNS-hulpprogramma's.
- Laat de AAD DS-beheerder een organisatie-eenheid (OU) maken.
- LDAPS inschakelen voor AAD DS.
- Maak een serviceaccount in Azure Active Directory met gedelegeerde lees- & schrijfbeheertoestemming naar de organisatie, zodat dit mogelijk is. Dit serviceaccount kan vervolgens machines aansluiten bij het domein en machineprincipals binnen de OU plaatsen. Het kan ook serviceprincipals maken binnen de organisatie-eenheid die u opgeeft tijdens het maken van het cluster.

    > [!Note]
    > Het serviceaccount hoeft geen AD-domeinbeheerdersaccount te zijn.

- Implementeer HDInsight ESP-cluster door de volgende parameters in te stellen:

    |Parameter |Beschrijving |
    |---|---|
    |Domeinnaam|De domeinnaam die is gekoppeld aan Azure AD DS.|
    |Domeinnaam|Het serviceaccount in het door Azure AD DS DC beheerde domein `hdiadmin@contoso.onmicrosoft.com`dat u in de vorige sectie hebt gemaakt, bijvoorbeeld: . Deze domeingebruiker wordt de beheerder van dit HDInsight-cluster.|
    |Domeinwachtwoord|Het wachtwoord van het serviceaccount.|
    |Organisatie-eenheid|De voorname naam van de organisatie-eenheid die u wilt `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`gebruiken met het CLUSTER HDInsight, bijvoorbeeld: . Als deze organisatie-eenheid niet bestaat, probeert het CLUSTER HDInsight de organisatie-eenheid te maken met behulp van de bevoegdheden van het serviceaccount.|
    |LDAPS-URL|bijvoorbeeld . `ldaps://contoso.onmicrosoft.com:636`|
    |Gebruikersgroep openen|De beveiligingsgroepen waarvan u de gebruikers wilt synchroniseren `HiveUsers`met het cluster, bijvoorbeeld: . Als u meerdere gebruikersgroepen wilt opgeven, scheidt u deze per puntkomma ';'. De groep(en) moet in de map staan voordat het ESP-cluster wordt gemaakt.|

Raadpleeg voor meer informatie de volgende artikelen:

- [Een inleiding tot Apache Hadoop-beveiliging met hdinsight-clusters met domeinverbonden](../domain-joined/hdinsight-security-overview.md)
- [Azure-domeinsamengevoegde Apache Hadoop-clusters plannen in HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Een HDInsight-cluster met domein configureren met Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Azure Active Directory-gebruikers synchroniseren met een HDInsight-cluster](../hdinsight-sync-aad-users-to-cluster.md)
- [Apache Hive-beleid configureren in hdinsight met domein](../domain-joined/apache-domain-joined-run-hive.md)
- [Apache Oozie uitvoeren in met domeinen verbonden HDInsight Hadoop-clusters](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>End-to-end bedrijfsbeveiliging implementeren

End-to-end bedrijfsbeveiliging kan worden bereikt met de volgende besturingselementen:

**Particuliere en beveiligde gegevenspijplijn (beveiliging op perimeterniveau)**
    - Perimeterniveau Beveiliging kan worden bereikt via Azure Virtual Networks, Network Security Groups en Gateway service.

**Verificatie en autorisatie voor toegang tot gegevens**
    - Het HDInsight-cluster met domeinmaken met Azure Active Directory Domain Services. (Enterprise Security Package).
    - Gebruik Ambari om op rollen gebaseerde toegang te bieden tot clusterbronnen voor AD-gebruikers.
    - Gebruik Apache Ranger om toegangscontrolebeleid in te stellen voor Hive op tabel / kolom / rijniveau.
    - SSH-toegang tot het cluster kan alleen worden beperkt tot de beheerder.

**Controleren**
    - Alle toegang tot de CLUSTERbronnen en gegevens van HDInsight weergeven en rapporteren.
    - Alle wijzigingen in het toegangscontrolebeleid weergeven en rapporteren.

**Codering**
    - Transparante server-side encryptie met behulp van door Microsoft beheerde sleutels of door de klant beheerde sleutels.
    - In Transit-versleuteling met client-side encryptie, https en TLS.

Raadpleeg voor meer informatie de volgende artikelen:

- [Overzicht van Azure Virtual Networks](../../virtual-network/virtual-networks-overview.md)
- [Overzicht azure Network Security Groups](../../virtual-network/security-overview.md)
- [Peering van virtuele Azure-netwerken](../../virtual-network/virtual-network-peering-overview.md)
- [Veiligheidsgids voor Azure Storage](../../storage/blobs/security-recommendations.md)
- [Azure Storage Service-versleuteling in rust](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Controle & waarschuwen gebruiken

Zie voor meer informatie het artikel:

[Overzicht van Azure-monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Clusters bijwerken

Regelmatig upgraden naar de nieuwste HDInsight-versie om te profiteren van de nieuwste functies. De volgende stappen kunnen worden gebruikt om het cluster te upgraden naar de nieuwste versie:

1. Maak een nieuw TEST HDInsight-cluster met de nieuwste beschikbare HDInsight-versie.
1. Test op het nieuwe cluster om ervoor te zorgen dat de taken en workloads werken zoals verwacht.
1. Wijzig naar behoefte taken of toepassingen of workloads.
1. Een back-up maken van tijdelijke gegevens die lokaal zijn opgeslagen op de clusterknooppunten.
1. Verwijder het bestaande cluster.
1. Maak een cluster van de nieuwste HDInsight-versie in hetzelfde virtuele netwerksubnet, met dezelfde standaardgegevens en metaopslag als het vorige cluster.
1. Tijdelijke gegevens importeren waarvan een back-up is genomen.
1. Taken starten/verder verwerken met het nieuwe cluster.

Zie voor meer informatie het artikel: [Upgrade HDInsight-cluster naar een nieuwe versie.](../hdinsight-upgrade-cluster.md)

## <a name="patch-cluster-operating-systems"></a>Patchclusterbesturingssystemen

Als managed Hadoop-service zorgt HDInsight voor het patchen van het besturingssysteem van de VM's die worden gebruikt door HDInsight-clusters.

Zie voor meer informatie het artikel: [OS patching for HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Na migratie

1. **Toepassingen herstellen** - Iteratief de nodige wijzigingen aanbrengen in de taken, processen en scripts.
2. **Test uitvoeren** - Iteratief uitvoeren van functionele en prestatietests.
3. **Optimaliseren** - Pak prestatieproblemen op basis van de bovenstaande testresultaten en test vervolgens opnieuw om de prestatieverbeteringen te bevestigen.

## <a name="next-steps"></a>Volgende stappen

Lees meer over [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
