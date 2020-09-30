---
title: ID-Broker (preview-versie) gebruiken voor referentie beheer-Azure HDInsight
description: Meer informatie over HDInsight ID Broker voor het vereenvoudigen van de verificatie voor Apache Hadoop clusters die lid zijn van een domein.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 24f15b8a4d5a5afd3a2794fe686d3acb0036cdd8
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565323"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Azure HDInsight ID Broker (preview-versie)

In dit artikel wordt beschreven hoe u de functie HDInsight ID Broker (HIB) in azure HDInsight instelt en gebruikt. U kunt deze functie gebruiken voor het ophalen van moderne OAuth-authenticatie voor Apache Ambari tijdens het afdwingen van Multi-Factor Authentication (MFA) zonder verouderde wachtwoord hashes in Azure Active Directory Domain Services (AAD-DS).

## <a name="overview"></a>Overzicht

HIB vereenvoudigt complexe verificatie-instellingen in de volgende scenario's:

* Uw organisatie is afhankelijk van Federatie om gebruikers te verifiëren voor het openen van cloud resources. Voor het gebruik van HDInsight-clusters (ESP Enterprise Security Package) moest u voorheen wachtwoord hash-synchronisatie inschakelen vanuit uw on-premises omgeving tot Azure Active Directory (Azure AD). Deze vereiste kan moeilijk of onwenselijk zijn voor sommige organisaties.

* Uw organisatie wil MFA afdwingen voor toegang via internet/HTTP op basis van Apache Ambari en andere cluster resources.

HIB biedt de verificatie-infra structuur die protocol overgang van OAuth (modern) naar Kerberos (verouderd) mogelijk maakt zonder dat wacht woord-hashes moeten worden gesynchroniseerd met AAD-DS. Deze infra structuur bestaat uit onderdelen die worden uitgevoerd op een Windows Server-VM (ID Broker-knoop punt), samen met cluster gateway-knoop punten.

Gebruik de volgende tabel om de beste verificatie optie te bepalen op basis van de behoeften van uw organisatie:

|Verificatieopties |HDInsight-configuratie | Factoren om rekening mee te houden |
|---|---|---|
| Volledig OAuth | ESP + HIB | 1. de veiligste optie (MFA wordt ondersteund) 2.    Hash-synchronisatie is niet vereist. 3.  Geen SSH/kinit/keytab-toegang voor on-premises accounts, die geen wacht woord-hash in AAD-DS hebben. 4.   Alleen Cloud accounts kunnen nog wel SSH/kinit/keytab. 5. Webgebaseerde toegang tot Ambari via OAuth 6.  Vereist het bijwerken van verouderde apps (JDBC/ODBC, etc.) voor de ondersteuning van OAuth.|
| OAuth + Basic auth | ESP + HIB | 1. webgebaseerde toegang tot Ambari via OAuth 2. Oudere apps blijven de basis verificatie gebruiken. 3. MFA moet zijn uitgeschakeld voor toegang tot basis verificatie. 4. Hash-synchronisatie is niet vereist. 5. Geen SSH/kinit/keytab-toegang voor on-premises accounts, die geen wacht woord-hash in AAD-DS hebben. 6. Alleen Cloud accounts kunnen nog SSH-kinit. |
| Volledige basis verificatie | PROTOCOLSPECIFIEKE | 1. Dit is vergelijkbaar met on-premises Setup. 2. Wachtwoord hash-synchronisatie met AAD-DS is vereist. 3. On-premises accounts kunnen SSH-kinit of keytab gebruiken. 4. MFA moet worden uitgeschakeld als de back-upopslag ADLS Gen2 |

Het volgende diagram toont de moderne op OAuth gebaseerde verificatie stroom voor alle gebruikers, waaronder federatieve gebruikers, nadat de ID Broker is ingeschakeld:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Verificatie stroom met ID-Broker":::

In dit diagram moet de client (ofwel browser of apps) eerst het OAuth-token verkrijgen en vervolgens het token aan de gateway in een HTTP-aanvraag presen teren. Als u zich al hebt aangemeld bij andere Azure-Services, zoals de Azure Portal, kunt u zich aanmelden bij uw HDInsight-cluster met een SSO-ervaring (eenmalige aanmelding).

Er zijn nog steeds veel oudere toepassingen die alleen basis authenticatie ondersteunen (bijvoorbeeld gebruikers naam/wacht woord). Voor deze scenario's kunt u HTTP-basis verificatie blijven gebruiken om verbinding te maken met de cluster gateways. In deze installatie moet u ervoor zorgen dat de netwerk verbinding van de gateway knooppunten naar het Federatie-eind punt (AD FS-eind punt) wordt voor een directe regel voor het controleren van Gateway knooppunten. 

In het volgende diagram ziet u de basis verificatie stroom voor federatieve gebruikers. Ten eerste probeert de gateway de verificatie te volt ooien met behulp van de [ROPC-stroom](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) . als er geen wacht woord-hashes zijn gesynchroniseerd met Azure AD, valt deze weer op het detecteren van AD FS-eind punt en de verificatie te volt ooien door het AD FS-eind punt te gebruiken.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Verificatie stroom met ID-Broker":::


## <a name="enable-hdinsight-id-broker"></a>HDInsight ID Broker inschakelen

Voer de volgende stappen uit om een ESP-cluster met de ID-Broker te maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Volg de basis stappen voor het maken van een ESP-cluster. Zie [een HDInsight-cluster met ESP maken](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)voor meer informatie.
1. Selecteer **HDINSIGHT id Broker inschakelen**.

Met de functie ID Broker wordt één extra virtuele machine aan het cluster toegevoegd. Deze VM is het ID Broker-knoop punt en bevat Server onderdelen ter ondersteuning van de verificatie. Het knoop punt ID Broker is een domein dat is gekoppeld aan het Azure AD DS-domein.

![Optie voor het inschakelen van ID-Broker](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken
Als u een nieuwe rol `idbrokernode` met de naam met de volgende kenmerken aan het reken Profiel van uw sjabloon toevoegt, wordt het cluster gemaakt met het id Broker-knoop punt ingeschakeld:

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

## <a name="tool-integration"></a>Integratie van hulp programma

HDIsngith-hulpprogram ma's zijn bijgewerkt naar systeem eigen ondersteuning voor OAuth. We raden u aan deze hulpprogram ma's te gebruiken voor moderne, op OAuth gebaseerde toegang tot de clusters. De HDInsight [IntelliJ-invoeg toepassing](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) kan worden gebruikt voor toepassingen op basis van Java, zoals scala. [Spark-& Hive-Hulpprogram ma's voor VS code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) kunnen worden gebruikt voor PySpark en Hive-taken. Ze ondersteunen zowel batch-als interactieve taken.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-toegang zonder wacht woord-hash in azure AD DS

|SSH-opties |Factoren om rekening mee te houden |
|---|---|
| Lokaal VM-account (bijvoorbeeld sshuser) | 1. u hebt dit account bij de aanmaak tijd van het cluster gemaakt. 2.  Er is geen Kerberos-authication voor dit account |
| Alleen Cloud account (bijvoorbeeld alice@contoso.onmicrosoft.com ) | 1. de wacht woord-hash is beschikbaar in AAD-DS 2. Kerberos-verificatie is mogelijk via SSH Kerberos |
| On-premises account (bijvoorbeeld alice@contoso.com ) | 1. SSH Kerberos-verificatie is alleen mogelijk als wacht woord-hash beschikbaar is in AAD-DS anders kan deze gebruiker geen SSH-bewerking naar het cluster doen |

Als u SSH wilt maken naar een VM die lid is van een domein of als u de opdracht wilt uitvoeren `kinit` , moet u een wacht woord opgeven. Voor SSH Kerberos-authenticatie moet de hash beschikbaar zijn in AAD-DS. Als u SSH alleen wilt gebruiken voor beheer scenario's, kunt u één alleen-Cloud account maken en gebruiken om te SSHen naar het cluster. Andere on-premises gebruikers kunnen nog steeds gebruikmaken van de hulpprogram ma's Ambari of HDInsight of HTTP Basic auth zonder dat de wacht woord-hash beschikbaar is in AAD-DS.

Als uw organisatie geen wacht woord-hashes synchroniseert met AAD-DS als best practice, maakt u één Cloud alleen gebruiker in azure AD en wijst u deze toe als cluster beheerder bij het maken van het cluster en gebruikt u dit voor beheer doeleinden, inclusief het verkrijgen van toegang tot de hoofdmap via SSH.

Raadpleeg deze [hand leiding](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues)voor informatie over het oplossen van problemen met verificatie.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-hib"></a>Clients die gebruikmaken van OAuth om verbinding te maken met de HDInsight-gateway met HIB

In de HIB-installatie kunnen aangepaste apps en clients die verbinding maken met de gateway, worden bijgewerkt om eerst het vereiste OAuth-token te verkrijgen. U kunt de stappen in dit [document](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) volgen om het token te verkrijgen met de volgende informatie:

*   OAuth-resource-URI: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Machtiging: (naam: cluster. ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Na het verkrijgen van het OAuth-token kunt u dit gebruiken in de autorisatie-header van de HTTP-aanvraag voor de cluster gateway (bijvoorbeeld https:// <clustername> -int.azurehdinsight.net). Een voor beeld van een krul opdracht naar Apache livy API kan er als volgt uitzien:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>Volgende stappen

* [Een HDInsight-cluster met Enterprise Security Package configureren met behulp van Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory-gebruikers synchroniseren met een HDInsight-cluster](../hdinsight-sync-aad-users-to-cluster.md)
* [Cluster-prestaties bewaken](../hdinsight-key-scenarios-to-monitor.md)
