---
title: ID-Broker (preview-versie) gebruiken voor referentie beheer-Azure HDInsight
description: Meer informatie over HDInsight ID Broker voor het vereenvoudigen van de verificatie voor Apache Hadoop clusters die lid zijn van een domein.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483009"
---
# <a name="use-id-broker-preview-for-credential-management"></a>ID-Broker (preview-versie) gebruiken voor referentie beheer

In dit artikel wordt beschreven hoe u de functie ID Broker kunt instellen en gebruiken in azure HDInsight. U kunt deze functie gebruiken om u aan te melden bij Apache Ambari via Azure Multi-Factor Authentication en de vereiste Kerberos-tickets te verkrijgen zonder wacht woord-hashes te hoeven gebruiken in Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Overzicht

De ID-Broker vereenvoudigt complexe verificatie-instellingen in de volgende scenario's:

* Uw organisatie is afhankelijk van Federatie om gebruikers te verifiëren voor het openen van cloud resources. Voor het gebruik van HDInsight-clusters (ESP Enterprise Security Package) moest u voorheen wachtwoord hash-synchronisatie inschakelen vanuit uw on-premises omgeving tot Azure Active Directory. Deze vereiste kan moeilijk of onwenselijk zijn voor sommige organisaties.

* U bouwt oplossingen die gebruikmaken van technologieën die afhankelijk zijn van verschillende verificatie mechanismen. Apache Hadoop en Apache zwerver zijn bijvoorbeeld afhankelijk van Kerberos, terwijl Azure Data Lake Storage afhankelijk is van OAuth.

ID Broker biedt een uniforme verificatie-infra structuur en verwijdert de vereiste voor het synchroniseren van wacht woord-hashes naar Azure AD DS. ID Broker bestaat uit onderdelen die worden uitgevoerd op een Windows Server-VM (ID Broker-knoop punt), samen met cluster gateway-knoop punten. 

In het volgende diagram ziet u de verificatie stroom voor alle gebruikers, met inbegrip van federatieve gebruikers, nadat ID Broker is ingeschakeld:

![Verificatie stroom met ID-Broker](./media/identity-broker/identity-broker-architecture.png)

Met ID Broker kunt u zich aanmelden bij ESP-clusters met behulp van Multi-Factor Authentication, zonder dat u een wacht woord hoeft op te geven. Als u zich al hebt aangemeld bij andere Azure-Services, zoals de Azure Portal, kunt u zich aanmelden bij uw HDInsight-cluster met een SSO-ervaring (eenmalige aanmelding).

## <a name="enable-hdinsight-id-broker"></a>HDInsight ID Broker inschakelen

Voer de volgende stappen uit om een ESP-cluster met de ID-Broker te maken:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Volg de basis stappen voor het maken van een ESP-cluster. Zie [een HDInsight-cluster met ESP maken](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)voor meer informatie.
1. Selecteer **HDINSIGHT id Broker inschakelen**.

Met de functie ID Broker wordt één extra virtuele machine aan het cluster toegevoegd. Deze VM is het ID Broker-knoop punt en bevat Server onderdelen ter ondersteuning van de verificatie. Het knoop punt ID Broker is een domein dat is gekoppeld aan het Azure AD DS-domein.

![Optie voor het inschakelen van ID-Broker](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>Integratie van hulp programma

De HDInsight [IntelliJ-invoeg toepassing](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) is bijgewerkt voor de ondersteuning van OAuth. U kunt deze invoeg toepassing gebruiken om verbinding te maken met het cluster en taken te verzenden.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-toegang zonder wacht woord-hash in azure AD DS

Nadat ID Broker is ingeschakeld, hebt u nog steeds een wacht woord-hash die is opgeslagen in azure AD DS voor SSH-scenario's met domein accounts. Als u SSH wilt maken naar een virtuele machine die lid is van een domein of als u de `kinit` opdracht wilt uitvoeren, moet u een wacht woord opgeven. 

Voor SSH-verificatie moet de hash beschikbaar zijn in azure AD DS. Als u SSH alleen wilt gebruiken voor beheer scenario's, kunt u één alleen-Cloud account maken en gebruiken om te SSHen naar het cluster. Andere gebruikers kunnen nog steeds gebruikmaken van Ambari-of HDInsight-hulpprogram ma's (zoals de IntelliJ-invoeg toepassing) zonder dat de wacht woord-hash beschikbaar is in azure AD DS.

## <a name="next-steps"></a>Volgende stappen

* [Een HDInsight-cluster met Enterprise Security Package configureren met behulp van Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory gebruikers synchroniseren met een HDInsight-cluster](../hdinsight-sync-aad-users-to-cluster.md)
* [Cluster-prestaties bewaken](../hdinsight-key-scenarios-to-monitor.md)
