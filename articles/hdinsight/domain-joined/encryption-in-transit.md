---
title: Azure HDInsight-versleuteling in transit
description: Meer informatie over beveiligings functies voor het leveren van versleuteling voor uw Azure HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 25e38beb561ee954db2987643775f3a3c6e05737
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89668772"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>IPSec-versleuteling in transit voor Azure HDInsight

In dit artikel wordt de implementatie van versleuteling in transit beschreven voor communicatie tussen Azure HDInsight-cluster knooppunten.

> [!Note]
> Versleuteling in transit is momenteel ingeschakeld voor de volgende regio's: VS-Oost, Zuid-Centraal VS, West-VS2. 

## <a name="background"></a>Achtergrond

Azure HDInsight biedt diverse beveiligings functies voor het beveiligen van uw bedrijfs gegevens. Deze oplossingen worden gegroepeerd op de pijlers van de beveiliging, verificatie, autorisatie, controle, versleuteling en naleving. Versleuteling kan worden toegepast op gegevens, zowel op rest als tijdens de overdracht.

Versleuteling op rest wordt gedekt door versleuteling aan de server zijde van Azure Storage-accounts, evenals schijf versleuteling op de Azure-Vm's die deel uitmaken van uw HDInsight-cluster.

Versleuteling van gegevens in transit op HDInsight wordt bereikt met [Transport Layer Security (TLS)](https://docs.microsoft.com/azure/hdinsight/transport-layer-security) voor het accssing van de cluster gateways en [Internet Protocol Security (IPSec)](https://en.wikipedia.org/wiki/IPsec) tussen cluster knooppunten. IPSec kan eventueel worden ingeschakeld tussen alle hoofd knooppunten, Worker-knoop punten, Edge-knoop punten en Zookeeper-knoop punten. Het is niet ingeschakeld voor verkeer tussen gateway [-of id Broker-](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker) knoop punten die op Windows gebaseerde virtuele machines zijn en andere op Linux gebaseerde knoop punten in het cluster.

## <a name="enable-encryption-in-transit"></a>Versleuteling in transit inschakelen

### <a name="azure-portal"></a>Azure Portal

Voer de volgende stappen uit om een nieuw cluster te maken met versleuteling in transit dat is ingeschakeld met behulp van de Azure Portal:

1. Begin met het normale proces voor het maken van het cluster. Zie op [Linux gebaseerde clusters maken in HDInsight met behulp van de Azure portal voor de](../hdinsight-hadoop-create-linux-clusters-portal.md) eerste stappen voor het maken van het cluster.
1. Voltooi de tabbladen **basis beginselen** en **opslag** . Ga naar het tabblad **beveiliging en netwerk** .

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Het tabblad Beveiliging en netwerk voor het cluster maken.":::

1. Klik op het tabblad **beveiliging en netwerk** op het selectie vakje **versleuteling in transit inschakelen** .

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Cluster maken: Schakel versleuteling in door Voer in.":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Een cluster maken met versleuteling in transit dat is ingeschakeld via de Azure CLI

Versleuteling in transit is ingeschakeld met behulp van de `isEncryptionInTransitEnabled` eigenschap.

U kunt [een voorbeeld sjabloon en een parameter bestand downloaden](https://github.com/Azure-Samples/hdinsight-enterprise-security). Voordat u de sjabloon en het Azure CLI-code fragment hieronder gebruikt, vervangt u de volgende tijdelijke aanduidingen door de juiste waarden:

| Tijdelijke aanduiding | Beschrijving |
|---|---|
| `<SUBSCRIPTION_ID>` | De ID van uw Azure-abonnement |
| `<RESOURCE_GROUP>` | De resource groep waar u het nieuwe cluster en opslag account wilt maken. |
| `<STORAGEACCOUNTNAME>` | Het bestaande opslag account dat moet worden gebruikt met het cluster. De naam moet de vorm hebben `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | De naam van uw HDInsight-cluster. |
| `<PASSWORD>` | Het wacht woord dat u hebt gekozen voor het aanmelden bij het cluster met behulp van SSH en het Ambari-dash board. |
| `<VNET_NAME>` | Het virtuele netwerk waar het cluster wordt geïmplementeerd. |

In het onderstaande code fragment worden de volgende eerste stappen uitgevoerd:

1. Meld u aan bij uw Azure-account.
1. Hiermee stelt u het actieve abonnement in waarin de bewerkingen worden gemaakt.
1. Hiermee maakt u een nieuwe resource groep voor de nieuwe implementatie activiteiten.
1. Implementeer de sjabloon voor het maken van een nieuw cluster.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az group deployment create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van bedrijfsbeveiliging in Azure HDInsight](hdinsight-security-overview.md)
* [Azure Active Directory gebruikers synchroniseren met een HDInsight-cluster](../disk-encryption.md).
