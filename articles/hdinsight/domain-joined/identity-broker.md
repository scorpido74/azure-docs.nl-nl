---
title: 'ID Broker (preview) gebruiken voor referentiebeheer: Azure HDInsight'
description: Meer informatie over HDInsight ID Broker om verificatie voor met een domein verbonden Apache Hadoop-clusters te vereenvoudigen.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 1e7eaf49fb8b62259b8c619c89edffd629dfde7f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685512"
---
# <a name="use-id-broker-preview-for-credential-management"></a>ID Broker (voorbeeld) gebruiken voor referentiebeheer

In dit artikel wordt beschreven hoe u de functie ID Broker in Azure HDInsight instelt en gebruikt. U deze functie gebruiken om u aan te melden bij Apache Ambari via Azure Multi-Factor Authentication en de vereiste Kerberos-tickets te krijgen zonder wachtwoordhashes in Azure Active Directory Domain Services (Azure AD DS) te verkrijgen.

## <a name="overview"></a>Overzicht

ID Broker vereenvoudigt complexe verificatie-instellingen in de volgende scenario's:

* Uw organisatie vertrouwt op federatie om gebruikers te authenticeren voor toegang tot cloudbronnen. Voorheen moest u, om ESP-clusters (HDInsight Enterprise Security Package) te gebruiken, wachtwoordhashsynchronisatie inschakelen vanuit uw on-premises omgeving naar Azure Active Directory. Deze eis kan moeilijk of ongewenst zijn voor sommige organisaties.

* U bouwt oplossingen die gebruikmaken van technologieën die afhankelijk zijn van verschillende authenticatiemechanismen. Apache Hadoop en Apache Ranger vertrouwen bijvoorbeeld op Kerberos, terwijl Azure Data Lake Storage afhankelijk is van OAuth.

ID Broker biedt een uniforme verificatie-infrastructuur en verwijdert de vereiste voor het synchroniseren van wachtwoordhashes naar Azure AD DS. ID Broker bestaat uit onderdelen die worden uitgevoerd op een Windows Server VM (ID Broker-knooppunt), samen met clustergatewayknooppunten. 

In het volgende diagram ziet u de verificatiestroom voor alle gebruikers, inclusief federatieve gebruikers, nadat ID Broker is ingeschakeld:

![Verificatiestroom met ID Broker](./media/identity-broker/identity-broker-architecture.png)

Met ID Broker u zich aanmelden bij ESP-clusters met behulp van Multi-Factor Authentication, zonder wachtwoorden te verstrekken. Als u zich al hebt aangemeld bij andere Azure-services, zoals de Azure-portal, u zich aanmelden bij uw HDInsight-cluster met één SSO-ervaring (sign-on).

## <a name="enable-hdinsight-id-broker"></a>HDInsight ID Broker inschakelen

Ga als volgt te werk om een ESP-cluster te maken met ID Broker ingeschakeld:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Volg de basisstappen voor het maken van een ESP-cluster. Zie [Een HDInsight-cluster maken met ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)voor meer informatie.
1. Selecteer **HDInsight ID Broker inschakelen**.

De functie ID Broker voegt één extra VM toe aan het cluster. Deze VM is het ID Broker-knooppunt en bevat servercomponenten ter ondersteuning van verificatie. Het id-broker-knooppunt is domein dat is verbonden met het Azure AD DS-domein.

![Optie om ID Broker in te schakelen](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken
Als u een nieuwe `idbrokernode` rol met de volgende kenmerken toevoegt aan het rekenprofiel van uw sjabloon, wordt het cluster gemaakt met het knooppunt ID-broker ingeschakeld:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>Gereedschapsintegratie

De HDInsight [IntelliJ plug-in](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) is bijgewerkt om OAuth te ondersteunen. U deze plug-in gebruiken om verbinding te maken met het cluster en taken in te dienen.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-toegang zonder wachtwoordhash in Azure AD DS

Nadat ID Broker is ingeschakeld, hebt u nog steeds een wachtwoordhash nodig die is opgeslagen in Azure AD DS voor SSH-scenario's met domeinaccounts. Als u SSH wilt gebruiken voor een `kinit` vm die is verbonden met een domein of als u de opdracht wilt uitvoeren, moet u een wachtwoord opgeven. 

SSH-verificatie vereist dat de hash beschikbaar is in Azure AD DS. Als u SSH alleen voor administratieve scenario's wilt gebruiken, u één cloudaccount maken en dat gebruiken om SSH in het cluster te gebruiken. Andere gebruikers kunnen nog steeds Ambari- of HDInsight-hulpprogramma's gebruiken (zoals de IntelliJ-plug-in) zonder dat de wachtwoordhash beschikbaar is in Azure AD DS.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Clients die OAuth gebruiken om verbinding te maken met HDInsight-gateway met ID Broker-installatie

In de id-broker-instelling kunnen aangepaste apps en clients die verbinding maken met de gateway worden bijgewerkt om eerst het vereiste OAuth-token te verkrijgen. U de stappen in dit [document](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) volgen om het token te verkrijgen met de volgende informatie:

*   OAuth resource uri:https://hib.azurehdinsight.net 
* AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Toestemming: (naam: Cluster.ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>Volgende stappen

* [Een HDInsight-cluster configureren met Enterprise Security Package met Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory-gebruikers synchroniseren met een HDInsight-cluster](../hdinsight-sync-aad-users-to-cluster.md)
* [Cluster-prestaties bewaken](../hdinsight-key-scenarios-to-monitor.md)
