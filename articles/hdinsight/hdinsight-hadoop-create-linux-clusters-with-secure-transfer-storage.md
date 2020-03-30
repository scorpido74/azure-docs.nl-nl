---
title: Apache Hadoop & beveiligde transferopslag - Azure HDInsight
description: Informatie over het maken van HDInsight-clusters met Azure-opslag-accounts voor veilige overdracht.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560079"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop-clusters met beveiligde opslagaccounts voor overdracht in Azure HDInsight

De functie [Veilige overdracht vereist](../storage/common/storage-require-secure-transfer.md) verhoogt de beveiliging van uw Azure-opslagaccount door alle aanvragen naar uw account af te dwingen via een beveiligde verbinding. Deze functie en het wasbs-schema worden alleen ondersteund door HDInsight-clusterversie 3.6 of nieuwer.

> [!IMPORTANT]
> Het inschakelen van veilige opslagoverdracht na het maken van een cluster kan leiden tot fouten met uw opslagaccount en wordt niet aanbevolen. Het is beter om een nieuw cluster te maken met behulp van een opslagaccount met beveiligde overdracht al ingeschakeld.

## <a name="storage-accounts"></a>Opslagaccounts

### <a name="azure-portal"></a>Azure Portal

Standaard is de vereiste eigenschap voor beveiligde overdracht ingeschakeld wanneer u een opslagaccount maakt in azure-portal.

Zie [Beveiligde overdracht met Azure-portal vereisen](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)als u een bestaand opslagaccount wilt bijwerken met azure-portal.

### <a name="powershell"></a>PowerShell

Voor de PowerShell-cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)moet u ervoor zorgen dat de parameter `-EnableHttpsTrafficOnly` is ingesteld op `1`.

Zie [Veilige overdracht met PowerShell vereisen](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)als u een bestaand opslagaccount met PowerShell wilt bijwerken.

### <a name="azure-cli"></a>Azure-CLI

Controleer voor `--https-only` het maken van het [AZ-opslagaccount voor](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)de opdracht Azure CLI op `true`.

Zie [Veilige overdracht met Azure CLI vereisen](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)als u een bestaand opslagaccount wilt bijwerken met Azure CLI.

## <a name="add-additional-storage-accounts"></a>Extra opslagaccounts toevoegen

Er zijn verschillende opties om extra opslagaccounts met veilige overdracht toe te voegen:

* De Azure Resource Manager-sjabloon in de laatste sectie wijzigen.
* Een cluster maken met [Azure Portal](https://portal.azure.com) en een gekoppelde opslagaccount opgeven.
* Een scriptactie gebruiken om extra opslagaccounts met veilige overdracht toe te voegen aan een bestaand HDInsight-cluster. Zie [Extra opslagaccounts toevoegen aan HDInsight](hdinsight-hadoop-add-storage.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Het gebruik van Azure Storage (WASB) in plaats van [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) als standaardgegevensarchief
* Zie [Azure Storage gebruiken met HDInsight](hdinsight-hadoop-use-blob-storage.md) voor meer informatie over hoe HDInsight Azure Storage gebruikt.
* Zie [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md) voor meer informatie over het uploaden van gegevens naar HDInsight.
