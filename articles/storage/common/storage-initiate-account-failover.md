---
title: Een failovervoor opslagaccount starten (voorbeeld) - Azure Storage
description: Meer informatie over het starten van een failoveraccount in het geval dat het primaire eindpunt voor uw opslagaccount niet beschikbaar is. De failover werkt de secundaire regio bij om de primaire regio voor uw opslagaccount te worden.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0c619224201d6225d5e5c127b342f71f2f7fced9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535349"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Een failovervan een opslagaccount starten (voorbeeld)

Als het primaire eindpunt voor uw georedundante opslagaccount om welke reden dan ook niet meer beschikbaar is, u een failoveraccount voor een account starten (voorbeeld). Een failovervan het account werkt het secundaire eindpunt bij om het primaire eindpunt voor uw opslagaccount te worden. Zodra de failover is voltooid, kunnen clients beginnen met schrijven naar de nieuwe primaire regio. Gedwongen failover stelt u in staat om een hoge beschikbaarheid voor uw toepassingen te behouden.

In dit artikel ziet u hoe u een accountfailover voor uw opslagaccount initieert met behulp van de Azure-portal, PowerShell of Azure CLI. Zie [Disaster recovery en account failover (preview) in Azure Storage](storage-disaster-recovery-guidance.md)voor meer informatie over accountfailover.

> [!WARNING]
> Een accountfailover resulteert meestal in een aantal gegevensverlies. Als u de implicaties van een fail-over van een account wilt begrijpen en u wilt voorbereiden op gegevensverlies, [bekijkt u het failoverproces van de account](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u een failoveraccount uitvoeren op uw opslagaccount, moet u ervoor zorgen dat u de volgende stap hebt uitgevoerd:

- Zorg ervoor dat uw opslagaccount is geconfigureerd om georedundante opslag (GRS) of georedundante opslag (RA-GRS) te gebruiken. Zie [Redundantie van Azure Storage](storage-redundancy.md)voor meer informatie over georedundante opslag.

## <a name="important-implications-of-account-failover"></a>Belangrijke implicaties van accountfailover

Wanneer u een failoveraccount voor uw opslagaccount initieert, worden de DNS-records voor het secundaire eindpunt bijgewerkt, zodat het secundaire eindpunt het primaire eindpunt wordt. Zorg ervoor dat u de mogelijke impact op uw opslagaccount begrijpt voordat u een failover start.

Als u de omvang van het waarschijnlijke gegevensverlies wilt schatten voordat `Get-AzStorageAccount` u een failover start, `-IncludeGeoReplicationStats` controleert u de eigenschap Last **Sync Time** met de PowerShell-cmdlet en neemt u de parameter op. Controleer dan `GeoReplicationStats` de woning voor uw account. \

Na de failover wordt uw opslagaccounttype automatisch geconverteerd naar lokaal redundante opslag (LRS) in het nieuwe primaire gebied. U georedundante opslag (GRS) of georedundante opslag (RA-GRS) voor lezen opnieuw inschakelen voor het account. Houd er rekening mee dat het omzetten van LRS naar GRS of RA-GRS extra kosten met zich meebrengt. Zie [Details bandbreedteprijzen voor](https://azure.microsoft.com/pricing/details/bandwidth/)meer informatie .

Nadat u GRS opnieuw inschakelt voor uw opslagaccount, begint Microsoft de gegevens in uw account te repliceren naar de nieuwe secundaire regio. Replicatietijd is afhankelijk van de hoeveelheid gegevens die wordt gerepliceerd.  

## <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om een failovervan het account vanuit de Azure-portal te starten:

1. Ga naar uw opslagaccount.
2. Selecteer **onder Instellingen**de optie **Geo-replicatie**. De volgende afbeelding toont de georeplicatie- en failoverstatus van een opslagaccount.

    ![Schermafbeelding van de status geo-replicatie en failover](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Controleer of uw opslagaccount is geconfigureerd voor georedundante opslag (GRS) of georedundante opslag (RA-GRS) voor lezen. Als dit niet het is, selecteert u **Configuratie** onder **Instellingen** om uw account bij te werken als georedundant. 
4. De eigenschap **Last Sync Time** geeft aan hoe ver de secundaire achter de primaire. **Last Sync Time** geeft een schatting van de omvang van het gegevensverlies dat u zult ervaren nadat de failover is voltooid.
5. Selecteer **Voorbereiden op failover (voorbeeld)**. 
6. Bekijk het bevestigingsdialoogvenster. Wanneer u klaar bent, voert u **Ja** in om de failover te bevestigen en te starten.

    ![Schermafbeelding van het bevestigingsdialoogvenster voor een failover van een account](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u PowerShell wilt gebruiken om een failoveraccount te starten, moet u eerst de voorbeeldmodule 6.0.1 installeren. Volg de volgende stappen om de module te installeren:

1. Verwijder eerdere installaties van Azure PowerShell:

    - Verwijder eerdere installaties van Azure PowerShell uit Windows met behulp van de instelling **Apps & functies** onder **Instellingen**.
    - Verwijder alle **Azure-modules** uit `%Program Files%\WindowsPowerShell\Modules`.

1. Zorg ervoor dat u de nieuwste versie van PowerShellGet hebt geïnstalleerd. Open een Windows PowerShell-venster en voer de volgende opdracht uit om de nieuwste versie te installeren:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Sluit het PowerShell-venster en open het opnieuw na het installeren van PowerShellGet. 

1. Installeer de nieuwste versie van Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installeer een Azure Storage preview-module die accountfailover ondersteunt:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. Sluit het PowerShell-venster en open deze opnieuw.
 
Voer de volgende opdracht uit om een failovervan PowerShell te starten:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gebruiken om een failovervan een account te starten, voert u de volgende opdrachten uit:

```azurecli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Volgende stappen

- [Disaster recovery en account failover (preview) in Azure Storage](storage-disaster-recovery-guidance.md)
- [Maximaal beschikbare toepassingen met RA-GRS ontwerpen](storage-designing-ha-apps-with-ragrs.md)
- [Zelfstudie: Een zeer beschikbare toepassing bouwen met Blob-opslag](../blobs/storage-create-geo-redundant-storage.md) 
