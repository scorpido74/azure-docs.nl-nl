---
title: Object replicatie configureren (preview-versie)
titleSuffix: Azure Storage
description: Meer informatie over het configureren van object replicatie voor het asynchroon kopiëren van blok-blobs van een container in het ene opslag account naar het andere.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/20/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6c951d7875086658763243c7c1973f08233f96e0
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749219"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>Object replicatie voor blok-blobs configureren (preview-versie)

Met object replicatie (preview) worden blok-blobs tussen een bron opslag account en een doel account asynchroon gekopieerd. Zie [object replicatie (preview)](object-replication-overview.md)voor meer informatie over object replicatie.

Wanneer u object replicatie configureert, maakt u een replicatie beleid dat het bron opslag account en het doel account opgeeft. Een replicatie beleid bevat een of meer regels die een bron container en een doel container opgeven en aangeven welke blok-blobs in de bron container worden gerepliceerd.

In dit artikel wordt beschreven hoe u object replicatie configureert voor uw opslag account met behulp van de Azure Portal, Power shell of Azure CLI. U kunt ook een van de Azure Storage Resource provider-client bibliotheken gebruiken om object replicatie te configureren.

## <a name="create-a-replication-policy-and-rules"></a>Een replicatie beleid en-regels maken

Voordat u object replicatie configureert, moet u de bron-en doel opslag accounts maken als deze nog niet bestaan. Beide accounts moeten voor algemeen gebruik v2-opslag accounts zijn. Zie [een Azure Storage-account maken](../common/storage-account-create.md)voor meer informatie.

Zorg er ook voor dat u zich hebt geregistreerd voor de volgende vooraf bekeken onderdelen:

- [Object replicatie (preview-versie)](object-replication-overview.md)
- [Versie beheer van BLOB (preview)](versioning-overview.md)
- [Ondersteuning voor feed wijzigen in Azure Blob Storage (preview-versie)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Voordat u object replicatie configureert in de Azure Portal, maakt u de bron-en doel containers in hun respectieve opslag accounts, als deze nog niet bestaan. Daarnaast is het inschakelen van BLOB-versie beheer en het wijzigen van de feed voor het bron account, en schakelt u BLOB-versie beheer in op het doel account.

Voer de volgende stappen uit om een replicatie beleid te maken in de Azure Portal:

1. Navigeer naar het bron opslag account in de Azure Portal.
1. Onder **instellingen**selecteert u **object replicatie**.
1. Selecteer **replicatie instellen**.
1. Selecteer het doel abonnement en het opslag account.
1. Selecteer in de sectie **container paren** een bron container van het bron account en een doel container van het doel account. U kunt Maxi maal 10 container paren per replicatie beleid maken.

    De volgende afbeelding toont een set replicatie regels.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Scherm opname van de replicatie regels in Azure Portal":::

1. Indien gewenst, geeft u een of meer filters op om alleen blobs te kopiëren die overeenkomen met het patroon van het voor voegsel. Als u bijvoorbeeld een voor voegsel opgeeft `b` , worden alleen blobs waarvan de naam begint met die letter, gerepliceerd. U kunt een virtuele map opgeven als onderdeel van het voor voegsel.

    De volgende afbeelding toont filters die bepalen welke blobs worden gekopieerd als onderdeel van een replicatie regel.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Scherm afbeelding met filters voor een replicatie regel":::

1. Standaard wordt het Kopieer bereik zo ingesteld dat alleen nieuwe objecten worden gekopieerd. Als u alle objecten in de container wilt kopiëren of objecten wilt kopiëren vanaf een aangepaste datum en tijd, selecteert u de koppeling **wijzigen** en configureert u het Kopieer bereik voor het container paar.

    In de volgende afbeelding ziet u een aangepast Kopieer bereik.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Scherm opname van het aangepaste Kopieer bereik voor object replicatie":::

1. Selecteer **opslaan en Toep assen** om het replicatie beleid te maken en te beginnen met het repliceren van gegevens.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een replicatie beleid wilt maken met Power shell, installeert u eerst versie [2.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) van de module AZ. Storage Power shell. Voer de volgende stappen uit om de preview-module te installeren:

1. Verwijder eerdere installaties van Azure PowerShell van Windows met de optie **Apps & onderdelen** onder **instellingen**.

1. Zorg ervoor dat de meest recente versie van PowerShellGet is geïnstalleerd. Open een Windows Power shell-venster en voer de volgende opdracht uit om de meest recente versie te installeren:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    Sluit het Power shell-venster en open het opnieuw nadat u PowerShellGet hebt geïnstalleerd.

1. Installeer de nieuwste versie van Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installeer de module AZ. Storage Preview:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Zie [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-az-ps)(Engelstalig) voor meer informatie over het installeren van Azure PowerShell.

In het volgende voor beeld ziet u hoe u een replicatie beleid maakt voor de bron-en doel accounts. Vergeet niet om waarden tussen punt haken te vervangen door uw eigen waarden:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een replicatie beleid met Azure CLI wilt maken, moet u eerst de preview-extensie voor Azure Storage installeren.:

```azurecli
az extension add -n storage-or-preview
```

Meld u vervolgens aan met uw Azure-referenties:

```azurecli
az login
```

Schakel BLOB-versie beheer in op de bron-en doel opslag accounts en schakel feed wijzigen in voor het bron account. Vergeet niet om waarden tussen punt haken te vervangen door uw eigen waarden:

```azurecli
az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Maak de bron-en doel containers in hun respectieve opslag accounts.

```azurecli
az storage container create --account-name <source-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <source-storage-account> --name source-container4 --auth-mode login

az storage container create --account-name <dest-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <dest-storage-account> --name source-container4 --auth-mode login
```

Maak een nieuw replicatie beleid en de bijbehorende regels voor het doel account.

```azurecli
az storage account or-policy create --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

Maak het beleid voor het bron account met behulp van de beleids-ID.

```azurecli
az storage account or-policy show --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>Een replicatie beleid verwijderen

Als u een replicatie beleid en de bijbehorende regels wilt verwijderen, gebruikt u Azure Portal, Power shell of CLI.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Voer de volgende stappen uit om een replicatie beleid te verwijderen in de Azure Portal:

1. Navigeer naar het bron opslag account in de Azure Portal.
1. Onder **instellingen**selecteert u **object replicatie**.
1. Klik op de knop **meer** naast de naam van het beleid.
1. Selecteer **regels verwijderen**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een replicatie beleid wilt verwijderen, verwijdert u het beleid van zowel het bron account als het doel account. Als u het beleid verwijdert, worden ook alle bijbehorende regels verwijderd.

```powershell
Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName |
    Remove-AzStorageObjectReplicationPolicy
Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName |
    Remove-AzStorageObjectReplicationPolicy
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een replicatie beleid wilt verwijderen, verwijdert u het beleid van zowel het bron account als het doel account. Als u het beleid verwijdert, worden ook alle bijbehorende regels verwijderd.

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van object replicatie (preview-versie)](object-replication-overview.md)