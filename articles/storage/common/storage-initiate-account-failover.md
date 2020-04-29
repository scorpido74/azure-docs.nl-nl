---
title: Failover van een opslag account initiëren (preview)-Azure Storage
description: Meer informatie over het initiëren van een account-failover in het geval dat het primaire eind punt voor uw opslag account niet meer beschikbaar is. De failover werkt de secundaire regio bij om de primaire regio voor uw opslag account te worden.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0c619224201d6225d5e5c127b342f71f2f7fced9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79535349"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Failover van een opslag account initiëren (preview-versie)

Als het primaire eind punt voor uw Geo-redundante opslag account om de een of andere reden niet beschikbaar is, kunt u een account-failover initiëren (preview). Met een account-failover wordt het secundaire eind punt bijgewerkt om het primaire eind punt voor uw opslag account te worden. Zodra de failover is voltooid, kunnen clients beginnen met het schrijven naar de nieuwe primaire regio. Met geforceerde failover kunt u hoge Beschik baarheid voor uw toepassingen onderhouden.

In dit artikel wordt beschreven hoe u een failover voor een account initieert voor uw opslag account met behulp van de Azure Portal, Power shell of Azure CLI. Zie [herstel na nood gevallen en failover van accounts (preview) in azure Storage](storage-disaster-recovery-guidance.md)voor meer informatie over account-failover.

> [!WARNING]
> Een account-failover resulteert doorgaans in gegevens verlies. Bekijk [inzicht in het account failover proces](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)voor meer informatie over de implicaties van een account-failover en voor bereiding op gegevens verlies.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u een account-failover kunt uitvoeren op uw opslag account, moet u ervoor zorgen dat u de volgende stap hebt uitgevoerd:

- Zorg ervoor dat uw opslag account is geconfigureerd voor het gebruik van geografisch redundante opslag (GRS) of geografisch redundante opslag met lees toegang (RA-GRS). Zie [Azure Storage redundantie](storage-redundancy.md)voor meer informatie over geografisch redundante opslag.

## <a name="important-implications-of-account-failover"></a>Belang rijke implicaties van account-failover

Wanneer u een failover voor een account initieert voor uw opslag account, worden de DNS-records voor het secundaire eind punt bijgewerkt zodat het secundaire eind punt het primaire eind punt wordt. Zorg ervoor dat u de mogelijke gevolgen voor uw opslag account begrijpt voordat u een failover initieert.

Als u de omvang van gegevens verlies wilt schatten voordat u een failover initieert, controleert u de **laatste synchronisatie tijd** - `Get-AzStorageAccount` eigenschap met de Power shell- `-IncludeGeoReplicationStats` cmdlet en neemt u de para meter op. Controleer vervolgens de `GeoReplicationStats` eigenschap voor uw account. \

Na de failover wordt uw type opslag account automatisch geconverteerd naar lokaal redundante opslag (LRS) in de nieuwe primaire regio. U kunt geografisch redundante opslag (GRS) of geografisch redundante opslag met lees toegang (RA-GRS) opnieuw inschakelen voor het account. Houd er rekening mee dat het converteren van LRS naar GRS of RA-GRS een extra kosten in beslag brengt. Zie [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie.

Nadat u GRS voor uw opslag account opnieuw hebt ingeschakeld, begint micro soft met het repliceren van de gegevens in uw account naar de nieuwe secundaire regio. De replicatie tijd is afhankelijk van de hoeveelheid gegevens die wordt gerepliceerd.  

## <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om een account-failover te initiëren vanuit de Azure Portal:

1. Ga naar uw opslagaccount.
2. Onder **instellingen**selecteert u **geo-replicatie**. De volgende afbeelding toont de geo-replicatie en de failover-status van een opslag account.

    ![Scherm opname van geo-replicatie en failover-status](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Controleer of uw opslag account is geconfigureerd voor geo-redundante opslag (GRS) of geografisch redundante opslag met lees toegang (RA-GRS). Als dat niet het geval is, selecteert u vervolgens **configuratie** onder **instellingen** om uw account bij te werken naar geo-redundant. 
4. De **laatste synchronisatie tijd** eigenschap geeft aan hoe ver de secundaire achterligt van de primaire. **Tijd van laatste synchronisatie** biedt een schatting van de mate van gegevens verlies die u zult ondervinden nadat de failover is voltooid.
5. Selecteer **voorbereiden voor failover (preview-versie)**. 
6. Bekijk het bevestigings venster. Wanneer u klaar bent, voert u **Ja** in om de failover te bevestigen en te initiëren.

    ![Scherm opname van het bevestigings dialoogvenster voor een account-failover](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[Zo](#tab/azure-powershell)

Als u Power shell wilt gebruiken om een account-failover te initiëren, moet u eerst de 6.0.1-Preview-module installeren. Volg deze stappen om de module te installeren:

1. Eerdere installaties van Azure PowerShell verwijderen:

    - Verwijder eerdere installaties van Azure PowerShell van Windows met de instelling **Apps & onderdelen** onder **instellingen**.
    - Verwijder alle **Azure** -modules `%Program Files%\WindowsPowerShell\Modules`uit.

1. Zorg ervoor dat de meest recente versie van PowerShellGet is geïnstalleerd. Open een Windows Power shell-venster en voer de volgende opdracht uit om de meest recente versie te installeren:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Sluit het Power shell-venster en open het opnieuw nadat u PowerShellGet hebt geïnstalleerd. 

1. Installeer de nieuwste versie van Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installeer een Azure Storage preview-module die de failover van het account ondersteunt:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. Sluit het Power shell-venster en open het opnieuw.
 
Voer de volgende opdracht uit om een account-failover te initiëren vanuit Power shell:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gebruiken om een account-failover te initiëren, voert u de volgende opdrachten uit:

```azurecli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Volgende stappen

- [Herstel na nood gevallen en failover van accounts (preview) in Azure Storage](storage-disaster-recovery-guidance.md)
- [Maximaal beschikbare toepassingen met RA-GRS ontwerpen](storage-designing-ha-apps-with-ragrs.md)
- [Zelf studie: een Maxi maal beschik bare toepassing bouwen met Blob Storage](../blobs/storage-create-geo-redundant-storage.md) 
