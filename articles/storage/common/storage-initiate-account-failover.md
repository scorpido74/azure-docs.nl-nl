---
title: Failover van een opslag account initiëren
titleSuffix: Azure Storage
description: Meer informatie over het initiëren van een account-failover in het geval dat het primaire eind punt voor uw opslag account niet meer beschikbaar is. De failover werkt de secundaire regio bij om de primaire regio voor uw opslag account te worden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2d07195e28c964a540eafdfba94a958e6c9f6981
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871352"
---
# <a name="initiate-a-storage-account-failover"></a>Failover van een opslag account initiëren

Als het primaire eind punt voor uw Geo-redundante opslag account om welke reden dan ook niet beschikbaar is, kunt u een failover voor het account initiëren. Met een account-failover wordt het secundaire eind punt bijgewerkt om het primaire eind punt voor uw opslag account te worden. Zodra de failover is voltooid, kunnen clients beginnen met het schrijven naar de nieuwe primaire regio. Met geforceerde failover kunt u hoge Beschik baarheid voor uw toepassingen onderhouden.

In dit artikel wordt beschreven hoe u een failover voor een account initieert voor uw opslag account met behulp van de Azure Portal, Power shell of Azure CLI. Zie [nood herstel en failover van het opslag account](storage-disaster-recovery-guidance.md)voor meer informatie over account-failover.

> [!WARNING]
> Een account-failover resulteert doorgaans in gegevens verlies. Bekijk [inzicht in het account failover proces](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)voor meer informatie over de implicaties van een account-failover en voor bereiding op gegevens verlies.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u een account-failover kunt uitvoeren op uw opslag account, moet u ervoor zorgen dat uw opslag account is geconfigureerd voor geo-replicatie. Uw opslag account kan een van de volgende redundantie opties gebruiken:

- Geografisch redundante opslag (GRS) of geografisch redundante opslag met lees toegang (RA-GRS)
- Geo-zone-redundante opslag (GZRS) of geo-zone-redundante opslag met lees toegang (RA-GZRS)

Zie [Azure Storage redundantie](storage-redundancy.md)voor meer informatie over Azure Storage redundantie.

## <a name="initiate-the-failover"></a>De failover initiëren

## <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om een account-failover te initiëren vanuit de Azure Portal:

1. Ga naar uw opslagaccount.
2. Onder **instellingen**selecteert u **geo-replicatie**. De volgende afbeelding toont de geo-replicatie en de failover-status van een opslag account.

    ![Scherm opname van geo-replicatie en failover-status](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Controleer of uw opslag account is geconfigureerd voor geo-redundante opslag (GRS) of geografisch redundante opslag met lees toegang (RA-GRS). Als dat niet het geval is, selecteert u vervolgens **configuratie** onder **instellingen** om uw account bij te werken naar geo-redundant. 
4. De **laatste synchronisatie tijd** eigenschap geeft aan hoe ver de secundaire achterligt van de primaire. **Tijd van laatste synchronisatie** biedt een schatting van de mate van gegevens verlies die u zult ondervinden nadat de failover is voltooid.
5. Selecteer **voorbereiden voor failover**.
6. Bekijk het bevestigings venster. Wanneer u klaar bent, voert u **Ja** in om de failover te bevestigen en te initiëren.

    ![Scherm opname van het bevestigings dialoogvenster voor een account-failover](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De functie account failover is algemeen beschikbaar, maar is nog steeds afhankelijk van een preview-module voor Power shell. Als u Power shell wilt gebruiken om een account-failover te initiëren, moet u eerst de module AZ. Storage [1.1.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) installeren. Volg deze stappen om de module te installeren:

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

## <a name="important-implications-of-account-failover"></a>Belang rijke implicaties van account-failover

Wanneer u een failover voor een account initieert voor uw opslag account, worden de DNS-records voor het secundaire eind punt bijgewerkt zodat het secundaire eind punt het primaire eind punt wordt. Zorg ervoor dat u de mogelijke gevolgen voor uw opslag account begrijpt voordat u een failover initieert.

Als u de omvang van gegevens verlies wilt schatten voordat u een failover initieert, controleert u de **laatste synchronisatie tijd** - `Get-AzStorageAccount` eigenschap met de Power shell- `-IncludeGeoReplicationStats` cmdlet en neemt u de para meter op. Controleer vervolgens de `GeoReplicationStats` eigenschap voor uw account.

Na de failover wordt uw type opslag account automatisch geconverteerd naar lokaal redundante opslag (LRS) in de nieuwe primaire regio. U kunt geografisch redundante opslag (GRS) of geografisch redundante opslag met lees toegang (RA-GRS) opnieuw inschakelen voor het account. Houd er rekening mee dat het converteren van LRS naar GRS of RA-GRS een extra kosten in beslag brengt. Zie [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie.

Nadat u GRS voor uw opslag account opnieuw hebt ingeschakeld, begint micro soft met het repliceren van de gegevens in uw account naar de nieuwe secundaire regio. De replicatie tijd is afhankelijk van de hoeveelheid gegevens die wordt gerepliceerd.  

## <a name="next-steps"></a>Volgende stappen

- [Herstel na nood geval en failover van het opslag account](storage-disaster-recovery-guidance.md)
- [Geo-redundantie gebruiken om Maxi maal beschik bare toepassingen te ontwerpen](geo-redundant-design.md)
- [Zelf studie: een Maxi maal beschik bare toepassing bouwen met Blob Storage](../blobs/storage-create-geo-redundant-storage.md)
